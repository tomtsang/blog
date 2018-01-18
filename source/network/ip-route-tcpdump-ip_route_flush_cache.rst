=================================================
因 三层交换 配置导致的 服务器的返回数据包没有到达防火强
=================================================

客户=>防火墙=>三层交换=>nginx=>服务器=>nginx=>三层交换=>防火墙=>客户

env
=================================================

中泰反馈说, wget http://58.250.241.39:8000/ok/ 无效


step
=================================================

* ping api-cm.zhangtl.com
* telnet 58.250.241.39
* 在 nginx 机器 tcpdump 抓包
* 防火墙 抓包 wireshark
* 检查 route 表


在 nginx 机器 tcpdump 抓包
-------------------------------------------------

::

    [root@nginx jlch]# tcpdump -i em1 -vv host 116.62.200.151
    tcpdump: listening on em1, link-type EN10MB (Ethernet), capture size 65535 bytes
    ^C
    0 packets captured
    0 packets received by filter
    0 packets dropped by kernel
    [root@nginx jlch]# ip a
    1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN
        link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
        inet 127.0.0.1/8 scope host lo
        valid_lft forever preferred_lft forever
        inet6 ::1/128 scope host
        valid_lft forever preferred_lft forever
    2: em1: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc mq state UP qlen 1000
        link/ether 44:a8:42:2b:e4:df brd ff:ff:ff:ff:ff:ff
        inet 10.10.11.10/24 brd 10.10.11.255 scope global em1
        valid_lft forever preferred_lft forever
        inet6 fe80::46a8:42ff:fe2b:e4df/64 scope link
        valid_lft forever preferred_lft forever
    3: em2: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc mq state UP qlen 1000
        link/ether 44:a8:42:2b:e4:e0 brd ff:ff:ff:ff:ff:ff
        inet 10.10.12.200/24 brd 10.10.12.255 scope global em2
        valid_lft forever preferred_lft forever
        inet6 fe80::46a8:42ff:fe2b:e4e0/64 scope link
        valid_lft forever preferred_lft forever
    [root@nginx jlch]# tcpdump -i em1 -vv host 116.62.200.151
    tcpdump: listening on em1, link-type EN10MB (Ethernet), capture size 65535 bytes
    ...
    ...
    ...
    18:07:23.400943 IP (tos 0x0, ttl 49, id 35291, offset 0, flags [DF], proto TCP (6), length 60)
        116.62.200.151.45546 > nginx.https: Flags [S], cksum 0xf6cf (correct), seq 1754917320, win 29200, options [mss 1460,sackOK,TS val 196508536 ecr 0,nop,wscale 7], length 0
    18:07:23.400990 IP (tos 0x0, ttl 64, id 0, offset 0, flags [DF], proto TCP (6), length 60)
        nginx.https > 116.62.200.151.45546: Flags [S.], cksum 0x5218 (incorrect -> 0xda72), seq 2351412130, ack 1754917321, win 28960, options [mss 1460,sackOK,TS val 3173333940 ecr 196501520,nop,wscale 7], length 0
    18:07:27.586308 IP (tos 0x0, ttl 64, id 0, offset 0, flags [DF], proto TCP (6), length 60)
        nginx.https > 116.62.200.151.45546: Flags [S.], cksum 0x5218 (incorrect -> 0xca18), seq 2351412130, ack 1754917321, win 28960, options [mss 1460,sackOK,TS val 3173338126 ecr 196501520,nop,wscale 7], length 0
    18:07:31.408717 IP (tos 0x0, ttl 49, id 35292, offset 0, flags [DF], proto TCP (6), length 60)
        116.62.200.151.45546 > nginx.https: Flags [S], cksum 0xd787 (correct), seq 1754917320, win 29200, options [mss 1460,sackOK,TS val 196516544 ecr 0,nop,wscale 7], length 0
    18:07:31.408763 IP (tos 0x0, ttl 64, id 0, offset 0, flags [DF], proto TCP (6), length 60)
        nginx.https > 116.62.200.151.45546: Flags [S.], cksum 0x5218 (incorrect -> 0xbb2a), seq 2351412130, ack 1754917321, win 28960, options [mss 1460,sackOK,TS val 3173341948 ecr 196501520,nop,wscale 7], length 0
    18:07:39.586327 IP (tos 0x0, ttl 64, id 0, offset 0, flags [DF], proto TCP (6), length 60)
        nginx.https > 116.62.200.151.45546: Flags [S.], cksum 0x5218 (incorrect -> 0x9b38), seq 2351412130, ack 1754917321, win 28960, options [mss 1460,sackOK,TS val 3173350126 ecr 196501520,nop,wscale 7], length 0

可以看到, 数据包, 从 nginx.https 返回给了 116.62.200.151...

检查 route 表
--------------------------------------------

那这个时候就要看 防火墙 抓包 wireshark

我去, 防火墙 没有 找到   nginx=>三层交换=>防火墙   的数据包....哇...

因 三层交换 配置导致的 服务器的返回数据包没有到达防火强


route 是不是正确

::

    [root@nginx nginx]# ip a
    1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN
        link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
        inet 127.0.0.1/8 scope host lo
        valid_lft forever preferred_lft forever
        inet6 ::1/128 scope host
        valid_lft forever preferred_lft forever
    2: em1: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc mq state UP qlen 1000
        link/ether 44:a8:42:2b:e4:df brd ff:ff:ff:ff:ff:ff
        inet 10.10.11.10/24 brd 10.10.11.255 scope global em1
        valid_lft forever preferred_lft forever
        inet6 fe80::46a8:42ff:fe2b:e4df/64 scope link
        valid_lft forever preferred_lft forever
    3: em2: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc mq state UP qlen 1000
        link/ether 44:a8:42:2b:e4:e0 brd ff:ff:ff:ff:ff:ff
        inet 10.10.12.200/24 brd 10.10.12.255 scope global em2
        valid_lft forever preferred_lft forever
        inet6 fe80::46a8:42ff:fe2b:e4e0/64 scope link
        valid_lft forever preferred_lft forever
    [root@nginx nginx]# route -n
    Kernel IP routing table
    Destination     Gateway         Genmask         Flags Metric Ref    Use Iface
    0.0.0.0         10.10.11.1      0.0.0.0         UG    100    0        0 em1
    10.10.11.0      0.0.0.0         255.255.255.0   U     100    0        0 em1
    10.10.12.0      0.0.0.0         255.255.255.0   U     100    0        0 em2

看一下 traceroute , 果然发现问题

::

    [root@nginx nginx]# traceroute 116.62.200.151
    traceroute to 116.62.200.151 (116.62.200.151), 30 hops max, 60 byte packets
    1  10.10.11.254 (10.10.11.254)  0.520 ms  0.500 ms  0.546 ms
    2  120.234.23.97 (120.234.23.97)  13.296 ms  13.302 ms  13.454 ms
    3  120.196.5.249 (120.196.5.249)  3.505 ms  3.793 ms  3.794 ms
    4  211.136.251.113 (211.136.251.113)  4.563 ms 211.136.248.81 (211.136.248.81)  4.505 ms 211.136.248.85 (211.136.248.85)  4.685 ms
    5  221.183.39.153 (221.183.39.153)  8.659 ms  9.296 ms  8.643 ms
    6  221.183.40.45 (221.183.40.45)  34.139 ms  33.174 ms  32.776 ms
    7  221.183.39.130 (221.183.39.130)  33.958 ms  37.229 ms  33.166 ms
    8  . (211.136.189.2)  33.906 ms  33.152 ms  34.244 ms
    9  . (117.185.3.206)  34.554 ms . (117.185.3.202)  34.692 ms  33.941 ms
    10  140.205.50.234 (140.205.50.234)  35.491 ms 140.205.50.230 (140.205.50.230)  33.408 ms 116.251.113.198 (116.251.113.198)  36.409 ms
    11  42.120.239.165 (42.120.239.165)  33.903 ms 140.205.24.29 (140.205.24.29)  34.767 ms *
    12  * * *
    13  * * *
    14  * * *
    15  * * *
    16  * * *
    17  * * *
    18  * * *
    19  * * *
    20  * * *
    21  * * *
    22  *^C

这里根本不应该会去10.10.11.254 , 而应该去 10.10.11.1 呀.
比如下面 去 qq.com 

::

    [root@nginx nginx]# traceroute 120.25.204.216
    traceroute to 120.25.204.216 (120.25.204.216), 30 hops max, 60 byte packets
    1  gateway (10.10.11.1)  1.040 ms  1.132 ms  1.329 ms
    2  10.10.0.5 (10.10.0.5)  0.499 ms  0.759 ms  1.035 ms
    3  202.104.136.193 (202.104.136.193)  8.310 ms  8.541 ms  8.535 ms
    4  10.1.100.69 (10.1.100.69)  2.580 ms  2.582 ms  2.814 ms
    5  10.1.100.26 (10.1.100.26)  3.597 ms  4.061 ms  4.063 ms
    6  119.145.0.250 (119.145.0.250)  410.793 ms  410.482 ms  410.258 ms
    7  57.104.38.59.broad.fs.gd.dynamic.163data.com.cn (59.38.104.57)  1.954 ms  1.762 ms  2.331 ms
    8  117.176.37.59.broad.dg.gd.dynamic.163data.com.cn (59.37.176.117)  2.698 ms  2.699 ms *
    9  183.56.65.14 (183.56.65.14)  9.605 ms 183.56.65.18 (183.56.65.18)  7.583 ms 183.56.65.26 (183.56.65.26)  2.584 ms
    10  119.147.223.34 (119.147.223.34)  2.558 ms 119.147.220.50 (119.147.220.50)  3.246 ms 119.147.223.118 (119.147.223.118)  3.217 ms
    11  183.2.182.118 (183.2.182.118)  17.190 ms 183.2.182.130 (183.2.182.130)  3.851 ms 183.2.182.126 (183.2.182.126)  5.212 ms
    12  183.2.180.158 (183.2.180.158)  3.315 ms 183.2.180.34 (183.2.180.34)  4.321 ms 183.2.180.70 (183.2.180.70)  3.312 ms
    13  * * 42.120.242.222 (42.120.242.222)  5.922 ms
    14  119.38.215.5 (119.38.215.5)  7.730 ms *  7.128 ms
    15  * * *
    16  * * *
    17  120.25.204.216 (120.25.204.216)  6.684 ms !X  4.352 ms !X  4.528 ms !X
    [root@nginx nginx]# ping qq.com
    PING qq.com (14.17.32.211) 56(84) bytes of data.
    64 bytes from 14.17.32.211: icmp_seq=1 ttl=52 time=1.49 ms
    ^C
    --- qq.com ping statistics ---
    1 packets transmitted, 1 received, 0% packet loss, time 0ms
    rtt min/avg/max/mdev = 1.490/1.490/1.490/0.000 ms
    [root@nginx nginx]# traceroute 14.17.32.211
    traceroute to 14.17.32.211 (14.17.32.211), 30 hops max, 60 byte packets
    1  gateway (10.10.11.1)  1.081 ms  1.134 ms  1.322 ms
    2  10.10.0.5 (10.10.0.5)  0.298 ms  0.374 ms  0.449 ms
    3  202.104.136.193 (202.104.136.193)  12.955 ms  12.962 ms  12.944 ms
    4  10.1.100.97 (10.1.100.97)  1.944 ms  2.378 ms  2.370 ms
    5  10.1.100.26 (10.1.100.26)  2.391 ms  2.373 ms  2.531 ms
    6  119.145.0.250 (119.145.0.250)  1.834 ms  2.169 ms  2.357 ms
    7  61.104.38.59.broad.fs.gd.dynamic.163data.com.cn (59.38.104.61)  2.028 ms 57.104.38.59.broad.fs.gd.dynamic.163data.com.cn (59.38.104.57)  1.874 ms  1.695 ms
    8  117.176.37.59.broad.dg.gd.dynamic.163data.com.cn (59.37.176.117)  2.008 ms^C
    [root@nginx nginx]#

## 解决 问题

根据网络工程师回忆, 确实是在调整运管商网络的过程中, 曾经切换过 路由至 10.10.11.254 , 也就意味着, 确实曾经使用过这个路由.

那么就很有可能,在切换的过程中, 造成缓存了.(如在切换时, 116.62.200.151 发了请求过来, 而记住了之前的路由) 

* 现在route
* ip route flush cache
* traceroute 116.62.200.151


查看一下当下的route表

::

    [root@nginx jlch]# route
    Kernel IP routing table
    Destination     Gateway         Genmask         Flags Metric Ref    Use Iface
    default         gateway         0.0.0.0         UG    100    0        0 em1
    10.10.11.0      0.0.0.0         255.255.255.0   U     100    0        0 em1
    10.10.12.0      0.0.0.0         255.255.255.0   U     100    0        0 em2

清route 缓存

::

    [root@nginx jlch]# route flush cache
    Flushing `inet' routing table not supported
    Usage: inet_route [-vF] del {-host|-net} Target[/prefix] [gw Gw] [metric M] [[dev] If]
        inet_route [-vF] add {-host|-net} Target[/prefix] [gw Gw] [metric M]
                                [netmask N] [mss Mss] [window W] [irtt I]
                                [mod] [dyn] [reinstate] [[dev] If]
        inet_route [-vF] add {-host|-net} Target[/prefix] [metric M] reject
        inet_route [-FC] flush      NOT supported
    [root@nginx jlch]# ip route flush cache
    [root@nginx jlch]# route
    Kernel IP routing table
    Destination     Gateway         Genmask         Flags Metric Ref    Use Iface
    default         gateway         0.0.0.0         UG    100    0        0 em1
    10.10.11.0      0.0.0.0         255.255.255.0   U     100    0        0 em1
    10.10.12.0      0.0.0.0         255.255.255.0   U     100    0        0 em2

测试 qq.com

::

    [root@nginx jlch]# traceroute 14.17.32.211
    traceroute to 14.17.32.211 (14.17.32.211), 30 hops max, 60 byte packets
    1  gateway (10.10.11.1)  0.873 ms  0.956 ms  1.175 ms
    2  10.10.0.5 (10.10.0.5)  0.252 ms  0.312 ms  0.399 ms
    3  202.104.136.193 (202.104.136.193)  11.141 ms  11.134 ms  11.122 ms
    4  10.1.100.97 (10.1.100.97)  2.182 ms  9.326 ms  19.228 ms
    5  10.1.100.26 (10.1.100.26)  1.900 ms  2.424 ms  2.424 ms
    6  119.145.0.250 (119.145.0.250)  1.828 ms  1.975 ms  2.460 ms
    7  57.104.38.59.broad.fs.gd.dynamic.163data.com.cn (59.38.104.57)  1.570 ms 61.104.38.59.broad.fs.gd.dynamic.163data.com.cn (59.38.104.61)  2.006 ms 57.104.38.59.broad.fs.gd.dynamic.163data.com.cn (59.38.104.57)  1.667 ms^C
    
traceroute 116.62.200.151
    
::

    [root@nginx jlch]# traceroute 116.62.200.151
    traceroute to 116.62.200.151 (116.62.200.151), 30 hops max, 60 byte packets
    1  gateway (10.10.11.1)  0.832 ms  0.960 ms  1.177 ms
    2  10.10.0.5 (10.10.0.5)  0.441 ms  0.724 ms  1.009 ms
    3  202.104.136.193 (202.104.136.193)  6.082 ms  6.298 ms  6.289 ms
    4  10.1.100.97 (10.1.100.97)  3.040 ms  3.412 ms  3.389 ms
    5  10.1.100.26 (10.1.100.26)  3.700 ms  4.398 ms  4.611 ms
    6  119.145.0.250 (119.145.0.250)  3.331 ms  3.316 ms  3.748 ms
    7  57.104.38.59.broad.fs.gd.dynamic.163data.com.cn (59.38.104.57)  1.831 ms 61.104.38.59.broad.fs.gd.dynamic.163data.com.cn (59.38.104.61)  2.248 ms  2.602 ms^C
    [root@nginx jlch]#  

好了, 这一下, 终于可以让 116.62.200.151 这个来源的IP访问正常了. 