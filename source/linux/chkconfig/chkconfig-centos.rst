
================================================================
CentOS设置程序开机自启动的方法
================================================================


转自：http://www.centos.bz/2011/09/centos-setup-process-startup-boot/

在CentOS系统下，主要有两种方法设置自己安装的程序开机启动。

/etc/rc.d/rc.local
================================================================

1、把启动程序的命令添加到/etc/rc.d/rc.local文件中，比如下面的是设置开机启动httpd。

::

    #!/bin/sh
    #
    # This script will be executed *after* all the other init scripts.
    # You can put your own initialization stuff in here if you don't
    # want to do the full Sys V style init stuff.
    
    touch /var/lock/subsys/local
    /usr/local/apache/bin/apachectl start

/etc/rc.d/init.d/
================================================================
2、把写好的启动脚本添加到目录/etc/rc.d/init.d/，然后使用命令chkconfig设置开机启动。

chkconfig 功能说明：检查，设置系统的各种服务。

语法：chkconfig [--add][--del][--list][系统服务] 或 chkconfig [--level <等级代号>][系统服务][on/off/reset]

--add 添加服务
--del 删除服务
--list 查看各服务启动状态

比如我们设置自启动mysql：

::

    #将mysql启动脚本放入所有脚本运行目录/etc/rc.d/init.d中
    cp /lamp/mysql-5.0.41/support-files/mysql.server /etc/rc.d/init.d/mysqld

    #改变权限
    chown root.root /etc/rc.d/init.d/mysqld

    #所有用户都可以执行，单只有root可以修改
    chmod 755 /etc/rc.d/init.d/mysqld

    #将mysqld 放入linux启动管理体系中
    chkconfig --add mysqld

    #查看全部服务在各运行级状态
    chkconfig --list mysqld

    #只要运行级别3启动，其他都关闭
    chkconfig --levels 245 mysqld off


例如：我们把httpd的脚本写好后放进/etc/rc.d/init.d/目录，使用

::

    chkconfig --add httpd
    chkconfig httpd on

命令即设置好了开机启动。

示例1
-----------------------------------------------

我在 10.10.12.10 机器上，做了2个 redis, 让其开机自启动。



::

    [jlch@kube-master-10 ~]$ cat /etc/rc.d/init.d/redis_6379
    #!/bin/sh
    #Configurations injected by install_server below....

    EXEC=/usr/local/bin/redis-server
    CLIEXEC=/usr/local/bin/redis-cli
    PIDFILE=/var/run/redis_6379.pid
    CONF="/etc/redis/6379.conf"
    REDISPORT="6379"
    ###############
    # SysV Init Information
    # chkconfig: - 58 74
    # description: redis_6379 is the redis daemon.
    ### BEGIN INIT INFO
    # Provides: redis_6379
    # Required-Start: $network $local_fs $remote_fs
    # Required-Stop: $network $local_fs $remote_fs
    # Default-Start: 2 3 4 5
    # Default-Stop: 0 1 6
    # Should-Start: $syslog $named
    # Should-Stop: $syslog $named
    # Short-Description: start and stop redis_6379
    # Description: Redis daemon
    ### END INIT INFO


    case "$1" in
        start)
            if [ -f $PIDFILE ]
            then
                echo "$PIDFILE exists, process is already running or crashed"
            else
                echo "Starting Redis server..."
                $EXEC $CONF
            fi
            ;;
        stop)
            if [ ! -f $PIDFILE ]
            then
                echo "$PIDFILE does not exist, process is not running"
            else
                PID=$(cat $PIDFILE)
                echo "Stopping ..."
                $CLIEXEC -p $REDISPORT shutdown
                while [ -x /proc/${PID} ]
                do
                    echo "Waiting for Redis to shutdown ..."
                    sleep 1
                done
                echo "Redis stopped"
            fi
            ;;
        status)
            PID=$(cat $PIDFILE)
            if [ ! -x /proc/${PID} ]
            then
                echo 'Redis is not running'
            else
                echo "Redis is running ($PID)"
            fi
            ;;
        restart)
            $0 stop
            $0 start
            ;;
        *)
            echo "Please use start, stop, restart or status as first argument"
            ;;
    esac


示例2
-------------------------------------------------------

::

    [jlch@kube-master-10 ~]$ cat /etc/rc.d/init.d/redis_6379
    #!/bin/sh
    #Configurations injected by install_server below....

    #EXEC=/usr/local/bin/redis-server
    EXEC=/opt/redis-4.0.2-haitong/src/redis-server 
    CLIEXEC=/usr/local/bin/redis-cli
    PIDFILE=/var/run/redis_6379.pid
    #CONF="/etc/redis/6379.conf"
    CONF="/opt/redis-4.0.2-haitong/redis.conf"
    REDISPORT="6379"
    ###############
    # SysV Init Information
    # chkconfig: - 58 74
    # description: redis_6379 is the redis daemon.
    ### BEGIN INIT INFO
    # Provides: redis_6379
    # Required-Start: $network $local_fs $remote_fs
    # Required-Stop: $network $local_fs $remote_fs
    # Default-Start: 2 3 4 5
    # Default-Stop: 0 1 6
    # Should-Start: $syslog $named
    # Should-Stop: $syslog $named
    # Short-Description: start and stop redis_6379
    # Description: Redis daemon
    ### END INIT INFO


    case "$1" in
        start)
            if [ -f $PIDFILE ]
            then
                echo "$PIDFILE exists, process is already running or crashed"
            else
                echo "Starting Redis server..."
                $EXEC $CONF
            fi
            ;;
        stop)
            if [ ! -f $PIDFILE ]
            then
                echo "$PIDFILE does not exist, process is not running"
            else
                PID=$(cat $PIDFILE)
                echo "Stopping ..."
                $CLIEXEC -p $REDISPORT shutdown
                while [ -x /proc/${PID} ]
                do
                    echo "Waiting for Redis to shutdown ..."
                    sleep 1
                done
                echo "Redis stopped"
            fi
            ;;
        status)
            PID=$(cat $PIDFILE)
            if [ ! -x /proc/${PID} ]
            then
                echo 'Redis is not running'
            else
                echo "Redis is running ($PID)"
            fi
            ;;
        restart)
            $0 stop
            $0 start
            ;;
        *)
            echo "Please use start, stop, restart or status as first argument"
            ;;
    esac
    [jlch@kube-master-10 ~]$ 


/etc/rc.d/rc.sysinit
================================================================
3、把启动程序的命令添加到/etc/rc.d/rc.sysinit 文件中

脚本/etc/rc.d/rc.sysinit，完成系统服务程序启动，如系统环境变量设置、设置系统时钟、加载字体、检查加载文件系统、生成系统启动信息日志文件等

比如我们设置自启动apache：

::

    echo"/usr/local/apache2/bin/apachectl start" >> /etc/rc.d/rc.sysinit