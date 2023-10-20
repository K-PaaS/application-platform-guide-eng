### [Index](https://github.com/K-PaaS/Guide-eng/blob/master/README.md) > [AP Install](../README.md) > Portal VM Type API

## Table of Contents  

1. [Document Outline](#1)  
    1.1. [Purpose](#1.1)  
    1.2. [Range](#1.2)  
    1.3. [References](#1.3)  

2. [K-PaaS AP Portal API Installation](#2)  
    2.1. [Prerequisite](#2.1)   
    2.2. [Stemcell Check](#2.2)    
    2.3. [Deployment Download](#2.3)   
    2.4. [Deployment File Modification](#2.4)  
    2.5. [Service Installation](#2.5)    
    2.6. [Service Installation Check](#2.6)  

3. [K-PaaS AP Portal Operation](#3)  
    3.1. [Enable the user's organizational creation flag](#3.1)  
    3.2. [User Portal UAA Page Error](#3.2)  
    3.3. [Operator's Portal User Page inquiry error](#3.3)  
    3.4. [DB Migration](#3.4)  
    3.5. [Log](#3.5)  
    3.6. [Apply Catalog](#3.6)  


## <div id="1"/> 1. Document Outline
### <div id="1.1"/> 1.1. Purpose

This document (K-PaaS AP Portal API Installation Guide) shows how to install K-PaaS AP Portal API using BOSH.

### <div id="1.2"/> 1.2. Range
The installation range was written based on the basic installation of the Portal API to verify the K-PaaS AP Portal.


### <div id="1.3"/> 1.3. References
BOSH Document: [http://bosh.io](http://bosh.io)  
Cloud Foundry Document: [https://docs.cloudfoundry.org](https://docs.cloudfoundry.org)  

## <div id="2"/> 2. K-PaaS AP Portal API Installation

### <div id="2.1"/> 2.1. Prerequisite  
This installation guide is based on installation in a Linux environment.
To install the service pack, BOSH CLI v2 must be installed and logged in to BOSH.<br>
If BOSH CLI v2 is not installed, you must first refer to the BOSH 2.0 Installation Guide document to install BOSH CLI v2 and familiarize yourself with the usage..<br>
If the UAA client is not installed, the UAA client needs to be installed.

- UAA client Installation (BOSH Dependency Installation Required)
```
$ sudo gem install cf-uaac
$ uaac -v
```

### <div id="2.2"/> 2.2. Stemcell Check

Check the list of Stemcells to verify that the Stemcells required for service installation are uploaded.
The Stemcell in this guide uses ubuntu-jammy 1.181.

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

- Portal Deployment Git Repository URL : https://github.com/K-PaaS/portal-deployment/tree/v5.2.23.1

```
# Deployment File Download , make directory, change directory
$ mkdir -p ~/workspace
$ cd ~/workspace

# Deployment File Download
$ git clone https://github.com/K-PaaS/portal-deployment.git -b v5.2.23.1
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

- Modify common_vars.yml to suit server environment.
- The Variables used in K-PaaS AP Portal API are: system_domain, ap_admin_username, ap_admin_password, ap_database_ips, ap_database_port, ap_database_type, ap_database_driver_class, ap_cc_db_id, ap_cc_db_password, ap_uaa_db_id, ap_uaa_db_password, uaa_client_admin_id, uaa_client_admin_secret, monitoring_api_url, portal_web_user_url, and portal_web_user_language.

> $ vi ~/workspace/common/common_vars.yml
```
... ((Skip)) ...

system_domain: "61.252.53.246.nip.io"		# Domain (Same as HAProxy Public IP when using nip.i)
ap_admin_username: "admin"			# Application Platform Admin Username
ap_admin_password: "admin"			# Application Platform Admin Password
ap_database_ips: "10.0.1.123"		# Application Platform Database IP (e.g. "10.0.1.123")
ap_database_port: 5524			# Application Platform Database Port (e.g. 5524(postgresql)/13307(mysql)) -- Do Not Use "3306"&"13306" in mysql
ap_database_type: "postgresql"		# Application Platform Database Type (e.g. "postgresql" or "mysql")
ap_database_driver_class: "org.postgresql.Driver"	# Application Platform Database driver-class (e.g. "org.postgresql.Driver" or "com.mysql.jdbc.Driver")
ap_cc_db_id: "cloud_controller"		# CCDB ID (e.g. "cloud_controller")
ap_cc_db_password: "cc_admin"		# CCDB Password (e.g. "cc_admin")
ap_uaa_db_id: "uaa"				# UAADB ID (e.g. "uaa")
ap_uaa_db_password: "uaa_admin"		# UAADB Password (e.g. "uaa_admin")
uaa_client_admin_id: "admin"			# UAAC Admin Client Admin ID
uaa_client_admin_secret: "admin-secret"		# Secret variables for accessing the UAAC Admin Client
monitoring_api_url: "61.252.53.241"		# Monitoring-WEB의 Public IP
portal_web_user_url: "http://portal-web-user.52.78.88.252.nip.io"
portal_web_user_language: ["ko", "en"]          # portal webuser language list (e.g. ["ko", "en"])

... ((Skip)) ...
```



- Modify the variable files used by Deployment YAML to suit your server environment.

> $ vi ~/workspace/portal-deployment/portal-api/vars.yml

```
# STEMCELL INFO
stemcell_os: "ubuntu-jammy"                                    # stemcell os
stemcell_version: "1.181"                                        # stemcell version

# NETWORKS INFO
private_networks_name: "default"                                # private network name
public_networks_name: "vip"                                     # public network name

# MARIADB INFO
mariadb_azs: [z6]                                               # mariadb : azs
mariadb_instances: 1                                            # mariadb : instances (1)
mariadb_vm_type: "minimal"                                      # mariadb : vm type
mariadb_persistent_disk_type: "10GB"                            # mariadb : persistent disk type
mariadb_port: "<MARIADB_PORT>"                                  # mariadb : database port (e.g. 13306) -- Do Not Use "3306"
mariadb_admin_password: "<MARIADB_ADMIN_PASSWORD>"              # mariadb : database admin password (e.g. "Kpaas@2019")

# HAPROXY INFO
haproxy_azs: [z7]                                               # haproxy : azs
haproxy_instances: 1                                            # haproxy : instances (1)
haproxy_vm_type: "small"                                        # haproxy : vm type
haproxy_public_ips: "<HAPROXY_PUBLIC_IPS>"                      # haproxy : public ips (e.g. "00.00.00.00")
haproxy_infra_admin: false                                      # haproxy : infra admin (default "false")

# BINARY_STORAGE INFO
binary_storage_azs: [z6]                                        # binary storage : azs
binary_storage_instances: 1                                     # binary storage : instances (1)
binary_storage_vm_type: "minimal"                               # binary storage : vm type
binary_storage_persistent_disk_type: "10GB"                     # binary storage : persistent disk type
binary_storage_auth_port: "<BINARY_STORAGE_AUTH_PORT>"          # binary storage : keystone port (e.g. 15001) -- Do Not Use "5000"
binary_storage_username: "<BINARY_STORAGE_USERNAME>"            # binary storage : username (e.g. "kpaas-portal")
binary_storage_password: "<BINARY_STORAGE_PASSWORD>"            # binary storage : password (e.g. "kpaas")
binary_storage_tenantname: "<BINARY_STORAGE_TENANTNAME>"        # binary storage : tenantname (e.g. "kpaas-portal")
binary_storage_email: "<BINARY_STORAGE_EMAIL>"                  # binary storage : email (e.g. "kpaas@kpaas.com")

# PORTAL_GATEWAY INFO
gateway_azs: [z6]                                               # gateway : azs
gateway_instances: 1                                            # gateway : instances (1)
gateway_vm_type: "small"                                        # gateway : vm type

# PORTAL_REGISTRATION INFO
registration_azs: [z6]                                          # registration : azs
registration_instances: 1                                       # registration : instances (1)
registration_vm_type: "small"                                   # registration : vm type
registration_infra_admin: false                                 # registration : infra admin (default "false")

# PORTAL_API INFO
api_azs: [z6]                                                   # portal-api : azs
api_instances: 1                                                # portal-api : instances (1)
api_vm_type: "minimal"                                          # portal-api : vm type
api_infra_admin: false                                          # portal-api : infra admin (default "false")

# PORTAL_COMMON_API INFO
common_api_azs: [z6]                                            # portal-common-api : azs
common_api_instances: 1                                         # portal-common-api : instances (1)
common_api_vm_type: "small"                                     # portal-common-api : vm type
common_api_infra_admin: false                                   # portal-common-api : infra admin (default "false")

# PORTAL_STORAGE_API INFO
storage_api_azs: [z6]                                           # portal-storage-api : azs
storage_api_instances: 1                                        # portal-storage-api : instances (1)
storage_api_vm_type: "small"                                    # portal-storage-api : vm type
storage_api_infra_admin: false                                  # portal-storage-api : infra admin (default "false")

# PORTAL_LOG_API INFO
log_api_azs: [z6]                                               # portal-log-api : azs
log_api_instances: 0                                            # portal-log-api : instances (1)
log_api_vm_type: "small"                                        # portal-log-api : vm type
log_api_infra_admin: false                                      # portal-log-api : infra admin (default "false")

log_api_influxdb_ip: "<LOG_API_INFLUXDB_IP>"                    # portal-log-api : InfluxDB IP
log_api_influxdb_http_port: "8086"                              # portal-log-api : InfluxDB HTTP PORT (default 8086)
log_api_influxdb_username: "<INFLUXDB_USERNAME>"                # portal-log-api : InfluxDB Admin Account Username
log_api_influxdb_password: "<INFLUXDB_PASSWORD>"                # portal-log-api : InfluxDB Admin Account Password
log_api_influxdb_https_enabled: true                            # portal-log-api : InfluxDB HTTPS Setting (default "true")
log_api_influxdb_database: "<INFLUXDB_DATABASE>"                # portal-log-api : InfluxDB Database Name
log_api_influxdb_measurement: "<INFLUXDB_MEASUREMENT>"          # portal-log-api : InfluxDB Measurement Name
log_api_influxdb_query_limit: "<INFLUXDB_QUERY_LIMIT>"          # portal-log-api : InfluxDB query limit (default "50")

# MAIL_SMTP INFO
mail_smtp_host: "<MAIL_SMTP_HOST>"                              # mail-smtp : host (e.g. "smtp.gmail.com")
mail_smtp_port: "<MAIL_SMTP_PORT>"                              # mail-smtp : port (e.g. "465")
mail_smtp_username: "<MAIL_SMTP_USERNAME>"                      # mail-smtp : user name
mail_smtp_password: "<MAIL_SMTP_PASSWORD>"                      # mail-smtp : password
mail_smtp_useremail: "<MAIL_SMTP_USER_EMAIL>"                   # mail-smtp : user email
mail_smtp_properties_auth: "true"                               # mail-smtp : properties auth
mail_smtp_properties_starttls_enable: "true"                    # mail-smtp : properties starttls enable
mail_smtp_properties_starttls_required: "true"                  # mail-smtp : properties starttls required
mail_smtp_properties_subject: "<MAIL_SMTP_PROPERTIES_SUBJECT>"  # mail-smtp : properties subject (e.g. "K-PaaS User Potal")

# ETC INFO
portal_default_security_username: "admin"                       # ETC : spring security username
portal_default_security_password: "openkpaas"                   # ETC : spring security password
portal_default_header_auth: "Basic YWRtaW46b3BlbmtwYWFz"        # ETC : default header auth (spring security id:password(base64))
```

### <div id="2.5"/> 2.5. Service Installation

- Modify the VARIABLES settings in the Deploy script file to match your server environment, and decide whether to add the option file.
  (Optional) -o operations/cce.yml (Apply CCE when Installing)

> $ vi ~/workspace/portal-deployment/portal-api/deploy.sh
```
#!/bin/bash

# VARIABLES
COMMON_VARS_PATH="<COMMON_VARS_FILE_PATH>"              # common_vars.yml File Path (e.g. ../../common/common_vars.yml)
CURRENT_IAAS="${CURRENT_IAAS}"                          # IaaS Information (When not using create-bosh-login.sh provided by K-PaaS enter aws/azure/gcp/openstack/vsphere)
BOSH_ENVIRONMENT="${BOSH_ENVIRONMENT}"                  # bosh director alias name (When not using create-bosh-login.sh provided by K-PaaS, check the name at bosh envs and enter)

# Depending on the number of portal-log-api instances, bifurcate whether to enable or disable the logging service.
LOG_API_INSTANCE_CNT=`grep 'log_api_instances' vars.yml | cut -d ":" -f2 | cut -d "#" -f1`

# DEPLOY
if [[ ${LOG_API_INSTANCE_CNT} -eq 1 ]]; then
  bosh -e ${BOSH_ENVIRONMENT} -n -d portal-api deploy --no-redact portal-api.yml \
     -o operations/${CURRENT_IAAS}-network.yml \
     -o operations/cce.yml \
     -l ${COMMON_VARS_PATH} \
     -l vars.yml
else
  bosh -e ${BOSH_ENVIRONMENT} -n -d portal-api deploy --no-redact portal-api.yml \
     -o operations/disable-logging-service.yml \
     -o operations/${CURRENT_IAAS}-network.yml \
     -o operations/cce.yml \
     -l ${COMMON_VARS_PATH} \
     -l vars.yml
fi
```

- Install Service
```
$ cd ~/workspace/portal-deployment/portal-api   
$ sh ./deploy.sh  
```


### <div id="2.6"/> 2.6. Service Installation Check

Check the installed service.

> $ bosh -e ${BOSH_ENVIRONMENT} -d portal-api vms  

```
Using environment '10.0.1.6' as client 'admin'

Task 4823. Done

Deployment 'portal-api'

Instance                                                          Process State  AZ  IPs            VM CID                                   VM Type        Active  
binary_storage/9f58a9b7-2a3d-4ee9-8975-7b04b99c0a21               running        z5  10.30.107.212  vm-e65ad396-ce65-4ef0-962d-5c54fa411769  portal_large   true  
haproxy/8cc2d633-2b43-4f3d-a2e8-72f5279c11d5                      running        z5  10.30.107.213  vm-315bfa1b-9829-46de-a19d-3bd65e9f9ad4  portal_large   true  
										     115.68.46.214                                                            
mariadb/117cbf05-b223-4133-bf61-e15f16494e21                      running        z5  10.30.107.211  vm-bc5ae334-12d4-41d4-8411-d9315a96a305  portal_large   true  
ap-portal-api/48fa0c5a-52eb-4ae8-a7b9-91275615318c           running        z5  10.30.107.217  vm-9d2a1929-0157-4c77-af5e-707ec496ed87  portal_medium  true  
ap-portal-common-api/060320fa-7f26-4032-a1d9-6a7a41a044a8    running        z5  10.30.107.219  vm-f35e9838-74cf-40e0-9f97-894b53a68d1f  portal_medium  true  
ap-portal-gateway/6baba810-9a4a-479d-98b2-97e5ba651784       running        z5  10.30.107.214  vm-7ec75160-bf34-442e-b755-778ae7dd3fec  portal_medium  true   
ap-portal-registration/3728ed73-451e-4b93-ab9b-c610826c3135  running        z5  10.30.107.215  vm-c4020514-c458-41c6-bcbc-7e0ee1bc6f42  portal_small   true  
ap-portal-storage-api/2940366a-8294-4509-a9c0-811c8140663a   running        z5  10.30.107.220  vm-79ad6ee1-1bb5-4308-8b71-9ed30418e2c1  portal_medium  true  

8 vms

Succeeded
```

## <div id="3"/>3. K-PaaS AP Portal Operatin

### <div id="3.1"/> 3.1. Enable the user's organization creating flag

K-PaaS sets up that ordinary users cannot create an organization. Enable user_org_creation FLAG so that users can create an organization because it is necessary to create an organization and space for portal deployments and to run tests. To activate FLAG, logging in with K-PaaS Admin(Operator) account is required.

```
$ cf enable-feature-flag user_org_creation
```
```
Setting status of user_org_creation as admin...
OK

Feature user_org_creation Enabled.
```

### <div id="3.2"/> 3.2. User portal UAA page error

![portal-1]
1. IF the uaac portal client is not registered, a redirect error occurs as shown on the screen.
2. You must add the portalclient through uaac client add.
    > $ uaac target\
    $ uaac token client get\
        Client ID:  admin\
        Client secret:  *****

3. uaac client add portalclient –s “portalclient Secret”
>--redirect_uri "userportal Url, userportal Url/callback"\
$ uaac client add portalclient -s xxxxx --redirect_uri "http://portal-web-user.xxxx.nip.io, http://portal-web-user.xxxx.nip.io/callback" \
--scope "cloud_controller_service_permissions.read , openid , cloud_controller.read , cloud_controller.write , cloud_controller.admin" \
--authorized_grant_types "authorization_code , client_credentials , refresh_token" \
--authorities="uaa.resource" \
--autoapprove="openid , cloud_controller_service_permissions.read"

![portal-2]
1. IF url is registered incorrectly in the uaac portal client, a redirect error occurs as shown on the screen.
2. The url should be modified through the uaac client update.
   > $ uaac target\
    $ uaac token client get\
   Client ID:  admin\
   Client secret:  *****
3. uaac client update portalclient --redirect_uri "userportal Url, userportal Url/callback"
    
    >$ uaac client update portalclient --redirect_uri "http://portal-web-user.xxxx.nip.io, http://portal-web-user.xxxx.nip.io/callback"

### <div id="3.3"/> 3.3. Operator's Portal User Page inquiry error
1. f the information cannot be retrieved and an error occurs when the page is moved, the DB information config must be modified and restarted after moving to the common-api VM.

### <div id="3.4"/> 3.4. DB Migration
Describes how to migrate the Portal DB used in previous versions to the K-PaaS 3.5 Portal DB.
##### 1. DB tool is used to connect existing DB and K-PaaS AP 3.5 Portal DB.
 * The migration description using the DB tool of the guide is based on navicat.
##### 2. Delete all record data in the table to be migrated.
![portal-9]
##### 3. Tools - Click Data Transfer to display the Migration Settings window.
![portal-10]
##### 4. Set source DB (existing DB) and target DB (K-PaaS AP 3.5 Portal DB) to be migrated.
![portal-11]
##### 5. Go to the option, uncheck the Create tables option in the Table Options, check the Contiune on error in the Order Options, and press next.
![portal-12]
##### 6. Set the table to move the data and press next.
![portal-13]
##### 7-1. Migration completed successfully
![portal-14]
##### 7-2. Migration error 
![portal-15]
##### 7-3. After correcting it according to the design of the K-PaaS AP Portal table that failed in the existing DB, we proceed with the migration again.

### <div id="3.5"/> 3.5. Log
You can check the log of each instance on the K-PaaS AP Portal.
1. Access the Instance to check the logs.
    > bosh ssh -d [deployment name] [instance name]

       Instance                                                          Process State  AZ  IPs            VM CID                                   VM Type        Active  
       binary_storage/9f58a9b7-2a3d-4ee9-8975-7b04b99c0a21               running        z5  10.30.107.212  vm-e65ad396-ce65-4ef0-962d-5c54fa411769  portal_large   true  
       haproxy/8cc2d633-2b43-4f3d-a2e8-72f5279c11d5                      running        z5  10.30.107.213  vm-315bfa1b-9829-46de-a19d-3bd65e9f9ad4  portal_large   true  
                                                                                            115.68.46.214                                                            
       mariadb/117cbf05-b223-4133-bf61-e15f16494e21                      running        z5  10.30.107.211  vm-bc5ae334-12d4-41d4-8411-d9315a96a305  portal_large   true  
       ap-portal-api/48fa0c5a-52eb-4ae8-a7b9-91275615318c                running        z5  10.30.107.217  vm-9d2a1929-0157-4c77-af5e-707ec496ed87  portal_medium  true  
       ap-portal-common-api/060320fa-7f26-4032-a1d9-6a7a41a044a8         running        z5  10.30.107.219  vm-f35e9838-74cf-40e0-9f97-894b53a68d1f  portal_medium  true  
       ap-portal-gateway/6baba810-9a4a-479d-98b2-97e5ba651784            running        z5  10.30.107.214  vm-7ec75160-bf34-442e-b755-778ae7dd3fec  portal_medium  true  
       ap-portal-registration/3728ed73-451e-4b93-ab9b-c610826c3135       running        z5  10.30.107.215  vm-c4020514-c458-41c6-bcbc-7e0ee1bc6f42  portal_small   true  
       ap-portal-storage-api/2940366a-8294-4509-a9c0-811c8140663a        running        z5  10.30.107.220  vm-79ad6ee1-1bb5-4308-8b71-9ed30418e2c1  portal_medium  true  
       
       8 vms

       Succeeded
       inception@inception:~$ bosh ssh -d portal-api ap-portal-api  << Enter the instance access (bosh ssh) command
       Using environment '10.30.40.111' as user 'admin' (openid, bosh.admin)

       Using deployment 'portal-api'

       Task 5195. Done
       Unauthorized use is strictly prohibited. All access and activity
       is subject to logging and monitoring.
       Welcome to Ubuntu 14.04.5 LTS (GNU/Linux 4.4.0-92-generic x86_64)

        * Documentation:  https://help.ubuntu.com/

       The programs included with the Ubuntu system are free software;
       the exact distribution terms for each program are described in the
       individual files in /usr/share/doc/*/copyright.

       Ubuntu comes with ABSOLUTELY NO WARRANTY, to the extent permitted by
       applicable law.

       Last login: Tue Sep  4 07:11:42 2018 from 10.30.20.28
       To run a command as administrator (user "root"), use "sudo <command>".
       See "man sudo_root" for details.

       ap-portal-api/48fa0c5a-52eb-4ae8-a7b9-91275615318c:~$

2. Go to the folder where log file is located.
    > Location : /var/vcap/sys/log/[job name]/

         ap-portal-api/48fa0c5a-52eb-4ae8-a7b9-91275615318c:~$ cd /var/vcap/sys/log/ap-portal-api/
         ap-portal-api/48fa0c5a-52eb-4ae8-a7b9-91275615318c:/var/vcap/sys/log/ap-portal-api$ ls
         ap-portal-api.stderr.log  ap-portal-api.stdout.log

3. Open log file and check the contents.
    > vim [job name].stdout.log

        Ex.)
        vim ap-portal-api.stdout.log
        2018-09-04 02:08:42.447 ERROR 7268 --- [nio-2222-exec-1] p.p.a.e.GlobalControllerExceptionHandler : Error message : Response : org.springframework.security.web.firewall.FirewalledResponse@298a1dc2
        Occured an exception : 403 Access token denied.
        Caused by...
        org.cloudfoundry.client.lib.CloudFoundryException: 403 Access token denied. (error="access_denied", error_description="Access token denied.")
                at org.cloudfoundry.client.lib.oauth2.OauthClient.createToken(OauthClient.java:114)
                at org.cloudfoundry.client.lib.oauth2.OauthClient.init(OauthClient.java:70)
                at org.cloudfoundry.client.lib.rest.CloudControllerClientImpl.initialize(CloudControllerClientImpl.java:187)
                at org.cloudfoundry.client.lib.rest.CloudControllerClientImpl.<init>(CloudControllerClientImpl.java:163)
                at org.cloudfoundry.client.lib.rest.CloudControllerClientFactory.newCloudController(CloudControllerClientFactory.java:69)
                at org.cloudfoundry.client.lib.CloudFoundryClient.<init>(CloudFoundryClient.java:138)
                at org.cloudfoundry.client.lib.CloudFoundryClient.<init>(CloudFoundryClient.java:102)
                at org.openpaas.portal.api.service.LoginService.login(LoginService.java:47)
                at org.openpaas.portal.api.controller.LoginController.login(LoginController.java:51)
                at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
                at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:62)
                at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
                at java.lang.reflect.Method.invoke(Method.java:498)
                at org.springframework.web.method.support.InvocableHandlerMethod.doInvoke(InvocableHandlerMethod.java:205)
                at org.springframework.web.method.support.InvocableHandlerMethod.invokeForRequest(InvocableHandlerMethod.java:133)
                at org.springframework.web.servlet.mvc.method.annotation.ServletInvocableHandlerMethod.invokeAndHandle(ServletInvocableHandlerMethod.java:97)
                at org.springframework.web.servlet.mvc.method.annotation.RequestMappingHandlerAdapter.invokeHandlerMethod(RequestMappingHandlerAdapter.java:827)
                at org.springframework.web.servlet.mvc.method.annotation.RequestMappingHandlerAdapter.handleInternal(RequestMappingHandlerAdapter.java:738)
                at org.springframework.web.servlet.mvc.method.AbstractHandlerMethodAdapter.handle(AbstractHandlerMethodAdapter.java:85)
                at org.springframework.web.servlet.DispatcherServlet.doDispatch(DispatcherServlet.java:967)
                at org.springframework.web.servlet.DispatcherServlet.doService(DispatcherServlet.java:901)
                at org.springframework.web.servlet.FrameworkServlet.processRequest(FrameworkServlet.java:970)
                at org.springframework.web.servlet.FrameworkServlet.doPost(FrameworkServlet.java:872)
                at javax.servlet.http.HttpServlet.service(HttpServlet.java:661)
                at org.springframework.web.servlet.FrameworkServlet.service(FrameworkServlet.java:846)
                at javax.servlet.http.HttpServlet.service(HttpServlet.java:742)
                at org.apache.catalina.core.ApplicationFilterChain.internalDoFilter(ApplicationFilterChain.java:231)
                at org.apache.catalina.core.ApplicationFilterChain.doFilter(ApplicationFilterChain.java:166)
                at org.apache.tomcat.websocket.server.WsFilter.doFilter(WsFilter.java:52)
                at org.apache.catalina.core.ApplicationFilterChain.internalDoFilter(ApplicationFilterChain.java:193)
                at org.apache.catalina.core.ApplicationFilterChain.doFilter(ApplicationFilterChain.java:166)

### <div id="3.6"/> 3.6. Apply Catalog
##### 1. Add Catalog buildpack and servicepack
After installing the K-PaaS AP Portal, you must register the build pack and service pack on the administrator portal to use it on the user portal.

 1. Access the Administrator Portal.(portal-web-admin.[public ip].nip.io)
 ![portal-3]
 2. Click Operation Management.
 ![portal-4]
 3. Go to Catalog page.
 ![portal-5]
 4. Go to the buildpack and servicepack detail page, enter the value in each index, and click Save.
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
[portal-9]:./images/Portal_9.png
[portal-10]:./images/Portal_10.png
[portal-11]:./images/Portal_11.png
[portal-12]:./images/Portal_12.png
[portal-13]:./images/Portal_13.png
[portal-14]:./images/Portal_14.png
[portal-15]:./images/Portal_15.png



### [Index](https://github.com/K-PaaS/Guide-eng/blob/master/README.md) > [AP Install](../README.md) > Portal VM Type API
