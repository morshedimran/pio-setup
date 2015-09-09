◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆
# Provisioning personium unit setup using ansible
◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆

## Introduction

The purpose of this document is to explain explecitely how to construct personium on the top of a single server using Ansible together with static inventory. Here experimentaly we executed ansible tasks in its host loop against a single machine, and was able to construct personium unit successfully.


---------------------------------------

## Server setup :white_check_mark:

#### server configuration sample
  Below are the server structure that we configured.

* server configuration
  * Server 1：Web, Bastion, AP, NFS, ES

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

※ Create and use self-signed SSL certificate unless the official SSL certificate is not available. 

Following is the self-signed ssl certificate creation procedure.：

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


#### Generate SSH key 

* Setup the ssh keys(RSA key pair) to access other servers from bastion server as “root” user. Follow the steps below:
##### Step One—Create the RSA Key Pair on the client machine (Bastion server)

* The entire key generation process looks like as follows:

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

The `public key` is now located in `/home/demo/.ssh/id_rsa.pub` 

The `private key` (identification) is now located in `/home/demo/.ssh/id_rsa`




##### Step Two—Put the public key to other remote servers. Follow the steps below:

* Copy the public key from bastion server
```
    # cat /root/.ssh/id_rsa.pub
    sammple output)
    ------------------
    ssh-rsa AAAAB3NzaC1yc2EAAAABIwAAAQEAxUTAHN8vxgp8w2tBeSYKLDvISg3LF9W/iiIQ5boQNPfHQkpXtbFAVmQ1uDMBf3bUOzQN0
    Hr+YnAtiV1D7mPjRdBapM7dzI3o4hcuy1Jk9o6J6ZY4SQosH23jOJJZhz0yLn/ACQ+aKeIu3DPj4Pw4C/BUfd+JlFGCRcr/OTjLmqtVer
    W70LLGSh1CwYr/b7uvKjxdzArxKlzsvCpGBU69Vn0g5+tUzOtvMEYRz1Jttn1gxrRpCqIUbtRbIlYEoNYpzt0hVBfOhNtfbBE8yb8Lw1A
    enBBP0WcBI7uGJpIdIhlPSIiOqyfG/XnSCVOWZCFGIc13CtOjHq3rabcdefg== root@ip-XX-XX-XX-XX
   ------------------
```

* Add the public key of bastion server to the **/root/.ssh/authorized_keys** of **all target remote servers**
   * Access to each remote server, and add the public key
   
```
    $ sudo su –  (※　switch to the root user)
    # vim /root/.ssh/authorized_keys  (※ Add the key below and save)
    -----------------
    ssh-rsa AAAAB3NzaC1yc2EAAAABIwAAAQEAxUTAHN8vxgp8w2tBeSYKLDvISg3LF9W/iiIQ5boQNPfHQkpXtbFAVmQ1uDMBf3bUOzQN0
    Hr+YnAtiV1D7mPjRdBapM7dzI3o4hcuy1Jk9o6J6ZY4SQosH23jOJJZhz0yLn/ACQ+aKeIu3DPj4Pw4C/BUfd+JlFGCRcr/OTjLmqtVer
    W70LLGSh1CwYr/b7uvKjxdzArxKlzsvCpGBU69Vn0g5+tUzOtvMEYRz1Jttn1gxrRpCqIUbtRbIlYEoNYpzt0hVBfOhNtfbBE8yb8Lw1A
    enBBP0WcBI7uGJpIdIhlPSIiOqyfG/XnSCVOWZCFGIc13CtOjHq3rabcdefg== root@ip-XX-XX-XX-XX
    ------------------
⇒　Notes:
      1. vi command displays the contents of the file.
      2. By Pressing[i]you will be in edit mode, and be able to copy the public key.
      3. Paste the public key, copied from bastion server
      4. Press[Esc], then type [:wq!] to save the file
    
⇒　If the directory if not available on the remote server, then create the file first
    　
    　# mkdir .ssh
      # touch authorized_keys
```

* Check the permission. Change if required

```
    # ls -la /root/.ssh/authorized_keys
    output ex:)
    ---------------
      -rw-------. 1 root root 952 Aug 27 02:41 /root/.ssh/authorized_keys
    ---------------
    [-rw-------.]should be shown.

⇒　Change the permission if required
       # chmod 600 /root/.ssh/authorized_keys
       # ls -la /root/.ssh/authorized_keys
    output ex:)
    ---------------
       -rw-------. 1 root root 952 Aug 27 02:41 /root/.ssh/authorized_keys
     ---------------

    # ls -la /root/
    output ex:)
    ---------------
      drwx------.  2 root root 4096 Aug 27 02:41 .ssh
    ---------------
  .ssh directory permission should be shown as [drwx------.]

⇒　Change the permission if required

    # chmod 700 /root/.ssh
    # ls -la /root/.ssh
    Output ex:)
    ---------------
      drwx------.  2 root root 4096 Aug 27 02:41 .ssh
    ---------------
    [drwx------.]should be shown.
    # exit

```

##### Step Three— SSH connection test (※　Access to remote servers form Bastion server using private key)

```
⇒　Access to Bastion server
    $ su root  (※　switch to the root user)
    # ssh -i ~/.ssh/id_rsa root@[Private IP of remote server]  (※　ssh to remote servers as root user)
    
    # exit (※　Exit from remote server, after accessing it successfully from bastion server)
```
--------------------------------------------------------------------

## Ansible configuration :white_check_mark:


#### Install Ansible（Client server：Bastion server）

* Add the epel repository to the Bastion server

```
    # yum localinstall http://dl.fedoraproject.org/pub/epel/6/x86_64/epel-release-6-8.noarch.rpm
    this ok [y/N]: 　　(※　type [y] and press enter)
```
* Install ansible

```
    # yum install ansible
    this ok [y/N]: 　　(※　type [y] and press enter)
```    
#### Check the ansible configuration file on client server (Bastion server)

* Access to Bastion server and check the following ansible setup files, if configures as required

```
1．/hosts file   (※　Static inventory file)
※　Check the hosts file if anything is missing   

    　　# cat /root/ansible/static_inventory/hosts | grep "【"
⇒　If nothing shows, meaning all are configured

2．/group_vars/[group name].yml file    (※　Group Variable files) 
※　Check if all the yml files under group_vars are modified as required

    　　# cat /root/ansible/group_vars/ap.yml
    　　# cat /root/ansible/group_vars/backup.yml
    　　# cat /root/ansible/group_vars/bastion.yml
    　　# cat /root/ansible/group_vars/common.yml
    　　# cat /root/ansible/group_vars/es.yml
    　　# cat /root/ansible/group_vars/mysql.yml
    　　# cat /root/ansible/group_vars/nfs.yml
    　　# cat /root/ansible/group_vars/web.yml
```

#### Execute Ansible

* Access to the Bastion server and change the ansible as current directory 

```
    # cd /root/ansible/

⇒　Exucute ansible
    # date; ansible-playbook init_personium.yml ; date
※  After few minutes-hours (case by case) ansible process will be done. (※　Don't kill the process in between) personium Unit will be created with the configured FQDN. Also will be accessable from web (ex: https://FQDN) 

・Confirm if ansible executed properly
  # egrep -B 3 -A 3 'failed:|error' /root/ansible.log
      ⇒ Check the fansible log file, if it has any error
```

#### Deployment testing at Bastion server

* Execute the reachability testing tool

```
    # /bin/sh pcs_regression.sh https://{{ FQDN of Web server }}
    [PCS Version(default) RT OK] (※　reachability testing is done, if it shows the same)
```
--------------------------------------------------------------------

## Conclusion

We explored to deploy personium on different environment using Ansible. Like as we also was able to deploy personium on AWS environment using CFT(Cloud Formation Template). This documnet is written for the developers conveniency, to be able to delpoy personium easily on the top environment. Also we have choosen ansible as it is designed as a multi-tier orchestration system, and by default runs in an easy push-based mode incorporate tests into the end of a playbook run.

Finally, hoping the readers will give a try deploying personium on any suitable environment. enjoy the system and give us feedback for further development of personium. Your comments or requiest will be highly appritiated.

--------------------------------------------------------------------
