サーバー4台以上で構成されるpersonium.ioサービスを構築するための手順書

---------------------------------------
はじめに

□サーバー構成
  Personiumを構成するサーバーには、以下の8種の役割を割り振る必要がある(※1)。
  ・Web				リバースプロキシサーバー。Global IPを持ち、インターネットへ接続している必要がある。
  ・AP				アプリケーションサーバー。Personium本体を実行する。
  ・ADS_Master			正本（MySQL）サーバー。マスターとして正本を保持する。
  ・ADS_Slave			正本（MySQL）の複製(スレーブ)サーバー。
  ・ES				ElasticSearchを実行するサーバー。
  ・NFS				NFSを稼働させるためのサーバー。
  ・Bastion			踏み台サーバー。ansibleの実行や、各サーバーへのSSH接続に用いる。
  ・Backup			PIOツール用サーバー(※2)。必要に応じてADS_SlaveやNFSサーバーからバックアップを行う。

※1：1つのサーバーに対し、これらの役割を複数を割り当てることも可能である。
※2：ツールとしてPIOデータバックアップツール、整合性チェックツール、Cell再帰的削除ツール、Elasticsearchインデックスリストアツールを備えている。

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
