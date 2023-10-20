### [Index](https://github.com/K-PaaS/Guide-eng/blob/master/README.md) > [AP User Guide](../README.md) > Node.js API Service Metering Development

## Table of Contents
1. [Document Outline](#1)
     * [1.1. Purpose](#2)
     * [1.2. Range](#3)
     * [1.3. References](#4)
2. [Node.js API Metering Development Guide](#5)
     * [2.1. Outline](#6)
     * [2.2. Construct Development Environment](#7)
	     * [2.2.1 Installation of Node.js and npm](#8)
	     * [2.2.2 CF-Abacus Installation](#9)
     * [2.3 Sample API Service Broker and Dashboard Development](#10)
     * [2.4 Sample API Service Development](#11)
	     * [2.4.1 Sample API Service Application Code Implementation](#12)
	     * [2.4.2 Sample API Service Application Metering Interworking Item](#13)
     * [2.5 API Service Interworking Sample Application Development](#14)
	     * [2.5.1 API Service Interworking Sample Application Code](#15)
	     * [2.5.2 API Service Interworking Sample Application Interface Item](#16)
	     * [2.5.3 VCAP_SERVICES Environment Setting Information](#17)
     * [2.6 Metering/Rating/Billing Policy](#18)
	     * [2.6.1 Metering Policy](#19)
	     * [2.6.2 Rating Policy](#20)
	     * [2.6.3 Billing Policy](#21)
	     * [2.6.4 Policy Registration](#22)
     * [2.7 Deployment](#23)
	     * [2.7.1 Application Platform Login](#24)
	     * [2.7.2 Create API Service Broker](#25)
	     * [2.7.3 API Service Application Deployment and Service Registration](#26)
	     * [2.7.4 API Service Interworking Sample Application Deployment and Service Connection](#27)
     * [2.8 Test](#28)
     * [2.9 API and CF-Abacus Interworking Test](#29)
     * [2.10 Sample Code](#30)
 


# <div id='1'></div> 1. Document Outline

### <div id='2'></div> 1.1 Purpose

This document (the Node.js API Service Metering Application Development Guide) describes how to meter API services by interworking the metering plug-in of the Application platform project with the Node.js API application.

### <div id='3'></div> 1.2 Range
The range of this document is limited to the development of Node.js API service application and CF-Abacus interworking of Application platform projects.

### <div id='4'></div> 1.3 References
**<https://docs.cloudfoundry.org/devguide/>**  
**<https://docs.cloudfoundry.org/buildpacks/node/node-tips.html>**  
**<https://nodejs.org/>**  
**<http://expressjs.com/ko/>**  
**<https://github.com/cloudfoundry-incubator/cf-abacus>**  


# <div id='5'></div> 2. Node.js API Metering Development Guide

### <div id='6'></div> 2.1 Outline

The API service and the application using the API service are to be written in Node.js language. Bind the application that uses API Service and the API service.
Using environmental information (VCAP_SERVICES) bound to the application, access information for each service is acquired and applied to the application to create an application that calls API services. The API service also creates an application that processes service requests and sends API usage history to CF-ABACUS.

![26](https://user-images.githubusercontent.com/104418463/200229570-ed57aa12-6b47-4fff-935b-65f3f5ea13ea.png)


<table>
  <tr>
    <th colspan='2'>Function</th>
    <th>Description</th>
  </tr>
  <tr>
    <td rowspan='4'>Runtime</td>
    <td width="160">Metering/Rating/Billing Policy</td>
    <td>Various policy definition information for services provided by API service providers. It is in JSON format, and when the policy is registered with CF-ABACUS, API usage is aggregated according to what is defined in the policy.<br> The policy must be defined by the service provider, refer to the following for JSON schema.<br> <u><b><a href="https://github.com/cloudfoundry-incubator/cf-abacus/blob/master/doc/api.md">https://github.com/cloudfoundry-incubator/cf-abacus/blob/master/doc/api.md</a></b></u>
    </td>
  </tr>
  <tr>
    <td>Service Broker API</td>
    <td>Refer to the Service Pack Development Guide for service broker API development as a protocol between Cloud Controller and Service Broker.<br>
    </td>
  </tr>
  <tr>
    <td>Service API</td>
    <td>Consists of an API service function provided by a service provider and a function to transmit API usage to CF-ABACUS.</td>
  </tr>
  <tr>
    <td>Dashboard</td>
    <td>A authentication to provide the service and service monitoring dashboard functions should be developed by the service provider.</td>
  </tr>
  <tr>
    <td colspan="2">CF-ABACUS</td>
    <td>It aggregates usage information collected as a CF-ABACUS core function.<br> For CF-ABACUS, install as mirco service form at the CF after installing the CF. Refer next for details.<br>
    <u><b><a href="https://github.com/cloudfoundry-incubator/cf-abacus">https://github.com/cloudfoundry-incubator/cf-abacus</a></b></u>
    </td>
  </tr>
</table>

※ This development guide describes the **API** **Service** development only. Refer to the linked site below for development and installation of other components.

### <div id='7'></div> 2.2 Construct Development Environment

For the development of Node.js application, the development environment is constructed with the following environment.

-   CF release: v226 and above
-   nodejs_buildpack: nodejs_buildpack-cached-v1.5.18.zip and above
-   Node.js: v5.11.1
-   npm: v3.8.6

### <div id='8'></div> 2.2.1 Installation of Node.js and npm

#### 1.  Installation of Node.js and npm  

	$ sudo apt-get install curl

	## When installing Node.js version 6.x
	## Register Source Repository
	$ curl -sL https://deb.nodesource.com/setup_6.x | sudo -E bash –

	## Node.js & Npm Installation
	$ sudo apt-get install -y nodejs

		## Check Node.js & Nmp Installation
		$ node -v
		$ npm -v

※ For Windows, download Node.js from the following site:  
**<a href="https://nodejs.org/ko/download/">https://nodejs.org/ko/download/</a>**

※ Development Tool  
Node.js is a javascript-based language that can use document editors such as Notepad++, Sublim Text, and EditPlus as development tools. You can also install and use Nodeclipse, the plug-in of Eclipse. As a commercial development tool, there are WebStome, etc.

### <div id='9'></div> 2.2.2 CF-Abacus Installation

Install CF-Abacus by referring to the Abacus installation guide provided separately.

### <div id='10'></div> 2.3 Sample API Service Broker and Dashboard Development

You can develop service brokers and dashboards by referring to the service broker development guide.

### <div id='11'></div> 2.4 Sample API Service Development

If there is a service request, the sample api service processes the response to the request and transmits metering information about the api service request to CF-ABACUS.

1.  Sample API Service Features  

		sample_api_node_service/
		├── .apprc
		├── .gitignore
		├── manifest.yml
		├── .npmrc
		├── package.json
		├── sampleApiService
		└── src
			├── app.js
			└── test
				└── test.js

<table>
  <tr>
    <th>File/Folder</th>
    <th>Purpose</th>
  </tr>
  <tr>
    <td>.apprc</td>
    <td>App Run Environment Setting File</td>
  </tr>
  <tr>
    <td>.gitignore</td>
    <td>When managing the shape through Git, set a file or directory that does not require configuration management.</td>
  </tr>
  <tr>
    <td>manifest.yml</td>
    <td>Configuration information for applications that you apply when deploying applications to a Application Platform<br>
    The name of the application, the distribution path, and the number of instances may be defined.
    </td>
  </tr>
  <tr>
    <td>.npmrc</td>
    <td>Npm Execution Environment Setting File</td>
  </tr>
  <tr>
    <td>package.json</td>
    <td>Used to describe the dependency information of npm required for the node.js application.<br>
    If you do not enter any information after installing the npm install command, use the information in this file to install npm.
    </td>
  </tr>
  <tr>
    <td>sampleApiService</td>
    <td>Service App Executing Script</td>
  </tr>
  <tr>
    <td>app.js</td>
    <td>Service App <br> Routing information and metering information transmission processing for a service request is defined.</td>
  </tr>
  <tr>
    <td>test.js</td>
    <td>Service app unit test module <br> Define a unit test of a service app through mocha.</td>
  </tr>
</table>
     
### <div id='12'></div> 2.4.1 Sample API Service Application Code Implementation

#### 1.  Describes the code configuration of the Package.json Sample Application.


	{
	  "name": "sample-api-node-service",   						## App Name
	  "version": "0.0.1",                    					## App Version
	  "description": "CF API Usage Service Metering Sample",
	  "main": "lib/app.js",                  					## Main of the Development Source
	  "bin": {
	    "sampleApiService": "./sampleApiService"
	  },
	  "files": [                            					## Describes the files or directories to be configured
	    ".apprc",
	    ".npmrc",
	    "manifest.yml",
	    "src/",
	    "sampleApiService"
	  ],
	  "scripts": {                           					## execute with npm run command or npm command
	    "start": "./sampleApiService start",   					## npm start: execute app
	    "stop": "./sampleApiService stop",   					## npm stop: stop app
	    "babel": "babel",                  						## npm run babel: compile developemt source
	    "test": "eslint && mocha",        						## npm test: test development source
	    "lint": "eslint",                    					## npm run lint: Check Development Source
	    "pub": "publish",                 						## npm run publish: Publish Development Source
	    "cfpack": "cfpack",               						## npm run cfpack: Packaging of compiled development sources
	    "cfpush": "cfpush"              						## npm run cfpush: Push packaged development source to cf
	  },
	  "author": "KPaaS", 
	  "license": "Apache-2.0",                    				## Declare License
	  "dependencies": {
	    "body-parser": "^1.15.2",                 				## json parser module
	    "cors": "^2.8.1",                         				## cross domain request allowing module
	    "express": "^4.14.0",                     				## node.js web framework
	    "abacus-oauth": "^0.0.6-dev.8",							## Oauth Module for Communication with Secure Abacus
	    "request": "^2.74.0",                    				## request module
	    "babel-preset-es2015": "^6.6.0",         				## Module for converting ECMA5 to ECMA6
	    "commander": "^2.8.1",                 					## Command Executing Module
	    "underscore": "^1.8.3"               					## Modules with defined functions available for javascript
	  },
	  "devDependencies": {                  					## Packages dependent on development environments
	    "abacus-babel": "file:../../tools/babel",				## Convert development source to ECMA5 -> ECMA6
	    "abacus-cfpack": "file:../../tools/cfpack",				## A package to package development source to push to cf
	    "abacus-cfpush": "file:../../tools/cfpush",  			## Package to push development source to cf
	    "abacus-coverage": "file:../../tools/coverage",			## Test source coverage rate check package for development source
	    "abacus-eslint": "file:../../tools/eslint",      		## Development Source Code Check Package
	    "abacus-mocha": "file:../../tools/mocha",				## mocha test execution package
	    "abacus-publish": "file:../../tools/publish"  			## Development source publish package
	  },
	  "engines": {
	    "node": ">=5.11.1",                     				## nodejs version
	    "npm": ">=3.8.6"                       					## npm version
	  }
	}


#### 2.  Describes the setting information required when deploying the Manifest.yml app to CF and the setting information required for the app execution environment.
.

```yml
applications:
- name: sample-api-node-service   								# Application Name
  host: sample-api-node-service   								# Appliction Host Name
  memory: 512M                    								# Application Memory Size
  disk_quota: 512M                								# Application Disk Size
  instances: 1                    								# Application Number of Instances
  command: npm start              								# Application Start Command At the CF
  path: ./.cfpack/app.zip         								# Location of the Application to be Deployed
  env:
    CONF: default              									# Command Executing Environment Setting Information
    DEBUG: s*                 									# Set debug output destination
    NODE_TLS_REJECT_UNAUTHORIZED: 0 # SSL flag off
    API: https://api.bosh-lite.com     							# CF API Service endpoint
    COLLECTOR: https://localhost/v1/metering/collected/usage  	# api usage sending endpoint 
    SECURED: true                            					# Secured Abacus Setting: false or true
    AUTH_SERVER: https://api.bosh-lite.com:443					# oauth Service Endpoint
    CLIENT_ID: abacus	                    					# oauth authentication id
    CLIENT_SECRET: secret                     					# oauth id password
    JWTKEY: |+                             						# Authentication service public key that validates with the Auth Server to service the app in secured mode. 
      -----BEGIN PUBLIC KEY-----
      MIGfMA0GCSqGSIb3DQEBAQUAA4GNADCBiQKBgQDHFr+KICms+tuT1OXJwhCUmR2d
      KVy7psa8xzElSyzqx7oJyfJ1JZyOzToj9T5SfTIq396agbHJWVfYphNahvZ/7uMX
      qHxf+ZH9BL1gk9Y6kCnbM5R60gfwjyW1/dQPjOzn9N394zd2FJoFHwdq9Qs0wBug
      spULZVNRxq7veq/fzwIDAQAB
      -----END PUBLIC KEY-----
    JWTALGO: RS256
```

-	ENV item
	- In addition to the items described below, the items required for the service can be added.

	<table>
	  <tr>
	    <th>ENV item</th>
	    <th>Description</th>
	  </tr>
	  <tr>
	    <td>DEBUG</td>
	    <td>Set application debug log output destination</td>
	  </tr>
	  <tr>
	    <td>NODE_TLS_REJECT_UNAUTHORIZED</td>
	    <td>-</td>
	  </tr>
	  <tr>
	    <td>API</td>
	    <td>CF API URL<br>https://api.<Application Platform Domain></td>
	  </tr>
	  <tr>
	    <td>COLLECTOR</td>
	    <td>Usage collection service URL for the Abacus Collector app<br>
	    https://<abacus-collector Domain> /v1/metering/collected/usage
	    </td>
	  </tr>
	  <tr>
	    <td>SECURED</td>
	    <td>When operating a secure abacus, always set true.</td>
	  </tr>
	  <tr>
	    <td>AUTH_SERVER</td>
	    <td>Set if SECURED is true.<br>
	    - when setting CF UAA as Auth_server, https://api.<Application Platform Domain><br>
	    - When setting AuthServer of Abacus as Auth_server, abacus-authserver-plugin
	    </td>
	  </tr>
	  <tr>
	    <td>CLIENT_ID</td>
	    <td>Set when SECURED is true.<br>Abacus.usage authorized id</td>
	  </tr>
	  <tr>
	    <td>CLIENT_SECRET</td>
	    <td>Set when SECURED is true.<br>Abacus.usage authorized password</td>
	  </tr>
	  <tr>
	    <td>JWTKEY</td>
	    <td>Set when SECURED is true.<br>
	    - If CF UAA is set to Auth_server, set the value of properties.jwt.verification_key in CF deployment manifest<br>
	    - If AuthServer of Abacus is set to Auth_server, set the Key value
	    </td>
	  </tr>
	  <tr>
	    <td>JWTALGO</td>
	    <td>Set when SECURED is true.<br>
	    - When CF UAA is set as Auth_server, RS256
	    - When AuthServer of Abacus is set as Auth_server, HS256
	    </td>
	  </tr>
	</table>
                            
#### 3.  Implement the functions of the App.js application and the metered information transmission function. The sample application implemented the function as follows.

-   Declare Dependency Module

		'use strict';

	 	// Implemented in ES5 for now
		/* eslint no-var: 0 */                   // When developing with ECMA5, release the var usage restriction from the eslint check.

		var express = require('express');        // Web Framework Module
		var request = require('request');        // request Module
		var bodyParser = require('body-parser'); // Module that convert request information to json
		var cp = require('child_process');
		var commander = require('commander');    // Module that can use commands
		var oauth = require('abacus-oauth');     // oauth module


-   Declare Variable

		// Create router
		var routes = express.Router(); // Middleware to set up app service endpoints

		// Abacus Collector App's URL
		var abacusCollectorUrl = process.env.COLLECTOR; // api usage sending url (abacus)

		// Abacus System Token Scope
		var scope = 'abacus.usage.write abacus.usage.read'; // If abacus is set to secure, set up token scope for transmission of API usage information (see the abacus installation guide for scope setting)

		// Set headers for cross-domain acceptance
		var accessControlAllowHeader = 'Origin,X-Requested-With,Content-Type,Accept';

		// The item below is the piled metering target information set.
		// When implementing an actual service, it is set through API calls or property values that provide the corresponding item.
		var resourceId = 'object-storage';
		var measure1 = 'storage';
		var measure2 = 'light_api_calls';
		var measure3 = 'heavy_api_calls';
		var serviceKey = '[cloudfoundry]';

-   Secure Abacus Security Implementation

		// abacus token reset
		var abacusToken = void 0;
		
		// Secure settings
		var secured = function secured() {
		  return process.env.SECURED === 'true' ? true : false;
		};
		
		// Set request header when sending api usage to abacus
		var authHeader = function authHeader(token) {
		  return token ? { authorization: token() } : {};
		};
		
		… Skip …
		
		var sampleApiService = function sampleApiService() {
		
		  var app = express();
		
		  // In the case of Secured abacue, an abacus token is obtained when starting the app service.
		  if (secured()) {
		    /*
		     AUTH_SERVER:   Authentication Server Endpoint https://hostname:port or 
		                    https://hostname
		     CLIENT_ID:     Authentication Server Authorized ID
		     CLIENT_SECRET: Authentication Server Authorized Password
		     SCOPE:         token scope
		     */
		
		    abacusToken = oauth.cache(process.env.AUTH_SERVER, process.env.CLIENT_ID,
		      process.env.CLIENT_SECRET, scope);
		
		    // Update abacus Token periodically.
		    abacusToken.start();
		  };
		
		 // The API service can also be executed in the secure mode.
		 // When running in the secure mode, implement to perform a validation check on all services registered in the route.
		 // In this sample, the validity check of abacus' oauth service was used.
		 // if (secured())
		 //   app.use(/^\/plan[0-9]/,
		 //     oauth.validator(process.env.JWTKEY, process.env.JWTALGO));
		
		app.use(routes);
		
		  return app;
		};
		
		… Skip …


-   COR Settings

		// The API service sets up a cross-domain because it requires additional request processing for the abacus in addition to the response processing of the requested service.
		routes.use(function(req, res, next) {
		  res.header('Access-Control-Allow-Origin', '*');
		  res.header('Access-Control-Allow-Methods', 'GET,PUT,POST,DELETE,OPTIONS');
		  res.header('Access-Control-Allow-Headers', accessControlAllowHeader);
		  next();
		});

-   Service API Implementation

		/*
		 Sample API 'Plan1'
		 1. Responding to Caller's request to process API service (Skip in Sample)
		 2. send api usage to abacus
		 */
		routes.post('/plan1', function(req, res, next) {
		
		  // Set api request time
		  var d = new Date();
		  var eventTime = Date.parse(d);
		
		  // Setting Meta Information Required for Metering Usage
		  var orgid = req.body.organization_id;
		  var spaceid = req.body.space_id;
		  var instanceid = reqs.body.instance_id ? reqs.body.instance_id : reqs.body.consumer_id;
		  var appid = req.body.consumer_id;
		  var planid = req.body.plan_id;
		  var credential = req.body.credential;
		
		  // Meta information required to run the actual service is set in inputs
		  // var inputs = req.body.inputs;
		
		  // Service usage rights check, the contents of the check are modified to suit the service to be provided.
		  if (credential.serviceKey != serviceKey)
		    return res.status(401).send();
		
		  // Check necessary items
		  if (!orgid || !spaceid || !appid)
		    return res.status(400).send();
		
		  // Write the API usage information to report to the abacus. (JSON Format)
		  var usage =
		    buildAppUsage(orgid, spaceid, appid, instanceid, planid, eventTime);
		
		  // api usage information check
		  if (usage.usage === null) return res.status(400).send();
		
		  // Set request information for sending api usage
		  var options = {
		    uri: abacusCollectorUrl,
		    headers: authHeader(abacusToken),
		    json: usage.usage
		  };
		
		  // Send api usage to abacus
		  request.post(options, function(error, response, body) {
		
		    // Checking the transmission process of the abacus
		    if (error) console.log(error);
		    else if (response.statusCode === 201 || response.statusCode === 200) {
		      // console.log('Successfully reported usage %j with headers %j',
		      //   usage, response.headers);
		      res.status(201).send(response.body);
		      return;
		    }
		    // Occurs when duplicate API usage is sent to abacus
		    else if (response.statusCode === 409) {
		      // console.log('Conflicting usage %j. Response: %j',
		      //   usage, response);
		      res.sendStatus(409);
		      return;
		    }
		    // other 400 / 500 series error checks
		    else {
		      // console.log('failed report usage %j with headers %j',
		      //   usage, response.headers);
		      res.sendStatus(response.statusCode);
		      return;
		    }
		  });
		  return res;
		});


-   Create Abacus Sending Json 

		// Create a data format to send to the abacus.
		var buildAppUsage =
		  function buildAppUsage(orgid, spaceid, appid, insid, planid, eventTime) {
		
		    var appUsage = { usage: null };
		
		    // sample api plan id가 'standard'
		    if (planid == 'standard')
		      appUsage = {
		        usage: {
		          start: eventTime,
		          end: eventTime,
		          organization_id: orgid,
		          space_id: spaceid,
		          consumer_id: 'app:' + appid,
		          resource_id: resourceId,
		          plan_id: planid,
		          resource_instance_id: insid,
		          measured_usage: [
		            {
		              measure: measure1,
		              quantity: 1073741824
		            },
		            {
		              measure: measure2,
		              quantity: 1000
		            },
		            {
		              measure: measure3,
		              quantity: 0
		            }
		          ]
		        }
		      };
		
		    return appUsage;
		  };


-   Other

		// Setting Information for the App Service
		// Set the name of the PORT, HOST, etc.
		var conf = function conf() {
		  process.env.PORT = commander.port || process.env.PORT || 9602;
		};
		
		// Create Command-line Interface
		// can execute the start and stop scripts described in the package.
		// Start App: npm start
		// Stop App: npm stop
		
		var runCLI = function runCLI() {
		
		  commander
		    .option('-p, --port <port>', 'port number [9602]')
		    .option('start', 'start the server')
		    .option('stop', 'stop the server')
		    .parse(process.argv);
		
		  // Start API server
		  if (commander.start) {
		    conf();
		
		    // Create app and listen on the configured port
		    var app = sampleApiService();
		
		    app.listen({
		      port: parseInt(process.env.PORT)
		    });
		  }
		  else if (commander.stop)
		
		    // Stop API App server
		    cp.exec(
		      'pkill -f "node ./sampleApiService"', function(err, stdout, stderr) {
		        if (err) console.log('Stop error %o', err);
		      });
		};
		
		// Export our public functions
		module.exports = sampleApiService;
		module.exports.runCLI = runCLI



### <div id='13'></div> 2.4.2 Sample API Service Application Metering Interworking Item

#### 1.  Metering Information Sending API Endpoints

	POST /v1/metering/collected/usage

#### 2.  API Service Metering Sending Items
<table>
  <tr>
    <th>Classification</th>
    <th>Type</th>
    <th>Description</th>
    <th>Example</th>
  </tr>
  <tr>
    <td>start</td>
    <td>UNIX Timestamp</td>
    <td>API process start time</td>
    <td>1396421450000</td>
  </tr>
  <tr>
    <td>end</td>
    <td>UNIX Timestamp</td>
    <td>API process response time</td>
    <td>1396421451000</td>
  </tr>
  <tr>
    <td>organization_id</td>
    <td>String</td>
    <td> App Organization ID that called API</td>
    <td>us-south:54257f98-83f0-4eca-ae04-9ea35277a538</td>
  </tr>
  <tr>
    <td>space_id</td>
    <td>String</td>
    <td>Area ID of the app that called the API</td>
    <td>d98b5916-3c77-44b9-ac12-04456df23eae</td>
  </tr>
  <tr>
    <td>consumer_id</td>
    <td>String</td>
    <td>App ID that called API</td>
    <td>App: d98b5916-3c77-44b9-ac12-04d61c7a4eae</td>
  </tr>
  <tr>
    <td>resource_id</td>
    <td>String</td>
    <td>API resource ID</td>
    <td>sample_api</td>
  </tr>
  <tr>
    <td>plan_id</td>
    <td>String</td>
    <td>API Metering Plan ID</td>
    <td>basic</td>
  </tr>
  <tr>
    <td>resource_instance_id</td>
    <td>String</td>
    <td>App ID that called API</td>
    <td>d98b5916-3c77-44b9-ac12-04d61c7a4eae</td>
  </tr>
  <tr>
    <td>measured_usage</td>
    <td>Array</td>
    <td>Metering Item</td>
    <td>-</td>
  </tr>
  <tr>
    <td>measure</td>
    <td>String</td>
    <td>Metering Target Name</td>
    <td>api_calls</td>
  </tr>
  <tr>
    <td>quantity</td>
    <td>Number</td>
    <td>Number of API processed for the API request</td>
    <td>10</td>
  </tr>
</table>
                                                                   

#### 3.  API Service Metering Send Item Example

	{
	  "start": 1396421450000,
	  "end": 1396421451000,
	  "organization_id": "us-south:54257f98-83f0-4eca-ae04-9ea35277a538",
	  "space_id": "d98b5916-3c77-44b9-ac12-04456df23eae",
	  "consumer_id": "app:d98b5916-3c77-44b9-ac12-045678edabae",
	  "resource_id": "sample_api",
	  "plan_id": "basic",
	  "resource_instance_id": "d98b5916-3c77-44b9-ac12-04d61c7a4eae",
	  "measured_usage": [
	    {
	      "measure": "api_calls",
	      "quantity": 10
	    }
	  ]
	}


Refer: <u><b><a href="https://github.com/cloudfoundry-incubator/cf-abacus/blob/master/doc/api.md">https://github.com/cloudfoundry-incubator/cf-abacus/blob/master/doc/api.md</a></b></u>


### <div id='14'></div> 2.5API Service Interworking Sample Application Development

 As an application using the Api service, this sample implemented only the function of simply requesting the API service through the web screen.


#### 1.  API service interwork sample application shape

	sample_api_node_caller/
	├── .apprc
	├── cfpush.sh
	├── .eslintignore
	├── .gitignore
	├── manifest.yml
	├── .npmrc
	├── package.json
	├── sampleApiCaller
	├── src
	│   ├── app.js
	│   ├── bower_components
	│   └── test
	│	   └── test.js
	└── views
		├── apiCaller.handlebars
		└── layouts
		   └── main.handlebars


<table>
  <tr>
    <th>File/Folder</th>
    <th>Purpose</th>
  </tr>
  <tr>
    <td>.apprc</td>
    <td>App execution configuration file</td>
  </tr>
  <tr>
    <td>cfpush.sh</td>
    <td>Modify the org_id of Manifest to the guid of the currently targeted org and push the packaged app to the cf.</td>
  </tr>
  <tr>
    <td>.eslintignore</td>
    <td>When executing Eslint, set files and directories to exclude check.</td>
  </tr>
  <tr>
    <td>.gitignore</td>
    <td>When configuration management through Git, set files or directories that do not need configuration management.</td>
  </tr>
  <tr>
    <td>manifest.yml</td>
    <td>This is the setting for the application when deploying to the Application platform. You can define the name of the application, the deployment path, the number of instances, etc.</td>
  </tr>
  <tr>
    <td>.npmrc</td>
    <td>Npm Execution Environment Configuration File</td>
  </tr>
  <tr>
    <td>package.json</td>
    <td>Used to describe dependency information of npm required for node.js application.<br>
   When no information is entered after installing the npm install command, it uses the information that is in the file to install npm.
 </td> 
  </tr>
  <tr>
    <td>sampleApiCaller</td>
    <td>Service call app execution script</td>
  </tr>
  <tr>
    <td>app.js</td>
    <td>Service call app<br>It defines routing information for web service and bind api service calls.</td>
  </tr>
  <tr>
    <td>bower_components</td>
    <td>Frontend library directory</td>
  </tr>
  <tr>
    <td>test.js</td>
    <td>Service app unit test module<br>Define unit tests of service app through mocha.</td>
  </tr>
  <tr>
    <td>views</td>
    <td>Sample API service call demo screen</td>
  </tr>
</table>

### <div id='15'></div> 2.5.1 API Service Interworking Sample Application Code

#### 1.  Package.json  
Describes the code configuration of the sample application.

	{
	  "name": "sample-api-node-caller",
	  "version": "0.0.1",
	  "description": "CF API Metering Caller Sample",
	  "main": "lib/app.js",
	  "bin": {
	    "sampleApiCaller": "./sampleApiCaller"
	  },
	  "files": [
	    ".apprc",
	    ".eslintignore",
	    ".npmrc",
	    "cfpush.sh",
	    "manifest.yml",
	    "src/",
	    "views/"
	  ],
	  "scripts": {
	    "start": "./sampleApiCaller start",
	    "stop": "./sampleApiCaller stop",
	    "babel": "babel",
	    "test": "eslint && mocha",
	    "lint": "eslint",
	    "pub": "publish",
	    "cfpack": "cfpack",
	    "cfpush": "./cfpush.sh"
	  },
	  "author": "KPaaS",
	  "license": "Apache-2.0",
	  "dependencies": {
	    "body-parser": "^1.15.2",
	    "cors": "^2.8.1",
	    "express": "^4.14.0",
	    "express-handlebars": "^3.0.0",       ## handler module
	    "babel-preset-es2015": "^6.6.0",
	    "request": "^2.74.0",
	    "commander": "^2.8.1",
	    "underscore": "^1.8.3"
	  },
	  "devDependencies": {
	    "abacus-babel": "file:../../tools/babel",
	    "abacus-cfpack": "file:../../tools/cfpack",
	    "abacus-cfpush": "file:../../tools/cfpush",
	    "abacus-coverage": "file:../../tools/coverage",
	    "abacus-eslint": "file:../../tools/eslint",
	    "abacus-mocha": "file:../../tools/mocha",
	    "abacus-publish": "file:../../tools/publish"
	  },
	  "engines": {
	    "node": ">=5.11.1",
	    "npm": ">=3.8.6"
	  }
	}



#### 2.  Manifest.yml  
Describes the setting information required when deploying the app to the CF and the setting information required for the app execution environment.
```yml
---
applications:
- name: sample-api-node-caller  # application name
  memory: 512M                  # application memory size
  disk_quota: 512M
  instances: 1                  # number of application instances
  path: ./.cfpack/app.zip       # location of the application to be deployed
  command: npm start            # application start command in CF
  env:
    DEBUG: a*
    ORG_ID: d6ce3670-ab9c-4453-b993-f2821f54846b
    SECURED: false
    #AUTH_SERVER: https://api.bosh-lite.com:443 
    #CLIENT_ID: abacus
    #CLIENT_SECRET: secret
```

-   ENV ITEMS
	- In addition to the items described below, items necessary for the service may be added.
    
	<table>
	  <tr>
        <th>ENV ITEMS</th>
        <th>Description</th>
      </tr>
      <tr>
        <td>DEBUG</td>
        <td>Setting the application debug log output destination</td>
      <tr>
      <tr>
        <td>ORG_ID</td>
        <td>Organization ID to deploy Caller Application</td>
      <tr>
      <tr>
        <td>SECURED</td>
        <td>When operating the API service as Secured, it must be set to true.</td>
      <tr>
      <tr>
        <td>AUTH_SERVER</td>
        <td>Set when SECURED is true.
        - when setting CF UAA as Auth_server, https://api.<Application Platform Domain>
        - When setting AuthServer of Abacus as Auth_server, abacus-authserver-plugin
        </td>
      <tr>
      <tr>
        <td>CLIENT_ID</td>
        <td>Set when SECURED is true.<br>Abacus.usage authentication id</td>
      <tr>
      <tr>
        <td>CLIENT_SECRET</td>
        <td>Set when SECURED is true.Abacus.usage authentication password<br></td>
      <tr>
	</table>
	                   
	                   
#### 3.  App.js  
Implements an application that requests API services.

-   Declare dependent modules

		'use strict';

		// Implemented in ES5 for now
		/* eslint no-var: 0 */
		
		var express = require('express');
		var request = require('request');
		var bodyParser = require('body-parser');
		var cp = require('child_process');
		var commander = require('commander');
		var handlebars = require('express-handlebars').create({ defaultLayout:'main' }); // Web service view processing module

-   Declare Variable

		// Cross domain request headers
		var accessControlAllowHeader = 'Origin,X-Requested-With,Content-Type,Accept';
		
		var vcapApp = undefined;
		var vcapService = undefined;
		var dummyOrgId = undefined;
		var vcapBindServices = undefined;
		
		// vcap_application information settings
		vcapApp = JSON.parse(process.env.VCAP_APPLICATION);
		
		// vcap_service information settings
		vcapService = JSON.parse(process.env.VCAP_SERVICES);
		
		// Organization guid settings
		dummyOrgId = process.env.ORG_ID;

-   Implement Secure Abacus security

		// When requesting an api service by referring to the api service, implement the process of setting the token in the header.

-   COR settings

		// The api service requires additional request processing for the abacus in addition to response processing of the requested service. so set up a cross-domain.
		routes.use(function(req, res, next) {
		  res.header('Access-Control-Allow-Origin', '*');
		  res.header('Access-Control-Allow-Methods', 'GET,PUT,POST,DELETE,OPTIONS');
		  res.header('Access-Control-Allow-Headers', accessControlAllowHeader);
		  next();
		});


-   Implement the service API

		// Get the service information bound to the app from VCAP_SERVICE.
		// Multiple services can be bound to an app.
		vcapBindServices = vcapService[Object.keys(vcapService)[0]];
		
		var sampleApiCaller = function sampleApiCaller() {
		
		  var app = express();
		
		  // Web service view process
		  app.engine('handlebars', handlebars.engine);
		  app.set('view engine', 'handlebars');
		
		  //Cross-domain process
		  app.use(function(req, res, next) {
		    res.header('Access-Control-Allow-Origin', '*');
		    res.header('Access-Control-Allow-Methods', 'GET,PUT,POST,DELETE,OPTIONS');
		    res.header('Access-Control-Allow-Headers', accessControlAllowHeader);
		    next();
		  });
		
		  // Mount the js module being referenced by the view to the middleware.
		  app.use('/bower_components',
		    express.static(__dirname + '/bower_components'));
		
		  // Mount the web service main on the middleware.
		  app.get('/', function(req, res) {
		    res.type('text/html');
		    res.render('apiCaller');         // create view
		  });
		
		  // to support JSON-encoded bodies
		  app.use(bodyParser.json());
		
		  // to support URL-encoded bodies
		  app.use(bodyParser.urlencoded({
		    extended: true
		  }));
		
		  /*
		   Sample Web Service
		   1. Request API service
		   */
		  app.post('/sampleApiSerivceCall', function(req, res, next) {
		
		    // When there is no service bound to the app
		    if (typeof vcapBindServices !== 'object') {
		      return res.status(502).send('unbind service called');
		      next();
		    };
		
		    // Find a service to interwork with API Service.
		    // Obtain service information to be interlocked among a plurality of bound services.
		    var bindApiService = vcapBindServices[0];
		
		    // Obtain the service url from the service information obtained above.
		    var serviceUrl = bindApiService.credentials.uri ?
		      bindApiService.credentials.uri : bindApiService.credentials.url;
		
		    // Write JSON to request to API Service.
		    var callEvent = buildSendData(req.body, bindApiService);
		
		    // Set request options
		    var options = {
		      uri: serviceUrl,
		      json: callEvent
		    };
		
		    // It requests the api service and responds to the result.
		    request.post(options, function(error, response, body) {
		
		      if (error) console.log(error);
		      else if (response.statusCode === 201 || response.statusCode === 200) {
		        // console.log('Successfully reported usage %j with headers %j',
		        //   usage, response.headers);
		        res.status(201).send(response.body);
		        return;
		      }
		      else {
		        // console.log('failed report usage %j with headers %j',
		        //   usage, response.headers);
		        res.sendStatus(response.statusCode);
		        return;
		      }
		    });
		    return res;
		  });
		
		  // Not found
		  app.use(function(req, res) {
		    res.type('text/plain');
		    res.status(404);
		    res.send('404 not found');
		  });
		
		  return app;
		};


-   Create Abacus Sending Json

		// Write the data to be sent to the service in JSON format.
		var buildSendData = function buildSendData(args, bindApiService) {
		
		  return {
		    organization_id: dummyOrgId,
		    space_id: vcapApp.space_id,
		    consumer_id: vcapApp.application_id,
		    instance_id: vcapApp.instance_id ?
		      vcapApp.instance_id : vcapApp.application_id,
		    plan_id: bindApiService.plan,
		    credential: bindApiService.credentials,
		    inputs: args
		  };
		};


-   Etc.

		// Setting information for app services
		// Set PORT, HOST name, etc.
		var conf = function conf() {
		  process.env.PORT = commander.port || process.env.PORT || 9601;
		};
		
		// Command line interface
		var runCLI = function runCLI() {
		
		  commander
		    .option('-p, --port <port>', 'port number [9601]')
		    .option('start', 'start the server')
		    .option('stop', 'stop the server')
		    .parse(process.argv);
		
		  // Start Caller server
		  if (commander.start) {
		    conf();
		
		    // Create app and listen on the configured port
		    var app = sampleApiCaller();
		
		    app.listen({
		      port: parseInt(process.env.PORT)
		    });
		  }
		  else if (commander.stop)
		
		  // Stop Caller App server
		    cp.exec(
		      'pkill -f "node ./sampleApiCaller"', function(err, stdout, stderr) {
		        if (err) console.log('Stop error %o', err);
		      });
		};
		
		// Export our public functions
		module.exports = sampleApiCaller;
		module.exports.runCLI = runCLI;



#### 4.  Views/apiCaller.handlebars  
Web screen requesting Api service

	<!DOCTYPE html>
	<html>
	<head>
	<meta charset="utf-8">
	<script src="bower_components/jquery/dist/jquery.min.js"></script>
	<script type="text/javascript">
	
	    $(document).ready(function() {
	
	        $('#result_div').html('');
	
	        var appUrl = document.URL;
	        // Set Request Service Entry 
	        var input_param1 = 'Seoul';
	        var input_param2 = 'Mugyodong';
	
	        $('#send_btn').click(function(){
	
	            $('#result_div').html('');
	
	            var data = sendData( input_param1, input_param2 );
	
	            $.ajax({
	                url: appUrl + 'sampleApiSerivceCall',
	                type:"POST",
	                data:data,
	                success:function(data)
	                {
	                    $('#result_div').html("posted.");
	                },
	                error:function(jqXHR,textStatus,errorThrown)
	                {
	                    $('#result_div').html(errorThrown);
	                }
	            });
	
	        });
	    });
	
	    // Set request service input item, write in JSON format.
	    var sendData = function buildJSON( input_param1, input_param2 ) {
	
	        return {
	            input_param1: input_param1,
	            input_param2: input_param2
	        };
	    };
	
	</script>
	</head>
	<body>
	<form id="form">
	    
	</form>
	
	<h3>CF REMOTE SERVICE API CALL TEST</h3>
	
	<button id="send_btn">SEND SERVICE API CALL</button>
	<br>
	<div id="result_div"></div>
	
	<!--<h4>{{vcapService}}</h4>-->
	
	</body>
	</html>


### <div id='16'></div> 2.5.2 API Service Interworking Sample Application Interface Item

#### 1.  API Service Endpoint

	GET|POST|PUT|DELETE <api_service_restful_api>

#### 2.  API Service Metering Send Items

<table>
  <tr>
    <th>Classification</th>
    <th>Type</th>
    <th>Description</th>
    <th>Example</th>
  </tr>
  <tr>
    <td>org_id</td>
    <td>String</td>
    <td>Organization ID of the app that requested the API service</td>
    <td>54257f98-83f0-4eca-ae04-9ea35277a538</td>
  </tr>
  <tr>
    <td>space_id</td>
    <td>String</td>
    <td>Area ID of the app that requested API service</td>
    <td>d98b5916-3c77-44b9-ac12-04456df23eae</td>
  </tr>
  <tr>
    <td>consumer_id</td>
    <td>String</td>
    <td>App ID that requested API service</td>
    <td>d98b5916-3c77-44b9-ac12-045678edabae</td>
  </tr>
  <tr>
    <td>instance_id</td>
    <td>String</td>
    <td>The resource instance ID of the app that requested the API service.</td>
    <td>d98b5916-3c77-44b9-ac12-045678edabad</td>
  </tr>
  <tr>
    <td>plan_id</td>
    <td>String</td>
    <td>The plan ID of the requested API service in the app.</td>
    <td>basic</td>
  </tr>
  <tr>
    <td>credentials</td>
    <td>JSON</td>
    <td>Set the credential required for the service requests.</td>
    <td>credentials: {<br>
           &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;key: value,<br>
           &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;…<br>
       }
    </td>
  </tr>
  <tr>
    <td>inputs</td>
    <td>JSON</td>
    <td>Set input information required for service request.</td>
    <td>
      inputs: {<br>
      &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;key: value,s<br>
      &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;...<br>
      }
    </td>
  </tr>
</table>


#### 3.  API Service Metering Send Items Example

	{
	  organization_id: 'd6ce3670-ab9c-4453-b993-f2821f54846b',
	  space_id: 'ab63eaed-7932-4f24-804d-dccb40a68752',
	  consumer_id: 'ff7476f9-f5b6-420c-96f0-ac39be43de8c',
	  instance_id: 'ff7476f9-f5b6-420c-96f0-ac39be43de8c',
	  plan_id: 'standard',
	  credential: {
		'serviceKey': '[cloudfoundry]',
		'url': 'http://localhost:9602/plan1'
	  },
	  inputs: {
		key1: 'val1',
		key2: 'val2'
	  }
	}


### <div id='17'></div> 2.5.3 VCAP_SERVICES Environment Setting Information

To obtain access information for each service to which an application deployed on the Application platform is bound, the information may be obtained by reading the VCAP_SERVICES environment setting information registered for each application.

#### 1.  Application environment information of the Application platform
-   When the service is bound, the configuration information in JSON format is registered for each application.

		{
		 "VCAP_SERVICES": {
		  "sample_api_node_service": [
		   {
		    "credentials": {
		     "documentUrl": "http://sample-api-node-service.bosh-lite.com/doc",
		     "serviceKey": "UxJWlzVP0VJquACPD+FohQ==",
		     "url": "http://sample-api-node-service.bosh-lite.com/"
		    },
		    "label": "sample_api_node_service",
		    "name": "sampleNodeApi",
		    "plan": "basic",
		    "provider": null,
		    "syslog_drain_url": "PASTA_SERVICE_METERING",
		    "tags": [
		     "Sample API Service"
		    ],
		    "volume_mounts": []
		   }
		  ]
		 }
		}
		
		{
		 "VCAP_APPLICATION": {
		  "application_id": "58872d8a-edfc-44df-97f0-df67cf9033a7",
		  "application_name": "sample-api-node-caller",
		  "application_uris": [
		   "sample-api-node-caller.bosh-lite.com"
		  ],
		  "application_version": "55678102-584c-4fca-8304-82f727506b1d",
		  "limits": {
		   "disk": 512,
		   "fds": 16384,
		   "mem": 512
		  },
		  "name": "sample-api-node-caller",
		  "space_id": "2ce08996-f463-406c-a971-adbbaf4e4ca5",
		  "space_name": "ops",
		  "uris": [
		   "sample-api-node-caller.bosh-lite.com"
		  ],
		  "users": null,
		  "version": "55678102-584c-4fca-8304-82f727506b1d"
		 }
		}



#### 2.  How to access application environment information of the Application platform in Node.js

-   It can be accessed by reading the VCAP_SERVICES value of the system environment variable.

 		process.env.VCAP_SERVICES

### <div id='18'></div> 2.6 Metering/Rating/Billing Policy

Since the Metering/Rating/Billing Policy is different for each service and each service provider, this guide does not cover an example of policy development. However, the format applicable to CF-ABACUS will be described.

### <div id='19'></div> 2.6.1 Metering Policy

Metering Policy is a JSON-formatted object that defines metering target designation and aggregation method from collected metering information. The service provider develops the policy for the service according to the metering policy schema.

#### 1.  Metering Policy Schema

<table>
  <tr>
    <th>Classification</th>
    <th>Type</th>
    <th>Necessity</th>
    <th>Description</th>
  </tr>
  <tr>
    <td>plan_id</td>
    <td>String</td>
    <td>O</td>
    <td>API Metering Plan ID</td>
  </tr>
  <tr>
    <td>measures</td>
    <td>Array</td>
    <td>At least one</td>
    <td>Defines API metering information collect targets</td>
  </tr>
  <tr>
    <td>name</td>
    <td>String</td>
    <td>O</td>
    <td>Metering information collect target name</td>
  </tr>
  <tr>
    <td>unit</td>
    <td>String</td>
    <td>O</td>
    <td>Metering information collect target unit</td>
  </tr>
  <tr>
    <td>metrics</td>
    <td>Array</td>
    <td>At least one</td>
    <td>Defines API Metering Aggregation Scheme</td>
  </tr>
  <tr>
    <td>name</td>
    <td>String</td>
    <td>O</td>
    <td>Metering information collect target name</td>
  </tr>
  <tr>
    <td>unit</td>
    <td>String</td>
    <td>O</td>
    <td>Metering information collect target unit</td>
  </tr>
  <tr>
    <td>meter</td>
    <td>String</td>
    <td>X</td>
    <td>Calculation formula or conversion formula applied to the collection stage for metering information</td>
  </tr>
  <tr>
    <td>accumulate</td>
    <td>String</td>
    <td>X</td>
    <td>Calculation formula or conversion formula applied to the cumulative step for metering information</td>
  </tr>
  <tr>
    <td>aggregate</td>
    <td>String</td>
    <td>X</td>
    <td>Calculation formula or conversion formula applied to the aggregation step for metering information</td>
  </tr>
  <tr>
    <td>summarize</td>
    <td>String</td>
    <td>X</td>
    <td>Calculation formula or conversion formula applied when reporting metering information</td>
  </tr>
  <tr>
    <td>title</td>
    <td>String</td>
    <td>X</td>
    <td>API Metering Title</td>
  </tr>
</table>
                      

#### 2.  Metering Policy Example

	{
	  "plan_id": "basic-object-storage",
	  "measures": [
	    {
	      "name": "storage",
	      "unit": "BYTE"
	    },
	    {
	      "name": "api_calls",
	      "units": "CALL"
	    }
	  ],
	  "metrics": [
	    {
	      "name": "storage",
	      "unit": "GIGABYTE",
	      "meter": "(m) => m.storage / 1073741824",
	      "accumulate": "(a, qty) => Math.max(a, qty)"
	    },
	    {
	      "name": "thousand_api_calls",
	      "unit": "THOUSAND_CALLS",
	      "meter": "(m) => m.light_api_calls / 1000",
	      "accumulate": "(a, qty) => a ? a + qty : qty",
	      "aggregate": "(a, qty) => a ? a + qty : qty",
	      "summarize": "(t, qty) => qty"
	    }
	  ]
	}


### <div id='20'></div> 2.6.2 Rating Policy

Rating Policy is a JSON-formatted object that defines the usage weight of each service.
A service provider develops a policy for a service according to the Rating Policy Schema.

#### 1.  Rating Policy Schema

<table>
  <tr>
    <th>Classification</th>
    <th>Type</th>
    <th>Necessity</th>
    <th>Description</th>
  </tr>
  <tr>
    <td>plan_id</td>
    <td>String</td>
    <td>O</td>
    <td>API Ranting Plan ID</td>
  </tr>
  <tr>
    <td>metrics</td>
    <td>Array</td>
    <td>At least one</td>
    <td>Rating Policy List</td>
  </tr>
  <tr>
    <td>name</td>
    <td>String</td>
    <td>O</td>
    <td>Rating Definition Target Name</td>
  </tr>
  <tr>
    <td>rate</td>
    <td>String</td>
    <td>X</td>
    <td>Weight calculation formula or conversion formula</td>
  </tr>
  <tr>
    <td>charge</td>
    <td>String</td>
    <td>X</td>
    <td>Charging for usage calculation formula or conversion formula</td>
  </tr>
  <tr>
    <td>title</td>
    <td>String</td>
    <td>X</td>
    <td>Rating Policy Name</td>
  </tr>
</table>
                   
#### 2.  Rating Policy Example

	{
	  "plan_id": "object-rating-plan",
	  "metrics": [
	    {
	      "name": "storage"
	    },
	    {
	      "name": "thousand_api_calls",
	      "rate": "(p, qty) => p ? p * qty : 0",
	      "charge": "(t, cost) => cost"
	    }
	  ]
	}



### <div id='21'></div> 2.6.3 Billing Policy

A billing policy is a JSON-formatted object that defines the unit price for each service. The service provider develops the policy for the service according to the Charging Policy schema.

#### 1.  Billing Policy Schema

<table>
  <tr>
    <th>Classification</th>
    <th>Type</th>
    <th>Necessity</th>
    <th>Description</th>
  </tr>
  <tr>
    <td>plan_id</td>
    <td>String</td>
    <td>O</td>
    <td>API Billing Plan ID</td>
  </tr>
  <tr>
    <td>metrics</td>
    <td>Array</td>
    <td>At least one</td>
    <td>Billing Policy List</td>
  </tr>
  <tr>
    <td>name</td>
    <td>String</td>
    <td>O</td>
    <td>Charging target name</td>
  </tr>
  <tr>
    <td>price</td>
    <td>Array</td>
    <td>at least one</td>
    <td>Billing Policy Details</td>
  </tr>
  <tr>
    <td>country</td>
    <td>String</td>
    <td>O</td>
    <td>Currency to be applied to the unit price for using the service</td>
  </tr>
  <tr>
    <td>price</td>
    <td>Number</td>
    <td>O</td>
    <td>Service Usage Unit Price</td>
  </tr>
  <tr>
    <td>title</td>
    <td>String</td>
    <td>X</td>
    <td>Billing Policy Title</td>
  </tr>
</table>
             
#### 2.  Billing Policy Example

	{
	  "plan_id": "object-pricing-basic",
	  "metrics": [
	    {
	      "name": "storage",
	      "prices": [
	        {
	          "country": "USA",
	          "price": 1
	        },
	        {
	          "country": "EUR",
	          "price": 0.7523
	        },
	        {
	          "country": "CAN",
	          "price": 1.06
	        }
	      ]
	    },
	    {
	      "name": "thousand_api_calls",
	      "prices": [
	        {
	          "country": "USA",
	          "price": 0.03
	        },
	        {
	          "country": "EUR",
	          "price": 0.0226
	        },
	        {
	          "country": "CAN",
	          "price": 0.0317
	        }
	      ]
	    }
	  ]
	}


### <div id='22'></div> 2.6.4 Policy Registration

A policy can be registered in CF-ABACUS in one of two ways.

#### 1.  By registering the js file  
After saving the created policy in the following directory, deploy or redeploy CF-ABACUS to the CF.

-   For Metering Policy

		cf-abacus/lib/plugins/provisioning/src/plans/metering

-   For Rating Policy

		cf-abacus/lib/plugins/provisioning/src/plans/pricing

-   For Billing Policy

	 	cf-abacus/lib/plugins/provisioning/src/plans/rating


#### 2.  By registering it to the DB  
There is no need to redeploy CF-ABACUS by saving the created policy in the DB using curl, etc. When registering a policy, the policy ID must be unique.

-   For Metering Policy 

		POST /v1/metering/plans/:metering_plan_id
	>

		$ curl -k -X POST 'http://abacus-provisioning-plugin.bosh-lite.com/v1/metering/plans/sample-linux-container' \
			 -H "Content-Type: application/json" \
			 -d '{"plan_id":"sample-linux-container","measures":[{"name":"current_instance_memory","unit":"GIGABYTE"},{"name":"current_running_instances","unit":"NUMBER"},{"name":"previous_instance_memory","unit":"GIGABYTE"},{"name":"previous_running_instances","unit":"NUMBER"}],"metrics":[{"name":"memory","unit":"GIGABYTE","type":"time-based","meter":"((m)=>({previous_consuming:newBigNumber(m.previous_instance_memory||0).div(1073741824).mul(m.previous_running_instances||0).mul(-1).toNumber(),consuming:newBigNumber(m.current_instance_memory||0).div(1073741824).mul(m.current_running_instances||0).toNumber()})).toString()","accumulate":"((a,qty,start,end,from,to,twCell)=>{if(end<from||end>=to)returnnull;constpast=from-start;constfuture=to-start;consttd=past+future;return{consuming:a&&a.since>start?a.consuming:qty.consuming,consumed:newBigNumber(qty.consuming).mul(td).add(newBigNumber(qty.previous_consuming).mul(td)).add(a?a.consumed:0).toNumber(),since:a&&a.since>start?a.since:start};}).toString()","aggregate":"((a,prev,curr,aggTwCell,accTwCell)=>{if(!curr)returna;constconsuming=newBigNumber(curr.consuming).sub(prev?prev.consuming:0);constconsumed=newBigNumber(curr.consumed).sub(prev?prev.consumed:0);return{consuming:consuming.add(a?a.consuming:0).toNumber(),consumed:consumed.add(a?a.consumed:0).toNumber()};}).toString()","summarize":"((t,qty,from,to)=>{if(!qty)return0;constrt=Math.min(t,to?to:t);constpast=from-rt;constfuture=to-rt;consttd=past+future;constconsumed=newBigNumber(qty.consuming).mul(-1).mul(td).toNumber();returnnewBigNumber(qty.consumed).add(consumed).div(2).div(3600000).toNumber();}).toString()"}]}' \
			-H "Authorization: $(cf oauth-token | grep bearer)"



-   For Rating Policy  

		POST /v1/rating/plans/:rating_plan_id
	>

		# Example
		$ curl -k -X POST 'http://abacus-provisioning-plugin.bosh-lite.com/v1/rating/plans/linux-rating-sample' \
			 -H "Content-Type: application/json" \
		     -d '{"plan_id":"linux-rating-sample","metrics":[{"name":"memory","rate":"((price,qty)=>({price:price,consuming:qty.consuming,consumed:qty.consumed})).toString(),charge:((t,qty,from,to)=>{if(!qty)return0;constrt=Math.min(t,to?to:t);constpast=from-rt;constfuture=to-rt;consttd=past+future;constconsumed=newBigNumber(qty.consuming).mul(-1).mul(td).toNumber();constgbhour=newBigNumber(qty.consumed).add(consumed).div(2).div(3600000).toNumber();returnnewBigNumber(gbhour).mul(qty.price).toNumber();}).toString()"}]}' \
			 -H "Authorization: $(cf oauth-token | grep bearer)"


-   For Billing Policy

		POST /v1/pricing/plans/:pricing_plan_id

	>

		# Example
		$ curl -k -X POST 'http://abacus-provisioning-plugin.bosh-lite.com/v1/pricing/plans/linux-pricing-sample' \
			 -H "Content-Type: application/json" \
			 -d '{"plan_id":"linux-pricing-sample","metrics":[{"name":"memory","prices":[{"country":"USA","price":0.00014}]}]}' \
			-H "Authorization: $(cf oauth-token | grep bearer)"



### <div id='23'></div> 2.7 Deployment

When an application is deployed on the Application platform, the deployed application can be used by connecting to the service provided by the Application platform. You can access the service by accessing the application environment variables of the Application platform only when it is executed on the Application platform.

### <div id='24'></div> 2.7.1 Application Platform Login

Log in to the Application platform to perform the process below

	$ cf api --skip-ssl-validation https://api.<Application Platform Domain> # Set Application Platform TARGET 
	
	$ cf login -u <user name> -o <org name> -s <space name> # Login request


### <div id='25'></div> 2.7.2  Create API Service Broker

Create a service to use at the application through the Application Platform. It can be created without a separate installation process, and can obtain access information through a binding process with the application.

-   Service Creation (Can check the service plan and service list using the cf marketplace command.)
-   Gradle Version: 2.2

		## Service Broker CF Deployment
		$ cd <Sample service broker path>/sample_api_java_broker
		$ gradle build -x test
		:compileJava
		Note: ~/KPAAS-API-METERING-SAMPLE/lib/sample_api_java_broker/src/main/java/org/openpaas/servicebroker/api/config/CatalogConfig.java uses unchecked or unsafe operations.
		Note: Recompile with -Xlint:unchecked for details.
		:processResources
		:classes
		:findMainClass
		:jar
		:bootRepackage
		:assemble
		:check
		:build
		
		BUILD SUCCESSFUL
		
		Total time: 28.905 secs
		
		This build could be faster, please consider using the Gradle Daemon: https://docs.gradle.org/2.14.1/userguide/gradle_daemon.html
		
		$ cf push
		
		## Create a service broker
		$ cf create-service-broker <Service Broker Name> <Authentication ID> <Authentication Password> <Service Broker Address>
		
		Example)
		$ cf create-service-broker sample-api-broker admin cloudfoundry http://sample-api-java-broker.bosh-lite.com
		
		## Check Service Broker
		$ cf service-brokers
		Getting service brokers as admin...
		
		name                url   
		sample-api-broker   http://sample-api-java-broker.bosh-lite.com
		
		## Check Service Catalog
		$ cf service-access
		Getting service access as admin...
		broker: sample-api-broker
		   service                                   plan       access   orgs   
		   standard_obejct_storage_light_api_calls   standard   none        
		   standard_obejct_storage_heavy_api_calls   basic      none
		
		## Allow access to the registered services
		$ cf enable-service-access <Service Name> -p <Plan Name>
		
		Example)
		$ cf enable-service-access standard_obejct_storage_light_api_calls -p standard


### <div id='26'></div> 2.7.3 API Service Application Deployment and Service Registration

You can deploy the API service application to the Application platform. The API service registered can be bound with other applications to provide API services.

#### 1.  Application Deployment

-   Deploy with the cf push command. If you do not enter a separate value, the settings in manifest.yml are used.

		## API Service Deployment
		$ cd <Sample api Service path>/sample_api_node_service
		$ npm install && npm run babel && npm run cfpack && cf push
		
		## Create Service
		$ cf create-service <Service Name> <Plan Name> <Service Instance Name>
		Example)
		$ cf create-service standard_obejct_storage_light_api_calls standard sampleNodejslightCallApi
		
		## Service Check
		$ cf services
		Getting services in org real / space ops as admin...
		OK
		
		name                       service                                   plan       bound apps   last operation   
		sampleNodejslightCallApi   standard_obejct_storage_light_api_calls   standard                create succeeded



### <div id='27'></div> 2.7.4 API Service Interworking Sample Application Deployment and Service Connection

The process of connecting an application and service is called 'bind', and through this process, access information to access the service is created.

-   Connecting applications and services

		## API Service Interworking Sample Application Deployment
		$ cd <Sample Application Path>/sample_api_node_caller
		$ npm install && npm run babel && npm run cfpack && ./cfpush.sh
		
		## Service bind
		$ cf bind-service <APP_NAME> <SERVICE_INSTANCE> -c <PARAMETERS_AS_JSON>
		
		Example) 
		$ cf bind-service sample-api-node-caller sampleNodejslightCallApi -c '{"serviceKey": "[cloudfoundry]"}'
		
		## Check service connection
		$ cf services
		Getting services in org real / space ops as admin...
		OK
		
		name                       service                                   plan       bound apps               last operation   
		sampleNodejslightCallApi   standard_obejct_storage_light_api_calls   standard   sample-api-node-caller   create succeeded
		
		## Execute Application
		$ cf start <APP_NAME>
		
		Example)
		$ cf start sample-api-node-caller
		
		## Shape check
		$ cf a
		Getting apps in org real / space ops as admin...
		OK
		
		name                      requested state   instances   memory   disk   urls   
		sample-api-node-service   started           1/1         512M     512M   sample-api-node-service.bosh-lite.com   
		sample-api-java-broker    started           1/1         512M     1G     sample-api-java-broker.bosh-lite.com   
		sample-api-node-caller    started           1/1         512M     512M   sample-api-node-caller.bosh-lite.com


### <div id='28'></div> 2.8 Test

The sample application is implemented as a REST service, and the eslint/mocha/Istanbul module is used for code check, test, and coverage check. To proceed with the test, the devDependencies module in package.json including the mocha module must be installed. (npm install)

#### 1.  Configuring Development Environment for Code Testing

-   Shape for testing

		<App>/
		├── .eslint                           // Set target for code check
		├── .eslintignore                     // Defines directory and file to exclude when code checking
		├── package.json                    // Defines app components
		├── lib                            // Automatically created when npm run babel is executed. The cfpack and mocha tests target the modules in the lib.
		│   ├── app.js                     // app.js converted to ECMA5
		│   ├── bower_components
		│   └── test
		│       └── test.js
		├── src                           // Code conversion and code check target directory
		│   ├── app.js
		│   ├── bower_components
		│   └── test                      // The mocha test module must be in the src/test directory.
		│       └── test.js
		└── views                        // Directories and files located other than src/lib are not subject to check and mocha testing.
		    ├── apiCaller.handlebars
		    └── layouts
		        └── main.handlebars


-   Configuration package.json for testing

		{
		  ... skip

		  "scripts": {
		    "babel": "babel",                       // Code Conversion
		    "test": "eslint && mocha",              // Code Check and Test
		    "lint": "eslint"                          // Code Check
		  },
		
		  ... skip
		  
		  "devDependencies": {
		    "abacus-babel": "^0.0.6-dev.8",          // Code Conversion (ECMA6 -> ECMA5)
		    "abacus-coverage": "^0.0.6-dev.8",       // Test Coverage Check
		    "abacus-eslint": "^0.0.6-dev.8",          // Code Check
		    "abacus-mocha": "^0.0.6-dev.8"         // Code Test 		  },
		  
		  ... skip
		}


-   Eslint settings

		// Skip the check only for items with false / 0 / disable.
		// For more information on the rules, see http://eslint.org/docs/rules/.
		---
		parser: espree
		
		env:
		  browser: true
		  node: true
		  es6: false
		  jasmine: true
		  mocha: true
		
		globals:
		  __DEV__: true
		  jest: true
		  sinon: true
		  chai: true
		  spy: true
		  stub: true
		
		parserOptions:
		  sourceType: "module"
		
		rules:
		  # ERRORS
		  space-before-blocks: 2                              // 2 or more consecutive whitespace characters
		  indent: [2, 2, { "SwitchCase": 1 }]                 // Set indentation format (only 2 space characters allowed)
		  #strict: [2, "global"]
		  semi: [2, "always"]                                 // ‘;’ omission
		  comma-dangle: [2, "never"]                     
		  no-unused-expressions: 2                      
		  block-scoped-var: 2
		  dot-notation: 2
		  consistent-return: 2                                // Inconsistency in response result format of function
		  no-unused-vars: [2, args: none]                     // Declare a variable that is not referenced in the source
		  quotes: [2, 'single']                               // Do not use ‘“” in text line
		  space-infix-ops: 2
		  no-else-return: 2
		  no-extra-parens: 2
		  no-eq-null: 2
		  no-floating-decimal: 2
		  no-param-reassign: 2
		  no-self-compare: 2
		  wrap-iife: [2, "inside"]
		  brace-style: [2, "stroustrup", { "allowSingleLine": false }]
		  object-curly-spacing: [1, "always"]
		  func-style: [2, "expression"]
		  no-lonely-if: 2
		  space-in-parens: [2, "never"]
		  space-before-function-paren: [2, "never"]
		  generator-star-spacing: [2, "before"]
		  spaced-comment: [2, "always"]
		  eol-last: 2                                         //  no open characters at the end of the source code.
		  no-multi-spaces: 2
		  curly: [2, "multi"]
		  camelcase: [2, {properties: "never"}]               // '_' is allowed in variable names
		  no-eval: 2
		  #require-yield: 2
		  no-var: 2                                           // Forbid var in variable declaration (ECMA6)
		  max-len: [2, 80]                                    // Allows up to 80 characters per line
		  complexity: [2, 6]
		  arrow-parens: [2, "always"]
		
		  # WARNINGS
		  # We use this for functions that reference each other
		  no-use-before-define: 1
		
		  # WISHLIST.
		  # valid-jsdoc: 1
		
		  # DISABLED. These aren't compatible with our style
		  # We use this for private/internal variables
		  no-underscore-dangle: 0
		  # We pass constructors around / access them from members
		  new-cap: 0
		  # We do this in a few places to align values
		  key-spacing: 0
		  # We do this a lot
		  space-after-keywords: 0
		  # We do this mostly for callbacks
		  no-shadow: 0
		  # We do not use spaces in brackets but use spaces in braces
		  space-in-brackets: 0


#### 2.  Run Test

-   Test the module under the Test directory.

		$ cd <Sample application path>/<App>
		$ npm install && npm run babel && npm test
		
		$ npm install && npm run babel && npm test
		sample-api-node-service@0.0.1 ~/KPAAS-API-METERING-SAMPLE/lib/sample_api_node_service
		├─┬ abacus-babel@0.0.6-dev.8 
		...  Installation package tree output ...
		
		npm WARN sample-api-node-service@0.0.1 No repository field.
		
		> sample-api-node-service@0.0.1 babel /home/cloud4u/workspace/KPAAS-API-METERING-SAMPLE/lib/sample_api_node_service
		> babel
		
		src/app.js -> lib/app.js
		src/test/test.js -> lib/test/test.js
		
		> sample-api-node-service@0.0.1 test /home/cloud4u/workspace/KPAAS-API-METERING-SAMPLE/lib/sample_api_node_service
		> eslint && mocha
		
		
		Testing...
		Running Istanbul instrumentation on node_modules/abacus-oauth/lib/index.js
		Running Istanbul instrumentation on node_modules/abacus-request/lib/index.js
		Running Istanbul instrumentation on node_modules/abacus-transform/lib/index.js
		Running Istanbul instrumentation on node_modules/abacus-yieldable/lib/index.js
		Running Istanbul instrumentation on node_modules/abacus-debug/lib/index.js
		Running Istanbul instrumentation on node_modules/abacus-events/lib/index.js
		Running Istanbul instrumentation on node_modules/abacus-lock/lib/index.js
		Running Istanbul instrumentation on node_modules/abacus-lrucache/lib/index.js
		Running Istanbul instrumentation on node_modules/abacus-retry/lib/index.js
		
		
		  sample-api-node-service
		Running Istanbul instrumentation on lib/app.js
		    ✓ Send API usage data to Abacus (59ms)
		    ✓ Missing metering parameter
		    ✓ Not serviced plan
		    ✓ Missing credentials
		    ✓ Send duplicated data to Abacus
		    ✓ Abacus is not serviced
		
		
		  6 passing (159ms)
		
		Source lib/app.js
		'use strict';
		
		// Implemented in ES5 for now
		/* eslint no-var: 0 */
		
		... skip ...
		
		// Export our public functions
		module.exports = sampleApiService;
		module.exports.runCLI = runCLI;
		
		Coverage lines 88.89% statements 86.9% lib/app.js
		
		Run time 686ms

### <div id='29'></div> 2.9 API and CF-Abacus Interworking Test

CF-Abacus interworking test for API interworking and API usage can be performed by accessing from a web browser through the url of the API interworking sample application.

#### 1.  Check CF-Abacus interwork

	## Check the organization guid
	$ cf org <Organization that deployed the sample application> --guid
	
	Example)
	$ cf org real --guid
	877d01b2-d177-4209-95b0-00de794d9bba
	
	## Check the sample application guid
	$ cf env <Sample application name>
	Example)
	$ cf env sample-api-node-caller
	Getting env variables for app sample-api-node-caller in org real / space ops as admin...
	OK
	
	<<skip>>
	{
	 "VCAP_APPLICATION": {
	  "application_id": "58872d8a-edfc-44df-97f0-df67cf9033a7",
	  "application_name": "sample-api-node-caller",
	  "application_uris": [
	   "sample-api-node-caller.bosh-lite.com"
	  ],
	  "application_version": "55678102-584c-4fca-8304-82f727506b1d",
	  "limits": {
	   "disk": 512,
	   "fds": 16384,
	   "mem": 512
	  },
	  "name": "sample-api-node-caller",
	  "space_id": "2ce08996-f463-406c-a971-adbbaf4e4ca5",
	  "space_name": "ops",
	  "uris": [
	   "sample-api-node-caller.bosh-lite.com"
	  ],
	  "users": null,
	  "version": "55678102-584c-4fca-8304-82f727506b1d"
	 }
	}
	
	<<skip>> 
	
	## Check API usage
	$ curl 'http://abacus-usage-reporting.<Application Platform domain>/v1/metering/organizations/<Organization that deployed the sample application>/aggregated/usage'
	
	Example)
	$ curl 'http://abacus-usage-reporting.bosh-lite.com/v1/metering/organizations/877d01b2-d177-4209-95b0-00de794d9bba/aggregated/usage'

### <div id='30'/> 2.10. Sample Code

The sample Code can be downloaded from the site below.

[Download](https://nextcloud.k-paas.org/index.php/s/mEbGNcJjrEj7GWx/download)


[Development_Guide_Nodejs_Image01]:./images/nodejs_Service_Metering/2-0-0.png


### [Index](https://github.com/K-PaaS/Guide-eng/blob/master/README.md) > [AP User Guide](../README.md) > Node.js API Service Metering Development
