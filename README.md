# istio-otel-tracing

Rough notes for now...

# BookInfo
```
istioctl install -f ./bookinfo/demo-profile-no-gateways.yaml -y
kubectl create ns bookinfo
kubectl label namespace bookinfo istio-injection=enabled

kubectl get crd gateways.gateway.networking.k8s.io &> /dev/null || \
{ kubectl kustomize "github.com/kubernetes-sigs/gateway-api/config/crd?ref=v1.2.1" | kubectl apply -f -; }
kubectl apply -f ./bookinfo/bookinfo.yaml -n bookinfo
kubectl exec "$(kubectl get pod -n bookinfo -l app=ratings -o jsonpath='{.items[0].metadata.name}')" -n bookinfo -c ratings -- curl -sS productpage:9080/productpage | grep -o "<title>.*</title>"
kubectl annotate gateway bookinfo-gateway networking.istio.io/service-type=ClusterIP --namespace=bookinfo
kubectl port-forward svc/bookinfo-gateway-istio 8080:80 -n bookinfo
```

# Tracing
```
kubectl create namespace observability
kubectl create secret generic newrelic-license-key --from-literal=license-key=<NR LICENSE KEY> -n observability
kubectl apply ./otel/otel-collector.yaml -n observability
istioctl install -y -f ./istio/istio-otel-tracing.yaml
kubectl apply -f ./istio/istio-telemetry-api.yaml -n istio-system
```