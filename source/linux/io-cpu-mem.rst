=========================================
io-cpu-memory 
=========================================

关于 io-cpu-memory 的查询

::

    [tom@check ~]$ iostat 
    Linux 3.10.0-327.4.4.el7.x86_64 (check) 	2017年12月11日 	_x86_64_	(8 CPU)

    avg-cpu:  %user   %nice %system %iowait  %steal   %idle
            3.82    0.00    0.32    0.94    0.00   94.92

    Device:            tps    kB_read/s    kB_wrtn/s    kB_read    kB_wrtn
    sda              49.91      1062.33      2048.76 9479441715 18281532382
    dm-0              1.51         3.60        77.51   32160568  691671407
    dm-1              1.60         3.17         3.24   28282128   28937428
    dm-2              0.61         3.89         8.54   34709518   76238588
    dm-3             48.04      1051.67      1959.46 9384260398 17484682850

    [tom@check ~]$ 
    [tom@check ~]$ vmstat 1
    procs -----------memory---------- ---swap-- -----io---- -system-- ------cpu-----
    r  b   swpd   free   buff  cache   si   so    bi    bo   in   cs us sy id wa st
    1  1 1796304 277276     12 8753928    0    0   133   256    0    1  4  0 95  1  0
    1  2 1796300 276716     12 8755312   16    0   720 26215 2242 2412 15  3 81  2  0
    1  1 1796296 275296     12 8756816    0    0     0 22747 1650 1171 12  1 86  1  0
    1  0 1796296 273772     12 8760704    4    0  1716 19107 1643 1915  9  1 85  4  0
    1  0 1796296 265688     12 8768892    0    0  6432 13806 1924 2063  9  1 86  5  0
    1  0 1796296 264920     12 8769968   20    0    20  2041 1640 1026 14  0 85  0  0
    [tom@check ~]$ top
    top - 17:18:46 up 103 days,  6:38, 10 users,  load average: 1.04, 0.93, 0.86
    Tasks: 393 total,   1 running, 392 sleeping,   0 stopped,   0 zombie
    %Cpu(s): 10.1 us,  0.8 sy,  0.0 ni, 85.3 id,  3.8 wa,  0.0 hi,  0.0 si,  0.0 st
    KiB Mem : 16216960 total,   261732 free,  7262872 used,  8692356 buff/cache
    KiB Swap: 16773116 total, 14976812 free,  1796304 used.  8445804 avail Mem 

    PID USER      PR  NI    VIRT    RES    SHR S  %CPU %MEM     TIME+ COMMAND                                                                                                                                                                                                  
    15825 mysql     20   0 9864996 4.908g   7512 S  81.9 31.7  17954:43 mysqld                                                                                                                                                                                                   
    898 root      20   0    4372    204     64 S   1.1  0.0 529:06.74 rngd                                                                                                                                                                                                     
    37715 rabbitmq  20   0 6059776  49684   2952 S   1.1  0.3 596:35.47 beam.smp                                                                                                                                                                                                 
    65 root      20   0       0      0      0 S   0.4  0.0  24:16.56 rcuos/6                                                                                                                                                                                                  
    819 root      20   0       0      0      0 S   0.4  0.0  30:43.70 xfsaild/dm-3                                                                                                                                                                                             
    1646 redis     20   0  150880   2908    800 S   0.4  0.0 111:13.10 redis-server                                                                                                                                                                                             
    2862 tom       20   0  146412   2404   1428 R   0.4  0.0   0:00.23 top                                                                                                                                                                                                      
    4247 tom       20   0  934376  19612   4796 S   0.4  0.1 306:20.27 PM2 v1.1.3: God                                                                                                                                                                                          
    4378 jlch      20   0  938180  23904   4876 S   0.4  0.1 437:56.05 PM2 v1.1.3: God                                                                                                                                                                                          
    31674 tom       20   0  938164  22372   4748 S   0.4  0.1  42:37.57 node /home/tom/                                                                                                                                                                                          
    45485 jenkins   20   0 9777.8m 1.186g   8236 S   0.4  7.7 242:55.06 java                                                                                                                                                                                                     
    46382 zabbix    20   0   84736   1252   1100 S   0.4  0.0  25:59.45 zabbix_agentd                                                                                                                                                                                            
        1 root      20   0  189620   3388   1560 S   0.0  0.0   8:59.88 systemd                                                                                                                                                                                                  
        2 root      20   0       0      0      0 S   0.0  0.0   0:00.70 kthreadd                                                                                                                                                                                                 
        3 root      20   0       0      0      0 S   0.0  0.0   4:51.05 ksoftirqd/0                                                                                                                                                                                              
        5 root       0 -20       0      0      0 S   0.0  0.0   0:00.00 kworker/0:0H                                                                                                                                                                                             
        8 root      rt   0       0      0      0 S   0.0  0.0   0:13.08 migration/0                                                                                                                                                                                              
        9 root      20   0       0      0      0 S   0.0  0.0   0:00.00 rcu_bh        