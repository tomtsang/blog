goaccess 安装 使用
------------------

reference
^^^^^^^^^^^

https://goaccess.io/download

http://www.itread01.com/articles/1476744943.html

http://www.jianshu.com/p/33e2e79d0373

http://chenx1242.blog.51cto.com/10430133/1882643


192.168.31.240:/opt/goaccess-1.2/

goaccess 安装
^^^^^^^^^^^^^^^^^^

::

    ps -ef | grep goacc*
    sudo yum install glib2 glib2-devel GeoIP-devel  ncurses-devel zlib zlib-devel -y
    cd /opt/
    ls
    sudo wget http://tar.goaccess.io/goaccess-1.2.tar.gz
    sudo tar -xzvf goaccess-1.2.tar.gz 
    cd goaccess-1.2/
    sudo ./configure --enable-utf8 --enable-geoip=legacy
    sudo make
    sudo make install
    ls
    ls goaccess*
    sudo cp /usr/local/etc/goaccess.conf .
    cat >>./goaccess.conf<<EOF
    time-format %H:%M:%S
    date-format %d/%b/%Y
    log-format %h %^[%d:%t %^] "%r" %s %b "%R" "%u"
    EOF

使用：

M1

::

    ls /var/log/nginx/access.log
    tail -f /var/log/nginx/access.log
    sudo goaccess -a -d -f /var/log/nginx/access.log -p ./goaccess.conf >
    sudo goaccess -a -d -f /var/log/nginx/access.log -p ./goaccess.conf
    /home/tom/index.html

M2

::

    [root@qlw goaccess-1.2]# goaccess -a -d -f /var/log/nginx/8000.access.log -p ./goaccess.conf -o /usr/share/nginx/html/qstation/statistics/report.html –real-time-html
    WebSocket server ready to accept new client connections

M3

::

    tail -f -n +0 /var/log/nginx/8000.access.log | grep --color=auto "factor-zx" | goaccess -p ./goaccess.conf -o /usr/share/nginx/html/qstation/statistics/report-factor-zx.html --real-time-html &
    tail -f -n +0 /var/log/nginx/8000.access.log | grep --color=auto "GET /factor-zx/ " | goaccess -p ./goaccess.conf -o /usr/share/nginx/html/qstation/statistics/report-factor-zx.html --real-time-html &

    tail -f -n +0 /var/log/nginx/8002.access.log | goaccess -p ./goaccess.conf -o /usr/share/nginx/html/qstation/statistics/report-factorzx.html --real-time-html
    tail -f -n +0 /var/log/nginx/443.access.log | goaccess -p ./goaccess.conf -o /data/nginx/html/statistics/reports.html --real-time-html

实践：

::

    root@10.10.11.10
    mkdir -p /data/nginx/html/statistics/
    tail -f -n +0 /var/log/nginx/443.access.log | grep "citics" | goaccess -p ./goaccess.conf -o /data/nginx/html/statistics/reports-citics.html --real-time-html