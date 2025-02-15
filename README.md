
# IBM DOcloud GreenTruck Sample

## Optimization Problem


A trucking company has a hub and spoke system. The shipments to be delivered are specified by an originating spoke, a 
destination spoke, and a shipment volume. The trucks have different types defined by a maximum capacity, a speed, and a
cost per mile. The model assigns the correct number of trucks to each route in order to minimize the cost of 
transshipment and meet the volume requirements. There is a minimum departure time and a maximum return time for trucks
at a spoke, and a load and unload time at the hub. Trucks of different types travel at different speeds. Therefore,
shipments are available at each hub in a timely manner. Volume availability constraints are taken into account, meaning 
that the shipments that will be carried back from a hub to a spoke by a truck must be available for loading before the 
truck leaves.

The assumptions are:
* exactly the same number of trucks that go from spoke to hub return from hub to spoke;
* each truck arrives at a hub as early as possible and leaves as late as possible; and 
* the shipments can be broken arbitrarily into smaller packages and shipped through different paths.

## Architecture

This sample shows how to build a simple web application that integrates with IBM DOcloud services:
* The webserver is written in Java and leverages the IBM Websphere Liberty runtime.
* The client uses the AngularJS framework to create simple pages and interact with the server using custom REST APIs.
* The data is stored in a MongoDB JSON datastore.
* The data and results are streamed back and forth between DOcloud and the MongoDB datastore.
* There is a single DOcloud job running at a time, and a single solution is stored in the database.
* Websockets are used to update the clients in real time for job submission and solution availability.


## Installation


### Prerequisites

1- Install [node.js](https://nodejs.org/download/).
Once installed, verify that you can execute `node` and `npm` from the command line:
```
node --version
npm --version
```

2- Install bower.
You will use bower to download and install frontend libraries. The following command installs bower:
```
npm install -g bower
```
You can review additional [installation documentation](http://bower.io/#install-bower) if necessary.

3- Install [Java 7](http://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html).
Once installed, you can check that it is accessible using this command:
```
java -version
```

4- Install [Maven](https://Maven.apache.org/download.cgi).
Once installed, you can check that it is accessible using this command:
```
mvn --version
```
In the following steps, you will need to edit the maven setting file located at `~/.m2/settings.xml`.
If you do not have this file already, a default one is provided in this repository (see file`settings.xml`), 
so that you can copy it to this location and fill out the placeholders as requested below.

5- Install [MongoDB](http://docs.mongodb.org/manual/installation/).
Once installed, you can check that it is accessible using this command:
```
mongo --version
```

6- Get an IBM WAS Liberty license.
During the build, the IBM WAS Liberty runtime will be downloaded and used to package the server. You will need to review and agree to the 
[license](http://public.dhe.ibm.com/ibmdl/export/pub/software/websphere/wasdev/downloads/wlp/8.5.5.6/lafiles/runtime/en.html).
Note the code at the end of the license file following `D/N:`.
Copy this code in a Maven property in your `~/.m2/settings.xml` settings file like this:
```xml
<profile>
  <id>wlp</id>
  <activation>
    <activeByDefault>true</activeByDefault>
  </activation>
  <properties>
	<wlp.licenseCode>...</wlp.licenseCode>
  </properties>
</profile>
```

7- Get the IBM DOcloud base URL and an API key.
Copy the base URL and the API key in Maven properties in your `~/.m2/settings.xml` settings file like this:
```xml
<profile>
  <id>docloud</id>
  <activation>
    <activeByDefault>true</activeByDefault>
  </activation>
  <properties>
      <docloud.baseurl>...</docloud.baseurl>
      <docloud.apikey.clientid>...</docloud.apikey.clientid>
  </properties>
</profile>
```

8- Download and install the IBM DOcloud API for Java library.
You can download the library from the [developer center](https://developer.ibm.com/docloud/docs/java-client-api/java-client-library/).
Extract the jar file starting with `docloud_api_java_client` from the zip file (do not take the javadoc jar file).
Then add the jar file to your Maven local repository like this:
```
mvn install:install-file -Dfile=<path-to-file> -DgroupId=com.ibm.optim.oaas -DartifactId=api_java_client -Dversion=1.0-R1-SNAPSHOT -Dpackaging=jar
```

9- (Optional) Signup and declare [IBM Bluemix](http://www.ibm.com/cloud-computing/bluemix/) credentials.
If you want to deploy the sample on the Cloud using the IBM Bluemix platform, the build procedure will do this for you. 
Copy your username and password in a Maven server declaration in your `~/.m2/settings.xml` settings file like this:
```xml
<servers>
   <server>
      <id>bluemix_prod</id>
  	  <username>...</username>
	  <password>...</password>
   </server>
</servers>
```

Then, create a qualifier that will make the route unique. If the qualifier is `mike`, the application
will be deployed to `greentruck-mike.mybluemix.net`. To do so, declare a property in your  `~/.m2/settings.xml` setting file like this:
```xml
<profile>
  <id>bluemix</id>
  <activation>
    <activeByDefault>true</activeByDefault>
  </activation>
  <properties>
    <cf.org>...</cf.org>
    <cf.urlQualifier>...</cf.urlQualifier>
  </properties>
</profile>
```

Finally, in your Bluemix dashboard, click on `Create a Space` and call it `demo`. The application will be deployed in this
space.

### Build

1- Download frontend dependencies.
From the root project directory, type this command to install all frontend dependencies:
```
bower install
```

2- Compile and package.
```
mvn package
```

### Run locally

1- Start Mongo.
You need to start `mongod` in another window or in the background using this command:
```
mongod
```

2- Run the server.
```
mvn liberty:run-server
```
The server should be running on `http://localhost:9080/`.

### Deploy to IBM Bluemix

If you have followed the optional step to register for IBM Bluemix and declare your credentials, you can compile, package, 
and deploy to Bluemix in one simple command: 

```
mvn deploy
```

If you want to deploy without running the tests locally (MongoDB is then not necessary locally), then deploy to Bluemix
with this command:
```
mvn deploy -DskipTests
```

## Dependencies

### Frontend dependencies loaded with Bower (see bower.json)
    
* bootstrap
* jquery
* angular
* angular-route
* swagger-ui
* font-awesome
* d3
* angular-smart-table
* angular-websocket
    
### Server side runtime dependencies loaded by Maven (see pom.xml)

* org.mongojack:mongojack
* com.ibm.optim.org.slf4j:slf4j-jdk14
* om.ibm.optim.oaas:api_java_client
* javax:javaee-api
* com.wordnik:swagger-jaxrs_2.10
* org.apache.httpcomponents
* com.ibm.icu


## License

This sample is delivered under the Apache License Version 2.0, January 2004 (see LICENSE.txt).

