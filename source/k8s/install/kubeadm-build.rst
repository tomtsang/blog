==================================================
kubeadm-build
==================================================



build kubeadm 
===================================================

修改 Kubeadm 使得 /etc/kubernetes/manifests/ 下的 *.yaml 文件带有 imagePullPolicy: IfNotPresent

env
---------------------------------------------------

192.168.31.114 jlch

gopath
---------------------------------------------------

::

    cd ~/gopath/src/github.com/kubernetes/
    git clone https://github.com/kubernetes/kubernetes.git
    cd kubernetes

修改吧
---------------------------------------------------


::

    jlch@mon1:~/gopath/src/github.com/kubernetes/kubernetes$ git status
    Not currently on any branch.
    Changes not staged for commit:
    (use "git add <file>..." to update what will be committed)
    (use "git checkout -- <file>..." to discard changes in working directory)

        modified:   cmd/kubeadm/app/phases/controlplane/manifests.go
        modified:   cmd/kubeadm/app/phases/etcd/local.go

    no changes added to commit (use "git add" and/or "git commit -a")
    jlch@mon1:~/gopath/src/github.com/kubernetes/kubernetes$ git diff cmd/kubeadm/app/phases/controlplane/manifests.go
    diff --git a/cmd/kubeadm/app/phases/controlplane/manifests.go b/cmd/kubeadm/app/phases/controlplane/manifests.go
    index 7d2784d..5b2833f 100644
    --- a/cmd/kubeadm/app/phases/controlplane/manifests.go
    +++ b/cmd/kubeadm/app/phases/controlplane/manifests.go
    @@ -75,6 +75,7 @@ func GetStaticPodSpecs(cfg *kubeadmapi.MasterConfiguration, k8sVersion *version.
                    kubeadmconstants.KubeAPIServer: staticpodutil.ComponentPod(v1.Container{
                            Name:          kubeadmconstants.KubeAPIServer,
                            Image:         images.GetCoreImage(kubeadmconstants.KubeAPIServer, cfg.GetControlPlaneImageRepository(), cfg.KubernetesVersion, cfg.UnifiedControlPlaneImage),
    +                        ImagePullPolicy: v1.PullIfNotPresent,
                            Command:       getAPIServerCommand(cfg, k8sVersion),
                            VolumeMounts:  mounts.GetVolumeMounts(kubeadmconstants.KubeAPIServer),
                            LivenessProbe: staticpodutil.ComponentProbe(int(cfg.API.BindPort), "/healthz", v1.URISchemeHTTPS),
    @@ -84,6 +85,7 @@ func GetStaticPodSpecs(cfg *kubeadmapi.MasterConfiguration, k8sVersion *version.
                    kubeadmconstants.KubeControllerManager: staticpodutil.ComponentPod(v1.Container{
                            Name:          kubeadmconstants.KubeControllerManager,
                            Image:         images.GetCoreImage(kubeadmconstants.KubeControllerManager, cfg.GetControlPlaneImageRepository(), cfg.KubernetesVersion, cfg.UnifiedControlPlaneImage),
    +                        ImagePullPolicy: v1.PullIfNotPresent,
                            Command:       getControllerManagerCommand(cfg, k8sVersion),
                            VolumeMounts:  mounts.GetVolumeMounts(kubeadmconstants.KubeControllerManager),
                            LivenessProbe: staticpodutil.ComponentProbe(10252, "/healthz", v1.URISchemeHTTP),
    @@ -93,6 +95,7 @@ func GetStaticPodSpecs(cfg *kubeadmapi.MasterConfiguration, k8sVersion *version.
                    kubeadmconstants.KubeScheduler: staticpodutil.ComponentPod(v1.Container{
                            Name:          kubeadmconstants.KubeScheduler,
                            Image:         images.GetCoreImage(kubeadmconstants.KubeScheduler, cfg.GetControlPlaneImageRepository(), cfg.KubernetesVersion, cfg.UnifiedControlPlaneImage),
    +                        ImagePullPolicy: v1.PullIfNotPresent,
                            Command:       getSchedulerCommand(cfg),
                            VolumeMounts:  mounts.GetVolumeMounts(kubeadmconstants.KubeScheduler),
                            LivenessProbe: staticpodutil.ComponentProbe(10251, "/healthz", v1.URISchemeHTTP),
    jlch@mon1:~/gopath/src/github.com/kubernetes/kubernetes$ git diff cmd/kubeadm/app/phases/etcd/local.go
    diff --git a/cmd/kubeadm/app/phases/etcd/local.go b/cmd/kubeadm/app/phases/etcd/local.go
    index e947794..8cc609a 100644
    --- a/cmd/kubeadm/app/phases/etcd/local.go
    +++ b/cmd/kubeadm/app/phases/etcd/local.go
    @@ -54,6 +54,7 @@ func GetEtcdPodSpec(cfg *kubeadmapi.MasterConfiguration) v1.Pod {
                    Name:    kubeadmconstants.Etcd,
                    Command: getEtcdCommand(cfg),
                    Image:   images.GetCoreImage(kubeadmconstants.Etcd, cfg.ImageRepository, "", cfg.Etcd.Image),
    +                ImagePullPolicy: v1.PullIfNotPresent,
                    // Mount the etcd datadir path read-write so etcd can store data in a more persistent manner
                    VolumeMounts:  []v1.VolumeMount{staticpodutil.NewVolumeMount(etcdVolumeName, cfg.Etcd.DataDir, false)},
                    LivenessProbe: staticpodutil.ComponentProbe(2379, "/health", v1.URISchemeHTTP),
    jlch@mon1:~/gopath/src/github.com/kubernetes/kubernetes$ 

make
---------------------------------------------------

::
    
    jlch@mon1:~/gopath/src/github.com/kubernetes/kubernetes$ make
    jlch@mon1:~/gopath/src/github.com/kubernetes/kubernetes$ cd _output/
    jlch@mon1:~/gopath/src/github.com/kubernetes/kubernetes/_output$ ls
    bin  local
    jlch@mon1:~/gopath/src/github.com/kubernetes/kubernetes/_output$ cd bin/
    jlch@mon1:~/gopath/src/github.com/kubernetes/kubernetes/_output/bin$ ls
    apiextensions-apiserver   conversion-gen  defaulter-gen  e2e.test  genfeddocs   genman              genyaml  gke-certificates-controller  hyperkube  kube-aggregator  kube-controller-manager  kubefed  kubemark    kube-scheduler  openapi-gen
    cloud-controller-manager  deepcopy-gen    e2e_node.test  gendocs   genkubedocs  genswaggertypedocs  ginkgo   go-bindata                   kubeadm    kube-apiserver   kubectl                  kubelet  kube-proxy  linkcheck       teststale
    jlch@mon1:~/gopath/src/github.com/kubernetes/kubernetes/_output/bin$ ./kubeadm version
    kubeadm version: &version.Info{Major:"1", Minor:"8+", GitVersion:"v1.8.3-dirty", GitCommit:"f0efb3cb883751c5ffdbe6d515f3cb4fbe7b7acd", GitTreeState:"dirty", BuildDate:"2017-11-20T07:05:42Z", GoVersion:"go1.9.2", Compiler:"gc", Platform:"linux/amd64"}
    jlch@mon1:~/gopath/src/github.com/kubernetes/kubernetes/_output/bin$ 

好了，现在我们要的就是 kubeadm 文件。


