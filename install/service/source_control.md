### [Index](https://github.com/PaaS-TA/Guide-eng/blob/master/README.md) > [AP Install](../README.md) > Source Control Service

## Table of Contents  

1. [Document Outline](#1)  
  1.1. [Purpose](#1.1)  
  1.2. [Range](#1.2)  
  1.3. [References](#1.3)  

2. [Configuration Management Service Installation](#2)  
  2.1. [Prerequisite](#2.1)   
  2.2. [Stemcell Check](#2.2)    
  2.3. [Deployment Download](#2.3)   
  2.4. [Deployment File Modification](#2.4)  
  2.5. [Service Installation](#2.5)    
  2.6. [Service Installation Check](#2.6)  
  
3. [Management and request for Configuration management service](#3)  
 3.1. [Register Service Broker Command](#3.1)  
 3.2. [UAA Client Registration](#3.2)  
 3.3. [Service Request](#3.3)  
　3.3.1. [Service Request - Portal](#3.3.1)   
　3.3.2. [Service Request - CLI](#3.3.2)   
    

## <div id='1'/> 1. Document Outline

### <div id='1.1'/> 1.1. Purpose
This document (Figure Management Service Pack Installation Guide) describes how to install the shape management service pack, which is a service pack provided by PaaS-TA, using Bosh.  

### <div id='1.2'/> 1.2. Range
The installation range was prepared based on the basic installation for verifying the shape management service pack.

### <div id='1.3'/> 1.3. References
BOSH Document: [http://bosh.io](http://bosh.io)  
Cloud Foundry Document: [https://docs.cloudfoundry.org](https://docs.cloudfoundry.org)  

## <div id="2"/> 2. Configuration Management Service Installation  

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

### <div id="2.2"/> 2.2. Stemcell Cgeck  

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
# Example of Stemcell upload command
$ bosh -e ${BOSH_ENVIRONMENT} upload-stemcell -n {STEMCELL_URL}
```

### <div id="2.3"/> 2.3. Deployment Download  

Download the deployment needed from Git Repository and place the file in the service installation directory.  

- Service Deployment Git Repository URL : https://github.com/PaaS-TA/service-deployment/tree/v5.1.25

```
# Deployment File Download, make directory, change directory
$ mkdir -p ~/workspace
$ cd ~/workspace

# Deployment File Download
$ git clone https://github.com/PaaS-TA/service-deployment.git -b v5.1.25

# common_vars.yml File Download (download if common_vars.yml doesn't exist)
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

... ((SKip)) ...

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
- The variable used by the configuration management service is system_domain.

> $ vi ~/workspace/common/common_vars.yml
```
... ((Skip)) ...

system_domain: "61.252.53.246.nip.io"		# Domain (Same as HAProxy Public IP when using nip.io)

... ((Skip)) ...

```


- Modify the variable file used by Deployment YAML to suit the server environment.

> $ vi ~/workspace/service-deployment/source-control-service/vars.yml

```
# STEMCELL
stemcell_os: "ubuntu-jammy"                                   # stemcell os
stemcell_version: "1.181"                                     # stemcell version

# VM_TYPE
vm_type_small: "minimal"                                       # vm type small

# NETWORK
private_networks_name: "default"                               # private network name
public_networks_name: "vip"                                    # public network name

# SCM-SERVER
scm_azs: [z3]                                                  # scm : azs
scm_instances: 1                                               # scm : instances (1)
scm_persistent_disk_type: "30GB"                               # scm : persistent disk type
scm_private_ips: "<SCM_PRIVATE_IPS>"                           # scm : private ips (e.g. "10.0.81.41")

# MARIA-DB# MARIA_DB
mariadb_azs: [z3]                                              # mariadb : azs
mariadb_instances: 1                                           # mariadb : instances (1) 
mariadb_persistent_disk_type: "2GB"                            # mariadb : persistent disk type 
mariadb_private_ips: "<MARIADB_PRIVATE_IPS>"                   # mariadb : private ips (e.g. "10.0.81.42")
mariadb_port: "<MARIADB_PORT>"                                 # mariadb : database port (e.g. 31306) -- Do Not Use "3306"
mariadb_admin_password: "<MARIADB_ADMIN_PASSWORD>"             # mariadb : database admin password (e.g. "!paas_ta202")
mariadb_broker_username: "<MARIADB_BROKER_USERNAME>"           # mariadb : service-broker-user id (e.g. "sourcecontrol")
mariadb_broker_password: "<MARIADB_BROKER_PASSWORD>"           # mariadb : service-broker-user password (e.g. "!scadmin2017")

# HAPROXY
haproxy_azs: [z7]                                              # haproxy : azs
haproxy_instances: 1                                           # haproxy : instances (1)
haproxy_persistent_disk_type: "2GB"                            # haproxy : persistent disk type
haproxy_private_ips: "<HAPROXY_PRIVATE_IPS>"                   # haproxy : private ips (e.g. "10.0.0.31")
haproxy_public_ips: "<HAPROXY_PUBLIC_IPS>"                     # haproxy : public ips (e.g. "101.101.101.5")

# WEB-UI
web_ui_azs: [z3]                                               # web-ui : azs
web_ui_instances: 1                                            # web-ui : instances (1)
web_ui_persistent_disk_type: "2GB"                             # web-ui : persistent disk type
web_ui_private_ips: "<WEB_UI_PRIVATE_IPS>"                     # web-ui : private ips (e.g. "10.0.81.44")

# SCM-API
api_azs: [z3]                                                  # scm-api : azs
api_instances: 1                                               # scm-api : instances (1)
api_persistent_disk_type: "2GB"                                # scm-api : persistent disk type
api_private_ips: "<API_PRIVATE_IPS>"                           # scm-api : private ips (e.g. "10.0.81.45")

# SERVICE-BROKER
broker_azs: [z3]                                               # service-broker : azs
broker_instances: 1                                            # service-broker : instances (1)
broker_persistent_disk_type: "2GB"                             # service-broker : persistent disk type
broker_private_ips: "<BROKER_PRIVATE_IPS>"                     # service-broker : private ips (e.g. "10.0.81.46")

# UAAC
uaa_client_sc_id: "scclient"                                   # source-control-service uaa client id
uaa_client_sc_secret: "clientsecret"                           # source-control-service uaa client secret
```

### <div id="2.5"/> 2.5. Service Installation

- Modify the VARIABLES settings in the Deploy script file to suit the server environment, and decide whether to add the option file.  
     (Optional) -o operations/cce.yml (Apply CCE when installing)

> $ vi ~/workspace/service-deployment/source-control-service/deploy.sh

```
#!/bin/bash
  
# VARIABLES
COMMON_VARS_PATH="<COMMON_VARS_FILE_PATH>"	# common_vars.yml File Path (e.g. ../../common/common_vars.yml)
CURRENT_IAAS="${CURRENT_IAAS}"			# IaaS Information (When not using create-bosh-login.sh provided by PaaS-TA, enter aws/azure/gcp/openstack/vsphere)
BOSH_ENVIRONMENT="${BOSH_ENVIRONMENT}"		# bosh director alias name (When not using create-bosh-login.sh provided by PaaS-TA, check the name at bosh envs and enter)

# DEPLOY
bosh -e ${BOSH_ENVIRONMENT} -n -d source-control-service deploy --no-redact source-control-service.yml \
    -o operations/${CURRENT_IAAS}-network.yml \
    -o operations/cce.yml \
    -l ${COMMON_VARS_PATH} \
    -l vars.yml
```

- Install service.  
```
$ cd ~/workspace/service-deployment/source-control-service  
$ sh ./deploy.sh  
```  


### <div id="2.6"/> 2.6. Service Installation Check

Check the installed service.

> $ bosh -e micro-bosh -d source-control-service vms  

```
Using environment '10.0.1.6' as client 'admin'

Task 7847. Done

Deployment 'source-control-service'

Instance                                                   Process State  AZ  IPs            VM CID                                   VM Type  Active
haproxy/878b393c-d817-4e71-8fe5-553ddf87d362               running        z5  115.68.47.179  vm-cc4c774d-9857-4b3a-9ffc-5f836098eb4e  minimal  true
								              10.30.107.123
mariadb/90ea7861-57ed-43f7-853d-25712a67ba2a               running        z5  10.30.107.122  vm-6952fb76-b4d6-4f53-86cb-b0517a76f0d0  minimal  true
scm-server/6e23addc-33c7-4bb0-af95-d66420f15c06            running        z5  10.30.107.121  vm-08eb6dd3-04ae-435c-9558-9b78286b730c  minimal  true
sourcecontrol-api/3ecb90fa-2211-4df6-82bb-5c91ed9a4310     running        z5  10.30.107.125  vm-ee4daa28-3e10-4409-9d6f-ce566d54e8a5  minimal  true
sourcecontrol-broker/ec83edb5-130f-4a91-9ac1-20fb622ed0a2  running        z5  10.30.107.126  vm-23d1a9fc-30d2-4a0f-8631-df8807fc8612  minimal  true
sourcecontrol-webui/840278e2-e1a2-4a30-b904-68538c7cd06f   running        z5  10.30.107.124  vm-0f7300dd-63b5-4399-b1fd-35aeccffac5c  minimal  true

6 vms

Succeeded
```

## <div id="3"/>3.  Management and request for Configuration management service

### <div id="3.1"/> 3.1. Register Service Broker Command

Once the service is installed, a configuration management service broker must be registered to use the service on the PaaS-TA portal.  
When registering a service broker, you must be logged in as a user with authority to register a service broker on an open cloud platform. 

- Check the list of service brokers  
> $ cf service-brokers

```
Getting service brokers as admin...

name   url
No service brokers found
```

- Service broker registering command
```
cf create-service-broker [SERVICE_BROKER] [USERNAME] [PASSWORD] [SERVICE_BROKER_URL]

[SERVICE_BROKER] : Service Broker Name
[USERNAME] / [PASSWORD] : Usser ID / PASSWORD with access to the service broker
[SERVICE_BROKER_URL] : Service Broker Access URL
```

- Register configuration management service broker.

> $ cf create-service-broker paasta-sourcecontrol-broker admin cloudfoundry http://<sourcecontrol-broker_ip>:8080
```
$ cf create-service-broker paasta-sourcecontrol-broker admin cloudfoundry http://10.30.107.126:8080
Creating service broker paasta-sourcecontrol-broker as admin...   
OK       
```

- Check the registered configuration management service broker.  
> $ cf service-brokers

```
Getting service brokers as admin...

name                         url
paasta-sourcecontrol-broker   http://10.30.107.126:8080
```

- Check service access information of configuration management service.  
> $ cf service-access -b paasta-sourcecontrol-broker  

```
Getting service access for broker paasta-sourcecontrol-broker as admin...
broker: paasta-sourcecontrol-broker
   service                  plan      access   orgs
   p-paasta-sourcecontrol   Default   none
```

-Set (all) permission for configuration management service access and reconfirm service access information.  
> $ cf enable-service-access p-paasta-sourcecontrol    
```
Enabling access to all plans of service p-paasta-sourcecontrol for all orgs as admin...
OK
```
> $ cf service-access -b paasta-sourcecontrol-broker   
```
Getting service access for broker paasta-sourcecontrol-broker as admin...
broker: paasta-sourcecontrol-broker
   service                  plan      access   orgs
   p-paasta-sourcecontrol   Default   all  
```

### <div id="3.2"/> 3.2. UAA Client Registration

- Set endpoint of uaac server.

```
# endpoint Setting
$ uaac target https://uaa.<DOMAIN> --skip-ssl-validation

# target Check
$ uaac target
Target: https://uaa.<DOMAIN>
Context: uaa_admin, from client uaa_admin
```

- Log in to uaac.

```
$ uaac token client get <UAA_CLIENT_ADMIN_ID> -s <UAA_CLIENT_ADMIN_SECRET>
Successfully fetched token via client credentials grant.
Target: https://uaa.<DOMAIN>
Context: admin, from client admin
```

- Create a configuration management service account. 
```
### uaac client add Description
uaac client add <CF_UAA_CLIENT_ID> -s <CF_UAA_CLIENT_SECRET> --redirect_uri <Configuration Management Service Dashboard URI> --scope <Permission Range> --authorized_grant_types <Authorization Type> --authorities=<Authorization Permission> --autoapprove=<Automatic Authorization>  

<CF_UAA_CLIENT_ID> : uaac Client id  
<CF_UAA_CLIENT_SECRET> : uaac Client secret  
<Configuration Management Service Dashboard URI> : Configuration Management Services Dashboard URI to be Redirected Successfully 
("http://<source-control-service의 haproxy public IP>:8080, http://<source-control-service의 haproxy public IP>:8080/repositories, http://<source-control-service의 haproxy public IP>:8080/repositories/user")  
<Permission Range> : Permissible range list that the clients can earn on behalf of the users  
<Authorization Type> : Authority list to use APIs provided by service  
<Authorization Permission> : Authority list granted to clients  
<Automatic Authorization> : Authority list that do not require user approval   
```
> $ uaac client add scclient -s clientsecret --redirect_uri "http://[DASHBOARD_URL]:8080 http://[DASHBOARD_URL]:8080/repositories http://[DASHBOARD_URL]:8080/repositories/user" \  
  --scope "cloud_controller_service_permissions.read , openid , cloud_controller.read , cloud_controller.write , cloud_controller.admin" \  
  --authorized_grant_types "authorization_code , client_credentials , refresh_token" \  
  --authorities="uaa.resource" \  
  --autoapprove="openid , cloud_controller_service_permissions.read"
```  
# e.g. Creating a Configuration Management Service Account
$ uaac client add scclient -s clientsecret --redirect_uri "http://115.68.47.179:8080 http://115.68.47.179:8080/repositories http://115.68.47.179:8080/repositories/user" \
  --scope "cloud_controller_service_permissions.read , openid , cloud_controller.read , cloud_controller.write , cloud_controller.admin" \
  --authorized_grant_types "authorization_code , client_credentials , refresh_token" \
  --authorities="uaa.resource" \
  --autoapprove="openid , cloud_controller_service_permissions.read"

# e.g. Configuration Management Service Account Verification
$ uaac clients
scclient
    scope: cloud_controller.read cloud_controller.write cloud_controller_service_permissions.read openid
        cloud_controller.admin
    resource_ids: none
    authorized_grant_types: refresh_token client_credentials authorization_code
    redirect_uri: http://115.68.47.179:8080 http://115.68.47.179:8080/repositories http://115.68.47.179:8080/repositories/user
    autoapprove: cloud_controller_service_permissions.read openid
    authorities: uaa.resource
    name: scclient
    lastmodified: 1542894096080
```  

### <div id='3.3'/> 3.3. Service Request
#### <div id='3.3.1'/> 3.3.1. Service Request - Portal
1. Access the PaaS-TA operator portal and log in.
![3-1-1]

2. Login > Service Management > Check the Configuration Management Service Broker on Service Broker page.
![3-1-2]

3. Service Management > Check the Service Control page for access to the deployment configuration management service plan.
![3-1-3]

4. Operation Management > Catalog > Check the App Service page and click on the "Configuration Management" service name.  
![3-2-1]

- Enter the following information on the detail page.

> ※ Catalog Management > App Service
> - Name : Configuration Management
> - Classification :  Development Support Tools
> - Service : p-paasta-sourcecontrol
> - Thumbnail : [Configuration Management Service Thumbnail]
> - Document URL : https://github.com/PaaS-TA/SOURCE-CONTROL-SERVICE-BROKER
> - Service Creating Parameter : owner
> - Service Creating Parameter : org_name
> - Using App bind : N
> - Public : Y
> - Using Dashboard : Y
> - OnDemand : N
> - Tag : paasta / tag6, free / tag2
> - Outline : Configuration Management
> - Description :
> GIT and SVN repository are provided as configuration management services.  
> Minimum requirements were made by configuration management server and configuration management service broker.
>  
![3-2-2]

- Access to PaaS-TA user portal then request for service through catalog.   

![003]

- Access the service through the dashboard URL.    

![004]  


#### <div id="3.3.2"/>  3.3.2. Service Request  - CLI
Describes how to request for configuration management services through the CLI.

- 4Command for Service Instance Request
```
cf create-service [SERVICE] [PLAN] [SERVICE_INSTANCE]

[SERVICE] : Service name shown at the Marketplace
[PLAN] : Policies for Services
[SERVICE_INSTANCE] : Name of the service instance to create
```

-Request for service for the use of configuration management services. (PaaS-TA user_id, org name setting)
> $ cf create-service p-paasta-sourcecontrol Default paasta-sourcecontrol -c '{"owner":"{user_id}", "org_name":"{org_name}"}'  
```
Creating service instance paasta-sourcecontrol in org system / space dev as admin...
OK
```

- Access the service by checking the service details dashboard URL information.
> $ cf service paasta-sourcecontrol
 ```
 ... (Skip) ...
 Dashboard:        http://115.68.47.179:8080/repositories/user/b840ecb4-15fb-4b35-a9fc-185f42f0de37
 Service broker:   paasta-sourcecontrol-broker
 ... (Skip) ...
 ```
 

[3-1-1]:./images/source-control/adminPortal_login.png
[3-1-2]:./images/source-control/adminPortal_serviceBroker.png
[3-1-3]:./images/source-control/adminPortal_serviceControl.png
[3-2-1]:./images/source-control/adminPortal_catalog.png
[3-2-2]:./images/source-control/adminPortal_catalogDetail.PNG
[003]:./images/source-control/userportal_catalog.png
[004]:./images/source-control/userportal_dashboard.png


### [Index](https://github.com/PaaS-TA/Guide-eng/blob/master/README.md) > [AP Install](../README.md) > Source Control Service
