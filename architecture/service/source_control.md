### [Index](https://github.com/PaaS-TA/Guide-eng/blob/master/README.md) > [AP Architecture](../README.md) > Source Control Service

## Purpose
This document provides the Architecture of Application Platform (AP) - Source Control Service.
<br><br>

## System Configuration Diagram
The Source Control Service provides a repository for securely storing the user's source code and various files.
It provides a commit history, branch list, etc., and provides a UI that can check various information on the web.


![sourcecontrol_architecture](https://user-images.githubusercontent.com/104418463/200266722-6308e1d9-5a72-475e-a7de-e6b068988c71.png)


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
