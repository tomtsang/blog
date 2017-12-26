===========================================
全文搜索引擎 Elasticsearch 入门教程
===========================================

http://www.ruanyifeng.com/blog/2017/08/elasticsearch.html


::

    [jlch@udvbct-ekl-a elasticsearch-5.6.5]$ curl -X GET 'http://localhost:9200/_cat/indices?v'
    health status index uuid pri rep docs.count docs.deleted store.size pri.store.size
    [jlch@udvbct-ekl-a elasticsearch-5.6.5]$ curl 'localhost:9200/_mapping?pretty=true'
    { }
    [jlch@udvbct-ekl-a elasticsearch-5.6.5]$ curl -X PUT 'localhost:9200/weather'
    {"acknowledged":true,"shards_acknowledged":true,"index":"weather"}
    [jlch@udvbct-ekl-a elasticsearch-5.6.5]$ curl -X GET 'http://localhost:9200/_cat/indices?v'
    health status index   uuid                   pri rep docs.count docs.deleted store.size pri.store.size
    yellow open   weather -58xfoleTr6sI58ZPZzLKA   5   1          0            0       324b           324b
    [jlch@udvbct-ekl-a elasticsearch-5.6.5]$ curl -X DELETE 'localhost:9200/weather'
    {"acknowledged":true}
    [jlch@udvbct-ekl-a elasticsearch-5.6.5]$ curl -X GET 'http://localhost:9200/_cat/indices?v'
    health status index uuid pri rep docs.count docs.deleted store.size pri.store.size
    [jlch@udvbct-ekl-a elasticsearch-5.6.5]$ 



有一个思维导图,见:

http://naotu.baidu.com/file/e7feaf5c7ab075150dcb22fb3977128d


