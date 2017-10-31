======================
log-bin
======================

配置 log-bin
================

在 数据库 中配置添加 log-bin=mysql.server 完成后 ，重启， 

::

    [root@check my.cnf.d]# ls /data/mariadb/mysql/
    aria_log.00000001  cnpmjs_test  ibdata1      ib_logfile1  ib_logfile3  ib_logfile5  openquote           product  q_station  slow_query_log.log  stock20151029    stock_sqlserver_df  sync  testhufei  zabbix
    aria_log_control   haitong      ib_logfile0  ib_logfile2  ib_logfile4  mysql        performance_schema  program  rates      stock               stock_quotation  stock_test          test  xiaozhao   zhongtai2
    [root@check my.cnf.d]# sudo systemctl start mariadb.service 
    [root@check my.cnf.d]# sudo systemctl status mariadb.service 
    ● mariadb.service - MariaDB database server
    Loaded: loaded (/usr/lib/systemd/system/mariadb.service; enabled; vendor preset: disabled)
    Active: active (running) since 二 2017-10-31 11:48:11 CST; 5s ago
    Process: 15315 ExecStartPost=/usr/libexec/mariadb-wait-ready $MAINPID (code=exited, status=0/SUCCESS)
    Process: 15269 ExecStartPre=/usr/libexec/mariadb-prepare-db-dir %n (code=exited, status=0/SUCCESS)
    Main PID: 15314 (mysqld_safe)
    Memory: 854.0M
    CGroup: /system.slice/mariadb.service
            ├─15314 /bin/sh /usr/bin/mysqld_safe --basedir=/usr
            └─15825 /usr/libexec/mysqld --basedir=/usr --datadir=/data/mariadb/mysql --plugin-dir=/usr/lib64/mysql/plugin --log-error=/var/log/mariadb/mariadb.log --pid-file=/var/run/mariadb/mariadb.pid --socket=/var/lib/mysql/mysql.sock

    10月 31 11:48:08 check systemd[1]: Starting MariaDB database server...
    10月 31 11:48:08 check mysqld_safe[15314]: 171031 11:48:08 mysqld_safe Logging to '/var/log/mariadb/mariadb.log'.
    10月 31 11:48:08 check mysqld_safe[15314]: 171031 11:48:08 mysqld_safe Starting mysqld daemon with databases from /data/mariadb/mysql
    10月 31 11:48:11 check systemd[1]: Started MariaDB database server.
    [root@check my.cnf.d]# ls /data/mariadb/mysql/
    aria_log.00000001  cnpmjs_test  ibdata1      ib_logfile1  ib_logfile3  ib_logfile5  mysql.000001  openquote           product  q_station  slow_query_log.log  stock20151029    stock_sqlserver_df  sync  testhufei  zabbix
    aria_log_control   haitong      ib_logfile0  ib_logfile2  ib_logfile4  mysql        mysql.index   performance_schema  program  rates      stock               stock_quotation  stock_test          test  xiaozhao   zhongtai2
    [root@check my.cnf.d]# 

如上，在 mysql 文件（/data/mariadb/mysql/）中，会新增 mysql.index， mysql.000001 这2个文件。

删除没必要的 log-bin 文件
============================

在数据库里执行

::

    show binary logs;
    PURGE MASTER LOGS to 'mysql.00000x';

