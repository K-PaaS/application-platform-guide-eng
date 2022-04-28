### [Index](https://github.com/PaaS-TA/Guide-eng/blob/master/README.md) > [AP Architecture](../README.md) > Pipeline Service

## Purpose
This document provides the Architecture of Application Platform (AP) - Pipeline Service.
<br><br>

## System Configuration Diagram

![pipeline_architecture_eng](https://user-images.githubusercontent.com/104418463/165662258-65112aba-dfcc-4963-8361-5360b7a59fbb.png)



<br>

| Classification | Specification |
|-------|------|
| mariadb | 1vCPU / 2GB RAM / 2GB Extra Disk |
| postgres | 1vCPU / 2GB RAM / 2GB Extra Disk |
| inspection | 1vCPU / 2GB RAM |
| haproxy | 1vCPU / 2GB RAM |
| ci_server | 1vCPU / 2GB RAM / 5GB Extra Disk |
| binary_storage | 1vCPU / 2GB RAM / 5GB Extra Disk |
| delivery-pipeline-common-api | 1vCPU / 2GB RAM |
| delivery-pipeline-inspection-api | 1vCPU / 2GB RAM |
| delivery-pipeline-binary-storage-api | 1vCPU / 2GB RAM |
| delivery-pipeline-api | 1vCPU / 2GB RAM / 2GB Extra Disk |
| delivery-pipeline-service-broker | 1vCPU / 2GB RAM / 2GB Extra Disk |
| delivery-pipeline-ui | 1vCPU / 2GB RAM |
| delivery-pipeline-scheduler | 1vCPU / 2GB RAM |



### [Index](https://github.com/PaaS-TA/Guide-eng/blob/master/README.md) > [AP Architecture](../README.md) > Pipeline Service
