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

Mongo-db service pack installation shall be performed by referring to Mongo-DB installation document.


Mongo-db service pack installation shall be performed by referring to Mongo-DB installation document.
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

The open cloud platform service API is a protocol (catalog, provision, de provision, update provision plan, bind, unbound) between Cloud Controller and Service Broker, which is implemented as a RESTful API and registered with Cloud Controller.

When  implementing metering for a service, select a process that fits the service policy and purpose of these conventions and link metering to that process.

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
CF-ABACUS is installed in the form of micro-service in CF after CF installation. Check below for details.<br>
<a href = "https://github.com/cloudfoundry-incubator/cf-abacus" >https://github.com/cloudfoundry-incubator/cf-abacus</a>
</td>
  </tr>
</table>                


※ This development guide only describes ***Development of functions that meter services that can determine when applications and services are bound as the start of service use**.

※ ***It does not describe the development of metering functions for specific API calls for services and the use of specific resources for services.***
※ Refer to the API Service Metering Development Guide for metering of API calls.

※ Refer to the linked site for development or installation of other components.


## <div id='8'/>2.2.  Configure Development Environment


Following environment is premised on the development environment for service metering development.

-   CF release: v226 and above (Test in a bosh-lite installation envionment)
-   gradle 2.14
-   java version "1.8.0_101"
-   springBootVersion: 1.3.0. BUILD-SNAPSHOT
-   mongo-db service broker 2.5 (Target Service Broker Project to Add Metering Services)
-   springBootCfServiceBrokerVersion "2.5.0" (Service Broker Library)
-   Spring Tool Suite or Eclipse


## <div id='9'/>2.3.  Service Broker Library



### <div id='10'/>2.3.1.  What is Service Broker Library?


In CF (Open Platform), there are various services that can be serviced on the platform.<br>
As each of these services are developing its own service broker, enables the applications to use the service in CF (open platform).<br>
The services vary, but the RESTAPI of the open platform for using the services is predetermined.<br>
The Service Broker Library is a library that allows different service brokers to provide services based on the REST API of this open platform by adding the service broker library Jar file to the build path and implementing abstraction classes.

In this guide, implementing metering services in the mongo-db service broker, an abstraction class for metering is added to this service broker library, and then the mongo-db service broker builds this library using dependency.


### <div id='11'/>2.3.2. Download the service broker library and import the project.

#### 1.  Download the service broker source that is being provided as an open source using git clone.<br> 

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
|    Modify     | ServiceIncetanceBindingController  | A controller that processes the service binding request of cloud controller.<br> Obtain the uaatoken from Sample MeteringOuthService and add the process of calling with the parameters of Sample MeteringReportService.   |
|    Modify     | ServiceInstanceBinding  | When the service-binding-request is processed by the ServiceIncidenceBinding Controller, report the usage report to the abacus-usage-collector with metering applied to the binding connection.   |     
|    Add     | SampleMeteringReportService  | SampleMeteringReportService abstracted interface with no information related to metering/rating/charging policies. This is an abstraction class provided for service providers to implement this interface to apply to service implementations. This is an abstraction class provided for service providers to implement this interface to apply to service implementations.<br>SampleMeteringReportService abstracted interface with no information related to metering/rating/charging policies. This is an abstraction class that is provided so that service providers who will implement this interface can apply it to service implementations.|     
|    Add     | SampleMeteringOAuthService  | It is an abstraction class for obtaining an access token to the abacus-usage-collector from a UAA server on an open platform and delivering the token to the SampleMeteringReportService.   |


Appearance of files added or modified for metering in the service broker library

![Java_Service_Metering_Image04]

### <div id='13'/>2.3.4.  ServiceInstanceBindingController

In the bindServiceInstance process, add the process of obtaining uaa token from  SampleMeteringOAuthServiceand calling with parameters from SampleMeteringReportService.

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

Add the environment information field of the application to be bound to implement the metering service to the Service InstanceBinding. The added fields will be passed to the mongo-db repository after mapping the field values of the service binding request parameter in the implementation of the Service Instance Binding Service. Gradle build the library.

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

UAA OAuthToken is needed to access abucus-collector RESTAPI if Abacus operates as Secured.<br>
The class that inherits the SampleMeteringOAuthServicemust implement the process of acquiring and returning UAA OAuthToken.

	package org.openpaas.servicebroker.service;
	import org.openpaas.servicebroker.exception.ServiceBrokerException;
	
	public interface SampleMeteringOAuthService {
	String getUAAToken() throws ServiceBrokerException;
	}


### <div id='16'/>2.3.7.  SampleMeteringReportService  Abstract Class
The class that inherits the SampleMeteringReportServicemust implement the process of sending the event information to the abacus-collector and returning the status code (HTTP status code) for the processing when processing the create binding request and delete binding request.

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
|   Modify      | datasource.properties   | Mongo-db Service Information   |     
|   Modify     | MongoServiceInstanceBindingService  |Add the process of mapping the metering information received by the service broker binding request parameter to the service instance binding.    |     
|   Add      | SampleMeteringReportServiceImpl  | Implement SampleMeteringReportService.   |     
|   Add     |SampleMeteringOAuthServiceImpl   | Implement SampleMeteringOAuthService.   |     
|   Modify     |Manifest.yml   | Describes the configuration information required when distributing the app to CF and the configuration information required for the app execution environment.   |


### <div id='21'/>2.4.4.  Add dependency for gradle build
Apply service broker library mongo-db service broker jar file

![Java_Service_Metering_Image03]

Gradle build the service broker library.

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



When Build is successed, /openpaas-service-java-broker/build/libs/openpaas-service-java-broker.jar gets created.

Copy the jar file to the /openpaas-service-java-broker-mongo/libs path of the mongo-db service broker and add dependency to the mongo-db service broker gradle build file.


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
	# abacus usage collector RESTAPI use authority (Register at UAA server a head of time.)
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
Map these information to the ServiceInstanceBinding in the metering field.

-   Request example of service broker binding CLI 

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

Information for obtaining a UAA token from the UAA server in application-mvc.properties is called to the class.

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
At this time, the blank ({}) or token is returned according toabacusSecured (Whether the abacus-collector is set to secured).

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

SampleMeteringReportServiceImpl  creates an https connection with the uaa token obtained from SampleMeteringOAuthServiceImpland POSTs service usage information to abacus-collector.

Since abacus-collector is preparing a process for the form to be POST according to the metering policy, JSON is generated in the form known by the abacus-collector and POST.

SampleMeteringReportServiceImpl is largely divided into two to process.


#### 1.  **Create usage information JSON by refering to the ServiceInstanceBinding Information.**

#### 2.  **Send the generated usage information JSON to abacus-collector. (HTTPS, HTTP)**


Create usage information JSON.

RESOURCE_ID linux-container and STANDARD_PLAN_ID standard are metric schemas provided by abacus as samples.

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


**abacus-collector** **인터페이스 항목**

| 항목명                 |유형             | 설명                                          | 예시                                           |
|-----------------------|-----------------|----------------------------------------------|------------------------------------------------|
|   start               | UNIX Timestamp  |바인드/언바인드 처리 시작 시각                   |1396421450000                                   |
|   end                 | UNIX Timestamp  |  바인드/언바인드 처리 응답 시각                 |1396421451000                                   |
|  organization_id      | String          | 바인드 요청을 호출한 앱의 조직 ID               | us-south:54257f98-83f0-4eca-ae04-9ea35277a538  |
|   space_id            |String           | 서비스 바인드 요청을 호출한 앱의 영역 ID         |d98b5916-3c77-44b9-ac12-04456df23eae            |
|  consumer_id          | String          |서비스 바인드 요청을 호출한 앱 ID                | App: d98b5916-3c77-44b9-ac12-04d61c7a4eae      |
|  resource_id          |String           |서비스 자원 ID                                 |linux-container                                 |
|  plan_id              |String           | 서비스 미터링 Plan ID                         |standard                                        |
|  resource_instance_id | String          |바인드 요청을 호출한 앱 ID                      | d98b5916-3c77-44b9-ac12-04d61c7a4eae            |
|  measured_usage       | Array           | 미터링 항목                                   | -                                         |
|   measure             | String          | 미터링 대상 명                                |sample_service_usage_param1                     |
|  quantity             |Number           |  서비스 사용량 예제는 메모리 사용량 (byte)      |1000000000                                       |


※ JSON 변환 예제

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



# <div id='27'/>2.5.  미터링/등급/과금 정책

서비스, 그리고 서비스 제공자 마다 미터링/등급/과금 정책 다르기 때문에 본
가이드에서는 정책의 개발 예제를 다루지는 않는다. 다만 CF-ABACUS에 적용할
수 있는 형식에 대해 설명한다.


### <div id='28'/>2.5.1.  미터링 정책
미터링 정책 스키마
미터링 정책이란 수집한 미터링 정보에서 미터링 대상의 지정 및 집계 방식을
정의한 JSON 형식의 오브젝트이다. 서비스 제공자는 미터링 정책 스키마에
맞춰 서비스에 대한 정책을 개발한다.


#### 1.  **미터링 정책 스키마**

| 항목명  |유형 | 필수| 설명|
|---------|---|----|-----|
|plan_id      |  String | O   |  API 서비스 미터링 Plan ID   |
|measures     | Array  |  최소 하나  |  API 서비스 미터링 정보 수집 대상 정의   |
|    name     | String  | O   |  미터링 정보 수집 대상 명   |
|    unit     | String  |  O  |  미터링 정보 수집 대상 단위   |
|metrics     | Array  | 최소 하나   | API 서비스 미터링 집계 방식 정의    |
|    name     |  String | O   | 미터링 정보 수집 대상 명    |
|    unit      |  String |  O  | 미터링 정보 수집 대상 단위    |
|    meter     |  String | X   | 미터링 정보에 대해서 수집 단계에 적용하는 계산식 또는 변환 식    |
|    accumulate      |String   |  X  | 미터링 정보에 대해서 누적 단계에 적용하는 계산식 또는 변환식    |
|    aggregate      |  String |  X  | 미터링 정보에 대해서 집계 단계에 적용하는 계산식 또는 변환식    |
|    summarize      | String  |  X  | 미터링 정보를 보고할 때 적용하는 계산식 또는 변환식    |
|    title      |  String |   X | API 서비스 미터링 제목    |

#### 2.  **미터링 정책 예제**

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


### <div id='29'/>2.5.2.  등급 정책 

등급 정책이란 각 서비스의 사용 가중치를 정의한 JSON 형식의 오브젝트이다.
서비스 제공자는 등급 정책 스키마에 맞춰 서비스에 대한 정책을 개발한다.


#### 1.  **등급 정책 스키마**

| 항목명  |유형 | 필수| 설명|
|---------|---|----|-----|
|plan_id      |  String | O   |  API 서비스 미터링 Plan ID   |
|metrics     | Array  |  최소 하나  |  등급 정책 목록   |
|    name     | String  | O   |  등급 정의 대상 명   |
|    rate     | String  |  X  |  가중치 계산식 또는 변환식   |
|    charge     | String  | X   | 사용량에 대한 과금 계산식 또는 변환식    |
|    title     |  String | X   | 등급 정책 명    |


#### 2.  **등급 정책 예제**

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


### <div id='30'/>2.5.3. 과금 정책 

과금 정책이란 각 서비스에 대한 사용 단가를 정의한 JSON 형식의
오브젝트이다. 서비스 제공자는 과금 정책 스키마에 맞춰 서비스에 대한
정책을 개발한다.


#### 1.  **과금 정책 스키마**

| 항목명  |유형 | 필수| 설명|
|---------|---|----|-----|
|plan_id      |  String | O   |  API 서비스 미터링 Plan ID   |
|metrics     | Array  |  최소 하나  |  과금 정책 목록   |
|    name     | String  | O   |  과금 대상 명  |
|    price     | String  |  최소 하나  |  과금 정책 상세   |
|    country     | String  | O   | 서비스 사용 단가에 적용할 통화    |
|    price     |  String | O   | 서비스 사용 단가    |
|    title     |  String | X   | 과금 정책 제목    |


#### 2.  **과금 정책 예제**

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



### <div id='31'/>2.5.4.  정책 등록

정책은 2가지 방식 중 하나의 방법으로 CF-ABACUS에 등록할 수 있다.

#### **1.  js 파일을 등록하는 방식**

작성한 정책을 다음의 디렉토리에 저장한 후, CF에 CF-ABACUS를 배포 또는
재배포 한다.

-	미터링 정책의 경우

		cf-abacus/lib/plugins/provisioning/src/plans/metering

-	등급 정책의 경우

		cf-abacus/lib/plugins/provisioning/src/plans/pricing

-	과금 정책의 경우

		cf-abacus/lib/plugins/provisioning/src/plans/rating


#### **2.  DB에 등록하는 방식**

작성한 정책을 curl 등을 이용해 DB에 저장하는 방식으로 CF-ABACUS를
재배포할 필요는 없다. 정책 등록 시, 정책 ID는 고유해야 한다.

-   미터링 정책의 경우

  		POST /v1/metering/plans/:metering_plan_id


-   등급 정책의 경우

  		POST /v1/rating/plans/:rating_plan_id

-   과금 정책의 경우

  		POST /v1/pricing/plans/:pricing_plan_id


## <div id='32'/>2.6  배포
파스-타 플랫폼에 애플리케이션을 배포하면 배포한 애플리케이션과 파스-타
플랫폼이 제공하는 서비스를 연결하여 사용할 수 있다. 파스-타 플랫폼상에서
실행을 해야만 파스-타 플랫폼의 애플리케이션 환경변수에 접근하여 서비스에
접속할 수 있다.

### <div id='33'/> 2.6.1. 파스-타 플랫폼 로그인

아래의 과정을 수행하기 위해서 파스-타 플랫폼에 로그인

  >$ cf api --skip-ssl-validation **https://api**.<***파스-타 도메인***> # **파스-타 플랫폼 TARGET 지정**

  >$ cf login -u *<**user name**>* -o *<**org name**>* -s *<**space name**>* **#로그인 요청**


### <div id='34'/>2.6.2.  mongo-db 서비스 브로커 생성

애플리케이션에서 사용할 서비스를 파스-타 플랫폼을 통하여 생성한다.
mongo-db 서비스 팩이 배포하고자 파스-타 플랫폼 환경에 release 되어
있어야 한다. 애플리케이션과 바인딩 과정을 통해 접속정보를 얻을 수 있다.

-   **서비스 생성 (cf marketplace 명령을 통해 서비스 목록과 각 서비스의
    플랜을 조회할 수 있다.)**

		## 서비스 브로커 CF 배포
		$ cd openpaas-service-java-broker-mongo
		$ cf push
		
		## 서비스 브로커 생성
		$ cf create-service-broker <서비스 브로커 명> <인증ID> <인증Password> <서비스 브로커 주소>
		
		예)
		$ cf create-service-broker openpaas-mongo-broker admin cloudfoundry http://openpaas-mongo-broker.bosh-lite.com
		
		## 서비스 브로커 확인
		$ cf service-brokers
		Getting service brokers as admin...
		
		name                url   
		openpaas-mongo-broker http://openpaas-mongo-broker.<파스-타 도메인>
		
		## 서비스 카탈로그 확인
		$ cf service-access
		Getting service access as admin...
		broker: sample-mongodb-broker
		   service                                   plan       access   orgs   
		   Mongo-DB                               default-plan none        
		   
		## 등록한 서비스 접근 허용
		$ cf enable-service-access <서비스명> -p <플랜 명>
		
		예)
		$ cf enable-service-access Mongo-DB
		
		# 서비스 생성
		$ cf create-service Mongo-DB default-plan  mongod_service


## <div id='35'/>2.6.3.  API 서비스 연동 샘플 애플리케이션 배포 및 서비스 연결

애플리케이션과 서비스를 연결하는 과정을 '바인드(bind)라고 하며, 이
과정을 통해 서비스에 접근할 수 있는 접속정보를 생성한다.

-   애플리케이션과 서비스 연결
-   이때 -c 옵션으로 미터링에 필요한 애플리케이션 환경정보를 세팅한다.

		## API 서비스 연동 샘플 애플리케이션 배포
		$ cd /binding-test-app
		$ cf push
		
		## 서비스 바인드
		$ cf bind-service <APP_NAME> <SERVICE_INSTANCE> -c <PARAMETERS_AS_JSON>
		
		예) 
		$ cf bind-service binding-test-app mongod_service -c '{"app_organization_id":"test05","app_space_id":"testspaceId","metering_plan_id":"standard"}'
		
		## 서비스 연결 확인
		$ cf services
		Getting services in org real / space ops as admin...
		OK
		
		name                       service                                   plan       bound apps               last operation   
		binding-test-app mongod_service standard   binding-test-app create succeeded
		
		## 애플리케이션 실행
		$ cf start <APP_NAME>
		
		예)
		$ cf start binding-test-app
		
		## 형상 확인
		$ cf a
		Getting apps in org real / space ops as admin...
		OK
		
		name                      requested state   instances   memory   disk   urls   
		binding-test-app          started           1/1         512M     512M   binding-test-app.<파스-타 도메인>
		openpaas-mongo-broker     started           1/1         512M     1G     openpaas-mongo-broker.<파스-타 도메인>


## <div id='36'/>2.7.  서비스 바인딩 CF-Abacus 연동 테스트

binding-test-app 과 mongo-db 서비스를 바인딩 실행해, CF-Abacus 연동
테스트를 진행 할 수 있다.

CF-Abacus 연동 확인

	## 테스트 바인딩
	$ cf bind-service binding-test-app mongod_service -c '{"app_organization_id":"testOrgGuid","app_space_id":"testSpaceGuId","metering_plan_id":"standard"}'
	
	<<후략>> 
	
	## API 사용량 확인
	$ curl 'http://abacus-usage-reporting.<파스-타 도메인>/v1/metering/organizations/<샘플 애플리케이션을 배포한 조직>/aggregated/usage'
	
	예)
	$ curl 'http://abacus-usage-reporting.bosh-lite.com/v1/metering/organizations/testOrgGuid /aggregated/usage'


## <div id='37'/>2.8.  단위 테스트

Junit 테스트로 구현 되어 있으며, 테스트 service class 에 대한 부분적
mock 적용을 위하여, owermock-mockito-release-full:1.6.1 을 사용하였다.


-   테스트를 위한 gradle.build dependency 작성


		dependencies {
		
		// 서비스브로커 라이브러리 
		compile files('libs/openpaas-service-java-broker-ex.jar')
		
		// 미터링 사용량 객체 생성 의존 라이브러리
		compile("org.json:json:20160212")
		
		…중략
		
		:${springBootCfServiceBrokerVersion}")
		
		testCompile("org.springframework.boot:spring-boot-starter-test:${springBootVersion}")
		testCompile("com.jayway.jsonpath:json-path:${jsonPathVersion}")
		testCompile("org.apache.httpcomponents:httpclient:4.4.1")
		testCompile("org.powermock:powermock-mockito-release-full:1.6.1")    
		…후략
		}


1.  테스트 실행
	
	-   Spring Tool Suite 의 네비게이터 트리의 /meteringTest 경로에서 오른쪽
			마우스 클릭 > Run As > JUNIT 테스트

## <div id='38'/>2.9. 샘플코드

샘플 코드는 아래의 사이트에 다운로드 할 수 있다.

[다운로드](https://paas-ta.kr/data/packages/2.0/PaaSTA-Metering.zip)


[Java_Service_Metering_Image01]:./images/Java_Service_Metering/service_broker_api_architecture.png
[Java_Service_Metering_Image02]:./images/Java_Service_Metering/service_metering_deployment_range.png
[Java_Service_Metering_Image03]:./images/Java_Service_Metering/mongo-db-jar.png
[Java_Service_Metering_Image04]:./images/Java_Service_Metering/service_broker_library_architecture.png


### [Index](https://github.com/PaaS-TA/Guide-eng/blob/master/README.md) > [AP User Guide](../README.md) > Java Service Metering 개발
