### [Index](https://github.com/PaaS-TA/Guide-eng/blob/master/README.md) > [AP Install](../README.md) > Pipeline Service

## Table of Contents  

1. [Document Outline](#1)  
  1.1. [Purpose](#1.1)  
  1.2. [Range](#1.2)  
  1.3. [References](#1.3)  
  
2. [Deployment Pipeline Service Installation](#2)  
 2.1. [Prerequisite](#2.1)  
 2.2. [Stemcell Check](#2.2)  
 2.3. [Deployment Download](#2.3)  
 2.4. [Deployment File Modification](#2.4)  
 2.5. [Service Installation](#2.5)    
 2.6. [Service Installation Check](#2.6)  
 2.7. [[Optional] Extended language installation](#2.7)  
    
3. [Manage and request for deployment pipeline services](#3)  
 3.1. [Service Broker Registration](#3.1)  
 3.2. [UAA Client Registration](#3.2)  
 3.3. [Java Offline Buildpack Registration](#3.3)  
 3.4. [Service Application](#3.4)  
　3.4.1. [Service Application - Portal](#3.4.1)   
　3.4.2. [Service Application - CLI](#3.4.2)   


## <div id='1'/> 1. Document Outline

### <div id='1.1'/> 1.1. Range
This document (Distribution Pipeline Service Pack Installation Guide) describes how to install the distribution pipeline service pack, which is a service pack provided by PaaS-TA, using Bosh.  

### <div id='1.2'/> 1.2. Range
The installation range was prepared based on the basic installation to verify the distribution pipeline service pack.


### <div id='1.3'/> 1.3. References
BOSH Document: [http://bosh.io](http://bosh.io)  
Cloud Foundry Document: [https://docs.cloudfoundry.org](https://docs.cloudfoundry.org)  

## <div id='2'/> 2. Deployment Pipeline Service Installation

### <div id='2.1'/> 2.1. Prerequisite

This installation guide is based on installing in a Linux environment  
To install the service pack, BOSH CLI v2 must be installed and logged in to BOSH. 
If BOSH CLI v2 is not installed, you should first refer to the BOSH 2.0 installation guide document to install BOSH CLI v2 and familiarize the usage.  
If the UAA client is not installed, installation of the UAA client is required.

- UAA client Installation (BOSH Dependency Installation Required)
```
$ sudo gem install cf-uaac
$ uaac -v
```

### <div id='2.2'/> 2.2. Stemcell Check

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

### <div id='2.3'/> 2.3. Deployment Download  

Download the deployment needed from Git Repository and place the file in the service installation directory.  

- Service Deployment Git Repository URL : https://github.com/PaaS-TA/service-deployment/tree/v5.1.5

```
# Deployment File Download, make directory, change directory
$ mkdir -p ~/workspace
$ cd ~/workspace

# Deployment File Download
$ git clone https://github.com/PaaS-TA/service-deployment.git -b v5.1.5

# common_vars.yml File Download(Download if common_vars.yml doesn't exist)
$ git clone https://github.com/PaaS-TA/common.git
```

### <div id='2.4'/> 2.4. Deployment File Modification

The BOSH Deployment manifest is a YAML file that defines the properties of components elements and deployments.  
Cloud config is used for network, vm_type, and disk_type used in Deployment files, and refer to the PaaS-TA AP installation guide for the usage  

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
- The variable used in the distribution pipeline is system_domain.

> $ vi ~/workspace/common/common_vars.yml
```
... ((Skip)) ...

system_domain: "61.252.53.246.nip.io"		# Domain (Same as HAProxy Public IP when using nip.io)

... ((Skip)) ...

```

- Modify the variable files used by Deployment YAML to suit the server environment.

> $ vi ~/workspace/service-deployment/pipeline-service/vars.yml

```
# STEMCELL
stemcell_os: "ubuntu-bionic"                                     # stemcell os
stemcell_version: "1.76"                                       # stemcell version

# NETWORK
private_networks_name: "default"                                 # private network name
public_networks_name: "vip"                                      # public network name

# UAAC
pipeline_clinet_id: "pipeclient"                                 # pipeline client id for UAA
pipeline_clinet_secret: "clientsecret"                           # pipeline client password for UAA

# MARIADB
mariadb_port: "13306"                                            # mariadb database port (default : 13306) -- Do Not Use "3306"
mariadb_azs: [z5]                                                # mariadb azs
mariadb_instances: 1                                             # mariadb instances
mariadb_persistent_disk_type: "2GB"                              # mariadb persistent disk type
mariadb_vm_type: "small"                                         # mariadb vm type (e.g. small/medium/large etc)
mariadb_internal_static_ips: "<MARIADB_PRIVATE_IP>"              # mariadb's private IP (e.g. "10.0.161.30")
mariadb_admin_password: "<MARIADB_ADMIN_PASSWORD>"               # mariadb admin password (e.g. "admin!Service")

# POSTGRES
postgres_port: "5532"                                            # postgresql port (default : 5532) -- Do Not Use "5432"
postgres_azs: [z5]                                               # postgresql azs
postgres_instances: 1                                            # postgresql instances
postgres_persistent_disk_type: "2GB"                             # postgresql persistent disk type
postgres_vm_type: "small"                                        # postgresql vm type
postgres_internal_static_ips: "<POSTGRES_PRIVATE_IP>"            # postgresql's private IP (e.g. "10.0.161.31")
postgres_datasource_username: "<POSTGRES_ADMIN_USERNAME>"        # postgresql username (e.g. sonar)
postgres_datasource_password: "<POSTGRES_ADMIN_PASSWORD>"        # postgresql password (e.g. sonar@2020)

# INSPECTION_SERVER
inspection_azs: [z5]                                             # inspection server(SonarQube) azs
inspection_instances: 1                                          # inspection server(SonarQube) instances 
inspection_vm_type: "small"                                      # inspection server(SonarQube) vm type
inspection_internal_static_ips: "<INSPECTION_SERVER_PRIVATE_IP>" # inspection server(SonarQube)'s private IP (e.g. "10.0.161.32")

# HAPROXY
haproxy_azs: [z7]                                                # haproxy azs
haproxy_instances: 1                                             # haproxy instances
haproxy_vm_type: "small"                                         # haproxy vm type
haproxy_internal_static_ips: "<HAPROXY_PRIVATE_IP>"              # haproxy's private IP (e.g. "10.0.0.11")
haproxy_public_static_ips: "<HAPROXY_PUBLIC_IP>"                 # haproxy's public IP

# CI_SERVER
ci_server_azs: [z5]                                                           # ci server(Jenkins) azs
ci_server_instances: 2                                                        # ci server(Jenkins) instances
ci_server_persistent_disk_type: "5GB"                                         # ci server(Jenkins) persistent disk type
ci_server_vm_type: "small"                                                    # ci server(Jenkins) vm type
ci_server_shared_internal_static_ip: "<CI_SERVER_SHARD_PRIVATE_IP>"           # ci server(Jenkins)'s private IP for shared (e.g. "10.0.161.33")
ci_server_dedicated_internal_static_ip: "<CI_SERVER_DEDICATED_PRIVATE_IP>"    # ci server(Jenkins)'s public IP for dedicated (e.g. "10.0.161.34")
ci_server_password: "<CI_SERVER_PASSWORD>"                                    # ci server(Jenkins) password (e.g. "admin!@#")
ci_server_admin_user_username: "<CI_SERVER_ADMIN_USERNAME>"                   # ci server(Jenkins) admin username (e.g. "admin")
ci_server_admin_user_password: "<CI_SERVER_ADMIN_PASSWORD>"                   # ci server(Jenkins) admin password (e.g. "admin!@#")
ci_server_http_url: "<CI_SERVER_HTTP_URL>"                                    # ci server(Jenkins) Enter the first two digits of the internal IP (e.g. If 10.110.10.10, enter "10.110")

# BINARY_STORAGE
binary_storage_azs: [z5]                                           # binary storage azs
binary_storage_instances: 1                                        # binary storage instances
binary_storage_persistent_disk_type: "5GB"                         # binary storage persistent disk type
binary_storage_vm_type: "small"                                    # binary storage vm type
binary_storage_internal_static_ips: "<BINARY_STORAGE_PRIVATE_IP>"  # binary storage's private IP (e.g. "10.0.161.35")
binary_storage_proxy_port: "10008"                                 # binary storage Proxy Server Port(Object Storage access Port) (default : 10008)
binary_storage_auth_port: 15001                                    # binary storage keystone port (e.g. 15001) -- Do Not Use "5000"
binary_storage_username: "paasta-pipeline"                         # binary storage First generated user name(Object Storage Access Username)
binary_storage_password: "paasta-pipeline"                         # binary storage First generated user password(Object Storage Access User Password)
binary_storage_tenantname: "paasta-pipeline"                       # binary storage First generated tenant name(Object Storage Access Tenant Name)
binary_storage_email: "email@email.com"                            # binary storage First generated user email
binary_storage_binary_desc: "paasta-pipeline-object service"       # binary storage Description
binary_storage_container: "delivery-pipeline-container"            # binary storage First generated container Name

# COMMON_API
common_api_port: "8081"                                          # common api port 
common_api_azs: [z5]                                             # common api azs
common_api_instances: 1                                          # common api instances
common_api_vm_type: "small"                                      # common api vm type
common_api_internal_static_ips: "<COMMON_API_PRIVATE_IP>"        # common api's private IP (e.g. "10.0.161.36")

# INSPECTION_API
inspection_api_port: "8083"                                         # inspection api port
inspection_api_azs: [z5]                                            # inspection api azs
inspection_api_instances: 1                                         # inspection api instances
inspection_api_vm_type: "small"                                     # inspection api vm type
inspection_api_internal_static_ips: "<INSPECTION_API_PRIVATE_IP>"   # inspection api's private IP (e.g. "10.0.161.37")

# BINARY_STORAGE_API
storage_api_port: "8080"                                         # storage api port
storage_api_azs: [z5]                                            # storage api azs
storage_api_instances: 1                                         # storage api instances
storage_api_vm_type: "small"                                     # storage api vm type
storage_api_internal_static_ips: "<STORAGE_API_PRIVATE_IP>"      # storage api's private IP (e.g. "10.0.161.38")

# API
api_port: "8082"                                                 # api port 
api_azs: [z5]                                                    # api azs
api_instances: 1                                                 # api instances
api_persistent_disk_type: "2GB"                                  # api persistent disk type
api_vm_type: "small"                                             # api vm type
api_internal_static_ips: "<API_PRIVATE_IP>"                      # api's private IP (e.g. "10.0.161.39")

# SERVICE_BROKER
service_broker_port: "8080"                                       # pipeline service broker port
service_broker_azs: [z5]                                          # pipeline service broker azs
service_broker_instances: 1                                       # pipeline service broker instances
service_broker_persistent_disk_type: "2GB"                        # pipeline service broker persistent disk type
service_broker_vm_type: "small"                                   # pipeline service broker vm type
service_broker_internal_static_ips: "<SERVICE_BROKER_PRIVATE_IP>" # pipeline service broker's private IP (e.g. "10.0.161.40")

# UI(DASHBOARD)
ui_port: "8084"                                                  # ui(dahsboard) port
ui_azs: [z5]                                                     # ui(dahsboard) azs
ui_instances: 1                                                  # ui(dahsboard) instances
ui_vm_type: "small"                                              # ui(dahsboard) vm type
ui_internal_static_ips: "<UI_PRIVATE_IP>"                        # ui(dahsboard)'s private IP (e.g. "10.0.161.41")

# SCHEDULER
scheduler_port: "8080"                                           # scheduler port
scheduler_azs: [z5]                                              # scheduler azs
scheduler_instances: 1                                           # scheduler instances
scheduler_vm_type: "small"                                       # scheduler vm type
scheduler_internal_static_ips: "<SCHEDULER_PRIVATE_IP>"          # scheduler's private IP (e.g. "10.0.161.42")
```

### <div id='2.5'/> 2.5. Service Installation

- Modify the VARIABLES settings in the Deploy script file to suit the server environment, and decide whether to add the option file.  

> $ vi ~/workspace/service-deployment/pipeline-service/deploy.sh

```
#!/bin/bash

# VARIABLES
COMMON_VARS_PATH="<COMMON_VARS_FILE_PATH>"	# common_vars.yml File Path (e.g. ../../common/common_vars.yml)
CURRENT_IAAS="${CURRENT_IAAS}"			# IaaS Information (When not using create-bosh-login.sh provided by PaaS-TA, enter aws/azure/gcp/openstack/vsphere)
BOSH_ENVIRONMENT="${BOSH_ENVIRONMENT}"		# bosh director alias name (When not using create-bosh-login.sh provided by PaaS-TA, check the name from bosh envs and enter)

# DEPLOY
bosh -e ${BOSH_ENVIRONMENT} -n -d pipeline-service deploy --no-redact pipeline-service.yml \
    -o operations/${CURRENT_IAAS}-network.yml \
    -o operations/cce.yml \
    -l ${COMMON_VARS_PATH} \
    -l vars.yml
```

- Install Service.  
```
$ cd ~/workspace/service-deployment/pipeline-service  
$ sh ./deploy.sh  
```


### <div id='2.6'/> 2.6. Service Installation Check

Check the installed service.  

> $ bosh -e micro-bosh -d pipeline-service vms  

```
Using environment '10.0.1.6' as client 'admin'

Task 296077. Done

Deployment 'pipeline-service'

Instance                                                                   Process State  AZ  IPs            VM CID                                VM Type  Active  Stemcell  
binary_storage/63b0c3de-0037-46c7-add7-c7fe54a9ac6c                        running        z5  10.0.161.17    28b7e75b-6fb4-4e3a-8a90-68d20e934441  small    true    -  
ci_server/48d7ffb1-9ac2-42af-915c-9adc7a215656                             running        z5  10.0.161.15    9694f782-acc2-4958-946c-1a21010c6325  small    true    -  
ci_server/de71d530-6b95-482e-a419-32e3c1e64f21                             running        z5  10.0.161.16    c7858b61-2dc1-4878-9e21-5ff5cd07d47b  small    true    -  
delivery-pipeline-api/ea2486ff-6477-4899-9e95-7370ed27efbe                 running        z5  10.0.161.21    ca7c0346-b086-4948-9da1-5410c5eec778  small    true    -  
delivery-pipeline-binary-storage-api/9d70363c-7e42-4d67-a452-b50a21a4e373  running        z5  10.0.161.20    fbc6441e-73e1-41c8-a5f4-c5fc00336936  small    true    -  
delivery-pipeline-common-api/87ce092c-2fc6-4b1b-9921-78a73e191c9e          running        z5  10.0.161.18    23e8e141-c869-449c-873e-f48a49252521  small    true    -  
delivery-pipeline-inspection-api/b8f8d86a-443a-482e-a668-b94624a882fb      running        z5  10.0.161.19    88425e31-68a2-42cd-9431-5a7cc372b9e6  small    true    -  
delivery-pipeline-scheduler/d2c02e3c-e545-47e1-a98a-d81de281a166           running        z5  10.0.161.24    8401e44a-16d5-4639-990c-876655500773  small    true    -  
delivery-pipeline-service-broker/151af074-db1f-4650-b789-477e69c51016      running        z5  10.0.161.22    d1f474ed-48cf-43e3-b7e8-a4cb05c00dbf  small    true    -  
delivery-pipeline-ui/d3ff6d00-93b9-4481-ac07-66cea65322f9                  running        z5  10.0.161.23    c65f56c5-13f8-4c12-8726-295a384b0a63  small    true    -  
haproxy/5a35c6b2-ac18-45cd-9705-a7a401721989                               running        z5  10.0.161.14    a1d70ef6-064d-46b1-b8fc-83ffb08ad82f  small    true    -  
                                                                                              101.55.50.208                                                           
inspection/0a91abe1-b888-4f86-a082-efd6aa9936de                            running        z5  10.0.161.13    5c7a1f2e-b406-44d2-b5fe-2f694c36036c  small    true    -  
mariadb/521553a6-4145-4c5c-9d8f-475db29c5807                               running        z5  10.0.161.11    5476fe5d-a4b2-4b25-8db7-00a0afa30186  small    true    -  
postgres/6a8a4d71-e46f-49ca-b992-407441a90965                              running        z5  10.0.161.12    c87ffcd0-599e-4f07-8d03-3b52d7ae3762  small    true    -  

14 vms

Succeeded
```

### <div id='2.7'/> 2.7. [Optional] Extended Language Installation

- Copy and run extended language scripts to ci_server.   
```
$ cd ~/workspace/service-deployment/pipeline-service/scripts/
$ bosh -d pipeline-service scp php-ci-server-script.sh ci_server/0:/tmp/
$ bosh -d pipeline-service ssh ci_server/0 
$ sudo su
$ mv /tmp/php-ci-server-script.sh /var/vcap/
$ sh /var/vcap/php-ci-server-script.sh
```


- Modify the account information in the script file to suit the server environment. 

> $ vi ~/workspace/service-deployment/pipeline-service/scripts/php-mariadb-script.sh

```
#!/bin/bash
mysql_path=/var/vcap/store/mariadb-10.5.15-linux-x86_64/bin/mysql #Verifying and Modifying Paths
mysql_port='<mysql_port>'           #mariadb port : (e.g. 13306)
mysql_user='<mysql_user>'           #mariadb user : (e.g. root)
mysql_password='<mysql_password>'   #mariadb password 
# insert code table
${mysql_path} -u${mysql_user} -p${mysql_password} -P${mysql_port} -Ddelivery_pipeline  << "EOF"
insert  into `code`(`code_type`,`code_group`,`code_name`,`code_value`,`code_order`) values ('language_type', NULL,'PHP', 'PHP', 2);
insert  into `code`(`code_type`,`code_group`,`code_name`,`code_value`,`code_order`) values ('language_type_version', 'PHP','PHP-7.4', 'PHP-7.4', 2);
insert  into `code`(`code_type`,`code_group`,`code_name`,`code_value`,`code_order`) values ('builder_type', 'PHP','COMPOSER', 'COMPOSER', 2);
insert  into `code`(`code_type`,`code_group`,`code_name`,`code_value`,`code_order`) values ('builder_type_version', 'COMPOSER','COMPOSER-2', 'COMPOSER-2.1.14', 2);
EOF

${mysql_path} -u${mysql_user} -p${mysql_password} -P${mysql_port} -Ddelivery_pipeline -e "select * from code;"

echo 'php data insert complete'
```

- Copy and run extended language scripts to mariadb, and request to pipeline services.  
```
$ cd ~/workspace/service-deployment/pipeline-service/scripts/
$ bosh -d pipeline-service scp php-mariadb-script.sh mariadb/0:/tmp/
$ bosh -d pipeline-service ssh mariadb/0 
$ sudo su
$ mv /tmp/php-mariadb-script.sh /var/vcap/
$ sh /var/vcap/php-mariadb-script.sh
```

## <div id='3'/> 3. Manage and request for deployment pipeline services 
If you register and disclose the distribution pipeline service through the PaaS-TA operator portal, you can request for and use the service through the PaaS-TA user portal.

### <div id='3.1'/> 3.1. Service Broker Registration

Once the deployment of the deployment pipeline service pack has been completed, you must first register the deployment pipeline service broker to use the service pack on the PAAS-TA Portal.
When registering as a service broker, you must be logged in as a user who can register a service broker on an open cloud platform.

- Check the list of service brokers.

> $ cf service-brokers   
```  
Getting service brokers as admin...

name   url
No service brokers found
```  

- Command for registering Service Broker
```
cf create-service-broker [SERVICE_BROKER] [USERNAME] [PASSWORD] [SERVICE_BROKER_URL]

[SERVICE_BROKER] : Service broker name
[USERNAME] / [PASSWORD] : User ID / PASSWORD with access to service broker
[SERVICE_BROKER_URL] : Service Broker Access URL
```

- Register deployment pipeline service broker.

> $ cf create-service-broker delivery-pipeline admin cloudfoundry http://<delivery-pipeline-service-broker_ip>:8080   
```  
$ cf create-service-broker delivery-pipeline-broker admin cloudfoundry http://10.0.161.22:8080
Creating service broker delivery-pipeline-broker as admin...
OK
```  

- Check the registered deployment pipeline service broker.

> $ cf service-brokers  
```  
Getting service brokers as admin...

name                           url
delivery-pipeline-broker       http://10.0.161.22:8080
```  

- Check the list of accessible.
>$ cf service-access  

```
Getting service access as admin...
broker: delivery-pipeline-broker
   service             plan                          access   orgs
   delivery-pipeline   delivery-pipeline-shared      none
   delivery-pipeline   delivery-pipeline-dedicated   none
```
Do not allow access by default when creating a service broker.

- Assign permission to a specific organization to access the service and recheck the access service list. (Overall Organization) 
> $ cf enable-service-access delivery-pipeline   
```
Enabling access to all plans of service delivery-pipeline for all orgs as admin...   
OK
```
> $ cf service-access   
```                          
Getting service access as admin...
broker: delivery-pipeline-broker
   service             plan                          access   orgs
   delivery-pipeline   delivery-pipeline-shared      all
   delivery-pipeline   delivery-pipeline-dedicated   all
```

### <div id='3.2'/> 3.2. UAAC Client Registration
Check the procedure for UAAC Client account registration.

- Register deployment pipeline UAAC Client.
```
### uaac client add Description
uaac client add {Client name} -s {Client Password} --redirect_URL{Dashboard URL} --scope {Permission Range} --authorized_grant_types {Authority Type} --authorities={Authority Permission} --autoapprove={Automatic Authorization}  
Client Name : uaac client name (pipeclient)  
Client Password : uaac Client password  
Dashboard URL: Dashboard URL to be successfully redirected   
Permission Range : Permissible range list that the clients can earn on behalf of the users 
Authority Type : Authority list to use APIs provided by service packs  
Authority Permission : Authority list granted to clients  
Automatic Authorization : Authority list that do not require user approval  
```

>$ uaac client add pipeclient -s clientsecret --redirect_uri "[DASHBOARD_URL]" /  
>--scope "cloud_controller_service_permissions.read , openid , cloud_controller.read , cloud_controller.write , cloud_controller.admin" /  
>--authorized_grant_types "authorization_code , client_credentials , refresh_token" /  
>--authorities="uaa.resource" /  
>--autoapprove="openid , cloud_controller_service_permissions.read"  

```  
### uaac endpoint Setting
$ uaac target https://uaa.<DOMAIN> --skip-ssl-validation

### target Check
$ uaac target
Target: https://uaa.<DOMAIN>
Context: uaa_admin, from client uaa_admin

### uaac Login
$ uaac token client get <UAA_CLIENT_ADMIN_ID> -s <UAA_CLIENT_ADMIN_SECRET>

### Deployment Pipeline uaac client Registration
$ uaac client add pipeclient -s clientsecret --redirect_uri "http://101.55.50.208:8084 http://101.55.50.208:8084/dashboard" \
   --scope "cloud_controller_service_permissions.read , openid , cloud_controller.read , cloud_controller.write , cloud_controller.admin" \
   --authorized_grant_types "authorization_code , client_credentials , refresh_token" \
   --authorities="uaa.resource" \
   --autoapprove="openid , cloud_controller_service_permissions.read"
```  


### <div id='3.3'/> 3.3. Java Offline Buildpack Registration
Register Java Offline Buildpack to use deployment pipeline service.

- Java Offline Buildpack Download 
> wget -O java-buildpack-offline-v4.37.zip https://nextcloud.paas-ta.org/index.php/s/8rGJXEFa8odFDLk/download 

**buildpack Registration**  

> $ cf create-buildpack java_buildpack_offline ..\buildpack\java-buildpack-offline-v4.25.zip 3   

**buildpack Registration Check**  

> $ cf buildpacks 
```
Getting buildpacks...

buildpack                position   enabled   locked   filename
staticfile_buildpack     1          true      false    staticfile_buildpack-cflinuxfs3-v1.4.43.zip
java_buildpack           2          true      false    java-buildpack-cflinuxfs3-v4.19.1.zip
java_buildpack_offline   3          true      false    java-buildpack-offline-v4.25.zip
ruby_buildpack           4          true      false    ruby_buildpack-cflinuxfs3-v1.7.40.zip
dotnet_core_buildpack    5          true      false    dotnet-core_buildpack-cflinuxfs3-v2.2.12.zip
nodejs_buildpack         6          true      false    nodejs_buildpack-cflinuxfs3-v1.6.51.zip
go_buildpack             7          true      false    go_buildpack-cflinuxfs3-v1.8.40.zip
python_buildpack         8          true      false    python_buildpack-cflinuxfs3-v1.6.34.zip
php_buildpack            9          true      false    php_buildpack-cflinuxfs3-v4.3.77.zip
nginx_buildpack          10         true      false    nginx_buildpack-cflinuxfs3-v1.0.13.zip
r_buildpack              11         true      false    r_buildpack-cflinuxfs3-v1.0.10.zip
binary_buildpack         12         true      false    binary_buildpack-cflinuxfs3-v1.0.32.zip
```
※ Reference URL : https://github.com/cloudfoundry/java-buildpack  

  
### <div id='3.4'/> 3.4. Service Application
#### <div id='3.4.1'/> 3.4.1. Service Application - Portal
1. Access the PaaS-TA operator portal and log in.
![3-1-1]

2. Login > Service Management > Check the Deployment Pipeline Service Broker on the Service Broker page.
![3-1-2]

3. Service Management> Check the Service Control page for access to the deployment pipeline service plan.
![3-1-3]

4. Operation Management > Catalog > Check the App Service page and click on the "Pipeline" service name.  
![3-2-1]

- Enter the following information on the detail page.

> ※ Catalog Management > App Service
> - Name : Pipeline
> - Classification :  Development Support Tools
> - Service : delivery-pipeline
> - Thumbnail : [Deployment Pipeline Service Thumbnail]
> - Document URL : https://github.com/PaaS-TA/DELIVERY-PIPELINE-SERVICE-BROKER
> - Service Creating Parameter : owner
> - Using App Bind : N
> - Public : Y
> - Using Dashboard : Y
> - OnDemand : N
> - Tag : paasta / tag6, free / tag2
> - Outline : A pipeline designed for development
> - Description :
> A pipeline designed for development.  
> Minimum requirements were made by Deployment Pipeline Server and Deployment Pipeline Service Broker.
>  
> ![3-2-2]

- Access to PaaS-TA user portal then request for service through catalog.   

![003]

- Access to service through Dashborad URL.    

![004]  


#### <div id='3.4.2'/> 3.4.2. Service Application - CLI
Describes how to request for pipeline service through CLI.

- Command for Requesting Service Instance
```
cf create-service [SERVICE] [PLAN] [SERVICE_INSTANCE]

[SERVICE] : Service Name shown at the Marketplace
[PLAN] : Policies for Services
[SERVICE_INSTANCE] : Name of the service instance to create
```

- Request for pipeline service. (PaaS-TA user_id Setting)
> cf create-service delivery-pipeline delivery-pipeline-shared pipeline-service -c '{"owner":"{user_id}"}'  
```
Creating service instance pipeline-service in org system / space dev as admin...
OK
```

- Access the service by checking the service details dashboard URL information.
> $ cf service pipeline-service
 ```
 ... (Skip) ...
 Dashboard:        http://101.55.50.208:8084/dashboard/2bcbe484-e235-441e-bdb6-ef88f73cb516/
 Service broker:   delivery-pipeline-broker
 ... (Skip) ...
 ```


[1-1-3]:./images/pipeline/Delivery_Pipeline_Architecture.jpg
[3-1-1]:./images/pipeline/adminPortal_login.png
[3-1-2]:./images/pipeline/adminPortal_serviceBroker.png
[3-1-3]:./images/pipeline/adminPortal_serviceControl.png
[3-2-1]:./images/pipeline/adminPortal_catalog.png
[3-2-2]:./images/pipeline/adminPortal_catalogDetail.png
[003]:./images/pipeline/userportal_catalog.PNG
[004]:./images/pipeline/userportal_dashboard.PNG



### [Index](https://github.com/PaaS-TA/Guide-eng/blob/master/README.md) > [AP Install](../README.md) > Pipeline Service
