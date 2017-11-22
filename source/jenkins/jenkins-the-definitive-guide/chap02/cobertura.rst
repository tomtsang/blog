==========================
cobertura
==========================

    
设置没有成功.


env2
====================

::

    120.25.204.216 master jlchaliyun
    

配置 job
==========================

构建:
--------------------------

Invoke top-level Maven targets

    Maven Version	选择: api-cm.zhangtl.com.jenkins	
 	Goals	写入: cobertura:cobertura


如果只写入这个构建,是成功的.

但是加入下面的,就不成功了.

构建后操作:
--------------------------

Publish Cobertura Coverage Report

    Cobertura xml report pattern: gameoflife-core/target/site/cobertura/coverage.xml 
或者    Cobertura xml report pattern: **/target/site/cobertura/coverage.xml 

这样配置,都没有成功.


具体原因, 因为现在我也不熟悉,先不搞了.
