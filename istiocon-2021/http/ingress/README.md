# Ingress HTTP original src ip preserve

This example shows how to preserve original src address for http traffic

## deploy application

1. deploy sleep service

```bash
kubectl apply -f https://raw.githubusercontent.com/istio/istio/master/samples/sleep/sleep.yaml
```

1. deploy httpbin service


```bash
kubectl apply -f https://github.com/istio/istio/raw/master/samples/httpbin/httpbin.yaml
```

1. enable access through ingress gateway

```bash
kubectl apply -f https://raw.githubusercontent.com/istio/istio/master/samples/httpbin/httpbin-gateway.yaml
```

## access httpbin from external


```bash
$ curl 172.19.0.4:32095/ip
{
  "origin": "10.244.0.1"
}

$ curl 172.19.0.4:32095/headers?show_env -iv
{
  "headers": {
    "Accept": "*/*",
    "Content-Length": "0",
    "Host": "172.19.0.4:32095",
    "User-Agent": "curl/7.47.0",
    "X-B3-Parentspanid": "22137f194380f1d7",
    "X-B3-Sampled": "0",
    "X-B3-Spanid": "1026938203253a8e",
    "X-B3-Traceid": "397b475126480fa922137f194380f1d7",
    "X-Envoy-Attempt-Count": "1",
    "X-Envoy-Internal": "true",
    "X-Forwarded-Client-Cert": "By=spiffe://cluster.local/ns/default/sa/httpbin;Hash=7f6e4fd1cc8b6d7424d30aad70f8ba2e19fc84eb26602585afbc5995e3b1478c;Subject=\"\";URI=spiffe://cluster.local/ns/istio-system/sa/istio-ingressgateway-service-account",
    "X-Forwarded-For": "10.244.0.1",
    "X-Forwarded-Proto": "http",
    "X-Request-Id": "69ccd143-5542-41f2-9700-6fb8ed0e52a4"
  }
}
```

## set ingress gateway service's externalTrafficPolicy to Local

If the k8s ingress controller like AWS NLB supports proxy protocol, you should enable proxy protocol envoy filter for istio ingress gateway.
Refer to https://istio.io/latest/blog/2020/show-source-ip/#step-2-create-proxy-protocol-envoy-filter



## access httpbin from external and check original ip

```bash
$ curl  172.19.0.4:32095/ip
  {
    "origin": "172.19.0.1" # this is the docker bridge ip
  }

$ curl  172.19.0.4:32095/headers?show_env=1
  {
    "headers": {
      "Accept": "*/*",
      "Content-Length": "0",
      "Host": "172.19.0.4:32095",
      "User-Agent": "curl/7.47.0",
      "X-B3-Parentspanid": "4dcc0441f7bc57b3",
      "X-B3-Sampled": "0",
      "X-B3-Spanid": "52e36ec727a341ff",
      "X-B3-Traceid": "86c146c0781ea9764dcc0441f7bc57b3",
      "X-Envoy-Attempt-Count": "1",
      "X-Envoy-Internal": "true",
      "X-Forwarded-Client-Cert": "By=spiffe://cluster.local/ns/default/sa/httpbin;Hash=7f6e4fd1cc8b6d7424d30aad70f8ba2e19fc84eb26602585afbc5995e3b1478c;Subject=\"\";URI=spiffe://cluster.local/ns/istio-system/sa/istio-ingressgateway-service-account",
      "X-Forwarded-For": "172.19.0.1",
      "X-Forwarded-Proto": "http",
      "X-Request-Id": "9a141b79-d79c-4f5c-b66e-c007da5b8667"
    }
  }
```








