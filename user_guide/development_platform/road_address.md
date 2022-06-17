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

API에 대한 등록을 완료하면 Implement 단계로 넘어갑니다. Implement단계에서는 서비스의 Endpoint를 등록합니다. Endpoint type은 Http 통신으로 서비스를 할 것이니 “HTTP Endpoint”를 선택을 하고 도로명 주소 서비스 서버의 URL을 Production, Sandbox Endpoint에 넣습니다. 개발 서버/테스트 서버는 Sandbox endpoint에 넣어주고 실제 운영 서비스는 Production endpoint에 넣어줍니다. 
여기서 입력한 Endpoint가 기본이 되고 뒤에 API, Parameter를 붙여서 실제 요청을 하게 됩니다.
>![api_platform_dorojuso_11]

그림. Endpoint 등록


Endpoint 등록이 완료되면 마지막 단계인 API의 접근설정을 합니다. 
통신방식(http, https)와 접근 제한 설정(Tier Availability)를 설정합니다. 제약사항을 더 추가하기 위해서는 API 플랫폼의 매뉴얼을 참고하여주세요. 여기서는 기본 설정만 하는 것으로 합니다. 
접근 제한 설정(Tier Availability)는 Unlimited로 설정을 합니다. 개방형 클라우드 플랫폼과의 연결시, 제한 설정을 하지 않고 모니터링만 하기로 되어 있기 때문에 두 시스템을 연결하는 서비스(서비스팩)에 Unlimited만 사용할 수 있게 설정되어 있습니다.
>![api_platform_dorojuso_12]

그림. 접속 환경 설정


이제 API에 대한 정보는 입력을 마쳤으며 이 API를 Publishing 을 하면 API플랫폼 Store에 보여지는 것을 확인하면 됩니다. 
>![api_platform_dorojuso_13]

그림. API플랫폼 


위와 같이 Store에서 만든 서비스가 보이고 API Console에서 API를 호출했을 때 결과 값이 정상으로 오면 등록이 성공적으로 된 것입니다.

지금까지 도로명 주소 서비스 서버를 만들었고 해당 서비스를 API 플랫폼에서 관리하게 하였습니다. 이제는 개방형 클라우드 플랫폼에 서비스를 등록하여 플랫폼 내에서 서비스를 이용할 수 있게 하면 준비는 완료됩니다.

### <a name="22"/>5.2. 도로명 주소 관리 API 등록
도로명 주소 서비스의 데이터를 관리하는 기능으로 조회, 등록, 수정, 삭제를 할 수 있게 합니다. 해당 관리 기능도 검색 서비스와 같이 API로 만들고 접근 관리는 API 플랫폼에서 합니다. 
API플랫폼에 등록하는 방법은 거의 유사하고 맨 처음 API의 기본 정보를 입력하는 부분만 다르니 이 부분만 설명하겠습니다.

도로명 주소 API 등록합니다. 등록, 수정, 삭제, 조회에 대해서 다 등록을 해야 합니다. 자세한 사항은 별첨B의 API 정의서로 등록을 하면 됩니다.
>![api_platform_dorojuso_14]

그림. 도로명 주소 관리 API 등록


도로명 주소 관리는 Parameter를 query 방식이 아닌 path 방식으로 설계하였습니다. API 플랫폼에서는 아래의 화면과 같이 URL Pattern에 dorojuso/manager/{building_code}와 같이 { }로 path 변수 명을 입력하면 자동으로 parameter를 설정해 줍니다.
>![api_platform_dorojuso_15]

그림. Path방식의 Parameter 입력


>![api_platform_dorojuso_16]

그림. Path에 변수를 넣었을 경우의 화면


여기서도 주의할 것은 각 API Pattern별로 OPTIONS을 같이 등록해야 합니다. Rest통신시 OPTIONS로 해당 API의 유효성을 체크해주기도 해서 등록을 해줘야 합니다. 나머지 Endpoint, 접속 환경 설정은 도로명 주소 검색 서비스와 동일하기 입력하시면 됩니다. 입력이 완료되고 Publish까지 완료되면 Store에서 API가 잘 보이는 지와 API Console에서 정상동작을 하는지 확인하시면 됩니다.

이렇게 도로명 주소 관리 서비스까지 API 플랫폼에 등록을 완료하였습니다.



# <a name="23"/>6. 개방형 클라우드 플랫폼 설정
개방형 클라우드 플랫폼에서 API 플랫폼의 API를 사용하기 위해서는 서비스팩(Service Broker)을 등록해야 합니다. 만약 한번이라도 API 플랫폼의 서비스팩을 등록을 했을 경우에는 서비스팩 Update를 해줘야 합니다.

개방형 클라우드 플랫폼에서 서비스를 등록하고 관리하는 방법은 따로 매뉴얼에서 설명이 되어 있으니 본 가이드에서는 간략하게 어떤 명령으로 등록하고 권한 설정, Update하는 명령 위주로만 설명하겠습니다.

### <a name="24"/>6.1. API 플랫폼 서비스팩 등록
cf cli 로 서버에 로그인을 합니다. 서비스팩(Service Broker)를 등록할 수 있는 권한을 가진 사용자 이여야합니다.

서비스 팩 등록을 위해서는 아래와 같이 명령을 입력합니다.
````
$ cf create-service-broker {서비스팩 이름} {서비스팩 사용자ID} {서비스팩 사용자비밀번호} http://{서비스팩 URL}
````
- 서비스팩 이름 : 서비스 팩 관리를 위해 개방형 클라우드 플랫폼에서 보여지는 명칭입니다. 서비스 Marketplace에서는 각각의 API 서비스 명이 보여지니 여기서 명칭은 서비스팩 리스트의 명칭입니다.
- 서비스팩 사용자ID / 비밀번호 : 서비스팩에 접근할 수 있는 사용자 ID입니다. 서비스팩도 하나의 API 서버이기 때문에 아무나 접근을 허용할 수 없어 접근이 가능한 ID/비밀번호를 입력합니다.
- 서비스팩 URL : 서비스팩이 제공하는 API를 사용할 수 있는 URL을 입력합니다.

서비스팩 등록이 완료되었으며 아래와 같이 등록된 정보를 확인할 수 있습니다.
````
$ cf service-brokers
Getting service brokers as admin...Cloud Controller
OK

Name            URL
{서비스팩 이름}   http://{서비스팩 URL}
````
하지만 현재 상태에서는 Marketplace에서 리스트를 확인할 수 없습니다. 이유는 Access할 수 있는 권한이 설정되지 않아서 입니다. 아래와 같이 서비스의 access 권한을 확인할 수 있습니다.
````
$ cf service-access

broker: apiplatform-sb
   service         plan        access   orgs
dorojusodb      Unlimited   none
   dorojusodbMgt   Unlimited   none
````

도로명 주소 검색/관리 서비스의 access가 설정이 안된 것을 확인 할 수 있습니다. 이 access를 enable 시켜야 Marketplace에서 확인이 가능하고 서비스 신청(Provision), 연결(Bind)를 할 수 있습니다.
````
$ cf enable-service-access dorojusodb
$ cf enable-service-access dorojusodbMgt

$ cf service-access

broker: apiplatform-sb
   service         plan        access   orgs
dorojusodb      Unlimited   all
   dorojusodbMgt   Unlimited   all
````
서비스를 Enable해주고 다시 서비스 접근 리스트를 조회하면 access가 all로 되어 있는 것을 확인할 수 있습니다.

이제 개방형 클라우드 플랫폼에서 도로명 주소 서비스를 사용할 준비가 되었습니다.

### <a name="25"/>6.2. API 플랫폼 서비스팩 Update
만약 기존에 서비스팩이 등록되었었으면 서비스팩 Update를 해야합니다. API플랫폼에 새로운 API를 만들었거나 없어졌을 경우에는 이 Update를 해줘서 개방형 클라우드 플랫폼과 정보를 맞춰야 합니다.

명령문은 아래와 같습니다.
````
$ cf update-service-broker {서비스팩 이름} {서비스팩 사용자ID} {서비스팩 비밀번호} http://{서비스팩 URL}
````
새로 생성된 서비스는 접근 권한을 새로 설정해야 하며 전 절에서 설명한 enable-service-access 를 이용하여 접근 권한을 줍니다.



# <a name="26"/>7. 도로명 주소 검색  Sample Web App 설명
본 Sample Web App은 개방형 클라우드 플랫폼에 배포되며 API 플랫폼의 서비스를 Provision과 Bind를 한 상태에서 사용이 가능합니다.

### <a name="27"/>7.1. Sample Web App 구조
Sample Web App은 개방형 클라우드 플랫폼에 App으로 배포가 됩니다. 여기서는 간단하게 Spring Boot를 이용하여 개방형 클라우드 플랫폼에서 환경정보(서비스 연결 정보)를 가져오고 Javascript, AngularJS내에서 그 정보를 활용할 수 있게 되어 있습니다.

<table>
  <tr>
    <td>모듈</td>
    <td>버전</td>
    <td>설명</td>
  </tr>
  <tr>
    <td>Java</td>
    <td>1.7</td>
    <td>Java 컴파일러/샐행환경</td>
  </tr>
  <tr>
    <td>Spring Boot</td>
    <td>1.3</td>
    <td>Java 만으로 Web App을 구동 시킬 수 있는 모듈</td>
  </tr>
  <tr>
    <td>Spring cloud</td>
    <td>1.0</td>
    <td>개방형 클라우드 플랫폼에서 서비스와 바인딩한 정보를 가져오기 위한 라이브러리</td>
  </tr>
    <tr>
      <td>thymeleaf</td>
      <td>1.3</td>
      <td>HTML에 객체를 전달하기 위해 사용한 모듈</td>
    </tr>
      <tr>
        <td>httpclient</td>
        <td>4.4.1</td>
        <td>API 플랫폼과 HTTP 통신을 하기 위함. (token을 가져올때 사용함)</td>
     </tr>
</table>
 
<table>
  <tr>
    <td>모듈</td>
    <td>버전</td>
    <td>설명</td>
  </tr>
  <tr>
    <td>AngularJS</td>
    <td>1.2.18</td>
    <td>HTML에서 MVC를 이용할 수 있게하는 Javascript Framework</td>
  </tr>
  <tr>
    <td>JQuery</td>
    <td>1.11</td>
    <td>Javascript를 잘 사용하기 위한 Library</td>
  </tr>
  <tr>
    <td>Bootstrap</td>
    <td>3.1</td>
    <td></td>
  </tr>
  <tr>
    <td>sb-admin</td>
    <td>1</td>
    <td>Bootstrap 테마로 각종 UI 컴포넌트와 메뉴틀을 제공함.</td>
  </tr>
</table>

### <a name="28"/>7.2. 개방형 클라우드 플랫폼에서 서비스 신청 및 바인딩 하기
6장에서 등록한 서비스를 앱에서 사용하기 위해서는 서비스 신청(Provision)과 연결(Binding)을 해야합니다. 연결을 위해서는 App이 한번은 배포가 되어 있어야 합니다.
개방형 클라우드 플랫폼의 자세한 사용법은 서비스 이용을 참조하여 주시기 바랍니다. 여기서는 간단하게 도로명 주소 서비스 연결하는 부분만 설명하겠습니다.

#### <a name="29"/>7.2.1 서비스 신청(Provision)하기
먼저 개방형 클라우드 플랫폼 Marketplace에서 서비스가 있는지 확인을 합니다.
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
