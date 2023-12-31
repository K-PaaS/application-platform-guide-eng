### [Index](https://github.com/K-PaaS/Guide-eng/blob/master/README.md) > [AP Install](../README.md) > MongoDB Service

## Table of Contents  

1. [Document Outline](#1)   
  1.1. [Purpose](#1.1)   
  1.2. [Range](#1.2)    
  1.3. [Refrences](#1.3)   
  
2. [Mongodb Service Installation](#2)  
  2.1. [Prerequisite](#2.1)   
  2.2. [Stemcell Check](#2.2)    
  2.3. [Deployment Download](#2.3)   
  2.4. [Deployment File Modification](#2.4)  
  2.5. [Service Installation](#2.5)    
  2.6. [Service Installation Check](#2.6)  
  
3. [Mongodb Linkage Sample Web App Description](#3)  
  3.1. [Mongodb Service Broker Registration](#3.1)  
  3.2. [Sample App Download](#3.2)  
  3.3. [Request for service in K-PaaS](#3.3)  
  3.4. [Request for service bind to Sample App and check for App](#3.4)   


## <div id='1'> 1. Document Outline
### <div id='1.1'> 1.1. Purpose

This document (Mongodb service pack installation guide) describes how to install Mongodb service pack, which is a service pack provided by K-PaaS, using Bosh.

### <div id='1.2'> 1.2. Range
The installation range was prepared based on the basic installation to verify the Mongodb service pack.

### <div id='1.3'> 1.3. References
BOSH Document: [http://bosh.io](http://bosh.io)  
Cloud Foundry Document: [https://docs.cloudfoundry.org](https://docs.cloudfoundry.org)  


## <div id='2'> 2.  Mongodb Service Insatatllation

### <div id="2.1"/> 2.1. Prerequisite  

This installation guide is based on installing in a Linux environment.
In order to install the service pack, BOSH CLI v2 must be installed and logged in to BOSH.
If BOSH CLI v2 is not installed, you should first refer to the BOSH 2.0 installation guide document to install BOSH CLI v2 and familiarize yourself with the usage.

### <div id="2.2"/> 2.2. Stemcell Check

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

- Service Deployment Git Repository URL : https://github.com/K-PaaS/service-deployment/tree/v5.1.25.1

```
# Deployment File Download , make directory, change directory
$ mkdir -p ~/workspace
$ cd ~/workspace

# Deployment File Download
$ git clone https://github.com/K-PaaS/service-deployment.git -b v5.1.25.1

# Download common_vars.yml file (download if common_vars.yml does not exist)
$ git clone https://github.com/K-PaaS/common.git
```

### <div id="2.4"/> 2.4. Deployment File Modification

The BOSH Deployment manifest is a YAML file that defines the properties of components elements and deployments.
Cloud config is used for network, vm_type, and disk_type used in Deployment files, and refer to the K-PaaS AP installation guide for the usage.

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
      security_groups: ap-security-group
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
- Variables used in MongoDB are: system_domain, ap_admin_username, ap_admin_password, ap_nats_ip, ap_nats_port, ap_nats_user, and ap_nats_password.

> $ vi ~/workspace/common/common_vars.yml
```
... ((Skip)) ...

system_domain: "61.252.53.246.nip.io"		# Domain (Same as HAProxy Public IP when using nip.io)
ap_admin_username: "admin"		        	# Application Platform Admin Username
ap_admin_password: "admin"	        		# Application Platform Admin Password
ap_nats_ip: "10.0.1.121"
ap_nats_port: 4222
ap_nats_user: "nats"
ap_nats_password: "7EZB5ZkMLMqT73h2Jh3UsqO"	# Application Platform Nats Password (After logging in to CredHub, check with the command 'credhub get -n /micro-bosh/ap/nats_password')

... ((Skip)) ...

```

- Modify the variable files used by Deployment YAML to suit the server environment.

> $ vi ~/workspace/service-deployment/mongodb/vars.yml
```
# STEMCELL
stemcell_os: "ubuntu-jammy"                                     # stemcell os
stemcell_version: "1.181"                                         # stemcell version

# NETWORK
private_networks_name: "default"                                 # private network name

# MONGODB_REPL_SET_NAME
replSetName1: "op1"                                              # replica set1 name
replSetName2: "op2"                                              # replica set2 name
replSetName3: "op3"                                              # replica set3 name : use to operations/add-replica-set.yml

# MONGODB_SLAVE1
mongodb_slave1_azs: [z3]                                         # mongodb slave1 azs
mongodb_slave1_instances: 2                                      # mongodb slave1 instances
mongodb_slave1_vm_type: "medium"                                 # mongodb slave1 vm type
mongodb_slave1_persistent_disk_type: "10GB"                      # mongodb slave1 persistent disk type
mongodb_slave1_static_ips: "<MONGODB_SLAVE1_PRIVATE_IPS>"        # mongodb slave1's private IPs (e.g. ["10.0.81.11","10.0.81.12"])

# MONGODB_SLAVE2
mongodb_slave2_azs: [z3]                                         # mongodb slave2 azs
mongodb_slave2_instances: 2                                      # mongodb slave2 instances
mongodb_slave2_vm_type: "medium"                                 # mongodb slave2 vm type
mongodb_slave2_persistent_disk_type: "10GB"                      # mongodb slave2 persistent disk type
mongodb_slave2_static_ips: "<MONGODB_SLAVE2_PRIVATE_IPS>"        # mongodb slave2's private IPs (e.g. ["10.0.81.14","10.0.81.15"])

# MONGODB_SLAVE3 : use to operations/add-replica-set.yml
mongodb_slave3_azs: [z3]                                         # mongodb slave3 azs
mongodb_slave3_instances: 2                                      # mongodb slave3 instances
mongodb_slave3_vm_type: "medium"                                 # mongodb slave3 vm type
mongodb_slave3_persistent_disk_type: "10GB"                      # mongodb slave3 persistent disk type
mongodb_slave3_static_ips: "<MONGODB_SLAVE3_PRIVATE_IPS>"        # mongodb slave3's private IPs (e.g. ["10.0.81.17","10.0.81.18"])

# MONGODB_MASTER1
mongodb_master1_azs: [z3]                                                # mongodb master1 azs
mongodb_master1_instances: 1                                             # mongodb master1 instances
mongodb_master1_vm_type: "medium"                                        # mongodb master1 vm type
mongodb_master1_persistent_disk_type: "10GB"                             # mongodb master1 persistent disk type
mongodb_master1_static_ips: "<MONGODB_MASTER1_PRIVATE_IP>"               # mongodb master1's private IP (e.g. "10.0.81.10")
mongodb_master1_replSet_hosts: "<MONGODB_MASTER1_REPLSET_HOSTS>"         # The first host is the master1 ip of replicaSet1,  followed by the IPs of slave1. (e.g. ["10.0.81.10", "10.0.81.11","10.0.81.12"])

# MONGODB_MASTER2
mongodb_master2_azs: [z3]                                                # mongodb master2 azs
mongodb_master2_instances: 1                                             # mongodb master2 instances

```

'pem.yml' does not modify the contents because MongoDB's own pem is registered and used.

### <div id="2.5"/> 2.5. Service Installation

- Modify the VARIABLES settings in the Deploy script file to suit the server environment, and decide whether to add the option file.  
	(optional) -o operations/cce.yml (Apply CCE when installing)


> $ vi ~/workspace/service-deployment/mongodb/deploy.sh

```
#!/bin/bash

# VARIABLES
COMMON_VARS_PATH="<COMMON_VARS_FILE_PATH>"  # common_vars.yml File Path (e.g. ../../common/common_vars.yml)
BOSH_ENVIRONMENT="${BOSH_ENVIRONMENT}"      # bosh director alias name (Create-bosh-login.sh provided by K-PaaS. If is not in use, check the name in bosh envs and enter)

# DEPLOY
bosh -e ${BOSH_ENVIRONMENT} -n -d mongodb deploy --no-redact mongodb.yml \
    -o operations/cce.yml \
    -l ${COMMON_VARS_PATH} \
    -l vars.yml \
    -l operations/pem.yml
```

- Install Service. 
```
$ cd ~/workspace/service-deployment/mongodb  
$ sh ./deploy.sh  
```  

### <div id="2.6"/> 2.6. Service Installation Check

Check the installed service.

> $ bosh -e micro-bosh -d mongodb vms  

```
Using environment '10.0.1.6' as client 'admin'

Task 8176. Done

Deployment 'mongodb'

Instance                                              Process State  AZ  IPs            VM CID                                   VM Type  Active  
mongodb_broker/0e8933f1-1b67-4486-b37a-2b104da1351a   running        z5  10.30.107.114  vm-e0bb79c6-6482-497a-b071-f7df4bf2a059  minimal  true  
mongodb_config/35ee66e6-9c25-44c2-85a4-e7c1d520641b   running        z5  10.30.107.111  vm-672ce5b9-4d8f-4b22-9745-43f7d9e39402  minimal  true  
mongodb_config/935aed3c-e7a4-4179-b397-68d0535bc1d9   running        z5  10.30.107.112  vm-8069a84b-5a91-44ca-a5d8-cca37b5d8952  minimal  true  
mongodb_master1/1e8b971e-c503-4ba6-bcba-ab28dd7dd797  running        z5  10.30.107.101  vm-54b33ec2-582d-44ef-a4bf-6281acfbf81b  minimal  true  
mongodb_master2/7a4460e4-a9b5-4d15-9508-adba3405f387  running        z5  10.30.107.104  vm-a388a44e-4ab9-4340-9227-b12b7bd2c410  minimal  true  
mongodb_shard/1fd85812-c8d4-4ebd-98f5-c8cf637db9e5    running        z5  10.30.107.113  vm-c2628ba8-feed-4401-b1c9-be1445722d34  minimal  true  
mongodb_slave1/2710c368-dbc2-4d72-a100-1fa37d73e2ec   running        z5  10.30.107.102  vm-048757cf-1c19-4c30-a3cd-2b0dd05c1554  minimal  true  
mongodb_slave1/bb6275f1-4ab5-4998-ba89-ef30c36c3f67   running        z5  10.30.107.103  vm-6d0f52ef-a0b3-4c26-8e04-cb5cef30337d  minimal  true  
mongodb_slave2/9671e09b-7ca1-4da2-af8a-88d20caeebfe   running        z5  10.30.107.106  vm-8a57713b-68df-4639-8ab3-3d12c01fd880  minimal  true  
mongodb_slave2/fed23144-9c18-42f6-9f99-213f7dc294ee   running        z5  10.30.107.105  vm-c58e860a-8b5e-43e1-abe9-c3043cbfb16d  minimal  true  

10 vms

Succeeded
```

## <div id='3'> 3. Mongodb Linkage Sample Web App Description

This Sample Web App is deployed to K-PaaS AP and can be used with Mongodb's service on Provision and Bind.

### <div id='3.1'> 3.1. Mongodb Service Broker Registration

When Mongodb service pack deployment is completed, you must first register the Mongodb service broker to use the service pack in the application.
When registering a service broker, you must be logged in as a user who can register a service broker on an open cloud platform.

- Check the list of service brokers.

> $ cf service-brokers  

```
Getting service brokers as admin...

No service brokers found
```

<br>

- Command for registering Service Broker
```
cf create-service-broker [SERVICE_BROKER] [USERNAME] [PASSWORD] [SERVICE_BROKER_URL]

[SERVICE_BROKER] : Service Broker Name
[USERNAME] / [PASSWORD] : User ID / PASSWORD with access to service broker
[SERVICE_BROKER_URL] : Service Broker Access URL
```
	
- Register the Mongodb service broker.

> $ cf create-service-broker mongodb-shard-service-broker admin cloudfoundry http://<mongodb_broker_ip>:8080  

```
$ cf create-service-broker mongodb-shard-service-broker admin cloudfoundry http://10.30.107.114:8080
Creating service broker mongodb-shard-service-broker as admin...
OK
```


- Check the registered mongodb service broker.

> $ cf service-brokers

```
Getting service brokers as admin...
name                           url
mongodb-shard-service-broker   http://10.30.107.114:8080
```


- Check the list of accessible services.

> $ cf service-access  
```
Getting service access as admin...

broker: mongodb-shard-service-broker
   offering   plan           access   orgs
   Mongo-DB   default-plan   none      
```

>Default access is not allowed when creating a service broker.


- Assign permission to a specific organization to access the service and recheck the access service list. (Overall Organization)

> $ cf enable-service-access Mongo-DB <br>
```
Enabling access to all plans of service offering Mongo-DB for all orgs as admin...
OK
```
> $ cf service-access

```
Getting service access as admin...

broker: mongodb-shard-service-broker
   offering   plan           access   orgs
   Mongo-DB   default-plan   all      
```

### <div id='3.2'> 3.2. Sample App Download

Sample Web App is deployed as an App to K-PaaS AP. When the app is deployed and operated, initial data is generated by accessing the bound Mongodb service connection information.
When the app runs normally after deployment is completed, access the app through a browser or curl to display Mongodb environment information (service connection information) and initial loaded data.

- Download zip file of sample apps
```
$ wget https://nextcloud.k-paas.org/index.php/s/scFDGk9iZBg8apZ/download --content-disposition  
$ unzip ap-service-samples-db49d1e.zip  
$ cd ap-service-samples/mongodb  
```

<br>

### <div id='3.3'> 3.3. Request for service in K-PaaS AP

In order to use the Mongodb service in the Sample Web App, you must request for a service (Provision).
*Note: When requesting for a service, you must be logged in as a user who can request for a service on an open cloud platform.


- Check whether there is a service in the K-PaaS Marketplace first.

> $ cf marketplace

```  
Getting all service offerings from marketplace in org system / space dev as admin...

offering   plans          description                     broker
Mongo-DB   default-plan   A simple mongo implementation   mongodb-shard-service-broker

TIP:  Use 'cf marketplace -e SERVICE_OFFERING' to view descriptions of individual plans of a given service offering.
```  

<br>

- Command for requesting Service Instance
```
cf create-service [SERVICE] [PLAN] [SERVICE_INSTANCE]

[SERVICE] : Service Name Shown in Marketplace
[PLAN] : Policies for Services
[SERVICE_INSTANCE] : Name of the service instance to create
```

- If there is a service you want on the Marketplace, request for a service (Provision).

> $ cf create-service Mongo-DB default-plan mongodb-service-instance 
```  
Creating service instance mongodb-service-instance in org system / space dev as admin...

Service instance mongodb-service-instance created.
OK
```  

<br>

- Check the generated Mongodb service instance.

> $ cf services 
```  
Getting service instances in org system / space dev as admin...

name                       offering   plan           bound apps   last operation     broker                         upgrade available
mongodb-service-instance   Mongo-DB   default-plan                create succeeded   mongodb-shard-service-broker   no
```  

<br>

### <div id='3.4'> 3.4. Request for service bind to Sample App and check for App  

When the service application is completed, the Sample Web App binds the generated service instance and uses the Mongodb service in the App.
*Note: When requesting for service bind, you must be logged in as a user who can request for service bind on an open cloud platform.
  
- Check the manifest file.  

> $ vi manifest.yml   

```
---
applications:
- name: hello-spring-mongodb
  memory: 1G
  instances: 1
  path: hello-spring-mongodb.war
```

- Deploy the app with --no-start option.  
> $ cf push --no-start 
```  
Pushing app hello-spring-mongodb to org system / space dev as admin...
Applying manifest file /home/ubuntu/workspace/samples/ap-service-samples/mongodb/manifest.yml...
Updating with these attributes...
  ---
  applications:
+ - name: hello-spring-mongodb
+   instances: 1
    path: /home/ubuntu/workspace/samples/ap-service-samples/mongodb/hello-spring-mongodb.war
    memory: 1G
+   default-route: true
Manifest applied
Packaging files to upload...
Uploading files...
 17.16 MiB / 17.16 MiB [========================================================================================================] 100.00% 1s

Waiting for API to complete processing files...

name:              hello-spring-mongodb
requested state:   stopped
routes:            hello-spring-mongodb.ap.kr
last uploaded:     
stack:             
buildpacks:        

type:           web
sidecars:       
instances:      0/1
memory usage:   1024M
     state   since                  cpu    memory   disk     details
#0   down    2023-10-11T04:25:42Z   0.0%   0 of 0   0 of 0   
```  
  
- Request for service instance bind created by Sample Web App.

> $ cf bind-service hello-spring-Mongodb mongodb-service-instance 

```
Binding service instance mongodb-service-instance to app hello-spring-Mongodb in org system / space dev as admin...
OK
```

Add a security group for communication with the service when running the app.

- Modify rule.json.
> $ vi rule.json   
```
## Set mongodb_shard IP of mongodb to destination
[
  {
    "protocol": "tcp",
    "destination": "<mongodb_shard_ip>",
    "ports": "27017"
  }
]
```
  
- Create a security group.

> $ cf create-security-group mongodb rule.json  

```
Creating security group mongodb as admin...

OK		
```
  
- Apply the security group created to use the Mongodb service.
> $ cf bind-running-security-group mongodb  
```
Binding security group mongodb to running as admin...
OK		
```
  
- Restart the App to apply the bind.

> $ cf restart hello-spring-mongodb 

```	
Restarting app hello-spring-mongodb in org system / space dev as admin...

Staging app and tracing logs...
   Downloading binary_buildpack...
   Downloading ruby_buildpack...
   Downloading java_buildpack...
   Downloading dotnet_core_buildpack...

........
........
Instances starting...
Instances starting...

name:              hello-spring-mongodb
requested state:   started
routes:            hello-spring-mongodb.ap.kr
last uploaded:     Wed 11 Oct 13:28:49 KST 2023
stack:             cflinuxfs3
buildpacks:        
	name             version                                                         detect output   buildpack name
	java_buildpack   v4.50-git@github.com:cloudfoundry/java-buildpack.git#5fe41f89   java            java

type:           web
sidecars:       
instances:      1/1
memory usage:   1024M
     state     since                  cpu    memory    disk      details
#0   running   2023-10-11T04:29:04Z   0.0%   0 of 1G   0 of 1G   

type:           task
sidecars:       
instances:      0/0
memory usage:   1024M
There are no running instances of this process.

```  


- Check if the app uses Mongodb service normally.

![mongodb_image_23]



[mongodb_image_23]:./images/mongodb/mongodb_image_23.png



### [Index](https://github.com/K-PaaS/Guide-eng/blob/master/README.md) > [AP Install](../README.md) > MongoDB Service
