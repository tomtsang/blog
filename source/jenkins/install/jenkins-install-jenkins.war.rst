

======================================
jenkins install jenkins.war
======================================


Jenkins install
=====================================

环境：192.168.31.181:/home/tom/

::

    java -jar jenkins.war --httpPort=8011

http://192.168.31.181:8011/

jlch, jlch, uuiduuid, 273412935@qq.com



Jenkins+github+nodejs
=====================================

https://segmentfault.com/a/1190000010200161

然后，我们配置吧。


在 github.com 中要配置 webhook, 这个时候，要填写 jenkins服务器IP, 这时候，我们就要 通过 nginx 作反向代理了。

然后，我在反向代理出错了。（主要原因是 jenkins 页面，没有写成 相对路径）

悲剧了，怎么办?

1. 去阿里云，然后，重新来一次

http://blog.csdn.net/frank_good/article/details/68930286

http://www.jianshu.com/p/22b7860b4e81 (我通过这个网址，做到成功了)

tomtsang- personal access token -

91e4a74a0ec87f7fce9b6344ead0caa86e7644a0

环境：120.25.204.216

安装方式：centos裸机安装。

2. 去 让一灯实现一下。


Jenkins+gitlab+nodejs
=====================================