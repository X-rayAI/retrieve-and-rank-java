# Retrieve and Rank Demo app
The IBM Watson™ Retrieve and Rank service helps users find the most relevant information for their queries by using a combination of search and machine learning algorithms to detect "signals" in the data. Developers load their data into the service, which is built on top of Apache Solr. The can train a machine learning model based on known results, and use the model to provide improved results to their end users.

Here's a [quick demo](http://retrieve-and-rank-demo.mybluemix.net/rnr-demo/dist/index.html#/).

## How it works
This application uses the publicly available test data called the [Cranfield collection](http://ir.dcs.gla.ac.uk/resources/test_collections/cran/). The collection contains abstracts of aerodynamics journal articles, a set of questions about aerodynamics, and indicators of how relevant an article is to a question. Some questions are not used as training data, which means that you can use them to validate the performance of the trained ranker. This subset of questions are are used in the demo.

Give it a try! Click this button to fork into IBM DevOps Services and deploy your own copy of this application on Bluemix."

[![Deploy to Bluemix](https://bluemix.net/deploy/button.png)](https://bluemix.net/deploy?repository=https://github.com/watson-developer-cloud/retrieve-and-rank-java)

## Before you begin
Ensure that you have the following prerequisites before you start:
* A Bluemix account. If you don't have one, [sign up][sign_up]. Experimental Watson Services are free to use.
* [Java Development Kit](http://www.oracle.com/technetwork/java/javase/downloads/index.html) version 1.7 or later.
* [Eclipse IDE for Java EE Developers](https://www.eclipse.org/downloads/packages/eclipse-ide-java-ee-developers/marsr).
* [Apache Maven](https://maven.apache.org/download.cgi), version 3.1 or later.
* [Git](https://git-scm.com/downloads).
* [Websphere Liberty Profile server](https://developer.ibm.com/wasdev/downloads/liberty-profile-using-non-eclipse-environments/), if you want to run the app in your local environment.

## Setup
To run the Retrieve and Rank demo app, you need an app and a Retrieve and Rank service instance bound to it. The following steps use Eclipse, but you can use the IDE of your choice.

### Get the project from GitHub
1. Create a local clone of this repository.
2. Add the repository to your local Eclipse workspace.

### Set up the Bluemix Environment
#### Creating an App
1. [Log in to Bluemix](https://console.ng.bluemix.net/) and navigate to the *Dashboard* on the top panel.
2. Create your app.
      1. Click **CREATE APP**.
      2. Select **WEB**.
      3. Select the starter **Liberty for Java**, and click **CONTINUE**.
      4. Type a unique name for your app, such as `rr-demo-app`, and click **Finish**.
      5. Select **CF Command Line Interface**. 
      6. If you haven't installed Cloud Foundry, click **Download CF Command Line Interface**. This link opens a GitHub repository. Download and install CF locally.

#### Adding an instance of the Retrieve and Rank service to your App
You can create a service instance to bind to your app or bind an existing instance:  

**Binding a new service instance to your app**
  1. [Log in to Bluemix](https://console.ng.bluemix.net/) and navigate to the *Dashboard* on the top panel. Find the app that you created in the previous section, and click it.
  2. Click **ADD A SERVICE OR API**.
  3. Select the **Watson** category, and select the **Retrieve and Rank** service.
  4. Make sure that your app is specified in the **App** dropdown on the right-hand side of the pop-up window under **Add Service**.
  5. Type a unique name for your service in the **Service name** field, such as `rnr-sample-service`.
  6. Click **CREATE**.
  7. Click **RESTAGE** to restart your app. If the app is not started, click **START**.

Or 

**Binding an existing service instance**
  1. [Log in to Bluemix](https://console.ng.bluemix.net/) and navigate to the *Dashboard* on the top panel. Locate and click on the app you created in the previous section.
  2. Click **BIND A SERVICE OR API**.
  3. Select the Retrieve and Rank service that you want to bind to your app, and click **ADD**.
  4. Click **RESTAGE** to restart your app.  
  5. To validate that your app is running and available, open http://{yourAppName}.mybluemix.net where {yourAppName} is the name of your app. For example, http://rr-demo-app.mybluemix.net/

#### Training the Service by completing the tutorial
Configure the service to use the Cranfield collection and train a ranker with the Cranfield data.

* Complete the tutorial in <a href="http://www.ibm.com/smarterplanet/us/en/ibmwatson/developercloud/doc/retrieve-rank/get_start.shtml" target="_blank">Getting started with the Retrieve and Rank service</a>.     
* As you complete the tutorial, save this information:
  * Solr cluster ID: This unique identifier of the Apache Solr Cluster that you create.
  * Collection name: The name you give to the Solr collection when you create it.  
  * Ranker ID: The unique identifier of the ranker you create.


#### Setting up environment variables in Bluemix
Use the values from the tutorial to specify environment variables in your app:
1. Navigate to the application dashboard in Bluemix. 
2. Click the Retrieve and Rank application you created earlier. 
3. Click **Environment Variables**. 
4. Click **USER-DEFINED**. 
5. Add three environment variables. Use the values that you copied from the tutorial:

  * CLUSTER_ID
  * COLLECTION_NAME
  * RANKER_ID

### Build the app
This project is configured to be built with Maven.
1. In your Eclipse window, expand the *retrieve-and-rank-java* project that you cloned from GitHub.
2. Right-click the project and select `Maven -> Update Project` to update Maven dependencies.
3. Keep the default options, and click **OK**.
4. Navigate to the location of your default deployment server. For Websphere Liberty, it is located in a location similar to this: *../LibertyRuntime/usr/servers/{server-name}*.
5. Open the `server.env` file (create one if it doesn't exist), and set the following entries:
  
  * VCAP_SERVICES: Add the JSON object that is displayed in the *Environment Variables* section of your application on Bluemix. When you add the JSON here, format it on one line.
  * For CLUSTER_ID, COLLECTION_NAME, and RANKER_ID, use the same values that you used for the environment variables in your app on Bluemix.
    
    For example:
    ```
    VCAP_SERVICES={"retrieve_and_rank": [{"name": "rr-demo-app","label": "retrieve_and_rank","plan": "standard","credentials": {"url": "https://gateway.watsonplatform.net/retrieveandrank/api", "username": "{username}", "password": "{password}" } } ] }
    CLUSTER_ID=_{cluster_id}
    COLLECTION_NAME={collection_name}
    RANKER_ID={ranker_id}
    ```
    
6. Switch to the navigator view in Eclipse. 
7. Right-click the `pom.xml` file, and select `Run As -> Maven Install`. Installation of Maven begins. During the installation, the following tasks are completed:
    * The JS code is compiled. The various Angular JS files are aggregated, uglified, and compressed. Various other pre-processing is performed on the web code, and the output is copied to the `retrieve-and-rank-java/src/main/webapp/dist` folder in the project.
    * The Java code is compiled, and JUnit tests are executed against the Java code. The compiled Java and JavaScript code and various other artifacts that are required by the web project are copied to a temporary location, and a `.war` file is created in */retrieve-and-rank-java/target directory*. This WAR file is used to deploy the application on Bluemix in the next section.


### Deploy the app
You can run the application on a local server or on Bluemix. Choose one of the following methods, and complete the steps:
#### Deploying the app on your local server in Eclipse
1. In Eclipse, click `Window -> Show View -> Servers`.
2. In the **Servers** view, right-click and select `New -> Server`.
3. Select the **WebSphere Application Server Liberty Profile** in the *Define a New Server* window and click **Next**.  
4. Configure the server with the default settings.  
5. In the **Available** list in the **Add and Remove** dialog, select the *retrieve-and-rank-java* project and click **Add >**. The project is added to the runtime configuration for the server in the **Configured** list.
6. Click **Finish**.
7. Copy the *server.env* file, which you edited earlier, from *retrieve-and-rank-java/src/it/resources/server.env* to the root folder of the newly defined server (For example, *wlp/usr/defaultserver/server.env*).  
8. Start the new server, and open http://localhost:serverPort/rnr-demo/dist/index.html#/ in your favorite browser, where {yourAppName} is the specific name of your app.
9. Execute the queries against the service!

Or

#### Deploying the app on the Websphere Liberty Profile in Bluemix
Deploy the WAR file that you built in the previous section by using Cloud Foundry commands.
1. Open the command prompt.

2. Navigate to the directory that contains the WAR file that you generated:
  
  ```sh
  $ cd retrieve-and-rank-java/target
  ```
  
3. Connect to Bluemix:

  ```sh
  $ cf api https://api.ng.bluemix.net
  $ cf login -u {your user ID}
  ```
  
4. Deploy the app to Bluemix:

  ```sh
  $ cf push {yourAppName} -p rnr-demo.war
  ```

  where {yourAppName} is the name of your app.
  
6. Navigate to [Bluemix](https://console.ng.bluemix.net/) to make sure the app is started. Click *Start*, if necessary.
7. Open http://{yourAppName}.mybluemix.net/rnr-demo/dist/index.html#/ in your browser, where {yourAppName} is the specific name of your app.
8. Execute queries against the service.

## Reference information
* Retrieve and Rank service [documentation](http://www.ibm.com/smarterplanet/us/en/ibmwatson/developercloud/doc/retrieve-rank/)
* [Configuring](http://www.ibm.com/smarterplanet/us/en/ibmwatson/developercloud/doc/retrieve-rank/configure.shtml) the Retrieve and Rank service
* Retrieve and Rank [API reference](http://www.ibm.com/smarterplanet/us/en/ibmwatson/developercloud/retrieve-and-rank/api/v1/)

[sign_up]: https://apps.admin.ibmcloud.com/manage/trial/bluemix.html?cm_mmc=WatsonDeveloperCloud-_-LandingSiteGetStarted-_-x-_-CreateAnAccountOnBluemixCLI
[cloud_foundry]: https://github.com/cloudfoundry/cli
