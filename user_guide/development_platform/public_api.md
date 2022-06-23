### [Index](https://github.com/PaaS-TA/Guide-eng/blob/master/README.md) > [AP User Guide](../README.md) > Public API Development


## Table of Contents
1. [Document Outline](#1)
     * [1.1. Purpose](#2)
     * [1.2. Range](#3)
     * [1.3. References](#4)
2. [Selecting API Service](#5)
     * [2.1. Data Portal Sign in and Log in](#6)
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
Applications deployed on open cloud platforms (OpenPaas) will be able to use externally provided services through service brokers. This document implements and validates service brokers so that external API services can be used on an open cloud platform. Through this, platform operators can register API services necessary for developers in the marketplace of open cloud platforms, and the purpose of this document is help users understand better.

### <div id='3'></div> 1.2. Range 
Platform operators expose the services that developers will use to marketplaces on open cloud platforms. Therefore, this document describes the implementation and deployment of API service brokers, and how to add API services in the application (Chapter 2-6) and guides the application to use API services in the application (Chapter 7), which is the domain of application developers, not platform operators, but necessary for verification.
In order to understand Chapter 4 API Service Broker Implementation of this document, it is necessary to be familiar with Chapter 2 Service Broker API Development Guide of the Service Pack Development Guide document, and only the JAVA method implementation is described.

### <div id='4'></div> 1.3. References
- Servicepack Development Guide
- CF document
- Incheon Culture and Arts Information Public Open API Center(**<http://iq.ifac.or.kr/openAPI/look/culture_guide.php>**)
- Naver Developer's Center(**<http://developer.naver.com/wiki/pages/Tutorial_JavaScript>**)

# <div id='5'></div>  2. Selecting API Service
Open cloud platform operators select APIs to provide platform users (developers) and provide them on the platform through service brokers. Since the provision of API services through service brokers requires the authority of the platform operator, developers can ask the operator to provide the necessary API services. Service brokers implement separately for each API portal because there may be differences in implementation methods depending on the portal that provides/introduces the service. For example, the public data portal (https://www.data.go.kr/) API service) implements and provides the public data portal API service, and the Naver (http://www.naver.com/) API service implements and provides the Naver API service broker.

※ Public API services are provided by each public institution and are open to the public in the form of an integrated introduction of these API services in a data portal. Representative data portals include the public data portal (https://www.data.go.kr/), Seoul Open Data Square (http://data.seoul.go.kr/)).

※ This document describes guide based on the public data portal (https://www.data.go.kr/). Details may vary depending on each portal or API service.

### <div id='6'></div> 2.1. Data Portal Sign in and Log in
※ Most data portals allow only logged-in users to issue service keys. Service key issuance is a role of a developer, not platform operator. It guides registration and login process because it is necessary to issue a service key to proceed with Chapter 7 (API service broker verification). 

In order to use the API of the public data portal, you must be signed in. Access the public data portal (https://www.data.go.kr) and click the [Sign In] button at the top to proceed to Sign in.

![2-1-0-0]

Select a general member or institutional member from the moved membership screen and press the [Join] button.

![2-1-0-1]

Enter name and email then click [Join] button.

![2-1-0-2]

When there is no user created with the information input even after checking the registration page, a screen shown below will appear.

![2-1-0-3]
![2-1-0-4]

"Public Data Portal Terms of Use" and "Guidance on the Collection and Use of Personal Information" will be introduced. If you agree with the content, indicate that you agree to each of the terms and conditions and press the [Agree] button.

![2-1-0-5]
![2-1-0-6]

Enter user basic information and user contact information. 
(1) Verify the email from the user's contact information. Verification email will be sent by clicking the [Verify Email] button and a (2)Enter Verification Code field appears. Enter the verification code sent to the email inputed and click [OK] button to complete the pocess. Click (3) [Finish] button the complete registering and go to the main page.

![2-1-0-7]

Sign in is completed.

![2-1-0-8]

On the main screen of the public data portal, log in by pressing the [Login] button at the top.

![2-1-0-9]

Enter ID and PW to login.

### <div id='7'></div>  2.2. API Search
Access the data portal to search for API services. Check the Open API service of the data portal.

![2-2-0-0]

Go to Open API Category.

![2-2-0-1]

If you go to the Opn API category, you can search by ①API service name or check the list classified by ②various filters.

![2-2-0-2]

① To obtain information related to cultural events at the national level, it searches for information on performance exhibition information inquiry service
② Click the API in the API service list to view the details of the API service in the search results.

![2-2-0-3]

Click the Details button on the moved screen.

![2-2-0-4]

As the window expands, information about API services can be checked. 
① Guide documents for the API service can be downloaded.
② Information such as a request address (Endpoint), a request/response field, and allowed traffic can be checked for each operation provided by the API service. Although there is no problem adding and using API services to the API service broker with the information provided here alone, depending on the API service, it may be necessary to check the guide document or go to the associated link. The information required to add API services to the service broker is as follows.

| Portal URL      | URL of the portal that provides/introduces the service |
|-------------|-----------------------------|
| Service Provider    | Enter the name of the organization that actually provides the service and the URL. |
| Guide Document URL | URL where the platform user (developer) can check the guide document of the service. The platform user can use the service by checking the operation or request parameters in the guide document of this URL. |
| Request address(Endpoint)   | URL for using API services. Service brokers use the term Endpoint. |
| Allowable Traffic    | The number and unit of requests permitted by the service provider. Depending on the service, the number and unit of requests differ from 1,000 per day to 100,000 per month. |

# <div id='8'></div>   3. API Service Broker Outline
### <div id='9'></div>  3.1. Outline
Service brokers serve to connect open cloud platforms with services outside the platform. Service brokers can be developed directly by platform operators or developed by external service providers who want to provide services within the platform and provided to platform operators. Service Broker Development is embodied by implementing six APIs: Catalog, Provision, Update, Bind, Unbind, and Deprovision. Since the development of a service broker varies depending on the characteristics of the service to be provided or the service policy, the understanding of the service to be provided before development should be based on. The two API service brokers that provide guidance in this document are designed to suit the characteristics of the API service.

### <div id='10'></div> 3.2. Service Broker APIs
- Catalog: Creates the list of services.
- Provision: Creates service instances.
- Update: Updates catalog.
- Bind: Binds service and application.
- Unbind: Unnind service and applications.
- Deprovision: Deletes service instance.

| <b>Service Broker APIs</b>      | <b>Related Commands</b> |
|-------------|-----------------------------|
| Catalog    | cf create-service-broker [Service Broker Name] [username] [password] [Service Borker URL] <br>check: cf service-access |
| Provision | cf create-service [Service Name] [Plan Name] [Service Instance Name] <br>check: cf services |
| Update   | cf update-service-broker [Service Broker Name] [username] [password] [Service Broker URL] <br>check: cf service-access |
| Bind    | cf bind-service [Application Name] [Service Instance Name] <br>check: cf env [Application Name] |
| Unbind    | cf unbind-service [Application Name] [Service Instance Name] <br>check: cf env |
| Deprovision    | cf delete-service [Service Instance Name] <br>check: cf services |
※ For more information on service broker APIs, see [2.5 Development Guide] in the Service Pack Development Guide document.

### <div id='11'></div> 3.3. How API Service Broker Broker Works
![3-3-0-0]

he API service broker contains information on the service to be used among the API services introduced in the API portal in a configuration file. When a platform operator or platform user enters a command into an open cloud platform and sends an API request to a service broker, the API service broker uses the information defined in the configuration file to send a response in the form requested by the platform. Based on this, the platform executes the operation for each command. It is accomplished by implementing six APIs that communicate with the implementation platform of the service broker.

# <div id='12'></div>   4. API Service Broker Implementation
### <div id='13'></div> 4.1. API Service Broker Setting File
##### <div id='14'></div> 4.1.1 Common Settings
API 서비스 브로커를 통해 서비스되는 서비스들이 공통적으로 갖게 되는 값을 설정파일에 정의하였다.

| <b>Key Value</b>      | <b>Description</b> | <b>Example of Value</b> |
|-------------|-----------------------------|-----------------------------|
| DashboardUrl    | Dashboard URL. It can be understood as a portal URL, and services of one service broker have a common dashboard URL because service brokers are classified according to the portal that provides the API. | http://www.data.go.kr |
| SupportUrl | Enter the official site address of the open cloud platform. | http://www.openpaas.org |

##### <div id='15'></div> 4.1.2 Service Settings
A separate value for each service was defined in the setting file. Different services are distinguished by attaching a service number to the key value. As the service is added, the service number can be increased, and the service number must be assigned in order from number 1..

| <b>Key Value</b>      | <b>Description</b> | <b>Example of Value</b> |
|-------------|-----------------------------|-----------------------------|
| Service1.Name | Service Name. It can be determined arbitrarily, but must be a unique value. The same service name cannot be used by other service brokers. | PublicPerformance |
| Service1.Description | Enter a brief description of the API service. If not defined in the configuration file, enter "no service description". It is exposed to users in a service marketplace on an open cloud platform. | Performances, exhibits information display |
| Service1.Provider | Name or URL of the provider of the API service. | http://www.culture.go.kr |
| Service1.DocumentUrl | It is a URL where technical documents and guide documents for API services can be checked. | https://www.data.go.kr/subMain.jsp#/L3B1YnIvd....(Skip) |
| Service1.Endpoint | Service URL/URI of API service. | http://www.culture.go.kr/openapi/rest/publicperformancedisplays |

##### <div id='16'></div> 4.1.3 Plan Settings
At least one plan must be defined in the configuration file for each service. If a plan is not defined, the service cannot be used on an open cloud platform. The key value of the plan includes the service number and the plan number, and for example, if the key value is [Service1.Plan1.Name], it means that it is the name of the first plan of service 1. Plan numbers should be given in order from number 1.

| <b>Key Value</b>      | <b>Description</b> | <b>Example of Value</b> |
|-------------|-----------------------------|-----------------------------|
| Service1.Plan1.Name | Plan Name. Plan name doesnt need a unique value as long as the services are different. | Basic |
| Service1.Plan1.Description | Enter a brief description of the plan. If not defined in the configuration file, enter "no plan descriptio. | total 1,000,000 calls |
| Service1.Plan1.Bullet | Billing information for the plan. Since it is an API service, enter the maximum number of allowed calls. Multiple inputs require modification of the code. | 1,000,000 callsr |
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
  ※ 'username:password' means the authentication ID and the authentication password of the service broker. When implementing a service broker, it is a value defined in the library. The defined authentication ID is 'admin' and the authentication password is 'cloudfoundry'..
  
##### <div id='19'></div> 4.2.2 Response
※{1} is a variable value to retrieve the key values of the service and plan defined in the setup file in order within the code.
<br>※ A character in [ ](bracket)of the key value refers to the key value of the service defined in the setting file.

- body

  | <b>Response Field</b>      | <b>Description</b> | <b>Sample Data</b> |
  |-------------|-----------------------------|-----------------------------|
  | services* | List of objects containing each service object | |
  | &nbsp;&nbsp;id* | Service ID. Must be unique, created with a combination of values read from the settings file and specified text. <br>Form: "Service"+{1}+[Service1.Name]+"ServiceID" | |
  | &nbsp;&nbsp;name* | Service Name. Value read from the settings file. <br>Key value: [Service1.Name] | PublicPerformance |
  | &nbsp;&nbsp;description* | Service Description. Value read from the settings file. <br>Key value: [Service1.Name] | Performances, exhibits information display |
  | &nbsp;&nbsp;bindable* | If it is bindable with the application. boolean type. <br>set value: true | true |
  | &nbsp;&nbsp;tags | Expose the classification, attributes, or underlying technology of a service <br>set value: "Public API Service" | Public API Service |
  | &nbsp;&nbsp;metadata | A list of meta data for providing the service. refer to 'Service Metadata' below for detailed description | |
  | &nbsp;&nbsp;requires* | Authorization list of services provided by the user. currently, only syslog_drain authority is provided <br>set values: "syslog_drain" | syslog_drain |
  | plan_updateable | Whether the service supports modifying of plans. boolean type. <br>set value: false | false |
  | &nbsp;&nbsp;plans* | List of objects containing each plan object for the service | |
  | &nbsp;&nbsp;&nbsp;&nbsp;id* | Plan ID. Must be unique, created with a combination of values read from the settings file and specified text. <br>form: "Service"+{1}+[Service1.Name]+"Plan"+{1}+[Service1.Plan1.Name]+"PlanID" | Service1 PublicPerformance Plan1 basic PlanID |
  | &nbsp;&nbsp;&nbsp;&nbsp;name* | Plan name. Value read from the settings file. <br>Key value: [Service1.Plan1.Name] | basic |
  | &nbsp;&nbsp;&nbsp;&nbsp;description* | Plan name. Value read from the settings file. <br>Key value: [Service1.Plan1.Description] | total 1,000,000 calls |
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
  ※ 'username:password' means the authentication ID and the authentication password of the service broker. When implementing a service broker, it is a value defined in the library. The defined authentication ID is 'admin' and the authentication password is 'cloudfoundry'.

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
  ※ 'username:password' means the authentication ID and the authentication password of the service broker. When implementing a service broker, it is a value defined in the library. The defined authentication ID is 'admin' and the authentication password is 'cloudfoundry'.

- body

  | <b>Request Field</b>      | <b>Description</b> | <b>Sample Data(Performance Exhibition Information API)</b> |
  |-------------|-----------------------------|-----------------------------|
  | plan_id |Plan ID to modify which was created from catalog | Service1 PublicPerformance Plan2 special PlanID |
  | service_id* | Service ID to modify the plan which was created from the catalog | Service1 PublicPerformance ServiceID |

##### <div id='25'></div> 4.4.2 Response
| <b>Response Field</b>      | <b>Description</b> |
|-------------|-----------------------------|
| {} | When the update was successfully done, it responses in "{}" form. |

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
  ※ 'username:password' means the authentication ID and the authentication password of the service broker. When implementing a service broker, it is a value defined in the library. The defined authentication ID is 'admin' and the authentication password is 'cloudfoundry'.

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
  | credentials | Credential information that allows application to access the service. It is provided in hash form. Check 'Credentials' for detailed information | |
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
  ※ 'username:password' means the authentication ID and the authentication password of the service broker. When implementing a service broker, it is a value defined in the library. The defined authentication ID is 'admin' and the authentication password is 'cloudfoundry'.

- body

  | <b>Request Field</b>      | <b>Description</b> | <b>Sample Data</b> |
  |-------------|-----------------------------|-----------------------------|
  | service_id | Service ID of the service instance to unbind which was created from catalog | Service1 PublicPerformance ServiceID |
  | plan_id | Plan ID of the service instance to unbind which was created from catalog | Service1 PublicPerformance Plan1 basic PlanID |

##### <div id='31'></div> 4.6.2 Response
- body

  | <b>Response</b>      | <b>Description</b> |
  |-------------|-----------------------------|
  | {} | If the unbinding was successfully done, it responses in "{}" form. |

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
  ※ 'username:password' means the authentication ID and the authentication password of the service broker. When implementing a service broker, it is a value defined in the library. The defined authentication ID is 'admin' and the authentication password is 'cloudfoundry'.

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
Create service broker from the Open Cloud Platform. Administrator authority of open cloud platform is required to create service broker. Log in to the open cloud platform with an administrator account through the command below.
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
Create a service broker from Open Cloud Platform. When the service broker creating command as show below is entered, the catalog will be performed and will define service based on the service information in the configuration file.

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
When a service broker is created on an open cloud platform, it proceeds with a catalog that generates a list of API services serviced through the service broker. The cataloged services are added to the service access list, and by checking the service access list, it is possible to confirm that the catalog has been successfully performed..
```
  $ cf service-access
```

![5-2-3-0]

### <div id='41'></div> 5.3. Allow Service Access
The catalog of the service access list in [5.2.3. Check Catalog] is set to none. This setting has to be modified so that the service list will be seen at the marktetplace. Use the command below to allow the access to the service.

```
  $ cf enable-service-access [Service Name] -p [Plan Name]
```
※ [Service name] and [plan name] refer to the service name and plan name defined in the service broker configuration file. Through the cf service-access command, you can check the list of service and plan names of services that is allowed to access the service.

Example)
```
  $ cf enalbe-service-access PublicPerformance -p basic
```

After allowing access to the services to be exposed to the marketplace using the command above, check the service access list again with the command below to confirm that the access setting of the services has been changed to all.
```
  $ cf service-access
```

![5-3-0-0]

### <div id='42'></div> 5.4. Check Marketplace
Services that allow access are registered in the marketplace, making them available to developers. Use the command to check the market place.

```
  $ cf marketplace
```

![5-4-0-0]

# <div id='43'></div>   6. Add/ Remove API Service
The add or remove API services from a sample service broker is accomplished by inserting or deleting information in the [4.1 API Service Broker Setting File].The sample service broker is designed to read the necessary information through the key value of the configuration file and use it in the service catalog and bind. The file name of the configuration file is 'application-mvc.properties' and information on API services required by the sample service broker is as follows.
※ The sample service broker is designed to read the service or plan in order of service number and plan number.
If the service number and plan number are not entered in order from number 1, the service or plan cannot be read normally. After adding or removing a service or plan, ensure that the service number or plan number continues from No. 1.

| <b>Setting files key value</b>      | <b>Description</b> |
|-------------|-----------------------------|
| Service[Service Number].Name | Enter name of the  API service in english |
| Service[Service Number].Description | Description about the API service. When registered in a marketplace, it is exposed to platform users (developers |
| Service[Service Number].Provider | URL or company name who provides API services |
| Service[Service Number].DocumentUrl | URL where you can check the technical documentation or specification of the API service |
| Service[Service Number].EndPoint | A URL that requests for API |
| Service[Service Number].Plan[Plan Number].Name | Plan name for the service. <br>Depending on the service, the plan number is given because it can have multiple plans, but API services generally have a single plan. |
| Service[Service Number].Plan[Plan Number].Description | Description of the plan for the service |
| Service[Service Number].Plan[Plan Number].Bullet | The form of service restrictions in plan of the service. Generally, API services manage services by giving limit on the number of calls. |
| Service[Service Number].Plan[Plan Number].Unit | The limit unit of the applicable plan for the service. In general, API services limit the number of daily or monthly calls. |

### <div id='44'></div> 6.1. Public API Service Broker Settings File Definitions
Among the two sample service brokers provided, the service list of the public API service brokers is defined as follows.

| <b>Settings file key value</b>      | <b>Value</b> |
|-------------|-----------------------------|
| Service 1 - Performance Exhibition Information Inquiry Service |
| Service1.Name | PublicPerformance |
| Service1.Description | Performances, exhibits information display |
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
  It is a service that provides information on performance exhibitions at the national level. It is serviced by the [Culture Portal] (http://www.culture.go.kr), and searching by region, period, and field is possible. Service key is issued through the public data portal. If you apply for a service key, you must have a waiting period of 1 to 2 days until the approval.<br>
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
  | (sido) | (Seoul) | When inquiring all regions, do not include variables, but when inquiring only about Seoul's performance exhibition information, put the value as 'Seoul'. |

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
  A service that provides event information in Incheon registered on the Incheon Culture and Arts Information Site. It is being served at the [Incheon Culture Portal](http://iq.ifac.or.kr). Although it is introduced on the public data portal, the issuance of service keys must be applied directly from the [Incheon Culture and Arts Information Public Open API Center](http://iq.ifac.or.kr/openAPI) Service keys are automatically approved, so there is no need to wait for approval.
  
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
  | srh_periodType | p | It is a value for inquiring event information by period. If omitted, events that are not currently held are also inquired. |
  | srh_sDate | culture | If you enter the variable 'srh_periodType', it is the date that becomes the search criterion. The sample application queries based on the requested date, so the date of the day is put in the form of 'yyyyMMdd'. |

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
  It is an API service provided by [Jeonnam Smart Tourism Culture Open API](http://api.namdokorea.com).It provides information on exhibition performances and theme parks in Jeollanam-do, but exhibition performance information is rare and virtually close to API services that provide information on tourist attractions. Application for service key issuance is available on the public data portal and the [Jeonnam-do Public Data Community Center] (http://data.jeonnam.go.kr), and it takes about one to two days to approve and issue.
  
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
  The map API provided from the [Naver Developers Center Open API](http://developer.naver.com/wiki/pages/OpenAPI). The sample application uses a Naver map using JavaScript. To this end, the following tags are written in the HTML file..
  
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
Most APIs introduced on public data portals can issue service keys on public data portals, but some must apply directly to service providers. Among the four public data portal APIs described in this document, [7.2.1.1, Performance Exhibition Information Inquiry Service], [7.2.1.3, Daejeon Metropolitan City Cultural Festival], and [7.2.1.4 Exhibition Performance/ Theme Park Information] can issue service keys on the public data portal. On the other hand, in the case of [7.2.1.2. Incheon Cultural Event], the service key acquisition procedure of the public data portal and the service key acquisition procedure of the [Incheon Culture and Arts Information Public Open API Center](http://iq.ifac.or.kr/openAPI) are divided and described.
##### 7.3.1.1 Public Data Portal Service Key Acquisition Procedure
  1. Sign in and login<br>
  ※ Refer to [2.1. Data Portal Sign in and Log in] in Chapter 2 of this document for public data portal membership and login.

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
  In order to use the API service of the Incheon Culture and Arts Information Public Open API Center, you must be a member of the [Incheon Cultural Foundation] (http://www.ifac.or.kr)site. First, go to [Incheon Culture and Arts Information Public Open API Center](http://iq.ifac.or.kr/openAPI).

  ![7-3-1-8]<br>
  After accessing, click [Sign in] at the top right of the main page.
  
  ![7-3-1-9]<br>
  When the [Sign in] button is clicked, it goes to the Incheon Culture and Arts Foundation family site. elect one of IPIN or mobile phone authentication to check whether you have (1) registered as a member or not and proceed to (2) authentication.

  ![7-3-1-10]<br>
  Click [Sign in] button and register.
  
  ![7-3-1-11]<br>
  ![7-3-1-12]<br>
  ![7-3-1-13]<br>
  On the agreement screen of the terms and conditions, (1) check the member agreement and  the guidance on the (2) collection and use of personal information, and  press the (3) I agree button.
  
  ![7-3-1-14]<br>
  Enter the basic information and select whether to receive the information and press the [Membership] button. Basic information must be entered to the mailing address.
  
  ![7-3-1-15]<br>
  Check if the registration is completed.
  
  ![7-3-1-16]<br>
  Access to the main page of [Incheon Culture and Arts Information Public Open API Center](http://iq.ifac.or.kr/openAPI) and click [Login] to log in.
  
  2. Apply fo Service Key<br>
  ![7-3-1-17]<br>
  On the API tour screen, you can check the list of available APIs. Press the [Apply] button on the right side of the API to use to proceed with the service key application process.

  ![7-3-1-18]<br>
  On the application form completion screen, (1) fill in all required input fields, (2) accept the terms and conditions, and (3) press the [OK] button to complete the service key application process..
  
  ![7-3-1-19]<br>
  The service key application registration has been completed.
  
  3. Verify the service key issuance<br>
  ![7-3-1-20]<br>
  On the main screen, click the [Key Issue/Manage] button in the middle right of the [Incheon Culture and Arts Information Public Open API Center](http://iq.ifac.or.kr/openAPI).

  ![7-3-1-21]<br>
  인증키 관리 화면으로 이동하면 (1) 서비스키, (2) 트래픽, (3) 승인 상태를 확인할 수 있고 (4) 서비스키 삭제도 가능하다.<br>
  ※ [7.2.1.2. 인천광역시 문화행사]의 경우 API서비스 제공자에게 직접 서비스키 신청을 하게 되기 때문에 공공 데이터 포털에서 서비스키 신청을 하는 경우와 달리 대기기간이 없고 승인 역시 자동승인 되므로 신청 절차만 완료하면 바로 사용할 수 있다.

##### <div id='53'></div> 7.3.2 Obtain Naver Open API Service Key
[2.2.1. 네이버 지도], [2.2.2. 네이버 주소-좌표 변환], [2.2.3. 네이버 검색] API 서비스키를 발급받기 위해서는 네이버 ID로 로그인이 되어 있어야 한다. [네이버 개발자센터의 Open API 페이지](http://developer.naver.com/wiki/pages/OpenAPI)에 접속하여 네이버 ID로 로그인하거나 회원가입을 진행한다.<br>

  1. 회원가입 및 로그인<br>
  ![7-3-2-0]<br>
  [네이버 개발자센터의 Open API 페이지](http://developer.naver.com/wiki/pages/OpenAPI)에 접속하여 우측 중간 [회원가입] 버튼을 클릭한다.

  ![7-3-2-1]<br>
  ![7-3-2-2]<br>
  ![7-3-2-3]<br>
  ![7-3-2-4]<br>
  약관 동의 화면에서 필수 동의 항목인 네이버 이용약관과 개인정보 수집 및 이용에 대한 안내에 동의를 체크하고 하단의 [동의] 버튼을 클릭한다.
  
  ![7-3-2-5]<br>
  가입화면에서 휴대전화번호를 포함한 입력항목들을 입력하고 (1) [인증] 버튼을 눌러 휴대전화 인증을 실시한다. 휴대전화를 통해 전달된 인증번호를 입력하고 (2) [확인] 버튼을 눌러 인증을 마치고 (3) [가입하기] 버튼을 눌러 회원가입을 완료한다.
  
  ![7-3-2-6]<br>
  [네이버 개발자센터의 Open API 페이지](http://developer.naver.com/wiki/pages/OpenAPI)에 접속하여 우측 상단 [로그인] 버튼을 클릭하여 로그인 한다.
  
  2. 서비스키 신청<br>
  ![7-3-2-7]<br>
  로그인이 완료되고 다시 네이버 개발자센터의 Open API 페이지로 이동하면 우측 중간에 [키 발급/관리] 버튼을 확인할 수 있다.

  ![7-3-2-8]<br>
  키 발급/관리 화면에서 네이버에서 제공하는 API 서비스에 대한 키 관리를 할 수 있다.<br>
  (1) [키 추가]버튼을 눌러 [7.2.2.3. 네이버 검색] API의 서비스 키를 발급받을 수 있고 (2) [키 추가] 버튼을 눌러 [7.2.2.1. 네이버 지도], [7.2.2.2. 네이버 주소-좌표 변환] API의 서비스키를 발급받을 수 있다. 하나의 서비스키로 [7.2.2.1. 네이버 지도]와 [7.2.2.2. 네이버 주소-좌표 변환] API 서비스를 공통으로 사용할 수 있다.<br>
	
  (1) 네이버 검색 API 키 발급<br>
  네이버 검색 API의 [키 추가] 버튼을 클릭한다<br>

  ![7-3-2-9]<br>
  ① 서비스를 사용할 도메인 정보를 입력해야 한다. 정확히 입력하지 않으면, API 서비스를 사용할 수 없다. 이때, Identifier 필드에 입력한 값과 개방형 클라우드 플랫폼에서 어플리케이션이 사용하는 도메인이 일치해야 한다. 어플리케이션의 도메인은 개방형 클라우드 플랫폼에 어플리케이션을 배포하는 시점에 결정한다.<br>
  참고: [7.3. 샘플 어플리케이션 배포]<br>
  ② 네이버 검색 API는 키 발급 과정에서도 휴대폰 인증을 실시해야 한다.<br>
  ③ 좌측의 보안문자를 입력한다. 보안문자 입력에서 대문자와 소문자는 구분하지 않는다.<br>
  ④ 약관의 내용을 숙지하고 동의하면, 체크한다.<br>
  ⑤ [키 발급] 버튼을 눌러 키 발급을 완료한다.<br>

  (2) 네이버 지도 API 키 발급<br>
  네이버 지도 API의 [키 추가] 버튼을 클릭한다<br>
  
  ![7-3-2-10]<br>
  ① 사용환경을 선택한다.<br>
  ② 사용환경에서 '웹'을 선택했기 때문에 URL 정보를 입력해야 한다. 정확히 입력하지 않으면, API 서비스를 사용할 수 없다. 이때, URL 필드에 입력한 값과 개방형 클라우드 플랫폼에서 어플리케이션이 사용하는 도메인이 일치해야 한다. 어플리케이션의 도메인은 개방형 클라우드 플랫폼에 어플리케이션을 배포하는 시점에 결정한다.<br>
  참고: [7.4.2. 어플리케이션 배포]<br>
  ③ 좌측의 보안문자를 입력한다. 보안문자 입력에서 대문자와 소문자는 구분하지 않는다.<br>
  ④ 약관의 내용을 숙지하고 동의하면, 체크한다.<br>
  ⑤ [키 발급] 버튼을 눌러 키 발급을 완료한다.<br>

  3. 서비스키 확인<br>
  ![7-3-2-11]<br>
  [네이버 개발자센터의 Open API 페이지](http://developer.naver.com/wiki/pages/OpenAPI)에 접속하고 로그인한 상태에서 우측 중간의 [키 발급/관리] 버튼을 클릭한다.

  ![7-3-2-12]<br>
  네이버에서 제공하는 API 서비스의 서비스키 전체를 관리할 수 있는 화면으로 이동한다. 각각의 API별로 ①발급된 사용자의 서비스 키와 등록 URL과 ②API 호출 수를 확인 할 수 있다. ③키 추가 및 ④수정/삭제 또한 이 화면에서 이루어 진다. [7.2.2.3. 네이버 검색] API 서비스는 상단의 검색 API의 발급 키로 사용이 가능하고 [7.2.2.1. 네이버 지도]와 [7.2.2.2. 네이버 주소-좌표 변환] API 서비스는 지도 API의 발급 키로 사용이 가능하다.

### <div id='54'></div> 7.4. 샘플 어플리케이션 배포
##### <div id='55'></div> 7.4.1. 개방형 클라우드 플랫폼 로그인
개방형 클라우드 플랫폼에 개발자 계정으로 로그인 한다. 개발자 계정은 운영자가 계정을 생성하고 개발자 권한을 부여함으로써 사용할 수 있게 된다. 개발자 계정 생성 및 권한 부여에 대한 설명은 기술하지 않는다. 본 문서에 기술된 검증절차는 관리자 계정으로 접속하여 검증하여도 무방하다.

```
  $ cf login
```

예시)
```
  $ cf login
  Email> kimdojun
  Password> asd20kwl
```

##### <div id='56'></div> 7.4.2. 어플리케이션 배포
개방형 클라우드 플랫폼에 어플리케이션을 배포(Deploy)한다. 어플리케이션 배포는 아래와 같은 명령어를 사용한다.

```
  $ cf push [어플리케이션명] -p [파일 경로] -b [빌드팩] -m [메모리] --no-start
```

※ [어플리케이션 명]은 개방형 클라우드 플랫폼에서 사용하는 어플리케이션의 이름으로 임의의 값을 지정할 수 있다. 배포 시 따로 옵션을 주지 않으면 어플리케이션 명을 바탕으로 어플리케이션의 도메인을 결정하게 된다. 네이버 Open API는 서비스키 발급 시, URL을 입력하도록 되어있다. 어플리케이션의 도메인이 서비스키를 발급 받을 때 입력한 URL과 다를 경우, API를 사용할 수 없다.<br>
※ '-p [파일 경로]'는 빌드 된 파일의 경로를 의미한다.<br>
※ '-b [빌드팩]'은 어플리케이션의 빌드팩을 선택할 수 있는 옵션이다. 입력하지 않을 경우 자동으로 찾게 된다. 선택 가능한 빌드팩 목록은 명령어 'cf buildpacks'로 확인 할 수 있다.<br>
※ '-m [메모리]'는 어플리케이션이 점유할 메모리의 크기를 지정하는 옵션이다. <br>
※ 어플리케이션 배포가 되면 자동으로 어플리케이션을 구동하게 되는데, '--no-start' 옵션은 어플리케이션이 자동으로 구동되지 않도록 한다.<br>

예시)
```
  $ cf push public-naver -p /home/kimdojun/sample/openpaas-service-java-broker-public-naver-api-sample.war -b java_buildpack -m 1024M --no-start
```

### <div id='57'></div> 7.5. 서비스 인스턴스 생성
마켓 플레이스에 등록된 서비스에 대해서 인스턴스 생성이 가능해진다.<br>
※ 서비스를 마켓 플레이스에 등록하는 것은 플랫폼 운영자의 역할이다. 마켓 플레이스에 서비스를 등록하는 방법은 본 문서의 [5.3. 서비스 접근 허용]을 참고한다.
##### <div id='58'></div> 7.5.1. 서비스 인스턴스 생성
```
  $ cf create-service [서비스명] [플랜명] [서비스 인스턴스명]
```
※ [서비스명]과 [플랜명]은 서비스 브로커 설정 파일에 정의된 서비스명과 플랜명을 의미한다. [서비스 인스턴스명]은 사용자가 임의로 입력하는 값이지만 제공되는 샘플 어플리케이션은 서비스 인스턴스명으로 서비스를 식별하도록 구현되어 있기 때문에, 임의의 값을 사용할 경우, 샘플 어플리케이션에서 서비스를 식별하지 못하므로 샘플 어플리케이션 소스의 수정이 필요하다.<br><br>

※ 샘플 어플리케이션은 개방형 클라우드 플랫폼에서의 서비스 인스턴스명을 아래의 표를 따르도록 강제한다. 서비스명의 첫 글자를 소문자로 바꾼 형태이다. 임의의 서비스 인스턴스명을 사용하기 위해서는 샘플 어플리케이션의 수정이 필요하다.<br>

| <b>API 서비스</b>      | <b>서비스명</b> | <b>서비스 인스턴스명</b> |
|-------------|-----------------------------|-----------------------------|
| 공연전시정보조회 서비스 | PublicPerformance | publicPerformance |
| 인천광역시 문화행사 | IncheonCulture | incheonCulture |
| 대전광역시 문화축제 | DaejeonFestival | daejeonFestival |
| 전시공연/테마파크 정보 | JeonnamPerformanceList | jeonnamPerformanceList |
| 네이버 지도 | NaverMap | naverMap |
| 네이버 주소-좌표 변환 | NaverAddressToGPS | naverAddressToGPS |
| 네이버 검색 | NaverSearch | naverSearch |

예시)
```
  $ cf create-service PublicPerformance basic publicPerformance
```

##### <div id='59'></div> 7.5.2. 서비스 인스턴스 확인
서비스 인스턴스의 생성을 통해 개방형 클라우드 플랫폼에서 서비스와 어플리케이션의 바인드가 가능해진다. 서비스 인스턴스의 생성을 확인하기 위해서, 서비스 인스턴스 목록을 노출하는 명령어를 사용한다.

```
  $ cf services
```

생성된 서비스의 인스턴스명과 서비스명, 플랜, 바인드 된 어플리케이션을 확인할 수 있다. bound apps의 값이 바인드 된 어플리케이션을 의미한다.<br>
![7-5-2-0]

### <div id='60'></div> 7.6. 서비스 바인드
[7.5. 서비스 인스턴스 생성]에서 생성된 서비스 인스턴스를 어플리케이션과 바인드하여 어플리케이션에서 API서비스를 사용할 수 있게 된다.
##### <div id='61'></div> 7.6.1. 서비스 바인드
생성된 서비스 인스턴스와 어플리케이션을 바인드한다. 바인드 시, -c 옵션을 주어 사용자가 직접 발급받은 해당 서비스의 서비스키를 입력할 수 있도록 한다. 샘플 서비스 브로커의 경우는 서비스 키를 입력하지 않을 경우 바인드가 정상적으로 진행되지 않도록 설계되어 있다.

```
  $ cf bind-service [어플리케이션명] [서비스 인스턴스명] -c '{"serviceKey":"[서비스키]"}'
```
※ [서비스 인스턴스명]은 서비스 인스턴스를 생성할 때, 입력한 명칭으로 서비스 명과 다르다. 서비스명과 서비스 인스턴스명의 차이는 [7.5.1. 서비스 인스턴스 생성]을 참고한다.

예시)
```
  $ cf bind-service public-naver publicPerformance -c '{"serviceKey":"adn2d241aaml%%3D"}'
```
위와 같은 형태로 어플리케이션이 사용할 7개의 API 서비스를 모두 바인드한다.

##### <div id='62'></div> 7.6.2. 서비스 바인드 확인
어플리케이션과 서비스를 바인드하면 바인드 된 각각의 서비스에 대한 정보는 어플리케이션의 VCAP_SERVICES 정보에서 확인할 수 있다.

```
  $ cf env [어플리케이션 명]
```

예시)
```
  $ cf env public-naver
```
![7-6-2-0]<br>
VCAP_SERVICES 정보를 확인하면 그림과 같이 각각의 서비스 별로 플랜, 엔드포인트, 바인드에서 -c 옵션을 통해 입력한 서비스키 등의 서비스 정보를 확인 할 수 있다. 샘플 어플리케이션은 이 VCAP_SERVICES 정보에서 엔드포인트와 서비스키를 가져와 필요한 API 서비스를 사용한다.

### <div id='62'></div> 7.7. 샘플 어플리케이션 동작 확인
명령어를 통해 어플리케이션의 URL을 확인할 수 있다.

```
  $ cf apps
```

![7-7-0-0]<br>
어플리케이션이 구동 중임을 확인하고 웹 브라우저에서 URL로 접속한다. 샘플 어플리케이션은 메인화면에서 'Hello World'를 출력하도록 구현되어있다.

![7-7-0-1]<br>
[URL]/main으로 이동하여 지도가 화면에 출력되는 것을 확인한다.

![7-7-0-2]<br>
※ 지도가 화면에 보이지 않는 경우는 네이버 지도 서비스키 신청 시 입력한 URL이 어플리케이션 URL과 동일하지 않아서 생기는 경우가 대부분이다. 샘플 어플리케이션을 예로 들면, 네이버 지도 API 서비스키 발급 시 URL 입력 창에 상단 화면에서 확인되는 URL인 http://public-naver.10.244.0.34.xip.io 를 입력하여야 한다. 자세한 내용은 [7.3.2 네이버 Open API 서비스 키 획득]의 [2. 서비스키 획득]에서 [(2) 네이버 지도 API 키 발급]과 [7.3. 샘플 어플리케이션 배포]을 참고한다.<br>

화면의 좌측 하단 셀렉트 박스에서 지역을 선택해보면서 각각의 API들이 정상적으로 작동하는 것을 확인한다.

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




### [Index](https://github.com/PaaS-TA/Guide-eng/blob/master/README.md) > [AP User Guide](../README.md) > Public API 개발
