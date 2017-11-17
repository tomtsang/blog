==================================
kubeadm-install-v1.8.3
==================================

https://kubernetes.io/docs/setup/independent/create-cluster-kubeadm/


Initializing your master
==================================

::

	kubeadm init --pod-network-cidr=10.244.0.0/16 

如果遇到类似下面错误

	[preflight] Some fatal errors occurred:
        Port 10250 is in use
        /etc/kubernetes/manifests is not empty
        /var/lib/kubelet is not empty

则，参考
https://github.com/kubernetes/kubernetes/issues/37063
运行下面命令：

::

	kubeadm reset
	systemctl start kubelet.service

之后，再次运行 

::

	kubeadm init --pod-network-cidr=10.244.0.0/16 

被墙了，出不去，我了个去，怎么办？

https://mritd.me/2016/10/29/set-up-kubernetes-cluster-by-kubeadm/#21安装包从哪来

好吧，那就去 hub.docker.com 中配置吧

找到所有要配置的 image
===========================

找 /etc/kubernetes/manifests/
------------------------------

::

    root@km:~# cd /etc/kubernetes/manifests/
    root@km:/etc/kubernetes/manifests# ls
    etcd.yaml  kube-apiserver.yaml  kube-controller-manager.yaml  kube-scheduler.yaml
    root@km:/etc/kubernetes/manifests# 
    root@km:/etc/kubernetes/manifests# cat *.yaml | grep image
        image: gcr.io/google_containers/etcd-amd64:3.0.17
        image: gcr.io/google_containers/kube-apiserver-amd64:v1.8.3
        image: gcr.io/google_containers/kube-controller-manager-amd64:v1.8.3
        image: gcr.io/google_containers/kube-scheduler-amd64:v1.8.3
    root@km:/etc/kubernetes/manifests# 

找源码
---------------------------------

https://github.com/kubernetes/kubernetes/tree/master/cmd/kubeadm

::

    root@km:~/kubernetes.20171116/cmd/kubeadm# git clone https://github.com/kubernetes/kubernetes kubernetes.20171116
    root@km:~# cd kubernetes.20171116
    root@km:~/kubernetes.20171116# cd cmd/kubeadm/
    root@km:~/kubernetes.20171116/cmd/kubeadm# grep gcr.io -r ./
    ./app/apis/kubeadm/v1alpha1/defaults.go:	DefaultImageRepository = "gcr.io/google_containers"
    ./app/phases/selfhosting/selfhosting_test.go:    image: gcr.io/google_containers/kube-apiserver-amd64:v1.7.4
    ./app/phases/selfhosting/selfhosting_test.go:        image: gcr.io/google_containers/kube-apiserver-amd64:v1.7.4
    ./app/phases/selfhosting/selfhosting_test.go:    image: gcr.io/google_containers/kube-controller-manager-amd64:v1.7.4
    ./app/phases/selfhosting/selfhosting_test.go:        image: gcr.io/google_containers/kube-controller-manager-amd64:v1.7.4
    ./app/phases/selfhosting/selfhosting_test.go:    image: gcr.io/google_containers/kube-scheduler-amd64:v1.7.4
    ./app/phases/selfhosting/selfhosting_test.go:        image: gcr.io/google_containers/kube-scheduler-amd64:v1.7.4
    ./app/phases/selfhosting/selfhosting_test.go:    - image: gcr.io/google_containers/busybox
    ./app/phases/selfhosting/selfhosting_test.go:        "image": "gcr.io/google_containers/busybox"
    ./app/phases/selfhosting/selfhosting_test.go:  - image: gcr.io/google_containers/busybox
    ./app/phases/upgrade/staticpods_test.go:imageRepository: gcr.io/google_containers
    ./app/util/template_test.go:	validTmplOut = "image: gcr.io/google_containers/pause-amd64:3.0"
    ./app/util/template_test.go:	doNothing    = "image: gcr.io/google_containers/pause-amd64:3.0"
    ./app/util/template_test.go:				ImageRepository: "gcr.io/google_containers",
    ./app/util/template_test.go:				ImageRepository: "gcr.io/google_containers",
    ./app/images/images_test.go:	gcrPrefix   = "gcr.io/google_containers"
    ./app/constants/constants.go:	DefaultCIImageRepository = "gcr.io/kubernetes-ci-images"
    root@km:~/kubernetes.20171116/cmd/kubeadm# 








