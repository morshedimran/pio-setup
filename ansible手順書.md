◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆
# Ansible Procedure manual for personium auto-generation

◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆


Ansible procedure for auto-constructing personium, using 4 servers or more.

---------------------------------------
GETTING STARTED 

## Server setup :white_check_mark:
  Following 8 rules are required to assign on the servers, where personium will be constructed. :one:

1. `Web`
2. Item 2
3. Item 3


  
  ・`Web`				リバースプロキシサーバー。Global IPを持ち、インターネットへ接続している必要がある。
  
  ・AP				アプリケーションサーバー。Personium本体を実行する。
  
  ・ADS_Master			正本（MySQL）サーバー。マスターとして正本を保持する。
  
  ・ADS_Slave			正本（MySQL）の複製(スレーブ)サーバー。
  
  ・ES				ElasticSearchを実行するサーバー。
  
  ・NFS				NFSを稼働させるためのサーバー。
  
  ・Bastion			踏み台サーバー。ansibleの実行や、各サーバーへのSSH接続に用いる。
  
  ・Backup			PIOツール用サーバー:two:。必要に応じてADS_SlaveやNFSサーバーからバックアップを行う。

:one:：1つのサーバーに対し、これらの役割を複数を割り当てることも可能である。

:two:：ツールとしてPIOデータバックアップツール、整合性チェックツール、Cell再帰的削除ツール、Elasticsearchインデックスリストアツールを備えている。

□サーバー構成例
  Personiumを実行するにあたり、実績のあるサーバー構成を以下に示す。
  ・4台構成
    サーバー1：Web, Bastion
    サーバー2：AP, NFS
    サーバー3：ES, ADS_Master
    サーバー4：ADS_Slave, backup


□ファイル構成
  /init_personium.yml			ansible-playbookコマンドで実行するべきyml
  /[group名].yml			groupごとの変数読み込みを行い、実行タスクをまとめるyml
  /ansible.cfg				実行に必要な設定が記述されている。変更不可。

  /static_inventory/			IPなど各環境に設定が必須な情報を置くフォルダ
  ★/hosts				各ホストの設定（IP address, FQDN, group, User name, Private Keyなど）

  /group_vars/				各種カスタマイズ・チューニングを行うためのファイルを置くフォルダ
  ★/[group名].yml			groupごとのカスタマイズ・チューニングに必要な設定値をまとめる

  /resource/				タスクに必要なファイル（リソース/変更が不要のもの）をまとめるフォルダ
    /[group名]/				groupごとのリソースを格納する

  /tasks/				タスクをまとめるフォルダ
    /[group名]/				groupごとの具体的なタスクを格納する

  /handlers/				ハンドラーをまとめるフォルダ
    /[group名]/				groupごとのハンドラーを格納する


  ※★…環境に応じた設定が必要となるファイル

  ※[group名]…web, ap, nfs, es, ads_master, ads_slave, bastion、backupおよびcommonの9種類
  （commonはサーバーの役割の名称ではないが、複数サーバーに共通した機能を提供するために設定する）


---------------------------------------
取扱注意ファイル：
以下のファイルはansible実行中に自動生成されるが、取扱注意のキーである。
  /fj/dc-core/conf/salt.key
  /fj/dc-core/conf/token.key


◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇

Part 1. 初期設定

◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇◇

■前提条件
・使用ユーザー:root
・ansible実行ユーザー:root
・ansible実行環境：Web/Bastionサーバー
・固定Global IPの確保とDNSへの登録は済ませておく。
・各サーバーに割り振られるPrivate IPを固定化しておく。

------------------------------------------------------------------------------------------------
□ansibleのパラメーターを設定
------------------------------------------------------------------------------------------------
・ansibleの以下ファイルを編集する

  - /static_inventory/hostsの編集  ★必須
  各パラメーターの値を設定する。※各パラメーターに関しては別資料「参考_ansible設定.txt」を参照
  
  - /group_vars/[group名].ymlの確認
  各サーバのチューニングを行う場合にパラメーターの値を設定する。※各パラメーターに関しては「ansible設定.txt」を参照


------------------------------------------------------------------------------------------------
□ansible資材の配備（対象サーバー：Bastionサーバー）
------------------------------------------------------------------------------------------------
・WinSCPなどを用い、Bastionサーバへ接続。
  （WinSCP：http://win-scp.joydownload.jp/?c=5&gclid=CK7L0pvWpcACFVYHvAodOF0AgQ）

・Ansibleフォルダをアップロードする。
  アップロード先：/root/


------------------------------------------------------------------------------------------------
□自己署名ユニット証明書/秘密鍵の準備
------------------------------------------------------------------------------------------------
・ユニット証明書の作成方法は別資料「参考_自己署名ユニット証明書作成手順.txt」を参照


------------------------------------------------------------------------------------------------
□自己署名ユニット証明書/秘密鍵の配置
------------------------------------------------------------------------------------------------
・証明書の配置
作成した自己署名ユニット証明書と秘密鍵をansibleフォルダの/resource/ap/opt/x509/フォルダへ配置する
ファイル名は以下の通り。
/resource/ap/opt/x509/
	unit-self-sign.crt（自己署名ユニット証明書）
	unit.key（秘密鍵）

※「自己署名ユニット証明書/秘密鍵の準備」にて自己署名ユニット証明書を作成した場合、この手順は不要です。


------------------------------------------------------------------------------------------------
□SSL証明書/秘密鍵の準備
------------------------------------------------------------------------------------------------
・SSL証明書と秘密鍵を別途準備しておく。

※正式なSSL証明書が構築時点でない場合は、自己署名ssl証明書を暫定で準備し、使用する
以下は自己署名ssl証明書作成手順です。：
       ⇒　# cd /root/ansible/resource/web/opt/nginx/conf
       ⇒　# openssl genrsa -des3 -out server.key 1024
               途中で「Enter pass phrase for server.key:」が表示されるが、任意の値を入力すること。
               長さは4文字～8191文字とする。
       ⇒　# openssl req -new -key server.key -out server.csr
               ・「Enter pass phrase for server.key:」はserver.keyで入力した値を用いる。
               ・以下の入力項目が表示されるが、任意の値を入力すること
                   ここから--------
                   Country Name (2 letter code) [XX]:
                   State or Province Name (full name) []:
                   Locality Name (eg, city) [Default City]:
                   Organization Name (eg, company) [Default Company Ltd]:
                   Organizational Unit Name (eg, section) []:
                   Common Name (eg, your name or your server's hostname) []:
                   Email Address []:
                   
                   Please enter the following 'extra' attributes
                   to be sent with your certificate request
                   A challenge password []:
                   An optional company name []:
                   ここまで--------
       ⇒　# cp server.key server.key.org
       ⇒　# openssl rsa -in server.key.org -out server.key
               ・「Enter pass phrase for server.key.org:」はserver.keyで入力した値を用いる。
       ⇒　# openssl x509 -req -days 365 -in server.csr -signkey server.key -out server.crt
       ⇒　# ls -l server.*
             ⇒以下のファイルが出力されること
               server.key.org
               server.crt
               server.csr
               server.key
  参照：http://qiita.com/nenokido2000/items/1d1c79a0a443ed923e92x

------------------------------------------------------------------------------------------------
□SSL証明書/秘密鍵の配置
------------------------------------------------------------------------------------------------

・証明書の配置
取得した証明書を/root/ansible/resource/web/opt/nginx/conf/フォルダへ配置する
/root/ansible/resource/web/opt/nginx/conf/
	server.crt（ssl証明書）
	server.key（秘密鍵）
※「SSL証明書/秘密鍵の準備」にて自己署名SSL証明書を作成した場合、この手順は不要です。

証明書の配置は完了です。



------------------------------------------------------------------------------------------------
□DISKの追加
------------------------------------------------------------------------------------------------
以下のサーバに増設ディスクの追加を作成しておく(下記ディスクサイズは推奨値)。
なお、ディスクのパスは下記括弧で記載されたパス固定で設定をすること。
ES + ADS_Masterサーバー         300GB (パス : /dev/xvdb)    用途：MySQLのdata
                                100GB (パス : /dev/xvdc)    用途：ESのdata
ADS_Slave + Backup サーバー     200GB (パス : /dev/xvdb)    用途：MySQLのdata(Slave)
                                200GB (パス : /dev/xvdc)    用途：データバックアップ
AP + NFSサーバー                 50GB (パス : /dev/xvdb)    用途：PIOログ
                                100GB (パス : /dev/xvdc)    用途：WebDav、イベントログ

------------------------------------------------------------------------------------------------
□SSHキーの作成（対象サーバー：Bastionサーバー）
------------------------------------------------------------------------------------------------
・Bastionサーバーに接続し、SSHキーを作成する
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
□SSHキーの配置（対象サーバー：Bastion以外の全サーバー）
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

