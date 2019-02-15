### Deploy cockroach db with ssd persistent disk on Kubernetes.
You can prefer cockroach db website steps to deploy cluster on kubernetes. but you just need to change in your cockroach file cockroachdb-statefulset-secure.yaml for ssd storage. and also you will have to create storage class for that.


### Define storage class.

```
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: pd-ssd
provisioner: kubernetes.io/gce-pd
parameters:
  type: pd-ssd
```

```
kubectl apply -f storage.yaml
```

### Now you have to modified the file "cockroachdb-statefulset-secure.yaml"
at the end of file add below line befor applying cockroach statefulset file to kubernetes.

```
      volumes:
      - name: datadir
        persistentVolumeClaim:
          claimName: datadir
      - name: certs
        emptyDir: {}
  podManagementPolicy: Parallel
  updateStrategy:
    type: RollingUpdate
  volumeClaimTemplates:
  - metadata:
      name: datadir
    spec:
      accessModes:
        - "ReadWriteOnce"
      storageClassName: pd-ssd
      resources:
        requests:
          storage: 50Gi
```

```
kubectl apply -f cockroachdb-statefulset-secure.yaml
```

Now you can see that ssd persistent storage disk mount to your cockroach cluster pods.
