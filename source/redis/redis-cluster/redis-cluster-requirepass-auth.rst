

==================
redis-cluster-requirepass-auth
==================

redis-cluster-requirepass-auth 配置密码时，一定要另加一句 `AUTH wudang`

::

    [jlch@check ~]$ redis-cli -h 192.168.31.166 -p 7000 -a wudang -c
    192.168.31.166:7000> AUTH wudang
    (error) ERR Client sent AUTH, but no password is set
    192.168.31.166:7000> CONFIG SET requirepass "wudang"
    OK
    192.168.31.166:7000> AUTH wudang
    OK
    192.168.31.166:7000> 

不然，配置的密码，在连接时会报错的哟。

