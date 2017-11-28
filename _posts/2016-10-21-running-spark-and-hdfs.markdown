---

title: Running Spark + HDFS in DC/OS on AWS
date: '2016-10-21 00:09:31'
---

#Great Idea, Right?

I wanted to collect some benchmarks for reading and writing data using Spark and HDFS. In theory, spinning up a [DC/OS](https://dcos.io/) cluster on [Amazon Web Services](https://aws.amazon.com/) seems like an easy way to do it. It turns out it's slightly more complicated in some non-intuitive ways. Hopefully this helps someone else out (or quite possibly, future me).

# The Good News
The good news is that DC/OS provides a [step-by-step guide](https://dcos.io/docs/1.7/administration/installing/cloud/aws/) for installing DC/OS on AWS, complete with a handy [CloudFormation template](https://downloads.dcos.io/dcos/EarlyAccess/commit/14509fe1e7899f439527fb39867194c7a425c771/aws.html). This part is great. Follow the instructions, and in a few minutes you should have a healthy DC/OS cluster with 5 agent nodes and either 1 or 3 master nodes, depending on whether you use the "single" or "HA" template. 

Once the cluster is ready, go to the DC/OS web UI by navigating to the Mesos master hostname, which can be found on the "Outputs" tab of the CloudFormation management page.

#The Bad News
The bad news is Spark and HDFS do not work together on DC/OS out of the box, and this is not covered anywhere in the documentation. In order to get them to work together, the HDFS hdfs-site.xml and core-site.xml files need to be hosted elsewhere, and that location is then configured in Spark.

#Install HDFS
From the DC/OS web UI, install the HDFS package. Once the install is finished and HDFS is running, find the hdfs-site.xml and core-site.xml files and download them. These two files need to be hosted somewhere else where the DC/OS cluster can still access them. The easiest solution is probably to drop them in an S3 bucket and make them publicly accessible.

#Install Spark
Use the DC/OS web UI and select the Spark package to install. **STOP**. 

In the dialog that appears, use the advanced config option and set the "HDFS config URL" to the URL where the two HDFS XML config files mentioned above are hosted. Once this configuration option is set, proceed with the install.

According to the [DC/OS Spark documentation](https://docs.mesosphere.com/1.7/usage/service-guides/spark/install/), *"By default, DC/OS Spark jobs are configured to read from DC/OS HDFS."* Unfortunately, as of DC/OS 1.7, this does not appear to be the case. If Spark is installed using the default settings, it is unable to find the HDFS name node. The docs do describe how to use a custom HDFS config, but it appears this is actually the only way to enable Spark to connect to HDFS. 

The docs go on to describe where the DC/OS HDFS config files are served. While it might seem like the logical thing to do would be to set the Spark HDFS config URL to the URL on the DC/OS cluster where the HDFS XML files are located, this does not work. Security permissions prevent Spark from being able to access them, thus the need to host them on S3. 

¯\\_(ツ)_/¯

