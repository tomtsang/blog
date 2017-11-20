==========================
javadoc
==========================



配置 job
==========================

构建:

Invoke top-level Maven targets

    Maven Version	选择: api-cm.zhangtl.com.jenkins	
 	Goals	写入: javadoc:javadoc 不能写 javadoc:javadoc -o, 加了 -o 会报错.




没有 plugin,报错
=========================
在 chap 02 中的 更多的报告--显示javadoc 小节中,要配置 javadoc

因为继前小节, 我们的环境是由 master, 调用 slave 模式进行, 因为我们的 slave 没有安装 javadoc, 所以直接报错误了.

错误如下:

::

    [INFO] ------------------------------------------------------------------------
    [ERROR] No plugin found for prefix 'javadoc' in the current project and in the plugin groups [org.apache.maven.plugins, org.codehaus.mojo] available from the repositories [local (/home/jenkins/.m2/repository), central (https://repo.maven.apache.org/maven2)] -> [Help 1]
    [ERROR] 
    [ERROR] To see the full stack trace of the errors, re-run Maven with the -e switch.
    [ERROR] Re-run Maven using the -X switch to enable full debug logging.
    [ERROR] 
    [ERROR] For more information about the errors and possible solutions, please read the following articles:
    [ERROR] [Help 1] http://cwiki.apache.org/confluence/display/MAVEN/NoPluginFoundForPrefixException
    Build step 'Invoke top-level Maven targets' marked build as failure
    Archiving artifacts
    Recording test results
    Finished: FAILURE

一看就明白了, No plugin found for prefix 'javadoc' in the current project and in the plugin groups 没有plugin. 

安装 javadoc
===========================

这里有一个疑问:
是下面中的哪一个呢?

* slave 安装 javadoc 
* master 下的 jenkins 安装javadoc 插件

我选择先在 master 中安装javadoc 插件.

通过浏览器, 安装完了 javadoc 插件后, 再构建,不行,一样的错误.
这个也是可以理解的,因为,master是委托 slave 去做具体的事务的,所以,master安装了javadoc, 肯定是没有用的嘛.

好吧,去 slave安装 javadoc.

::

    [jenkins@test_240 game-of-life]$ sudo yum search openjdk-javadoc
    [jenkins@test_240 game-of-life]$ sudo yum install java-1.8.0-openjdk-javadoc -y

成功了.

然后,直接在 slave 上 ,在工程中运行 mvn javadoc:javadoc , 在3个子文件夹,成功了. 在 gameoflife-web 下失败了.
那就 cd gameoflife-web 后,单独跑一下, 果然是失败了.


::

    [jenkins@test_240 gameoflife-web]$ /home/jenkins/software/apache-maven-3.5.2/bin/mvn javadoc:javadoc
    [INFO] Scanning for projects...
    [WARNING]
    [WARNING] Some problems were encountered while building the effective model for com.wakaleo.gameoflife:gameoflife-web:war:1.0-SNAPSHOT
    [WARNING] Reporting configuration should be done in <reporting> section, not in maven-site-plugin <configuration> as reportPlugins parameter.
    [WARNING]
    [WARNING] It is highly recommended to fix these problems because they threaten the stability of your build.
    [WARNING]
    [WARNING] For this reason, future Maven versions might no longer support building such malformed projects.
    [WARNING]
    [INFO]
    [INFO] ------------------------------------------------------------------------
    [INFO] Building gameoflife-web 1.0-SNAPSHOT
    [INFO] ------------------------------------------------------------------------
    [INFO]
    [INFO] >>> maven-javadoc-plugin:3.0.0-M1:javadoc (default-cli) > generate-sources @ gameoflife-web >>>
    [WARNING] The POM for com.wakaleo.gameoflife:gameoflife-core:jar:1.0-SNAPSHOT is missing, no dependency information available
    [INFO] ------------------------------------------------------------------------
    [INFO] BUILD FAILURE
    [INFO] ------------------------------------------------------------------------
    [INFO] Total time: 1.062 s
    [INFO] Finished at: 2017-11-20T16:42:10+08:00
    [INFO] Final Memory: 12M/150M
    [INFO] ------------------------------------------------------------------------
    [ERROR] Failed to execute goal on project gameoflife-web: Could not resolve dependencies for project com.wakaleo.gameoflife:gameoflife-web:war:1.0-SNAPSHOT: Could not find artifact com.wakaleo.gameoflife:gameoflife-core:jar:1.0-SNAPSHOT -> [Help 1]
    [ERROR]
    [ERROR] To see the full stack trace of the errors, re-run Maven with the -e switch.
    [ERROR] Re-run Maven using the -X switch to enable full debug logging.
    [ERROR]
    [ERROR] For more information about the errors and possible solutions, please read the following articles:
    [ERROR] [Help 1] http://cwiki.apache.org/confluence/display/MAVEN/DependencyResolutionException
    [jenkins@test_240 gameoflife-web]$


这样,就是说, 是工程自己的问题了.
那我们先把这个 gameoflife-web 去掉了先吧.

gameoflife-web 去掉
==========================


怎么去掉呢?

把 pom.xml 中的 gameoflife-web 注释掉. (大约在212行).

::

    [jenkins@test_240 game-of-life]$ ls
    gameoflife-acceptance-tests  gameoflife-build  gameoflife-core  gameoflife-deploy  gameoflife-web  infinitest.filters  pom.xml  README.markdown  target
    [jenkins@test_240 game-of-life]$ vi pom.xml
    [jenkins@test_240 game-of-life]$ grep gameoflife-web ./pom.xml
            <!--<module>gameoflife-web</module>-->
    [jenkins@test_240 game-of-life]$

再一跑, 成功了.

配置 job
===========================

既然成功了. 那么, 回 浏览器配置吧..

构建后操作

Publish Javadoc

    Javadoc directory: gameoflife-core/target/site/apidocs
    勾选上 Retain Javadoc for each successful build


再构建
===========================

构建成功. 这时, 在主页面, 会多一个 Javadoc 的连接按钮. 可以点击, 看一下效果.


game over








