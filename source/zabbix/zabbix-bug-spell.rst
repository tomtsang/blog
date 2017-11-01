=======================================
zabbix bug spell
=======================================

安装后，启动，看状态，报错了

	[root@mongodb1 jlch]# sudo systemctl status zabbix-agent.service
	[root@mongodb1 jlch]# journalctl -xe
	[root@mongodb1 jlch]# tail -n 200 /var/log/messages

从这里可以看到一条信息如下：

::

	systemd: [/usr/lib/systemd/system/anaconda-pre.service:18] Unknown lvalue 'StardardInput' in section 'Service'

google 查到这里

https://bugzilla.redhat.com/show_bug.cgi?id=1401333

说了，要：
Change "Stardard" to "Standard".

修改吧。

	[root@mongodb1 jlch]# vi /usr/lib/systemd/system/anaconda-pre.service
	[root@mongodb1 jlch]# sudo systemctl status anaconda-pre.service 
	● anaconda-pre.service - pre-anaconda logging service
	   Loaded: loaded (/usr/lib/systemd/system/anaconda-pre.service; static; vendor preset: disabled)
	   Active: inactive (dead)

	6月 30 12:50:17 mongodb1 systemd[1]: [/usr/lib/systemd/system/anaconda-pre.service:18] Unknown lvalue 'StardardInput' in section 'Service'
	6月 30 12:50:17 mongodb1 systemd[1]: [/usr/lib/systemd/system/anaconda-pre.service:18] Unknown lvalue 'StardardInput' in section 'Service'
	6月 30 12:50:17 mongodb1 systemd[1]: [/usr/lib/systemd/system/anaconda-pre.service:18] Unknown lvalue 'StardardInput' in section 'Service'
	6月 30 12:50:17 mongodb1 systemd[1]: [/usr/lib/systemd/system/anaconda-pre.service:18] Unknown lvalue 'StardardInput' in section 'Service'
	6月 30 12:50:17 mongodb1 systemd[1]: [/usr/lib/systemd/system/anaconda-pre.service:18] Unknown lvalue 'StardardInput' in section 'Service'
	6月 30 12:50:17 mongodb1 systemd[1]: [/usr/lib/systemd/system/anaconda-pre.service:18] Unknown lvalue 'StardardInput' in section 'Service'
	6月 30 12:50:17 mongodb1 systemd[1]: [/usr/lib/systemd/system/anaconda-pre.service:18] Unknown lvalue 'StardardInput' in section 'Service'
	6月 30 12:50:17 mongodb1 systemd[1]: [/usr/lib/systemd/system/anaconda-pre.service:18] Unknown lvalue 'StardardInput' in section 'Service'
	6月 30 12:50:17 mongodb1 systemd[1]: [/usr/lib/systemd/system/anaconda-pre.service:18] Unknown lvalue 'StardardInput' in section 'Service'
	6月 30 12:50:17 mongodb1 systemd[1]: [/usr/lib/systemd/system/anaconda-pre.service:18] Unknown lvalue 'StardardInput' in section 'Service'
	[root@mongodb1 jlch]# sudo systemctl start anaconda-pre.service 

果然，这样 anaconda-pre 就正常了，然后再启动 zabbix-agent ，也正常了。