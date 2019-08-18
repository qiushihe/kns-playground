# Local k3s Cluster with k3d

Download `k3d` CLI binary from: https://github.com/rancher/k3d/releases

## Create Cluster with 2 Workers

```
$ k3d create --workers 2
```

## Create Cluster with 2 Workers and a Local Docker Volume

```
$ docker volume create --driver local local-volume
$ k3d create --workers 2 --volume local-volume:/opt/local-path-storage
```

## Launch k3d Shell

The k3d shell have `KUBECONFIG` environment variable already set so there is no need to manually set them.

**For UNIX bash/etc.**

```
$ k3d shell --shell bash
```

**For Windows PowerShell**

```
> PowerShell -NoExit -Command {function prompt {"($([regex]::Match($(k3d get-kubeconfig), '([^/]+)\/kubeconfig\.yaml$').captures.groups[1].value)) PS $($ExecutionContext.SessionState.Path.CurrentLocation)$('>' * ($nestedPromptLevel + 1)) "} [Environment]::SetEnvironmentVariable("KUBECONFIG", (k3d get-kubeconfig), "Process")}
```

... this command will create a new PowerShell sub-shell session with `KUBECONFIG` environment variable already set to the correct value, and with PowerShell prompt text prepended with the local cluster name to avoid mistake ... because taking PowerShell seriously enough is plainly impossible for anyone to pull off.

## Verify Cluster Status

```
$ kubectl cluster-info
```

... ensure the output indicates that the cluster is the one running locally.

## Clean Up

Delete cluster:

```
$ k3d delete
```

Delete local volume:

```
$ docker volume rm local-volume
```
