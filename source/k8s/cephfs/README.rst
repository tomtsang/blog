===============================
k8s cephfs README
===============================

本次, k8s cephfs 结合, 前前后后, 花了差不多1个月的时间, 终于是有结果了. 

主要大过程是:

::

    安装 k8s.v1.7.3
        flannel 网络 成功
    安装 ceph
    安装 cephfs
    然后 k8s + cephfs 
        make && make push
        deployment.yaml 失败
        docker 成功
        test-pod 成功
        检查 SUCCESS 失败
        Input/Output Error 发现不对, 升级内核
    升级 k8s.v1.7.3 至 k8s.v1.8.3
        升级失败
    科学上网
        XX-net 失败
        加速度 成功
    全新安装 k8s.v1.8.4
        remove kube* 成功
        install kube* 成功
        init 成功
        apply -f kube-flannel.yml
    然后 k8s + cephfs
        docker 成功, 关闭
        重新设置 cni0
        secret 成功
        configmap.yaml 成功
        deployment.yaml 成功
        class.yaml 成功
        claim.yaml 成功
        test-pod.yaml 成功
    检查 mount
        mount , ceph-client 失败
        mount , kn1 成功
    Game over!
