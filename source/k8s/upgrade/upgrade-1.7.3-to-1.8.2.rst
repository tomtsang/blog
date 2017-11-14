=================================================
Upgrading kubeadm clusters from 1.7.3 to 1.8.2
=================================================

Upgrading kubeadm clusters from 1.7 to 1.8

https://kubernetes.io/docs/tasks/administer-cluster/kubeadm-upgrade-1-8/


env
=====================

::

    192.168.31.120 km, master
    192.168.31.119 kn1
    192.168.31.118 kn2



下载 kubeadm
=====================

    https://dl.k8s.io/release/v1.8.2/bin/linux/amd64/kubeadm

::

    root@km:~# sudo chmod a+rx /usr/bin/kubeadm  
    root@km:~# kubeadm version
    kubeadm version: &version.Info{Major:"1", Minor:"8", GitVersion:"v1.8.2", GitCommit:"bdaeafa71f6c7c04636251031f93464384d54963", GitTreeState:"clean", BuildDate:"2017-10-24T19:38:10Z", GoVersion:"go1.8.3", Compiler:"gc", Platform:"linux/amd64"}
    root@km:~# 
    
上传
=====================

::

    root@km:~# kubeadm config upload from-file --config ./admin.conf 
    unable to decode config from "./admin.conf" [no kind "Config" is registered for version "v1"]

不是这样的 conf 文件呀。

::

    root@km:~# kubeadm config upload from-flags []
    unable to get URL "https://dl.k8s.io/release/stable-1.8.txt": Get https://storage.googleapis.com/kubernetes-release/release/stable-1.8.txt: read tcp 192.168.31.120:47118->172.217.24.16:443: read: connection reset by peer
    root@km:~# 

要FQ呀。


