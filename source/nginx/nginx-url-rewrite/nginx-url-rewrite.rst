==================
nginx url rewrite
==================

http://www.cnblogs.com/czlun/articles/7010604.html

env
=================

::

    192.168.31.240

step
=================

先把常用的 url 用 wget, curl 把文件取下来。

::

    wget --header="accept:application/json" -d  "http://192.168.31.181:3062/stocks/600006.SH/factor_analysis?type=1&cycle=2"  # -d 输出调试信息
    curl -v -X GET "http://192.168.31.181:3062/stocks/600006.SH/factor_analysis?type=1&cycle=2" -H "accept:application/json"  # -v 输出调试信息





