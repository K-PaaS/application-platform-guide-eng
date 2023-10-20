### [Index](https://github.com/K-PaaS/Guide-eng/blob/master/README.md) > [AP User Guide](../README.md) > On-Demand Service Development

## Table of Contents
1. [Document Outline](#1)
    * [1.1. Purpose](#11)
    * [1.2.Range](#12)
    * [1.3. References](#13)
2. [On-Demand Service Outline](#2)
    * [2.1. On-Demand Service Architecture and Process](#21)
    * [2.2. Term Definition](#22)
3. [On-Demand Service Broker API](#3)
    * [3.1. Service Architecture](#31)
    * [3.2. Service Broker API Architecture](#32)
    * [3.3. Pivotal(Cloud Foundry) Marketplace Model](#33)
4. [On-Demand Service Broker API Development Guide](#4)
    * [4.1. Development Guide](#41)
    * [4.2. Service and VM Instance Creating API Guide](#42)
    * [4.3. On-Demand Service-Broker Implementation Source Development Guide](#43)
    * [4.4. On-Demand Release Development Guide](#44)
    * [4.5. On-Demand Deployment Development Guide](#45)

# <a name="1"/>1. Document Outline

### <a name="11"/>1.1. Purpose

This document (Development Guide\_OnDemand) guides the on-demand service development standards for open cloud platform projects and includes the contents from the on-demand service architecture to testing.

Through this guide document, we intend to improve the efficiency and maintenance of on-demand service development by increasing the understanding of on-demand service. In addition, on-demand services developed according to the presented standards ensure functionality and integrity in open cloud platforms.

### <a name="12"/>1.2. Range
The range of this document is limited to the on-demand service development related to
the open cloud platform project, except for other open source introductions.

### <a name="13"/>1.3. References

# <a name="2"/>2. On-Demand Service Outline
The on-demand service broker is developed using the Java Spring Framework.
On-demand prevents reckless waste of resources because the provider produces and
provides the service when the user requests the service.
Currently, the on-demand service is provided only in the dedicating form.
This chapter defines the terms used and describes the on-demand architecture.

### <a name="21"/>2.1. On-Demand Service Architecture and Process

![On-Demand_Image_01]

**Picture 2-1 On-demand service architecture on an open cloud platform**

![On-Demand_Image_02]

**Picture 2-2 On-demand service processes on an open cloud platform**

The on-demand service deployed in the open cloud platform provides four major processes:
Service Registration(Create_Service), Service Binding(Service_binding),
Service Preparation(Service_inprogress), Service Deletion(Service_delete)


When a user requests to deploy an application to an open cloud platform,
the deployment process begins. Each step does the following:

-   **Service Registration (Create_Service):** Creates the service when service creation is requested.

-   **Service Preparation (Service_inprogress):** Creates the VM needed when executing the service.

-   **Service Binding (Service_binding):** Applies the service's environment settings at the application's environment setting.

-   **Service Deletion (Service_delete):** Deletes (stop) the service when the user requests it.

When a user requests a service, a VM is created to run the service. The in-progress service is converted to the ready state,
and after the VM is created, the corresponding service is converted to the complete state and provides the service to the user.

### <a name="22"/>2.2. Term Definition

Before explaining the architecture, some terms used in this document are summarized below.

-   **On-Demand: 'As long as there is a demand (at any time)'~ In other words,
      it is a generic term for a system or strategy in which demand determines everything and is not supply-oriented.


-   **Application**: In an open cloud platform, an application is a unit of deployment.
      That is, it refers to a combination of a file in a source code or packaged form
      (eg, .war) and a file defining additional information (meta) to be used during deployment.

-   **Service**: The service is used in an open cloud platform by implementing a cloud controller client API called the Service Broker API.
The Services API is a version of the independent cloud controller API. This makes external applications available on the platform.

# <a name="3"/>3. On-Demand Service Broker API
The open cloud platform Service API defines the protocol between the Cloud Controller and the Service Broker. Broker is implemented in HTTP (or HTTPS) endpoints URI format. One or more services may be provided by one broker, and load balancing and horizontal scalability may be provided.

#### <a name="31"/>3.1. Service Architecture
![On-Demand_Image_03]  
[Picture Source]: http://docs.cloudfoundry.org/services/overview.html


Services are used in an open cloud platform by implementing a cloud controller client API
called Service Broker API. The Services API is an independent version of the cloud controller API.
This makes external applications available on the platform. (database, message queue, rest endpoint, etc)

#### <a name="32"/>3.2. Service Broker API Architecture
![On-Demand_Image_04]  
[Picture Source]: http://docs.cloudfoundry.org/services/api.html

The Open Cloud Platform Service API is a protocol between Cloud Controller and Service Broker (catalog, provision, deprovision, update provision plan, bind, unbound), and the Service Broker implements it as a RESTful API and registers it with the Cloud Controller.

#### <a name="33"/>3.3. Pivotal(Cloud Foundry) Marketplace Model
![On-Demand_Image_05]  
[Picture Source]: http://www.slideshare.net/platformcf/cloud-foundry-marketplacepowered-by-appdirect

It is a leader in cloud service marketplace and management solutions, and has established a marketplace of many global companies. (Samsung, Cloud Foundry, ETC)
AppDirect provides Cloud Foundry service brokerages and additional services.

Description of Service Provider and Cloud Foundry integration
![On-Demand_Image_06]
[Picture Source]: http://www.slideshare.net/platformcf/cloud-foundry-marketplacepowered-by-appdirect

# <a name="4"/>4. On-Demand Service Broker API Development Guide

#### <a name="41"/>4.1. Development Guide
The method of implementing the service is up to the provider and developer. Open cloud platforms require service providers to implement six Service Broker APIs. At this time, using the 2.4 Pivotal Marketplace Model, it can be provided using AppDirect's brokerage function in consultation with the service provider provided by AppDirect. Broker can also be implemented as a separate application or by adding the HTTP endpoint required for existing services.

The basic service broker API guide (Document URL) is referred to and development proceeds.
Currently, on-demand service broker development supports only JAVA.


The environment file (application.yml) of the On-Demand service broker is as follows The value of {} should be placed according to the conditions. (Can be modified when deploying)
```
server:
  port: 8080

bosh:
  client_id: {bosh_client_admin_id}                               
  client_secret: {bosh_client_secret}
  url: {bosh_diretor_url}
  oauth_url: {bosh_diretor_url:8443}
  deployment_name: {on-demand-service-broker}
  instance_name: {on-demand-service-instance name}

spring:
  application:
    name: ap-on-demand-broker
  datasource:
    driver-class-name: com.mysql.jdbc.Driver
    url: "jdbc:mysql://{on_demand_service_broker_database_url}/on-demand?autoReconnect=true&useUnicode=true&characterEncoding=utf8"
    username: root
    password: {Broker_Database_password}
  jpa:
    hibernate:
      ddl-auto: none
      database: mysql
      show-sql: true

serviceDefinition:
  id: 54e2de61-de84-4b9c-afc3-88d08aadfcb6
  name: redis
  desc: "A Application Platform source control service for application development.provision parameters : parameters {owner : owner}"
  bindable: true
  planupdatable: false
  bullet:
    name: 100
    desc: 100
  plan1:
    id: 2a26b717-b8b5-489c-8ef1-02bcdc445720
    name: dedicated-vm
    desc: Redis service to provide a key-value store
    type: A
  org_limitation: 1
  space_limitation: 1

cloudfoundry:
  cc:
    api:
      url: https://api.{cloudfoundry_url}
      uaaUrl: https://uaa.{cloudfoundry_url}  # YOUR UAA API URL
      sslSkipValidation: true
  user:
    admin:
      username: {cloudfoundry_admin_id} # YOUR CF ADMIN ACCOUNT
      password: {cloudfoundry_admin_password}# YOUR CF ADMIN PASSWORD

instance:
  password: admin_test
  port: 6379

```

##### <a name="42"/>Service and VM Instance Creation API Guide
Note: The On-Demand service broker uses the Bosh API.
The Bosh API to implement the On-Demand format is as follows.


1. Bosh Api Setting
<br/>
1.1.   BoshDirector
<br/>
Log in to Bosh Director and receive tokens to save and create an object with access to the Bosh API.

##### parameter

    client_id :: string
    client_secret :: string
    bosh_url :: string
    oauth_url :: string

##### Request

    POST oauth_url + "/oauth/token?client_id=" + client_id + "&client_secret=" + client_secret + "&grant_type=client_credentials

##### Response
    OAuth2AccessToken

2. ServiceInstace
<br/>
2.1 VMInstance
<br/>
    Get the VMInstance information deployed in Bosh.

##### parameter

    deployment_name :: string
    instance_name :: string

##### Request

    Get bosh_url + "/deployments/" + deployment_name + "/instances?format=full"

    The way to search with format=full is to generate a task by Bosh itself, find the task with a redirect, and return a value.

    As an error occurs in the redirect part when requesting an api with the default Resttemplate, you need to create and request a Resttemplate so that the redirect function is not performed.

##### Response
     task :: string


2.2. TaskResult
<br/>
Check the Bosh task. (option: output=result) You can check the detailed value of the deployment.

##### parameter

    task :: string

##### Request

    Get bosh_url + "/deployments/" + deployment_name + "/instances?format=full"

    The way to search with format=full is to generate a task by Bosh itself, find the task with a redirect, and return a value.

    As an error occurs in the redirect part when requesting an api with the default Resttemplate, you need to create and request a Resttemplate so that the redirect function is not performed.

##### Response
    result :: string
	Bosh API customizes and returns it in List<Map> format.


2.3. GetLock
<br/>
It is a function that inquires whether the deployment is locked before the deployment update.

##### parameter

    null

##### Request

    Get bosh_url + "/locks"

##### Response
    result :: string
    It is returned in Json format from Bosh API.

2.4. UpdateInstance
<br/>
Update the instance status (stop --> start)

##### parameter

    deployment_name :: string
    instance_name :: string
    instance_id :: string
    type :: string

   The type is set to "started" or "detached".
  
##### Request

    Get bosh_url + "/deployments/" + deployment_name + "/jobs/" + job_name + "/" + job_id + "?state=" + state

##### Response
    null

2.5. GetTaskID
<br/>
Among the tasks currently in operation (state = queued, processing, and canceling), the ID such as the corresponding deployment_name is queried.

##### parameter

    deployment_name :: string

##### Request

    Get bosh_url + "/tasks?state=queued,processing,cancelling"

##### Response
    List<map>

##### StartInstance
Run the Instance VM.(stop -> start)

##### parameter

    task_id :: string
    instance_name :: string
    instance_id :: string

##### Request

    Get bosh_url + "/tasks/" + task_id + "/output?type=debug"

##### Response
    String

##### CreateInstance
It is a function that creates one VM by increasing the number of Instances when there is no VM to allocate when a service application comes in.

##### parameter

    deployment_name :: string
    instance_name :: string

##### Request

    Post bosh_url + "/deployments"

##### body
The contents of the manifest.ymml configuration required for bosh deployment should be converted into strings and included.

Example)
```
instance_groups:
- azs:
  - z7
  instances: 1
  jobs:
  - name: binary_storage
    release: ap-portal-api-release
  name: binary_storage
  networks:
  - default:
    - dns
    - gateway
    name: service_private
  - name: service_public
  persistent_disk: 102400
  stemcell: default
  vm_type: medium
- azs:
  - z2
  instances: 1
  jobs:
  - name: mariadb
    release: common-infra
  name: mariadb
  networks:
  - name: service_private
  persistent_disk: 4096
  stemcell: default
  vm_type: small
name: common-infra-service
properties:
  binary_storage:
    auth_port: 5000
    binary_desc:
    - marketplace-container
    container:
    - marketplace-container
    email:
    - email@email.com
    - email@email.com
    password:
    - kpaas
    proxy_ip:
    proxy_port: 10008
    tenantname:
    - kpaas-marketplace
    username:
    - kpaas-marketplace
  haproxy:
    http_port: 8080
  mariadb:
    admin_user:
      password: '!paas_ta202'
    port: 3306
  postgres:
    datasource:
      database: sonar
      password: sonar
      username: sonar
    port: 5432
    vcap_password: c1oudc0w
releases:
- name: ap-portal-api-release
  version: latest
- name: common-infra
  version: latest
stemcells:
- alias: default
  os: ubuntu-xenial
  version: 315.36
update:
  canaries: 1
  canary_watch_time: 5000-120000
  max_in_flight: 1
  serial: false
  update_watch_time: 5000-120000
```

##### Response
    null


##### <a name="43"/>4.3. On-Demand Service-Broker Implementation Source Development Guide
The service broker development guide related to on-demand implementation will be processed.
Currently, only the JAVA version is available. The source shown in the example can be found in K-PaaS GitHub's On-Demand-broker.

1. On-Demand Service Instance
<br/>
The information on the assigned VM is stored in the broker DB with the ServiceInstance object.

Example)JpaServiceInstance Class
```
@Entity
@Table(name = "on_demand_info")
public class JpaServiceInstance extends ServiceInstance {

    @JsonSerialize
    @JsonProperty("service_instance_id")
    @Id
    @Column(name = "service_instance_id")
    private String serviceInstanceId;

    @JsonSerialize
    @Column(name = "service_id")
    @JsonProperty("service_id")
    private String serviceDefinitionId;

    @JsonSerialize
    @Column(name = "plan_id")
    @JsonProperty("plan_id")
    private String planId;

    @JsonSerialize
    @Column(name = "organization_guid")
    @JsonProperty("organization_guid")
    private String organizationGuid;

    @JsonSerialize
    @Column(name = "space_guid")
    @JsonProperty("space_guid")
    private String spaceGuid;

    @JsonSerialize
    @Column(name = "dashboard_url")
    @JsonProperty("dashboard_url")
    private String dashboardUrl;

    @Transient
    @JsonIgnore
    private boolean async;

    @JsonSerialize
    @JsonProperty("vm_instance_id")
    @Column(name = "vm_instance_id")
    private String vmInstanceId;

    @JsonSerialize
    @JsonProperty("app_guid")
    @Column(name = "app_guid")
    private String appGuid;

    @JsonSerialize
    @JsonProperty("task_id")
    @Column(name = "task_id")
    private String taskId;

    @JsonSerialize
    @JsonProperty("app_parameter")
    @Column(name = "app_parameter")
    private String app_parameter;


    public JpaServiceInstance() {
        super();
    }

    public JpaServiceInstance(CreateServiceInstanceRequest request) {
        super(request);
        setServiceDefinitionId(request.getServiceDefinitionId());
        setPlanId(request.getPlanId());
        setOrganizationGuid(request.getOrganizationGuid());
        setSpaceGuid(request.getSpaceGuid());
        setServiceInstanceId(request.getServiceInstanceId());
        AtomicReference<String> param = new AtomicReference<>("{");
        AtomicInteger i = new AtomicInteger(1);
        try {
            if (request.getParameters() != null) {
                request.getParameters().forEach((key, value) -> {
                    if (key.equals("app_guid")) {
                        setAppGuid(value.toString());
                    }
                    param.set(param.get() + "\"" + key + "\":\"" + value.toString() + "\"");
                    if (i.get() < request.getParameters().size()) {
                        param.set(param.get() + ",");
                    }
                    i.set(i.get() + 1);
                });
            }
        }catch (Exception e){
        }
        param.set(param.get() + "}");
        setApp_parameter(param.get());
    }

    @Override
    public String getDashboardUrl() {
        return dashboardUrl;
    }

    public void setDashboardUrl(String dashboardUrl) {
        this.dashboardUrl = dashboardUrl;
    }

    public String getAppGuid() {
        return appGuid;
    }

    public void setAppGuid(String appGuid) {
        this.appGuid = appGuid;
    }

    public String getTaskId() {
        return taskId;
    }

    public void setTaskId(String taskId) {
        this.taskId = taskId;
    }

    @Override
    public String getPlanId() {
        return planId;
    }

    public void setPlanId(String planId) {
        this.planId = planId;
    }

    @Override
    public String getServiceDefinitionId() {
        return serviceDefinitionId;
    }

    public void setServiceDefinitionId(String serviceDefinitionId) {
        this.serviceDefinitionId = serviceDefinitionId;
    }

    @Override
    public String getServiceInstanceId() {
        return serviceInstanceId;
    }

    public void setServiceInstanceId(String serviceInstanceId) {
        this.serviceInstanceId = serviceInstanceId;
    }

    @Override
    public String getSpaceGuid() {
        return spaceGuid;
    }

    public void setSpaceGuid(String spaceGuid) {
        this.spaceGuid = spaceGuid;
    }

    @Override
    public String getOrganizationGuid() {
        return organizationGuid;
    }

    public void setOrganizationGuid(String organizationGuid) {
        this.organizationGuid = organizationGuid;
    }

    public String getVmInstanceId() {
        return vmInstanceId;
    }

    public void setVmInstanceId(String vmInstanceId) {
        this.vmInstanceId = vmInstanceId;
    }

    @Override
    public boolean isAsync() {
        return async;
    }

    @Override
    public ServiceInstance and() {
        return this;
    }

    @Override
    public JpaServiceInstance withDashboardUrl(String dashboardUrl) {
        this.dashboardUrl = dashboardUrl;
        return this;
    }

    @Override
    public ServiceInstance withAsync(boolean async) {
        this.async = async;
        return this;
    }


    public String getApp_parameter() {
        return app_parameter;
    }

    public void setApp_parameter(String app_parameter) {
        this.app_parameter = app_parameter;
    }

    public void setAsync(boolean async) {
        this.async = async;
    }

    @Override
    public String toString() {
        return "JpaServiceInstance{" +
                "serviceInstanceId='" + serviceInstanceId + '\'' +
                ", serviceDefinitionId='" + serviceDefinitionId + '\'' +
                ", planId='" + planId + '\'' +
                ", organizationGuid='" + organizationGuid + '\'' +
                ", spaceGuid='" + spaceGuid + '\'' +
                ", dashboardUrl='" + dashboardUrl + '\'' +
                ", async=" + async +
                ", vmInstanceId='" + vmInstanceId + '\'' +
                ", appGuid='" + appGuid + '\'' +
                ", taskId='" + taskId + '\'' +
                '}';
    }
}
```
◎ 1.1. Description of variables customized in On-Demand-broker

##### vm_instance_id : Instance Id of the VM to be assigned to the service instance
##### appGuid : The GUID of the application that will perform service binding.
##### taskId : Task ID of BOSH performing VM task to assign to service

◎ 1.2. JPAInstance(CreateServiceInstanceRequest request) creator
##### To use the app template using the K-PaaS AP Portal, the service parameter value assigned to the arbitrarily designated key is received and assigned to the appGuid.
       ```
       Example)
       public JpaServiceInstance(CreateServiceInstanceRequest request) {
        super(request);
        setServiceDefinitionId(request.getServiceDefinitionId());
        setPlanId(request.getPlanId());
        setOrganizationGuid(request.getOrganizationGuid());
        setSpaceGuid(request.getSpaceGuid());
        setServiceInstanceId(request.getServiceInstanceId());
        AtomicReference<String> param = new AtomicReference<>("{");
        AtomicInteger i = new AtomicInteger(1);
        try {
            if (request.getParameters() != null) {
                request.getParameters().forEach((key, value) -> {
                    if (key.equals("app_guid")) {
                        setAppGuid(value.toString());
                    }
                    param.set(param.get() + "\"" + key + "\":\"" + value.toString() + "\"");
                    if (i.get() < request.getParameters().size()) {
                        param.set(param.get() + ",");
                    }
                    i.set(i.get() + 1);
                });
            }
        }catch (Exception e){
        }
        param.set(param.get() + "}");
        setApp_parameter(param.get());
      }
      ```

2. On-Demand createServiceInstance
When a user requests a service, the VM restarts or is created in the process.

1.1. Check if Deployment is currently configured properly and bring up the Instance List.
     ```

     ```
##### Stop service creation when an error occurs in 1.1.

     ```
     Example)
     List<DeploymentInstance> deploymentInstances = onDemandDeploymentService.getVmInstance(deployment_name, instance_name);
            if (deploymentInstances == null) {
                throw new ServiceBrokerException(deployment_name + " is Working");
            }
     ```

 If there is an idle VM in the 1.2. Instance List, creates and provides a Service Instance where the VM information is stored.

     ```
     Example)
     List<DeploymentInstance> startedDeploymentInstances = deploymentInstances.stream().filter((x) -> x.getState().equals(BoshDirector.INSTANCE_STATE_START) && x.getJobState().equals("running")).collect(Collectors.toList());
            for(DeploymentInstance dep : startedDeploymentInstances){
                if(jpaServiceInstanceRepository.findByVmInstanceId(dep.getId()) == null){
                    jpaServiceInstance.setVmInstanceId(dep.getId());
                    jpaServiceInstance.setDashboardUrl(dep.getIps().substring(1,dep.getIps().length()-1));
                    jpaServiceInstanceRepository.save(jpaServiceInstance);
                    jpaServiceInstance.withAsync(true);
                    SecurityGroups securityGroups = common.cloudFoundryClient().securityGroups();
                    cloudFoundryService.SecurityGurop(request.getSpaceGuid(), jpaServiceInstance.getDashboardUrl(), securityGroups);
                    logger.info("Create service instance");
                    return jpaServiceInstance;
                }
            }
     ```

1.3. If there are no idle VMs, check if the deployment is currently locked before updating the instance. If it is in a locked-in state, it causes a "create not possible" error.

     ```
     Example)
     if (onDemandDeploymentService.getLock(deployment_name)) {
                throw new ServiceBrokerException(deployment_name + " is Working");
     }
     ```

1.4. When it finds a VM in a stopped state, it launches the VM and creates and provides a service instance with the VM information.

     ```
     Example)
     List<DeploymentInstance> detachedDeploymentInstances = deploymentInstances.stream().filter(x -> x.getState().equals(BoshDirector.INSTANCE_STATE_DETACHED)).collect(Collectors.toList());
     String taskID = "";
     for (DeploymentInstance dep : detachedDeploymentInstances) {
         onDemandDeploymentService.updateInstanceState(deployment_name, instance_name, dep.getId(), BoshDirector.INSTANCE_STATE_START);
         while (true) {
             Thread.sleep(1000);
             taskID = onDemandDeploymentService.getTaskID(deployment_name);
             if (taskID != null) {
                 logger.info("taskID : " + taskID);
                 break;
             }
         }
         String ips = "";
         while (true) {
             Thread.sleep(1000);
             ips = onDemandDeploymentService.getStartInstanceIPS(taskID, instance_name, dep.getId());
             if (ips != null) {
                 break;
             }
         }
         jpaServiceInstance.setVmInstanceId(dep.getId());
         jpaServiceInstance.setDashboardUrl(ips);
         jpaServiceInstanceRepository.save(jpaServiceInstance);
         jpaServiceInstance.withAsync(true);
         SecurityGroups securityGroups = common.cloudFoundryClient().securityGroups();
         cloudFoundryService.SecurityGurop(request.getSpaceGuid(), jpaServiceInstance.getDashboardUrl(), securityGroups);
         return jpaServiceInstance;
     }
     ```

1.5. If there is no VM stopped in 1.4., modify the manifest of the deployment to increase the VMs required for the service instance, and then create and provide an instance with the relevant information.

     ```
     Example)
     onDemandDeploymentService.createInstance(deployment_name, instance_name);
     while (true) {
       Thread.sleep(1000);
       taskID = onDemandDeploymentService.getTaskID(deployment_name);
       if (taskID != null) {
         logger.info("Create Instance taskID : " + taskID);
         break;
       }
     }
     String ips = "";
     while (true) {
       Thread.sleep(1000);
       ips = onDemandDeploymentService.getUpdateInstanceIPS(taskID);
       if (ips != null) {
           break;
       }
     }
     String instanceId = "";
     while (true) {
         Thread.sleep(1000);
         instanceId = onDemandDeploymentService.getUpdateVMInstanceID(taskID, instance_name);
         if (instanceId != null) {
             break;
         }
     }
     jpaServiceInstance.setDashboardUrl(ips);
     jpaServiceInstance.setVmInstanceId(instanceId);
     jpaServiceInstanceRepository.save(jpaServiceInstance);
     jpaServiceInstance.withAsync(true);
     SecurityGroups securityGroups = common.cloudFoundryClient().securityGroups();
     cloudFoundryService.SecurityGurop(request.getSpaceGuid(), jpaServiceInstance.getDashboardUrl(), securityGroups);
     return jpaServiceInstance;
     ```

3. On-Demand getOperationServiceInstance

1.1. Inquire the current Bosh task and if Deployment is included in the running task, return the status of progress if there is no progress status.

     ```
     If null is returned from CloudController, inprogress is returned as final.
     Conversely, if an instance value is passed, succeed is returned.
     Example)
     public JpaServiceInstance getOperationServiceInstance(String Instanceid) {
        JpaServiceInstance instance = jpaServiceInstanceRepository.findByServiceInstanceId(Instanceid);
        if (onDemandDeploymentService.runningTask(deployment_name, instance)) {
            logger.info("Instance Creation Succesful");
            ExecutorService executor = Executors.newSingleThreadExecutor();
            CompletableFuture.runAsync(() -> {
                try {
                    if (instance.getAppGuid() != null) {
                        ServiceBindingsV2 serviceBindingsV2 = common.cloudFoundryClient().serviceBindingsV2();
                        ApplicationsV2 applicationsV2 = common.cloudFoundryClient().applicationsV2();
                        cloudFoundryService.ServiceInstanceAppBinding(instance.getAppGuid(), instance.getServiceInstanceId(), (Map) this.objectMapper.readValue(instance.getApp_parameter(), Map.class), serviceBindingsV2, applicationsV2);
                    }
                } catch (Exception e) {
                    logger.error(e.getMessage());
                }
            }, executor);
            return instance;
        }
        logger.info("Creating an instance");
        return null;
     }
    ```

4. On-Demand deleteServiceInstance

1.1. Remove and return the service instance.
1.2. Prepare to stop VMs assigned to instances that have been deleted asynchronously.
##### Check whether the deployment is in a locked state. If it is Lock, check again after 15 seconds, and if it is not Lock, stop the corresponding VM.

      ```
      Example)
      ExecutorService executor = Executors.newSingleThreadExecutor();
            CompletableFuture.runAsync(() -> {
                lock.lock();
                try {
                while (true) {
                    if (onDemandDeploymentService.getLock(deployment_name)) {
                        Thread.sleep(15000);
                        continue;
                    }
                    onDemandDeploymentService.updateInstanceState(deployment_name, instance_name, instance.getVmInstanceId(), BoshDirector.INSTANCE_STATE_DETACHED);
                    cloudFoundryService.DelSecurityGurop(common.cloudFoundryClient().securityGroups(), instance.getSpaceGuid(), instance.getDashboardUrl());
                    logger.info("VM DETACHED SUCCEED : VM_ID : " + instance.getVmInstanceId());
                    break;
                }
                } catch (InterruptedException e) {
                    logger.error(e.getMessage());
                }
                lock.unlock();
            }, executor);
      ```

##### <a name="44"/>4.4. On-Demand Release Development Guide
Since the service must be deployed through the Bosh release, it must be written according to the Bosh release development method. Bosh release consists of scripts related to packages and jobs.

1. On-Demand Release Basic Configuration

    ```
    Example)
    .
    ├── README.md
    ├── blobs
    ├── config
    │   ├── blobs.yml
    │   └── final.yml
    ├── create.sh
    ├── delete.sh
    ├── deployments
    │   ├── deploy-vsphere.sh
    │   ├── necessary_on_demand_vars.yml
    │   ├── ap_on_demand_service_broker.yml
    │   └── unnecessary_on_demand_vars.yml
    ├── jobs
    │   ├── mariadb
    │   │   ├── monit
    │   │   ├── spec
    │   │   └── templates
    │   │       ├── bin
    │   │       │   ├── mariadb_ctl.erb
    │   │       │   ├── post-start
    │   │       │   └── pre-start
    │   │       └── conf
    │   │           ├── init.sql
    │   │           └── mariadb.cnf
    │   ├── ap-on-demand-broker
    │   │   ├── monit
    │   │   ├── spec
    │   │   └── templates
    │   │       ├── bin
    │   │       │   ├── monit_debugger
    │   │       │   └── service_ctl.erb
    │   │       ├── data
    │   │       │   ├── application.yml.erb
    │   │       │   └── properties.sh
    │   │       └── helpers
    │   │           ├── ctl_setup.sh
    │   │           └── ctl_utils.sh
    ├── packages
    │   ├── java
    │   │   ├── packaging
    │   │   └── spec
    │   ├── mariadb
    │   │   ├── packaging
    │   │   └── spec
    │   ├── ap-on-demand-broker
    │   │   ├── packaging
    │   │   └── spec
    └── src
        ├── java
        │   └── server-jre-8u121-linux-x64.tar.gz
        ├── mariadb
        │   └── mariadb-10.1.22-linux-x86_64.tar.gz
        └── ap-on-demand-broker
            └── ap-on-demand-broker.jar

    ```

2. When developing a release by applying the service as On-Demand, you can add it to jobs, packages, and src to create a release file.

    ```
    Example) On-Demand Redis Relases Tree
    .
    ├── README.md
    ├── blobs
    │   ├── aws-cli
    │   ├── ginkgo
    │   ├── go
    │   ├── nginx
    │   └── redis
    │       └── redis-4.0.11.tar.gz
    ├── config
    │   ├── blobs.yml
    │   └── final.yml
    ├── create.sh
    ├── delete.sh
    ├── deployments
    │   ├── deploy-vsphere.sh
    │   ├── necessary_on_demand_vars.yml
    │   ├── ap_on_demand_service_broker.yml
    │   └── unnecessary_on_demand_vars.yml
    ├── jobs
    │   ├── mariadb
    │   │   ├── monit
    │   │   ├── spec
    │   │   └── templates
    │   │       ├── bin
    │   │       │   ├── mariadb_ctl.erb
    │   │       │   ├── post-start
    │   │       │   └── pre-start
    │   │       └── conf
    │   │           ├── init.sql
    │   │           └── mariadb.cnf
    │   ├── ap-on-demand-broker
    │   │   ├── monit
    │   │   ├── spec
    │   │   └── templates
    │   │       ├── bin
    │   │       │   ├── monit_debugger
    │   │       │   └── service_ctl.erb
    │   │       ├── data
    │   │       │   ├── application.yml.erb
    │   │       │   └── properties.sh
    │   │       └── helpers
    │   │           ├── ctl_setup.sh
    │   │           └── ctl_utils.sh
    │   ├── redis
    │   │   ├── monit
    │   │   ├── spec
    │   │   └── templates
    │   │       ├── bin
    │   │       │   ├── health_check
    │   │       │   └── pre_start.sh
    │   │       └── config
    │   │           ├── bpm.yml.erb
    │   │           └── redis.conf.erb
    │   └── sanity-tests
    │       ├── monit
    │       ├── spec
    │       └── templates
    │           └── bin
    │               └── run
    ├── packages
    │   ├── java
    │   │   ├── packaging
    │   │   └── spec
    │   ├── mariadb
    │   │   ├── packaging
    │   │   └── spec
    │   ├── ap-on-demand-broker
    │   │   ├── packaging
    │   │   └── spec
    │   └── redis-4
    │       ├── packaging
    │       └── spec
    └── src
        ├── java
        │   └── server-jre-8u121-linux-x64.tar.gz
        ├── mariadb
        │   └── mariadb-10.1.22-linux-x86_64.tar.gz
        └── ap-on-demand-broker
            └── ap-on-demand-broker.jar
    ```

3. After completing the release configuration, create a tgz file using the bosh cli command and upload it.

    ```
    Example)
    $ bosh create-release --force --tarball on-demand-release.tgz --name on-demand-release --version 1.0
    $ bosh upload-release on-demand-release.tgz(bosh ur on-demand-release.tgz)

	or, you can customize the create.sh file and run it.
    ```

##### <a name="45"/>4.5. On-Demand Deployment Development Guide
The BOSH Deploymentmanifest is a YAML file that defines components and deployment properties.
In the deployment manifest, which Stemcell (OS, BOSH agent) will be used to install the software, and the Release (Software packages, Config templates, Scripts) name and version, VMs capacity, Jobs params, etc. are defined, and the software ( Here, the service pack) is installed.

1. On-Demand Deployment Basic Configuration

    ```
    .
    ├── deploy-vsphere.sh                     bosh deploy execution file
    ├── necessary_on_demand_vars.yml          Required Change property file to be included in manifest.yml
    ├── ap_on_demand_service_broker.yml   deploy manifest.yml file
    └── unnecessary_on_demand_vars.yml        property file to be included in manifest.yml
    ```
    deploy-vsphere.sh

    ```
    bosh -d on-demand-service-broker deploy ap_on_demand_service_broker.yml \
   -l necessary_on_demand_vars.yml\
   -l unnecessary_on_demand_vars.yml
    ```

    necessary_on_demand_vars.yml : Required property files that need to be modified(If not entered correctly, an error occurs during deployment)

    ```
    #!/bin/bash

    ---
    ### On-Demand Bosh Deployment Name Setting ###
    deployment_name: on-demand-service-broker                       #On-Demand Deployment Name

    ### Main Stemcells Setting ###
    stemcell_os: ubuntu-xenial                                      # Deployment Main Stemcell OS
    stemcell_version: latest                                       # Main Stemcell Version
    stemcell_alias: default                                         # Main Stemcell Alias

    ### On-Demand Release Deployment Setting ###
    releases_name : on-demand-redis-release                               # On-Demand Release Name
    internal_networks_name : default                        # Some Network From Your 'bosh cloud-config(cc)'
    mariadb_disk_type : 2GB                                        # MariaDB Disk Type 'bosh cloud-config(cc)'
    broker_port : 8080                                              # On-Demand Broker Server Port
    bosh_client_admin_id: admin                                     # Bosh Client Admin ID
    bosh_client_admin_secret: bosh_clinet_password                  # Bosh Client Admin Secret 'echo ${BOSH_CLIENT_SECRET}'
    bosh_url: https://xx.xx.xx.xxx                                  # Bosh URL 'bosh env'
    bosh_director_port: 25555                                       # Bosh API Port
    bosh_oauth_port: 8443                                           # Bosh Oauth Port

    cloudfoundry_url: xx.xxx.xx.xxx.xip.io                          # CloudFoundry URL
    cloudfoundry_sslSkipValidation: true                            # CloudFoundry Login SSL Validation
    cloudfoundry_admin_id: admin                                    # CloudFoundry Admin ID
    cloudfoundry_admin_password: admin                         # CloudFoundry Admin Password

    ### On-Demand Service Property(Changes are possible) ###
    mariadb_port: 3306                                              # MariaDB Server Port
    mariadb_user_password: DB_password                              # MariaDB Root Password
    ```

    unnecessary_on_demand_vars.yml : property file that does not need to be changed

    ```
    #!/bin/bash

    ---
    service_instance_guid: 54e2de61-de84-4b9c-afc3-88d08aadfcb6
    service_instance_name: redis
    service_instance_bullet_name: Redis Dedicated Server Use
    service_instance_bullet_desc: Redis Service Using a Dedicated Server
    service_instance_plan_guid: 2a26b717-b8b5-489c-8ef1-02bcdc445720
    service_instance_plan_name: dedicated-vm
    service_instance_plan_desc: Redis service to provide a key-value store
    service_instance_org_limitation: -1                                        # Limit the number of services per org, If -1, there is no limit
    service_instance_space_limitation: -1                                      #Limit the number of services per service, If -1, there is no limit

    ```

    ap_on_demand_service_broker.yml : manifest.yml file of On-demand-Service

    ```
    ---
    name: "((deployment_name))"        #Service deployment name (required) that can be checked with bosh deployments
    stemcells:
    - alias: "((stemcell_alias))"
      os: "((stemcell_os))"
      version: "((stemcell_version))"

    variables:
    - name: redis-password
      type: password

    releases:
    - name: "((releases_name))"                  # Service release name (required), can be checked with  bosh releases
      version: "1.0"                                             # Service release version (required): The latest version of the service release uploaded at the latest

    update:
      canaries: 1                                               # canary 인스턴스 수(필수)
      canary_watch_time: 5000-120000                            # canary 인스턴스가 수행하기 위한 대기 시간(필수)
      update_watch_time: 5000-120000                            # non-canary 인스턴스가 수행하기 위한 대기 시간(필수)
      max_in_flight: 1                                          # non-canary 인스턴스가 병렬로 update 하는 최대 개수(필수)
      serial: false

    instance_groups:
    ########## INFRA ##########
    - name: mariadb
      azs:
      - z5
      instances: 1
      vm_type: medium
      stemcell: "((stemcell_alias))"
      persistent_disk_type: "((mariadb_disk_type))"
      networks:
      - name: "((internal_networks_name))"
      jobs:
      - name: mariadb
        release: "((releases_name))"
      syslog_aggregator: null

    ######## BROKER ########

    - name: ap-on-demand-broker
      azs:
      - z5
      instances: 1
      vm_type: service_medium
      stemcell: "((stemcell_alias))"
      networks:
      - name: "((internal_networks_name))"
      jobs:
      - name: ap-on-demand-broker
        release: "((releases_name))"
      syslog_aggregator: null

    ######### COMMON PROPERTIES ##########
    properties:
      broker:
        server:
          port: "((broker_port))"
        datasource:
          password: "((mariadb_user_password))"
        service_instance:
          guid: "((service_instance_guid))"
          name: "((service_instance_name))"
          bullet:
            name: "((service_instance_bullet_name))"
            desc: "((service_instance_bullet_desc))"
          plan:
            id: "((service_instance_plan_guid))"
            name: "((service_instance_plan_name))"
            desc: "((service_instance_plan_desc))"
          org_limitation: "((service_instance_org_limitation))"
          space_limitation: "((service_instance_space_limitation))"
        bosh:
          client_id: "((bosh_client_admin_id))"
          client_secret: "((bosh_client_admin_secret))"
          url: ((bosh_url)):((bosh_director_port))
          oauth_url: ((bosh_url)):((bosh_oauth_port))
          deployment_name: "((deployment_name))"
          instance_name: "((on_demand_service_instance_name))"
        cloudfoundry:
          url: "((cloudfoundry_url))"
          sslSkipValidation: "((cloudfoundry_sslSkipValidation))"
          admin:
            id: "((cloudfoundry_admin_id))"
            password: "((cloudfoundry_admin_password))"
      mariadb:                                                # MARIA DB SERVER setting information
        port: "((mariadb_port))"                                            # MARIA DB PORT Number
        admin_user:
          password: "((mariadb_user_password))"                             # MARIA DB ROOT Account Password
        host_names:
        - mariadb0   

    ```

2. You can deploy the service added On-Demand-Service (On-Demand-Service-Redis).
To deploy the On-Demand-Service that has added the service, you can proceed with deployment by setting the properties required for the service and adding it to the manifest.yml.


    On-Demand-Redis property.yml(Example)
    ```
    #!/bin/bash

    ---
    ### On-Demand Bosh Deployment Name Setting ###
    deployment_name: on-demand-service-broker                       #On-Demand Deployment Name

    ### Main Stemcells Setting ###
    stemcell_os: ubuntu-xenial                                      # Deployment Main Stemcell OS
    stemcell_version: latest                                       # Main Stemcell Version
    stemcell_alias: default                                         # Main Stemcell Alias

    ### On-Demand Release Deployment Setting ###
    releases_name : on-demand-redis-release                               # On-Demand Release Name
    internal_networks_name : default                        # Some Network From Your 'bosh cloud-config(cc)'
    mariadb_disk_type : 2GB                                        # MariaDB Disk Type 'bosh cloud-config(cc)'
    broker_port : 8080                                              # On-Demand Broker Server Port
    bosh_client_admin_id: admin                                     # Bosh Client Admin ID
    bosh_client_admin_secret: bosh_clinet_password                  # Bosh Client Admin Secret 'echo ${BOSH_CLIENT_SECRET}'
    bosh_url: https://xx.xx.xx.xxx                                  # Bosh URL 'bosh env'
    bosh_director_port: 25555                                       # Bosh API Port
    bosh_oauth_port: 8443                                           # Bosh Oauth Port

    cloudfoundry_url: xx.xxx.xx.xxx.xip.io                          # CloudFoundry URL
    cloudfoundry_sslSkipValidation: true                            # CloudFoundry Login SSL Validation
    cloudfoundry_admin_id: admin                                    # CloudFoundry Admin ID
    cloudfoundry_admin_password: admin                         # CloudFoundry Admin Password

    ### On-Demand Service Property(Changes are possible) ###
    mariadb_port: 3306                                              # MariaDB Server Port
    mariadb_user_password: DB_password                              # MariaDB Root Password

    ### On-Demand Dedicated Service Instance Properties ###         #Add additional properties to apply to the service

    on_demand_service_instance_name: redis                          # On-Demand Service Instance Name
    service_password: service_password
    service_port: 6379

    ```

    On-Demand-Redis ap_on_demand_service_broker(Example)
    ```
    ---
    name: "((deployment_name))"        #Service deployment name (required) that can be checked with bosh deployments

    stemcells:
    - alias: "((stemcell_alias))"
      os: "((stemcell_os))"
      version: "((stemcell_version))"

    addons:
    - name: bpm
      jobs:
      - name: bpm
        release: bpm

    variables:
    - name: redis-password
      type: password

    releases:
    - name: bpm
      sha1: f2bd126b17b3591160f501d88d79ccf0aba1ae54
      url: git+https://github.com/cloudfoundry-incubator/bpm-release
      version: 1.0.3
    - name: "((releases_name))"                  # Service release name (required), identifiable as bosh releases
      version: "1.0"                                             # Service release version (required): The latest version of the service release uploaded

    update:
      canaries: 1                                               # Number of canary instances (required)
      canary_watch_time: 5000-120000                            # Waiting time for the canary instance to perform (required)
      update_watch_time: 5000-120000                            # Waiting time for non-canary instances to perform (required)
      max_in_flight: 1                                          # Maximum number of parallel updates by non-canary instances (required)
      serial: false

    instance_groups:
    ########## INFRA ##########
    - name: mariadb
      azs:
      - z5
      instances: 1
      vm_type: medium
      stemcell: "((stemcell_alias))"
      persistent_disk_type: "((mariadb_disk_type))"
      networks:
      - name: "((internal_networks_name))"
      jobs:
      - name: mariadb
        release: "((releases_name))"
      syslog_aggregator: null

    ######## BROKER ########

    - name: ap-on-demand-broker
      azs:
      - z5
      instances: 1
      vm_type: service_medium
      stemcell: "((stemcell_alias))"
      networks:
      - name: "((internal_networks_name))"
      jobs:
      - name: ap-on-demand-broker
        release: "((releases_name))"
      syslog_aggregator: null
    - name: redis
      azs:
      - z5
      instances: 0
      vm_type: medium
      stemcell: "((stemcell_alias))"
      persistent_disk: 1024
      networks:
      - name: "((internal_networks_name))"
      jobs:
      - name: redis
        release: "((releases_name))"
    - name: sanity-tests
      azs:
      - z5
      instances: 1
      lifecycle: errand
      vm_type: medium
      stemcell: "((stemcell_alias))"
      networks:
      - name: "((internal_networks_name))"
      jobs:
      - name: sanity-tests
        release: "((releases_name))"

    ######### COMMON PROPERTIES ##########
    properties:
      broker:
        server:
          port: "((broker_port))"
        datasource:
          password: "((mariadb_user_password))"
        service_instance:
          guid: "((service_instance_guid))"
          name: "((service_instance_name))"
          bullet:
            name: "((service_instance_bullet_name))"
            desc: "((service_instance_bullet_desc))"
          plan:
            id: "((service_instance_plan_guid))"
            name: "((service_instance_plan_name))"
            desc: "((service_instance_plan_desc))"
          org_limitation: "((service_instance_org_limitation))"
          space_limitation: "((service_instance_space_limitation))"
        bosh:
          client_id: "((bosh_client_admin_id))"
          client_secret: "((bosh_client_admin_secret))"
          url: ((bosh_url)):((bosh_director_port))
          oauth_url: ((bosh_url)):((bosh_oauth_port))
          deployment_name: "((deployment_name))"
          instance_name: "((on_demand_service_instance_name))"
        cloudfoundry:
          url: "((cloudfoundry_url))"
          sslSkipValidation: "((cloudfoundry_sslSkipValidation))"
          admin:
            id: "((cloudfoundry_admin_id))"
            password: "((cloudfoundry_admin_password))"
      mariadb:                                                # MARIA DB SERVER Setting Information
        port: "((mariadb_port))"                                            # MARIA DB PORT Number
        admin_user:
          password: "((mariadb_user_password))"                             # MARIA DB ROOT Account Password
        host_names:
        - mariadb0
    ######### SERVICE PROPERTIES #################
      service:
        password: "((service_password))"
        port: "((service_port))"

    ```
3. Check the created service instance when registering as a broker and applying for the service after successful service deployment. On-Demand-Service installation completed upon completion of creation.


[On-Demand_Image_01]:./images/on-demand/1.png
[On-Demand_Image_02]:./images/on-demand/2.png
[On-Demand_Image_03]:./images/on-demand/3.png
[On-Demand_Image_04]:./images/on-demand/4.png
[On-Demand_Image_05]:./images/on-demand/5.png
[On-Demand_Image_06]:./images/on-demand/6.png


### [Index](https://github.com/K-PaaS/Guide-eng/blob/master/README.md) > [AP User Guide](../README.md) > On-Demand Service Development
