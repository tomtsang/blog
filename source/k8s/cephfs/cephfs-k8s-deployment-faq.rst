==================================================
k8s cephfs 在 deployment.yaml 中的使用
==================================================

Environment
==================================================


::

	k8s-master 192.168.31.120 km master
	k8s-node1 192.168.31.119 kn1 node1
	k8s-node2 192.168.31.118 kn2 node2


配置 deployment.yaml
==================================================

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


对应于 

方法1 docker

::

	docker run -ti -v  /home/jlch:/kube -v /var/run/kubernetes:/var/run/kubernetes --privileged --net=host  quay.io/external_storage/cephfs-provisioner /usr/local/bin/cephfs-provisioner -master=https://10.96.0.1/  -kubeconfig=/kube/admin.conf -id=cephfs-provisioner-1



apply
==================================================


::

    jlch@km:~/cephfs$ k apply -f deployment.yaml 
    deployment "cephfs-provisioner" created
    jlch@km:~/cephfs$ k get pods
    NAME                                READY     STATUS              RESTARTS   AGE
    cephfs-provisioner-cff8d95c-8b498   0/1       ContainerCreating   0          3s

describe
==================================================
报错了

::

    jlch@km:~/cephfs$ k describe pod cephfs-provisioner-cff8d95c-8b498
    Name:           cephfs-provisioner-cff8d95c-8b498
    Namespace:      default
    Node:           kn1/192.168.31.119
    Start Time:     Fri, 24 Nov 2017 11:13:06 +0800
    Labels:         app=cephfs-provisioner
                    pod-template-hash=79948517
    Annotations:    kubernetes.io/created-by={"kind":"SerializedReference","apiVersion":"v1","reference":{"kind":"ReplicaSet","namespace":"default","name":"cephfs-provisioner-cff8d95c","uid":"645fa2f3-d0c5-11e7-85d4-000c...
    Status:         Pending
    IP:             
    Created By:     ReplicaSet/cephfs-provisioner-cff8d95c
    Controlled By:  ReplicaSet/cephfs-provisioner-cff8d95c
    Containers:
    cephfs-provisioner:
        Container ID:  
        Image:         quay.io/external_storage/cephfs-provisioner:latest
        Image ID:      
        Port:          <none>
        Command:
        /usr/local/bin/cephfs-provisioner
        Args:
        -id=cephfs-provisioner-1
        -master=https://10.96.0.1/
        -kubeconfig=/kube/admin.conf
        State:          Waiting
        Reason:       ContainerCreating
        Ready:          False
        Restart Count:  0
        Environment:
        PROVISIONER_NAME:  <set to the key 'provisioner.name' of config map 'cephfs-provisioner'>  Optional: false
        Mounts:
        /kube from kube-config (rw)
        /var/run/kubernetes from kube-run-env (rw)
        /var/run/secrets/kubernetes.io/serviceaccount from default-token-np6qz (ro)
    Conditions:
    Type           Status
    Initialized    True 
    Ready          False 
    PodScheduled   True 
    Volumes:
    kube-config:
        Type:  HostPath (bare host directory volume)
        Path:  /home/jlch
    kube-run-env:
        Type:  HostPath (bare host directory volume)
        Path:  /var/run/kubernetes
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
    Normal   Scheduled               20s                default-scheduler  Successfully assigned cephfs-provisioner-cff8d95c-8b498 to kn1
    Normal   SuccessfulMountVolume   19s                kubelet, kn1       MountVolume.SetUp succeeded for volume "kube-run-env"
    Normal   SuccessfulMountVolume   19s                kubelet, kn1       MountVolume.SetUp succeeded for volume "kube-config"
    Normal   SuccessfulMountVolume   19s                kubelet, kn1       MountVolume.SetUp succeeded for volume "default-token-np6qz"
    Warning  FailedCreatePodSandBox  12s (x7 over 18s)  kubelet, kn1       Failed create pod sandbox.
    Warning  FailedSync              11s (x8 over 18s)  kubelet, kn1       Error syncing pod
    Normal   SandboxChanged          11s (x7 over 17s)  kubelet, kn1       Pod sandbox changed, it will be killed and re-created.
    jlch@km:~/cephfs$

看到了吧。是 部署在 kn1 上的时候，报出 Failed create pod sandbox. 错误来了。
那就要去 kn1 上查看日志了。

::

    root@kn1:~# vi /var/log/syslog
    ...
    # 查找 ceph
    Nov 24 09:09:19 kn1 systemd-udevd[23014]: Could not generate persistent MAC address for vethf620eecc: No such file or directory
    Nov 24 09:09:19 kn1 kubelet[6008]: E1124 09:09:19.678998    6008 cni.go:301] Error adding network: "cni0" already has an IP address different from 10.244.1.1/24
    Nov 24 09:09:19 kn1 kubelet[6008]: E1124 09:09:19.679048    6008 cni.go:250] Error while adding to cni network: "cni0" already has an IP address different from 10.244.1.1/24
    Nov 24 09:09:19 kn1 kernel: [1788519.065714] cni0: port 1(vethf620eecc) entered disabled state
    ...

查找关键字 ceph， 找到了上面的信息，明显是与 cni0 相关。这上面讲了，"cni0" already has an IP address different from 10.244.1.1/24，
也就是说，可能现有已有一个cni0, 但是与kubernetes自动给它分配的ip: 10.244.1.1/24, 不一致了。
查一下ip, 

::

    root@kn1:~# ip a
    1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
        link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
        inet 127.0.0.1/8 scope host lo
        valid_lft forever preferred_lft forever
        inet6 ::1/128 scope host 
        valid_lft forever preferred_lft forever
    2: ens160: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc mq state UP group default qlen 1000
        link/ether 00:0c:29:6f:74:6a brd ff:ff:ff:ff:ff:ff
        inet 192.168.31.119/24 brd 192.168.31.255 scope global ens160
        valid_lft forever preferred_lft forever
        inet6 fe80::20c:29ff:fe6f:746a/64 scope link 
        valid_lft forever preferred_lft forever
    3: docker0: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1500 qdisc noqueue state DOWN group default 
        link/ether 02:42:96:2d:2c:25 brd ff:ff:ff:ff:ff:ff
        inet 172.17.0.1/16 scope global docker0
        valid_lft forever preferred_lft forever
        inet6 fe80::42:96ff:fe2d:2c25/64 scope link 
        valid_lft forever preferred_lft forever
    4: flannel.1: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1450 qdisc noqueue state UNKNOWN group default 
        link/ether 9e:b7:c9:ed:7e:cb brd ff:ff:ff:ff:ff:ff
        inet 10.244.2.0/32 scope global flannel.1
        valid_lft forever preferred_lft forever
        inet6 fe80::9cb7:c9ff:feed:7ecb/64 scope link 
        valid_lft forever preferred_lft forever
    4639: cni0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1450 qdisc noqueue state UP group default qlen 1000
        link/ether 0a:58:0a:f4:02:01 brd ff:ff:ff:ff:ff:ff
        inet 10.244.2.1/24 scope global cni0
        valid_lft forever preferred_lft forever
        inet6 fe80::b401:beff:fedf:2203/64 scope link 
        valid_lft forever preferred_lft forever
    4640: veth3fec5211@if3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1450 qdisc noqueue master cni0 state UP group default 
        link/ether 72:3c:c8:35:61:6f brd ff:ff:ff:ff:ff:ff link-netnsid 0
        inet6 fe80::703c:c8ff:fe35:616f/64 scope link 
        valid_lft forever preferred_lft forever
    root@kn1:~# 

我去，果然是。这里面的是 10.244.2.1/24。我靠。
怎么办？怎么办？
修改cni0呗。

修改 cni0
====================================================

::

    root@kn1:~# systemctl stop docker 
    root@kn1:~# ip a
    root@kn1:~# brctl --help
    root@kn1:~# brctl # 这里会提示怎么安装。
    root@kn1:~# apt install bridge-utils
    root@kn1:~# brctl --help 
    root@kn1:~# ifconfig cni0 down
    root@kn1:~# brctl delbr cni0
    root@kn1:~# ip a
    root@kn1:~# systemctl start docker
    root@kn1:~# ip a # 这个时候，docker 启动的 containers 会自动帮助把 cni0 启动起来的。

检查
================================================

最后的效果

::

    root@kn1:~# ip a
    1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
        link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
        inet 127.0.0.1/8 scope host lo
        valid_lft forever preferred_lft forever
        inet6 ::1/128 scope host 
        valid_lft forever preferred_lft forever
    2: ens160: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc mq state UP group default qlen 1000
        link/ether 00:0c:29:6f:74:6a brd ff:ff:ff:ff:ff:ff
        inet 192.168.31.119/24 brd 192.168.31.255 scope global ens160
        valid_lft forever preferred_lft forever
        inet6 fe80::20c:29ff:fe6f:746a/64 scope link 
        valid_lft forever preferred_lft forever
    3: docker0: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1500 qdisc noqueue state DOWN group default 
        link/ether 02:42:96:2d:2c:25 brd ff:ff:ff:ff:ff:ff
        inet 172.17.0.1/16 scope global docker0
        valid_lft forever preferred_lft forever
        inet6 fe80::42:96ff:fe2d:2c25/64 scope link 
        valid_lft forever preferred_lft forever
    4: flannel.1: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1450 qdisc noqueue state UNKNOWN group default 
        link/ether ba:84:65:bb:4d:68 brd ff:ff:ff:ff:ff:ff
        inet 10.244.1.0/32 scope global flannel.1
        valid_lft forever preferred_lft forever
        inet6 fe80::b884:65ff:febb:4d68/64 scope link 
        valid_lft forever preferred_lft forever
    4639: cni0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1450 qdisc noqueue state UP group default qlen 1000
        link/ether 0a:58:0a:f4:01:01 brd ff:ff:ff:ff:ff:ff
        inet 10.244.1.1/24 scope global cni0
        valid_lft forever preferred_lft forever
        inet6 fe80::f48c:7eff:fecd:4e08/64 scope link 
        valid_lft forever preferred_lft forever
    4640: veth3fec5211@if3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1450 qdisc noqueue master cni0 state UP group default 
        link/ether 72:3c:c8:35:61:6f brd ff:ff:ff:ff:ff:ff link-netnsid 0
        inet6 fe80::703c:c8ff:fe35:616f/64 scope link 
        valid_lft forever preferred_lft forever
    root@kn1:~# 

好了，再回到 km ，检查 deployment, 

::

    jlch@km:~/cephfs$ k get pods
    NAME                                READY     STATUS    RESTARTS   AGE
    cephfs-provisioner-cff8d95c-6tgcs   1/1       Running   1          3h
    jlch@km:~/cephfs$ 


