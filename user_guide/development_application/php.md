### [Index](https://github.com/PaaS-TA/Guide-eng/blob/master/README.md) > [AP User Guide](../README.md) > PHP Development

## Table of Contents
1. [Outline](#1)
     * 1.1. [Document Outline](#1.1)
         * 1.1.1. [Purpose](#1.1.1)
         * 1.1.2. [Range](#1.1.2)
         * 1.1.3. [Restrictions](#1.1.3)
         * 1.1.4. [References](#1.1.4)
2. [PHP Application Development guide](#2)
     * 2.1. [Outline](#2.1)
     * 2.2. [Development Environment Configuration](#2.2)
         * 2.2.1. [Download PHP Sample Source](#2.2.1)
         * 2.2.2. [XAMP Installation](#2.2.2)
         * 2.2.3. [PHP Executing Environment Setting](#2.2.3)
         * 2.2.4. [Composer Installation](#2.2.4)
         * 2.2.5. [Mongo Driver Installation](#2.2.5)
     * 2.3. [Development](#2.3)
         * 2.3.1. [Use Package Description](#2.3.1)
         * 2.3.2. [Directory Description](#2.3.2)
         * 2.3.3. [Application Environment Setting](#2.3.3)
         * 2.3.4. [VCAP_SERVICES Environment Setting Information](#2.3.4)
         * 2.3.5. [Connect Mysql](#2.3.5)
         * 2.3.6. [Connect CUBRID](#2.3.6)
         * 2.3.7. [Connect MongoDB](#2.3.7)
         * 2.3.8. [Connect Redis](#2.3.8)
         * 2.3.9. [Connect RabbitMQ](#2.3.9)
         * 2.3.10. [Connect GlusterFS](#2.3.10)
     * 2.4. [Deployment](#2.4)
     * 2.5. [Test](#2.5)

  
# <div id='1'> 1.  Outline
  
## <div id='1.1'> 1.1.  Document Outline
 
### <div id='1.1.1'> 1.1.1.  Purpose 

This document (PHP Application Developement Guide) provides a way to integrate services from development platform projects with PHP applications.
  
### <div id='1.1.2'> 1.1.2.  Range

The service to connect are MySQL, MongoDB, Redis, and GlusterFS. Use MySQL and MongoDB for saving datas. Session is to provide Redis with a sample application. Use ClusterFS for image file management (Upload) that you use.

### <div id='1.1.3'>  1.1.3.  Restrictions

Some services (RabbitMQ, CUBRID) were unable to be connected because the current PHP buildpack v4.3.1 of Cloud Foundry supporting drivers did not exactly match the services of this business. An explaination on how to access the connection with MongoDB with DB Admin account because DB authentication procedure is not applied.
In actual use, you need to customize the PHP build pack and proceed with development according to the project environment.


### <div id='1.1.4'> 1.1.4.  Reference

-	PHP Buildpack :https://github.com/cloudfoundry/php-buildpack
-	XAMP Site :https://www.apachefriends.org/index.html

  
# <div id='2'> 2.  PHP Application Development guide
 
## <div id='2.1'> 2.1.  Outline

The development environment can be configured locally or managed by deploying it directly on an open platform, depending on the network configuration of the open platform. This section describes how to easily configure a development environment in a Windows environment and deploy it on an open platform.
 
## <div id='2.2'> 2.2.  Development Environment Configuration

To configure a PHP development environment, you need to install a Web server, PHP engine, and an extension, and there is a tool that configures it easily. In this guide, we will install and configure using XAMP.

The systems configured for this documentation are as follows:
-	OS: Windows 8.1 64bit
-	XAMP PHP 5.5.30
-	Mongo library :
-	Composer : 

PHP implements a REST/full server, and the screen (HTML) is provided by Apache's web server. HTML and PHP work separately.
 
### <div id='2.2.1'>  2.2.1.  Download PHP Sample Source

The location of the sample can be changed but can be found on the Open Platform homepage. Please check the appropriate GIT location and download the source with the command below. The GIT Client must be installed to use the command.

        $ git clone 

 
### <div id='2.2.2'> 2.2.2.  XAMP Installation

BOSH creates and manages VMs that create stemcells on AWS. To create a stemcell, an account must be created on AWS and an environment must be configured to create a stemcell.
  1.	Download page appears directly when accessing this URL (https://www.apachefriends.org/index.html). Select "Click here for other versions". 
    
  ![./images/php/php_develope_guide2.png](./images/php/php_develope_guide2.png)<br>
  First page of XAMP Official Mainpage
    
  Windows version of PHP 5.5.30 (32bit) was downloaded.
    
  2.	Run all the downloaded files and click Next for all. However, when directories are asked as below, you must change the location or remember the location. After the installation is complete, the environment variable (Path) should be placed in the PHP execution directory.
    
  ![./images/php/php_develope_guide3.png](./images/php/php_develope_guide3.png)<br>
    XAMP installation directory
    
  3.	If the installation is processed properly, it will look like the picture below. A message saying 'the program might get slow because of Antivirus program' just click "OK" at the first use.
    
  ![./images/php/php_develope_guide4.png](./images/php/php_develope_guide4.png)<br>
    XAMP Installation in progress
    
  4.	When the installation is complete, a message stating Control Panel is Ready. The settings are set as Default so just start and it will be executed as the Control Panel shown below.
    
  ![./images/php/php_develope_guide5.png](./images/php/php_develope_guide5.png)<br>
    XAMP Managing Pannel Screen
    
  Select the desired service and click "Start" to execute the selected service(Only Apache will be used in the example). However, the port used by the service(80 for Apache) should not be in use.
    
  5.	Select Apache (httpd. conf) from the Config of Apache and change the location of DocumentRootdhk Directory to where the development source is to directly connect to the location you develop when calling from the browser to http://localhost. The location of the development source is the same as the location set in 2.2.1.
        
        
      DocumentRoot C:\ development source path
      <DirectoryC:\development source path>
      
 
### <div id='2.2.3'> 2.2.3. PHP Executing Environment Setting

  1.	Puts PHP that was installed with XAMP into Environment Setting (Path) so that it can be run anywhere. Select Control Panel -> System -> Advanced System Setting to change system properties window as shown below. 

  ![./images/php/php_develope_guide5.png](./images/php/php_develope_guide6.png)<br>
  System Properties Window
  
  2.	Select "Environment Variable" and edit Path. At the end of the variable value, add the PHP directory under the XAMP installation directory. 

  ![./images/php/php_develope_guide5.png](./images/php/php_develope_guide7.png)<br>
    Path Environment Variable Setting

  3.	To check if it was configured normally, execute "cmd" and slect php-version. If it appears like below, the configuration was done successfully.
 
  ![./images/php/php_develope_guide5.png](./images/php/php_develope_guide8.png)<br>
    Check PHP Version from cmd

 
### <div id='2.2.4'> 2.2.4. Composer Installation

  Composer is a tool that manages library needed in development. The mainpage is as follows. https://getcomposer.org/
  
  1.	There is a way to download and install Composer and set it to Path. But we will be using Maunal. The composer.phar file is still used on the Open Platform, so composer.phar file was installed in the development location with manual installation. (If the source was downloaded from Git, there is no need for installation anymore.)
  
  2.	Manual Installation is simple. Just enter the source at the route directory like the example below. Settings from 2.2.3 Environment Variable should be done to run PHP command.            php r "readfile('https://getcomposer.org/installer');" | php
	      
  3.	Configure the necessary Package in composer.json. When installed, the packages that can be used at PHP will be installed below vendor directory. 
  
  * Cautions *
  The released extension of PHP buildpack must be checked first to be used at the Open Platform because it has differenct environment with XAMP. Be aware that it can run in the XAMP environment but not in Open Platfrom.
  PHP Buildpack Extenstion :https://github.com/cloudfoundry/php-buildpack/releases


 
### <div id='2.2.5'> 2.2.5.  Mongo Driver Installation

  Mongo drive installation is to install the the Mongo driver provided by the Open Platform. Releated documents are at http://docs.php.net/manual/en/mongo.installation.php#mongo.installation.windows. Install the library file and add informations in config file.
  
  1.	DLL must be downloaded first from the PECL mainpage(http://pecl.php.net/package/mongo). This guide selected 1.6.12 version. 
   
  Click DLL from the link above and download "5.5 Thread Safe (TS) x86". When the file is unziped, only php_mongo.dll file is required.
  
  ![./images/php/php_develope_guide5.png](./images/php/php_develope_guide7.png)
  
  2.	Copy the php_mongo.dll file under the ext of PHP at the installed XAMP directory.
  
  3.	Select php.ini from the PHP directory of XAMP Install directory and add as shown below.
    extension=php_mongo.dll
  
  4.	Restart the Apache server when the php.ini file has been set up. If there is an error, the XAMP panel displays the error in red, verify the Apache server comes up normally.
  
  5.	To verify if the module is installed successfully, run info.php at the root of the source code. Select http://localhost/info.php from the browser and if the setting information can be found at the mongo part of the content, it is installed normally.
  

 
## <div id='2.3'> 2.3.  Development

Describes the package configuration and source directory configuration required for development and explains the part that links with each service.
 
### <div id='2.3.1'> 2.3.1.  Use Package Description

Use Composer to manage Dependency. The package configuration of the Composer.json file is described in the table below. Setup information is stored in composer.json..


|Package Name |Version |Description |
|--------------------------|------|--------------------------------------------|
|slim/slim                 |2.*     |Used for REST/full Framewok of PHP. |
|videlalvaro/php-amqplib   |2.5.*   |Used for connecting with RabbitMQ Service. (Cannot be used due to the reason that SSL connection is not available at the moment)| 
|predis/predis             |1.0.*   |Used to connect with Redis Service. |
|rackspace/php-opencloud   |1.15.*  |Used to upload files to ClusterFS with Openstack connection SDK.. |
|guzzlehttp/guzzle         |6.*     |Used to change container authority from GlusterFS to  Http client. |
|phpunit/phpunit           |4.3.*   |This is a program for PHP unit test. When you run Vendor\bin\phpunit, it runs the test case in the test directory and displays the results on the screen.. |


  When you want to install libarary using Composer, run install as shown below. This command is automatically executed when deployed on an open platform, such as a PHP build pack. 
    phpcomposer.phar install
  
  If the composer.json file is changed in the local development environment, you can change the configuration of the package using update instead of install..


 
### <div id='2.3.2'> 2.3.2.  Directory Description

For convenience of development, the API service is configured as a separate directory, and the resource directory has static files (js, css, image) required by HTML.

|File/Folder  |Purpose  |
|-----------|------------------------------------------|
|.bp-config|	Where the extensions used in PHP buildpacks are defined.|
|api|	Where the source related to REST API are in. It is divided by each service.|
|resources|	There are static files (js, css, image, etc.) used by HTML.|
|Test|	Where the test case for PHP unit test are.|
|vendor|	Where Package installed with Composer is.|
|.cfiignore|	Define files/directories that make deployment exceptions when deployed on open platforms.|
|.htaccess|	This is where the pattern for url is defined for REST/full implementation.|
|composer.json, composer.phar, composer.lock|	A file that manages Dependency of the Package with Composer file.|
|Info.php|	Web page (including phpinfo) to check modules installed in PHP.|
|login.html|	Login page for example execution.|
|main.html|	This is the main page to show the organization chart of the example.|
|manage..html|	The manage page for managing the data in the example. This is the screen that will be shown after login.|
|manifest.yml|	This is a setup file that you use when you push onto an open platform.|
|phpunit.xml|	Settings that define PHP unit tests.|

 
### <div id='2.3.3'> 2.3.3.  Application Environment Setting

Environment Setting for REST/full service and Extension to be used at PHP buildpack must be applied. 

1.	Configure .htaccess for REST/full Service
Settings for handling REST/full API format /api/variables. Add .htaccess to the route directory and insert the following.

        <IfModulemod_rewrite.c>
        RewriteEngine On
        RewriteCond %{REQUEST_FILENAME} !-f
        RewriteCond %{REQUEST_FILENAME} !-d
        RewriteRule /(.*)$ /api/api.php?request=$1 [QSA,NC,L]
        </IfModule>
	
	
2.	Add Extenstion to PHP buildpack
Just like how you added a mongo drive in XAMP, you should also add a term extension library to an open platform. Available libraries can be found here.
To add, create the options.json file in the .bp-config directory and add it as follows.

        {
            "PHP_EXTENSIONS": ["mysqli", "mongo", "amqp"]
        }
    
 	mysqli :an extension for connecting with mysql
 	mongo : an extension for connecting with mongo
 	amqp :an extension for connecting with rabbitmq (Currently not available due to no connection with SSL)

  
### <div id='2.3.4'> 2.3.4.  VCAP_SERVICES Environment Setting Information 

Service creation/binding is performed on an open platform to use the service. Must obtain VCAP_SERVICES configuration information to use the associated services. This information contains all the information needed to access the service, including the Host address/port, username, and password to connect to. 

1.	Check the information of the connected service
Use CF cli to check the information connected with the service. Instead of putting this information directly into the source, bring VCAP information from the source and set it up initially.

        $ cfenvphp-sample
        
        {
         "VCAP_SERVICES": {
         "p-mysql": [
           {
            "credentials": {
             "hostname": "10.30.40.63",
             "jdbcUrl": "jdbc:mysql://10.30.40.63:3306/cf_ea68784e_3de6_439d_afc1_d51b4e95627b?user=ZwCFnQRiT3KANqHZ\u0026password=qs7oqi4nSvWq6UQa",
        "name": "cf_ea68784e_3de6_439d_afc1_d51b4e95627b",
        "password": "qs7oqi4nSvWq6UQa",
             "port": 3306,
             "uri": "mysql://ZwCFnQRiT3KANqHZ:qs7oqi4nSvWq6UQa@10.30.40.63:3306/cf_ea68784e_3de6_439d_afc1_d51b4e95627b?reconnect=true",
             "username": "ZwCFnQRiT3KANqHZ"
            },
            "label": "p-mysql",
            "name": "sample-mysql-instance",
            "plan": "100mb",
            "tags": [
             "mysql"
            ]
           }
          ],
        …..(Skip)…..


2.	Bring environment setting information from PHP
The way to bring VCAP environment settings information from PHP is simple. You can use the routine to get the env information of the system. The example below is to get the connection information for mysql service. 
(Location :api/mysql_view.php)

        if (array_key_exists("VCAP_SERVICES", $_ENV)) {
        // Check if the VCAP Services is in $_ENV (System environment setting) information.
        
               $env = json_decode($_ENV["VCAP_SERVICES"], $assoc=true);
               //Converts the contents of VCAP_SERVICES to JSON objects.
        
               $this->host = $env["p-mysql"][0]["credentials"]["hostname"].':'$env["p-mysql"][0]["credenti
        alls"]["port"];
               // Brings host location and port information.
               $this->username = $env["p-mysql"][0]["credentials"]["username"];
               // Brings user information.
               $this->password = $env["p-mysql"][0]["credentials"]["password"];
               // Brings user password information.
               $this->dbname = $env["p-mysql"][0]["credentials"]["name"];
               // Brings users DB name.

Environment setting information varies from service to service. Use the cfenv command to pinpoint the exact location or refer to the service provider's manual.

 
### <div id='2.3.5'> 2.3.5.  Connect Mysql

Use the mysqli added in Extenstion. It is set as default in XAMP, so there is no need for installation. 
(위치 :api/mysql_view.php)


1.	Access to Mysql
2.	
        $conn = new mysqli($this->host, $this->username, $this->password, $this->dbname);
        
        if($conn->connect_error) {
            die("conncetion failed:".$conn->connect_error);            
        }

Use mysqli to access to mysql service. Access through the host, username, password, and db name brought from the environment setting.

2.	Send Query and get results
Create a Query and run it as a Prepared Statement. Receive the executed result value and make it into the desired type of array. When all processing is complete, close the connection and statement.

        $sql = "SELECT * FROM ORG_TBL";
        $stmt = $conn->prepare($sql);
        $stmt->execute();
        $org_result = $stmt->get_result();
        
        if ($org_result->num_rows> 0) {
        while($row = $org_result->fetch_assoc()) {
        
        $org = array(
                   "id" =>strval($row["id"]),
                   "label" => $row["label"],
                   "desc" => $row["desc"],
                   "url" => $row["url"],
                   "created" => $row["created"],
                   "modified" => $row["modified"]
            );
        
        array_push($result["orgs"], $org);
        }
         $result["org"] = $org;
        }
        
        $stmt->close();
        $conn->close();

3.	Convert the result value to Json and let HTML do the processing.

        echo json_encode($result);

 
### <div id='2.3.6'> 2.3.6.  Connect CUBRID

Current CF's default build pack does not support CUBRID, so it was not implemented in this sample. If you need to use CUBRID in the project, please contact us separately. 

 
### <div id='2.3.7'> 2.3.7.  Connect MongoDB

Use the mongo library added in Extenstion. However, there is a problem with user authentication with the current mongo library. the library's bug should be fixed. In this guide, we inevitably accessed MongoDB's Root account and implemented the example.
(위치 :api/mongodb_view.php)

1.	Access to Mongodbl. Use the uri information brought from environment settings.
2.	
        $mongo = new MongoClient($this->uri);

2.	Set Collection and request information from the selected Collection. Use the Find command to request the required information. Place the received data to the $cursor, and change the data form to the wanted form.

        $collection = $mongo->selectCollection($this->dbname, 'ORG_TBL');
        $cursor = $collection->find(array('_id'=>new MongoId($org_id)));
        
        foreach ($cursor as $row) {
        $org = array(
                        "id" =>strval($row["_id"]),
                        "label" =>isset($row["label"]) ? $row["label"] : "",
                        "desc" =>isset($row["desc"]) ? $row["desc"] : "",
                        "url" =>isset($row["url"]) ? $row["url"] : "",
                        "created" =>isset($row["created"]) ? $row["created"] : "",
                        "modified" =>isset($row["modified"]) ? $row["modified"] : ""
            );
            $result["org"] = $org;            
        }

3.	Convert the result value to Json and let HTML do the processing.
	
        echo json_encode($result);

 
### <div id='2.3.8'> 2.3.8.  Connect Redis

The Redis integration uses additional packages installed through Composer. 

1.	To use Redis, Predis' Class announce register.

        Predis\Autoloader::register();

2.	Access to Redis. Use Host, Port, Password received from the environment settings to access to Redis.

        $redis = new Predis\Client(
        array(
             "scheme" => "tcp",
             "host" => $this->host,
             "port" => $this->port,
             "password" => $this->password
        ));

3.	Save Session key and User ID(username) in Redis.

        $redis->set($key, $username);



 
### <div id='2.3.9'> 2.3.9.  Connect RabbitMQ

There is a problem with SSL connection when connecting amqp from PHP build pack in CF. Therefore, the service integration is not implemented. There are only php files that specify how to connect. (Loacation :api/rebbitmq_view.php)

 
### <div id='2.3.10'> 2.3.10.  Connect GlusterFS


We use a package called php-opencloud and install it through the composer. However, there is no SDK that creates the Container Public, so the API is called directly (in REST format) and permissions are set to Public. 

1.	Connect with GlusterFS (File Upload)
On an open platform, Object Storage is used with ClusterFS, and it is configured to use Openstack's Swift to use Object Storage through the API.
php-opencloud is an SDK provided by the rackspace company that created the swift.  
Announce to use Opencloud.

        use OpenCloud\Rackspace;
        
2.	Check if the access to Openstack(Object Storage) is well.

        $client = new OpenCloud\OpenStack($this->host, array(
               "username" => $this->username,
                  "password" => $this->password,
                  "tenantName" => $this->tenantName,
        ));
        $client->authenticate();

3.	Set the Container to upload the file. If there is no selected Container(directory), Create new Container. Set the created Container as Public(Read Authority) to make everyone be able to read without authenticating.
        
        $service = $client->objectStoreService($this->catalogName, 'RegionOne', 'publicURL');
        
        $container;
        // Bring Container
        try {            
        $container = $service->getContainer($this->containerName
        } catch (Exception $e) {
        // Create
        $container = $service->createContainer($this->containerName);
        
        // Modify the created Container to Public mode
        // The PHP-OpenCloud SDK does not support this part. Set up by calling the API directly
        $baseUrl = $container->getService()->getEndpoint()->getPublicUrl().'/'.$this->containerName;
        $httpClient = new GuzzleHttp\Client();
        $res = $httpClient->request('POST', $baseUrl, 
                ['headers' => ['X-Auth-Token' => $container->getService()->getClient()->getToken(), 'X-Container-Read' => '.r:*']]
              );        
        
        // Response Code crossed to 204. Success!
        }

4.	Upload the file to selected Container. Since the container is set to Public, it is readable anywhere with the URL of the image. 
        
        $fileName = time().'_'.$fileName;
        // Save File
        $result = $container->uploadObject($fileName, fopen($file, 'r+'), array('name'=> $fileName, 'Content-Type' => 'image/jpeg'));

5.	Make the saved result in to URL form. Can access to the image directly with the URL.
        
        $result = array('thumb_img_path' => $container->getService()->getEndpoint()->getPublicUrl().'/'.$this->containerName.'/'.$fileName);

6.	Convert the result value to Json and let HTML do the processing.
        
        echo json_encode($result);



## <div id='2.4'>  2.4.  Deployment 

This is for installing sample applications on development platforms. Describes the proactive operations for using CF PUSH command and the operations for creating and connecting services.

1)	Create ./manifest.yml
-	When cf push command is used, the manifest.yml of the current directory will be refered when processing deployment.
	
        ---
        applications:
        - name:php-sample-app# Application Name
          memory: 128M# Application Memory Size
          instances: 1# Application's Number of Instances
        path: .
        buildpack: https://github.com/cloudfoundry/php-buildpack.git# Announce the buildpack to use

※When staging an application, the assigned port is registered as an environment variable. This port is also used to check the status of the application, so it is recommended to designate the port as above.

2)	Open Platform Login

        $ cfapi https://api.cf.open-paas.com# Set TARGET to Open Platform
        #cfapi [target url]
        
        $ cf login -u testUser -o sample_test -s sample_space# Request Login
         #cf login -u [user name] -o [org name] -s [space name]
        API endpoint: https://api.cf.open-paas.com
        
        Password>
        Authenticating...
        OK
        
        Targeted org sample_test
        
        Targeted space sample_space
        
        API endpoint:   https://api.cf.open-paas.com (API version: 2.29.0)   
        User:           testUser
        Org:            sample_test
        Space:          sample_space


3)	Create Open Platform Service

        $ cf marketplace# Request marketplace list

        service         plans                   description
        p-mysql	100mb, 1gb		MySQL databases on demand   
        p-rabbitmqstandard		RabbitMQ is a robust …..
        redis-sb	shared-vm, dedicated-vm	Redis service to provide a ……

        $ cf create-service p-mysql 100mb sample-mysql-instance# Create Service
        #cf create-service [service] [plan] [service name]
        
        $ cf services# Lookup Service list
        
        nameservice      plan              bound apps		last…
        sample-mysql-instance       p-mysql100mb            node-sample, p....	…
        sample-rabbitmq-instance    p-rabbitmq   standard           python-sample-....	…
        sample-redis-instanceredis-sbshared-vmpython-sample-....	…
        
        $ cf bind-servicephp-sample-app sample-mysql-instance# Bind Application Service
         #cf bind-service [app name] [service name]
        
        $ cf start php-sample-app# Start Application
        #cf start [app name]


4)	Start application and Service binding at Oprn Platform application
        $ cf push --no-start
        # Uploads application without running it.
        
        $ cf services# Check Service List
        
        nameservice      plan              bound apps		last…
        sample-mysql-instance       p-mysql100mb            node-sample, p....	…
        sample-cubrid-instanceCubridDButf8              node-sample, p....	…
        sample-mongo-instanceMongo-DB   default-plan       node-sample, p....	…
        sample-rabbitmq-instance    p-rabbitmq   standard           python-sample-....	…
        sample-redis-instanceredis-sbshared-vmpython-sample-....	…
        sample-glusterfs-instanceglusterfsglusterfs-1000Mb   glusterfs-samp....	…
        
        $ cf bind-service php-sample-app sample-mysql-instance# Bind Applicaiton Service
        
        $ cf start php-sample-app# Start Application


5)	Create Mysql and Cubrid Table
-	A table should be created in the DB for the organization management function of the Sample App.
-	Refer to 'Client Tool Connection' in the OpenPaaS Mysql, Cubrid Service Pack Installation Guide on how to add tables to Mysql and Cubrid.
-	Use Client tool to create the table below and run sql for each. (Both Mysql and Cubrid can be created with the same sql.)
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



## <div id='2.5'>  2.5.  Test


Use phpunit to do PHP unit test. The test case is in the test directory, and the unit test runs as below.

        Vendor\bin\phpunit



### [Index](https://github.com/PaaS-TA/Guide-eng/blob/master/README.md) > [AP User Guide](../README.md) > PHP Development
