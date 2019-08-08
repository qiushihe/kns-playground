# Create Local k3s Cluster with k3d

Download `k3d` CLI binary from: https://github.com/rancher/k3d/releases

## Create Cluster with 2 Workers

```
$ k3d create --workers 2
```

## Launch k3d Shell

The k3d shell have `KUBECONFIG` environment variable already set so there is no need to manually set them.

**For UNIX bash/etc.**

```
$ k3d shell --shell bash
```

**For Windows PowerShell**

```
> PowerShell -NoExit -Command {[Environment]::SetEnvironmentVariable("KUBECONFIG", (k3d get-kubeconfig), "Process")}
```

## Verify Cluster Status

```
$ kubectl cluster-info
```

... ensure the output indicates that the cluster is the one running locally.
