=======================
chap02
=======================

env
====================

::

    192.168.31.181 master
    192.168.31.172 slave1
    192.168.31.240 slave2

slave 节点安装 maven
=========================

http://maven.apache.org/download.cgi

上 slave节点

下载apache-maven-3.5.2-bin.tar.gz包

::
    cd /home/jenkins/
    mkdir software && cd software
    wget http://mirror.bit.edu.cn/apache/maven/maven-3/3.5.2/binaries/apache-maven-3.5.2-bin.tar.gz
    tar -zxvf apache-maven-3.5.2-bin.tar.gz 

添加成下面这个样子
这样, maven 的文件夹就是
/home/jenkins/software/apache-maven-3.5.2/
让它生效吧.后续会把这个路径加入到 jenkins 的maven配置中来

::

    cd /home/jenkins
    vi .bash_profile
    [jenkins@test_240 game-of-life-default]$ cat ~/.bash_profile
    # .bash_profile

    # Get the aliases and functions
    if [ -f ~/.bashrc ]; then
        . ~/.bashrc
    fi

    # User specific environment and startup programs

    PATH=$PATH:$HOME/.local/bin:$HOME/bin

    export PATH
    # maven所在的目录
    export M2_HOME=/home/jenkins/software/apache-maven-3.5.2
    # maven bin所在的目录
    export M2=$M2_HOME/bin
    # 将maven bin加到PATH变量中
    export PATH=$M2:$PATH
    [jenkins@test_240 game-of-life-default]$    

检验一下

::

    source .bash_profile
    [jenkins@test_240 game-of-life-default]$ mvn -version
    Apache Maven 3.5.2 (138edd61fd100ec658bfa2d307c43b76940a5d7d; 2017-10-18T15:58:13+08:00)
    Maven home: /home/jenkins/software/apache-maven-3.5.2
    Java version: 1.8.0_151, vendor: Oracle Corporation
    Java home: /usr/lib/jvm/java-1.8.0-openjdk-1.8.0.151-1.b12.el7_4.x86_64/jre
    Default locale: zh_CN, platform encoding: UTF-8
    OS name: "linux", version: "3.10.0-229.14.1.el7.x86_64", arch: "amd64", family: "unix"
    [jenkins@test_240 game-of-life-default]$

说明安装 Maven 成功.



配置你的 Maven 设置
======================
回 master 节点.

开浏览器.

http://blog.csdn.net/tengxing007/article/details/77626628

点击 系统管理/全局tool配置(Global Tool Configuration)/
在 Maven 项目中,写入 slave 的 maven 地址(/home/jenkins/software/apache-maven-3.5.2/)

::

    Name  192.168.31.240.jenkins.maven3.5.2
    MAVEN_HOME /home/jenkins/software/apache-maven-3.5.2/

因为 这个 maven 地址, 不是 master的maven 地址,所以会有一个黄色的小警告,如下:

::

    /home/jenkins/software/apache-maven-3.5.2 is not a directory on the Jenkins master (but perhaps it exists on some agents)

好了, 配置完成了.

配置Job
===================
源码管理
选择 Git
    Repository URL 写: git@github.com:tomtsang/game-of-life.git

构建
Invoke top-level Maven targets
[Help]
 	Maven Version	写入: 192.168.31.240.jenkins.maven3.5.2	
 	Goals 写入:	clean package

构建后操作
Archive the artifacts
 	用于存档的文件	写入: **/target/*.jar


立即构建
=====================

点击

报错了.

然后,我直接上 slave 节点, 去把 工程 clone 下来, 然后直接跑 mvn clean package, 然后输出与 jenkins 日志一样, 确实是报错了.

