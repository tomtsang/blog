=============================================
kubeadm-install-ubuntu-v1.8.4
=============================================

Environment
=============================================

所有节点都要安装 kubeadm, kubelet, kubectl

安装时，全使用 root 用户。直到 kubeadm join 成功后，全使用 非root用户

::

    192.168.31.120 km master
    192.168.31.119 kn1 node
    192.168.31.118 kn2 node
    

加代理
=============================================
准备FQ网络

* 命令行

::

    root@km:~#  export http_proxy="http://192.168.31.239:8118/"
    root@km:~#  export https_proxy="http://192.168.31.239:8118/"
    root@km:~#  export no_proxy="localhost,127.0.0.1,192.168.31.120,10.96.0.10,github.com,ubuntu.com"

* apt

::

    root@km:~# cat /etc/apt/apt.conf
    Acquire::http::proxy "http://192.168.31.239:8118/";
    Acquire::https::proxy "https://192.168.31.239:8118/";
    Acquire::no::proxy "ubuntu.com";
    root@km:~# 

* docker

重点是加了 Environment="HTTP_PROXY=http://192.168.31.239:8118/" "HTTPS_PROXY=http://192.168.31.239:8118/" "NO_PROXY=localhost,127.0.0.1,docker.io"

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
    Environment="HTTP_PROXY=http://192.168.31.239:8118/" "HTTPS_PROXY=http://192.168.31.239:8118/" "NO_PROXY=localhost,127.0.0.1,docker.io"
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

卸载之前已安装的kube*
=============================================

::

    root@km:~# sudo apt remove kubelet kubeadm kubectl -y

卸载完之后检查 

::

    root@km:~# which kubeadm
    root@km:~# which kubelet
    root@km:~# which kubectl



安装docer
=============================================

看官网

安装kube*
=============================================

看官网

kubeadm join
=============================================

看 kubeadm-install-FAQ/kubeadm-join.rst
