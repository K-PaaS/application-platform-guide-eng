### [Index](https://github.com/PaaS-TA/Guide-eng/blob/master/README.md) > [AP User Guide](../README.md) > Java Service Metering Development

## Table of Contents

1. [Outline](#1)
	* [Document Outline](#2)
	 * [Purpose](#3)
	 * [Range](#4)
	 * [References](#5)

2. [JAVA API Service Metering Development Guide](#6)
    * [Outline](#7)
    * [Configuring the Development Environment](#8)
    * [Service Broker Library](#9)
     * [What is Service Broker Library?](#10)
     * [Download the service broker library and import the project](#11)
     * [Files added or modified for metering in the service broker library](#12)
     * [ServiceInstanceBindingController](#13)
     * [ServiceInstanceBinding](#14)
     * [SampleMeteringReportService  Abstract Class](#15)
     * [SampleMeteringOAuthService  Abstract Class](#16)
    * [Service Broker Library](#17)
     * [mongo-db Service Broker API](#18)
     * [mongo-db Service Broker API Download](#19)
     * [Files that are added and modified in the mongo-db Service Broker API](#20)
     * [Add dependency for gradle build](#21)
     * [application-mvc.properties Settings](#22)
     * [datasource.properties Settings](#23)
     * [MongoServiceInstanceBindingService Implementation Body](#24)
     * [SampleMeteringOAuthService Implementation](#25)
     * [SampleMeteringReportService Implementation](#26)
    * [Metering/Rating/Billing Policy](#27)
     * [Metering Policy](#28)
     * [Rating Policy](#39)
     * [Billing Policy](#30)
     * [Register Policy](#31)
    * [Deployment](#32)
     * [Login to PaaS-TA Platforn](#33)
     * [Create mongo-db Service Broker](#34)
     * [API Service Interworking Sample Application Deployment and Service Connection](#35)
    * [Service Binding CF-Abacus Interworking Test](#36)
    * [Unit Test](#37)
    * [Sample Code](#38)


# <div id='1'/>1.  Outline
## <div id='2'/> 1.1 Document Outline
### <div id='3'/>1.1.1.  Purpose


This document (Java Service Broker Metering Application Development Guide) describes how to meter CF (Cloud Foundry) services by adding metering services to service brokers in PaaS-TA platform projects.


### <div id='4'/>1.1.2.  Range

The range of this document is limited to the development of Cloud Foundry JAVA service broker application metering and CF-Abacus linkage in the Pas-Ta platform project.
For service broker API development, refer to the service broker API development guide provided separately.

This document describes the development environment of Ubuntu 14.04 ver.

This document describes the development environment in which the Mongo-db service pack is installed.

Mongo-db service pack installation shall be performed by referring to the Mongo-DB installation document.


Mongo-db service pack installation shall be performed by referring to the Mongo-DB installation document.
(For cf-abacus installation, refer to the Abacus installation guide provided separately.)

## <div id='5'/>1.3.  References

-   **[https://docs.cloudfoundry.org/devguide/](https://docs.cloudfoundry.org/devguide/)**
-   **[http://cli.cloudfoundry.org/ko-KR/cf/](http://cli.cloudfoundry.org/ko-KR/cf/)**
-   **[https://github.com/cloudfoundry-community/spring-boot-cf-service-broker/](https://github.com/cloudfoundry-community/spring-boot-cf-service-broker)**
-   **[https://github.com/cloudfoundry-incubator/cf-abacus](https://github.com/cloudfoundry-incubator/cf-abacus)**


# <div id='6'/>2.  Java Service Metering Development Guide


## <div id='7'/>2.1.  Outline


CF Services is used in open cloud platforms by implementing a cloud controller client API called the Service Broker API.
The Services API is a version of the independent cloud controller API.
This makes external applications available on the platform. (database, message queue, rest endpoint, etc.)

The open cloud platform service API is a protocol (catalog, provision, de-provision, update provision plan, bind, unbound) between Cloud Controller and Service Broker, which is implemented as a RESTful API and registered with Cloud Controller.

When implementing metering for a service, select a process that fits the service policy and purpose of these conventions and link metering to that process.

This development guide guides how to measure bind and unbind time using the mongo-db service as an example.

When binding an API service with an application intended to use the service, the binding information is obtained using the application environment information (orgguid, spaceguid, appguid, metering planid) applied to CF CLI binding request, and a metering service function is added to the mongo-db service.


Service Broker API Architecture

![Java_Service_Metering_Image01]

![Java_Service_Metering_Image02]

<table>
  <tr>
    <th colspan ="2">function</th>
     <th>description</th>
  </tr>
  <tr>
     <td rowspan="4">Runtime</td>
     <td>metering/rating/billing policy</td>
     <td>Various policy definition information for services provided by the service provider. It is in JSON format, and when the policy is registered with CF-ABACUS, the service usage is aggregated according to the policy defined.
The policy must be defined by the service provider, for JSON schema refer to: <br>
<a href = "https://github.com/cloudfoundry-incubator/cf-abacus/blob/master/doc/api.md" >https://github.com/cloudfoundry-incubator/cf-abacus/blob/master/doc/api.md
</a>
</td>
  </tr>
   <tr>
     <td width="160px">Service Broker API</td>
     <td>Process service create, delete, update, bind, and unbind between Cloud Controller and Service. In this document, a metering service is developed and added to the Mongo-db service broker API.
</td>
  </tr> 
  <tr>
     <td>Service Broker <br>Metering Service 
</td>
     <td>Service that the service broker API sends service usage information to the abacus-usage-collector (this is the development area to guide in this document)</td>
  </tr> 
   <tr>
     <td>Service</td>
     <td>Service capabilities provided by service provider</td>
  </tr> 
  <tr>
  	<td colspan ="2">CF-ABACUS</td>
    <td>Aggregates usage information collected as a CF-ABACUS core function.<br>
CF-ABACUS is installed in the form of a micro-service in CF after CF installation. Check below for details.<br>
<a href = "https://github.com/cloudfoundry-incubator/cf-abacus" >https://github.com/cloudfoundry-incubator/cf-abacus</a>
</td>
  </tr>
</table>                


※ This development guide only describes ***Development of functions that meter services that can determine when applications and services are bound as the start of service use**.

※ ***It does not describe the development of metering functions for specific API calls for services and the use of specific resources for services.***
※ Refer to the API Service Metering Development Guide for metering of API calls.

※ Refer to the linked site for the development or installation of other components.


## <div id='8'/>2.2.  Configure Development Environment


The following environment is premised on the development environment for service metering development.

-   CF release: v226 and above (Test in a bosh-lite installation environment)
-   gradle 2.14
-   java version "1.8.0_101"
-   springBootVersion: 1.3.0. BUILD-SNAPSHOT
-   mongo-db service broker 2.5 (Target Service Broker Project to Add Metering Services)
-   springBootCfServiceBrokerVersion "2.5.0" (Service Broker Library)
-   Spring Tool Suite or Eclipse


## <div id='9'/>2.3.  Service Broker Library



### <div id='10'/>2.3.1.  What is Service Broker Library?


In CF (Open Platform), various services can be serviced on the platform.<br>
As each of these services is developing its service broker, enables the applications to use the service in CF (open platform).<br>
The services vary, but the RESTAPI of the open platform for using the services is predetermined.<br>
The Service Broker Library is a library that allows different service brokers to provide services based on the REST API of this open platform by adding the service broker library Jar file to the build path and implementing abstraction classes.

In this guide, implementing metering services in the mongo-db service broker, an abstraction class for metering is added to this service broker library, and then the mongo-db service broker builds this library using dependency.


### <div id='11'/>2.3.2. Download the service broker library and import the project.

#### 1.  Download the service broker source that is being provided as an open-source using git clone.<br> 

**[https://github.com/cloudfoundry-community/spring-boot-cf-service-broker/tree/master/src/main/java/org/cloudfoundry/community/servicebroker/controller](https://github.com/cloudfoundry-community/spring-boot-cf-service-broker/tree/master/src/main/java/org/cloudfoundry/community/servicebroker/controller)**
  
	$ git clone https://github.com/cloudfoundry-community/spring-boot-cf-service-broker.git
	Cloning into 'spring-boot-cf-service-broker'...
	remote: Counting objects: 2394, done.
	remote: Total 2394 (delta 0), reused 0 (delta 0), pack-reused 2394
	Receiving objects: 100% (2394/2394), 351.72 KiB | 279.00 KiB/s, done.
	Resolving deltas: 100% (939/939), done.
	Checking connectivity... done.


Import downloaded sources into Java development tools Eclipse and Spring Tool Suite.

After adding the Gradle plug-in to the Eclipse, importing the gradle makes development easier.


### <div id='12'/>2.3.3.  Files added or modified for metering in the service broker library


| 　　  |Java class | Description|
|---------|---|----|
|    Modify     | ServiceIncetanceBindingController  | A controller that processes the service binding request of the cloud controller.<br> Obtain the uaatoken from Sample MeteringOuthService and add the process of calling with the parameters of Sample MeteringReportService.   |
|    Modify     | ServiceInstanceBinding  | When the service-binding request is processed by the ServiceIncidenceBinding Controller, report the usage report to the abacus-usage-collector with metering applied to the binding connection.   |     
|    Add     | SampleMeteringReportService  | SampleMeteringReportService abstracted interface with no information related to metering/rating/charging policies. This is an abstraction class provided for service providers to implement this interface to apply to service implementations. This is an abstraction class provided for service providers to implement this interface to apply to service implementations.<br>SampleMeteringReportService abstracted interface with no information related to metering/rating/charging policies. This is an abstract class that is provided so that service providers who will implement this interface can apply it to service implementations.|     
|    Add     | SampleMeteringOAuthService  | It is an abstraction class for obtaining an access token to the abacus-usage-collector from a UAA server on an open platform and delivering the token to the SampleMeteringReportService.   |


The appearance of files added or modified for metering in the service broker library

![Java_Service_Metering_Image04]

### <div id='13'/>2.3.4.  ServiceInstanceBindingController

In the bindServiceInstance process, add the process of obtaining the uaa token from  SampleMeteringOAuthServiceand calling with parameters from SampleMeteringReportService.

	@RequestMapping (value = BASE_PATH + "/{bindingId}", method = RequestMethod.PUT)
	public ResponseEntity<ServiceInstanceBindingResponse> bindServiceInstance (
			@PathVariable("instanceId") String instanceId, 
			@PathVariable("bindingId") String bindingId,
			@Valid @RequestBody CreateServiceInstanceBindingRequest request) throws
			ServiceInstanceDoesNotExistException, ServiceInstanceBindingExistsException, 
			ServiceBrokerException {
		ServiceInstance instance = serviceInstanceService.getServiceInstance(instanceId);
		if (instance == null) {
			throw new ServiceInstanceDoesNotExistException(instanceId);
		}
		ServiceInstanceBinding binding = serviceInstanceBindingService.createServiceInstanceBinding(			request. withServiceInstanceId(instanceId).and (). withBindingId(bindingId));
	
		if (binding == null) {
			throw new ServiceBrokerException(bindingId);
		}
		
		String uaaToken = sampleMeteringOAuthService.getUAAToken();
		
		try {
			int httpStatus = sampleMeteringReportService.reportServiceInstanceBinding(binding, uaaToken); 
			if (httpStatus == 400) {
				throw new ServiceBrokerException(bindingId);
			} 	
		} catch (ServiceBrokerException e) {
			throw e;
		}
		
		logger. debug ("ServiceInstanceBinding Created: " + binding. getId());
	return new ResponseEntity<ServiceInstanceBindingResponse>(
			new ServiceInstanceBindingResponse(binding), 
			binding.getHttpStatus());
	}


### <div id='14'/>2.3.5.  ServiceInstanceBinding 

Add the environment information field of the application to be bound to implement the metering service to the Service InstanceBinding. The added fields will be passed to the mongo-db repository after mapping the field values of the service binding request parameter in the implementation of the Service Instance Binding Service. Gradle builds the library.

	package org.openpaas.servicebroker.model;
	
	import java.util.HashMap;
	import java.util.Map;
	import org.springframework.http.HttpStatus;
	import com.fasterxml.jackson.annotation.JsonIgnore;

	public class ServiceInstanceBinding {
	
	private String id;
	private String serviceInstanceId;
	private Map<String,Object> credentials = new HashMap<String,Object>();
	private String syslogDrainUrl;
	// Field used in metering
	private String appGuid;
	
	// Field added for metering
	private String appOrganizationId;
	private String appSpaceId;
	private String meteringPlanId;
	
	@JsonIgnore
	private HttpStatus httpStatus = HttpStatus.CREATED;
	public ServiceInstanceBinding (String id, 
			String serviceInstanceId, 
			Map<String,Object> credentials,
			String syslogDrainUrl, String appGuid,
			String appOrganizationId,
			String appSpaceId,
			String meteringPlanId			
			) {
		this.id = id;
		this.serviceInstanceId = serviceInstanceId;
		setCredentials(credentials);
		this.syslogDrainUrl = syslogDrainUrl;
		this.appGuid = appGuid;
		
		this.appOrganizationId = appOrganizationId;		
		this.appSpaceId = appSpaceId;		
		this.meteringPlanId = meteringPlanId;
	}


### <div id='15'/>2.3.6.  SampleMeteringOAuthService  Abstract Class

UAA OAuthToken is needed to access abacus-collector RESTAPI if Abacus operates as Secured.<br>
The class that inherits the SampleMeteringOAuthServicemust implement the process of acquiring and returning UAA OAuthToken.

	package org.openpaas.servicebroker.service;
	import org.openpaas.servicebroker.exception.ServiceBrokerException;
	
	public interface SampleMeteringOAuthService {
	String getUAAToken() throws ServiceBrokerException;
	}


### <div id='16'/>2.3.7.  SampleMeteringReportService  Abstract Class
The class that inherits the SampleMeteringReportServicemust implements the process of sending the event information to the abacus-collector and returning the status code (HTTP status code) for the processing when processing the create binding request and delete binding request.

	package org.openpaas.servicebroker.service;
	import org.openpaas.servicebroker.exception.ServiceBrokerException;
	import org.openpaas.servicebroker.model.ServiceInstanceBinding;
	public interface SampleMeteringReportService {
	int reportServiceInstanceBinding(ServiceInstanceBinding serviceInstanceBinding, 
			String uaaToken)
			throws ServiceBrokerException;
	
	int reportServiceInstanceBindingDelete(ServiceInstanceBinding serviceInstanceBinding, 
			String uaaToken)
			throws ServiceBrokerException;	
	
	}




## <div id='17'/>2.4.  Service Broker Library

### <div id='18'/>2.4.1.  mongo-db Service Broker API

The service broker library has been modified to prepare abstraction classes and model objects for metering. 
From now on, we will describe implementing metering in the mongo-db service broker API that implements the service broker library.

### <div id='19'/>2.4.2.  mongo-db Service Broker API Download

The mongo-db service broker API uses a separate Zip file package.

### <div id='20'/>2.4.3.  Files that are added and modified in the mongo-db Service Broker API

| 　　|Type | Necessity|
|---------|---|----|
|   Modify      |build.gradle   |  Build setting file<br>Add the required dependency to create the metering usage object.|     
|   Modify      | application-mvc.properties  | Map the information in the service binding request.<br>Add an environmental information field of the application to be bound to implement the metering service.|     
|   Modify      | datasource. properties   | Mongo-db Service Information   |     
|   Modify     | MongoServiceInstanceBindingService  |Add the process of mapping the metering information received by the service broker binding request parameter to the service instance binding.    |     
|   Add      | SampleMeteringReportServiceImpl  | Implement SampleMeteringReportService.   |     
|   Add     |SampleMeteringOAuthServiceImpl   | Implement SampleMeteringOAuthService.   |     
|   Modify     |Manifest.yml   | Describes the configuration information required when distributing the app to CF and the configuration information required for the app execution environment.   |


### <div id='21'/>2.4.4.  Add dependency for gradle build
Apply service broker library mongo-db service broker jar file

![Java_Service_Metering_Image03]

Gradle builds the service broker library.

	@openpaas-service-broker/openpaas-service-java-broker$ gradle build -x test
	:compileJava
	:processResources UP-TO-DATE
	:classes
	:findMainClass
	:jar
	:bootRepackage
	:assemble
	:check
	:build
	
	BUILD SUCCESSFUL
	
	Total time: 58.845 secs



When Build is succeeded, /openpaas-service-java-broker/build/libs/openpaas-service-java-broker.jar gets created.

Copy the jar file to the /openpaas-service-java-broker-mongo/libs path of the mongo-db service broker and add a dependency to the mongo-db service broker gradle build file.


The dependencies portion of the mongo-db service broker build.gradle file

	dependencies {
	    // Service Broker Library 
	    compile files('libs/openpaas-service-java-broker.jar')
	    // Metering usage object generating dependency
	    compile("org.json:json:20160212")
	    compile("com.googlecode.json-simple:json-simple:1.1")
	
	    providedRuntime("org.springframework.boot:spring-boot-starter-tomcat:${springBootVersion}")
	    compile("org.springframework.boot:spring-boot-starter-web:${springBootVersion}")
	    compile("org.springframework.boot:spring-boot-starter-security:${springBootVersion}")
	    	//testCompile("org.cloudfoundry:spring-boot-cf-service-broker-tests:${springBootCfServiceBrokerVersion}")
	    testCompile("org.springframework.boot:spring-boot-starter-test:${springBootVersion}")
	    testCompile("com.jayway.jsonpath:json-path:${jsonPathVersion}")
	    testCompile("org.apache.httpcomponents:httpclient:4.4.1")
	    
	    testCompile("org.powermock:powermock-mockito-release-full:1.6.1")    
		compile("org.apache.commons:commons-dbcp2")    
	    compile("org.springframework.boot:spring-boot-starter-data-mongodb:${springBootVersion}")
	}


### <div id='22'/>2.4.5.  application-mvc.properties Settings

	# The address of abacus usage collector RESTAPI
	abacus.collector: https://abacus-usage-collector.<PaaS-TA Domain> /v1/metering/collected/usage
	# If the abacus usage collector is in secured mode, then true / if not, false
	abacus.secured: true
	# uaa server of the Open Platform
	uaa.server: https://uaa.<PaaS-TA Domain>
	# abacus usage collector RESTAPI use authority (Register at UAA server ahead of time.)
	uaa.client.id: abacus-linux-container
	uaa.client.secret: secret
	uaa.client.scope: abacus.usage.linux-container.write,abacus.usage.linux-container.read 


Refer to uaa ****UAA****Account Registration** for **Secured Abacus**** in the separate **abacus****Installation Guide** regarding on how to set up the uaa account.


### <div id='23'/>2.4.6.  datasource.properties Settings

	# Set by referring to the Mongo-DB service deployment manifest file.
	mongodb.hosts = 10.244.14.2, 10.244.14.14, 10.244.14.26
	mongodb.port = 27017
	mongodb.dbName = mongo-broker
	mongodb.userName = root
	mongodb.authSource = admin
	mongodb.password = openpaas

### <div id='24'/>2.4.7.  MongoServiceInstanceBindingService Implementation Body

When requesting the service broker binding CLI, the application environment information is input through the parameter object.
Map this information to the ServiceInstanceBinding in the metering field.

-   Request an example of a service broker binding CLI 

  	    $ cf bind-service sample-api-node-caller mongod_service -c 
		'{"app_organization_id":"test05","app_space_id":"testspaceId","metering_plan_id":"standard"}'


Map information transferred to parameters to a Service InstanceBinding object to store in mongo-db. 
After storing through the mongo-db repository, return the bound information.
	
	// Acquire metering-related information input by parameter and map it to ServiceInstanceBinding.
	Map<String, Object> paraMap = request.getParameters();
	String appOrganizationId = (String) paraMap.get("app_organization_id");
	String appSpaceId = (String) paraMap.get("app_space_id");
	String meteringPlanId = (String) paraMap.get("metering_plan_id");
	binding = new ServiceInstanceBinding(request.getBindingId(), request.getServiceInstanceId(), credentials, null,request.getAppGuid(), appOrganizationId, appSpaceId, meteringPlanId);
	
	repository.save(binding);
	
	return binding;


### <div id='25'/>2.4.8.  SampleMeteringOAuthService Implementation

Information for obtaining a UAA token from the UAA server in application-MVC. properties are called to the class.

	@Component
	@Service
	public class SampleMeteringOAuthServiceImpl implements SampleMeteringOAuthService {
		@Value("${uaa.server}")
		String authServer;
		
		@Value("${uaa.client.id}")
		String clientId;
		
		@Value("${uaa.client.secret}")
		String clientSecret;
		
		@Value("${uaa.client.scope}")
		String scope;
		
		@Value("${abacus.secured}")
		String abacusSecured;



SampleMeteringOAuthServiceImpl implements SampleMeteringOAuthService.

SampleMeteringOAuthServiceImpl creates an https connection and requests a token from the UAA server.
At this time, the blank ({}) or token is returned according to toabacusSecured (Whether the abacus-collector is set to secure).

	@Override
	public String getUAAToken() throws ServiceBrokerException {
		
	if(!SECURED.equals(abacusSecured)){
		return "";
	} else {
		String authToken = "";		
		StringBuffer sb = new StringBuffer();
		
		HttpsURLConnection conn = (HttpsURLConnection) getConnetionUAA();
	        conn.setRequestMethod("GET");
	        conn.setDoInput(true);
	        String authHeader = getAuthKey(clientId, clientSecret);
	        conn.setRequestProperty("authorization", authHeader);
	
		InputStreamReader in = new InputStreamReader((InputStream) conn.getContent());
		BufferedReader br = new BufferedReader(in);
	
		String line;
		while ((line = br.readLine()) != null) {
			sb.append(line).append("\n");
		}
	
		authToken= parseAuthToken(sb.toString());
		br.close();
		in.close();
		conn.disconnect();
		return authToken;
	} 


### <div id='26'/>2.4.9.  SampleMeteringReportService Implementation

SampleMeteringReportServiceImpl creates an https connection with the uaa token obtained from SampleMeteringOAuthServiceImpland POSTs service usage information to abacus-collector.

Since the abacus-collector is preparing a process for the form to be POST according to the metering policy, JSON is generated in the form known by the abacus-collector and POST.

SampleMeteringReportServiceImpl is largely divided into two processes.


#### 1.  **Create usage information JSON by referring to the ServiceInstanceBinding Information.**

#### 2.  **Send the generated usage information JSON to abacus-collector. (HTTPS, HTTP)**


Create usage information JSON.

RESOURCE_ID linux-container and STANDARD_PLAN_ID standard are metric schemas provided by the abacus as samples.

In this guide, this metering schema is described as a metering schema for mongo-db service binding and unbinding.

The service provider must set a policy that matches the service to be provided and register the metering schema with abacus-provisioning to transmit metering to the abacus-collector. (See **metering/charging policies below for more information on policy registration**)


In the following example, the constants for metering reporting are described according to abacus' linux-container metering scheme, and PLAN_STANDARD_QUANTITY, PLAN_EXTRA_QUANTITY, etc. are arbitrarily determined. Set the corresponding item according to the service through DB or property, etc.

	// Constant for metering reports
	private static final String RESOURCE_ID = "linux-container";
	private static final int BIND = 1;
	private static final int UNBIND = 0;
	private static final String MEASURE_1 = "sample_service_usage_param1";
	private static final String MEASURE_2 = "sample_service_usage_param2";
	private static final String MEASURE_3 = "previous_sample_service_usage_param1";
	private static final String MEASURE_4 = "previous_sample_service_usage_param2";
	private static final String STANDARD_PLAN_ID = "standard";
	private static final int PLAN_STANDARD_QUANTITY = 50000000;
	private static final int PLAN_EXTRA_QUANTITY = 1000000000;
	private static final String SECURED = "true";
	
	/***************************************************
	 * @description : Creating JSON for Reports
	 * @title : buildServiceUsage
	 * @return : JSONObject
	 ***************************************************/
	public JSONObject buildServiceUsage(ServiceInstanceBinding binding, int mode) {
	
		String orgId = (String) binding.getAppOrganizationId();
		String spaceId = (String) binding.getAppSpaceId();
		String planId = (String) binding.getMeteringPlanId();
		String appId = (String) binding.getAppGuid();
	
		LocalDateTime now = LocalDateTime.now();
		Timestamp timestamp = Timestamp.valueOf(now);
	
		JSONObject jsonObjectUsage = new JSONObject();
		jsonObjectUsage.put("start", timestamp.getTime());
		jsonObjectUsage.put("end", timestamp.getTime());
		jsonObjectUsage.put("organization_id", orgId);
		jsonObjectUsage.put("space_id", spaceId);
		jsonObjectUsage.put("consumer_id", "app:" + appId);
		jsonObjectUsage.put("resource_id", RESOURCE_ID);
		jsonObjectUsage.put("plan_id", planId);
		jsonObjectUsage.put("resource_instance_id", appId);
		JSONArray measuredUsageArr = new JSONArray();
		JSONObject measuredUsage1 = new JSONObject();
		JSONObject measuredUsage2 = new JSONObject();
		JSONObject measuredUsage3 = new JSONObject();
		JSONObject measuredUsage4 = new JSONObject();
	
		int quantity = 0;
		if (STANDARD_PLAN_ID.equals(planId)) {
			quantity = PLAN_STANDARD_QUANTITY;
		} else {
			quantity = PLAN_EXTRA_QUANTITY;
		}
		if (mode == BIND) {
			measuredUsage1.put("measure", MEASURE_1);
			measuredUsage1.put("quantity", quantity);
			measuredUsageArr.put(measuredUsage1);
			measuredUsage2.put("measure", MEASURE_2);
			measuredUsage2.put("quantity", 1);
			measuredUsageArr.put(measuredUsage2);
			measuredUsage3.put("measure", MEASURE_3);
			measuredUsage3.put("quantity", 0);
			measuredUsageArr.put(measuredUsage3);
			measuredUsage4.put("measure", MEASURE_4);
			measuredUsage4.put("quantity", 0);
			measuredUsageArr.put(measuredUsage4);
		} else { // UNBIND
			measuredUsage1.put("measure", MEASURE_1);
			measuredUsage1.put("quantity", 0);
			measuredUsageArr.put(measuredUsage1);
			measuredUsage2.put("measure", MEASURE_2);
			measuredUsage2.put("quantity", 0);
			measuredUsageArr.put(measuredUsage2);
			measuredUsage3.put("measure", MEASURE_3);
			measuredUsage3.put("quantity", quantity);
			measuredUsageArr.put(measuredUsage3);
			measuredUsage4.put("measure", MEASURE_4);
			measuredUsage4.put("quantity", 1);
			measuredUsageArr.put(measuredUsage4);
		}
		jsonObjectUsage.put("measured_usage", measuredUsageArr);
		return jsonObjectUsage;
	}


**abacus-collector** **Interface List**

| Classification                 |Type             | Description                                          | Example                                           |
|-----------------------|-----------------|----------------------------------------------|------------------------------------------------|
|   start               | UNIX Timestamp  |Bind/Unbind processing start time                   |1396421450000                                   |
|   end                 | UNIX Timestamp  | Bind/Unbind Processing Response Time                 |1396421451000                                   |
|  organization_id      | String          | Organization ID of the app that called the bind request               | us-south:54257f98-83f0-4eca-ae04-9ea35277a538  |
|   space_id            |String           | Area ID of the app that called the service bind request         |d98b5916-3c77-44b9-ac12-04456df23eae            |
|  consumer_id          | String          |App ID calling service bind request                | App: d98b5916-3c77-44b9-ac12-04d61c7a4eae      |
|  resource_id          |String           |Service Resource ID                                 |linux-container                                 |
|  plan_id              |String           | Service Metering Plan ID                         |standard                                        |
|  resource_instance_id | String          |App ID that invoked the bind request                      | d98b5916-3c77-44b9-ac12-04d61c7a4eae            |
|  measured_usage       | Array           | Metering List                                   | -                                         |
|   measure             | String          | Metering Target Name                                |sample_service_usage_param1                     |
|  quantity             |Number           |  Example of service usage is memory usage (byte)      |1000000000                                       |


※ Example of JSON conversion

	{  
	   "consumer_id":"app:d98b5916-3c77-44b9-ac12-04d61c7a4eae ",
	   "resource_instance_id":"d98b5916-3c77-44b9-ac12-04d61c7a4eae ",
	   "organization_id":" us-south:54257f98-83f0-4eca-ae04-9ea35277a538",
	   "measured_usage":[  
	      {  
	         "measure":"sample_service_usage_param1",
	         "quantity":50000000
	      },
	      {  
	         "measure":"sample_service_usage_param2",
	         "quantity":0
	      },
	      {  
	         "measure":"previous_sample_service_usage_param1",
	         "quantity":50000000
	      },
	      {  
	         "measure":"previous_sample_service_usage_param2",
	         "quantity":1
	      }
	   ],
	   "start":1396421450000,
	   "resource_id":"linux-container",
	   "end":1396421450000,
	   "space_id":" d98b5916-3c77-44b9-ac12-04456df23eae ",
	   "plan_id":"standard"
	}



# <div id='27'/>2.5.  Metering/Rating/Billing Policy

This guide does not address examples of the development of policies because they differ from service provider to service and from metering to rating to billing policy.
However, the format applicable to CF-ABACUS will be described.


### <div id='28'/>2.5.1.  Metering Policy
Metering policy Schema Metering policy is an object in JSON format that defines the designation and aggregation method of metering targets from the collected metering information.
Service providers develop policies for services in line with the metering policy schema.


#### 1.  **Metering Policy Schema**

| Classification  |Type | Necessity| Description|
|---------|---|----|-----|
|plan_id      |  String | O   |  API Service Metering Plan ID   |
|measures     | Array  |  At least one  |  Define API service metering information collection targets   |
|    name     | String  | O   |  Metering Information Collection Target Name   |
|    unit     | String  |  O  |  Units to which metering information is collected   |
|metrics     | Array  | At least one   | Define API Service Metering Aggregation Scheme    |
|    name     |  String | O   | Metering Information Collection Target Name    |
|    unit      |  String |  O  | Units to which metering information is collected    |
|    meter     |  String | X   | Calculation or conversion expressions that apply to the collection stage for metering information    |
|    accumulate      |String   |  X  | Calculation or conversion expressions that apply to the cumulative phase for metering information    |
|    aggregate      |  String |  X  | Calculation or conversion expressions that apply to the aggregation stage for metering information    |
|    summarize      | String  |  X  | Calculation or conversion expressions that you apply when reporting metering information    |
|    title      |  String |   X | API Service Metering Title    |

#### 2.  **Metering Policy Example**

	{
	  "plan_id": "basic-linux-container",
	  "measures": [
	    {
	      name: 'sample_service_usage_param1',
	      unit: ‘SAMPLE_UNIT’
	    },
	    {
	      name: 'sample_service_usage_param2',
	      unit: ‘SAMPLE_UNIT’
	    },
	    {
	      name: 'previous_service_usage_param1',
	      unit: ‘SAMPLE_UNIT’
	    },
	    {
	      name: 'previous_service_usage_param2',
	      unit: ‘SAMPLE_UNIT’
	    } ],
	  "metrics": [
	    {
	      name: 'sample_metric',
	      unit: ‘SAMPLE_UNIT’,
	      type: 'time-based',
	
	      meter: ((m) => ({
	        previous_consuming: new BigNumber(m.previous_instance_memory || 0)
	          .div(1073741824).mul(m.previous_running_instances || 0)
	          .mul(-1).toNumber(),
	        consuming: new BigNumber(m.current_instance_memory || 0)
	          .div(1073741824).mul(m.current_running_instances || 0).toNumber()
	      })).toString(),
	
	      accumulate: ((a, qty, start, end, from, to, twCell) => {
	        // Do not accumulate usage out of boundary
	        if (end < from || end >= to)
	          return null;
	
	        const past = from - start;
	        const future = to - start;
	        const td = past + future;
	        return {
	          // Keep the consuming & since to the latest value
	          consuming: a && a.since > start ? a.consuming : qty.consuming,
	          consumed: new BigNumber(qty.consuming).mul(td)
	            .add(new BigNumber(qty.previous_consuming).mul(td))
	            .add(a ? a.consumed : 0).toNumber(),
	          since: a && a.since > start ? a.since : start
	        };
	      }).toString(),
	
	      aggregate: ((a, prev, curr, aggTwCell, accTwCell) => {
	        // Usage was rejected by accumulate
	        if (!curr)
	          return a;
	
	        const consuming = new BigNumber(curr.consuming)
	          .sub(prev ? prev.consuming : 0);
	        const consumed = new BigNumber(curr.consumed)
	          .sub(prev ? prev.consumed : 0);
	        return {
	          consuming: consuming.add(a ? a.consuming : 0).toNumber(),
	          consumed: consumed.add(a ? a.consumed : 0).toNumber()
	        };
	      }).toString(),
	
	      summarize: ((t, qty, from, to) => {
	        // no usage
	        if (!qty)
	          return 0;
	        // Apply stop on running instance
	        const rt = Math.min(t, to ? to : t);
	        const past = from - rt;
	        const future = to - rt;
	        const td = past + future;
	        const consumed = new BigNumber(qty.consuming)
	          .mul(-1).mul(td).toNumber();
	        return new BigNumber(qty.consumed).add(consumed)
	          .div(2).div(3600000).toNumber();
	      }).toString()
	    }
	  ]
	};


### <div id='29'/>2.5.2.  Rating Policy

A rating policy is an object in JSON format that defines the usage weight of each service.
The service provider develops a policy for the service in line with the rating policy schema.


#### 1.  **Rating Policy Schema**

| Classification  |Type | Necessity| Description|
|---------|---|----|-----|
|plan_id      |  String | O   |  API Service Metering Plan ID   |
|metrics     | Array  |  At least one  |  List of rating policies   |
|    name     | String  | O   |  Class Definition Destination Name   |
|    rate     | String  |  X  |  Weight calculation or conversion formula   |
|    charge     | String  | X   | Billing formula or conversion formula for usage    |
|    title     |  String | X   | Class Policy Name    |


#### 2.  **Rating Policy Example**

	{
	  "plan_id": "standard",
	  "metrics": [
	    {
	      "name": "sample_metrics"
	    },
	    {
	      "name": "sample_service_usage_param1",
	      "rate": "(p, qty) => p ? p * qty : 0",
	      "charge": "(t, cost) => cost"
	    }
	  ]
	}


### <div id='30'/>2.5.3. Billing Policy 

The billing policy is a JSON-type object that defines the unit price of use for each service.
Service providers develop policies for services in line with the billing policy schema.


#### 1.  **Billing Policy Schema**

| Classification  |Type | Necessity| Description|
|---------|---|----|-----|
|plan_id      |  String | O   |  API Service Metering Plan ID   |
|metrics     | Array  |  At least one  |  Billing Policy List   |
|    name     | String  | O   |  Billing target name  |
|    price     | String  |  At least one  |  Billing Policy Details   |
|    country     | String  | O   | Currency to be applied to the unit price of the service    |
|    price     |  String | O   | Service usage unit price    |
|    title     |  String | X   | Billing Policy Title    |


#### 2.  **Billing Policy Example**

	{
	  "plan_id": "standard",
	  "metrics": [
	    {
	      "name": "sample_service_usage_param1",
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
	      "name": " sample_service_usage_param2",
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



### <div id='31'/>2.5.4.  Register Policy

Policies can be registered to CF-ABACUS in one of two ways.

#### **1.  By registering a js file**

Save the created policy to the following directory and deploy or redeploy CF-ABACUS into CF.

-	In the case of Metering Policy

		cf-abacus/lib/plugins/provisioning/src/plans/metering

-	In case of rating Policy

		cf-abacus/lib/plugins/provisioning/src/plans/pricing

-	In the case of Billing Policy

		cf-abacus/lib/plugins/provisioning/src/plans/rating


#### **2.  By registering it to the DB**

There is no need to redeploy CF-ABACUS by storing the prepared policy in DB using curl or the like. When registering a policy, the policy ID must be unique.

-   In the case of Metering Policy

  		POST /v1/metering/plans/:metering_plan_id


-   In the case of Rating Policy

  		POST /v1/rating/plans/:rating_plan_id

-   In the case of Billing Policy

  		POST /v1/pricing/plans/:pricing_plan_id


## <div id='32'/>2.6  Deployment
When an application is deployed on the PaaS-TA platform, you can connect and use the deployed application with the services provided by the PaaS-TA platform.
It should be executed only on the PaaS-TA platform to access the application environment variable and access the service.

### <div id='33'/> 2.6.1. PaaS-TA Platform Login

Log in to the PaaS-TA platform to perform the procedure below

  >$ cf api --skip-ssl-validation **https://api**.<***PaaS-TA Domain***> # **Set PaaS-TA Platform TARGET**

  >$ cf login -u *<**user name**>* -o *<**org name**>* -s *<**space name**>* **#Request login**


### <div id='34'/>2.6.2.  Create mongo-db Service Broker

Create the service to use at the application through the PaaS-TA Platform.  
Mongo-db service must be deployed in the PaaS-TA platform environment.  
Access information can be obtained through the application and binding process.  

-   **Create Service (The cf marketplace command allows you to view the list of services and the plan for each service.)**

		## Service Broker CF Deployment
		$ cd openpaas-service-java-broker-mongo
		$ cf push
		
		## Create Service Broker
		$ cf create-service-broker <Service Broker Name> <Authentication ID> <Aunthentication Password> <Service Broker Address>
		
		Example)
		$ cf create-service-broker openpaas-mongo-broker admin cloudfoundry http://openpaas-mongo-broker.bosh-lite.com
		
		## Check Service Broker
		$ cf service-brokers
		Getting service brokers as admin...
		
		name                url   
		openpaas-mongo-broker http://openpaas-mongo-broker.<PaaS-TA Domain>
		
		## Check Service Catalog
		$ cf service-access
		Getting service access as admin...
		broker: sample-mongodb-broker
		   service                                   plan       access   orgs   
		   Mongo-DB                               default-plan none        
		   
		## Allow access to registered services
		$ cf enable-service-access <Service Name> -p <Plan Name>
		
		Example)
		$ cf enable-service-access Mongo-DB
		
		# Create Service
		$ cf create-service Mongo-DB default-plan  mongod_service


## <div id='35'/>2.6.3.  API Service Interworking Sample Application Deployment and Service Connection

The process of connecting an application to a service is called a 'bind'. Through this process, access information to access the service is generated.

-   Bind Application and Service
-   Set the application environment information necessary for metering with the -c option.

		## API Service Interworking Sample Application Deployment
		$ cd /binding-test-app
		$ cf push
		
		## Service Bind
		$ cf bind-service <APP_NAME> <SERVICE_INSTANCE> -c <PARAMETERS_AS_JSON>
		
		Example) 
		$ cf bind-service binding-test-app mongod_service -c '{"app_organization_id":"test05","app_space_id":"testspaceId","metering_plan_id":"standard"}'
		
		## Check Service Connection
		$ cf services
		Getting services in org real / space ops as admin...
		OK
		
		name                       service                                   plan       bound apps               last operation   
		binding-test-app mongod_service standard   binding-test-app create succeeded
		
		## Application Execution
		$ cf start <APP_NAME>
		
		Example)
		$ cf start binding-test-app
		
		## Check Shape
		$ cf a
		Getting apps in org real / space ops as admin...
		OK
		
		name                      requested state   instances   memory   disk   urls   
		binding-test-app          started           1/1         512M     512M   binding-test-app.<PaaS-TA Domain>
		openpaas-mongo-broker     started           1/1         512M     1G     openpaas-mongo-broker.<PaaS-TA Domain>


## <div id='36'/>2.7.  Service Binding CF-Abacus Interworking Test

The binding-test-app and mongo-db services can be bound to perform a CF-Abacus interworking test.

Check CF-Abacus Interwork

	## Test Binding
	$ cf bind-service binding-test-app mongod_service -c '{"app_organization_id":"testOrgGuid","app_space_id":"testSpaceGuId","metering_plan_id":"standard"}'
	
	<<Skip>> 
	
	## Check API Usage
	$ curl 'http://abacus-usage-reporting.<PaaS-TA Domain>/v1/metering/organizations/<Orgainization that deployed sampele application>/aggregated/usage'
	
	Example)
	$ curl 'http://abacus-usage-reporting.bosh-lite.com/v1/metering/organizations/testOrgGuid /aggregated/usage'


## <div id='37'/>2.8.  Unit Test

It is implemented as a Junit test and owermock-mockito-release-full:1.6.1 was used for the partial mock appliance for the test service class.


-   Create gradle. build dependency for test


		dependencies {
		
		// Service Broker Library 
		compile files('libs/openpaas-service-java-broker-ex.jar')
		
		// Metering Usage Object Create Dependent Library
		compile("org.json:json:20160212")
		
		…Skip
		
		:${springBootCfServiceBrokerVersion}")
		
		testCompile("org.springframework.boot:spring-boot-starter-test:${springBootVersion}")
		testCompile("com.jayway.jsonpath:json-path:${jsonPathVersion}")
		testCompile("org.apache.httpcomponents:httpclient:4.4.1")
		testCompile("org.powermock:powermock-mockito-release-full:1.6.1")    
		…Skip
		}


1.  Execute Test
	
	-   Right-click /meteringTest path in the Navigator Tree from Spring Tool Suite > Run As > JUNIT Test

## <div id='38'/>2.9. Sample Code

The Sample code can be downloaded from the site below.

[Download](https://paas-ta.kr/data/packages/2.0/PaaSTA-Metering.zip)


[Java_Service_Metering_Image01]:./images/Java_Service_Metering/service_broker_api_architecture.png
[Java_Service_Metering_Image02]:./images/Java_Service_Metering/service_metering_deployment_range.png
[Java_Service_Metering_Image03]:./images/Java_Service_Metering/mongo-db-jar.png
[Java_Service_Metering_Image04]:./images/Java_Service_Metering/service_broker_library_architecture.png


### [Index](https://github.com/PaaS-TA/Guide-eng/blob/master/README.md) > [AP User Guide](../README.md) > Java Service Metering Development
