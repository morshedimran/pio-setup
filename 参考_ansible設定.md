◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆
# personium delpoyable ansible setup instruction
◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆

## Introduction

This it the setup documment to launch Personium by ansible. Part 1( initial configuration) must be completed, where Part 2(Item to be set upon ansible execution) modification is based on the requirement.

Below are the procedure that we followed.

---------------------------------------
:sparkles: GETTING STARTED :sparkles:

### Items to be set initially（Fill destination：/hosts） :white_check_mark:

* all elements inside `/static_inventory/hosts` file enclosed with `【】`, replace with the constructed server configuration.

```
    Ex：ansible_ssh_user=【Ansible_Execution_User】
    ↓
    ansible_ssh_user=root
```
---------------
* Modify the hosts file as per instruction below

```
★ Common Server Setting
【Ansible_Execution_User】
　⇒ Specify a user to rexecute ansible

【SSH_PrivateKey】
　⇒ Set the secret key in the absolute path for  ansible user ssh public key authentication.

★ Bastion server
【Bastion_Private_IP】
　⇒ Specify the private IP of Bastion server

【Bastion_Tag_Name】
　⇒ Specify the host name for Bastion server

【Bastion_Network_Separation】
　⇒ Specify the network catagory for Bastion server

★ Web server
【Web_Private_IP】
　⇒ Specify the private IP of Web server

【Web_Tag_Name】
　⇒ Specify the host name for Web server

【Web_Global_IP】
　⇒ Specify the global IP for Web server

【Web_FQDN】
　⇒ Specify the FQDN for Web server（same as unit FQDN）

★ AP server
【AP_Private_IP】
　⇒ Specify the private IP of  AP server

【AP_Network_Separation】
　⇒ Specify the network catagory for AP server

【AP_Tag_Name】
　⇒ Specify the host name for AP server

【Master_Token】
　⇒ To authorize all kind of operation, set the master token. (strictly managed)

★ ADS_Master server
【ADS_Master_Private_IP】
　⇒ Specify the private IP of ADS_Master server 
　
【ADS_Master_Tag_Name】
　⇒ Specify the host name for ADS_Master server

★ ADS_Slave server
【ADS_Slave_Private_IP】
　⇒ Specify the private IP of ADS_Slave server

【ADS_Slave_Tag_Name】
　⇒ Specify the host name for ADS_Slave server

★ ADS common setup
【ADS_User_Name】
　⇒ Set the general user account name for mysql(ads)
【ADS_User_Password】
　⇒ Assign the password for the general user accounts of mysql(ads)
【ADS_Root_Name】
　⇒ Set the root user account name for mysql(ads)
【ADS_Root_Password】
　⇒ Assign the password for the root user accounts of mysql(ads)
【ADS_Repl_Name】
　⇒ Set the account name for replication on mysql (ads)
【ADS_Repl_Password】
　⇒ Assign the password for the replication account of mysql(ads)

★ Backup server
【Backup_Private_IP】
　⇒ Set the private IP for Backup server 

【Buckup_Tag_Name】
　⇒ Specify the host name for Backup server

【Buckup_Network_Separation】
　⇒ Specify the network catagory for Backup server

★ ES server
【ES_Private_IP】
　⇒ Set the private IP for ES server

【ES_Tag_Name】
　⇒ Specify the host name for ES server

★ NFS server
【NFS_Private_IP】
　⇒ Set the private IP for NFS server

【nfs_Tag_Name】
　⇒ Specify the host name for nfs server

---------------

```

### Item to be set upon ansible execution（File destination：/group_vars/[group name].yml） :white_check_mark:

As an option, changing the recorded values of all .yml files under group_vars directory is possible. But basically, no modification is required unless server tuning is nacessary.

```
----

□Web server seraled (file destination：/group_vars/web.yml)
  tag_ServerType: web

  nginx_version: 1.7.6
  
  nginx_hm_version: 0.25


□AP server seraled(file destination：/group_vars/ap.yml)
  tag_ServerType: ap

  tomcat_version: 8.0.14
  
  tomcat_xms: 1024m
  
  tomcat_xmx: 1024m
  
  tomcat_metaspace_size: 256m
  
  tomcat_max_metaspace_size: 256m
  
  commons_daemon_version : 1.0.15

  lock_host: pcs-nfs
  
  lock_port: 11211
  
  cache_host: pcs-nfs
  
  cache_port: 11212
  
  cache_manager: memcached


□ADS_Master/ADS_Slave server seraled(file destination：/group_vars/mysql.yml)
  tag_ServerType: mysql

  ads_username: mysql

  ads_groupname: mysql


□ES server seraled(file destination：/group_vars/es.yml)
  tag_ServerType: es

  es_heapsize: 3328

  version: 1.3.4


□nfs server seraled(file destination：/group_vars/nfs.yml)
  tag_ServerType: nfs

  # lock / cache instance
  cache_in_nfs: true

  lock_port: 11211

  cache_port: 11212

  # ec2 memcached cachesize
  memcached_lock_cachesize: 512

  memcached_cache_cachesize: 512

  memcached_version: 1.4.21

  memcached_lock_maxconn: 1024

  memcached_cache_maxconn: 1024


□bastion server seraled(file destination：/group_vars/bastion.yml)
  tag_ServerType: bastion

```
## Summary

