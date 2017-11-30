======================================
把ip从办公31段移动到生产10段
======================================

env
======================================

192.168.31.247 =》 10.10.12.20

step
======================================

修改IP配置如下
--------------------------------------

::

    jlch@uRedis:~$ sudo cat /etc/network/interfaces
    [sudo] password for jlch: 
    # This file describes the network interfaces available on your system
    # and how to activate them. For more information, see interfaces(5).

    source /etc/network/interfaces.d/*

    # The loopback network interface
    auto lo
    iface lo inet loopback

    # The primary network interface
    auto eno1
    iface eno1 inet manual
    bond-master bond0

    auto eno2
    iface eno2 inet manual
    bond-master bond0

    auto bond0
    iface bond0 inet static
    address 10.10.12.20
    netmask 255.255.255.0
    gateway 10.10.12.1
    # dns-* options are implemented by the resolvconf package, if installed
    dns-nameservers 202.96.134.133

    bond-mode 0
    bond-miimon 100
    bond-slaves eno1 eno2
    jlch@uRedis:~$

重启网络
--------------------------------------

::

    jlch@ubuntu:~$ sudo /etc/init.d/networking status
    jlch@ubuntu:~$ sudo /etc/init.d/networking restart
    jlch@ubuntu:~$ sudo /etc/init.d/networking status
    [sudo] password for jlch: 
    ● networking.service - Raise network interfaces
    Loaded: loaded (/lib/systemd/system/networking.service; enabled; vendor preset: enabled)
    Drop-In: /run/systemd/generator/networking.service.d
            └─50-insserv.conf-$network.conf
    Active: active (exited) since Thu 2017-11-30 02:23:26 CST; 7h ago
        Docs: man:interfaces(5)
    Process: 50569 ExecStop=/sbin/ifdown -a --read-environment (code=exited, status=0/SUCCESS)
    Process: 50624 ExecStart=/sbin/ifup -a --read-environment (code=exited, status=0/SUCCESS)
    Process: 50617 ExecStartPre=/bin/sh -c [ "$CONFIGURE_INTERFACES" != "no" ] && [ -n "$(ifquery --read-environment --list --exclude=lo)" ] && udevadm settle (code=exited, status=0/SUCCESS)
    Main PID: 50624 (code=exited, status=0/SUCCESS)
        Tasks: 0
    Memory: 0B
        CPU: 0
    CGroup: /system.slice/networking.service

    Nov 30 02:23:25 ubuntu systemd[1]: Starting Raise network interfaces...
    Nov 30 02:23:25 ubuntu ifup[50624]: Waiting for bonding kernel module to be ready (will timeout after 5s)
    Nov 30 02:23:25 ubuntu ifup[50624]: Waiting for bond master bond0 to be ready
    Nov 30 02:23:26 ubuntu systemd[1]: Started Raise network interfaces.

在重启后，可能就会断开连接了。
这个时候需要网络工程师作调整（请通知他），则，直到网络工程师调整完成后，再次 ssh 连接。

检查
--------------------------------------

::

    jlch@ubuntu:~$ ip a
    1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1
        link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
        inet 127.0.0.1/8 scope host lo
        valid_lft forever preferred_lft forever
        inet6 ::1/128 scope host 
        valid_lft forever preferred_lft forever
    2: eno1: <BROADCAST,MULTICAST,SLAVE,UP,LOWER_UP> mtu 1500 qdisc mq master bond0 state UP group default qlen 1000
        link/ether 18:66:da:9d:ca:62 brd ff:ff:ff:ff:ff:ff
        inet 192.168.31.247/24 brd 192.168.31.255 scope global eno1
        valid_lft forever preferred_lft forever
    3: eno2: <NO-CARRIER,BROADCAST,MULTICAST,SLAVE,UP> mtu 1500 qdisc mq master bond0 state DOWN group default qlen 1000
        link/ether 18:66:da:9d:ca:62 brd ff:ff:ff:ff:ff:ff
    4: eno3: <BROADCAST,MULTICAST> mtu 1500 qdisc noop state DOWN group default qlen 1000
        link/ether 18:66:da:9d:ca:64 brd ff:ff:ff:ff:ff:ff
    5: eno4: <BROADCAST,MULTICAST> mtu 1500 qdisc noop state DOWN group default qlen 1000
        link/ether 18:66:da:9d:ca:65 brd ff:ff:ff:ff:ff:ff
    6: bond0: <BROADCAST,MULTICAST,MASTER,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default qlen 1000
        link/ether 18:66:da:9d:ca:62 brd ff:ff:ff:ff:ff:ff
        inet 10.10.12.20/24 brd 10.10.12.255 scope global bond0
        valid_lft forever preferred_lft forever
        inet6 fe80::1a66:daff:fe9d:ca62/64 scope link 
        valid_lft forever preferred_lft forever
    jlch@ubuntu:~$ ping 10.10.12.1
    jlch@ubuntu:~$ 

ping 10.10.12.1 正常，说明网络已经OK了。

注意，这里虽然 eno1 为 192.168.31.247，且可以做到 ping 192.168.31.247, 但是，ping 192.168.31.1 已不通。这个无关紧要了。


