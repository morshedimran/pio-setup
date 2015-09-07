◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆
# personium delpoyable ansible setup instruction
◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆

## Introduction

This it the setup documment to launch Personium by ansible. Part 1( initial configuration) must be completed, where Part 2(Item to be set upon ansible execution) modification is based on the requirement.

Below are the procedure that we followed.

---------------------------------------
:sparkles: GETTING STARTED :sparkles:

### Items to be set initially（Fill destination：/hosts） :white_check_mark:

* all elements inside `/static_inventory/hosts` file enclosed with **【】**, replace with the constructed server configuration.

```
    Ex：ansible_ssh_user=【Ansible_Execution_User】
        ↓
        ansible_ssh_user=root

---------------

----

★Common Server Setting
【Ansible_Execution_User】
　⇒ Specify a user to rexecute ansible

【SSH_PrivateKey】
　⇒ Set the secret key in the absolute path for  ansible user ssh public key authentication.

:collision: Bastion server
【Bastion_Private_IP】
　⇒ Specify the private IP of Bastion server

【Bastion_Tag_Name】
　⇒ Specify the host name to be set  for Bastion server

【Bastion_Network_Separation】
　⇒ Specify the network setup for Bastion server

■Webサーバー
【Web_Private_IP】
　⇒ Specify the private IP of Web server

【Web_Tag_Name】
　⇒ Specify the host name to be set  for Web server

【Web_Global_IP】
　⇒ Specify the global IP for Web server

【Web_FQDN】
　⇒ Specify the FQDN for Web server（same as unit FQDN）

■APサーバー
【AP_Private_IP】
　⇒ Specify the private IP of  AP server

【AP_Network_Separation】
　⇒ Specify the network setup for AP server

【AP_Tag_Name】
　⇒ Specify the host name to be set  for AP server

【Master_Token】
　⇒各種操作を許可するためのマスタートークンを設定する（厳重管理のこと）

■ADS_Masterサーバー
【ADS_Master_Private_IP】
　⇒ADS_MasterサーバーのプライベートIPを指定する

【ADS_Master_Tag_Name】
　⇒ADS_Masterサーバーに設定するホスト名を指定する

■ADS_Slaveサーバー
【ADS_Slave_Private_IP】
　⇒ADS_SlaveサーバーのプライベートIPを指定する

【ADS_Slave_Tag_Name】
　⇒ADS_Slaveサーバーに設定するホスト名を指定する

■ADS共通設定項目
【ADS_User_Name】
　⇒mysql(ads)上の一般ユーザーのアカウント名を指定する
【ADS_User_Password】
　⇒mysql(ads)上の一般ユーザーのパスワードを指定する
【ADS_Root_Name】
　⇒mysql(ads)上のrootユーザーのアカウント名を指定する
【ADS_Root_Password】
　⇒mysql(ads)上のrootユーザーのパスワードを指定する
【ADS_Repl_Name】
　⇒mysql(ads)上のレプリケーション用のアカウント名を指定する
【ADS_Repl_Password】
　⇒mysql(ads)上のレプリケーション用のパスワードを指定する

■Backupサーバー
【Backup_Private_IP】
　⇒BackupサーバーのプライベートIPを指定する

【Buckup_Tag_Name】
　⇒Backupサーバーに設定するホスト名を指定する

【Buckup_Network_Separation】
　⇒Buckupサーバーのネットワーク部を指定する

■ESサーバー
【ES_Private_IP】
　⇒ESサーバーのプライベートIPを指定する

【ES_Tag_Name】
　⇒ESサーバーに設定するホスト名を指定する

■NFSサーバー
【NFS_Private_IP】
　⇒NFSサーバーのプライベートIPを指定する

【nfs_Tag_Name】
　⇒nfsサーバーに設定するホスト名を指定する

---------------

```

### Item to be set upon ansible execution（記入先：/group_vars/[group名].yml） :white_check_mark:

group_vars/フォルダ内にある.ymlファイルには、オプションとして設定可能な値が記述されている。
基本的に変更する必要はないが、サーバーのチューニングなどを行う場合には修正してよい。

```
----

□Webサーバー関連(設定先：/group_vars/web.yml)
  tag_ServerType: web

  nginx_version: 1.7.6
  
  nginx_hm_version: 0.25


□APサーバー関連(設定先：/group_vars/ap.yml)
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


□ADS_Master/ADS_Slaveサーバー関連(設定先：/group_vars/mysql.yml)
  tag_ServerType: mysql

  ads_username: mysql

  ads_groupname: mysql


□ESサーバー関連(設定先：/group_vars/es.yml)
  tag_ServerType: es

  es_heapsize: 3328

  version: 1.3.4


□nfsサーバー関連(設定先：/group_vars/nfs.yml)
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


□bastionサーバー関連(設定先：/group_vars/bastion.yml)
  tag_ServerType: bastion

```
## Summary

