=======================
maven config
=======================

配置你的 Maven 设置

env1
====================

::

    192.168.31.181 master centos7
    192.168.31.172 slave1 ubuntu16
    192.168.31.240 slave2 centos7

env2
====================

::

    120.25.204.216 master jlchaliyun
    120.234.23.98 slave1 jlchsecdn
    202.104.136.202 slave2 jlchsetom

slave 节点安装 maven
========================================

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

错误如下:

::

    No compiler is provided in this environment. Perhaps you are running on a JRE rather than a JDK?

怎么办?

解决 "No compiler is provided in this environment. Perhaps you are running on a JRE rather than a JDK?"
========================================================================================================= 

网上有一些文章都是在说 eclipse 下的配置, 与我们的环境不符嘛.

https://stackoverflow.com/questions/26313902/maven-error-perhaps-you-are-running-on-a-jre-rather-than-a-jdk

提出使用
`apt-get install openjdk-7-jdk openjdk-7-doc openjdk-7-jre-lib`
但是我们的 slave 是 centos7. 
接着查到了
http://blog.csdn.net/cdnight/article/details/78293767

先安装
yum install java-devel -y
试一下.来吧

::

    sudo yum install java-devel -y
    cd game-of-life/
    ls
    mvn clean package
    .....
    [INFO] gameoflife-web ..................................... SUCCESS [ 35.860 s]
    [INFO] ------------------------------------------------------------------------
    [INFO] BUILD SUCCESS
    [INFO] ------------------------------------------------------------------------
    [INFO] Total time: 49.793 s
    [INFO] Finished at: 2017-11-17T09:40:58+08:00
    [INFO] Final Memory: 30M/337M
    [INFO] ------------------------------------------------------------------------
    [jenkins@test_240 game-of-life]$

我去,成功了.哈哈哈哈~~~~

重新jenkins构建
=================

打开 http://192.168.31.181:8080/

点击 立即构建 .

哈哈, 成功了.

到这里,说明 maven 是配置OK了.



