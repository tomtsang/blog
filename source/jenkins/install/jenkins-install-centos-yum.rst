
================================
jenkins install centos yum
================================

检查 8080 端口有没有占用。

::

	sudo lsof -i:8080
	
按官网安装


::

	sudo wget -O /etc/yum.repos.d/jenkins.repo https://pkg.jenkins.io/redhat-stable/jenkins.repo
	sudo rpm --import https://pkg.jenkins.io/redhat-stable/jenkins.io.key
	sudo yum install jenkins -y
	sudo systemctl status jenkins.service 
	sudo systemctl start jenkins.service 
	sudo systemctl status jenkins.service 
	sudo /etc/rc.d/init.d/jenkins start 
	sudo journalctl -xe 

查到原因

::

	Oct 18 13:13:11 jlch_web_001 runuser[21226]: pam_unix(runuser:session): session opened for user jenkins by (uid=0)
	Oct 18 13:13:11 jlch_web_001 jenkins[21225]: Starting Jenkins bash: /usr/bin/java: No such file or directory
	Oct 18 13:13:11 jlch_web_001 runuser[21226]: pam_unix(runuser:session): session closed for user jenkins
	Oct 18 13:13:11 jlch_web_001 jenkins[21225]: [FAILED]
	Oct 18 13:13:11 jlch_web_001 systemd[1]: jenkins.service: control process exited, code=exited status=1
	Oct 18 13:13:11 jlch_web_001 systemd[1]: Failed to start LSB: Jenkins Automation Server.
	-- Subject: Unit jenkins.service has failed
	-- Defined-By: systemd
	-- Support: http://lists.freedesktop.org/mailman/listinfo/systemd-devel
	-- 
	-- Unit jenkins.service has failed.
	-- 
	-- The result is failed.
	
哈哈，没有安装 java

::

	sudo yum list installed |grep java 
	sudo yum search java|grep jdk 

安装吧

::

	sudo yum install java-1.8.0-openjdk -y 
	
再启动

::

	sudo systemctl start jenkins.service 
	sudo systemctl status jenkins.service 
	
开端口

::

	sudo firewall-cmd --help 
	sudo firewall-cmd --permanent --add-port=8080/tcp 
	sudo firewall-cmd --reload 
	sudo firewall-cmd --list-ports 

浏览器去咯。

http://120.25.204.216:8080/


