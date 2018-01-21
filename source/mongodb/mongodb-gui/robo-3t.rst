=========================================================
MongoDB的客户端robo3t在，Ubuntu 16.04中启动时报一个QT的错误
=========================================================


ref
==========

https://www.cnblogs.com/hexin0614/p/7487191.html

原帖路径：https://askubuntu.com/questions/941383/cannot-run-robo-3t-qt-app

https://github.com/Studio3T/robomongo/issues/1384

env
===========

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

step
=============

【解决方法】

https://www.cnblogs.com/hexin0614/p/7487191.html

在参考这个URL之前,我参考了 https://www.zhihu.com/question/27387909 , 运行了 

:: 

    sudo apt-get install qt5-default qtcreator

但是我觉得应该与这条语句无关..所以如果有机会再次遇到这个问题, 先不执行上面这句, 然后看一下效果...


1 mkdir ~/robo-backup
2 mv robo3t-1.1.1-linux-x86_64-c93c6b0/lib/libstdc++* ~/robo-backup/
3 robo3t-1.1.1-linux-x86_64-c93c6b0/bin/robo3t
 
果然是成功了....
