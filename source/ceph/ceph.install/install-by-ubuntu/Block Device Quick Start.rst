==================================
Block Device Quick Start
==================================

env
========

192.168.31.172 ceph-client

修改 hostname 为 ceph-client

INSTALL CEPH
=============

admin 节点：

::

    ceph-deploy install ceph-client
    ceph-deploy admin ceph-client

ceph-client 节点：

::

    sudo chmod +r /etc/ceph/ceph.client.admin.keyring

CREATE A BLOCK DEVICE POOL
===========================

admin 节点：

原来在 admin 节点，是没有这个 rbd 命令的。

要通过 sudo apt install ceph-common 之后，才会有。所以先运行一下。

::

    cephu@cephadmin:~/my-cluster$ sudo apt install ceph-common -y
    cephu@cephadmin:~/my-cluster$ rbd pool init jlch
    2017-10-24 15:57:45.951917 7fde4fa6e0c0 -1 auth: unable to find a keyring on /etc/ceph/ceph.client.admin.keyring,/etc/ceph/ceph.keyring,/etc/ceph/keyring,/etc/ceph/keyring.bin,: (2) No such file or directory
    2017-10-24 15:57:45.951937 7fde4fa6e0c0 -1 monclient: ERROR: missing keyring, cannot use cephx for authentication
    2017-10-24 15:57:45.951942 7fde4fa6e0c0  0 librados: client.admin initialization error (2) No such file or directory
    rbd: couldn't connect to the cluster!
    cephu@cephadmin:~/my-cluster$

ceph-client 节点：

::

    jlch@k-m:/etc/apt/sources.list.d$ sudo ls /etc/ceph/* -l
    [sudo] password for jlch:
    -rw-r--r-- 1 root root  63 Oct 24 15:32 /etc/ceph/ceph.client.admin.keyring
    -rw-r--r-- 1 root root 249 Oct 24 15:32 /etc/ceph/ceph.conf
    -rw-r--r-- 1 root root  92 Apr 21  2017 /etc/ceph/rbdmap
    -rw------- 1 root root   0 Oct 24 11:20 /etc/ceph/tmp2IJh4C
    jlch@k-m:/etc/apt/sources.list.d$

我去，明明有的，为什么说 unable to find a keyring on *******，哪里出的问题?



