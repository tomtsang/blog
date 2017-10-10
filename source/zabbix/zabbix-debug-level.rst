zabbix 配置 debug level
-----------------------

在 ``/etc/zabbix/zabbix_agentd.conf`` 中设置 ``DebugLevel=3``

::

    [root@mariadb-node-0 ~]# cat /etc/zabbix/zabbix_agentd.conf | grep Debug
    ### Option: DebugLevel
    DebugLevel=3
    [root@mariadb-node-0 ~]# 

``DebugLevel=5`` 则输出下面这样的日志：

::

    [root@mariadb-node-0 ~]# tail -f /var/log/zabbix/zabbix_agentd.log 
    148519:20171010:103802.537 __zbx_zbx_setproctitle() title:'collector [processing data]'
    148519:20171010:103802.537 In update_cpustats()
    148519:20171010:103802.538 End of update_cpustats()
    148519:20171010:103802.538 __zbx_zbx_setproctitle() title:'collector [idle 1 sec]'
    148520:20171010:103802.563 __zbx_zbx_setproctitle() title:'listener #1 [processing request]'
    148520:20171010:103802.563 Requested [disk.rawait[sda]]
    148520:20171010:103802.563 In zbx_popen() command:'iostat -dx | sed -n '4p' | awk '{print $11}''
    148520:20171010:103802.564 End of zbx_popen():7
    148997:20171010:103802.564 zbx_popen(): executing script
    148520:20171010:103802.571 In zbx_waitpid()
    148520:20171010:103802.571 zbx_waitpid() exited, status:0
    148520:20171010:103802.571 End of zbx_waitpid():148997
    148520:20171010:103802.571 EXECUTE_STR() command:'iostat -dx | sed -n '4p' | awk '{print $11}'' len:4 cmd_result:'3.18'
    148520:20171010:103802.571 Sending back [3.18]
    148520:20171010:103802.571 __zbx_zbx_setproctitle() title:'listener #1 [waiting for connection]'
    148525:20171010:103802.581 In send_buffer() host:'zabbix_server' port:10051 entries:0/100
    148525:20171010:103802.581 End of send_buffer():SUCCEED
    148525:20171010:103802.581 __zbx_zbx_setproctitle() title:'active checks #1 [idle 1 sec]'
    ^C^C

``DebugLevel=3`` 则输出下面这样的日志：

::

    [root@mariadb-node-0 ~]# tail -f /var/log/zabbix/zabbix_agentd.log 
    150001:20171010:103908.063 IPv6 support:          YES
    150001:20171010:103908.063 TLS support:           YES
    150001:20171010:103908.063 **************************
    150001:20171010:103908.063 using configuration file: /etc/zabbix/zabbix_agentd.conf
    150001:20171010:103908.064 agent #0 started [main process]
    150002:20171010:103908.065 agent #1 started [collector]
    150003:20171010:103908.065 agent #2 started [listener #1]
    150004:20171010:103908.065 agent #3 started [listener #2]
    150005:20171010:103908.066 agent #4 started [listener #3]
    150006:20171010:103908.066 agent #5 started [active checks #1]
    ^C
    [root@mariadb-node-0 ~]# 