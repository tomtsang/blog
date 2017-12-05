=============================================

=============================================

Environment
=============================================

所有节点都要安装 kubeadm, kubelet, kubectl

安装时，全使用 root 用户。直到 kubeadm join 成功后，全使用 非root用户

::

    192.168.31.120 km master
    192.168.31.119 kn1 node
    192.168.31.118 kn2 node

问题
=============================================

网络出了问题了，ping 不了 kn1, kn2 中的pod的IP


解决
=============================================



https://github.com/coreos/flannel/blob/476abd9ef37e7111a1268c41afbd7154046b492a/Documentation/troubleshooting.md#firewalls

::

    root@km:~# k get pods --all-namespaces -o wide
    NAMESPACE     NAME                                READY     STATUS    RESTARTS   AGE       IP               NODE
    default       cephfs-provisioner-cff8d95c-6tgcs   1/1       Running   2          11d       10.244.1.90      kn1
    default       mysql-0                             2/2       Running   2          22h       10.244.1.87      kn1
    default       mysql-1                             2/2       Running   0          22h       10.244.2.243     kn2
    default       mysql-2                             2/2       Running   2          22h       10.244.1.89      kn1
    default       nginx-665ff4c6f7-rgrz6              1/1       Running   1          1d        10.244.1.88      kn1
    default       nginx-665ff4c6f7-tmfft              1/1       Running   0          1d        10.244.2.240     kn2
    kube-system   etcd-km                             1/1       Running   8          13d       192.168.31.120   km
    kube-system   kube-apiserver-km                   1/1       Running   4          13d       192.168.31.120   km
    kube-system   kube-controller-manager-km          1/1       Running   4          13d       192.168.31.120   km
    kube-system   kube-dns-545bc4bfd4-2p847           3/3       Running   391        13d       10.244.2.231     kn2
    kube-system   kube-flannel-ds-fz28b               1/1       Running   0          5h        192.168.31.120   km
    kube-system   kube-flannel-ds-mfmvm               1/1       Running   0          5h        192.168.31.118   kn2
    kube-system   kube-flannel-ds-s5ps6               1/1       Running   1          5h        192.168.31.119   kn1
    kube-system   kube-proxy-hlbc5                    1/1       Running   9          13d       192.168.31.118   kn2
    kube-system   kube-proxy-v24fg                    1/1       Running   4          13d       192.168.31.120   km
    kube-system   kube-proxy-wxjg8                    1/1       Running   6          13d       192.168.31.119   kn1
    kube-system   kube-scheduler-km                   1/1       Running   4          13d       192.168.31.120   km


通过查看 kn1 下的 /var/log/syslog

可以知道是 flannel 的问题。

从 下面这个地方知道

https://github.com/coreos/flannel/blob/476abd9ef37e7111a1268c41afbd7154046b492a/Documentation/troubleshooting.md#firewalls

要打开 UDP port 8472

那就在 km, kn1, kn2, 三机子上 

::

    ufw allow 8472/udp 

然后再检查，就ping成功了。

::

    root@km:~# ping 10.244.2.231
    PING 10.244.2.231 (10.244.2.231) 56(84) bytes of data.
    64 bytes from 10.244.2.231: icmp_seq=1 ttl=63 time=0.463 ms
    64 bytes from 10.244.2.231: icmp_seq=2 ttl=63 time=0.403 ms
    ^C
    --- 10.244.2.231 ping statistics ---
    2 packets transmitted, 2 received, 0% packet loss, time 999ms
    rtt min/avg/max/mdev = 0.403/0.433/0.463/0.030 ms
    root@km:~# ping 10.244.1.90
    PING 10.244.1.90 (10.244.1.90) 56(84) bytes of data.
    64 bytes from 10.244.1.90: icmp_seq=1 ttl=63 time=0.435 ms
    64 bytes from 10.244.1.90: icmp_seq=2 ttl=63 time=0.410 ms
    ^C
    --- 10.244.1.90 ping statistics ---
    2 packets transmitted, 2 received, 0% packet loss, time 999ms
    rtt min/avg/max/mdev = 0.410/0.422/0.435/0.024 ms
    root@km:~# 