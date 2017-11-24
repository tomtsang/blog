=================================================
k8s 中 cephfs 成功的 yaml 文件
=================================================

https://github.com/kubernetes-incubator/external-storage/tree/master/ceph/cephfs

对 我们好不容易成功安装的 k8s-cephfs 进行一个记录呀！

环境
======================================

::

	k8s-master 192.168.31.120 km master
	k8s-node1 192.168.31.119 kn1 node1
	k8s-node2 192.168.31.118 kn2 node2

	cephfs-admin 192.168.31.115
	cephfs-monitor 192.168.31.114
	cephfs-client 192.168.31.172
  
git remote
=================================================

::

  jlch@km:~/cephfs$ git remote -v
  origin	https://github.com/kubernetes-incubator/external-storage (fetch)
  origin	https://github.com/kubernetes-incubator/external-storage (push)
  jlch@km:~/cephfs$ git log | head
  commit f1eb2a4ddf944fdd35a16e686ae104c1db8753b2
  Merge: 06aaf46 52a4da4
  Author: Matthew Wong <mawong@redhat.com>
  Date:   Tue Nov 21 01:48:43 2017 -0500

      Merge pull request #468 from sathieu/patch-1
      
      flex: Fix file shbang

  commit 06aaf46950c9f6f741b34afc1d9f7807bdbe078c
  jlch@km:~/cephfs$ 

git status
=================================================

记录一下主要的修改点

::

  jlch@km:~/cephfs$ git status
  On branch master
  Your branch is up-to-date with 'origin/master'.
  Changes not staged for commit:
    (use "git add <file>..." to update what will be committed)
    (use "git checkout -- <file>..." to discard changes in working directory)

    modified:   ceph-secret-admin.yaml
    modified:   claim.yaml
    modified:   class.yaml
    modified:   deployment.yaml
    modified:   test-pod.yaml

  no changes added to commit (use "git add" and/or "git commit -a")
  jlch@km:~/cephfs$ 

git diff 
=================================================

ceph-secret-admin.yaml
-------------------------------------------------

::

  jlch@km:~/cephfs$ git diff ceph-secret-admin.yaml
  diff --git a/ceph/cephfs/ceph-secret-admin.yaml b/ceph/cephfs/ceph-secret-admin.yaml
  index c3a741a..1ebaac3 100644
  --- a/ceph/cephfs/ceph-secret-admin.yaml
  +++ b/ceph/cephfs/ceph-secret-admin.yaml
  @@ -5,4 +5,5 @@ metadata:
  type: "kubernetes.io/cephfs"
  data:
  #Please note this value is base64 encoded.
  -  key: QVFDTXBIOVlNNFExQmhBQVhHTlF5eU9uZThac1hxV0dvbi9kSVE9PQ==
  +  #key: QVFDTXBIOVlNNFExQmhBQVhHTlF5eU9uZThac1hxV0dvbi9kSVE9PQ==
  +  key: AQCtj+5ZnNTvGRAA2RxAGcIQZJnaJSPEz4jdGw==
  jlch@km:~/cephfs$ 

claim.yaml
-------------------------------------------------

::

  jlch@km:~/cephfs$ git diff claim.yaml
  diff --git a/ceph/cephfs/claim.yaml b/ceph/cephfs/claim.yaml
  index 2dca6ae..280a7a9 100644
  --- a/ceph/cephfs/claim.yaml
  +++ b/ceph/cephfs/claim.yaml
  @@ -9,4 +9,4 @@ spec:
      - ReadWriteMany
    resources:
      requests:
  -      storage: 1Gi
  +      storage: 10Gi
  jlch@km:~/cephfs$ 


class.yaml
-------------------------------------------------

::


  jlch@km:~/cephfs$ git diff class.yaml
  diff --git a/ceph/cephfs/class.yaml b/ceph/cephfs/class.yaml
  index 91c73c0..b2825a3 100644
  --- a/ceph/cephfs/class.yaml
  +++ b/ceph/cephfs/class.yaml
  @@ -4,7 +4,7 @@ metadata:
    name: cephfs
  provisioner: ceph.com/cephfs
  parameters:
  -    monitors: 172.24.0.6:6789
  +    monitors: 192.168.31.114:6789
      adminId: admin
      adminSecretName: ceph-secret-admin
      adminSecretNamespace: "kube-system"
  jlch@km:~/cephfs$ 
  
test-pod.yaml
-------------------------------------------------

::


  jlch@km:~/cephfs$ git diff test-pod.yaml
  diff --git a/ceph/cephfs/test-pod.yaml b/ceph/cephfs/test-pod.yaml
  index 4888676..ece87e8 100644
  --- a/ceph/cephfs/test-pod.yaml
  +++ b/ceph/cephfs/test-pod.yaml
  @@ -5,7 +5,8 @@ metadata:
  spec:
    containers:
    - name: test-pod
  -    image: gcr.io/google_containers/busybox:1.24
  +    image: gcr.io/google_containers/busybox:latest
  +    imagePullPolicy: IfNotPresent
      command:
        - "/bin/sh"
      args:
  jlch@km:~/cephfs$

deployment.yaml
-------------------------------------------------

::

  jlch@km:~/cephfs$ git diff deployment.yaml
  diff --git a/ceph/cephfs/deployment.yaml b/ceph/cephfs/deployment.yaml
  index 37c5f87..dc0315c 100644
  --- a/ceph/cephfs/deployment.yaml
  +++ b/ceph/cephfs/deployment.yaml
  @@ -14,6 +14,7 @@ spec:
        containers:
        - name: cephfs-provisioner
          image: "quay.io/external_storage/cephfs-provisioner:latest"
  +        imagePullPolicy: IfNotPresent
          env:
          - name: PROVISIONER_NAME
            valueFrom:
  @@ -24,3 +25,23 @@ spec:
            - "/usr/local/bin/cephfs-provisioner"
          args:
            - "-id=cephfs-provisioner-1"
  +          - "-master=https://10.96.0.1/"
  +          - "-kubeconfig=/kube/admin.conf" 
  +        volumeMounts:
  +          - mountPath: /kube
  +            name: kube-config
  +          - mountPath: /var/run/kubernetes
  +            name: kube-run-env
  +      volumes:
  +      - name: kube-config
  +        hostPath:
  +          # directory location on host
  +          path: /home/jlch
  +          # this field is optional
  +          type: Directory
  +      - name: kube-run-env
  +        hostPath:
  +          # directory location on host
  +          path: /var/run/kubernetes
  +          # this field is optional
  +          type: Directory

cat
=================================================

::

  jlch@km:~/cephfs$ ls
  cephfs_provisioner  cephfs-provisioner.go  ceph-secret-admin.yaml  CHANGELOG.md  claim.yaml  class.yaml  configmap.yaml  deployment.yaml  Dockerfile  local-start.sh  Makefile  OWNERS  README.md  test-pod.yaml
  jlch@km:~/cephfs$ cat ceph-secret-admin.yaml 
  apiVersion: v1
  kind: Secret
  metadata:
    name: ceph-secret-admin
  type: "kubernetes.io/cephfs"
  data:
  #Please note this value is base64 encoded.
    #key: QVFDTXBIOVlNNFExQmhBQVhHTlF5eU9uZThac1hxV0dvbi9kSVE9PQ==
    key: AQCtj+5ZnNTvGRAA2RxAGcIQZJnaJSPEz4jdGw==
  jlch@km:~/cephfs$ cat claim.yaml 
  kind: PersistentVolumeClaim
  apiVersion: v1
  metadata:
    name: claim1
    annotations:
      volume.beta.kubernetes.io/storage-class: "cephfs"
  spec:
    accessModes:
      - ReadWriteMany
    resources:
      requests:
        storage: 10Gi
  jlch@km:~/cephfs$ cat class.yaml 
  kind: StorageClass
  apiVersion: storage.k8s.io/v1
  metadata:
    name: cephfs
  provisioner: ceph.com/cephfs
  parameters:
      monitors: 192.168.31.114:6789
      adminId: admin
      adminSecretName: ceph-secret-admin
      adminSecretNamespace: "kube-system"

  jlch@km:~/cephfs$ cat test-pod.yaml 
  kind: Pod
  apiVersion: v1
  metadata:
    name: test-pod
  spec:
    containers:
    - name: test-pod
      image: gcr.io/google_containers/busybox:latest
      imagePullPolicy: IfNotPresent
      command:
        - "/bin/sh"
      args:
        - "-c"
        - "touch /mnt/SUCCESS && exit 0 || exit 1"
      volumeMounts:
        - name: pvc
          mountPath: "/mnt"
    restartPolicy: "Never"
    volumes:
      - name: pvc
        persistentVolumeClaim:
          claimName: claim1
  jlch@km:~/cephfs$ cat deployment.yaml 
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
          image: "quay.io/external_storage/cephfs-provisioner:latest"
          imagePullPolicy: IfNotPresent
          env:
          - name: PROVISIONER_NAME
            valueFrom:
              configMapKeyRef:
                key: provisioner.name
                name: cephfs-provisioner
          command:
            - "/usr/local/bin/cephfs-provisioner"
          args:
            - "-id=cephfs-provisioner-1"
            - "-master=https://10.96.0.1/"
            - "-kubeconfig=/kube/admin.conf" 
          volumeMounts:
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
  jlch@km:~/cephfs$ 




