===========================================
Kibana 基础入门
===========================================


env
=========================

192.168.31.149(192.168.31.193) centos7

reference
=========================

https://www.cnblogs.com/moonlightL/p/7764919.html

step
=========================


在操作前, 必须先把 rudiments-elasticsearch.rst 的内容先走一遍.

2.2 修改配置文件

::

    vim config/kibana.yml

# 将默认配置改成如下：

::

    server.port: 5601
    server.host: "0.0.0.0"
    elasticsearch.url: "http://192.168.31.149:9200"
    kibana.index: ".kibana"

2.3 启动

bin/kibana
启动后打开浏览器访问 http://192.168.2.43:5601 浏览 kibana 界面：

三、演示
上图中，提示不能获取映射，即 Elasticsearch 中的索引。我们需要手动配置。在 Index Pattern 下边的输入框中输入 access-*(好了,因为我们之前的index是 accounts, 所以这里就写 accounts)，它是 Elasticsearch 中的一个索引名称开头。

Kibana 会自动检测在 Elasticsearch 中是否存在该索引名称，如果有，则下边出现 “Create” 按钮，我们点击进行创建并来到如下界面：

“Discovery” 菜单界面主要用于通过搜索请求，过滤结果，查看文档数据。可以查询搜索请求的文档总数，获取字段值的统计情况并通过柱状图进行展示。

点击左侧 “Discovery” 菜单，来到如下界面：