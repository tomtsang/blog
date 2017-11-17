======================================
upgrade-v1.8.3-failure-install-v1.8.3 
======================================


Installing Docker
==================================

::

    root@km:~# cat /etc/apt/sources.list.d/docker.list
    deb https://apt.dockerproject.org/repo ubuntu-xenial main
    root@km:~# cat /etc/docker/daemon.json
    {  
        "registry-mirrors": ["https://0d6wdn2y.mirror.aliyuncs.com"]
    }
    root@km:~# vi /etc/docker/daemon.json
    root@km:~# cat /etc/docker/daemon.json
    {  
        "exec-opts": ["native.cgroupdriver=systemd"],
        "registry-mirrors": ["https://0d6wdn2y.mirror.aliyuncs.com"]
    }

    root@km:~# apt-get install -y curl apt-transport-https
    Reading package lists... Done
    Building dependency tree       
    Reading state information... Done
    apt-transport-https is already the newest version (1.2.24).
    curl is already the newest version (7.47.0-1ubuntu2.4).
    The following packages were automatically installed and are no longer required:
    golang-1.8-go golang-1.8-race-detector-runtime golang-1.8-src
    Use 'sudo apt autoremove' to remove them.
    0 upgraded, 0 newly installed, 0 to remove and 0 not upgraded.
    1 not fully installed or removed.
    After this operation, 0 B of additional disk space will be used.
    Setting up etcd (2.2.5+dfsg-1ubuntu1) ...
    insserv: warning: current start runlevel(s) (empty) of script `etcd' overrides LSB defaults (2 3 4 5).
    insserv: warning: current stop runlevel(s) (0 1 2 3 4 5 6) of script `etcd' overrides LSB defaults (0 1 6).

Installing kubeadm, kubelet and kubectl
===========================================================

::

    root@km:~# apt-get install -y apt-transport-https
    Reading package lists... Done
    Building dependency tree       
    Reading state information... Done
    apt-transport-https is already the newest version (1.2.24).
    The following packages were automatically installed and are no longer required:
    golang-1.8-go golang-1.8-race-detector-runtime golang-1.8-src
    Use 'sudo apt autoremove' to remove them.
    0 upgraded, 0 newly installed, 0 to remove and 0 not upgraded.
    root@km:~# cat /etc/apt/sources.list.d/kubernetes.list
    deb http://apt.kubernetes.io/ kubernetes-xenial main
    root@km:~# apt-get install -y kubelet kubeadm kubectl
    Reading package lists... Done
    Building dependency tree       
    Reading state information... Done
    kubeadm is already the newest version (1.8.3-00).
    kubectl is already the newest version (1.8.3-00).
    kubectl set to manually installed.
    kubelet is already the newest version (1.8.3-00).
    The following packages were automatically installed and are no longer required:
    golang-1.8-go golang-1.8-race-detector-runtime golang-1.8-src
    Use 'sudo apt autoremove' to remove them.
    0 upgraded, 0 newly installed, 0 to remove and 0 not upgraded.
    root@km:~# 


去下一个页面吧
https://kubernetes.io/docs/setup/independent/create-cluster-kubeadm/

kubelet 服务检查
=====================

后来发现，在这里应该测试一下 kubelet.service。

原来，虽然我 apt install kubelet , 但是， 遗留了 /etc/
