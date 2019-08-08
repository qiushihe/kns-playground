# Install Helm and Dashboard

## Install Helm

Get Helm binary from: https://github.com/helm/helm/releases

### Create Service Account for Tiller

```
$ kubectl create serviceaccount tiller --namespace kube-system
```

### Create Cluster Role Binding for Tiller

```
$ kubectl create clusterrolebinding tiller --clusterrole cluster-admin --serviceaccount=kube-system:tiller
```

### Initialize Helm

```
$ helm init --service-account tiller
```

... verify Helm installation with:

```
$ kubectl get pods --namespace kube-system
```

... and look for tiller-deploy-XXX deployment and ensure that is has no errors.

## Install Dashboard

For more info see: https://github.com/kubernetes/dashboard

### Install Dashboard via Helm

```
$ helm install stable/kubernetes-dashboard --name dashboard --set fullnameOverride="kubernetes-dashboard" --namespace kube-system
```

### Create Cluster-Admin Role Binding for Dashboard

```
$ kubectl create clusterrolebinding dashboard-admin --namespace kube-system --clusterrole=cluster-admin --serviceaccount=kube-system:kubernetes-dashboard
```

### Get Login Token for Dashboard

```
$ kubectl describe secret $(kubectl get serviceaccount kubernetes-dashboard -o jsonpath="{.secrets[0].name}" --namespace kube-system) --namespace kube-system
```

### Access Dashboard via Proxy

```
$ kubectl proxy
```

... and open dashboard proxy URL:

* http://localhost:8001/api/v1/namespaces/kube-system/services/https:kubernetes-dashboard:https/proxy

... and when prompted, choose "Token" login method and enter the token obtained from the previous step.
