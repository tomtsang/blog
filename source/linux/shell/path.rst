===========================
path
===========================


修改使用路径
=====================
::

    root@km:~# which kubectl
    /usr/local/bin/kubectl
    root@km:~# kubectl version
    Client Version: version.Info{Major:"1", Minor:"7", GitVersion:"v1.7.3", GitCommit:"2c2fe6e8278a5db2d15a013987b53968c743f2a1", GitTreeState:"clean", BuildDate:"2017-08-03T07:00:21Z", GoVersion:"go1.8.3", Compiler:"gc", Platform:"linux/amd64"}
    The connection to the server 192.168.31.120:6443 was refused - did you specify the right host or port?
    root@km:~# which kubeadm
    /usr/bin/kubeadm
    root@km:~# echo $PATH
    /home/jlch/.ana/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games
    root@km:~# ls /usr/bin/kube*
    /usr/bin/kubeadm  /usr/bin/kubectl  /usr/bin/kubelet
    root@km:~# /usr/bin/kubectl version
    Client Version: version.Info{Major:"1", Minor:"8", GitVersion:"v1.8.3", GitCommit:"f0efb3cb883751c5ffdbe6d515f3cb4fbe7b7acd", GitTreeState:"clean", BuildDate:"2017-11-08T18:39:33Z", GoVersion:"go1.8.3", Compiler:"gc", Platform:"linux/amd64"}
    The connection to the server 192.168.31.120:6443 was refused - did you specify the right host or port?

从上面可知，/usr/local/bin:/usr/sbin 导致 kubectl 使用了 /usr/local/bin/kubectl ,而没有使用 /usr/bin/kubectl .如果说就是想使用，怎么办？

::

    root@km:~# mv /usr/local/bin/kubectl /usr/local/bin/kubectl.delete
    root@km:~# kubectl version
    bash: /usr/local/bin/kubectl: No such file or directory
    root@km:~# 

哈哈，这就尴尬了。

::

    root@km:~# ls /usr/bin/kube*
    /usr/bin/kubeadm  /usr/bin/kubectl  /usr/bin/kubelet
    root@km:~# echo $PATH
    /home/jlch/.ana/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games
    root@km:~# ls /usr/local/bin/kube*
    /usr/local/bin/kubectl.delete
    root@km:~# 

看，这个时候，是不是很奇怪，明明应该向 /usr/bin/下找 kubectl 的。
问农总。
这个时候，切换一个 terminal , 比如shell 再登录，就可以了。

::

    root@km:~# su - root
    root@km:~# kubectl version
    Client Version: version.Info{Major:"1", Minor:"8", GitVersion:"v1.8.3", GitCommit:"f0efb3cb883751c5ffdbe6d515f3cb4fbe7b7acd", GitTreeState:"clean", BuildDate:"2017-11-08T18:39:33Z", GoVersion:"go1.8.3", Compiler:"gc", Platform:"linux/amd64"}
    The connection to the server localhost:8080 was refused - did you specify the right host or port?
    root@km:~# 

OK了。