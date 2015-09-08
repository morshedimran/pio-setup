◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆
# Provisioning personium unit setup using ansible
◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆

## Introduction

The purpose of this document is to explain explecitely how to construct personium on the top of 4 servers using Ansible together with static inventory. Here experimentaly we executed ansible tasks in its host loop against multiple remote machines (4 machines in our case), and was able to construct personium unit successfully.


---------------------------------------
:sparkles: GETTING STARTED :sparkles:

## Server setup :white_check_mark:

#### server configuration sample
  Below are the server structure that we configured.

* server configuration
  * Server 1：Web, Bastion
  * Server 2：AP, NFS
  * Server 3：ES, ADS_Master
  * Server 4：ADS_Slave, backup

---------------------------------------

Please go thru to the following file for the construction procedure:
