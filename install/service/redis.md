### [Index](https://github.com/PaaS-TA/Guide-eng/blob/master/README.md) > [AP Install](../README.md) > Redis Service

## Table of Contents  

1. [Document Outline](#1)  
  1.1. [Purpose](#1.1)  
  1.2. [Range](#1.2)  
  1.3. [References](#1.3)  
  
2. [On-Demand-Redis Service Installation](#2)  
  2.1. [Prerequisite](#2.1)   
  2.2. [Stemcell Check](#2.2)    
  2.3. [Deployment Download](#2.3)   
  2.4. [Deployment File Modification](#2.4)  
  2.5. [Service Installation](#2.5)    
  2.6. [Service Installation Check](#2.6)  

3. [On-Demand-Redis Service Broker Registration Using CF CLI](#3)  
  3.1. [On-Demand-Redis Service Broker Registration](#3.1)  
  3.2. [Sample App Download](#3.2)  
  3.3. [Request for service in PaaS-TA](#3.3)  
  3.4. [Request for service bind to Sample App and check for App](#3.4)  

4. [Redis Service Test using Portal](#4)  
  4.1. [Application for service](#4.1)  



## <div id='1'> 1. Document Outline

### <div id='1.1'> 1.1. Purpose
This document (Redis service pack installation guide) describes how to install Redis service pack, which is a service pack provided by PaaS-TA, using Bosh.

### <div id='1.2'> 1.2. Range
The installation range was prepared based on the basic installation to verify the Redis service pack.

### <div id='1.3'> 1.3. References
BOSH Document: [http://bosh.io](http://bosh.io)  
Cloud Foundry Document: [https://docs.cloudfoundry.org](https://docs.cloudfoundry.org)  


## <div id='2'>  2. On-Demand Redis Service Installation
	
### <div id="2.1"/> 2.1. Prerequisite  

Check the Stemcell list to make sure that the Stemcell required for service installation is uploaded.
The Stemcell of this guide uses ubuntu-jammy 1.181.

> $ bosh -e ${BOSH_ENVIRONMENT} stemcells

```
Using environment '10.0.1.6' as client 'admin'

Name                                       Version   OS             CPI  CID  
bosh-openstack-kvm-ubuntu-jammy-go_agent  1.181      ubuntu-jammy  -    ce507ae4-aca6-4a6d-b7c7-220e3f4aaa7d

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

- Service Deployment Git Repository URL : https://github.com/PaaS-TA/service-deployment/tree/v5.1.25

```
# Deployment File Download , make directory, change directory
$ mkdir -p ~/workspace
$ cd ~/workspace

# Deployment File Download
$ git clone https://github.com/PaaS-TA/service-deployment.git -b v5.1.25

# common_vars.yml File Download (Download if common_vars.yml doesn't exist)
$ git clone https://github.com/PaaS-TA/common.git
```

### <div id="2.4"/> 2.4. Deployment File Modification

The BOSH Deployment manifest is a YAML file that defines the properties of components elements and deployments. 
Cloud config is used for network, vm_type, and disk_type used in Deployment files, and refer to the PaaS-TA AP installation guide for the usage.

- Check the Cloud config settings.

> $ bosh -e micro-bosh cloud-config   

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

- Modify common_vars.yml to suit the server environment.
- The variables used in redis are: bosh_url, bosh_client_admin_id, bosh_client_admin_secret, bosh_director_port, bosh_oauth_port, system_domain, paasta_admin_username, paasta_admin_password, and bosh_version.

> $ vi ~/workspace/common/common_vars.yml
```
... ((Skip)) ...
bosh_url: "https://10.0.1.6"			# BOSH URL (e.g. "https://00.000.0.0")
bosh_client_admin_id: "admin"			# BOSH Client Admin ID
bosh_client_admin_secret: "ert7na4jpew48"	# BOSH Client Admin Secret('echo $(bosh int ~/workspace/paasta-deployment/bosh/{iaas}/creds.yml --path /admin_password)' can check by using this command)
bosh_director_port: 25555			# BOSH director port
bosh_oauth_port: 8443				# BOSH oauth port
bosh_version: 271.2				# BOSH version('bosh env' command for on-demand service, e.g. "271.2")
system_domain: "61.252.53.246.nip.io"		# Domain (Same as HAProxy Public IP when using nip.io)
paasta_admin_username: "admin"			# PaaS-TA Admin Username
paasta_admin_password: "admin"			# PaaS-TA Admin Password
... ((Skip)) ...
```


- Modify the variable files used by Deployment YAML to suit the server environment.

> $ vi ~/workspace/deployment/service-deployment/redis/vars.yml
```
# STEMCELL
stemcell_os: "ubuntu-jammy"                                      # Deployment Main Stemcell OS
stemcell_version: "1.181"                                        # Main Stemcell Version

# NETWORK
private_networks_name: "default"                                  # private network name

# MARIA DB
mariadb_azs: [z5]                                                 # mariadb azs
mariadb_instances: 1                                              # mariadb instances 
mariadb_vm_type: "medium"                                         # mariadb vm type
mariadb_persistent_disk_type: "2GB"                               # mariadb persistent disk type
mariadb_user_password: "admin"                                    # mariadb admin password
mariadb_port: 13306                                               # mariadb port (e.g. 13306) -- Do Not Use "3306"

# ON DEMAND BROKER
broker_azs: [z5]                                                  # broker azs
broker_instances: 1                                               # broker instances 
broker_vm_type: "service_medium"                                  # broker vm type

# REDIS
redis_azs: [z5]                                                   # redis azs
redis_vm_type: "medium"                                           # redis vm type
redis_persistent_disk_type: "1GB"                                 # redis persistent disk type

# PROPERTIES
broker_server_port: 8080                                          # broker server port

### On-Demand Dedicated Service Instance Properties ###
on_demand_service_instance_name: "redis"                          # On-Demand Service Instance Name
service_password: "PaaS-TA#2021!"                                 # On-Demand Redis Service password
service_port: 6379                                                # On-Demand Redis Service port

# SERVICE PLAN INFO
service_instance_guid: "54e2de61-de84-4b9c-afc3-88d08aadfcb6"            # Service Instance Guid
service_instance_name: "redis"                                           # Service Instance Name
service_instance_bullet_name: "Redis Dedicated Server Use"               # Service Instance bullet Name
service_instance_bullet_desc: "Redis Service Using a Dedicated Server"   # Enter description about Service Instance bullet
service_instance_plan_guid: "2a26b717-b8b5-489c-8ef1-02bcdc445720"       # Service Instance Plan Guid
service_instance_plan_name: "dedicated-vm"                               # Service Instance Plan Name
service_instance_plan_desc: "Redis service to provide a key-value store" # Enter description about Service Instance Plan
service_instance_org_limitation: "-1"                                    # Limit the number of Service Instances that can be installed in Org. (No limit for -1)
service_instance_space_limitation: "-1"                                  # Limit the number of Service Instances that can be installed in Space. (No limit for -1)
```

### <div id="2.5"/> 2.5. Service Installation

- Modify the VARIABLES settings in the Deploy script file to suit the server environment, and decide whether to add the option file.
     (Optional) -o operations/cce.yml (Apply CCE when installting)

> $ vi ~/workspace/service-deployment/redis/deploy.sh

```
#!/bin/bash

# VARIABLES
COMMON_VARS_PATH="<COMMON_VARS_FILE_PATH>"	# common_vars.yml File Path (e.g. ../../common/common_vars.yml)
BOSH_ENVIRONMENT="${BOSH_ENVIRONMENT}"		# bosh director alias name (Create-bosh-login.sh provided by PaaS-TA.If is not in use, check the name in bosh envs and enter)

# DEPLOY
bosh -e ${BOSH_ENVIRONMENT} -n -d redis deploy --no-redact redis.yml \
    -o operations/cce.yml \
    -l ${COMMON_VARS_PATH} \
    -l vars.yml
```

- Install Service.  
```
$ cd ~/workspace/service-deployment/redis  
$ sh ./deploy.sh  
```  


### <div id="2.6"/> 2.6. Service Installation Check

Check the installed service.

> $ bosh -e micro-bosh -d redis vms  

```
Using environment '10.0.1.6' as client 'admin'

Task 936167. Done

Deployment 'redis'

Instance                                                       Process State  AZ  IPs           VM CID                                   VM Type         Active  
mariadb/e35f3ece-9c34-41f4-a88e-d8365e9b8c70                   running        z5  10.30.255.25  vm-5168ec8d-f42f-40fa-9c3a-8635bf138b0a  medium          true  
paas-ta-on-demand-broker/13c11522-10dd-485c-bb86-3ac5337223d0  running        z5  10.30.255.26  vm-eab6e832-8b7c-49bc-ac04-80258896880d  service_medium  true  

2 vms

Succeeded
```

## <div id='3'> 3. On-Demand-Redis service using CF CLI
### <div id='3.1'> 3.1. On-Demand-Redis Service Broker Registration
When the Redis service pack deployment is complete, you must first register the On-Demand-Redis service broker to use the service pack in the application.
When registering a service broker, you must log in as a user who can register a service broker in PaaS-TA.


- Check the list of service brokers.

> $ cf service-brokers
```
Getting service brokers as admin...

name   url
No service brokers found
```

- Command for registering Service Broker
```
cf create-service-broker [SERVICE_BROKER] [USERNAME] [PASSWORD] [SERVICE_BROKER_URL]

[SERVICE_BROKER] : Service Broker Name
[USERNAME] / [PASSWORD] : User ID / PASSWORD with access to service broker
[SERVICE_BROKER_URL] : Service Broker Access URL
```

	
- Register the On-Demand-Redis service broker.

> $ cf create-service-broker on-demand-redis-service admin cloudfoundry http://<paas-ta-on-demand-broker_ip>:8080 

```
$ cf create-service-broker on-demand-redis-service admin cloudfoundry http://10.30.255.26:8080
Creating service broker on-demand-redis-service as admin...
OK
```

- Check the registered On-Demand-Redis service broker

> $ cf service-brokers 
```
Getting service brokers as admin...

name                     url
on-demand-redis-service  http://10.30.255.26:8080
```


- Check the list of accessible services.

> $ cf service-access 
```
Getting service access as admin...
broker: on-demand-redis-service
   offering   plan           access   orgs
   redis      dedicated-vm   none   

```
Access is initially not permitted when registering as a service broker. Therefore, access is set to none.


- Assign permission to a specific organization to access the service and recheck the access service list. (Overall Organization)

> $ cf enable-service-access redis  <br>
```
Enabling access to all plans of service offering redis for all orgs as admin...
OK
```
> $ cf service-access 
```
Getting service access as admin...
	
broker: on-demand-redis-service
   offering   plan           access   orgs
   redis      dedicated-vm   all   
```

### <div id='3.2'> 3.2. Sample App Download

- Download zip file of sample app
```
$ wget https://nextcloud.paas-ta.org/index.php/s/BoSbKrcXMmTztSa/download --content-disposition  
$ unzip paasta-service-samples-459dad9.zip  
$ cd paasta-service-samples/redis  
```

<br>

### <div id='3.3'> 3.3. Request for service in PaaS-TA
In order to use the Redis service in the Sample App, you must request for a service (Provision).
*Note: When Requesting for a service, you must be logged in as a user who can request for a service in PaaS-TA.

- Check whether there is a service in the PaaS-TA Marketplace first.

> $ cf marketplace

```
OK
service   plans          description
redis     dedicated-vm   A paasta source control service for application development.provision parameters : parameters {owner : owner}
```

<br>

- Command for requesting Service Instance
```
cf create-service [SERVICE] [PLAN] [SERVICE_INSTANCE]

[SERVICE] : Service name shown at the Marketplace
[PLAN] : Policies for Services
[SERVICE_INSTANCE] : Name of the service instance to create
```
	
- If there is a service you want on the Marketplace, request for a service (Provision).

> $ cf create-service redis dedicated-vm redis

```
Creating service instance redis in org system / space dev as admin...
OK

Create in progress. Use 'cf services' or 'cf service redis' to check operation status.
```


<br>

- Check the status of the generated Redis service instance.
 * In the case of create in progress, use, bind, and delete of the service are restricted because the service is being prepared, so you have to wait until it is created successful.
> $ cf service redis  

```
Showing info of service redis in org system / space dev as admin...

name:            redis
service:         redis
tags:            
plan:            dedicated-vm
description:     A paasta source control service for application development.provision parameters : parameters {owner : owner}
documentation:   https://paas-ta.kr
dashboard:       10.30.255.26

Showing status of last operation from service redis...

status:    create in progress
message:   
started:   2019-07-05T05:58:13Z
updated:   2019-07-05T05:58:16Z

There are no bound apps for this service.
```
- Verify that the status of the generated Redis service instance has been created successfully.
```
Showing info of service redis in org system / space dev as admin...

name:            redis
service:         redis
tags:            
plan:            dedicated-vm
description:     A paasta source control service for application development.provision parameters : parameters {owner : owner}
documentation:   https://paas-ta.kr
dashboard:       10.30.255.26

Showing status of last operation from service redis...

status:    create succeeded
message:   test
started:   2019-07-05T05:58:13Z
updated:   2019-07-05T06:01:20Z

There are no bound apps for this service.
```

<br>
	
- When a service is created through an on-demand-service, a security-group is created and automatically assigned to the space.
- Verify that redis_[Service Assigned SpaceGuid] is created in the Security-group.
	
> $ cf space [space] --guid  
```
20bc9b52-c3d5-4cd2-94d9-7f444f9ab464
```

> $ cf security-groups  
```
Getting security groups as admin...
OK

     name                                         organization   space   lifecycle
#0   abacus                                       abacus-org     dev     running
#1   dns                                          <all>          <all>   running
     dns                                          <all>          <all>   staging
#2   public_networks                              <all>          <all>   running
     public_networks                              <all>          <all>   staging
#3   redis_20bc9b52-c3d5-4cd2-94d9-7f444f9ab464   system         dev     running
```


### <div id='3.4'> 3.4. Request for service bind to Sample App and check for App
When the service application is completed, the Sample App binds the generated service instance and uses the Redis service in the App.
*Note: When Requesting for service bind, you must be logged in as a user who can request for service bind in PaaS-TA.

- Check the manifest file.  

> $ vi manifest.yml   

```
---
applications:
- name: redis-example-app
  memory: 256M
  instances: 1
  path: .
  buildpacks: [ruby_buildpack]
```

- Deploy app with --no-start option.

> $ cf push --no-start 
```  
Pushing app redis-example-app to org system / space dev as admin...
Applying manifest file /home/ubuntu/workspace/samples/paasta-service-samples/redis/manifest.yml...
Manifest applied
Packaging files to upload...
Uploading files...
 1.23 MiB / 1.23 MiB [===================================================================================================

Waiting for API to complete processing files...

name:              redis-example-app
requested state:   stopped
routes:            redis-example-app.paastacloud.shop
last uploaded:     
stack:             
buildpacks:        

type:           web
sidecars:       
instances:      0/1
memory usage:   256M
     state   since                  cpu    memory   disk     details
#0   down    2021-11-22T05:39:06Z   0.0%   0 of 0   0 of 0   
```  
  
- Request for service instance bind created by Sample Web App.

> $ cf bind-service redis-example-app redis 

```	
Binding service redis to app redis-example-app in org system / space dev as admin...
OK
```
	
- Restart the App to apply the bind.

> $ cf restart redis-example-app 

```	
Restarting app redis-example-app in org system / space dev as admin...

Staging app and tracing logs...
   Downloading ruby_buildpack...
   Downloaded ruby_buildpack
   Cell 4a88ce8b-1e72-485a-8f62-1fe0c6b9a7cd creating container for instance 4a47d02a-24d6-4046-b2b8-866b915eaf6a
   Cell 4a88ce8b-1e72-485a-8f62-1fe0c6b9a7cd successfully created container for instance 4a47d02a-24d6-4046-b2b8-866b915e
   Downloading app package...
   Downloaded app package (1.4M)
   -----> Ruby Buildpack version 1.8.37

........
........
Instances starting...
Instances starting...

name:              redis-example-app
requested state:   started
routes:            redis-example-app.paastacloud.shop
last uploaded:     Mon 22 Nov 05:40:50 UTC 2021
stack:             cflinuxfs3
buildpacks:        
	name             version   detect output   buildpack name
	ruby_buildpack   1.8.37    ruby            ruby

type:           web
sidecars:       
instances:      1/1
memory usage:   256M
     state     since                  cpu    memory   disk     details
#0   running   2021-11-22T05:41:01Z   0.0%   0 of 0   0 of 0  
```  


<br>

Check if the App uses Redis service normally.

- Check with curl

```
$ export APP=redis-example-app.[CF Domain]
$ curl -X PUT $APP/foo -d 'data=bar'
success
$ curl -X GET $APP/foo
bar
$ curl -X DELETE $APP/foo
success
```


<br>

## <div id='4'> 4. Redis Service Test using portal
If the user and manager portals are installed, it is possible to apply for, bind, and test the Redis service through the portal.


- Access the Administrator Portal and check the list of brokers on the Service Broker page of Service Management.
![1]
- Register the On-Demand-Redis service broker.
![2]
![3]
- Check the registered On-Demand-Redis service broker.
![4]
- Check the list of accessible services on the Service Control page of Service Management.
![5]

Access is initially not permitted when registering as a service broker. Therefore, access is set to none.
- Assign permission to a specific organization to access the service and recheck the access service list. (Overall Organization)
![6]

### <div id='4.1'> 4.1. Application for service
In order to request for a service on the user portal, you must register the service on the catalog page of the administrator portal first to use it.

- Go to the catalog page of the operation management on the administrator portal and register the service.
![7]
Set the app bind parameter value to app_guid and set the on-demand value to Y before proceeding with service registration.
- After logging in to the user portal, create a service from the catalog page.
![8]


- Check the status of the generated Redis service instance.
Service status : in progress
![9]
 
Service status : created succeed
![10]

- Go to the security group page of the administrator portal security and confirm that redis_[service assigned space guide] is created.
![11]


[redis_image_01]:./images/redis/redis_image_01.png
[redis_image_02]:./images/redis/redis_image_02.png
[redis_image_03]:./images/redis/redis_image_03.png
[redis_image_04]:./images/redis/redis_image_04.png
[redis_image_05]:./images/redis/redis_image_05.png
[redis_image_06]:./images/redis/redis_image_06.png
[redis_image_07]:./images/redis/redis_image_07.png
[redis_image_08]:./images/redis/redis_image_08.png
[redis_image_09]:./images/redis/redis_image_09.png
[redis_image_10]:./images/redis/redis_image_10.png
[redis_image_11]:./images/redis/redis_image_11.png
[redis_image_12]:./images/redis/redis_image_12.png
[redis_image_13]:./images/redis/redis_image_13.png
[redis_image_14]:./images/redis/redis_image_14.png
[redis_image_15]:./images/redis/redis_image_15.png
[redis_image_16]:./images/redis/redis_image_16.png
[redis_image_17]:./images/redis/redis_image_17.png
[redis_image_18]:./images/redis/redis_image_18.png
[redis_image_19]:./images/redis/redis_image_19.png
[redis_image_20]:./images/redis/redis_image_20.png
[redis_image_21]:./images/redis/redis_image_21.png
[redis_image_22]:./images/redis/redis_image_22.png
[redis_image_23]:./images/redis/redis_image_23.png
[redis_image_24]:./images/redis/redis_image_24.png
[redis_image_25]:./images/redis/redis_image_25.png
[redis_image_26]:./images/redis/redis_image_26.png
[1]:./images/redis/redis_test1.PNG
[2]:./images/redis/redis_test2.PNG
[3]:./images/redis/redis_test3.PNG
[4]:./images/redis/redis_test4.PNG
[5]:./images/redis/redis_test5.PNG
[6]:./images/redis/redis_test6.PNG
[7]:./images/redis/redis_test7.PNG
[8]:./images/redis/redis_test8.PNG
[9]:./images/redis/redis_test9.PNG
[10]:./images/redis/redis_test10.PNG
[11]:./images/redis/redis_test11.PNG



### [Index](https://github.com/PaaS-TA/Guide-eng/blob/master/README.md) > [AP Install](../README.md) > Redis Service
