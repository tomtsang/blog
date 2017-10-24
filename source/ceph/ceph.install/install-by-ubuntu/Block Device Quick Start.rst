==================================
Block Device Quick Start
==================================

env
========

192.168.31.172 ceph-client

修改 hostname 为 ceph-client

INSTALL CEPH
=============

::

    ceph-deploy install ceph-client
    ceph-deploy admin ceph-client
    sudo chmod +r /etc/ceph/ceph.client.admin.keyring