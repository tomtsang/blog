http://www.linuxidc.com/Linux/2016-01/127983.htm

一.CentOS下查看系统是否已安装telnet

::

    rpm -qa | grep telnet
    telnet-0.17-48.el6.x86_64
    telnet-server-0.17-48.el6.x86_64

telnet 是挂在 xinetd 底下的，所以同时查看是否安装了xinetd服务

::

    rpm -qa | grep xinetd
    xinetd-2.3.14-39.el6_4.x86_64

两者都没有的安装的话，yum安装以下服务

::

    yum install xinetd telnet telnet-server -y