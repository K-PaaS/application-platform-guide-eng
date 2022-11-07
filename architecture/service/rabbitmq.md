### [Index](https://github.com/PaaS-TA/Guide-eng/blob/master/README.md) > [AP Architecture](../README.md) > RabbitMQ Service

## Purpose
This document provides the Architecture of Application Platform (AP) - RabbitMQ Service.
<br><br>

## System Configuration Diagram
RabbitMQ Service provides opensource message broker servers in a shared form.
Used to deliver messages to many users, delegate requests to other APIs and respond quickly when processing times for requests are long, or to reduce the degree of bonding between applications using MQ.

![rabbitmq_architecture_eng](https://user-images.githubusercontent.com/104418463/165661632-c1f8a920-f685-46f1-a2a7-b2d98b1f3b5f.png)


<br>

| Classification | Specification |
|-------|-----|
| rmq-broker | 1vCPU / 2GB RAM  |
| rmq | 1vCPU / 2GB RAM  |
| haproxy | 1vCPU / 2GB RAM |



### [Index](https://github.com/PaaS-TA/Guide-eng/blob/master/README.md) > [AP Architecture](../README.md) > RabbitMQ Service
