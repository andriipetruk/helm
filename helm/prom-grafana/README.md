wget https://raw.githubusercontent.com/kubernetes/charts/master/stable/grafana/values.yaml

helm dependency update

Proxy to local: 
export POD_NAME=$(kubectl get pods --namespace default -l "app=prometheus,component=prom-server" -o jsonpath="{.items[0].metadata.name}")
kubectl --namespace default port-forward $POD_NAME 9090