# Getting Started with Personium using Ansible
-------------------------------

## Introduction

The purpose of this document is to explain how to construct personium.io unit using Ansible combined with static inventory. Here experimentally we executed Ansible tasks in its host loop against single or multiple remote machines (4 machines in our case), and was able to construct personium.io unit successfully. Some required initial information for constructing the personium.io unit are included in this document.

### About personium.io unit
  Personium.io unit will be constructed by combining the interconnected servers where the following 7 roles will be assigned dynamically.

| **Role**        | **Requirement**  |    **Overview**                                                                        |
|-----------------|:----------------:|----------------------------------------------------------------------------------------|
| `Web`           |  Required        | Reverse proxy server, contains Global IP and also should be accessible to the internet |
| `AP`            |  Required        | Application server, where personium.io will be executed                                |
| `ADS_Master`    |  Optional        | Basically `MySQL` server. Contributes as Master                                        |
| `ADS_Slave`     |  Optional        | Basically `MySQL` server. Contributes as Slave                                         |
| `ES`            |  Required        | server to operate `ElasticSearch`                                                      |
| `NFS`           |  Required        | server to operate `Network File System (NFS)`.                                         |
| `Bastion`       |  Optional        | Bastion server. Used to execute ansible and to connect other servers by ssh.           |

:high_brightness: Also possible to assign multiple roles on a single server.

### About personium.io unit structure

Personium.io unit is configurable based on different purpose of usages like evaluation, development, verification, production etc..
For the easiness of the developer, we also implemented the personium.io unit setup tool to construct personium.io unit automatically based on developer needs.
Of course you can build the personium.io unit without using the tool, but we recommend to use the tool to construct the personium.io unit as it will be easier and fast.


### About personium.io setup tool

This setup tool will install middleware and configure the OS and its network on the machines before installing personium.io module.
There are some patterns of personium.io unit constructed by setup tools, so please select suitable one based on you purpose.

#### Pattern-1 : Evaluation

If interested on personium.io, you may also try to construct personium.io unit on your local machine (virtualbox) as a separate project using the setup tool.

* Please refer to setup-vagrant: https://github.com/personium/setup-vagrant/

#### Pattern-2 : Development, Verification (on process)

* Machine environment : **Linux**
* The number of personium.io unit servers : **1 Server**
  * Server elements : Bastion, Web, AP, NFS, ES
* Setup time : 1 hour
* Setup tool: https://github.com/personium/setup-ansible/1-server_unit/
* Note  
  If you are more interested on personium.io and want to develop some simple applications or to test this system, you can select this pattern. You will get personium.io unit as a single server.

#### Pattern-3 : Production

* Machine environment : **Linux**
* The number of personium.io unit servers : **4 Servers**
  * Server-1 elements : Bastion,Web
  * Server-2 elements : AP,NFS
  * Server-3 elements : ES,ADS_Master
  * Server-4 elements : ADS_Slave
* Setup time :  2 hours
* Setup tool: https://github.com/personium/setup-ansible/4-server_unit/
* Note  
  If you are devoted to personium.io and will release marvelous applications with it, let's try this pattern! You will get personium.io unit with 4 servers, which will meet practical performance.


### Summary

In this document, we tried to share a general understanding about configuring the personium.io unit by using Ansible. Please go thru with our other documents which will help you to construct the personium.io unit on a single or multiple servers based on your purposes.