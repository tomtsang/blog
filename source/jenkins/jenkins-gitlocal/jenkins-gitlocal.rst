
===================
jenkins-gitlocal
===================


https://juejin.im/entry/58f18fc2570c3500562cb433


env
=========

::

    192.168.31.181 master
    192.168.31.172 slave1
    192.168.31.240 slave2

install
=========

https://pkg.jenkins.io/redhat-stable/

::

    
    sudo systemctl enable jenkins
    sudo systemctl start jenkins

防火墙把 8080 打开


配置master
===============

把jenkins用户设置为sudo用户

::

    $ grep jenkins /etc/passwd
    jenkins:x:496:496:Jenkins Continuous Integration Server:/var/lib/jenkins:/bin/false

修改一下,让可以登陆

::

    $ grep jenkins /etc/passwd
    jenkins:x:496:496:Jenkins Continuous Integration Server:/var/lib/jenkins:/bin/bash
    $ su jenkins
    $ cd ~
    $ pwd
    /var/lib/jenkins


配置公钥

配置slave节点
=================

建立用户
-----------------

::

    sudo adduser jenkins
    sudo passwd jenkins


配置公钥给slave
-----------------
::

    ssh jenkins@slave1 'mkdir -p .ssh && cat >> .ssh/authorized_keys' < ~/.ssh/id_rsa.pub
    ssh jenkins@slave2 'mkdir -p .ssh && cat >> .ssh/authorized_keys' < ~/.ssh/id_rsa.pub

把 jenkins 用户加入到 sudo 用户
-------------------------------


配置Node
==================

来到浏览器, 192.168.31.181:8080, 登陆

系统管理/管理节点/新建节点/

注意要填写 label 哟

这个时候, 如果说能够正常连接上就说明 slave 成功与 master 连接了.

如果出错了, 马上看日志吧. 

如果日志信息说是

找不到 java , 而自己去 slave 命令行下 which java, 是有正常返回的,那说明,是少了 openjdk .
怎么办?安装呗.

::

    [jenkins@test_240 ~]$ sudo yum search openjdk
    [jenkins@test_240 ~]$ sudo yum install -y java-1.8.0-openjdk

好了,这下安装成功,然后重新再来一连接, 成功了哟!

配置pipeline
==================

Repository URL 时: 写入具体的 git 地址就可以了. 如: git@secdn:trend.git, 这个地址后续会在 slave 节点中运行 git clone git@secdn:trend.git 
这也是说, 这些 slave 节点, 要有这个 trend 仓库的git clone 权限哟. 如果没有设置,这里要回头去设置了.

构建
==================

点一下"立即构建"
看效果吧.

game over!
==============
