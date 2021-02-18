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

## access httpbin from sleep and check the ip

```bash
$ k exec -ti sleep-557747455f-tt97m -c sleep sh -- curl httpbin:8000/headers?show_env=1
  {
    "headers": {
      "Accept": "*/*",
      "Host": "httpbin:8000",
      "User-Agent": "curl/7.74.0-DEV"
    }
  }

$ k exec -ti sleep-557747455f-tt97m -c sleep sh -- curl httpbin:8000/ip
{
  "origin": "127.0.0.1"
}
```

## set httpbin's interceptionMode to TPROXY

```bash
kubectl apply -f httpbin-deploy.yaml
```

Note: should also enable route local net  `echo 1 > /proc/sys/net/ipv4/conf/eth0/route_localnet` if necessary.

```bash
$ k exec -ti sleep-557747455f-tt97m -c sleep sh -- curl httpbin:8000/headers?show_env=1
{
  "headers": {
    "Accept": "*/*",
    "Host": "httpbin:8000",
    "User-Agent": "curl/7.74.0-DEV"
  }
}

$ k exec -ti sleep-557747455f-tt97m -c sleep sh -- curl httpbin:8000/ip
{
  "origin": "10.244.0.17"
}
```