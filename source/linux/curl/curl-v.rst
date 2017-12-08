====================================
curl -v 参数作调试
====================================


加了这个 -v 参数后，可以看到更详细的内容。

从内网取

::

    [root@test_240 conf.d]# curl -X POST "http://10.10.11.20:8000/transmitter-api-v2/session" -H "accept: application/json" -H "content-type: application/json" -d "{ \"login_name\": \"xiaozhao\", \"login_pwd\": \"wdxiaozhao\"}" -v
    * About to connect() to 10.10.11.20 port 8000 (#0)
    *   Trying 10.10.11.20...
    * Connected to 10.10.11.20 (10.10.11.20) port 8000 (#0)
    > POST /transmitter-api-v2/session HTTP/1.1
    > User-Agent: curl/7.29.0
    > Host: 10.10.11.20:8000
    > accept: application/json
    > content-type: application/json
    > Content-Length: 54
    > 
    * upload completely sent off: 54 out of 54 bytes
    < HTTP/1.1 404 Not Found
    < Server: nginx/1.8.0
    < Date: Fri, 08 Dec 2017 06:47:01 GMT
    < Content-Type: application/json; charset=utf-8
    < Content-Length: 63
    < Connection: keep-alive
    < Access-Control-Allow-Origin: *
    < Access-Control-Allow-Methods: GET, POST, PUT, UPDATE, DELETE, PATCH, OPTIONS
    < Access-Control-Allow-Headers: Origin, X-Requested-With, Content-Type, Accept, x-access-token
    < 
    * Connection #0 to host 10.10.11.20 left intact
    {"code":"ResourceNotFound","message":"/session does not exist"}

从阿里云过外网取

::

    [tom@jlch_web_001 ~]$ curl -X POST "http://120.234.23.98:8000/transmitter-api-v2/session" -H "accept: application/json" -H "content-type: applicatin_name\": \"xiaozhao\", \"login_pwd\": \"wdxiaozhao\"}"  -v
    * About to connect() to 120.234.23.98 port 8000 (#0)
    *   Trying 120.234.23.98...
    * Connected to 120.234.23.98 (120.234.23.98) port 8000 (#0)
    > POST /transmitter-api-v2/session HTTP/1.1
    > User-Agent: curl/7.29.0
    > Host: 120.234.23.98:8000
    > accept: application/json
    > content-type: application/json
    > Content-Length: 54
    > 
    * upload completely sent off: 54 out of 54 bytes
    < HTTP/1.1 404 Not Found
    < Content-Type: text/html
    < Expires: 0
    < Cache-control: private
    < Content-Length: 334
    < 
    * Connection #0 to host 120.234.23.98 left intact
    Sorry, Page Not Found

防火墙，直接篡改了 404的错误页面，还是很666的.....
