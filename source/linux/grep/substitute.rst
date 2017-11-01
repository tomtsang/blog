
===========
substitute
===========


替换

reference
================

http://linux.it.net.cn/e/shell/2015/0102/11241.html

http://www.cnblogs.com/qq78292959/archive/2012/03/08/2385080.html

http://blog.csdn.net/lzyzuixin/article/details/7638979

http://wiki.jikexueyuan.com/project/shell-learning/sed-search-and-replace.html

command
================

::

    grep "https://api-cm.zhangtl.com/" -rl package.json | xargs sed -i 's/https:\/\/api-cm.zhangtl.com\//http:\/\/10.10.15.181:8000\//g'
    grep "192.168.31.99" -rl ./ | xargs sed -i "s/192.168.31.99/192.168.31.105/g"
    grep "192.168.31.105" -rl ./ | xargs sed -i "s/192.168.31.105/192.168.31.99/g"
