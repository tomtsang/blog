===========================================
Elasticsearch 基础入门
===========================================

env
=========================

elasticsearch: 192.168.31.149(192.168.31.193) centos7

reference
=========================

https://www.extlight.com/2017/09/27/Elasticsearch-%E5%9F%BA%E7%A1%80%E5%85%A5%E9%97%A8/

step
=========================

2.5 安装插件

修改 Elasticsearch 的配置文件：

::

    cd elasticsearch-5.6.1
    vim config/elasticsearch.yml
    # 在文件末尾添加 2 段配置
    http.cors.enabled: true
    http.cors.allow-origin: "*"

安装插件 mobz/elasticsearch-head

::

    sudo docker run -p 9100:9100 mobz/elasticsearch-head:5

运行完成后, 在浏览器输入

http://192.168.31.149:9100/

然后在 elasticsearch 集群输入框中, 输入elasticsearch 集群IP地址: 

http://192.168.31.149:9200/

马上就有了内容, 很方便..

三、使用

准备 postman 工具

http://blog.csdn.net/water_0815/article/details/53263643

使用 POSTMAN 工具进行接口的请求。

draft, 期间用到的一些 json 文字
=====================================

::

    {
        "settings": {
            "index": {
                "number_of_shards": "3",
                "number_of_replicas": "1"
            }
        },
        "mappings": {
            "apple": {
                "properties": {
                    "color": {
                        "type": "text"
                    },
                    "createTime": {
                        "format": "yyyy-MM-dd HH:mm:ss || yyyy-MM-dd || epoch_millis",
                        "type": "date"
                    },
                    "weight": {
                        "type": "integer"
                    }
                }
            },
            "orange": {}
        }
    }

    {
        "color": "red",
        "createTime":"2017-12-27",
        "weight": 2
    }

    {"doc":{"color":"black"}}

    {
        "query": {
            "match":{
                "color": "green"
            }
        }
    }