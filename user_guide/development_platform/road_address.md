### [Index](https://github.com/PaaS-TA/Guide-eng/blob/master/README.md) > [AP User Guide](../README.md) > API Platform Road Address Development


## Table of Contents
1. [Outline](#1)
    * [1.1. Document Outline](#2)
    * [1.1.1 Purpose](#3)
    * [1.1.2 Range](#4)
    * [1.1.3 References](#5)
2. [System Configuration](#6)
     * [2.1. System Configuration Diagram](#7)
3. [Road Address Service](#8)
     * [3.1. Obtain Road Name Address](#9)
     * [3.2. Define an address data table](#10)
     * [3.3. Insert in Address Data DB](#11)
4. [Road Name Address Service](#12)
     * [4.1. Road Name Address Service Configuration](#13)
     * [4.2. Road Name Address Service API](#14)
     * [4.2.1. Road Name Address Search Service](#15)
     * [4.2.2. Road Name Address Manage Service](#16)
     * [4.3. Source Description](#17)
     * [4.3.1. Class Diagram](#18)
     * [4.3.2. Source List and Description](#19)
5. [Road Name Address Service Registration (API Platform)](#20)
     * [5.1. Road Name Address Search API Registration](#21)
     * [5.2. Road Name Address Manage API Registration](#22)
6. [Open Cloud Platform Settings](#23)
     * [6.1. API Platform Servicepack Registration](#24)
     * [6.2. API Platform Servicepack Update](#25)
7. [Road Name Address Search Sample Web App Description](#26)
     * [7.1. Sample Web App Structure](#27)
     * [7.2. Binding and Provisioning for Service in Open Cloud Platform](#28)
     * [7.2.1. Provisioning of Service](#29)
     * [7.2.2. Binding of Service and Sample App](#30)
     * [7.3. Source List and Description](#31)
     * [7.3.1. Spring Server Area](#32)
     * [7.3.2. Web Area (Shows only the significant files are - inside the main/resources)](#33)
8. [Annex A. Road Name Address Search API Definitions](#34)
     * [8.1. Road Name Address Search](#35)
9. [Annex B. Road Name Address Manage API Definitions](#36)
     * [9.1. Road Name Address Search](#37)
     * [9.2. Road Name Address Search (One)](#38)
     * [9.3. Register Road Name Address](#39)
     * [9.4. Modify Road Name Address](#40)
     * [9.5. Delete Road Name Address](#41)
     
     
     
# <a name="1"/>1. Outline

### <a name="2"/>1.1. Document Outline

##### <a name="3"/>1.1.1. Purpose
To provide "common components" as a service in the e-government framework for user convenience, we present ways to service some components and link them with API platforms.

##### <a name="4"/>1.1.2. Range
Build "Road Name Address Search" function among common components of e-Government framework into its own DB. Data is produced using the information provided by the road name address guide system ([***http://www.juso.go.kr***](http://www.juso.go.kr))and using the specifications of the road name address service Open API. The API platform is used to authenticate and manage the API created. 

##### <a name="5"/>1.1.3. References
-   [Guide] Utilization method of making address(full address).pdf’ – Download from Road Name Address Guide System ([***http://www.juso.go.kr***](http://www.juso.go.kr))
-   [Guide]Utilization Method of OpenAPI Link Application and .pdf’ – Download from Road Name Address Guide System([***http://www.juso.go.kr***](http://www.juso.go.kr))
-   WSO2 API Manager Document – Getting Started - Quick Start Guide([***https://docs.wso2.com/display/AM180/Quick+Start+Guide***](https://docs.wso2.com/display/AM180/Quick+Start+Guide))



# <a name="6"/>2. System Configuration

### <a name="7"/>2.1. System Configuration Diagram
Create a Road Name Address Service (API) and register it at the API platform. And general developers apply for and use the road name address service on the "open cloud platform".
>![api_platform_dorojuso_01]

Picture. System Configuration Diagram of Road Name Address Search Service


The API platform manages access to the road name address service API, statistics, and life cycle management. For a detailed description of the API platform, please refer to the separate API platform guide.

The Road Name Address Service (API) was developed by some REST/full type of server and the road name address information is managed by MySQL DB.



# <a name="8"/>3. Road Name Address Service

### <a name="9"/>3.1. Obtain Road Name Address
To build a road name address on its own DBMS, you must first obtain a road name address. This information can be downloaded from the "Road Name Address Information System" ([***http://www.juso.go.kr***](http://www.juso.go.kr)). 

Select "Provide Address" -> "Provide Road Name Address DB" on the main screen when you access the site as shown below.
>![api_platform_dorojuso_02]

Picture. Proceeds to a page that provides Addresses


On the Address Delivery page, you can download the PDF file describing how to use the address data and the most recent address data information. Get the latest date information for the "Download full address deployment guide" and full address (final draft). Download the information by clicking on the two areas marked in red on the screen below.
>![api_platform_dorojuso_03]

Picture. Download build guide and final address


The downloaded "[Guide]Utilization method of making address(full address).pdf" gives you a sense of the structure of the data. The specifications of the downloaded address file are defined on page 13 of the document. If you look in the center, there is a "building management number" and this information is the primary key.
>![api_platform_dorojuso_04]

Picture. Spec information of “[Guide]Utilization method of making address(full address).pdf”


The address file is a zipped file that is about 158 MB. (As of June 2015) If you unzip this file, you can check the address file with TXT information as below.. 
>![api_platform_dorojuso_05]

picture. Address fule list


When you unzip the file, it's about 1.85GB. This TXT file is a text file with the "|" delimiter and must import data into MySQL.

### <a name="10"/>3.2. Define an address data table
To store address data, you must define the structure of the table. This document is defined according to the grammar of MySQL because it stores and manages road name address information in MySQL.
As much as possible, the structure is similar to the Korean name defined in "[Guide]Utilization method of making address(full address).pdf"..

````
CREATE TABLE IF NOT EXISTS `egov_common`.`doro_juso` (
  `code` CHAR(10) NULL COMMENT '',
  `sido` VARCHAR(40) NULL COMMENT '',
  `sigungu` VARCHAR(40) NULL COMMENT '',
  `eupmyundong` VARCHAR(40) NULL COMMENT '',
  `ri` VARCHAR(40) NULL COMMENT '',
  `san` CHAR(1) NULL COMMENT '0:land, 1:mountain',
  `bunji` SMALLINT(4) NULL COMMENT '',
  `ho` SMALLINT(4) NULL COMMENT '',
  `doro_code` CHAR(12) NULL COMMENT 'zip code(5)+road name number(7)',
  `doro` VARCHAR(80) NULL COMMENT '',
  `jiha` CHAR(1) NULL COMMENT '0:land, 1:ground, 2:air',
  `bon` SMALLINT(5) NULL COMMENT '',
  `bu` SMALLINT(5) NULL COMMENT '',
  `gunmul` VARCHAR(40) NULL COMMENT '',
  `gunmul_sangse` VARCHAR(100) NULL COMMENT '',
  `gunmul_no` VARCHAR(25) NOT NULL COMMENT '',
  `eupmyundong_no` SMALLINT(2) NULL COMMENT '',
  `hang_code` CHAR(10) NULL COMMENT '',
  `hang` VARCHAR(20) NULL COMMENT '',
  `zipcode` CHAR(6) NULL COMMENT '',
  `zipno` CHAR(3) NULL COMMENT '',
  `dayaeng` VARCHAR(40) NULL COMMENT '',
  `idong` CHAR(2) NULL COMMENT '31 : Building Numbering, 34, Modification, 63 : Abolition of Building\n72 : Abolition of some buildings in the building group,\n73 : Create some buildings in the building group',
  `update_date` CHAR(8) NULL COMMENT '',
  `defore_doro` VARCHAR(25) NULL COMMENT '',
  `sigungu_gunmul` VARCHAR(200) NULL COMMENT '',
  `gongdong` CHAR(1) NULL COMMENT '0:Non-appartment, 1:Appartment',
  `gicho_no` CHAR(5) NULL COMMENT '',
  `juso_sang` CHAR(1) NULL COMMENT '0:Not awared, 1:Awarded',
  `bigo1` VARCHAR(15) NULL COMMENT '',
  `bigo2` VARCHAR(15) NULL COMMENT '',
  PRIMARY KEY (`gunmul_no`)  COMMENT '')
ENGINE = InnoDB;

CREATE INDEX `IDX_SIGUNGU` ON `egov_common`.`doro_juso` (`sigungu` ASC)  COMMENT '';
CREATE INDEX `IDX_DONG` ON `egov_common`.`doro_juso` (`eupmyundong` ASC)  COMMENT '';
CREATE INDEX `IDX_DORO` ON `egov_common`.`doro_juso` (`doro` ASC)  COMMENT '';
````



### <a name="11"/>3.3. Insert in Address Data DB
There is one thing that needs to be checked before importing that address information into the DBMS. First, checking of the language settings of the DBMS to build is needed. Currently, this file has Korean standard ANSI.  when configuring DBs, they are usually configured with UTF8, so in order to import Hangul accurately, you need to develop a program that reads files one by one and changes character encoding to insert them or use commercial tools.

This document explains how to import data to MySQL servers, convert existing TXT files to UTF8, and use the MySQL import function to put the converted files into DBMS. DBMS's DB is set to UTF8

To convert a file's encoding to UTF8, open it in Notepad and "save it under a different name" in a Windows environment.
>![api_platform_dorojuso_06]

picture. Save it as from the NotePad


Prepare thw file and use MySQL Import to import the data.
````
mysqlimport -u [User ID] -p [Data Base Name] --fields-terminated-by="|" --lines-terminated-by="\n" [File Name]
````

The [File Name] should be the same with the Table Name. In cases of importing multiple files, each file names should be renamed and run the corresponding commands.

mysqlimport command statement explaination:
-u [User ID] : User ID that can be Inserted into that database.
-p : Asks for the password when Mysql Import is being done.
[Data Base Name] : The name of the database where road name address are to be saved.
--field-terminated-by=“|” : Characters that distinguish each field/column are marked with |.
--lines-terminated-by=”\n” :Separate the ends of a line with "\n" (Windows uses \r\n for ending.)
[File Name] : It is the file location to Import the files and the file name is same as Table name.

When all the text files are imported, the preparation for the data of road name adress is completed.



# <a name="12"/>4. Road Name Address Service

### <a name="13"/>4.1. Road Name Address Service Configuration
One common component of the e-Government framework is the Road Name Address Service (OpenAPI). This component is built with its own DB and developed to support road name address search services in the same structure as Road Name Address Service (Open API).

The information on the components developed using the Spring Framework is as follows.

<table>
  <tr>
    <td>Module</td>
    <td>Version</td>
    <td>Description</td>
  </tr>
  <tr>
    <td>Java</td>
    <td>1.7</td>
    <td>Java Compiler/Execution Environment</td>
  </tr>
  <tr>
    <td>Spring Boot</td>
    <td>1.3</td>
    <td>A module that can run the Web App with Java only</td>
  </tr>
  <tr>
    <td>JSON Path</td>
    <td>2.0.0</td>
    <td>Manage by changing it to JSON data</td>
  </tr>
    <tr>
      <td>Spring JDBC</td>
      <td>4.0.0</td>
      <td>Spring Library for JDBC use</td>
    </tr>
      <tr>
        <td>MySQL connector</td>
        <td>5.1.27</td>
        <td>Driver Library for MySQL connection</td>
     </tr>
</table>



### <a name="14"/>4.2. Road Name Address Service API

#### <a name="15"/>4.2.1. Road Name Address Search Service
<table>
  <tr>
    <td>API</td>
    <td>Method</td>
    <td>Description</td>
  </tr>
  <tr>
    <td>addrLinkApi.do?currentPage={currentPage}&</td>
    <td>GET</td>
    <td>Perform searching by recieving the  CurrentPage, Number of data per page (countPerPage), and word to search (keyword) as Paramater.</td>
  </tr>
</table>
※  Refer to Annex A. for detailed definition of the API.

#### <a name="16"/>4.2.2. Road Name Address Manage Service
<table>
  <tr>
    <td>API</td>
    <td>Method</td>
    <td>Description</td>
  </tr>
  <tr>
    <td>/dorojuso/manager/{currentPage}/{countPerPage}/{keyword}</td>
    <td>GET</td>
    <td>Perform searching by recieving the  CurrentPage, Number of data per page (countPerPage), and word to search (keyword) as Paramater. (PATH Method)</td>
  </tr>
  <tr>
    <td>/dorojuso/manager/{building_code}</td>
    <td>GET</td>
    <td>Take the building management number (PK) as a path variable and retrieve an road name address.</td>
  </tr>    
  <tr>
    <td>/dorojuso/manager</td>
    <td>POST</td>
    <td>Input the road name address information in the body and register the road name address.</td>
  </tr>
  <tr>
    <td>/dorojuso/manager/{building_code}</td>
    <td>PUT</td>
    <td>Input the road name address information in the body and modify the road name address.</td>
  </tr>
  <tr>
    <td>/dorojuso/manager/{building_code}</td>
    <td>DELETE</td>
    <td>Deletes the Road name address data.</td>
  </tr>
</table>
※ Refer to Annex A. for detailed definition of the API.

### <a name="17"/>4.3. Source Description

#### <a name="18"/>4.3.1. Class Diagram
The configuration for the major classes is shown in the Class Diagram below. The important controller, service, and DAO were displayed in the process excluding the model, exception, and utility.
>![api_platform_dorojuso_07]

picture. Class Diagram of Road Name Address Service

The Base Controller defines various exceptions. Once the DoroJusoController has control over road name address retrieval, DoroJusoService is responsible for business logic.
The DoroJusomanagerController, with the DoroJusoConroller, inherits the BaseController, controls the registration, modification, and deletion services (API) to manage road name addresses, and is responsible for the business logic of the DoroJusoManager Service.

#### <a name="19"/>4.3.2. Source List and Description
The location of the source will be located on the Git Hub of the "Open Cloud Platform" and the location to be open to the public will be shared separately through the website.
(A private location for the developer is [**https://github.com/PaaS-TA/SERVICE-EGOV-COMMON-JUSO**](https://github.com/PaaS-TA/SERVICE-EGOV-COMMON-JUSO))

<table>
  <tr>
    <td>Package Name/Source Name</td>
    <td>Description</td>
  </tr>
  <tr>
    <td colspan=2>org.openpaas.egovframwork.comcomponent.dorojuso</td>
  </tr>
  <tr>
    <td>Application</td>
    <td>A class that starts the spring boot, including the main.</td>
  </tr>
  <tr>
    <td>MysqlConfig</td>
    <td>Config Class to set the Datasource for MySQL.</td>
  </tr>
  <tr>
    <td>SimpleCORSFilter</td>
    <td>Filter class for processing CORS.</td>
  </tr>
  <tr>
    <td colspan=2>org.openpaas.egovframwork.comcomponent.common</td>
  </tr>
  <tr>
    <td>StringUtils</td>
    <td>This is the class of utilities that are commonly used for the processing of strings.</td>
  </tr>
  <tr>
    <td colspan=2>org.openpaas.egovframwork.comcomponent.controller</td>
  </tr>
  <tr>
    <td>BaseContoller</td>
    <td>Error processing and exception processing were defined as the controllers' upper controllers.</td>
  </tr>
  <tr>
    <td>DoroJusoController</td>
    <td>This is the class that controls the road name address search service.</td>
  </tr>
  <tr>
    <td>DoroJusoManagerController</td>
    <td>This is the class that controls the road name address management service.</td>
  </tr>
  <tr>
    <td colspan=2>org.openpaas.egovframwork.comcomponent.dao</td>
  </tr>
  <tr>
    <td>DoroJusoDAO</td>
    <td>DAO interface for connection with road name address DB.</td>
  </tr>
  <tr>
    <td colspan=2>org.openpaas.egovframwork.comcomponent.dao.impl</td>
  </tr>
  <tr>
    <td>DoroJusoDAOImpl</td>
    <td>A class that implemented the DAO interface.</td>
  </tr>
  <tr>
    <td colspan=2>org.openpaas.egovframwork.comcomponent.exception</td>
  </tr>
  <tr>
    <td>DoroJusoBadRequestException</td>
    <td>An Exception to handle errors for invalid requests.</td>
  </tr>
  <tr>
    <td>DoroJusoDoesNotExistException</td>
    <td>Exception to handle errors in road name information that do not exist when modified or deleted.</td>
  </tr>
  <tr>
    <td>DoroJusoException</td>
    <td>Exception that handles general errors while processing road name addresses.</td>
  </tr>
  <tr>
    <td>DoroJusoExistsException</td>
    <td>Exception dealing with duplicate errors because the road name address already exists.</td>
  </tr>
  <tr>
    <td colspan=2>org.openpaas.egovframwork.comcomponent.model</td>
  </tr>
  <tr>
    <td>Common</td>
    <td>A model that has common parts (error codes, numbers, etc.) in road name address search results value.</td>
  </tr>
  <tr>
    <td>DoroJuso</td>
    <td>The model of the entire road name address search result. It contains the common part and the Juso list.</td>
  </tr>
  <tr>
    <td>DoroJusoInfo</td>
    <td>The model for importing information from the road name address DB.</td>
  </tr>
  <tr>
    <td>DoroJusoInfoResult</td>
    <td>Model used by road name address management results (retrieve).</td>
  </tr> 
  <tr>
    <td>Juso</td>
    <td>The model of the juso part of the road name address search result value.</td>
  </tr>
  <tr>
    <td>ResultMessage</td>
    <td>The result model of road name address management (registration, modification, deletion).</td>
  </tr>
  <tr>
    <td>colspan=2>org.openpaas.egovframwork.comcomponent.service</td>
  </tr>
  <tr>
    <td>DoroJusoManagerService</td>
    <td>The service responsible for the business logic of road name address management.</td>
  </tr>
  <tr>
    <td>DoroJusoService</td>소
    <td>The service responsible for the business logic of road name address search.</td>
  </tr>
</table>



# <a name="20"/>5. Road Name Address Service Registration (API Platform)
The road name address service created in Chapter 4 does not have a certification/API management (Life cycle) part. It is simply a structure that responds to requests. Many essential functions such as user authentication, Token generation/management, API life cycle management, and statistics are required for the actual service.
To this end, the API platform was selected to help service the common components of the existing e-government framework.
For detailed instructions on how to install and use the API platform, refer to "Analyze_API Manager Installation Manual".
This document briefly describes the method and know-how of registering the Road Name Address Service API on the API platform.

### <a name="21"/>5.1. Road Name Address Search API Registration
Register the ability to search for road name addresses with the service API that the actual user will use. First, access the Publisher screen of the API platform. (URL will be announced separately.)

Add API in API platform Publisher.
>![api_platform_dorojuso_08]

picture. Enter basic API information


Enter basic information for the service. Name will be the name that will be shown in Marketplace on the "open cloud platform." 
Context will be the first path to access the API platform. Select a name that does not exist on the existing API platform and proceed.
Description is not mandatory for API platforms, but is mandatory for open cloud platforms.

Register the real API. To make it like the specification provided by the Road Name Address Service ([***http://www.juso.go.kr***](http://www.juso.go.kr))he API was set to addrLinkApi.do and the parameter also used the same name. Input addrLinkApi.do in the URL Param, select GET, OPTIONS, and then click Add New Resource. 
This creates two APIs: GET and OPTIONS.
>![api_platform_dorojuso_09]

picture. API Registration


When you press the Add New Resource button, GET and OPTIONS appear at the bottom of the screen so that you can define each parameter. Click GET to expand the screen to enter the parameter in GET. For currentPage, countPerPage, enter Data type as integer, and for Parameter type, select query.For keyword, put Data type as String and Parameter type as query. All Required as true, and must be selected.
>![api_platform_dorojuso_10]

picture. Insert Parameter


The reason for registering OPTIONS is in preparation for times when OPTIONS are requested to check the CORS to see if the API is valid in https or other web communication, so OPTIONS are required when registering the API.

Once you have completed the registration for the API, proceed to the Implementation step. The Implementation step registers the endpoint of the service.
Endpoint type will be serviced by Http communication, so select "HTTP Endpoint" and insert the URL of the road name address service server into Production, Sandbox Endpoint. The development server/test server is placed in the sandbox endpoint and the actual operational services are placed in the production endpoint.
The endpoint entered here is the default, and the API and parameter are attached to it to make an actual request.
>![api_platform_dorojuso_11]

picture. Endpoint Registration


When Endpoint registration is completed, proceed to the last step which is acess setting.  
Set the communication method (http, https) and the access limit setting (Tier Availability). Please refer to the API platform manual to add more restrictions.  
Only basic settings are done in here. Set the Tier Availability to Unlimited.  
When connecting to an open cloud platform, only Unlimited is enabled for the services (service packs) that connect the two systems because they are supposed to monitor without any restrictions.
>![api_platform_dorojuso_12]

picture. Access Environment Settings


Now you have entered the information about the API, and when Publishing this API, it can checked at the API platform store. 
>![api_platform_dorojuso_13]

picture. API Platform 


As shown above, if the service created by the Store is visible and the result value is normal when the API is called from the API Console, the registration is successful.

So far, we have created a road name address service server and managed the service on the API platform. Now you're ready to register your services on an open cloud platform and make them available within the platform.

### <a name="22"/>5.2. Road Name Address Manage API Registration
The data management function of the road name address service enables retrieving, registering, modifying, and deleting. The corresponding management functions are also made into APIs, such as search services, and access management is done at the API platform. 
The method of registering on the API platform is similar exept for the part where the basic information of the API is being entered.
The diffrence will be discussed below.

Register road name address API. Registration, Modification, Deletion, and Retrieve must be registered. For more information, you can register it as the API definition in Annex B.
>![api_platform_dorojuso_14]

picture. Registering the road name address manage API


The road name address management is designed with a path method rather than a query method. The API platform automatically sets the parameter by entering the path variable name with {} such as dorojuso/manager/{building_code} in the URL pattern as shown in the screen below.
>![api_platform_dorojuso_15]

picture. Enter Parameter with Path Method


>![api_platform_dorojuso_16]

picture. A screen when variables are inserted to Path


It is important to note that OPTIONS must be registered for each API pattern. You need to register the API by checking the validity of the API with OPTIONS during Rest communication. You can enter the rest of the Endpoint and Access Preferences are the same as the Road Name Address Search Service. Once the entry is complete and the publication is complete, you can check whether the API is visible in the store and whether it is working properly in the API Console.

We have completed registration of the road name address management service on the API platform.



# <a name="23"/>6. Open Cloud Platform Settings
To use the API of the API platform on an open cloud platform, you must register a service broker. 
If you register the service pack of the API platform for at least once, you need to update the service pack.

The manual describes how to register and manage services in an open cloud platform, so this guide will briefly explain which commands to register, set permissions, and update.

### <a name="24"/>6.1. API API Platform Servicepack Registration
Log in to the server with cf cli. The user should have authority to reigster the Servicepack(Service Broker).

Enter the command as shown below for registering the servicepack.
````
$ cf create-service-broker {servicepack name} {servicepack userID} {servicepack user password} http://{servicepack URL}
````
- Servicepack Name : Name seen on open cloud platforms for service pack management. Service Marketplace shows each API service name, where the name is the name of the service pack list.
- Servicepack UserID / PW : User ID that can have access to the servicepack. Enter ID/PW with authorization since the servicepack is also an API server, only a user with authorization can have access.
- Servicepack URL : Enter the URL where the API provided by the service pack is available.

Servicepack registration has been completed and registered information can be checked as below.
````
$ cf service-brokers
Getting service brokers as admin...Cloud Controller
OK

Name            URL
{Servicepack Name}   http://{Servicepack URL}
````
The list cannot be checked from the Marketplace with the current status because the Access authority is not given yet. The access authority can be checked as shown below.
````
$ cf service-access

broker: apiplatform-sb
   service         plan        access   orgs
dorojusodb      Unlimited   none
   dorojusodbMgt   Unlimited   none
````

You can check that the access of the road name address search/management service is not set. Access must be enabled to be checked on the Marketplace, and service application (Provision) and connection (Bind) can be made.
````
$ cf enable-service-access dorojusodb
$ cf enable-service-access dorojusodbMgt

$ cf service-access

broker: apiplatform-sb
   service         plan        access   orgs
dorojusodb      Unlimited   all
   dorojusodbMgt   Unlimited   all
````
Enable the service and check the service list again and check if the access has been changed to all.

Road name address services are now ready for use on open cloud platforms.

### <a name="25"/>6.2. API Platform Servicepack Update
If the service pack has been registered before, update the service pack. If a new API is created or lost on the API platform, this update should be made to match the information with the open cloud platform.

The command is as shown below.
````
$ cf update-service-broker {Servicepack Name} {Servicepack UserID} {Servicepack Password} http://{Servicepack URL}
````
The newly created service requires a new set of access authorities. Authorities can be given through the enable-service-access command described above.



# <a name="26"/>7. Road Name Address Search Sample Web App Description
This Sample Web App is deployed to the Open Cloud Platform. The service of API platform can be used in a provisioned and bound condition.

### <a name="27"/>7.1. Sample Web App Structure
The Sample Web App is deployed to the Open Cloud Platform as an App. In here, Spring Boot is used to bring environment settings(service connection information) from the Open Cloud Platform and use those informations inside the Javascript and AngularJS.

<table>
  <tr>
    <td>Module</td>
    <td>Version</td>
    <td>Description</td>
  </tr>
  <tr>
    <td>Java</td>
    <td>1.7</td>
    <td>Java Comfiler/Execution Evironment</td>
  </tr>
  <tr>
    <td>Spring Boot</td>
    <td>1.3</td>
    <td>A module that can run the Web App with Java only</td>
  </tr>
  <tr>
    <td>Spring cloud</td>
    <td>1.0</td>
    <td>Library for retrieving information bound to services from an open cloud platform</td>
  </tr>
    <tr>
      <td>thymeleaf</td>
      <td>1.3</td>
      <td>Modules used to pass objects to HTML</td>
    </tr>
      <tr>
        <td>httpclient</td>
        <td>4.4.1</td>
        <td>To communicate HTTP with the API platform (used to import tokens)</td>
     </tr>
</table>
 
<table>
  <tr>
    <td>Module</td>
    <td>Version</td>
    <td>Descriptin</td>
  </tr>
  <tr>
    <td>AngularJS</td>
    <td>1.2.18</td>
    <td>Javascript Framework to make MVC available in HTML</td>
  </tr>
  <tr>
    <td>JQuery</td>
    <td>1.11</td>
    <td>A Library for Good Use of Javascript</td>
  </tr>
  <tr>
    <td>Bootstrap</td>
    <td>3.1</td>
    <td></td>
  </tr>
  <tr>
    <td>sb-admin</td>
    <td>1</td>
    <td>Provides various UI components and menu frames in bootstrap theme.</td>
  </tr>
</table>

### <a name="28"/>7.2. Binding and Provisioning for Service in Open Cloud Platform
In order to use the registered service described in chapter 6, it should be provisioned or bound. The App should be deployed for at least once to be connected.
For detailed description of the Open Cloud Platform's usage, refer to the service usage. In here, a brief explaination on connection of the road name address service will be done.

#### <a name="29"/>7.2.1 Provisioning of Service
First, check if there are services on the open cloud platform Marketplace.
````
$ cf marketplace

service         plans                                                description
dorojusodb      Unlimited                                            search the doro juso
dorojusodbMgt   Unlimited                                            dorojusodbmgt 

* These service plans have an associated cost. Creating a service instance will incur this cost.

TIP:  Use 'cf marketplace -s SERVICE' to view descriptions of individual plans of a given service.
````

Marketplace에서 원하는 서비스가 있는지 확인하면 서비스 신청(Provision)을 합니다. 
````
$ cf create-service {서비스명} {서비스플랜} {내서비스명}
````
- 서비스명 : dorojusodb, dorojusodbMgt로 Marketplace에서 보여지는 서비스 명칭입니다.
- 서비스플랜 : 서비스에 대한 정책으로 plans에 있는 정보 중 하나를 선택합니다. API 플랫폼은 Unlimited만 지원하므로 Unlimited를 선택하면 됩니다.
- 내서비스명 : 내 서비스에서 보여지는 명칭입니다. 이 명칭을 기준으로 환경설정정보를 가져오기 때문에 서버에 설정되어 있는 명칭으로 꼭 등록을 해야 합니다. (dorojusodb, dorojusomgt)
서비스 생성이 끝났으면 내가 만든 앱과 연결(Binding)을 해야합니다.

#### <a name="30"/>7.2.2. 서비스와 Sample App을 연결(Binding) 하기
먼저 나의 앱이 배포되어 있는 상태를 확인합니다. 
````
$ cf apps

name                                 requested state   instances   memory   disk   urls
service_egov_common_juso_sampleApp   started           1/1         512M     1G     service-egov-common-juso-sampleapp.cf-dev.open-paas.com
````
초기에 앱을 일단 기본 구성만으로 배포를 해놓고 서비스와 연결(Binding)을 해야만 서비스의 정보(접속 URL, 접속 ID, 비밀번호)를 가져올 수 있습니다. 개방형 클라우드 플랫폼의 앱 개발/배포에 대한 부분에서 자세히 설명되어 있습니다.

나의 앱의 이름을 확인한 후에 앱과 서비스를 연결(Binding)합니다.
````
$ cf bind-service {내앱 명칭} {내서비스명}
````
- 내앱 명칭 : 위의 예제로 보면 service_egov_common_juso_sampleApp이 됩니다. 나의 앱의 고유한 명칭을 이야기합니다.
- 내서비스명 : 서비스 신청(Provision)시에 부여한 명칭입니다.

연결이 정상적으로 이루어 졌으면 앱을 restage하라고 나옵니다. 현재 앱은 서비스와 연결이 안되어 있는 상태이고 restage를 해야지 연결(Binding)이 잘되었는지 확인이 가능합니다.
````
$ cf services

name            service          plan        bound apps                           last operation
dorojusodbmgt   dorojusodbMgt  Unlimited   service_egov_common_juso_sampleApp  create succeeded
dorojusodb      dorojusodb      Unlimited   service_egov_common_juso_sampleApp   create succeeded
````
위의 내용을 보면 service_egov_common_juso_sampleApp가 dorojusodbmgt와 dorojusodb완 연결되어 있는 것을 확인할 수 있습니다.

### <a name="31"/>7.3. 소스 리스트 및 설명
해당 소스의 위치는 “개방형 클라우드 플랫폼”의 Git Hub에 위치하며 일반에게 공개할 위치는 따로 홈페이지를 통해서 공유가 될 예정입니다.
(개발을 위한 Private 위치는 https://github.com/PaaS-TA/SERVICE-EGOV-COMMON-JUSO-SAMPLEAPP 입니다.)

#### <a name="32"/>7.3.1. Spring 서버 영역

<table>
  <tr>
    <td>Package명/소스명</td>
    <td>설명</td>
  </tr>
  <tr>
    <td colspan=2>org.openpaas.egovframwork.comcomponent.dorojuso.sampleApp</td>
  </tr>
  <tr>
    <td>Application</td>
    <td>Spring Boot를 시작하는 Main을 포함하는 Class입니다.</td>
  </tr>
  <tr>
    <td>ApplicationInstanceInfoCreator</td>
    <td>개방형 클라우드 플랫폼에 배포된 앱의 정보를 담는 Class입니다.</td>
  </tr>
  <tr>
    <td>CloudFoundryConnector</td>
    <td>개방형 클라우드 플랫폼에서 환경설정 정보를 가져오기 위한 Class입니다.</td>
  </tr>
  <tr>
    <td>CloudFoundryServiceInfoCreator</td>
    <td>개방형 클라우드 플랫폼에서 서비스와 연결(Binding)된 정보를 담는 Class입니다.</td>
  </tr>
  <tr>
    <td>Tags</td>
    <td>서비스팩에서 설정한 Tag 정보를 담는 Class입니다.</td>
  </tr>
  <tr>
    <td colspan=2>org.openpaas.egovframwork.comcomponent.sampleApp.controller</td>
  </tr>
  <tr>
    <td>SampleController</td>
    <td>/ 로 오는 요청을 처리하고 API플랫폼과 token 요청을 처리를 Control하고 있습니다.</td>
  </tr>
  <tr>
    <td colspan=2>org.openpaas.egovframwork.comcomponent.sampleApp.model</td>
  </tr>
  <tr>
    <td>ResultMessage</td>
    <td>요청에 대한 처리 결과(등록, 수정, 삭제) 정보 Class입니다.</td>
  </tr>
  <tr>
    <td>TokenRequestVO</td>
    <td>Token을 요청하기 위한 사용자 정보를 가진 Class입니다.</td>
  </tr>
  <tr>
    <td>TokenVO</td>
    <td>API 플랫폼에서 획득한 Token을 보내기 위한 Class입니다.
</td>
  </tr>
  <tr>
    <td colspan=2>org.openpaas.egovframwork.comcomponent.sampleApp.service</td>
  </tr>
  <tr>
    <td>SampleService</td>
    <td>API 플랫폼에서 SSL을 사용하지 않고 Token을 가져오기 위한 비즈니스 로직을 수행합니다.</td>
  </tr>
</table>

#### <a name="33"/>7.3.2. Web 영역 (중요한 파일만 표시 -  main/resources 안에 있음)
Web 부분의 소스 설명은 개발한 부분만 정리를 하였으며 main/resoureces 안에 있는 파일들 입니다.

<table>
  <tr>
    <td>디렉토리/파일명</td>
    <td>설명</td>
  </tr>
  <tr>
    <td colspan=2>Template</td>
  </tr>
  <tr>
    <td>app.html</td>
    <td>SampleController에서 호출하는 부분은 개방형 클라우드 플랫폼에서 설정된 환경설정(서버 정보, 서비스 연결 정보)를 보냅니다.</td>
  </tr>
  <tr>
    <td colspan=2>public/js</td>
  </tr>
  <tr>
    <td>app.js</td>
    <td>AngularJS가 시작되는 부분은 Route정보를 가지고 있습니다.</td>
  </tr>
  <tr>
    <td>controller.js</td>
    <td>기본 콘트롤러를 선언합니다. 자세한 콘트롤러는 개별적으로 선언합니다.</td>
  </tr>
  <tr>
    <td colspan=2>public/js/controller</td>
  </tr>
  <tr>
    <td>dorojJusoController.js</td>
    <td>도로명 주소 Open API를 이용한 화면의 Controller입니다. (본 가이드에서는 다루지 않음)</td>
  </tr>
  <tr>
    <td>dorojJusoDBController.js</td>
    <td>도로명 주소 검색 서비스를 이용한 화면의 Controller입니다.</td>
  </tr>
  <tr>
    <td>dorojJusoDBMgtController.js</td>
    <td>도로명 주소 관리 서비스를 이용한 화면의 Controller입니다.</td>
  </tr>
  <tr>
    <td colspan=2>public/partials</td>
  </tr>
  <tr>
    <td>doroJusoDBList.html</td>
    <td>도로명 주소 검색 서비스 리스트 화면 HTML 입니다.</td>
  </tr>
  <tr>
    <td>doroJusoDBMgtForm.html</td>
    <td>도로명 주소 관리 등록/수정시 사용하는 화면 HTML 입니다.</td>
  </tr>
  <tr>
    <td>doroJusoDBMgtList.html</td>
    <td>도로명 주소 관리 리스트 화면 HTML 입니다.</td>
  </tr>
  <tr>
    <td>doroJusoList.html</td>
    <td>도로명 주소 Open API 사용 리스트 화면 HTML 입니다.</td>
  </tr>
  <tr>
    <td>main.html</td>
    <td>처음 표시되는 화면입니다. (내용은 없음)</td>
  </tr>
  <tr>
    <td>waitForm.html</td>
    <td>조회시 사용하는 배경에 로딩 이미지를 표시하기 위한 HTML 입니다.</td>
  </tr>    
</table>



# <a name="34"/>8. 별첨 A. 도로명 주소 검색 API 정의서

## <a name="35"/>8.1. 도로명 주소 검색
사용자가 도로명 주소로 검색할 때 사용하는 검색 서비스입니다. 스펙은 도로명 주소 API (http://www.juso.go.kr) 의 스펙을 그대로 사용하였습니다.

### 8.1.1. Route
````
GET/ http://<server & base path>/addrLinkApi.do
````

### 8.1.2. Request

#### 8.1.2.1. Parameters
<table>
  <tr>
    <td>이름</td>
    <td>설명</td>
  </tr>
  <tr>
    <td>currentPage</td>
    <td>현재 페이지 수</td>
  </tr>
  <tr> 
    <td>countPerPage</td>
    <td>페이지당 데이터 개수</td>
  </tr>
  <tr>
    <td>keyword</td>
    <td>검색할 단어 keyword</td>
  </tr>
</table>

#### 8.1.2.2. Body
N/A

#### 8.1.2.3. Headers
````
Accept : application/json
````

### 8.1.3. Response

#### 8.1.3.1. Status
````
200 OK
````

#### 8.1.3.2. Body
````
{
    "common": {
    "totalCount": 10,
    "currentPage": 10,
    "countPerPage": 10,
    "errorCode": "0",
    "errorMessage": "정상"
    },
    "juso": [
        {
        "roadAddr": "경기도 양평군 강남로 912 (강상면)",
        "roadAddrPart1": "경기도 양평군 강남로 912",
        "roadAddrPart2": "(강상면)",
        "jibunAddr": "경기도 양평군 강상면 66-0",
        "engAddr": "",
        "zipNo": "476-913",
        "admCd": "4183031021",
        "rnMgtSn": "418303217033",
        "bdMgtSn": "4183031021100660000007779"
    },
    {
        "roadAddr": "경기도 양평군 강남로 952 (강상면)",
        "roadAddrPart1": "경기도 양평군 강남로 952",
        "roadAddrPart2": "(강상면)",
        "jibunAddr": "경기도 양평군 강상면 71-1",
        "engAddr": "",
        "zipNo": "476-913",
        "admCd": "4183031021",
        "rnMgtSn": "418303217033",
        "bdMgtSn": "4183031021100710001010017"
    },
    …
    ]
} 
````



# <a name="36"/>9. 별첨 B. 도로명 주소 관리 API 정의서

## <a name="37"/>9.1. 도로명 주소 검색
관리자가 도로명 주소로 검색할 때 사용하는 검색 서비스입니다. DB 구조와 동일하게 정보를 보내줍니다.

### 9.1.1. Route
````
GET/ http://<server & base path>/dorojuso/manager
````

### 9.1.2. Request

#### 9.1.2.1. Parameters (PATH 방식)
<table>
  <tr>
    <td>이름</td>
    <td>설명</td>
  </tr>
  <tr>
    <td>currentPage</td>
    <td>현재 페이지 수</td>
  </tr>
  <tr>
    <td>countPerPage</td>
    <td>페이지당 데이터 개수</td>
  </tr>
  <tr>
    <td>keyword</td>
    <td>검색할 단어 keyword</td>
  </tr>
</table>

#### 9.1.2.2. Body
N/A

#### 9.1.2.3. Headers
````
Accept : application/json
````

### 9.1.3. Responser

#### 9.1.3.1. Status
````
200 OK
````

#### 9.1.3.2. Body
````
{
    "juso": [
        {
            "code": "4183031021",
            "sido": "경기도",
            "sigungu": "양평군",
            "eupmyundong": "강상면",
            "ri": "병산리",
            "san": "0",
            "bunji": 66,
            "ho": 0,
            "doro_code": "418303217033",
            "doro": "강남로",
            "jiha": "0",
            "bon": 912,
            "bu": 1,
            "gunmul": "",
            "gunmul_sangse": "",
            "gunmul_no": "4183031021100660000007779",
            "eupmyundong_no": 1,
            "hang_code": "4183031000",
            "hang": "강상면",
            "zipcode": "476913",
            "zipno": "011",
            "dayaeng": "",
            "idong": "",
            "update_date": "",
            "defore_doro": "",
            "sigungu_gunmul": "",
            "gongdong": "0",
            "gicho_no": "12571",
            "juso_sang": "0",
            "bigo1": "",
            "bigo2": ""
        },
        …
    ]
}
````

## <a name="38"/>9.2. 도로명 주소 검색 (한 개)
관리자가 도로명 주소로 검색할 때 사용하는 검색 서비스입니다. DB 구조와 동일하게 정보를 보내줍니다.

### 9.2.1. Route
````
GET/ http://<server & base path>/dorojuso/manager
````

### 9.2.2. Request

#### 9.2.2.1. Parameters (PATH 방식)
<table>
  <tr>
    <td>이름</td>
    <td>설명</td>
  </tr>
  <tr>
    <td>building_code</td>
    <td>건물 관리 번호</td>
  </tr>
</table>

#### 9.2.2.2. Body
N/A

#### 9.2.2.3. Headers
````
Accept : application/json
````

### 9.2.3. Response

#### 9.2.3.1. Status
````
200 OK
````

#### 9.2.3.2. Body
````
{
    "code": "4183031021",
    "sido": "경기도",
    "sigungu": "양평군",
    "eupmyundong": "강상면",
    "ri": "병산리",
    "san": "0",
    "bunji": 66,
    "ho": 0,
    "doro_code": "418303217033",
    "doro": "강남로",
    "jiha": "0",
    "bon": 912,
    "bu": 1,
    "gunmul": "",
    "gunmul_sangse": "",
    "gunmul_no": "4183031021100660000007779",
    "eupmyundong_no": 1,
    "hang_code": "4183031000",
    "hang": "강상면",
    "zipcode": "476913",
    "zipno": "011",
    "dayaeng": "",
    "idong": "",
    "update_date": "",
    "defore_doro": "",
    "sigungu_gunmul": "",
    "gongdong": "0",
    "gicho_no": "12571",
    "juso_sang": "0",
    "bigo1": "",
    "bigo2": ""
}
````
## <a name="39"/>9.3. 도로명 주소 등록
관리자가 도로명 주소를 새로 등록할 때 사용합니다. 건물관리 번호가 Key로 중복시 오류가 발생합니다.

### 9.3.1. Route
````
POST/ http://<server & base path>/dorojuso/manager
````

### 9.3.2. Request

#### 9.3.2.1. Parameters (PATH 방식)
N/A

#### 9.3.2.2. Body
````
{
    "code": "4183031021",
    "sido": "경기도",
    "sigungu": "양평군",
    "eupmyundong": "강상면",
    "ri": "병산리",
    "san": "0",
    "bunji": 66,
    "ho": 0,
    "doro_code": "418303217033",
    "doro": "강남로",
    "jiha": "0",
    "bon": 912,
    "bu": 1,
    "gunmul": "",
    "gunmul_sangse": "",
    "gunmul_no": "4183031021100660000007779",
    "eupmyundong_no": 1,
    "hang_code": "4183031000",
    "hang": "강상면",
    "zipcode": "476913",
    "zipno": "011",
    "dayaeng": "",
    "idong": "",
    "update_date": "",
    "defore_doro": "",
    "sigungu_gunmul": "",
    "gongdong": "0",
    "gicho_no": "12571",
    "juso_sang": "0",
    "bigo1": "",
    "bigo2": ""
}
````

#### 9.3.2.3. Headers
````
Accept : application/json
````

### 9.3.3. Response

#### 9.3.3.1. Status
````
200 OK
````

#### 9.3.3.2. Body
````
{
    "message": "DoroJuso saved. gunmul_no:4183031021100660000007779"
}
````

## <a name="40"/>9.4. 도로명 주소 수정
관리자가 도로명 주소를 새로 등록할 때 사용합니다. 건물관리 번호가 Key로 중복시 오류가 발생합니다.

### 9.4.1. Route
````
PUT/ http://<server & base path>/dorojuso/manager
````

### 9.4.2. Request

#### 9.4.2.1. Parameters (PATH 방식)
<table>
  <tr>
    <td>이름</td>
    <td>설명</td>
  </tr>
  <tr>
    <td>building_code</td>
    <td>건물 관리 번호</td>
  </tr>
</table>

#### 9.4.2.2. Body
````
{
    "code": "4183031021",
    "sido": "경기도",
    "sigungu": "양평군",
    "eupmyundong": "강상면",
    "ri": "병산리",
    "san": "0",
    "bunji": 66,
    "ho": 0,
    "doro_code": "418303217033",
    "doro": "강남로",
    "jiha": "0",
    "bon": 912,
    "bu": 1,
    "gunmul": "",
    "gunmul_sangse": "",
    "gunmul_no": "4183031021100660000007779",
    "eupmyundong_no": 1,
    "hang_code": "4183031000",
    "hang": "강상면",
    "zipcode": "476913",
    "zipno": "011",
    "dayaeng": "",
    "idong": "",
    "update_date": "",
    "defore_doro": "",
    "sigungu_gunmul": "",
    "gongdong": "0",
    "gicho_no": "12571",
    "juso_sang": "0",
    "bigo1": "",
    "bigo2": ""
}
````

#### 9.4.2.3. Headers
````
Accept : application/json
````

### 9.4.3. Response

#### 9.4.3.1. Status
````
200 OK
````

#### 9.4.3.2. Body
````
{
    "message": "DoroJuso updated. gunmul_no:4183031021100660000007779"
}
````

## <a name="41"/>9.5. 도로명 주소 삭제
관리자가 도로명 주소를 삭제할 때 사용합니다.

### 9.5.1. Route
````
DELETE/ http://<server & base path>/dorojuso/manager
````

### 9.5.2. Request

#### 9.5.2.1. Parameters (PATH 방식)
<table>
  <tr>
    <td>이름</td>
    <td>설명</td>
  </tr>
  <tr>
    <td>building_code</td>
    <td>건물 관리 번호</td>
  </tr>
</table>

#### 9.5.2.2. Body
N/A

#### 9.5.2.3. Headers
````
Accept : application/json
````

### 9.5.3. Response

#### 9.5.3.1. Status
````
200 OK
````

#### 9.5.3.2. Body
````
{
    "message": "DoroJuso deleted. gunmul_no:4183031021100660000007779"
}
````

[api_platform_dorojuso_01]:./images/openpaas-application-apiplatform-dorojuso/api_platform_dorojuso_01.png
[api_platform_dorojuso_02]:./images/openpaas-application-apiplatform-dorojuso/api_platform_dorojuso_02.png
[api_platform_dorojuso_03]:./images/openpaas-application-apiplatform-dorojuso/api_platform_dorojuso_03.png
[api_platform_dorojuso_04]:./images/openpaas-application-apiplatform-dorojuso/api_platform_dorojuso_04.png
[api_platform_dorojuso_05]:./images/openpaas-application-apiplatform-dorojuso/api_platform_dorojuso_05.png
[api_platform_dorojuso_06]:./images/openpaas-application-apiplatform-dorojuso/api_platform_dorojuso_06.png
[api_platform_dorojuso_07]:./images/openpaas-application-apiplatform-dorojuso/api_platform_dorojuso_07.png
[api_platform_dorojuso_08]:./images/openpaas-application-apiplatform-dorojuso/api_platform_dorojuso_08.png
[api_platform_dorojuso_09]:./images/openpaas-application-apiplatform-dorojuso/api_platform_dorojuso_09.png
[api_platform_dorojuso_10]:./images/openpaas-application-apiplatform-dorojuso/api_platform_dorojuso_10.png
[api_platform_dorojuso_11]:./images/openpaas-application-apiplatform-dorojuso/api_platform_dorojuso_11.png
[api_platform_dorojuso_12]:./images/openpaas-application-apiplatform-dorojuso/api_platform_dorojuso_12.png
[api_platform_dorojuso_13]:./images/openpaas-application-apiplatform-dorojuso/api_platform_dorojuso_13.png
[api_platform_dorojuso_14]:./images/openpaas-application-apiplatform-dorojuso/api_platform_dorojuso_14.png
[api_platform_dorojuso_15]:./images/openpaas-application-apiplatform-dorojuso/api_platform_dorojuso_15.png
[api_platform_dorojuso_16]:./images/openpaas-application-apiplatform-dorojuso/api_platform_dorojuso_16.png


### [Index](https://github.com/PaaS-TA/Guide-eng/blob/master/README.md) > [AP User Guide](../README.md) > API Platform 도로주소 개발
