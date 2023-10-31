### [Index](https://github.com/K-PaaS/Guide-eng/blob/master/README.md) > [AP Install](../README.md) > Pipeline Service

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
This document (Distribution Pipeline Service Pack Installation Guide) describes how to install the distribution pipeline service pack, which is a service pack provided by K-PaaS, using Bosh.  

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
# Example of Stemcell Upload Command
$ bosh -e ${BOSH_ENVIRONMENT} upload-stemcell -n {STEMCELL_URL}
```

### <div id='2.3'/> 2.3. Deployment Download  

Download the deployment needed from Git Repository and place the file in the service installation directory.  

- Service Deployment Git Repository URL : https://github.com/K-PaaS/service-deployment/tree/v5.1.25.1

```
# Deployment File Download, make directory, change directory
$ mkdir -p ~/workspace
$ cd ~/workspace

# Deployment File Download
$ git clone https://github.com/K-PaaS/service-deployment.git -b v5.1.25.1

# common_vars.yml File Download(Download if common_vars.yml doesn't exist)
$ git clone https://github.com/K-PaaS/common.git
```

### <div id='2.4'/> 2.4. Deployment File Modification

The BOSH Deployment manifest is a YAML file that defines the properties of components elements and deployments.  
Cloud config is used for network, vm_type, and disk_type used in Deployment files, and refer to the K-PaaS AP installation guide for the usage  

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
stemcell_os: "ubuntu-jammy"                                     # stemcell os
stemcell_version: "1.181"                                       # stemcell version

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
```

### <div id='2.5'/> 2.5. Service Installation

- Modify the VARIABLES settings in the Deploy script file to suit the server environment, and decide whether to add the option file.  

> $ vi ~/workspace/service-deployment/pipeline-service/deploy.sh

```
#!/bin/bash

# VARIABLES
COMMON_VARS_PATH="<COMMON_VARS_FILE_PATH>"	# common_vars.yml File Path (e.g. ../../common/common_vars.yml)
CURRENT_IAAS="${CURRENT_IAAS}"			# IaaS Information (When not using create-bosh-login.sh provided by K-PaaS, enter aws/azure/gcp/openstack/vsphere)
BOSH_ENVIRONMENT="${BOSH_ENVIRONMENT}"		# bosh director alias name (When not using create-bosh-login.sh provided by K-PaaS, check the name from bosh envs and enter)

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
If you register and disclose the distribution pipeline service through the K-PaaS AP operator portal, you can request for and use the service through the K-PaaS AP user portal.

### <div id='3.1'/> 3.1. Service Broker Registration

Once the deployment of the deployment pipeline service pack has been completed, you must first register the deployment pipeline service broker to use the service pack on the K-PaaS AP Portal.
When registering as a service broker, you must be logged in as a user who can register a service broker on an open cloud platform.

- Check the list of service brokers.

> $ cf service-brokers   
```  
Getting service brokers as admin...

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

broker: delivery-pipeline
   offering   plan                 access   orgs
   pipeline   pipeline-dedicated   none     
   pipeline   pipeline-shared      none   
```
Do not allow access by default when creating a service broker.

- Assign permission to a specific organization to access the service and recheck the access service list. (Overall Organization) 
> $ cf enable-service-access pipeline   
```
Enabling access to all plans of service offering pipeline for all orgs as admin...
OK
```
> $ cf service-access   
```                          
Getting service access as admin...

broker: delivery-pipeline
   offering   plan                 access   orgs
   pipeline   pipeline-dedicated   all      
   pipeline   pipeline-shared      all
```

### <div id='3.2'/> 3.2. UAAC Client Registration
Check the procedure for UAAC Client account registration.

- Register deployment pipeline UAAC Client.
```
### uaac client add Description
uaac client add {Client name} -s {Client Password} --redirect_uri{Dashboard URL} --scope {Permission Range} --authorized_grant_types {Authority Type} --authorities={Authority Permission} --autoapprove={Automatic Authorization}  
Client Name : uaac client name (pipeclient)  
Client Password : uaac Client password  
Dashboard URL: Dashboard URL to be successfully redirected   
Permission Range : Permissible range list that the clients can earn on behalf of the users 
Authority Type : Authority list to use APIs provided by service packs  
Authority Permission : Authority list granted to clients  
Automatic Authorization : Authority list that do not require user approval  
```

>$ uaac client add pipeclient -s clientsecret --redirect_uri "[DASHBOARD_URL]" \  
>--scope "cloud_controller_service_permissions.read , openid , cloud_controller.read , cloud_controller.write , cloud_controller.admin" \  
>--authorized_grant_types "authorization_code , client_credentials , refresh_token" \  
>--authorities="uaa.resource" \    
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
> wget -O java-buildpack-offline-v4.37.zip https://nextcloud.k-paas.org/index.php/s/8rGJXEFa8odFDLk/download 

**buildpack Registration**  

> $ cf create-buildpack java_buildpack_offline java-buildpack-offline-v4.37.zip 3
```
Creating buildpack java_buildpack_offline as admin...
OK
Uploading buildpack java_buildpack_offline as admin...
 794.99 MiB / 794.99 MiB [===================================================================================] 100.00% 7s
OK
Processing uploaded buildpack java_buildpack_offline...
OK
```

**buildpack Registration Check**  

> $ cf buildpacks 
```
Getting buildpacks as admin...
position   name                     stack        enabled   locked   filename
1          java_buildpack           cflinuxfs3   true      false    java-buildpack-cflinuxfs3-v4.50.zip
2          staticfile_buildpack     cflinuxfs3   true      false    staticfile_buildpack-cflinuxfs3-v1.5.32.zip
3          java_buildpack_offline                true      false    java-buildpack-offline-v4.37.zip
4          ruby_buildpack           cflinuxfs3   true      false    ruby_buildpack-cflinuxfs3-v1.8.56.zip
5          dotnet_core_buildpack    cflinuxfs3   true      false    dotnet-core_buildpack-cflinuxfs3-v2.3.44.zip
6          nodejs_buildpack         cflinuxfs3   true      false    nodejs_buildpack-cflinuxfs3-v1.7.72.zip
7          go_buildpack             cflinuxfs3   true      false    go_buildpack-cflinuxfs3-v1.9.48.zip
8          python_buildpack         cflinuxfs3   true      false    python_buildpack-cflinuxfs3-v1.7.56.zip
9          php_buildpack            cflinuxfs3   true      false    php_buildpack-cflinuxfs3-v4.4.64.zip
10         nginx_buildpack          cflinuxfs3   true      false    nginx_buildpack-cflinuxfs3-v1.1.41.zip
11         r_buildpack              cflinuxfs3   true      false    r_buildpack-cflinuxfs3-v1.1.31.zip
12         binary_buildpack         cflinuxfs3   true      false    binary_buildpack-cflinuxfs3-v1.0.45.zip
```
※ Reference URL : https://github.com/cloudfoundry/java-buildpack  

  
### <div id='3.4'/> 3.4. Service Application
#### <div id='3.4.1'/> 3.4.1. Service Application - Portal
1. Access the K-PaaS AP operator portal and log in.
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
> - Document URL : https://github.com/K-PaaS/ap-pipeline-broker
> - Service Creating Parameter : owner
> - Using App Bind : N
> - Public : Y
> - Using Dashboard : Y
> - OnDemand : N
> - Tag : k-paas / tag6, free / tag2
> - Outline : A pipeline designed for development
> - Description :
> A pipeline designed for development.  
> Minimum requirements were made by Deployment Pipeline Server and Deployment Pipeline Service Broker.
>  
![3-2-2]

- Access to K-PaaS AP user portal then request for service through catalog.   

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

- Request for pipeline service. (K-PaaS AP user_id Setting)
> cf create-service pipeline pipeline-shared pipeline-service -c '{"owner":"{user_id}"}'
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


[3-1-1]:./images/pipeline/adminPortal_login.png
[3-1-2]:./images/pipeline/adminPortal_serviceBroker.png
[3-1-3]:./images/pipeline/adminPortal_serviceControl.png
[3-2-1]:./images/pipeline/adminPortal_catalog.png
[3-2-2]:./images/pipeline/adminPortal_catalogDetail.png
[003]:./images/pipeline/userportal_catalog.PNG
[004]:./images/pipeline/userportal_dashboard.PNG



### [Index](https://github.com/K-PaaS/Guide-eng/blob/master/README.md) > [AP Install](../README.md) > Pipeline Service
