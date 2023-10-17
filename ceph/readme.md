
Deploy with Rook based on [this guide](https://rook.io/docs/rook/v1.11/Getting-Started/quickstart/#deploy-the-rook-operator).

Config host storage devices based on [doc](https://rook.io/docs/rook/v1.11/CRDs/Cluster/host-cluster/).

ltdr; storage is configured in `cluster.yaml` under `storage` section.

Create Ceph cluster:

```bash
kubectl create -f crds.yaml -f common.yaml -f operator.yaml
kubectl create -f cluster.yaml
```

Create dashboard service running on node port 32000 (more details in [doc](https://rook.github.io/docs/rook/v1.11/Storage-Configuration/Monitoring/ceph-dashboard/#enable-the-ceph-dashboard)):

```
kubectl create -f dashboard-service.yaml
```

Username is `admin`, get password by using command:

```
kubectl -n rook-ceph get secret rook-ceph-dashboard-password -o jsonpath="{['data']['password']}" | base64 --decode && echo
```

Create shared file system for pods (follow [guide](https://rook.io/docs/rook/v1.11/Storage-Configuration/Shared-Filesystem-CephFS/filesystem-storage/#create-the-filesystem)):

```
kubectl create -f filesystem.yaml
kubectl create -f storageclass.yaml
```


Create PVC like this:

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: cephfs-pvc
  namespace: kube-system
spec:
  accessModes:
  - ReadWriteMany
  resources:
    requests:
      storage: 1Gi
  storageClassName: rook-cephfs
```

and mount it as volume like this:

```yaml
      volumes:
      - name: image-store
        persistentVolumeClaim:
          claimName: cephfs-pvc
          readOnly: false
```
