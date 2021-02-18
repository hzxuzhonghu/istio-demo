# Inner cluster HTTP original src ip preserve

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

## access httpbin from sleep and check the ip

```bash
k exec -ti sleep-557747455f-tt97m -c sleep sh -- curl httpbin:8000/ip
{
  "origin": "127.0.0.1"
}

kubectl exec -ti sleep-557747455f-tt97m -c sleep -- curl httpbin:8000/headers?show_env=1
{
  "headers": {
    "Accept": "*/*",
    "Content-Length": "0",
    "Host": "httpbin:8000",
    "User-Agent": "curl/7.74.0-DEV",
    "X-B3-Parentspanid": "0edb2a182b8bff2c",
    "X-B3-Sampled": "0",
    "X-B3-Spanid": "cd779fd86d319930",
    "X-B3-Traceid": "a62568b46a5afbc40edb2a182b8bff2c",
    "X-Envoy-Attempt-Count": "1",
    "X-Forwarded-Client-Cert": "By=spiffe://cluster.local/ns/default/sa/httpbin;Hash=a961a0ee79e13d3df02d31c40f4c9235b1e20eeea3150a60846c7d5780796202;Subject=\"\";URI=spiffe://cluster.local/ns/default/sa/sleep",
    "X-Forwarded-Proto": "http",
    "X-Request-Id": "4999d03a-0210-4401-8fe8-7638d4905502"
  }
}
```

## enable X-Forwarded-For header

enable sleep service's outbound http connection manager `use_remote_address` and disable `skip_xff_append`

```bash
kubectl apply -f yaml
```

## access httpbin from sleep and check the ip

```bash
k exec -ti sleep-557747455f-tt97m -c sleep sh -- curl httpbin:8000/ip
{
  "origin": "10.244.0.14"
}

k exec -ti sleep-557747455f-tt97m -c sleep sh -- curl httpbin:8000/headers?show_env=1
{
  "headers": {
    "Accept": "*/*",
    "Content-Length": "0",
    "Host": "httpbin:8000",
    "User-Agent": "curl/7.74.0-DEV",
    "X-B3-Parentspanid": "61ba7da3f787b798",
    "X-B3-Sampled": "0",
    "X-B3-Spanid": "fbb377827cb73fa2",
    "X-B3-Traceid": "1e2bb5fb800f434a61ba7da3f787b798",
    "X-Envoy-Attempt-Count": "1",
    "X-Envoy-Internal": "true",
    "X-Forwarded-Client-Cert": "By=spiffe://cluster.local/ns/default/sa/httpbin;Hash=a961a0ee79e13d3df02d31c40f4c9235b1e20eeea3150a60846c7d5780796202;Subject=\"\";URI=spiffe://cluster.local/ns/default/sa/sleep",
    "X-Forwarded-For": "10.244.0.14",
    "X-Forwarded-Proto": "http",
    "X-Request-Id": "4d8f9ef3-d018-424f-af92-061c3d3e673f"
  }
}
```



