

nfs-server
==========

Ubuntu 16.04安装NFS server
==========================

reference
---------

http://www.linuxidc.com/Linux/2016-04/129848.htm

http://www.linuxidc.com/Linux/2012-09/70728.htm

环境
----

| 192.168.31.232 nfs-server
| 192.168.31.197 nfs-client, 测试

step
----

安装包

::

    sudo apt-get -y install nfs-kernel-server 
    sudo apt-get -y install nfs-common

设置存储位置

::

    mkdir -p /opt/share
    chmod -R 777 /opt/share

配置 /etc/exports

::

    root@ubuntu:/opt/share# vi /etc/exports
    root@ubuntu:/opt/share# cat /etc/exports
    # /etc/exports: the access control list for filesystems which may be exported
    #               to NFS clients.  See exports(5).
    #
    # Example for NFSv2 and NFSv3:
    # /srv/homes       hostname1(rw,sync,no_subtree_check) hostname2(ro,sync,no_subtree_check)
    #
    # Example for NFSv4:
    # /srv/nfs4        gss/krb5i(rw,sync,fsid=0,crossmnt,no_subtree_check)
    # /srv/nfs4/homes  gss/krb5i(rw,sync,no_subtree_check)
    #
    /opt/share *(rw,no_root_squash)
    root@ubuntu:/opt/share#

重启相关服务

::

    sudo service rpcbind restart
    sudo service portmap restart
    sudo service nfs-kernel-server restart

到 nfs-client 机器中，创建 a.txt , 然后回来

::

    cd /opt/share/
    ls
    cat a.txt

如果有数据，那就是OK了。

over
----