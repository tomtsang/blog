

=======================
redis-cluster-del-key
=======================


总体思路：

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

