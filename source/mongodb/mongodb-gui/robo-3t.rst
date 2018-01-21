=========================================================
MongoDB的客户端robo3t在，Ubuntu 16.04中启动时报一个QT的错误
=========================================================



https://www.cnblogs.com/hexin0614/p/7487191.html


【问题】

MongoDB的客户端robo3t在，Ubuntu 16.04中启动时报一个QT的错误：

复制代码
1 This application failed to start because it could not find or load the Qt platform plugin "xcb"
2 in "".
3 
4 Available platform plugins are: xcb.
5 
6 Reinstalling the application may fix this problem.
7 Aborted (core dumped)
复制代码
 

【解决方法】

1 mkdir ~/robo-backup
2 mv robo3t-1.1.1-linux-x86_64-c93c6b0/lib/libstdc++* ~/robo-backup/
3 robo3t-1.1.1-linux-x86_64-c93c6b0/bin/robo3t
 

原帖路径：https://askubuntu.com/questions/941383/cannot-run-robo-3t-qt-app

https://github.com/Studio3T/robomongo/issues/1384

