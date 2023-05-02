# lab metrics-server
## installation from source 
```shell script
  kubectl create -f components.yaml 
```

## install from helm 
```shell
helm repo add metrics-server https://kubernetes-sigs.github.io/metrics-server/
helm upgrade --install metrics-server metrics-server/metrics-server
# troubleshooting
#Usage of --kubelet-insecure-tls is expected in clusters that don't provide proper certificates to Kubelet. Such clusters should not be used in production, as so we don't support them with default Metrics Server manifests. 
k edit deploy metrics-server
```

After au while we got some values  
```shell script
    k top nodes 
    k top pods 
``` 