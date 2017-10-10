
daemon-reload
=============

每一次修改完了 ``*.service`` , 都要进行\ ``systemctl daemon-reload``

::

    [root@mariadb-node-0 zabbix]# vi /usr/lib/systemd/system/zabbix-agent.service 
    [root@mariadb-node-0 zabbix]# systemctl daemon-reload 
    [root@mariadb-node-0 zabbix]# systemctl stop zabbix-agent.service 
    [root@mariadb-node-0 zabbix]# systemctl start zabbix-agent.service 
    [root@mariadb-node-0 zabbix]# systemctl status zabbix-agent.service 
    ● zabbix-agent.service - Zabbix Agent
       Loaded: loaded (/usr/lib/systemd/system/zabbix-agent.service; disabled; vendor preset: disabled)
       Active: active (running) since 二 2017-10-10 10:39:08 CST; 2s ago
      Process: 149998 ExecStart=/usr/sbin/zabbix_agentd -c $CONFFILE (code=exited, status=0/SUCCESS)
     Main PID: 150001 (zabbix_agentd)
       Memory: 2.5M
       CGroup: /system.slice/zabbix-agent.service
               ├─150001 /usr/sbin/zabbix_agentd -c /etc/zabbix/zabbix_agentd.conf
               ├─150002 /usr/sbin/zabbix_agentd: collector [idle 1 sec]
               ├─150003 /usr/sbin/zabbix_agentd: listener #1 [waiting for connection]
               ├─150004 /usr/sbin/zabbix_agentd: listener #2 [waiting for connection]
               ├─150005 /usr/sbin/zabbix_agentd: listener #3 [waiting for connection]
               └─150006 /usr/sbin/zabbix_agentd: active checks #1 [idle 1 sec]

    10月 10 10:39:08 mariadb-node-0 systemd[1]: Starting Zabbix Agent...
    10月 10 10:39:08 mariadb-node-0 systemd[1]: PID file /run/zabbix/zabbix_agentd.pid not readable (yet?) after start.
    10月 10 10:39:08 mariadb-node-0 systemd[1]: zabbix-agent.service: Supervising process 150001 which is not our child. We'll...exits.
    10月 10 10:39:08 mariadb-node-0 systemd[1]: Started Zabbix Agent.
    10月 10 10:39:09 mariadb-node-0 python[150008]: SELinux is preventing /usr/sbin/zabbix_agentd from using the setrlimit acc...ocess.
                                                     
                                                     *****  Plugin catchall (100. confidence) suggests   **************************...
    Hint: Some lines were ellipsized, use -l to show in full.
    [root@mariadb-node-0 zabbix]# 

