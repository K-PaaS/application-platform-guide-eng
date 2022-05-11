### [Index](https://github.com/PaaS-TA/Guide-eng/blob/master/README.md) > [AP Install](../README.md) > Gateway Service

## Table of Contents

1. [Document Outline](#1)  
  1.1. [Purpose](#1.1)  
  1.2. [Range](#1.2)  
  1.3. [References](#1.3)  

2. [Application Gateway Service Installation](#2)  
  2.1. [Prerequisite](#2.1)   
  2.2. [Stemcell Check](#2.2)    
  2.3. [Deployment Download](#2.3)   
  2.4. [Deployment File Modification](#2.4)  
  2.5. [Service Installation](#2.5)    
  2.6. [Service Installation Check](#2.6)  
  
3. [Application Gateway Service Management and Registration](#3)  
 3.1. [Service Broker Registration](#3.1)   
 3.2. [Service Application](#3.2)  
　3.2.1. [Service Application - Portal](#3.2.1)   
　3.2.2. [Service Application - CLI](#3.2.2)   



## <div id="1"/> 1. Document Outline

### <div id="1.1"/> 1.1. Purpose

This document (Application Gateway Service Pack Installation Guide) describes how to install the Application Gateway Service Pack, which is a service pack provided by PaaS-TA, using Bosh  

### <div id="1.2"/> 1.2. Range

The installation scope was prepared based on the basic installation to verify the application gateway service pack.  

### <div id="1.3"/> 1.3. References
BOSH Document: [http://bosh.io](http://bosh.io)  
Cloud Foundry Document: [https://docs.cloudfoundry.org](https://docs.cloudfoundry.org)  
Application Gateway Service(WSO2) Document : [https://apim.docs.wso2.com/en/3.2.0/](https://apim.docs.wso2.com/en/3.2.0/)

## <div id="2"/> 2. Application Gateway Service Installation  

### <div id="2.1"/> 2.1. Prerequisite  

This installation guide is based on installing in a Linux environment.   
To install the service pack, BOSH CLI v2 must be installed and logged in to BOSH.  
If BOSH CLI v2 is not installed, you should first refer to the BOSH 2.0 installation guide document to install BOSH CLI v2 and familiarize the usage. 

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
# Deployment file download, make directory, change directory
$ mkdir -p ~/workspace
$ cd ~/workspace

# Deployment File Download
$ git clone https://github.com/PaaS-TA/service-deployment.git -b v5.1.6

# common_vars.yml File Download(download if common_vars.yml doesn't exist)
$ git clone https://github.com/PaaS-TA/common.git
```

### <div id="2.4"/> 2.4. Deployment File Modification

The BOSH Deployment manifest is a YAML file that defines the properties of components elements and deployments. 
Cloud config is used for network, vm_type, and disk_type used in Deployment files, and refer to the PaaS-TA AP installation guide for the usage.  

- Check Cloud config settings.   

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
- Variables used in Gateway service are: bosh_url, bosh_client_admin_id, bosh_client_admin_secret, bosh_director_port, and bosh_oauth_port.

> $ vi ~/workspace/common/common_vars.yml
```
... ((Skip)) ...

bosh_url: "https://10.0.1.6"			# BOSH URL (e.g. "https://00.000.0.0")
bosh_client_admin_id: "admin"			# BOSH Client Admin ID
bosh_client_admin_secret: "ert7na4jpew48"	# BOSH Client Admin Secret('echo $(bosh int ~/workspace/paasta-deployment/bosh/{iaas}/creds.yml --path /admin_password)' Can be checked through commads)
bosh_director_port: 25555			# BOSH director port
bosh_oauth_port: 8443				# BOSH oauth port

... ((Skip)) ...

```

- Modify the variable files used by Deployment YAML to suit the server environment.

> $ vi ~/workspace/service-deployment/gateway-service/vars.yml

```
# STEMCELL
stemcell_os: "ubuntu-bionic"                                         # stemcell os
stemcell_version: "1.76"                                           # stemcell version

# VM_TYPE
vm_type_default: "medium"                                            # vm type default
vm_type_highmem: "small-highmem-16GB"                                # vm type highmemory

# NETWORK
private_networks_name: "default"                                     # private network name
public_networks_name: "vip"                                          # public network name :: The public network name can only use "vip" or "service_public".

# MARIA_DB
mariadb_azs: [z3]                                                    # mariadb : azs
mariadb_instances: 1                                                 # mariadb : instances (1) 
mariadb_persistent_disk_type: "10GB"                                 # mariadb : persistent disk type 
mariadb_port: "<MARIADB_PORT>"                                       # mariadb : database port (e.g. 31306) -- Do Not Use "3306"
mariadb_admin_password: "<MARIADB_ADMIN_PASSWORD>"                   # mariadb : database admin password (e.g. "paas-ta!admin")
mariadb_broker_username: "<MARIADB_BROKER_USERNAME>"                 # mariadb : service-broker-user id (e.g. "apigateway")
mariadb_broker_password: "<MARIADB_BROKER_PASSWORD>"                 # mariadb : service-broker-user password (e.g. "broker!admin")

# SERVICE-BROKER
broker_azs: [z3]                                                     # service-broker : azs
broker_instances: 1                                                  # service-broker : instances (1)
broker_port: "<SERVICE_BROKER_PORT>"                                 # service-broker : broker port (e.g. "8080")
broker_logging_level_broker: "INFO"                                  # service-broker : broker logging level
broker_logging_level_hibernate: "INFO"                               # service-broker : hibernate logging level
broker_services_id: "<SERVICE_BROKER_SERVICES_GUID>"                 # service-broker : service guid (e.g. "8b78dfb6-1fb6-4586-b767-45b5f77e0d42")
broker_services_plans_id: "<SERVICE_BROKER_SERVICES_PLANS_GUID>"     # service-broker : service plan id (e.g. "b5e33932-8f87-4712-9776-887bfb73c584")

# API-GATEWAY
api_gateway_azs: [z7]                                                # api-gateway : azs
api_gateway_instances: 2                                             # api-gateway : instances (N)
api_gateway_persistent_disk_type: "20GB"                             # api-gateway : persistent disk type
api_gateway_public_ips: "<API_GATEWAY_PUBLIC_IPS>"                   # api-gateway : public ips (e.g. ["00.00.00.00" , "11.11.11.11"])
api_gateway_admin_password: "<API_GATEWAY_ADMIN_PASSWORD>"           # api-gateway : api-gateway super admin password (e.g. "admin!Service") special characters Only(-!^*)
```

### <div id="2.5"/> 2.5. Service Installation

- Modify the VARIABLES settings in the Deploy script file to suit the server environment, and select whether to add the option file.  
     (Optional) -o operations/cce.yml (Apply CCE when installing) 

> $ vi ~/workspace/service-deployment/gateway-service/deploy.sh

```
#!/bin/bash

# VARIABLES
COMMON_VARS_PATH="<COMMON_VARS_FILE_PATH>"  # common_vars.yml File Path (e.g. ../../common/common_vars.yml)
CURRENT_IAAS="${CURRENT_IAAS}"              # IaaS Information (When not using create-bosh-login.sh provided by PaaS-TA, enter aws/azure/gcp/openstack/vsphere)
BOSH_ENVIRONMENT="${BOSH_ENVIRONMENT}"      # bosh director alias name (When not using create-bosh-login.sh provided by PaaS-TA,Check the name at bosh envs and enter)

# DEPLOY
bosh -e ${BOSH_ENVIRONMENT} -n -d gateway-service deploy --no-redact gateway-service.yml \
    -o operations/${CURRENT_IAAS}-network.yml \
    -o operations/cce.yml \
    -l ${COMMON_VARS_PATH} \
    -l vars.yml

```

- Install Service.  
```
$ cd ~/workspace/service-deployment/gateway-service  
$ sh ./deploy.sh  
```  

### <div id="2.6"/> 2.6. Service Installation Check

Check the installed Service.  

> $ bosh -e micro-bosh -d gateway-service vms  

```
Using environment '10.0.1.6' as client 'admin'

Task 53. Done

Deployment 'gateway-service'

Instance                                             Process State  AZ  IPs           VM CID               VM Type  Active  
api-gateway/248133ba-73e4-4fd5-bf29-834cd4345f33     running        z7  10.0.0.123    i-0e7eee082646e7097  medium   true  
                                                                        52.78.10.153                                  
api-gateway/de1608fc-e254-40fd-a190-4d9366b50658     running        z7  10.0.0.122    i-0e457a121da8afaa8  medium   true  
                                                                        13.124.4.62                                   
mariadb/5b19e4ba-ea0b-4e76-b37b-8e6c991907ef         running        z3  10.0.81.122   i-0d7296803b6a2d36e  medium   true  
service-broker/6bcc651a-f94e-4b38-aee7-3640407315b6  running        z3  10.0.81.123   i-043331991d8beeda7  medium   true  

4 vms

Succeeded
```

## <div id="3"/>3.  Application Gateway Service Management and Registration

If you register and disclose the service through the PaaS-TA operator portal, you can apply for and use the service through the PaaS-TA user portal.

### <div id="3.1"/> 3.1. Service Broker Registration

Once the service is installed, an application gateway service broker must be registered to use the service on the PaaS-TA portal.  
When registering a service broker, you must be logged in as a user with authority to register a service broker on an open cloud platform.  

- Check the list of service brokers  
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

- Register Application Gateway Service Broker.  

> $ cf create-service-broker api-gateway-service-broker admin cloudfoundry http://<service-broker_ip>:8080
```
$ cf create-service-broker api-gateway-service-broker admin cloudfoundry http://10.0.81.123:8080
Creating service broker api-gateway-service-broker as admin...   
OK                                                              
```

- Check the registered application gateway service broker.  
> $ cf service-brokers

```
Getting service brokers as admin...

name                         url
api-gateway-service-broker   http://10.0.81.123:8080
```

- Check service access information of application gateway service.  
> $ cf service-access -b api-gateway-service-broker  

```
Getting service access for broker api-gateway-service-broker as admin...
broker: api-gateway-service-broker
   service       plan           access   orgs
   api-gateway   dedicated-vm   none
```

- Set (all) permission for service access of the application gateway service and reconfirm service access information.  
> $ cf enable-service-access api-gateway   
```
Enabling access to all plans of service api-gateway for all orgs as admin...
OK
```

> $ cf service-access -b api-gateway-service-broker   

```
Getting service access for broker api-gateway-service-broker as admin...
broker: api-gateway-service-broker
   service       plan           access   orgs
   api-gateway   dedicated-vm   all
```

### <div id='3.2'/> 3.2. Service Application
#### <div id='3.2.1'/> 3.2.1. Service Application - Portal

Access the PaaS-TA operator portal and register the service.  

> ※ Operation Management > Catalog > App service registration
> - Name : Application Gateway Service
> - Classification :  Development Support Tools
> - Service : api-gateway
> - Thumbnail : [Application Gateway Service Thumbnail]
> - Document URL : https://github.com/PaaS-TA/PAAS-TA-API-GATEWAY-SERVICE-BROKER
> - Service Creating Parameter : password 
> - Using App bind : N
> - Public : Y
> - Using Dashboard : Y
> - OnDemand : N
> - Tag : paasta / tag1, free / tag2
> - Outline : Application Gateway Service
> - Description :
> WSO2 service, an application gateway service that provides functions such as API registration and API lifecycle management, is provided in a dedicated manner.  
>  
> The service administrator's account is serviceadmin/ <Password entered when applying for a service>.  
>  
> ![002]

-	Access the PaaS-TA user portal and apply for services through the catalog.   

![003]

-	Access the service through the dashboard URL. (The administrator account of the service is serviceadmin/[Password entered when applying for the service])  

![004]  

#### <div id='3.2.2'/> 3.2.2. Service Application - CLI
Explains how to apply for the Gateway service through the CLI.

- Service Instance Application Commands
```
cf create-service [SERVICE] [PLAN] [SERVICE_INSTANCE]

[SERVICE] : Service name shown at the Marketplace
[PLAN] : Policies for Services
[SERVICE_INSTANCE] : Name of the service instance to create
```

- Apply for Gateway service. (Set password variable)
> $ cf create-service api-gateway dedicated-vm gateway -c '{"password":"{password}"}'
```
Creating service instance gateway in org system / space dev as admin...
OK
```

- Access the service by checking the service details dashboard URL information.
> $ cf service gateway
 ```
 ... (Skip) ...
dashboard:        https://13.124.4.62:9443/carbon|https://13.124.4.62:9443/publisher|https://13.124.4.62:9443/devportal|https://13.124.4.62:9443/admin
service broker:   api-gateway-service-broker
 ... (Skip) ...
 ```




[001]:./images/apigateway-service/image001.png
[002]:./images/apigateway-service/image002.png
[003]:./images/apigateway-service/image003.png
[004]:./images/apigateway-service/image004.png
[005]:./images/apigateway-service/image005.png
[006]:./images/apigateway-service/image006.png
[007]:./images/apigateway-service/image007.png
[011]:./images/apigateway-service/image011.jpeg
[012]:./images/apigateway-service/image012.jpeg
[013]:./images/apigateway-service/image013.jpeg


### [Index](https://github.com/PaaS-TA/Guide-eng/blob/master/README.md) > [AP Install](../README.md) > Gateway Service
