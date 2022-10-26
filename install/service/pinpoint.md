### [Index](https://github.com/PaaS-TA/Guide-eng/blob/master/README.md) > [AP Install](../README.md) > Pinpoint APM Service

## Table of Contents  

1. [Document Outline](#1)  
  1.1. [Purpose](#1.1)   
  1.2. [Range](#1.2)   
  1.3. [References](#1.3)  
  
2. [Pinpoint Service Installation](#2)  
  2.1. [Prerequisite](#2.1)   
  2.2. [Stemcell Check](#2.2)    
  2.3. [Deployment Download](#2.3)   
  2.4. [Deployment File Modification](#2.4)  
  2.5. [Service Installation](#2.5)    
  2.6. [Service Installation Check](#2.6)  
 
3. [Sample Web App Interworking Pinpoint](#3)    
  3.1. [Pinpoint Service Broker Registration](#3.1)  
  3.2. [Sample Web App Structure](#3.2)   
  3.3. [Request for service in PaaS-TA](#3.3)  
  3.4. [Request for service bind to Sample Web App and check for App](#3.4)  

## <div id='1'> 1. Document Outline
### <div id='1.1'> 1.1. Purpose

This document (Pinpoint Service Pack Installation Guide) describes how to install Pinpoint Service Pack, a service pack provided by PaaS-TA, using Bosch.

### <div id='1.2'> 1.2. Range
The installation Range was prepared based on the basic installation to verify the Pinpoint service pack.


### <div id='1.3'> 1.3. References
BOSH Document: [http://bosh.io](http://bosh.io)  
Cloud Foundry Document: [https://docs.cloudfoundry.org](https://docs.cloudfoundry.org)  

## <div id='2'> 2. Pinpoint Service Installation

### <div id="2.1"/> 2.1. Prerequisite  

This installation guide is based on installing in a Linux environment.  
To install the service pack, BOSH CLI v2 must be installed and logged in to BOSH.  
If BOSH CLI v2 is not installed, you should first refer to the BOSH 2.0 installation guide document to install BOSH CLI v2 and familiarize the usage.  

- Check the bosh runtime-config to see if there are pinpoints in the bosh-dns include deployments.  
 ※ (Note) If there is no pinpoint in bosh-dns include deployments, open the dns.yml in ~/workspace/paasta-deployment/bosh/runtime-configs to add pinpoint and update the bosh runtime-config.   

> $ bosh -e micro-bosh runtime-config
```
Using environment '10.0.1.6' as client 'admin'

---
addons:
- include:
    deployments:
    - paasta
    - pinpoint
    - pinpoint-monitoring
    - rabbitmq
    stemcell:
    - os: ubuntu-trusty
    - os: ubuntu-xenial
    - os: ubuntu-bionic
  jobs:
  - name: bosh-dns
    properties:
      api:
        client:
          tls: "((/dns_api_client_tls))"
        server:
          tls: "((/dns_api_server_tls))"
      cache:
        enabled: true
      health:
        client:
          tls: "((/dns_healthcheck_client_tls))"
        enabled: true
        server:
          tls: "((/dns_healthcheck_server_tls))"
    release: bosh-dns
  name: bosh-dns
...(Skip)...

Succeeded
```

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

If the corresponding Stemcell is not uploaded, copy the Stemcell link to the corresponding IaaS environment and version from [bosh.io Stemcell](https://bosh.io/stemcells/) and run the following command.

```
# Example of Stemcell Upload Command
$ bosh -e ${BOSH_ENVIRONMENT} upload-stemcell -n {STEMCELL_URL}
```


### <div id="2.3"/> 2.3. Deployment Download  

Download the deployment needed from Git Repository and place the file in the service installation directory.  

- Service Deployment Git Repository URL : https://github.com/PaaS-TA/service-deployment/tree/v5.1.6

```
# Deployment File Download, make directory, change directory
$ mkdir -p ~/workspace
$ cd ~/workspace

# Deployment File Download
$ git clone https://github.com/PaaS-TA/service-deployment.git -b v5.1.6
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

- Modify the variable file used by Deployment YAML to suit the server environment.

> $ vi ~/workspace/service-deployment/pinpoint/vars.yml
```
# STEMCELL
stemcell_os: "ubuntu-bionic"                                     # stemcell os
stemcell_version: "1.76"                                       # stemcell version

# NETWORK
private_networks_name: "default"                                 # private network name
public_networks_name: "vip"                                      # public network name

# H_MASTER
master_azs: [z3]                                                 # h_master azs
master_instances: 1                                              # h_master instances (default : 1)
master_vm_type: "large"                                          # h_master vm type 
master_persistent_disk_type: "30GB"                              # h_master persistent disk type

# COLLECTOR
collector_azs: [z3]                                              # collector azs
collector_instances: 1                                           # collector instances (default : 1)
collector_vm_type: "large"                                       # collector vm type
collector_persistent_disk_type: "30GB"                           # collector persistent disk type

collector_tcp_port: 29994                                        # collector tcp listen port (default : 29994)
collector_stat_port: 29995                                       # collector stat port (default : 29995)
collector_span_port: 29996                                       # collector span port (default : 29996)

# PINPOINT_WEB
pinpoint_azs: [z3]                                               # pinpoint azs
pinpoint_instances: 1                                            # pinpoint instances (default : 1)
pinpoint_vm_type: "large"                                        # pinpoint vm type
pinpoint_persistent_disk_type: "30GB"                            # pinpoint persistent disk type

# BROKER
broker_azs: [z3]                                                 # broker azs
broker_instances: 1                                              # broker instances (default : 1)
broker_vm_type: "large"                                          # broker vm type
broker_persistent_disk_type: "30GB"                              # broker persistent disk type

# HAPROXY_WEBUI
webui_azs: [z7]                                                  # webui azs
webui_instances: 1                                               # webui instances (default : 1)
webui_vm_type: "large"                                           # webui vm type
webui_persistent_disk_type: "30GB"                               # webui persistent disk type
webui_haproxy_public_ip: "<WEB_UI_PUBLIC_IP>"                    # webui haproxy's public IP
```

### <div id="2.5"/> 2.5. Service Installation

- Modify the VARIABLES settings in the Deploy script file to suit the server environment, and decide whether to add the option file.  
     (optional) -o operations/cce.yml (Apply CCE when installing)

> $ vi ~/workspace/service-deployment/pinpoint/deploy.sh

```
#!/bin/bash

# VARIABLES
COMMON_VARS_PATH="<COMMON_VARS_FILE_PATH>"	# common_vars.yml File Path (e.g. ../../common/common_vars.yml)
CURRENT_IAAS="${CURRENT_IAAS}"			# IaaS Information (When not using create-bosh-login.sh provided by PaaS-TA, enter aws/azure/gcp/openstack/vsphere)
BOSH_ENVIRONMENT="${BOSH_ENVIRONMENT}"		# bosh director alias name (When not using create-bosh-login.sh provided by PaaS-TA,check the name at bosh envs and enter)

# DEPLOY
bosh -e ${BOSH_ENVIRONMENT} -n -d pinpoint deploy --no-redact pinpoint.yml \
    -o operations/${CURRENT_IAAS}-network.yml \
    -o operations/cce.yml \
    -l ${COMMON_VARS_PATH} \
    -l vars.yml
```

- Install Service.  
```
$ cd ~/workspace/service-deployment/pinpoint  
$ sh ./deploy.sh  
```  


### <div id="2.6"/> 2.6. Service Installation Check

Check the installed service.  

> $ bosh -e micro-bosh -d pinpoint vms  

```
Using environment '10.0.1.6' as client 'admin'

Task 5006. Done

Deployment 'pinpoint'

Instance                                              Process State  AZ  IPs            VM CID                                   VM Type  Active
broker/7a9d8423-c5f9-4e3c-be16-42e9215f8268           running        z5  10.30.107.182  vm-1b9b0891-8a02-45af-8b0c-ca21bf56e64e  minimal  true
collector/5f10f9cf-67ed-4c08-8c14-99d7f7a818c2        running        z5  10.30.107.145  vm-1714d225-85fa-4236-94db-7ed26baa52b3  minimal  true
h_master/723a34d6-0756-41e2-b11e-1530596cbb09         running        z5  10.30.107.175  vm-037e021b-3dba-4ea3-961c-6bd259d25c4b  minimal  true
haproxy_webui/22999f9c-0798-43a5-b93b-bc5d44c4d210    running        z5  10.30.107.178  vm-294e229c-aa89-4a5c-9960-4009579bbf54  minimal  true
									 3.12.24.53
webui/30f7c9cf-ab03-4f78-a9bf-96c5148a9ec1            running        z5  10.30.107.180  vm-62d5e876-2ab4-4327-ba54-2f74b53e3da9  minimal  true

5 vms

Succeeded
```

##  <div id='3'> 3. Sample Web App Interworking Pinpoint

This Sample Web App is deployed on an open cloud platform and Pinpoint's service can be used with Provision and Bind.

### <div id='3.1'> 3.1. Pinpoint Service Broker Registration

When the Pinpoint service pack deployment is completed, the Pinpoint service broker must be registered first to use the service pack in the application.  
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
[USERNAME] / [PASSWORD] : User ID / PASSWORD with access to service broker
[SERVICE_BROKER_URL] : Service Broker Access URL
```

- Register Pinpoint Service Broker.

> $ cf create-service-broker pinpoint-service-broker admin cloudfoundry http://<broker_ip>:8080

```
$ cf create-service-broker pinpoint-service-broker admin cloudfoundry http://10.30.107.182:8080
Creating service broker pinpoint-service-broker as admin...
OK
```

-   Check the registered Pinpoint service broker.

> $ cf service-brokers
```
Getting service brokers as admin...
name url
pinpoint-service-broker http://10.30.107.182:8080
```

-   Check the list of accessible services.

> $ cf service-access
```
Getting service access as admin...
broker: pinpoint-service-broker
   offering   plan                access   orgs
   Pinpoint   Pinpoint_standard   none  
```
Access is not allowed by default when creating a service broker.

- Assign permission to a specific organization to access the service and recheck the access service list. (Overall Organization)

> $ cf enable-service-access Pinpoint
```
Enabling access to all plans of service Pinpoint for all orgs as admin...
OK
```

- Check the permission to access the service.
> $ cf service-access
```
broker: pinpoint-service-broker
   offering   plan                access   orgs
   Pinpoint   Pinpoint_standard   all  
```

### <div id='3.2'> 3.2. Sample Web App Download

The Sample Web App is deployed as an App to PaaS-TA. Initial data is generated through Pinpoint service Bind on the deployed app.  
After the binding is completed, Pinpoint service monitoring for the app can be performed through the browser through the connection url.

- Download Zip File of Sample Apps 
```
$ wget https://nextcloud.paas-ta.org/index.php/s/NDgriPk5cgeLMfG/download --content-disposition  
$ unzip paasta-service-samples.zip  
$ cd paasta-service-samples/pinpoint  
```

<br>

### <div id='3.3'> 3.3. Request for service in PaaS-TA

To use the Pinpoint service in the Sample Web App, you must request for a service (Provision).  
*Note: When requesting for a service, you must be logged in as a user who can request for a service in PaaS-TA.  

- check whether there is a service in the PaaS-TA Marketplace first.

> $ cf marketplace

```
Getting services from marketplace in org org / space space as admin...
OK

service    plans               description
Pinpoint   Pinpoint_standard   A simple pinpoint implementation
```

- Command for requesting Service Instance
```
cf create-service [SERVICE] [PLAN] [SERVICE_INSTANCE]

[SERVICE] : Service nName shown in the Marketplace
[PLAN] : Policies for Services
[SERVICE_INSTANCE] : Name of the service instance to create
```

- If there is a service that you want in the Marketplace, create a service instance by requesting for a service (Provision).

> $ cf create-service Pinpoint Pinpoint_standard PS1

```
Creating service instance PS1 in org org / space space as admin...
OK
```

- Check the generated Pinpoint service instance.

> $ cf services
```
Getting services in org system / space space as admin...
OK

name   service      plan                 bound apps   last
PS1    Pinpoint     Pinpoint_standard                 create succeeded
```

### <div id='3.4'> 3.4. Request for service bind to Sample Web App and check for App

When the service application is completed, the Sample Web App binds the generated service instance and uses the Pinpoint service in the App.  
*Note: When requesting for service bind, you must be logged in as a user who can request for service bind on the PaaS-TA platform.

- Check manifest fie. 
	
> $ vi manifest.yml   

```
---
applications:
- name: spring-music-pinpoint
  memory: 1G
  disk_quota: 1G 
  random-route: false
  path: spring-music-1.0.jar
  buildpacks:
    - pinpoint_buildpack
  env:
    JBP_CONFIG_SPRING_AUTO_RECONFIGURATION: '{enabled: false}'
```

- Pinpoint buildpack Registration  
	
> $ cf create-buildpack pinpoint_buildpack java-buildpack-pinpoint-monitoring-2402a2c.zip 14
```	
Creating buildpack pinpoint_buildpack as admin...
OK

Uploading buildpack pinpoint_buildpack as admin...
 13.96 MiB / 13.96 MiB [=================================================================================================
OK

Processing uploaded buildpack pinpoint_buildpack...
OK
```

- Deploy app with --no-start option.

> $ cf push --no-start 
```  
Applying manifest file /home/ubuntu/workspace/samples/paasta-service-samples/pinpoint/manifest.yml...
Manifest applied
Packaging files to upload...
Uploading files...
 48.91 MiB / 48.91 MiB [=================================================================================================

Waiting for API to complete processing files...

name:              spring-music-pinpoint
requested state:   stopped
routes:            spring-music-pinpoint.paastacloud.shop
last uploaded:     
stack:             
buildpacks:        

type:           web
sidecars:       
instances:      0/1
memory usage:   1024M
     state   since                  cpu    memory   disk     details
#0   down    2021-11-22T05:26:04Z   0.0%   0 of 0   0 of 0   
```  
	
- Request for service instance bind created by Sample Web App.

> $ cf bind-service spring-music-pinpoint PS1 -c '{"application_name":"spring-music-pinpoint"}'
	
```	
Binding service PS1 to app spring-music-pinpoint in org org / space space as admin...
OK
TIP: Use 'cf restage spring-music-pinpoint' to ensure your env variable changes take effect
```
	
When running the app, add a security group for communication with the service.
	
- Modify rule.json.  
> $ vi rule.json   
```
## Set the collector IP of pinpoint to destination
[
  {
    "protocol": "all",
    "destination": "<collector_ip>"
  }
]
```
  
- Create security group.  

> $ cf create-security-group pinpoint rule.json  

```
Creating security group pinpoint as admin...

OK		
```
  
- Request the security group that you created to use the Pinpoint service.
> $ cf bind-running-security-group pinpoint  
```
Binding security group pinpoint to running as admin...
OK		
```

- Restage the app for the binding to be applied.

> $ cf restage spring-music-pinpoint

```	
Staging app and tracing logs...
   Downloading pinpoint_buildpack...
   Downloaded pinpoint_buildpack (14M)
   Cell 4a88ce8b-1e72-485a-8f62-1fe0c6b9a7cd creating container for instance 89996fa5-3197-4a9c-8304-9a5b288c74ee
   Cell 4a88ce8b-1e72-485a-8f62-1fe0c6b9a7cd successfully created container for instance 89996fa5-3197-4a9c-8304-9a5b288c
   Downloading app package...
   Downloaded app package (50M)

........
........
	
Instances starting...
Instances starting...

name:              spring-music-pinpoint
requested state:   started
routes:            spring-music-pinpoint.paasta.kr
last uploaded:     Mon 22 Nov 05:28:14 UTC 2021
stack:             cflinuxfs3
buildpacks:        
	name                 version   detect output   buildpack name
	pinpoint_buildpack                             

type:           web
sidecars:       
instances:      1/1
memory usage:   1024M
     state     since                  cpu    memory        disk           details
#0   running   2021-11-22T05:28:37Z   0.0%   47.5M of 1G   177.9M of 1G   

type:           task
sidecars:       
instances:      0/0
memory usage:   1024M
There are no running instances of this process.
```

- Check service normal operation
> $ cf service PS1
```
name:             PS1
service:          Pinpoint
tags:             
plan:             Pinpoint_standard
description:      A simple pinpoint implementation
documentation:    http://www.openpaas.org
dashboard:        http://3.53.24.53/#/main
service broker:   pinpoint-service-broker
```

- PINPOINT UI Access 
```
# PINPOINT APP UI
http://[DASHBOARD]/[application_name]@SPRING_BOOT/realtime

e.g. http://3.12.24.53/#/main/spring-music-pinpoint@SPRING_BOOT/realtime
```	
	
![pinpoint_image_04]

[pinpoint_image_01]:./images/pinpoint/pinpoint-image1.png
[pinpoint_image_01-1]:./images/pinpoint/pinpoint-image1-1.png
[pinpoint_image_02]:./images/pinpoint/pinpoint-image2.png
[pinpoint_image_03]:./images/pinpoint/pinpoint-image3.png
[pinpoint_image_04]:./images/pinpoint/pinpoint-image4.png


### [Index](https://github.com/PaaS-TA/Guide-eng/blob/master/README.md) > [AP Install](../README.md) > Pinpoint APM Service
