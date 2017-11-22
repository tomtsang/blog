===============================================
docker login failure
===============================================

login failure - 代理问题
===============================================

::

    root@km:~/dockerRegistry# docker login reg.jlch.com:5000
    Username (zimug): 
    Password: 
    Error response from daemon: Get https://reg.jlch.com:5000/v1/users/: EOF
    root@km:~/dockerRegistry# vi /lib/systemd/system/docker.service 


重点把 reg.jlch.com 加入 Environment 中。

::

    root@km:~# cat /lib/systemd/system/docker.service 
    [Unit]
    Description=Docker Application Container Engine
    Documentation=https://docs.docker.com
    After=network.target docker.socket firewalld.service
    Requires=docker.socket

    [Service]
    Type=notify
    # the default is not to use systemd for cgroups because the delegate issues still
    # exists and systemd currently does not support the cgroup feature set required
    # for containers run by docker
    Environment="HTTP_PROXY=http://192.168.31.239:8118/" "HTTPS_PROXY=http://192.168.31.239:8118/" "NO_PROXY=localhost,127.0.0.1,docker.io,reg.jlch.com"
    ExecStart=/usr/bin/dockerd -H fd://
    ExecReload=/bin/kill -s HUP $MAINPID
    LimitNOFILE=1048576
    # Having non-zero Limit*s causes performance problems due to accounting overhead
    # in the kernel. We recommend using cgroups to do container-local accounting.
    LimitNPROC=infinity
    LimitCORE=infinity
    # Uncomment TasksMax if your systemd version supports it.
    # Only systemd 226 and above support this version.
    TasksMax=infinity
    TimeoutStartSec=0
    # set delegate yes so that systemd does not reset the cgroups of docker containers
    Delegate=yes
    # kill only the docker process, not all processes in the cgroup
    KillMode=process

    [Install]
    WantedBy=multi-user.target
    root@km:~# 

重启docker

    root@km:~/dockerRegistry# systemctl daemon-reload
    root@km:~/dockerRegistry# systemctl restart docker.service 
    root@km:~/dockerRegistry# docker login reg.jlch.com:5000
    Username (zimug): 
    Password: 
    Login Succeeded
    root@km:~/dockerRegistry#