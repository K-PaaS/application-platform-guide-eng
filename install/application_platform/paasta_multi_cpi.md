### [Index](https://github.com/PaaS-TA/Guide-eng/blob/master/README.md) > [AP Install](../README.md) > PaaS-TA Multi CPI

## Table of Contents

1. [Outline](#1)  
 1.1. [Purpose](#1.1)  
 1.2. [Range](#1.2)  
 1.3. [Refrences](#1.3)  
2. [Multi CPI](#2)  
 2.1. [Prerequisite](#2.1)  
 2.2. [Download the installation file](#2.2)  
 2.3. [OpenVPN](#2.3)  
　2.3.1. [Variable Setting](#2.3.1)       
　2.3.2. [Generate Certificate](#2.3.2)       
　2.3.3. [OpenVPN Installation](#2.3.3)  
　2.3.4. [OpenVPN Connection Check](#2.3.4)  
　2.3.5. [Add Static Routing (Select)](#2.3.5)  
 2.4. [Multi CPI Setting](#2.4)   
　2.4.1. [BOSH Installation](#2.4.1)    
　2.4.2. [CPI Config Setting](#2.4.2)    
　　2.4.2.1. [Same IaaS for AZ](#2.4.2.1)    
　　2.4.2.2. [Different IaaS for AZ](#2.4.2.2)    
　2.4.3. [Cloud Config Setting](#2.4.3)    
　　2.4.3.1. [Same IaaS for AZ](#2.4.3.1)    
　　2.4.3.2. [Different IaaS for AZ](#2.4.3.2)    
　2.4.4. [Stemcell Upload](#2.4.4)    
　2.4.5. [AP Installation Test with Multi-CPI](#2.4.5)    
 
# <div id='1'/>1. Document Outline

## <div id='1.1'/>1.1. Purpose
This document is a Multi-CPI setup guide for BOSH2 (hereinafter BOSH) and explains how to set up and use Multi-CPI that deploys VMs in IaaS environments. (hereinafter referred to as Main IaaS AZ) and other IaaS environments (hereinafter referred to as Second IaaS AZ) where BOSH is installed through one BOSH.

<br>

## <div id='1.2'/>1.2. Range
This guide was conducted on the premise that there is a basic understanding of BOSH and PaaS-TA AP.
For multi-cpi-deployment, a guide was prepared based on the installation of paasta-deployment v5.7.1.
Multi-cpi-deployment can be configured on AWS, OpenStack, and vSphere.
The classification was largely based on the case where Main IaaS AZ and Second IaaS AZ are the same (e.g. A OpenStack ⇆ B OpenStack, hereinafter Same IaaS AZ) and the case where Main IaaS AZ and Second IaaS AZ are different (e.g. OpenStack ⇆ AWS, hereinafter Different IaaS AZ).

The configurable IaaS cases are as follows.


<table class="tg">
<thead>
  <tr>
    <th class="tg-9wq8">Category</th>
    <th class="tg-9wq8">Main IaaS AZ</th>
    <th class="tg-9wq8">Second IaaS AZ</th>
  </tr>
</thead>
<tbody>
  <tr>
    <td class="tg-9wq8" rowspan="6">Different IaaS AZ</td>
    <td class="tg-0pky">AWS</td>
    <td class="tg-0pky">OpenStack</td>
  </tr>
  <tr>
    <td class="tg-0pky">AWS</td>
    <td class="tg-0pky">vSphere</td>
  </tr>
  <tr>
    <td class="tg-0pky">OpenStack</td>
    <td class="tg-0pky">AWS</td>
  </tr>
  <tr>
    <td class="tg-0pky">OpenStack</td>
    <td class="tg-0pky">vSphere</td>
  </tr>
  <tr>
    <td class="tg-0lax">vSphere</td>
    <td class="tg-0lax">AWS</td>
  </tr>
  <tr>
    <td class="tg-0lax">vSphere</td>
    <td class="tg-0lax">OpenStack</td>
  </tr>
  <tr>
    <td class="tg-nrix" rowspan="3">Same IaaS AZ</td>
    <td class="tg-0lax">AWS</td>
    <td class="tg-0lax">AWS</td>
  </tr>
  <tr>
    <td class="tg-0lax">OpenStack</td>
    <td class="tg-0lax">OpenStack</td>
  </tr>
  <tr>
    <td class="tg-0lax">vSphere</td>
    <td class="tg-0lax">vSphere</td>
  </tr>
</tbody>
</table>
   
<br>

## <div id='1.3'/>1.3. Refrences

This document was written by referring to Cloud Foundry's BOSH Document, cf-deployment, and Open VPN.

BOSH Document: [https://bosh.io](https://bosh.io)  
CF Document: [https://docs.cloudfoundry.org/](https://docs.cloudfoundry.org/)  
BOSH cpi-config Document: [https://bosh.io/docs/cpi-config](https://bosh.io/docs/cpi-config)  
BOSH guide-multi-cpi-aws Document: [https://bosh.io/docs/guide-multi-cpi-aws](https://bosh.io/docs/guide-multi-cpi-aws)  
BOSH Deployment: [https://github.com/cloudfoundry/bosh-deployment](https://github.com/cloudfoundry/bosh-deployment)  
CF Deployment: [https://github.com/cloudfoundry/cf-deployment](https://github.com/cloudfoundry/cf-deployment)  
OpenVPN : [https://openvpn.net/](https://openvpn.net/)  

<br><br>

# <div id='2'/>2.  Multi CPI
When Multi-CPI is set up in BOSH, VMs can be deployed in two environments, Main IaaS AZ and Second IaaS AZ, respectively, through one BOSH.
In this guide, OpenVPN is installed on the Main IaaS AZ and the Second IaaS AZ, respectively, and BOSH is installed on the Main IaaS AZ and Multi CPI is set up.
<br>

## <div id='2.1'/>2.1. Prerequisite

This guide is based on the Linux environment.
In addition, BOSH CLI must be installed before Multi-CPI can be configured.
If the BOSH CLI is not installed, first refer to the BOSH Installation Guide document and proceed with the BOSH CLI installation.


<br>


## <div id='2.2'/>2.2. ownload the installation file

- Download if paasta-deployment for installing BOSH and multi-cpi-deployment for Multi CPI setup do not exist

```
$ mkdir -p ~/workspace
$ cd ~/workspace
$ git clone https://github.com/PaaS-TA/paasta-deployment.git -b v5.7.1
$ git clone https://github.com/PaaS-TA/multi-cpi-deployment.git -b v5.7.1
```

<br>

## <div id='2.3'/>2.3. OpenVPN
In order for BOSH to communicate between Main IaaS AZ and Second IaaS AZ, OpenVPN will be installed on Main IaaS AZ and Second IaaS AZ.

<br>

### <div id='2.3.1'/>2.3.1. Variable Setting
```
# Go to Deployment folder to install OpenVPN
$ cd ~/workspace/multi-cpi-deployment/openvpn
```

- Set the OpenVPN az1 variable installed in the Main IaaS AZ.
  (Uncomment and set variables for IaaS environments where OpenVPN will be installed.)
> $ vi vars-az1.yml
```
### openvpn default
wan_ip: "XXX.XXX.XXX.XXX"                         # Used by OpenVPN Server-1 ip 
push_routes: ["XXX.XXX.XXX.XXX 255.255.255.0"]    # ex) 10.0.10.0 255.255.255.0
lan_gateway: "XXX.XXX.XXX.XXX"                    # ex) 10.0.10.1
lan_ip: "XXX.XXX.XXX.XXX"                         # ex) 10.0.10.10
lan_network: "XXX.XXX.XXX.XXX"                    # ex) 10.0.10.0
lan_network_mask_bits: "24"                       # ex) 24
vpn_network: "XXX.XXX.XXX.XXX"                    # ex) 192.168.0.0 (az-1: 192.168.0.0, az-2: 192.168.1.0)
vpn_network_mask: "XXX.XXX.XXX.XXX"               # ex) 255.255.255.0
vpn_network_mask_bits: "24"                       # ex) 24
remote_network_cidr_block: "XXX.XXX.XXX.XXX/24"   # ex) 20.0.20.0/24
remote_vpn_ip: "XXX.XXX.XXX.XXX"                  # Used by OpenVPN Server-2 ip 

#### IaaS :: AWS
#access_key_id: "XXXXXXXXXXXXXXX"                  # AWS Access Key
#secret_access_key: "XXXXXXXXXXXXXXX"              # AWS Secret Key
#region: "ap-northeast-2"                          # AWS Region
#availability_zone: "ap-northeast-2a"              # AWS Region
#subnet_id: "paasta-subnet"                        # AWS Subnet ex) subnet-0ebc.....
#default_security_groups: ["bosh-sg"]              # AWS Security-Group
#bootstrap_ssh_key_name: "bosh-key"                # AWS SSH Private Key Name
#bootstrap_ssh_key_path: "/.ssh/bosh-key.pem"      # AWS SSH Private Key Path
#route_table_id: "XXXXXXXXXXXXXXX"                 # AWS Route table id ex) rtb-4127..

#### IaaS :: openstack 
#auth_url: "http://XXX.XXX.XXX.XXX:5000/v3/"       # Openstack Keystone URL
#az: "nova"                                        # Openstack AZ Zone
#default_key_name: "bosh-key"                      # Openstack Key Name
#default_security_groups: ["bosh-sg"]              # Openstack Security Group
#net_id: "XXXXXXXXXXXXXXX"                         # Openstack Network ID ex) 70f151db-274....
#openstack_password: "XXXXXXXXXXXXXXX"             # Openstack User Password
#openstack_username: "XXXXXXXXXXXXXXX"             # Openstack User Name
#openstack_domain: "default"                       # Openstack Domain Name
#openstack_project: "paasta"                       # Openstack Project
#private_key: "/.ssh/bosh-key.pem"                 # Openstack SSH Private Key Path
#region: "RegionOne"                               # Openstack Region

#### IaaS :: vsphere 
#wan_gateway: "XXX.XXX.XXX.XXX"                    # Used by OpenVPN gateway ex) 172.100.100.100
#wan_network: "XXX.XXX.XXX.XXX"                    # Used by OpenVPN Network ex) 172.100.100.0
#wan_network_mask_bits: "24"                       # Used by OpenVPN Mask bits ex) 24
#wan_network_name: "Public Network"                # vCenter Public Network Name 
#lan_network_name: "Private Network"               # vCenter Private Network Name 
#vcenter_dc: "DataCenter"                          # vCenter Data Center Name
#vcenter_cluster: "CLUSTER"                        # vCenter Cluster Name
#vcenter_ip: "XXX.XXX.XXX.XXX"                     # vCenter Private IP
#vcenter_user: "XXXXXXXXXXXXXXX"                   # vCenter User Name
#vcenter_password: "XXXXXXXXXXXXXXX"               # vCenter User Password
#vcenter_ds: "DataStorage"                         # vCenter Data Storage Name
#vcenter_templates: "Templates"                    # vCenter Templates Name
#vcenter_vms: "Vms"                                # vCenter VMS Name
#vcenter_disks: "Disks"                            # vCenter Disk Name
```

- Set the OpenVPN az2 variable installed in the Second IaaS AZ. 
  (Uncomment and set variables for IaaS environments where OpenVPN will be installed.)
> $ vi vars-az2.yml
```
### openvpn default
wan_ip: "XXX.XXX.XXX.XXX"                         # Used by OpenVPN Server-2 ip 
push_routes: ["XXX.XXX.XXX.XXX 255.255.255.0"]    # ex) 20.0.20.0 255.255.255.0
lan_gateway: "XXX.XXX.XXX.XXX"                    # ex) 20.0.20.1
lan_ip: "XXX.XXX.XXX.XXX"                         # ex) 20.0.20.10
lan_network: "XXX.XXX.XXX.XXX"                    # ex) 20.0.20.0
lan_network_mask_bits: "24"                       # ex) 24
vpn_network: "XXX.XXX.XXX.XXX"                    # ex) 192.168.1.0 (az-1: 192.168.0.0, az-2: 192.168.1.0)
vpn_network_mask: "XXX.XXX.XXX.XXX"               # ex) 255.255.255.0
vpn_network_mask_bits: "24"                       # ex) 24
remote_network_cidr_block: "XXX.XXX.XXX.XXX/24"   # ex) 10.0.10.0/24
remote_vpn_ip: "XXX.XXX.XXX.XXX"                  # Used by OpenVPN Server-1 ip 

... ((Skip)) ...

```

<br>

### <div id='2.3.2'/>2.3.2. Generate Certificate
Run generate_ca.sh to generate the certificate to be used in OpenVPN.
```
$ source generate_ca.sh

$ ll creds
drwxrwxr-x 2 ubuntu ubuntu  4096 Jul 22 02:12 ./
drwxrwxr-x 4 ubuntu ubuntu  4096 Jul 21 01:47 ../
-rw-rw-r-- 1 ubuntu ubuntu 18098 Jul 14 04:22 vpn-server-az1.yml
-rw-rw-r-- 1 ubuntu ubuntu 18102 Jul 14 04:22 vpn-server-az2.yml
```

<br>

### <div id='2.3.3'/>2.3.3. OpenVPN Installation
- Add options to be used for IaaS at deploy-vpn-\*.sh.
```
### OpenVPN az1 IaaS Setting
$ vi deploy-vpn-az1.sh

ex) Choose one and add it.
-o operations/init-aws.yml \
-o operations/init-openstack.yml \
-o operations/init-vsphere.yml \


### OpenVPN az2 IaaS Setting
$ vi deploy-vpn-az2.sh

ex) Choose one and add it.
-o operations/init-aws.yml \
-o operations/init-openstack.yml \
-o operations/init-vsphere.yml \
```


- Install OpenVPNaz1 and OpenVPNaz2 using the generated certificate.
```
### OpenVPN az1 Installation
$ source deploy-vpn-az1.sh

### OpenVPN az2 Installation
$ source deploy-vpn-az2.sh
```

<br>

### <div id='2.3.4'/>2.3.4. OpenVPN Connection Check
When OpenVPN is completely installed, verify that interconnection is possible.
```
# OpenVPN Generate Authentication Key
## OpenVPN az1.key
$ bosh int creds/vpn-deploy-az1.yml --path /ssh/private_key > openvpn-az1.key 
$ chmod 600 openvpn-az1.key

## OpenVPN az2.key
$ bosh int creds/vpn-deploy-az2.yml --path /ssh/private_key > openvpn-az2.key 
$ chmod 600 openvpn-az2.key


# Connection Check
## openVPN az1
$ ssh openvpn@{openvpn-az1-ip} -i openvpn-az1.key 

## ping Connection Check
$ ping {openvpn-az2-ip}

## ifconfig Check (Check network interface tun0, tun2)
$ sudo su
$ ifconfig 
```

<br>

### <div id='2.3.5'/>2.3.5. Add Static Routing (Select)
Static routing may be added to use the client tunnel.
If IaaS does not support adding static routes, establish static routing through the command on all VMs that will use OpenVPN's network.
```
$ sudo ip route add {remote_network_cidr_block} via {lan_ip}
e.g.) sudo ip route add 20.0.20.0/24 via 10.0.10.10

$ ping {remote_network_ip}
```

<br>

## <div id='2.4'/>2.4. Multi CPI Setting
May install BOSH and set up Multi-CPI to deploy VMs in the Main IaaS AZ and Second IaaS AZ with one BOSH.

- Move Multi CPI File to  BOSH Folder
```
$ cp ~/workspace/multi-cpi-deployment/multi-cpi ~/workspace/paasta-deployment/bosh -r
$ cd ~/workspace/paasta-deployment/bosh
```

<br>

### <div id='2.4.1'/>2.4.1. BOSH Installation
For Same IaaS AZ, install BOSH without adding other options, and for Different IaaS AZ, install BOSH by adding options.
This guide explains the additional options.
In this guide, four examples are described, but options and distribution files are changed according to the situation.
For more information on installing BOSH, see the BOSH Installation Guide.

- Multi CPI Related Option File

|File Name|Description|
|------|---|
| deploy-cpi-aws-secondary.yml | Use if infrastructure without BOSH is AWS |
| deploy-cpi-openstack-secondary.yml	 | Use if infrastructure without BOSH is OpenStack |
| deploy-cpi-vsphere-secondary.yml	 | Use if infrastructure without BOSH is OpenStack vSphere|
| deploy-cpi-registry-secondary.yml | Use if infrastructure without BOSH is OpenStack vSphere <br>(used for paasta-deployment v5.7.0 or lower version deployment, <br>v5.7.0 or higher has no need to use.) |

- Example 1. AWS - Openstack BOSH Installation
> $ vi deploy-aws.sh
```diff
 bosh create-env bosh.yml \
 	--state=aws/state.json \
 	--vars-store=aws/creds.yml \
 	-o aws/cpi.yml \
+ 	-o multi-cpi/deploy-cpi-openstack-secondary.yml \
 	-o uaa.yml \
 	-o credhub.yml \
 	-o jumpbox-user.yml \
 	-o cce.yml \
 	-l aws-vars.yml
```

- Example 2. AWS - vSphere BOSH Installation
> $ vi deploy-aws.sh
```diff
 bosh create-env bosh.yml \
 	--state=aws/state.json \
 	--vars-store=aws/creds.yml \
 	-o aws/cpi.yml \
+ 	-o multi-cpi/deploy-cpi-vsphere-secondary.yml \
 	-o uaa.yml \
 	-o credhub.yml \
 	-o jumpbox-user.yml \
 	-o cce.yml \
 	-l aws-vars.yml
```


- Exmaple 3. Openstack - vSphere BOSH Installation
> $ vi deploy-openstack.sh
```diff
 bosh create-env bosh.yml \
 	--state=openstack/state.json \
 	--vars-store=openstack/creds.yml \
 	-o openstack/cpi.yml \
+ 	-o multi-cpi/deploy-cpi-vsphere-secondary.yml \
 	-o uaa.yml \
 	-o credhub.yml \
 	-o jumpbox-user.yml \
 	-o cce.yml \
 	-o openstack/disable-readable-vm-names.yml \
 	-l openstack-vars.yml
```

- Exmaple 4. vSphere - AWS BOSH Installation (paasta-deployment v5.7.0 and above)
> $ vi deploy-vsphere.sh
```diff
 bosh create-env bosh.yml \
 	--state=vsphere/state.json \
 	--vars-store=vsphere/creds.yml \
 	-o vsphere/cpi.yml \
 	-o vsphere/resource-pool.yml  \
+ 	-o multi-cpi/deploy-cpi-aws-secondary.yml \
 	-o uaa.yml  \
 	-o credhub.yml  \
 	-o jumpbox-user.yml  \
 	-l vsphere-vars.yml
```

- Example 5. vSphere - AWS BOSH Installation (paasta-deployment v5.7.0 and lower)
> $ vi deploy-vsphere.sh
```diff
 bosh create-env bosh.yml \
 	--state=vsphere/state.json \
 	--vars-store=vsphere/creds.yml \
 	-o vsphere/cpi.yml \
 	-o vsphere/resource-pool.yml  \
+ 	-o multi-cpi/deploy-cpi-aws-secondary.yml \
+ 	-o multi-cpi/deploy-cpi-registry-secondary.yml \
 	-o uaa.yml  \
 	-o credhub.yml  \
 	-o jumpbox-user.yml  \
 	-l vsphere-vars.yml
```


- Proceed with BOSH installation after setting the variable
```
$ vi {IaaS}-vars.yml
$ source deploy-{IaaS}.sh
```

<br>


### <div id='2.4.2'/>2.4.2. CPI Config Setting
Proceed with additional settings for the CPI.  
Uncomment the cpi-config.yml according to the corresponding IaaS to proceed.
|File Name|Description|
|------|---|
| cpi-config.yml	 | cpi config file to add multi-cpi |
| cpi-vars.yml	 | multi-cpi Setting File |

#### <div id='2.4.2.1'/>2.4.2.1. Same IaaS for AZ

- Example AWS - When using AWS
> $ vi multi-cpi/cpi-vars.yml
```
... ((Skip)) ...

## MULTI-CPI VARIABLE :: AWS
aws_access_key_id: "XXXXXXXXXXXXXXX"                    # AWS Access Key
aws_secret_access_key: "XXXXXXXXXXXXX"                  # AWS Secret Key
aws_default_key_name: "paasta-key"                      # AWS Key Name
aws_default_security_groups: ["paasta-security"]        # AWS Security-Group
aws_region: "ap-northeast-2"                            # AWS Region

... ((Skip)) ...

# IF USE SAME IAAS, CPI MULTI-CPI VARIABLE

## MULTI-CPI VARIABLE :: AWS second
aws_second_access_key_id: "XXXXXXXXXXXXXXX"                    # AWS Second Access Key
aws_second_secret_access_key: "XXXXXXXXXXXXX"                  # AWS Second Secret Key
aws_second_default_key_name: "paasta-key"                      # AWS Second Key Name
aws_second_default_security_groups: ["paasta-security"]        # AWS Second Security-Group
aws_second_region: "ap-northeast-2"                            # AWS Second Region

... ((Skip)) ...
```

> $ vi multi-cpi/cpi-config.yml (Uncomment IaaS information to use.)
```
#### DIFFERENT IAAS CPI

cpis:
- name: aws-cpi
  type: aws
  properties:
    access_key_id: ((aws_access_key_id))
    secret_access_key: ((aws_secret_access_key))
    default_key_name: ((aws_default_key_name))
    default_security_groups: ((aws_default_security_groups))
    region: ((aws_region))

... ((Skip)) ...

#### SAME IAAS CPI

- name: aws-cpi-second
  type: aws
  properties:
    access_key_id: ((aws_second_access_key_id))
    secret_access_key: ((aws_second_secret_access_key))
    default_key_name: ((aws_second_default_key_name))
    default_security_groups: ((aws_second_default_security_groups))
    region: ((aws_second_region))

... ((Skip)) ...
```

- Apply CPI Config
```
$ bosh update-cpi-config multi-cpi/cpi-config.yml -l multi-cpi/cpi-vars.yml
```

#### <div id='2.4.2.2'/>2.4.2.2. Different IaaS for AZ
- Example AWS - When using OpenStack
> $ vi multi-cpi/cpi-vars.yml
```
... ((Skip)) ...

## MULTI-CPI VARIABLE :: AWS
aws_access_key_id: "XXXXXXXXXXXXXXX"                    # AWS Access Key
aws_secret_access_key: "XXXXXXXXXXXXX"                  # AWS Secret Key
aws_default_key_name: "paasta-key"                      # AWS Key Name
aws_default_security_groups: ["paasta-security"]        # AWS Security-Group
aws_region: "ap-northeast-2"                            # AWS Region

## MULTI-CPI VARIABLE :: OpenStack
openstack_auth_url: "http://XX.XXX.XX.XX:XXXX/v3/"      # OpenStack Keystone URL
openstack_username: "XXXXXX"                            # OpenStack User Name
openstack_password: "XXXXXX"                            # OpenStack User Password
openstack_domain: "XXXXXX"                              # OpenStack Domain Name
openstack_project: "PaaS-TA"                            # OpenStack Project
openstack_region: "RegionOne"                           # OpenStack Region
openstack_default_key_name: "paasta-key"                # OpenStack Key Name
openstack_default_security_groups: ["paasta-security"]  # OpenStack Security Group

... ((Skip)) ...
```

> vi multi-cpi/cpi-config.yml (Uncomment information to use.)
```
#### DIFFERENT IAAS CPI

cpis:
- name: aws-cpi
  type: aws
  properties:
    access_key_id: ((aws_access_key_id))
    secret_access_key: ((aws_secret_access_key))
    default_key_name: ((aws_default_key_name))
    default_security_groups: ((aws_default_security_groups))
    region: ((aws_region))

- name: openstack-cpi
  type: openstack
  properties:
    auth_url: ((openstack_auth_url))
    username: ((openstack_username))
    api_key: ((openstack_password))
    domain: ((openstack_domain))
    project: ((openstack_project))
    region: ((openstack_region))
    default_key_name: ((openstack_default_key_name))
    default_security_groups: ((openstack_default_security_groups))
    human_readable_vm_names: true

... ((Skip)) ...
```

- Apply CPI Config 
```
$ bosh update-cpi-config multi-cpi/cpi-config.yml -l multi-cpi/cpi-vars.yml
```

<br>

### <div id='2.4.3'/>2.4.3. Cloud Config Setting
Proceed with additional setup for Cloud Config.
Same IaaS AZ uses the cloud-config file in the paasta-deployment folder, and Different IaaS AZ uses the cloud-config file in the bosh/multi-cpi folder. 

#### <div id='2.4.3.1'/>2.4.3.1. Same IaaS for AZ

```diff
Specify the cpi-name of each infrastructure in the azs of cloud-config

 azs:
 - cloud_properties:
     availability_zone: ap-northeast-1a
   name: z1
+  cpi: aws-cpi
 - cloud_properties:
     availability_zone: ap-northeast-1a
   name: z2
+  cpi: aws-cpi
 - cloud_properties:
     availability_zone: ap-northeast-2a
   name: z3
+  cpi: aws-cpi-second
 - cloud_properties:
     availability_zone: ap-northeast-2a
   name: z4
+  cpi: aws-cpi-second
 ...
 ...
```

- Apply Cloud Config 
```
$ bosh update-cloud-config ~/workspace/cloud-config/{iaas}-cloud-config.yml 
```

#### <div id='2.4.3.2'/>2.4.3.2. Different IaaS for AZ

|File Name|Description|
|------|---|
| cloud-config-aws-openstack.yml | AWS, OpenStack cloud config file |
| cloud-config-openstack-vsphere.yml	| OpenStack, vSphere cloud config file |
| cloud-config-vsphere-aws.yml	 | vSphere, AWS cloud config file |

```
# AWS - OpenStack or OpenStack - When Using AWS
$ bosh update-cloud-config ~/workspace/bosh/multi-cpi/cloud-config-aws-openstack.yml

# OpenStack - vSphere or vSphere - When Using OpenStack
$ bosh update-cloud-config ~/workspace/bosh/multi-cpi/cloud-config-openstack-vsphere.yml

# vSphere - AWS or AWS - When using vSphere
$ bosh update-cloud-config ~/workspace/bosh/multi-cpi/cloud-config-vsphere-aws.yml
```

- Apply Cloud Config
```
$ bosh update-cloud-config ~/workspace/bosh/multi-cpi/cloud-config-{iaas}-{iaas}.yml 
```

<br>

### <div id='2.4.4'/>2.4.4. Stemcell Upload
Log in to the installed BOSH and proceed with the IaaS Stemcell upload.
(e.g. Use both AWS and OpenStack environments, execute both of those commands.)  
```
# Use ubuntu-bionic 1.76 which is the same stem cell as paasta-deployment v5.7.1.
# For AWS stem cells, use light stemcells

# AWS
$ bosh upload-stemcell https://storage.googleapis.com/bosh-aws-light-stemcells/1.76/light-bosh-stemcell-1.76-aws-xen-hvm-ubuntu-bionic-go_agent.tgz --fix

# OpenStack
$ bosh upload-stemcell https://storage.googleapis.com/bosh-core-stemcells/1.76/bosh-stemcell-1.76-openstack-kvm-ubuntu-bionic-go_agent.tgz --fix

# vSphere
$ bosh upload-stemcell https://storage.googleapis.com/bosh-core-stemcells/1.76/bosh-stemcell-1.76-vsphere-esxi-ubuntu-bionic-go_agent.tgz --fix
```

<br>


### <div id='2.4.5'/>2.4.5. AP Installation Test with Multi-CPI
After completing the Multi-CPI setup, PaaS-TA AP is installed to test whether communication between each other is smooth.
For a description of the runtime-config setting or variable setting required for PaaS-TA AP, refer to the PaaS-TA AP Guide.

In this guide, among several cases, Diego-cell was installed on OpenStack and the remaining VMs were installed on AWS based on AWS - OpenStack.
Not only Diego-cell but also other VMs can be deployed and deployed across different IaaS. Change the distribution method according to the application settings to proceed with the installation. 

- Go to PaaS-TA AP Installation Folder
```
$ cd ~/workspace/paasta-deployment/paasta
```

- diego-cell zone change
> $ vi vars.yml
```
... ((Skip)) ...

# DIEGO-CELL
diego_cell_azs: ["z4", "z5"]		# Diego-Cell Available Zone
diego_cell_instances: 3			# Diego-Cell Number of Instances

... ((Skip)) ...
```

- PaaS-TA AP Installation
```
$ source deploy-aws.sh
```


After completing PaaS-TA AP installation, push the Test APP to check if the app is working properly.




### [Index](https://github.com/PaaS-TA/Guide-eng/blob/master/README.md) > [AP Install](../README.md) > PaaS-TA Multi CPI
