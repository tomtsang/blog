============================
kubeadm-kubelet-cni
============================

问题1 kubelet 没有初如化 cni
============================

现场
----------------------------

::

    root@km:~# cat k8.export.sh 
    sudo cp /etc/kubernetes/admin.conf $HOME/
    sudo chown $(id -u):$(id -g) $HOME/admin.conf
    export KUBECONFIG=$HOME/admin.conf
    root@km:~# export KUBECONFIG=$HOME/admin.conf
    root@km:~# k get nodes
    NAME      STATUS     ROLES     AGE       VERSION
    km        NotReady   master    18h       v1.8.4
    kn1       Ready      <none>    29s       v1.8.4
    kn2       Ready      <none>    29s       v1.8.4

出错了。

descibe
----------------------------

::

    root@km:~# k describe node km
    Name:               km
    Roles:              master
    Labels:             beta.kubernetes.io/arch=amd64
                        beta.kubernetes.io/os=linux
                        kubernetes.io/hostname=km
                        node-role.kubernetes.io/master=
    Annotations:        node.alpha.kubernetes.io/ttl=0
                        volumes.kubernetes.io/controller-managed-attach-detach=true
    Taints:             node-role.kubernetes.io/master:NoSchedule
    CreationTimestamp:  Tue, 21 Nov 2017 17:36:14 +0800
    Conditions:
    Type             Status  LastHeartbeatTime                 LastTransitionTime                Reason                       Message
    ----             ------  -----------------                 ------------------                ------                       -------
    OutOfDisk        False   Wed, 22 Nov 2017 11:58:45 +0800   Tue, 21 Nov 2017 17:36:14 +0800   KubeletHasSufficientDisk     kubelet has sufficient disk space available
    MemoryPressure   False   Wed, 22 Nov 2017 11:58:45 +0800   Tue, 21 Nov 2017 17:36:14 +0800   KubeletHasSufficientMemory   kubelet has sufficient memory available
    DiskPressure     False   Wed, 22 Nov 2017 11:58:45 +0800   Tue, 21 Nov 2017 17:36:14 +0800   KubeletHasNoDiskPressure     kubelet has no disk pressure
    Ready            False   Wed, 22 Nov 2017 11:58:45 +0800   Tue, 21 Nov 2017 17:36:14 +0800   KubeletNotReady              runtime network not ready: NetworkReady=false reason:NetworkPluginNotReady message:docker: network plugin is not ready: cni config uninitialized
    Addresses:
    InternalIP:  192.168.31.120
    Hostname:    km
    Capacity:
    cpu:     4
    memory:  8175088Ki
    pods:    110
    Allocatable:
    cpu:     4
    memory:  8072688Ki
    pods:    110
    System Info:
    Machine ID:                 78cb13728eba6f6c819e6dea599a5db9
    System UUID:                564D7A67-BDF7-E109-61AC-DDC9929A346F
    Boot ID:                    8f8d7bb3-abb6-4d6a-b1ee-261cd1a2cc74
    Kernel Version:             4.4.0-62-generic
    OS Image:                   Ubuntu 16.04.3 LTS
    Operating System:           linux
    Architecture:               amd64
    Container Runtime Version:  docker://Unknown
    Kubelet Version:            v1.8.4
    Kube-Proxy Version:         v1.8.4
    PodCIDR:                     10.244.0.0/24
    ExternalID:                  km
    Non-terminated Pods:         (5 in total)
    Namespace                  Name                          CPU Requests  CPU Limits  Memory Requests  Memory Limits
    ---------                  ----                          ------------  ----------  ---------------  -------------
    kube-system                etcd-km                       0 (0%)        0 (0%)      0 (0%)           0 (0%)
    kube-system                kube-apiserver-km             250m (6%)     0 (0%)      0 (0%)           0 (0%)
    kube-system                kube-controller-manager-km    200m (5%)     0 (0%)      0 (0%)           0 (0%)
    kube-system                kube-proxy-v24fg              0 (0%)        0 (0%)      0 (0%)           0 (0%)
    kube-system                kube-scheduler-km             100m (2%)     0 (0%)      0 (0%)           0 (0%)
    Allocated resources:
    (Total limits may be over 100 percent, i.e., overcommitted.)
    CPU Requests  CPU Limits  Memory Requests  Memory Limits
    ------------  ----------  ---------------  -------------
    550m (13%)    0 (0%)      0 (0%)           0 (0%)
    Events:         <none>
    root@km:~# 

重点是这一句： runtime network not ready: NetworkReady=false reason:NetworkPluginNotReady message:docker: network plugin is not ready: cni config uninitialized

那明显是 cni 插件 出错了。

找 kubeadm 哪里 与 cni 相关
----------------------------

看 /etc/systemd/system/kubelet.service.d/10-kubeadm.conf 也可以知道，这个地方是 --cni-conf-dir=/etc/cni/net.d 与 KUBELET_NETWORK_ARGS 相关

::

    root@km:~# cat /etc/systemd/system/kubelet.service.d/10-kubeadm.conf 
    [Service]
    Environment="KUBELET_KUBECONFIG_ARGS=--bootstrap-kubeconfig=/etc/kubernetes/bootstrap-kubelet.conf --kubeconfig=/etc/kubernetes/kubelet.conf"
    Environment="KUBELET_SYSTEM_PODS_ARGS=--pod-manifest-path=/etc/kubernetes/manifests --allow-privileged=true"
    Environment="KUBELET_NETWORK_ARGS=--network-plugin=cni --cni-conf-dir=/etc/cni/net.d --cni-bin-dir=/opt/cni/bin"
    Environment="KUBELET_DNS_ARGS=--cluster-dns=10.96.0.10 --cluster-domain=cluster.local"
    Environment="KUBELET_AUTHZ_ARGS=--authorization-mode=Webhook --client-ca-file=/etc/kubernetes/pki/ca.crt"
    # Value should match Docker daemon settings.
    # Defaults are "cgroupfs" for Debian/Ubuntu/OpenSUSE and "systemd" for Fedora/CentOS/RHEL
    Environment="KUBELET_CGROUP_ARGS=--cgroup-driver=cgroupfs"
    Environment="KUBELET_CADVISOR_ARGS=--cadvisor-port=0"
    Environment="KUBELET_CERTIFICATE_ARGS=--rotate-certificates=true"
    #ExecStart=
    ExecStart=/usr/bin/kubelet $KUBELET_KUBECONFIG_ARGS $KUBELET_SYSTEM_PODS_ARGS $KUBELET_NETWORK_ARGS $KUBELET_DNS_ARGS $KUBELET_AUTHZ_ARGS $KUBELET_CGROUP_ARGS $KUBELET_CADVISOR_ARGS $KUBELET_CERTIFICATE_ARGS  
    root@km:~# 

之前，只有 Kubelet 与 cni 相关，去找 kubelet 

看 kubelet 
----------------------------

::

    root@km:/opt/cni/bin# systemctl status kubelet -l
    ● kubelet.service - kubelet: The Kubernetes Node Agent
    Loaded: loaded (/lib/systemd/system/kubelet.service; enabled; vendor preset: enabled)
    Drop-In: /etc/systemd/system/kubelet.service.d
            └─10-kubeadm.conf
    Active: active (running) since Tue 2017-11-21 17:32:53 CST; 18h ago
        Docs: http://kubernetes.io/docs/
    Main PID: 12169 (kubelet)
        Tasks: 18
    Memory: 53.0M
        CPU: 37min 39.889s
    CGroup: /system.slice/kubelet.service
            └─12169 /usr/bin/kubelet --bootstrap-kubeconfig=/etc/kubernetes/bootstrap-kubelet.conf --kubeconfig=/etc/kubernetes/kubelet.conf --pod-manifest-path=/etc/kubernetes/manifests --allow-privileged=true --network-plugin=cni --cni-conf-dir=/etc/cni/net.d --cni-bin

    Nov 22 12:06:47 km kubelet[12169]: E1122 12:06:47.325019   12169 kubelet.go:2095] Container runtime network not ready: NetworkReady=false reason:NetworkPluginNotReady message:docker: network plugin is not ready: cni config uninitialized
    Nov 22 12:06:48 km kubelet[12169]: E1122 12:06:48.015039   12169 fs.go:418] Stat fs failed. Error: no such file or directory
    Nov 22 12:06:52 km kubelet[12169]: W1122 12:06:52.326529   12169 cni.go:196] Unable to update cni config: No networks found in /etc/cni/net.d
    Nov 22 12:06:52 km kubelet[12169]: E1122 12:06:52.326863   12169 kubelet.go:2095] Container runtime network not ready: NetworkReady=false reason:NetworkPluginNotReady message:docker: network plugin is not ready: cni config uninitialized
    Nov 22 12:06:56 km kubelet[12169]: W1122 12:06:56.240582   12169 helpers.go:847] eviction manager: no observation found for eviction signal allocatableNodeFs.available
    Nov 22 12:06:57 km kubelet[12169]: W1122 12:06:57.328311   12169 cni.go:196] Unable to update cni config: No networks found in /etc/cni/net.d
    Nov 22 12:06:57 km kubelet[12169]: E1122 12:06:57.328571   12169 kubelet.go:2095] Container runtime network not ready: NetworkReady=false reason:NetworkPluginNotReady message:docker: network plugin is not ready: cni config uninitialized
    Nov 22 12:07:00 km kubelet[12169]: E1122 12:07:00.897325   12169 fs.go:418] Stat fs failed. Error: no such file or directory
    Nov 22 12:07:02 km kubelet[12169]: W1122 12:07:02.329909   12169 cni.go:196] Unable to update cni config: No networks found in /etc/cni/net.d
    Nov 22 12:07:02 km kubelet[12169]: E1122 12:07:02.330121   12169 kubelet.go:2095] Container runtime network not ready: NetworkReady=false reason:NetworkPluginNotReady message:docker: network plugin is not ready: cni config uninitialized
    root@km:/opt/cni/bin# 
    root@km:/opt/cni/bin# ls /etc/cni/net.d
    root@km:/opt/cni/bin# 

Unable to update cni config: No networks found in /etc/cni/net.d, 说明这下面少了文件嘛。

加 10-flannel.conf 文件
----------------------------

那到 kn1，kn2 下看这个地方的文件是什么，copy过来试一下。

::

    root@km:/opt/cni/bin# cat <<EOF >/etc/cni/net.d/10-flannel.conf
    {
    "name": "cbr0",
    "type": "flannel",
    "delegate": {
        "isDefaultGateway": true
    }
    }
    root@km:/opt/cni/bin# cat /etc/cni/net.d/10-flannel.conf 
    {
    "name": "cbr0",
    "type": "flannel",
    "delegate": {
        "isDefaultGateway": true
    }
    }
    root@km:/opt/cni/bin# 

重启 kubelet
----------------------------

::

    root@km:/opt/cni/bin# sudo systemctl restart kubelet.service 
    root@km:/opt/cni/bin# sudo systemctl status kubelet.service 
    ● kubelet.service - kubelet: The Kubernetes Node Agent
    Loaded: loaded (/lib/systemd/system/kubelet.service; enabled; vendor preset: enabled)
    Drop-In: /etc/systemd/system/kubelet.service.d
            └─10-kubeadm.conf
    Active: active (running) since Wed 2017-11-22 12:13:17 CST; 6s ago
        Docs: http://kubernetes.io/docs/
    Main PID: 20922 (kubelet)
        Tasks: 16
    Memory: 30.3M
        CPU: 1.433s
    CGroup: /system.slice/kubelet.service
            └─20922 /usr/bin/kubelet --bootstrap-kubeconfig=/etc/kubernetes/bootstrap-kubelet.conf --kubeconfig=/etc/kubernetes/kubelet.conf --pod-manifest-path=/etc/kubernetes/manifests --allow-privileged=true --network-plugin=cni --cni-conf-dir=/etc/cni/net.d --cni-bin

    Nov 22 12:13:23 km kubelet[20922]: I1122 12:13:23.194931   20922 reconciler.go:212] operationExecutor.VerifyControllerAttachedVolume started for volume "lib-modules" (UniqueName: "kubernetes.io/host-path/60948ce8-cea3-11e7-b022-000c299a346f-lib-modules") pod "kube-proxy
    Nov 22 12:13:23 km kubelet[20922]: I1122 12:13:23.195034   20922 reconciler.go:212] operationExecutor.VerifyControllerAttachedVolume started for volume "kube-proxy-token-x4p78" (UniqueName: "kubernetes.io/secret/60948ce8-cea3-11e7-b022-000c299a346f-kube-proxy-token-x4p7
    Nov 22 12:13:23 km kubelet[20922]: I1122 12:13:23.195100   20922 reconciler.go:212] operationExecutor.VerifyControllerAttachedVolume started for volume "kubeconfig" (UniqueName: "kubernetes.io/host-path/bfaf9b679f8fe6133395d353696bb6a8-kubeconfig") pod "kube-scheduler-k
    Nov 22 12:13:23 km kubelet[20922]: I1122 12:13:23.195162   20922 reconciler.go:212] operationExecutor.VerifyControllerAttachedVolume started for volume "etcd" (UniqueName: "kubernetes.io/host-path/d76e26fba3bf2bfd215eb29011d55250-etcd") pod "etcd-km" (UID: "d76e26fba3bf
    Nov 22 12:13:23 km kubelet[20922]: I1122 12:13:23.195223   20922 reconciler.go:212] operationExecutor.VerifyControllerAttachedVolume started for volume "k8s-certs" (UniqueName: "kubernetes.io/host-path/05a0699921507cc649967b80b3386902-k8s-certs") pod "kube-apiserver-km"
    Nov 22 12:13:23 km kubelet[20922]: I1122 12:13:23.195278   20922 reconciler.go:212] operationExecutor.VerifyControllerAttachedVolume started for volume "ca-certs" (UniqueName: "kubernetes.io/host-path/05a0699921507cc649967b80b3386902-ca-certs") pod "kube-apiserver-km" (
    Nov 22 12:13:23 km kubelet[20922]: I1122 12:13:23.195337   20922 reconciler.go:212] operationExecutor.VerifyControllerAttachedVolume started for volume "k8s-certs" (UniqueName: "kubernetes.io/host-path/f561b959c5fd24759a4bcc1002f17d77-k8s-certs") pod "kube-controller-ma
    Nov 22 12:13:23 km kubelet[20922]: I1122 12:13:23.195394   20922 reconciler.go:212] operationExecutor.VerifyControllerAttachedVolume started for volume "ca-certs" (UniqueName: "kubernetes.io/host-path/f561b959c5fd24759a4bcc1002f17d77-ca-certs") pod "kube-controller-mana
    Nov 22 12:13:23 km kubelet[20922]: I1122 12:13:23.195481   20922 reconciler.go:212] operationExecutor.VerifyControllerAttachedVolume started for volume "kubeconfig" (UniqueName: "kubernetes.io/host-path/f561b959c5fd24759a4bcc1002f17d77-kubeconfig") pod "kube-controller-
    Nov 22 12:13:23 km kubelet[20922]: I1122 12:13:23.195556   20922 reconciler.go:212] operationExecutor.VerifyControllerAttachedVolume started for volume "flexvolume-dir" (UniqueName: "kubernetes.io/host-path/f561b959c5fd24759a4bcc1002f17d77-flexvolume-dir") pod "kube-con
    root@km:/opt/cni/bin# 

再查 node状态
----------------------------

::

    root@km:/opt/cni/bin# k get nodes
    NAME      STATUS    ROLES     AGE       VERSION
    km        Ready     master    18h       v1.8.4
    kn1       Ready     <none>    16m       v1.8.4
    kn2       Ready     <none>    16m       v1.8.4
    root@km:/opt/cni/bin# 

检查网络
----------------------------

事后发现，flannel 与 docker无关，与 ps 无关。

::

    root@km:~# docker ps | grep flannel
    root@km:/opt/cni/bin# ps -ef | grep flannel
    root     22240 16316  0 12:32 pts/0    00:00:00 grep --color=auto flannel
    root@km:/opt/cni/bin# 

但是 ip a 可查看有这个网络

::

    root@km:/opt/cni/bin# ip a
    1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1
        link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
        inet 127.0.0.1/8 scope host lo
        valid_lft forever preferred_lft forever
        inet6 ::1/128 scope host 
        valid_lft forever preferred_lft forever
    2: ens160: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc mq state UP group default qlen 1000
        link/ether 00:0c:29:9a:34:6f brd ff:ff:ff:ff:ff:ff
        inet 192.168.31.120/24 brd 192.168.31.255 scope global ens160
        valid_lft forever preferred_lft forever
        inet6 fe80::20c:29ff:fe9a:346f/64 scope link 
        valid_lft forever preferred_lft forever
    3: docker0: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1500 qdisc noqueue state DOWN group default 
        link/ether 02:42:05:cf:43:c1 brd ff:ff:ff:ff:ff:ff
        inet 172.17.0.1/16 scope global docker0
        valid_lft forever preferred_lft forever
        inet6 fe80::42:5ff:fecf:43c1/64 scope link 
        valid_lft forever preferred_lft forever
    4: flannel.1: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1450 qdisc noqueue state UNKNOWN group default 
        link/ether b2:6d:04:a9:38:8d brd ff:ff:ff:ff:ff:ff
        inet 10.244.0.0/32 scope global flannel.1
        valid_lft forever preferred_lft forever
        inet6 fe80::b06d:4ff:fea9:388d/64 scope link 
        valid_lft forever preferred_lft forever
    5: cni0: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1500 qdisc noqueue state DOWN group default qlen 1000
        link/ether 0a:58:0a:f4:00:01 brd ff:ff:ff:ff:ff:ff
        inet 10.244.0.1/24 scope global cni0
        valid_lft forever preferred_lft forever
        inet6 fe80::2827:4dff:fe51:9390/64 scope link 
        valid_lft forever preferred_lft forever
    root@km:/opt/cni/bin# 

game over
----------------------------