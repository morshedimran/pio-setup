# Provisioning personium.io unit setup using ansible
-------------------------------

## Introduction

The purpose of this document is to explain how we were able to construct personium.io unit using ansible combined with static inventory. Here experimentally we executed ansible tasks in its host loop against single or multiple remote machines (4 machines in our case), and was able to construct personium.io unit successfully. Some required initial information for constructing the personium.io unit are included in this document.

---------------------------------------
:sparkles: GETTING STARTED :sparkles:

### About personium.io unit
  Personium unit will be constructed by combining the interrelated servers where the following 7 roles will be assigned accordingly.

| **Server**      | **Requirement**  |    **Overview**                                                                    |
|-----------------|:----------------:|------------------------------------------------------------------------------------|
| `Web`           |  Required        | Reverse proxy server, contain Global IP, and should be accessible to the internet. |
| `AP`            |  Required        | Application server, where personium will be executed.                              |
| `ADS_Master`    |  Optional        | Basically `MySQL` server. Contributes as Master.                                   |
| `ADS_Slave`     |  Optional        | Basically `MySQL` server. Contributes as Slave.                                    |
| `ES`            |  Required        | server to operate `ElasticSearch`.                                                |
| `NFS`           |  Required        | server to operate `Network File System (NFS)`.                                    |
| `Bastion`       |  Optional        | Bastion server. Will be used to execute ansible and to connect other servers thru ssh.|


:high_brightness: Also possible to assign multiple roles on a single server.

### About personium.io unit structure

personium.io unit is configurable based on different types of usages like evaluation、development、verification、production etc..

For the easiness of the developer, we also implemented the personium.io unit configuration setup tool to construct personium.io unit automatically based on developer needs.
Of course you can may build the personium.io unit without using the tool, but we recommend to use the tool to construct the personium.io unit.


### About personium.io setup tool


  セットアップツールはあなたの用意したマシンのOS、ネットワーク、ミドルのインストールや設定を行い、personium.ioのモジュールのインストールを行います。
  あなたの用途に合わせたセットアップツールを選択してください。
  

* Pattern 1  usage purpose： evaluation
```
    personium.ioを試してみたい場合は、あなたのローカルマシン（virtualbox）にpersonium.ioをセットアップするツールが別プロジェクトで用意されています。
    以下のプロジェクトを参照してください。

    setup-vagrant
      https://github.com/personium/setup-vagrant/

```


* Pattern 2  usage purpose：開発,検証  (準備中)


```

    ・Machine environment：Linux server

    ・personium.io unit configuration server：single server
        Server elements：Bastion,Web,AP,NFS,ES

    ・Set-up time：

    ・Setup Tool
        https://github.com/personium/setup-ansible/1-server_unit/

```

* Pattern 3  purpose：production environment

```
    ・Machine environment：Linux server

    ・personium.io unit configuration server：4 servers
        Server-1 elements：Bastion,Web,
        Server-2 elements：AP,NFS
        Server-3 elements：ES,ADS_Master
        Server-4 elements：ADS_Slave

    ・Set-up time： 2 hours approximately

    ・Setup Tool
        https://github.com/personium/setup-ansible/4-server_unit/

```

### Summary

In this document, we tried to share a general understanding about configuring the personium.io unit by using ansible. Please go thru with our other documents which will help you to construct the personium.io unit on a single or multiple servers based on your needs.
