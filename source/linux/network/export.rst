===================================================
export
===================================================


shell 下
===================================================

::

    root@km:~# export
    ...
    declare -x http_proxy="http://192.168.31.239:8118/"
    declare -x https_proxy="http://192.168.31.239:8118/"
    declare -x no_proxy="localhost,127.0.0.1,192.168.31.120,10.96.0.10,github.com,ubuntu.com"

no_proxy

* 支持类型

    export noproxy=.dev,localhost,127.0.0.1,.31.123,123

* 不支持类型
    export no_proxy=192.,192.*,192.168.0.0/16

/etc/apt/apt.conf下
===================================================

这下面的 no::proxy 不知道生效不生效。有知道的小伙伴，求赐教，谢。

::

    root@km:~# cat /etc/apt/apt.conf
    #Acquire::http::proxy "http://192.168.31.10:8031/";
    Acquire::http::proxy "http://192.168.31.239:8118/";
    #Acquire::https::proxy "https://192.168.31.10:8031/";
    Acquire::https::proxy "https://192.168.31.239:8118/";
    Acquire::no::proxy "ubuntu.com";
    root@km:~# 


