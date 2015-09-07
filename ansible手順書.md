◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆
# Provisioning personium unit setup using ansible
◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆

## Introduction

The purpose of this document is to explain how to use Ansible together with static inventory to construct personium unit. Here experimentaly we executed ansible tasks in its host loop against multiple remote machines (4 machines in our case), and was able to construct personium unit successfully.

Below are the procedure that we followed.

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

#### Assign roles on multiple servers
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



#### File structure 

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

## Initial setup for ansible :white_check_mark:


* Prerequisite:
  * User:root
  * ansible execution user: root
  * ansible execution environment：Web/Bastion server
  * DNS registered fixed Global IP.
  * Fixed private IP of all the remote servers.


#### Setup ansible parameters

* Edit the following files of ansible folder
  * Edit /static_inventory/hosts file  ※★ set the value of each parameter.

      ※ Please refer to 「参考_ansible設定.txt」 file, for more details about each parameter.
  * Check /group_vars/[group name].yml file. Re-set the parameter value, if server tuning is necessary.

      ※ Please refer to 「ansible設定.txt」file, for more details about each parameter.
 

#### Deploy ansible （server destination：Bastion server）

* Connect to the Bastion server using WinSCP or other related tools
  
      （WinSCP：http://win-scp.joydownload.jp/?c=5&gclid=CK7L0pvWpcACFVYHvAodOF0AgQ）

* Updoal the `ansible` folder on Bastion server under `/root/` directory.


#### Prepare Self-signed unit certificate and secret key

* Please refer to 「参考_自己署名ユニット証明書作成手順.txt」file, for self-signed unit certificate creation procedure.


#### Configure the self-signed unit certificate and private key

* Arrange  certificate
  * Deploy the `self-signed unit certificate` and `private key` under `ansible/resource/ap/opt/x509/` folder with the following file names.

　　　　   　⇒　unit-self-sign.crt（self-signed unit certificate）
	
　　　　   　⇒　unit.key（private key）

※ You may escape the procedure above, if creating the self-signed unit certificate is based on the preparation of self-signed unit certificate and private key.


#### Prepare SSL certificate / private key

* Prepare the SSL certificate and private key seperately

※If the official SSL certificate could not be arranged, then create and use self-signed ssl certificate. Following is the self-signed ssl certificate creation procedure.：

    # cd /root/ansible/resource/web/opt/nginx/conf
    # openssl genrsa -des3 -out server.key 1024
           Enter pass phrase for server.key:                            ※ Optional (Characters length: is 4 - 8191)
    # openssl req -new -key server.key -out server.csr
           Enter pass phrase for server.key:                            ※ enter the value of `server.key`
           Country Name (2 letter code) [XX]:                           ※ Optional ( entered value will be visible in the certificate)
           State or Province Name (full name) []:                       ※ Optional ( entered value will be visible in the certificate)
           Locality Name (eg, city) [Default City]:                     ※ Optional ( entered value will be visible in the certificate)
           Organization Name (eg, company) [Default Company Ltd]:       ※ Optional ( entered value will be visible in the certificate)
           Organizational Unit Name (eg, section) []:                   ※ Optional ( entered value will be visible in the certificate)
           Common Name (eg, your name or your server's hostname) []:    ※ Optional ( entered value will be visible in the certificate)
           Email Address []:                                            ※ Optional ( entered value will be visible in the certificate)
                   
               Please enter the following 'extra' attributes
               to be sent with your certificate request
               A challenge password []:
               An optional company name []:
               
    # cp server.key server.key.org
    # openssl rsa -in server.key.org -out server.key
           Enter pass phrase for server.key.org:     ※ enter the value of `server.key`
    # openssl x509 -req -days 365 -in server.csr -signkey server.key -out server.crt
    # ls -l server.*
          ⇒ Check if the following files are created
                server.key.org
                server.crt
                server.csr
                server.key
  ※ Refer [Create SSL certificate](http://qiita.com/nenokido2000/items/1d1c79a0a443ed923e92x) for more details.


#### Deploy SSL certificate / private key

* Certificate deployment
   * Deploy the certificate under `/root/ansible/resource/web/opt/nginx/conf/` folder
 
   
            /root/ansible/resource/web/opt/nginx/conf/
   
               ⇒　server.crt（sSSL certificate）
       
               ⇒　server.key（private key）

※ In the case of Self-signed SSL certificate, the above process is not required to follow.

Certificate deployment is done.


#### Add DISK

Add the extentional disk on the server below (Following disk sizes are recomended).
Note: It is required to add the extentional disk on the following path

    ES + ADS_Master server         300GB (path : /dev/xvdb)    Purpose：MySQL data (Master)
                                   100GB (path : /dev/xvdc)    Purpose：ES data
    ADS_Slave + Backup server      200GB (path : /dev/xvdb)    Purpose：MySQL data (Slave)
                                   200GB (path : /dev/xvdc)    Purpose：Data backup
    AP + NFS server                50GB (path  : /dev/xvdb)    Purpose：PIO log
                                   100GB (path : /dev/xvdc)    Purpose：WebDav, event log


#### Generate SSH key （Client machine：Bastion server）

* Setup the ssh keys(RSA key pair) to access other servers from bastion server as “root” user. Follow the steps below:
##### Step One—Create the RSA Key Pair on the client machine

* The entire key generation process looks like this:

```
    $ su root
    Password:
    # ssh-keygen -t rsa
    Generating public/private rsa key pair.
    Enter file in which to save the key (/root/.ssh/id_rsa):
    Enter passphrase (empty for no passphrase):
    Enter same passphrase again:
    Your identification has been saved in /root/.ssh/id_rsa.
    Your public key has been saved in /root/.ssh/id_rsa.pub.
    The key fingerprint is:
    0b:a0:a6:41:8c:6d:3f:46:96:a2:24:dc:7a:87:43:db root@ip-172-31-10-87
    The key's randomart image is:
    +--[ RSA 2048]----+
    |                 |
    |+..  .           |
    |o=+o=            |
    |+oo*+.           |
    |o.o=+E. S        |
    | +..o. . .       |
    |.       .        |
    |                 |
    |                 |
    +-----------------+
```
The public key is now located in **/home/demo/.ssh/id_rsa.pub** 

The private key (identification) is now located in **/home/demo/.ssh/id_rsa**

##### Step Two—Put the public key to other remote servers. Follow the steps below:

* Copy the public key from bastion server
```
   # cat /root/.ssh/id_rsa.pub
   sammple output)
   ------------------
   ssh-rsa AAAAB3NzaC1yc2EAAAABIwAAAQEAxUTAHN8vxgp8w2tBeSYKLDvISg3LF9W/iiIQ5boQNPfHQkpXtbFAVmQ1uDMBf3bUOzQN0Hr+YnAtiV1D7mPjRdBapM7dzI3o4hcuy1Jk9o6J6ZY4SQosH23jOJJZhz0yLn/ACQ+aKeIu3DPj4Pw4C/BUfd+JlFGCRcr/OTjLmqtVerW70LLGSh1CwYr/b7uvKjxdzArxKlzsvCpGBU69Vn0g5+tUzOtvMEYRz1Jttn1gxrRpCqIUbtRbIlYEoNYpzt0hVBfOhNtfbBE8yb8Lw1AenBBP0WcBI7uGJpIdIhlPSIiOqyfG/XnSCVOWZCFGIc13CtOjHq3rabcdefg== root@ip-XX-XX-XX-XX
   ------------------
```

* Add the public key of bastion server to the **/root/.ssh/authorized_keys** of all target remote servers
   * Access to each remote server, and add the public key
   * 
   
```
    $ sudo su –
    # vim /root/.ssh/authorized_keys  (※ Add the key below and save)
    -----------------
    ssh-rsa AAAAB3NzaC1yc2EAAAABIwAAAQEAxUTAHN8vxgp8w2tBeSYKLDvISg3LF9W/iiIQ5boQNPfHQkpXtbFAVmQ1uDMBf3bUOzQN0Hr+YnAtiV1D7mPjRdBapM7dzI3o4hcuy1Jk9o6J6ZY4SQosH23jOJJZhz0yLn/ACQ+aKeIu3DPj4Pw4C/BUfd+JlFGCRcr/OTjLmqtVerW70LLGSh1CwYr/b7uvKjxdzArxKlzsvCpGBU69Vn0g5+tUzOtvMEYRz1Jttn1gxrRpCqIUbtRbIlYEoNYpzt0hVBfOhNtfbBE8yb8Lw1AenBBP0WcBI7uGJpIdIhlPSIiOqyfG/XnSCVOWZCFGIc13CtOjHq3rabcdefg== root@ip-XX-XX-XX-XX
    ------------------
    
⇒　If the directory if not available on the remote server, then create the file first
    　
    　# mkdir .ssh
      # touch authorized_keys
　```


    # ssh-keygen -t rsa
    →~/.ssh/に配備される
  →id_rsa.pub：公開鍵
    id_rsa：秘密鍵

  パスワードは空欄でEnterを押下する
  (自動構築中にパスワード入力を求められてしまうため)
  
・公開鍵の確認(次項「SSHキーの配置」の手順で使用するため、結果をコピーしておくこと)
  # cat /root/.ssh/id_rsa.pub
      出力例)
      ------------------
      ssh-rsa AAAAB3NzaC1yc2EAAAABIwAAAQEAxUTAHN8vxgp8w2tBeSYKLDvISg3LF9W/iiIQ5boQNPfHQkpXtbFAVmQ1uDMBf3bUOzQN0Hr+YnAtiV1D7mPjRdBapM7dzI3o4hcuy1Jk9o6J6ZY4SQosH23jOJJZhz0yLn/ACQ+aKeIu3DPj4Pw4C/BUfd+JlFGCRcr/OTjLmqtVerW70LLGSh1CwYr/b7uvKjxdzArxKlzsvCpGBU69Vn0g5+tUzOtvMEYRz1Jttn1gxrRpCqIUbtRbIlYEoNYpzt0hVBfOhNtfbBE8yb8Lw1AenBBP0WcBI7uGJpIdIhlPSIiOqyfG/XnSCVOWZCFGIc13CtOjHq3rabcdefg== root@ip-XX-XX-XX-XX
      ------------------

------------------------------------------------------------------------------------------------
#### SSHキーの配置（対象サーバー：Bastion以外の全サーバー）
------------------------------------------------------------------------------------------------
※以下の作業はBastionサーバー以外の全サーバーで実施する。
↓↓ここから------------

・各サーバーへ接続し、キーを配置する
  /root/.ssh/authorized_keysに、生成した公開鍵を追加する

    ※/root/.sshフォルダがない場合はフォルダを作成し、authorized_keysも作成する
      # mkdir .ssh
      # touch authorized_keys

  以下のコマンドを使い、ファイルの末尾に公開鍵を追加する
  # vi /root/.ssh/authorized_keys
      1. viコマンドを実行すると、ファイルの中身が表示されます。
      2. 「i」を押すと編集モードになるので、その状態でファイル末尾までいきます。
      3. コピーした公開鍵を右クリックで「貼り付け」を選択します。
      4. 「Esc」を押し「:」、「w」、「q」の順に押すとファイルが保存されて、viコマンドが終了されます。

・パーミッションの確認
  # ls -la /root/.ssh/authorized_keys
  出力例)
    ---------------
      -rw-------. 1 root root 952 Aug 27 02:41 /root/.ssh/authorized_keys
    ---------------

  「-rw-------.」と表示されていること。
  異なる場合はパーミッションの修正を行う。
    下記コマンドを実行する。
       ---------ここから
       # chmod 600 /root/.ssh/authorized_keys
       # ls -la /root/.ssh/authorized_keys
         出力例)
         ---------------
           -rw-------. 1 root root 952 Aug 27 02:41 /root/.ssh/authorized_keys
         ---------------
        「-rw-------.」と表示されていること。
       ---------ここまで

  # ls -la /root/
  出力例)
  ---------------
    drwx------.  2 root root 4096 Aug 27 02:41 .ssh
  ---------------
  .sshディレクトリのぱーにしょんが「drwx------.」と表示されていること。
  異なる場合はパーミッションの修正を行う。
    下記コマンドを実行する。
       ↓↓ここから---------
       # chmod 700 /root/.ssh
       # ls -la /root/.ssh
         出力例)
         ---------------
           drwx------.  2 root root 4096 Aug 27 02:41 .ssh
         ---------------
        「drwx------.」と表示されていること。
       ↑↑ここまで---------

  # exit

↑↑ここまで------------

------------------------------------------------------------------------------------------------
□SSH接続の確認（対象サーバー：Bastionサーバー）
------------------------------------------------------------------------------------------------
・Bastionサーバーから各サーバへssh接続を行う
  # ssh -i ~/.ssh/id_rsa root@[接続先サーバーのPrivate IP]
  
  ユーザー名rootでログインできること

  # exit

◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇

Part 2. ansible設定

◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇

------------------------------------------------------------------------------------------------
□Ansibleのインストール（対象サーバー：Bastionサーバー）
------------------------------------------------------------------------------------------------
・Bastionサーバーへ接続し、epelリポジトリを追加する
  # yum localinstall http://dl.fedoraproject.org/pub/epel/6/x86_64/epel-release-6-8.noarch.rpm
     ⇒「Is this ok [y/N]: 」 が表示されて入力が要求される。「y」を入力して「Enter」を押す。
・ansible本体をインストールする
  # yum install ansible
     ⇒「Is this ok [y/N]: 」 が表示されて入力が要求される。「y」を入力して「Enter」を押す。

------------------------------------------------------------------------------------------------
□ansible設定項目の確認（対象サーバー：Bastionサーバー）
------------------------------------------------------------------------------------------------
・Bastionサーバーに接続し、ansible設定項目が修正されていることを確認
  1． /hostsの確認
    hostsの設定内容に漏れがないかを確認する
    # cat /root/ansible/static_inventory/hosts | grep "【"
        ⇒何も表示されなければすべての項目が設定されている。

  2． /group_vars/[group名].ymlの確認
      ※group_vars内を変更した場合のみ実施。修正が反映されていることを確認すること。
    # cat /root/ansible/group_vars/ap.yml
    # cat /root/ansible/group_vars/backup.yml
    # cat /root/ansible/group_vars/bastion.yml
    # cat /root/ansible/group_vars/common.yml
    # cat /root/ansible/group_vars/es.yml
    # cat /root/ansible/group_vars/mysql.yml
    # cat /root/ansible/group_vars/nfs.yml
    # cat /root/ansible/group_vars/web.yml

------------------------------------------------------------------------------------------------
□ansibleの実行
------------------------------------------------------------------------------------------------
・Bastionサーバへ接続し、カレントディレクトリをansibleフォルダへ変更する
  # cd /root/ansible/

・ansibleを実行する
  # date; ansible-playbook init_personium.yml ; date
※  数分～数時間ほどでansibleの処理が完了し、設定したFQDNのPIOユニットが完成します。Web（https）でのアクセスが可能となります。

・ansibleの実行確認
  # egrep -B 3 -A 3 'failed:|error' /root/ansible.log
      ⇒何も表示されないことを確認

◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇

Part 3. 疎通確認

◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇

------------------------------------------------------------------------------------------------
□踏み台サーバにて疎通確認を実施する
------------------------------------------------------------------------------------------------
・疎通確認ツールを実行する
  「PCS Version(default) RT OK」が表示されることを確認する
  # /bin/sh pcs_regression.sh https://{{ WebサーバーのFQDN }}
        ⇒「PCS Version(default) RT OK」が出力されれば疎通確認完了。

