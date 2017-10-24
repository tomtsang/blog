=================
Preflight
=================


# http://docs.ceph.com/docs/master/start/quick-start-preflight/#

这里的配置注意点如下：

ceph-admin 节点：
^^^^^^^^^^^^^^^^^^^^^^

安装时， with a stable Ceph release (e.g., luminous.) ，安装 luminous 或以上版本。

::

        cephu@cephadmin:~/my-cluster$ sudo cat /etc/apt/sources.list.d/ceph.list
        deb https://download.ceph.com/debian-luminous/ xenial main
        cephu@cephadmin:~/my-cluster$

配置 ~/.ssh/config 方便后续安装

::

    cephu@cephadmin:~/my-cluster$ cat ~/.ssh/config
    Host node0
        Hostname cephadmin
        User cephu
    Host node1
        Hostname mon1
        User cephu
    Host node2
        Hostname cephfsn2
        User cephu
    Host node3
        Hostname cephfsn3
        User cephu
    cephu@cephadmin:~/my-cluster$

这里，我后来想了一下，应该把这个地方，修改成 全用 node0, node1, node2, node3, 然后，各节点的　Hostname 也修改成这些，这样子才是真的方便安装。如下：

::

    cephu@cephadmin:~/my-cluster$ cat ~/.ssh/config
    Host node0
        Hostname node0
        User cephu
    Host node1
        Hostname node1
        User cephu
    Host node2
        Hostname node2
        User cephu
    Host node3
        Hostname node3
        User cephu
    cephu@cephadmin:~/my-cluster$

存储节点：
^^^^^^^^^^^^^^^^^

::

        cephu@cephadmin:~/my-cluster$ cat /etc/hosts
        127.0.0.1       localhost
        127.0.1.1       ubuntu
        #127.0.0.1      cephfs5

        # The following lines are desirable for IPv6 capable hosts
        ::1     localhost ip6-localhost ip6-loopback
        ff02::1 ip6-allnodes
        ff02::2 ip6-allrouters

        192.168.31.115 cephadmin
        192.168.31.114 mon1
        192.168.31.113 cephfsn2
        192.168.31.173 cephfsn3
