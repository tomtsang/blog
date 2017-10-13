

=======================
redis-cluster-del-key
=======================


总体思路
------------

先拿到 key 所在的 slot ，然后就能找到对应 的 IP:Port, 然后删除就行了。


查 cluster 信息
^^^^^^^^^^^^^^^

::

    [jlch@check ~]$ redis-cli -h 192.168.31.166 -p 7000 -a wudang -c cluster nodes

查  key 所在的 slot
^^^^^^^^^^^^^^^^^^^^^

::

    [jlch@check ~]$ redis-cli -h 192.168.31.166 -p 7000 -a wudang -c cluster keyslot 600000 

删除 key
^^^^^^^^^

::

    [jlch@check ~]$ redis-cli -h 192.168.31.168 -p 7004 -a wudang -c get 600000
    [jlch@check ~]$ redis-cli -h 192.168.31.168 -p 7004 -a wudang -c del 600000

批量删除
-------------

http://blog.csdn.net/badyting/article/details/76176824

::

    cat redis-cluster-del.sh
    #!/bin/bash
    echo "start"
    redis_list=("127.0.0.1:6379" "127.0.0.1:6380")
    for info in ${redis_list[@]}
    do  
        echo "开始执行:$info"  
        ip=`echo $info | cut -d \: -f 1`
        port=`echo $info | cut -d \: -f 2`
        # 如果有密码，要加上 -a password
        cat key.txt | xargs -t -n1 redis-cli -h $ip -p $port -c del  
    done 
    echo "game over"