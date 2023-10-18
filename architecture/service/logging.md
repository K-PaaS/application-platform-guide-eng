### [Index](https://github.com/PaaS-TA/Guide-eng/blob/master/README.md) > [AP Architecture](../README.md) > Logging Service

## Purpose
This document provides the Architecture of Application Platform (AP) - Logging Service.
<br><br>

## System Configuration Diagram


![logging_architecture_eng](https://user-images.githubusercontent.com/104418463/165663396-cc2ffddf-60a1-45b0-874a-d6ba34888c90.png)



<br>

| Classification  | Specification |
|-------|----|
| fluentd | 1vCPU / 4GB RAM |
| influxdb | 4vCPU / 8GB RAM / 10GB Extra Disk |
| collector | 1vCPU / 4GB RAM |



### [Index](https://github.com/PaaS-TA/Guide-eng/blob/master/README.md) > [AP Architecture](../README.md) > Logging Service
