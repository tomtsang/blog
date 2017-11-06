========================
nginx-conf-server_name
========================

下面的配置，是 蛟龙出海 阿里云机器 的 nginx 配置。

注意看下面的server_name 

::

    [tom@jlch_web_001 ~]$ cd /etc/nginx/conf.d/
    [tom@jlch_web_001 conf.d]$ ls
    jiaolongchuhai.conf  mjiaolongchuhai.conf  zhangtl.conf
    [tom@jlch_web_001 conf.d]$ cat zhangtl.conf 
    server {
            listen       80;
            server_name  www.zhangtl.com www.zhangtl.cn;
            root         /home/jlch/zhangtl;
            index index.html index.htm;

            location / {
            }


            error_page  404              /404.html;
            location = /40x.html {
            }

            error_page   500 502 503 504  /50x.html;
            location = /50x.html {
            }

        }

    [tom@jlch_web_001 conf.d]$ cat mjiaolongchuhai.conf 
    server {
            listen       80;
            server_name  m.jiaolongchuhai.com;
            root         /home/jlch/mjiaolongchuhai;
            index index.html index.htm;

            location / {
            }


            error_page  404              /404.html;
            location = /40x.html {
            }

            error_page   500 502 503 504  /50x.html;
            location = /50x.html {
            }

        }

    [tom@jlch_web_001 conf.d]$ cat jiaolongchuhai.conf 
    server {
            listen       80;
            server_name  www.jiaolongchuhai.com;
            root         /home/jlch/jiaolongchuhai;
            index index.html index.htm;

            location / {
            }

        
            error_page  404              /404.html;
            location = /40x.html {
            }

            error_page   500 502 503 504  /50x.html;
            location = /50x.html {
            }

        }

    [tom@jlch_web_001 conf.d]$ 

