=============================
logstash 和filebeat 是什么关系
=============================


https://www.zhihu.com/question/54058964

logstash 和filebeat都具有日志收集功能，filebeat更轻量，占用资源更少，但logstash 具有filter功能，能过滤分析日志。一般结构都是filebeat采集日志，然后发送到消息队列，redis，kafaka。然后logstash去获取，利用filter功能过滤分析，然后存储到elasticsearch中

