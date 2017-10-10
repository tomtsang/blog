
zabbix 常用命令
================

安装：

::

	sudo rpm -ivh http://repo.zabbix.com/zabbix/3.4/rhel/7/x86_64/zabbix-release-3.4-1.el7.centos.noarch.rpm
	sudo yum install -y zabbix-agent
	sudo systemctl start zabbix-agent.service
	sudo systemctl restart zabbix-agent.service
	sudo systemctl status zabbix-agent.service

开放端口：

::

	sudo firewall-cmd --permanent --add-port=10050/tcp; sudo firewall-cmd --reload; sudo firewall-cmd --permanent --list-port
	sudo ufw allow 10050

安装插件：

::

	sudo yum install -y sysstat
	sudo systemctl status sysstat
	sudo systemctl enable sysstat

修改配置：

::

	vi /etc/zabbix/zabbix-agent.conf
	
	
	## 修改下列配置
	# Server=10.10.11.20
	Server=zabbix_server
	ServerActive=10.10.11.20
	Hostname=本机IP
	
	## 新增下列配置
	##Linux磁盘IOPS
	##---------------------------------
	#每秒合并读操作的次数，读相邻块(block)的两个请求被合并成一个IO
	UserParameter=disk.rrqm[*],iostat -dx | sed -n '4p' | awk '{print $$2}'
	#每秒合并写操作的次数
	UserParameter=disk.wrqm[*],iostat -dx | sed -n '4p' | awk '{print $$3}'
	#每秒读操作的次数
	UserParameter=disk.rps[*],iostat -dx | sed -n '4p' | awk '{print $$4}'
	#每秒写操作次数
	UserParameter=disk.wps[*],iostat -dx | sed -n '4p' | awk '{print $$5}'
	#每秒读写次数之和
	UserParameter=disk.iops[*],iostat -d | sed -n '4p' | awk '{print $$2}'
	#每秒读的数据量(MB为 单位)
	UserParameter=disk.rMBps[*],iostat -dxm | sed -n '4p' | awk '{print $$6}'
	#每秒写的数据量(MB为单位)
	UserParameter=disk.wMBps[*],iostat -dxm | sed -n '4p' | awk '{print $$7}'
	#每个I/O平均所需的时间(包括等待时间和处理时间，毫秒为单位)
	UserParameter=disk.await[*],iostat -dx | sed -n '4p' | awk '{print $$10}'
	#每个读操作平均所需的时间包括等待时间和处理时间，毫秒为单位)
	UserParameter=disk.rawait[*],iostat -dx | sed -n '4p' | awk '{print $$11}'
	#每个写操作平均所需的时间(包括等待时间和处理时间，毫秒为单位)
	UserParameter=disk.wawait[*],iostat -dx | sed -n '4p' | awk '{print $$12}'
	#每个IO请求的平均处理时间(毫秒为单位)
	UserParameter=disk.svctm[*],iostat -dx | sed -n '4p' | awk '{print $$13}'
	#采样周期内用于IO操作的时间比率，即IO队列非空的时间比率
	UserParameter=disk.util[*],iostat -dx | sed -n '4p' | awk '{print $$14}'
	##---------------------------------


以下机器全都安装好了。

::

	## centos
	192.168.31.249

	10.10.15.181
	10.10.15.240
	10.10.12.17
	10.10.12.18
	10.10.12.16
	10.10.13.100
	10.10.13.101
	10.10.13.12
	10.10.13.11
	10.10.13.110
	10.10.12.10 
	10.10.11.20
	10.10.13.10 

	## ubuntu
	12.19    
	13.103


## over
