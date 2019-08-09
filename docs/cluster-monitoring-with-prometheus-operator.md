# Cluster Monitoring with Prometheus Operator

For more info, see: https://www.digitalocean.com/community/tutorials/how-to-set-up-digitalocean-kubernetes-cluster-monitoring-with-helm-and-prometheus-operator

## Prepare Custom Values

Edit `./monitoring/custom-values.yaml` and update configurations as necessary.

## Install Prometheus Operator with Helm

```
$ helm install --namespace monitoring --name cluster-monitoring -f ./monitoring/custom-values.yaml stable/prometheus-operator
```

This will install, among other things:

* Prometheus
* Grafana
* Alertmanager

Verify pods are running:

```
$ kubectl --namespace monitoring get pods -l "release=cluster-monitoring"
```

### Access Services with Port Forwarding

**Grafana**

```
$ kubectl port-forward -n monitoring svc/cluster-monitoring-grafana 8000:80
```

Open URL: http://localhost:8000

... and login with:

* Username: `admin`
* Password: `[password configured in ./monitoring/custom-values.yaml]`

**Prometheus**

```
$ kubectl port-forward -n monitoring svc/cluster-monitoring-pr-prometheus 9090:9090
```

Open URL: http://localhost:9090

**AlertManager**

```
$ kubectl port-forward -n monitoring svc/cluster-monitoring-pr-alertmanager 9093:9093
```

Open URL: http://localhost:9093
