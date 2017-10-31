
不连网 部署 factor 海通版本
---------------------------------

redis
^^^^^^^^

::

	tar -zxvf redis.tar.gz
	
nginx
^^^^^^^^

::

	ls
	wget http://nginx.org/packages/centos/7/x86_64/RPMS/nginx-1.12.1-1.el7.ngx.x86_64.rpm
	ls
	rpm -ivh nginx-1.12.1-1.el7.ngx.x86_64.rpm
	sudo rpm -ivh nginx-1.12.1-1.el7.ngx.x86_64.rpm
	ls
	which nginx
	ls /etc/nginx/conf.d/
	ls /usr/share/nginx/html/
	
nodejs
^^^^^^^^

::

	ls
	which xz
	xz -d node-v6.11.4-linux-x64.tar.xz
	ls
	tar -xvf node-v6.11.4-linux-x64.tar
	ls
	cd node-v6.11.4-linux-x64/
	ls
	cd bin/
	sudo ln -s /home/jlch/node-v6.11.4-linux-x64/bin/node /usr/bin/node
	cd
	node
	
pm2 
^^^^^^^^

::


	ls
	tar -zxvf pm2.tar.gz
	ls
	cd pm2/
	ls
	cd bin/
	ls
	pwd
	./pm2 list
	pwd
	cd ~
	ls
	cd node-v6.11.4-linux-x64/
	ls
	cd lib/node_modules/
	ls
	cd npm/
	ls
	cd ..
	pwd
	ls
	mv ~/pm2/ .
	ls
	cd pm2
	ls
	cd bin
	ls
	pwd
	sudo ln -s /home/jlch/node-v6.11.4-linux-x64/lib/node_modules/pm2/bin/pm2 /usr/bin/pm2
	cd
	pm2 l

如果这里出错了，可能是因为之前操作过pm2, 所以请将 ~/.pm2/ 删除，也就是
	rm -rf ~/.pm2/

	
factor_svr
^^^^^^^^

::


	tar -zxvf factor_svr.tar.gz
	
factor-html	
^^^^^^^^

::
	
	cp factor-html/ /usr/share/nginx/html/factor/
	
	

	