### [Index](https://github.com/PaaS-TA/Guide-eng/blob/master/README.md) > [AP Architecture](../README.md) > Lifecycle Service

## Purpose
This document provides the Architecture of Application Platform (AP) - Lifecycle Service.
<br><br>

## System Configuration
Lifecycle Service ins configured based on open source TAIGA.
It provides a UI that can manage people, various documents, and issues necessary to manage the life cycle of an application from the conceptual concept of the application to the end of its need.
It provides an API-life cycle server (taiga) for user organizations, not a multi-tenant-based shared service.

![lifecycle_architecture_eng](https://user-images.githubusercontent.com/104418463/165675774-fed7f22d-ac9d-4707-b92f-2520c3551b47.png)


<br>

| Classification | Specification |
|-------|----|
| mariadb | 2vCPU / 4GB RAM / 10GB Additional Disk |
| service-broker | 2vCPU / 4GB RAM |
| app-lifecycle | 2vCPU / 4GB RAM / 20GB Additional Disk |



### [Index](https://github.com/PaaS-TA/Guide-eng/blob/master/README.md) > [AP Architecture](../README.md) > Lifecycle Service
