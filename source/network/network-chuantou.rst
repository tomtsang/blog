===============================================
内网穿透，可以让全世界访问家用电脑里的网站
===============================================

内网穿透，可以让全世界访问家用电脑里的网站

https://gitee.com/wapai/chuantou

env
===============================================

::

    192.168.1.104   client, home
    120.25.204.216  server, aliyun

step
===============================================

server
-----------------------------------------------

::

    sudo systemctl status firewalld.service
    export PATH=$PATH:/home/tom/go/bin
    git clone https://gitee.com/wapai/chuantou 
    cd chuantou/
    ls 
    go run server.go -localPort 3002 -remotePort 20012
    sudo firewall-cmd --permanent --add-port=3002/tcp
    sudo firewall-cmd --reload
    go run server.go -localPort 3002 -remotePort 20012
    

client
-----------------------------------------------

::

    export PATH=$PATH:/home/jlch/go/bin
    git clone https://gitee.com/wapai/chuantou 
    cd chuantou/
    ls 
    go run client.go -host 120.25.204.216 -localPort 80 -remotePort 20012


这样, 在浏览器中, 通过 serverIP:3002 可以得到 clientIP:80 的页面.