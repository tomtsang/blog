================
transmitter
================

判断外网访问是否正常
====================

::

    curl -X POST "http://qliang.com.cn/transmitter_api/v1/session" -H "accept: application/json" -H "content-type: application/json" -d "{ \"login_name\": \"htqilei\", \"login_pwd\": \"123456\"}"
    curl -X POST "https://api-cm.zhangtl.com/transmitter_api/v1/session" -H "accept: application/json" -H "content-type: application/json" -d "{ \"login_name\": \"htqilei\", \"login_pwd\": \"123456\"}"
    curl -X POST "https://api-ct.zhangtl.com:8443/transmitter_api/v1/session" -H "accept: application/json" -H "content-type: application/json" -d "{ \"login_name\": \"htqilei\", \"login_pwd\": \"123456\"}"


