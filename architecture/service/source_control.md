### [Index](https://github.com/PaaS-TA/Guide-eng/blob/master/README.md) > [AP Architecture](../README.md) > Source Control Service

## Purpose
This document provides the Architecture of Application Platform (AP) - Source Control Service.
<br><br>

## System Configuration Diagram
![Source Control_architecture_eng](https://user-images.githubusercontent.com/104418463/165657254-70b8412f-ca52-44f1-9ad1-77f3910e8a38.png)

<br>

| Classification | Specification |
|-------|-----|
| scm-server | 1vCPU / 2GB RAM / 30GB Extra Disk |
| mariadb | 1vCPU / 2GB RAM / 2GB Extra Disk |
| haproxy | 1vCPU / 2GB RAM / 2GB Extra Disk |
| sourcecontrol-webui | 1vCPU / 2GB RAM / 2GB Extra Disk |
| sourcecontrol-api | 1vCPU / 2GB RAM / 2GB Extra Disk |
| sourcecontrol-broker | 1vCPU / 2GB RAM / 2GB Extra Disk |


### [Index](https://github.com/PaaS-TA/Guide-eng/blob/master/README.md) > [AP Architecture](../README.md) > Source Control Service
