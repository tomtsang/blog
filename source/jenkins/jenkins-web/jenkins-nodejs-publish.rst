=================================
jenkins-nodejs-publish
=================================

使用Jenkins自动部署nodejs应用

env1
=================================

::

    120.25.204.216 jenkins master
    10.10.15.181 jenkins remote-server


env2
=================================

::

    10.10.15.181 jenkins master
    10.10.15.181 jenkins remote-server

reference
=================================

http://blog.csdn.net/frank_good/article/details/68930286

安装插件
==================================

系统管理 -> 插件管理 

主要是安装  Publish Over SSH, SSH plugin

::

    GIT client 
    GIT 
    Git server  
    GitHub API  
    GitHub  
    SSH Credentials  
    SSH  
    Publish Over SSH

安装完成后，重新启动 jenkins。
如果无法自动重启，可以输入 '/restart' 在地址栏，即可手动重启。

添加remote计算机
==================================

系统管理 -> 系统设置


SSH remote hosts
---------------------------------

增加remote server
这个是增加ssh remote 插件的服务器

点击 新增

Hostname	写入 api-ct.zhangtl.com
 	Port	写入 6000
 	Credentials	点击 Add , 选择 jenkins (这里其实是之前配置的一个credential) 

点击 Check connection, 如显示 Successfull connection, 表示连接成功

增加远程拷贝文件的remote server

Publish over SSH
---------------------------------

Jenkins SSH Key	
 	Passphrase 选择 密码, ••••••••••••••••••••••••••••••••••••••••••••••••••••••••••
 	Path to key	写入 /var/lib/jenkins/.ssh/id_rsa 这个就是 master 的 私钥
 	Key	这里可以不填写. 如果说 上面没有写私钥的地址,那这里就直接把 私钥 复制到这里.也就是2者写1就行了
 	Disable exec	不动, 不打勾
 	SSH Servers	
SSH Server 这个就是 远程的server 的信息了
 	Name	写入 api-ct.zhanglt.com.6000.jenkins, 这个是给自己看的
 	Hostname	写入 api-ct.zhangtl.com 这个是要解析的,也就是IP了
 	Username	写入 jenkins  用户名
 	Remote Directory	写入 /var/lib/jenkins   这里我写的是 jenkins 用户的 $HOME 目录, 

点击 Test Configuration, 如显示 Success 表示成功了.

创建项目
==================================

我现在在github上建了一个nodejs的helloworld项目：
https://github.com/tomtsang/jenkinsNodejs 
是 fork 于 
https://github.com/DoubleSpout/jenkinsNodejs

我们需要在jenkins中也创建一个相应的项目来，依次点击：新建 ->  取名为 nodejsPublish

进入项目，点击配置，我们就可以对这个项目进行配置，让它自动部署

General
----------------------------------------------------------------
Github project

Project url 写入 https://github.com/tomtsang/jenkinsNodejs/

源码管理
----------------------------------------------------------------

设置 git 仓库路径
 Git / Repositories	
 	Repository URL	写入 https://github.com/tomtsang/jenkinsNodejs/
 	Credentials	可以不选择, 选择也可以, 我是 Add, 选择 jenkins 这个.

因为是公共仓库，所以可以不需要验证用户名，密码

构建
----------------------------------------------------------------

Execute shell
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

构建完毕之后，将代码打包保存, 所以增加一个构建完毕的shell脚本执行

具体的是, 打包代码，然后移动到workspace目录下

点击 增加构建步骤, 选择 Execute shell, command 写入 

::

    #rm -rf /var/lib/jenkins/jobs/nodejsPublish/nodejsPublish.tar.gz
    #tar -zcvf /tmp/nodejsPublish.tar.gz -C /var/lib/jenkins/jobs/nodejsPublish/workspace/ .
    tar -zcvf /tmp/nodejsPublish.tar.gz -C /var/lib/jenkins/workspace/nodejsPublish/ .
    mv /tmp/nodejsPublish.tar.gz /var/lib/jenkins/workspace/nodejsPublish/
    echo {$HOME} 
    echo "Execute shell"

Execute shell script on remote host using ssh
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
接着我们需要连接到部署服务器上，将原来的服务暂时暂停，删除上一次部署的压缩包，选择添加远程shell脚本执行，
如:将pm2服务停止，这里大家可以根据自己的要求写脚本

点击 增加构建步骤, 选择 Execute shell script on remote host using ssh, 

SSH site 选择 jenkins@api-ct.zhangtl.com:6000 , 这个选择项目也就是上面配置的Publish over SSH/SSH Server的结果 

command 写入 

::

    pm2 stop all
    pm2 delete all
    echo {$HOME}
    echo  "Execute shell script on remote host using ssh"

Send files or execute commands over ssh
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
最后我们要把打包好的代码发送到服务器上，然后执行安装依赖包操作，并且启动nodejs服务，

也就是
选择通过ssh发送文件并执行命令到远程服务器, 然后 解压代码，执行依赖包安装，然后通过pm2启动nodejs服务。

点击 增加构建步骤, 选择 Send files or execute commands over ssh,

SSH Publishers / SSH Server

Name 
    选择 api-ct.zhanglt.com.6000.jenkins

Transfers / Transfer Set
 	Source files	写入 nodejsPublish.tar.gz, Source files的默认当前路径就是workspaces，也就是 /var/lib/jenkins/workspace/, 这里写入的是它的相对路径
 	Remove prefix	不写
 	Remote directory	写入 tom/nodejsPublish/ , Remote directory的当前路径就是之前添加远程服务器设置的，我当时设置为/var/lib/jenkins/, 同样写相对路径
 	Exec command 写入 如下:

::

    echo {$HOME}
    ls -l
    ls tom/nodejsPublish/

这个 Exec command 下方会有红色提示(但是我发现没有关系, 不受影响, 这是为什么? 求赐教), 提示如下:

::

    Either Source files, Exec command or both must be supplied	

开始构建
==================================
配置好上述命令之后，我们点击保存，然后点击立即构建，

出现蓝色的球，就表示构建成功，
红色的球表示构建失败，
黄色的球表示执行构建命令失败，

不论构建成功还是失败，我们可以通过查看控制台输出看到构建的结果

如果我们有多台设备，就只需要配置一次，然后以后每次有代码变更，手动点击立即构建即可自动部署到目标服务器，免去我们枯草的重复劳动。

当然其他语言，例如python，php都是可以这样来部署的。


jenkins log
==================================

我本次的 控制台输出 如下

::

    Started by user jlch
    Building on master in workspace /var/lib/jenkins/workspace/nodejsPublish
    > /usr/bin/git rev-parse --is-inside-work-tree # timeout=10
    Fetching changes from the remote Git repository
    > /usr/bin/git config remote.origin.url https://github.com/tomtsang/jenkinsNodejs/ # timeout=10
    Fetching upstream changes from https://github.com/tomtsang/jenkinsNodejs/
    > /usr/bin/git --version # timeout=10
    using GIT_SSH to set credentials 
    > /usr/bin/git fetch --tags --progress https://github.com/tomtsang/jenkinsNodejs/ +refs/heads/*:refs/remotes/origin/*
    > /usr/bin/git rev-parse refs/remotes/origin/master^{commit} # timeout=10
    > /usr/bin/git rev-parse refs/remotes/origin/origin/master^{commit} # timeout=10
    Checking out Revision f48169f7462594f445a52f64e4c0a36376c089ae (refs/remotes/origin/master)
    > /usr/bin/git config core.sparsecheckout # timeout=10
    > /usr/bin/git checkout -f f48169f7462594f445a52f64e4c0a36376c089ae
    Commit message: "tangren"
    > /usr/bin/git rev-list f48169f7462594f445a52f64e4c0a36376c089ae # timeout=10
    [nodejsPublish] $ /bin/sh -xe /tmp/jenkins1008771630424029501.sh
    + tar -zcvf /tmp/nodejsPublish.tar.gz -C /var/lib/jenkins/workspace/nodejsPublish/ .
    ./
    ./.git/
    ./.git/config
    ./.git/logs/
    ./.git/logs/refs/
    ./.git/logs/refs/remotes/
    ./.git/logs/refs/remotes/origin/
    ./.git/logs/refs/remotes/origin/master
    ./.git/logs/HEAD
    ./.git/index
    ./.git/refs/
    ./.git/refs/remotes/
    ./.git/refs/remotes/origin/
    ./.git/refs/remotes/origin/master
    ./.git/refs/heads/
    ./.git/refs/tags/
    ./.git/hooks/
    ./.git/hooks/pre-rebase.sample
    ./.git/hooks/pre-applypatch.sample
    ./.git/hooks/update.sample
    ./.git/hooks/commit-msg.sample
    ./.git/hooks/prepare-commit-msg.sample
    ./.git/hooks/pre-push.sample
    ./.git/hooks/applypatch-msg.sample
    ./.git/hooks/post-update.sample
    ./.git/hooks/pre-commit.sample
    ./.git/objects/
    ./.git/objects/fa/
    ./.git/objects/fa/296423e71ae782c22b374b6eaa07aa187bef6e
    ./.git/objects/89/
    ./.git/objects/89/8d1d090bd8da654b1fb6f3672a683e3f5942dd
    ./.git/objects/00/
    ./.git/objects/00/693b7e12fe8eebfeb8a0a50ecbc41c34f0ffe1
    ./.git/objects/64/
    ./.git/objects/64/e43ba90329a2db9f03f5fd60b825a8c69598f9
    ./.git/objects/fb/
    ./.git/objects/fb/0ea539f22a2cf415e2e9fbf2f52f6f847abfdd
    ./.git/objects/bd/
    ./.git/objects/bd/f6ae0c7f31e52c1f6a10d9ddf58e36d904a30d
    ./.git/objects/f4/
    ./.git/objects/f4/8169f7462594f445a52f64e4c0a36376c089ae
    ./.git/objects/ee/
    ./.git/objects/ee/84c56b0cac714754952079a4fecbdf17afd0de
    ./.git/objects/a2/
    ./.git/objects/a2/949bf24f3c31110982db73ee5ae5f1890201a0
    ./.git/objects/35/
    ./.git/objects/35/a95e2f5f67bab140269b1db1b34fc3cd46fdd5
    ./.git/objects/2b/
    ./.git/objects/2b/896fc37a879c86b389a1ad1dadbe9b8fb70359
    ./.git/objects/cb/
    ./.git/objects/cb/462c01a49216ca83b066c9455165070451e1e5
    ./.git/objects/f0/
    ./.git/objects/f0/224f93301c34c512c8d479e8a5fc9fc02efa13
    ./.git/objects/90/
    ./.git/objects/90/61189f9601ff1132a8101ec5bbea9d1ce2a998
    ./.git/objects/29/
    ./.git/objects/29/561a10c6484f7ef37c02abc444988a2f1c4f7d
    ./.git/objects/1c/
    ./.git/objects/1c/3f756b6ee0fc0c34333d9e4bdcd5b926e5c722
    ./.git/objects/08/
    ./.git/objects/08/5df15cd44e08c07affbe11cf2892ce58f09da2
    ./.git/objects/08/cf9368a5991917d492bae266a11caa20e74513
    ./.git/objects/87/
    ./.git/objects/87/448347cfe603b42c9fd63c57bb75ecd6cf8d06
    ./.git/objects/bc/
    ./.git/objects/bc/c46eb7e365aed7183db000c844bed2ac261f9a
    ./.git/objects/d2/
    ./.git/objects/d2/b2b3aad1755ab667cb880b6a8f2f153be2ea59
    ./.git/objects/info/
    ./.git/objects/14/
    ./.git/objects/14/fe3b780e02cb14e5247872259366dfb36047c9
    ./.git/objects/b7/
    ./.git/objects/b7/e189f6f999c05ef212c73985620004ce7dba6a
    ./.git/objects/b6/
    ./.git/objects/b6/a0ab9ce05dfeb67cdca62652462a2384b91662
    ./.git/objects/pack/
    ./.git/objects/c3/
    ./.git/objects/c3/096e2191d287ed22242d8f8cafcc58e6c01c50
    ./.git/objects/5d/
    ./.git/objects/5d/3a0f97ba1ebde0face66ac632000ce373bbbe4
    ./.git/objects/df/
    ./.git/objects/df/3e49b23675c87ae38b2561e02f468b3aa4465c
    ./.git/objects/a7/
    ./.git/objects/a7/56ebd1d4efc084af35056c7e2ee4e38521e8a0
    ./.git/objects/c5/
    ./.git/objects/c5/4d43514be4c21e13a5b46970a10391e5776533
    ./.git/objects/ac/
    ./.git/objects/ac/ad1d8e4c144059129f335d7672f1bdf85ecdb0
    ./.git/objects/8e/
    ./.git/objects/8e/c113bd2cc0054b69a361cc3c0d5a8813aa7c4b
    ./.git/objects/47/
    ./.git/objects/47/a836795a1aa6ef23612c12a164256044af393c
    ./.git/objects/1d/
    ./.git/objects/1d/0960dc4eb0aced6133d0b79613f14afe272735
    ./.git/objects/25/
    ./.git/objects/25/3f1497de9565b220ed807c2e68e00509142d9d
    ./.git/branches/
    ./.git/info/
    ./.git/info/exclude
    ./.git/description
    ./.git/HEAD
    ./.git/FETCH_HEAD
    ./applist.json
    ./app.js
    ./package.json
    ./README.md
    + mv /tmp/nodejsPublish.tar.gz /var/lib/jenkins/workspace/nodejsPublish/
    + echo '{/var/lib/jenkins}'
    {/var/lib/jenkins}
    + echo 'Execute shell'
    Execute shell
    [SSH] script:
    HOME="/var/lib/jenkins"

    pm2 stop all
    pm2 delete all
    echo {$HOME}
    echo  "Execute shell script on remote host using ssh"

    [SSH] executing...
    [PM2][WARN] No process found
    [PM2][WARN] No process found
    ┌──────────┬────┬──────┬─────┬────────┬─────────┬────────┬────────┬──────────┐
    │ App name │ id │ mode │ pid │ status │ restart │ uptime │ memory │ watching │
    └──────────┴────┴──────┴─────┴────────┴─────────┴────────┴────────┴──────────┘
    Use `pm2 show <id|name>` to get more details about an app
    ┌──────────┬────┬──────┬─────┬────────┬─────────┬────────┬────────┬──────────┐
    │ App name │ id │ mode │ pid │ status │ restart │ uptime │ memory │ watching │
    └──────────┴────┴──────┴─────┴────────┴─────────┴────────┴────────┴──────────┘
    Use `pm2 show <id|name>` to get more details about an app
    {/var/lib/jenkins}
    Execute shell script on remote host using ssh

    [SSH] completed
    [SSH] exit-status: 0

    SSH: Connecting from host [jlch_web_001]
    SSH: Connecting with configuration [api-ct.zhanglt.com.6000.jenkins] ...
    SSH: EXEC: STDOUT/STDERR from command [echo {/var/lib/jenkins}
    ls -l
    ls tom/nodejsPublish/] ...
    {/var/lib/jenkins}
    total 100
    drwxr-xr-x  6 jenkins jenkins 4096 Nov 16 12:51 caches
    -rw-r--r--  1 jenkins jenkins 1592 Nov 17 09:59 config.xml
    -rw-r--r--  1 jenkins jenkins 2522 Nov 16 09:09 credentials.xml
    drwxr-xr-x  3 jenkins jenkins   15 Nov 16 15:14 fingerprints
    -rw-r--r--  1 jenkins jenkins  159 Nov 15 14:46 hudson.model.UpdateCenter.xml
    -rw-r--r--  1 jenkins jenkins 1254 Nov 16 14:15 hudson.plugins.emailext.ExtendedEmailPublisher.xml
    -rw-r--r--  1 jenkins jenkins  370 Nov 17 09:59 hudson.plugins.git.GitTool.xml
    -rw-r--r--  1 jenkins jenkins  173 Nov 17 09:59 hudson.plugins.gradle.Gradle.xml
    -rw-r--r--  1 jenkins jenkins  145 Nov 17 09:59 hudson.tasks.Ant.xml
    -rw-r--r--  1 jenkins jenkins  374 Nov 17 09:59 hudson.tasks.Maven.xml
    -rw-------  1 jenkins jenkins 1712 Nov 15 14:46 identity.key.enc
    -rw-r--r--  1 jenkins jenkins   94 Nov 15 14:46 jenkins.CLI.xml
    -rw-r--r--  1 jenkins jenkins    6 Nov 15 15:34 jenkins.install.InstallUtil.lastExecVersion
    -rw-r--r--  1 jenkins jenkins    6 Nov 15 15:34 jenkins.install.UpgradeWizard.state
    -rw-r--r--  1 jenkins jenkins  247 Nov 17 09:59 jenkins.mvn.GlobalMavenConfig.xml
    drwxr-xr-x  6 jenkins jenkins   86 Nov 16 14:33 jobs
    drwxr-xr-x  4 jenkins jenkins   31 Nov 15 16:44 logs
    -rw-r--r--  1 jenkins jenkins  907 Nov 15 14:46 nodeMonitors.xml
    drwxr-xr-x  4 jenkins jenkins   56 Nov 15 18:06 nodes
    -rw-r--r--  1 jenkins jenkins  298 Nov 17 09:59 org.jenkinsci.plugins.docker.commons.tools.DockerTool.xml
    -rw-r--r--  1 jenkins jenkins  255 Nov 17 09:59 org.jenkinsci.plugins.gitclient.JGitApacheTool.xml
    -rw-r--r--  1 jenkins jenkins  243 Nov 17 09:59 org.jenkinsci.plugins.gitclient.JGitTool.xml
    -rw-r--r--  1 jenkins jenkins   46 Nov 16 14:11 org.jenkinsci.plugins.workflow.flow.FlowExecutionList.xml
    drwxr-xr-x 73 jenkins jenkins 8192 Nov 15 14:51 plugins
    -rw-r--r--  1 jenkins jenkins   64 Nov 15 14:45 secret.key
    -rw-r--r--  1 jenkins jenkins    0 Nov 15 14:45 secret.key.not-so-secret
    drwx------  4 jenkins jenkins 4096 Nov 16 09:55 secrets
    drwxrwxr-x  3 jenkins jenkins   67 Nov 16 16:15 software
    drwxrwxr-x  3 jenkins jenkins   26 Nov 25 08:17 tom
    drwxr-xr-x  2 jenkins jenkins 4096 Nov 24 14:46 updates
    drwxr-xr-x  2 jenkins jenkins   23 Nov 15 14:46 userContent
    drwxr-xr-x  3 jenkins jenkins   17 Nov 15 15:34 users
    drwxr-xr-x  2 jenkins jenkins    6 Nov 15 14:50 workflow-libs
    nodejsPublish.tar.gz
    SSH: EXEC: completed after 201 ms
    SSH: Disconnecting configuration [api-ct.zhanglt.com.6000.jenkins] ...
    SSH: Transferred 1 file(s)
    Build step 'Send files or execute commands over SSH' changed build result to SUCCESS
    Finished: SUCCESS   

game over