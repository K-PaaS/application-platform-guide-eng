### [Index](https://github.com/K-PaaS/Guide-eng/blob/master/README.md) > [AP User Guide](../README.md) > Servicepack Development


## Table of Contents
1. [Outline](#1)	
	* [1.1. Document Outline](#2)	
	* [1.1.1. Purpose](#3)
	* [1.1.2. Range](#4)
	* [1.1.3. References](#5)	
2. [Service Broker API Development Guide](#6)	
	* [2.1. Outline](#7)	
	* [2.2.	Service Architecture](#8)
	* [2.3.	Service Broker API Architecture](#9)	
	* [2.4.	Pivotal(Cloud Foundry) Marketplace Model](#10)	
	* [2.5.	Development Guide](#11)	
	* [2.5.1. Catalog API Guide](#12)	
	* [2.5.2. Provision API Guide](#13)	
	* [2.5.3. Update Instance API Guide](#14)	
	* [2.5.4. Deprovision API Guide](#15)	
	* [2.5.5. Bind API Guide](#16)	
	* [2.5.6. Unbind API Guide](#17)	
3. [Service release Development Guide](#18)	
	* [3.1.	Outline](#19)	
	* [3.2.	Bosh Architecture](#20)	
	* [3.3.	Release Directory Configuration](#21)	
	* [3.3.1. packages](#22)	
	* [3.3.2. jobs](#23)
	* [3.3.3. src](#24)	
	* [3.3.4. shared](#25)	
	* [3.3.5. releases](#26)	
	* [3.3.6. config](#27)	
	* [3.3.7. final_builds](#28)	
	* [3.3.8. deployments](#29)	
	* [3.3.9. content_migrations](#30)	
	* [3.4.	Development Guide](#31)	
	* [3.4.1. packages guide](#32)	
	* [3.4.1.1. packaging](#33)
	* [3.4.1.2. pre_packaging](#34)	
	* [3.4.1.3. spec](#35)	
	* [3.4.2. jobs guide](#36)	
	* [3.4.2.1. templates](#37)	
	* [3.4.2.2. monit](#38)	
	* [3.4.2.3. spec](#39)	
4. [Deployment Guide](#40)	
5. [Deploy Guide](#41)	

### <a name="1"/>1. Outline
#### <a name="2"/>1.1. Document Outline
##### <a name="3"/>1.1.1. Purpose

This document (servicepack guide) is presented as a guide to define various standards and measures to be applied to achieve quality goals in the development and operation of service packs for Open PaaS projects based on the e-government standard framework.
The purpose of this development standard guide is to improve the quality of the development system, to enhance readability and understanding of source code among service pack developers, and support smooth system maintenance after completion of the project.

##### <a name="4"/>1.1.2. Range
The range of this document is limited to the development of service packs for Open PaaS projects based on the e-government standard framework.

##### <a name="5"/>1.1.3. References
http://docs.cloudfoundry.org/services/  
http://bosh.io/docs/create-release.html  
https://github.com/cloudfoundry/bosh-sample-release  
https://github.com/cloudfoundry/cf-mysql-release/  
https://github.com/cloudfoundry-community/cf-mysql-java-broker-Bosh release
https://github.com/cloudfoundry-community/cf-mysql-java-broker  
http://rubykr.github.io/rails_guides/getting_started.html  
http://www.appdirect.com  
 
### <a name="6"/>2. Service Broker API Development Guide
#### <a name="7"/>2.1. Outline
The open cloud platform Service API defines the protocol between the Cloud Controller and the Service Broker. Broker is implemented in HTTP (or HTTPS) endpoints URI format. One or more services may be provided by one broker and may be provided horizontally and extensively to enable load balancing.

#### <a name="8"/>2.2. Service Architecture
![openpaas-servicepack-01]  
[picture reference]: http://docs.cloudfoundry.org/services/overview.html

Services are used in open cloud platforms by implementing a cloud controller client API called the Service Broker API. The Services API is a version of the independent cloud controller API.
This makes external applications available on the platform. (database, message queue, rest endpoint, etc)

#### <a name="9"/>2.3. Service Broker API Architecture
![openpaas-servicepack-02]  
[picture reference]: http://docs.cloudfoundry.org/services/api.html

The Open Cloud Platform Service API is a protocol between Cloud Controller and Service Broker (catalog, provision, deprovision, update provision plan, bind, unbound), and the Service Broker implements it as a RESTful API and registers it with the Cloud Controller.

#### <a name="10"/>2.4. Pivotal(Cloud Foundry) Marketplace Model
![openpaas-servicepack-03]  
[picture reference]: http://www.slideshare.net/platformcf/cloud-foundry-marketplacepowered-by-appdirect

AppDirect: a leading company in cloud service marketplace and management solution.The established marketplace of many global companies. (Samsung, Cloud Foundry, ETC)
AppDirect offers Cloud Foundry service brokerages and additional services. 

Describe Service Provider and Cloud Foundry integration
![openpaas-servicepack-04]  
[picture reference]: http://www.slideshare.net/platformcf/cloud-foundry-marketplacepowered-by-appdirect

#### <a name="11"/>2.5. Development Guide
The method of implementing the service is up to the provider and developer. Open cloud platforms require service providers to implement 6 Service Broker APIs. This 2.4 Pivotal Marketplace Model can be used to consult with the service provider provided by AppDirect and provide it using AppDirect's intermediary function. The broker can be implemented as a separate application or by adding HTTP endpoints required for existing services.

This development guide guides how the service back-end is controlled by the service broker. If you use AppDirect, refer to http://go.appdirect.com/request-more-information.

Service Broker requires 6 basic API functions. (refer to the API guide of each for detailed descriptions)
![1](https://user-images.githubusercontent.com/104418463/200229978-2283d8ea-3d54-40d1-a8e9-aa9435b8431b.png)


Two major versions of the Service Broker API currently support open cloud platforms v1 and v2. Since v1 is not used and can be removed in the next version of the open cloud platform, Service Broker recommends implementing it as v2.
- Version Information (This guide is written based on version 2.5)
![2](https://user-images.githubusercontent.com/104418463/200230018-0b2052b7-70ec-421f-b276-1b76f115d7db.png)


- Certification

The Cloud Controller authenticates with the broker using HTTP default authentication (authentication header) for all requests and rejects all broker registrations that do not include user names and passwords. The broker checks the username and password and returns a 401 Unauthorized message if the credentials are invalid. If additional security is required on the Cloud Controller, SSL is used to support access to the broker.

##### <a name="12"/>2.5.1. Catalog API Guide
The service catalog retrieves information about the service and the service plan. The Cloud Controller initially retrieves endpoints from all brokers and checks the user-facing service catalog stored in the Cloud Controller database. The Cloud Controller also updates the catalog whenever the broker is updated. When the Catalog API is implemented, a service broker can be registered through the CF CLI.

1. Request

1.1. Route

	GET /v2/catalog

1.2. cURL

	curl -H "X-Broker-API-Version: 2.4" http://username:password@broker-url/v2/catalog
	Ex)
	curl -H "X-Broker-API-Version: 2.4" http://admin:eaa139af583c@10.30.40.61/v2/catalog

2. Response

2.1. Status Code

![3](https://user-images.githubusercontent.com/104418463/200230048-a02e42af-62f7-4348-b4ec-2882c4676a4c.png)

2.2. Body (* means required)
![4](https://user-images.githubusercontent.com/104418463/200230054-e54a68a0-bd36-40ab-817b-e0bead665296.png)

2.3. Service Metadata
![5](https://user-images.githubusercontent.com/104418463/200230088-d1e1f564-5403-435e-8fc2-b13f65e9e07f.png)

2.4.	Plan Metadata
![6](https://user-images.githubusercontent.com/104418463/200230113-54db4a2b-29be-41b9-80a5-716664bbb446.png)

2.5.	Quota Plan
![7](https://user-images.githubusercontent.com/104418463/200230149-83b66edf-2d9e-41f8-b0f0-5db580ebca30.png)


◎ sample body response message
	
	{
	  "services": [
	    {
	      "id": "44b26033-1f54-4087-b7bc-da9652c2a539",
	      "name": "p-mysql",
	      "description": "A MySQL service for application development and testing",
	      "tags": [
	        "mysql"
	      ],
	      "metadata": {
	        "displayName": "MySQL for Pivotal CF",
	        "imageUrl": "data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAEAAAABACAYAAACqaXHeAAAACXBIWXMAAAsTAAALEwEAmpwYAAAAGXRFWHRTb2Z0d2FyZQBBZG9iZSBJbWFnZVJlYWR5ccllPAAABoxJREFUeNrkW01sG0UUnkQ90FNNOfVHilvKBQS2iASHVtiCHkAqxI3ECZG4KOJGnOZQxCVxeuHnEJz0BlVjF3FCyg9UooeKOogeqBTkOEov/DlS056a2hfCASm8b3izXY/XZr07Xq/hSSs7jr2775v3vu+92Zm+vb098X+2fR2/wAeZCL0k6YjzEeHXA9pXa3SU+H2R35f++nCu0sn76+tEBJDTcDDNjsd8nm6LjmU68gRGKdQAkONwesKA063AyDEY1dAAwI5n6RgIKHVrDETOLxC+ACDHEeL5AB13AiJNICwHCgATG0Y8ExIyX2Egqh0HgAku38E89xMNKQKh2M6P+tt0PsUSFTbnBcvqTeYj8wDwiZcc9DtstkD3mjWaAuz8Qo8VeXOUDhO+AeCwX+rRSvccgZD3DAATXrEHwr6VnW0lk00BYKkrdVHjTapDvFlP0aoZygXhfOzQETH0zHMi+vhBecAqD3fExRvfyldD6pDnvsRdBHCFd9PE1SOP7Rexw0esv1d/+8V6f+XNt8TQ08+KlTsbYv3etijdvyu/j8+rf+6KJz+eMYn1eYqCnFsAKn5GHyM5dfo1kTh+QgxEDorvf//H6QH6HH/Dyve35evLn12SzupRsTZ+Qbzy+aU6wAykQlSvFvc5OO+5qcHowfHRwRdEYe22GL56Wayzo05R0cy5GgNi0HmVCjlu050jgImv4oX14dR3774nR/Odr770lb+Zk0kJEM7TATtmJ0S9Ekx5lbzFkTHK4W0Z0n7Jq/LwgUwfRYqGbaJVKZz1csbRwRdlfk9+s2imtSNSXNnckDyAcxu2NEd6PQBc9HjKfTD5xRvXG8jMj01eWxTDX1wWs2fOSlVAihnkgpRTBKS9nvENSNlm2fed6WEPEoQUxkkVwC8GQXAEIOXnjCZGHw6PUMjPnhm2gMB5wSuw2deHTQEwVAcAhX/Ua/hj1Mqa1GGk4AT0vF1DBTh/qygW3x6TkmoHAdcyxQlc7FkRkPQjWUVNrzFS4ycTYoFy15sK7Ijn5z+RUXCFz6HkFZxgKBXqAIh7LXowKhi1OgIjNUD1d86njqs6QIGAFEFhZSgKvAEQY0L69f1pOUKDNFJ6/quQdaoCvYIApZGzHD8UxfiphAkAonYAIq5D/lRSlKhxgeN+Kz7XkkgRheuqGgHRZ6BIGrAD4HqSExdeuVMOxHF7RM1QmilSRSokjj9lhgzb/QEI71Ni+Llbq7JkdWtod5tJJXhEOrq72zRt4LQCACW3F4VxUoK2AQDh1ehGkZOR/e7ZGDeMokYHAXKZJADwuWqXIasAuLD2Yz2IDM76vbtWSgQeAZKISKdxtGNg8nG6aV0x4tT1naey1976AtxpUpgMSanTfIHJkrs/qDwGe8MhnbzAJfFDRxuaIdQBCHUoTictMAAQvpgkQYWnA3CgSSpBZfC/DnSE1uUVAFtBgIAOT84dEAiqmkNEgFOac851qwYwbZgYUQBUgooE5DTIcy1zwZoU1cmunv1/tlTikRQ/IRXDwByhRYIAIBEEAKpKhOOxw0cdSa4hRbTaH2pR8l9lluwA4I9REaDJWR86/q3TdBppyCZI1SQARREim2IJxMhvVXfE1Z9u1/3/pWMn5IyzMQCw+oqqopoIwTNAtNeYVkchNE+jrKeHmn8wUAss64XQctBp4GTo9CB/hp8JNKiyekDSryMSBmvF8OgpQII+a4N8QyHEj5Br3XZeFksjY9JBXf6UiiA9pk6/6jv8nSrBfLcBQK+A/h8yia7zwfRHDaMNbkAh5QSQGwGyPxnSmyE8O+v60je7RMJJVI7V3T+szxAFPkgw17QXYGQKYZJEkOHktSU50arKZ/kkitrmrfYnZVb1ZXROzVA2DFxQFxGbZek85iHhPPL/a4oGD7NSWf2DZusD8MXpMIGg1hzIKbnNjbbnIxDZNPppVwAwCKiUwrgg0mvjE3VaSttqPiAdtlTwYalm64j7W/TKiICJ/4DzM63WD7tZKBkKafRaVznlvdsUUJEwETZpdFvvu4lgV3OCjGKhx5xPutk/4HpStIdAKLh13hUHOHACgAjrynEQXradH3jdMhPnjios64g97x3y9FyAJRIgzIUk5KNeN0753jbHy2vQRicCdnwVtX27e4SMA2ADIsnV42gvOG4cAC0iUgyGqV5CbZ/Nmd5L3NfJ3eMMRlI82jjtNk2g4+AZjHKxkxuo+7qxfV4tUXOwkqk9wW7tbwEGAJwbJQSR3aMDAAAAAElFTkSuQmCC",
	        "longDescription": "Provisioning a service instance creates a MySQL database. Binding applications to the instance creates unique credentials for each application to access the database.",
	        "providerDisplayName": "Pivotal Software",
	        "documentationUrl": "http://docs.gopivotal.com/",
	        "supportUrl": "http://gopivotal.com/support/"
	      },
	      "plans": [
	        {
	          "id": "ab08f1bc-e6fc-4b56-a767-ee0fea6e3f20",
	          "name": "100mb-dev",
	          "description": "Shared MySQL Server",
	          "metadata": {
	            "costs": [
	              {
	                "amount": {
	                  "usd": 0
	                },
	                "unit": "MONTH"
	              }
	            ],
	            "bullets": [
	              "Not for production use - server is not replicated",
	              "Shared MySQL server",
	              "100 MB storage",
	              "40 concurrent connections"
	            ],
	            "displayName": "(( merge || \"100 MB Dev\" ))"
	          }
	        }
	      ],
	      "bindable": true,
	      "dashboard_client": {
	        "id": "p-mysql",
	        "secret": "eaa139af583c",
	        "redirect_uri": "http://10.30.40.61/"
	      }
	    }
	  ]
	}

3.	Implementing Catalog Rest API
	
3.1.	JAVA Method
	
	-- CatalogRestController.java (Use Spring Framework)
	
	@Controller
	@RequestMapping("/v2/catalog")         // Use Spring Annotation
	class CatalogRestController {
	  def settings;
	
	@RequestMapping(method=RequestMethod.GET)
	  @ResponseBody
	  synchronized Map getCatalog() {
	    if (!settings) {
	      Yaml yaml = new Yaml(); 
	      // The service information and Plan information are inside the settings.yml file
	      settings = yaml.load(this.class.getClassLoader().getResourceAsStream("settings.yml"));
	    }
	
	    return settings;
	  }
	
	}
	
	-- settings.yml file
	
	services:
	- name: p-mysql
	  id: 3101b971-1044-4816-a7ac-9ded2e028079
	  description: MySQL service for application development and testing
	  tags:
	    - mysql
	    - relational
	  max_db_per_node: 250
	  metadata:
	    provider:
	      name:
	    listing:
	      imageUrl: ~
	      blurb: MySQL service for application development and testing
	  plans:
	  - name: 5mb
	    id: 2451fa22-df16-4c10-ba6e-1f682d3dcdc9
	    description: Shared MySQL Server, 5mb persistent disk, 40 max concurrent connections
	    max_storage_mb: 5 # in MB
	    metadata:
	      cost: 0.0
	      bullets:
	      - content: Shared MySQL server
	      - content: 5 MB storage
	      - content: 40 concurrent connections

3.2.	Ruby Method(Ruby on Rails)

	-- When creating an application, rails are used to create a basic creation structure for a new application. See table below
	$ rails new<broker_name>
	![openpaas-servicepack-13]
	
		-- config/routes.rb: Modified routing file with routing information for posts
		
		CfMysqlBroker::Application.routes.draw do
		  resource :preview, only: [:show]
		
		namespace :v2 do
		resource :catalog, only: [:show] // Access Routing Settings (V2/catalog)
		    patch 'service_instances/:id' => 'service_instances#set_plan'
		    resources :service_instances, only: [:update, :destroy] do
		      resources :service_bindings, only: [:update, :destroy]
		    end
		  end
		
		end
		
		-- RestController Implementing (app/controllers/v2/catalogs_controller.rb)
		
		class V2::CatalogsController < V2::BaseController
		  def show
		    render json: {
		      services: services.map {|service| service.to_hash }
		    }
		  end
		
		  private
		
		  def services
		    (Settings['services'] || []).map {|attrs| Service.build(attrs)}
		  end
		end
	
3.3.	Node.js Method

		-- Makes Rest API by using express, which is the most used web framework module in Node.js.
		
		# sample (app.js)
		
		var express = require('express')
		  , http = require('http')
		  , app = express()
		  , server = http.createServer(app);
		
		app.get('/v2/catalog ', function (req, res) {
		// Implement catalog functions
		});
		
		server.listen(8000, function() {   // Set port
		  console.log('Express server listening on port ' + server.address().port);
		});

4. Catalog API development specifications by service
In the case of the Catalog API, regardless of the type of service, the information is stored in the settings.yml file or other meta-file or source where the service and plan information is stored. If AppDirect is used, the AppDirect API that inquires catalog information is called to provide the result. For sample settings.yml file, refer to 3. Catalog Rest API Configuration.


◎ Example of Pivotal Service Plan
- Example of clearDB plan  
[picture reference]:http://run.pivotal.io/

![openpaas-servicepack-14]

![openpaas-servicepack-15]
 
![openpaas-servicepack-16]

![openpaas-servicepack-17]

◎ Example of Pivotal Service Dashboard
- Example of clearDB Dashboard  
[picture reference] https://www.cleardb.com/
![openpaas-servicepack-18]

![openpaas-servicepack-19]
 
##### <a name="13"/>2.5.2. Provision API Guide
When the broker sends a provision request from the Cloud Controller, it creates a new service instance for the developer. Provision results vary depending on the type of services in provision.
For Mysql Database, create a new DATABASE schema. In addition, the provision in the case of a non-data service may mean obtaining an account on an existing system. For detailed information, refer to the provision of each service.

1. Request

1.1. Route

	PUT /v2/service_instances/:instance_id

Note: The instance_id of the service instance is provided by the Cloud Controller. This ID is used for instance deletion, binding, and unbinding.

1.2. cURL
	$ curl http://username:password@broker-url/v2/service_instances/:instance_id -d '{
	  "service_id":        "service-guid-here",
	  "plan_id":           "plan-guid-here",
	  "organization_guid": "org-guid-here",
	  "space_guid":        "space-guid-here"
	}' -X PUT -H "X-Broker-API-Version: 2.4" -H "Content-Type: application/json"

1.3.	Body

![8](https://user-images.githubusercontent.com/104418463/200230229-73949202-3a1e-4388-8a6a-17da4bdf64dc.png)

2. Response
2.1. Status Code

![9](https://user-images.githubusercontent.com/104418463/200230289-623dc721-71f9-4f1e-803d-9dce5161ad6a.png)

2.2.	Body 
All response bodies are in the format JSON Object ({}).

![10](https://user-images.githubusercontent.com/104418463/200230346-63b448c7-d300-464a-bdcf-d4501e6e820c.png)


2.3.	Dashboard Single Sign-On.
Single Sign-On (SSO) allows open cloud platform users to access dashboards of third-party services using open cloud platform credentials. A service dashboard is a web interface in which some or all of the functions provided by the service may be used. SSO integrates and manages recurring logins and accounts for multiple services. Because it handles OAuth2 protocol authentication, user credentials are not sent directly to the service. To use SSO functions, the Cloud Controller UAA client must have permission to create and delete service brokers. Configure clients when installing an open cloud platform. (Refer to installation document)

◎ Dashboard SSO Setting Example during CF Installation)
	
	properties:
	    uaa:
	      clients:
	        cc-service-dashboards:
	          secret: cc-broker-secret
	          scope: openid,cloud_controller_service_permissions.read
	          authorities: clients.read,clients.write,clients.admin
	          authorized-grant-types: client_credentials

3. Provision Rest API Implementation
3.1. JAVA Method

	-- ServiceInstanceRestController.java (Use Spring Framework)
	
	@Controller
	@RequestMapping("/v2/service_instances/{id}")
	class ServiceInstanceRestController {
	  @Autowired
	private ServiceInstanceService service;
	
	  @RequestMapping(method = RequestMethod.PUT)
	  @ResponseBody
	  Map update(@PathVariable String id) {
	ServiceInstance instance = service.findById(id);   // Implement service by using Spring Framework
	    if (!service.isExists(instance)) {
	service.create(instance);        // The part of creating a service instance (implementation of development specification)
	    }
	    return [:];
	
	  }
	}

3.2. Ruby Method(Ruby on Rails)

	-- config/routes.rb: file that contains routing information
	
	CfMysqlBroker::Application.routes.draw do
	  resource :preview, only: [:show]
	
	namespace :v2 do
	resource :catalog, only: [:show] // access routing settings (V2/catalog)
	patch 'service_instances/:id' => 'service_instances#set_plan'
	    resources :service_instances, only: [:update, :destroy] do
	      resources :service_bindings, only: [:update, :destroy]
	    end
	  end
	
	end
	
	-- RestController Implementation (app/controllers/v2/service_instances_controller.rb)
	
	class V2::ServiceInstancesController < V2::BaseController
	
	  # This is actually the create
	  def update
	// Implementation of service instance creating function (Implementation of development details)
	  end
	
	end

3.3. Node.js Method

	◎ sample (app.js) : Refer Catalog API
	
	var router = express.Router();
	
	router.route('/v2/service_instances/:id’)
	
	.put(function(req, res, next) {
	// Implementing service instance creation function (Implementation of development specifications)
	
	})

4. Provision API development specifications by service
- Use a unique name when creating an instance.
- Verify if the requested instance ID to create exists already.
- Check if the selected plan information can be used when creating the instance. If possible, create the instance.
- When the instance creation is completed, send it to the Cloud Controller in JSON Object format as shown above.

4.1. RDBMS

1. In the case of Mysql

	- Check if the database to create exists 
	SHOW DATABASES LIKE '${instance.database}'
	
	- Create a new database
	CREATE DATABASE IF NOT EXISTS ${instance.database}
	
	- Send the Dashboard information in JSON Object format to Cloud Controller after creation.

2. In the case of Cubrid DB 

	- Create and move the directory to create a database
	$ mkdir <databasename>
	$ cd <databasename>
	
	- Create database
	$ cubrid created--db-volume-size=100M --log-volume-size=100M <databasename> en_US
	
	- Execute database
	$ cubrid service start
	$ cubrid server start <databasename>

4.2. Mass Storage
1. In the case of GlusterFS
◎ to upload files to ClusterFS, configure a service back-end with GlusterFS and OpenStack swift first to upload and download files using Object Storage. (similar to the Amazon S3 method)

	- Create a new Swift Account
	
	Method: PUT 
	
	Req URL : http(s)://[IP Address OR HostName]/auth/v2/[AccountID]
	
	Header  : X-Auth-Admin-User: .super_admin
		  X-Auth-Admin-Key: swauthkey
	          X-Account-Meta-Quota-Bytes: [Size(Byte)]


4.3.	NoSQL DB
1. In case of mongoDB

	- Create a new database
	>use <databasename>
	switched to db <databasename>

##### <a name="14"/>2.5.3. Update Instance API Guide
Update Instance API modifies the plan of the previously existing service instances. Which means it upgrades or downgrades the plan of the service instance.
To use this function, the broker should be set as “plan_updateable: true” from the catalog endpoint. If this option field is not included, it returns an error in the contents of the service plan modification he requests, and the broker does not call the API. If this field is included, an API call is made to the broker for all plan modification requests in the open cloud platform. The broker checks for plan support availability.

1.	Request
1.1.	Route
	PATCH /v2/service_instances/:instance_id

Note: instance_id is the GUID of the previous provision service instance

1.2.	cURL
	$ curl http://username:password@broker-url/v2/service_instances/:instance_id -d '{
	  "plan_id": "plan-guid-here"
	}' -X PATCH -H "X-Broker-API-Version: 2.4" -H "Content-Type: application/json"

1.3.	Body
	
![12](https://user-images.githubusercontent.com/104418463/200230543-bb440800-baa3-43aa-ada2-fc2c0b6a3add.png)



2.	Response
<br/>
2.1.	Status Code
STATUS CODE	DESCRIPTION
	
![13](https://user-images.githubusercontent.com/104418463/200230554-3bf778bd-b060-42c9-ab17-09d5eaa3edcc.png)



2.2.	Body 
All response bodies should be in JSON Object ({}) format.

3.	Implementing Update Service Instance Rest API 
3.1.	JAVA Method
	-- ServiceInstanceRestController.java (Use Spring Framework)
	
	@Controller
	@RequestMapping("/v2/service_instances/{id}")
	class ServiceInstanceRestController {
	  @Autowired
	  private ServiceInstanceService service;
	
	  @RequestMapping(method = RequestMethod.PATCH)
	  @ResponseBody
	  Map updateInstance(@PathVariable String id) {
	    ServiceInstance instance = service.findById(id);   // Implement service by using spring framework
	    if (!service.isExists(instance)) {
	      service.update(instance);        // a part where service instance can be modified (Implementation of Development Specifications)
	    }
	    return [:];
	
	  }
	}

3.2.	Ruby Method(Ruby on Rails)
	-- config/routes.rb: a modified routing file consisting of modified routing information for posts
	
	CfMysqlBroker::Application.routes.draw do
	  resource :preview, only: [:show]
	
	namespace :v2 do
	resource :catalog, only: [:show] // Access Routing Settings (V2/catalog)
	patch 'service_instances/:id' => 'service_instances#set_plan'
	resources :service_instances, only: [:update, :destroy] do
	      resources :service_bindings, only: [:update, :destroy]
	    end
	  end
	
	end

	-- RestController Implementation (app/controllers/v2/service_instances_controller.rb)
	
	class V2::ServiceInstancesController < V2::BaseController
	
	def set_plan
	// Update service instance plan information
	  end
	
	end

3.3.	Node.js Method
	◎ sample (app.js) : Refer to Catalog API
	
	var router = express.Router();
	
	router.route('/v2/service_instances/:id’)
	
	.patch(function(req, res, next) {
	  // Implements service instance modifying functions (Implementation of Development Specifications)
	
	})

4.	Update Service Instance API development specification by service 4.1. in common.
	1.	Check whether the plan information currently provided and the plan information requested for change are different.
	
	2.	When downgrading, an error occurs when the capacity being used is larger than the capacity to downgrade.
	
	3.	When upgrading, update the plan information. (Example: in case of DBMS service, the number of connections and storage capacity)
	
	4.	Communicate the changed contents to the Cloud Controller.

##### <a name="15"/>2.5.4. Deprovision API Guide
When a broker receives a deployment request from an open cloud platform, it deletes all the resources that it provided when creating the provision.

1.	Request
1.1.	Route
	DELETE /v2/service_instances/:instance_id

1.2.	Parameters
![14](https://user-images.githubusercontent.com/104418463/200230594-7d1fdabb-4222-4a39-8480-d031df1fdcb1.png)


1.3.	cURL
	$ curl 'http://username:password@broker-url/v2/service_instances/:instance_id?service_id=
	    service-id-here&plan_id=plan-id-here' -X DELETE -H "X-Broker-API-Version: 2.4"

2.	Response
2.1.	Status Code
![15](https://user-images.githubusercontent.com/104418463/200230609-8d1b625a-b955-4705-9597-7bf00bd56b51.png)


2.2.	Body 
All response bodies should be in JSON Object ({}) format.
Receive "{}" value on success.

3.	Deprovision Rest API Implementation
3.1.	JAVA Method
	-- ServiceInstanceRestController.java (Use Spring Framework)
	
	@Controller
	@RequestMapping("/v2/service_instances/{id}")
	class ServiceInstanceRestController {
	  @Autowired
	private ServiceInstanceService service;
	
	@RequestMapping(method = RequestMethod.DELETE)
	  @ResponseBody
	  Map destroy(@PathVariable String id) {
	    ServiceInstance instance = service.findById(id);
	    if (service.isExists(instance)) {
	      service.delete(instance); // Implement service instance delete function (Implementation of Development Specifications)
	    }
	    return [:]
	  }
	}

3.2.	Ruby Method(Ruby on Rails)
	-- config/routes.rb: a modified routing file consisting of modified routing information for posts
	
	CfMysqlBroker::Application.routes.draw do
	  resource :preview, only: [:show]
	
	namespace :v2 do
	resource :catalog, only: [:show] // Access Routing Settings (V2/catalog)
	patch 'service_instances/:id' => 'service_instances#set_plan'
	    resources :service_instances, only: [:update, :destroy] do
	      resources :service_bindings, only: [:update, :destroy]
	    end
	  end
	
	end
	
	-- RestController Implementation (app/controllers/v2/service_instances_controller.rb)
	
	class V2::ServiceInstancesController < V2::BaseController
	
	  def destroy
	// Implementing service instance delete function(Implementation of Development Specifications)
	  end
	
	end

3.3.	Node.js Method
	◎ sample (app.js) : Refer to Catalog API
	
	var router = express.Router();
	
	router.route('/v2/service_instances/:id’)
	
	.delete(function(req, res, next) {
	// Implementing service instance delete function(Implementation of Development Specifications)
	
	})


4.	Deployment API development specification by service
-	Check to see if there is a service instance requesting deletion.
-	When there is an instance requesting deletion, check if it is bound with an Application.
-	If the instance requesting deletion is bound with an Application, communicate the error to the Cloud Controller.
-	If there is no Application bound with the instance requesting for deletion, proceed with the deletion.

4.1.	RDBMS
1. In the case of Mysql

- Delete database
	DROP DATABASE IF EXISTS #{connection.quote_table_name(database_name)}

2. In the case of Cubrid DB 

- removing database after service shutdown
	$ cubrid service stop
	$ cubrid deletedb <databasename>

- remove the directory of the removed database
	$ rm –rf <install database path>/<databasename>


4.2.	Mass storage
1.  In the case of GlusterFS

	- Delete Swift Account
	
	Method: DELETE 
	
	Req URL : http(s)://[IP Address OR HostName]/auth/v2/[AccountID]
	
	Header  : X-Auth-Admin-User: .super_admin
		  X-Auth-Admin-Key: swauthkey

4.3.	NoSQL DB
1. In case of mongoDB

	- Delete database
	>use <databasename>
	switched to db <databasename>
	>db.dropDatabase()
	>{ "dropped" : "<databasename>", "ok" : 1 }
	>


##### <a name="16"/>2.5.5. Bind API Guide
If the service is available only with Provision, there is no need to implement the bind function, and only the resulting success message needs to be sent to the open cloud platform. When a broker receives a binding request from an open cloud platform, it returns the information necessary to utilize the provisioned resources. The information is provided in the credentials. It should not affect other applications by issuing unique credentials to the Application.

1.	Request
<br/>
1.1.	Route
	PUT /v2/service_instances/:instance_id/service_bindings/:binding_id
Note: The binding_id is provided by the Cloud Controller to perform service binding. The binding_id is used for unbinding requests that will be shown later.

1.2.	cURL
	$ curl http://username:password@broker-url/v2/service_instances/
	:instance_id/service_bindings/:binding_id -d '{
	  "plan_id":        "plan-guid-here",
	  "service_id":     "service-guid-here",
	  "app_guid":       "app-guid-here"
	}' -X PUT -H "X-Broker-API-Version: 2.4" -H "Content-Type: application/json"

1.3.	Body
	
![16](https://user-images.githubusercontent.com/104418463/200230689-2129b7d0-f168-4a32-a6f6-34b200126a91.png)


2.	Response
<br/>
2.1.	Status Code
	
![17](https://user-images.githubusercontent.com/104418463/200230699-ff968e7c-4687-4fdf-adad-bcd8052701d6.png)

A different status code response means failure.

2.2.	Body 
	
![18](https://user-images.githubusercontent.com/104418463/200230721-7510158a-b8aa-4799-9cd4-1ad776780827.png)

2.3.	Binding Credentials 
<br/>
In the case of service binding, authentication information that the user can use in the application is returned in response to the bind API call. Provide these credentials to the open cloud platform environment variable VCAP_SERVICES. It is recommended to be used in the Credentials field list. Use if the fields provided meet the user's requirements. Additional fields can be provided as needed.

Important: If you provide a service that supports the connection string, at least the uri key must be provided. As mentioned above, a separate credential field may also be provided. Buildpacks and Application libraries use the uri key.
![19](https://user-images.githubusercontent.com/104418463/200230745-5a2562cd-5057-47b9-b6b4-51e86d625983.png)


◎ Example VCAP_SERVICES result

	VCAP_SERVICES=
	{
	  cleardb: [
	    {
	      name: "cleardb-1",
	      label: "cleardb",
	      plan: "spark",
	      credentials: {
	        name: "ad_c6f4446532610ab",
	        hostname: "us-cdbr-east-03.cleardb.com",
	        port: "3306",
	        username: "b5d435f40dd2b2",
	        password: "ebfc00ac",
	        uri: "mysql://b5d435f40dd2b2:ebfc00ac@us-cdbr-east-03.cleardb.com:3306/ad_c6f4446532610ab",
	        jdbcUrl: "jdbc:mysql://b5d435f40dd2b2:ebfc00ac@us-cdbr-east-03.cleardb.com:3306/ad_c6f4446532610ab"
	      }
	    }
	  ],
	  cloudamqp: [
	    {
	      name: "cloudamqp-6",
	      label: "cloudamqp",
	      plan: "lemur",
	      credentials: {
	        uri: "amqp://ksvyjmiv:IwN6dCdZmeQD4O0ZPKpu1YOaLx1he8wo@lemur.cloudamqp.com/ksvyjmiv"
	      }
	    }
	    {
	      name: "cloudamqp-9dbc6",
	      label: "cloudamqp",
	      plan: "lemur",
	      credentials: {
	        uri: "amqp://vhuklnxa:9lNFxpTuJsAdTts98vQIdKHW3MojyMyV@lemur.cloudamqp.com/vhuklnxa"
	      }
	    }
	  ],
	  rediscloud: [
	    {
	      name: "rediscloud-1",
	      label: "rediscloud",
	      plan: "20mb",
	      credentials: {
	        port: "6379",
	        host: "pub-redis-6379.us-east-1-2.3.ec2.redislabs.com",
	        password: "1M5zd3QfWi9nUyya"
	      }
	    },
	  ],
	}

2.4.	Application Log Streaming 
<br/>
The open cloud platform streams logs for applications bound to service instances. Logs for all applications bound to a service instance are streamed to that instance.

The operation method is as follows.
1.	The broker returns a value for syslog_drain_url in response to the bind.
2.	Update the key and value of syslog_drain_url in VCAP_SERVICES when the application reboots.
3.	DEAs continuously stream application logs from the Loggregator.
4.	If syslog_drain_url exists in VCAP_SERVICES, the DEA tags the field in the log.
5.	The Loggregator logs streams by tagging the key on the designated location with the value.


3.	Bind Rest API Implementation
3.1.	JAVA Method
	-- ServiceBindingRestController.java (Use Spring Framework)
	
	@Controller
	@RequestMapping("/v2/service_instances/{instanceId}/service_bindings/{bindingId}")
	class ServiceBindingRestController {
	  @Autowired ServiceBindingService bindingService;
	
	@RequestMapping(method = RequestMethod.PUT)
	  @ResponseBody
	  ServiceBinding update(@PathVariable String instanceId, @PathVariable String bindingId) {
	    ServiceBinding binding = bindingService.findById(bindingId, instanceId);
	    bindingService.save(binding);     // Implement service bind function (Implementation of Development Specifications)
	    return binding;
	  }
	
	}

3.2.	Ruby Method(Ruby on Rails)
	-- config/routes.rb: Modified routing file with routing information for posts
	
	CfMysqlBroker::Application.routes.draw do
	  resource :preview, only: [:show]
	
	namespace :v2 do
	resource :catalog, only: [:show] // Access routing settings (V2/catalog)
	    patch 'service_instances/:id' => 'service_instances#set_plan'
	    resources :service_instances, only: [:update, :destroy] do
	resources :service_bindings, only: [:update, :destroy]
	    end
	  end
	
	end

	-- RestController Implementation (app/controllers/v2/service_bindings_controller.rb)
	
	class V2::ServiceBindingsController< V2::BaseController
	
	  def update
	// Implement service bind function (Implementation of Development Specifications)
	  end
	
	end

3.3.	Node.js Method
	◎ sample (app.js) : Refer to Catalog API

	var router = express.Router();
	
	router.route('/v2/service_instances/:instanceId/service_bindings/:bindingId’)
	
	.put(function(req, res, next) {
	// Implementating service bind function (Implementation of Development Specifications)
	
	})


4.	Bind API development specification by service
-	Check if there is a service instance to bind.
-	Create information to bind to the application and deliver it to the application.
-	At this time, bind information is randomly generated and base64 encoded and sent.

4.1.	RDBMS
1. In the case of Mysql

	- Create a user to connect to the database.
	CREATE USER #{username} IDENTIFIED BY #{password}
	
	- The database created during provision is authorized to be used by the user and the number of connections corresponding to the plan is provided.
	
	GRANT ALL PRIVILEGES ON #{databasename}.* TO #{username}@'%'WITH MAX_USER_CONNECTIONS #{max_user_connections}
	
	- Relocate the permission table on the server.
	FLUSH PRIVILEGES

2. In the case of Cubrid DB 

	- Create a user to connect to the database.
	CREATE USER #{username};

Note: In the Cubrid DB, the minimum unit of the authorization given is a table. Give access to the table made.

4.2.	Mass Storage
1. In the case of GlusterFS

	- Create a new Swift User
	
	Method: PUT 
	
	Req URL : http(s)://[IP Address OR HostName]:[PORT]/auth/v2/[AccountID]/[UserId]
	
	Header  : X-Auth-Admin-User: .super_admin
		  X-Auth-Admin-Key: swauthkey
		  X-Auth-User-Key: [Password]
		  X-Auth-User-Admin: [true OR false]

4.3.	NoSQL DB
1. In case of mongoDB

	- Create a user to connect to the database and grant access roles (read, write).
	>use <databasename>
	switched to db <databasename>
	>db.getSiblingDB("<databasename>").runCommand(
	 { createUser: "<username>",
	 pwd: "<password>",
	roles: [
	"readWrite"
	         ]
	   }
	 )

##### <a name="17"/>2.5.6. Unbind API Guide 
Refer: Brokers that do not provide binding services do not need to implement the Unbind API.
When a broker receives an unbind request from an open cloud platform, it deletes all resources created by the bind. If deleted, the service is inaccessible.

1.	Request
1.1.	Route
	DELETE /v2/service_instances/:instance_id/service_bindings/:binding_id

1.2.	Parameters
		
![20](https://user-images.githubusercontent.com/104418463/200230796-e806bb72-d27a-40e4-9b48-8bcb26fca760.png)


1.3.	cURL
	$ curl 'http://username:password@broker-url/v2/service_instances/:instance_id/
	service_bindings/:binding_id?service_id=service-id-here&plan_id=plan-id-here' -X DELETE -H "X-Broker-API-Version: 2.4"

2.	Response
<br/>
2.1.	Status Code
		
![21](https://user-images.githubusercontent.com/104418463/200230828-578c1f1a-81a2-40e4-8da7-327472c09f73.png)


2.2.	Body 
All response bodies should be in JSON Object ({}) format.
Receive "{}" value on success.

3.	Unbind Rest API Implementation
<br/>
3.1.	JAVA Method
	-- ServiceBindingRestController.java (Use Spring Framework)
	
	@Controller
	@RequestMapping("/v2/service_instances/{instanceId}/service_bindings/{bindingId}")
	class ServiceBindingRestController {
	  @Autowired ServiceBindingService bindingService;
	
	@RequestMapping(method = RequestMethod.DELETE)
	@ResponseBody
	Map destroy(@PathVariable String instanceId, @PathVariable String bindingId) {
	    ServiceBinding binding = bindingService.findById(bindingId, instanceId); 
	    bindingService.destroy(binding);  // Implement service unbind function (Implementation of Development Specifications)
	    return [:];
	  }
	}

3.2.	Ruby Method(Ruby on Rails)
	-- config/routes.rb: Modified routing file with routing information for posts
	
	CfMysqlBroker::Application.routes.draw do
	  resource :preview, only: [:show]
	
	namespace :v2 do
	resource :catalog, only: [:show] // Access Routing Settings (V2/catalog)
	    patch 'service_instances/:id' => 'service_instances#set_plan'
	    resources :service_instances, only: [:update, :destroy] do
	resources :service_bindings, only: [:update, :destroy]
	    end
	  end
	
	end
	
	-- RestController Implementation (app/controllers/v2/service_bindings_controller.rb)
	
	class V2::ServiceBindingsController< V2::BaseController
	
	  def destroy
	// Implement service unbind function (Implementation of Development Specifications)
	  end
	
	end

3.3.	Node.js Method
	◎ sample (app.js): Refer to Catalog API
	
	var router = express.Router();
	
	router.route('/v2/service_instances/:instanceId/service_bindings/:bindingId’)
	
	.delete(function(req, res, next) {
	// Implement service unbind function (Implementation of Development Specifications)
	
	})

4.	Unbind API development specification by service
-	Check if an instance of bind to be unbound exists.
-	Delete the information bound to the application and deliver the result to the application.

4.1.	RDBMS
1. In the case of Mysql
	
	- Check if there is a user to unbind
	SHOW GRANTS FOR #{username)}
	
	- Delete the created user.
	DROP USER #{username}
	
	- Relocate the authentication table on the server.
	FLUSH PRIVILEGES

2. In the case of Cubrid DB 

	- Delete the user-created when binding.
	DROP USER #{username};

4.2.	Mass Storage
1. In the case of GlusterFS

	- Delete Swift User
	
	Method: DELETE
	
	Req URL : http(s)://[IP Address OR HostName]/auth/v2/[AccountID]/[UserId]
	
	Header  : X-Auth-Admin-User: .super_admin
		  X-Auth-Admin-Key: swauthkey	  X-Auth-User-Key: [Password]
		  X-Auth-User-Admin: [true OR false]

4.3.	NoSQL DB
1. In case of mongoDB
	
	- Delete the user created when binding.
	>use <databasename>
	switched to db <databasename>
	>db.runCommand( { dropUser: "<username>"
	                } )

 

### <a name="18"/>3. Service release Development Guide

#### <a name="19"/>3.1.	Outline
The BOSH release consists of jobs (packages-driven scripts, monit scripts, etc.), packages, source code, and metadata from related sources. Install software through BOSH (service back-end, bro, etc.). The binary file (also known as "blobs") required for packaging, eliminates the need of necessity to store files inside the release storage. It is stored in the Blob repository in the release and can be referenced externally. Write a release yml to utilize the BOSH release.

Refer: If the service back-end (refer to the software: 2.2 Service Architecture provided by the service provider) is a provider that already provides an external service, it is not necessary to install the service back-end with the BOSH release, and only the broker can develop and connect to the external service. In this case, install with Bosh release for Broker only. However, if an externally provided service is included in the IaaS where the CF is installed (if it is a cloud operation in a disconnected network), the service back-end is deployed as a BOSH release. In addition, if you use external services and want to provide (cf push) as an application for an open cloud platform, you can skip the BOSH release and proceed with the 2. Service Broker Guide.

#### <a name="20"/>3.2.	Bosh Architecture
 
![openpaas-servicepack-33]  
> [picture reference]: http://www.cloudsofchange.com/2012/05/fork-in-road-to-cloud.html

-	Similar to open cloud platform architecture (Message Bus, Health Monitor, Blobstore, etc.)
-	Director is similar to Cloud Controller
-	CPIs implementation varies depending on the type of IaaS. (CPI: Cloud Provider Interface)
-	Workers are responsible for executing tasks determined by the Director

![openpaas-servicepack-34]  
> [picture reference]: https://www.ibm.com/developerworks/community/blogs/fe313521-2e95-46f2-817d-44a4f27eba32/entry/porting_cloud_foundry_on_power8_ubuntu_le?lang=en

#### <a name="21"/>3.3.	Release Directory Configuration
The directory structure can be configured with the Bosh release. Bosh is an open-source tool for release engineering, deployment, and lifecycle management of large-scale deployed services.

##### <a name="22"/>3.3.1. packages
Packages provide the information necessary to prepare a binary dependency for Bosh release installation. (packaging, pre_packaging, spec file)

![openpaas-servicepack-35]
 
##### <a name="23"/>3.3.2. jobs
It consists of the operations and shutdown scripts of the installed packages and the monitoring script.

![openpaas-servicepack-36]

##### <a name="24"/>3.3.3. src
Configures a component source code or pre-compiled software file used in a service release.
 
![openpaas-servicepack-37]
 
##### <a name="25"/>3.3.4. shared
Manage common component sources such as ruby and lib. (Option)
 
![openpaas-servicepack-38]
 
##### <a name="26"/>3.3.5. releases
-	Manage version-specified service release yml files. (yaml installation method)
-	Manage version-specified service release tgz zipped files. (tarball installation method)
-	Refer to the development guide below for the installation method of Yaml and tarball.
 
![openpaas-servicepack-39]

##### <a name="27"/>3.3.6. config
Configure the URL and the settings file for access credentials in the Bosh blobstore for storing the final release.
 
![openpaas-servicepack-40]

##### <a name="28"/>3.3.7. final_builds
Provides public blobstore information for final jobs and packages.

![openpaas-servicepack-41]
 
##### <a name="29"/>3.3.8. deployments
manages service deployment manifest files by IaaS.
 
![openpaas-servicepack-42]

##### <a name="30"/>3.3.9. content_migrations
Manage migration information files from this version. (Option)
 
![openpaas-servicepack-43]
 
#### <a name="31"/>3.4.Development Guide
<br/>
must be written according to the Bosh release development method because the service must be deployed through the Bosh release.  
The Bosh release consists of scripts related to packages and jobs.
Bosh offers two ways to release software.
Bosh upload release CLI command and process are as follows.

bosh upload release CLI 
bosh upload release [<release_file>] [--rebase] [--skip-if-exists]
release_file: local files or remote URIs information
    --rebase:Set to Director with the latest version
    --skip-if-exists:Do not upload if the release exists

1.	Installation process using the Yaml file [When <release_file> parameter is yml file]: Read the cf-<service_name>-<version>.yml file inside the release directory and set value as sha1. It is a method of accessing and installing the blobstore of config/final.yml with the blobstore_id of index.yml in the corresponding packages or jobs folder in the final_builds folder.
	
2.	Installation process using tarball(Zipped files containing all release files to install: tgz format) [When <release_file> parameter is tgz file]: All packages and jobs files to be installed, and the release (release.MF), and job meta files are in the tgz zip file, so it is installed without downloading from the blobstore. (Zip as .tgz file inside the releases directory)

##### <a name="32"/>3.4.1. Packages Guide
Consists of packing, pre_packaging, and spec files related to the installation of service software.

###### <a name="33"/>3.4.1.1. Packaging
The packaging file provides a script for installing the software.

◎ packaging file instruction
1	Create packaging script file automatically with the “bosh generate package PACKAGE_NAME” command.  
1.1	Example) $ bosh generate package test (Execute in service release)  
1.2	A test package folder is created in the packages folder, and a package, pre_packaging, and spec files are created inside the folder   
1.3	You can create a file manually by creating a directory instead of using the bosh generate package command   
2	At compilation, Bosh receives the source file referenced in the package specification and consists of executable binaries and scripts that require deployment operations.   
3	Include the following contents when writing packing scripts.   
3.1	For Ruby applications, BOSH must install Ruby gems and copy the source files. (RubyGems is the package manager of the Ruby Programming Language, which provides a standard format for distributing ruby programs and libraries.).    
3.2	For Ruby itself, BOSH must compile the source into binary.   
3.3	For Python applications, BOSH must install Python eggs and copy the source file.    
4	Observe these principles when writing a packaging script.    
4.1	Start the script with "set -e –x". This helps to debug during compilation by immediately scripting termination if an error occurs.    
4.2	Verify if the code for copy, install, or compile (represented by the BOSH_INSTALL_TARGET environment variable) can be generated in the directory to be installed. Use configure or equivalent operation for "make" commands.    
4.3	Ensure that the binaries deployed as dependencies of the BOSH package specification file are available.    

	◎ Example libyaml packaging script
	set -e -x
	
	tar xzf libyaml_0.1.4/yaml-0.1.4.tar.gz
	pushd yaml-0.1.4
	  ./configure --prefix=${BOSH_INSTALL_TARGET}
	
	  make
	  make install
	popd
	
	◎ Example Ruby packaging script
	set -e -x
	
	tar xzf ruby_1.9.3/ruby-1.9.3-p484.tar.gz
	pushd ruby-1.9.3-p484
	  ./configure \
	    --prefix=${BOSH_INSTALL_TARGET} \
	    --disable-install-doc \
	    --with-opt-dir=/var/vcap/packages/libyaml_0.1.4
	
	  make
	  make install
	popd
	
	tar zxvf ruby_1.9.3/rubygems-1.8.24.tgz
	pushd rubygems-1.8.24
	  ${BOSH_INSTALL_TARGET}/bin/ruby setup.rb
	popd
	
	${BOSH_INSTALL_TARGET}/bin/gem install ruby_1.9.3/bundler-1.2.1.gem --no-ri --no-rdoc
	
	◎ Example ruby_app packaging script
	set -e -x
	
	cp -a ruby_app/* ${BOSH_INSTALL_TARGET}
	
	cd ${BOSH_INSTALL_TARGET}
	
	/var/vcap/packages/ruby_1.9.3/bin/bundle install \
	  --local \
	  --deployment \
	  --without development test

###### <a name="34"/>3.4.1.2. pre_packaging
The pre_packaging file provides a script for pre-packaging software. (Option)
Use of the pre_packaging file is not recommended and is not discussed in this tutorial. 
It is specified in the https://bosh.io/docs/create-release.html#dev-release-release document

	◎ mysql-service-broker pre_packaging Sample
	abort script on any command that exits with a non-zero value
	set -e
	
	(
	  cd ${BUILD_DIR}/cf-mysql-broker
	
	  # cache gems
	  bundle package --all
	
	  RAILS_ENV=assets bundle exec rake assets: precompile
	
	  # remove unneeded files
	  rm -rf spec
	)

###### <a name="35"/>3.4.1.3. spec
The meter information of the package to be installed is provided such as name, dependencies, and installation file information.

◎ spec file explanation
1	name: package name  
2	dependencies: (option) Define a list of other packages that depend on the package  
3	files: defines a list of files in a package, or a list of files by explicit or pattern matching   
4	Procedure for editing package spec files  
4.1	Check all the compilation time dependencies. If the package is depending on the other package, a dependency occurs when compiling. 
	(4.2 After creating the spec file, add the contents of the dependencies if there is a dependency)
Example) dependency graph
 
![openpaas-servicepack-44]  
[picture reference]: https://bosh.io/docs/create-release.html

4.2	Create spec script file automatically by using the “bosh generate package PACKAGE_NAME” command.  
4.2.1	Example) $ bosh generate package test (Execute in service release folder)  
4.2.2	A test package folder is created in the packages folder, and a package, pre_packaging, and spec files are created inside the folder    
4.2.3	You can create a file manually by creating a directory instead of using the bosh generate package command    
4.3	Copy all the files the package needs to the src directory. Typically, a file is a source code. If it contains pre-compiled software (e.g. ruby-1.9.3-p484.tar.gz), copy the compressed file containing the pre-compiled binary.  
4.4	Create a spec file
4.4.1	Add the package name and file name
4.4.2	From the spec file, add the name of the compile-time dependency. If there is no compile-time dependency in the package, use [] to indicate the empty array  
4.4.3	First, find the files in the src directory, and if there is none, find them in the blobs of the blobstore.  
4.4.4	If the file is configured as a source, the globbing pattern(<package_name>/**/*) is typically used.

	◎ Example Ruby package spec file
	---
	name: ruby_1.9.3
	
	  dependencies:
	  - libyaml_0.1.4
	
	  files:
	  - ruby_1.9.3/ruby-1.9.3-p484.tar.gz
	  - ruby_1.9.3/rubygems-1.8.24.tgz
	  - ruby_1.9.3/bundler-1.2.1.gem

##### <a name="36"/>3.4.2. jobs guide
All jobs should be provided with a method of starting and stopping. Therefore, control scripts are written and MONIT files are written to monitor the jobs (processes) that are executed.

###### <a name="37"/>3.4.2.1. templates
The template file drives and stops the installed package and configures the associated settings file.

◎ control script explanation : *.erb file
1	It includes a start and a stop command.
2	Configure in ERb template format in the templates directory for related jobs. (Configuring with shell script)
3	For each job, create a control script that configures log operations in “/var/vcap/sys/log/JOB_NAME”.

	◎ Example mariadb_ctl.erb
	!/bin/bash -e
	
	set -e
	
	export MARIADB_JOB_DIR=/var/vcap/jobs/mysql
	RUN_DIR=/var/vcap/sys/run/mysql
	datadir=/var/vcap/store/mysql
	LOG_DIR=/var/vcap/sys/log/mysql
	LOG_FILE=$LOG_DIR/maria-ctl.log
	CONFIG_DIR=/etc/mysql
	export JOB_INDEX=<%= index %>
	STATE_FILE=/var/vcap/store/mysql/state.txt
	MYSQL_DAEMON_FILE=/var/vcap/packages/mariadb_ctrl/mysql_daemon.sh
	MYSQL_CLIENT_FILE=/var/vcap/packages/mariadb/bin/mysql
	MYSQL_SERVER_FILE=/var/vcap/packages/mariadb/support-files/mysql.server
	DB_SEED_SCRIPT_FILE=$MARIADB_JOB_DIR/bin/mysql_database_seed.sh
	package_dir=/var/vcap/packages/mariadb_ctrl
	executable_name=mariadb_ctrl-executable
	MYSQL_UPGRADE_SCRIPT_FILE=$package_dir/mysql_upgrade.sh
	MYSQL_SHOW_DATABASES_SCRIPT_FILE=$package_dir/show_databases.sh
	
	export MY_NAME=mariadb_ctl
	export RUN_DIR=/var/vcap/sys/run/$MY_NAME
	export PIDFILE=$RUN_DIR/$MY_NAME.pid
	
	DATABASE_SEED_ATTEMPTS=30
	
	source /var/vcap/packages/common/utils.sh
	
	◎ add mysql to path
	if [ ! -f /usr/local/bin/mysql ]; then
	  log "Adding mysql to path"
	  ln -s /var/vcap/packages/mariadb/bin/mysql /usr/local/bin
	fi
	
	◎ add xtrabackup to path
	export PATH=$PATH:/var/vcap/packages/xtrabackup/bin
	
	◎ add perl libraries to perl env
	export PERL5LIB=$PERL5LIB:/var/vcap/packages/xtrabackup/lib/perl/5.18.2
	
	case $1 in
	
	  ◎ The start script must always exit 0 if there's a chance Maria could start successfully,
	  ◎ as if monit sees a pid for Maria but this script exited with error, it will assume
	  ◎ someone else started the process in the background.
	  ◎ This will latch the status to "Execution failed" until someone manually calls
	  monit unmonitor && monit monitor, at which point monit would find the pid and reset the status to 'running'
	
	  start)
	
	if [[ ! -d "$RUN_DIR" ]]; then
	      log "start script: directory $RUN_DIR does not exist, creating it now"
	      mkdir -p $RUN_DIR
	    fi
	
	    log "start script: checking for existing instance of $MY_NAME"
	    set +e
	    $(source /var/vcap/packages/common/utils.sh; pid_guard $PIDFILE $MY_NAME)
	    pg_exit_code=$?
	    log "start script: pg_exit_code: $pg_exit_code"
	    if [ $pg_exit_code -eq 1 ]; then
	log "start script: $MY_NAME already running. Exiting 0 so that monit doesn't think that execution failed"
	      exit 0
	    fi
	    set -e
	
	    log "start script: writing pid $$ for $MY_NAME to $PIDFILE"
	    echo $$ > $PIDFILE
	
	log "start script: checking if mysqld_safe is already running... "
	    set +e
	    pgrep -f /var/vcap/packages/mariadb/bin/mysqld_safe
	    result_code=$?
	    set -e
	    // Exit code of 0 means we did find a process, so we should exit.
	    if [ $result_code -eq 0 ]; then
	      log "start script: mysqld_safe already running - exiting with 0 so that monit doesn't think that execution failed"
	      exit 0
	    else
	      log "start script: mysqld_safe not already running - continue"
	    fi
	
	    Start syslog forwarding
	    /var/vcap/packages/syslog_aggregator/setup_syslog_forwarder.sh $MARIADB_JOB_DIR/config
	
	    mkdir -p $LOG_DIR
	    touch $LOG_FILE
	    chown vcap:vcap $LOG_FILE
	    date >> $LOG_FILE 2>> $LOG_FILE
	
	    It is surprisingly hard to get the config file location passed in
	    on the command line to the mysql.server script. This is easier.
	    mkdir -p $CONFIG_DIR
	    rm -f /etc/my.cnf
	    rm -f $CONFIG_DIR/my.cnf
	    ln -sf $MARIADB_JOB_DIR/config/my.cnf $CONFIG_DIR/my.cnf
	
	if ! test -d ${datadir}; then
	      log "start script: making ${datadir} and running /var/vcap/packages/mariadb/scripts/mysql_install_db"
	      mkdir -p ${datadir}
	      /var/vcap/packages/mariadb/scripts/mysql_install_db \
	             --basedir=/var/vcap/packages/mariadb --user=vcap \
	             --datadir=${datadir} >> $LOG_FILE 2>> $LOG_FILE
	    fi
	    chown -R vcap:vcap ${datadir}
	
	    cd $package_dir
	
	<% node_ip = spec.networks.send(p('network_name')).ip %>
	
	log "start script: starting mariadb_ctrl..."
	    $package_dir/$executable_name \
	             -logFile=$LOG_FILE \
	             -stateFile=$STATE_FILE \
	             -mysqlDaemon=$MYSQL_DAEMON_FILE \
	             -mysqlClient=$MYSQL_CLIENT_FILE \
	             -mysqlUser=<%= p('admin_username')%> \
	             -mysqlPassword=<%= p('admin_password')%> \
	             -jobIndex=$JOB_INDEX \
	             -dbSeedScript=$DB_SEED_SCRIPT_FILE \
	             -upgradeScriptPath=$MYSQL_UPGRADE_SCRIPT_FILE \
	             -showDatabasesScriptPath=$MYSQL_SHOW_DATABASES_SCRIPT_FILE \
	             -numberOfNodes=<%= p('cluster_ips').length %> \
	             -clusterIps=<%= (p('cluster_ips') - [node_ip]).join(',') %> \
	             -maxDatabaseSeedTries=$DATABASE_SEED_ATTEMPTS
	>> $LOG_FILE 2>> $LOG_FILE
	
	
	log "start script: completed starting mariadb_ctrl."
	    ;;
	
	  stop)
	log "stop script: stopping mariadb_ctrl..."
	    mkdir -p $LOG_DIR
	    date >> $LOG_FILE 2>> $LOG_FILE
	    log "stop script: stopping node $JOB_INDEX" >> $LOG_FILE
	    /var/vcap/packages/mariadb/support-files/mysql.server stop >> $LOG_FILE 2>> $LOG_FILE
	
	    log "stop script: completed stopping mariadb_ctrl"
	    ;;
	
	  *)
	    echo "Usage: mysql_ctl {start|stop}"
	    ;;
	
	esac

###### <a name="38"/>3.4.2.2. monit
In the deployed release, the BOSH Agent runs on the job VM. The BOSH in turn communicates with the agent by executing commands from the control script. The Agent uses an open-source process monitoring software called Monit.

◎ monit file explanation
1	Specify the Job Process ID (PID) file.
2	Specify to let the job belog to vcap group

	◎ Example mariadb monit file
	check process mariadb_ctrl-executable
	  with pidfile /var/vcap/sys/run/mysql/mysql.pid
	  start program "/var/vcap/jobs/mysql/bin/mariadb_ctl start" with timeout 300 seconds
	  stop program "/var/vcap/jobs/mysql/bin/mariadb_ctl stop" with timeout 10 seconds
	  group vcap
	  depends on galera-healthcheck-executable
	
	check process galera-healthcheck-executable
	  with pidfile /var/vcap/sys/run/galera-healthcheck/galera-healthcheck.pid
	  start program "/var/vcap/jobs/mysql/bin/galera-healthcheck_ctl start" with timeout 10 seconds
	  stop program "/var/vcap/jobs/mysql/bin/galera-healthcheck_ctl stop" with timeout 10 seconds
	  group vcap
	  depends on gra-log-purger-executable
	
	check process gra-log-purger-executable
	  with pidfile /var/vcap/sys/run/gra-log-purger/gra-log-purger.pid
	  start program "/var/vcap/jobs/mysql/bin/gra-log-purger_ctl start" with timeout 10 seconds
	  stop program "/var/vcap/jobs/mysql/bin/gra-log-purger_ctl stop" with timeout 10 seconds
	  group vcap

###### <a name="39"/>3.4.2.3. spec
The meter information of the package to be installed is provided such as name, dependencies, and installation file information.

◎ spec file explanation 
1	name: job name  
2	templates: Exists in key/value format   
2.1	Each key is the template name  
2.2	Each value is the path of the file to the job VM  
2.3	File path is based on “/var/vcap/jobs/<job_name>” directory  
2.4	Example) bin/mariadb_ctl ->/var/vcap/jobs/<job_name>/bin/ mariadb_ctl  
3	packages: list of the package being installed
4	properties: the variables used in the template file

	◎ Example MySQL job spec file
	---
	name: mysql
	
	templates:
	  mariadb_ctl.erb:  bin/mariadb_ctl
	  my.cnf.erb:     config/my.cnf
	  mariadb_init.erb: config/mariadb_init
	  galera-healthcheck_ctl.erb: bin/galera-healthcheck_ctl
	  gra-log-purger_ctl.erb: bin/gra-log-purger_ctl
	  gra-log-purger.sh.erb: bin/gra-log-purger.sh
	  mysql_database_seed.sh.erb: bin/mysql_database_seed.sh
	  syslog_forwarder.conf.erb: config/syslog_forwarder.conf
	
	packages:
	- xtrabackup
	- mariadb
	- mariadb_ctrl
	- galera
	- galera-healthcheck
	- gra-log-purger
	- golang
	- common
	- syslog_aggregator
	
	properties:
	  admin_username:
	    description: 'Username for the MySQL server admin user'
	    default: 'root'
	  admin_password:
	    description: 'Password for the MySQL server admin user'
	  port:
	    description: 'Port the mysql server should bind to'
	    default: 3306
	  max_connections:
	    description: 'Maximum total number of database connections for the node'
	    default: 1500
	  innodb_buffer_pool_size:
	    description: 'The size in bytes of the memory buffer InnoDB uses to cache data and indexes of its tables'
	  cluster_ips:
	description: 'List of nodes.  Must have the same number of ips as there are nodes in the cluster'
	  haproxy_mysql_user:
	    description: 'A user for HAProxy health check'
	  haproxy_ips:
	    description: 'List of haproxy node ip addresses'
	
	  #these two properties are also used by the Broker
	  gcache_size:
	    description: 'Cache size used by galera (maximum amount of data possible in an IST), in MB'
	    default: 512
	  ib_log_file_size:
	    description: 'Size of the ib_log_file used by innodb, in MB'
	    default: 1024
	  seeded_databases:
	    description: 'Set of databases to seed'
	    default: {}
	
	  network_name:
	    description: "The name of the network (needed for the syslog aggregator)"
	  syslog_aggregator.address:
	    description: "IP address for syslog aggregator"
	  syslog_aggregator.port:
	    description: "TCP port of syslog aggregator"
	  syslog_aggregator.all:
	description: "Define whether forwarders should also send non-mysql syslog activity to the aggregator."
	    default: false
	  syslog_aggregator.transport:
	description: "Transport to be used when forwarding logs (tcp|udp|relp)."
	    default: "tcp"
	
	
### <a name="40"/>4. Deployment Guide
BOSH Deploymentmanifest is a YAML file that defines the components element and properties of the deployment.
Deployment manifest uses Bosh deploy CLI to install the software(servicepack). When installing, it defines the name, version, VMs capacity, Jobs params, and Release (Software packages, Config templates, Scripts) as well as which Stemcell (OS, BOSH agent) to use. (refer to the picture in 3.2 Bosh Architecture의 Modules components)

The contents of the BOSH Deployment Manifest are as follows.
	Deployment Identification: Information of the deployment name and the UUID of the BOSH Director that manages the deployment
	Releases Block: Name and version information for each release within the deployment
	Networks Block: Information on the network configuration
	Resource Pools Block: Properties of virtual machines that you create and manage with BOSH
	Disk Pools Block: Properties for disks created and managed by BOSH
	Compilation Block: Virtual Machine Properties Required When Compiling
	Update Block: Defines how BOSH updates job instances during deployment
	Jobs Block: Configuration and resource information for jobs
	Properties Block: Describes global properties and generalized configuration information

1.	Deployment Identification
name [String, required]: Name of deployment. A single BOSH Director manages multiple deployments and distinguishes them by their names.
director_uuid [String, required]: The BOSH CLI must match the UUID of the current target BOSH Director to allow all operations on the deployment. Through the 'bosh status' CLI, you can check the UUID currently targeted by the BOSH Director.
![openpaas-servicepack-45]

bosh status CLI
 
	◎ Example
	name: my-redis-deployment
	director_uuid: 8b701af8-d658-48ee-893e-9d299622e332

2.	Releases Block
releases [Array, required]: Name and version information for each release within the deployment
name [String, required]: Name used in release
version [String, required]: The version used by the release. Use the latest version if 'latest' is used

	◎  example
	releases:
	- {name: redis, version: 12}

3.	Networks Block
networks [Array, required]: Specify the network configuration of tasks that each sub-block listed in the network block can refer to. There are three types of networks: manual, dynamic, and VIP.

◎  AWS Example
'cloud_properties' schema used by Dynamic network or manual network subnets
subnet [String, required]: subnet ID created in AWS

	◎  Example of manual network:
	
		networks:
		- name: default
		  type: manual
		
		  subnets:
		  - range:   10.10.0.0/24
		    gateway: 10.10.0.1
		    cloud_properties:
		      subnet: subnet-9be6c3f7
	
	◎ Example of dynamic network:
	
		networks:
		- name: default
		  type: dynamic
		  cloud_properties:
		    subnet: subnet-9be6c6gh
	
	◎ Example of vip network:
	
		networks:
		- name: default
		 OpenStack Example
	'cloud_properties' schema used by Dynamic network or manual network subnets
	net_id [String, required]:  subnet ID created in OpenStack example) net-b98ab66e-6fae-4c6a-81af-566e630d21d1
	security_groups [Array, optional]: security groups applied to network configuration.
	
	
	◎ Example of manual network:
	
		networks:
		- name: default
		  type: manual
		
		  subnets:
		  - range:   10.10.0.0/24
		    gateway: 10.10.0.1
		    cloud_properties:
		      net_id: net-b98ab66e-6fae-4c6a-81af-566e630d21d1
		      security_groups: [my-sec-group]
	
	◎ Example of dynamic network:
	
		networks:
		- name: default
		  type: dynamic
		  cloud_properties:
		    net_id: net-b98ab66e-6fae-4c6a-81af-566e630d21d1
	
	◎ Example of vip network:
	
		networks:
		- name: default
		  type: vip
		  cloud_properties: {}

◎ vSphere Example
Schema 'cloud_properties' used by the manual network subnet
name [String, required]: network name used in vSphere

	Example of manual network:
	
		networks:
		- name: default
		  type: manual
		
		  subnets:
		  - range:   10.10.0.0/24
		    gateway: 10.10.0.1
		    cloud_properties:
		      name: VM Network

Refer:vSphere CPI does not support dynamic or vip networks.

◎ vCloud Example
Schema 'cloud_properties' used by the manual network subnet
name [String, required]: Name of the network created by the vApp

	Example of manual network:
	
	networks:
	- name: default
	  type: manual
	
	  subnets:
	  - range:   10.10.0.0/24
	    gateway: 10.10.0.1
	    cloud_properties:
	      name: VPC_BOSH

Refer:vCloud CPI does not support dynamic or vip networks.

4. Resource Pools Block
resource_pools [Array, required]: Specify the resource pools to be used during deployment and the name must be unique if multiple resource pools are used  
Resource pools: Collection of virtual machines created from the same stemcell  
name [String, required]: Unique resource pool name  
network [String, required]: Network name declared by Networks block  
size [Integer, optional]: The number of virtual machines in the resource pool, and if you omit the value, it becomes the total number of job instances. An error occurs when the value input is lesser than the jobs instance    
stemcell [Hash, required]: stemcell information created in the resource pool virtual machine
- name [String, required]: stemcell name
- version [String, required]: stemcell version
cloud_properties [Hash, required]: Describes a specific property of IaaS required to create a compilation VM (instance_type, availability_zone)
env [Hash, optional]: When calling create_stemcell from the cloud provider interface (CPI), it is a virtual machine environment variable and the env data is available on the BOSH Agents set on the virtual machine. Default is {}

◎ AWS Example
instance_type [String, required]: Instance type example) m1.small   
availability_zone [String, required]: availability zone to create an instance example) us-east-1a   
key_name [String, optional]: key pair name. the default key pair name is global CPI within the default_key_name settings example) bosh  
spot_bid_price [Float, optional]:Bid price in dollars example) 0.03  
ephemeral_disk [Hash, optional]: EBS backed ephemeral disk of custom size for when instance storage is not large enough or not available for selected instance type
- size [Integer, required]: megabytes disk size
- type [String, optional]: disk type(standard, gp2). Default standard
  - standard stands for EBS magnetic drives
  - gp2 stands for EBS general purpose drives (SSD)

	resource_pools:
	- name: default
	  network: default
	  stemcell:
	    name: bosh-aws-xen-ubuntu-trusty-go_agent
	    version: latest
	  cloud_properties:
	    instance_type: m1.small
	    availability_zone: us-east-1a

◎ OpenStack Example
instance_type [String, required]: instance type example) m1.small
availability_zone [String, required]: availability zone to create an instance example) us-east-1a
key_name [String, optional]: key pair name. the default key pair name is global CPI within the default_key_name settings example) bosh
scheduler_hints [Hash, optional]: Data passed to the OpenStack Filter scheduler to influence its decision where new VMs can be placed example) { group: af09abf2-2283... }


	resource_pools:
	- name: default
	  network: default
	  stemcell:
	    name: bosh-openstack-kvm-ubuntu-trusty-go_agent
	    version: latest
	  cloud_properties:
	    instance_type: m1.small
	    availability_zone: us-east-1a

◎ vSphere Example
cpu [Integer, required]: Number of CPUs  
ram [Integer, required]: RAM in megabytes  
disk [Integer, required]: Ephemeral disk size in megabytes.  
datacenters [Array, optional]: Use an array of datacenters regardless of VM location; it must have one and match the datacenter configured in the global CPI option.  
-	name [String, required]: Datacenter name  
-	clusters [Array, required]: Array of clusters available regardless of VM location  
-	<cluster name> [String, required]: Cluster name  
-	drs_rulers [Array, optional]: The array of DRS rules is applied to restrict VM placement. Only one exists    
-	name [String, required]: DRS rule name that Director will create    
-	type [String, required]: DRS rule type. Currently, only separate_vms is being provided  

	resource_pools:
	- name: default
	  network: default
	  stemcell:
	    name: bosh-vsphere-esxi-ubuntu-trusty-go_agent
	    version: latest
	  cloud_properties:
	    cpu: 2
	    ram: 1_024
	    disk: 10_240

◎ vCloud Example
cpu [Integer, required]: Number of CPUs  
ram [Integer, required]: RAM in megabytes  
disk [Integer, required]: Ephemeral disk size in megabytes  

	resource_pools:
	- name: default
	  network: default
	  stemcell:
	    name: bosh-vcloud-esxi-ubuntu-trusty-go_agent
	    version: latest
	  cloud_properties:
	    cpu: 2
	    ram: 1_024
	    disk: 10_240

5. Disk Pools Block
disk_pools [Array, required]: Specifies the disk pools used during deployment, and if multiple disk pools are used, the name must be a unique identifier  
disk pools: Logical collection of disks. Created with the same settings    
name [String, required]: unique disk pool name  
disk_size [Integer, required]: Specify the disk size and display it as an integer value  
cloud_properties [Hash, required]: Specific properties of IaaS required to create disks (type, iops)  

◎ AWS Example
	type [String, optional]: disk type(standard, gp2). default is standard  
	- standard stands for EBS magnetic drives  
	- gp2 stands for EBS general purpose drives (SSD)  
	encrypted [Boolean, optional]: Turns on EBS volume encryption for this persistent disk. Root and temporary disks are not encrypted. The default is false.  
	
	disk_pools:
	- name: default
	  disk_size: 10_240
	  cloud_properties:
	    type: m1.small

◎ OpenStack Example
	type [String, optional]: OpenStack Settings Volume Type example) SSD
	
	disk_pools:
	- name: default
	  disk_size: 10_240
	  cloud_properties:
	    type: SSD

◎ vSphere Example
	Currently, cloud properties for disks are not provided
	
	disk_pools:
	- name: default
	  disk_size: 10_240
	  cloud_properties: {}

◎ vCloud Example
	Currently, cloud properties for disks are not provided
	
	disk_pools:
	- name: default
	  disk_size: 10_240
	  cloud_properties: {}

6. Compilation Block
compilation [Hash, required]: Virtual Machine Properties Required When Compiling  
workers [Integer, required]: Maximum number of virtual machines to compile  
network [String, required]: Network name declared by Networks block   
reuse_compilation_vms [Boolean, optional]: In the case of false, BOSH creates a new compilation VM for each new package compilation and deletes the VM when modifying is completed. In the case of true, reuse it. The default is false  
cloud_properties [Hash, required]: Specific properties of IaaS required to create a compilation VM (instance_type, availability_zone)   

◎ Example
	compilation:
	workers: 2
	network: default
	  reuse_compilation_vms: true
	  cloud_properties:
	  instance_type: c1.medium
	  availability_zone: us-east-1c

7. Update Block  
update [Hash, required]: Defines update properties, which control how BOSH updates the job instance during deployment  
canaries [Integer, required]: number of canary instances  
canary: Update instance to canary instance first and stop deployment in case of error  
canary_watch_time [Integer or Range, required]: stand by time for the canary instance to perform  
update_watch_time [Integer or Range, required]: stand by time for a non-canary instance to perform  
max_in_flight [Integer, required]: Maximum number of non-canary instances updating in parallel  

◎ Example
	update:
	canaries: 1
	max_in_flight: 10
	canary_watch_time: 1000-30000
	  update_watch_time: 1000-30000

8. Jobs Block
jobs [Array, required]: Specifies the mapping between BOSH release jobs and cloud instances, and Jobs is specified in BOSH release, and Jobs block defines how BOSH creates and drives virtual machines by IaaS  
name [String, required]: unique name   
templates [Array, required]: job template information of release    
	- name [String, required]: job template name  
	- release [String, required]: The release name where the job template exists  
instances [Integer, required]: Number of job instances; each instance is a VM running this particular job  
resource_pool [String, required]: Resource pool name defined in Resource Pools block    
networks [Array, required]: defines network  
	- name [String, required]: Network name defined in Networks block  
	- static_ips [Array, optional]: defines IP addresses to use  
	- default [Array, optional]: Specify network components (DNS, Gateway)
persistent_disk [Integer, optional]: Defining a Persistent Disk Size  
update [Hash, optional]: Set up specific updates for the job   
properties [Hash, optional]: Specify job properties  

◎ Example
	- name: redis-master
	  instances: 1
	  templates:
	  - {name: redis-server, release: redis}
	  persistent_disk: 10_240
	  resource_pool: redis-servers
	  networks:
	  - name: default
	
	- name: redis-slave
	  instances: 2
	  templates:
	  - {name: redis-server, release: redis}
	  persistent_disk: 10_240
	  resource_pool: redis-servers
	  networks:
	  - name: default

9. Properties Block
Describe global properties and general configuration information  
Global properties are available without restriction  
  - Passwords, Account names, Shared secrets, Hostnames, IP addresses, port numbers, max_connections, etc.

◎ Example
	properties:
	  redis:
	    max_connections: 10

### <a name="41"/>5. Deploy Guide
To deploy software through the BOSH deployment CLI, the system cell and release to be used in the deployment manifestyml must be uploaded first, and the deployment manifestyml file must be written. 
Check if the stemcell and release are uploaded properly. The BOSH CLI to check is ‘bosh stemcells’ and ‘bosh releases’.
Provides the bosh deploy CLI command which deploys Software(servicepack software). 

Proceed to deployment with the procedures below.

1. Save the deployment manifest file to deploy at the BOSH.(bosh deployment CLI)
![openpaas-servicepack-46]
 
2. Deploy Software. (bosh deploy CLI)
![openpaas-servicepack-47]
 
3. Check the deployed software. (bosh vms)
![openpaas-servicepack-48]

 [openpaas-servicepack-01]:./images/openpaas-servicepack/openpaas-servicepack-01.PNG
 [openpaas-servicepack-02]:./images/openpaas-servicepack/openpaas-servicepack-02.PNG
 [openpaas-servicepack-03]:./images/openpaas-servicepack/openpaas-servicepack-03.PNG
 [openpaas-servicepack-04]:./images/openpaas-servicepack/openpaas-servicepack-04.PNG
 [openpaas-servicepack-05]:./images/openpaas-servicepack/openpaas-servicepack-05.PNG
 [openpaas-servicepack-06]:./images/openpaas-servicepack/openpaas-servicepack-06.PNG
 [openpaas-servicepack-07]:./images/openpaas-servicepack/openpaas-servicepack-07.PNG
 [openpaas-servicepack-08]:./images/openpaas-servicepack/openpaas-servicepack-08.PNG
 [openpaas-servicepack-09]:./images/openpaas-servicepack/openpaas-servicepack-09.PNG
 [openpaas-servicepack-10]:./images/openpaas-servicepack/openpaas-servicepack-10.PNG
 [openpaas-servicepack-11]:./images/openpaas-servicepack/openpaas-servicepack-11.PNG
 [openpaas-servicepack-12]:./images/openpaas-servicepack/openpaas-servicepack-12.PNG
 [openpaas-servicepack-13]:./images/openpaas-servicepack/openpaas-servicepack-13.PNG
 [openpaas-servicepack-14]:./images/openpaas-servicepack/openpaas-servicepack-14.png
 [openpaas-servicepack-15]:./images/openpaas-servicepack/openpaas-servicepack-15.png
 [openpaas-servicepack-16]:./images/openpaas-servicepack/openpaas-servicepack-16.png
 [openpaas-servicepack-17]:./images/openpaas-servicepack/openpaas-servicepack-17.png
 [openpaas-servicepack-18]:./images/openpaas-servicepack/openpaas-servicepack-18.png
 [openpaas-servicepack-19]:./images/openpaas-servicepack/openpaas-servicepack-19.png
 [openpaas-servicepack-20]:./images/openpaas-servicepack/openpaas-servicepack-20.PNG
 [openpaas-servicepack-21]:./images/openpaas-servicepack/openpaas-servicepack-21.PNG
 [openpaas-servicepack-22]:./images/openpaas-servicepack/openpaas-servicepack-22.PNG
 [openpaas-servicepack-23]:./images/openpaas-servicepack/openpaas-servicepack-23.PNG
 [openpaas-servicepack-24]:./images/openpaas-servicepack/openpaas-servicepack-24.PNG
 [openpaas-servicepack-25]:./images/openpaas-servicepack/openpaas-servicepack-25.PNG
 [openpaas-servicepack-26]:./images/openpaas-servicepack/openpaas-servicepack-26.PNG
 [openpaas-servicepack-27]:./images/openpaas-servicepack/openpaas-servicepack-27.PNG
 [openpaas-servicepack-28]:./images/openpaas-servicepack/openpaas-servicepack-28.PNG
 [openpaas-servicepack-29]:./images/openpaas-servicepack/openpaas-servicepack-29.PNG
 [openpaas-servicepack-30]:./images/openpaas-servicepack/openpaas-servicepack-30.PNG
 [openpaas-servicepack-31]:./images/openpaas-servicepack/openpaas-servicepack-31.PNG
 [openpaas-servicepack-32]:./images/openpaas-servicepack/openpaas-servicepack-32.PNG
 [openpaas-servicepack-33]:./images/openpaas-servicepack/openpaas-servicepack-33.png
 [openpaas-servicepack-34]:./images/openpaas-servicepack/openpaas-servicepack-34.png
 [openpaas-servicepack-35]:./images/openpaas-servicepack/openpaas-servicepack-35.png
 [openpaas-servicepack-36]:./images/openpaas-servicepack/openpaas-servicepack-36.png
 [openpaas-servicepack-37]:./images/openpaas-servicepack/openpaas-servicepack-37.png
 [openpaas-servicepack-38]:./images/openpaas-servicepack/openpaas-servicepack-38.png
 [openpaas-servicepack-39]:./images/openpaas-servicepack/openpaas-servicepack-39.png
 [openpaas-servicepack-40]:./images/openpaas-servicepack/openpaas-servicepack-40.png
 [openpaas-servicepack-41]:./images/openpaas-servicepack/openpaas-servicepack-41.png
 [openpaas-servicepack-42]:./images/openpaas-servicepack/openpaas-servicepack-42.png
 [openpaas-servicepack-43]:./images/openpaas-servicepack/openpaas-servicepack-43.png
 [openpaas-servicepack-44]:./images/openpaas-servicepack/openpaas-servicepack-44.png
 [openpaas-servicepack-45]:./images/openpaas-servicepack/openpaas-servicepack-45.png
 [openpaas-servicepack-46]:./images/openpaas-servicepack/openpaas-servicepack-46.png
 [openpaas-servicepack-47]:./images/openpaas-servicepack/openpaas-servicepack-47.png
 [openpaas-servicepack-48]:./images/openpaas-servicepack/openpaas-servicepack-48.png


### [Index](https://github.com/K-PaaS/Guide-eng/blob/master/README.md) > [AP User Guide](../README.md) > Servicepack Development
