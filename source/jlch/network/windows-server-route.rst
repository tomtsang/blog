=======================================
蛟龙-windows-server加路由
=======================================

为 windows-server 加路由, 加无线段, 方便陈近南从无线(192.168.33.***)登陆windows-server(192.168.31.242)

env
=======================================

- IP: 192.168.31.242, 10.10.15.242
- OS: windows server
- 无线IP段: 192.168.33.0/24
- 测试IP: 192.168.33.220

step
=======================================

打开cmd

查看一下现有route

::

    route print


先给某一个测试 无线IP(这里为192.168.33.220)设置.
因为这里是双网卡机器, 所以这里最后应该写成 192.168.31.1 而不是本机IP 192.168.31.242

::

    route -p add 192.168.33.220 mask 255.255.255.255 192.168.31.1

然后相互ping一下(也可以telnet 192.168.31.242 3389)
成功.

删除掉刚刚的测试

::

    route delete 192.168.33.220

正式加路由

::

    route -p add 192.168.33.0 mask 255.255.255.0 192.168.31.1

ref
=======================

- `windows下添加路由 <https://blog.csdn.net/wangzhen209/article/details/77748107>`_