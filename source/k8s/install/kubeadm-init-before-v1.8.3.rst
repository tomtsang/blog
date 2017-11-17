==================================
kubeadm-init-before-v1.8.3
==================================

在运行 kubeadm init 之前的动作


kubelet 服务检查
==========================================================

后来发现，在这里应该测试一下 kubelet.service。

原来，虽然我 apt install kubelet , 但是， 遗留了之前 kubeadm 的一些配置.(应该把它们清空的.) 如: /etc/systemd/system/kubelet.service.d/10-kubeadm.conf 

让我们来吧。

删除这个新安装的 kubelet
-----------------------------------------------------

::

    root@km:/etc/cni/net.d# 
    root@km:/etc/cni/net.d# apt remove kubelet
    Reading package lists... Done
    Building dependency tree       
    Reading state information... Done
    The following packages were automatically installed and are no longer required:
    ebtables golang-1.8-go golang-1.8-race-detector-runtime golang-1.8-src kubernetes-cni socat
    Use 'apt autoremove' to remove them.
    The following packages will be REMOVED:
    kubeadm kubelet
    0 upgraded, 0 newly installed, 2 to remove and 11 not upgraded.
    After this operation, 274 MB disk space will be freed.
    Do you want to continue? [Y/n] y
    (Reading database ... 113087 files and directories currently installed.)
    Removing kubeadm (1.8.3-00) ...
    Removing kubelet (1.8.3-00) ...
    root@km:/etc/cni/net.d# 
    root@km: ~# cd /lib/systemd/system
    root@km:/lib/systemd/system# ls kube*
    ls: cannot access 'kube*': No such file or directory
    root@km:/lib/systemd/system# 

删除原 conf 文件
----------------------------------------------------

删除从 /lib/systemd/system/kubelet.service 下看到的这个 conf 文件

::

    root@km:/opt/cni/bin# cd /etc/systemd/system/kubelet.service.d/
    root@km:/etc/systemd/system/kubelet.service.d# ls
    10-kubeadm.conf
    root@km:/etc/systemd/system/kubelet.service.d# rm 10-kubeadm.conf 
    root@km:/etc/systemd/system/kubelet.service.d# cd ..
    root@km:/etc/systemd/system# rmdir kubelet.service.d/
    root@km:/etc/systemd/system# 

再安装 kubelet
-----------------------------

::

    root@km:/lib/systemd/system# apt install kubelet
    Reading package lists... Done
    Setting up kubelet (1.8.3-00) ...
    root@km:/lib/systemd/system# systemctl start kubelet
    root@km:/lib/systemd/system# systemctl status kubelet
    ● kubelet.service - kubelet: The Kubernetes Node Agent
    Loaded: loaded (/lib/systemd/system/kubelet.service; enabled; vendor preset: enabled)
    Active: activating (auto-restart) (Result: exit-code) since Fri 2017-11-17 17:34:35 CST; 1s ago
        Docs: http://kubernetes.io/docs/
    Process: 25074 ExecStart=/usr/bin/kubelet (code=exited, status=1/FAILURE)
    Main PID: 25074 (code=exited, status=1/FAILURE)

    Nov 17 17:34:35 km systemd[1]: kubelet.service: Unit entered failed state.
    Nov 17 17:34:35 km systemd[1]: kubelet.service: Failed with result 'exit-code'.
    root@km:/lib/systemd/system# 

还出错了

::
    root@km:/lib/systemd/system# cat kubelet.service 
    [Unit]
    Description=kubelet: The Kubernetes Node Agent
    Documentation=http://kubernetes.io/docs/

    [Service]
    ExecStart=/usr/bin/kubelet
    Restart=always
    StartLimitInterval=0
    RestartSec=10

    [Install]
    WantedBy=multi-user.target
    root@km:/lib/systemd/system# journalctl -xe
    .....
    Nov 17 17:36:51 km kubelet[25463]: error: failed to run Kubelet: Running with swap on is not supported, please disable swap! or set --fail-swap-on flag to false. /proc/swaps contained: [Filename 

从信息中看到，要把 swap 关闭，或者带 --fail-swap-on 参数。
那就带参数试一下先。

::

    root@km:/lib/systemd/system# kubelet --fail-swap-on=false

成功逃过这个错误。
但是报了另一个错误。

::
    
    root@km:/lib/systemd/system# kubelet --fail-swap-on=false
    W1117 17:48:12.355020   27348 cni.go:196] Unable to update cni config: No networks found in /etc/cni/net.d
    I1117 17:48:12.361647   27348 docker_service.go:207] Docker cri networking managed by kubernetes.io/no-op
    error: failed to run Kubelet: failed to create kubelet: misconfiguration: kubelet cgroup driver: "cgroupfs" is different from docker cgroup driver: "systemd"

这个显然与 docker 相关。

::

    root@km:/lib/systemd/system# cat /etc/docker/daemon.json 
    {  
        "exec-opts": ["native.cgroupdriver=systemd"],
        "registry-mirrors": ["https://0d6wdn2y.mirror.aliyuncs.com"]
    }

那好了，先把这个后来加进来的 "exec-opts": ["native.cgroupdriver=systemd"], 删除了吧。

    root@km:/lib/systemd/system# cat /etc/docker/daemon.json 
    {  
        "registry-mirrors": ["https://0d6wdn2y.mirror.aliyuncs.com"]
    }
    root@km:/lib/systemd/system# sudo systemctl restart docker
    ...
    root@km:/lib/systemd/system# kubelet --fail-swap-on=false
    I1117 17:52:52.954982   28490 feature_gate.go:156] feature gates: map[]
    I1117 17:52:52.955098   28490 controller.go:114] kubelet config controller: starting controller
    I1117 17:52:52.955117   28490 controller.go:118] kubelet config controller: validating combination of defaults and flags
    I1117 17:52:52.974917   28490 client.go:75] Connecting to docker on unix:///var/run/docker.sock
    I1117 17:52:52.974977   28490 client.go:95] Start docker client with request timeout=2m0s
    W1117 17:52:52.977139   28490 cni.go:196] Unable to update cni config: No networks found in /etc/cni/net.d
    I1117 17:52:52.983192   28490 feature_gate.go:156] feature gates: map[]
    W1117 17:52:52.983420   28490 server.go:289] --cloud-provider=auto-detect is deprecated. The desired cloud provider should be set explicitly
    W1117 17:52:52.983465   28490 server.go:324] standalone mode, no API client
    I1117 17:52:52.983810   28490 manager.go:149] cAdvisor running in container: "/sys/fs/cgroup/cpu,cpuacct/user.slice"
    W1117 17:52:53.017865   28490 manager.go:157] unable to connect to Rkt api service: rkt: cannot tcp Dial rkt api service: dial tcp [::1]:15441: getsockopt: connection refused
    W1117 17:52:53.018035   28490 manager.go:166] unable to connect to CRI-O api service: Get http://%2Fvar%2Frun%2Fcrio.sock/info: dial unix /var/run/crio.sock: connect: no such file or directory
    I1117 17:52:53.049774   28490 fs.go:139] Filesystem UUIDs: map[90ef4dc9-3b9e-4031-ad7f-209b328d2f3b:/dev/sda1 2017-02-15-20-36-22-00:/dev/sr0 3687da9b-d812-42a1-b42c-a3d3e7178372:/dev/dm-0 8679fa71-e60b-4686-a5fa-9a0cc5023568:/dev/dm-1]
    I1117 17:52:53.049836   28490 fs.go:140] Filesystem partitions: map[tmpfs:{mountpoint:/run major:0 minor:19 fsType:tmpfs blockSize:0} /dev/mapper/ubuntu--vg-root:{mountpoint:/var/lib/docker/aufs major:252 minor:0 fsType:ext4 blockSize:0} /dev/sda1:{mountpoint:/boot major:8 minor:1 fsType:ext2 blockSize:0}]
    I1117 17:52:53.052104   28490 manager.go:216] Machine: {NumCores:4 CpuFrequency:2097571 MemoryCapacity:8371290112 HugePages:[{PageSize:1048576 NumPages:0} {PageSize:2048 NumPages:0}] MachineID:78cb13728eba6f6c819e6dea599a5db9 SystemUUID:564D7A67-BDF7-E109-61AC-DDC9929A346F BootID:8f8d7bb3-abb6-4d6a-b1ee-261cd1a2cc74 Filesystems:[{Device:tmpfs DeviceMajor:0 DeviceMinor:19 Capacity:837132288 Type:vfs Inodes:1021886 HasInodes:true} {Device:/dev/mapper/ubuntu--vg-root DeviceMajor:252 DeviceMinor:0 Capacity:75452612608 Type:vfs Inodes:4694016 HasInodes:true} {Device:/dev/sda1 DeviceMajor:8 DeviceMinor:1 Capacity:494512128 Type:vfs Inodes:124928 HasInodes:true}] DiskMap:map[252:0:{Name:dm-0 Major:252 Minor:0 Size:76793511936 Scheduler:none} 252:1:{Name:dm-1 Major:252 Minor:1 Size:8589934592 Scheduler:none} 2:0:{Name:fd0 Major:2 Minor:0 Size:4096 Scheduler:deadline} 8:0:{Name:sda Major:8 Minor:0 Size:85899345920 Scheduler:deadline}] NetworkDevices:[{Name:cni0 MacAddress:0a:58:0a:f4:00:01 Speed:0 Mtu:1500} {Name:ens160 MacAddress:00:0c:29:9a:34:6f Speed:10000 Mtu:1500} {Name:flannel.1 MacAddress:b2:6d:04:a9:38:8d Speed:0 Mtu:1450}] Topology:[{Id:0 Memory:8371290112 Cores:[{Id:0 Threads:[0] Caches:[]} {Id:1 Threads:[1] Caches:[]} {Id:2 Threads:[2] Caches:[]} {Id:3 Threads:[3] Caches:[]}] Caches:[{Size:20971520 Type:Unified Level:3}]}] CloudProvider:Unknown InstanceType:Unknown InstanceID:None}
    I1117 17:52:53.053151   28490 manager.go:222] Version: {KernelVersion:4.4.0-62-generic ContainerOsVersion:Ubuntu 16.04.3 LTS DockerVersion:17.05.0-ce DockerAPIVersion:1.29 CadvisorVersion: CadvisorRevision:}
    W1117 17:52:53.053992   28490 server.go:232] No api server defined - no events will be sent to API server.
    I1117 17:52:53.054023   28490 server.go:422] --cgroups-per-qos enabled, but --cgroup-root was not specified.  defaulting to /
    I1117 17:52:53.056245   28490 container_manager_linux.go:252] container manager verified user specified cgroup-root exists: /
    I1117 17:52:53.056284   28490 container_manager_linux.go:257] Creating Container Manager object based on Node Config: {RuntimeCgroupsName: SystemCgroupsName: KubeletCgroupsName: ContainerRuntime:docker CgroupsPerQOS:true CgroupRoot:/ CgroupDriver:cgroupfs ProtectKernelDefaults:false NodeAllocatableConfig:{KubeReservedCgroupName: SystemReservedCgroupName: EnforceNodeAllocatable:map[pods:{}] KubeReserved:map[] SystemReserved:map[] HardEvictionThresholds:[{Signal:memory.available Operator:LessThan Value:{Quantity:100Mi Percentage:0} GracePeriod:0s MinReclaim:<nil>} {Signal:nodefs.available Operator:LessThan Value:{Quantity:<nil> Percentage:0.1} GracePeriod:0s MinReclaim:<nil>} {Signal:nodefs.inodesFree Operator:LessThan Value:{Quantity:<nil> Percentage:0.05} GracePeriod:0s MinReclaim:<nil>}]} ExperimentalQOSReserved:map[] ExperimentalCPUManagerPolicy:none ExperimentalCPUManagerReconcilePeriod:10s}
    I1117 17:52:53.056462   28490 container_manager_linux.go:288] Creating device plugin handler: false
    W1117 17:52:53.061351   28490 kubelet_network.go:69] Hairpin mode set to "promiscuous-bridge" but kubenet is not enabled, falling back to "hairpin-veth"
    I1117 17:52:53.061391   28490 kubelet.go:517] Hairpin mode set to "hairpin-veth"
    W1117 17:52:53.065042   28490 cni.go:196] Unable to update cni config: No networks found in /etc/cni/net.d
    I1117 17:52:53.071790   28490 docker_service.go:207] Docker cri networking managed by kubernetes.io/no-op
    I1117 17:52:53.102961   28490 docker_service.go:224] Setting cgroupDriver to cgroupfs
    I1117 17:52:53.140011   28490 remote_runtime.go:43] Connecting to runtime service unix:///var/run/dockershim.sock
    I1117 17:52:53.142110   28490 kuberuntime_manager.go:178] Container runtime docker initialized, version: 17.05.0-ce, apiVersion: 1.29.0
    W1117 17:52:53.142331   28490 probe.go:215] Flexvolume plugin directory at /usr/libexec/kubernetes/kubelet-plugins/volume/exec/ does not exist. Recreating.
    I1117 17:52:53.144299   28490 server.go:718] Started kubelet v1.8.3
    E1117 17:52:53.144359   28490 kubelet.go:1234] Image garbage collection failed once. Stats initialization may not have completed yet: failed to get imageFs info: unable to find data for container /
    W1117 17:52:53.144478   28490 kubelet.go:1318] No api server defined - no node status update will be sent.
    I1117 17:52:53.144496   28490 server.go:128] Starting to listen on 0.0.0.0:10250
    I1117 17:52:53.145570   28490 kubelet_node_status.go:280] Setting node annotation to enable volume controller attach/detach
    I1117 17:52:53.146415   28490 server.go:296] Adding debug handlers to kubelet server.
    I1117 17:52:53.159723   28490 fs_resource_analyzer.go:66] Starting FS ResourceAnalyzer
    I1117 17:52:53.159773   28490 status_manager.go:136] Kubernetes client is nil, not starting status manager.
    I1117 17:52:53.159791   28490 kubelet.go:1768] Starting kubelet main sync loop.
    I1117 17:52:53.159842   28490 kubelet.go:1779] skipping pod synchronization - [container runtime is down PLEG is not healthy: pleg was last seen active 2562047h47m16.854775807s ago; threshold is 3m0s]
    W1117 17:52:53.160705   28490 container_manager_linux.go:869] CPUAccounting not enabled for pid: 28490
    W1117 17:52:53.160728   28490 container_manager_linux.go:872] MemoryAccounting not enabled for pid: 28490
    E1117 17:52:53.160816   28490 container_manager_linux.go:603] [ContainerManager]: Fail to get rootfs information unable to find data for container /
    I1117 17:52:53.160887   28490 volume_manager.go:246] Starting Kubelet Volume Manager
    I1117 17:52:53.228692   28490 factory.go:355] Registering Docker factory
    W1117 17:52:53.228739   28490 manager.go:265] Registration of the rkt container factory failed: unable to communicate with Rkt api service: rkt: cannot tcp Dial rkt api service: dial tcp [::1]:15441: getsockopt: connection refused
    W1117 17:52:53.228910   28490 manager.go:276] Registration of the crio container factory failed: Get http://%2Fvar%2Frun%2Fcrio.sock/info: dial unix /var/run/crio.sock: connect: no such file or directory
    I1117 17:52:53.228931   28490 factory.go:54] Registering systemd factory
    I1117 17:52:53.229203   28490 factory.go:86] Registering Raw factory
    I1117 17:52:53.229466   28490 manager.go:1140] Started watching for new ooms in manager
    I1117 17:52:53.230193   28490 manager.go:311] Starting recovery of all containers
    I1117 17:52:53.342529   28490 manager.go:316] Recovery completed
    I1117 17:52:53.487640   28490 kubelet_node_status.go:280] Setting node annotation to enable volume controller attach/detach
    E1117 17:52:53.522074   28490 summary.go:92] Failed to get system container stats for "/user.slice/user-1000.slice/session-553.scope": failed to get cgroup stats for "/user.slice/user-1000.slice/session-553.scope": failed to get container info for "/user.slice/user-1000.slice/session-553.scope": unknown container "/user.slice/user-1000.slice/session-553.scope"
    W1117 17:52:53.522142   28490 helpers.go:847] eviction manager: no observation found for eviction signal allocatableNodeFs.available
    I1117 17:53:03.522396   28490 kubelet_node_status.go:280] Setting node annotation to enable volume controller attach/detach
    E1117 17:53:03.554477   28490 summary.go:92] Failed to get system container stats for "/user.slice/user-1000.slice/session-553.scope": failed to get cgroup stats for "/user.slice/user-1000.slice/session-553.scope": failed to get container info for "/user.slice/user-1000.slice/session-553.scope": unknown container "/user.slice/user-1000.slice/session-553.scope"
    W1117 17:53:03.554543   28490 helpers.go:847] eviction manager: no observation found for eviction signal allocatableNodeFs.available

这下是真的成功了。

打断它。把 --fail-swap-on=false 参数，加到 启动文件下吧。

::

    cd /lib/systemd/system
    root@km:/lib/systemd/system# vi kubelet.service 
    root@km:/lib/systemd/system# cat kubelet.service 
    root@km:/lib/systemd/system# systemctl daemon-reload 
    root@km:/lib/systemd/system# systemctl start kubelet
    root@km:/lib/systemd/system# systemctl status kubelet
    ● kubelet.service - kubelet: The Kubernetes Node Agent
    Loaded: loaded (/lib/systemd/system/kubelet.service; enabled; vendor preset: enabled)
    Active: active (running) since Fri 2017-11-17 17:54:21 CST; 15s ago
        Docs: http://kubernetes.io/docs/
    Main PID: 28824 (kubelet)
        Tasks: 14
    Memory: 22.5M
        CPU: 1.005s
    CGroup: /system.slice/kubelet.service
            └─28824 /usr/bin/kubelet --fail-swap-on=false

    Nov 17 17:54:21 km kubelet[28824]: I1117 17:54:21.815998   28824 factory.go:54] Registering systemd factory
    Nov 17 17:54:21 km kubelet[28824]: I1117 17:54:21.816270   28824 factory.go:86] Registering Raw factory
    Nov 17 17:54:21 km kubelet[28824]: I1117 17:54:21.816520   28824 manager.go:1140] Started watching for new ooms in manager
    Nov 17 17:54:21 km kubelet[28824]: I1117 17:54:21.817206   28824 manager.go:311] Starting recovery of all containers
    Nov 17 17:54:21 km kubelet[28824]: I1117 17:54:21.915504   28824 manager.go:316] Recovery completed
    Nov 17 17:54:22 km kubelet[28824]: I1117 17:54:22.050494   28824 kubelet_node_status.go:280] Setting node annotation to enable volume controller attach/detach
    Nov 17 17:54:22 km kubelet[28824]: W1117 17:54:22.082192   28824 helpers.go:847] eviction manager: no observation found for eviction signal allocatableNodeFs.available
    Nov 17 17:54:31 km systemd[1]: Started kubelet: The Kubernetes Node Agent.
    Nov 17 17:54:32 km kubelet[28824]: I1117 17:54:32.082476   28824 kubelet_node_status.go:280] Setting node annotation to enable volume controller attach/detach
    Nov 17 17:54:32 km kubelet[28824]: W1117 17:54:32.114163   28824 helpers.go:847] eviction manager: no observation found for eviction signal allocatableNodeFs.available
    root@km:/lib/systemd/system# 

如果说, 想保证重开机也生效,那就直接
https://askubuntu.com/questions/214805/how-do-i-disable-swap
来吧.

::

    sudo swapoff -a  
    
成功了。

kubelet stop
------------------------------

因为 kubeadm v1.8.3 在 kubeadm init 的过程中会自动启动 kubelet ,所以这里要把 kubelet stop了。

::

    root@km:/lib/systemd/system# systemctl stop kubelet

好了。kubelet 这一块是OK了。
