◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆
# Provisioning personium unit setup using ansible
◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆

## Introduction

The purpose of this document is to explain how we were able to construct personium unit using ansible together with static inventory. Here experimentaly we executed ansible tasks in its host loop against single or multiple remote machines (4 machines in our case), and was able to construct personium unit successfully.

Below are the general notifications, which is required to have the better understanding for this configuration.

---------------------------------------
:sparkles: GETTING STARTED :sparkles:

### Assign roles on multiple servers
  personium unit construction requires to assign the following 8 rules on different servers :one:.

1.`Web` Reverse proxy server, contain Global IP, and should be accessible to the internet.

2.`AP` Application server, where personium will be executed.

3.`ADS_Master` Basically `MySQL` server. Contributes as Master.

4.`ADS_Slave` Basically `MySQL` server. Contributes as Slave.

5.`ES` server for running `ElasticSearch`.

6.`NFS` server for running `Network File System (NFS)`.

7.`Bastion` Bastion server. Will be used to execute ansible and to connect other servers thru ssh.

8.`Backup` pio tool server :two:. It performs the backup from ADS_Slave and NFS server if necessary.

  
:one: ：Possible to assign multiple rules on a single server.

:two: ：As a tool it works as `pio data backup` tool, `consistency check` tool, `Cell recursive Deletion` Tool and the `Elasticsearch index restoring` tool.



### File structure 

  * `/init_personium.yml`  :		yml file that should be executed by ansible-playbook
  * `/[group name].yml`	   :		Retrieve the variable of each group, which will be executed by task yml
  * `/ansible.cfg`         :		Described required Settings for execution. Modification is not required
  * `/static_inventory/`   :		This folder contains all the essential information of different environments
  * `※★/hosts`	          :     	Setup for each host (IP address, FQDN, group, User name, Private Key, etc.)
  * `/group_vars/`	   :		Folder to organize files in order to perform various customization or tuning
  * `※★[group name].yml`  :		Collections of value for each group, which requires to customize/tuning the settings
  * `/resource/`			Folder to organize files (Modification is not required) those are necessary in task
  *  `/[groue name]/`			Store the resources of each group
  *  `/tasks/`				Folder to organize task
  *  `[groue name]/`			Store specific task for each group
  *  `/handlers/`			Folder to organize handler
  *  `/[group name]/`			Store handler for each group
  
  
  ※★:Files required to modify according to the environment.

  ※[group name] : web, ap, nfs, es, ads_master, ads_slave, bastion、backup and common. All in all 9 group names.
  （Here `common` is not the server role. Common group is used to set some general functionalities on all the servers.）

File (key) handling Caution: :zap:

The following key file will be generated automatically during the ansible execution, we advice to handle the keys carefully.

    /fj/dc-core/conf/salt.key
    /fj/dc-core/conf/token.key

---------------------------------------

### Summary

In this document, we tried to share a general understanding about configuraing the personium by using ansible. Please go thru with our other documents which will help you to construct the personium on a single or multiple servers based on your needs.
