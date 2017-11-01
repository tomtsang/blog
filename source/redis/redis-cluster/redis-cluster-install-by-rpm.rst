

===============================
redis-cluster-install-by-rpm
===============================



http://rpmfind.net/
http://rpmfind.net/linux/rpm2html/search.php?query=redis&submit=Search+...
http://rpmfind.net/linux/RPM/remi/enterprise/7/x86_64/redis-4.0.2-1.el7.remi.x86_64.html

https://pkgs.org/download/redis
https://centos.pkgs.org/7/remi-x86_64/redis-4.0.2-1.el7.remi.x86_64.rpm.html

--- 改 fedora 版本

http://rpmfind.net/linux/rpm2html/search.php?query=redis&submit=Search+...&system=&arch=


step
^^^^^^^^^^^^^^^^

安装 ruby, gem

	参考 http://blogtt.readthedocs.io/en/latest/ruby/ruby-install/ruby%20install%20by%20rpm.html#ruby
	
gem install redis

	参考 http://blogtt.readthedocs.io/en/latest/ruby/gem-install/gem-install-redis.html
	
启动 redis

::

	systemctl status redis
	systemctl start redis
	systemctl status redis
	redis-cli
	systemctl stop redis
	
启动 redis-cluster

下面这一部分，可以参考一下，当时给 海通证券部署factor 的情况。

::

	cd by-source/
	ls
	cd redis-deploy-conf/
	ls
	./redis-deploy-ip-port-passwd.sh 127.0.0.1 7000 wudang
	./redis-deploy-ip-port-passwd.sh 127.0.0.1 7001 wudang
	./redis-deploy-ip-port-passwd.sh 127.0.0.1 7002 wudang
	./redis-deploy-ip-port-passwd.sh 127.0.0.1 7003 wudang
	./redis-deploy-ip-port-passwd.sh 127.0.0.1 7004 wudang
	./redis-deploy-ip-port-passwd.sh 127.0.0.1 7005 wudang
	ls
	ls /usr/lib/systemd/system/redis*
	cat /usr/lib/systemd/system/redis.service
	/usr/bin/redis-server node-7000/redis.conf &
	/usr/bin/redis-server node-7001/redis.conf &
	/usr/bin/redis-server node-7002/redis.conf &
	/usr/bin/redis-server node-7003/redis.conf &
	/usr/bin/redis-server node-7004/redis.conf &
	/usr/bin/redis-server node-7005/redis.conf &
	./redis-trib.rb create --replicas 1 127.0.0.1:7000 127.0.0.1:7001 127.0.0.1:7002 127.0.0.1:7003 127.0.0.1:7004 127.0.0.1:7005


