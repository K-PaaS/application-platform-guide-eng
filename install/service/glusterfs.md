### [Index](https://github.com/PaaS-TA/Guide-eng/blob/master/README.md) > [AP Install](../README.md) > GlusterFS Service

## Table of Contents  

1. [Document Outline](#1)  
  1.1. [Purpose](#1.1)  
  1.2. [Range](#1.2)  
  1.3. [References](#1.3)  
  
2. [GlusterFS Service Installation](#2)  
  2.1. [Prerequisite](#2.1)   
  2.2. [Stemcell Check](#2.2)    
  2.3. [Deployment Download](#2.3)   
  2.4. [Deployment File Monification](#2.4)  
  2.5. [Service Installation](#2.5)    
  2.6. [Service Installation Check](#2.6)  
  
3. [GlusterFS Linkage Sample App Description](#3)    
  3.1. [Service Broker Registration](#3.1)   
  3.2. [Sample App Download](#3.2)    
  3.3. [Apply for service in PaaS-TA](#3.3)   
  3.4. [Apply for service bind to Sample App and check for App](#3.4)   


## <div id="1"/> 1. Document Outline

### <div id="1.1"/>1.1. Purpose
This document (GlusterFS service pack installation guide) describes how to install the GlusterFS service pack, which is a service pack provided by PaaS-TA, using Bosch.

### <div id="1.2"/> 1.2. Range
The installation scope was prepared based on the basic installation to verify the GlusterFS service pack.


### <div id="1.3"/>1.3. References
BOSH Document: [http://bosh.io](http://bosh.io)  
Cloud Foundry Document: [https://docs.cloudfoundry.org](https://docs.cloudfoundry.org)  

## <div id="2"/>2. GlusterFS Service Installation

### <div id="2.1"/> 2.1. Prerequisite  

This installation guide is based on installing in a Linux environment.
In order to install the service pack, BOSH CLI v2 must be installed and logged in to BOSH. 
If BOSH CLI v2 is not installed, you should first refer to the BOSH 2.0 installation guide document to install BOSH CLI v2 and familiarize yourself with the usage.
A Swift & GlusterFS server must be installed to connect with the service broker.

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

# common_vars.yml File Download(Download if common_vars.yml doesn't exist)
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
- The verification glusterfs uses are: system_domain, paasta_admin_username, and  paasta_admin_password.

> $ vi ~/workspace/common/common_vars.yml
```
... ((Skip)) ...

system_domain: "61.252.53.246.nip.io"		# Domain (Same as HAProxy Public IP when using nip.io)
paasta_admin_username: "admin"			# PaaS-TA Admin Username
paasta_admin_password: "admin"			# PaaS-TA Admin Password

... ((Skip)) ...

```



- Modify the variable files used by Deployment YAML to suit the server environment.

> $ vi ~/workspace/service-deployment/glusterfs/vars.yml

```
# STEMCELL
stemcell_os: "ubuntu-bionic"                                     # stemcell os
stemcell_version: "1.76"                                       # stemcell version


# NETWORK
private_networks_name: "default"                                 # private network name
public_networks_name: "vip"                                      # public network name


# MYSQL
mysql_azs: [z4]                                                  # mysql azs
mysql_instances: 1                                               # mysql instances 
mysql_vm_type: "medium"                                          # mysql vm type
mysql_persistent_disk_type: "2GB"                                # mysql persistent disk type
mysql_port: 13306                                                # mysql port (e.g. 13306) -- Do Not Use "3306"
mysql_admin_username: "<MYSQL_ADMIN_USERNAME>"                   # mysql admin username (e.g. "root")
mysql_admin_password: "<MYSQL_ADMIN_PASSWORD>"                   # mysql admin password (e.g. "admin#1234" At least 8 digits mixed with English/number/special characters or at least 10 digits mixed with 2 types)


# GLUSTERFS SERVER
glusterfs_url: "<GLUSTERFS_PUBLIC_IP>"                           # Glusterfs Service public Address
glusterfs_tenantname: "<GLUSTERFS_TENANT_NAME>"                  # Glusterfs Service Tenant Name(e.g. "service")
glusterfs_username: "<GLUSTERFS_USERNAME>"                       # Glusterfs Service account ID(e.g. "swift")
glusterfs_password: "<GLUSTERFS_PASSWORD>"                       # Glusterfs Service Password(e.g. "password")
glusterfs_domainname: "<GLUSTERFS_DOMAIN_NAME>"                  # Glusterfs Service Domain Name(e.g. "default")
swiftproxy_port: "<SWIFT_PROXY_PORT>"                            # Glusterfs Service swift proxy port (e.g. "10008")
auth_port: "<AUTH_PORT>"                                         # Glusterfs Service auth port (e.g. "15001")

# GLUSTERFS_BROKER
broker_azs: [z4]                                                 # glusterfs broker azs
broker_instances: 1                                              # glusterfs broker instances 
broker_persistent_disk_type: "4GB"                               # glusterfs broker persistent disk type
broker_vm_type: "small"                                          # glusterfs broker vm type


# GLUSTERFS_BROKER_REGISTRAR
broker_registrar_azs: [z4]                                       # broker registrar azs
broker_registrar_instances: 1                                    # broker registrar instances 
broker_registrar_vm_type: "small"                                # broker registrar vm type


# GLUSTERFS_BROKER_DEREGISTRAR
broker_deregistrar_azs: [z4]                                     # broker deregistrar azs
broker_deregistrar_instances: 1                                  # broker deregistrar instances 
broker_deregistrar_vm_type: "small"                              # broker deregistrar vm type
```

### <div id="2.5"/> 2.5. Service Installation

- Modify the VARIABLES settings in the Deploy script file to suit the server environment, and select whether to add the option file.
     (Optional) -o operations/cce.yml (Apply CCE when installing)

> $ vi ~/workspace/service-deployment/glusterfs/deploy.sh

```
#!/bin/bash

# VARIABLES
COMMON_VARS_PATH="<COMMON_VARS_FILE_PATH>"    # common_vars.yml File Path (e.g. ../../common/common_vars.yml)
BOSH_ENVIRONMENT="${BOSH_ENVIRONMENT}"        # bosh director alias name (When not using create-bosh-login.sh provided by PaaS-TA, check the name at bosh envs and enter)

bosh -e ${BOSH_ENVIRONMENT} -n -d glusterfs deploy --no-redact glusterfs.yml \
    -o operations/cce.yml \
    -l ${COMMON_VARS_PATH} \
    -l vars.yml
```

- Install Service  
```
$ cd ~/workspace/service-deployment/glusterfs  
$ sh ./deploy.sh  
```  


### <div id="2.6"/> 2.6. Service Installation Check

Check the installed service.

> $ bosh -e micro-bosh -d glusterfs vms  

```
Using environment '10.0.1.6' as client 'admin'

Task 1343. Done

Deployment 'glusterfs'

Instance                                                      Process State  AZ  IPs          VM CID                                   VM Type  Active  
mysql/8770bc70-8681-4079-8360-086219d6231b                    running        z3  10.30.52.10  vm-96697221-0ff9-4520-8a68-2314c62057a5  medium   true  
paasta-glusterfs-broker/229fb890-645b-4213-89a1-fc2116de3f54  running        z3  10.30.52.11  vm-ace55b8f-3ce0-4482-b03b-96fbc567592e  medium   true  

2 vms

Succeeded
```

## <div id="3"/>3. GlusterFS Linkage Sample App Description
This Sample Web App is distributed to PaaS-TA and can be used with the service of GlusterFS on Provision and Bind.

### <div id="3.1"/> 3.1. Service Broker Registration  

When the GlusterFS service pack deployment is completed, the application must first register the GlusterFS service broker to use the service pack.
When registering a service broker, you must log in as a user who can register a service broker in PaaS-TA

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
[USERNAME] / [PASSWORD] : User ID / PASSWORD with access to service broker
[SERVICE_BROKER_URL] : Service Broker Access URL
```

- Register the GlusterFS service broker.
  
> $ cf create-service-broker glusterfs-service admin cloudfoundry http://<paasta-glusterfs-broker_ip>:8080
```  
$ cf create-service-broker glusterfs-service admin cloudfoundry http://10.30.52.11:8080
Creating service broker glusterfs-service as admin...
OK
```  

- Check the registered GlusterFS service broker.

> $ cf service-brokers   
```  
Getting service brokers as admin...

name                           url
glusterfs-service              http://10.30.52.11:8080
```  

- Check the list of accessible services.

>`$ cf service-access`  
```  
Getting service access as admin...
broker: glusterfs-service
   service     plan               access   orgs
   glusterfs   glusterfs-5Mb      none
   glusterfs   glusterfs-100Mb    none
   glusterfs   glusterfs-1000Mb   none
```  
>Access is initially not permitted when registering as a service broker. Therefore, access is set to none.

- Assign permission to a specific organization to access the service and recheck the access service list. (Overall Organization)

> $ cf enable-service-access glusterfs   
```
Enabling access to all plans of service glusterfs for all orgs as admin...
OK
```
> $ cf service-access   
```  
Getting service access as admin...
broker: glusterfs-service
   service     plan               access   orgs
   glusterfs   glusterfs-5Mb      all
   glusterfs   glusterfs-100Mb    all
   glusterfs   glusterfs-1000Mb   all
```  


### <div id='3.2'> 3.2. Sample App Download

- Download zip file of sample apps
```
$ wget https://nextcloud.paas-ta.org/index.php/s/NDgriPk5cgeLMfG/download --content-disposition  
$ unzip paasta-service-samples.zip  
$ cd paasta-service-samples/glusterfs  
```

<br>

### <div id="3.3"/> 3.3. Apply for service in PaaS-TA
In order to use the GlusterFS service in the Sample App, you must apply(provision) for a service.
*Note: When applying for a service, you must be logged in as a user who can apply for a service in PaaS-TA.

- Check whether there is a service in the PaaS-TA Marketplace First.

> $ cf marketplace

```  
Getting services from marketplace in org system / space dev as admin...
OK

service      plans                                              description
glusterfs    glusterfs-5Mb, glusterfs-100Mb, glusterfs-1000Mb   A simple glusterfs implementation 

TIP:  Use 'cf marketplace -s SERVICE' to view descriptions of individual plans of a given service.
```  

<br>
	
- Service Instance Application Commands
```
cf create-service [SERVICE] [PLAN] [SERVICE_INSTANCE]

[SERVICE] : Service name shown at the Marketplace
[PLAN] : Policies for Services
[SERVICE_INSTANCE] : Name of the service instance to create
```

- If there is a service you want on the Marketplace, apply for a service (Provision).

> $ cf create-service glusterfs glusterfs-1000Mb glusterfs-service-instance 
```  
Creating service instance glusterfs-service-instance in org system / space dev as admin...
OK
```  

<br>


- Verify the generated GlusterFS service instance.

> $ cf services
```  
Getting services in org system / space dev as admin...
OK

name                        service     plan                 bound apps            last operation
glusterfs-service-instance  glusterfs   glusterfs-1000Mb                           create succeeded
```  

<br>


### <div id='3.4'> 3.4. Apply for service bind to Sample App and check for App

When the service application is completed, the Sample Web App binds the generated service instance and uses the GlusterFS service in the App.
*Note: When applying for service bind, you must be logged in as a user who can apply for service bind on an open cloud platform.
  
- Check the manifest file. (Set swift_region to the region that you want to use on the GlusterFS server.)

> $ vi manifest.yml   
```
---
applications:
- name: hello-spring-glusterfs
  memory: 1G
  instances: 1
  path: hello-spring-glusterfs.war
  buildpacks:
  - java_buildpack
  env:
    swift_region: paasta
```

- Deploy app with --no-start opition.

> $ cf push --no-start 
```  
Applying manifest file /home/ubuntu/workspace/samples/paasta-service-samples/gluserfs/manifest.yml...
Manifest applied
Packaging files to upload...
Uploading files...
 17.06 MiB / 17.06 MiB [=================================================================================================

Waiting for API to complete processing files...

name:              hello-spring-glusterfs
requested state:   stopped
routes:            hello-spring-glusterfs.paasta.kr
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

> $ cf bind-service hello-spring-glusterfs glusterfs-service-instance

```
Binding service glusterfs-service-instance to app hello-spring-glusterfs in org system / space dev as admin...
OK
```

When running the app, add a security group for communication with the service.

- Modify rule.json.  
> $ vi rule.json   
```
## Set GlusterFS IP and PORT (swiftproxy_port, auth_port) to destination
[
  {
    "protocol": "tcp",
    "ports": "<auth_port>, <swiftproxy_port>",
    "destination": "<glusterfs_ip>",
    "log": true,
    "description": "Allow tcp traffic to gluster"
  }
]
```
  
- Create a security group.

> $ cf create-security-group glusterfs rule.json  

```
Creating security group glusterfs as admin...

OK		
```
  
- Apply the security group that you created to use the GlusterFS service.
> $ cf bind-running-security-group glusterfs  
```
Binding security group glusterFS to running as admin...
OK		
```
  
- Restart App for bind to take effect.

> $ cf restart hello-spring-glusterFS 

```	
Restarting app hello-spring-glusterFS in org system / space dev as admin...

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

name:              hello-spring-glusterFS
requested state:   started
routes:            hello-spring-glusterFS.paasta.kr
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


- Verify that the App uses the GlusterFS service normally.
	
![glusterfs_image_18]
<br>

![glusterfs_image_19]

[glusterfs_image_01]:./images/glusterfs/glusterfs_image_01.png

[glusterfs_image_07]:./images/glusterfs/glusterfs_image_07.png
[glusterfs_image_08]:./images/glusterfs/glusterfs_image_08.png
[glusterfs_image_09]:./images/glusterfs/glusterfs_image_09.png
[glusterfs_image_10]:./images/glusterfs/glusterfs_image_10.png
[glusterfs_image_11]:./images/glusterfs/glusterfs_image_11.png
[glusterfs_image_12]:./images/glusterfs/glusterfs_image_12.png
[glusterfs_image_13]:./images/glusterfs/glusterfs_image_13.png
[glusterfs_image_14]:./images/glusterfs/glusterfs_image_14.png
[glusterfs_image_15]:./images/glusterfs/glusterfs_image_15.png
[glusterfs_image_16]:./images/glusterfs/glusterfs_image_16.png
[glusterfs_image_17]:./images/glusterfs/glusterfs_image_17.jpeg
[glusterfs_image_18]:./images/glusterfs/glusterfs_image_18.png
[glusterfs_image_19]:./images/glusterfs/glusterfs_image_19.png


### [Index](https://github.com/PaaS-TA/Guide-eng/blob/master/README.md) > [AP Install](../README.md) > GlusterFS Service
