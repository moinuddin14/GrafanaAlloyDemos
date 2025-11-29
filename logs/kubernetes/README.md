# Reference URLs

- Loki Helm Repository: https://grafana.github.io/helm-charts/
- Loki values.yaml: https://github.com/grafana/loki/blob/main/production/helm/loki/values.yaml
- Prometheus Helm Repository: https://prometheus-community.github.io/helm-charts
- Prometheus values.yaml: https://github.com/prometheus-community/helm-charts/blob/main/charts/kube-prometheus-stack/values.yaml

# Prerequisites installation

Add the Grafana Repository `helm repo add grafana https://grafana.github.io/helm-charts` and search for grafana repo `helm search repo grafana`

Installing `kind` cluster on Mac OS

```shell
# For Intel Macs
[ $(uname -m) = x86_64 ] && curl -Lo ./kind https://kind.sigs.k8s.io/dl/v0.30.0/kind-darwin-amd64
# For M1 / ARM Macs
[ $(uname -m) = arm64 ] && curl -Lo ./kind https://kind.sigs.k8s.io/dl/v0.30.0/kind-darwin-arm64
chmod +x ./kind
mv ./kind /usr/local/bin/kind
```

Create a Kind Cluster with custom name 

`kind create cluster --name kind-alloy`

Fetch all the kubernetes kind clusters

`kind get clusters`

In order to interact with a specific cluster, you only need to specify the cluster name as a context in kubectl

`kubectl cluster-info --context kind-kind-alloy`

Loading an Image Into Your Cluster

`kind load docker-image my-app:latest my-db:latest my-cache:latest --name kind-kind-alloy`

Create Namespace `kubectl create namespace monitoring`

# Installing Loki

Installing loki if custom-values.yaml file isn't present

Some parameters may be missing; best to refer to the video tutorial for complete setup instructions).

```shell
helm install loki grafana/loki --version 6.29.0 --namespace monitoring \
  --set deploymentMode=SingleBinary \
  --set loki.auth_enabled=false \
  --set singleBinary.replicas=1 \
  --set write.replicas=0 \
  --set read.replicas=0 \
  --set backend.replicas=0 \
  --set loki.commonConfig.replication_factor=1 \
  --set loki.storage.type=filesystem \
  --set loki.storage.filesystem.directory=/var/loki/chunks \
  --set loki.useTestSchema=true \
  --set chunksCache.enabled=false \
  --set resultsCache.enabled=false \
  --set test.enabled=false
```
# Testing Loki

port-forward

`kubectl port-forward --namespace monitoring svc/loki-gateway 3100:80`

# Send test logs

`curl -H "Content-Type: application/json" -H "X-Scope-OrgID: fake" -XPOST -s "http://localhost:3100/loki/api/v1/push" \
  --data-raw '{"streams": [{"stream": {"job": "test"}, "values": [["'$(date +%s)'000000000", "test log entry"]]}]}'
query for the same logs`

`curl -H "X-Scope-OrgID: fake" "http://localhost:3100/loki/api/v1/query_range" --data-urlencode 'query={job="test"}'`

# Installing kube-prometheus-stack

Installing kube-prometheus-stack if custom-values.yaml file isn't present. This adds loki as an additional data source.

Some parameters may be missing; best to refer to the video tutorial for complete setup instructions).

```shell
helm install prometheus prometheus-community/kube-prometheus-stack --version 45.7.1 \
  --namespace monitoring \
  --set "grafana.additionalDataSources[0].name=Loki" \
  --set "grafana.additionalDataSources[0].type=loki" \
  --set "grafana.additionalDataSources[0].url=http://loki-gateway.monitoring.svc.cluster.local" \
  --set "grafana.additionalDataSources[0].access=proxy"
```

# Testing Prometheus

port-forward

`kubectl port-forward -n monitoring svc/prometheus-operated 9090:9090`

# Testing Grafana

Access grafana password

`kubectl get secret -n monitoring prometheus-grafana -o jsonpath="{.data.admin-password}" | base64 --decode`

# port-forward

`kubectl port-forward -n monitoring svc/grafana 3000:80`