```
Vagrant Up
Vagrant ssh
# install 
curl https://raw.githubusercontent.com/helm/helm/master/scripts/get-helm-3 | bash

kubectl create namespace monitoring
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo add stable https://charts.helm.sh/stable
helm repo update
helm install prometheus prometheus-community/kube-prometheus-stack --namespace monitoring --kubeconfig /etc/rancher/k3s/k3s.yaml
kubectl get pods,svc --namespace=monitoring

kubectl create namespace observability
# Please use the last stable version
export jaeger_version=v1.28.0 
# jaegertracing.io_jaegers_crd.yaml
kubectl create -n observability -f https://raw.githubusercontent.com/jaegertracing/jaeger-operator/${jaeger_version}/deploy/crds/jaegertracing.io_jaegers_crd.yaml
# service_account.yaml
kubectl create -n observability -f https://raw.githubusercontent.com/jaegertracing/jaeger-operator/${jaeger_version}/deploy/service_account.yaml
# role.yaml
kubectl create -n observability -f https://raw.githubusercontent.com/jaegertracing/jaeger-operator/${jaeger_version}/deploy/role.yaml
# role_binding.yaml
kubectl create -n observability -f https://raw.githubusercontent.com/jaegertracing/jaeger-operator/${jaeger_version}/deploy/role_binding.yaml
# operator.yaml
kubectl create -n observability -f https://raw.githubusercontent.com/jaegertracing/jaeger-operator/${jaeger_version}/deploy/operator.yaml

# cluster_role.yaml
kubectl create -f https://raw.githubusercontent.com/jaegertracing/jaeger-operator/${jaeger_version}/deploy/cluster_role.yaml
# cluster_role_binding.yaml
kubectl create -f https://raw.githubusercontent.com/jaegertracing/jaeger-operator/${jaeger_version}/deploy/cluster_role_binding.yaml

kubectl get deployments jaeger-operator -n observability
kubectl get pods,svc -n observability


# to can copy file from host to VM
scp -P 2222 -r /folder vagrant@127.0.0.1:/home/vagrant

kubectl apply -f backend.yaml
kubectl apply -f frontend.yaml
kubectl apply -f jeager-instance.yaml



kubectl -n observability  port-forward  service/bkk-traces-query --address 0.0.0.0 16686:16686
for i in 1 2 3 4 5 6 7 8 9; do curl localhost:8081; done


kubectl --namespace monitoring port-forward svc/prometheus-grafana --address 0.0.0.0 3000:80
Forwarding from 0.0.0.0:3000 -> 3000

bkk-traces-query.observability.svc.cluster.local:16686
```