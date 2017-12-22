==========================================
logstash plugin configuration
==========================================







Inputs
------------------------------------------

input 及输入是指日志数据传输到Logstash中。其中常见的配置如下：

    file：从文件系统中读取一个文件，很像UNIX命令 "tail -0a"
    syslog：监听514端口，按照RFC3164标准解析日志数据
    redis：从redis服务器读取数据，支持channel(发布订阅)和list模式。redis一般在Logstash消费集群中作为"broker"角色，保存events队列共Logstash消费。
    lumberjack：使用lumberjack协议来接收数据，目前已经改为 logstash-forwarder。
    tcp:

Filters
------------------------------------------

Fillters 在Logstash处理链中担任中间处理组件。他们经常被组合起来实现一些特定的行为来，处理匹配特定规则的事件流。常见的filters如下：

    grok：解析无规则的文字并转化为有结构的格式。Grok 是目前最好的方式来将无结构的数据转换为有结构可查询的数据。有120多种匹配规则，会有一种满足你的需要。
    mutate：mutate filter 允许改变输入的文档，你可以从命名，删除，移动或者修改字段在处理事件的过程中。
        gsub: 仅对字条串类型字段有效
        split: 分割
        join: 粘合
        merge: 合并
        rename: 重命名
        update: 更新
        replace: 更新或添加
        filter/mutate 内部是有执行次序的.
    drop：丢弃一部分events不进行处理，例如：debug events。
    clone：拷贝 event，这个过程中也可以添加或移除字段。
    geoip：添加地理信息(为前台kibana图形化展示使用)
    date: 转换日志记录中的时间字符串
    dissect: 解析简单规则的文字.
    json: 处理json格式
    kv: 处理key-value格式日志
    metrics: 数值统计. meter(速率阀值检测), timer(box and whisker 异常检测)
    ruby: 随心所欲的Ruby处理
    split: 拆分事件
    elapsed: 


Outputs
------------------------------------------

outputs是logstash处理管道的最末端组件。一个event可以在处理过程中经过多重输出，但是一旦所有的outputs都执行结束，这个event也就完成生命周期。一些常用的outputs包括：

    elasticsearch：如果你计划将高效的保存数据，并且能够方便和简单的进行查询...Elasticsearch是一个好的方式。是的，此处有做广告的嫌疑,呵呵。
        flush_size: 事件间隔条数,默认500条
        idle_flush_time: idle flush 间隔时间, 默认1秒
        以上2个参数, 只要满足一个条件就促发.

    file：将event数据保存到文件中。
        在 file 下使用 codec, codec 设置无效.
        gzip: 压缩
    email: 发送邮件.
    exec: 调用命令执行. 比如, 发短信.
    nagios: 发送数据给本机的 nagios.cmd 管道命令文件
    nagios_nsca: 调用 send_nsca 命令以NSCA协议格式把数据发送给 nagios 服务器(远端或本地皆可)
    zabbix: 调用本机的 zabbix_sender 命令发送
    ganglia: 通过 UDP 协议, 发送gmetric型数据给本机/远端的 gmond 或者 gmetad
    graphite：将event数据发送到图形化组件中，一个很流行的开源存储图形化展示的组件。http://graphite.wikidot.com/。
    statsd：statsd是一个统计服务，比如技术和时间统计，通过udp通讯，聚合一个或者多个后台服务，如果你已经开始使用statsd，该选项对你应该很有用.
    stdout: 最基础和最简单的输出插件.
        codec: 一般可以省略掉后面的配置区段
        workers: 多少个线程
        加上命令行参数 -vv 运行, 查看更多详细调试信息.
    tcp: 发送网络数据. 不建议使用.
    hadoop_webhdfs: Post data to WebHDFS.
    hdfs: it import java classes like "org.apache.hadoop.fs.FileSystem" etc.



Codecs
------------------------------------------

codecs 是基于数据流的过滤器，它可以作为input，output的一部分配置。Codecs可以帮助你轻松的分割发送过来已经被序列化的数据。流行的codecs包括 json,msgpack,plain(text)。


    json：使用json格式对数据进行编码/解码
    multiline：将汇多个事件中数据汇总为一个单一的行。比如：java异常信息和堆栈信息
    log4j: 应用日志
    collectd: 收集系统性能, 在系统运行和存储信息时周期性的统计系统的相关统计信息
    netflow:


获取完整的配置信息，请参考 Logstash文档中 "plugin configuration"部分。


