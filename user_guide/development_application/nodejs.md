### [Index](https://github.com/PaaS-TA/Guide-eng/blob/master/README.md) > [AP User Guide](../README.md) > Node.js Development

## Table of Contents
1. [Document Outline](#1)
     * [1.1. Purpose](#2)
     * [1.2. Range](#3)
     * [1.3. References](#4)
2. [Configuration of Development Environment](#5)
     * [2.1. Installation of Node.js and npm](#6)
3. [Development](#7)
     * [3.1. Create Node.js Express Application](#8)
     * [3.2. Node.js Sample Application](#9)
     * [3.3. Application Configuration](#10)
     * [3.4. VCAP_SERVICES Environment Information](#11)
     * [3.5. Connect Mysql](#12)
     * [3.6. Connect Cubrid](#13)
     * [3.7. Connect MongoDB](#14)
     * [3.8. Connect Redis](#15)
     * [3.9. Connect RabbitMQ](#16)
     * [3.10. Connect GlusterFS](#17)
4. [Deployment](#18)
     * [4.1. Open Platform Login](#19)
     * [4.2. Create Service](#20)
     * [4.3. Application Deployment](#21)
     * [4.4. Connect Application and Service](#22)
     * [4.5. Execute Acpplication](#23)
5. [Test](#24)




# <div id='1'> 1. Document Outline


### <div id='2'> 1.1. Purpose

This document, node.js Application Development Guide, is a use guide on how to deploy the application and use the service by connecting the Node.js application with the servicepack (Mysql, Cubrid, MongoDB, RabbitMQ, Redis, GlusterFS) of the Open Platform Project.


### <div id='3'> 1.2. Range

This document covers the Node.js Application Development and servicepack connection of the Open Platform Project.


### <div id='4'> 1.3. References
**<https://docs.cloudfoundry.org/devguide/>**  
**<https://docs.cloudfoundry.org/buildpacks/node/node-tips.html>**  
**<https://nodejs.org/>**  
**<http://expressjs.com/ko/>**  
**<https://github.com/felixge/node-mysql>**  
**<https://github.com/CUBRID/node-cubrid>**  
**<https://github.com/mongodb/node-mongodb-native>**  
**<https://github.com/NodeRedis/node_redis>**  
**<https://github.com/postwait/node-amqp>**  
**<https://github.com/pkgcloud/pkgcloud>**  
**<https://mochajs.org/>**  


# <div id='5'> 2. Configuration of Development Environment

Bind the various servicepacks registered in Open PaaS with the application made in Node.js language. The Node.js application can be created in a Windows environment so that access information for each service can be obtained from environmental information (VCAP_SERVICES) bound to the application and applied to the application.

Configure the development environment to develop the Node.js Application as shown below.

- BuildPack: v1.3.4
- OS : Windows 7 64bit
- Node.js : v0.12.4
- npm : v2.10.1


### <div id='6'> 2.1. Installation of Node.js and npm

##### 1. Node.js Download

- Access the address below and download node-v0.12.4-x64.msi.

>https://nodejs.org/dist/v0.12.4/x64/node-v0.12.4-x64.msi
>![2-2-1-0]

##### 2. Node.js Installation

- Double click node-v0.12.4-x64.msi from the downloaded folder and start the installation.

![2-2-1-1]

- Click the "Run" button to continue.

![2-2-1-2]

- Click the "Next" button to continue.

![2-2-1-3]

- Agree to the license by checking the "I accept the terms in the License Agreement" and click "Next" to continue.

![2-2-1-4]

- Enter or select the installation path and click the "Next" button to continue the process.
For this example, the installation path was set to C:\Program Files\nodejs\.

![2-2-1-5]

- Select the item to install and click "Next" to continue.
For this example, Node.js, npm, and doc were selected and installed. The environment variable PATH was added as well.

![2-2-1-6]

- Click the "Install" button and install.

![2-2-1-7]

- Click the "Finish" button to complete the installation.

![2-2-1-8]

- 'Window Key+R' or 'Start->Run' icon to open the run window and enter 'cmd', click the "OK" button to open the command window.

![2-2-1-9]

- Enter the command below at the cmd tab to verify if the node.js and npm version was installed properly.

><div>>node -v
><div>>npm -v
![2-2-1-10]

Development Tool
Node.js is a javascript-based language that can use document editors such as Notepad++, Sublim Text, and EditPlus as the development tool. Also, Plugin Nodeclipse of Eclipse can be used.


# <div id='7'> 3. Development

Data management for sample applications uses either MySQL, CubridDB, or MongoDB, so it is determined by the DBType value in the body of the request upon API request.


### <div id='8'> 3.1. Create Node.js Express Application
##### 1. Use 'express-generator' to create Express Application

- In the command window, go to the path for development and enter the command below to install 'expression-generator' npm.

><div>>npm install express-generator
![2-3-1-0]

- Create Express Application. '-e' option uses view engine as ejs and default view engine is jade.

><div>>.\node_modules\.bin\express -e
![2-3-1-1]

##### 2. npm Installation

- Install npm which is included by default in the Express Application. The definition of npm to install is defined at package.json.

><div>>npm install
![2-3-1-2]

##### 3. Run Node.js Express Application

- Run the application by using any of the commands below.

><div>>npm start
><div>>node bin/www
![2-3-1-3]

- Access the link address below with the browser and verify if the application works properly.

><div>http://localhost:3000/
![2-3-1-4]


### <div id='9'> 3.2. Node.js Sample Application

##### 1. Download Node.js Sample Application

- The completed sample application can be downloaded from the /OpenPaaSSample/node-sample-app of the link below.

> https://nextcloud.paas-ta.org/index.php/s/x8Tg37WDFiL5ZDi/download

##### 2. Go to the Node.js Sample Application Path

- Go to the Node.js sample application path below the downloaded path.

><div>>cd node-sample-app
![2-3-2-0]

##### 3. Node.js Sample Application Directory Structure

![2-3-2-1]

<table>
  <tr>
    <td>File/Folder</td>
    <td>Purpose</td>
  </tr>
  <tr>
    <td>package.json</td>
    <td>Used to describe dependency information of npm required for node.js application.<br>
    If you do not enter any information after installing the npm install command, use the information in this file to install npm.</td>
  </tr>
  <tr>
    <td>app.js</td>
    <td>Setting about Express. Routing information for http requests is also defined here.</td>
  </tr>
  <tr>
    <td>bin/www</td>
    <td>It is essentially the starting point of node.js sample application, sets up an http server. The Port used when running the server can be set here.</td>
  </tr>
  <tr>
    <td>routes/</td>
    <td>The actual contents to be performed after routing in app.js are written. The contents of the service connection are also here.</td>
  </tr>
  <tr>
    <td>public/</td>
    <td>An externally accessible directory. There are static files needed for web services such as css and js.<br>
    Settings for externally accessible directories are set in the app.js file..</td>
  </tr>
  <tr>
    <td>views/</td>
    <td>Where ejs files are located.<br>
    The ejs file is a template engine that helps you write HTML more easily and is used when you set the view engine of express to ejs.<br>
    Use the render() method to convert the ejs file to HTML and show.</td>
  </tr>
  <tr>
    <td>test/</td>
    <td>a directory that wrote the mocha test.</td>
  </tr>
  <tr>
    <td>(node_modules)</td>
    <td>Although it is not visible in the picture above, it is installed under this directory when the module is installed with npm install. The npm modules installed here can be retrieved from the application to require and be used.</td>
  </tr>
  <tr>
    <td>Makefile</td>
    <td>a file to run mocha test easily in linux.</td>
  </tr>
  <tr>
    <td>manifest.yml</td>
    <td>An application setting when deploying at the Open Platform. Application Name, Path to be deployed, and number of instances can be defined.</td>
  </tr>
  <tr>
    <td>.cfignore</td>
    <td>Describes directories or files that will not be included in distribution on an open platform.</td>
  </tr>
  <tr>
    <td>.gitignore</td>
    <td>Describes directories or files that will not be included in Git deployment.</td>
  </tr>
  <tr>
    <td>README.md</td>
    <td>A brief description of the Node.js sample application.</td>
  </tr>
</table>


### <div id='10'> 3.3. Application Configuration

This sample was installed by explicitly selecting the version of each module based on Node.js version 0.12.4 and nm version 2.10.1.
When modifying package.json, it is recommended to install the module that matches the version of Node.js installed.

1)  ./package.json
- Define modules needed in an application.

```json
{
  "name": "node-sample-app",
  "version": "0.8.0",
  "private": true,
  "scripts": {
    "start": "node ./bin/www"
  },
  "dependencies": {
    "body-parser": "1.13.2",
    "cookie-parser": "1.3.5",
    "debug": "2.2.0",
    "morgan": "1.6.1",
    "serve-favicon": "2.3.0",
    "express": "4.13.1",
    "ejs": "2.3.4",
    "generic-pool": "2.2.1",
    "mysql": "2.9.0",
    "node-cubrid":"2.2.5",
    "mongodb":"2.0.48",
    "redis":"2.4.2",
    "uuid":"2.0.1",
    "amqp":"0.2.4",
    "pkgcloud":"1.2.0-alpha.0",
    "formidable":"1.0.17",
    "mocha":"2.3.4",
    "should":"7.1.1",
    "supertest":"1.1.0"
  },
  "engines": {
    "node": "0.12.4",
    "npm": "2.10.1"
  }
}
```

<table>
  <tr>
    <td>name</td>
    <td>Application Name</td>
  </tr>
  <tr>
    <td>version</td>
    <td>Application Version</td>
  </tr>
  <tr>
    <td>private</td>
    <td>Set whether or not to post on the npm. (true: do not post)</td>
  </tr>
  <tr>
    <td>scripts.start</td>
    <td>Commands to run with the npm start command(Command to run Application)</td>
  </tr>
</table>

- dependencies

<table>
  <tr>
    <td>body-parser</td>
    <td rowspan=7> Modules used in Express Framework.</td>
  </tr>
  <tr>
    <td>cookie-parser</td>
  </tr>
  <tr>
    <td>debug</td>
  </tr>
  <tr>
    <td>morgan</td>
  </tr>
  <tr>
    <td>serve-favicon</td>
  </tr>
  <tr>
    <td>express</td>
  </tr>
  <tr>
    <td>ejs</td>
  </tr>
  <tr>
    <td>generic-pool</td>
    <td>Module to create and manage connection pool</td>
  </tr>
  <tr>
    <td>mysql</td>
    <td>mysql Module</td>
  </tr>
  <tr>
    <td>node-cubrid</td>
    <td>cubrid Module</td>
  </tr>
  <tr>
    <td>mongodb</td>
    <td>mongodb Module</td>
  </tr>
  <tr>
    <td>redis</td>
    <td>redis Module</td>
  </tr>
  <tr>
    <td>uuid</td>
    <td>Modules that creates URN</td>
  </tr>
  <tr>
    <td>amqp</td>
    <td>rabbitMQ Enabled Module</td>
  </tr>
  <tr>
    <td>pkgcloud</td>
    <td>swift, glusterfs Enabled Module</td>
  </tr>
  <tr>
    <td>formidable</td>
    <td>Module that parses form data</td>
  </tr>
  <tr>
    <td>mocha</td>
    <td>node.js test Module</td>
  </tr>
  <tr>
    <td>should</td>
    <td>Module used in mocha test</td>
  </tr>
  <tr>
    <td>supertes</td>
    <td>Module used in rest test</td>
  </tr>
</table>

- engines

<table>
  <tr>
    <td>node</td>
    <td>node.js module version to use in application.<br>
    When deployed on an open platform and used, there are restrictions on the version available depending on the Node.js version supported by Node Buildpack.<br>
    - https://github.com/cloudfoundry/nodejs-buildpack/blob/master/CHANGELOG</td>
  </tr>
  <tr>
    <td>npm</td>
    <td>npm version to use in application<br>
    Like Node.js, there are restrictions on versions available depending on the version supported by Node Buildpack.</td>
  </tr>
</table>

2) Module Installation
- Install the module defined in package.json. If you did not specify the module name, install all modules in package.json's dependencies.
```
>npm install
```

3) ./bin/www
- Set the PORT used by the HTTP server to use the PORT provided by the open platform. An open platform uses this value to detect if an application is working properly. Applications do not function properly with a PORT other than this value.

```
#!/usr/bin/env node

/**
 * Module dependencies.
 */

var app = require('../app');
var debug = require('debug')('node-sample-app:server');
var http = require('http');

/**
 * Get port from environment and store in Express.
 * Get the PORT environment variable and put it in the variable.
 * 'process.env.PORT' is an environment variable used in Cloud.
 */

var port = normalizePort(process.env.PORT || '3000');
app.set('port', port);

/**
 * Create HTTP server.
 * Create HTTP Server.
 */

var server = http.createServer(app).listen(app.get('port'), function(){
  console.log('Express server listening on port ' + app.get('port'));
});

/**
 * Listen on provided port, on all network interfaces.
 * Sets the PORT the server will use.
 */

server.listen(port);
server.on('error', onError);
server.on('listening', onListening);
...(Skip)
```

4) ./app.js
- Request URL Mapping setup

```javascript
var express = require('express');
var path = require('path');
var favicon = require('serve-favicon');
var logger = require('morgan');
var cookieParser = require('cookie-parser');
var bodyParser = require('body-parser');

var routes = require('./routes/index');
var users = require('./routes/users');

var app = express();

// js files to perform after URL mapping
var org_chart_mysql = require('./routes/rest/org_chart/mysql')
  , org_chart_mongo = require('./routes/rest/org_chart/mongo')
  , org_chart_cubrid = require('./routes/rest/org_chart/cubrid')
  , orgs_mysql = require('./routes/rest/orgs/mysql')
  , orgs_mongo = require('./routes/rest/orgs/mongo')
  , orgs_cubrid = require('./routes/rest/orgs/cubrid')
  , groups_mysql = require('./routes/rest/groups/mysql')
  , groups_mongo = require('./routes/rest/groups/mongo')
  , groups_cubrid = require('./routes/rest/groups/cubrid')
  , login = require('./routes/rest/login/login')
  , image = require('./routes/rest/image/image')
  , page = require('./routes/page/page_processing');

// view engine setup
// Sets View engine
app.set('views', path.join(__dirname, 'views/'));
app.set('view engine', 'ejs');

// uncomment after placing your favicon in /public
//app.use(favicon(path.join(__dirname, 'public', 'favicon.ico')));
app.use(logger('dev'));
app.use(bodyParser.json());
app.use(bodyParser.urlencoded({ extended: false }));
app.use(cookieParser());
// Set static file location
app.use(express.static(path.join(__dirname, 'public')));

/*
* URL Mapping setup
*/
app.use(   '/', routes);
app.use(   '/users', users);

// page
app.get(   '/login', page.login);
app.get(   '/manage', page.manage);
app.get(   '/main/:id', page.main);

// org-chart
app.get(   '/org-chart/:org_id/mysql', org_chart_mysql.index);
app.get(   '/org-chart/:org_id/cubrid', org_chart_cubrid.index);
app.get(   '/org-chart/:org_id/mongo', org_chart_mongo.index);
app.get(   '/org-chart/:org_id/status/mysql', org_chart_mysql.status);
app.get(   '/org-chart/:org_id/status/cubrid', org_chart_cubrid.status);
app.get(   '/org-chart/:org_id/status/mongo', org_chart_mongo.status);

//orgs
//  mysql
app.get(   '/orgs/mysql', orgs_mysql.index);
app.post(  '/orgs/mysql', orgs_mysql.create);
app.get(   '/orgs/:org_id/mysql', orgs_mysql.show);
app.put(   '/orgs/:org_id/mysql', orgs_mysql.update);
app.delete('/orgs/:org_id/mysql', orgs_mysql.destroy);
…..(Skip)
```


### <div id='11'> 3.4. VCAP_SERVICES Environment Information
To obtain access information for each service to which an application deployed on an open platform is bound, information can be obtained by reading the VCAP_SERVICES environment information registered for each application.

1)  Application environment information of Open Platform
- environment information is registered to each application in JSON form when the service is bound.

```json
{
 "VCAP_SERVICES": {
  "CubridDB": [
   {
    "credentials": {
     "host": "10.30.60.23::",
     "hostname": "10.30.60.23",
     "jdbcurl": "jdbc:cubrid:10.30.60.23::fccf1d7869ff72ce:b2f6b4af1e7bd7d8:45f179c648ee60a5:",
     "name": "fccf1d7869ff72ce",
     "password": "45f179c648ee60a5",
     "port": "",
     "uri": "cubrid:10.30.60.23::fccf1d7869ff72ce:b2f6b4af1e7bd7d8:45f179c648ee60a5:",
     "username": "b2f6b4af1e7bd7d8"
    },
    "label": "CubridDB",
    "name": "sample-cubrid-instance",
    "plan": "utf8",
    "tags": [
     "cubrid",
     "document"
    ]
   }
  ],
…..(Skip)…..
```

2)  How to access application environment information on open platforms in Node.js.
- The VCAP_SERVICES value of the system environment variable may be read and accessed.
```
process.env.VCAP_SERVICES
```


### <div id='12'> 3.5. Connect Mysql
1)  ./route/db/mysql/db_pooling.js
- Create mysql Connection Pool by accessing application environment information on open platforms
```javascript
/**
 * Connect generic-pool
 * Implement MySQL Pool module
 */

var generic_pool  = require("generic-pool");
var mysql   = require("mysql");

config = {};
if (process.env.VCAP_SERVICES) {
  // cloud env Setting. Refer to 2.3.4 VCAP_SERVICES environment information for data structure
  var cloud_env   = JSON.parse(process.env.VCAP_SERVICES);
  var mysql_env   = cloud_env["Mysql-DB"][0]["credentials"];

  config = {
    host:mysql_env.hostname,
    user:mysql_env.username,
    password:mysql_env.password,
    database:mysql_env.name
  };
} else {
  // local env
  config = {
    host:'10.30.40.63',
    user:'cESTBl9QpxGVF5Xa',
    password:'aVu1ynInBnaEeFY0',
    database:'cf_ea68784e_3de6_439d_afc1_d51b4e95627b'
  };
}

var pooling   = generic_pool.Pool({
  name:"mysql",
  create:function(cb){
    // create Connection
    var conn = mysql.createConnection(config);
    conn.connect(function(err){
      if( err) console.log("error in connecting mysql");
      else {
      //  console.log("mysql connected successfully");
      } cb(err, conn);
      // Throw the connection object into the pooling via the callback function
    });
  },
  destroy:function(myConn){
    myConn.end(function(err){
      if( err)  console.log("error in unconnecting mysql");
  //    else    console.log("mysql unconnected successfully");
    });
  },
  min:3,
  max:5,
  idleTimeoutMillis:1000*500,
  log:false

});

process.on("exit", function(){
  pooling.drain(function(){
    pooling.destroyAllNow();
  });
});

module.exports = pooling;
```


### <div id='13'> 3.6. Connect Cubrid
1)  ./route/db/cubrid/db_pooling.js
- Create a cubic connection pool by accessing application environment information on an Open Platform

```javascript
/**
 * Connect generic-pool
 * Implement cubrid pool module
 */

var generic_pool  = require("generic-pool");
var cubrid    = require("node-cubrid");

var   database
  , port
  , hostname
  , username
  , password;
if (process.env.VCAP_SERVICES) {
  // cloud env Setting. Refer to 2.3.4 VCAP_SERVICES environment information for data structure
  var cloud_env   = JSON.parse(process.env.VCAP_SERVICES);
  var cubrid_env    = cloud_env["CubridDB"][0]["credentials"];

  database  = cubrid_env.name
  port    = cubrid_env.port
  hostname  = cubrid_env.hostname
  username  = cubrid_env.username
  password  = cubrid_env.password;
} else {
  // local env
  database  = 'fccf1d7869ff72ce'
  port    = ''
  hostname  = '10.30.60.23'
  username  = 'b2f6b4af1e7bd7d8'
  password  = '45f179c648ee60a5';
}
var pooling   = generic_pool.Pool({
  name:"cubrid",
  create:function(cb){
//    console.log("cubrid_env.uri:" + cubrid_env.uri);
    var conn = cubrid.createCUBRIDConnection(hostname, port, username, password, database);
    // create Connection
    conn.connect(function(err){
      if( err) console.log("error in connecting cubrid");
      else{
//        console.log("cubrid connected successfully");
        cb(err, conn);
      }
      // Throw the connection object into the pooling via the callback function.
    });
  },
  destroy:function(myConn){
    myConn.end(function(err){
      if( err)  console.log("error in unconnecting cubrid");
//      else    console.log("cubrid unconnected successfully");
    });
  },
  min:3,
  max:5,
  idleTimeoutMillis:1000*50,
  log:false,

});

process.on("exit", function(){
  pooling.drain(function(){
    pooling.destroyAllNow();
  });
});

module.exports = pooling;
```

### <div id='14'> 3.7. Connect MongoDB
1)  ./route/db/mongo/db_pooling.js
- Create a mongodb Connection Pool by accessing application environment information on an open platform

```javascript
/**
 * connect generic-pool
 * Implement mongo pool module
 */

var generic_pool = require("generic-pool");
var mongoClient  = require("mongodb").MongoClient;

var url = '';
if (process.env.VCAP_SERVICES) {
  // cloud env. Setting. Refer to 2.3.4 VCAP_SERVICES environment information for data structure
  var cloud_env = JSON.parse(process.env.VCAP_SERVICES);
  var mongo_env = cloud_env["Mongo-DB"][0]["credentials"];

  url = mongo_env.uri;

} else {
  // local env.
  url = 'mongodb://d3e35ad5-9f49-43ae-bc85-08e39ec1d8eb:fc23791e-b2d8-402d-b070-90bfbdb5dcfa@10.30.60.53:27017/e37e541c-75de-4f01-8196-63e2d902e768';
}

var pooling = generic_pool.Pool({
        name:"mongo",
        create:function(cb){
    // create Connection
    mongoClient.connect(url, function(err, db){
      if (err) console.log("error in connecting mongo");
      else {
        cb(err, db);
      }
    });
        },
        destroy:function(myDb){
                myDb.close(function(err){
                        if( err)        console.log("error in unconnecting mysql");
        //              else            console.log("mysql unconnected successfully");
                });
        },
        min:3,
        max:5,
        idleTimeoutMillis:1000*500,
        log:false

});


module.exports = pooling;
```


### <div id='15'> 3.8. Connect Redis
1)  ./route/redis/redis.js
- Create a redis Connection by accessing application environment information on an open platform

```javascript
var redis = require("redis");

var options = {};
if (process.env.VCAP_SERVICES) {
  // cloud env. Setting. Refer to 2.3.4 VCAP_SERVICES environment information for data structure
  var services = JSON.parse(process.env.VCAP_SERVICES);
  var redisConfig = services["redis-sb"];

  if (redisConfig) {
      var node = redisConfig[0];
      options = {
      host: node.credentials.host,
      port: node.credentials.port,
      pass: node.credentials.password,
    };
  }

} else {
  // local env.
    options = {
    host: '10.30.40.71',
    port: '34838',
    pass: 'c239b721-d986-4ee3-8816-b5f5fa9f3ffb',
  };
}

var client = null;
exports.open = function(cb) {
//  console.log(JSON.stringify(options));
  //create Client
  client = redis.createClient(options);
  // get auth.
  client.auth(options.pass);

  cb(client);
}
exports.close = function(){
  client.end();
}
```


### <div id='16'> 3.9. Connect RabbitMQ
1)  ./route/rabbitMQ/rabbitMQ.js
- Create rabbitMQ Connection by accessing application environment information on an open platform

```javascript
var amqp = require('amqp');

var url = '';
if (process.env.VCAP_SERVICES) {
  // cloud env. Setting. Refer to 2.3.4 VCAP_SERVICES environment information for data structure
        var services = JSON.parse(process.env.VCAP_SERVICES);
        var rabbitMQConfig = services["p-rabbitmq"];

        if (rabbitMQConfig) {
                var node = rabbitMQConfig[0];
    url = node.credentials.uri;
        }
} else {
  // local env.
  url = 'amqps://14b1ab93-4cdb-46af-8cdd-8d8073bbe282:cl71e9ihgu6gvhj1eiqj9uh4um@10.30.40.82:5671/6ffb4d8a-8748-4f00-a338-80e6eadee822';
}

exports.open = function(cb){
  // create connection.
  var conn = amqp.createConnection({url: url});

  // it must be cb(callback) after the 'ready' event.
  conn.on('ready', function(){
    cb(conn);
  });
}

// not used.
/*
exports.close = function(){
  conn.disconnect();
}
*/
```

### <div id='17'> 3.10. Connect GlusterFS
1)  ./route/glusterfs/glusterfs.js
- Create glusterfs Connection by accessing application environment information on an open platform

```javascript
var pkgcloud = require('pkgcloud');
var http = require('http');
var url = require('url');

var credentials = {};
var container_name = 'node_container';
if (process.env.VCAP_SERVICES) {
  // cloud env. Setting. Refer to 2.3.4 VCAP_SERVICES environment information for data structure
  var services = JSON.parse(process.env.VCAP_SERVICES);
  var glusterfsConfig = services["glusterfs"];

  if (glusterfsConfig) {
    var config = glusterfsConfig[0];
    credentials = {
      provider: 'openstack', //
            username: config.credentials.username,
            password: config.credentials.password,
      authUrl:  config.credentials.auth_url.substring(0, config.credentials.auth_url.lastIndexOf('/')),
      region: 'RegionOne' //
    };
  }
} else {
  // local env.
    credentials = {
      provider: 'openstack',
            username: 'cf13d551d997458e',
            password: 'b45cc01d53a4f0e0',
      authUrl:  'http://54.199.136.22:5000/',
      region: 'RegionOne'
    };
}

// create Client
var client = pkgcloud.storage.createClient(credentials);

// delete container for test
/*
client.destroyContainer(container_name, function(err, result){
  if (err) console.log(err);
  else console.log(result);
});
*/

// check container
client.getContainer(container_name, function(err, container){
        if (err)
        {
    // if container not exist
                if (err.statusCode === 404)
                {
      // create container
                        client.createContainer({name:container_name}, function(create_err, create_container){
                                if (create_err) console.log(err);
        else
        {
          // if container created successfully, set a readable member(X-Contaner-Read: .r:*)
          // if the container was created successfully, set the container to be readable by anyone.(X-Contaner-Read: .r:*)
          // There is a bug in the code(pkgcloud). so I used api call.
          // if metadata is placed to pkgcloud module, set through API because the value above cannot be entered properly when the logic is attached with prefix.
          var serviceUrl = url.parse(create_container.client._serviceUrl);
          var option = {
            host: serviceUrl.hostname,
            port: serviceUrl.port,
            path: serviceUrl.path+'/'+container_name,
            method: 'POST',
            headers: {
              'X-Auth-Token': create_container.client._identity.token.id,
              'X-Container-Read': '.r:*' // ACL form
            }
          };
          var req = http.request(option, function(res){
          });
          req.end();
        }

                        });
                }
                else    console.log(err);
        }
});

module.exports = client;
```


# <div id='18'> 4. Deployment

Deploying an application on an open platform allows you to connect and use the deployed application with the services provided by the open platform. Only when it is executed on an open platform can it access the application environment variables of the open platform and access the service.


### <div id='19'> 4.1.  Open Platform Login

Log in to Open Platform to perform the task below

>$ cf api --skip-ssl-validation https://api.cf.open-paas.com # Set TARGET to Open Platform<br>
>$ cf login -u testUser -o sample_test -s sample_space # Login Request<br>
![2-4-1-0]


### <div id='20'> 4.2.  Create Service
Create the application to use at the service through Open Platform. It may be generated without a separate service installation process, and access information may be obtained through an application and binding process.
- Service list and plan of each service can be checked through Create Service command (cf marketplace).

><div># cf create-service SERVICE PLAN SERVICE_INSTANCE [-c PARAMETERS_AS_JSON] [-t TAGS]
><div>$ cf create-service p-mysql 100mb node-mysql
><div>$ cf create-service CubridDB utf8 node-cubrid
><div>$ cf create-service Mongo-DB default-plan node-mongodb
><div>$ cf create-service redis-sb shared-vm node-redis
><div>$ cf create-service glusterfs glusterfs-5Mb node-glusterfs
><div>$ cf create-service p-rabbitmq standard node-rabbitmq
![2-4-2-0]


### <div id='21'> 4.3. Application Deployment

Deploy the application at the Open Platform. The deployed application can be used through binding with the created service.

- When cf push is commanded, the deployment is proceeded by referring to the manifest.yml of the current directory.

##### 1. Create manifest.yml

```yaml
---
applications:
- name: node-sample-app # Application name
  memory: 512M # Application's memory size
  instances: 1 # Application's number of instance
  command: npm start # Command to execute the application
  path: ./ # path of the application to be located
```

##### 2. Create Mysql and Cubrid Table
- A table must be created at the DB to function as the managing organization of the Sample App.
- For more information on how to add tables to Mysql and Cubrid, refer to 'Client Tool Connection' in the OpenPaaS Mysql, Cubrid Service Pack Installation Guide.
- Execute the table creation SQL below using the Client tool. (Both Mysql and Cubrid can be created with the same SQL.)

```
DROP TABLE IF EXISTS ORG_TBL;
DROP TABLE IF EXISTS GROUP_TBL;


CREATE TABLE ORG_TBL (
  id INT AUTO_INCREMENT PRIMARY KEY
  , label VARCHAR(40) NOT NULL
  , `desc` VARCHAR(150)
  , url VARCHAR(500) DEFAULT '#'
  , created TIMESTAMP DEFAULT CURRENT_TIMESTAMP NOT NULL
  , modified TIMESTAMP
);

CREATE TABLE GROUP_TBL (
  id INT AUTO_INCREMENT PRIMARY KEY
  , org_id INTEGER NOT NULL
  , parent_id INTEGER
  , label VARCHAR(40) NOT NULL
  , `desc` VARCHAR(150)
  , thumb_img_name VARCHAR(256)
  , thumb_img_path VARCHAR(512)
  , url VARCHAR(500) DEFAULT '#'
  , created TIMESTAMP  DEFAULT CURRENT_TIMESTAMP  NOT NULL
  , modified TIMESTAMP
);

ALTER TABLE GROUP_TBL
ADD FOREIGN KEY(org_id)
REFERENCES ORG_TBL(id)
ON DELETE CASCADE;

ALTER TABLE GROUP_TBL
ADD FOREIGN KEY(parent_id)
REFERENCES GROUP_TBL(id)
ON DELETE CASCADE;
```

##### 3. Application Deployment

- Deploy with the cf push command. If the value was not set, it uses the setting of manifest.yml. Since the service is not connected yet, deploy with --no-start option and do not execute yet.

><div>$ cf push --no-start
![2-4-3-0]


### <div id='22'> 4.4. Connect Application and Service

Bind: A process where application and service get connected. This process generates access information to access the service.

- Connect Application and Service

><div>cf bind-service APP_NAME SERVICE_INSTANCE [-c PARAMETERS_AS_JSON]
><div>$ cf bind-service node-sample-app node-mysql
><div>$ cf bind-service node-sample-app node-cubrid
><div>$ cf bind-service node-sample-app node-mongodb
><div>$ cf bind-service node-sample-app node-redis
><div>$ cf bind-service node-sample-app node-glusterfs
><div>$ cf bind-service node-sample-app node-rabbitmq
![2-4-4-0]

Check Connection

><div>$ cf services
![2-4-4-1]


### <div id='23'> 4.5. Execute Application

The application is executed with the access information environment variable generated through the service binding process.

><div>$ cf start node-sample-app
![2-4-5-0]


# <div id='24'> 5. Test

The sample application is configured as a REST service. Mocha module was used for the REST Test. All the modules of package.json including the mocha module should be installed to proceed with the test. (npm install)

##### 1. Makefile
- It is written to solve the inconvenience of accessing and executing the bin file every time. Available on Linux operating systems.

```
test:
  @./node_modules/.bin/mocha -u tdd

.PHONY: test
```

##### 2. Execute Test

- Execute the test under the test directory.

2.1.  Window

><div>> .\node_modules\.bin\mocha -u tdd test

2.2.  Linux

><div>$ make test
![2-5-0-0]

    
[2-2-1-0]:./images/nodejs/2-2-1-0.png
[2-2-1-1]:./images/nodejs/2-2-1-1.png
[2-2-1-2]:./images/nodejs/2-2-1-2.png
[2-2-1-3]:./images/nodejs/2-2-1-3.png
[2-2-1-4]:./images/nodejs/2-2-1-4.png
[2-2-1-5]:./images/nodejs/2-2-1-5.png
[2-2-1-6]:./images/nodejs/2-2-1-6.png
[2-2-1-7]:./images/nodejs/2-2-1-7.png
[2-2-1-8]:./images/nodejs/2-2-1-8.png
[2-2-1-9]:./images/nodejs/2-2-1-9.png
[2-2-1-10]:./images/nodejs/2-2-1-10.png
[2-3-1-0]:./images/nodejs/2-3-1-0.png
[2-3-1-1]:./images/nodejs/2-3-1-1.png
[2-3-1-2]:./images/nodejs/2-3-1-2.png
[2-3-1-3]:./images/nodejs/2-3-1-3.png
[2-3-1-4]:./images/nodejs/2-3-1-4.png
[2-3-2-0]:./images/nodejs/2-3-2-0.png
[2-3-2-1]:./images/nodejs/2-3-2-1.png
[2-4-1-0]:./images/nodejs/2-4-1-0.png
[2-4-2-0]:./images/nodejs/2-4-2-0.png
[2-4-3-0]:./images/nodejs/2-4-3-0.png
[2-4-4-0]:./images/nodejs/2-4-4-0.png
[2-4-4-1]:./images/nodejs/2-4-4-1.png
[2-4-5-0]:./images/nodejs/2-4-5-0.png
[2-5-0-0]:./images/nodejs/2-5-0-0.png

### [Index](https://github.com/PaaS-TA/Guide-eng/blob/master/README.md) > [AP User Guide](../README.md) > Node.js Development
