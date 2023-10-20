### [Index](https://github.com/K-PaaS/Guide-eng/blob/master/README.md) > [AP Install](../README.md) > Portal Container Type

## Table of Contents  

1. [Document Outline](#1)  
    1.1. [Purpose](#1.1)  
    1.2. [Range](#1.2)  
    1.3. [References](#1.3)  

2. [K-PaaS AP Portal infra Installation](#2)  
    2.1. [Prerequisite](#2.1)   
    2.2. [Stemcell Check](#2.2)    
    2.3. [Deployment Download](#2.3)   
    2.4. [Deployment File Modification](#2.4)  
    2.5. [Service Installation](#2.5)    
    2.6. [Service Installation Check](#2.6)  

3. [K-PaaS AP Portal Installation](#3)  
    3.1. [Portal App Configuration](#3.1)  
    3.2. [Portal Set App Deployment Script Variables](#3.2)  
    3.3. [Portal Run App Deployment Script](#3.3)  

4. [K-PaaS AP Portal Operation](#4)  
    4.1. [Enable the user's organizational creation flag](#4.1)  
    4.2. [User Portal UAA Page Error](#4.2)  
    4.3. [Apply Catalog](#4.3)  


## <div id="1"/> 1. Document Outline
### <div id="1.1"/> 1.1. Purpose

This document (K-PaaS AP Portal Container Type Installation Guide) describes how to install K-PaaS AP Portal using BOSH and K-PaaS AP.

### <div id="1.2"/> 1.2. Range
The installation range was created based on the installation of the Portal infrastructure and the distribution of the Portal App to verify the K-PaaS AP Portal.

### <div id="1.3"/> 1.3. References
BOSH Document: [http://bosh.io](http://bosh.io)  
Cloud Foundry Document: [https://docs.cloudfoundry.org](https://docs.cloudfoundry.org)  

## <div id="2"/> 2. K-PaaS AP Portal infra Installation  

### <div id="2.1"/> 2.1. Prerequisite
This installation guide is based on installation in a Linux environment.
To install the service pack, BOSH CLI v2 must be installed and logged in to BOSH.
If BOSH CLI v2 is not installed, you must first refer to the BOSH 2.0 Installation Guide document to install BOSH CLI v2 and familiarize yourself with the usage.
If the UAA client is not installed, the UAA client needs to be installed.

- UAA client Installation (BOSH Dependency Installation Required)
```
$ sudo gem install cf-uaac
$ uaac -v
```

### <div id="2.2"/> 2.2. Stemcell Check
Check the list of Stemcells to verify that the Stemcells required for service installation are uploaded.
The Stemcell in this guide uses ubuntu-jammy  1..181.

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

- Portal Deployment Git Repository URL : https://github.com/K-PaaS/portal-deployment/tree/v5.2.23

```
# Deployment File Download , make directory, change directory
$ mkdir -p ~/workspace
$ cd ~/workspace

# Deployment File Download
$ git clone https://github.com/K-PaaS/portal-deployment.git -b v5.2.23
```

### <div id="2.4"/> 2.4. Deployment File Modification
The BOSH Deployment manifest is a YAML file that defines the properties of the Components element and the deployment. 
Network, vm_type, disk_type, etc. used in the deployment file utilize Cloud config, and refer to the K-PaaS AP installation guide for utilization methods.

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

- Modify common_vars.yml to suit your server environment.
- The variable used by K-PaaS AP Portal infrastructure is system_domain, portal_web_user_language, and portal_web_admin_language.

> $ vi ~/workspace/common/common_vars.yml
```
... ((Skip)) ...

system_domain: "61.252.53.246.nip.io"		        # Domain (Same as HAProxy Public IP when using nip.io)
portal_web_user_language: ["ko", "en"]              # portal webuser language list (e.g. ["ko", "en"])
portal_web_admin_language: ["ko", "en"]             # portal webadmin language list (e.g. ["ko", "en"])

... ((Skip)) ...
```


- Modify the variable files used by Deployment YAML to suit your server environment.

> $ vi ~/workspace/portal-deployment/portal-container-infra/vars.yml

```
# STEMCELL INFO
stemcell_os: "ubuntu-jammy"                                    # stemcell os
stemcell_version: "1.181"                                        # stemcell version

# NETWORKS INFO
private_networks_name: "default"                                # private network name

# PORTAL-INFRA INFO
infra_azs: [z3]                                                 # infra : azs
infra_instances: 1                                              # infra : instances (1)
infra_vm_type: "large"                                          # infra : vm type
infra_persistent_disk_type: "20GB"                              # infra : persistent disk type


# MARIADB INFO
mariadb_port: "<MARIADB_PORT>"                                  # mariadb : database port (e.g. 13306) -- Do Not Use "3306"
mariadb_admin_password: "<MARIADB_ADMIN_PASSWORD>"              # mariadb : database admin password (e.g. "Kpass@2019")
portal_default_api_name: "K-PaaS"                              # portal default api name (e.g. K-PaaS {Version})
portal_default_api_url: "http://<PORTAL_GATEWAY_ROUTE>"         # portal default api url (portal gateway url) (e.g. "http://portal-gateway.<DOMAIN>")
portal_default_header_auth: "Basic YWRtaW46b3BlbmtwYWFz"       # portal default header auth
portal_default_api_desc: "K-PaaS infra"                        # portal default api description (e.g. K-PaaS {Version} infra)


# BINARY_STORAGE INFO
binary_storage_auth_port: "<BINARY_STORAGE_AUTH_PORT>"          # binary storage : keystone port (e.g. 15001) -- Do Not Use "5000"
binary_storage_username: "<BINARY_STORAGE_USERNAME>"            # binary storage : username (e.g. "kpaas-portal")
binary_storage_password: "<BINARY_STORAGE_PASSWORD>"            # binary storage : password (e.g. "kpaas")
binary_storage_tenantname: "<BINARY_STORAGE_TENANTNAME>"        # binary storage : tenantname (e.g. "kpaas-portal")
binary_storage_email: "<BINARY_STORAGE_EMAIL>"                  # binary storage : email (e.g. "kpaas@kpaas.com")
```

### <div id="2.5"/> 2.5. Service Installation

- Modify the VARIABLES settings in the Deploy script file to match your server environment, and decide whether to add the option file.
     (Optional) -o operations/cce.yml (Apply CCE when installing)

> $ vi ~/workspace/portal-deployment/portal-container-infra/deploy.sh
```
#!/bin/bash

# VARIABLES
COMMON_VARS_PATH="<COMMON_VARS_FILE_PATH>"  # common_vars.yml File Path (e.g. ../../common/common_vars.yml)
BOSH_ENVIRONMENT="${BOSH_ENVIRONMENT}"      # bosh director alias name (When not using create-bosh-login.sh provided by K-PaaS, check the name at bosh envs and enter)

# DEPLOY
bosh -e ${BOSH_ENVIRONMENT} -n -d portal-container-infra deploy --no-redact portal-container-infra.yml \
   -o operations/cce.yml \
   -l ${COMMON_VARS_PATH} \
   -l vars.yml
```

- Install Service  
```
$ cd ~/workspace/portal-deployment/portal-container-infra    
$ sh ./deploy.sh  
```


### <div id="2.6"/> 2.6. Service Installation Check
Check the installed service.

> $ bosh -e ${BOSH_ENVIRONMENT} -d portal-container-infra vms  

```
Using environment '10.0.1.6' as client 'admin'

Task 1246. Done

Deployment 'portal-container-infra'

Instance                                    Process State  AZ  IPs          VM CID               VM Type  Active  
infra/3193a1fa-156d-4dd9-935d-4b67cdcc1182  running        z3  10.0.81.121  i-09ec0c2e7f3594683  large    true  

1 vms

Succeeded
```

## <div id="3"/> 3. K-PaaS AP Portal Installation
### <div id="3.1"/> 3.1. Portal App Configuration
8 Portal-related apps are deployed on K-PaaS AP, and the configuration are as follows.
```
portal-app-1.2.14
├── portal-api-2.4.3
├── portal-common-api-2.2.6
├── portal-gateway-2.1.2
├── portal-registration-2.1.0
├── portal-ssh-1.0.0
├── portal-storage-api-2.2.1
├── portal-web-admin-2.3.5
└── portal-web-user-2.4.10
```
### <div id="3.2"/> 3.2. Portal App Deployment Script Variable Settings
Navigate to the location where the script is located to run the Portal App deployment script.

```
### Change Installation Directory
$ cd ~/workspace/portal-deployment/portal-container-infra/scripts
```

Set the Script variable. (FILE PATH Partial Change Required, Remaining Options)
> $ vi portal-app-variable.yml
```
#!/bin/bash

##FILE PATH
COMMON_VARS_PATH=~/workspace/common/common_vars.yml	# common_vars.yml Path
PORTAL_INFRA_VARS_PATH=~/workspace/portal-deployment/portal-container-infra/vars.yml	# portal_infra_vars.yml Path
PORTAL_APP_WORKING_DIRECTORY=~/workspace/portal-deployment/portal-container-infra/portal-app # Portal APP Working Path


##PORTAL VARIABLE
USER_APP_SIZE_MB=0                      # USER My App size(MB), if value==0 -> unlimited
MONITORING_ENABLE=false                 # Monitoring Enable Option
SSH_ENABLE=true                         # SSH Enable Option
TAIL_LOG_INTERVAL=250                   # tail log interval (ms)

PORTAL_ORG_NAME="portal"                # Application Platform Portal Org Name
PORTAL_SPACE_NAME="system"              # Application Platform Portal Space Name
PORTAL_QUOTA_NAME="portal_quota"        # Application Platform Portal Quota Name
PORTAL_SECURITY_GROUP_NAME="portal"     # Application Platform Portal Security Group Name

MAIL_SMTP_HOST="smtp.gmail.com"         # Mail-SMTP Host
MAIL_SMTP_PORT="465"                    # Mail-SMTP Port
MAIL_SMTP_USERNAME="kpaas"              # Mail-SMTP User Name
MAIL_SMTP_PASSWORD="kpaas"              # Mail-SMTP Password
MAIL_SMTP_USEREMAIL="kpaas@gmail.com"   # Mail-SMTP User Email

SPRING_SECURITY_USERNAME="admin"        # Spring Security Username
SPRING_SECURITY_PASSWORD="openkpaas"    # Spring Security Password

##PORTAL APP INSTANCES
PORTAL_API_INSTANCE=1                   # PORTAL-API INSTANCES
PORTAL_COMMON_API_INSTANCE=1            # PORTAL-COMMON-API INSTANCES
PORTAL_GATEWAY_INSTANCE=1               # PORTAL-GATEWAY INSTANCES
PORTAL_REGISTRATION_INSTANCE=1          # PORTAL-REGISTRATION INSTANCES
PORTAL_STORAGE_API_INSTANCE=1           # PORTAL-STORAGE-API INSTANCES
PORTAL_WEB_ADMIN_INSTANCE=1             # PORTAL-WEB-ADMIN INSTANCES
PORTAL_WEB_USER_INSTANCE=1              # PORTAL-WEB-USER INSTANCES


##UNCHANGE VARIABLE(if defulat install, don't change variable)
AP_DEPLOYMENT_TYPE="ap"                 # Application Platform Deployment Type
AP_CORE_DEPLOYMENT_NAME="ap"            # Application Platform Deployment Name
PORTAL_INFRA_DEPLOYMENT_NAME="portal-container-infra"   # Portal Container Infra Deployment Name
AP_DATABASE_INSTANCE_NAME="database"    # Application Platform Database Instance Name
PORTAL_INFRA_DATABASE_NAME="infra"      # Portal Container Infra Database Name
UAA_ADMIN_CLIENT_ID="admin"             # UAA Client ID
CC_DB_NAME="cloud_controller"           # Application Platform CCDB Name
CC_DB_USER_NAME="cloud_controller"      # Application Platform CCDB ID
UAA_DB_NAME="uaa"                       # Application Platform UAADB Name
UAA_DB_USER_NAME="uaa"                  # Application Platform UAADB ID
UAAC_PORTAL_CLIENT_ID="portalclient"    # UAAC Portal Client ID

IS_AP_EXTERNAL_DB=false                 # (true or false)
AP_EXTERNAL_DB_IP=                      # Application Platform External DB IP
AP_EXTERNAL_DB_PORT=                    # Application Platform External DB Port
AP_EXTERNAL_DB_KIND=                    # Application Platform External DB Kind(IF USE e.g. postgres or mysql)
IS_PORTAL_EXTERNAL_DB=false             # (true or false)
PORTAL_EXTERNAL_DB_IP=                  # Portal External DB IP
PORTAL_EXTERNAL_DB_PORT=                # Portal External DB Port
PORTAL_EXTERNAL_DB_PASSWORD=            # Portal External DB Password
IS_PORTAL_EXTERNAL_STORAGE=false        # (true or false)
PORTAL_EXTERNAL_STORAGE_IP=             # Portal External Storage IP
PORTAL_EXTERNAL_STORAGE_PORT=           # Portal External Storage Port
PORTAL_EXTERNAL_STORAGE_TENANTNAME=     # Portal External Storage Tenant Name
PORTAL_EXTERNAL_STORAGE_USERNAME=       # Portal External Storage Username
PORTAL_EXTERNAL_STORAGE_PASSWORD=       # Portal External Storage Password

USE_LOGGING_SERVICE=false               # (true or false)
LOGGING_INFLUXDB_IP=10.0.1.115          # Logging Service InfluxDB IP
LOGGING_INFLUXDB_PORT=8086              # Logging Service InfluxDB HTTP PORT
LOGGING_INFLUXDB_USERNAME="admin"       # Logging Service InfluxDB Username
LOGGING_INFLUXDB_PASSWORD="K-PaaS2022"  # Logging Service InfluxDB Password
LOGGING_INFLUXDB_DATABASE="logging_db"  # Logging Service InfluxDB DB Name
LOGGING_INFLUXDB_MEASUREMNET="logging_measurement"      # Logging Service InfluxDB Measurement Name
LOGGING_INFLUXDB_LIMIT=50               # Logging Service InfluxDB query limit
LOGGING_INFLUXDB_HTTPS_ENABLED=false    # (true or false)
```


### <div id="3.3"/> 3.3. Run the Portal App Deployment Script
When the variable setting is complete, run the deployment script.
> $ source deploy-portal-app.sh
```
.....
.....

name                  requested state   processes           routes
portal-api            started           web:1/1, task:0/0   portal-api.61.252.53.246.nip.io
portal-common-api     started           web:1/1, task:0/0   portal-common-api.61.252.53.246.nip.io
portal-gateway        started           web:1/1, task:0/0   portal-gateway.61.252.53.246.nip.io
portal-registration   started           web:1/1, task:0/0   portal-registration.61.252.53.246.nip.io
portal-storage-api    started           web:1/1, task:0/0   portal-storage-api.61.252.53.246.nip.io
portal-web-admin      started           web:1/1, task:0/0   portal-web-admin.61.252.53.246.nip.io
portal-web-user       started           web:1/1             portal-web-user.61.252.53.246.nip.io
ssh-app               started           web:1/1             ssh-app.61.252.53.246.nip.io
```



## <div id="4"/>4. K-PaaS AP Portal Operation

### <div id="4.1"/> 4.1. Enable the user's organizational creation flag

K-PaaS sets up that ordinary users cannot create an organization. Enable user_org_creation FLAG so that users can create an organization because it is necessary to create an organization and space for portal deployments and to run tests. To activate FLAG, logging in with K-PaaS Admin(Operator) account is required.

```
$ cf enable-feature-flag user_org_creation
```
```
Setting status of user_org_creation as admin...
OK

Feature user_org_creation Enabled.
```

### <div id="4.2"/> 4.2. Error on user portal UAA page

- Set the endpoint of the uaac and run the uaac login.
```
# endpoint Setting
$ uaac target https://uaa.<DOMAIN> --skip-ssl-validation

# target check
$ uaac target
Target: https://uaa.<DOMAIN>
Context: uaa_admin, from client uaa_admin

# uaac login
$ uaac token client get <UAA_CLIENT_ADMIN_ID> -s <UAA_CLIENT_ADMIN_SECRET>
Successfully fetched token via client credentials grant.
Target: https://uaa.<DOMAIN>
Context: admin, from client admin
```
- redirect error - portalclient not registered 
![portal-1]  
1. If the uaac portal client is not registered, a redirect error occurs as shown on the screen.
2. You must add the portalclient through uaac client add.
> $ uaac client add <PORTAL_UAA_CLIENT_ID> -s <PORTAL_UAA_CLIENT_SECRET> --redirect_uri <PORTAL_WEB_USER_URI>, <PORTAL_WEB_USER_URI>/callback --scope   "cloud_controller_service_permissions.read , openid , cloud_controller.read , cloud_controller.write , cloud_controller.admin" --authorized_grant_types "authorization_code , client_credentials , refresh_token" --authorities="uaa.resource" --autoapprove="openid , cloud_controller_service_permissions.read"  

```
# e.g. Create a portal client account

$ uaac client add portalclient -s clientsecret --redirect_uri "http://portal-web-user.<DOMAIN>, http://portal-web-user.<DOMAIN>/callback" \
--scope "cloud_controller_service_permissions.read , openid , cloud_controller.read , cloud_controller.write , cloud_controller.admin" \
--authorized_grant_types "authorization_code , client_credentials , refresh_token" \
--authorities="uaa.resource" \
--autoapprove="openid , cloud_controller_service_permissions.read"
```

- redirectError - Error registering redirect_uri in the portalclient
![portal-2]  
1. If uri is registered incorrectly in the uaac portal client, a redirect error occurs as shown on that screen.
2. The uri should be modified through the uaac client update.
> $ uaac client update portalclient --redirect_uri "<PORTAL_WEB_USER_URI>, <PORTAL_WEB_USER_URI>/callback"   

```
#  e.g. portal client redirect_uri update
$ uaac client update portalclient --redirect_uri "http://portal-web-user.<DOMAIN>, http://portal-web-user.<DOMAIN>/callback"
```

### <div id="4.3"/> 4.3. Apply Catalog
##### 1. Add Catalog buildpack and servicepack
After installing the K-PaaS AP Portal, you must register the build pack and service pack on the administrator portal to use it on the user portal.
- [Catalog Image Download](https://nextcloud.k-paas.org/index.php/s/EmzfJw38H4GQKTr/download)

1. Access the Administrator Portal.(portal-web-admin.\<DOMAIN\>)  
![portal-3]
2. Click Operation Management.
![portal-4]
3. Go to the catalog page.
![portal-5]
4. Go to the build pack and service pack detail screen, enter the value in each item column, and click Save.
![portal-6]
   ※ The catalog management code is required when registering and modifying catalogs. When there is no code available as of the moment, follow the instruction below.
   1. ① Click "Manage Code".
   2. From the **Group Table**, click the corresponding ② "Code Category".
   3. Click the "Register" button to add the catalog management code to the **Detail Table** and use it.
   ![portal-7]

5. Check whether the changed value is applied in the user portal.
![portal-8]   

[portal-1]:./images/Portal_1.jpg
[portal-2]:./images/Portal_2.jpg
[portal-3]:./images/Portal_3.png
[portal-4]:./images/Portal_4.png
[portal-5]:./images/Portal_5.png
[portal-6]:./images/Portal_6.png
[portal-7]:./images/Portal_7.png
[portal-8]:./images/Portal_8.png



### [Index](https://github.com/K-PaaS/Guide-eng/blob/master/README.md) > [AP Install](../README.md) > Portal Container Type
