==============================
10-kubeadm.conf
==============================


::
    root@km:/etc/systemd/system/kubelet.service.d# cat 10-kubeadm.conf 
    [Service]
    Environment="KUBELET_KUBECONFIG_ARGS=--bootstrap-kubeconfig=/etc/kubernetes/bootstrap-kubelet.conf --kubeconfig=/etc/kubernetes/kubelet.conf"
    Environment="KUBELET_SYSTEM_PODS_ARGS=--pod-manifest-path=/etc/kubernetes/manifests --allow-privileged=true"
    Environment="KUBELET_NETWORK_ARGS=--network-plugin=cni --cni-conf-dir=/etc/cni/net.d --cni-bin-dir=/opt/cni/bin"
    Environment="KUBELET_DNS_ARGS=--cluster-dns=10.96.0.10 --cluster-domain=cluster.local"
    Environment="KUBELET_AUTHZ_ARGS=--authorization-mode=Webhook --client-ca-file=/etc/kubernetes/pki/ca.crt"
    Environment="KUBELET_CADVISOR_ARGS=--cadvisor-port=0"
    Environment="KUBELET_CERTIFICATE_ARGS=--rotate-certificates=true --cert-dir=/var/lib/kubelet/pki"
    #ExecStart=/usr/bin/kubelet -v 5
    ExecStart=/usr/bin/kubelet -v 5 $KUBELET_KUBECONFIG_ARGS $KUBELET_SYSTEM_PODS_ARGS $KUBELET_NETWORK_ARGS $KUBELET_DNS_ARGS $KUBELET_AUTHZ_ARGS $KUBELET_CADVISOR_ARGS $KUBELET_CERTIFICATE_ARGS

和下面这个

::

    root@km:/etc/systemd/system/kubelet.service.d# cat 10-kubeadm.conf 
    [Service]
    ExecStart=/usr/bin/kubelet  --bootstrap-kubeconfig=/etc/kubernetes/bootstrap-kubelet.conf --kubeconfig=/etc/kubernetes/kubelet.conf  --pod-manifest-path=/etc/kubernetes/manifests --allow-privileged=true   --cluster-dns=10.96.0.10 --cluster-domain=cluster.local  --authorization-mode=Webhook --client-ca-file=/etc/kubernetes/pki/ca.crt  --authorization-mode=Webhook --client-ca-file=/etc/kubernetes/pki/ca.crt --cadvisor-port=0 --rotate-certificates=true --cert-dir=/var/lib/kubelet/pki 


这2个效果是一样的。
