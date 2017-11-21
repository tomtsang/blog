==================================
使用Privoxy将socks5代理转为http代理
==================================

https://blog.phpgao.com/privoxy-shadowsocks.html

https://tzrgaga.github.io/2017/04/12/forward-socks-by-privoxy/


下载Privoxy
==================================

http://www.privoxy.org/sf-download-mirror/Win32/3.0.26%20%28stable%29/

修改配置
==================================

找到Privoxy的配置文件位置，默认是在C:\Program Files (x86)\Privoxy\config.txt

然后使用编辑器来编辑它，底部加入一行forward-socks5 / 127.0.0.1:1080 .

加

::

    forward-socks5	/		127.0.0.1:1080	.

修改 绑定IP

::

    #listen-address  127.0.0.1:8118
    # 0:8118， *:8118, [::1]:8118, 都无效。
    listen-address  0.0.0.0:8118


启动
==================================

打开 C:\Program Files (x86)\Privoxy

双击 privoxy.exe

测试
==================================

本机确实是有在 LISTENING, 看：

::

    C:\Users\k>netstat -anto | grep 8118
    TCP    127.0.0.1:8118         0.0.0.0:0              LISTENING       9648     InHost
    TCP    127.0.0.1:8118         127.0.0.1:54831        ESTABLISHED     9648     InHost
    TCP    127.0.0.1:54831        127.0.0.1:8118         ESTABLISHED     15868    InHost
    TCP    [::1]:8118             [::]:0                 LISTENING       16524    InHost

    C:\Users\k>

使用另一台机器，如 linux

::

    export http_proxy=http://192.168.31.239:8118
    export https_proxy=http://92.168.31.239:8118
    wget www.google.com
    curl -sSL https://dl.k8s.io/release/stable.txt

    