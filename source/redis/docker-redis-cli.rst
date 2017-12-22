===================================
docker 中 redis-cli 连接
===================================

用 docker 容器来执行一个 redis 客户端

docker run -t -i --rm redis /bin/bash

::

    [jlch@udvbct-ekl-a ~]$ sudo docker pull redis
    [jlch@udvbct-ekl-a ~]$ sudo docker run -t -i --rm redis /bin/bash
    root@b0541c712009:/data# redis-cli -h 192.168.31.149
    192.168.31.149:6379> ping
    PONG
    192.168.31.149:6379>
    root@b0541c712009:/data# redis-cli -h 192.168.31.181
    192.168.31.181:6379> ping
    PONG
    192.168.31.181:6379>
    root@b0541c712009:/data#