
zabbix-debug-1
--------------


在 10.10.12.17 这台机器上，zabbix-agent 服务报错了

::

	[root@mariadb-node-0 zabbix]# systemctl start zabbix-agent.service 
	Job for zabbix-agent.service failed because a configured resource limit was exceeded. See "systemctl status zabbix-agent.service" and "journalctl -xe" for details.
	[root@mariadb-node-0 zabbix]# journalctl -xe
	10月 10 10:04:08 mariadb-node-0 systemd[1]: Starting Zabbix Agent...
	-- Subject: Unit zabbix-agent.service has begun start-up
	-- Defined-By: systemd
	-- Support: http://lists.freedesktop.org/mailman/listinfo/systemd-devel
	-- 
	-- Unit zabbix-agent.service has begun starting up.
	10月 10 10:04:08 mariadb-node-0 systemd[1]: PID file /var/run/zabbix/zabbix_agentd.pid not readable (yet?) after start.
	10月 10 10:04:08 mariadb-node-0 systemd[1]: zabbix-agent.service: Supervising process 124777 which is not our child. We'll most like
	10月 10 10:04:08 mariadb-node-0 systemd[1]: zabbix-agent.service: main process exited, code=exited, status=1/FAILURE
	10月 10 10:04:08 mariadb-node-0 kill[124779]: kill: 向 124777 发送信号失败: 没有那个进程
	10月 10 10:04:08 mariadb-node-0 systemd[1]: zabbix-agent.service: control process exited, code=exited status=1
	10月 10 10:04:08 mariadb-node-0 systemd[1]: Failed to start Zabbix Agent.
	-- Subject: Unit zabbix-agent.service has failed
	-- Defined-By: systemd
	-- Support: http://lists.freedesktop.org/mailman/listinfo/systemd-devel
	-- 
	-- Unit zabbix-agent.service has failed.
	-- 
	-- The result is failed.
	10月 10 10:04:08 mariadb-node-0 systemd[1]: Unit zabbix-agent.service entered failed state.
	10月 10 10:04:08 mariadb-node-0 systemd[1]: zabbix-agent.service failed.
	10月 10 10:04:18 mariadb-node-0 systemd[1]: zabbix-agent.service holdoff time over, scheduling restart.
	10月 10 10:04:18 mariadb-node-0 systemd[1]: Starting Zabbix Agent...
	-- Subject: Unit zabbix-agent.service has begun start-up
	-- Defined-By: systemd
	-- Support: http://lists.freedesktop.org/mailman/listinfo/systemd-devel
	-- 
	-- Unit zabbix-agent.service has begun starting up.
	10月 10 10:04:18 mariadb-node-0 systemd[1]: PID file /var/run/zabbix/zabbix_agentd.pid not readable (yet?) after start.
	10月 10 10:04:18 mariadb-node-0 systemd[1]: zabbix-agent.service: Supervising process 125011 which is not our child. We'll most like
	10月 10 10:04:18 mariadb-node-0 systemd[1]: Started Zabbix Agent.
	-- Subject: Unit zabbix-agent.service has finished start-up
	-- Defined-By: systemd
	-- Support: http://lists.freedesktop.org/mailman/listinfo/systemd-devel
	-- 
	-- Unit zabbix-agent.service has finished starting up.
	-- 
	-- The start-up result is done.
	10月 10 10:04:18 mariadb-node-0 systemd[1]: zabbix-agent.service: main process exited, code=exited, status=1/FAILURE
	10月 10 10:04:18 mariadb-node-0 kill[125014]: 用法：
	10月 10 10:04:18 mariadb-node-0 kill[125014]: kill [选项] <pid|名称> [...]
	10月 10 10:04:18 mariadb-node-0 kill[125014]: 选项：
	10月 10 10:04:18 mariadb-node-0 kill[125014]: -a, --all              不限于只对和当前进程的用户 id 相同的进程进行
	10月 10 10:04:18 mariadb-node-0 kill[125014]: 名称-进程id 转换
	10月 10 10:04:18 mariadb-node-0 kill[125014]: -s, --signal <信号>     发送指定的信号
	10月 10 10:04:18 mariadb-node-0 kill[125014]: -q, --queue <信号>      使用 sigqueue(2) 代替 kill(2)
	10月 10 10:04:18 mariadb-node-0 systemd[1]: zabbix-agent.service: control process exited, code=exited status=1
	10月 10 10:04:18 mariadb-node-0 kill[125014]: -p, --pid              打印 pid 而不向它们发送信号
	10月 10 10:04:18 mariadb-node-0 kill[125014]: -l, --list [=<信号>]   列出信号名，或将一个信号转换为名称
	10月 10 10:04:18 mariadb-node-0 kill[125014]: -L, --table            列出信号名和数值
	10月 10 10:04:18 mariadb-node-0 kill[125014]: -h, --help     显示此帮助并退出
	10月 10 10:04:18 mariadb-node-0 kill[125014]: -V, --version  输出版本信息并退出
	10月 10 10:04:18 mariadb-node-0 kill[125014]: 更多信息请参阅 kill(1)。
	10月 10 10:04:18 mariadb-node-0 systemd[1]: Unit zabbix-agent.service entered failed state.
	10月 10 10:04:18 mariadb-node-0 systemd[1]: zabbix-agent.service failed.
	[root@mariadb-node-0 zabbix]# 

查进程，查日志

::

	[root@mariadb-node-0 zabbix]# ps -ef | grep zabbix
	root     132576 131957  0 10:15 pts/2    00:00:00 grep --color=auto zabbix
	[root@mariadb-node-0 zabbix]# tail -f /var/log/zabbix/zabbix_agentd.log 
	zabbix_agentd [131649]: cannot open log: cannot create semaphore set: [28] No space left on device
	zabbix_agentd [131712]: cannot open log: cannot create semaphore set: [28] No space left on device
	zabbix_agentd [131939]: cannot open log: cannot create semaphore set: [28] No space left on device
	zabbix_agentd [131948]: cannot open log: cannot create semaphore set: [28] No space left on device
	zabbix_agentd [132150]: cannot open log: cannot create semaphore set: [28] No space left on device
	zabbix_agentd [132279]: cannot open log: cannot create semaphore set: [28] No space left on device
	zabbix_agentd [132288]: cannot open log: cannot create semaphore set: [28] No space left on device
	zabbix_agentd [132456]: cannot open log: cannot create semaphore set: [28] No space left on device
	zabbix_agentd [132579]: cannot open log: cannot create semaphore set: [28] No space left on device
	zabbix_agentd [132586]: cannot open log: cannot create semaphore set: [28] No space left on device
	zabbix_agentd [132875]: cannot open log: cannot create semaphore set: [28] No space left on device
	^C
	[root@mariadb-node-0 zabbix]# tail -f /var/log/messages
	Oct 10 10:15:48 mariadb-node-0 kill: -q, --queue <信号>      使用 sigqueue(2) 代替 kill(2)
	Oct 10 10:15:48 mariadb-node-0 systemd: zabbix-agent.service: control process exited, code=exited status=1
	Oct 10 10:15:48 mariadb-node-0 kill: -p, --pid              打印 pid 而不向它们发送信号
	Oct 10 10:15:48 mariadb-node-0 kill: -l, --list [=<信号>]   列出信号名，或将一个信号转换为名称
	Oct 10 10:15:48 mariadb-node-0 kill: -L, --table            列出信号名和数值
	Oct 10 10:15:48 mariadb-node-0 kill: -h, --help     显示此帮助并退出
	Oct 10 10:15:48 mariadb-node-0 kill: -V, --version  输出版本信息并退出
	Oct 10 10:15:48 mariadb-node-0 kill: 更多信息请参阅 kill(1)。
	Oct 10 10:15:48 mariadb-node-0 systemd: Unit zabbix-agent.service entered failed state.
	Oct 10 10:15:48 mariadb-node-0 systemd: zabbix-agent.service failed.
	Oct 10 10:15:59 mariadb-node-0 systemd: zabbix-agent.service holdoff time over, scheduling restart.
	Oct 10 10:15:59 mariadb-node-0 systemd: Starting Zabbix Agent...
	Oct 10 10:15:59 mariadb-node-0 systemd: PID file /run/zabbix/zabbix_agentd.pid not readable (yet?) after start.
	Oct 10 10:15:59 mariadb-node-0 systemd: zabbix-agent.service: Supervising process 132893 which is not our child. We'll most likely not notice when it exits.
	Oct 10 10:15:59 mariadb-node-0 systemd: Started Zabbix Agent.
	Oct 10 10:15:59 mariadb-node-0 systemd: zabbix-agent.service: main process exited, code=exited, status=1/FAILURE
	Oct 10 10:15:59 mariadb-node-0 kill: 用法：
	Oct 10 10:15:59 mariadb-node-0 kill: kill [选项] <pid|名称> [...]
	Oct 10 10:15:59 mariadb-node-0 kill: 选项：
	Oct 10 10:15:59 mariadb-node-0 kill: -a, --all              不限于只对和当前进程的用户 id 相同的进程进行
	Oct 10 10:15:59 mariadb-node-0 kill: 名称-进程id 转换
	Oct 10 10:15:59 mariadb-node-0 kill: -s, --signal <信号>     发送指定的信号
	Oct 10 10:15:59 mariadb-node-0 kill: -q, --queue <信号>      使用 sigqueue(2) 代替 kill(2)
	Oct 10 10:15:59 mariadb-node-0 kill: -p, --pid              打印 pid 而不向它们发送信号
	Oct 10 10:15:59 mariadb-node-0 kill: -l, --list [=<信号>]   列出信号名，或将一个信号转换为名称
	Oct 10 10:15:59 mariadb-node-0 kill: -L, --table            列出信号名和数值
	Oct 10 10:15:59 mariadb-node-0 kill: -h, --help     显示此帮助并退出
	Oct 10 10:15:59 mariadb-node-0 kill: -V, --version  输出版本信息并退出
	Oct 10 10:15:59 mariadb-node-0 kill: 更多信息请参阅 kill(1)。
	Oct 10 10:15:59 mariadb-node-0 systemd: zabbix-agent.service: control process exited, code=exited status=1
	Oct 10 10:15:59 mariadb-node-0 systemd: Unit zabbix-agent.service entered failed state.
	Oct 10 10:15:59 mariadb-node-0 systemd: zabbix-agent.service failed.
	^C
	[root@mariadb-node-0 zabbix]# 

| 出现这种错误，我们要去查一下，到底为什么会
| ``zabbix_agentd [132875]: cannot open log: cannot create semaphore set: [28] No space left on device``
| 和
| ``Oct 10 10:15:59 mariadb-node-0 systemd: PID file /run/zabbix/zabbix_agentd.pid not readable (yet?) after start.``

#. 查一下硬盘

   df -h

发现是OK的。

#. 检查，是不是真的 ``No space left on device``

我们用root 用户，在 ``/run/zabbix/`` 下 创建文件，

::

    sudo -u zabbix echo 'hello' > /run/zabbix/zabbix_agentd.pid  ## 这里的命令是 sudo -u zabbix, 不是 sudo su - zabbix 
    cat /run/zabbix/zabbix_agentd.pid
    cd /run/zabbix/
    ls 
    ls -la ## 过了一会，发现消失了。
    sudo -u root echo 'hello' > /run/zabbix/zabbix_agentd.pid  ## 切成 root
    cat zabbix_agentd.pid 
    ls 
    ls ## ## 过了一会，发现消失了。 
    sudo -u root echo 'hello' > /run/zabbix/a.txt  ## 切成 root
    cat a.txt
    ls 
    ls ## ## 过了一会，发现消失了。

到底是什么自动删除了它呢？

#. google了一下，找到，原来还真的是内存文件系统，具体网址找不到了。

命令 ipcs 查看
命令 ipcrm 删除

::

	[jlch@mariadb-node-0 zabbix]$ which ipcs
	/usr/bin/ipcs
	[jlch@mariadb-node-0 zabbix]$
	[root@mariadb-node-0 zabbix]# ipcs

	--------- 消息队列 -----------
	键        msqid      拥有者  权限     已用字节数 消息      

	------------ 共享内存段 --------------
	键        shmid      拥有者  权限     字节     nattch     状态      
	0x00000000 65536      tom        600        4194304    2          目标       
	0x00000000 163841     tom        600        4194304    2          目标       
	0x00000000 262146     tom        600        1048576    2          目标       
	0x00000000 360451     tom        600        4194304    2          目标       
	0x00000000 458756     tom        600        393216     2          目标       

	--------- 信号量数组 -----------
	键        semid      拥有者  权限     nsems     
	0x00000000 98304      zabbix     600        13        
	0x00000000 131073     zabbix     600        13        
	0x00000000 163842     zabbix     600        13        
	0x00000000 196611     zabbix     600        13        
	0x00000000 229380     zabbix     600        13        
	0x00000000 262149     zabbix     600        13        
	0x00000000 294918     zabbix     600        13        
	0x00000000 327687     zabbix     600        13        
	0x00000000 360456     zabbix     600        13        
	0x00000000 393225     zabbix     600        13        
	0x00000000 425994     zabbix     600        13        
	0x00000000 458763     zabbix     600        13        
	0x00000000 491532     zabbix     600        13        
	0x00000000 524301     zabbix     600        13        
	0x00000000 557070     zabbix     600        13        
	0x00000000 589839     zabbix     600        13        
	0x00000000 622608     zabbix     600        13        
	0x00000000 655377     zabbix     600        13        
	0x00000000 688146     zabbix     600        13        
	0x00000000 720915     zabbix     600        13        
	0x00000000 753684     zabbix     600        13        
	0x00000000 786453     zabbix     600        13        
	0x00000000 819222     zabbix     600        13        
	0x00000000 851991     zabbix     600        13        
	0x00000000 884760     zabbix     600        13        
	0x00000000 917529     zabbix     600        13        
	0x00000000 950298     zabbix     600        13        
	0x00000000 983067     zabbix     600        13        
	0x00000000 1015836    zabbix     600        13        
	0x00000000 1048605    zabbix     600        13        
	0x00000000 1081374    zabbix     600        13        
	0x00000000 1114143    zabbix     600        13        
	0x00000000 1146912    zabbix     600        13        
	0x00000000 1179681    zabbix     600        13        
	0x00000000 1212450    zabbix     600        13        
	0x00000000 1245219    zabbix     600        13        
	0x00000000 1277988    zabbix     600        13        
	0x00000000 1310757    zabbix     600        13        
	0x00000000 1343526    zabbix     600        13        
	0x00000000 1376295    zabbix     600        13        
	0x00000000 1409064    zabbix     600        13        
	0x00000000 1441833    zabbix     600        13        
	0x00000000 1474602    zabbix     600        13        
	0x00000000 1507371    zabbix     600        13        
	0x00000000 1540140    zabbix     600        13        
	0x00000000 1572909    zabbix     600        13        
	0x00000000 1605678    zabbix     600        13        
	0x00000000 1638447    zabbix     600        13        
	0x00000000 1671216    zabbix     600        13        
	0x00000000 1703985    zabbix     600        13        
	0x00000000 1736754    zabbix     600        13        
	0x00000000 1769523    zabbix     600        13        
	0x00000000 1802292    zabbix     600        13        
	0x00000000 1835061    zabbix     600        13        
	0x00000000 1867830    zabbix     600        13        
	0x00000000 1900599    zabbix     600        13        
	0x00000000 1933368    zabbix     600        13        
	0x00000000 1966137    zabbix     600        13        
	0x00000000 1998906    zabbix     600        13        
	0x00000000 2031675    zabbix     600        13        
	0x00000000 2064444    zabbix     600        13        
	0x00000000 2097213    zabbix     600        13        
	0x00000000 2129982    zabbix     600        13        
	0x00000000 2162751    zabbix     600        13        
	0x00000000 2195520    zabbix     600        13        
	0x00000000 2228289    zabbix     600        13        
	0x00000000 2261058    zabbix     600        13        
	0x00000000 2293827    zabbix     600        13        
	0x00000000 2326596    zabbix     600        13        
	0x00000000 2359365    zabbix     600        13        
	0x00000000 2392134    zabbix     600        13        
	0x00000000 2424903    zabbix     600        13        
	0x00000000 2457672    zabbix     600        13        
	0x00000000 2490441    zabbix     600        13        
	0x00000000 2523210    zabbix     600        13        
	0x00000000 2555979    zabbix     600        13        
	0x00000000 2588748    zabbix     600        13        
	0x00000000 2621517    zabbix     600        13        
	0x00000000 2654286    zabbix     600        13        
	0x00000000 2687055    zabbix     600        13        
	0x00000000 2719824    zabbix     600        13        
	0x00000000 2752593    zabbix     600        13        
	0x00000000 2785362    zabbix     600        13        
	0x00000000 2818131    zabbix     600        13        
	0x00000000 2850900    zabbix     600        13        
	0x00000000 2883669    zabbix     600        13        
	0x00000000 2916438    zabbix     600        13        
	0x00000000 2949207    zabbix     600        13        
	0x00000000 2981976    zabbix     600        13        
	0x00000000 3014745    zabbix     600        13        
	0x00000000 3047514    zabbix     600        13        
	0x00000000 3080283    zabbix     600        13        
	0x00000000 3113052    zabbix     600        13        
	0x00000000 3145821    zabbix     600        13        
	0x00000000 3178590    zabbix     600        13        
	0x00000000 3211359    zabbix     600        13        
	0x00000000 3244128    zabbix     600        13        
	0x00000000 3276897    zabbix     600        13        
	0x00000000 3309666    zabbix     600        13        
	0x00000000 3342435    zabbix     600        13        
	0x00000000 3375204    zabbix     600        13        
	0x00000000 3407973    zabbix     600        13        
	0x00000000 3440742    zabbix     600        13        
	0x00000000 3473511    zabbix     600        13        
	0x00000000 3506280    zabbix     600        13        
	0x00000000 3539049    zabbix     600        13        
	0x00000000 3571818    zabbix     600        13        
	0x00000000 3604587    zabbix     600        13        
	0x00000000 3637356    zabbix     600        13        
	0x00000000 3670125    zabbix     600        13        
	0x00000000 3702894    zabbix     600        13        
	0x00000000 3735663    zabbix     600        13        
	0x00000000 3768432    zabbix     600        13        
	0x00000000 3801201    zabbix     600        13        
	0x00000000 3833970    zabbix     600        13        
	0x00000000 3866739    zabbix     600        13        
	0x00000000 3899508    zabbix     600        13        
	0x00000000 3932277    zabbix     600        13        
	0x00000000 3965046    zabbix     600        13        
	0x00000000 3997815    zabbix     600        13        
	0x00000000 4030584    zabbix     600        13        
	0x00000000 4063353    zabbix     600        13        
	0x00000000 4096122    zabbix     600        13        
	0x00000000 4128891    zabbix     600        13        
	0x00000000 4161660    zabbix     600        13        
	0x00000000 4194429    zabbix     600        13        
	0x00000000 4227198    zabbix     600        13        
	0x00000000 4259967    zabbix     600        13        

删除它们

::

	[root@mariadb-node-0 zabbix]# ipcrm --help

	用法：
	 ipcrm [options]
	 ipcrm <shm|msg|sem> <id> [...]

	选项：
	 -m, --shmem-id <id>        按 id 号移除共享内存段
	 -M, --shmem-key <键>       按键值移除共享内存段
	 -q, --queue-id <id>        按 id 号移除消息队列
	 -Q, --queue-key <键>       按键值移除消息队列
	 -s, --semaphore-id <id>    按 id 号移除信号量
	 -S, --semaphore-key <键>  按键值移除信号量
	 -a, --all[=<shm|msg|sem>]  全部移除
	 -v, --verbose              解释正在进行的操作

	 -h, --help     显示此帮助并退出
	 -V, --version  输出版本信息并退出

	更多信息请参阅 ipcrm(1)。
	[root@mariadb-node-0 zabbix]# ipcrm -s 98304
	[root@mariadb-node-0 zabbix]# 
	
这样就好办了，写个shell吧。

::

	[root@mariadb-node-0 zabbix]# cat ~/t.sh 
	#!/usr/bin/bash
	for line in `cat ./sem-id.txt`
	do
	  echo $line
	  ipcrm  -s $line
	done
	echo "game over!"
	[root@mariadb-node-0 zabbix]#
	
ok, 全删除了。

把原有的配置文件还原，然后 重启服务 吧。

重启之后就OK了。


--------------

还有一个方向是这样的：

http://www.itzgeek.com/how-tos/linux/centos-how-tos/how-to-install-zabbix-agent-on-centos-7-ubuntu-16-04-debian-8.html

设置与查看 selinux

::

    [jlch@mariadb-node-0 zabbix]$ getenforce
    Permissive
    [jlch@mariadb-node-0 zabbix]$ sudo setenforce 0
    [jlch@mariadb-node-0 zabbix]$ sudo getenforce
    Permissive

查日志：

::

    [jlch@mariadb-node-0 zabbix]$ sudo cat /var/log/audit/audit.log |grep failed
    ... ## 这里有很多很多啦
    'unit=zabbix-agent comm="systemd" exe="/usr/lib/systemd/systemd" hostname=? addr=? terminal=? res=failed'
    type=SERVICE_STOP msg=audit(1507601499.089:102137): pid=1 uid=0 auid=4294967295 ses=4294967295 subj=system_u:system_r:init_t:s0 msg='unit=zabbix-agent comm="systemd" exe="/usr/lib/systemd/systemd" hostname=? addr=? terminal=? res=failed'
    [jlch@mariadb-node-0 zabbix]$ ^C

查 audit 返回的原因

::

    [jlch@mariadb-node-0 zabbix]$ sudo cat /var/log/audit/audit.log |grep zabbix-agent |audit2allow
    [sudo] password for jlch: 
    Nothing to do
    [jlch@mariadb-node-0 zabbix]$ sudo cat /var/log/audit/audit.log |grep zabbix-agent |audit2why
    Nothing to do
    [jlch@mariadb-node-0 zabbix]$ 

发现没有返回原因，说明原因不在这个地方嘛。（当然不在这个地方了，我们这里是因为
内存文件系统 ipcs 嘛）

所以不用再考虑 selinux 了。
