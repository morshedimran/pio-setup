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

Please go thru to the following file for the construction procedure:
`ansible execution process.md`, `Ansible Setup_Reference .md` and `Self-signed unit certificate creation procedure_Reference.md`
