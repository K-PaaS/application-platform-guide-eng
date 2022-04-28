### [Index](https://github.com/PaaS-TA/Guide-eng/blob/master/README.md) > [AP Architecture](../README.md) > Logging Service

## Purpose
This document provides the Architecture of Application Platform (AP) - Logging Service.
<br><br>

## System Configuration Diagram


![logging_architecture_eng](https://user-images.githubusercontent.com/104418463/165663060-26373737-8e9c-405f-ab25-0b8163d8bef2.png)


<br>

| Classification | Specification |
|-------|----|
| elasticsearch_master | 1vCPU / 2GB RAM / 10GB Extra Disk |
| queue | 1vCPU / 2GB RAM / 10GB Extra Disk |
| maintenance | 1vCPU / 2GB RAM |
| elasticsearch_data | 2vCPU / 4GB RAM / 20GB Extra Disk |
| visualization | 1vCPU / 2GB RAM |
| collector | 1vCPU / 2GB RAM |
| parser | 1vCPU / 2GB RAM |
| router | 1vCPU / 2GB RAM |



### [Index](https://github.com/PaaS-TA/Guide-eng/blob/master/README.md) > [AP Architecture](../README.md) > Logging Service
