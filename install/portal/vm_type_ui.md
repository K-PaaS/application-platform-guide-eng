### [Index](https://github.com/PaaS-TA/Guide-eng/blob/master/README.md) > [AP Install](../README.md) > Portal VM Type UI

## Table of Contents

1. [Document Outline](#1)  
    1.1. [Purpose](#1.1)  
    1.2. [Range](#1.2)  
    1.3. [References](#1.3)  

2. [PaaS-TA AP Portal UI Installation](#2)  
    2.1. [Prerequisite](#2.1)   
    2.2. [Stemcell Check](#2.2)    
    2.3. [Deployment Download](#2.3)   
    2.4. [Deployment File Modification](#2.4)  
    2.5. [Service Installation](#2.5)    
    2.6. [Service Installation Check](#2.6)  
    2.7. [Portal SSH Installation](#2.7)  

3. [PaaS-TA AP Portal Operation](#3)  
    3.1. [Enable the user's organizational creation flag](#3.1)  
    3.2. [User Portal UAA Page Error](#3.2)  
    3.3. [Operator's Portal User Page Inquiry error](#3.3)  
    3.4. [Log](#3.4)  
    3.5. [Apply Catalog](#3.5)   


## <div id="1"/> 1. Document Outline
### <div id="1.1"/> 1.1. Purpose

This document (PaaS-TA AP Portal UI Installation Guide) describes how to install PaaS-TA AP Portal UI using BOSH.

### <div id="1.2"/> 1.2. Range
The installation range was created based on the basic installation of the Portal UI to verify the PaaS-TA AP Portal.

### <div id="1.3"/> 1.3. References
BOSH Document: [http://bosh.io](http://bosh.io)  
Cloud Foundry Document: [https://docs.cloudfoundry.org](https://docs.cloudfoundry.org)  

## <div id="2"/> 2. PaaS-TA AP Portal UI Installation

### <div id="2.1"/> 2.1. Prerequisite  

This installation guide is based on installation in a Linux environment.
To install the service pack, BOSH CLI v2 must be installed and logged in to BOSH.<br>
If BOSH CLI v2 is not installed, you must first refer to the BOSH 2.0 Installation Guide document to install BOSH CLI v2 and be familiarize on the usage.<br>
If the UAA client is not installed, the UAA client needs to be installed.

- UAA client Installation (BOSH Dependency Installation Required)
```
$ sudo gem install cf-uaac
$ uaac -v
```

### <div id="2.2"/> 2.2. Stemcell Check

Check the list of Stemcells to verify that the Stemcells required for service installation are uploaded.
The Stemcell in this guide uses ubuntu-jammy  1.181.

> $ bosh -e ${BOSH_ENVIRONMENT} stemcells

```
Using environment '10.0.1.6' as client 'admin'

Name                                       Version   OS             CPI  CID  
bosh-openstack-kvm-ubuntu-jammy-go_agent  1.181      ubuntu-jammy  -    ce507ae4-aca6-4a6d-b7c7-220e3f4aaa7d

(*) Currently deployed

1 stemcells

Succeeded
```

If the corresponding Stemcell is not uploaded, copy the Stemcell link to the corresponding IaaS environment and version from bosh.io Stemcell [bosh.io Stemcell](https://bosh.io/stemcells/) and run the following command.

```
# Example of Stemcell upload command
$ bosh -e ${BOSH_ENVIRONMENT} upload-stemcell -n {STEMCELL_URL}
```

### <div id="2.3"/> 2.3. Deployment Download  

Download the deployment needed from Git Repository and place the file at the service installation directory

- Portal Deployment Git Repository URL : https://github.com/PaaS-TA/portal-deployment/tree/v5.2.23

```
# Deployment File Download , make directory, change directory
$ mkdir -p ~/workspace
$ cd ~/workspace

# Deployment File Download
$ git clone https://github.com/PaaS-TA/portal-deployment.git -b v5.2.23
```

### <div id="2.4"/> 2.4. Deployment File Modification

The BOSH Deployment manifest is a YAML file that defines the properties of the Component elements and the deployment.
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
- The variables used in the PaaS-TA AP Portal UI are system_domain, paasta_api_version, uaa_client_portal_secret, portal_web_user_language, and portal_web_admin_language.

> $ vi ~/workspace/common/common_vars.yml
```
... ((Skip)) ...

system_domain: "61.252.53.246.nip.io"		# Domain (Same as HAProxy Public IP when using nip.io)
paasta_api_version: "v3"
uaa_client_portal_secret: "clientsecret"	# Secret variables for accessing the UAAC Portal Client
portal_web_user_language: ["ko", "en"]             # portal webuser language list (e.g. ["ko", "en"])
portal_web_admin_language: ["ko", "en"]             # portal webadmin language list (e.g. ["ko", "en"])

... ((Skip)) ...
```



- Modify the variable files used by Deployment YAML to suit server environment.

> $ vi ~/workspace/portal-deployment/portal-ui/vars.yml  
```
# STEMCELL INFO
stemcell_os: "ubuntu-jammy"                                             # stemcell os
stemcell_version: "1.181"                                                 # stemcell version

# NETWORKS INFO
private_networks_name: "default"                                         # private network name
public_networks_name: "vip"                                              # public network name

# MARIADB INFO
mariadb_azs: [z6]                                                        # mariadb : azs
mariadb_instances: 1                                                     # mariadb : instances (1)
mariadb_vm_type: "minimal"                                               # mariadb : vm type
mariadb_persistent_disk_type: "10GB"                                     # mariadb : persistent disk type
mariadb_port: "<MARIADB_PORT>"                                           # mariadb : database port (e.g. 13306) -- Do Not Use "3306"
mariadb_admin_password: "<MARIADB_ADMIN_PASSWORD>"                       # mariadb : database admin password (e.g. "mariadb")

# HAPROXY INFO
haproxy_azs: [z7]                                                        # haproxy : azs
haproxy_instances: 1                                                     # haproxy : instances (1)
haproxy_vm_type: "small"                                                 # haproxy : vm type
haproxy_public_ips: "<HAPROXY_PUBLIC_IPS>"                               # haproxy : public ips (e.g. "00.00.00.00")
haproxy_infra_admin: false                                               # haproxy : infra admin (default "false")

# PORTAL_WEBADMIN INFO
webadmin_azs: [z6]                                                       # webadmin : azs
webadmin_instances: 1                                                    # webadmin : instances (1)
webadmin_vm_type: "small"                                                # webadmin : vm type

# PORTAL_WEBUSER INFO
webuser_azs: [z6]                                                        # webuser : azs
webuser_instances: 1                                                     # webuser : instances (1)
webuser_vm_type: "small"                                                 # webuser : vm type
webuser_monitoring: false                                                # webuser : Whether monitoring is used. If true, the monitoring window is activated in the app details.
webuser_automaticapproval: false                                         # webuser : Whether you can access PaaS-TA when signing up. If true, you must approve it on the administrator portal to access it.
user_app_size: 0                                                         # webuser :Limit capacity when deploying user myApp (unlimited if value is 0)

# ETC INFO
paasta_deployment_type: "ap"                                             # ETC : Paas-TA deployment type (ap, sidecar)
portal_default_api_name: "PaaS-TA"                                       # ETC : default api name (e.g. PaaS-TA {Version})
portal_default_api_url: "http://<PORTAL-API-HAPROXY-PUBLIC-IP>:2225"     # ETC : default api url
portal_default_header_auth: "Basic YWRtaW46b3BlbnBhYXN0YQ=="             # ETC : default header auth
portal_default_api_desc: "PaaS-TA infra"                                 # ETC : default api description (e.g. PaaS-TA {Version} infra)
apache_limit_request_body: <APACHE limitRequestBody>                     # Apache Limiting Upload File Size Directory / ex> 5000000
apache_usr_limit_request_body: <APACHE limitRequestBody>                 # Apache Limiting Upload File Size Directory webDir ex> 10240000
```

### <div id="2.5"/> 2.5. Service Installation

- Modify the VARIABLES settings in the Deploy script file to match your server environment, and decide whether to add the option file.
     (Optional) -o operations/cce.yml (install with CCE applied)

> $ vi ~/workspace/portal-deployment/portal-ui/deploy.sh
```
#!/bin/bash

# VARIABLES
COMMON_VARS_PATH="<COMMON_VARS_FILE_PATH>"	# common_vars.yml File Path (e.g. ../../common/common_vars.yml)
CURRENT_IAAS="${CURRENT_IAAS}"			# IaaS Information (When not using create-bosh-login.sh provided by PaaS-TA, enter aws/azure/gcp/openstack/vsphere)
BOSH_ENVIRONMENT="${BOSH_ENVIRONMENT}"		# bosh director alias name (When not using create-bosh-login.sh provided by PaaS-TA, check the name at bosh envs and enter)

# DEPLOY
bosh -e ${BOSH_ENVIRONMENT} -n -d portal-ui deploy portal-ui.yml \
    -o operations/${CURRENT_IAAS}-network.yml \
    -o operations/cce.yml \
    -l ${COMMON_VARS_PATH} \
    -l vars.yml
```

- Install Service 
```
$ cd ~/workspace/portal-deployment/portal-ui   
$ sh ./deploy.sh  
```

### <div id="2.6"/> 2.6. Service Installation Check

Check the installed service.

> $ bosh -e ${BOSH_ENVIRONMENT} -d portal-ui vms  

```
Using environment '10.0.1.6' as client 'admin'

Task 4823. Done

Deployment 'portal-ui'

Instance                                                      Process State  AZ  IPs            VM CID                                   VM Type       Active  
haproxy/5c30c643-94d1-491c-9f6c-e72de4b0e6a4                  running        z7  10.30.56.10    vm-891ff2dd-4ee0-4c42-8fa8-b2d0cf0b8537  portal_tiny   true  
									         115.68.46.180                                                           
mariadb/19bf81a9-cde9-432b-87ca-cbac1f28854a                  running        z6  10.30.56.9     vm-7a6f8042-e9b8-434c-abbf-776bbfd3386d  portal_small  true  
paas-ta-portal-webadmin/bc536f61-10bd-4702-af5f-5e63500e110e  running        z6  10.30.56.11    vm-176ccac5-f154-4420-b821-9ed30a18f3e2  portal_small  true  
paas-ta-portal-webuser/409c038b-d013-41d3-b6b2-aebb4a02d908   running        z6  10.30.56.12    vm-d9cf481f-64c7-45fd-aadb-e4eb1b31945a  portal_tiny   true  

4 vms

Succeeded
```

### <div id="2.7"/> 2.7. Portal SSH Installation

From Portal 5.1.0 and above, SSH access to deployed applications is possible.

For this, deploy the Portal SSH App must be done first.

- Create organizations and spaces for portal deployments
```
### Create and set up organizations and spaces for portal deployments
$ cf create-quota portal_quota -m 20G -i -1 -s -1 -r -1 --reserved-route-ports -1 --allow-paid-service-plans
$ cf create-org portal -q portal_quota
$ cf create-space system -o portal  
$ cf target -o portal -s system
```


- Portal SSH Download and Deployment
```
$ cd ~/workspace/portal-deployment
$ wget --content-disposition https://nextcloud.paas-ta.org/index.php/s/awPjYDYCMiHY7yF/download
$ unzip portal-ssh.zip
$ cd portal-ssh
$ cf push
```


## <div id="3"/>3. PaaS-TA AP Portal Opreration

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
![paas-ta-portal-1]
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

![paas-ta-portal-2]
1. If url is registered incorrectly in the uaac portal client, a redirect error occurs as shown on the screen.
2. The url should be modified through the uaac client update.
   > $ uaac target\
    $ uaac token client get\
   Client ID:  admin\
   Client secret:  *****
3. uaac client update portalclient --redirect_uri "userportal Url, userportal Url/callback"
    
    >$ uaac client update portalclient --redirect_uri "http://portal-web-user.xxxx.nip.io, http://portal-web-user.xxxx.nip.io/callback"

### <div id="3.3"/> 3.3. Operator's Portal User Page inquiry error
1. If the information cannot be retrieved and an error occurs when the page is moved, the DB information config must be modified and restarted after moving to the common-api VM.

### <div id="3.4"/> 3.4. Log
You can check the log of each instance on the Paas-TA Portal.
1. Access the Instance to check the logs.
    > bosh ssh -d [deployment name] [instance name]

       Instance                                                          Process State  AZ  IPs            VM CID                                   VM Type        Active   
       haproxy/8cc2d633-2b43-4f3d-a2e8-72f5279c11d5                      running        z5  10.30.107.213  vm-315bfa1b-9829-46de-a19d-3bd65e9f9ad4  portal_large   true  
                                                                                            115.68.46.214                                                            
       mariadb/117cbf05-b223-4133-bf61-e15f16494e21                      running        z5  10.30.107.211  vm-bc5ae334-12d4-41d4-8411-d9315a96a305  portal_large   true  
       paas-ta-portal-webadmin/8047fcbd-9a98-4b61-b161-0cbb277fa643      running        z5  10.30.107.221  vm-188250fd-e918-4aab-9cbe-7d368852ea8a  portal_medium  true  
       paas-ta-portal-webuser/cb206717-81c9-49ed-a0a8-e6c3b957cb66       running        z5  10.30.107.222  vm-822f68a5-91c8-453a-b9b3-c1bbb388e377  portal_medium  true  

       11 vms

       Succeeded
       inception@inception:~$ bosh ssh -d paas-ta-portal-ui paas-ta-portal-webadmin  << Enter the instance access (bosh ssh) command
       Using environment '10.30.40.111' as user 'admin' (openid, bosh.admin)

       Using deployment 'paas-ta-portal-webadmin'

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

       paas-ta-portal-webadmin/48fa0c5a-52eb-4ae8-a7b9-91275615318c:~$

2. Go to the folder where log file is located.
    > Location : /var/vcap/sys/log/[job name]/

         paas-ta-portal-webadmin/48fa0c5a-52eb-4ae8-a7b9-91275615318c:~$ cd /var/vcap/sys/log/paas-ta-portal-webadmin/
         paas-ta-portal-webadmin/48fa0c5a-52eb-4ae8-a7b9-91275615318c:/var/vcap/sys/log/paas-ta-portal-webadmin$ ls
         paas-ta-portal-webadmin.stderr.log  paas-ta-portal-webadmin.stdout.log

3. Open log file and check the contents.
    > vim [job name].stdout.log

        Ex.)
        vim paas-ta-portal-webadmin.stdout.log
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

### <div id="3.5"/> 3.5. Apply Catalog
##### 1. Add Catalog buildpack and servicepack
After installing the Paas-TA Portal, you must register the build pack and service pack on the administrator portal to use it on the user portal.
- [Download Catalog Image](https://nextcloud.paas-ta.org/index.php/s/EmzfJw38H4GQKTr/download)

 1. Access the Administrator Portal.(portal-web-admin.[public ip].nip.io)
 ![paas-ta-portal-3]
 2. Click Operation Management.
 ![paas-ta-portal-4]
 3. Go to Catalog page.
 ![paas-ta-portal-5]
 4. Go to the buildpack and servicepack detail page, enter the value in each index, and click Save.
 ![paas-ta-portal-6]

    ※ The catalog management code is required when registering and modifying catalogs. When there is no code available as of the moment, follow the instruction below.
    1. ① Click "Manage Code".
    2. From the **Group Table**, click the corresponding ② "Code Category".
    3. Click the "Register" button to add the catalog management code to the **Detail Table** and use it.
    ![paas-ta-portal-7]
 5. Check whether the changed value is applied in the user portal.
 ![paas-ta-portal-8]


[paas-ta-portal-1]:./images/Portal_1.jpg
[paas-ta-portal-2]:./images/Portal_2.jpg
[paas-ta-portal-3]:./images/Portal_3.png
[paas-ta-portal-4]:./images/Portal_4.png
[paas-ta-portal-5]:./images/Portal_5.png
[paas-ta-portal-6]:./images/Portal_6.png
[paas-ta-portal-7]:./images/Portal_7.png
[paas-ta-portal-8]:./images/Portal_8.png
[paas-ta-portal-16]:./images/Portal_16.png



### [Index](https://github.com/PaaS-TA/Guide-eng/blob/master/README.md) > [AP Install](../README.md) > Portal VM Type UI
