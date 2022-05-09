### [Index](https://github.com/PaaS-TA/Guide-eng/blob/master/README.md) > [AP Install](../README.md) > Logging Service

## Table of Contents

1. [Document Outline](#1)  
  1.1. [Purpose](#1.1)  
  1.2. [Range](#1.2)   
  1.3. [References](#1.3)  

2. [Logging Service Installation](#2)  
  2.1. [Prerequisite](#2.1)   
  2.2. [Stemcell Check](#2.2)    
  2.3. [Deployment Download](#2.3)   
  2.4. [Deployment File Modification](#2.4)  
  2.5. [Service Installation](#2.5)      
  2.6. [Sercice Installation Check](#2.6)  

3. [Logging Service management](#3)  
  3.1. [Logging Service UAA Client Registration](#3.1)  
  3.2. [Logging Service Usage Enable](#3.2)  



## <div id="1"/> 1. Document Outline

### <div id="1.1"/> 1.1. Purpose

This document (Logging Service Pack Installation Guide) describes how to install the Logging service pack, which is a service pack provided by PaaS-TA, using Bosh.  

### <div id="1.2"/> 1.2. Range

The installation range was prepared based on the basic installation to verify the Logging service pack.

### <div id="1.3"/> 1.3. References
BOSH Document: [http://bosh.io](http://bosh.io)  
Cloud Foundry Document: [https://docs.cloudfoundry.org](https://docs.cloudfoundry.org)  

## <div id="2"/> 2. Logging Service Installation  

### <div id="2.1"/> 2.1. Prerequisite 

This installation guide is based on installing in a Linux environment.  
To install the service pack, BOSH CLI v2 must be installed and logged in to BOSH.  
If BOSH CLI v2 is not installed, you should first refer to the BOSH 2.0 installation guide document to install BOSH CLI v2 and familiarize the usage.  
If the UAA client is not installed, installation of the UAA client is required.

- UAA client Installation (BOSH Dependency installation required)
```
$ sudo gem install cf-uaac
$ uaac -v
```

※ "firehose-to-syslog" uaac client check  

Verify if "firehose-to-syslog" is registered in uaac client, If registered, check "authorities" and grant "cloud_controller.admin" permission.  

```
# endpoint Setting
$ uaac target https://uaa.<DOMAIN> --skip-ssl-validation

# target Check
$ uaac target
Target: https://uaa.<DOMAIN>
Context: uaa_admin, from client uaa_admin

# uaac Login
$ uaac token client get <UAA_ADMIN_CLIENT_ID> -s <UAA_ADMIN_CLIENT_SECRET>

# "firehose-to-syslog" uaac client Check
$ uaac client get firehose-to-syslog
scope: cloud_controller.admin_read_only cloud_controller.global_auditor openid routing.router_groups.write network.write scim.read cloud_controller.admin uaa.user cloud_controller.read
    password.write routing.router_groups.read cloud_controller.write network.admin doppler.firehose scim.write
client_id: firehose-to-syslog
resource_ids: none
authorized_grant_types: client_credentials
autoapprove:
authorities: uaa.none doppler.firehose                   >>>>>>>>  Verify cloud_controller.admin authorization
lastmodified: 1552530293656

# "firehose-to-syslog" uaac client modification
$ uaac client update firehose-to-syslog --authorities "doppler.firehose, uaa.none, cloud_controller.admin"

# "firehose-to-syslog" uaac client Check
$ uaac client get firehose-to-syslog
scope: cloud_controller.admin_read_only cloud_controller.global_auditor openid routing.router_groups.write network.write scim.read cloud_controller.admin uaa.user cloud_controller.read
    password.write routing.router_groups.read cloud_controller.write network.admin doppler.firehose scim.write
client_id: firehose-to-syslog
resource_ids: none
authorized_grant_types: client_credentials
autoapprove:
authorities: uaa.none doppler.firehose cloud_controller.admin
lastmodified: 1552530293656
```

### <div id="2.2"/> 2.2. Stemcell Check  

Check the Stemcell list to make sure that the Stemcell required for service installation is uploaded.  
Stemcell in this guide uses ubuntu-xenial 621.94.  

> $ bosh -e micro-bosh stemcells  

```
Using environment '10.0.1.6' as client 'admin'

Name                                     Version  OS             CPI  CID  
bosh-aws-xen-hvm-ubuntu-xenial-go_agent  621.94*  ubuntu-xenial  -    ami-0297ff649e8eea21b  

(*) Currently deployed

1 stemcells

Succeeded
```  

If the corresponding Stemcell is not uploaded, copy the Stemcell link to the corresponding IaaS environment and version from [bosh.io Stemcell] (https://bosh.io/stemcells/) and run the following command.

```
# Example of Stemcell Upload Command
bosh -e ${BOSH_ENVIRONMENT} upload-stemcell -n {STEMCELL_URL}
```

### <div id="2.3"/> 2.3. Deployment Download

Download the deployment needed from Git Repository and place the file in the service installation directory.  

- Service Deployment Git Repository URL : https://github.com/PaaS-TA/service-deployment/tree/v5.0.6

```
# Deployment file download, make directory, change directory
$ mkdir -p ~/workspace
$ cd ~/workspace

# Deployment File Download
$ git clone https://github.com/PaaS-TA/service-deployment.git -b v5.0.6

# common_vars.yml File Download(download if common_vars.yml doesn't exist)
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
- Variables used in Logging service are : system_domain, uaa_client_admin_id, and uaa_client_admin_secret.

> $ vi ~/workspace/common/common_vars.yml
```
... ((Skip)) ...

# PAAS-TA INFO
system_domain: "61.252.53.246.nip.io"		# Domain (Same as HAProxy Public IP when using nip.io)
uaa_client_admin_id: "admin"			# UAAC Admin Client Admin ID
uaa_client_admin_secret: "admin-secret"		# Secret Variables for Accessing the UAAC Admin Client

... ((Skip)) ...

```


- Modify the variable files used by Deployment YAML to suit the server environment.

> $ vi ~/workspace/service-deployment/logging-service/vars.yml

```
# STEMCELL
stemcell_os: "ubuntu-xenial"                                     # stemcell os
stemcell_version: "621.94"                                       # stemcell version

# VM_TYPE
vm_type_minimal: "minimal"                                       # vm type minimal
vm_type_default: "small"                                         # vm type small
vm_type_medium: "medium"                                         # vm type medium

# NETWORK
private_networks_name: "default"                                 # private network name 
public_networks_name: "vip"                                      # public network name
private_nat_networks_name: "default"                             # AWS의 경우, NATS Network Name

# ELASTICSEARCH_MASTER
es_master_azs: [z3]                                              # elasticsearch master : azs
es_master_instances: 1                                           # elasticsearch master : instances (1) 
es_master_persistent_disk_type: "10GB"                           # elasticsearch master : persistent disk type
es_master_private_ips: "<ES_MASTER_PRIVATE_IPS>"                 # elasticsearch master : private ips (e.g. ["10.0.81.11"])
es_master_private_url: "<ES_MASTER_PRIVATE_URL>"                 # elasticsearch master : private url (e.g. "10.0.81.11")

# QUEUE
queue_azs: [z3]                                                  # queue : azs
queue_instances: 1                                               # queue : instances (1)
queue_persistent_disk_type: "10GB"                               # queue : persistent disk type
queue_private_ips: "<QUEUE_PRIVATE_IPS>"                         # queue : private ips (e.g. ["10.0.81.12"])
queue_private_url: "<QUEUE_PRIVATE_URL>"                         # queue : private url (e.g. "10.0.81.12")

# MAINTENANCE
maintenance_azs: [z3]                                            # maintenance : azs
maintenance_instances: 1                                         # maintenance : instances (1)
maintenance_private_ips: "<MAINTENANCE_PRIVATE_IPS>"             # maintenance : private ips (e.g. ["10.0.81.13"])

# ELASTICSEARCH_DATA
es_data_azs: [z3]                                                # elasticsearch data : azs 
es_data_instances: 2                                             # elasticsearch data : instances (N)
es_data_persistent_disk_type: "20GB"                             # elasticsearch data : persistent disk type
es_data_private_ips: "<ES_DATA_PRIVATE_IPS>"                     # elasticsearch data : private ips (e.g. ["10.0.81.14", "10.0.81.15"])

# VISUALIZATION
visualization_azs: [z3]                                          # visualization : azs
visualization_instances: 1                                       # visualization : instances (1) 
visualization_private_ips: "<VISUALIZATION_PRIVATE_IPS>"         # visualization : private ips (e.g. ["10.0.81.16"])
visualization_version:  "5.3.0"                                  # visualization : version (5.3.0)

# COLLECTOR
collector_azs: [z3]                                              # collector : azs
collector_instances: 1                                           # collector : instances (1)
collector_private_ips: "<COLLECTOR_PRIVATE_IPS>"                 # collector : private ips (e.g. ["10.0.81.17"])

# PARSER
parser_azs: [z3]                                                 # parser : azs
parser_instances: 2                                              # parser : instances (N)
parser_private_ips: "<PARSER_PRIVATE_IPS>"                       # parser : private ips (e.g. ["10.0.81.18", "10.0.81.19"])
parser_es_index: "%{[@metadata][index]}-%{+YYYY.MM.dd.HH}"       # parser : elasticsearch index
parser_es_index_type: '%{[@metadata][type]}'                     # parser : elasticsearch index type

# ROUTER
router_azs: [z7]                                                 # router : azs 
router_instances: 1                                              # router : instances (1)
router_private_ips: "<ROUTER_PRIVATE_IPS>"                       # router : private ips (e.g. ["10.0.0.101"])
router_public_ips: "<ROUTER_PUBLIC_IPS>"                         # router : public ips (e.g. "13.209.212.226")
router_private_url: "<ROUTER_PRIVATE_URL>"                       # router : private url (e.g. "10.0.0.101")

# UAAC
uaa_client_laas_id: "laasclient"                                 # logging service uaa client id
uaa_client_laas_secret: "clientsecret"                           # logging service uaa client secret

# LOGGING SERVICE
es_config_index_prefix: "laas-"                                  # logging service elasticsearch index prefix ("laas-")
retention_period: 7                                              # logging service retention period
laas_logo: "data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAALQAAABGCAYAAABll74gAAAAGXRFWHRTb2Z0d2FyZQBBZG9iZSBJbWFnZVJlYWR5ccllPAAAA3FpVFh0WE1MOmNvbS5hZG9iZS54bXAAAAAAADw/eHBhY2tldCBiZWdpbj0i77u/IiBpZD0iVzVNME1wQ2VoaUh6cmVTek5UY3prYzlkIj8+IDx4OnhtcG1ldGEgeG1sbnM6eD0iYWRvYmU6bnM6bWV0YS8iIHg6eG1wdGs9IkFkb2JlIFhNUCBDb3JlIDUuNi1jMTQyIDc5LjE2MDkyNCwgMjAxNy8wNy8xMy0wMTowNjozOSAgICAgICAgIj4gPHJkZjpSREYgeG1sbnM6cmRmPSJodHRwOi8vd3d3LnczLm9yZy8xOTk5LzAyLzIyLXJkZi1zeW50YXgtbnMjIj4gPHJkZjpEZXNjcmlwdGlvbiByZGY6YWJvdXQ9IiIgeG1sbnM6eG1wTU09Imh0dHA6Ly9ucy5hZG9iZS5jb20veGFwLzEuMC9tbS8iIHhtbG5zOnN0UmVmPSJodHRwOi8vbnMuYWRvYmUuY29tL3hhcC8xLjAvc1R5cGUvUmVzb3VyY2VSZWYjIiB4bWxuczp4bXA9Imh0dHA6Ly9ucy5hZG9iZS5jb20veGFwLzEuMC8iIHhtcE1NOk9yaWdpbmFsRG9jdW1lbnRJRD0ieG1wLmRpZDpkYzhkNmI4YS1kZWNkLThkNGItOThiNC0zMjRjZjU1OTE0NmYiIHhtcE1NOkRvY3VtZW50SUQ9InhtcC5kaWQ6MkRBNUQ0REMyQ0EwMTFFOEI2QkZBQUY2QUYwM0UzRjkiIHhtcE1NOkluc3RhbmNlSUQ9InhtcC5paWQ6MkRBNUQ0REIyQ0EwMTFFOEI2QkZBQUY2QUYwM0UzRjkiIHhtcDpDcmVhdG9yVG9vbD0iQWRvYmUgUGhvdG9zaG9wIENDIChXaW5kb3dzKSI+IDx4bXBNTTpEZXJpdmVkRnJvbSBzdFJlZjppbnN0YW5jZUlEPSJ4bXAuaWlkOjQ5YzFjOTUyLWE2MDEtZTI0NC1iMzIzLTMzYTIyNzI1NDcxYiIgc3RSZWY6ZG9jdW1lbnRJRD0ieG1wLmRpZDpkYzhkNmI4YS1kZWNkLThkNGItOThiNC0zMjRjZjU1OTE0NmYiLz4gPC9y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"
```  

### <div id="2.5"/> 2.5. Service Installation

- Modify the VARIABLES settings in the Deploy script file to suit the server environment, and select whether to add the option file.   

> $ vi ~/workspace/service-deployment/logging-service/deploy.sh

```
#!/bin/bash

# VARIABLES
COMMON_VARS_PATH="<COMMON_VARS_FILE_PATH>"  # common_vars.yml File Path (e.g. ../../common/common_vars.yml)
CURRENT_IAAS="${CURRENT_IAAS}"              # IaaS Information (When not using create-bosh-login.sh provided by PaaS-TA,enter aws/azure/gcp/openstack/vsphere)
BOSH_ENVIRONMENT="${BOSH_ENVIRONMENT}"      # bosh director alias name (When not using create-bosh-login.sh provided by PaaS-TA, check the name at bosh envs and enter)

# DEPLOY
bosh -e ${BOSH_ENVIRONMENT} -n -d logging-service deploy --no-redact logging-service.yml \
    -o operations/${CURRENT_IAAS}-network.yml \
    -l ${COMMON_VARS_PATH} \
    -l vars.yml
```

- Install service.  
```
$ cd ~/workspace/service-deployment/logging-service
$ sh ./deploy.sh  
```  


### <div id="2.6"/> 2.6. Service Installation Check

Check the installed service.  

> $ bosh -e micro-bosh -d logging-service vms
```
Using environment '10.30.40.111' as client 'admin'

Task 68432. Done

Deployment 'logging-service'

Instance                                                   Process State  AZ  IPs            VM CID                                   VM Type  Active  
collector/d2a1aed9-d10f-42df-91ec-e21f1baecfb8             running        z5  10.30.107.131  vm-e73085ec-e336-4c54-a842-37989dc4fe1d  default  true  
elasticsearch_data/d779c528-8f75-4b4c-b2d9-ac367c1e5ece    running        z5  10.30.107.133  vm-5b1fed2f-774f-47cf-9a14-edc015e790f1  medium   true  
elasticsearch_data/fa38698e-913c-4296-aac8-c0b56c84a71e    running        z5  10.30.107.134  vm-36a47dab-8d09-4daa-bb8c-0394f4d83fd7  medium   true  
elasticsearch_master/4698c36b-413d-4370-b671-44ee075a0cf0  running        z5  10.30.107.135  vm-46152b8f-d660-413c-9396-8b4068a4a454  default  true  
maintenance/dba09e1e-06c0-42bf-a30d-d97a62c536bc           running        z5  10.30.107.136  vm-780e1595-9aa9-445c-b056-27ff4e844017  minimal  true  
parser/3dfdc7bc-8dde-4ed1-95d0-eb638d4900fa                running        z5  10.30.107.138  vm-44210be7-0dab-46db-8cb6-d71a2c29d3c8  default  true  
parser/7ef8ffd6-7d8b-4ae0-bd8c-17f5e7092ca2                running        z5  10.30.107.137  vm-1eb78459-3050-4ab2-8f49-78f0ddb795b0  default  true  
queue/cc986003-b6c1-4570-b2d7-32ecfd40eedf                 running        z5  10.30.107.139  vm-f11ec996-5c1e-46a0-972a-8b1415267df0  default  true  
router/c64e9519-713c-4f24-9b04-4bbf2d0ac457                running        z5  10.30.107.140  vm-32ebc53c-6bef-48d7-854e-4b09a4dd9d01  minimal  true  
                                                                              115.68.47.181                                                      
visualization/d1ac0c78-aa4c-465d-9193-64f2e2de269a         running        z5  10.30.107.143  vm-75fdb6a6-e77f-4adb-8336-ec77254c82fa  default  true

```

## <div id="3"/>3.  Logging Service Management

When the service installation is completed, the logging service UAA Client registration and logging service activation code must be registered to use the service on the PaaS-TA portal.


### <div id="3.1"/>  3.1.	UAA Client Registration

-	Set endpoint of uaac server.

```
# endpoint Setting
$ uaac target https://uaa.<DOMAIN> --skip-ssl-validation

# target Check
$ uaac target
Target: https://uaa.<DOMAIN>
Context: uaa_admin, from client uaa_admin

```

-	Log in to uaac.

```
$ uaac token client get <UAA_ADMIN_CLIENT_ID> -s <UAA_ADMIN_CLIENT_SECRET>
Successfully fetched token via client credentials grant.
Target: https://uaa.<DOMAIN>
Context: admin, from client admin

```

-	Create a Logging service account. 
```
### uaac client add description
uaac client add <CF_UAA_CLIENT_ID> -s <CF_UAA_CLIENT_SECRET> --redirect_uri <Logging Service URI> --scope <Permission Range> --authorized_grant_types <Authorization Type> --authorities=<Authority Permission> --autoapprove=<Automatic Authorization>  

<CF_UAA_CLIENT_ID> : uaac client id  
<CF_UAA_CLIENT_SECRET> : uaac client secret  
<Logging Service URI> : Logging Service Access URI to be Redirected Successfully (router public IP of http://<logging-service>)  
<Permission Range> : Permissible range list that the clients can earn on behalf of the users  
<Authority Type> : Authority list to use APIs provided by service packs   
<Authority Permission> : Authority list granted to clients 
<Automatic Authorization> : Authority list that do not require user approval  
``` 
> uaac client add laasclient -s clientsecret --redirect_uri " http://[DASHBOARD_URL]" \
>  --scope "cloud_controller_service_permissions.read , openid , cloud_controller.read , cloud_controller.write , cloud_controller.admin" \
>  --authorized_grant_types "authorization_code , client_credentials , refresh_token" \
>  --authorities="uaa.resource" \
>--autoapprove="openid , cloud_controller_service_permissions.read"
```  
# e.g. Create a Logging Service Account
$ uaac client add laasclient -s clientsecret --redirect_uri " http://115.68.47.181" \
  --scope "cloud_controller_service_permissions.read , openid , cloud_controller.read , cloud_controller.write , cloud_controller.admin" \
  --authorized_grant_types "authorization_code , client_credentials , refresh_token" \
  --authorities="uaa.resource" \
--autoapprove="openid , cloud_controller_service_permissions.read"

# e.g. Verify created Logging service account
$ uaac clients
laasclient
    scope: cloud_controller.read cloud_controller.write cloud_controller_service_permissions.read openid
        cloud_controller.admin
    resource_ids: none
    authorized_grant_types: refresh_token client_credentials authorization_code
    redirect_uri: http://115.68.47.181
    autoapprove: cloud_controller_service_permissions.read openid
    authorities: uaa.resource
    name: laasclient
    lastmodified: 1542894096080

```  

## <div id="3.2"/>  3.2. Logging Service Activation Code Registration

-	Access the PaaS-Ta operator portal and log in.
![002]

-	Go to the code management menu of the operation management and register the code as follows.

> ※ Group Table  
> Code ID  : LAAS  
> Code Name : Logging Service  
> ![003]
>
> ※ Detail Table  
> Key : laas_base_url  
> Value : http://<Logging Service Access IP>/app/laas  
> Outline : Logging Service Base URL  
> Use : Y  
> ![004]

![005]

[001]:./images/logging-service/image001.png
[002]:./images/logging-service/image002.png
[003]:./images/logging-service/image003.png
[004]:./images/logging-service/image004.png
[005]:./images/logging-service/image005.png

### [Index](https://github.com/PaaS-TA/Guide-eng/blob/master/README.md) > [AP Install](../README.md) > Logging Service
