### [Index](https://github.com/K-PaaS/Guide-eng/blob/master/README.md) > [AP Install](../README.md) > Logging Service

## Table of Contents

1. [Document Outline](#1)  
  1.1. [Purpose](#1.1)  
  1.2. [Range](#1.2)   
  1.3. [References](#1.3)  

2. [Logging Service Installation](#2)  
  2.1. [Prerequisite](#2.1)   
  2.2. [Stemcell Check](#2.2)    
  2.3. [Deployment Download](#2.3)   
  2.4. [Deployment File Modification](#2.4)  
  2.5. [Service Installation](#2.5)      
  2.6. [Sercice Installation Check](#2.6)  
  2.7. [Portal Log API Deployment](#2.7)  
  2.7.1 [Portal VM Type](#2.7.1)  
  2.7.2 [Portal App Type](#2.7.2)

3. [Logging Service Management](#3)  
  3.1. [Enable Logging Service ](#3.1)   



## <div id="1"/> 1. Document Outline

### <div id="1.1"/> 1.1. Purpose

This document (Logging Service Installation Guide) shows how to install the Logging Service using Bosh.  
When installing the Logging Service, user can check the accumulated logs of apps deployed with cf based on the **time of installation**.  
The storage period varies depending on the operating organization, and the default is 7 days.

### <div id="1.2"/> 1.2. Range

The installation range was prepared based on the basic installation to verify the Logging service.

### <div id="1.3"/> 1.3. References
BOSH Document: [http://bosh.io](http://bosh.io)  
Cloud Foundry Document: [https://docs.cloudfoundry.org](https://docs.cloudfoundry.org)  

<br>

# <div id="2"/> 2. Logging 서비스 설치

## <div id="2.1"/> 2.1. Prerequisite

This installation guide is based on installing in a Linux environment.  
To install the service, you need to install **BOSH**, **K-PaaS AP**, and **Portal**.

## <div id="2.2"/> 2.2. Stemcell 확인

Check the Stemcell list to make sure that the Stemcell required for service installation is uploaded.  
Stemcell in this guide uses ubuntu-jammy 1.181.  

> $ bosh -e ${BOSH_ENVIRONMENT} stemcells  

```
Using environment '10.0.1.6' as client 'admin'

Name                                       Version  OS             CPI  CID  
bosh-openstack-kvm-ubuntu-jammy-go_agent   1.181*   ubuntu-jammy   -    5f8c7470-e948-4a6f-99e6-5dffce49026f


(*) Currently deployed

1 stemcells

Succeeded
```  

If the corresponding Stemcell is not uploaded, copy the Stemcell link to the corresponding IaaS environment and version from [bosh.io Stemcell] (https://bosh.io/stemcells/) and run the following command.

```
# Example of Stemcell Upload Command
bosh -e ${BOSH_ENVIRONMENT} upload-stemcell -n {STEMCELL_URL}
```

### <div id="2.3"/> 2.3. Deployment Download

Download the deployment needed from Git Repository and place the file in the service installation directory.  

- Service Deployment Git Repository URL : https://github.com/K-PaaS/service-deployment/tree/v5.1.25.1

```
# Deployment file download, make directory, change directory
$ mkdir -p ~/workspace
$ cd ~/workspace

# Deployment File Download
$ git clone https://github.com/K-PaaS/service-deployment.git -b v5.1.25.1
```

## <div id="2.4"/> 2.4. Deployment File Modification
- Modify 'common_vars.yml' to suit the server environment. 
- Variables used in Logging service are : system_domain, uaa_client_admin_id, and uaa_client_admin_secret.

> $ vi ~/workspace/common/common_vars.yml
```yaml
... ((Skip)) ...

system_domain: "61.252.53.246.nip.io"		# Domain (Same as HAProxy Public IP when using nip.io)
uaa_client_admin_id: "admin"			# UAAC Admin Client Admin ID
uaa_client_admin_secret: "admin-secret"		# Secret Variables for Accessing the UAAC Admin Client

... ((Skip)) ...

```


- Modify the variable files used by Deployment YAML to suit the server environment.

> $ vi ~/workspace/service-deployment/logging-service/vars.yml

```yaml
# STEMCELL
stemcell_os: "ubuntu-jammy"   # Stemcell OS
stemcell_version: "1.181"   # Stemcell Version


# VARIABLE
syslog_forwarder_custom_rule: 'if ($msg contains "DEBUG") then stop'    #  Custom Rule to send from Application Platform Logging Agent
syslog_forwarder_fallback_servers: []
portal_deploy_type: "vm"                        # Application Platform Portal deploy type(vm, app)

# Fluentd
fluentd_azs: ["z4"]                    # fluentd : azs
fluentd_instances: 1                   # fluentd : instances (1)
fluentd_vm_type: "small"               # fluentd : vm type
fluentd_network: "default"             # fluentd network
fluentd_ip: "<FLUENTD_IP>"
fluentd_port: "3514"                   # fluentd Port
fluentd_transport: "tcp"               # fluentd Logging Protocol

# INFLUXDB
influxdb_azs: ["z4"]                            # InfluxDB : azs
influxdb_instances: 1                           # InfluxDB : instances (1)
influxdb_vm_type: "large"                       # InfluxDB : vm type
influxdb_network: "default"                     # InfluxDB network
influxdb_persistent_disk_type: "10GB"           # InfluxDB persistent disk type

influxdb_ip: "<INFLUXDB_IP>"
influxdb_http_port: "8086"                  # default 8086
influxdb_username: "<INFLUXDB_USERNAME>"    # InfluxDB Admin Account Username
influxdb_password: "<INFLUXDB_PASSWORD>"    # InfluxDB Admin Account Password
influxdb_interval: "7d"                     # InfluxDB Retention Policy (bootstrapper)
influxdb_https_enabled: "true"              # InfluxDB HTTPS Setting

influxdb_database: "<INFLUXDB_DATABASE>"          # InfluxDB Database Name
influxdb_measurement: "<INFLUXDB_MEASUREMENT>"    # InfluxDB Measurement Name
influxdb_time_precision: "s"    # hour(h), minutes(m), second(s), millisecond(ms), microsecond(u), nanosecond(ns)

# COLLECTOR
collector_azs: ["z4"]                           # collector : azs
collector_instances: 1                          # collector : instances (1)
collector_vm_type: "small"                      # collector : vm type
collector_network: "default"                    # collector network
```  

## <div id="2.5"/> 2.5. Service Installation

- Modify the VARIABLES settings in the Deploy script file to suit the server environment 

> $ vi ~/workspace/portal-deployment/logging-service/deploy.sh

```
#!/bin/bash

# VARIABLES
COMMON_VARS_PATH="<COMMON_VARS_FILE_PATH>"              # common_vars.yml File Path (e.g. ../../common/common_vars.yml)
BOSH_ENVIRONMENT="${BOSH_ENVIRONMENT}"                  # bosh director alias name (When not using create-bosh-login.sh provided by K-PaaS, check the name at bosh envs and enter)


# Depending on the type of Portal installation and protocol type, bifurcate the option file usage.
PORTAL_DEPLOY_TYPE=`grep portal_deploy_type vars.yml | cut -d "#" -f1`
FLUENTD_TRANSPORT=`grep fluentd_transport vars.yml`


if [[ "${PORTAL_DEPLOY_TYPE}" =~ "app" ]]; then
  if [[ "${FLUENTD_TRANSPORT}" =~ "tcp" ]]; then
    bosh -e ${BOSH_ENVIRONMENT} -d logging-service -n deploy logging-service.yml \
          -o operations/use-protocol-tcp.yml \
          -l vars.yml \
          -l operations/pem.yml \
          -l ${COMMON_VARS_PATH}
  else
    bosh -e ${BOSH_ENVIRONMENT} -d logging-service -n deploy logging-service.yml \
          -l vars.yml \
          -l operations/pem.yml \
          -l ${COMMON_VARS_PATH}
  fi
elif [[ "${PORTAL_DEPLOY_TYPE}" =~ "vm" ]]; then
  if [[ "${FLUENTD_TRANSPORT}" =~ "tcp" ]]; then
    bosh -e ${BOSH_ENVIRONMENT} -d logging-service -n deploy logging-service.yml \
          -o operations/use-protocol-tcp.yml \
          -l vars.yml \
          -l operations/pem.yml \
          -l ${COMMON_VARS_PATH}
  else
    bosh -e ${BOSH_ENVIRONMENT} -d logging-service -n deploy logging-service.yml \
          -l vars.yml \
          -l operations/pem.yml \
          -l ${COMMON_VARS_PATH}
  fi
else
  echo "Logging Service can't install. Please check 'portal_deploy_type'."
fi
```

- Install service.  
```
$ cd ~/workspace/service-deployment/logging-service
$ source deploy.sh  
```  


## <div id="2.6"/> 2.6. Service Installation Check

Check the installed service.  

> $ bosh -e ${BOSH_ENVIRONMENT} -d logging-service vms
```
Using environment '10.0.1.6' as client 'admin'

Task 193. Done

Deployment 'logging-service'

Instance                                        Process State  AZ  IPs         VM CID                                VM Type  Active  Stemcell  
collector/542d5b3f-6243-4bea-a8a9-185edba2cbee  running        z1  10.0.1.162  67ad8e85-2ac2-420e-bb71-14fbd7e43013  small    true    bosh-openstack-kvm-ubuntu-jammy-go_agent/1.181  
fluentd/4ec53005-6390-401f-a569-285d1d9bcb95    running        z1  10.0.1.105  2f5d3ab2-787e-4034-ac6f-fb5b75fc29ec  small    true    bosh-openstack-kvm-ubuntu-jammy-go_agent/1.181  
influxdb/e8fc41b5-e5ed-4176-ad5b-216d2cccf88b   running        z1  10.0.1.115  7654584c-ee0c-42df-bef7-d070362eb878  large    true    bosh-openstack-kvm-ubuntu-jammy-go_agent/1.181

```

## <div id="2.7"/> 2.7. Portal Log API Deployment

To enable Logging service, Portal Log API must be deployed.  
When Log API is already deployed when deploying Portal, continue from [3. Logging Service Management](#3).

### <div id="2.7.1"/> 2.7.1. Portal VM Type

- Modify the variable file used in Portal API.

> $ vi ~/workspace/portal-deployment/portal-api/vars.yml

```
...
# PORTAL_LOG_API INFO
log_api_azs: [z6]                                               # portal-log-api : azs
log_api_instances: 1                                            # portal-log-api : instances (1)
log_api_vm_type: "small"                                        # portal-log-api : vm type
log_api_infra_admin: false                                      # portal-log-api : infra admin (default "false")

log_api_influxdb_ip: "10.0.1.115"                               # portal-log-api : InfluxDB IP
log_api_influxdb_http_port: "8086"                              # portal-log-api : InfluxDB HTTP PORT (default 8086)
log_api_influxdb_username: "admin"                              # portal-log-api : InfluxDB Admin Account Username
log_api_influxdb_password: "K-PaaS2022"                        # portal-log-api : InfluxDB Admin Account Password
log_api_influxdb_https_enabled: true                            # portal-log-api : InfluxDB HTTPS Setting (default "true")

log_api_influxdb_database: "logging_db"                         # portal-log-api : InfluxDB Database Name
log_api_influxdb_measurement: "logging_measurement"             # portal-log-api : InfluxDB Measurement Name
log_api_influxdb_query_limit: "50"                              # portal-log-api : InfluxDB query limit (default "50")

...

```
- Install Service.

```
$ cd ~/workspace/portal-deployment/portal-api   
$ sh ./deploy.sh  
```

- Check the installed Service.

> $ bosh -e ${BOSH_ENVIRONMENT} -d portal-api vms
```diff
  Using environment '10.0.1.6' as client 'admin'

  Task 4823. Done
  
 Deployment 'portal-api'

 Instance                                                          Process State  AZ  IPs            VM CID                                   VM Type        Active  
  binary_storage/9f58a9b7-2a3d-4ee9-8975-7b04b99c0a21               running        z5  10.30.107.212  vm-e65ad396-ce65-4ef0-962d-5c54fa411769  portal_large   true  
  haproxy/8cc2d633-2b43-4f3d-a2e8-72f5279c11d5                      running        z5  10.30.107.213  vm-315bfa1b-9829-46de-a19d-3bd65e9f9ad4  portal_large   true  
                                                                                       115.68.46.214
  mariadb/117cbf05-b223-4133-bf61-e15f16494e21                      running        z5  10.30.107.211  vm-bc5ae334-12d4-41d4-8411-d9315a96a305  portal_large   true  
  ap-portal-api/48fa0c5a-52eb-4ae8-a7b9-91275615318c                running        z5  10.30.107.217  vm-9d2a1929-0157-4c77-af5e-707ec496ed87  portal_medium  true  
  ap-portal-common-api/060320fa-7f26-4032-a1d9-6a7a41a044a8         running        z5  10.30.107.219  vm-f35e9838-74cf-40e0-9f97-894b53a68d1f  portal_medium  true  
  ap-portal-gateway/6baba810-9a4a-479d-98b2-97e5ba651784            running        z5  10.30.107.214  vm-7ec75160-bf34-442e-b755-778ae7dd3fec  portal_medium  true  
+ ap-portal-log-api/a4460008-42b5-4ba0-84ee-fff49fe6c1bd            running        z5  10.30.107.218  vm-9ec0a1b0-09f6-415b-8e23-53af91fd94b8  portal_medium  true  
  ap-portal-registration/3728ed73-451e-4b93-ab9b-c610826c3135       running        z5  10.30.107.215  vm-c4020514-c458-41c6-bcbc-7e0ee1bc6f42  portal_small   true  
  ap-portal-storage-api/2940366a-8294-4509-a9c0-811c8140663a        running        z5  10.30.107.220  vm-79ad6ee1-1bb5-4308-8b71-9ed30418e2c1  portal_medium  true  
  ...

  9 vms

  Succeeded
```
### <div id="2.7.2"/> 2.7.2. Portal App Type

- Modify the Manifest file used in Portal App.

> $ vi ~/workspace/portal-container-infra/portal-app/portal-app-1.2.14.1/portal-log-api-2.3.2.1/manifest.yml
```yaml
applications:
- name: portal-log-api
  memory: 1G
  instances: 1
  buildpacks:
  - java_buildpack
  path: ap-portal-log-api.jar
  env:

    ...

    ### logging info (InfluxDB)
    influxdb_ip: 10.0.1.115
    influxdb_url: https://10.0.1.42:9096
    influxdb_username: joy
    influxdb_password: joy
    influxdb_database: logging_https
    influxdb_measurement: logging_app_udp
    influxdb_limit: 30
    influxdb_httpsEnabled: true

```

- Deploy App.

```
$ cd ~/workspace/portal-container-infra/portal-app/portal-app-1.2.14.1/portal-log-api-2.3.2.1   
$ cf push  
Pushing app portal-log-api to org portal / space system as admin...
Applying manifest file /home/ubuntu/workspace/portal-deployment/portal-container-infra/portal-app/portal-app-1.2.14.1/portal-log-api-2.3.2.1/manifest.yml...
...
Waiting for app portal-log-api to start...
Instances starting...
Instances starting...
Instances starting...
Instances starting...
Instances starting...
Instances starting...
Instances starting...
name:              portal-log-api
requested state:   started
routes:            portal-log-api.61.252.53.246.nip.io
last uploaded:     Wed 28 Jun 04:01:52 UTC 2023
stack:             cflinuxfs3
buildpacks:        
	name             version                                                         detect output   buildpack name
	java_buildpack   v4.50-git@github.com:cloudfoundry/java-buildpack.git#5fe41f89   java            java
type:            web
sidecars:        
instances:       1/1
memory usage:    1024M
start command:   JAVA_OPTS="-agentpath:$PWD/.java-buildpack/open_jdk_jre/bin/jvmkill-1.17.0_RELEASE=printHeapHistogram=1 -Djava.io.tmpdir=$TMPDIR -XX:ActiveProcessorCount=$(nproc)
                 -Djava.ext.dirs=$PWD/.java-buildpack/container_security_provider:$PWD/.java-buildpack/open_jdk_jre/lib/ext -Djava.security.properties=$PWD/.java-buildpack/java_security/java.security $JAVA_OPTS" &&
                 CALCULATED_MEMORY=$($PWD/.java-buildpack/open_jdk_jre/bin/java-buildpack-memory-calculator-3.13.0_RELEASE -totMemory=$MEMORY_LIMIT -loadedClasses=22025 -poolType=metaspace -stackThreads=250
                 -vmOptions="$JAVA_OPTS") && echo JVM Memory Configuration: $CALCULATED_MEMORY && JAVA_OPTS="$JAVA_OPTS $CALCULATED_MEMORY" && MALLOC_ARENA_MAX=2 SERVER_PORT=$PORT eval exec
                 $PWD/.java-buildpack/open_jdk_jre/bin/java $JAVA_OPTS -cp $PWD/. org.springframework.boot.loader.JarLauncher
     state     since                  cpu    memory        disk       logging               details
#0   running   2023-06-28T04:02:13Z   0.0%   46.5K of 1G   8K of 1G   1.2K/s of unlimited   
type:            task
sidecars:        
instances:       0/0
memory usage:    1024M
start command:   JAVA_OPTS="-agentpath:$PWD/.java-buildpack/open_jdk_jre/bin/jvmkill-1.17.0_RELEASE=printHeapHistogram=1 -Djava.io.tmpdir=$TMPDIR -XX:ActiveProcessorCount=$(nproc)
                 -Djava.ext.dirs=$PWD/.java-buildpack/container_security_provider:$PWD/.java-buildpack/open_jdk_jre/lib/ext -Djava.security.properties=$PWD/.java-buildpack/java_security/java.security $JAVA_OPTS" &&
                 CALCULATED_MEMORY=$($PWD/.java-buildpack/open_jdk_jre/bin/java-buildpack-memory-calculator-3.13.0_RELEASE -totMemory=$MEMORY_LIMIT -loadedClasses=22025 -poolType=metaspace -stackThreads=250
                 -vmOptions="$JAVA_OPTS") && echo JVM Memory Configuration: $CALCULATED_MEMORY && JAVA_OPTS="$JAVA_OPTS $CALCULATED_MEMORY" && MALLOC_ARENA_MAX=2 SERVER_PORT=$PORT eval exec
                 $PWD/.java-buildpack/open_jdk_jre/bin/java $JAVA_OPTS -cp $PWD/. org.springframework.boot.loader.JarLauncher
There are no running instances of this process.
``` 
<br>

# <div id="3"/>3.  Logging Service Management

After the service installation is complete, register the Logging service activation code to use the service in the K-PaaS AP portal.

## <div id="3.1"/>  3.1. Enable Logging Service

-	Access the K-PaaS operator portal and log in.
![002]

-	Go to the code management menu of the operation management and register the code as follows.

> ※ Group Table  
> Code ID  : LOGGING  
> Code Name : Logging Service  

![003]

> ※ Detail Table  
> Key : enable_logging_service  
> Value : true  
> Outline : Logging Service Enable Code   
> Use : Y  

![004]

![005]


[002]:./images/logging-service/image002.png
[003]:./images/logging-service/image003.png
[004]:./images/logging-service/image004.png
[005]:./images/logging-service/image005.png

### [Index](https://github.com/K-PaaS/Guide-eng/blob/master/README.md) > [AP Install](../README.md) > Logging Service
