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

加代理原因：kubeadm init 会去检查最新版本，及最新版本镜像是什么，镜像是否要更新。
如果本地有了相同的docker image id，就不会下载，不会更新。
这意味着，我们前几天的手工build kubeadm，达成 在 /etc/kubernetes/mainfest/ 下的 *.yaml 文件 加上 "imagePullPolicy: IfNotPresent" , 没有意义了。

::

    root@km:~#  export http_proxy="http://192.168.31.239:8118/"
    root@km:~#  export https_proxy="http://192.168.31.239:8118/"
    root@km:~#  export no_proxy="localhost,127.0.0.1,192.168.31.120,10.96.0.10,github.com,ubuntu.com"

* apt

加代理原因： apt update 要去 google.com 下载

::

    root@km:~# cat /etc/apt/apt.conf
    Acquire::http::proxy "http://192.168.31.239:8118/";
    Acquire::https::proxy "https://192.168.31.239:8118/";
    Acquire::no::proxy "ubuntu.com";
    root@km:~# 

* docker

加代理原因： 当 kubeadm init 中下载完了最新的images后，要通过 docker 专有的环境去下载镜像，并启动。这个过程，需要代理。

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


具体如下

卸载
--------------------------------------------------------

::

    jlch@kn1:~$ sudo apt remove kubelet kubeadm kubectl 
    Reading package lists... Done
    Building dependency tree       
    Reading state information... Done
    Package 'kubeadm' is not installed, so not removed
    Package 'kubelet' is not installed, so not removed
    The following packages were automatically installed and are no longer required:
    ebtables kubernetes-cni socat
    Use 'sudo apt autoremove' to remove them.
    The following packages will be REMOVED:
    kubectl
    0 upgraded, 0 newly installed, 1 to remove and 86 not upgraded.
    After this operation, 72.4 MB disk space will be freed.
    Do you want to continue? [Y/n] y
    (Reading database ... 67947 files and directories currently installed.)
    Removing kubectl (1.7.3-01) ...
    jlch@kn1:~$ 

检查
--------------------------------------------------------

::

    jlch@kn1:~$ which kubelet
    jlch@kn1:~$ which kubeadm
    jlch@kn1:~$ 
    root@kn1:~# which kubectl
    /usr/local/bin/kubectl
    root@kn1:~# kubectl version
    Client Version: version.Info{Major:"1", Minor:"7", GitVersion:"v1.7.3", GitCommit:"2c2fe6e8278a5db2d15a013987b53968c743f2a1", GitTreeState:"clean", BuildDate:"2017-08-03T07:00:21Z", GoVersion:"go1.8.3", Compiler:"gc", Platform:"linux/amd64"}
    The connection to the server localhost:8080 was refused - did you specify the right host or port?
    root@kn1:~# mv /usr/local/bin/kubectl /usr/local/bin/kubectl.v1.7.3
    root@kn1:~# which kubectl
    /usr/bin/kubectl
    root@kn1:~# 
    root@kn1:~# su - jlch
    jlch@kn1:~$ kubectl version
    Client Version: version.Info{Major:"1", Minor:"8", GitVersion:"v1.8.4", GitCommit:"9befc2b8928a9426501d3bf62f72849d5cbcd5a3", GitTreeState:"clean", BuildDate:"2017-11-20T05:28:34Z", GoVersion:"go1.8.3", Compiler:"gc", Platform:"linux/amd64"}
    The connection to the server localhost:8080 was refused - did you specify the right host or port?
    jlch@kn1:~$ 




安装docer
=============================================

看官网

根据官网安装其它
---------------------------------------------
::

    root@kn1:~# curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | apt-key add -
    OK
    root@kn1:~# cat /etc/apt/sources.list.d/kubernetes.list
    deb http://apt.kubernetes.io/ kubernetes-xenial main


安装kube*
=============================================

安装kubelet kubeadm kubectl
---------------------------------------------

看官网

::

    root@kn1:~# apt-get install -y kubelet kubeadm kubectl
    Reading package lists... Done
    Building dependency tree       
    Reading state information... Done
    The following NEW packages will be installed:
    kubeadm kubectl kubelet
    0 upgraded, 3 newly installed, 0 to remove and 87 not upgraded.
    Need to get 46.0 MB of archives.
    After this operation, 326 MB of additional disk space will be used.
    Get:1 https://packages.cloud.google.com/apt kubernetes-xenial/main amd64 kubelet amd64 1.8.4-00 [19.2 MB]
    Get:2 https://packages.cloud.google.com/apt kubernetes-xenial/main amd64 kubectl amd64 1.8.4-00 [8,612 kB]                         
    Get:3 https://packages.cloud.google.com/apt kubernetes-xenial/main amd64 kubeadm amd64 1.8.4-00 [18.1 MB]                          
    Fetched 46.0 MB in 1min 22s (557 kB/s)                                                                                             
    Selecting previously unselected package kubelet.
    (Reading database ... 67946 files and directories currently installed.)
    Preparing to unpack .../kubelet_1.8.4-00_amd64.deb ...
    Unpacking kubelet (1.8.4-00) ...
    Selecting previously unselected package kubectl.
    Preparing to unpack .../kubectl_1.8.4-00_amd64.deb ...
    Unpacking kubectl (1.8.4-00) ...
    Selecting previously unselected package kubeadm.
    Preparing to unpack .../kubeadm_1.8.4-00_amd64.deb ...
    Unpacking kubeadm (1.8.4-00) ...
    Setting up kubelet (1.8.4-00) ...
    Setting up kubectl (1.8.4-00) ...
    Setting up kubeadm (1.8.4-00) ...
    Installing new version of config file /etc/systemd/system/kubelet.service.d/10-kubeadm.conf ...
    root@kn1:~# 




kubeadm join
=============================================

看 kubeadm-join.rst
