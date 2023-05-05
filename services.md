1-Create a pod with image nginx called nginx and expose its port 80

```K run nginx --image=nginx --port=80 --expose```

2- Confirm that ClusterIP has been created. Also check endpoints
```shell
k get svc nginx 
k get ep 
```
3- Get service's ClusterIP, create a temp busybox pod and 'hit' that IP with wget
```shell
kubectl get svc nginx
kubectl run busybox --rm --image=busybox -it -- sh 
wget -O- IP:80
```
4- Convert the ClusterIP to NodePort for the same service and find the NodePort port. Hit service using Node's IP. Delete the service and the pod at the end.
```shell
k edit svc nginx
```