### [Index](https://github.com/PaaS-TA/Guide-eng/blob/master/README.md) > [AP User Guide](../README.md) > Public API Development


## Table of Contents
1. [Document Outline](#1)
     * [1.1. Purpose](#2)
     * [1.2. Range](#3)
     * [1.3. References](#4)
2. [Selecting API Service](#5)
     * [2.1. Data Portal Sign in and Login](#6)
     * [2.2. API Search](#7)
3. [API Service Broker Outline](#8)
     * [3.1. Outline](#9)
     * [3.2. Service Broker APIs](#10)
     * [3.3. How API Service Broker Broker Works](#11)
4. [API Service Broker Implementation](#12)
     * [4.1. API Service Broker Setting File](#13)
         * [4.1.1. Common Settings](#14)
         * [4.1.2. Service Settings](#15)
         * [4.1.3. Plan Settings](#16)
     * [4.2. Catalog](#17)
         * [4.2.1. Request](#18)
         * [4.2.2. Response](#19)
     * [4.3. Provision](#20)
         * [4.3.1. Request](#21)
         * [4.3.2. Response](#22)
     * [4.4. Update](#23)
         * [4.4.1. Request](#24)
         * [4.4.2. Response](#25)
     * [4.5. Bind](#26)
         * [4.5.1. Request](#27)
         * [4.5.2. Response](#28)
     * [4.6. Unbind](#29)
         * [4.6.1. Request](#30)
         * [4.6.2. Response](#31)
     * [4.7. Deprovision](#32)
         * [4.7.1. Request](#33)
         * [4.7.2. Response](#34)
5. [API Service Broker Deployment](#35)
     * [5.1. Log in to Open Cloud Platform](#36)
     * [5.2. Create Service Broker](#37)
         * [5.2.1. Create Service Broker](#38)
         * [5.2.2. Check Created Service Broker](#39)
         * [5.2.3. Check Catalog](#40)
     * [5.3. Allow Service Access](#41)
     * [5.4. Check Marketplace](#42)
6. [Add/ Remove API Service](#43)
     * [6.1. Public API Service Broker Settings File Definitions](#44)
     * [6.2. Naver API Service Broker Settings File Definitions](#45)
7. [API Service Broker Verification](#46)
     * [7.1. Sample Application Outline](#47)
     * [7.2. Using API Service](#48)
         * [7.2.1. Using API Service](#49)
         * [7.2.2. Naver Open API Service](#50)
     * [7.3. Acquire API service key](#51)
         * [7.3.1. Obtain Public Data Portal API Service Key](#52)
         * [7.3.2. Obtain Naver Open API Service Key](#53)
     * [7.4. Sample Application Deployment](#54)
         * [7.4.1. Open Cloud Platform Login](#55)
         * [7.4.2. Application Deployment](#56)
     * [7.5. Create Service Instance](#57)
         * [7.5.1. Create Service Instance](#58)
         * [7.5.2. Check Created Service Instance](#59)
     * [7.6. Bind Service](#60)
         * [7.6.1. Bind Service](#61)
         * [7.6.2. Check Bound Service](#62)
     * [7.7. Check Sample Application Behavior](#63)


# <div id='1'></div> 1. Document Outline

### <div id='2'></div> 1.1. Purpose
Applications deployed on open cloud platforms (OpenPaas) will be able to use externally provided services through service brokers. This document implements and validates service brokers so that external API services can be used on an open cloud platform. Through this, platform operators can register API services necessary for developers in the marketplace of open cloud platforms, and the purpose of this document is to help users understand better.

### <div id='3'></div> 1.2. Range 
Platform operators expose the services that developers will use to marketplaces on open cloud platforms. Therefore, this document describes the implementation and deployment of API service brokers, and how to add API services in the application (Chapter 2-6) and guides the application to use API services in the application (Chapter 7), which is the domain of application developers, not platform operators, but necessary for verification.
To understand Chapter 4 API Service Broker Implementation of this document, it is necessary to be familiar with Chapter 2 Service Broker API Development Guide of the Service Pack Development Guide document, and only the JAVA method implementation is described.

### <div id='4'></div> 1.3. References
- Servicepack Development Guide
- CF document
- Incheon Culture and Arts Information Public Open API Center(**<http://iq.ifac.or.kr/openAPI/look/culture_guide.php>**)
- Naver Developer's Center(**<http://developer.naver.com/wiki/pages/Tutorial_JavaScript>**)

# <div id='5'></div>  2. Selecting API Service
Open cloud platform operators select APIs to provide platform users (developers) and provide them on the platform through service brokers. Since the provision of API services through service brokers requires the authority of the platform operator, developers can ask the operator to provide the necessary API services. Service brokers implement separately for each API portal because there may be differences in implementation methods depending on the portal that provides/introduces the service. For example, the public data portal (https://www.data.go.kr/) API service) implements and provides the public data portal API service, and the Naver (http://www.naver.com/) API service implements and provides the Naver API, service broker.

※ Public API services are provided by each public institution and are open to the public in the form of an integrated introduction of these API services in a data portal. Representative data portals include the public data portal (https://www.data.go.kr/), and Seoul Open Data Square (http://data.seoul.go.kr/)).

※ This document describes a guide based on the public data portal (https://www.data.go.kr/). Details may vary depending on each portal or API service.

### <div id='6'></div> 2.1. Data Portal Sign in and Log in
※ Most data portals allow only logged-in users to issue service keys. Service key issuance is a role of a developer, not a platform operator. It guides the registration and login process because it is necessary to issue a service key to proceed with Chapter 7 (API service broker verification). 

To use the API of the public data portal, you must be signed in. Access the public data portal (https://www.data.go.kr) and click the [Sign In] button at the top to proceed to Sign in.

![2-1-0-0]

Select a general member or institutional member from the moved membership screen and press the [Join] button.

![2-1-0-1]

Enter name and email then click the [Join] button.

![2-1-0-2]

When there is no user created with the information input even after checking the registration page, a screen shown below will appear.

![2-1-0-3]
![2-1-0-4]

"Public Data Portal Terms of Use" and "Guidance on the Collection and Use of Personal Information" will be introduced. If you agree with the content, indicate that you agree to each of the terms and conditions and press the [Agree] button.

![2-1-0-5]
![2-1-0-6]

Enter user basic information and user contact information. 
(1) Verify the email from the user's contact information. A verification email will be sent by clicking the [Verify Email] button and a (2)Enter Verification Code field appears. Enter the verification code sent to the email input and click the [OK] button to complete the process. Click (3) [Finish] button the complete registering and go to the main page.

![2-1-0-7]

Sign-in is completed.

![2-1-0-8]

On the main screen of the public data portal, log in by pressing the [Login] button at the top.

![2-1-0-9]

Enter ID and PW to log in.

### <div id='7'></div>  2.2. API Search
Access the data portal to search for API services. Check the Open API service of the data portal.

![2-2-0-0]

Go to Open API Category.

![2-2-0-1]

If you go to the Open API category, you can search by ①API service name or check the list classified by ②various filters.

![2-2-0-2]

① To obtain information related to cultural events at the national level, it searches for information on performance exhibition information inquiry service
② Click the API in the API service list to view the details of the API service in the search results.

![2-2-0-3]

Click the Details button on the proceeded screen.

![2-2-0-4]

As the window expands, information about API services can be checked. 
① Guide documents for the API service can be downloaded.
② Information such as a request address (Endpoint), a request/response field, and allowed traffic can be checked for each operation provided by the API service. Although there is no problem adding and using API services to the API service broker with the information provided here alone, depending on the API service, it may be necessary to check the guide document or go to the associated link. The information required to add API services to the service broker is as follows.

| Portal URL      | URL of the portal that provides/introduces the service |
|-------------|-----------------------------|
| Service Provider    | Enter the name of the organization that provides the service and the URL. |
| Guide Document URL | URL where the platform user (developer) can check the guide document of the service. The platform user can use the service by checking the operation or request parameters in the guide document of this URL. |
| Request address(Endpoint)   | URL for using API services. Service brokers use the term Endpoint. |
| Allowable Traffic    | The number and unit of requests permitted by the service provider. Depending on the service, the number and unit of requests differ from 1,000 per day to 100,000 per month. |

# <div id='8'></div>   3. API Service Broker Outline
### <div id='9'></div>  3.1. Outline
Service brokers serve to connect open cloud platforms with services outside the platform. Service brokers can be developed directly by platform operators or developed by external service providers who want to provide services within the platform and provided to platform operators. Service Broker Development is embodied by implementing six APIs: Catalog, Provision, Update, Bind, Unbind, and Deprovision. Since the development of a service broker varies depending on the characteristics of the service to be provided or the service policy, the understanding of the service to be provided before development should be based on. The two API service brokers that guide this document are designed to suit the characteristics of the API service.

### <div id='10'></div> 3.2. Service Broker APIs
- Catalog: Creates the list of services.
- Provision: Creates service instances.
- Update: Updates catalog.
- Bind: Binds service and application.
- Unbind Unnind service and applications.
- Deprovision: Deletes service instance.

| <b>Service Broker APIs</b>      | <b>Related Commands</b> |
|-------------|-----------------------------|
| Catalog    | cf create-service-broker [Service Broker Name] [username] [password] [Service Broker URL] <br>check: cf service-access |
| Provision | cf create-service [Service Name] [Plan Name] [Service Instance Name] <br>check: cf services |
| Update   | cf update-service-broker [Service Broker Name] [username] [password] [Service Broker URL] <br>check: cf service-access |
| Bind    | cf bind-service [Application Name] [Service Instance Name] <br>check: cf env [Application Name] |
| Unbind    | cf unbind-service [Application Name] [Service Instance Name] <br>check: cf env |
| Deprovision    | cf delete-service [Service Instance Name] <br>check: cf services |
※ For more information on service broker APIs, see [2.5 Development Guide] in the Service Pack Development Guide document.

### <div id='11'></div> 3.3. How API Service Broker Broker Works
![3-3-0-0]

The API service broker contains information on the service to be used among the API services introduced in the API portal in a configuration file. When a platform operator or platform user enters a command into an open cloud platform and sends an API request to a service broker, the API service broker uses the information defined in the configuration file to send a response in the form requested by the platform. Based on this, the platform executes the operation for each command. It is accomplished by implementing six APIs that communicate with the implementation platform of the service broker.

# <div id='12'></div>   4. API Service Broker Implementation
### <div id='13'></div> 4.1. API Service Broker Setting File
##### <div id='14'></div> 4.1.1 Common Settings
API 서비스 브로커를 통해 서비스되는 서비스들이 공통적으로 갖게 되는 값을 설정파일에 정의하였다.

| <b>Key Value</b>      | <b>Description</b> | <b>Example of Value</b> |
|-------------|-----------------------------|-----------------------------|
| DashboardUrl    | Dashboard URL. It can be understood as a portal URL, and services of one service broker have a common dashboard URL because service brokers are classified according to the portal that provides the API. | http://www.data.go.kr |
| SupportUrl | Enter the official site address of the open cloud platform. | http://www.openpaas.org |

##### <div id='15'></div> 4.1.2 Service Settings
A separate value for each service was defined in the setting file. Different services are distinguished by attaching a service number to the key value. As the service is added, the service number can be increased, and the service number must be assigned in order from number 1.

| <b>Key Value</b>      | <b>Description</b> | <b>Example of Value</b> |
|-------------|-----------------------------|-----------------------------|
| Service1.Name | Service Name. It can be determined arbitrarily but must be a unique value. The same service name cannot be used by other service brokers. | PublicPerformance |
| Service1.Description | Enter a brief description of the API service. If not defined in the configuration file, enter "no service description". It is exposed to users in a service marketplace on an open cloud platform. | Performances exhibits information display |
| Service1.Provider | Name or URL of the provider of the API service. | http://www.culture.go.kr |
| Service1.DocumentUrl | It is a URL where technical documents and guide documents for API services can be checked. | https://www.data.go.kr/subMain.jsp#/L3B1YnIvd....(Skip) |
| Service1.Endpoint | Service URL/URI of API service. | http://www.culture.go.kr/openapi/rest/publicperformancedisplays |

##### <div id='16'></div> 4.1.3 Plan Settings
At least one plan must be defined in the configuration file for each service. If a plan is not defined, the service cannot be used on an open cloud platform. The key value of the plan includes the service number and the plan number, and for example, if the key value is [Service1.Plan1.Name], it means that it is the name of the first plan of service 1. Plan numbers should be given in order from number 1.

| <b>Key Value</b>      | <b>Description</b> | <b>Example of Value</b> |
|-------------|-----------------------------|-----------------------------|
| Service1.Plan1.Name | Plan Name. Plan name doesn't need a unique value as long as the services are different. | Basic |
| Service1.Plan1.Description | Enter a brief description of the plan. If not defined in the configuration file, enter "no plan description. | total 1,000,000 calls |
| Service1.Plan1.Bullet | Billing information for the plan. Since it is an API service, enter the maximum number of allowed calls. Multiple inputs require modification of the code. | 1,000,000 calls |
| Service1.Plan1.Unit | Enter the units of the maximum number of allowed calls. For example, it can be entered as per month, per day, weekly, total, etc. | Total |

### <div id='17'></div> 4.2. Catalog
※ Refer to [2.5.1. Catalog API Guide] of the Service Pack Development Guide document for detailed information.  
##### <div id='18'></div> 4.2.1 Request
- Route
  ```
  GET /v2/catalog
  ```
  
- cURL
  ```
  curl -H "X-Broker-API-Version: 2.5" http://username:password@broker-url/v2/catalog
  ```
  ※ 'username: password' means the authentication ID and the authentication password of the service broker. When implementing a service broker, it is a value defined in the library. The defined authentication ID is 'admin' and the authentication password is 'cloudfoundry'.
  
##### <div id='19'></div> 4.2.2 Response
※{1} is a variable value to retrieve the key values of the service and plan defined in the setup file in order within the code.
<br>※ A character in [ ](bracket)of the key value refers to the key value of the service defined in the setting file.

- body

  | <b>Response Field</b>      | <b>Description</b> | <b>Sample Data</b> |
  |-------------|-----------------------------|-----------------------------|
  | services* | List of objects containing each service object | |
  | &nbsp;&nbsp;id* | Service ID. Must be unique, created with a combination of values read from the settings file and specified text. <br>Form: "Service"+{1}+[Service1.Name]+"ServiceID" | |
  | &nbsp;&nbsp;name* | Service Name. The value read from the settings file. <br>Key value: [Service1.Name] | PublicPerformance |
  | &nbsp;&nbsp;description* | Service Description. The value read from the settings file. <br>Key value: [Service1.Name] | Performances, exhibits information display |
  | &nbsp;&nbsp;bindable* | If it is bindable with the application. boolean type. <br>set value: true | true |
  | &nbsp;&nbsp;tags | Expose the classification, attributes, or underlying technology of a service <br>set value: "Public API Service" | Public API Service |
  | &nbsp;&nbsp;metadata | A list of metadata for providing the service. refer to 'Service Metadata' below for a detailed description | |
  | &nbsp;&nbsp;requires* | Authorization list of services provided by the user. currently, only syslog_drain authority is provided <br>set values: "syslog_drain" | syslog_drain |
  | plan_updateable | Whether the service supports modifying plans. boolean type. <br>set value: false | false |
  | &nbsp;&nbsp;plans* | List of objects containing each plan object for the service | |
  | &nbsp;&nbsp;&nbsp;&nbsp;id* | Plan ID. Must be unique, created with a combination of values read from the settings file and specified text. <br>form: "Service"+{1}+[Service1.Name]+"Plan"+{1}+[Service1.Plan1.Name]+"PlanID" | Service1 PublicPerformance Plan1 basic PlanID |
  | &nbsp;&nbsp;&nbsp;&nbsp;name* | Plan name. The value read from the settings file. <br>Key value: [Service1.Plan1.Name] | basic |
  | &nbsp;&nbsp;&nbsp;&nbsp;description* | Plan name. The value read from the settings file. <br>Key value: [Service1.Plan1.Description] | total 1,000,000 calls |
  | &nbsp;&nbsp;&nbsp;&nbsp;metadata | A list of metadata for the plan of the service. Check 'Plan metadata' below for a detailed description | |
  | &nbsp;&nbsp;&nbsp;&nbsp;free | Display the Paid/Free Billing Policy.Boolean type. Default is true. <br>set value: true | true |

- Service Metadata

  | <b>Response Field</b>      | <b>Description</b> | <b>Sample Data</b> |
  |-------------|-----------------------------|-----------------------------|
  | metadata.displayName | Service name as it appears in the graphical client <br>Key value: [Service1.Name] | PublicPerformance |
  | metadata.imageUrl | Image URL for the service <br>set value: "no image" | no image |
  | metadata.longDescription | Service Detailed Description <br>Key value: [Service1.Description] | Performances, exhibits information display |
  | metadata.providerDisplayName | Name of the organization that provides the actual service <br>Key value: [Service1.Provider] | Performances, exhibits information display |
  | metadata.documentationUrl | Service-related document URL <br>Key value: [Service1.DocumentationUrl] | https://www.data.go.kr/subMain.jsp#/L3B1YnIvdXNlL3ByaS9Jcm9z...(skip) |
  | metadata.supportUrl | service supporting URL <br>Key value: [SupportUrl] | http://www.openpaas.org |

- Plan Metadata

  | <b>Response Field</b>      | <b>Description</b> | <b>Sample Data</b> |
  |-------------|-----------------------------|-----------------------------|
  | metadata.bullets | Billing information for the plan. Enter the maximum number of calls because it is an API service <br>Key value: [Service1.Plan1.Bullet] | 1,000,000 calls |
  | metadata.costs | Cost information of the plan. Consists of the map type amount and string type unit. <br>amount set value: "KRW",0 <br>※KRW is in Korean currency <br>unit Key value: [Service1.Plan1.Unit] | Json structure <br>"costs": [{"amount": {"KRW": 0} "unit": "total" }] |
  | metadata.displayName | The plan name that appears in the graphical client <br>Key value: [Service1.Plan1.Name] | basic |

### <div id='20'></div> 4.3. Provision
※ Refer to [2.5.1 Provision Guide] of the Service Pack Development Guide document for detailed information.
##### <div id='21'></div>  4.3.1 Request
- Route
  ```
  PUT /v2/service_instances/:instance_id
  ```
  ※ Instance_id is a unique ID generated by the cloud controller when a service instance creation command is entered.
  
- cURL
  ```
  $ curl http://username:password@broker-url/v2/service_instances/:instance_id -d '{
  "service_id":        "Service1 PublicPerformance ServiceID",
  "plan_id":           "Service1 PublicPerformance Plan1 basic PlanID",
  "organization_guid": "[org-guid-here]",
  "space_guid":        "[space-guid-here]"
  }' -X PUT -H "X-Broker-API-Version: 2.5" -H "Content-Type: application/json"
  ```
  ※ 'username: password' means the authentication ID and the authentication password of the service broker. When implementing a service broker, it is a value defined in the library. The defined authentication ID is 'admin' and the authentication password is 'cloudfoundry'.

- body

  | <b>Request Field</b>      | <b>Description</b> | <b>Sample Data</b> |
  |-------------|-----------------------------|-----------------------------|
  | service_id* | Service ID created from catalog | Service1 PublicPerformance ServiceID |
  | plan_id* | Plan ID created from catalog  | Plan ID created from catalog |
  | organization_guid* | GUID value of user org requesting for provision | [The GUID value that the cloud controller used for Org identification] |
  | space_guid* | GUID value of user Space requesting for provision | [The GUID value that the cloud controller used for space identification] |
  
##### <div id='22'></div> 4.3.2 Response
| <b>Response Field</b>      | <b>Description</b> | <b>Sample Data</b> |
|-------------|-----------------------------|-----------------------------|
| dashboard_url | Uses the public data portal URL <br>Key value: [DashboardUrl] | http://www.data.go.kr |

### <div id='23'></div> 4.4. Update
※ Refer to [2.5.3 Update Instance API Guide] in the Service Pack Development Guide document for detailed information.
##### <div id='24'></div> 4.4.1 Request
- Route
  ```
  PATCH /v2/service_instances/:instance_id
  ```
  ※ instance_id is the unique ID of the service instance created by the provision
  
- cURL
  ```
  $ curl http://username:password@broker-url/v2/service_instances/:instance_id -d '{
  "plan_id": "Service1 PublicPerformance Plan2 special PlanID"
  }' -X PATCH -H "X-Broker-API-Version: 2.4" -H "Content-Type: application/json"
  ```
  ※ 'username: password' means the authentication ID and the authentication password of the service broker. When implementing a service broker, it is a value defined in the library. The defined authentication ID is 'admin' and the authentication password is 'cloudfoundry'.

- body

  | <b>Request Field</b>      | <b>Description</b> | <b>Sample Data(Performance Exhibition Information API)</b> |
  |-------------|-----------------------------|-----------------------------|
  | plan_id |Plan ID to modify which was created from catalog | Service1 PublicPerformance Plan2 special PlanID |
  | service_id* | Service ID to modify the plan which was created from the catalog | Service1 PublicPerformance ServiceID |

##### <div id='25'></div> 4.4.2 Response
| <b>Response Field</b>      | <b>Description</b> |
|-------------|-----------------------------|
| {} | When the update was successfully done, it responds in "{}" form. |

### <div id='26'></div> 4.5. Bind
※ Refer to [2.5.5 Bind API Guide] in the Service Pack Development Guide document for detailed information.
##### <div id='27'></div> 4.5.1 Request
- Route
  ```
  PUT /v2/service_instances/:instance_id/service_bindings/:binding_id
  ```
  
- cURL
  ```
  $ curl http://username:password@broker-url/v2/service_instances/
  :instance_id/service_bindings/:binding_id -d '{
  "plan_id":       "Service1 PublicPerformance Plan1 basic PlanID",
  "service_id":     "Service1 PublicPerformance ServiceID",
  "app_guid":       "app-guid-here"
  }' -X PUT -H "X-Broker-API-Version: 2.5" -H "Content-Type: application/json"
  ```
  ※ 'username: password' means the authentication ID and the authentication password of the service broker. When implementing a service broker, it is a value defined in the library. The defined authentication ID is 'admin' and the authentication password is 'cloudfoundry'.

- body

  | <b>Request Field</b>      | <b>Description</b> | <b>Sample Data</b> |
  |-------------|-----------------------------|-----------------------------|
  | plan_id | Plan ID of the service instance to bind which was created from the catalog | Service1 PublicPerformance Plan1 basic PlanID |
  | service_id | Service ID of the service instance that you are binding which was created in the catalog | Service1 PublicPerformance ServiceID |
  | app_guid | GUID of the application to bind | GUID of the application to bind |

##### <div id='28'></div> 4.5.2 Response
- body

  | <b>Response Field</b>      | <b>Description</b> | <b>Sample Data</b> |
  |-------------|-----------------------------|-----------------------------|
  | credentials | Credential information that allows the application to access the service. It is provided in hash form. Check 'Credentials' for detailed information | |
  | syslog_drain_url | Log URL for applications bound to open cloud platforms | |
  
- Credentials

  | <b>Credential</b>      | <b>Description</b> | <b>Sample Data</b> |
  |-------------|-----------------------------|-----------------------------|
  | url | Endpoints in API services defined in the settings file <br>Key value: [Service1.Endpoint] | http://www.culture.go.kr/openapi/rest/publicperformancedisplays |
  | serviceKey | Authentication key issued from service provider to use API service <br>※ Ente when binding the service | [Key value issued by the user] |
  | documentUrl | URL for checking API service technical documentation, development guide, etc <br>Key value: [Service1.DocumentationUrl] | https://www.data.go.kr/subMain.jsp#/L3B1YnIvdXNlL3ByaS... (skip) |
  
### <div id='29'></div> 4.6. Unbind
※ Refer to [2.5.6 Unbind API Guide] of the service pack development guide document for detailed information.
##### <div id='30'></div> 4.6.1 Request
- Route
  ```
  DELETE /v2/service_instances/:instance_id/service_bindings/:binding_id
  ```
  
- cURL
  ```
  $ curl 'http://username:password@broker-url/v2/service_instances/:instance_id/
  service_bindings/:binding_id?service_id=Service1 PublicPerformance ServiceID &plan_id=Service1 PublicPerformance Plan1 basic PlanID' -X DELETE -H "X-Broker-API-Version: 2.4"
  ```
  ※ 'username: password' means the authentication ID and the authentication password of the service broker. When implementing a service broker, it is a value defined in the library. The defined authentication ID is 'admin' and the authentication password is 'cloudfoundry'.

- body

  | <b>Request Field</b>      | <b>Description</b> | <b>Sample Data</b> |
  |-------------|-----------------------------|-----------------------------|
  | service_id | Service ID of the service instance to unbind which was created from catalog | Service1 PublicPerformance ServiceID |
  | plan_id | Plan ID of the service instance to unbind which was created from catalog | Service1 PublicPerformance Plan1 basic PlanID |

##### <div id='31'></div> 4.6.2 Response
- body

  | <b>Response</b>      | <b>Description</b> |
  |-------------|-----------------------------|
  | {} | If the unbinding was successfully done, it responds in "{}" form. |

### <div id='32'></div> 4.6. Deprovision
※ Refer to [2.5.4 Deferral API Guide] of the service pack development guide document for detailed information.
##### <div id='33'></div> 4.7.1 Request
- Route
  ```
  DELETE /v2/service_instances/:instance_id
  ```
  
- cURL
  ```
  $ curl 'http://username:password@broker-url/v2/service_instances/:instance_id?service_id=
  Service1 PublicPerformance ServiceID plan_id=Service1 PublicPerformance Plan1 basic PlanID -X DELETE -H "X-Broker-API-Version: 2.5"
  ```
  ※ 'username: password' means the authentication ID and the authentication password of the service broker. When implementing a service broker, it is a value defined in the library. The defined authentication ID is 'admin' and the authentication password is 'cloudfoundry'.

- body

  | <b>Request Field</b>      | <b>Description</b> | <b>Sample Data</b> |
  |-------------|-----------------------------|-----------------------------|
  | service_id | Service ID of the service instance that is being deprovisioned, which was created from catalog | Service1 PublicPerformance ServiceID |
  | plan_id | Plan ID of the service instance that is being deprovisioned, which was created from catalog | Service1 PublicPerformance Plan1 basic PlanID |

##### <div id='34'></div> 4.7.2 Response
- body

  | <b>Response</b>      | <b>Description</b> |
  |-------------|-----------------------------|
  | {} | All responses of Body of JSON Object is in "{}" form. |

# <div id='35'></div>   5. API Service Broker Deployment
Drives a service broker to use in an Open Cloud Platform. Service brokers can be used by driving in the form of an application within an open cloud platform as a single application, but this document guides you through how to use it in an open cloud platform by running it on an external server. An external server in which a service broker is driven must configure an environment in which communication with an open cloud platform is possible. There is no separate guidance on the operation of the service broker.  

### <div id='36'></div> 5.1. Log in to Open Cloud Platform
Create a service broker from the Open Cloud Platform. Administrator authority of open cloud platform is required to create a service broker. Log in to the open cloud platform with an administrator account through the command below.
```
  $ cf login
```

Example)
```
  $ cf login
  Email> admin
  Password> 123456
```

### <div id='37'></div> 5.2. Create Service Broker
Create a service broker from Open Cloud Platform. When the service broker creating command as shown below is entered, the catalog will be performed and will define service based on the service information in the configuration file.

##### <div id='38'></div> 5.2.1 Create Service Broker
```
  $ cf create-service-broker [Service Broker Name] [Authentication  ID] [Authentication  Password] [Service Broker Address]
```
※[Service Broker Name] is a name that refers to a service broker within an open cloud platform and is arbitrarily determined by the user. [Authentication ID] and [Authentication Password] are values defined in the library when implementing a service broker. The authentication ID defined in the provided sample is 'admin', and the authentication password is 'cloudfoundry'. [Service Broker Address] means the address where the service broker is driven.

예시)
```
  $ cf create-service-broker public-api-sb admin cloudfoundry http://10.30.60.100:8080
```

##### <div id='39'></div> 5.2.2 Check Created Service Broker
The command to check the list of service brokers. Can be used to verify that the service broker is created normally.
```
  $ cf service-brokers
```

![5-2-2-0]

##### <div id='40'></div> 5.2.3 Check Catalog
When a service broker is created on an open cloud platform, it proceeds with a catalog that generates a list of API services serviced through the service broker. The cataloged services are added to the service access list, and by checking the service access list, it is possible to confirm that the catalog has been successfully performed.
```
  $ cf service-access
```

![5-2-3-0]

### <div id='41'></div> 5.3. Allow Service Access
The catalog of the service access list in [5.2.3. Check Catalog] is set to none. This setting has to be modified so that the service list will be seen in the marketplace. Use the command below to allow access to the service.

```
  $ cf enable-service-access [Service Name] -p [Plan Name]
```
※ [Service name] and [plan name] refer to the service name and plan name defined in the service broker configuration file. Through the cf service-access command, you can check the list of services and plan names of services that are allowed to access the service.

Example)
```
  $ cf enable-service-access PublicPerformance -p basic
```

After allowing access to the services to be exposed to the marketplace using the command above, check the service access list again with the command below to confirm that the access setting of the services has been changed to all.
```
  $ cf service-access
```

![5-3-0-0]

### <div id='42'></div> 5.4. Check Marketplace
Services that allow access are registered in the marketplace, making them available to developers. Use the command to check the marketplace.

```
  $ cf marketplace
```

![5-4-0-0]

# <div id='43'></div>   6. Add/ Remove API Service
The add or remove API services from a sample service broker is accomplished by inserting or deleting information in the [4.1 API Service Broker Setting File]. The sample service broker is designed to read the necessary information through the key value of the configuration file and use it in the service catalog and bind. The file name of the configuration file is 'application-mvc. properties' and information on API services required by the sample service broker is as follows.
※ The sample service broker is designed to read the service or plan in order of service number and plan number.
If the service number and plan number are not entered in the order from number 1, the service or plan cannot be read normally. After adding or removing a service or plan, ensure that the service number or plan number continues from No. 1.

| <b>Setting files key value</b>      | <b>Description</b> |
|-------------|-----------------------------|
| Service[Service Number]. Name | Enter the name of the  API service in English |
| Service[Service Number].Description | Description of the API service. When registered in a marketplace, it is exposed to platform users (developers |
| Service[Service Number].Provider | URL or company name who provides API services |
| Service[Service Number].DocumentUrl | URL where you can check the technical documentation or specification of the API service |
| Service[Service Number].EndPoint | A URL that requests for API |
| Service[Service Number].Plan[Plan Number].Name | Plan name for the service. <br>Depending on the service, the plan number is given because it can have multiple plans, but API services generally have a single plan. |
| Service[Service Number].Plan[Plan Number].Description | Description of the plan for the service |
| Service[Service Number].Plan[Plan Number].Bullet | The form of service restrictions in the plan of the service. Generally, API services manage services by giving a limit on the number of calls. |
| Service[Service Number].Plan[Plan Number].Unit | The limit unit of the applicable plan for the service. In general, API services limit the number of daily or monthly calls. |

### <div id='44'></div> 6.1. Public API Service Broker Settings File Definitions
Among the two sample service brokers provided, the service list of the public API service brokers is defined as follows.

| <b>Settings file key value</b>      | <b>Value</b> |
|-------------|-----------------------------|
| Service 1 - Performance Exhibition Information Inquiry Service |
| Service1.Name | PublicPerformance |
| Service1.Description | Performances exhibits information display |
| Service1.Provider | http://www.culture.go.kr |
| Service1.DocumentUrl | https://www.data.go.kr/subMain.jsp#/L3B1YnIvdXNlL3ByaS9Jcm9zT3BlbkFwaURldGFpbC9vcGVuQXBpTGlzdFBhZ2UkQF4wMTJtMSRAXnB1YmxpY0RhdGFQaz0xNTAwMDEyMCRAXmJybUNkPU9DMDAwOCRAXnJlcXVlc3RDb3VudD0zNDQkQF5vcmdJbmRleD1PUEVOQVBJ |
| Service1.EndPoint | http://www.culture.go.kr/openapi/rest/publicperformancedisplays |
| Service1.Plan1.Name | Basic |
| Service1.Plan1.Description | total 1,000,000 calls |
| Service1.Plan1.Bullet | 1,000,000 calls |
| Service1.Plan1.Unit | Total |
| Service 2 - Incheon's Cultural Event |
| Service2.Name | IncheonCulture |
| Service2.Description | Culture performances information held in Incheon City |
| Service2.Provider | http://iq.ifac.or.kr/ |
| Service2.DocumentUrl | http://iq.ifac.or.kr/openAPI/look/ |
| Service2.EndPoint | http://iq.ifac.or.kr/openAPI/real/search.php |
| Service2.Plan1.Name | Basic |
| Service2.Plan1.Description | 5,000 calls per day |
| Service2.Plan1.Bullet | 5,000 calls |
| Service2.Plan1.Unit | per day |
| Service 3 - Daejeon Cultural Festival |
| Service3.Name | DaejeonFestival |
| Service3.Description | Culture Festival information held in Daejeon City |
| Service3.Provider | http://data.daejeon.go.kr/ |
| Service3.DocumentUrl | https://www.data.go.kr/subMain.jsp#/L3B1YnIvcG90L215cC9Jcm9zTXlQYWdlL29wZW5EZXZHdWlkZVBhZ2UkQF4wMTJtMSRAXnB1YmxpY0RhdGFQaz0xNTAwNjk2OSRAXnB1YmxpY0RhdGFEZXRhaWxQaz11ZGRpOjJkZjQ4YzY3LWIxYjAtNGMyOS04YTc3LWQyMmQ2Mjc3MTJmZCRAXm9wcnRpblNlcU5vPTgyNTYkQF5tYWluRmxhZz10cnVl |
| Service3.EndPoint | http://data.daejeon.go.kr/openapi-data/service/rest/festival/festivalDaejeonService/festivalDaejeon |
| Service3.Plan1.Name | Basic |
| Service3.Plan1.Description | 1,000 calls per day |
| Service3.Plan1.Bullet |1,000 calls |
| Service3.Plan1.Unit | per day |
| Service 4 - Exhibition performance/ theme park information |
| Service4.Name | JeonnamPerformanceList |
| Service4.Description | Performances, exhibits information held in Jeonnam |
| Service4.Provider | http://api.namdokorea.com/ |
| Service4.DocumentUrl | http://api.namdokorea.com/main |
| Service4.EndPoint | http://api.namdokorea.com/openapi/performance/list |
| Service4.Plan1.Name | Basic |
| Service4.Plan1.Description | 5,000 calls per day |
| Service4.Plan1.Bullet | 5,000 calls |
| Service4.Plan1.Unit | per day |

### <div id='45'></div> 6.2.  Naver API Service Broker Settings File Definitions
Among the two sample service brokers provided, the service list of Naver API service brokers is defined as follows.

| <b>Setting file key value</b>      | <b>Value</b> |
|-------------|-----------------------------|
| Service 1 - Naver Map Service |
| Service1.Name | NaverMap |
| Service1.Description | Naver map API service |
| Service1.Provider | http://www.naver.com |
| Service1.DocumentUrl | http://developer.naver.com/wiki/pages/JavaScript |
| Service1.EndPoint | http://openapi.map.naver.com/openapi/naverMap.naver |
| Service1.Plan1.Name | basic |
| Service1.Plan1.Description | 1,000,000 calls per day |
| Service1.Plan1.Bullet | 1,000,000 calls |
| Service1.Plan1.Unit | per day |
| Service 2 - Naver Address-Coordinate Translation Service |
| Service2.Name | NaverAddressToGPS |
| Service2.Description | Convert address into gps coordinate |
| Service2.Provider | http://www.naver.com |
| Service2.DocumentUrl | http://developer.naver.com/wiki/pages/JavaScript#section-JavaScript-_EC_9A_94_EC_B2_ADURLRequestUrl |
| Service2.EndPoint | http://openapi.map.naver.com/api/geocode |
| Service2.Plan1.Name | basic |
| Service2.Plan1.Description | 1,000,000 calls |
| Service2.Plan1.Bullet | 1,000,000 calls |
| Service2.Plan1.Unit | per day |
| Service 3 - Naver Search Service |
| Service3.Name | NaverSearch |
| Service3.Description | Naver search API(Blog, News, Movie, Local, etc.) |
| Service3.Provider | http://www.naver.com |
| Service3.DocumentUrl | http://developer.naver.com/wiki/pages/SrchAPI |
| Service3.EndPoint | http://openapi.naver.com/search |
| Service3.Plan1.Name | basic |
| Service3.Plan1.Description | 25,000 calls |
| Service3.Plan1.Bullet | 25,000 calls |
| Service3.Plan1.Unit | per day |

# <div id='46'></div>   7. API Service Broker Verification
※ Check whether the platform user (developer) can bind the API service to the application and use it. To this end, a sample application using API service is produced to verify whether it operates normally.
<br>※ Verification is carried out with the implementation of the public data portal service broker and Naver Open API service broker completed to use API services necessary for verification.


### <div id='47'></div> 7.1. Sample Application Outline
The venue of the event is displayed on the map using the local cultural event API service provided by local governments across the country. The map is printed on the screen through the Naver Map API and a select box is created at the bottom so that users can select regions. Depending on the selected region, the location of the event is marked on the map using the cultural event API provided by the region.

### <div id='48'></div> 7.2.Using API Service
##### <div id='49'></div> 7.2.1. Public Data Portal API Services
##### 7.2.1.1 Performance exhibition information inquiry service
  1. API Introduction<br>
  It is a service that provides information on performance exhibitions at the national level. It is serviced by the [Culture Portal] (http://www.culture.go.kr), and searching by region, period, and field is possible. The service key is issued through the public data portal. If you apply for a service key, you must have a waiting period of 1 to 2 days until the approval.<br>
  ※ The sample data column of the request variable specifies the values used in the sample application.
  
  2. Classification<br>
  
  | Method | GET |
  |-------------|-----------------------------|
  | Endpoint | http://www.culture.go.kr/openapi/rest/publicperformancedisplays |
  | Operation | /area |

  3. Request<br>
  
  | <br>Variable Name | <br>Sample Data | <br>Description |
  |-------------|-----------------------------|-----------------------------|
  | serviceKey | 999 | The number of event information per page. Enter '999' to view the entire page. |
  | (sido) | (Seoul) | When inquiring about all regions, do not include variables, but when inquiring only about Seoul's performance exhibition information, but the value as 'Seoul'. |

  4. Response<br>
  
  | <br>Variable Name | <br>Description |
  |-------------|-----------------------------|
  | title | The title of the event. |
  | place | The place where the even is taken. |
  | startDate | The starting date of the event. |
  | endDate | The last day of the event. |
  | thumbnail | The URL of the event's image poster. |
  | gpsX | GPS X coordinates. |
  | gpsY | GPS Y coordinates. |

##### 7.2.1.2 Incheon Metropolitan City Cultural Event
  1. API Introduction<br>
  A service that provides event information in Incheon registered on the Incheon Culture and Arts Information Site. It is being served at the [Incheon Culture Portal](http://iq.ifac.or.kr). Although it is introduced on the public data portal, the issuance of service keys must be applied directly from the [Incheon Culture and Arts Information Public Open API Center](http://iq.ifac.or.kr/openAPI) Service keys are automatically approved, so there is no need to wait for the approval.
  
  2. Outline<br>
  
  | Method | GET |
  |-------------|-----------------------------|
  | Endpoint | http://iq.ifac.or.kr/openAPI/real/search.php |
  
  3. Request<br>
  
  | <br>Variable Name | <br>Sample Data | <br>Description |
  |-------------|-----------------------------|-----------------------------|
  | apiKey | [Service Key] | It is the service authorization key. Do not put in sample data because the user has to issue directly. |
  | pSize | 999 | The number of event information per page. Enter the maximum value of '999' to query the maximum data. |
  | resultType | xml | You can specify the output method of the response result between json and xml. |
  | svID | culture | This is a service ID for inquiring the cultural event information. |
  | srh_periodType | p | It is a value for inquiring about event information by period. If omitted, events that are not currently held are also inquired about. |
  | srh_sDate | culture | If you enter the variable 'srh_periodType', it is the date that becomes the search criterion. The sample application queries are based on the requested date, so the date of the day is put in the form of 'yyyyMMdd'. |

  4. Response<br>
  
  | <br>Variable Name | <br>Description |
  |-------------|-----------------------------|
  | title | The title of the event. |
  | place | The place where the event is taken. Since the coordinate value is not responded to, the coordinate value is obtained by searching for a place name using the Naver search API. |
  | startDate | The starting date of the event. |
  | endDate | The last day of the event. |
  | thumbnail | The URL of the event's image poster. |

##### 7.2.1.3 Daejeon Metropolitan City Cultural Events
  1. API Introduction<br>
  It is an API service for the Daejeon Cultural Festival provided by Daejeon Metropolitan City Public Information [Open API Service] (http://data.daejeon.go.kr)t provides information on cultural festivals in Daejeon Metropolitan City, and service keys can be issued on both the public data portal and the Daejeon Public Information Open API Service. If you apply for a service key, it will take about two to three days to approve and issue it.
  
  2. Classification<br>
  
  | Method | GET |
  |-------------|-----------------------------|
  | Endpoint | http://data.daejeon.go.kr/openapi-data/service/rest/festival/festivalDaejeonService/festivalDaejeon |
  
  3. Request<br>
  
  | <br>Variable Name | <br>Sample Data | <br>Description |
  |-------------|-----------------------------|-----------------------------|
  | serviceKey | [Service Key] |  It is the service authorization key. Do not put in sample data because the user has to issue directly. |
  | numOfRows | 999 | The number of event information per page. Enter the maximum value of '999' to query the maximum data. |
  
  4. Response<br>
  
  | <br>Variable Name | <br>Description |
  |-------------|-----------------------------|
  | title | The title of the event. |
  | place | The place where the event is taken. Since the coordinate value is not responded to, the coordinate value is obtained by searching for a place name using the Naver search API |
  | startDate | The starting date of the event. |
  | endDate | The last day of the event. |
  | thumbnail | The URL of the event's image poster. |

##### 7.2.1.4 Exhibition performance/ theme park information
  1. API Introduction<br>
  It is an API service provided by [Jeonnam Smart Tourism Culture Open API](http://api.namdokorea.com). It provides information on exhibition performances and theme parks in Jeollanam-do, but exhibition performance information is rare and virtually close to API services that provide information on tourist attractions. Application for service key issuance is available on the public data portal and the [Jeonnam-do Public Data Community Center] (http://data.jeonnam.go.kr), and it takes about one to two days to approve and issue.
  
  2. Classification<br>
  
  | Method | GET |
  |-------------|-----------------------------|
  | Endpoint | http://api.namdokorea.com/openapi/performance/list |
  
  3. Request<br>
  
  | <br>Variable Name | <br>Sample Data | <br>Description |
  |-------------|-----------------------------|-----------------------------|
  | key | [Service Key] | It is the service authorization key. Do not put in sample data because the user has to issue directly. |
  | pageSize | 999 | The number of event information per page. Enter the maximum value of '999' to query the maximum data. |
  | format | xml | You can specify the output method of the response result between json and xml. |
  
  4. Response<br>
  
  | <br>Variable | <br>Description |
  |-------------|-----------------------------|
  | NAME | The name of the tourist attraction. |
  | NEWADDR | It is the address of a tourist attraction. Since the coordinate value is not responded to, the coordinate value for that address is obtained using the Naver address-coordinate translation API. |
  | MASTERIMG | The image URL of the tourist attraction. |

##### <div id='50'></div> 7.2.1. Naver Open API Service
##### 7.2.1.1 Naver Map
  1. API Introduction<br>
  The map API provided from the [Naver Developers Center Open API](http://developer.naver.com/wiki/pages/OpenAPI). The sample application uses a Naver map using JavaScript. To this end, the following tags are written in the HTML file.
  
  ```
   <script type="text/javascript" src="[Endpoint]?ver=2.0&key=[Service key]"></script>
  ```
  In the application, it is input and used in the following form.
  ```
   <script type="text/javascript" src="http://openapi.map.naver.com/openapi/naverMap.naver?ver=2.0&key=f32441ebcd3cc9de474f80s1wf1e54e3"></script>
  ```
  Refer to the URL below for detailed usage. 
  Naver Developer's Center Tutorial: http://developer.naver.com/wiki/pages/Tutorial_JavaScript

##### 7.2.2.2 Naver address-to-coordinate translation
  1. API Introduction<br>
  It is an address-to-coordinate translation API provided by [Naver Developer's Center Open API](http://developer.naver.com/wiki/pages/OpenAPI) When an address is entered as a request variable, a coordinate value for the address is responded. If the address is not correct, it does not respond to the value or it responds to the wrong value. Since the service key uses the same key as the Naver Map API, the number of calls per service key is counted together.
  
  2. Classification<br>
  
  | Method | GET |
  |-------------|-----------------------------|
  | Endpoint | http://openapi.map.naver.com/api/geocode |
  
  3. Request<br>
  
  | <br>Variable Name | <br>Sample Data | <br>Description |
  |-------------|-----------------------------|-----------------------------|
  | key | [Service Key] |  The service authentication key. The user needs to be issued directly, so the sample data is not included. Use the same key value as the Naver Map API. |
  | query | [Address] | Enter the address you want to convert to coordinates. |
  
  4. Response<br>
  
  | <br>Variable Name | <br>Description |
  |-------------|-----------------------------|
  | x | GPS X coordinate value for address requested for search. |
  | y | GPS Y coordinate value for address requested for search. |

##### 7.2.2.3 Naver Search
  1. API Introduction<br>
  It is a regional search API provided by [Naver Developer's Center Open API](http://developer.naver.com/wiki/pages/OpenAPI). Naver search API provides search APIs such as blogs, news, books, and regions. Which of these searches will be used is determined through the tag value of the request variable. Naver regional search responds to information such as addresses and coordinates for search terms, and the corresponding coordinate values use a Cartec coordinate system (WGS84) rather than a longitude coordinate system (WGS84) used by the Naver address-coordinate transformation API.
  
  2. Classification<br>
  
  | Method | GET |
  |-------------|-----------------------------|
  | Endpoint | http://openapi.naver.com/search |
  
  3. Request<br>
  
  | <br>Variable Name | <br>Sample Data | <br>Description |
  |-------------|-----------------------------|-----------------------------|
  | key | [Serive Key] | This is the service authentication key. Do not put in sample data because the user has to issue directly |
  | query | [Address] | Enter the name of the place you want to search. |
  | target | local | Enter to use regional search among Naver search APIs. |
  | start | 1 | The starting location of the search. |
  | display | 1 | This is the number of search results output. Naver search API uses only the first result value to expose multiple search results in a high-accuracy order. |
  
  4. Response<br>
  
  | <br>Variable Name | <br>Description |
  |-------------|-----------------------------|
  | mapx | The X-coordinate value for the address for which the search was requested. It uses a coordinate system different from other APIs as a Cartec coordinate system value. |
  | mapy | The Y-coordinate value for the address for which the search was requested. It uses a coordinate system different from other APIs as a Cartec coordinate system value. |

### <div id='51'></div> 7.3. API Acquire API service key
##### <div id='52'></div> 7.3.1. Obtain Public Data Portal API Service Key
Most APIs introduced on public data portals can issue service keys on public data portals, but some must apply directly to service providers. Among the four public data portal APIs described in this document, [7.2.1.1, Performance Exhibition Information Inquiry Service], [7.2.1.3, Daejeon Metropolitan City Cultural Festival], and [7.2.1.4 Exhibition Performance/ Theme Park Information] can issue service keys on the public data portal. On the other hand, in the case of [7.2.1.2. Incheon Cultural Event], the service key acquisition procedure of the public data portal, and the service key acquisition procedure of the [Incheon Culture and Arts Information Public Open API Center](http://iq.ifac.or.kr/openAPI) are divided and described.
##### 7.3.1.1 Public Data Portal Service Key Acquisition Procedure
  1. Sign in and login<br>
  ※ Refer to [2.1. Data Portal Sign in and login] in Chapter 2 of this document for public data portal membership and login.

  2. Application for the service key<br>
  ![7-3-1-0]<br>
  After completing the login, search for the API that you want to issue the service key in the search field at the top center of the main screen.

  ![7-3-1-1]<br>
  Find the API you want to use in the search results and click it.
  
  ![7-3-1-2]<br>
  (1) [Application for utilization] Press the button, to go to the service key application screen and proceed with the application process.<br>
  Check details about the API service and technical documents for each service by pressing the (2) [Detailed Information] button.

  ![7-3-1-3]<br>
  Select service type.
  
  ![7-3-1-4]<br>
  Select utilization information.
  
  ![7-3-1-5]<br>
  Select which of the API service's detailed functions to use.
  
  ![7-3-1-6]<br>
  Accept the(1) license display and click the (2) [Apply] button.
  
  ![7-3-1-7]<br>
  Check the notification message indicating that the service key application process has been completed.

##### 7.3.1.2 Incheon Culture and Arts Information Public Open API Center Service Key Acquisition Procedure
  1. Sign in and login<br>
  To use the API service of the Incheon Culture and Arts Information Public Open API Center, you must be a member of the [Incheon Cultural Foundation] (http://www.ifac.or.kr)site. First, go to [Incheon Culture and Arts Information Public Open API Center](http://iq.ifac.or.kr/openAPI).

  ![7-3-1-8]<br>
  After accessing, click [Sign in] at the top right of the main page.
  
  ![7-3-1-9]<br>
  When the [Sign in] button is clicked, it goes to the Incheon Culture and Arts Foundation family site. elect one of IPIN or mobile phone authentication to check whether you have (1) registered as a member or not and proceed to (2) authentication.

  ![7-3-1-10]<br>
  Click the [Sign in] button and register.
  
  ![7-3-1-11]<br>
  ![7-3-1-12]<br>
  ![7-3-1-13]<br>
  On the agreement screen of the terms and conditions, (1) check the member agreement and the guidance on the (2) collection and use of personal information, and press the (3) I agree button.
  
  ![7-3-1-14]<br>
  Enter the basic information and select whether to receive the information and press the [Membership] button. Basic information must be entered into the mailing address.
  
  ![7-3-1-15]<br>
  Check if the registration is completed.
  
  ![7-3-1-16]<br>
  Access the main page of [Incheon Culture and Arts Information Public Open API Center](http://iq.ifac.or.kr/openAPI) and click [Login] to log in.
  
  2. Apply for Service Key<br>
  ![7-3-1-17]<br>
  On the API tour screen, you can check the list of available APIs. Press the [Apply] button on the right side of the API to use to proceed with the service key application process.

  ![7-3-1-18]<br>
  On the application form completion screen, (1) fill in all required input fields, (2) accept the terms and conditions, and (3) press the [OK] button to complete the service key application process.
  
  ![7-3-1-19]<br>
  The service key application registration has been completed.
  
  3. Verify the service key issuance<br>
  ![7-3-1-20]<br>
  On the main screen, click the [Key Issue/Manage] button in the middle right of the [Incheon Culture and Arts Information Public Open API Center](http://iq.ifac.or.kr/openAPI).

  ![7-3-1-21]<br>
  If you go to the authentication key management screen, you can check (1) the service key, (2) traffic, (3) approval status, and (4) the delete service key.<br>
  ※ In the case of [7.2.1.2. Incheon Metropolitan City Cultural Event], the service key is applied directly to the API service provider, so unlike the application of the service key on the public data portal, there is no waiting period and approval is automatically approved, so it can be used immediately.

##### <div id='53'></div> 7.3.2 Obtain Naver Open API Service Key
[2.2.1. Naver Map], [2.2.2. Naver address-to-coordinate translation], [2.2.3. Naver Search] To get the API service key, you must be logged in with Naver ID. Access to [Naver Developer Center's Open API Page](http://developer.naver.com/wiki/pages/OpenAPI) and log in with Naver ID  or sign in.<br>

  1. Sign in and Login<br>
  ![7-3-2-0]<br>
  Access [Naver Developer Center's Open API Page](http://developer.naver.com/wiki/pages/OpenAPI) and click [Sign in] at the right.

  ![7-3-2-1]<br>
  ![7-3-2-2]<br>
  ![7-3-2-3]<br>
  ![7-3-2-4]<br>
  On the agreement screen of the terms and conditions, check the consent of Naver's terms and conditions and instructions on the collection and use of personal information, and click the [I Agree] button at the bottom.
  
  ![7-3-2-5]<br>
  Enter input items including a mobile phone number on the subscription screen, and press the (1) [Authentication] button to authenticate the mobile phone. Enter the authentication number delivered through the mobile phone,  press the(2) [OK] button to complete the authentication, and press the (3) [Sign In] button to complete the registration.
  
  ![7-3-2-6]<br>
  Access [Naver Developer Center's Open API Page](http://developer.naver.com/wiki/pages/OpenAPI)and click the [Login] button from the right top part and log in.
  
  2. Apply for Service Key<br>
  ![7-3-2-7]<br>
  After login, go to the Open API page of Naver Developer Center again, and you can check the [Issue/Manage Key] button in the middle of the right.

  ![7-3-2-8]<br>
  Key management for API services provided by Naver may be performed on the key issuance/management screen.<br>
  Click the (1) [Add Key] button to get the service key of [7.2.2.3. Naver Search] API, and click (2) [Add Key] button of [7.2.2.1. Naver Map], [7.2.2.2. Naver address-to-coordinate translation] API to issue the service key. [7.2.2.1. Naver Map] and [7.2.2.2. Naver address-to-coordinate translation] API service can be used commonly with one key.<br>
	
  (1)Issuing Naver Search API Key.<br>
  Click the [Add Key] button in the Naver Search API.<br>

  ![7-3-2-9]<br>
  ① You must enter domain information to use the service. If you do not enter it correctly, you cannot use the API service. In this case, the domain used by the application in the open cloud platform must match the value input in the Identifier field. The domain of the application is determined when the application is deployed on an open cloud platform.<br>
  Refer to: [7.3. Sample Application Deployment]<br>
  ② Naver Search API requires mobile phone authentication in the process of issuing keys.<br>
  ③ Enter the security character on the left. Do not separate uppercase and lowercase characters in security text input.<br>
  ④ Check if you understand and agree to the terms and conditions.<br>
  ⑤ Click the [Issue Key] button and complete issuing of the key.<br>

  (2) Issuing Naver Map API Key<br>
  Click the [Add Key] button on the Naver Map API<br>
  
  ![7-3-2-10]<br>
  ① Select the use environment.<br>
  ② Must enter URL information because you selected 'Web' in your environment. If it is not entered correctly, you cannot use the API service. In this case, the value input in the URL field and the domain used by the application in the open cloud platform should match. The domain of the application is determined when the application is deployed on an open cloud platform.<br>
  Refer to: [7.4.2. Application Deployment]<br>
  ③ Enter the security character on the left. Do not separate uppercase and lowercase characters in security text input.<br>
  ④ Check if you understand and agree to the terms and conditions.<br>
  ⑤ Click the [Issue Key] button and complete the issuing of the key.<br>

  3. Check Service Key<br>
  ![7-3-2-11]<br>
  Access to [Naver Developer Center's Open API Page](http://developer.naver.com/wiki/pages/OpenAPI) and log in. Click the [Issue/ Manage Key] button located on the right side.

  ![7-3-2-12]<br>
  Go to the screen where you can manage the entire service key of the API service provided by Naver. ① For each API, the service key, registration URL, and ② the number of API calls issued can be checked. [7.2.2.3. Naver Search] API service can be used as the issuing key of the top search API, and [7.2.2.1. Naver Map] and [7.2.2.2.2.1. Naver Address-coordinate conversion] API service can be used as the issuing key of the map API.

### <div id='54'></div> 7.4. Sample Application Deployment
##### <div id='55'></div> 7.4.1. Open Cloud Platform Login
Log in to an open cloud platform with a developer account. The developer account can be used by the operator by creating the account and granting developer rights. It does not describe the creation and authorization of developer accounts. The verification procedure described in this document may be verified by accessing the administrator account.

```
  $ cf login
```

Example)
```
  $ cf login
  Email> kimdojun
  Password> asd20kwl
```

##### <div id='56'></div> 7.4.2. Application Deployment
Deploy applications on an open cloud platform. Application distribution uses the following command:

```
  $ cf push [Application Name] -p [File Path] -b [Buildpack] -m [Memory] --no-start
```

※ [Application Name]may specify an arbitrary value as the name of an application used in an open cloud platform. If no option is given at the time of deployment, the domain of the application is determined based on the application name. Naver Open API is required to enter a URL when issuing a service key. If the domain of the application is different from the URL entered when the service key is issued, the API cannot be used..<br>
※ '-p [File Path]' means the path to the built file.<br>
※ '-b [Buildpack]'is an option to select a buildpack of an application. If you do not enter it, it will automatically find it. The list of selectable buildpacks can be checked with the command 'cf buildpacks'.<br>
※ '-m [memory]'is an option to specify the size of memory that the application will occupy. <br>
※ When an application is deployed, it will automatically run the application, but the '--no-start' option prevents the application from running automatically.<br>

Example)
```
  $ cf push public-naver -p /home/kimdojun/sample/openpaas-service-java-broker-public-naver-api-sample.war -b java_buildpack -m 1024M --no-start
```

### <div id='57'></div> 7.5. Create Service Instance
Instances can be created for services registered in the marketplace.<br>
※ It is the role of the platform operator to register the service in the marketplace. Refer to [5.3. Allow Service Access] in this document for information on how to register services in the marketplace.
##### <div id='58'></div> 7.5.1. Create Service Instance
```
  $ cf create-service [Service Name] [Plan Name] [Service Instance Name]
```
※ [Service Name] and [Plan Name]refer to the service name and plan name defined in the service broker configuration file. [Service Instance Name]is a value that the user randomly inputs, but the provided sample application is implemented to identify the service with the service instance name, so if the arbitrary value is used, the sample application cannot identify the service, so the sample application source needs to be modified.<br><br>

※ The sample application forces the service instance name in the open cloud platform to follow the table below. It is a form in which the first letter of the service name is changed to lowercase. Modification of the sample application is required to use any service instance name.<br>

| <b>API Service</b>      | <b>Service Name</b> | <b>Service Instance Name</b> |
|-------------|-----------------------------|-----------------------------|
| Performance exhibition information inquiry service | PublicPerformance | publicPerformance |
| Incheon Metropolitan City Cultural Event | IncheonCulture | incheonCulture |
| Daejeon Metropolitan City Cultural Festival | DaejeonFestival | daejeonFestival |
| Exhibition performance/ theme park information | JeonnamPerformanceList | jeonnamPerformanceList |
| Naver Map | NaverMap | naverMap |
| Naver Address-Coordinate Translation | NaverAddressToGPS | naverAddressToGPS |
| Naver Search | NaverSearch | naverSearch |

Example)
```
  $ cf create-service PublicPerformance basic publicPerformance
```

##### <div id='59'></div> 7.5.2. Check Created Service Instance
The creation of service instances enables the binding of services and applications on an open cloud platform. To verify the creation of a service instance, use a command that exposes the list of service instances.

```
  $ cf services
```

It is possible to check the instance name, service name, plan, and bound application of the generated service. The value of bound apps refers to the application bound.<br>
![7-5-2-0]

### <div id='60'></div> 7.6. Bind Service
Bind the service instance created from [7.5. Create Service Instance] and Application to be able to use API service.
##### <div id='61'></div> 7.6.1. Bind Service
생성된 서비스 인스턴스와 어플리케이션을 바인드한다. 바인드 시, -c 옵션을 주어 사용자가 직접 발급받은 해당 서비스의 서비스키를 입력할 수 있도록 한다. 샘플 서비스 브로커의 경우는 서비스 키를 입력하지 않을 경우 바인드가 정상적으로 진행되지 않도록 설계되어 있다.

```
  $ cf bind-service [Application Name] [Service Instance Name] -c '{"serviceKey":"[Service Key]"}'
```
※ [Service Instance Name] is the name entered when creating the service instance and is different from the service name. Refer to [7.5.1. Create Service Instance] for the difference between service name and service instance name.

Example)
```
  $ cf bind-service public-naver publicPerformance -c '{"serviceKey":"adn2d241aaml%%3D"}'
```
All seven API services to be used by the application are bound as shown above.

##### <div id='62'></div> 7.6.2. Check Bound Service
When you bind an application and service, information about each bound service can be found in the VCAP_SERVICES information of the application.

```
  $ cf env [Application Name]
```

Example)
```
  $ cf env public-naver
```
![7-6-2-0]<br>
If you check VCAP_SERVICES information, you can check service information such as plans, endpoints, and service keys entered through -c options for each service as shown in the figure. The sample application takes the endpoint and the service key from this VCAP_SERVICES information and uses the required API service.

### <div id='62'></div> 7.7. Check Sample Application Behavior
You can check the URL of the application through the command.

```
  $ cf apps
```

![7-7-0-0]<br>
Verify the application if it is running and access the URL from a web browser. The sample application is implemented to output 'Hello World' on the main screen.

![7-7-0-1]<br>
Go to [URL]/main and check if the map is displayed on the screen.

![7-7-0-2]<br>
※ In most cases, when a map is not visible on the screen, the URL entered when applying for the Naver Map service key is not the same as the application URL. For example, when issuing a Naver Map API service key, http://public-naver.10.244.0.34.xip.io, a URL that is checked on the top screen, must be entered in the URL input window. For details, refer to [(2) Issuing Naver Map API Key] and [7.3. Sample Application Deployment] of [2. Obtain Service Key] from [7.3.2 Obtain Naver Open API service key].<br>

Check that each API operates normally by selecting an area in the lower-left select box of the screen.

![7-7-0-3]


[2-1-0-0]:./images/publicapi/2-1-0-0.png
[2-1-0-1]:./images/publicapi/2-1-0-1.png
[2-1-0-2]:./images/publicapi/2-1-0-2.png
[2-1-0-3]:./images/publicapi/2-1-0-3.png
[2-1-0-4]:./images/publicapi/2-1-0-4.png
[2-1-0-5]:./images/publicapi/2-1-0-5.png
[2-1-0-6]:./images/publicapi/2-1-0-6.png
[2-1-0-7]:./images/publicapi/2-1-0-7.png
[2-1-0-8]:./images/publicapi/2-1-0-8.png
[2-1-0-9]:./images/publicapi/2-1-0-9.png
[2-2-0-0]:./images/publicapi/2-2-0-0.png
[2-2-0-1]:./images/publicapi/2-2-0-1.png
[2-2-0-2]:./images/publicapi/2-2-0-2.png
[2-2-0-3]:./images/publicapi/2-2-0-3.png
[2-2-0-4]:./images/publicapi/2-2-0-4.png
[3-3-0-0]:./images/publicapi/3-3-0-0.png
[5-2-2-0]:./images/publicapi/5-2-2-0.png
[5-2-3-0]:./images/publicapi/5-2-3-0.png
[5-3-0-0]:./images/publicapi/5-3-0-0.png
[5-4-0-0]:./images/publicapi/5-4-0-0.png
[7-3-1-0]:./images/publicapi/7-3-1-0.png
[7-3-1-1]:./images/publicapi/7-3-1-1.png
[7-3-1-2]:./images/publicapi/7-3-1-2.png
[7-3-1-3]:./images/publicapi/7-3-1-3.png
[7-3-1-4]:./images/publicapi/7-3-1-4.png
[7-3-1-5]:./images/publicapi/7-3-1-5.png
[7-3-1-6]:./images/publicapi/7-3-1-6.png
[7-3-1-7]:./images/publicapi/7-3-1-7.png
[7-3-1-8]:./images/publicapi/7-3-1-8.png
[7-3-1-9]:./images/publicapi/7-3-1-9.png
[7-3-1-10]:./images/publicapi/7-3-1-10.png
[7-3-1-11]:./images/publicapi/7-3-1-11.png
[7-3-1-12]:./images/publicapi/7-3-1-12.png
[7-3-1-13]:./images/publicapi/7-3-1-13.png
[7-3-1-14]:./images/publicapi/7-3-1-14.png
[7-3-1-15]:./images/publicapi/7-3-1-15.png
[7-3-1-16]:./images/publicapi/7-3-1-16.png
[7-3-1-17]:./images/publicapi/7-3-1-17.png
[7-3-1-18]:./images/publicapi/7-3-1-18.png
[7-3-1-19]:./images/publicapi/7-3-1-19.png
[7-3-1-20]:./images/publicapi/7-3-1-20.png
[7-3-1-21]:./images/publicapi/7-3-1-21.png
[7-3-2-0]:./images/publicapi/7-3-2-0.png
[7-3-2-1]:./images/publicapi/7-3-2-1.png
[7-3-2-2]:./images/publicapi/7-3-2-2.png
[7-3-2-3]:./images/publicapi/7-3-2-3.png
[7-3-2-4]:./images/publicapi/7-3-2-4.png
[7-3-2-5]:./images/publicapi/7-3-2-5.png
[7-3-2-6]:./images/publicapi/7-3-2-6.png
[7-3-2-7]:./images/publicapi/7-3-2-7.png
[7-3-2-8]:./images/publicapi/7-3-2-8.png
[7-3-2-9]:./images/publicapi/7-3-2-9.png
[7-3-2-10]:./images/publicapi/7-3-2-10.png
[7-3-2-11]:./images/publicapi/7-3-2-11.png
[7-3-2-12]:./images/publicapi/7-3-2-12.png
[7-5-2-0]:./images/publicapi/7-5-2-0.png
[7-6-2-0]:./images/publicapi/7-6-2-0.png
[7-7-0-0]:./images/publicapi/7-7-0-0.png
[7-7-0-1]:./images/publicapi/7-7-0-1.png
[7-7-0-2]:./images/publicapi/7-7-0-2.png
[7-7-0-3]:./images/publicapi/7-7-0-3.png




### [Index](https://github.com/PaaS-TA/Guide-eng/blob/master/README.md) > [AP User Guide](../README.md) > Public API Development
