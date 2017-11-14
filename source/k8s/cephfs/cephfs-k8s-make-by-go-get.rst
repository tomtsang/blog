=============================
cephfs-k8s-make-by-go-get
=============================

安装 golang 1.7 以上的版本。 我们这里安装 1.9.1

	cd /home/jlch/
	tar -xvf go1.9.2.linux-amd64.tar
	ls go
	export PATH=$PATH:/home/jlch/go/bin

验证go 
=========

::

	go version
	
配置 GOPATH
===============

::

	mkdir gopath
	export GOPATH=/home/jlch/gopath/

go get 
====================

::

	go get github.com/kubernetes-incubator/external-storage

配置 Dockerfile
================

后来发现 docker image 的文件不对。

这个地方的 ENV CEPH_VERSION "jewel" 应该修改成 ENV CEPH_VERSION "luminous"

然后再 make

::

	cd /github.com/kubernetes-incubator/external-storage/ceph/cephfs/
	vi Dockerfile

看一下。

::

	root@km:~/cephfs# cat ~/kubernetes.io/TUTORIALS/Stateful-Applications/cephfs-stateful/external-storage/ceph/cephfs/Dockerfile
	# Copyright 2017 The Kubernetes Authors.
	#
	# Licensed under the Apache License, Version 2.0 (the "License");
	# you may not use this file except in compliance with the License.
	# You may obtain a copy of the License at
	#
	#     http://www.apache.org/licenses/LICENSE-2.0
	#
	# Unless required by applicable law or agreed to in writing, software
	# distributed under the License is distributed on an "AS IS" BASIS,
	# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
	# See the License for the specific language governing permissions and
	# limitations under the License.

	FROM centos:7

	ENV CEPH_VERSION "jewel"
	RUN rpm -Uvh https://download.ceph.com/rpm-$CEPH_VERSION/el7/noarch/ceph-release-1-1.el7.noarch.rpm && \
	yum install -y epel-release && \
	yum install -y ceph-common python-cephfs

	COPY cephfs-provisioner /usr/local/bin/cephfs-provisioner
	COPY cephfs_provisioner/cephfs_provisioner.py /usr/local/bin/cephfs_provisioner
	CMD ["chmod", "o+x", "/usr/local/bin/cephfs_provisioner"]
	root@km:~/cephfs# 


make
====================

::

	cd gopath/src/
	cd ./github.com/kubernetes-incubator/external-storage/ceph/cephfs/
	make ceph/cephfs/
	
这个时候，在 ceph/cephfs/ 下会多出一个 cephfs-provisioner 文件

::

	[tom@test_240 cephfs]$ ls
	cephfs_provisioner  cephfs-provisioner  cephfs-provisioner.go  ceph-secret-admin.yaml  CHANGELOG.md  claim.yaml  class.yaml  configmap.yaml  deployment.yaml  Dockerfile  local-start.sh  Makefile  OWNERS  README.md  test-pod.yaml
	[tom@test_240 cephfs]$ 

make push
====================

生成 docker image , quay.io/external_storage/cephfs-provisioner

::

	make push

如果说出现下面这个样子，说明是 make 成了 docker image了，但是 Push 没有成功（应该是指 push 到 docker.io 没有成功）

::

	79c182856123: Preparing 
	cf516324493c: Preparing 
	unauthorized: access to the requested resource is not authorized
	make: *** [push] 错误 1
	[tom@test_240 cephfs]$ 

push 到 registry
============================

因为有 reg.jlch.com:5000 这个 registry 了，先登录

::

	docker login reg.jlch.com:5000
	docker tag quay.io/external_storage/cephfs-provisioner:latest reg.jlch.com:5000/quay.io/external_storage/cephfs-provisioner:20171114
	docker push reg.jlch.com:5000/quay.io/external_storage/cephfs-provisioner:20171114

删除

::

	docker rmi reg.jlch.com:5000/quay.io/external_storage/cephfs-provisioner:20171114

game over
============================


