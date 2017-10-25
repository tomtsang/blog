==========================================
big-data-1g-download-failure
==========================================

蛟龙-海通，通讯程序，超过1G的数据，就出错了。

那么百度了一下，

https://www.baidu.com/s?ie=utf-8&f=8&rsv_bp=0&rsv_idx=1&tn=baidu&wd=nginx%20%E4%B8%8B%E8%BD%BD%201G%20%E6%96%AD%E5%BC%80&rsv_pq=80215161000085a1&rsv_t=0e99I%2FzMf%2BHB7Umakbrg9zOn08tQ2h6Nc%2FAkVeEmCSu43QW%2B%2Ffyfn%2BD2zaY&rqlang=cn&rsv_enter=1&rsv_sug3=23&rsv_sug1=10&rsv_sug7=100&rsv_sug2=0&inputT=11519&rsv_sug4=11519 | nginx 下载 1G 断开_百度搜索
https://segmentfault.com/q/1010000000383415 | nginx 反向代理，遇到大文件无法下载 - SegmentFault
http://blog.chinaunix.net/uid-20332519-id-5755724.html | Nginx反向代理导致大文件下载失败-lsstarboy-ChinaUnix博客
http://www.ttlsa.com/nginx/optimizing-nginx-bigger-file/ | nginx大文件下载优化 – 运维生存时间
http://www.cnblogs.com/breezey/p/6740229.html | nginx反向代理下载文件失败处理 - breezey - 博客园

::

        proxy_send_timeout 120;
        proxy_read_timeout 120;
        proxy_max_temp_file_size 4096m;

重启了一下 nginx, 看一下

::

    [jlch@qlw ~]$ sudo /usr/sbin/nginx -s reload
    nginx: [warn] duplicate MIME type "text/html" in /etc/nginx/conf.d/8000.qstation.conf:11
    nginx: [warn] duplicate MIME type "text/html" in /etc/nginx/conf.d/8002.factor-zx.conf:10
    [jlch@qlw ~]$ sudo systemctl restart nginx

等行痴的反馈吧。

