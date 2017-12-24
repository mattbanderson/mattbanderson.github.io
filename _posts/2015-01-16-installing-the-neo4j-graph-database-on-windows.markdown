---

title: Installing the Neo4j Graph Database on Windows
date: '2015-01-16 03:34:08'
---

# Graph Database Overview
First, a brief introduction to graph databases. Graph databases are a convenient, natural way to represent almost any kind of data. As a result, they have experienced a rapid growth in popularity over the last several years.

Data is stored using a graph structure; in other words, nodes, edges, and properties are used represent information and relationships.

[Neo4j](http://neo4j.com/) is the most popular graph database available today. It is open-source, fully ACID compliant, and capable of scaling to billions of nodes and relationships.

# Official Neo4j Documentation
The official documentation for installing Neo4j on Windows can be found here: [http://neo4j.com/docs/2.1.6/server-installation.html#windows-install](http://neo4j.com/docs/2.1.6/server-installation.html#windows-install).

# Why Neo4j?
I am currently working on a project that involves data where the relationships in the data are every bit as important as the actual data itself.

A graph database seemed like a natural fit, so I set out to learn how to get started with Neo4j.

# Using the Installer
Initially, I installed Neo4j Community Edition locally using the Windows 64-bit installer. To download an installer (or other release), head over to [http://neo4j.com/download/](http://neo4j.com/download/).

The installer worked flawlessly, and I had it up and running in minutes. The installer adds a start menu option that opens a small program window to start/stop/configure your local Neo4j instance.

This is a great way to quickly experiment with Neo4j and find out if it meets your needs.

# Loading Data
My next objective was loading data into the database.  I wrote a small C# program to read in data from a CSV file and create nodes and relationships.

I used the [Readify/Neo4jClient](https://github.com/Readify/Neo4jClient) (also available via NuGet) to greatly simplify the process. It provides a fluent interface for writing Cypher queries (Neo4j's query language) and executing them against a database, and the [wiki documentation](https://github.com/Readify/Neo4jClient/wiki) had everything I needed to get started.

# A Word of Caution When Loading Data
A brief digression and warning: even when using Readify/Neo4jClient, some string concatenation is still necessary to build Cypher statements. It is *very* easy to change something (e.g. a property name on an object) and then forget to make the corresponding change in a string in a Cypher query.

I found this out when I started loading data and my create operations quickly slowed to a crawl. I thought I was testing for uniqueness before creating, but what I was actually doing was always creating new nodes and then pointlessly searching through all the existing ones to check non-existent properties (oops!).

Once I caught the mistake, my data ingestion speed *massively* increased.  Ultimately, it took me approximately five hours to create about 155,000 nodes and 210,000 relationships with no other optimizations besides fixing the previously described mistake.

I should also add that I was checking for uniqueness as I created nodes and relationships, so my ingestion speed gradually slowed as more and more nodes and edges were added.

# To the Server!
With Neo4j working and a database full of nodes and relationships, I wanted to make it available for other members of my to check out and experiment with. I logged into one of our development servers and ran the installer.

It still worked great, but it occurred to me that having an application to start/stop the database was not the ideal solution for a server install. As soon as I logged off, the application would close, and the database would stop.

Fortunately, Neo4j has an option to install it as Windows service. This option requires a Java Virtual Machine (JVM) to be installed on the computer, whereas the installer does not have this prerequisite.

# Installing the JVM
This particular server did not have a JVM installed, so off I went to the [Oracle site](http://www.oracle.com/technetwork/java/javase/downloads/index.html) to download it.

Neo4j "recommends" that you install Java 7; however, I can tell you from experience that, as of v2.1.6, you *must* use Java 7, as Java 8 will not work and you will receive an error message when running the install script, something like:

*"ERROR! You are using an unsupported version of Java, please use Oracle HotSpot 1.7".*

When you go to the Oracle site to download Java, be sure to pick the right version (I had to scroll down), as Java 8 will typically be listed first. I downloaded the [Server JRE version for Windows x64](http://www.oracle.com/technetwork/java/javase/downloads/server-jre7-downloads-1931105.html) and followed the [Server JRE installation instructions](http://docs.oracle.com/javase/7/docs/webnotes/install/windows/server-jre-installation-windows.html).

Another note: the download site makes it appear that you will download a .tar.gz file, but my download was actually just a .gz file. I was still able to unzip and extract it successfully by manually changing the extension to .tar.gz.

Once the JVM installed, make sure you set the JAVA_HOME environment variable to the location where you installed the JRE.

# Install Neo4j as a Windows Service
With the JVM successfully installed, I followed the "[As a Windows service](http://neo4j.com/docs/2.1.6/server-installation.html#windows-install)" instructions in the Neo4j Manual. I downloaded the 64-bit Zip file from [http://neo4j.com/download]([http://neo4j.com/download]), extracted it to a directory on the server, and ran the install script. It worked perfectly, and Neo4j was running as a service!

# Configuring Neo4j
In the directory structure created by extracting the Neo4j Zip file, there should be a "conf" folder. This folder contains several .properties files that can be used to configure your Neo4j install.

By default, Neo4j is configured to use http://localhost:7474/.

Since I wanted to make Neo4j available for the rest of my team to use, in the neo4j-server.properties file, I uncommented the "org.neo4j.server.webserver.address" property and set it to the server's IP address.

There is also an "org.neo4j.server.database.location" property that allows you to specify the location of your Neo4j database.

If you change any of the properties in this file or any of the other configuration files, be sure to restart the Neo4j service!

# Conclusion
Other than a couple minor JVM-related hiccups, installing Neo4j could not have been much smoother.

I should also add that on top of the graph database itself, Neo4j also comes with a very slick browser-based UI that allows you to interact with your data and run Cypher queries interactively. So not only is it very easy to set up, it's just as easy to start visualizing the data you put in it!

For an even lighter-weight, no-install test drive, check out the UI using Neo4j's interactive "Getting started with Cypher" tutorials at [http://neo4j.com/docs/2.1.6/tutorials-cypher.html]([http://neo4j.com/docs/2.1.6/tutorials-cypher.html]).
