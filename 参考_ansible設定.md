◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆
# Personium自動構築ansible  設定項目説明書
◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆

## Introduction

Personiumを立ち上げるために必要な設定項目についての説明書
Part 1は必ず設定するベき項目、Part 2は必要に応じて設定するべき項目である。

Below are the procedure that we followed.

---------------------------------------
:sparkles: GETTING STARTED :sparkles:

### 初期設定にて設定する項目（記入先：/hosts） :white_check_mark:

* /static_inventory/hosts内にある【】で囲まれた部分を、
構築したいシステムの構成に合わせて変更すること。

```
    Ex：ansible_ssh_user=【Ansible_Execution_User】
        ↓
        ansible_ssh_user=root

---------------

----

■サーバー共通項目
【Ansible_Execution_User】
　⇒ansibleを実行するユーザーを指定する

【SSH_PrivateKey】
　⇒ansible実行ユーザーがssh公開鍵認証に用いる秘密鍵を絶対パスで指定する

■Bastionサーバー
【Bastion_Private_IP】
　⇒BastionサーバーのプライベートIPを指定する

【Bastion_Tag_Name】
　⇒Bastionサーバーに設定するホスト名を指定する

【Bastion_Network_Separation】
　⇒Bastionサーバーのネットワーク部を指定する

■Webサーバー
【Web_Private_IP】
　⇒WebサーバーのプライベートIPを指定する

【Web_Tag_Name】
　⇒Webサーバーに設定するホスト名を指定する

【Web_Global_IP】
　⇒WebサーバーのグローバルIPを指定する

【Web_FQDN】
　⇒WebサーバーのFQDN（ユニットのFQDN）

■APサーバー
【AP_Private_IP】
　⇒APサーバーのプライベートIPを指定する

【AP_Network_Separation】
　⇒APサーバーのネットワーク部を指定する

【AP_Tag_Name】
　⇒APサーバーに設定するホスト名を指定する

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

### ansibleの実行に際して設定する項目（記入先：/group_vars/[group名].yml） :white_check_mark:

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

