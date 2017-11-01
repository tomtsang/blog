root@km:~/cephfs# cat claim.yaml
kind: PersistentVolumeClaim
apiVersion: v1
metadata:
  name: claim1
  annotations:
    volume.beta.kubernetes.io/storage-class: "cephfs"
spec:
  accessModes:
    - ReadWriteMany
  resources:
    requests:
      storage: 10Gi
	  
	  
	  
	  
	  
	  
	  
	  
	  
root@km:~/cephfs# cat class.yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: cephfs
provisioner: ceph.com/cephfs
parameters:
    #monitors: 172.24.0.6:6789
    monitors: 192.168.31.114:6789
    adminId: admin
    adminSecretName: ceph-secret-admin
    adminSecretNamespace: "kube-system"

	
	
	
	
root@km:~/cephfs# cat deployment.yaml
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  name: cephfs-provisioner
spec:
  replicas: 1
  strategy:
    type: Recreate
  template:
    metadata:
      labels:
        app: cephfs-provisioner
    spec:
      containers:
      - name: cephfs-provisioner
        image: "quay.io/external_storage/cephfs-provisioner:latest"
        imagePullPolicy: IfNotPresent
        env:
        - name: PROVISIONER_NAME
          valueFrom:
            configMapKeyRef:
              key: provisioner.name
              name: cephfs-provisioner
        command:
          - "/usr/local/bin/cephfs-provisioner"
        args:
          - "-id=cephfs-provisioner-1"
root@km:~/cephfs#




