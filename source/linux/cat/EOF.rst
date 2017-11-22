=======================================
EOF 输入
=======================================





https://kubernetes.io/docs/setup/independent/install-kubeadm/#installing-kubeadm-kubelet-and-kubectl

::

    root@km:/opt/cni/bin# cat <<EOF >/etc/cni/net.d/10-flannel.conf
    {
    "name": "cbr0",
    "type": "flannel",
    "delegate": {
        "isDefaultGateway": true
    }
    }
    EOF
    root@km:/opt/cni/bin# 
