============================
cephfs-k8s 中的 make
============================

下载 make ，报错了。

::

	root@km:~/kubernetes.io/TUTORIALS/Stateful-Applications/cephfs-stateful# cd external-storage/ceph/cephfs/
	root@km:~/kubernetes.io/TUTORIALS/Stateful-Applications/cephfs-stateful/external-storage/ceph/cephfs# ls
	cephfs_provisioner  cephfs-provisioner.go  ceph-secret-admin.yaml  CHANGELOG.md  claim.yaml  class.yaml  configmap.yaml  deployment.yaml  Dockerfile  local-start.sh  Makefile  OWNERS  README.md  test-pod.yaml
	root@km:~/kubernetes.io/TUTORIALS/Stateful-Applications/cephfs-stateful/external-storage/ceph/cephfs# make
	CGO_ENABLED=0 GOOS=linux go build -a -ldflags '-extldflags "-static"' -o cephfs-provisioner cephfs-provisioner.go
	cephfs-provisioner.go:28:2: cannot find package "github.com/golang/glog" in any of:
			/usr/lib/go-1.8/src/github.com/golang/glog (from $GOROOT)
			/root/go/src/github.com/golang/glog (from $GOPATH)
	cephfs-provisioner.go:29:2: cannot find package "github.com/kubernetes-incubator/external-storage/lib/controller" in any of:
			/usr/lib/go-1.8/src/github.com/kubernetes-incubator/external-storage/lib/controller (from $GOROOT)
			/root/go/src/github.com/kubernetes-incubator/external-storage/lib/controller (from $GOPATH)
	cephfs-provisioner.go:30:2: cannot find package "k8s.io/api/core/v1" in any of:
			/usr/lib/go-1.8/src/k8s.io/api/core/v1 (from $GOROOT)
			/root/go/src/k8s.io/api/core/v1 (from $GOPATH)
	cephfs-provisioner.go:31:2: cannot find package "k8s.io/apimachinery/pkg/apis/meta/v1" in any of:
			/usr/lib/go-1.8/src/k8s.io/apimachinery/pkg/apis/meta/v1 (from $GOROOT)
			/root/go/src/k8s.io/apimachinery/pkg/apis/meta/v1 (from $GOPATH)
	cephfs-provisioner.go:32:2: cannot find package "k8s.io/apimachinery/pkg/util/uuid" in any of:
			/usr/lib/go-1.8/src/k8s.io/apimachinery/pkg/util/uuid (from $GOROOT)
			/root/go/src/k8s.io/apimachinery/pkg/util/uuid (from $GOPATH)
	cephfs-provisioner.go:33:2: cannot find package "k8s.io/apimachinery/pkg/util/wait" in any of:
			/usr/lib/go-1.8/src/k8s.io/apimachinery/pkg/util/wait (from $GOROOT)
			/root/go/src/k8s.io/apimachinery/pkg/util/wait (from $GOPATH)
	cephfs-provisioner.go:34:2: cannot find package "k8s.io/client-go/kubernetes" in any of:
			/usr/lib/go-1.8/src/k8s.io/client-go/kubernetes (from $GOROOT)
			/root/go/src/k8s.io/client-go/kubernetes (from $GOPATH)
	cephfs-provisioner.go:35:2: cannot find package "k8s.io/client-go/rest" in any of:
			/usr/lib/go-1.8/src/k8s.io/client-go/rest (from $GOROOT)
			/root/go/src/k8s.io/client-go/rest (from $GOPATH)
	cephfs-provisioner.go:36:2: cannot find package "k8s.io/client-go/tools/clientcmd" in any of:
			/usr/lib/go-1.8/src/k8s.io/client-go/tools/clientcmd (from $GOROOT)
			/root/go/src/k8s.io/client-go/tools/clientcmd (from $GOPATH)
	cephfs-provisioner.go:37:2: cannot find package "k8s.io/kubernetes/pkg/api/v1/helper" in any of:
			/usr/lib/go-1.8/src/k8s.io/kubernetes/pkg/api/v1/helper (from $GOROOT)
			/root/go/src/k8s.io/kubernetes/pkg/api/v1/helper (from $GOPATH)
	Makefile:27: recipe for target 'all' failed
	make: *** [all] Error 1
	root@km:~/kubernetes.io/TUTORIALS/Stateful-Applications/cephfs-stateful/external-storage/ceph/cephfs#  
	
少了包呀。

查一下，有没有go, GOPATH


::

	root@km:~/kubernetes.io/TUTORIALS/Stateful-Applications/cephfs-stateful/external-storage/ceph/cephfs# which go
	/usr/bin/go
	root@km:~/kubernetes.io/TUTORIALS/Stateful-Applications/cephfs-stateful/external-storage/ceph/cephfs# echo $GOPATH

	root@km:~/kubernetes.io/TUTORIALS/Stateful-Applications/cephfs-stateful/external-storage/ceph/cephfs# 
	
没有设置，那好了，设置成我们之前用过的地址。

先看一下，我们之前的go包：


::

	root@km:~# cd kubernetes
	root@km:~/kubernetes# ls
	api    BUILD.bazel   cluster  code-of-conduct.md  docs      federation  hack         LICENSE  Makefile                  OWNERS          pkg     README.md  SUPPORT.md  third_party   Vagrantfile  WORKSPACE
	build  CHANGELOG.md  cmd      CONTRIBUTING.md     examples  Godeps      labels.yaml  logo     Makefile.generated_files  OWNERS_ALIASES  plugin  staging    test        translations  vendor
	root@km:~/kubernetes# 
	
随便查一个，上面没有的包吧，如 client-go:


::

	root@km:~/kubernetes# ls -lR > ls-lr
	root@km:~/kubernetes# cat ls-lr | grep client-go
	drwxr-xr-x 17 root root 4096 Aug 29 18:47 client-go
	drwxr-xr-x 5 root root 4096 Aug 29 18:47 client-go
	...
	...
	./staging/src/k8s.io/client-go:
	./staging/src/k8s.io/client-go/discovery:
	./staging/src/k8s.io/client-go/discovery/cached:
	./staging/src/k8s.io/client-go/discovery/fake:
	./staging/src/k8s.io/client-go/dynamic:
	./staging/src/k8s.io/client-go/dynamic/fake:
	./staging/src/k8s.io/client-go/examples:
	...
	...
	lrwxrwxrwx 1 root root   34 Aug 29 18:47 client-go -> ../../staging/src/k8s.io/client-go
	root@km:~/kubernetes# 
	
有呀，这样就不用重复下载了。

::

	root@km:~/kubernetes# cd staging/
	root@km:~/kubernetes/staging# ls
	BUILD  godeps-json-updater.go  OWNERS  pkg  prime-apimachinery.sh  README.md  src
	root@km:~/kubernetes/staging# cd src
	root@km:~/kubernetes/staging/src# ls
	github.com  golang.org  gopkg.in  k8s.io
	root@km:~/kubernetes/staging/src# cd k8s.io/
	root@km:~/kubernetes/staging/src/k8s.io# ls
	api  apiextensions-apiserver  apimachinery  apiserver  client-go  code-generator  kube-aggregator  kube-openapi  kubernetes  metrics  sample-apiserver
	root@km:~/kubernetes/staging/src/k8s.io# pwd
	/root/kubernetes/staging/src/k8s.io
	root@km:~/kubernetes/staging/src/k8s.io# 
	
设置一下 GOPATH


::

	root@km:~/kubernetes/staging# export GOPATH=/root/kubernetes/staging
	root@km:~/kubernetes/staging# echo $GOPATH
	/root/kubernetes/staging
	root@km:~/kubernetes/staging#
	
重新来一下，并且 `go get` 


::

	root@km:~/kubernetes/staging# cd ~/kubernetes.io/TUTORIALS/Stateful-Applications/cephfs-stateful/external-storage/ceph/cephfs
	root@km:~/kubernetes.io/TUTORIALS/Stateful-Applications/cephfs-stateful/external-storage/ceph/cephfs# make
	CGO_ENABLED=0 GOOS=linux go build -a -ldflags '-extldflags "-static"' -o cephfs-provisioner cephfs-provisioner.go
	/root/kubernetes/staging/src/k8s.io/client-go/discovery/discovery_client.go:26:2: cannot find package "github.com/emicklei/go-restful-swagger12" in any of:
			/usr/lib/go-1.8/src/github.com/emicklei/go-restful-swagger12 (from $GOROOT)
			/root/kubernetes/staging/src/github.com/emicklei/go-restful-swagger12 (from $GOPATH)
	/root/kubernetes/staging/src/k8s.io/client-go/discovery/discovery_client.go:27:2: cannot find package "github.com/golang/protobuf/proto" in any of:
			/usr/lib/go-1.8/src/github.com/golang/protobuf/proto (from $GOROOT)
			/root/kubernetes/staging/src/github.com/golang/protobuf/proto (from $GOPATH)
	/root/kubernetes/staging/src/k8s.io/client-go/discovery/discovery_client.go:28:2: cannot find package "github.com/googleapis/gnostic/OpenAPIv2" in any of:
			/usr/lib/go-1.8/src/github.com/googleapis/gnostic/OpenAPIv2 (from $GOROOT)
			/root/kubernetes/staging/src/github.com/googleapis/gnostic/OpenAPIv2 (from $GOPATH)
	/root/kubernetes/staging/src/k8s.io/client-go/tools/clientcmd/auth_loaders.go:26:2: cannot find package "github.com/howeyc/gopass" in any of:
			/usr/lib/go-1.8/src/github.com/howeyc/gopass (from $GOROOT)
			/root/kubernetes/staging/src/github.com/howeyc/gopass (from $GOPATH)
	/root/kubernetes/staging/src/k8s.io/client-go/tools/clientcmd/client_config.go:28:2: cannot find package "github.com/imdario/mergo" in any of:
			/usr/lib/go-1.8/src/github.com/imdario/mergo (from $GOROOT)
			/root/kubernetes/staging/src/github.com/imdario/mergo (from $GOPATH)
	/root/kubernetes/staging/src/k8s.io/client-go/util/flowcontrol/throttle.go:22:2: cannot find package "github.com/juju/ratelimit" in any of:
			/usr/lib/go-1.8/src/github.com/juju/ratelimit (from $GOROOT)
			/root/kubernetes/staging/src/github.com/juju/ratelimit (from $GOPATH)
	cephfs-provisioner.go:29:2: cannot find package "github.com/kubernetes-incubator/external-storage/lib/controller" in any of:
			/usr/lib/go-1.8/src/github.com/kubernetes-incubator/external-storage/lib/controller (from $GOROOT)
			/root/kubernetes/staging/src/github.com/kubernetes-incubator/external-storage/lib/controller (from $GOPATH)
	/root/kubernetes/staging/src/k8s.io/apimachinery/pkg/util/uuid/uuid.go:22:2: cannot find package "github.com/pborman/uuid" in any of:
			/usr/lib/go-1.8/src/github.com/pborman/uuid (from $GOROOT)
			/root/kubernetes/staging/src/github.com/pborman/uuid (from $GOPATH)
	Makefile:27: recipe for target 'all' failed
	make: *** [all] Error 1
	root@km:~/kubernetes.io/TUTORIALS/Stateful-Applications/cephfs-stateful/external-storage/ceph/cephfs# go get github.com/emicklei/go-restful-swagger12
	root@km:~/kubernetes.io/TUTORIALS/Stateful-Applications/cephfs-stateful/external-storage/ceph/cephfs# go get github.com/golang/protobuf/proto
	root@km:~/kubernetes.io/TUTORIALS/Stateful-Applications/cephfs-stateful/external-storage/ceph/cephfs# go get github.com/googleapis/gnostic/OpenAPIv2
	root@km:~/kubernetes.io/TUTORIALS/Stateful-Applications/cephfs-stateful/external-storage/ceph/cephfs# go get github.com/howeyc/gopass
	package golang.org/x/crypto/ssh/terminal: unrecognized import path "golang.org/x/crypto/ssh/terminal" (https fetch: Get https://golang.org/x/crypto/ssh/terminal?go-get=1: dial tcp 216.239.37.1:443: i/o timeout)
	root@km:~/kubernetes.io/TUTORIALS/Stateful-Applications/cephfs-stateful/external-storage/ceph/cephfs# 
	
呀，这里要去 https://golang.org/x/crypto/ssh/terminal 这要FQ的呀，怎么办？

农总来了。

打开浏览器，github.com, 搜索，crypto, 语言选择 go, 找到 golang/crypto, 打开，复制 url, 

打开 另一个 terminal, 然后，从之前知道 GOPATH 是 /root/kubernetes/staging/ ,那进去。


::

	root@km:~# 
	root@km:~# cd kubernetes/staging/
	root@km:~/kubernetes/staging# ls
	BUILD  godeps-json-updater.go  OWNERS  pkg  prime-apimachinery.sh  README.md  src
	root@km:~/kubernetes/staging# cd src
	root@km:~/kubernetes/staging/src# ls
	github.com  golang.org  gopkg.in  k8s.io
	root@km:~/kubernetes/staging/src# cd golang.org/
	root@km:~/kubernetes/staging/src/golang.org# ls
	x
	root@km:~/kubernetes/staging/src/golang.org# cd x
	root@km:~/kubernetes/staging/src/golang.org/x# ls
	net  text
	root@km:~/kubernetes/staging/src/golang.org/x# 
	
好了，到这里看到了，确实是没有 crypto, 下载 golang 在 github.com 下的官方镜像吧。


::

	root@km:~/kubernetes/staging/src/golang.org/x# git clone https://github.com/golang/crypto.git
	Cloning into 'crypto'...
	remote: Counting objects: 3889, done.
	remote: Compressing objects: 100% (22/22), done.
	remote: Total 3889 (delta 6), reused 18 (delta 4), pack-reused 3863
	Receiving objects: 100% (3889/3889), 2.82 MiB | 44.00 KiB/s, done.
	Resolving deltas: 100% (2469/2469), done.
	Checking connectivity... done.
	root@km:~/kubernetes/staging/src/golang.org/x# ls
	crypto  net  text
	root@km:~/kubernetes/staging/src/golang.org/x# 
	
好了，现在有了。

如果后续还有其它的 golang 的包，也这么处理吧。

回到之前的 terminal 吧。
接着 go get


::

	root@km:~/kubernetes.io/TUTORIALS/Stateful-Applications/cephfs-stateful/external-storage/ceph/cephfs# go get github.com/kubernetes-incubator/external-storage/lib/controller
	root@km:~/kubernetes.io/TUTORIALS/Stateful-Applications/cephfs-stateful/external-storage/ceph/cephfs# make
	CGO_ENABLED=0 GOOS=linux go build -a -ldflags '-extldflags "-static"' -o cephfs-provisioner cephfs-provisioner.go
	# command-line-arguments
	./cephfs-provisioner.go:65: cannot use cephFSProvisioner literal (type *cephFSProvisioner) as type controller.Provisioner in return argument:
			*cephFSProvisioner does not implement controller.Provisioner (wrong type for Delete method)
					have Delete(*"k8s.io/api/core/v1".PersistentVolume) error
					want Delete(*"github.com/kubernetes-incubator/external-storage/vendor/k8s.io/api/core/v1".PersistentVolume) error
	./cephfs-provisioner.go:69: cannot use cephFSProvisioner literal (type *cephFSProvisioner) as type controller.Provisioner in assignment:
			*cephFSProvisioner does not implement controller.Provisioner (wrong type for Delete method)
					have Delete(*"k8s.io/api/core/v1".PersistentVolume) error
					want Delete(*"github.com/kubernetes-incubator/external-storage/vendor/k8s.io/api/core/v1".PersistentVolume) error
	./cephfs-provisioner.go:134: cannot use options.PersistentVolumeReclaimPolicy (type "github.com/kubernetes-incubator/external-storage/vendor/k8s.io/api/core/v1".PersistentVolumeReclaimPolicy) as type "k8s.io                           /api/core/v1".PersistentVolumeReclaimPolicy in field value
	./cephfs-provisioner.go:135: cannot use options.PVC.Spec.AccessModes (type []"github.com/kubernetes-incubator/external-storage/vendor/k8s.io/api/core/v1".PersistentVolumeAccessMode) as type []"k8s.io/api/cor                           e/v1".PersistentVolumeAccessMode in field value
	./cephfs-provisioner.go:137: cannot use "k8s.io/api/core/v1".ResourceName("k8s.io/api/core/v1".ResourceStorage) (type "k8s.io/api/core/v1".ResourceName) as type "github.com/kubernetes-incubator/external-stor                           age/vendor/k8s.io/api/core/v1".ResourceName in map index
	./cephfs-provisioner.go:137: cannot use options.PVC.Spec.Resources.Requests["k8s.io/api/core/v1".ResourceName("k8s.io/api/core/v1".ResourceStorage)] (type "github.com/kubernetes-incubator/external-storage/ve                           ndor/k8s.io/apimachinery/pkg/api/resource".Quantity) as type "k8s.io/apimachinery/pkg/api/resource".Quantity in map value
	./cephfs-provisioner.go:147: cannot use "k8s.io/api/core/v1".CephFSVolumeSource literal (type *"k8s.io/api/core/v1".CephFSVolumeSource) as type *"k8s.io/api/core/v1".CephFSPersistentVolumeSource in field val                           ue
	./cephfs-provisioner.go:318: cannot use clientset (type *"k8s.io/client-go/kubernetes".Clientset) as type "github.com/kubernetes-incubator/external-storage/vendor/k8s.io/client-go/kubernetes".Interface in ar                           gument to controller.NewProvisionController:
			*"k8s.io/client-go/kubernetes".Clientset does not implement "github.com/kubernetes-incubator/external-storage/vendor/k8s.io/client-go/kubernetes".Interface (wrong type for Admissionregistration metho                           d)
					have Admissionregistration() "k8s.io/client-go/kubernetes/typed/admissionregistration/v1alpha1".AdmissionregistrationV1alpha1Interface
					want Admissionregistration() "github.com/kubernetes-incubator/external-storage/vendor/k8s.io/client-go/kubernetes/typed/admissionregistration/v1alpha1".AdmissionregistrationV1alpha1Interface
	Makefile:27: recipe for target 'all' failed
	make: *** [all] Error 2
	root@km:~/kubernetes.io/TUTORIALS/Stateful-Applications/cephfs-stateful/external-storage/ceph/cephfs#

又报错。

这个好像是说冲突了嘛。。。

农总来了。


::


	root@km:~/kubernetes.io/TUTORIALS/Stateful-Applications/cephfs-stateful/external-storage# ln -s ~/kubernetes.io/TUTORIALS/Stateful-Applications/cephfs-stateful/external-storage/lib build/src/github.com/kubernetes-incubator/external-storage/
	root@km:~/kubernetes.io/TUTORIALS/Stateful-Applications/cephfs-stateful/external-storage# make ceph/cephfs
	cd ceph/cephfs; \
	make container
	make[1]: Entering directory '/root/kubernetes.io/TUTORIALS/Stateful-Applications/cephfs-stateful/external-storage/ceph/cephfs'
	CGO_ENABLED=0 GOOS=linux go build -a -ldflags '-extldflags "-static"' -o cephfs-provisioner cephfs-provisioner.go
	docker build -t quay.io/external_storage/cephfs-provisioner:latest .
	Sending build context to Docker daemon  36.23MB
	Step 1/6 : FROM centos:7
	7: Pulling from library/centos
	d9aaf4d82f24: Pull complete
	Digest: sha256:eba772bac22c86d7d6e72421b4700c3f894ab6e35475a34014ff8de74c10872e
	Status: Downloaded newer image for centos:7
	 ---> 196e0ce0c9fb
	Step 2/6 : ENV CEPH_VERSION "jewel"
	 ---> Running in c48e286ca165
	 ---> e9689ae3f521
	Removing intermediate container c48e286ca165
	Step 3/6 : RUN rpm -Uvh https://download.ceph.com/rpm-$CEPH_VERSION/el7/noarch/ceph-release-1-1.el7.noarch.rpm &&   yum install -y epel-release &&   yum install -y ceph-common python-cephfs
	 ---> Running in 69640c268019
	warning: /var/tmp/rpm-tmp.6mAWB0: Header V4 RSA/SHA256 Signature, key ID 460f3994: NOKEY
	Retrieving https://download.ceph.com/rpm-jewel/el7/noarch/ceph-release-1-1.el7.noarch.rpm
	Preparing...                          ########################################
	Updating / installing...
	ceph-release-1-1.el7                  ########################################
	Loaded plugins: fastestmirror, ovl
	Determining fastest mirrors
	 * base: mirrors.cn99.com
	 * extras: mirrors.cn99.com
	 * updates: mirrors.cn99.com


	  userspace-rcu.x86_64 0:0.7.16-1.el7
	....
	...
	... # 好长的一断时间，大约半个小时后
	Complete!
	 ---> 21cae91539c0
	Removing intermediate container b1c88201e72b
	Step 4/6 : COPY cephfs-provisioner /usr/local/bin/cephfs-provisioner
	 ---> 73664d0eca7a
	Removing intermediate container c076e6de19f0
	Step 5/6 : COPY cephfs_provisioner/cephfs_provisioner.py /usr/local/bin/cephfs_provisioner
	 ---> 25bc4c6da1d1
	Removing intermediate container 0eba40388f99
	Step 6/6 : CMD chmod o+x /usr/local/bin/cephfs_provisioner
	 ---> Running in 5bf6e5fbc6e1
	 ---> 262cba7e52ed
	Removing intermediate container 5bf6e5fbc6e1
	Successfully built 262cba7e52ed
	Successfully tagged quay.io/external_storage/cephfs-provisioner:latest
	docker tag quay.io/external_storage/cephfs-provisioner:latest quay.io/external_storage/cephfs-provisioner:latest
	make[1]: Leaving directory '/root/kubernetes.io/TUTORIALS/Stateful-Applications/cephfs-stateful/external-storage/ceph/cephfs'
	root@km:~/kubernetes.io/TUTORIALS/Stateful-Applications/cephfs-stateful/external-storage# 
	
成功了，看一下 docker image 是不是多了一个 quay.io/external_storage/cephfs-provisioner:latest


::

	root@km:~/kubernetes.io/TUTORIALS/Stateful-Applications/cephfs-stateful/external-storage# docker images
	REPOSITORY                                               TAG                 IMAGE ID            CREATED             SIZE
	quay.io/external_storage/cephfs-provisioner              latest              262cba7e52ed        5 minutes ago       485MB
	gcr.io/google-samples/hello-frontend                     1.0                 d8ca9fb857d9        3 weeks ago         183MB

果然有了。

这样，make 算是完成了。
