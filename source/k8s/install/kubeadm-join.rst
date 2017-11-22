
=============================================
kubeadm join
=============================================

Environment
=============================================

已安装 kubeadm, kubelet, kubectl

安装时，全使用 root 用户。直到 kubeadm join 成功后，全使用 非root用户

::

    192.168.31.120 km master
    192.168.31.119 kn1 node
    192.168.31.118 kn2 node
    

kubeadm join
=============================================

/etc/kubernetes/pki/ca.crt already exists
---------------------------------------------

sudo kubeadm join --token ce4253.8322cc2590378260 192.168.31.120:6443 --discovery-token-ca-cert-hash sha256:bb0b9ef27e5ffef06776ca10a87ed548cefedc703ddaf904316c87d4a7f3655d

这个来自于 master节点， kubeadm init 后的提示。

::

    jlch@kn1:~$ sudo kubeadm join --token ce4253.8322cc2590378260 192.168.31.120:6443 --discovery-token-ca-cert-hash sha256:bb0b9ef27e5ffef06776ca10a87ed548cefedc703ddaf904316c87d4a7f3655d
    [kubeadm] WARNING: kubeadm is in beta, please do not use it for production clusters.
    [preflight] Running pre-flight checks
    [preflight] WARNING: docker version is greater than the most recently validated version. Docker version: 17.05.0-ce. Max validated version: 17.03
    [preflight] Some fatal errors occurred:
        /etc/kubernetes/pki/ca.crt already exists
        /etc/kubernetes/kubelet.conf already exists
        running with swap on is not supported. Please disable swap
    [preflight] If you know what you are doing, you can skip pre-flight checks with `--skip-preflight-checks`
    jlch@kn1:~$ mkdir etc.kubernetes
    jlch@kn1:~$ mkdir etc.kubernetes/pki/
    jlch@kn1:~$ sudo mv /etc/kubernetes/pki/ca.crt etc.kubernetes/pki/
    jlch@kn1:~$ sudo mv /etc/kubernetes/kubelet.conf etc.kubernetes/

Please disable swap
---------------------------------------------

::


    jlch@kn1:~$ sudo kubeadm join --token ce4253.8322cc2590378260 192.168.31.120:6443 --discovery-token-ca-cert-hash sha256:bb0b9ef27e5ffef06776ca10a87ed548cefedc703ddaf904316c87d4a7f3655d
    [kubeadm] WARNING: kubeadm is in beta, please do not use it for production clusters.
    [preflight] Running pre-flight checks
    [preflight] WARNING: docker version is greater than the most recently validated version. Docker version: 17.05.0-ce. Max validated version: 17.03
    [preflight] Some fatal errors occurred:
        running with swap on is not supported. Please disable swap
    [preflight] If you know what you are doing, you can skip pre-flight checks with `--skip-preflight-checks`
    jlch@kn1:~$ sudo swapoff -a 

再来
---------------------------------------------

::

    jlch@kn1:~$ sudo kubeadm join --token ce4253.8322cc2590378260 192.168.31.120:6443 --discovery-token-ca-cert-hash sha256:bb0b9ef27e5ffef06776ca10a87ed548cefedc703ddaf904316c87d4a7f3655d
    [kubeadm] WARNING: kubeadm is in beta, please do not use it for production clusters.
    [preflight] Running pre-flight checks
    [preflight] WARNING: docker version is greater than the most recently validated version. Docker version: 17.05.0-ce. Max validated version: 17.03
    [discovery] Trying to connect to API Server "192.168.31.120:6443"
    [discovery] Created cluster-info discovery client, requesting info from "https://192.168.31.120:6443"
    [discovery] Requesting info from "https://192.168.31.120:6443" again to validate TLS against the pinned public key
    [discovery] Cluster info signature and contents are valid and TLS certificate validates against pinned roots, will use API Server "192.168.31.120:6443"
    [discovery] Successfully established connection with API Server "192.168.31.120:6443"
    [bootstrap] Detected server version: v1.8.4
    [bootstrap] The server supports the Certificates API (certificates.k8s.io/v1beta1)

    Node join complete:
    * Certificate signing request sent to master and response
    received.
    * Kubelet informed of new secure connection details.

    Run 'kubectl get nodes' on the master to see this machine join.
    jlch@kn1:~$ 

成功了。

回 master 检查一下。
