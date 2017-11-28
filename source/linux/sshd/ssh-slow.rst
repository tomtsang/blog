===========================================
ssh 连接 登录 很慢
===========================================

env
===========================================

10.10.15.181 => 10.10.13.12

问题
===========================================


10.10.12.13, 生成信号（/home/jlch/OpenX/OpenQuote2/kline_signaller.js ）的时候，要连接 Mongo 10.10.13.12，但是，连接明显很慢，导致先连接 redis, 然后产生的信号没有连接上mongo并保存。

然后，发现，ssh 10.10.13.12, 明显太慢了，大约要 10s.

解决
===========================================

登录 10.10.13.12 ，修改 10.10.13.12 的 /etc/ssh/sshd_config 文件

主要是  GSSAPIAuthentication  和 UseDNS 参数修改成 no

::

    [jlch@mongodb1 ~]$ sudo cat /etc/ssh/sshd_config | grep GSSAPIAuthentication
    #GSSAPIAuthentication yes
    GSSAPIAuthentication no
    [jlch@mongodb1 ~]$ sudo cat /etc/ssh/sshd_config | grep UseDNS
    #UseDNS yes
    UseDNS no
    [jlch@mongodb1 ~]$ sudo systemctl restart sshd

然后再次登录，则很快了。

