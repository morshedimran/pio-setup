# Environment and Source Code Setup #

----------

List of tools required for code setup:

1. JDK 7
2. Maven
3. Eclipse Luna
4. ElasticSearch version 1.2.1

## JDK 7 installation instructions: ##

1. Download jdk7 from [http://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html](http://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html) as per machines operating system. Before downloading you need to accept license agreement
2. After downloading just run the setup and follow on screen instruction to complete the setup.

	
> Follow below steps for setting the PATH variable for JDK (jdk1.7.0\bin) as follows on Windows:

- Click Start, then Control Panel, then System.
- Click Advanced, then Environment Variables.
- Append the location of the bin folder of the JDK, for the PATH variable in System Variables. 
	
Note: The PATH environment variable is a series of directories separated by semicolons (;).

For Example: Full path looks something like C:\Program Files\Java\jdk1.7.0\bin.

To verify that JAVA is correctly installed, Open a new command prompt and run command java -version

## Maven installation instructions: ##

1. Download latest maven version from [https://maven.apache.org/download.cgi](https://maven.apache.org/download.cgi)
1. Unzip the downloaded archive folder to any location on machine.

	
> Follow below steps for setting the PATH variable for apache-maven-3.3.3\bin directory as follows on Windows:

* Click Start, then Control Panel, then System.
* Click Advanced, then Environment Variables.
* Append the location of the bin folder of the Maven, for the PATH variable in System Variables.

Note: The PATH environment variable is a series of directories separated by semicolons (;).

For Example: Full path looks something like C:\apache-maven-3.3.3\bin.

To verify that maven is correctly installed, Open a new command prompt and run command mvn --version

## ElasticSearch Installation instructions: ##

1. Download Elastic Search (0.19.9 or 1.2.1) from [http://www.elasticsearch.org/download/](http://www.elasticsearch.org/download/)
1. Unzip the downloaded archive folder to any location on machine.
1. Go to config folder inside ElasticSearch. Modify the elasticsearch.yml file.

> Make following changes to the elasticsearch.yml file:

* Change cluster.name to elasticsearch_XXXXXX> 
<pre>
cluster.name: elasticsearch_XXXXXX
</pre>
* Uncomment the following definitions:

Before Change
<pre>
# Note, that for development on a local machine, with small indices, it usually
# makes sense to "disable" the distributed features:
#
# index.number_of_shards: 1
# index.number_of_replicas: 0
</pre>
After Change
<pre>
# Note, that for development on a local machine, with small indices, it usually
# makes sense to "disable" the distributed features:
#
index.number_of_shards: 1
index.number_of_replicas: 0
</pre>
* Add the following:
<pre>
action.auto_create_index: false
index.refresh_interval : -1
</pre>
Install elasticsearch-head from [https://github.com/mobz/elasticsearch-head/archive/master.zip](https://github.com/mobz/elasticsearch-head/archive/master.zip) 
Unzip the downloaded archive folder to any location on machine.

> **ElasticSearch start up:**
> 
> Execute bat file.
> elasticsearch \ bin \ elasticsearch.bat
> 
> Open elasticsearch-head-master\index.html
> 
> Check if the ElasticSearch is up running on local machine and (cluster health should be green)

## Eclipse Luna Installation instructions: ##

Install "Eclipse IDE for Java EE Developers" from [http://www.eclipse.org/downloads/packages/release/Luna/R](http://www.eclipse.org/downloads/packages/release/Luna/R)
Eclipse environment setup:
Eclipse environment setup:


1. Install Maven plugin in eclipse.
[Help] -> [Install New Software ...], type the following at [Work with] [http://subclipse.tigris.org/m2eclipse/latest/](http://subclipse.tigris.org/m2eclipse/latest/) and follow the instructions.

1. Install Subversion plugin in eclipse.
[Help] -> [Install New Software ...], type the following at [Work with] [http://subclipse.tigris.org/update_1.10.x/](http://subclipse.tigris.org/update_1.10.x/) and follow the instructions.


**Code setup:**


- Checkout the project from [http://ooshima.vpdc.sg.soft.fujitsu.com/svn/pcs/dc1/trunk](http://ooshima.vpdc.sg.soft.fujitsu.com/svn/pcs/dc1/trunk) to local machine.


- Follow below steps to import dc1-core project in eclipse:
Select [File] → [Import] → [Maven] → [Existing Maven Projects] [Root Directory] → [Browse to dc1\core folder on your machine] → [Finish]


- Follow below steps to configure web project setting in eclipse:
[Right-click the dc1-core project, and select Properties] → [Select Web Project Settings] → [Change the Context root value to "dc1-core" if any other value is mentioned]


- Ensure JDK7 is configured in project build path. If not then follow the below steps:
[Right-click the dc1-core project, and select Build Path] → [Configure Build Path..] → [Click on Libraries Tab, Select JRE System Library] → [Click Edit button and update the path of jdk7]

- Create a file "dc-config.properties" with the below content inside dc1-core\src\ main\resources folder.

<pre>
##############################################
# core
##############################################

# elasticsearch
com.fujitsu.dc.core.es.cluster.name=<ElasticSearch_Cluster_Name>
com.fujitsu.dc.core.es.unitPrefix=<ElasticSearch_Cluster_Name>

# Ads
com.fujitsu.dc.core.masterToken=c3s-innov
com.fujitsu.dc.core.es.ads.jdbc.url=jdbc:mysql://10.123.107.118:3306
com.fujitsu.dc.core.es.ads.jdbc.user=dc1-core
com.fujitsu.dc.core.es.ads.jdbc.password=c3s-innov_core
com.fujitsu.dc.core.es.ads.type=none

# lock type configurations
com.fujitsu.dc.core.lock.type=inProcess

# cache configurations (memcached protocol)
com.fujitsu.dc.core.cache.type=inProcess
com.fujitsu.dc.core.cache.cell.enabled=false
com.fujitsu.dc.core.cache.box.enabled=false
com.fujitsu.dc.core.cache.schema.enabled=false

com.fujitsu.dc.core.security.sercret16 = {any lower-case alphanumeric 16-digit}

##############################################
# engine
##############################################
To change to those specified in com.fujitsu.dc.engine.elasticsearch.cluster = elasticsearch_XXXXXX \u2605 elasticsearch.yml.

# Unit Prefix
com.fujitsu.dc.engine.es.unitPrefix=<ElasticSearch_Cluster_Name>
</pre>

- In dc1-core pom file, by default "dc1-core-es0.19" is active. If ElasticSeacrch version 0.19 is downloaded then no changes are required in pom file and if ElasticSeacrch version 1.2.1 is downloaded then set "activeByDefault" of dc1-core-es0.19 to false and "activeByDefault" of dc1-core-es1.2 to true.
If ElasticSearch 1.2.1 is downloaded then profile for ElasticSearch should be like below:
<pre>
	&lt;profile&gt;
		&lt;id&gt;es0.19&lt;/id&gt;
		&lt;activation&gt;
			&lt;activeByDefault&gt;false&lt;/activeByDefault&gt;
		&lt;/activation&gt;
		&lt;properties&gt;
			&lt;final.name&gt;dc1-core-es0.19&lt;/final.name&gt;
			&lt;esapi.version&gt;0_19&lt;/esapi.version&gt;
			&lt;java.version&gt;1.6&lt;/java.version&gt;
			&lt;jvm.option&gt;-Xms512m -Xmx1024m -XX:MaxPermSize=256m&lt;/jvm.option&gt;
			&lt;logback.version&gt;1.0.7&lt;/logback.version&gt;
			&lt;slf4j.version&gt;1.6.6&lt;/slf4j.version&gt;
			&lt;/properties&gt;
	&lt;/profile&gt;
	&lt;profile&gt;
		&lt;id&gt;es1.2&lt;/id&gt;
		&lt;activation&gt;
			&lt;activeByDefault&gt;true&lt;/activeByDefault&gt;
		&lt;/activation&gt;
		&lt;properties&gt;
			&lt;final.name&gt;dc1-core-es1.2&lt;/final.name&gt;
			&lt;esapi.version&gt;1_2&lt;/esapi.version&gt;
			&lt;java.version&gt;1.6&lt;/java.version&gt;
			&lt;jvm.option&gt;-Xms512m -Xmx1024m -XX:MaxPermSize=256m&lt;/jvm.option&gt;
			&lt;logback.version&gt;1.0.7&lt;/logback.version&gt;
			&lt;slf4j.version&gt;1.6.6&lt;/slf4j.version&gt;
		&lt;/properties&gt;
	&lt;/profile&gt;	
</pre>

- Before building dc1-core project make sure all repositories mentioned in dc1-core#pom file are accessible from your machine.

- Run Maven Clean command and Maven build command with goal install or package. Make sure build is success.

- To verify code setup is done successfully in eclipse, execute com.fujitsu.dc.test.setup.Setup#reset with JUnit and see data is created in elasticsearch-head.
