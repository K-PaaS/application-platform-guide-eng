### [Index](https://github.com/PaaS-TA/Guide-eng/blob/master/README.md) > [AP Install](../README.md) > Lifecycle Service

## Table of Contents

1. [Document Outline](#1)  
  1.1. [Purpose](#1.1)  
  1.2. [Range](#1.2)   
  1.3. [References](#1.3)   

2. [Lifecycle managing service installation](#2)  
  2.1. [Prerequisite](#2.1)   
  2.2. [Stemcell Check](#2.2)    
  2.3. [Deployment Download](#2.3)   
  2.4. [Deployment File Modification](#2.4)  
  2.5. [Service Installation](#2.5)     
  2.6. [Service Installation Check](#2.6)  
  
3. [Lifecycle managing service management and application](#3)  
 3.1. [Service Broker Registration](#3.1)   
 3.2. [Service Application](#3.2)  
　3.2.1. [Service Application - Portal](#3.2.1)   
　3.2.2. [Service Application - CLI](#3.2.2)   




## <div id="1"/> 1. Document Outline

### <div id="1.1"/> 1.1. Purpose

This document (Lifecycle Management Service Pack Installation Guide) describes how to install Lifecycle Management Service Pack, a service pack provided by PaaS-TA, using Bosh.  

### <div id="1.2"/> 1.2. Range

The installation range was prepared based on the basic installation to verify the lifecycle management service pack.  

### <div id="1.3"/> 1.3. References
BOSH Document: [http://bosh.io](http://bosh.io)  
Cloud Foundry Document: [https://docs.cloudfoundry.org](https://docs.cloudfoundry.org)   
Lifecycle Management Services (TAIGA)References: [https://resources.taiga.io/](https://resources.taiga.io/)

## <div id="2"/> 2. Lifecycle managing service installation  

### <div id="2.1"/> 2.1. Prerequisite 

This installation guide is based on installing in a Linux environment.  
To install the service pack, BOSH CLI v2 must be installed and logged in to BOSH.  
If BOSH CLI v2 is not installed, you should first refer to the BOSH 2.0 installation guide document to install BOSH CLI v2 and familiarize the usage.  

### <div id="2.2"/> 2.2. Stemcell Check  

Check the list of Stemcells to verify that the Stemcells required for service installation are uploaded.  
Stemcell in this guide uses ubuntu-bionic 1.76.  

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

- Service Deployment Git Repository URL : https://github.com/PaaS-TA/service-deployment/tree/v5.1.5

```
# Deployment file download, make directory, change directory
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

- Check Cloud config Settings.   

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
- The variables uesd in Lifecycle are: bosh_url, bosh_client_admin_id, bosh_client_admin_secret, bosh_director_port,and bosh_oauth_port.

> $ vi ~/workspace/common/common_vars.yml
```
... ((Skip)) ...

bosh_url: "https://10.0.1.6"			# BOSH URL (e.g. "https://00.000.0.0")
bosh_client_admin_id: "admin"			# BOSH Client Admin ID
bosh_client_admin_secret: "ert7na4jpew48"	# BOSH Client Admin Secret('echo $(bosh int ~/workspace/paasta-deployment/bosh/{iaas}/creds.yml --path /admin_password)' Can be checked through command)
bosh_director_port: 25555			# BOSH director port
bosh_oauth_port: 8443				# BOSH oauth port

... ((Skip)) ...

```


- Modify the variable file used by Deployment YAML to suit the server environment.

> $ vi ~/workspace/service-deployment/lifecycle-service/vars.yml

```
# STEMCELL
stemcell_os: "ubuntu-bionic"                                                         # stemcell os
stemcell_version: "1.76"                                                           # stemcell version

# VM_TYPE
vm_type_default: "medium"                                                            # vm type default
vm_type_highmem: "small-highmem-16GB"                                                # vm type highmemory

# NETWORK
private_networks_name: "default"                                                     # private network name
public_networks_name: "vip"                                                          # public network name :: The public network name can only use "vip" or "service_public".

# MARIA_DB
mariadb_azs: [z3]                                                                    # mariadb : azs
mariadb_instances: 1                                                                 # mariadb : instances (1) 
mariadb_persistent_disk_type: "10GB"                                                 # mariadb : persistent disk type 
mariadb_port: "<MARIADB_PORT>"                                                       # mariadb : database port (e.g. 31306) -- Do Not Use "3306"
mariadb_admin_password: "<MARIADB_ADMIN_PASSWORD>"                                   # mariadb : database admin password (e.g. "paas-ta!admin")
mariadb_broker_username: "<MARIADB_BROKER_USERNAME>"                                 # mariadb : service-broker-user id (e.g. "applifecycle")
mariadb_broker_password: "<MARIADB_BROKER_PASSWORD>"                                 # mariadb : service-broker-user password (e.g. "broker!admin")

# SERVICE-BROKER
broker_azs: [z3]                                                                     # service-broker : azs
broker_instances: 1                                                                  # service-broker : instances (1)
broker_port: "<SERVICE_BROKER_PORT>"                                                 # service-broker : broker port (e.g. "8080")
broker_logging_level_broker: "INFO"                                                  # service-broker : broker logging level
broker_logging_level_hibernate: "INFO"                                               # service-broker : hibernate logging level
broker_services_id: "<SERVICE_BROKER_SERVICES_GUID>"                                 # service-broker : service guid (e.g. "b988f110-2bc3-46ce-8e55-9b8d50e529d4")
broker_services_plans_id: "<SERVICE_BROKER_SERVICES_PLANS_GUID>"                     # service-broker : service plan id (e.g. "6eb97b3e-91db-4880-ad8a-503003e8e7dd")

# APP-LIFECYCLE
app_lifecycle_azs: [z7]                                                              # app-lifecycle : azs
app_lifecycle_instances: 2                                                           # app-lifecycle : instances (N)
app_lifecycle_persistent_disk_type: "20GB"                                           # app-lifecycle : persistent disk type
app_lifecycle_public_ips: "<APP_LIFECYCLE_PUBLIC_IPS>"                               # app-lifecycle : public ips (e.g. ["00.00.00.00" , "11.11.11.11"])
app_lifecycle_admin_password: "<APP_LIFECYCLE_ADMIN_PASSWORD>"                       # app-lifecycle : app-lifecycle super admin password (e.g. "admin!super")
app_lifecycle_serviceadmin_password: "<APP_LIFECYCLE_SERVICEADMIN_INIT_PASSWORD>"    # app-lifecycle : app-lifecycle serviceadmin user init password (e.g. "Service!admin")
postgres_port: "<APP_LIFECYCLE_POSTGRES_PORT>"                                       # app-lifecycle : app-lifecycle postgres port (e.g. "5524" default:5432)
```

### <div id="2.5"/> 2.5. Service Installation

- Modify the VARIABLES settings in the Deploy script file to suit the server environment, and select whether to add the option file.  
     (Optional) -o operations/cce.yml (Apply CCE when installing)

> $ vi ~/workspace/service-deployment/lifecycle-service/deploy.sh

```
#!/bin/bash

# VARIABLES
COMMON_VARS_PATH="<COMMON_VARS_FILE_PATH>"    # common_vars.yml File Path (e.g. ../../common/common_vars.yml)
CURRENT_IAAS="${CURRENT_IAAS}"                # IaaS Information (PaaS-TA에서 제공되는 create-bosh-login.sh 미 사용시 aws/azure/gcp/openstack/vsphere 입력)
BOSH_ENVIRONMENT="${BOSH_ENVIRONMENT}"        # bosh director alias name (PaaS-TA에서 제공되는 create-bosh-login.sh 미 사용시 bosh envs에서 이름을 확인하여 입력)

# DEPLOY
bosh -e ${BOSH_ENVIRONMENT} -n -d lifecycle-service deploy --no-redact lifecycle-service.yml \
    -o operations/${CURRENT_IAAS}-network.yml \
    -o operations/cce.yml \
    -l ${COMMON_VARS_PATH} \
    -l vars.yml 
```

- Install Service.  
```
$ cd ~/workspace/service-deployment/lifecycle-service
$ sh ./deploy.sh  
```  


### <div id="2.6"/> 2.6. Service Installation Check

Check the installed Service.  

> $ bosh -e micro-bosh -d lifecycle-service vms

```
Using environment '10.0.1.6' as client 'admin'

Task 108. Done

Deployment 'lifecycle-service'

Instance                                             Process State  AZ  IPs           VM CID               VM Type  Active  
app-lifecycle/4a4e1dc8-7214-46ca-9d3a-4254cb784e6f   running        z7  10.0.0.122    i-08fdee7878ea1fd77  medium   true  
                                                                        13.124.4.62                                   
app-lifecycle/a066d71a-0c93-48e3-bddc-0e6dadb1ffcd   running        z7  10.0.0.123    i-044fcd1932afeda19  medium   true  
                                                                        52.78.10.153                                  
mariadb/e859e63c-4358-4ef7-bbeb-93fa19be7baf         running        z3  10.0.81.122   i-0091a0c9848be5277  medium   true  
service-broker/3307c237-d9a9-4885-ae78-007db70a0e22  running        z3  10.0.81.123   i-00c9496182c78d040  medium   true  

4 vms

Succeeded
```

## <div id="3"/>3.  Lifecycle managing service management and application

If you register and disclose the service through the PaaS-TA operator's portal, you can apply for and use the service through the PaaS-TA user's portal.

### <div id="3.1"/> 3.1. Service Broker Registration

Once the service is installed, a service broker must be registered to use the service on the PaaS-TA portal.  
When registering as a service broker, you must be logged in as a user with permission to register a service broker on an open cloud platform.

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

- Register Lifecycle Management Service Broker.  

> $ cf create-service-broker app-lifecycle-service-broker admin cloudfoundry http://<service-broker_ip>:8080
```
### e.g. Register Lifecycle Management Service Broker
$ cf create-service-broker app-lifecycle-service-broker admin cloudfoundry http://10.0.81.123:8080
Creating service broker app-lifecycle-service-broker as admin...  
OK                                                               
```

- Check the registered Lifecycle Management Service Broker.
> $ cf service-brokers

```
Getting service brokers as admin...

name                           url
app-lifecycle-service-broker   http://10.0.81.123:8081
```

- Check service access information of lifecycle management service.
> $ cf service-access -b app-lifecycle-service-broker   

```
Getting service access for broker app-lifecycle-service-broker as admin...
broker: app-lifecycle-service-broker
   service         plan           access   orgs
   app-lifecycle   dedicated-vm   none
```

- Set (full) permission for service access of lifecycle management services and reconfirm service access information.
> $ cf enable-service-access app-lifecycle  
```
Enabling access to all plans of service app-lifecycle for all orgs as admin...
OK
```
> $ cf service-access -b app-lifecycle-service-broker  

```
Getting service access for broker app-lifecycle-service-broker as admin...
broker: app-lifecycle-service-broker
   service         plan           access   orgs
   app-lifecycle   dedicated-vm   all
```

### <div id='3.2'/> 3.2. Service Application
#### <div id='3.2.1'/> 3.2.1. Service Application - Portal

-	Access the PaaS-TA operator portal and register the service.  

> ※ Operation Management > Catalog > App service registration
> - Name : Lifecycle Management Service
> - Classification :  Development Support Tools
> - Service : app-lifecycle
> - Thumbnail : [Lifecycle management service thumbnail]
> - Document URL : https://github.com/PaaS-TA/PAAS-TA-APP-LIFECYCLE-SERVICE-BROKER
> - Service Creation Parameters : password
> - Using App Bind : N
> - Public : Y
> - Using Dashboarf : Y
> - OnDemand : N
> - Tag : paasta / tag1, free / tag2
> - outline : Lifecycle Management Service
> - Description :
> 체계적인 Agile 개발 지원과 프로젝트 협업에 필요한 커뮤니케이션 중심의 문서 및 지식 공유 지원 기능을 제공하는 TAIGA를 dedicated 방식으로 제공합니다.
> 서비스 관리자 계정은 serviceadmin/<서비스 신청 시 입력한 Password> 입니다.
>  
> ![002]

-	PaaS-TA 사용자  포탈에 접속하여, 카탈로그를 통해 서비스를 신청한다.   

![003]

-	대시보드 URL을 통해 서비스에 접근한다.  (서비스의 관리자 계정은 serviceadmin/[서비스 신청시 입력받은 패스워드])  

![004]  

#### <div id='3.2.2'/> 3.2.2. 서비스 신청 - CLI
CLI 를 통한 Lifecycle 서비스 신청 방법을 설명한다.

- 서비스 인스턴스 신청 명령어
```
cf create-service [SERVICE] [PLAN] [SERVICE_INSTANCE]

[SERVICE] : Marketplace에서 보여지는 서비스 명
[PLAN] : 서비스에 대한 정책
[SERVICE_INSTANCE] : 생성할 서비스 인스턴스 이름
```

- Lifecycle 서비스를 신청한다. (password 변수 설정)
> $ cf create-service app-lifecycle dedicated-vm lifecycle -c '{"password":"password"}'
```
Creating service instance app-lifecycle in org system / space dev as admin...
OK
```

- 서비스 상세의 대시보드 URL 정보를 확인하여 서비스에 접근한다.
> $ cf service app-lifecycle
 ```
 ... (생략) ...
dashboard:        http://13.124.4.62/admin/|http://13.124.4.62/discover
service broker:   app-lifecycle-service-broker
 ... (생략) ...
 ```


[001]:./images/applifecycle-service/image001.png
[002]:./images/applifecycle-service/image002.png
[003]:./images/applifecycle-service/image003.png
[004]:./images/applifecycle-service/image004.png
[005]:./images/applifecycle-service/image005.png
[006]:./images/applifecycle-service/image006.png


### [Index](https://github.com/PaaS-TA/Guide-eng/blob/master/README.md) > [AP Install](../README.md) > Lifecycle Service
