==================================
kubeadm-install-v1.8.3
==================================

https://kubernetes.io/docs/setup/independent/create-cluster-kubeadm/

env
====================================

::

    192.168.31.120 km master
    192.168.31.119 kn1 node
    192.168.31.118 kn2 node


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

最后，居然什么也没有找到。哎呀。好吧。

回到  https://gitee.com/tomt/tom_docker_registry_push_pull.git ，看了一下，也就是这4个需要更新一下。

::

    gcr.io/google_containers/kube-apiserver-amd64:v1.8.3                
    gcr.io/google_containers/kube-proxy-amd64:v1.8.3              
    gcr.io/google_containers/kube-scheduler-amd64:v1.8.3              
    gcr.io/google_containers/kube-controller-manager-amd64:v1.8.3   

好了。明确了image, 那就做吧。


github hub.docker.io 配置 image
---------------------------------

注意，在 hub.docker.io 下，自动构建仓库，时，取 github的仓库，对这个仓库名称有限制

* 不能大于3个 “-” 号
* 长度不能太长

这样呢，gcr.io/google_containers/kube-controller-manager-amd64:v1.8.3， 在github上本来要创建名称为 dockerlibraryk8s-kube-controller-manager-amd64 ，那这个不符合要求的。
怎么办？改个名称咯。
我把它修改成 dockerlibraryk8s-kube-cm-amd64了。
记得最后，pull 下来的时候，docker tag 一下。

::

    root@km:~/tom_k8s_kubernetes_install/k8/docker-library-k8s-v1.8.3# ls
    dockerlibraryk8s.sh  k8s-v1.8.3.txt
    root@km:~/tom_k8s_kubernetes_install/k8/docker-library-k8s-v1.8.3# cat dockerlibraryk8s.sh 
    #images=(heapster-influxdb-amd64:v1.3.3 heapster-amd64:v1.4.0 heapster-grafana-amd64:v4.4.3)
    #images=(heapster-grafana-amd64:v4.4.3)
    for imageName in `cat k8s-v1.8.3.txt`; do
    echo $imageName
    echo ${imageName%:*}
    echo ${imageName#*:}
    imageNamelast=dockerlibraryk8s-${imageName%:*}
    echo docker pull tomtsang/$imageNamelast
    docker pull tomtsang/$imageNamelast
    echo docker tag tomtsang/$imageNamelast gcr.io/google_containers/$imageName
    docker tag tomtsang/$imageNamelast gcr.io/google_containers/$imageName
    echo docker rmi tomtsang/$imageNamelast
    docker rmi tomtsang/$imageNamelast
    done
    echo "game over"

    root@km:~/tom_k8s_kubernetes_install/k8/docker-library-k8s-v1.8.3# docker pull tomtsang/dockerlibraryk8s-kube-cm-amd64
    Using default tag: latest
    latest: Pulling from tomtsang/dockerlibraryk8s-kube-cm-amd64
    0ffadd58f2a6: Already exists 
    18c5c31a1ebe: Pull complete 
    Digest: sha256:4e738f80a607772c205ca597c1d5874ee50ac40f0a5e88ab85084fd45b684ac0
    Status: Downloaded newer image for tomtsang/dockerlibraryk8s-kube-cm-amd64:latest
    root@km:~/tom_k8s_kubernetes_install/k8/docker-library-k8s-v1.8.3# docker tag tomtsang/dockerlibraryk8s-kube-cm-amd64 gcr.io/google_containers/dockerlibraryk8s-kube-cm-amd64:v1.8.3
    root@km:~/tom_k8s_kubernetes_install/k8/docker-library-k8s-v1.8.3# docker tag gcr.io/google_containers/dockerlibraryk8s-kube-cm-amd64:v1.8.3 gcr.io/google_containers/kube-controller-manager-amd64:v1.8.3
    root@km:~/tom_k8s_kubernetes_install/k8/docker-library-k8s-v1.8.3# docker rmi tomtsang/dockerlibraryk8s-kube-cm-amd64
    Untagged: tomtsang/dockerlibraryk8s-kube-cm-amd64:latest
    Untagged: tomtsang/dockerlibraryk8s-kube-cm-amd64@sha256:4e738f80a607772c205ca597c1d5874ee50ac40f0a5e88ab85084fd45b684ac0
    root@km:~/tom_k8s_kubernetes_install/k8/docker-library-k8s-v1.8.3# 


使用 http://git.oschina.net/tomt/tom_k8s_kubernetes_install 这个仓库吧。

::

    root@km:~/tom_k8s_kubernetes_install/k8/docker-library-k8s-v1.8.3# git remote -v
    origin	http://git.oschina.net/tomt/tom_k8s_kubernetes_install (fetch)
    origin	http://git.oschina.net/tomt/tom_k8s_kubernetes_install (push)
    root@km:~/tom_k8s_kubernetes_install/k8/docker-library-k8s-v1.8.3# ls
    dockerlibraryk8s.sh  k8s-v1.8.3.txt
    root@km:~/tom_k8s_kubernetes_install/k8/docker-library-k8s-v1.8.3# pwd
    /root/tom_k8s_kubernetes_install/k8/docker-library-k8s-v1.8.3
    root@km:~/tom_k8s_kubernetes_install/k8/docker-library-k8s-v1.8.3# ls
    dockerlibraryk8s.sh  k8s-v1.8.3.txt
    root@km:~/tom_k8s_kubernetes_install/k8/docker-library-k8s-v1.8.3# ./dockerlibraryk8s.sh

到现在为止，应该 4 个 image都成功pull 到了 master节点了。

docker registry push 
=======================================

因为我们后面要在 node 节点上使用，所以干脆就直接 push 到 docker registry 去吧。



