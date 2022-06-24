### [Index](https://github.com/PaaS-TA/Guide-eng/blob/master/README.md) > [AP User Guide](../README.md) > Java API Service Metering Development


## Table of Contents

1. [Outline](#1)
	* [Document Outline](#2)
	 * [Purpose](#3)
	 * [Range](#4)
	 * [References](#5)

1. [JAVA API Service Metering Development Guide](#6)
    * [Outline](#7)
    * [Configure Development Environment](#8)
     * [CF-Abacus Installation](#9)
    * [Sample API Service Development](#10)
     * [Create gradle Project](#11)
     * [Sample API Service Features](#12)
     * [Dependencies and Properties Setting](#13)
     * [MeteringAuthService Class](#14)
     * [MeteringService Class](#15)
     * [SampleApiJavaServiceController Class](#16)
    * [API Service Interworking Sample Application](#17)
    * [API Service Interworking Sample Application Interface Items](#18)
    * [Metering/Rating/Charging Policy](#19)
     * [Metering Policy](#20)
     * [Rating Policy](#21)
     * [Charging Policy](#22)
     * [Register Policy](#23)
    * [Deployment](#24)
     * [Log in Paas-Ta Platform](#25)
     * [Create API Service Broker](#26)
     * [API Service Application Deployment and Service Registration](#27)
     * [API Service Interworking Sample Application Deployment and Service Connection](#28)
    * [API and CF-Abacus Interworking Tests](#29)
    * [Sample Code](#30)
	
# <div id='1'/>1. Outline

## <div id='2'/>1.1. Document Outline

### <div id='3'/>1.1.1. Purpose

This document (Java API Service Metering Application Development Guide) describes how to meter API services by linking the metering plug-in of the Pas-Ta platform project with the Java API Metering Service application.



### <div id='4'/>1.1.2. Range
The range of this document is limited to the development of metering methods for JAVA API service applications in PAS-TAR platform projects and the CF-Abacus linkage.

This document describes the creation of API metering service applications in Java languages.

This document does not implement API service-specific business logic, but only the function of metering when API service is called.

"Applications using API services" referred to in this document are developed by referring to the separately provided **Node.js API Metering Development Guide**.

### <div id='5'/>1.1.3. References
-   [https://docs.cloudfoundry.org/devguide/](https://docs.cloudfoundry.org/devguide/)
-   [https://github.com/cloudfoundry-incubator/cf-abacus](https://github.com/cloudfoundry-incubator/cf-abacus)


## <div id='6'/>2. JAVA API Service Metering Development Guide
### <div id='7'/>2.1 Outline


Create API service applications in Java language. The API service creates an application that processes the service request and sends API usage history to CF-ABACUS.

![Java_Api_Service_Metering_Image01]

<table border = "1px;">
  <tr>
    <th colspan ="2">function</th>
     <th>Description</th>
  </tr>
  <tr>
     <td rowspan="4">Runtime</td>
     <td>Metering/Rating/Charging Policy</td>
     <td>Various policy definition information for services provided by API service providers. It is in JSON format, and when the policy is registered with CF-ABACUS, API usage is aggregated according to the policy defined.<br>
The policy must be defined by the service provider, refer below for the the JSON schema.<br>
https://github.com/cloudfoundry-incubator/cf-abacus/blob/master/doc/api.md
</td>
  </tr>
   <tr>
     <td width="160px">Service Broker API</td>
     <td>Refer to the Service Pack Development Guide for service broker API development as a protocol between Cloud Controller and Service Broker.
</td>
  </tr> 
  <tr>
     <td>Service API</td>
     <td>It consists of an API service function provided by a service provider and a function to transmit API usage to CF-ABACUS.</td>
  </tr> 
   <tr>
     <td>Dashboad</td>
     <td>It should be developed by the service provider as a dashboard function for authentication to provide services, service monitoring, etc.</td>
  </tr> 
  <tr>
  	<td colspan ="2">CF-ABACUS</td>
    <td>Aggregates usage information collected as a CF-ABACUS core function.<br>
CF-ABACUS is installed in the form of micro-service in CF after CF installation. See the following for details.<br>
https://github.com/cloudfoundry-incubator/cf-abacus
</td>
  </tr>
</table>                                              

※ This development guide describes ***API service*** development only and refers to the site linked to the development or installation of other components.



## <div id='8'/>2.2 Configure Development Environment


The development environment is constructed in the following environment for Java application development.



-   CF release: v226 above
-   java version "1.8.0_101"
-   springBootVersion : 1.3.0.BUILD-SNAPSHOT
-   gradle 2.14
-   Spring Tool Suite or Eclipse



### <div id='9'/>2.2.1 CF-Abacus Installation
    

Install CF-Abacus by referring to the Abacus installation guide provided separately.


## <div id='10'/>2.3 Sample API Service Development 
    
If there is a service request, the sample api service processes the response to that request and sends metering information to CF-ABACUS.


### <div id='11'/>2.3.1 Create gradle Project

Create a project directory and initialize it into a gradle project


	$ mkdir sample_api_java_service // Project Directory
	$ cd sample_api_java_service/
	~/sample_api_java_service $ gradle init --type java-library // gradle reset
	: wrapper
	: init

	BUILD SUCCESSFU

	Total time: 2.435 secs

	This build could be faster, please consider using the Gradle Daemon: 
	https://docs.gradle.org/2.14/userguide/gradle_daemon.html
  

### <div id='12'/>2.3.2 Sample API Service Features

![Java_Api_Service_Metering_Image02]

Describe dependencies and properties geometry

| **File**       |           **Purpose**          |
|---------------|-----------------------------|
|build.gradle   |Describe the dependency information your application needs    |
|.gitignore     |When configuration management is performed through Git, a file or directory that does not require configuration management is set.               |
|manifest.yml   |Configuration information for the application to be applied when deploying an application to a parser platform <br>Can define the name, deployment path, and number of instances of the application.        |
|gradlew        |Gradlew build executable for Linux environment. <br> Automatically generated when initializing gradle.     |
|gradlew.bat    |Gradle build executable for use in Windows environment. <br> Automatically generated when initializing gradle.      |
|settings.gradle    |Configuration file to apply when gradlew runs  <br> Automatically generated when initializing gradle.    |


Java File Shape Description
  
| **File**       |           **Purpose**          |
|---------------|-----------------------------|
|MeteringConfig   |Load metering.properties when running application    |
|MeteringAuthService     |Obtain and return an access token to the abacus-usage-collector from a UAA server on a PAS-Other platform.              |
|MeteringService   |When API service usage requests are processed by the SampleApiJavaServiceController, a metering usage report is reported to the abacus-usage-collector for API service processing.       |
|SampleApiJavaServiceApplication        |When SpringBoot is running, it loads the context objects required for the SpringBoot application.   |
|SampleApiJavaServiceController   |REST Controller that handles API service usage requests.<br> This sample application does not implement API service-specific business logic, but only performs the function of sending API usage to abacus-collector.|
|application.properties     |When the SpringBoot is running, the properties required for the spring    |
|metering.properties      |Properties to be set when sending API usage to abacus-collector are defined.    |

### <div id='13'/>2.3.3 Dependencies and Properties Setting


-  build.gradle

	Describe the dependencies used by the sample Api service application.

  
		Skip..

		dependencies {

			// https://mvnrepository.com/artifact/org.springframework/spring-test
			compile group: 'org.springframework', name: 'spring-test', version: '2.5'
		     
		    providedRuntime("org.springframework.boot:spring-boot-starter-tomcat:${springBootVersion}")
		    compile("org.springframework.boot:spring-boot-starter-web:${springBootVersion}")
		
		    // Metering usage object generation dependency
		    compile("org.json:json:20160212")    // When Creating Json object
		    compile("com.sun.jersey:jersey-bundle:1.18.1") ")    // When Creating https connection
		    compile("com.googlecode.json-simple:json-simple:1.1") // json parse 
		    compile("commons-codec:commons-codec:1.5") // When Creating https connection
		}	

		Skipped..

  



-   manifest.yml

	Describes the configuration information required when deploying the app to CF and the configuration information required for the app execution environment.
```yml
applications:
- name: sample-api-node-service  # Application Name
  memory: 512M # Application Memory Size
  instances: 1 # Number of Application Instances
  host: sample-api-java-service
  path: ./build/libs/sample_api_java_service.jar # Location of the Application to be Deployed
  env:
    SPRING_PROFILES_ACTIVE : cloud
```


-   metering.properties

	Describes the necessary configuration information and account information when sending API service usage information to the abacus-collector.


		# Address of abacus usage collector RESTAPI

		abacus.collector = https://abacus-usage-collector.<CFdomain/v1/metering/collected/usage

		# abacus usage collector is secure mode true / if not false

		abacus.secured = true

		# uaa server of the PaaS-TA platform

		uaa.server = https://uaa.<CFdomain>

		# abacus usage collector RESTAPI account information and authentications (Preset to UAA server)

		uaa.client.id = <abacus.usage.read/write ID with scope authorities>

		uaa.client.secret = <abacus.usage.read/write PW with scope authorities>

		uaa.client.scope = abacus.usage.object-storage.write,abacus.usage.object-storage.read
  

### <div id='14'/>2.3.4 MeteringAuthService Class
    
The UAA token is acquired and returned by referring to the UAA server URL and account information.
 
	public String getUaacTokenHTTPS () throws MalformedURLException {
	
		String authToken = "";
		String urlStr = authServer + "/oauth/token?grant_type=client_credentials&scope=" + encodeURIComponent(scope);
		StringBuffer sb = new StringBuffer();
	
		try {
	
			TrustManager[] trustAllCerts = new TrustManager[] { new X509TrustManager() {
				public java.security.cert.X509Certificate[] getAcceptedIssuers() {
					return null;
				}
	
				public void checkClientTrusted(X509Certificate[] certs, String authType) {
				}
	
				public void checkServerTrusted(X509Certificate[] certs, String authType) {
				}
			} };
	
			SSLContext sc = SSLContext.getInstance("SSL");
			sc.init(null, trustAllCerts, new java.security.SecureRandom());
			HttpsURLConnection.setDefaultSSLSocketFactory(sc.getSocketFactory());
			URL url = new URL(urlStr);
			HttpURLConnection conn = (HttpURLConnection) url.openConnection();
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
	
		} catch (Exception e) {
			System.out.println(e.toString());
		}
	
		return authToken;
	}


Encodes the account information acquired from metering.properties to BASE64.

	public String getAuthKey(String id, String secret) throws Exception {	
		String authKey = ""; 	
		try {	
			String encodedConsumerKey = URLEncoder.encode(id, "UTF-8");
			String encodedConsumerSecret = URLEncoder.encode(secret, "UTF-8");
			String fullKey = encodedConsumerKey + ":" + encodedConsumerSecret;
			byte[] encodedBytes = Base64.encodeBase64(fullKey.getBytes());		
			authKey = "Basic " + new String(encodedBytes);	
		} catch (Exception e) {
			e.printStackTrace();			
			throw e;
		}	
		return authKey;
	}


Extract access_token from JSON object returned from UAA SERVER.

  
	private String parseAuthToken(String jsonStr) throws ParseException{		
		String barerStr;		
		JSONParser jsonParser = new JSONParser();
		JSONObject jsonObject = (JSONObject) jsonParser.parse(jsonStr);
		barerStr = (String) jsonObject.get("access_token");		
		return barerStr;
	}





### <div id='15'/>2.3.5 MeteringService Class
According to the Auth setting information of the abacus-collector, branch processing for the transmission method is performed.

	public void reportUsageData(String orgId, String spaceId, String appId, String planId) throws Exception {
		JSONObject serviceUsage = buildServiceUsage(orgId, spaceId, appId, planId);
	
		if (SECURED.equals(abacusSecured)) {
			reportUsageDataHTTPS(serviceUsage);
		} else {
			reportUsageDataHTTP(serviceUsage);
		}
	}


To transfer API usage to the Abacus-collector, a token is acquired from a CF or authentication server, set to the HTTP header, and create an HTTPS Connection.

	public void reportUsageDataHTTPS(JSONObject serviceUsage) throws Exception {
		StringBuffer sb = new StringBuffer();
		try {
			TrustManager[] trustAllCerts = new TrustManager[] { new X509TrustManager() {
				public java.security.cert.X509Certificate[] getAcceptedIssuers() {
					return null;
				}
	
				public void checkClientTrusted(X509Certificate[] certs, String authType) {
				}  // Create Authentication Certificate.
	
				public void checkServerTrusted(X509Certificate[] certs, String authType) {
				}
			} };
	
			SSLContext sc = SSLContext.getInstance("SSL");
			sc.init(null, trustAllCerts, new java.security.SecureRandom());
			HttpsURLConnection.setDefaultSSLSocketFactory(sc.getSocketFactory());
	
			URL url = new URL(collectorUrl);
			HttpURLConnection conn = (HttpURLConnection) url.openConnection();
			conn.setRequestMethod("POST");
			conn.setDoInput(true);
			conn.setDoOutput(true);
			conn.setUseCaches(false);
	
			conn.setRequestProperty("Content-Type", "application/json; charset=UTF-8");
	
			String bareStr = "bearer " + meteringAuthService.getUaacTokenHTTPS();
			conn.setRequestProperty("Authorization", bareStr);
	
			byte[] out = serviceUsage.toString().getBytes(StandardCharsets.UTF_8);
	
			DataOutputStream dout = new DataOutputStream(conn.getOutputStream());
			dout.write(out);
			dout.close();
	
			InputStreamReader in = new InputStreamReader((InputStream) conn.getInputStream());
			BufferedReader br = new BufferedReader(in);
	
			String line;
			while ((line = br.readLine()) != null) {
				sb.append(line).append("\n");
			}
	
			System.out.println(sb.toString());
			System.out.println(serviceUsage + " was repoerted.");
	
			br.close();
			in.close();
			conn.disconnect();
	
		} catch (Exception e) {
			Exception se = new Exception(e);
			throw se;
		}
	}


Set up an HTTP header to send API usage to the Abacus-collector and create an HTTP Connection.

	public void reportUsageDataHTTP(JSONObject serviceUsage) throws Exception {
	
		try {
			URL url = new URL(collectorUrl);
			URLConnection con = url.openConnection();
			HttpURLConnection http = (HttpURLConnection) con;
			http.setRequestMethod("POST"); // PUT is another valid option
			http.setDoOutput(true);
			http.setDoInput(true);
			http.setUseCaches(false);
	
			byte[] out = serviceUsage.toString().getBytes(StandardCharsets.UTF_8);
			int length = out.length;
	
			http.setFixedLengthStreamingMode(length);
			http.setRequestProperty("Content-Type", "application/json; charset=UTF-8");
			http.connect();
	
			try (OutputStream os = http.getOutputStream()) {
				os.write(out);
			}
	
		} catch (IOException e) {
			e.printStackTrace();
			throw new Exception(e);
		}
	}


Create an API service usage JSON to be sent to the Abacus-collector.

	private JSONObject buildServiceUsage (String orgId, String spaceId, String appId, String planId)
			throws JSONException {
	
		LocalDateTime now = LocalDateTime.now();
		Timestamp timestamp = Timestamp.valueOf(now);
	
		JSONObject jsonObjectUsage = new JSONObject ();
	
		jsonObjectUsage.put ("start", timestamp.getTime());
		jsonObjectUsage.put ("end", timestamp.getTime());
		jsonObjectUsage.put ("organization_id", orgId);
		jsonObjectUsage.put ("space_id", spaceId);
		jsonObjectUsage.put ("consumer_id", "app:" + appId);
		jsonObjectUsage.put ("resource_id", RESOURCE_ID);
		jsonObjectUsage.put ("plan_id", planId);
		jsonObjectUsage.put ("resource_instance_id", appId);
	
		JSONArray measuredUsageArr = new JSONArray ();
		JSONObject measuredUsage1 = new JSONObject ();
		JSONObject measuredUsage2 = new JSONObject ();
		JSONObject measuredUsage3 = new JSONObject ();
	
		int quantity = 0;
	
		if (STANDARD_PLAN_ID.equals(planId)) {
			quantity = PLAN_STANDARD_QUANTITY;
		} else if (EXTRA_PLAN_ID.equals(planId)) {
			quantity = PLAN_EXTRA_QUANTITY;
		}
	
		measuredUsage1.put ("measure", MEASURE_1);
		measuredUsage1.put ("quantity", quantity);
		measuredUsageArr.put(measuredUsage1);
		measuredUsage2.put ("measure", MEASURE_2);
		measuredUsage2.put ("quantity", 1);
		measuredUsageArr.put(measuredUsage2);
		measuredUsage3.put ("measure", MEASURE_3);
		measuredUsage3.put ("quantity", 0);
		measuredUsageArr.put(measuredUsage3);
	
		jsonObjectUsage.put ("measured_usage", measuredUsageArr);
		return jsonObjectUsage;
	}	


-   API Service Metering Transfer Items (Transfer Report JSON Details)

 | Classification  |Type | Description| Example|
 |---------|---|----|-----|
 |  start  |  UNIX |  Timestamp  |   API processing start time  | 1396421450000 |
 |  end       |  UNIX | Timestamp   | API processing response time    | 1396421451000
 |  space_id  |   String| Area ID of the app that called the API   | d98b5916-3c77-44b9-ac12-04456df23eae    |
 |  resource_id       | String  | API Resource ID   | sample_api    |
 | plan_id        | String  | API Metering Plan ID   | basic    |
 |  resource_instance_id       | String  | App ID that called API   | d98b5916-3c77-44b9-ac12-04d61c7a4eae    |
 | measured_usage      | Array  |  Metering Items  |   -  |
 | measure        |  String | Metering Target Name   |  api_calls   |
 | quantity        |Number   | Number of API processing for that API request    | 10    |
 					
※ Example of JSON conversion

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


### <div id='16'/>2.3.6 SampleApiJavaServiceController Class

REST Controller for handling service usage requests. In this sample application, only metering functions are performed.

	Skipped..
	@RequestMapping (value = "/plan1", method = RequestMethod.POST)
	public ResponseEntity<String> serviceAPIPlan01(@RequestBody String input) throws Exception {	
		JSONParser jsonParser = new JSONParser ();
		JSONObject jsonObject = (JSONObject) jsonParser.parse(input);	
		String orgId = (String) jsonObject.get("organization_id");
		String spaceId = (String) jsonObject.get("space_id");	
		String appId = (String) jsonObject.get("consumer_id");	
		String planId = (String) jsonObject.get("plan_id");	
		JSONObject serviceKeyOBJ = (JSONObject) jsonObject.get("credential");	
		String serviceKey = (String) serviceKeyOBJ.get("serviceKey");	
		
		if(!SERVICE_KEY.equals(serviceKey))
			return new ResponseEntity<>("credential is wrong", HttpStatus.UNAUTHORIZED);
		
		meteringService.reportUsageData(orgId, spaceId, appId, planId);		
		
		String successStr = "orgId:" + orgId + "/ spaceId:" + spaceId + "/ appId:" + appId + "/ planId:" + planId + " was reported to abacus collector.";
		
		return new ResponseEntity<>(successStr, HttpStatus.OK);
	} 
	Skip..



## <div id='17'/>2.4 API Service Interworking Sample Application

The current guide does not describe the development of an application calling an API service.. 
For the development of sample applications, see **Api Service Interworking Application Development** in the **Node.js API Metering Development Guide**.

### <div id='18'/>2.4.1 API Service Interworking Sample Application Interface Items

#### 1.  **API Service Endpoint**

	GET|POST|PUT|DELETE <api_service_restful_api>


#### 2.  **List of API Service Meterings Sent**

| Classification  |Type | Description| Example|
|---------|---|----|-----|
|  org_id       | String  | Organization ID of the app that requested API service    | 54257f98-83f0-4eca-ae04-9ea35277a538    |
|  space_id       | String  |Space ID of the app that requested API service     |d98b5916-3c77-44b9-ac12-04456df23eae     |
|consumer_id         |String   |App ID requesting for API service         |d98b5916-3c77-44b9-ac12-045678edabae     |
|instance_id         |String   |Resource instance ID of the app requesting for API service     |d98b5916-3c77-44b9-ac12-045678edabad     |
|plan_id         |String   |Plan ID of the requested API service for the app    |basic     |
|credentials         |JSON   |Set the required credentials for the service request    |credentials: {<br>key: value,<br>…<br>}     |
| inputs        |JSON   |Set input required for service request.    | inputs: {<br>key:value,<br>...<br>}    |




#### 3.  **Example of API Service Metering Sent Items**

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



## <div id='19'/>2.5. Metering/Rating/Charging Policy

This guide does not address examples of development of policies because they differ from service provider to service and from metering to rating to billing policy. However, the format applicable to CF-ABACUS is described.


### <div id='20'/>2.5.1. Metering Policy

Metering policy is an object in JSON format that defines the designation and aggregation method of metering targets from the collected metering information. The service provider develops a policy for the service in line with the metering policy schema.


#### 1.  **Metering Policy Schema**

| Classification  |Type | Necessity| Example|
|---------|---|----|-----|
|plan_id         |String   | O   |API Metering Plan ID     |
|measures         |Array   |At least one    |Define API metering information collection targets     |
|  name         |String   |O    |Metering Information Collection Target Name     |
|  unit         |String   |O    |Units to which metering information is collected     |
|metrics        |Array   |At least one    |Define API metering aggregation schemes     |
|  name         |String   |O    |Metering Information Collection Target Name     |
|  unit         |String   |O    |Units to which metering information is collected     |
|  meter         |String   |X    |Calculation or conversion expressions that apply to the collection stage for metering information     |
|  accumulate         |String   |X    |Calculation or conversion expressions that apply to the cumulative phase for metering information     |
|  aggregate         |String   |X   |Calculation or conversion expressions that apply to the aggregation stage for metering information     |
|  summarize         |String   |X   |Calculation or conversion expressions that is applied when reporting metering information     |
|  title         |String   |X   |API metering title     |


#### 2.  **Metering Policy Example**

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


### <div id='21'/>2.5.2. Rating Policy

A rating policy is an object in JSON format that defines the usage weight of each service.
The service provider develops a policy for the service in line with the rating policy schema.

#### 1.  **Rating Policy Schema**

| Classification  |Type | Necessity| Description|
|---------|---|----|-----|
| plan_id|   String |  O        |   API rating Plan ID    |
| metrics |   Array  |  at least one|  List of rating policy	|
| name    |   String |  O        |   Grade difinition subject name|
| rate    |   String |  X        |   Weight calculation or conversion formula|
| charge  |   String |  X        |   Billing formula or conversion formula for usage|
| title   |   String |  X        |   Rating Poicy Title|


#### 2.  **Example of Rating Policy**

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


### <div id='22'/>2.5.3. Billing Policy

A billing policy is an object in the form of JSON that defines the unit cost of use for each service. 
The service provider develops a policy for the service in line with the billing policy schema.

#### 1.  **Billing Policy Schema**

| Classification  |Type | Necessity| Description|
|---------|---|----|-----|
| plan_id|   String |  O        |   API Billing Plan ID|
| metrics  |  Array   | at least one |  List of Billing Policy|
| name     |  String  | O        |   Billing target name|
| price    |  Array   | at least one |  Billing policy details|
| country  |  String  | O        |   Currency to be applied to the unit price of the service|
| price    |  Number  | O        |   Unit price of service use|
| title    |  String  | X        |   Billing policy title|


#### 2.  **Example of Billing Policy**
		
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



### <div id='23'/>2.5.4. Register Policy
Policies can be registered to CF-ABACUS in one of two ways:

#### 1.  **By registering a js File**

After storing the prepared policy in the following directory, CF-ABACUS is deployed or redeploy into CF.

-   In case of Metering Policy
		
		cf-abacus/lib/plugins/provisioning/src/plans/metering

-   In case of Rating Policy

		cf-abacus/lib/plugins/provisioning/src/plans/pricing

-   In case of Billing Policy

		cf-abacus/lib/plugins/provisioning/src/plans/rating


#### 2.  **By Registering in the DB**

There is no need to redeploy CF-ABACUS by storing the prepared policy in DB using curl or the like. When registering a policy, the policy ID must be unique.


-   In case of Metering Policy

		POST /v1/metering/plans/:metering_plan_id
	>
	
		## Example
		$ curl -k -X POST 'http://abacus-provisioning-plugin.bosh-lite.com/v1/metering/plans/sample-linux-container' \
			 -H "Content-Type: application/json" \
			 -d '{"plan_id":"sample-linux-container","measures":[{"name":"current_instance_memory","unit":"GIGABYTE"},{"name":"current_running_instances","unit":"NUMBER"},{"name":"previous_instance_memory","unit":"GIGABYTE"},{"name":"previous_running_instances","unit":"NUMBER"}],"metrics":[{"name":"memory","unit":"GIGABYTE","type":"time-based","meter":"((m)=>({previous_consuming:newBigNumber(m.previous_instance_memory||0).div(1073741824).mul(m.previous_running_instances||0).mul(-1).toNumber(),consuming:newBigNumber(m.current_instance_memory||0).div(1073741824).mul(m.current_running_instances||0).toNumber()})).toString()","accumulate":"((a,qty,start,end,from,to,twCell)=>{if(end<from||end>=to)returnnull;constpast=from-start;constfuture=to-start;consttd=past+future;return{consuming:a&&a.since>start?a.consuming:qty.consuming,consumed:newBigNumber(qty.consuming).mul(td).add(newBigNumber(qty.previous_consuming).mul(td)).add(a?a.consumed:0).toNumber(),since:a&&a.since>start?a.since:start};}).toString()","aggregate":"((a,prev,curr,aggTwCell,accTwCell)=>{if(!curr)returna;constconsuming=newBigNumber(curr.consuming).sub(prev?prev.consuming:0);constconsumed=newBigNumber(curr.consumed).sub(prev?prev.consumed:0);return{consuming:consuming.add(a?a.consuming:0).toNumber(),consumed:consumed.add(a?a.consumed:0).toNumber()};}).toString()","summarize":"((t,qty,from,to)=>{if(!qty)return0;constrt=Math.min(t,to?to:t);constpast=from-rt;constfuture=to-rt;consttd=past+future;constconsumed=newBigNumber(qty.consuming).mul(-1).mul(td).toNumber();returnnewBigNumber(qty.consumed).add(consumed).div(2).div(3600000).toNumber();}).toString()"}]}' \
			-H "Authorization: $(cf oauth-token | grep bearer)"



-   In case of Rating Policy

	 	POST /v1/rating/plans/:rating_plan_id
	>

		## Example
		$ curl -k -X POST 'http://abacus-provisioning-plugin.bosh-lite.com/v1/rating/plans/linux-rating-sample' \
			 -H "Content-Type: application/json" \
		     -d '{"plan_id":"linux-rating-sample","metrics":[{"name":"memory","rate":"((price,qty)=>({price:price,consuming:qty.consuming,consumed:qty.consumed})).toString(),charge:((t,qty,from,to)=>{if(!qty)return0;constrt=Math.min(t,to?to:t);constpast=from-rt;constfuture=to-rt;consttd=past+future;constconsumed=newBigNumber(qty.consuming).mul(-1).mul(td).toNumber();constgbhour=newBigNumber(qty.consumed).add(consumed).div(2).div(3600000).toNumber();returnnewBigNumber(gbhour).mul(qty.price).toNumber();}).toString()"}]}' \
			 -H "Authorization: $(cf oauth-token | grep bearer)"



-   In case of Billing Policy

		POST /v1/pricing/plans/:pricing_plan_id
	>

		## Example
		$ curl -k -X POST 'http://abacus-provisioning-plugin.bosh-lite.com/v1/pricing/plans/linux-pricing-sample' \
			 -H "Content-Type: application/json" \
			 -d '{"plan_id":"linux-pricing-sample","metrics":[{"name":"memory","prices":[{"country":"USA","price":0.00014}]}]}' \
			-H "Authorization: $(cf oauth-token | grep bearer)"



## <div id='24'/>2.6. Deployment

When deploying an application on PaaS-TA Platform, the application can be connected and be used with the services provided by the PaaS-TA Platform.
Only when executed on the PaaS-TA Platform can access service in the application environment variable of the PaaS-TA Platform.


### <div id='25'/>2.6.1 PaaS-TA Platform Login

Login to PaaS-TA Platform to follow the process below
	
`$ cf api --skip-ssl-validation`*`https://api.`**`<PAAS-TA DOMAIN> `**`#Set PAAS-TA Platform TARGET`*

`$ cf login -u <user name> -o <org name> -s <space name>#request login`


### <div id='26'/>2.6.2. Create API Service Broker

Create the service to be used in the application is through the PaaS-TA platform.
Can generate without a separate service installation process, and access information can be obtained through an application and binding process.

-   Create Service (cf marketplace command allows you to view the list of services and the plan for each service.)

		##Service Broker CF Deployment
		$ cd <Sample Service Broker Path>/sample_api_java_broker
		$ cf push


  		##Create Service Broker
  		$ cf create-service-broker <Service Broker Name> <Authentication ID> <Authentication Password> <Service Broker Address>

  		Example)
  		$ cf create-service-broker sample-api-broker admin cloudfoundry http://sample-api-java-broker.bosh-lite.com

  		##Check Service Broker
  		$ cf service-brokers
  		Getting service brokers as admin...

 		name url
  		sample-api-broker http://sample-api-java-broker.bosh-lite.com

  		##Check Service Catalog
  		$ cf service-access
  		Getting service access as admin...
  		broker: sample-api-broker
  		service plan access orgs
  		standard_obejct_storage_light_api_calls standard none
  		standard_obejct_storage_heavy_api_calls basic none

  		##Allow Access to Registered Services
  		$ cf enable-service-access <Service Name> -p <Plan Name>

		  Example)
  		$ cf enable-service-access standard_obejct_storage_light_api_calls -p standard


### <div id='27'/>2.6.3. API Service Application Deployment and Service Registration

Deploys API service applications on the PaaS-TA platform. The API registered as a service can provide API services by combining it with other applications.
1.  **Application Deployment**

	-	build with gradle build -x test command.

	-	Deploy with cf push command. Uses the settings in manifest.yml unless you add a separate value

			## API Service Deployment
			$ cd <Sample API Service Path>/sample_api_java_service
  			## gradle build
  			$ gradle build -x test
  			:compileJava
  			:processResources
  			:classes
  			:findMainClass
  			:jar
  			:bootRepackage
  			:assemble
  			:check
	  		:build
 
			BUILD SUCCESSFUL

			Total time: 13.426 secs
	  		$ cf push
	
			##Create Service
	  		$ cf create-service <Service Name> <Plan Name> <Service Instance Name>
	  		예)
	  		$ cf create-service standard_obejct_storage_light_api_calls standard sampleNodejslightCallApi
	
	  		##Service Check
	  		$ cf services
	  		Getting services in org real / space ops as admin...
	  		OK

			name service plan bound apps last operation
	  		sampleNodejslightCallApi standard_obejct_storage_light_api_calls standard create succeeded


### <div id='28'/>2.6.4. API Service Interworking Sample Application Deployment and Service Connection

The process of connecting an application to a service is called a 'bind', and through this process, access information to access the service is generated.

-   Binding Application and Service

		## API Service Interworking Sample Application Deployment
		$ cd <Sample Application Path>/sample_api_node_caller
		$ npm install && npm run babel && npm run cfpack && ./cfpush.sh
		
		## Service Bind
		$ cf bind-service <APP_NAME> <SERVICE_INSTANCE> -c <PARAMETERS_AS_JSON>
		
		Example) 
		$ cf bind-service sample-api-node-caller sampleNodejslightCallApi -c '{"serviceKey": "cloudfoundry"}'
		
		## Check Service Connection
		$ cf services
		Getting services in org real / space ops as admin...
		OK
		
		name                       service                                   plan       bound apps               last operation   
		sampleNodejslightCallApi   standard_obejct_storage_light_api_calls   standard   sample-api-node-caller   create succeeded
		
		## Execute Application
		$ cf start <APP_NAME>
		
		Example)
		$ cf start sample-api-node-caller
		
		## Check Form
		$ cf a
		Getting apps in org real / space ops as admin...
		OK
		
		name                      requested state   instances   memory   disk   urls   
		sample-api-node-service   started           1/1         512M     512M   sample-api-node-service.bosh-lite.com   
		sample-api-java-broker    started           1/1         512M     1G     sample-api-java-broker.bosh-lite.com   
		sample-api-node-caller    started           1/1         512M     512M   sample-api-node-caller.bosh-lite.com



## <div id='29'/>2.7. API and CF-Abacus Interworking Tests

Access from a web browser through the url of the API-linked sample application. CF-Abacus-linked tests on API-linked and API usage can be conducted.

1.  **Check CF-Abacus Connection**


		## Check Organization guid 
		$ cf org <The organization that deployed sample application> --guid
		
		Example)
		$ cf org real --guid
		877d01b2-d177-4209-95b0-00de794d9bba
		
		## Check Sample Application guid
		$ cf env <Sample Application Name>
		Example)
		$ cf env sample-api-node-caller
		Getting env variables for app sample-api-node-caller in org real / space ops as admin...
		OK
		
		<<Skip>>
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
		
		<<Skipped>> 
		
		## Check API usage
		$ curl 'http://abacus-usage-reporting.<PaaS-TA Domain>/v1/metering/organizations/<organization that deployed sample application>/aggregated/usage'
		
		예)
		$ curl 'http://abacus-usage-reporting.bosh-lite.com/v1/metering/organizations/877d01b2-d177-4209-95b0-00de794d9bba/aggregated/usage'

## <div id='30'/>2.8. Sample Code

Sample Code downloaded from the site below.

[Download](https://nextcloud.paas-ta.org/index.php/s/mEbGNcJjrEj7GWx/download)

[Java_Api_Service_Metering_Image01]:./images/Java_Api_Service_Metering/meteringAPI_development_range.png
[Java_Api_Service_Metering_Image02]:./images/Java_Api_Service_Metering/sampleAPI_Service.png


### [Index](https://github.com/PaaS-TA/Guide-eng/blob/master/README.md) > [AP User Guide](../README.md) > Java API Service Metering Development
