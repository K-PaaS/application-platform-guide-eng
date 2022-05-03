### [Index](https://github.com/PaaS-TA/Guide-eng/blob/master/README.md) > [AP Install](../README.md) > Portal VM Type API

## Table of Contents  

1. [Document Outline](#1)  
    1.1. [Purpose](#1.1)  
    1.2. [Range](#1.2)  
    1.3. [References](#1.3)  

2. [PaaS-TA AP Portal API Installation](#2)  
    2.1. [Prerequisite](#2.1)   
    2.2. [Stemcell Check](#2.2)    
    2.3. [Deployment Download](#2.3)   
    2.4. [Deployment File Modification](#2.4)  
    2.5. [Service Installation](#2.5)    
    2.6. [Service Installation Check](#2.6)  

3. [PaaS-TA AP Portal Operation](#3)  
    3.1. [Enable the user's organizational creation flag](#3.1)  
    3.2. [User Portal UAA Page Error](#3.2)  
    3.3. [Operator's Portal User Page inquiry error](#3.3)  
    3.4. [DB Migration](#3.4)  
    3.5. [Log](#3.5)  
    3.6. [Apply Catalog](#3.6)  


## <div id="1"/> 1. Document Outline
### <div id="1.1"/> 1.1. Purpose

This document (PaaS-TA AP Portal API Installation Guide) shows how to install PaaS-TA AP Portal API using BOSH.

### <div id="1.2"/> 1.2. Range
The installation scope was written based on the basic installation of the Portal API to verify the PaaS-TA AP Portal.


### <div id="1.3"/> 1.3. References
BOSH Document: [http://bosh.io](http://bosh.io)  
Cloud Foundry Document: [https://docs.cloudfoundry.org](https://docs.cloudfoundry.org)  

## <div id="2"/> 2. PaaS-TA AP Portal API Installation

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
The Stemcell in this guide uses ubuntu-bionic 1.76.

> $ bosh -e ${BOSH_ENVIRONMENT} stemcells

```
Using environment '10.0.1.6' as client 'admin'

Name                                       Version   OS             CPI  CID  
bosh-openstack-kvm-ubuntu-bionic-go_agent  1.76      ubuntu-bionic  -    ce507ae4-aca6-4a6d-b7c7-220e3f4aaa7d

(*) Currently deployed

1 stemcells

Succeeded
```

If the corresponding Stemcell is not uploaded, copy the corresponding IaaS environment and version stemcell link from [bosh.io Stemcell] (https://bosh.io/stemcells/) and execute the following command.

```
# Example of Stemcell Upload Command
$ bosh -e ${BOSH_ENVIRONMENT} upload-stemcell -n {STEMCELL_URL}
```

### <div id="2.3"/> 2.3. Deployment Download  

Download the deployment needed from Git Repository and place the file at the service installation directory 

- Portal Deployment Git Repository URL : https://github.com/PaaS-TA/portal-deployment/tree/v5.2.5

```
# Deployment File Download , make directory, change directory
$ mkdir -p ~/workspace
$ cd ~/workspace

# Deployment File Download
$ git clone https://github.com/PaaS-TA/portal-deployment.git -b v5.2.5
```

### <div id="2.4"/> 2.4. Deployment File Modification

The BOSH Deployment manifest is a YAML file that defines the properties of the Components element and the deployment.
Network, vm_type, disk_type, etc. used in the deployment file utilize Cloud config, and refer to the PaaS-TA AP installation guide for utilization methods. 

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

- Modify common_vars.yml to suit server environment.
- The Variables used in PaaS-TA AP Portal API are: system_domain, paasta_admin_username, paasta_admin_password, paasta_database_ips, paasta_database_port, paasta_database_type, paasta_database_driver_class, paasta_cc_db_id, paasta_cc_db_password, paasta_uaa_db_id, paasta_uaa_db_password, uaa_client_admin_id, uaa_client_admin_secret, monitoring_api_url, and portal_web_user_url.

> $ vi ~/workspace/common/common_vars.yml
```
... ((Skip)) ...

system_domain: "61.252.53.246.nip.io"		# Domain (Same as HAProxy Public IP when using nip.i)
paasta_admin_username: "admin"			# PaaS-TA Admin Username
paasta_admin_password: "admin"			# PaaS-TA Admin Password
paasta_database_ips: "10.0.1.123"		# PaaS-TA Database IP (e.g. "10.0.1.123")
paasta_database_port: 5524			# PaaS-TA Database Port (e.g. 5524(postgresql)/13307(mysql)) -- Do Not Use "3306"&"13306" in mysql
paasta_database_type: "postgresql"		# PaaS-TA Database Type (e.g. "postgresql" or "mysql")
paasta_database_driver_class: "org.postgresql.Driver"	# PaaS-TA Database driver-class (e.g. "org.postgresql.Driver" or "com.mysql.jdbc.Driver")
paasta_cc_db_id: "cloud_controller"		# CCDB ID (e.g. "cloud_controller")
paasta_cc_db_password: "cc_admin"		# CCDB Password (e.g. "cc_admin")
paasta_uaa_db_id: "uaa"				# UAADB ID (e.g. "uaa")
paasta_uaa_db_password: "uaa_admin"		# UAADB Password (e.g. "uaa_admin")
uaa_client_admin_id: "admin"			# UAAC Admin Client Admin ID
uaa_client_admin_secret: "admin-secret"		# Secret variables for accessing the UAAC Admin Client
monitoring_api_url: "61.252.53.241"		# Monitoring-WEB의 Public IP
portal_web_user_url: "http://portal-web-user.52.78.88.252.nip.io"

... ((Skip)) ...
```



- Modify the variable files used by Deployment YAML to suit your server environment.

> $ vi ~/workspace/portal-deployment/portal-api/vars.yml

```
# STEMCELL INFO
stemcell_os: "ubuntu-bionic"                                    # stemcell os
stemcell_version: "1.76"                                        # stemcell version

# NETWORKS INFO
private_networks_name: "default"                                # private network name
public_networks_name: "vip"                                     # public network name

# MARIADB INFO
mariadb_azs: [z6]                                               # mariadb : azs
mariadb_instances: 1                                            # mariadb : instances (1)
mariadb_vm_type: "minimal"                                      # mariadb : vm type
mariadb_persistent_disk_type: "10GB"                            # mariadb : persistent disk type
mariadb_port: "<MARIADB_PORT>"                                  # mariadb : database port (e.g. 13306) -- Do Not Use "3306"
mariadb_admin_password: "<MARIADB_ADMIN_PASSWORD>"              # mariadb : database admin password (e.g. "Paasta@2019")

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
binary_storage_username: "<BINARY_STORAGE_USERNAME>"            # binary storage : username (e.g. "paasta-portal")
binary_storage_password: "<BINARY_STORAGE_PASSWORD>"            # binary storage : password (e.g. "paasta")
binary_storage_tenantname: "<BINARY_STORAGE_TENANTNAME>"        # binary storage : tenantname (e.g. "paasta-portal")
binary_storage_email: "<BINARY_STORAGE_EMAIL>"                  # binary storage : email (e.g. "paasta@paasta.com")

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
log_api_instances: 1                                            # portal-log-api : instances (1)
log_api_vm_type: "small"                                        # portal-log-api : vm type
log_api_infra_admin: false                                      # portal-log-api : infra admin (default "false")

# MAIL_SMTP INFO
mail_smtp_host: "<MAIL_SMTP_HOST>"                              # mail-smtp : host (e.g. "smtp.gmail.com")
mail_smtp_port: "<MAIL_SMTP_PORT>"                              # mail-smtp : port (e.g. "465")
mail_smtp_username: "<MAIL_SMTP_USERNAME>"                      # mail-smtp : user name
mail_smtp_password: "<MAIL_SMTP_PASSWORD>"                      # mail-smtp : password
mail_smtp_useremail: "<MAIL_SMTP_USER_EMAIL>"                   # mail-smtp : user email
mail_smtp_properties_auth: "true"                               # mail-smtp : properties auth
mail_smtp_properties_starttls_enable: "true"                    # mail-smtp : properties starttls enable
mail_smtp_properties_starttls_required: "true"                  # mail-smtp : properties starttls required
mail_smtp_properties_subject: "<MAIL_SMTP_PROPERTIES_SUBJECT>"  # mail-smtp : properties subject (e.g. "PaaS-TA User Potal")
```

### <div id="2.5"/> 2.5. Service Installation

- Modify the VARIABLES settings in the Deploy script file to match your server environment, and select whether to add the option file.
  (Optional) -o operations/cce.yml (Apply CCE when Installing)

> $ vi ~/workspace/portal-deployment/portal-api/deploy.sh
```
#!/bin/bash

# VARIABLES
COMMON_VARS_PATH="<COMMON_VARS_FILE_PATH>"	# common_vars.yml File Path (e.g. ../../common/common_vars.yml)
CURRENT_IAAS="${CURRENT_IAAS}"			# IaaS Information (When not using create-bosh-login.sh provided by PaaS-TA enter aws/azure/gcp/openstack/vsphere)
BOSH_ENVIRONMENT="${BOSH_ENVIRONMENT}"		# bosh director alias name (When not using create-bosh-login.sh provided by PaaS-TA, check the name at bosh envs and enter)

# DEPLOY
bosh -e ${BOSH_ENVIRONMENT} -n -d portal-api deploy --no-redact portal-api.yml \
   -o operations/${CURRENT_IAAS}-network.yml \
   -o operations/cce.yml \
   -l ${COMMON_VARS_PATH} \
   -l vars.yml
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
paas-ta-portal-api/48fa0c5a-52eb-4ae8-a7b9-91275615318c           running        z5  10.30.107.217  vm-9d2a1929-0157-4c77-af5e-707ec496ed87  portal_medium  true  
paas-ta-portal-common-api/060320fa-7f26-4032-a1d9-6a7a41a044a8    running        z5  10.30.107.219  vm-f35e9838-74cf-40e0-9f97-894b53a68d1f  portal_medium  true  
paas-ta-portal-gateway/6baba810-9a4a-479d-98b2-97e5ba651784       running        z5  10.30.107.214  vm-7ec75160-bf34-442e-b755-778ae7dd3fec  portal_medium  true  
paas-ta-portal-log-api/a4460008-42b5-4ba0-84ee-fff49fe6c1bd       running        z5  10.30.107.218  vm-9ec0a1b0-09f6-415b-8e23-53af91fd94b8  portal_medium  true  
paas-ta-portal-registration/3728ed73-451e-4b93-ab9b-c610826c3135  running        z5  10.30.107.215  vm-c4020514-c458-41c6-bcbc-7e0ee1bc6f42  portal_small   true  
paas-ta-portal-storage-api/2940366a-8294-4509-a9c0-811c8140663a   running        z5  10.30.107.220  vm-79ad6ee1-1bb5-4308-8b71-9ed30418e2c1  portal_medium  true  

9 vms

Succeeded
```

## <div id="3"/>3. PaaS-TA AP Portal Operatin

### <div id="3.1"/> 3.1. Enable the user's organization creating flag

PaaS-TA sets up that ordinary users cannot create an organization. Enable user_org_creation FLAG so that users can create an organization because it is necessary to create an organization and space for portal deployments and to run tests. To activate FLAG, logging in with PaaS-TA Admin(Operator) account is required.

```
$ cf enable-feature-flag user_org_creation
```
```
Setting status of user_org_creation as admin...
OK

Feature user_org_creation Enabled.
```

### <div id="3.2"/> 3.2. User portal UAA page error

>![paas-ta-portal-31]
1. If the uaac portal client is not registered, a redirect error occurs as shown on the screen.
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

 >![paas-ta-portal-32]
1. If url is registered incorrectly in the uaac portal client, a redirect error occurs as shown on the screen.
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
Describes how to migrate the Portal DB used in previous versions to the PaaS-TA 3.5 Portal DB.
##### 1. DB tool is used to connect existing DB and Paas-TA 3.5 Portal DB.
 * The migration description using the DB tool of the guide is based on navicat.
##### 2. Delete all record data in the table to be migrated.
>![paas-ta-portal-25]
##### 3. Tools - Click Data Transfer to display the Migration Settings window.
>![paas-ta-portal-21]
##### 4. Set source DB (existing DB) and target DB (Paas-TA 3.5 Portal DB) to be migrated.
>![paas-ta-portal-20]
##### 5. Go to the option, uncheck the Create tables option in the Table Options, check the Contiune on error in the Order Options, and press next.
>![paas-ta-portal-24]
##### 6. Set the table to move the data and press next.
>![paas-ta-portal-22]
##### 7-1. Migration completed successfully
>![paas-ta-portal-23]
##### 7-2. Migration error 
>![paas-ta-portal-26]
##### 7-3. After correcting it according to the design of the Paas-TA Portal table that failed in the existing DB, we proceed with the migration again.

### <div id="3.5"/> 3.5. Log
You can check the log of each instance on the Paas-TA Portal.
1. Access the Instance to check the logs.
    > bosh ssh -d [deployment name] [instance name]

       Instance                                                          Process State  AZ  IPs            VM CID                                   VM Type        Active  
       binary_storage/9f58a9b7-2a3d-4ee9-8975-7b04b99c0a21               running        z5  10.30.107.212  vm-e65ad396-ce65-4ef0-962d-5c54fa411769  portal_large   true  
       haproxy/8cc2d633-2b43-4f3d-a2e8-72f5279c11d5                      running        z5  10.30.107.213  vm-315bfa1b-9829-46de-a19d-3bd65e9f9ad4  portal_large   true  
                                                                                            115.68.46.214                                                            
       mariadb/117cbf05-b223-4133-bf61-e15f16494e21                      running        z5  10.30.107.211  vm-bc5ae334-12d4-41d4-8411-d9315a96a305  portal_large   true  
       paas-ta-portal-api/48fa0c5a-52eb-4ae8-a7b9-91275615318c           running        z5  10.30.107.217  vm-9d2a1929-0157-4c77-af5e-707ec496ed87  portal_medium  true  
       paas-ta-portal-common-api/060320fa-7f26-4032-a1d9-6a7a41a044a8    running        z5  10.30.107.219  vm-f35e9838-74cf-40e0-9f97-894b53a68d1f  portal_medium  true  
       paas-ta-portal-gateway/6baba810-9a4a-479d-98b2-97e5ba651784       running        z5  10.30.107.214  vm-7ec75160-bf34-442e-b755-778ae7dd3fec  portal_medium  true  
       paas-ta-portal-log-api/a4460008-42b5-4ba0-84ee-fff49fe6c1bd       running        z5  10.30.107.218  vm-9ec0a1b0-09f6-415b-8e23-53af91fd94b8  portal_medium  true  
       paas-ta-portal-registration/3728ed73-451e-4b93-ab9b-c610826c3135  running        z5  10.30.107.215  vm-c4020514-c458-41c6-bcbc-7e0ee1bc6f42  portal_small   true  
       paas-ta-portal-storage-api/2940366a-8294-4509-a9c0-811c8140663a   running        z5  10.30.107.220  vm-79ad6ee1-1bb5-4308-8b71-9ed30418e2c1  portal_medium  true  
       paas-ta-portal-webadmin/8047fcbd-9a98-4b61-b161-0cbb277fa643      running        z5  10.30.107.221  vm-188250fd-e918-4aab-9cbe-7d368852ea8a  portal_medium  true  
       paas-ta-portal-webuser/cb206717-81c9-49ed-a0a8-e6c3b957cb66       running        z5  10.30.107.222  vm-822f68a5-91c8-453a-b9b3-c1bbb388e377  portal_medium  true  

       11 vms

       Succeeded
       inception@inception:~$ bosh ssh -d paas-ta-portal-v2 paas-ta-portal-api  << Enter the instance access (bosh ssh) command
       Using environment '10.30.40.111' as user 'admin' (openid, bosh.admin)

       Using deployment 'paas-ta-portal-v2'

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

       paas-ta-portal-api/48fa0c5a-52eb-4ae8-a7b9-91275615318c:~$

2. Go to the folder where log file is located.
    > Location : /var/vcap/sys/log/[job name]/

         paas-ta-portal-api/48fa0c5a-52eb-4ae8-a7b9-91275615318c:~$ cd /var/vcap/sys/log/paas-ta-portal-api/
         paas-ta-portal-api/48fa0c5a-52eb-4ae8-a7b9-91275615318c:/var/vcap/sys/log/paas-ta-portal-api$ ls
         paas-ta-portal-api.stderr.log  paas-ta-portal-api.stdout.log

3. Open log file and check the contents.
    > vim [job name].stdout.log

        Ex.)
        vim paas-ta-portal-api.stdout.log
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
                at org.openpaas.paasta.portal.api.service.LoginService.login(LoginService.java:47)
                at org.openpaas.paasta.portal.api.controller.LoginController.login(LoginController.java:51)
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
After installing the Paas-TA Portal, you must register the build pack and service pack on the administrator portal to use it on the user portal.

 1. Access the Administrator Portal.(portal-web-admin.[public ip].nip.io)
    
    >![paas-ta-portal-15]
 2. Press Operation Management.
    
    >![paas-ta-portal-16]
 2. Go to Catalog page.
    
    >![paas-ta-portal-17]
 3. Go to the buildpack and servicepack detail page, enter the value in each index, and click Save.
    
    >![paas-ta-portal-18]
 4. Check whether the changed value is applied in the user portal.
    
    >![paas-ta-portal-19]

[paas-ta-portal-01]:./images/Paas-TA-Portal_01.png
[paas-ta-portal-02]:./images/Paas-TA-Portal_02.png
[paas-ta-portal-03]:./images/Paas-TA-Portal_03.png
[paas-ta-portal-04]:./images/Paas-TA-Portal_04.png
[paas-ta-portal-05]:./images/Paas-TA-Portal_05.png
[paas-ta-portal-06]:./images/Paas-TA-Portal_06.png
[paas-ta-portal-07]:./images/Paas-TA-Portal_07.png
[paas-ta-portal-08]:./images/Paas-TA-Portal_08.png
[paas-ta-portal-09]:./images/Paas-TA-Portal_09.png
[paas-ta-portal-10]:./images/Paas-TA-Portal_10.png
[paas-ta-portal-11]:./images/Paas-TA-Portal_11.png
[paas-ta-portal-12]:./images/Paas-TA-Portal_12.png
[paas-ta-portal-13]:./images/Paas-TA-Portal_13.png
[paas-ta-portal-14]:./images/Paas-TA-Portal_14.png
[paas-ta-portal-15]:./images/Paas-TA-Portal_15.png
[paas-ta-portal-16]:./images/Paas-TA-Portal_16.png
[paas-ta-portal-17]:./images/Paas-TA-Portal_17.png
[paas-ta-portal-18]:./images/Paas-TA-Portal_18.png
[paas-ta-portal-19]:./images/Paas-TA-Portal_19.png
[paas-ta-portal-20]:./images/Paas-TA-Portal_20.png
[paas-ta-portal-21]:./images/Paas-TA-Portal_21.png
[paas-ta-portal-22]:./images/Paas-TA-Portal_22.png
[paas-ta-portal-23]:./images/Paas-TA-Portal_23.png
[paas-ta-portal-24]:./images/Paas-TA-Portal_24.png
[paas-ta-portal-25]:./images/Paas-TA-Portal_25.png
[paas-ta-portal-26]:./images/Paas-TA-Portal_26.png
[paas-ta-portal-27]:./images/Paas-TA-Portal_27.PNG
[paas-ta-portal-28]:./images/Paas-TA-Portal_28.PNG
[paas-ta-portal-29]:./images/Paas-TA-Portal_29.png
[paas-ta-portal-31]:./images/Paas-TA-Portal_27.jpg
[paas-ta-portal-32]:./images/Paas-TA-Portal_28.jpg


### [Index](https://github.com/PaaS-TA/Guide-eng/blob/master/README.md) > [AP Install](../README.md) > Portal VM Type API
