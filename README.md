![image](https://github.com/user-attachments/assets/ba6865ab-f5e4-48b8-9bb3-2cf61448a5cc)


## Terraform Module - Rook ( Kubernetes )   | ⭐⭐⭐
Rook turns distributed storage systems into self-managing, self-scaling, self-healing storage services. It automates the tasks of a storage administrator: deployment, bootstrapping, configuration, provisioning, scaling, upgrading, migration, disaster recovery, monitoring, and resource management


🚀  Key Features
```
✅ Simple and reliable automated storage management
✅ Hyper-scale or hyper-converge your storage clusters
✅ Efficiently distribute and replicate data to minimize loss
✅ Provision, file, block, and object storage
✅ Manage open-source Ceph storage
✅ Easily enable elastic storage in your datacenters
✅ Optimize workloads on commodity hardware
```


### Components :
```
📃 Longhorn Backing Image Manager
📃 Longhorn Instance Manager
📃 Longhorn Manager
📃 Longhorn Share Manager
📃 Longhorn UI
```


🔨 Integration :
```
# MySQL
# MariaDB
# PostgresSQL
# Cassandra
# Kafka
# Mongo
# Elastic
```


🧩 Config 
```
apiVersion: ceph.rook.io/v1
kind: CephBlockPool
metadata:
  name: replicapool
  namespace: rook-ceph
spec:
  failureDomain: host
  replicated:
    size: 3
---
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
   name: rook-ceph-block
# Change "rook-ceph" provisioner prefix to match the operator namespace if needed
provisioner: rook-ceph.rbd.csi.ceph.com
parameters:
    # clusterID is the namespace where the rook cluster is running
    clusterID: rook-ceph
    # Ceph pool into which the RBD image shall be created
    pool: replicapool

    # (optional) mapOptions is a comma-separated list of map options.
    # For krbd options refer
    # https://docs.ceph.com/docs/master/man/8/rbd/#kernel-rbd-krbd-options
    # For nbd options refer
    # https://docs.ceph.com/docs/master/man/8/rbd-nbd/#options
    # mapOptions: lock_on_read,queue_depth=1024

    # (optional) unmapOptions is a comma-separated list of unmap options.
    # For krbd options refer
    # https://docs.ceph.com/docs/master/man/8/rbd/#kernel-rbd-krbd-options
    # For nbd options refer
    # https://docs.ceph.com/docs/master/man/8/rbd-nbd/#options
    # unmapOptions: force

    # RBD image format. Defaults to "2".
    imageFormat: "2"

    # RBD image features
    # Available for imageFormat: "2". Older releases of CSI RBD
    # support only the `layering` feature. The Linux kernel (KRBD) supports the
    # full complement of features as of 5.4
    # `layering` alone corresponds to Ceph's bitfield value of "2" ;
    # `layering` + `fast-diff` + `object-map` + `deep-flatten` + `exclusive-lock` together
    # correspond to Ceph's OR'd bitfield value of "63". Here we use
    # a symbolic, comma-separated format:
    # For 5.4 or later kernels:
    #imageFeatures: layering,fast-diff,object-map,deep-flatten,exclusive-lock
    # For 5.3 or earlier kernels:
    imageFeatures: layering

    # The secrets contain Ceph admin credentials.
    csi.storage.k8s.io/provisioner-secret-name: rook-csi-rbd-provisioner
    csi.storage.k8s.io/provisioner-secret-namespace: rook-ceph
    csi.storage.k8s.io/controller-expand-secret-name: rook-csi-rbd-provisioner
    csi.storage.k8s.io/controller-expand-secret-namespace: rook-ceph
    csi.storage.k8s.io/node-stage-secret-name: rook-csi-rbd-node
    csi.storage.k8s.io/node-stage-secret-namespace: rook-ceph

    # Specify the filesystem type of the volume. If not specified, csi-provisioner
    # will set default as `ext4`. Note that `xfs` is not recommended due to potential deadlock
    # in hyperconverged settings where the volume is mounted on the same node as the osds.
    csi.storage.k8s.io/fstype: ext4

# Delete the rbd volume when a PVC is deleted
reclaimPolicy: Delete

# Optional, if you want to add dynamic resize for PVC.
# For now only ext3, ext4, xfs resize support provided, like in Kubernetes itself.
allowVolumeExpansion: true

```

