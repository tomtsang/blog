=======================================
delete node
=======================================

::

    clear
    cat k8.export.sh 
    export KUBECONFIG=$HOME/admin.conf
    ls
    k get node
    kubectl drain kn1 --delete-local-data --force --ignore-daemonsets
    kubectl delete node kn1
    kubectl drain kn2 --delete-local-data --force --ignore-daemonsets
    kubectl delete node kn2
    k get node
    clear
    kubectl drain km --delete-local-data --force --ignore-daemonsets
    kubeadm reset
    k get pod --all-namespaces
    docker ps 
