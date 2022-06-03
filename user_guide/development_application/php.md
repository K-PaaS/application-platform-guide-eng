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

개방형 플랫폼에서는 서비스를 사용하기 위해 서비스 생성/바인딩을 합니다. 그리고 연결된 서비스를 사용하기 위해 VCAP_SERVICES 환경설정 정보를 가져와야 합니다. 이 정보는 연결할 Host 주소/포트, 사용자명, 비밀번호 등 서비스 접속에 필요한 모든 정보를 포함하고 있습니다. 

1.	연결된 서비스 정보 확인하기
CF cli를 통해서 서비스와 연결된 정보를 확인합니다. 이 정보를 직접소스에 넣는 것이 아니라 소스에서 VCAP 정보를 가져와서 초기에 설정정보를 셋팅해야합니다.

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
        …..(이하 생략)…..


2.	PHP에서 환경설정 정보 가져오기
PHP에서 VCAP 환경정보를 가져오는 방법은 간단합니다. System의 env 정보를 가져오는 루틴을 사용하면 됩니다. 아래의 예시는 mysql 서비스의 Conncetion 정보를 가져오는 부분입니다. 
(위치 :api/mysql_view.php)

        if (array_key_exists("VCAP_SERVICES", $_ENV)) {
        // $_ENV (시스템 환경설정) 정보에서 VCAP Services가 있는지를 체크합니다.
        
               $env = json_decode($_ENV["VCAP_SERVICES"], $assoc=true);
               //VCAP_SERVICES의 내용을 JSON 객체로 컨버젼합니다.
        
               $this->host = $env["p-mysql"][0]["credentials"]["hostname"].':'$env["p-mysql"][0]["credenti
        alls"]["port"];
               // host위치정보와 port정보를 가져옵니다.
               $this->username = $env["p-mysql"][0]["credentials"]["username"];
               // 사용자정보를 가져옵니다.
               $this->password = $env["p-mysql"][0]["credentials"]["password"];
               // 사용자 비밀번호 정보를 가져옵니다.
               $this->dbname = $env["p-mysql"][0]["credentials"]["name"];
               // 사용자의 DB 명을 가져옵니다.

환경설정정보는 서비스마다 위치/명칭이 다릅니다. cfenv 명령으로 정확한 위치를 파악하거나 서비스 제공자의 매뉴얼을 참조하여야 합니다.

 
### <div id='2.3.5'> 2.3.5.  Mysql 연동

Extenstion에 추가한 mysqli를 사용합니다. XAMP에서는 기본으로 설정이 되어 있기 때문에 따로 설치작업이 필요없습니다. 
(위치 :api/mysql_view.php)


1.	Mysql 에 접속하기
2.	
        $conn = new mysqli($this->host, $this->username, $this->password, $this->dbname);
        
        if($conn->connect_error) {
            die("conncetion failed:".$conn->connect_error);            
        }

mysqli를 이용하여 mysql 서비스에 접속합니다. 환경설정에서 가져온 host, username, password와 db명을 이용하여 접속을 합니다.

2.	Query보내고 결과값 받기
Query를 작성하고 Prepared Statement로 실행을 합니다. 실행된 결과값을 받아서 원하는 형태의 Array로 만들어 줍니다. 모든 처리가 완료되면 close로 connection과 statement를 종료합니다.

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

3.	결과값을  Json으로컨버전하여 HTML에서 처리할 수 있도록 합니다.

        echo json_encode($result);

 
### <div id='2.3.6'> 2.3.6.  CUBRID 연동

현재 CF의 기본 빌드팩에서는 CUBRID를 지원하지 않아 본 샘플에서는 구현하지 않았습니다. 만약 프로젝트에서 CUBRID를 사용해야하면 별도로 문의 바랍니다. 

 
### <div id='2.3.7'> 2.3.7.  MongoDB 연동

Extenstion에 추가한 mongo 라이브러리를 이용합니다. 단 현재 mongo 라이브러리로는 사용자 인증에 문제가 있습니다. 라이브러리가 버그 fix가 되어야 합니다. 본 가이드에서는 부득이하게 MongoDB의 Root계정으로 접속하여 예제를 구현하였습니다.
(위치 :api/mongodb_view.php)

1.	Mongodbl 에 접속합니다. 환경설정에서 받아온 uri 정보를 이용합니다.
2.	
        $mongo = new MongoClient($this->uri);

2.	Collection을 설정하고 해당 Collection에서 정보를 요청합니다. Find 명령을 이용하여 필요한 정보를 요청합니다. 받아온 결과는 $cursor에 넣고 원하는 데이터 형태로 변경합니다.

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

3.	결과값을  Json으로컨버전하여 HTML에서 처리할 수 있도록 합니다.
	
        echo json_encode($result);

 
### <div id='2.3.8'> 2.3.8.  Redis 연동

Redis 연동은 추가로 Composer를 통해 설치가된 패키지를 사용합니다. 

1.	Redis 사용을 위해서 Predis의 Class에서 register를 선언합니다.

        Predis\Autoloader::register();

2.	Redis에  접속을 합니다. 환경설정에서 받은 Host, Port, Passworf를 이용하여 Redis에 접속을 합니다.

        $redis = new Predis\Client(
        array(
             "scheme" => "tcp",
             "host" => $this->host,
             "port" => $this->port,
             "password" => $this->password
        ));

3.	Session key와 사용자 ID(username)을 Redis에 저장합니다.

        $redis->set($key, $username);



 
### <div id='2.3.9'> 2.3.9.  RabbitMQ연동

CF의 PHP 빌드팩에서amqp접속시 SSL 접속에 문제가 있습니다. 그래서 해당 서비스 연동은 구현이 안되어 있습니다. 접속방법만 명시한 php 파일만 있습니다. (위치 :api/rebbitmq_view.php)

 
### <div id='2.3.10'> 2.3.10.  GlusterFS 연동


php-opencloud라는 패키지를 사용하며 composer를 통해서 설치가 되게 되어 있습니다. 단 Container를 Public하게 생성하는 SDK가 없어서 API를 직접 호출(REST형식)하여 권한을 Public으로 설정하고 있습니다. 

1.	GlusterFS와 연동하기 (파일 Upload)
개방형 플랫폼에서는 Object Storage를 GlusterFS를 사용하는데 Object Storage를 API를 통해 사용하기 위해 Openstack의 Swift를 이용하여 서비스를 할 수 있게 구성되어 있습니다.
php-opencloud는 swif를 만든 rackspace 회사에서 제공하는 SDK입니다. 
Opencloud를 사용하기 위해 선언을 합니다.

        use OpenCloud\Rackspace;
        
2.	Openstack(Object Storage)에 접속을 하고 잘 접속이 되었는지 체크합니다.

        $client = new OpenCloud\OpenStack($this->host, array(
               "username" => $this->username,
                  "password" => $this->password,
                  "tenantName" => $this->tenantName,
        ));
        $client->authenticate();

3.	파일을 올리기 위한 Container를 설정합니다. 만약에 해당 Container(directory)가 없으면 Container를 새로 생성을 합니다. 그리고 생성된 Container는 Public(읽기권한)으로 설정하여 인증없이 모든 사람이 읽을 수 있게 합니다.
        
        $service = $client->objectStoreService($this->catalogName, 'RegionOne', 'publicURL');
        
        $container;
        // Container를 가져오기
        try {            
        $container = $service->getContainer($this->containerName
        } catch (Exception $e) {
        // 생성하기
        $container = $service->createContainer($this->containerName);
        
        // 만들어진 Container를 Public 모드로 변경하기
        // PHP-OpenCloud SDK에서 해당 부분을 지원하지 않아서 직접 API를 호출하여 설정함
        $baseUrl = $container->getService()->getEndpoint()->getPublicUrl().'/'.$this->containerName;
        $httpClient = new GuzzleHttp\Client();
        $res = $httpClient->request('POST', $baseUrl, 
                ['headers' => ['X-Auth-Token' => $container->getService()->getClient()->getToken(), 'X-Container-Read' => '.r:*']]
              );        
        
        // Response Code가 204로 넘어옴. 성공
        }

4.	파일을 해당 컨테이너에 Upload합니다. Container를 Public으로 설정하였기 때문에 이미지의 URL만 있으면 어디서든 읽어올 수 있습니다. 
        
        $fileName = time().'_'.$fileName;
        // 파일 저장
        $result = $container->uploadObject($fileName, fopen($file, 'r+'), array('name'=> $fileName, 'Content-Type' => 'image/jpeg'));

5.	저장된 결과를 URL 형태로 만듭니다. 이 URL로 직접 접속하여 해당 이미지에 접근이 가능합니다.
        
        $result = array('thumb_img_path' => $container->getService()->getEndpoint()->getPublicUrl().'/'.$this->containerName.'/'.$fileName);

6.	결과값을  Json으로컨버전하여 HTML에서 처리할 수 있도록 합니다.
        
        echo json_encode($result);



## <div id='2.4'>  2.4.  배포 

개발형 플랫폼에 샘플 애플리케이션을 설치하기 위한 부분입니다. CF PUSH 명령문을 사용하기 위한 사전작업과 서비스를 생성하고 연결하는 작업을 설명하고 있습니다.

1)	./manifest.yml 생성
-	cf push 명령시 현재 디렉토리의manifest.yml을 참조하여 배포가 진행된다.
	
        ---
        applications:
        - name:php-sample-app# 애플리케이션 이름
          memory: 128M# 애플리케이션 메모리 사이즈
          instances: 1# 애플리케이션 인스턴스 개수
        path: .
        buildpack: https://github.com/cloudfoundry/php-buildpack.git# 사용할 빌드팩을 선언

※애플리케이션 스테이징시할달 받은 포트가 환경변수로 등록되어있다. 이 포트는 애플리케이션의 상태 체크에도 사용되므로 위와 같이 포트를 지정할 것을 권장한다.

2)	개방형 플랫폼 로그인

        $ cfapi https://api.cf.open-paas.com# 개방형 플랫폼 TARGET 지정
        #cfapi [target url]
        
        $ cf login -u testUser -o sample_test -s sample_space# 로그인 요청
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


3)	개방형 플랫폼 서비스 생성

        $ cf marketplace# 마켓플레이스 목록 요청

        service         plans                   description
        p-mysql	100mb, 1gb		MySQL databases on demand   
        p-rabbitmqstandard		RabbitMQ is a robust …..
        redis-sb	shared-vm, dedicated-vm	Redis service to provide a ……

        $ cf create-service p-mysql 100mb sample-mysql-instance# 서비스 생성
        #cf create-service [service] [plan] [service name]
        
        $ cf services# 서비스 목록 조회
        
        nameservice      plan              bound apps		last…
        sample-mysql-instance       p-mysql100mb            node-sample, p....	…
        sample-rabbitmq-instance    p-rabbitmq   standard           python-sample-....	…
        sample-redis-instanceredis-sbshared-vmpython-sample-....	…
        
        $ cf bind-servicephp-sample-app sample-mysql-instance# 애플리케이션 서비스 바인딩
         #cf bind-service [app name] [service name]
        
        $ cf start php-sample-app# 애플리케이션 시작
        #cf start [app name]


4)	개방형 플랫폼 애플리케이션에 서비스 바인딩 및 애플리케이션 시작
        $ cf push --no-start
        # 애플리케이션 업로드만 실행하고 시작하지는 않는다.
        
        $ cf services# 서비스 목록 조회
        
        nameservice      plan              bound apps		last…
        sample-mysql-instance       p-mysql100mb            node-sample, p....	…
        sample-cubrid-instanceCubridDButf8              node-sample, p....	…
        sample-mongo-instanceMongo-DB   default-plan       node-sample, p....	…
        sample-rabbitmq-instance    p-rabbitmq   standard           python-sample-....	…
        sample-redis-instanceredis-sbshared-vmpython-sample-....	…
        sample-glusterfs-instanceglusterfsglusterfs-1000Mb   glusterfs-samp....	…
        
        $ cf bind-service php-sample-app sample-mysql-instance# 애플리케이션 서비스 바인딩
        
        $ cf start php-sample-app# 애플리케이션 시작


5)	Mysql, Cubrid 테이블 생성
-	Sample App의 조직관리 기능을 위해 DB에 테이블을 생성해 주어야 한다.
-	Mysql과 Cubrid에 테이블을 추가하는 방법은 OpenPaaS Mysql, Cubrid 서비스팩 설치 가이드의 'Client 툴 접속'을 참고한다.
-	Client 툴을 이용하여 아래의 테이블 생성 sql를 각각 실행한다. (Mysql과 Cubrid 양쪽다 동일한 sql로 생성가능하다.)
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



## <div id='2.5'>  2.5.  테스트


PHP 단위테스트는 phpunit를 이용해서 합니다. 테스트 케이스는 test 디렉토리에 있으며 단위 테스트 실행은 아래와 같이 실행합니다.

        Vendor\bin\phpunit



### [Index](https://github.com/PaaS-TA/Guide-eng/blob/master/README.md) > [AP User Guide](../README.md) > PHP 개발
