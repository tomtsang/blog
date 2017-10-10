
cephfs
------

环境：
^^^^^^^^^

192.168.31.115
192.168.31.114
192.168.31.113


line1 官网
^^^^^^^

我的安装设计是这样的：

admin-node, deploy-node(ceph-deploy)：192.168.31.115  cephfs5
mon.0: 192.168.31.114   cephfs4
osd.0: 192.168.31.113   cephfs3
osd.1: 192.168.31.115   cephfs5
mds.0: 192.168.31.113   cephfs3
mds.1: 192.168.31.114   cephfs4

line2
^^^^^^^

http://tonybai.com/2017/05/08/mount-cephfs-acrossing-nodes-in-kubernetes-cluster/


over
^^^^^^^^^