### [Index](https://github.com/PaaS-TA/Guide-eng/blob/master/README.md) > [AP Architecture](../README.md) > Logging Service

## Purpose
This document provides the Architecture of Application Platform (AP) - Logging Service.
<br><br>

## System Configuration Diagram


![logging_architecture_eng](https://user-images.githubusercontent.com/104418463/165663396-cc2ffddf-60a1-45b0-874a-d6ba34888c90.png)



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
