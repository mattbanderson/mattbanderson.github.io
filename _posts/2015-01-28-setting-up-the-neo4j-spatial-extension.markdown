---

title: Setting Up and Using the Neo4j Spatial Extension
date: '2015-01-28 02:50:01'
---

*For instructions on how to install the Neo4j graph database, see my [previous post](/installing-the-neo4j-graph-database-on-windows)*.

Not only can Neo4j do lots of fun graph-related things, it can also be used for geospatial queries, thanks to the [Neo4j Spatial](https://github.com/neo4j-contrib/spatial) extension library!  

Neo4j Spatial allows you to add spatial indexes to your data and do things like search for items within a certain distance from point of reference or within a specified area (i.e. a circle or a rectangle, respectively).

To get started with Neo4j Spatial, follow the steps listed [here](https://github.com/neo4j-contrib/spatial#using-the-neo4j-spatial-server-plugin). In summary, those steps are as follows:

 * Download the appropriate zip for your version of Neo4j
 * Unzip it in your Neo4j plugins directory
 * Restart your Neo4j server
 * Verify the plugin was installed successfully (e.g. curl http://localhost:7474/db/data/ and look for "SpatialPlugin" under "extensions" in the JSON reponse)

I went through several rounds of trial and error with various layers and indexes before I settled on the steps described below. Many thanks to others who blogged or posted to StackOverflow about it. 

I started by loading my data from a tab delimited text file. My data set consisted of a collection of events that occurred over time and organizations associated with those events. I filtered my data set so that I only loaded events that had geospatial latitude and longitude coordinates associated with them. 

However you load your data, make sure your Neo4j nodes with geospatial coordinates have a property named "id". Leave it empty for now, but make sure it exists--*more on that later*.

After loading my data, I used the REST API to create a simple point layer and a spatial index. I used the excellent [RestSharp library](http://restsharp.org/), available via [NuGet](https://www.nuget.org/packages/RestSharp), to make this easier. My code looked something like this:

	var baseUri = "http://localhost:7474";
	var indexName = "YourGeoIndexName";
	var nodeLabel = "YourNodeLabelHere";

	var restClient = new RestClient(new Uri(baseUri + 	"/db/data/ext/SpatialPlugin/graphdb/addSimplePointLayer"));
	var request = new RestRequest(Method.POST);
	request.AddJsonBody(
		new
			{
				layer = indexName,
				lat = "lat",
				lon = "lon"
			});
	var response = restClient.Execute(request);
	Console.WriteLine(response.Content);

	var restClient = new RestClient(new Uri(baseUri + "/db/data/index/node/"));
	var request = new RestRequest(Method.POST);
	request.AddJsonBody(
		new
			{
				name = indexName,
				config = new {provider = "spatial", geometry_type = "point", lat = "lat", lon = "lon"}
		});
	var response = restClient.Execute(request);
	Console.WriteLine(response.Content);

Next, I retrieved the IDs of all my Neo4j nodes with geospatial (latitude/longitude) coordinates using the C# Neo4jClient library:

	var nodeIds = client.Cypher.Match("(n:" + nodeLabel + ")").Where("has(n.lat)").AndWhere("has(n.lon)").Return(n => n.Id()).Results;

NOTE: I am retrieving the node IDs assigned by Neo4j--not an Id property that I have created or assigned myself--using the ID() function.

Neo4j node IDs in hand, I iterate through them and add each one to the simple point layer I created earlier:

	var restClient = new RestClient(new Uri(baseUri + "/db/data/ext/SpatialPlugin/graphdb/addNodeToLayer"));
	foreach (var id in nodeIds)
	{
		var request = new RestRequest(Method.POST);
		request.AddJsonBody(new { layer = indexName, node = baseUri + "/db/data/node/" + id });
		var response = restClient.Execute(request);
		Console.WriteLine(response.Content);
	}

Finally, I execute a Cypher query to set the "id" property I described earlier (the one that was left empty) to the Neo4j-generated node ID using the ID() function: 

	client.Cypher.Match("(n:" + nodeLabel + ")").Set("n.id = id(n)").ExecuteWithoutResults();

Setting this "id" property allows you to perform Cypher queries using your spatial index. If this were not set, you would be limited to queries using the Neo4j Spatial REST API only. 

Initially, this may seem like a minor distinction, but Cypher makes it much easier to combine geospatial querying with other filters (e.g. keywords, dates, etc.). 

Special thanks to StackOverflow user [Jim Biard](http://stackoverflow.com/users/3813694/jim-biard) for this [great answer](http://stackoverflow.com/a/24700885).

As part of my trial and error, I read several tutorials and answers that recommended adding each node to an index like so:

	var restClient = new RestClient(new Uri(baseUri + 	"/db/data/eindex/node/" + indexName));
	var request = new RestRequest(Method.POST);
	request.AddJsonBody(
		new
			{
				key = "dummy",
				value = "dummy",
				uri = baseUri + "/db/data/node/" + nodeId
			});
	var response = restClient.Execute(request);
	Console.WriteLine(response.Content);

In my experience, this step is not necessary, and I found it most useful to add the Neo4j nodes directly to the spatial index as described above.