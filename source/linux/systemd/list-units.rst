================================
systemctl list-units
================================

systemctl list-units 可以查看所有已注册成 *.service 的服务。

::

    root@km:/etc/systemd/system/kubelet.service.d# sudo systemctl status kubelet
    ● kubelet.service - kubelet: The Kubernetes Node Agent
    Loaded: loaded (/lib/systemd/system/kubelet.service; enabled; vendor preset: enabled)
    Drop-In: /etc/systemd/system/kubelet.service.d
            └─10-kubeadm.conf
    Active: activating (auto-restart) (Result: exit-code) since Fri 2017-11-17 14:56:03 CST; 3s ago
        Docs: http://kubernetes.io/docs/
    Process: 30677 ExecStart=/usr/bin/kubelet $KUBELET_KUBECONFIG_ARGS $KUBELET_SYSTEM_PODS_ARGS $KUBELET_NETWORK_ARGS $KUBELET_DNS_ARGS $KUBELET_AUTHZ_ARGS $KUBELET_CADVISOR_ARGS $KUBELET_CERTIFICATE_ARGS $KUBELET_EXTRA_ARGS (code=exited, status=1/FAILURE)
    Main PID: 30677 (code=exited, status=1/FAILURE)

    Nov 17 14:56:03 km systemd[1]: kubelet.service: Unit entered failed state.
    Nov 17 14:56:03 km systemd[1]: kubelet.service: Failed with result 'exit-code'.
    root@km:/etc/systemd/system/kubelet.service.d# 

说明已有 kubelet 服务了哈。

但是，万一你不知道服务全名，这个时候，就可以用 list-units了。

::

    root@km:/etc/systemd/system/kubelet.service.d# systemctl list-units | grep kube
    kubelet.service                                                                           loaded activating auto-restart kubelet: The Kubernetes Node Agent
    root@km:/etc/systemd/system/kubelet.service.d# 
