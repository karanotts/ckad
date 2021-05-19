# Commands and Arguments

If the image has defined [ENTRYPOINT] and [CMD] it can be redefinet in kubernetes with ```command``` and ```args```

```
FROM alpine
ENTRYPOINT ["ping"]
CMD ["google.com"]
```

``` 
docker tag pinger karanotts/pinger

docker push karanotts/pinger 
```


Run as default with entrypoint ["ping"] and cmd ["google.com"]
```
docker run karanotts/pinger
PING google.com (74.125.193.138): 56 data bytes
64 bytes from 74.125.193.138: seq=0 ttl=52 time=1.288 ms
64 bytes from 74.125.193.138: seq=1 ttl=52 time=1.282 ms
64 bytes from 74.125.193.138: seq=2 ttl=52 time=1.291 ms
```

Override default cmd with custom ```bing.com```
```
docker run karanotts/pinger bing.com
PING bing.com (204.79.197.200): 56 data bytes
64 bytes from 204.79.197.200: seq=0 ttl=112 time=0.795 ms
64 bytes from 204.79.197.200: seq=1 ttl=112 time=1.199 ms
64 bytes from 204.79.197.200: seq=2 ttl=112 time=1.495 ms
```

Override default entrypoint and command with ```echo "Hello!"```

``` docker run --entrypoint COMMAND <image-name> ARGUMENT```
```
docker run --entrypoint echo karanotts/pinger "Hello!"
Hello!
```

Run in kubernetes with default ```command``` [ENTRYPOINT] and ```args``` [CMD]
```
kubectl run pinger --image=karanotts/pinger 
pod/pinger created
kubectl logs pinger
PING google.com (74.125.193.101): 56 data bytes
64 bytes from 74.125.193.101: seq=0 ttl=52 time=1.295 ms
64 bytes from 74.125.193.101: seq=1 ttl=52 time=1.358 ms
64 bytes from 74.125.193.101: seq=2 ttl=52 time=1.307 ms
```

Run in kubernetes, overriding default args with ```bing.com```
```
kubectl run binger --image=karanotts/pinger -- bing.com
pod/binger created
kubectl logs binger
PING bing.com (204.79.197.200): 56 data bytes
64 bytes from 204.79.197.200: seq=0 ttl=112 time=1.756 ms
64 bytes from 204.79.197.200: seq=1 ttl=112 time=1.466 ms
64 bytes from 204.79.197.200: seq=2 ttl=112 time=1.800 ms
```

Override the entire ```command```
```
apiVersion: v1
kind: Pod
metadata:
  name: custom
spec:
  containers:
  - command: ["/bin/sh"]
    args:
    - -c
    - echo Hello!
    image: karanotts/pinger
    name: custom
```
```
kubectl create -f custom.yaml 
pod/custom created

kubectl logs custom
Hello!
```