# cka-exercices
cka basic exercices  

## Extra
Go to the master node
```shell
# Changing owner of .kube directory
sudo chown -R $USER:$USER kubectl .kube # Changing owner of kubectl
chmod +x kubectl    # set kubectl as an executable
sudo cp kubectl /usr/local/bin  # copy to the relevant path within the $PATH
alias k='kubectl'  # alias 
alias ll='ls -alrt'
source <( kubectl completion bash | sed s/kubectl/k/g )
k get nodes   # Check
```


You will be evaluated on 5 topics around Kubernetes administration:
1. Cluster Architecture, Installation & Configuration --  25%
2. Workloads & Scheduling -- 15%
3. Services & Networking -- 20%
4. Storage --  10%
5. Troubleshooting -- 30%  


## Exercice -1  
On the namespace exe1, create a Deployment named nginx that uses the image nginx:1.17.0. Set two
replicas to begin with.  
Find on which nodes all pods are.  
Check the cpu and memory values used by this deployment.   

## Solution Exercice -1
### Namespace and tools
```shell
k create ns exe1  # create a namespace
k get ns  # check 
# kubectx is a tool to switch between contexts (clusters) on kubectl faster.
# kubens is a tool to switch between Kubernetes namespaces (and configure them for kubectl) easily.
sudo git clone https://github.com/ahmetb/kubectx /opt/kubectx  # download tools
sudo ln -s /opt/kubectx/kubectx /usr/local/bin/kubectx  # kubectx  for context
sudo ln -s /opt/kubectx/kubens /usr/local/bin/kubens # kubens  for namespace
kubens exe1 # switch to exe1 namespace
```
### install metrics-server from helm 
```shell
helm repo add metrics-server https://kubernetes-sigs.github.io/metrics-server/
helm upgrade --install metrics-server metrics-server/metrics-server
# troubleshooting
#Usage of --kubelet-insecure-tls is expected in clusters that don't provide proper certificates to Kubelet. Such clusters should not be used in production, as so we don't support them with default Metrics Server manifests. 
k edit deploy metrics-server
```
### Deployment and results
```shell
k create deployment nginx --image=nginx:1.17.0 --replicas=2  # create a deployment
k get pod -o wide
k top pods 
```

Exercice -2
On the namespace exe2, create a Deployment named memcached that uses the image bitnami/memcached:1.27.1.2
Set to 3 replicas, check the log

## Exercice -3
Scale the Deployment to 7 replicas using the scale command. Ensure that the correct number of Pods exist.
