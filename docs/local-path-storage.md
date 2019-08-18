# Local Path Storage

For more info, see: https://github.com/rancher/local-path-provisioner

## Create k3s Cluster with Local Volume

Create a local docker volume:

```
$ docker volume create --driver local local-volume
```

... and ensure the `k3d` cluster creation command is run with the `--volume` option:

```
$ k3d create --workers 2 --volume local-volume:/opt/local-path-storage
```

## Create Local Path Storage Provisioner

```
$ kubectl create -f ./local-path-storage/provisioner.yml
```

## Create and Verify Example PVC and Pod

Create a example PVC:

```
$ kubectl create -f ./local-path-storage/example/pvc.yml
```

... since we're using local-path-storage provisioner, the persistent volume inside Kubernetes will not be created until it's mounted (and the persistent volume claim would also remain in "pending" status).

Create a example pod:

```
$ kubectl create -f ./local-path-storage/example/pod.yml
```

... and write something to the Local Path Storage PVC backed volume:

```
$ kubectl exec volume-test -- sh -c "echo hello-world > /data/test-file"
```

The data can now be verified by:

```
$ docker run --rm -i -v=local-volume:/tmp/storage-volume busybox ls -al /tmp/storage-volume

$ docker run --rm -i -v=local-volume:/tmp/storage-volume busybox cat /tmp/storage-volume/pvc-xxx-xxx-xxx/test-file
```
