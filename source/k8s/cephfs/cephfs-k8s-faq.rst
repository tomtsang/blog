===============================================
k8s 与 cephfs 相关的FAQ
===============================================

Input/output error 
===============================================

::

	cephu@ceph-client:/mnt/mycephfs/volumes/kubernetes$ ll kubernetes-dynamic-pvc-a2c667ad-d0c7-11e7-b656-0a580af40148/
	ls: reading directory 'kubernetes-dynamic-pvc-a2c667ad-d0c7-11e7-b656-0a580af40148/': Input/output error  
	total 0
	drwxr-xr-x 1 root root 0 Nov 24 11:36 ./
	drwxr-xr-x 1 root root 0 Nov 24 11:29 ../
	cephu@ceph-client:/mnt/mycephfs/volumes/kubernetes$ 

这个问题，看了一下，https://github.com/kubernetes-incubator/external-storage/issues/345， 

最后，有用户是这样回复的

::

    I tried to update my ubuntu kernel from 4.4.0 to 4.10.0 (sudo apt install linux-image-4.10.0-28-generic) and after a reboot, the error is gone, everything works fine from now on :)

所以就是升级内核了。升级去吧。
升级一下，果然成功了。


# what? 又是这个问题。见 k8s