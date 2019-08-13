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
$ helm install --namespace kube-system --name dashboard -f ./custom-values/dashboard.yml stable/kubernetes-dashboard
```

### Access Dashboard with Port Forwarding

```
$ kubectl port-forward -n kube-system svc/kubernetes-dashboard 8888:80
```

Open URL: http://localhost:8888

... and click on "Skip" to skip login.

### Access Dashboard via Proxy

```
$ kubectl proxy
```

Open URL: http://localhost:8001/api/v1/namespaces/kube-system/services/http:kubernetes-dashboard:http/proxy


### Obtain Dashboard Login Token

If the login token is ever needed, it can be obtained via this command:

```
$ kubectl describe secret $(kubectl get serviceaccount kubernetes-dashboard -o jsonpath="{.secrets[0].name}" --namespace kube-system) --namespace kube-system
```
