### [Index](https://github.com/PaaS-TA/Guide-eng/blob/master/README.md) > [AP Install](../README.md) > MySQL Service

## Table of Contents  

1. [Document Outline](#1)  
  1.1. [Purpose](#1.1)  
  1.2. [Range](#1.2)  
  1.3. [References](#1.3)  
  
2. [MySQL Service Installaion](#2)  
  2.1. [Prerequisite](#2.1)   
  2.2. [Stemcell Check](#2.2)    
  2.3. [Deployment Download](#2.3)   
  2.4. [Deployment File Modification](#2.4)  
  2.5. [Service Installaion](#2.5)    
  2.6. [Service Installation Check](#2.6)   
  
3. [MySQL Linkage Sample Web App Description](#3)  
  3.1. [Service Broker Registration](#3.1)  
  3.2. [Sample Web App Download](#3.2)  
  3.3. [Apply for services from PaaS-TA](#3.3)  
  3.4. [Deploy Sample Web App and Verify MySQL Binds](#3.4)  

4. [Access MySQL Client Tool](#4)  
  4.1. [Install and Connect to HeidiSQL](#4.1)  






## <div id='1'> 1. Document Outline
### <div id='1.1'> 1.1. Purpose

This document (MySQL Service Pack Installation Guide) describes how to install MySQL Service Pack, a service pack provided by PaaS-TA, using Bosch.
	
	
### <div id='1.2'> 1.2. Range
The installation scope was written based on the basic installation to verify the MySQL service pack.

### <div id='1.3'> 1.3. References
BOSH Document: [http://bosh.io](http://bosh.io)  
Cloud Foundry Document: [https://docs.cloudfoundry.org](https://docs.cloudfoundry.org)  

## <div id='2'> 2. MySQL Service Installation

### <div id="2.1"/> 2.1. Prerequisite  

This installation guide is based on installation in a Linux environment.
To install the service pack, BOSH CLI v2 must be installed and logged in to BOSH.
If BOSH CLI v2 is not installed, you must first refer to the BOSH 2.0 Installation Guide document to install BOSH CLI v2 and familiarize yourself with the usage.

### <div id="2.2"/> 2.2. Stemcell Check

Check the list of Stemcells to verify that the Stemcells required for service installation are uploaded.
The Stemcell in this guide uses ubuntu-bionic 1.76.

> $ bosh -e ${BOSH_ENVIRONMENT} stemcells

```
Using environment '10.0.1.6' as client 'admin'

Name                                       Version   OS             CPI  CID  
bosh-openstack-kvm-ubuntu-bionic-go_agent  1.76      ubuntu-bionic  -    ce507ae4-aca6-4a6d-b7c7-220e3f4aaa7d

(*) Currently deployed

1 stemcells

Succeeded
```

If the corresponding Stemcell is not uploaded, copy the corresponding IaaS environment and version stemcell link from [bosh.io Stemcell] (https://bosh.io/stemcells/) and execute the following command.

```
# Example of Stemcell Upload Command
$ bosh -e ${BOSH_ENVIRONMENT} upload-stemcell -n {STEMCELL_URL}
```

### <div id="2.3"/> 2.3. Deployment Download  

Download the deployment needed from Git Repository and place the file at the service installation directory

- Service Deployment Git Repository URL : https://github.com/PaaS-TA/service-deployment/tree/v5.1.5

```
# Deployment File Download , make directory, change directory
$ mkdir -p ~/workspace
$ cd ~/workspace

# Deployment File Download
$ git clone https://github.com/PaaS-TA/service-deployment.git -b v5.1.5
```

### <div id="2.4"/> 2.4. Deployment File Modification

The BOSH Deployment manifest is a YAML file that defines the properties of the Components element and the deployment.
Network, vm_type, disk_type, etc. used in the deployment file utilize Cloud config, and refer to the PaaS-TAAP installation guide for utilization methods

- Check the contents of the cloud config setting.

> $ bosh -e ${BOSH_ENVIRONMENT} cloud-config   

```
Using environment '10.0.1.6' as client 'admin'

azs:
- cloud_properties:
    availability_zone: ap-northeast-2a
  name: z1
- cloud_properties:
    availability_zone: ap-northeast-2a
  name: z2

... ((Skip)) ...

disk_types:
- disk_size: 1024
  name: default
- disk_size: 1024
  name: 1GB

... ((Skip)) ...

networks:
- name: default
  subnets:
  - az: z1
    cloud_properties:
      security_groups: paasta-security-group
      subnet: subnet-00000000000000000
    dns:
    - 8.8.8.8
    gateway: 10.0.1.1
    range: 10.0.1.0/24
    reserved:
    - 10.0.1.2 - 10.0.1.9
    static:
    - 10.0.1.10 - 10.0.1.120

... ((Skip)) ...

vm_types:
- cloud_properties:
    ephemeral_disk:
      size: 3000
      type: gp2
    instance_type: t2.small
  name: minimal
- cloud_properties:
    ephemeral_disk:
      size: 10000
      type: gp2
    instance_type: t2.small
  name: small

... ((Skip)) ...

Succeeded
```

- Modify the variable files used by Deployment YAML to suit your server environment.

> $ vi ~/workspace/service-deployment/mysql/vars.yml	
```
# STEMCELL
stemcell_os: "ubuntu-bionic"                                     # stemcell os
stemcell_version: "1.76"                                       # stemcell version

# NETWORK
private_networks_name: "default"                                 # private network name

# MYSQL
mysql_azs: [z4]                                                  # mysql azs
mysql_instances: 1                                               # mysql instances (N)
mysql_vm_type: "small"                                           # mysql vm type
mysql_persistent_disk_type: "8GB"                                # mysql persistent disk type
mysql_port: 13306                                                # mysql port (e.g. 13306) -- Do Not Use "3306"
mysql_admin_password: "<MYSQL_ADMIN_PASSWORD>"                   # mysql admin password (e.g. "admin!Service")

# ARBITRATOR
arbitrator_azs: [z4]                                             # arbitrator azs 
arbitrator_instances: 1                                          # arbitrator instances (1)
arbitrator_vm_type: "small"                                      # arbitrator vm type

# PROXY
proxy_azs: [z4]                                                  # proxy azs
proxy_instances: 1                                               # proxy instances (1)
proxy_vm_type: "small"                                           # proxy vm type
proxy_mysql_port: 13307                                          # proxy mysql port (e.g. 13307) -- Do Not Use "3306"

# MYSQL_BROKER
mysql_broker_azs: [z4]                                           # mysql broker azs
mysql_broker_instances: 1                                        # mysql broker instances (1)
mysql_broker_vm_type: "small"                                    # mysql broker vm type
```

### <div id="2.5"/> 2.5. Service Installation

- Modify the VARIABLES settings in the Deploy script file to match your server environment, and select whether to add the option file.
     (Optional) -o operations/cce.yml (Appky CCE when installing)


> $ vi ~/workspace/service-deployment/mysql/deploy.sh

```
#!/bin/bash

# VARIABLES
COMMON_VARS_PATH="<COMMON_VARS_FILE_PATH>"    # common_vars.yml File Path (e.g. ../../common/common_vars.yml)
BOSH_ENVIRONMENT="${BOSH_ENVIRONMENT}"        # bosh director alias name (When not using create-bosh-login.sh provided by PaaS-TA, check the name at bosh envs and enter)

# DEPLOY
bosh -e ${BOSH_ENVIRONMENT} -n -d mysql deploy --no-redact mysql.yml \
    -o operations/cce.yml \
    -l ${COMMON_VARS_PATH} \
    -l vars.yml
```

- Service Installation
```
$ cd ~/workspace/service-deployment/mysql  
$ sh ./deploy.sh  
```  


### <div id="2.6"/> 2.6. Service Installation Check

Check the installed service.

> $ bosh -e ${BOSH_ENVIRONMENT} -d mysql vms  

```
Using environment '10.0.1.6' as client 'admin'

Task 4525. Done

Deployment 'mysql'

Instance                                                       Process State  AZ  IPs            VM CID                                   VM Type  Active  
arbitrator/2e190b67-e2b7-4e2d-a72d-872c2019c963                running        z5  10.30.107.165  vm-214663a8-fcbc-4ae4-9aae-92027b9725a9  minimal  true  
mysql-broker/05c44b41-0fc1-41c0-b814-d79558850480              running        z5  10.30.107.167  vm-7c3edc00-3074-4e98-9c89-9e9ba83b47e4  minimal  true  
mysql/fe6943ed-c0c1-4a99-8f4c-d209e165898a                     running        z5  10.30.107.164  vm-81ecdc43-03d2-44f5-9b89-c6cdaa443d8b  minimal  true  
proxy/5b883a78-eb43-417f-98a2-d44c13c29ed4                     running        z5  10.30.107.168  vm-e447eb75-1119-451f-adc9-71b0a6ef1a6a  minimal  true  

5 vms

Succeeded
```	

## <div id='3'> 3. MySQL Linkage Sample Web App Description

This Sample App can be used as it is bound with the MySQL service when distributed to PaaS-TA while the MySQL service is provisioned.

### <div id='3.1'> 3.1. Register MySQL Service Broker
Once the Mysql service pack has been deployed, the application must first register the MySQL service broker to use the service pack.
When registering a service broker, you must be logged in as a user who can register a service broker in PaaS-TA.
- Check the list of service brokers.

> $ cf service-brokers  
```  
Getting service brokers as admin...

name   url
No service brokers found
```   

- Service Broker Registration Commands
```
cf create-service-broker [SERVICE_BROKER] [USERNAME] [PASSWORD] [SERVICE_BROKER_URL]

[SERVICE_BROKER] : Service Broker Name
[USERNAME] / [PASSWORD] : User ID/PASSWORD to access the service broker
[SERVICE_BROKER_URL] : Service Broker Access URL
```
	
- Register MySQL service broker.

>`$ cf create-service-broker mysql-service-broker admin cloudfoundry http://<mysql-broker_ip>:8080`
```  
$ cf create-service-broker mysql-service-broker admin cloudfoundry http://10.30.107.167:8080
Creating service broker mysql-service-broker as admin...
OK
```  

- Check the registered MySQL service broker.

>`$ cf service-brokers`
```  
$ cf service-brokers
Getting service brokers as admin...

name                      url
mysql-service-broker      http://10.30.107.167:8080
```  

- Check the list of accessible services.

>`$ cf service-access`
```  
$ cf service-access
Getting service access as admin...
broker: mysql-service-broker
   service    plan                 access   orgs
   Mysql-DB   Mysql-Plan1-10con    none
   Mysql-DB   Mysql-Plan2-100con   none
```  
Default access is not allowed when creating a service broker.

- Assign access to the service to a specific organization and double-check the access service list. (Overall Organization)

> $ cf enable-service-access Mysql-DB  
```
Enabling access to all plans of service Mysql-DB for all orgs as admin...
OK
```
> $ cf service-access   
```
Getting service access as admin...
broker: mysql-service-broker
   service    plan                 access   orgs
   Mysql-DB   Mysql-Plan1-10con    all
   Mysql-DB   Mysql-Plan2-100con   all
```  

### <div id='3.2'> 3.2. Sample Web App Download  

Sample App is deployed as an app in PaaS-TA. When the app runs, the initial data is generated by accessing the bound MySQL service connection information.
After accessing the app through a browser, the initially generated data can be inquired through "MYSQL data import".

- Download zip file of sample apps
```
$ wget https://nextcloud.paas-ta.org/index.php/s/NDgriPk5cgeLMfG/download --content-disposition  
$ unzip paasta-service-samples.zip  
$ cd paasta-service-samples/mysql  
```

### <div id='3.3'> 3.3. Apply for service in PaaS-TA
In order to use the MySQL service in the Sample App, you must apply for a service (Provision).

*Note: you must be logged in as a user who can apply for the service from PaaS-TA when applying for a service.

- Check whether there is a service in the PaaS-TA Marketplace first.

> $ cf marketplace   
```  
Getting services from marketplace in org org system / space dev as admin...
OK

service      plans                                    description
Mysql-DB     Mysql-Plan1-10con, Mysql-Plan2-100con*   A simple mysql implementation

TIP:  Use 'cf marketplace -s SERVICE' to view descriptions of individual plans of a given service.
```  

- Service Instance Application Commands
```
cf create-service [SERVICE] [PLAN] [SERVICE_INSTANCE]

[SERVICE] : Name of Service being shown in Marketplace
[PLAN] : Policies for Services
[SERVICE_INSTANCE] : Name of the service instance to be created
```
	
- If there is a service you want from Marketplace, apply for a service (Provision).

> $ cf create-service Mysql-DB Mysql-Plan2-100con mysql-service-instance   
```  
Creating service instance mysql-service-instance in org org system / space dev as admin...
OK

Attention: The plan `Mysql-Plan2-100con` of service `Mysql-DB` is not free.  The instance `mysql-service-instance` will incur a cost.  Contact your administrator if you think this is in error.
```  

- Verify the MySQL service instance that was created.

> $ cf services 
```  
Getting services in org system / space dev as admin...
OK

name                      service    plan                 bound apps            last operation
mysql-service-instance    Mysql-DB   Mysql-Plan2-100con                         create succeeded
```  

### <div id='3.4'> 3.4. Sample Web App Deployment and MySQL Bind Verification 
When the service application is completed, the Sample Web App binds the generated service instance and uses the MySQL service in the App.
*참고: When applying for service bind, you must be logged in as a user who can apply for service bind in PaaS-TA.
	
- Check the manifest file. 

> $ vi manifest.yml   

```
---
applications:
- name: mysql-sample-app
  memory: 1024M
  instances: 1
  buildpack: java_buildpack
  path: mysql-sample-app.war
  env:
    mysql_datasource_driver-class-name: org.mariadb.jdbc.Driver
    mysql_datasource_jdbc-url: jdbc:\${vcap.services.mysql-service-instance.credentials.uri}
    mysql_datasource_username: \${vcap.services.mysql-service-instance.credentials.username}
    mysql_datasource_password: \${vcap.services.mysql-service-instance.credentials.password}

```

- Deploy the app with the --no-start option.
> $ cf push --no-start  
```  
Applying manifest file /home/ubuntu/workspace/samples/paasta-service-samples/mysql/manifest.yml...
Manifest applied
Packaging files to upload...
Uploading files...
 26.48 MiB / 26.48 MiB [================================================================================================================] 100.00% 1s

Waiting for API to complete processing files...

name:              mysql-sample-app
requested state:   stopped
routes:            mysql-sample-app.paasta.kr
last uploaded:     
stack:             
buildpacks:        

type:           web
sidecars:       
instances:      0/1
memory usage:   1024M
     state   since                  cpu    memory   disk     details
#0   down    2021-11-22T05:21:57Z   0.0%   0 of 0   0 of 0   
```  
	
- Apply for service instance bind created by Sample Web App.

> $ cf bind-service mysql-sample-app mysql-service-instance  

```
Binding service mysql-service-instance to app mysql-sample-app in org system / space dev as admin...
OK
```

When running the app, add a security group for communication with the service.

- modify rule.json 
> $ vi rule.json   
```
## set mysql's proxy IP at destination
[
  {
    "protocol": "tcp",
    "destination": "<proxy_ip>",
    "ports": "13307"
  }
]
```
<br>

- Create a security group.

> $ cf create-security-group mysql rule.json  

```
Creating security group mysql as admin...

OK		
```

<br>

- Apply the security group that you created to use the Mysql service.
> $ cf bind-running-security-group mysql  
```
Binding security group mysql to running as admin...
OK		
```
	
- Restart the app.


> $ cf restart mysql-sample-app  
```	
Restarting app mysql-sample-app in org system / space dev as admin...

Staging app and tracing logs...
   Downloading java_buildpack...
   Downloaded java_buildpack
   Cell 4a88ce8b-1e72-485a-8f62-1fe0c6b9a7cd creating container for instance 678aa272-945b-41a9-8924-0782891d0cc4
   Cell 4a88ce8b-1e72-485a-8f62-1fe0c6b9a7cd successfully created container for instance 678aa272-945b-41a9-8924-0782891d0cc4
   Downloading app package...
   Downloaded app package (30.5M)

........
........
Instances starting...
Instances starting...

name:              mysql-sample-app
requested state:   started
routes:            mysql-sample-app.paasta.kr
last uploaded:     Mon 22 Nov 05:23:48 UTC 2021
stack:             cflinuxfs3
buildpacks:        
	name             version                                                             detect output   buildpack name
	java_buildpack   v4.37-https://github.com/cloudfoundry/java-buildpack.git#ab2b4512   java            java
```  

- Check if the app uses the MySQL service normally.

![update_mysql_vsphere_34]  

## <div id='4'> 4. Access MySQL Client Tool

The MySQL service connection information bound to the application is configured with Private IP and cannot be connected directly from the MySQL Client tool. Therefore, the MySQL Client tool should be connected using a tool that provides SSH tunnels, proxy tunnels, etc. This guide provides a way to connect using SSH tunnels and guides you to HeidiSQL, an open-source, using the MySQL Client tool. To connect from HeidiSQL, you must first create an SSH tunnelable VM instance. This instance must be accessible via SSH, and after that, a security group must be configured to access the service pack installed in Open PaaS with Private IP and its ports. This part is configured by contacting the vSphere administrator and OpenPaaS operator.

### <div id='4.1'> 4.1. Installing and Connecting HeidiSQL

The HeidiSQL program is an open-source software that can be used for free.

- To download HeidiSQL, go to the URL below to download the installation file.

>[http://www.heidisql.com/download.php](http://www.heidisql.com/download.php)

>![mysql_vsphere_4.1.01]

<br>

- Run the downloaded installation file.

>![mysql_vsphere_4.1.02]

<br>

- Bellow is a guide for installing HeidSQL. Click Next.

>![mysql_vsphere_4.1.03]

<br>

- It is about program license. Check I accept the agreement and click Next.

>![mysql_vsphere_4.1.04]

<br>

- Set the path to install HeidiSQL and click the Next button.

>If no separate path setting is required, it is installed in the C drive Program Files folder by default.

>![mysql_vsphere_4.1.05]

<br>

- 설치 완료 후 시작메뉴에 HeidiSQL 바로가기 아이콘의 이름을 설정하는 과정이다.  
>Next 버튼을 클릭하여 다음 과정을 진행한다.

>![mysql_vsphere_4.1.06]

<br>

- 체크박스가 4개가 있다. 아래의 경우를 고려하여 체크 및 해제를 한다.
>
  바탕화면에 바로가기 아이콘을 생성할 경우  
  sql확장자를 HeidiSQL 프로그램으로 실행할 경우  
  heidisql 공식 홈페이지를 통해 자동으로 update check를 할 경우  
  heidisql 공식 홈페이지로 자동으로 버전을 전송할 경우

> 체크박스에 체크 설정/해제를 완료했다면 Next 버튼을 클릭한다.

>![mysql_vsphere_4.1.07]

<br>

- 설치를 위한 모든 설정이 한번에 출력된다. 확인 후 Install 버튼을 클릭하여 설치를 진행한다.

>![mysql_vsphere_4.1.08]

<br>

- Finish 버튼 클릭으로 설치를 완료한다.

>![mysql_vsphere_4.1.09]

<br>

- HeidiSQL을 실행했을 때 처음 뜨는 화면이다. 이 화면에서 Server에 접속하기 위한 profile을 설정/저장하여 접속할 수 있다. 신규 버튼을 클릭한다.

>![mysql_vsphere_4.1.10]

<br>

- 어떤 Server에 접속하기 위한 Connection 정보인지 별칭을 입력한다.

>![mysql_vsphere_4.1.11]

<br>

- 네트워크 유형의 목록에서 MySQL(SSH tunel)을 선택한다.

>![mysql_vsphere_4.1.12]

<br>

- 아래 붉은색 영역에 접속하려는 서버 정보를 모두 입력한다.

>![mysql_vsphere_4.1.13]

>서버 정보는 Application에 바인드되어 있는 서버 정보를 입력한다. cf env <app_name> 명령어로 이용하여 확인한다.

>**예)** $cf env hello-spring-mysql

>![mysql_vsphere_4.1.14]

<br>

- - SSH 터널 탭을 클릭하고 OpenPaaS 운영 관리자에게 제공받은 SSH 터널링 가능한 서버 정보를 입력한다. plink.exe 위치 입력은 Putty에서 제공하는 plink.exe 실행 위치를 넣어주고 만일 해당 파일이 없을 경우 plink.exe 내려받기 링크를 클릭하여 다운받는다. 로컬 포트 정보는 임의로 넣고 열기 버튼을 클릭하면 Mysql 데이터베이스에 접속한다.

>(참고) 만일 개인 키로 접속이 가능한 경우에는 openstack용 Open PaaS Mysql 서비스팩 설치 가이드를 참고한다.

>![mysql_vsphere_4.1.15]

<br>

- 접속이 완료되면 좌측에 스키마 정보가 나타난다. 하지만 초기설정은 테이블, 뷰, 프로시져, 함수, 트리거, 이벤트 등 모두 섞여 있어서 한눈에 구분하기가 힘들어서 접속한 DB 별칭에 마우스 오른쪽 클릭 후 "트리 방식 옵션" - "객체를 유형별로 묶기"를 클릭하면 아래 화면과 같이 각 유형별로 구분이된다.

>![mysql_vsphere_4.1.16]

<br>

- 우측 화면에 쿼리 탭을 클릭하여 Query문을 작성한 후 실행 버튼(삼각형)을 클릭한다.  

>쿼리문에 이상이 없다면 정상적으로 결과를 얻을 수 있을 것이다.

>![mysql_vsphere_4.1.17]
	
	
	
[mysql_vsphere_1.3.01]:./images/mysql/mysql_vsphere_1.3.01.png
[mysql_vsphere_2.2.01]:./images/mysql/mysql_vsphere_2.2.01.png
[mysql_vsphere_2.2.02]:./images/mysql/mysql_vsphere_2.2.02.png
[mysql_vsphere_2.2.03]:./images/mysql/mysql_vsphere_2.2.03.png
[mysql_vsphere_2.2.04]:./images/mysql/mysql_vsphere_2.2.04.png
[mysql_vsphere_2.2.05]:./images/mysql/mysql_vsphere_2.2.05.png
[mysql_vsphere_2.2.06]:./images/mysql/mysql_vsphere_2.2.06.png
[mysql_vsphere_2.2.07]:./images/mysql/mysql_vsphere_2.2.07.png
[mysql_vsphere_2.2.08]:./images/mysql/mysql_vsphere_2.2.08.png
[mysql_vsphere_2.3.01]:./images/mysql/mysql_vsphere_2.3.01.png
[mysql_vsphere_2.3.02]:./images/mysql/mysql_vsphere_2.3.02.png
[mysql_vsphere_2.3.03]:./images/mysql/mysql_vsphere_2.3.03.png
[mysql_vsphere_2.3.04]:./images/mysql/mysql_vsphere_2.3.04.png
[mysql_vsphere_2.3.05]:./images/mysql/mysql_vsphere_2.3.05.png
[mysql_vsphere_2.3.06]:./images/mysql/mysql_vsphere_2.3.06.png
[mysql_vsphere_2.3.07]:./images/mysql/mysql_vsphere_2.3.07.png
[mysql_vsphere_2.4.01]:./images/mysql/mysql_vsphere_2.4.01.png
[mysql_vsphere_2.4.02]:./images/mysql/mysql_vsphere_2.4.02.png
[mysql_vsphere_2.4.03]:./images/mysql/mysql_vsphere_2.4.03.png
[mysql_vsphere_2.4.04]:./images/mysql/mysql_vsphere_2.4.04.png
[mysql_vsphere_2.4.05]:./images/mysql/mysql_vsphere_2.4.05.png
[mysql_vsphere_3.1.01]:./images/mysql/mysql_vsphere_3.1.01.png
[mysql_vsphere_3.2.01]:./images/mysql/mysql_vsphere_3.2.01.png
[mysql_vsphere_3.2.02]:./images/mysql/mysql_vsphere_3.2.02.png
[mysql_vsphere_3.2.03]:./images/mysql/mysql_vsphere_3.2.03.png
[mysql_vsphere_3.3.01]:./images/mysql/mysql_vsphere_3.3.01.png
[mysql_vsphere_3.3.02]:./images/mysql/mysql_vsphere_3.3.02.png
[mysql_vsphere_3.3.03]:./images/mysql/mysql_vsphere_3.3.03.png
[mysql_vsphere_3.3.04]:./images/mysql/mysql_vsphere_3.3.04.png
[mysql_vsphere_3.3.05]:./images/mysql/mysql_vsphere_3.3.05.png
[mysql_vsphere_3.3.06]:./images/mysql/mysql_vsphere_3.3.06.png
[mysql_vsphere_3.3.07]:./images/mysql/mysql_vsphere_3.3.07.png
[mysql_vsphere_3.3.08]:./images/mysql/mysql_vsphere_3.3.08.png
[mysql_vsphere_3.3.09]:./images/mysql/mysql_vsphere_3.3.09.png
[mysql_vsphere_4.1.01]:./images/mysql/mysql_vsphere_4.1.01.png
[mysql_vsphere_4.1.02]:./images/mysql/mysql_vsphere_4.1.02.png
[mysql_vsphere_4.1.03]:./images/mysql/mysql_vsphere_4.1.03.png
[mysql_vsphere_4.1.04]:./images/mysql/mysql_vsphere_4.1.04.png
[mysql_vsphere_4.1.05]:./images/mysql/mysql_vsphere_4.1.05.png
[mysql_vsphere_4.1.06]:./images/mysql/mysql_vsphere_4.1.06.png
[mysql_vsphere_4.1.07]:./images/mysql/mysql_vsphere_4.1.07.png
[mysql_vsphere_4.1.08]:./images/mysql/mysql_vsphere_4.1.08.png
[mysql_vsphere_4.1.09]:./images/mysql/mysql_vsphere_4.1.09.png
[mysql_vsphere_4.1.10]:./images/mysql/mysql_vsphere_4.1.10.png
[mysql_vsphere_4.1.11]:./images/mysql/mysql_vsphere_4.1.11.png
[mysql_vsphere_4.1.12]:./images/mysql/mysql_vsphere_4.1.12.png
[mysql_vsphere_4.1.13]:./images/mysql/mysql_vsphere_4.1.13.png
[mysql_vsphere_4.1.14]:./images/mysql/mysql_vsphere_4.1.14.png
[mysql_vsphere_4.1.15]:./images/mysql/mysql_vsphere_4.1.15.png
[mysql_vsphere_4.1.16]:./images/mysql/mysql_vsphere_4.1.16.png
[mysql_vsphere_4.1.17]:./images/mysql/mysql_vsphere_4.1.17.png



[update_mysql_vsphere_01]:./images/mysql/update_mysql_vsphere_01.png
[update_mysql_vsphere_02]:./images/mysql/update_mysql_vsphere_02.png
[update_mysql_vsphere_03]:./images/mysql/update_mysql_vsphere_03.png
[update_mysql_vsphere_04]:./images/mysql/update_mysql_vsphere_04.png
[update_mysql_vsphere_05]:./images/mysql/update_mysql_vsphere_05.png
[update_mysql_vsphere_06]:./images/mysql/update_mysql_vsphere_06.png
[update_mysql_vsphere_07]:./images/mysql/update_mysql_vsphere_07.png
[update_mysql_vsphere_08]:./images/mysql/update_mysql_vsphere_08.png
[update_mysql_vsphere_09]:./images/mysql/update_mysql_vsphere_09.png
[update_mysql_vsphere_10]:./images/mysql/update_mysql_vsphere_10.png
[update_mysql_vsphere_11]:./images/mysql/update_mysql_vsphere_11.png
[update_mysql_vsphere_12]:./images/mysql/update_mysql_vsphere_12.png
[update_mysql_vsphere_13]:./images/mysql/update_mysql_vsphere_13.png
[update_mysql_vsphere_14]:./images/mysql/update_mysql_vsphere_14.png
[update_mysql_vsphere_15]:./images/mysql/update_mysql_vsphere_15.png

[update_mysql_vsphere_25]:./images/mysql/update_mysql_vsphere_25.png
[update_mysql_vsphere_30]:./images/mysql/update_mysql_vsphere_30.png
[update_mysql_vsphere_31]:./images/mysql/update_mysql_vsphere_31.png
[update_mysql_vsphere_34]:./images/mysql/update_mysql_vsphere_34.png

[update_mysql_vsphere_35]:./images/mysql/update_mysql_vsphere_35.png
[update_mysql_vsphere_36]:./images/mysql/update_mysql_vsphere_36.png
[update_mysql_vsphere_37]:./images/mysql/update_mysql_vsphere_37.png
[update_mysql_vsphere_38]:./images/mysql/update_mysql_vsphere_38.png
[update_mysql_vsphere_39]:./images/mysql/update_mysql_vsphere_39.png
[update_mysql_vsphere_40]:./images/mysql/update_mysql_vsphere_40.png
[update_mysql_vsphere_41]:./images/mysql/update_mysql_vsphere_41.png
[update_mysql_vsphere_42]:./images/mysql/update_mysql_vsphere_42.png
[update_mysql_vsphere_43]:./images/mysql/update_mysql_vsphere_43.png
[update_mysql_vsphere_44]:./images/mysql/update_mysql_vsphere_44.png
[update_mysql_vsphere_45]:./images/mysql/update_mysql_vsphere_45.png
[update_mysql_vsphere_46]:./images/mysql/update_mysql_vsphere_46.png
[update_mysql_vsphere_47]:./images/mysql/update_mysql_vsphere_47.png
[update_mysql_vsphere_48]:./images/mysql/update_mysql_vsphere_48.png
[update_mysql_vsphere_49]:./images/mysql/update_mysql_vsphere_49.png
[update_mysql_vsphere_50]:./images/mysql/update_mysql_vsphere_50.png

### [Index](https://github.com/PaaS-TA/Guide-eng/blob/master/README.md) > [AP Install](../README.md) > MySQL Service
