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
