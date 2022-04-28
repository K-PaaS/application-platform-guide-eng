### [Index](https://github.com/PaaS-TA/Guide-eng/blob/master/README.md) > [AP Install](../README.md) > BOSH

## Table of Contents

1. [Outline](#1)  
 1.1. [Purpose](#1.1)  
 1.2. [Range](#1.2)  
 1.3. [References](#1.3)  
2. [Configuring and Installing the BOSH Installation Environment](#2)  
 2.1. [BOSH Installation Procedure](#2.1)  
 2.2. [Inception Server Configuration](#2.2)  
 2.3. [BOSH Installation](#2.3)  
　2.3.1. [Prerequisite](#2.3.1)  
　2.3.2. [BOSH CLI and Dependency Installation](#2.3.2)  
　2.3.3. [Installation File Download](#2.3.3)  
　2.3.4. [BOSH Installation](#2.3.4)  
　　2.3.4.1. [BOSH Installation Variable File](#2.3.4.1)  
　　2.3.4.2. [BOSH Installation Option File](#2.3.4.2)  
　　2.3.4.3. [BOSH Installation Shell Script](#2.3.4.3)  
　2.3.5. [BOSH Installation](#2.3.5)  
　2.3.6. [BOSH Login](#2.3.6)  
3. [BOSH Option File Utilization](#3)  
 3.1. [CredHub](#3.1)   
　 3.1.1. [CredHub CLI Installation](#3.1.1)  
　 3.1.2. [CredHub Login](#3.1.2)  
 3.2. [Jumpbox](#3.2)   
4. [Others](#4)  
 4.1. [Create BOSH Login Script](#4.1)   

## Executive Summary

This document is an installation guide document for BOSH2 (hereinafter referred to as BOSH) and explains how to configure and use the environment to run BOSH.

# <div id='1'/>1. Document Outline

## <div id='1.1'/>1.1. Purpose
BOSH, which can distribute service systems to cloud environments, is an open-source project that integrates release engineering, development, and software lifecycle management. The purpose of this document is to install BOSH in an Inception environment (installation environment).

## <div id='1.2'/>1.2. Range
This document was written based on installing and configuring packages and libraries for BOSH installation based on Linux environments (Ubuntu 18.04) and using them to install BOSH.

## <div id='1.3'/>1.3. References

This document was prepared by referring to Cloud Foundry's BOSH Document and Cloud Foundry Document.

BOSH Document: [http://bosh.io](http://bosh.io)  
BOSH Deployment: [https://github.com/cloudfoundry/bosh-deployment](https://github.com/cloudfoundry/bosh-deployment)  
Cloud Foundry Document: [https://docs.cloudfoundry.org](https://docs.cloudfoundry.org)  


# <div id='2'/>2. Configuring and Installing the BOSH Installation Environment 

## <div id='2.1'/>2.1. BOSH Installaion Procedure
Inception (a PaaS-TA installation) is an installation environment for installing BOSH and PaaS-TA, either VM or server equipment. 
OS Version is based on Ubuntu 18.04. Inception VM must be created manually in IaaS.

Inception VM recommends Ubuntu 18.04, vCPU 2 Core, Memory 4G, and Disk 100G or higher.

## <div id='2.2'/>2.2.  Inception Server Configuration

The Inception server is a deployment job execution server that has the necessary environment for installing BOSH and PaaS-TA, such as packages, libraries, and Manifest files.
The Inception server should be capable of external communication.

The components to be configured on the Inception server for BOSH and PaaS-TA installation are as follows.

- BOSH CLI 6.1.x and above
- BOSH Dependency : ruby, ruby-dev, openssl etc.
- BOSH Deployment:manifest deployment for BOSH installation
- PaaS-TA Deployment : Manifest deployment for PaaS-TA installation

## <div id='2.3'/>2.3.  BOSH Installation

### <div id='2.3.1'/>2.3.1.    Prerequisite

- This installation guide is based on Ubuntu 18.04.

- Set which ports should be opened for IaaS security groups.

|Port|Remarks|
|---|---|
|22|Use BOSH|
|6868|Use BOSH|
|25555|Use BOSH|
|53|Use PaaS-TA|
|68|Use PaaS-TA|
|80|Use PaaS-TA|
|443|Use PaaS-TA|
|4443|Use PaaS-TA|


- Disable the ICMP types 13 (timestamp request) and types 14 (timestamp response) rule in the inbound of the IaaS security group. (CVE-1999-0524 ICMP timestamp response security issue applied)

  Ex. - AWS security group config)  
  ![Security_Group_ICMP_Image1](./images/bosh/security-group-icmp-01.png)  


### <div id='2.3.2'/>2.3.2.    BOSH CLI and Dependency Installation

- BOSH Dependency Installation (Ubuntu 18.04)

```
$ sudo apt-get update
$ sudo apt install -y build-essential zlibc zlib1g-dev ruby ruby-dev openssl libxslt1-dev libxml2-dev libssl-dev libreadline7 libreadline-dev libyaml-dev libsqlite3-dev sqlite3
```

- BOSH Dependency Installation (Ubuntu 16.04)

```
$ sudo apt-get update
$ sudo apt install -y libcurl4-openssl-dev gcc g++ build-essential zlibc zlib1g-dev ruby ruby-dev openssl libxslt-dev libxml2-dev libssl-dev libreadline6 libreadline6-dev libyaml-dev libsqlite3-dev sqlite3
```

- BOSH CLI Installation

```
$ mkdir -p ~/workspace
$ cd ~/workspace
$ sudo apt update
$ curl -Lo ./bosh https://github.com/cloudfoundry/bosh-cli/releases/download/v6.4.7/bosh-cli-6.4.7-linux-amd64
$ chmod +x ./bosh
$ sudo mv ./bosh /usr/local/bin/bosh
$ bosh -v
```

The BOSH2 CLI has the function of generating BOSH certificate information when installing BOSH.
Cloud Foundry's default BOSH CLI has a certificate limited for a year.
The BOSH certificate requires communication between BOSH internal components.
After a year of installing BOSH, the certification has to be renewed.  
- Certificate renew guide video - [Link](https://youtu.be/zn8VO-fHAFE?t=1994)

### <div id='2.3.3'/>2.3.3.    Installation File Download

- Download if deployment for installing BOSH does not exist.
```
$ mkdir -p ~/workspace
$ cd ~/workspace
$ git clone https://github.com/PaaS-TA/paasta-deployment.git -b v5.7.1
```

- paasta/deployment/paasta-deployment 이하 폴더 확인

```
$ cd ~/workspace/paasta-deployment
$ ls
README.md  bosh  cloud-config  paasta
```

<table>
<tr>
<td>bosh</td>
<td>BOSH 설치를 위한 manifest 및 설치 파일이 존재하는 폴더</td>
</tr>
<tr>
<td>cloud-config</td>
<td>VM 배포를 위한 IaaS network, storage, vm 관련 설정 파일이 존재하는 폴더</td>
</tr>
<tr>
<td>paasta</td>
<td>PaaS-TA AP 설치를 위한 manifest 및 설치 파일이 존재하는 폴더</td>
</tr>
</table>


### <div id='2.3.4'/>2.3.4.    BOSH 설치 파일

~/workspace/paasta-deployment/bosh 폴더에는 BOSH 설치를 위한 IaaS별 Shell Script 파일이 존재한다.  

Shell Script 파일을 이용하여 BOSH를 설치한다.
파일명은 deploy-{IaaS}.sh 로 만들어졌다.  
또한 {IaaS}-vars.yml을 수정하여 BOSH 설치시 적용하는 변수을 설정할 수 있다.

<table>
<tr>
<td>aws-vars.yml</td>
<td>AWS 환경에 BOSH 설치시 적용하는 변수 설정 파일</td>
</tr>
<tr>
<td>openstack-vars.yml</td>
<td>OpenStack 환경에 BOSH 설치시 적용하는 변수 설정 파일</td>
</tr>
<tr>
<td>vsphere-vars.yml</td>
<td>vSphere 환경에 BOSH 설치시 적용하는 변수 설정 파일</td>
</tr>
<tr>
<td>deploy-aws.sh</td>
<td>AWS 환경에 BOSH 설치를 위한 Shell Script 파일</td>
</tr>
<tr>
<td>deploy-openstack.sh</td>
<td>OpenStack 환경에 BOSH 설치를 위한 Shell Script 파일</td>
</tr>
<tr>
<td>deploy-vsphere.sh</td>
<td>vSphere 환경에 BOSH 설치를 위한 Shell Script 파일</td>
</tr>
<tr>
<td>bosh.yml</td>
<td>BOSH를 생성하는 Manifest 파일</td>
</tr>
</table>




#### <div id='2.3.4.1'/>2.3.4.1. BOSH 설치 Variable File 설정

BOSH를 설치하는 IaaS환경에 맞춰서 Variable File을 설정한다.

- AWS 환경 설치 시 

> $ vi ~/workspace/paasta-deployment/bosh/aws-vars.yml
```
# BOSH VARIABLE
bosh_client_admin_id: "admin"				# Bosh Client Admin ID
private_cidr: "10.0.1.0/24"				# Private IP Range
private_gw: "10.0.1.1"					# Private IP Gateway
bosh_url: "10.0.1.6"					# Private IP
director_name: "micro-bosh"				# BOSH Director Name
access_key_id: "XXXXXXXXXXXXXXX"			# AWS Access Key
secret_access_key: "XXXXXXXXXXXXX"			# AWS Secret Key
region: "ap-northeast-2"				# AWS Region
az: "ap-northeast-2a"					# AWS AZ Zone
default_key_name: "aws-paasta.pem"			# AWS Key Name
default_security_groups: ["bosh"]			# AWS Security-Group
subnet_id: "paasta-subnet"				# AWS Subnet
private_key: "~/.ssh/aws-paasta.pem"			# SSH Private Key Path (해당 IaaS에 접근권한을 가진 Private key의 경로)

# MONITORING VARIABLE(PaaS-TA Monitoring을 설치할 경우 향후 설치할 VM의 값으로 미리 수정)
metric_url: "xx.xx.xxx.xxx"				# PaaS-TA Monitoring InfluxDB IP
syslog_address: "xx.xx.xxx.xxx"				# Logsearch의 ls-router IP
syslog_port: "2514"					# Logsearch의 ls-router Port
syslog_transport: "relp"				# Logsearch Protocol
```

- OpenStack 환경 설치 시

> $ vi ~/workspace/paasta-deployment/bosh/openstack-vars.yml
```
# BOSH VARIABLE
bosh_client_admin_id: "admin"				# Bosh Client Admin ID
director_name: "micro-bosh"				# BOSH Director Name
private_cidr: "10.0.1.0/24"				# Private IP Range
private_gw: "10.0.1.1"					# Private IP Gateway
bosh_url: "10.0.1.6"					# Private IP
auth_url: "http://XX.XXX.XX.XX:XXXX/v3/"		# Openstack Keystone URL
az: "nova"						# Openstack AZ Zone
default_key_name: "paasta"				# Openstack Key Name
default_security_groups: ["paasta"]			# Openstack Security Group
net_id: "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX"		# Openstack Network ID
openstack_password: "XXXXXX"				# Openstack User Password
openstack_username: "XXXXXX"				# Openstack User Name
openstack_domain: "XXXXXXX"				# Openstack Domain Name
openstack_project: "PaaSTA"				# Openstack Project
private_key: "~/.ssh/id_rsa.pem"			# SSH Private Key Path (해당 IaaS에 접근권한을 가진 Private key의 경로)
region: "RegionOne"					# Openstack Region

# MONITORING VARIABLE(PaaS-TA Monitoring을 설치할 경우 향후 설치할 VM의 값으로 미리 수정)
metric_url: "10.0.161.101"				# PaaS-TA Monitoring InfluxDB IP
syslog_address: "10.0.121.100"				# Logsearch의 ls-router IP
syslog_port: "2514"					# Logsearch의 ls-router Port
syslog_transport: "relp"				# Logsearch Protocol
```

- vSphere 환경 설치 시

> $ vi ~/workspace/paasta-deployment/bosh/vsphere-vars.yml
```
# BOSH VARIABLE
bosh_client_admin_id: "admin"			# Bosh Client Admin ID
director_name: "micro-bosh"			# BOSH Director Name
private_cidr: "10.0.1.0/24"			# Private IP Range
private_gw: "10.0.1.1"				# Private IP Gateway
bosh_ip: "10.0.1.6"				# Private IP
network_name: "PaaS-TA"				# Private Network Name (vCenter)
vcenter_dc: "PaaS-TA-DC"			# vCenter Data Center Name
vcenter_ds: "PaaS-TA-Storage"			# vCenter Data Storage Name
vcenter_ip: "XX.XX.XXX.XX"			# vCenter Private IP
vcenter_user: "XXXXX"				# vCenter User Name
vcenter_password: "XXXXXX"			# vCenter User Password
vcenter_templates: "PaaS-TA_Templates"		# vCenter Templates Name
vcenter_vms: "PaaS-TA_VMs"			# vCenter VMS Name
vcenter_disks: "PaaS-TA_Disks"			# vCenter Disk Name
vcenter_cluster: "PaaS-TA"			# vCenter Cluster Name
vcenter_rp: "PaaS-TA_Pool"			# vCenter Resource Pool Name

# MONITORING VARIABLE(PaaS-TA Monitoring을 설치할 경우 수정)
metric_url: "10.0.161.101"			# PaaS-TA Monitoring InfluxDB IP
syslog_address: "10.0.121.100"			# Logsearch의 ls-router IP
syslog_port: "2514"				# Logsearch의 ls-router Port
syslog_transport: "relp"			# Logsearch Protocol
```



#### <div id='2.3.4.2'/>2.3.4.2. BOSH 설치 Option 파일

설치 Shell Script에서 사용되는 Option 파일은 다음과 같다.  

<table>
<tr>
<td>파일명</td>
<td>설명</td>
</tr>
<tr>
<td>uaa.yml</td>
<td>UAA 적용</td>
</tr>
<tr>
<td>credhub.yml</td>
<td>CredHub 적용</td>
</tr>
<tr>
<td>jumpbox-user.yml</td>
<td>BOSH Jumpbox user 생성</td>
</tr>
<tr>
<td>cce.yml</td>
<td>CCE 조치 적용</td>
</tr>
</table>



#### <div id='2.3.4.3'/>2.3.4.3. BOSH 설치 Shell Script

BOSH 설치 명령어는 create-env로 시작한다.  
Shell이 아닌 BOSH Command로 실행 가능하며, 설치하는 IaaS 환경에 따라 Option이 달라진다.  
BOSH 삭제 시 delete-env 명령어를 사용하여 설치된 BOSH를 삭제할 수 있다.

BOSH 설치 Option은 아래와 같다.

<table>
<tr>
<td>--state</td>
<td>BOSH 설치 명령어 실행 시 생성되는 파일로, 설치된 BOSH의 IaaS 설정 정보가 저장된다. (Backup 필요)</td>
</tr>
<tr>
<td>--vars-store</td>
<td>BOSH 설치 명령어 실행 시 생성되는 파일로, 설치된 BOSH의 내부 컴포넌트가 사용하는 인증서 및 인증정보가 저장된다. (Backup 필요)</td>
</tr>   
<tr>
<td>-o</td>
<td>BOSH 설치 시 적용하는 Operation 파일을 설정할 경우 사용한다. <br>IaaS별 CPI 또는 Jumpbox-user, CredHub 등의 설정을 적용할 수 있다.</td>
</tr>
<tr>
<td>-v</td>
<td>BOSH 설치 시 적용하는 변수 또는 Operation 파일에 변수를 설정할 경우 사용한다. <br>Operation 파일 속성에 따라 필수 또는 선택 항목으로 나뉜다.</td>
</tr>
<tr>
<td>-l, --var-file</td>
<td>YAML파일에 작성한 변수를 읽어올때 사용한다.</td>
</tr>
</table>

설치 Shell Script에 Option을 변경필요가 있다면 해당 명령어를 실행하여 변경한다.

- AWS 환경 설치 시 

> $ vi ~/workspace/paasta-deployment/bosh/deploy-aws.sh
```
bosh create-env bosh.yml \                         
	--state=aws/state.json \			# BOSH Latest Running State, 설치 시 생성, Backup 필요
	--vars-store=aws/creds.yml \			# BOSH Credentials and Certs, 설치 시 생성, Backup 필요
	-o aws/cpi.yml \				# AWS CPI 적용
	-o uaa.yml \					# UAA 적용      
	-o credhub.yml \				# CredHub 적용    
	-o jumpbox-user.yml \				# Jumpbox-user 적용  
	-o cce.yml \					# CCE 조치 적용
 	-l aws-vars.yml					# AWS 환경에 BOSH 설치시 적용하는 변수 설정 파일
```

- OpenStack 환경 설치 시 

> $ vi ~/workspace/paasta-deployment/bosh/deploy-openstack.sh
```
bosh create-env bosh.yml \                       
	--state=openstack/state.json \			# BOSH Latest Running State, 설치 시 생성, Backup 필요
	--vars-store=openstack/creds.yml \		# BOSH Credentials and Certs, 설치 시 생성, Backup 필요
	-o openstack/cpi.yml \				# Openstack CPI 적용
	-o uaa.yml \					# UAA 적용
	-o credhub.yml \				# CredHub 적용
	-o jumpbox-user.yml \				# Jumpbox-user 적용
	-o cce.yml \					# CCE 조치 적용
	-o openstack/disable-readable-vm-names.yml \	# VM 명을 UUIDs로 적용
	-l openstack-vars.yml				# OpenStack 환경에 BOSH 설치시 적용하는 변수 설정 파일
```

- vSphere 환경 설치 시 

> $ vi ~/workspace/paasta-deployment/bosh/deploy-vsphere.sh
```
bosh create-env bosh.yml \
	--state=vsphere/state.json \			# BOSH Latest Running State, 설치 시 생성, Backup 필요
	--vars-store=vsphere/creds.yml \		# BOSH Credentials and Certs, 설치 시 생성, Backup 필요
	-o vsphere/cpi.yml \				# vSphere CPI 적용
	-o vsphere/resource-pool.yml  \				# vSphere resouce-pool 사용 설정
	-o uaa.yml  \					# UAA 적용
	-o credhub.yml  \				# CredHub 적용
	-o jumpbox-user.yml  \				# Jumpbox-user 적용
	-o cce.yml \					# CCE 조치 적용
	-l vsphere-vars.yml				# vSphere 환경에 BOSH 설치시 적용하는 변수 설정 파일
```


- Shell Script 파일에 실행 권한 부여

```
$ chmod +x ~/workspace/paasta-deployment/bosh/*.sh  
```


### <div id='2.3.5'/>2.3.5. BOSH 설치

Variable File과 설치 Shell Script의 설정이 완료되었으면 다음 명령어를 이용하여 설치를 진행한다.  

- BOSH 설치 Shell Script 파일 실행

```
$ cd ~/workspace/paasta-deployment/bosh
$ ./deploy-{iaas}.sh
```

- BOSH 설치 완료

```
  Compiling package 'uaa_utils/90097ea98715a560867052a2ff0916ec3460aabb'... Skipped [Package already compiled] (00:00:00)
  Compiling package 'davcli/f8a86e0b88dd22cb03dec04e42bdca86b07f79c3'... Skipped [Package already compiled] (00:00:00)
  Updating instance 'bosh/0'... Finished (00:01:44)
  Waiting for instance 'bosh/0' to be running... Finished (00:02:16)
  Running the post-start scripts 'bosh/0'... Finished (00:00:13)
Finished deploying (00:11:54)

Stopping registry... Finished (00:00:00)
Cleaning up rendered CPI jobs... Finished (00:00:00)

Succeeded
```


### <div id='2.3.6'/>2.3.6. BOSH 로그인
BOSH가 설치되면, BOSH 설치 폴더 이하 {iaas}/creds.yml 파일이 생성된다.  
creds.yml은 BOSH 인증정보를 가지고 있으며, creds.yml을 활용하여 BOSH에 로그인한다.  
BOSH 로그인 후, BOSH CLI 명령어를 이용하여 PaaS-TA를 설치할 수 있다.  
**BOSH를 이용하여 VM를 배포하려면 반드시 BOSH에 로그인을 해야한다.**  
BOSH 로그인 명령어는 다음과 같다.  

```
$ cd ~/workspace/paasta-deployment/bosh
$ export BOSH_CA_CERT=$(bosh int ./{iaas}/creds.yml --path /director_ssl/ca)
$ export BOSH_CLIENT=admin
$ export BOSH_CLIENT_SECRET=$(bosh int ./{iaas}/creds.yml --path /admin_password)
$ bosh alias-env {director_name} -e {bosh_url} --ca-cert <(bosh int ./{iaas}/creds.yml --path /director_ssl/ca)
$ bosh -e {director_name} env
```

## <div id='3'/> 3. BOSH Option 파일 활용
### <div id='3.1'/>3.1. CredHub
CredHub은 인증정보 저장소이다.  
BOSH 설치 시 Operation 파일로 credhub.yml을 적용하면, 이후 BOSH를 통해 생성되는 Deployments에서 사용하는 인증정보(Certificate, Password)를 CredHub에 저장한다.  
인증정보가 필요할 때, CredHub CLI를 통해 CredHub에 로그인하여 인증정보 조회, 수정, 삭제를 할 수 있다.

#### <div id='3.1.1'/>3.1.1 CredHub CLI 설치
CredHub CLI는 BOSH를 설치한 Inception(설치환경)에 설치한다.

```
$ wget https://github.com/cloudfoundry-incubator/credhub-cli/releases/download/2.9.0/credhub-linux-2.9.0.tgz
$ tar -xvf credhub-linux-2.9.0.tgz
$ chmod +x credhub
$ sudo mv credhub /usr/local/bin/credhub
$ credhub --version
```
#### <div id='3.1.2'/>3.1.2. CredHub 로그인
CredHub에 로그인하기 위해 BOSH를 설치한 bosh-deployment 디렉터리의 creds.yml을 활용하여 로그인한다.

```
$ cd ~/workspace/paasta-deployment/bosh
$ export CREDHUB_CLIENT=credhub-admin
$ export CREDHUB_SECRET=$(bosh int --path /credhub_admin_client_secret {iaas}/creds.yml)
$ export CREDHUB_CA_CERT=$(bosh int --path /credhub_tls/ca {iaas}/creds.yml)
$ credhub login -s https://{bosh_url}:8844 --skip-tls-validation
```

### <div id='3.2'/>3.2. Jumpbox
BOSH 설치 시 Operation 파일로 jumpbox-user.yml을 적용하면, BOSH VM에 Jumpbox user가 생성되어 BOSH VM에 접근할 수 있다.
접근하기 위한 인증키는 BOSH에서 자체적으로 생성하며, 인증키를 통해 BOSH VM에 접근할 수 있다.  
BOSH VM에 이상이 있거나 상태를 체크할 때 Jumpbox를 활용하여 BOSH VM에 접근할 수 있다.  

**💥 BOSH 설치 시 cce.yml을 추가하면 BOSH의 Jumpbox 계정의 비밀번호 기한이 90일로 설정된다.**  
**비밀번호 만료전에 BOSH에 재 접속하여 비밀번호를 변경하여 관리해야 한다. (미 변경시 Jumpbox 계정 잠금)**

```
$ cd ~/workspace/paasta-deployment/bosh
$ bosh int {iaas}/creds.yml --path /jumpbox_ssh/private_key > jumpbox.key
$ chmod 600 jumpbox.key
$ ssh jumpbox@{bosh_url} -i jumpbox.key
```

```
ubuntu@inception:~/workspace/paasta-deployment/bosh$ ssh jumpbox@10.0.1.6 -i jumpbox.key
Unauthorized use is strictly prohibited. All access and activity
is subject to logging and monitoring.
Welcome to Ubuntu 18.04.6 LTS (GNU/Linux 4.15.0-54-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage
Last login: Thu Oct 17 03:57:48 UTC 2019 from 10.0.0.9 on pts/0
Last login: Fri Oct 25 07:05:42 2019 from 10.0.0.9
bosh/0:~$
```



## <div id='4'/>4. 기타
### <div id='4.1'/>4.1. BOSH 로그인 생성 스크립트

PaaS-TA 5.5부터 BOSH 로그인을 하는 스크립트의 생성을 지원한다.
해당 스크립트의 BOSH_DEPLOYMENT_PATH, CURRENT_IAAS, BOSH_IP, BOSH_CLIENT_ADMIN_ID, BOSH_ENVIRONMENT, BOSH_LOGIN_FILE_PATH, BOSH_LOGIN_FILE_NAME를 BOSH 환경과 스크립트를 저장하고 싶은 위치로 변경 후 실행한다.

- BOSH Login 생성 Script의 설정 수정

> $ vi ~/workspace/paasta-deployment/bosh/create-bosh-login.sh
```
#!/bin/bash

BOSH_DEPLOYMENT_PATH="<BOSH_DEPLOYMENT_PATH>" 	# (e.g. ~/workspace/paasta-deployment/bosh)
CURRENT_IAAS="aws"				# (e.g. aws/azure/gcp/openstack/vsphere/bosh-lite)
BOSH_IP="10.0.1.6"				# (e.g. 10.0.1.6)
BOSH_CLIENT_ADMIN_ID="admin"			# (e.g. admin)
BOSH_ENVIRONMENT="micro-bosh"			# (e.g. micro-bosh)
BOSH_LOGIN_FILE_PATH="/home/ubuntu/.env"	# (e.g. /home/ubuntu/.env)
BOSH_LOGIN_FILE_NAME="micro-bosh-login-env"	# (e.g. micro-bosh-login-env)

mkdir -p ${BOSH_LOGIN_FILE_PATH}
echo 'export CRED_PATH='${BOSH_DEPLOYMENT_PATH}'
export CURRENT_IAAS='${CURRENT_IAAS}'
export BOSH_CA_CERT=$(bosh int $CRED_PATH/$CURRENT_IAAS/creds.yml --path /director_ssl/ca)
export BOSH_CLIENT='${BOSH_CLIENT_ADMIN_ID}'
export BOSH_CLIENT_SECRET=$(bosh int $CRED_PATH/$CURRENT_IAAS/creds.yml --path /admin_password)
export BOSH_ENVIRONMENT='${BOSH_ENVIRONMENT}'


bosh alias-env $BOSH_ENVIRONMENT -e '${BOSH_IP}' --ca-cert <(bosh int $CRED_PATH/$CURRENT_IAAS/creds.yml --path /director_ssl/ca)

credhub login -s https://'${BOSH_IP}':8844 --skip-tls-validation --client-name=credhub-admin --client-secret=$(bosh int --path /credhub_admin_client_secret $CRED_PATH/$CURRENT_IAAS/creds.yml)


' > ${BOSH_LOGIN_FILE_PATH}/${BOSH_LOGIN_FILE_NAME}
```

- BOSH Login 생성 Script 실행

```
$ cd ~/workspace/paasta-deployment/bosh
$ source create-bosh-login.sh
```


- 생성된 Script로 BOSH Login 실행

```
$ source {BOSH_LOGIN_FILE_PATH}/{BOSH_LOGIN_FILE_NAME}
```


### [Index](https://github.com/PaaS-TA/Guide-eng/blob/master/README.md) > [AP Install](../README.md) > BOSH
