
========================================================
mysql could not be resolved: Name or service not known
========================================================

开发人员说,数据库连接不上...

第一时间, tail -f 打开日志, 发现

错误日志有类似警告：

::

    1.120119 16:26:04 [Warning] IP address '192.168.1.10' could not be resolved: Name or service not known
    2.120119 16:26:04 [Warning] IP address '192.168.1.14' could not be resolved: Name or service not known
    3.120119 16:26:04 [Warning] IP address '192.168.1.17' could not be resolved: Name or service not known

通过show processlist发现大量类似如下的连接：

::

    1.|592|unauthenticated user|192.168.1.10:35320|NULL|Connect| |login|NULL|
    2.|593|unauthenticated user|192.168.1.14:35321|NULL|Connect| |login|NULL|
    3.|594|unauthenticated user|192.168.1.17:35322|NULL|Connect| |login|NULL|

先是参考 http://www.jb51.net/article/70893.htm 

skip-name-resolve 参数的作用：不再进行反解析（ip不反解成域名），这样可以加快数据库的反应时间

::

    [mysqld] 
    skip-name-resolve

设置后, 重启, 没有生效.

然后查看系统

top

发现, 负载很高呀...

回忆了一下, 是刚刚之前, 做了一个大数据的 mv 操作.

然后, 我把这个进程 kill 后, 再重启数据库, 就一切正常了.

我的天啊, 原来, 数据库这台机器, 的负载不能太高呀.....

