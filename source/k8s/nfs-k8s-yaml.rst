root@km:~/kubernetes.io/TUTORIALS/Stateful-Applications/StatefulSet-Basics/v# cat test-claim.yaml
kind: PersistentVolumeClaim
apiVersion: v1
metadata:
  name: test-claim
  annotations:
    volume.beta.kubernetes.io/storage-class: "managed-nfs-storage"
spec:
  accessModes:
    - ReadWriteMany
  resources:
    requests:
      storage: 1Mi
	  
	  
	  
	  
	  
	  
	  
	  
	  
root@km:~/kubernetes.io/TUTORIALS/Stateful-Applications/StatefulSet-Basics/v# cat class.yaml
apiVersion: storage.k8s.io/v1beta1
kind: StorageClass
metadata:
  name: managed-nfs-storage
provisioner: fuseim.pri/ifs # or choose another name, must match deployment's env PROVISIONER_NAME'











root@km:~/kubernetes.io/TUTORIALS/Stateful-Applications/StatefulSet-Basics/v# cat deployment.yaml
kind: Deployment
apiVersion: extensions/v1beta1
metadata:
  name: nfs-client-provisioner
spec:
  replicas: 1
  strategy:
    type: Recreate
  template:
    metadata:
      labels:
        app: nfs-client-provisioner
    spec:
      containers:
        - name: nfs-client-provisioner
          image: quay.io/external_storage/nfs-client-provisioner:latest
          imagePullPolicy: IfNotPresent
          volumeMounts:
            - name: nfs-client-root
              mountPath: /persistentvolumes
          env:
            - name: PROVISIONER_NAME
              value: fuseim.pri/ifs
            - name: NFS_SERVER
              value: 192.168.31.232
            - name: NFS_PATH
              value: /data/nfs-storage/k8s-storage/ssd
      volumes:
        - name: nfs-client-root
          nfs:
            server: 192.168.31.232
            path: /data/nfs-storage/k8s-storage/ssd
root@km:~/kubernetes.io/TUTORIALS/Stateful-Applications/StatefulSet-Basics/v#
