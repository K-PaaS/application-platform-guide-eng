### [Index](https://github.com/PaaS-TA/Guide-eng/blob/master/README.md) > [AP Install](../README.md) > MongoDB Service

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
  
3. [Mongodb Linkage Sample Web App Description(#3)  
  3.1. [Mongodb Service Broker Registration](#3.1)  
  3.2. [Sample App Download](#3.2)  
  3.3. [Apply for service in PaaS-TA](#3.3)  
  3.4. [Apply for service bind to Sample App and check for App](#3.4)   


## <div id='1'> 1. Document Outline
### <div id='1.1'> 1.1. Purpose

This document (Mongodb service pack installation guide) describes how to install Mongodb service pack, which is a service pack provided by PaaS-TA, using Bosh.

### <div id='1.2'> 1.2. Range
The installation scope was prepared based on the basic installation to verify the Mongodb service pack.

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
The Stemcell of this guide uses ubuntu-bionic 1.76.

> $ bosh -e ${BOSH_ENVIRONMENT} stemcells

```
Using environment '10.0.1.6' as client 'admin'

Name                                       Version   OS             CPI  CID  
bosh-openstack-kvm-ubuntu-bionic-go_agent  1.76      ubuntu-bionic  -    ce507ae4-aca6-4a6d-b7c7-220e3f4aaa7d

(*) Currently deployed

1 stemcells

Succeeded
```

If the corresponding Stemcell is not uploaded, copy the corresponding Stemcell link to the corresponding IaaS environment and version from [bosh.io Stemcell] and run the following command:

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

# Download common_vars.yml file (download if common_vars.yml does not exist)
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
- Variables used in MongoDB are: system_domain, paasta_admin_username, paasta_admin_password, paasta_nats_ip, paasta_nats_port, paasta_nats_user, and	paasta_nats_password.

> $ vi ~/workspace/common/common_vars.yml
```
... ((Skip)) ...

system_domain: "61.252.53.246.nip.io"		# Domain (Same as HAProxy Public IP when using nip.io)
paasta_admin_username: "admin"			# PaaS-TA Admin Username
paasta_admin_password: "admin"			# PaaS-TA Admin Password
paasta_nats_ip: "10.0.1.121"
paasta_nats_port: 4222
paasta_nats_user: "nats"
paasta_nats_password: "7EZB5ZkMLMqT73h2Jh3UsqO"	# PaaS-TA Nats Password (After logging in to CredHub, check with the command 'credhub get -n /micro-bosh/paasta/nats_password')

... ((Skip)) ...

```

- Modify the variable files used by Deployment YAML to suit the server environment.

> $ vi ~/workspace/service-deployment/mongodb/vars.yml
```
# STEMCELL
stemcell_os: "ubuntu-bionic"                                     # stemcell os
stemcell_version: "1.76"                                         # stemcell version

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
mongodb_master2_vm_type: "medium"                                        # mongodb master2 vm type
mongodb_master2_persistent_disk_type: "10GB"                             # mongodb master2 persistent disk type
mongodb_master2_static_ips: "<MONGODB_MASTER2_PRIVATE_IP>"               # mongodb master2's private IP (e.g. "10.0.81.13")
mongodb_master2_replSet_hosts: "<MONGODB_MASTER2_REPLSET_HOSTS>"         # The first host is the master2 ip of replicaSet2,  followed by the IPs of slave2. (e.g. ["10.0.81.13", "10.0.81.14","10.0.81.15"])

# MONGODB_MASTER3 : use to operations/add-replica-set.yml
mongodb_master3_azs: [z3]                                                # mongodb master3 azs
mongodb_master3_instances: 1                                             # mongodb master3 instances
mongodb_master3_vm_type: "medium"                                        # mongodb master3 vm type
mongodb_master3_persistent_disk_type: "10GB"                             # mongodb master3 persistent disk type
mongodb_master3_static_ips: "<MONGODB_MASTER3_PRIVATE_IP>"               # mongodb master3's private IP (e.g. "10.0.81.16")
mongodb_master3_replSet_hosts: "<MONGODB_MASTER3_REPLSET_HOSTS>"         # The first host is the master3 ip of replicaSet3,  followed by the IPs of slave3. (e.g. ["10.0.81.16", "10.0.81.17","10.0.81.18"])

# MONGODB_CONFIG
mongodb_config_azs: [z3]                                                 # mongodb config azs
mongodb_config_instances: 2                                              # mongodb config instances : less than 3 instances
mongodb_config_vm_type: "medium"                                         # mongodb config vm type
mongodb_config_persistent_disk_type: "10GB"                              # mongodb config persistent disk type
mongodb_config_static_ips: "<MONGODB_CONFIG_PRIVATE_IPS>"                # mongodb config's private IPs (e.g. ["10.0.81.19", "10.0.81.20"])

# MONGODB_SHARD
mongodb_shard_azs: [z3]                                                  # mongodb shard azs
mongodb_shard_instances: 1                                               # mongodb shard instances
mongodb_shard_vm_type: "medium"                                          # mongodb shard vm type
mongodb_shard_static_ips: "<MONGODB_SHARD_PRIVATE_IP>"                   # mongodb shard's private IP (e.g. "10.0.81.21")

# MONGODB_BROKER
mongodb_broker_azs: [z3]                                                 # mongodb broker azs
mongodb_broker_instances: 1                                              # mongodb broker instances
mongodb_broker_vm_type: "medium"                                         # mongodb broker vm type
mongodb_broker_static_ips: "<MONGODB_BROKER_PRIVATE_IP>"                 # mongodb broker's private IP (e.g. "10.0.81.22")

# BROKER_REGISTRAR
broker_registrar_broker_azs: [z3]                                        # broker registrar azs
broker_registrar_broker_instances: 1                                     # broker registrar instances
broker_registrar_broker_vm_type: "medium"                                # broker registrar vm type

# BROKER_DEREGISTRAR
broker_deregistrar_broker_azs: [z3]                                      # broker deregistrar azs
broker_deregistrar_broker_instances: 1                                   # broker deregistrar instances
broker_deregistrar_broker_vm_type: "medium"                              # broker deregistrar vm type
```

'pem.yml' does not modify the contents because MongoDB's own pem is registered and used.

### <div id="2.5"/> 2.5. Service Installation

- Modify the VARIABLES settings in the Deploy script file to suit the server environment, and select whether to add the option file.
     (optional) -o operations/cce.yml (Apply CCE when installing)


> $ vi ~/workspace/service-deployment/mongodb/deploy.sh

```
#!/bin/bash

# VARIABLES
COMMON_VARS_PATH="<COMMON_VARS_FILE_PATH>"  # common_vars.yml File Path (e.g. ../../common/common_vars.yml)
BOSH_ENVIRONMENT="${BOSH_ENVIRONMENT}"      # bosh director alias name (Create-bosh-login.sh provided by PaaS-TA. If is not in use, check the name in bosh envs and enter)

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

This Sample Web App is distributed to PaaS-TA and can be used with Mongodb's service on Provision and Bind.

### <div id='3.1'> 3.1. Mongodb Service Broker Registration

When Mongodb service pack distribution is completed, the application must first register the Mongodb service broker to use the service pack.
When registering a service broker, you must be logged in as a user who can register a service broker on an open cloud platform.

- Check the list of service brokers.

> $ cf service-brokers  

```
Getting service brokers as admin...

name   url
No service brokers found
```

<br>

- Service Broker Registering Command
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

Sample Web App is distributed as an App to PaaS-TA. When the app is distributed and operated, initial data is generated by accessing the bound Mongodb service connection information.
When the app runs normally after distribution is completed, access the app through a browser or curl to display Mongodb environment information (service connection information) and initial loaded data.

- Download zip file of sample apps
```
$ wget https://nextcloud.paas-ta.org/index.php/s/NDgriPk5cgeLMfG/download --content-disposition  
$ unzip paasta-service-samples.zip  
$ cd paasta-service-samples/mongodb  
```

<br>

### <div id='3.3'> 3.3. Apply for service in PaaS-TA

In order to use the Mongodb service in the Sample Web App, you must apply for a service (Provision).
*Note: When applying for a service, you must be logged in as a user who can apply for a service on an open cloud platform.


- Check whether there is a service in the PaaS-TA Marketplace first.

> $ cf marketplace

```  
Getting services from marketplace in org system / space dev as admin...
OK

service      plans          description
Mongo-DB     default-plan   A simple mongo implementation

TIP:  Use 'cf marketplace -s SERVICE' to view descriptions of individual plans of a given service.
```  

<br>

- Service Instance Application Commands
```
cf create-service [SERVICE] [PLAN] [SERVICE_INSTANCE]

[SERVICE] : Service Name Shown in Marketplace
[PLAN] : Policies for Services
[SERVICE_INSTANCE] : Name of the service instance to create
```

- If there is a service you want on the Marketplace, apply for a service (Provision).

> $ cf create-service Mongo-DB default-plan mongodb-service-instance 
```  
Creating service instance mongodb-service-instance in org system / space dev as admin...
OK
```  

<br>

- Check the generated Mongodb service instance.

> $ cf services 
```  
Getting services in org system / space dev as admin...
OK

name                      service    plan                 bound apps            last operation
mongodb-service-instance  Mongo-DB   default-plan                               create succeeded
```  

<br>

### <div id='3.4'> 3.4. Apply for service bind to Sample App and check for App  

When the service application is completed, the Sample Web App binds the generated service instance and uses the Mongodb service in the App.
*Note: When applying for service bind, you must be logged in as a user who can apply for service bind on an open cloud platform.
  
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
Applying manifest file /home/ubuntu/workspace/samples/paasta-service-samples/mongodb/manifest.yml...
Manifest applied
Packaging files to upload...
Uploading files...
 17.06 MiB / 17.06 MiB [=================================================================================================

Waiting for API to complete processing files...

name:              hello-spring-mongodb
requested state:   stopped
routes:            hello-spring-mongodb.paasta.kr
last uploaded:     
stack:             
buildpacks:        

type:           web
sidecars:       
instances:      0/1
memory usage:   1024M
     state   since                  cpu    memory   disk     details
#0   down    2021-11-22T05:13:12Z   0.0%   0 of 0   0 of 0   
```  
  
- Apply for service instance bind created by Sample Web App.

> $ cf bind-service hello-spring-Mongodb mongodb-service-instance 

```
Binding service mongodb-service-instance to app hello-spring-Mongodb in org system / space dev as admin...
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
   Downloading nodejs_buildpack...
   Downloading php_buildpack...
   Downloading nginx_buildpack...

........
........
Instances starting...
Instances starting...

name:              hello-spring-mongodb
requested state:   started
routes:            hello-spring-mongodb.paasta.kr
last uploaded:     Mon 22 Nov 05:19:59 UTC 2021
stack:             cflinuxfs3
buildpacks:        
	name             version                                                             detect output   buildpack na
	java_buildpack   v4.37-https://github.com/cloudfoundry/java-buildpack.git#ab2b4512   java            java

type:           web
sidecars:       
instances:      1/1
memory usage:   1024M
     state     since                  cpu    memory    disk       details
#0   running   2021-11-22T05:20:19Z   0.0%   0 of 1G   8K of 1G   

```  


- Check if the app uses Mongodb service normally.

![mongodb_image_23]



[mongodb_image_01]:./images/mongodb/mongodb_image_01.png
[mongodb_image_02]:./images/mongodb/mongodb_image_02.png
[mongodb_image_03]:./images/mongodb/mongodb_image_03.png
[mongodb_image_04]:./images/mongodb/mongodb_image_04.png
[mongodb_image_05]:./images/mongodb/mongodb_image_05.png
[mongodb_image_06]:./images/mongodb/mongodb_image_06.png
[mongodb_image_07]:./images/mongodb/mongodb_image_07.png
[mongodb_image_08]:./images/mongodb/mongodb_image_08.png
[mongodb_image_09]:./images/mongodb/mongodb_image_09.png
[mongodb_image_10]:./images/mongodb/mongodb_image_10.png
[mongodb_image_11]:./images/mongodb/mongodb_image_11.png
[mongodb_image_12]:./images/mongodb/mongodb_image_12.png
[mongodb_image_13]:./images/mongodb/mongodb_image_13.png
[mongodb_image_14]:./images/mongodb/mongodb_image_14.png
[mongodb_image_15]:./images/mongodb/mongodb_image_15.png
[mongodb_image_16]:./images/mongodb/mongodb_image_16.png
[mongodb_image_17]:./images/mongodb/mongodb_image_17.png
[mongodb_image_18]:./images/mongodb/mongodb_image_18.png
[mongodb_image_19]:./images/mongodb/mongodb_image_19.png
[mongodb_image_20]:./images/mongodb/mongodb_image_20.png
[mongodb_image_21]:./images/mongodb/mongodb_image_21.png
[mongodb_image_22]:./images/mongodb/mongodb_image_22.png
[mongodb_image_23]:./images/mongodb/mongodb_image_23.png
[mongodb_image_24]:./images/mongodb/mongodb_image_24.png
[mongodb_image_25]:./images/mongodb/mongodb_image_25.png
[mongodb_image_26]:./images/mongodb/mongodb_image_26.png
[mongodb_image_27]:./images/mongodb/mongodb_image_27.png
[mongodb_image_28]:./images/mongodb/mongodb_image_28.png
[mongodb_image_29]:./images/mongodb/mongodb_image_29.png
[mongodb_image_30]:./images/mongodb/mongodb_image_30.png
[mongodb_image_31]:./images/mongodb/mongodb_image_31.png
[mongodb_image_32]:./images/mongodb/mongodb_image_32.png
[mongodb_image_33]:./images/mongodb/mongodb_image_33.png
[mongodb_image_34]:./images/mongodb/mongodb_image_34.png
[mongodb_image_35]:./images/mongodb/mongodb_image_35.png
[mongodb_image_36]:./images/mongodb/mongodb_image_36.png
[mongodb_image_37]:./images/mongodb/mongodb_image_37.png
[mongodb_image_38]:./images/mongodb/mongodb_image_38.png
[mongodb_image_39]:./images/mongodb/mongodb_image_39.png
[mongodb_image_40]:./images/mongodb/mongodb_image_40.png
[mongodb_image_41]:./images/mongodb/mongodb_image_41.png
[mongodb_image_42]:./images/mongodb/mongodb_image_42.png


### [Index](https://github.com/PaaS-TA/Guide-eng/blob/master/README.md) > [AP Install](../README.md) > MongoDB Service
