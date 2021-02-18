# Preserve tcp original source ip within cluster

## deploy application

1. deploy sleep service

```bash
kubectl apply -f https://raw.githubusercontent.com/istio/istio/master/samples/sleep/sleep.yaml
```

1. deploy httpbin service as tcp service


```bash
kubectl apply -f httpbin-svc.yaml
```

Note: service port name is `tcp`, which indicates `httpbin` is now a tcp service from istio's point.

## enable access through ingress gateway

   ```bash
   kubectl apply -f https://raw.githubusercontent.com/istio/istio/master/samples/httpbin/httpbin-gateway.yaml
   ```