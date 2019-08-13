# Install Prometheus Operator and Loki

For more info, see: https://www.digitalocean.com/community/tutorials/how-to-set-up-digitalocean-kubernetes-cluster-monitoring-with-helm-and-prometheus-operator

## Prepare Custom Values

Edit `./monitoring/custom-values.yaml` and update configurations as necessary.

## Install Prometheus Operator with Helm

```
$ helm install --namespace monitoring --name prometheus-operator -f ./custom-values/prometheus-operator.yml stable/prometheus-operator
```

... this command may take a while to run and it will install, among other things:

* [Prometheus](https://prometheus.io)
* [Grafana](https://grafana.com)

Verify pods are running:

```
$ kubectl --namespace monitoring get pods -l "release=prometheus-operator"
```

### Access Services with Port Forwarding

**Grafana**

```
$ kubectl port-forward -n monitoring svc/prometheus-operator-grafana 8080:80
```

Open URL: http://localhost:8080

**Prometheus**

```
$ kubectl port-forward -n monitoring svc/prometheus-operator-prometheus 9090:9090
```

Open URL: http://localhost:9090

## Install Loki and Promtail with Helm

For more info, see: https://github.com/grafana/loki

Add Loki chart repo to Helm:

```
$ helm repo add loki https://grafana.github.io/loki/charts
```

... and update Helm charts:

```
$ helm repo update
```

### Install Loki

```
$ helm install --namespace monitoring --name loki loki/loki
```

TODO: Install Loki with persistence:

```
$ helm install --namespace monitoring --name loki loki/loki --set "loki.persistence.enabled=true,loki.persistence.storageClassName=XXX"
```

### Install Promtail

This will install Promtail as a DaemonSet which will automatically gather logs from all containers in the cluster and send them to Loki.

```
$ helm install --namespace monitoring --name promtail loki/promtail --set "loki.serviceName=loki"
```

### Add Loki Data Source to Grafana

1. Log into Grafana and on the left side bar select "Configuration" -> "Data Sources".
2. On the Data Sources screen, click on "Add data source" and select "Loki" as data source type.
3. Under "HTTP" -> "URL" enter `http://loki:3100` and click "Save & Test".
