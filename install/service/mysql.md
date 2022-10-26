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

This document (MySQL Service Pack Installation Guide) describes how to install MySQL Service Pack, a service pack provided by PaaS-TA, using Bosh.
	
	
### <div id='1.2'> 1.2. Range
The installation range was written based on the basic installation to verify the MySQL service pack.

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

If the corresponding Stemcell is not uploaded, copy the Stemcell link to the corresponding IaaS environment and version from [bosh.io Stemcell](https://bosh.io/stemcells/) and run the following command.

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
Network, vm_type, disk_type, etc. used in the deployment file utilize Cloud config, and refer to the PaaS-TA AP installation guide for utilization methods

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

This Sample App can be used as it is bound with the MySQL service when deployed to PaaS-TA while the MySQL service is provisioned.

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

- This is the process of setting the name of the HeidiSQL shortcut icon at the Start menu after the installing 
>Click the Next button to proceed to the next step.

>![mysql_vsphere_4.1.06]

<br>

- There are 4 check boxes. Check and release considering the following cases.
>
  When you create a shortcut icon on your desktop
  When running the sql extension as a HeidiSQL program
  When you automatically check for updates through the official website of heidisql
  When you automatically send the version to the official website of heidisql,

> Click the Next button when you have finished setting/unchecking the check box.

>![mysql_vsphere_4.1.07]

<br>

- All settings for installation are printed at once. After checking, click the Install button to proceed with the installation

>![mysql_vsphere_4.1.08]

<br>

- Click the Finish button to complete the installation.

>![mysql_vsphere_4.1.09]

<br>

- This is the first screen that appears when you run HeidiSQL. On this screen, you can access by setting/saving a profile for accessing the server. Click the New button.

>![mysql_vsphere_4.1.10]

<br>

- Enter an alias for the connection information to connect to which server

>![mysql_vsphere_4.1.11]

<br>

- Select MySQL (SSHTunel) from the list of network types.

>![mysql_vsphere_4.1.12]

<br>

- Enter all server information to access the red area below.

>![mysql_vsphere_4.1.13]

>The server information inputs the server information bound to the application. Check using the cfenv <app_name> command.

>**Ex.)** $cf env hello-spring-mysql

>![mysql_vsphere_4.1.14]

<br>

- - Click the SSH Tunnel tab and enter the SSH tunnelable server information provided to the OpenPaaS operations manager. The plink.exe location entry lets you enter the location where Putty runs the plink.exe and if the file is not present, plink. Click the download link to download the exe. Enter local port information arbitrarily and click the Open button to access the Mysql database.

>(Note) If access is possible with a private key, refer to the Open PaaS Mysql service pack installation guide for openstack.

>![mysql_vsphere_4.1.15]

<br>

- When the connection is complete, schema information appears on the left. However, the initial settings are all mixed with tables, views, procedures, functions, triggers, and events, which are difficult to distinguish at a glance, so right-click on the DB alias you accessed and click "Tree Options" - "Bundle Objects by Type" as shown on the screen below.

>![mysql_vsphere_4.1.16]

<br>

- On the right screen, click the Query tab to create a Query statement, and then click the Run button (triangle).

>If there is no abnormality in the query statement, the result will be obtained normally.

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
