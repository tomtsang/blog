

snapd
=====

ubuntu 下

192.168.31.197 出现的问题, 
snapd 下的 gdm3 , 一直往 /var/log/syslog 下写数据, 妈妈呀, 谁受得了这个....
后来, 重启后, 通过 查看 /var/log/syslog 可以得到一些信息, 很多信息来自于 它们3个, 所以, 我直接把它们关闭了.

::

    systemctl status gdm3
    systemctl stop gdm3
    systemctl disable gdm3
    systemctl status snapd
    systemctl stop snapd
    systemctl disable snapd
    systemctl stop haproxy
    systemctl disable haproxy


