=================================================
Upgrading kubeadm clusters from 1.7.3 to 1.8.3
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

proxy FQ 
=====================

::

    root@km:~# cat proxy.sh 
    #!/bin/bash
    NO_PROXY=localhost,127.0.0.1/8,192.168.31.1/24
    export NO_PROXY
    export http_proxy=http://192.168.31.10:1080/
    export https_proxy=http://192.168.31.10:1080/
    root@km:~# 


kubeadm upgrade apply v1.8.3
==================================
FQ后，重新来过

::

    root@km:~# cp kubeadm.v1.8.3  kubeadm
    root@km:~# mv kubeadm /usr/bin/
    root@km:~# chmod a+rx /usr/bin/kubeadm
    root@km:~# kubeadm version
    kubeadm version: &version.Info{Major:"1", Minor:"8", GitVersion:"v1.8.3", GitCommit:"f0efb3cb883751c5ffdbe6d515f3cb4fbe7b7acd", GitTreeState:"clean", BuildDate:"2017-11-08T18:27:48Z", GoVersion:"go1.8.3", Compiler:"gc", Platform:"linux/amd64"}
    root@km:~# kubeadm config upload from-flags []
    [uploadconfig] Storing the configuration used in ConfigMap "kubeadm-config" in the "kube-system" Namespace
    root@km:~# kubeadm upgrade plan
    [preflight] Running pre-flight checks
    [upgrade] Making sure the cluster is healthy:
    [upgrade/health] Checking API Server health: Healthy
    [upgrade/health] Checking Node health: All Nodes are healthy
    [upgrade/health] Checking Static Pod manifests exists on disk: All manifests exist on disk
    [upgrade/config] Making sure the configuration is correct:
    [upgrade/config] Reading configuration from the cluster...
    [upgrade/config] FYI: You can look at this config file with 'kubectl -n kube-system get cm kubeadm-config -oyaml'
    [upgrade] Fetching available versions to upgrade to
    [upgrade/versions] Cluster version: v1.7.3
    [upgrade/versions] kubeadm version: v1.8.3
    [upgrade/versions] Latest stable version: v1.8.3
    [upgrade/versions] Latest version in the v1.7 series: v1.7.10

    Components that must be upgraded manually after you've upgraded the control plane with 'kubeadm upgrade apply':
    COMPONENT   CURRENT      AVAILABLE
    Kubelet     3 x v1.7.3   v1.7.10

    Upgrade to the latest version in the v1.7 series:

    COMPONENT            CURRENT   AVAILABLE
    API Server           v1.7.3    v1.7.10
    Controller Manager   v1.7.3    v1.7.10
    Scheduler            v1.7.3    v1.7.10
    Kube Proxy           v1.7.3    v1.7.10
    Kube DNS             1.14.5    1.14.5

    You can now apply the upgrade by executing the following command:

        kubeadm upgrade apply v1.7.10

    _____________________________________________________________________

    Components that must be upgraded manually after you've upgraded the control plane with 'kubeadm upgrade apply':
    COMPONENT   CURRENT      AVAILABLE
    Kubelet     3 x v1.7.3   v1.8.3

    Upgrade to the latest stable version:

    COMPONENT            CURRENT   AVAILABLE
    API Server           v1.7.3    v1.8.3
    Controller Manager   v1.7.3    v1.8.3
    Scheduler            v1.7.3    v1.8.3
    Kube Proxy           v1.7.3    v1.8.3
    Kube DNS             1.14.5    1.14.5

    You can now apply the upgrade by executing the following command:

        kubeadm upgrade apply v1.8.3

    _____________________________________________________________________

    root@km:~# kubeadm upgrade apply v1.8.3
    [preflight] Running pre-flight checks
    [upgrade] Making sure the cluster is healthy:
    [upgrade/health] Checking API Server health: Healthy
    [upgrade/health] Checking Node health: All Nodes are healthy
    [upgrade/health] Checking Static Pod manifests exists on disk: All manifests exist on disk
    [upgrade/config] Making sure the configuration is correct:
    [upgrade/config] Reading configuration from the cluster...
    [upgrade/config] FYI: You can look at this config file with 'kubectl -n kube-system get cm kubeadm-config -oyaml'
    [upgrade/version] You have chosen to upgrade to version "v1.8.3"
    [upgrade/versions] Cluster version: v1.7.3
    [upgrade/versions] kubeadm version: v1.8.3
    [upgrade/confirm] Are you sure you want to proceed with the upgrade? [y/N]: y
    [upgrade/prepull] Will prepull images for components [kube-apiserver kube-controller-manager kube-scheduler]
    [upgrade/prepull] Prepulling image for component kube-scheduler.
    [upgrade/prepull] Prepulling image for component kube-apiserver.
    [upgrade/prepull] Prepulling image for component kube-controller-manager.
    [apiclient] Found 1 Pods for label selector k8s-app=upgrade-prepull-kube-apiserver
    [apiclient] Found 0 Pods for label selector k8s-app=upgrade-prepull-kube-controller-manager
    [apiclient] Found 1 Pods for label selector k8s-app=upgrade-prepull-kube-controller-manager
    [apiclient] Found 0 Pods for label selector k8s-app=upgrade-prepull-kube-scheduler
    [apiclient] Found 1 Pods for label selector k8s-app=upgrade-prepull-kube-scheduler
    [upgrade/prepull] Prepulled image for component kube-apiserver.
    [upgrade/prepull] Prepulled image for component kube-controller-manager.
    [upgrade/prepull] Prepulled image for component kube-scheduler.
    [upgrade/prepull] Successfully prepulled the images for all the control plane components
    [upgrade/apply] Upgrading your Static Pod-hosted control plane to version "v1.8.3"...
    [upgrade/staticpods] Writing upgraded Static Pod manifests to "/etc/kubernetes/tmp/kubeadm-upgraded-manifests025451644"
    [controlplane] Wrote Static Pod manifest for component kube-apiserver to "/etc/kubernetes/tmp/kubeadm-upgraded-manifests025451644/kube-apiserver.yaml"
    [controlplane] Wrote Static Pod manifest for component kube-controller-manager to "/etc/kubernetes/tmp/kubeadm-upgraded-manifests025451644/kube-controller-manager.yaml"
    [controlplane] Wrote Static Pod manifest for component kube-scheduler to "/etc/kubernetes/tmp/kubeadm-upgraded-manifests025451644/kube-scheduler.yaml"
    [upgrade/staticpods] Moved upgraded manifest to "/etc/kubernetes/manifests/kube-apiserver.yaml" and backed up old manifest to "/etc/kubernetes/tmp/kubeadm-backup-manifests188726187/kube-apiserver.yaml"
    [upgrade/staticpods] Waiting for the kubelet to restart the component
    [upgrade/apply] FATAL: couldn't upgrade control plane. kubeadm has tried to recover everything into the earlier state. Errors faced: [timed out waiting for the condition]
    root@km:~# 

报错了。

解决报错
=====================

加入 kubeadm init 参数
-------------------------

通过 kubeadm upgrade --help

发现，要先把之前 kubeadm init 的参数，作为configmap传进来。
回忆一下，之前是 kubeadm init --pod-network-cidr=10.244.0.0/16，所以这里把它加进去



::

    root@km:~# kubeadm config upload from-flags [--pod-network-cidr=10.244.0.0/16]
    [uploadconfig] Storing the configuration used in ConfigMap "kubeadm-config" in the "kube-system" Namespace
    root@km:~# 
    root@km:~# k get configmap kubeadm-config -n kube-system
    NAME             DATA      AGE
    kubeadm-config   1         1d
    root@km:~# k describe configmap kubeadm-config -n kube-system
    Name:		kubeadm-config
    Namespace:	kube-system
    Labels:		<none>
    Annotations:	<none>

    Data
    ====
    MasterConfiguration:
    ----
    api:
    advertiseAddress: 192.168.31.120
    bindPort: 6443
    authorizationModes:
    - Node
    - RBAC
    certificatesDir: /etc/kubernetes/pki
    cloudProvider: ""
    etcd:
    caFile: ""
    certFile: ""
    dataDir: /var/lib/etcd
    endpoints: null
    image: ""
    keyFile: ""
    imageRepository: gcr.io/google_containers
    kubernetesVersion: v1.8.3
    networking:
    dnsDomain: cluster.local
    podSubnet: ""
    serviceSubnet: 10.96.0.0/12
    nodeName: km
    token: ""
    tokenTTL: 24h0m0s
    unifiedControlPlaneImage: ""

    Events:	<none>
    root@km:~# 

呀！没把参数 --pod-network-cidr=10.244.0.0/16，加入呀（如果加入了 podSubnet: "" 会变成 podSubnet: 10.244.0.0/16 的）

看来刚刚不能加 [] 这个中括号。
再来。

::

    root@km:~# kubeadm config upload from-flags --pod-network-cidr=10.244.0.0/16
    [uploadconfig] Storing the configuration used in ConfigMap "kubeadm-config" in the "kube-system" Namespace
    root@km:~# k get cm kubeadm-config -n kube-system -o yaml
    apiVersion: v1
    data:
    MasterConfiguration: |
        api:
        advertiseAddress: 192.168.31.120
        bindPort: 6443
        authorizationModes:
        - Node
        - RBAC
        certificatesDir: /etc/kubernetes/pki
        cloudProvider: ""
        etcd:
        caFile: ""
        certFile: ""
        dataDir: /var/lib/etcd
        endpoints: null
        image: ""
        keyFile: ""
        imageRepository: gcr.io/google_containers
        kubernetesVersion: v1.8.3
        networking:
        dnsDomain: cluster.local
        podSubnet: 10.244.0.0/16
        serviceSubnet: 10.96.0.0/12
        nodeName: km
        token: ""
        tokenTTL: 24h0m0s
        unifiedControlPlaneImage: ""
    kind: ConfigMap
    metadata:
    creationTimestamp: 2017-11-14T10:04:45Z
    name: kubeadm-config
    namespace: kube-system
    resourceVersion: "10542232"
    selfLink: /api/v1/namespaces/kube-system/configmaps/kubeadm-config
    uid: 3de0d764-c923-11e7-96bf-000c299a346f
    root@km:~# 

成功。

kubeadm upgrade plan
----------------------

::

    root@km:~# kubeadm upgrade plan
    [preflight] Running pre-flight checks
    [upgrade] Making sure the cluster is healthy:
    [upgrade/health] Checking API Server health: Healthy
    [upgrade/health] Checking Node health: All Nodes are healthy
    [upgrade/health] Checking Static Pod manifests exists on disk: All manifests exist on disk
    [upgrade/config] Making sure the configuration is correct:
    [upgrade/config] Reading configuration from the cluster...
    [upgrade/config] FYI: You can look at this config file with 'kubectl -n kube-system get cm kubeadm-config -oyaml'
    [upgrade] Fetching available versions to upgrade to
    [upgrade/versions] Cluster version: v1.7.3
    [upgrade/versions] kubeadm version: v1.8.3
    [upgrade/versions] Latest stable version: v1.8.3
    [upgrade/versions] Latest version in the v1.7 series: v1.7.10

    Components that must be upgraded manually after you've upgraded the control plane with 'kubeadm upgrade apply':
    COMPONENT   CURRENT      AVAILABLE
    Kubelet     3 x v1.7.3   v1.7.10

    Upgrade to the latest version in the v1.7 series:

    COMPONENT            CURRENT   AVAILABLE
    API Server           v1.7.3    v1.7.10
    Controller Manager   v1.7.3    v1.7.10
    Scheduler            v1.7.3    v1.7.10
    Kube Proxy           v1.7.3    v1.7.10
    Kube DNS             1.14.5    1.14.5

    You can now apply the upgrade by executing the following command:

        kubeadm upgrade apply v1.7.10

    _____________________________________________________________________

    Components that must be upgraded manually after you've upgraded the control plane with 'kubeadm upgrade apply':
    COMPONENT   CURRENT      AVAILABLE
    Kubelet     3 x v1.7.3   v1.8.3

    Upgrade to the latest stable version:

    COMPONENT            CURRENT   AVAILABLE
    API Server           v1.7.3    v1.8.3
    Controller Manager   v1.7.3    v1.8.3
    Scheduler            v1.7.3    v1.8.3
    Kube Proxy           v1.7.3    v1.8.3
    Kube DNS             1.14.5    1.14.5

    You can now apply the upgrade by executing the following command:

        kubeadm upgrade apply v1.8.3

    _____________________________________________________________________

    root@km:~# 

kubeadm upgrade apply
---------------------------

如果说kubernetes的配置文件不是默认/etc/kubernetes/admin.conf，那则在这里加 --kubeconfig /root/admin.conf 就行了。
如：

::

    root@km:~# kubeadm upgrade apply v1.8.3 --kubeconfig /root/admin.conf --force

多打印一些信息吧

::

    root@km:~# kubeadm upgrade apply v1.8.3 --print-config --skip-preflight-checks
    [preflight] Skipping pre-flight checks
    [upgrade] Making sure the cluster is healthy:
    [upgrade/health] Checking API Server health: Healthy
    [upgrade/health] Checking Node health: All Nodes are healthy
    [upgrade/health] Checking Static Pod manifests exists on disk: All manifests exist on disk
    [upgrade/config] Making sure the configuration is correct:
    [upgrade/config] Reading configuration from the cluster...
    [upgrade/config] FYI: You can look at this config file with 'kubectl -n kube-system get cm kubeadm-config -oyaml'
    [upgrade/config] Configuration used:
        api:
        advertiseAddress: 192.168.31.120
        bindPort: 6443
        authorizationModes:
        - Node
        - RBAC
        certificatesDir: /etc/kubernetes/pki
        cloudProvider: ""
        etcd:
        caFile: ""
        certFile: ""
        dataDir: /var/lib/etcd
        endpoints: null
        image: ""
        keyFile: ""
        imageRepository: gcr.io/google_containers
        kubernetesVersion: v1.8.3
        networking:
        dnsDomain: cluster.local
        podSubnet: 10.244.0.0/16
        serviceSubnet: 10.96.0.0/12
        nodeName: km
        token: ef36c0.2aa43b08712e4d54
        tokenTTL: 24h0m0s
        unifiedControlPlaneImage: ""
    [upgrade/version] You have chosen to upgrade to version "v1.8.3"
    [upgrade/versions] Cluster version: v1.7.3
    [upgrade/versions] kubeadm version: v1.8.3
    [upgrade/confirm] Are you sure you want to proceed with the upgrade? [y/N]: y
    [upgrade/prepull] Will prepull images for components [kube-apiserver kube-controller-manager kube-scheduler]
    [upgrade/prepull] Prepulling image for component kube-scheduler.
    [upgrade/prepull] Prepulling image for component kube-apiserver.
    [upgrade/prepull] Prepulling image for component kube-controller-manager.
    [apiclient] Found 1 Pods for label selector k8s-app=upgrade-prepull-kube-apiserver
    [apiclient] Found 0 Pods for label selector k8s-app=upgrade-prepull-kube-controller-manager
    [apiclient] Found 1 Pods for label selector k8s-app=upgrade-prepull-kube-controller-manager
    [apiclient] Found 0 Pods for label selector k8s-app=upgrade-prepull-kube-scheduler
    [apiclient] Found 1 Pods for label selector k8s-app=upgrade-prepull-kube-scheduler
    [upgrade/prepull] Prepulled image for component kube-apiserver.
    [upgrade/prepull] Prepulled image for component kube-controller-manager.
    [upgrade/prepull] Prepulled image for component kube-scheduler.
    [upgrade/prepull] Successfully prepulled the images for all the control plane components
    [upgrade/apply] Upgrading your Static Pod-hosted control plane to version "v1.8.3"...
    [upgrade/staticpods] Writing upgraded Static Pod manifests to "/etc/kubernetes/tmp/kubeadm-upgraded-manifests076595912"
    [controlplane] Wrote Static Pod manifest for component kube-apiserver to "/etc/kubernetes/tmp/kubeadm-upgraded-manifests076595912/kube-apiserver.yaml"
    [controlplane] Wrote Static Pod manifest for component kube-controller-manager to "/etc/kubernetes/tmp/kubeadm-upgraded-manifests076595912/kube-controller-manager.yaml"
    [controlplane] Wrote Static Pod manifest for component kube-scheduler to "/etc/kubernetes/tmp/kubeadm-upgraded-manifests076595912/kube-scheduler.yaml"
    [upgrade/staticpods] Moved upgraded manifest to "/etc/kubernetes/manifests/kube-apiserver.yaml" and backed up old manifest to "/etc/kubernetes/tmp/kubeadm-backup-manifests094178183/kube-apiserver.yaml"
    [upgrade/staticpods] Waiting for the kubelet to restart the component
    [upgrade/apply] FATAL: couldn't upgrade control plane. kubeadm has tried to recover everything into the earlier state. Errors faced: [timed out waiting for the condition]
    root@km:~# 

还是失败了。
彻底失败了。


没有办法了。

新的尝试
=============

按照下面的办法再试一下吧。
http://www.youruncloud.com/docker/1_106.html

发现还是不行呀。

全新安装 v1.8.3
===================

