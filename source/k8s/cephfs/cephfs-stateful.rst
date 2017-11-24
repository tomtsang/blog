======================================
使用 cephfs 完成 statefulset 的练习
======================================

环境
======================================

::

	k8s-master 192.168.31.120 km master
	k8s-node1 192.168.31.119 kn1 node1
	k8s-node2 192.168.31.118 kn2 node2

	cephfs-admin 192.168.31.115
	cephfs-monitor 192.168.31.114
	cephfs-client 192.168.31.172

各 k8s-node 安装完 ceph-common(sudo apt install ceph-common -y)

准备
======================================

在进行操作前，请完成下面的操作：

1）阅读

http://www.cnblogs.com/iiiiher/p/7159810.html

https://github.com/kubernetes-incubator/external-storage/tree/master/nfs-client

注意： 

https://github.com/kubernetes-incubator/external-storage 这个仓库，是官方提供之外的 External storage plugins, provisioners, and helper libraries ，因为我们在[官方文档 中看到了 nfs 是不支持 provisioners ](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#provisioner)的，所以要来这里了哟。

2）git clone

	git clone https://github.com/kubernetes-incubator/external-storage

	cd  external-storage/nfs-client/deploy/ 

3）按步骤来进行。

现在打开 https://github.com/kubernetes-incubator/external-storage/tree/master/ceph/cephfs 

开始动手了

step
======================================

Compile the provisioner
--------------------------------------

见 cephfs-k8s-make-by-go-get.rst 

这个地方，请参看 http://blogtt.readthedocs.io/en/latest/k8s/cephfs/cephfs-k8s-make-by-go-get.html

Make the container image and push to the registry
-------------------------------------------------------------

这个地方，看公司情况。主要是 把 docker image 放到仓库去，然后让 k8s各个节点机，docker pull 这个make 后的 image.

Start Kubernetes local cluster
--------------------------------------

略

配置 configmap
--------------------------------------

这个小步骤，https://github.com/kubernetes-incubator/external-storage/tree/master/ceph/cephfs 并没有写。但是确实是要走的。

::

	jlch@km:~/cephfs$ ls
	cephfs_provisioner  cephfs-provisioner.go  ceph-secret-admin.yaml  CHANGELOG.md  claim.yaml  class.yaml  configmap.yaml  deployment.yaml  Dockerfile  local-start.sh  Makefile  OWNERS  README.md  test-pod.yaml
	jlch@km:~/cephfs$ k apply -f configmap.yaml 
	jlch@km:~/cephfs$ k get cm
	NAME                 DATA      AGE
	cephfs-provisioner   1         23h
	jlch@km:~/cephfs$ 

Create a Ceph admin secret
--------------------------------------

在 cephfs-admin 下

::

	ceph auth get client.admin 2>&1 |grep "key = " |awk '{print  $3'} |xargs echo -n > /tmp/secret
	
把这个/tmp/secret 弄到 k8s-master 的 /tmp/secret

在 k8s-master 下

::

	kubectl create secret generic ceph-secret-admin --from-file=/tmp/secret --namespace=kube-system


Start CephFS provisioner
--------------------------------------

方法1 deployment.yaml
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

此方法的具体细节见 `cephfs-k8s-deployment-faq.rst`_

.. _cephfs-k8s-deployment-faq.rst: http://blogtt.readthedocs.io/en/latest/k8s/cephfs/cephfs-k8s-deployment-faq.html

::

	kubectl create -f deployment.yaml



方法2 docker
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

::

	docker run -ti -v /root/.kube:/kube -v /var/run/kubernetes:/var/run/kubernetes --privileged --net=host  cephfs-provisioner /usr/local/bin/cephfs-provisioner -master=http://127.0.0.1:8080 -kubeconfig=/kube/config -id=cephfs-provisioner-1 

这个官方的配置肯定与我们的实际不同，修改一下配置哟。

1. kube配置文件

因为我们的机器是 /root/admin.conf 了哈, 所以直接修改名字成 config 

::

	root@km:~# cat k8.export.sh
	sudo cp /etc/kubernetes/admin.conf $HOME/
	sudo chown $(id -u):$(id -g) $HOME/admin.conf
	export KUBECONFIG=$HOME/admin.conf
	root@km:~# cp admin.conf config
	root@km:~# 
	
2. image

我们 make 后的 image 是 quay.io/external_storage/cephfs-provisioner:latest 

3. ceph参数 -master

看一下service

::

	root@km:~/cephfs# k get svc --all-namespaces | grep default | grep kubernetes
	default       kubernetes             10.96.0.1        <none>        443/TCP                        71d
	root@km:~/cephfs#

所以我们这里是 -master=https://10.96.0.1/ 或者  -master=https://10.96.0.1:443/ 

综合一下，我们的操作应该是：

::

	root@km:~# docker run -ti -v  /root/:/kube -v /var/run/kubernetes:/var/run/kubernetes --privileged --net=host  quay.io/external_storage/cephfs-provisioner /usr/local/bin/cephfs-provisioner -master=https://10.96.0.1/  -kubeconfig=/kube/config -id=cephfs-provisioner-1

好了，基于 docker 方式的 pv 应该是跑起来了。


方法1 deployment.yaml 与 方法2 docker 的对应关系
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

方法2 docker

::

	docker run -ti -v  /home/jlch:/kube -v /var/run/kubernetes:/var/run/kubernetes --privileged --net=host  quay.io/external_storage/cephfs-provisioner /usr/local/bin/cephfs-provisioner -master=https://10.96.0.1/  -kubeconfig=/kube/admin.conf -id=cephfs-provisioner-1

对应于 
方法1 deployment.yaml

::

	apiVersion: extensions/v1beta1
	kind: Deployment
	metadata:
		name: cephfs-provisioner
	spec:
		replicas: 1
		strategy:
			type: Recreate
		template:
			metadata:
				labels:
					app: cephfs-provisioner
			spec:
				containers:
				- name: cephfs-provisioner
					image: "quay.io/external_storage/cephfs-provisioner:latest" # 对应 镜像
					imagePullPolicy: IfNotPresent
					env:
					- name: PROVISIONER_NAME
						valueFrom:
							configMapKeyRef:
								key: provisioner.name
								name: cephfs-provisioner
					command: # 这里对应 命令
						- "/usr/local/bin/cephfs-provisioner"
					args: # 这里对应三个参数
						- "-id=cephfs-provisioner-1"
						- "-master=https://10.96.0.1/"
						- "-kubeconfig=/kube/admin.conf" 
					volumeMounts: # 对应 -v
						- mountPath: /kube
							name: kube-config
						- mountPath: /var/run/kubernetes
							name: kube-run-env
				volumes:
				- name: kube-config
					hostPath:
						# directory location on host
						path: /home/jlch
						# this field is optional
						type: Directory
				- name: kube-run-env
					hostPath:
						# directory location on host
						path: /var/run/kubernetes
						# this field is optional
						type: Directory


Create a CephFS Storage Class
--------------------------------------
	
Replace Ceph monitor's IP in class.yaml with your own and create storage class:

::

	kubectl create -f class.yaml
	
Create a claim
--------------------------------------

::

	kubectl create -f claim.yaml

Create a Pod using the claim
--------------------------------------

::

	kubectl create -f test-pod.yaml	
	
因为我之前安装过 flannel 的缘故，所以部署在 kn2 上的时候，报出 Failed create pod sandbox. 错误来了。

::

	jlch@km:~/cephfs$ k describe  pod test-pod
	Name:         test-pod
	Namespace:    default
	Node:         kn2/192.168.31.118
	Start Time:   Fri, 24 Nov 2017 11:29:18 +0800
	Labels:       <none>
	Annotations:  kubectl.kubernetes.io/last-applied-configuration={"apiVersion":"v1","kind":"Pod","metadata":{"annotations":{},"name":"test-pod","namespace":"default"},"spec":{"containers":[{"args":["-c","touch /mnt/S...
	Status:       Pending
	IP:           
	Containers:
		test-pod:
			Container ID:  
			Image:         gcr.io/google_containers/busybox:v1.1.1
			Image ID:      
			Port:          <none>
			Command:
				/bin/sh
			Args:
				-c
				touch /mnt/SUCCESS && exit 0 || exit 1
			State:          Waiting
				Reason:       ContainerCreating
			Ready:          False
			Restart Count:  0
			Environment:    <none>
			Mounts:
				/mnt from pvc (rw)
				/var/run/secrets/kubernetes.io/serviceaccount from default-token-np6qz (ro)
	Conditions:
		Type           Status
		Initialized    True 
		Ready          False 
		PodScheduled   True 
	Volumes:
		pvc:
			Type:       PersistentVolumeClaim (a reference to a PersistentVolumeClaim in the same namespace)
			ClaimName:  claim1
			ReadOnly:   false
		default-token-np6qz:
			Type:        Secret (a volume populated by a Secret)
			SecretName:  default-token-np6qz
			Optional:    false
	QoS Class:       BestEffort
	Node-Selectors:  <none>
	Tolerations:     node.alpha.kubernetes.io/notReady:NoExecute for 300s
									node.alpha.kubernetes.io/unreachable:NoExecute for 300s
	Events:
		Type     Reason                  Age                From               Message
		----     ------                  ----               ----               -------
		Normal   Scheduled               39s                default-scheduler  Successfully assigned test-pod to kn2
		Normal   SuccessfulMountVolume   39s                kubelet, kn2       MountVolume.SetUp succeeded for volume "default-token-np6qz"
		Normal   SuccessfulMountVolume   38s                kubelet, kn2       MountVolume.SetUp succeeded for volume "pvc-a2bdd1d1-d0c7-11e7-85d4-000c299a346f"
		Warning  FailedCreatePodSandBox  31s (x8 over 38s)  kubelet, kn2       Failed create pod sandbox.
		Warning  FailedSync              31s (x8 over 38s)  kubelet, kn2       Error syncing pod
		Normal   SandboxChanged          31s (x7 over 38s)  kubelet, kn2       Pod sandbox changed, it will be killed and re-created.
	jlch@km:~/cephfs$ 

这里同样地，参考一下 cephfs-k8s-deployment-faq.rst 就可以了。

查看一下pod

::

	jlch@km:~/cephfs$ k get pod 
	NAME                                READY     STATUS    RESTARTS   AGE
	cephfs-provisioner-cff8d95c-6tgcs   1/1       Running   1          22m
	jlch@km:~/cephfs$ 

我的pod 去哪里了？？？
噢。因为我的 pod 是一次性的任务，它直接Completed了。所以要 带 -a 参数。

::

	jlch@km:~/cephfs$ k get pod -a
	NAME                                READY     STATUS      RESTARTS   AGE
	cephfs-provisioner-cff8d95c-6tgcs   1/1       Running     1          23m
	test-pod                            0/1       Completed   0          2m
	jlch@km:~/cephfs$ 

好了，确实是pod执行完了。

确认 cephfs 数据
--------------------------------------
那怎么看我的效果呢？

cephfs-client （内核版本4.4）验证
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

::

	cephu@ceph-client:~/mycephfs$ sudo mount -t ceph 192.168.31.114:6789:/ /mnt/mycephfs -o name=admin,secretfile=admin.secret
	unable to read secretfile: No such file or directory
	error reading secret file
	failed to parse ceph_options
	cephu@ceph-client:~/mycephfs$ cd  # 这个目录不对，没有 admin.secret 文件
	cephu@ceph-client:~$ ls
	admin.secret  mycephfs  release.asc
	cephu@ceph-client:~$ cat admin.secret # 看到了吧，这个密码，就是 cephfs 的密码呀。重要
	AQCtj+****************IQZJnaJSPEz4jdGw==
	cephu@ceph-client:~$ 
	cephu@ceph-client:~$ sudo mount -t ceph 192.168.31.114:6789:/ /mnt/mycephfs -o name=admin,secretfile=admin.secret  # mount 成功
	cephu@ceph-client:~$ ls /mnt/mycephfs/
	a.txt  b.txt  c.txt  h.txt  volumes
	cephu@ceph-client:~$ cd /mnt/mycephfs/
	cephu@ceph-client:/mnt/mycephfs$ ls
	a.txt  b.txt  c.txt  h.txt  volumes
	cephu@ceph-client:/mnt/mycephfs$ cd volumes/
	cephu@ceph-client:/mnt/mycephfs/volumes$ ls
	_deleting  k8s  kubernetes  _kubernetes:kubernetes-dynamic-pvc-5467e02a-c132-11e7-bda7-000c299a346f.meta  _kubernetes:kubernetes-dynamic-pvc-a2c667ad-d0c7-11e7-b656-0a580af40148.meta
	cephu@ceph-client:/mnt/mycephfs/volumes$ cd kubernetes/
	cephu@ceph-client:/mnt/mycephfs/volumes/kubernetes$ ls
	haha  hahaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa  hahaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaabbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbb  kubernetes-dynamic-pvc-5467e02a-c132-11e7-bda7-000c299a346f  kubernetes-dynamic-pvc-a2c667ad-d0c7-11e7-b656-0a580af40148
	cephu@ceph-client:/mnt/mycephfs/volumes/kubernetes$ ll
	total 0
	drwxr-xr-x 1 root root 0 Nov 24 11:29 ./
	drwxr-xr-x 1 root root 0 Nov 24 11:29 ../
	drwxr-xr-x 1 root root 0 Nov  1 14:35 haha/
	drwxr-xr-x 1 root root 0 Nov  1 14:36 hahaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa/
	drwxr-xr-x 1 root root 0 Nov  1 14:38 hahaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaabbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbb/
	drwxr-xr-x 1 root root 0 Nov  4 15:37 kubernetes-dynamic-pvc-5467e02a-c132-11e7-bda7-000c299a346f/
	drwxr-xr-x 1 root root 0 Nov 24 11:36 kubernetes-dynamic-pvc-a2c667ad-d0c7-11e7-b656-0a580af40148/  # 哈哈，从时间上看，这个就是我们要的文件夹。
	cephu@ceph-client:/mnt/mycephfs/volumes/kubernetes$ ll kubernetes-dynamic-pvc-a2c667ad-d0c7-11e7-b656-0a580af40148/
	ls: reading directory 'kubernetes-dynamic-pvc-a2c667ad-d0c7-11e7-b656-0a580af40148/': Input/output error  # what? 又是这个问题。升级内核去吧。我们这里不升级了哈。见 cephfs-k8s-faq.rst
	total 0
	drwxr-xr-x 1 root root 0 Nov 24 11:36 ./
	drwxr-xr-x 1 root root 0 Nov 24 11:29 ../
	cephu@ceph-client:/mnt/mycephfs/volumes/kubernetes$ cd 

好了，这里没用，换一个内核版本 4.10.0 以上的机器来吧。先umount 吧。

::

	cephu@ceph-client:/mnt/mycephfs/volumes/kubernetes$ cd 
	cephu@ceph-client:~$ umount /mnt/mycephfs/
	cephu@ceph-client:~$ ls /mnt/mycephfs/
	cephu@ceph-client:~$ mount
	sysfs on /sys type sysfs (rw,nosuid,nodev,noexec,relatime)
	proc on /proc type proc (rw,nosuid,nodev,noexec,relatime)
	udev on /dev type devtmpfs (rw,nosuid,relatime,size=4067060k,nr_inodes=1016765,mode=755)
	devpts on /dev/pts type devpts (rw,nosuid,noexec,relatime,gid=5,mode=620,ptmxmode=000)
	tmpfs on /run type tmpfs (rw,nosuid,noexec,relatime,size=817444k,mode=755)
	/dev/mapper/ubuntu--vg-root on / type ext4 (rw,relatime,errors=remount-ro,data=ordered)
	securityfs on /sys/kernel/security type securityfs (rw,nosuid,nodev,noexec,relatime)
	tmpfs on /dev/shm type tmpfs (rw,nosuid,nodev)
	tmpfs on /run/lock type tmpfs (rw,nosuid,nodev,noexec,relatime,size=5120k)
	tmpfs on /sys/fs/cgroup type tmpfs (ro,nosuid,nodev,noexec,mode=755)
	cgroup on /sys/fs/cgroup/systemd type cgroup (rw,nosuid,nodev,noexec,relatime,xattr,release_agent=/lib/systemd/systemd-cgroups-agent,name=systemd)
	pstore on /sys/fs/pstore type pstore (rw,nosuid,nodev,noexec,relatime)
	cgroup on /sys/fs/cgroup/freezer type cgroup (rw,nosuid,nodev,noexec,relatime,freezer)
	cgroup on /sys/fs/cgroup/memory type cgroup (rw,nosuid,nodev,noexec,relatime,memory)
	cgroup on /sys/fs/cgroup/hugetlb type cgroup (rw,nosuid,nodev,noexec,relatime,hugetlb)
	cgroup on /sys/fs/cgroup/perf_event type cgroup (rw,nosuid,nodev,noexec,relatime,perf_event)
	cgroup on /sys/fs/cgroup/blkio type cgroup (rw,nosuid,nodev,noexec,relatime,blkio)
	cgroup on /sys/fs/cgroup/devices type cgroup (rw,nosuid,nodev,noexec,relatime,devices)
	cgroup on /sys/fs/cgroup/cpu,cpuacct type cgroup (rw,nosuid,nodev,noexec,relatime,cpu,cpuacct)
	cgroup on /sys/fs/cgroup/net_cls,net_prio type cgroup (rw,nosuid,nodev,noexec,relatime,net_cls,net_prio)
	cgroup on /sys/fs/cgroup/pids type cgroup (rw,nosuid,nodev,noexec,relatime,pids)
	cgroup on /sys/fs/cgroup/cpuset type cgroup (rw,nosuid,nodev,noexec,relatime,cpuset)
	systemd-1 on /proc/sys/fs/binfmt_misc type autofs (rw,relatime,fd=32,pgrp=1,timeout=0,minproto=5,maxproto=5,direct)
	hugetlbfs on /dev/hugepages type hugetlbfs (rw,relatime)
	debugfs on /sys/kernel/debug type debugfs (rw,relatime)
	mqueue on /dev/mqueue type mqueue (rw,relatime)
	fusectl on /sys/fs/fuse/connections type fusectl (rw,relatime)
	/dev/sda1 on /boot type ext2 (rw,relatime,block_validity,barrier,user_xattr,acl)
	lxcfs on /var/lib/lxcfs type fuse.lxcfs (rw,nosuid,nodev,relatime,user_id=0,group_id=0,allow_other)
	/dev/mapper/ubuntu--vg-root on /var/lib/docker/aufs type ext4 (rw,relatime,errors=remount-ro,data=ordered)
	none on /var/lib/docker/aufs/mnt/3ebf0690df4cb6798be2be8c6bee8a77eacfa7e89c42acacc2a97ea2bc3af09a type aufs (rw,relatime,si=781e745ed7325e3c,dio,dirperm1)
	nsfs on /run/docker/netns/default type nsfs (rw)
	shm on /var/lib/docker/containers/9e4d9c65734a4e566b4303071ec85f0ea1a18efb2f077e51d09d8457039b725d/shm type tmpfs (rw,nosuid,nodev,noexec,relatime,size=65536k)
	none on /var/lib/docker/aufs/mnt/1a10a53a1baa1de36489cc6f35562790a3659b0dfa08331ee3521d5b89f4f848 type aufs (rw,relatime,si=781e745c1d80ee3c,dio,dirperm1)
	shm on /var/lib/docker/containers/9ceeadb3b8184a36d946bd1aeb98a50a0682b75de141c2195373faa3786bbe66/shm type tmpfs (rw,nosuid,nodev,noexec,relatime,size=65536k)
	tmpfs on /run/user/113 type tmpfs (rw,nosuid,nodev,relatime,size=817444k,mode=700,uid=113,gid=120)
	tmpfs on /run/user/1003 type tmpfs (rw,nosuid,nodev,relatime,size=817444k,mode=700,uid=1003,gid=1003)
	tmpfs on /run/user/1000 type tmpfs (rw,nosuid,nodev,relatime,size=817444k,mode=700,uid=1000,gid=1000)
	cephu@ceph-client:~$ ls /mnt/mycephfs/
	cephu@ceph-client:~$ 

可以了。

kn1 （内核版本4.12.0）验证
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

查内核版本

::

	jlch@kn1:~$ uname -a
	Linux kn1 4.12.0-041200-generic #201707022031 SMP Mon Jul 3 00:32:52 UTC 2017 x86_64 x86_64 x86_64 GNU/Linux
	jlch@kn1:~$ 

查一下mount下已有的ceph

::

	jlch@kn1:~$ mount | grep ceph
	jlch@kn1:~$ 

把密码文件admin.secret搞过来 

::

	jlch@kn1:~$ ls
	10-kubeadm.conf  apt.conf                     etc.kubernetes     hpa-example.sh  k3-dockerlibraryk8s.sh  linux-image-4.12.0-041200-generic_4.12.0-041200.201707022031_amd64.deb  nginx-dockerlibraryk8s.sh  redis.sh
	admin.conf       busybox-dockerlibraryk8s.sh  hello-frontend.sh  image           k8                      liveness-dockerlibraryk8s.sh                                            node-hello.sh              registry.crt
	admin.secret     docker                       hello-go-gke.sh    job-wq-2        k8.export.sh            mydockersimages.tar                                                     proxy.sh                   xtrabackup-dockerlibraryk8s.sh

mount

::

	jlch@kn1:~$ sudo mount -t ceph 192.168.31.114:6789:/ /mnt/mycephfs -o name=admin,secretfile=admin.secret
	jlch@kn1:~$ mount | grep ceph
	192.168.31.114:6789:/ on /mnt/mycephfs type ceph (rw,relatime,name=admin,secret=<hidden>,acl)
	jlch@kn1:~$ 

成功了，查一下文件

::

	jlch@kn1:~$ ls /mnt/mycephfs/
	a.txt    b.txt    c.txt    h.txt    volumes/ 
	jlch@kn1:~$ cd /mnt/mycephfs/volumes/
	jlch@kn1:/mnt/mycephfs/volumes$ ls
	_deleting  k8s  kubernetes  _kubernetes:kubernetes-dynamic-pvc-5467e02a-c132-11e7-bda7-000c299a346f.meta  _kubernetes:kubernetes-dynamic-pvc-a2c667ad-d0c7-11e7-b656-0a580af40148.meta
	jlch@kn1:/mnt/mycephfs/volumes$ cd kubernetes/
	jlch@kn1:/mnt/mycephfs/volumes/kubernetes$ ll
	total 0
	drwxr-xr-x 1 root root 5 Nov 24 11:29 ./
	drwxr-xr-x 1 root root 5 Nov 24 11:29 ../
	drwxr-xr-x 1 root root 0 Nov  1 14:35 haha/
	drwxr-xr-x 1 root root 0 Nov  1 14:36 hahaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa/
	drwxr-xr-x 1 root root 0 Nov  1 14:38 hahaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaabbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbb/
	drwxr-xr-x 1 root root 1 Nov  4 15:37 kubernetes-dynamic-pvc-5467e02a-c132-11e7-bda7-000c299a346f/
	drwxr-xr-x 1 root root 1 Nov 24 11:36 kubernetes-dynamic-pvc-a2c667ad-d0c7-11e7-b656-0a580af40148/  # 哈哈，从时间上看，这个就是我们要的文件夹。 
	jlch@kn1:/mnt/mycephfs/volumes/kubernetes$ cd kubernetes-dynamic-pvc-a2c667ad-d0c7-11e7-b656-0a580af40148/
	jlch@kn1:/mnt/mycephfs/volumes/kubernetes/kubernetes-dynamic-pvc-a2c667ad-d0c7-11e7-b656-0a580af40148$ ls
	SUCCESS
	jlch@kn1:/mnt/mycephfs/volumes/kubernetes/kubernetes-dynamic-pvc-a2c667ad-d0c7-11e7-b656-0a580af40148$ 

哈哈，找到了之前由 test-pod 创建的文件 SUCCESS。

到此，https://github.com/kubernetes-incubator/external-storage/tree/master/ceph/cephfs 的几个步骤已全走完了。

game over!
	
	
	
	