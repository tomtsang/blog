==========================================
rudiments-logstash-elasticsearch-kibana
==========================================

【入门篇】logstash、elasticsearch、kibana搭建日志平台

http://blog.csdn.net/pistolove/article/details/53634433

安装elasticsearch的时候, 感觉好烦, 配置来配置去的...直接用 docker 了..

::

    sudo docker pull logstash:5.6.5
    sudo docker pull elasticsearch:5.6.5
    sudo docker pull kibana:5.6.5

启动elasticsearch 

::

    sudo docker run -d -p 9200:9200 --name="es" elasticsearch:5.6.5

启动kibana

::

    [jlch@udvbct-ekl-a elasticsearch-6.1.1]$ sudo docker run -d -p 5601:5601 --name="kibana" kibana:5.6.5
    [sudo] password for jlch:
    14a4381dbfcda13a232d8f066d3af394c0cdb486e8bf93276e9b2b7e6721ca8d
    [jlch@udvbct-ekl-a elasticsearch-6.1.1]$ sudo docker ps
    CONTAINER ID        IMAGE                 COMMAND                  CREATED             STATUS              PORTS                              NAMES
    14a4381dbfcd        kibana:5.6.5          "/docker-entrypoint.…"   6 seconds ago       Up 3 seconds        0.0.0.0:5601->5601/tcp             kibana
    5cfd6dac25c2        elasticsearch:5.6.5   "/docker-entrypoint.…"   20 minutes ago      Up 20 minutes       0.0.0.0:9200->9200/tcp, 9300/tcp   es

开放端口

::    

    [jlch@udvbct-ekl-a elasticsearch-6.1.1]$ sudo firewall-cmd --permanent --add-port=9200/tcp
    success
    [jlch@udvbct-ekl-a elasticsearch-6.1.1]$ sudo firewall-cmd --permanent --add-port=5601/tcp
    success
    [jlch@udvbct-ekl-a elasticsearch-6.1.1]$ sudo firewall-cmd --reload
    success
    [jlch@udvbct-ekl-a elasticsearch-6.1.1]$

这样浏览器中打开, 就可以看到了.

    http://192.168.31.149:9200/

    http://192.168.31.149:5601/app/kibana

