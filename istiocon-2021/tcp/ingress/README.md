# Preserve tcp original source ip within cluster

## deploy application

1. deploy sleep service

```bash
kubectl apply -f https://raw.githubusercontent.com/istio/istio/master/samples/sleep/sleep.yaml
```

1. deploy httpbin service as tcp service


```bash
kubectl apply -f httpbin.yaml
```

note: service port name is `tcp`, which indicates `httpbin` is now a tcp service from istio's point.

## access httpbin from sleep and check the ip

```bash
$ k exec -ti sleep-557747455f-b62dt -c sleep sh -- curl httpbin:8000/headers?show_env=1
  {
    "headers": {
      "Accept": "*/*",
      "Host": "httpbin:8000",
      "User-Agent": "curl/7.74.0-DEV"
    }
  }

$ k exec -ti sleep-557747455f-b62dt -c sleep sh -- curl httpbin:8000/ip
{
  "origin": "127.0.0.1"
}
```

## set interceptionMode to TPROXY



