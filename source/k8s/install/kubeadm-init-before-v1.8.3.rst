==================================
kubeadm-init-before-v1.8.3
==================================

在运行 kubeadm init 之前的动作


kubelet 服务检查
=====================

后来发现，在这里应该测试一下 kubelet.service。

原来，虽然我 apt install kubelet , 但是， 遗留了之前 kubeadm 的一些配置.(应该把它们清空的.) 如: /etc/systemd/system/kubelet.service.d/10-kubeadm.conf 



