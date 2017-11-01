
=================
scp ssh 同时出错
=================

::

	[cdn@test_240 ~]$ scp -r ~/tmp/ tom@120.25.204.216:/home/tom/
	ssh_exchange_identification: read: Connection reset by peer
	lost connection
	[cdn@test_240 ~]$ ssh -T git@git.oschina.net
	ssh_exchange_identification: read: Connection reset by peer
	[cdn@test_240 ~]$ 



连 scp 也会报这个错误了。。。。

后来被证实是 一灯 防火墙的问题

汗~~~~