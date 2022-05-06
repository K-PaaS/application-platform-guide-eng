### [Index](https://github.com/PaaS-TA/Guide-eng/blob/master/README.md) > [AP Install](../README.md) > WEB IDE Service

## Table of Contents
1. [Document Outline](#1)  
  1.1. [Purpose](#1.1)  
  1.2. [Range](#1.2)    
  1.3. [References](#1.3)  

2. [WEB IDE Installation](#2)  
  2.1. [Prerequisite](#2.1)   
  2.2. [Stemcell Check](#2.2)    
  2.3. [Deployment Download](#2.3)   
  2.4. [Deployment File Modification](#2.4)  
  2.5. [Service Installation](#2.5)    
  2.6. [Service Installation Check](#2.6)  

3. [Web-IDE's PaaS-TA Portal Site Interworking](#3)  
 3.1. [WEB-IDE Service Broker Registration](#3.1)  
 3.2. [Application for service](#3.2)  
　3.2.1. [Application for service - Portal](#3.2.1)   
　3.2.2. [Application for service - CLI](#3.2.2)   

4. [Use guide of CF CLI in WEB-IDE](#4)  
  4.1. [WEB-IDE New Project Screen](#4.1)  
  4.2. [WEB-IDE Workspace Screen](#4.2)  
  4.3. [Run the CF CLI on the WEB-IDE Terminal](#4.3)  

5. [WEB IDE IP Expansion](#5)  
  5.1. [Service Check](#5.1)   
  5.2. [Deployment File Modification](#5.2)   
  5.3. [Service reinstallation](#5.3)    
  5.4. [Service Installation Check](#5.4)    


## <div id="1"/> 1. Document Outline

### <div id='1.1'/>1.1. Purpose

This document (WEB-IDE service pack installation guide) describes how to install the WEB-IDE service pack, which is a service pack provided by PaaS-TA, using Bosh.

### <div id='1.2'/> 1.2. Range
The installation range was prepared based on the basic installation to verify the use of WEB-IDE.


### <div id='1.3'/>1.3. References
BOSH Document: [http://bosh.io](http://bosh.io)  
Cloud Foundry Document: [https://docs.cloudfoundry.org](https://docs.cloudfoundry.org)  
Eclipse Che Technology: [https://www.eclipse.org/che/technology/](https://www.eclipse.org/che/technology/)  


## <div id='2'/> 2. WEB IDE Installation  

### <div id="2.1"/> 2.1. Prerequisite 

This installation guide is based on installing in a Linux environment. 
In order to install the service pack, BOSH CLI v2 must be installed and logged in to BOSH.  
If BOSH CLI v2 is not installed, you should first refer to the BOSH 2.0 installation guide document to install BOSH CLI v2 and familiarize with the usage.

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

Download the deployment needed from Git Repository and place the file at the service installation directory.  

- Service Deployment Git Repository URL : https://github.com/PaaS-TA/service-deployment/tree/v5.1.5

```
# Deployment File Download , make directory, change directory
$ mkdir -p ~/workspace
$ cd ~/workspace

# Deployment File Download
$ git clone https://github.com/PaaS-TA/service-deployment.git -b v5.1.5

# common_vars.yml File Download(Download if common_vars.yml doesn't exist)
$ git clone https://github.com/PaaS-TA/common.git
```

### <div id="2.4"/> 2.4. Deployment File Modification

The BOSH Deployment manifest is a YAML file that defines the properties of components elements and deployments.
Cloud config is used for network, vm_type, and disk_type used in Deployment files, and refer to the PaaS-TA AP installation guide the usage.  

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
- The Variables used in WEB IDE are: bosh_url, bosh_client_admin_id, bosh_client_admin_secret, bosh_director_port,  bosh_oauth_port, bosh_version, system_domain, paasta_admin_username, paasta_admin_password.

> $ vi ~/workspace/common/common_vars.yml
```
... ((Skip)) ...

bosh_url: "https://10.0.1.6"			# BOSH URL (e.g. "https://00.000.0.0")
bosh_client_admin_id: "admin"			# BOSH Client Admin ID
bosh_client_admin_secret: "ert7na4jpew"		# BOSH Client Admin Secret('echo $(bosh int ~/workspace/paasta-deployment/bosh/{iaas}/creds.yml --path /admin_password)' command can be used to check)
bosh_director_port: 25555			# BOSH director port
bosh_oauth_port: 8443				# BOSH oauth port
bosh_version: 271.2				# BOSH version(can be checked through 'bosh env' command, on-demand service용, e.g. "271.2")
system_domain: "61.252.53.246.nip.io"		# Domain (Same as HAProxy Public IP when using nip.io)
paasta_admin_username: "admin"			# PaaS-TA Admin Username
paasta_admin_password: "admin"			# PaaS-TA Admin Password

... ((Skip)) ...

```



- Modify the variable files used by Deployment YAML to suit the server environment.

> $ vi ~/workspace/service-deployment/web-ide/vars.yml

```
deployment_name: "web-ide"                                                # Service Deployed Name

# STEMCELL
stemcell_os: "ubuntu-bionic"                                              # stemcell os
stemcell_version: "1.76"                                                  # stemcell version
stemcell_alias: "default"                                                 # stemcell alias

# NETWORK
private_networks_name: "default"                                          # private network name
public_networks_name: "vip"                                               # public network name

# ECLIPSE-CHE
eclipse_che_azs: [z7]                                                     # eclipse-che : azs
eclipse_che_instances: 0                                                  # eclipse-che : instances (default : 0)
eclipse_che_vm_type: "large"                                              # eclipse-che : vm type
eclipse_che_public_ips: "<ECLIPSE_CHE_PUBLIC_IPS>"                        # eclipse-che : public ips (e.g. ["00.00.00.00" , "11.11.11.11"], At initial deployment [])
eclipse_che_buffer_ips: "<ECLIPSE_CHE_BUFFER_IPS>"                        # eclipse-che : Extra public ips for On Demand


# MARIA_DB
mariadb_azs: [z4]                                                         # mariadb : azs
mariadb_instances: 1                                                      # mariadb : instances (1) 
mariadb_vm_type: "small"                                                  # mariadb : vm type
mariadb_persistent_disk_type: "10GB"                                      # mariadb : persistent disk type
mariadb_port: "<MARIADB_PORT>"                                            # mariadb : database port (e.g. 31306) -- Do Not Use "3306"
mariadb_admin_password: "<MARIADB_ADMIN_PASSWORD>"                        # mariadb : database admin password (e.g. "Paasta@2021")

# SERVICE-BROKER
broker_azs: [z4]                                                          # service-broker : azs
broker_instances: 1                                                       # service-broker : instances (1)
broker_vm_type: "medium"                                                  # service-broker : vm type
broker_port: "<BROKER_PORT>"                                              # service-broker : broker port (e.g. "8080")
serviceDefinition_id: "<SERVICE_GUID>"                                    # serviceDefinition_id : service guid (e.g. "af86588c-6212-11e7-907b-b6006ad3webide0")
serviceDefinition_name: "WEB IDE"
serviceDefinition_plan1_id: "<SERVICE_PLAN_ID>"                           # serviceDefinition_plan1_id : service plan id (e.g. "a5930564-6212-11e7-907b-b6006ad3webide1")
serviceDefinition_plan1_name: "<SERVICE_PLAN_NAME>"                       # serviceDefinition_plan1_name : service plan name (e.g. "dedicated-vm")
serviceDefinition_plan1_desc: "WEB IDE SERVICE"
serviceDefinition_bullet_name: "Web IDE OnDemand Server Use"
serviceDefinition_bullet_desc: "Web IDE Service Using a OnDemand Server"
serviceDefinition_org_limitation: "-1"                                    # serviceDefinition_org_limitation : Restrictions Service for Organizations
serviceDefinition_space_limitation: "-1"                                  # serviceDefinition_space_limitation : Restrictions Service for Spacae

# CF
cloudfoundry_sslSkipValidation: "true"
```

### <div id="2.5"/> 2.5. Service Installation

- Modify the VARIABLES settings in the Deploy script file to suit the server environment.  
  (Optional) -o operations/cce.yml (Apply CCE when installing) 

> $ vi ~/workspace/service-deployment/web-ide/deploy.sh

```
#!/bin/bash
  
# VARIABLES
COMMON_VARS_PATH="<COMMON_VARS_FILE_PATH>"       # common_vars.yml File Path (e.g. ../../common/common_vars.yml)
CURRENT_IAAS="${CURRENT_IAAS}"					 # IaaS Information (PaaS-TA에서 제공되는 create-bosh-login.sh 미 사용시 aws/azure/gcp/openstack/vsphere 입력)
BOSH_ENVIRONMENT="${BOSH_ENVIRONMENT}"			 # bosh director alias name (PaaS-TA에서 제공되는 create-bosh-login.sh 미 사용시 bosh envs에서 이름을 확인하여 입력)

# DEPLOY
bosh -e ${BOSH_NAME} -n -d web-ide deploy --no-redact web-ide.yml \
    -o operations/${IAAS}-network.yml \
    -o operations/cce.yml \
    -l ${COMMON_VARS_PATH} \
    -l vars.yml      
```

- 서비스를 설치한다.  
```
$ cd ~/workspace/service-deployment/web-ide
$ sh ./deploy.sh  
```  


### <div id="2.6"/> 2.6. 서비스 설치 확인

설치 완료된 서비스를 확인한다.  

> $ bosh -e micro-bosh -d web-ide vms

```
Using environment '10.30.40.111' as user 'admin' (openid, bosh.admin)

Task 7872. Done

Deployment 'web-ide'

Instance                                            Process State  AZ  IPs            VM CID                                   VM Type  Active
mariadb/ec34aa5b-c7cc-4297-9e2d-babf05d83832        running        z3  10.30.56.55    vm-9e1631af-b6c8-481e-aad3-3fd713f106a9  small    true
webide-broker/a641df99-d36a-49ee-8329-018fe10fa23d  running        z3  10.30.56.56    vm-eb784964-48cd-4e4c-b080-53675d3738c2  medium   true

2 vms

Succeeded
```



## <div id='3'/> 3. WEB-IDE의 PaaS-TA 포털사이트 연동

### <div id='3.1'/> 3.1. WEB-IDE 서비스 브로커 등록

- 서비스 브로커 등록 명령어
```
cf create-service-broker [SERVICE_BROKER] [USERNAME] [PASSWORD] [SERVICE_BROKER_URL]

[SERVICE_BROKER] : 서비스 브로커 명
[USERNAME] / [PASSWORD] : 서비스 브로커에 접근할 수 있는 사용자 ID / PASSWORD
[SERVICE_BROKER_URL] : 서비스 브로커 접근 URL
```

- web-ide 서비스 브로커를 등록한다.
> $ cf create-service-broker webide-service-broker admin cloudfoundry http://<webide-broker_ip>:8080
```
$ cf create-service-broker webide-service-broker admin cloudfoundry http://10.30.56.56:8080
Creating service broker webide-service-broker as admin...
OK
```
<br>

- 등록된 WEB-IDE 서비스 브로커를 확인한다.
> $ cf service-brokers  
```
Getting service brokers as admin...

name                          url
webide-service-broker         http://10.30.56.56:8080
```
<br>

- 접근 가능한 서비스 목록을 확인한다.
> $ cf service-access
```
Getting service access as admin...
broker: webide-service-broker
   offering   plan           access   orgs
   webide     dedicated-vm   none      
```
<br>
서비스 브로커 등록시 최초에는 접근을 허용하지 않는다. 따라서 access는 none으로 설정된다.

- 특정 조직에 해당 서비스 접근 허용을 할당하고 접근 서비스 목록을 다시 확인한다. (전체 조직)
> $ cf enable-service-access webide <br>
```
Enabling access to all plans of service webide for all orgs as admin...
OK
```
> $ cf service-access 
```
Getting service access as admin...

broker: webide-service-broker
   offering   plan           access   orgs
   webide     dedicated-vm   all      
```
<br>

### <div id='3.2'/> 3.2. 서비스 신청
#### <div id='3.2.1'/> 3.2.1. 서비스 신청 - 포탈

1. 운영자 포털에 접속하여 운영관리 > 카탈로그 > 앱서비스를 클릭하여 앱 서비스 등록을 클릭한다..  

- 아래의 내용을 상세 페이지에 입력한다.

> ※ 카탈로그 관리 > 앱 서비스
> - 이름 : WEB IDE
> - 분류 :  개발 지원 도구
> - 서비스 : webide
> - 썸네일 : [WEB IDE 서비스 썸네일]
> - 문서 URL : https://github.com/PaaS-TA/PAAS-TA-WEB-IDE-BROKER
> - 앱 바인드 사용 : N
> - 공개 : Y
> - 대시보드 사용 : Y
> - 온디멘드 : N
> - 태그 : paasta / tag6, free / tag2
> - 요약 : WEB IDE
> - 설명 :
> 웹 프로그래밍을 위한 WEB IDE - eclipse-che
>  
> ![3-2-2]

- PaaS-TA 사용자 포탈에 접속하여, 카탈로그를 통해 서비스를 신청한다.   

![003]

- 대시보드 URL을 통해 서비스에 접근한다.    

![004]  


#### <div id="3.2.2"/>  3.2.2. 서비스 신청 - CLI
CLI 를 통한 WEB-IDE 서비스 신청 방법을 설명한다.

- PaaS-TA Marketplace에서 서비스가 있는지 확인을 한다.

> $ cf marketplace
```
Getting services from marketplace in org system / space dev as admin...
OK

offering   plans          description                                                                 broker
webide     dedicated-vm   A paasta web ide service for application development.provision parameters   webide-service-broker
```
<br>

- 서비스 인스턴스 신청 명령어
```
cf create-service [SERVICE] [PLAN] [SERVICE_INSTANCE]

[SERVICE] : Marketplace에서 보여지는 서비스 명
[PLAN] : 서비스에 대한 정책
[SERVICE_INSTANCE] : 생성할 서비스 인스턴스 이름
```

- Marketplace에서 원하는 서비스가 있으면 서비스 신청(Provision)을 한다.

> $ cf create-service webide dedicated-vm webide-service  
```
Creating service instance paasta-webide-service in org system / space dev as admin...
OK

Create in progress. Use 'cf services' or 'cf service webide' to check operation status.
```
<br>

- 생성된 WEB-IDE VM 인스턴스를 확인한다.

> bosh -e micro-bosh -d web-ide vms  
```
Using environment '10.30.40.111' as user 'admin' (openid, bosh.admin)

Task 7872. Done

Deployment 'web-ide'

Instance                                            Process State  AZ  IPs            VM CID                                   VM Type  Active
eclipse-che/ed136540-c650-47a2-918b-bb7f6020469d    running        z7  10.30.56.54    vm-5a3a2b10-d0c9-47c8-97f0-6ea64c339df8  large    true
							               115.68.46.178
mariadb/ec34aa5b-c7cc-4297-9e2d-babf05d83832        running        z3  10.30.56.55    vm-9e1631af-b6c8-481e-aad3-3fd713f106a9  small    true
webide-broker/a641df99-d36a-49ee-8329-018fe10fa23d  running        z3  10.30.56.56    vm-eb784964-48cd-4e4c-b080-53675d3738c2  medium   true

3 vms

Succeeded
```
<br>

- 생성된 WEB-IDE 서비스 인스턴스를 확인한다.

> $ cf service webide-service
```
 ... (생략) ...
 Dashboard:        http://115.68.46.178:8080
 Service broker:   webide-service-broker
 ... (생략) ...
```
<br>









## <div id='4'/> 4. WEB-IDE 에서 CF CLI 사용법

### <div id='4.1'/> 4.1. WEB-IDE New Project 화면
***※ [PaaS-TA 운영자 포탈 4.3.3 카탈로그 관리 서비스 가이드](/use-guide/portal/PAAS-TA_ADMIN_PORTAL_USE_GUIDE_V1.1.md#4.3.3) 참고***  

- 사용할 언어를 선택하고 Create workspace and project 로 새로운 프로젝트를 시작한다.

![](./images/webide/web-ide-08-1.png)

<br>


### <div id='4.2'/> 4.2. WEB-IDE Workspace 화면

- WEB-IDE를 처음 생성 시 Workspace를 새로 생성하는 화면이 열리고 프로젝트를 설정에 맞게 생성하여 작업을 진행한다.

![](./images/webide/web-ide-on-02.jpeg)

![](./images/webide/web-ide-on-03.jpeg)

![](./images/webide/web-ide-on-04.jpeg)

<br>

### <div id='4.3'/> 4.3. WEB-IDE Teminal에서의 CF CLI 실행

- -cf api 명령을 이용해 endpoint를 지정한다.

> ![](./images/webide/web-ide-12.png)

- cf login 명령어로 로그인하고 조직과 공간을 선택한다.

> ![](./images/webide/web-ide-13.png)

- cf push 를 이용해 cf에 앱을 업로드한다.

> ![](./images/webide/web-ide-14.png)




## <div id='5'/> 2. WEB IDE IP 증설
### <div id="5.1"/> 5.1. 서비스 확인

현재 생성된 WEB-IDE VM 인스턴스를 확인한다.

> bosh -e micro-bosh -d web-ide vms  
```
Using environment '10.30.40.111' as user 'admin' (openid, bosh.admin)

Task 7872. Done

Deployment 'web-ide'

Instance                                            Process State  AZ  IPs            VM CID                                   VM Type  Active
eclipse-che/ed136540-c650-47a2-918b-bb7f6020469d    running        z7  10.30.56.54    vm-5a3a2b10-d0c9-47c8-97f0-6ea64c339df8  large    true
							               115.68.46.178
mariadb/ec34aa5b-c7cc-4297-9e2d-babf05d83832        running        z3  10.30.56.55    vm-9e1631af-b6c8-481e-aad3-3fd713f106a9  small    true
webide-broker/a641df99-d36a-49ee-8329-018fe10fa23d  running        z3  10.30.56.56    vm-eb784964-48cd-4e4c-b080-53675d3738c2  medium   true

3 vms

Succeeded
```
<br>



### <div id="5.2"/> 5.2. Deployment 파일 수정

기존 설치할때 사용했던 Deployment YAML에서 eclipse_che_instances의 값을 배포된 eclipse-che의 수만큼 변경을 해주고 eclipse_che_public_ips에 설치된 public ip를 입력한다.  
그리고 WEB-IDE에 추가시킬 IP를 eclipse_che_buffer_ips에 추가한다.

> $ vi ~/workspace/service-deployment/web-ide/vars.yml

```
.....

# ECLIPSE-CHE
eclipse_che_azs: [z7]                                                   # eclipse-che : azs
eclipse_che_instances: 1                                                # eclipse-che : instances (1), ondemand service default 0
eclipse_che_vm_type: "large"                                            # eclipse-che : vm type
eclipse_che_public_ips: ["115.68.46.178"]                               # eclipse-che : public ips (e.g. ["00.00.00.00" , "11.11.11.11"])
eclipse_che_buffer_ips: ["115.68.46.178", "52.153.36.143"]              # eclipse-che : OnDemand 에서 사용할 여분의 public ips
eclipse_che_instance_name: "eclipse-che"                                # eclipse-che : 작업 이름

........

```


### <div id="5.3"/> 5.3. 서비스 재 설치

- 서비스를 재 설치한다.  
```
$ cd ~/workspace/service-deployment/web-ide
$ sh ./deploy.sh  

Using environment '10.0.1.6' as client 'admin'

Using deployment 'web-ide'

Release 'paas-ta-webide-release/2.0' already exists.

  instance_groups:
  - name: webide-broker
    properties:
      network:
        static_ips:
+       - 52.153.36.143
Task 581

Task 581 | 02:20:43 | Preparing deployment: Preparing deployment (00:00:02)
Task 581 | 02:20:45 | Preparing deployment: Rendering templates (00:00:01)
Task 581 | 02:20:46 | Preparing package compilation: Finding packages to compile (00:00:00)
Task 581 | 02:20:46 | Updating instance webide-broker: webide-broker/f47c5c19-92d3-4b84-86da-89e8e53090fc (0) (canary) (00:00:13)

Task 581 Started  Mon Jan 11 02:20:43 UTC 2021
Task 581 Finished Mon Jan 11 02:20:59 UTC 2021
Task 581 Duration 00:00:16
Task 581 done

```  


### <div id="5.4"/> 5.4. 서비스 설치 확인

설치 완료된 서비스를 확인한다.  

> $ bosh -e micro-bosh -d web-ide vms

```
Using environment '10.30.40.111' as user 'admin' (openid, bosh.admin)

Task 7872. Done

Deployment 'web-ide'

Instance                                            Process State  AZ  IPs            VM CID                                   VM Type  Active
eclipse-che/ed136540-c650-47a2-918b-bb7f6020469d    running        z7  10.30.56.54    vm-5a3a2b10-d0c9-47c8-97f0-6ea64c339df8  large    true
							               115.68.46.178
mariadb/ec34aa5b-c7cc-4297-9e2d-babf05d83832        running        z3  10.30.56.55    vm-9e1631af-b6c8-481e-aad3-3fd713f106a9  small    true
webide-broker/a641df99-d36a-49ee-8329-018fe10fa23d  running        z3  10.30.56.56    vm-eb784964-48cd-4e4c-b080-53675d3738c2  medium   true

3 vms

Succeeded
```
[3-2-2]:./images/webide/adminPortal_catalogDetail.png
[003]:./images/webide/userportal_catalog.png
[004]:./images/webide/userportal_dashboard.png


### [Index](https://github.com/PaaS-TA/Guide-eng/blob/master/README.md) > [AP Install](../README.md) > WEB IDE Service
