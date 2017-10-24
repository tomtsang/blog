===========================
README (install-by-ubuntu)
===========================

本次安装，完全按照 官方文档 http://docs.ceph.com/docs/master/start/ 进行。

env
=============

::

    192.168.31.115 cephadmin # 这个是admin节点
    192.168.31.114 mon1 # 存储节点
    192.168.31.113 cephfsn2 # 存储节点
    192.168.31.173 cephfsn3  # 存储节点
    192.168.31.172 ceph-client  # 客户端节点

上面这几个，可以全部写进 /etc/hosts 中。且各节点的 hostname 必须对应相同（否则安装容易出错）。

