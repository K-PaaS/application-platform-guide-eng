### [Index](https://github.com/PaaS-TA/Guide-eng/blob/master/README.md) > [AP Architecture](../README.md) > Redis Service

## Purpose
This document provides the Architecture of Application Platform (AP) - Redis Service.
<br><br>

## System Configuration Diagram
Redis service provides in-memory cache storage.
It provides a user on-demand Redis server rather than a multi-tenant-based shared service.


![Redis_architecture](https://user-images.githubusercontent.com/104418463/200266609-a406eee2-ed8d-47db-aa19-55eb53336047.png)



<br>

| Classification | Specification |
|-------|-----|
| mariadb | 2vCPU / 4GB RAM / 2GB Extra Disk |
| paas-ta-on-demand-broker | 2vCPU / 4GB RAM |
| redis | 2vCPU / 4GB RAM / 1GB Extra Disk |



### [Index](https://github.com/PaaS-TA/Guide-eng/blob/master/README.md) > [AP Architecture](../README.md) > Redis Service
