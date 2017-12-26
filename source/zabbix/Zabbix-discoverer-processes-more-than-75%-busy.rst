=====================================================
Zabbix discoverer processes more than 75% busy
=====================================================



https://www.kaijia.me/2014/01/zabbix-poller-processes-more-than-75-busy-alert-issue-solved/


env 
=====================================================

10.10.11.20 出现了 

Zabbix discoverer processes more than 75% busy

错误

step
=====================================================

编辑Zabbix Server的配置文件/etc/zabbix/zabbix_server.conf，找到配置StartPollers的段落：

::

    ### Option: StartPollers
    #       Number of pre-forked instances of pollers.
    #
    # Mandatory: no
    # Range: 0-1000
    # Default:
    # StartPollers=5

取消StartPollers=一行的注释或者直接在后面增加：

::

    StartPollers=10

因为我这里本来的就是10, 所以我直接修改成20, 这样子, 报错就消失了

::

    StartPollers=10

第2天
======================================================

第2天, 这个问题又来了, 所以我只好选择继续看这个文章

选择每天重启zabbix_server吧.

重启Zabbix。当然另外一种从整体上降低Zabbix服务器负载的方法就是定期重启Zabbix，这种方法可以用Cron实现，运行：

crontab -e 

这个计划会每天自动重启Zabbix服务以结束僵尸进程并清理内存等。目前Kaijia这样配置Zabbix后还没有再次遇到过“Zabbix poller processes more than 75% busy”的问题。


所以我只好这样了.. 等明天接着观察吧.

::

    [root@qlw jlch]# crontab -l
    SHELL=/bin/bash
    PATH=/usr/local/bin:/usr/bin
    #m    h    dom    mon    dow    user command
    10      2       *       *       *       systemctl restart zabbix_server.service > /dev/null 2>&1
    [root@qlw jlch]#

