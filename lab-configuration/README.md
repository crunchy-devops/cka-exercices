# lab-configuration 

Kubernetes clusters have to types of users service accounts and normal users, but normal users are assumed
to be managed by an outside service. There are no objects to represent them and they cannot be added via an API
call, but service accounts can be added.
We will use RBAC to configure access to actions within a namespace for a new contractor, Developer Dan who will be
working on a new project.

Create two namespaces, one for production and the other for development.  
```kubectl create ns development```

```kubectl create ns production```

View the current clusters and context available. The context allows you to configure the cluster to use, namespace and user for kubectl commands in an easy and consistent manner.  
```kubectl config get-contexts```


Make a copy of your access configuration file. Later steps will update this file and we can view the differences.   
```cp ~/.kube/config ~/cluster-api-config```


Create a new user DevDan and assign a password of 12345678.  
```sudo useradd -s /bin/bash DevDan```

Generate a private key then Certificate Signing Request (CSR) for DevDan.  
```openssl genrsa -out DevDan.key 2048```

Note: Try commenting RANDFILE line in file /etc/ssl/openssl.cnf

```shell
openssl req -new -key DevDan.key \
-out DevDan.csr -subj "/CN=DevDan/O=development"
```

Using thew newly created request generate a self-signed certificate using the x509 protocol. Use the CA keys for the Kubernetes cluster and set a 45-day expiration. You’ll need to use sudo to access to the inbound files.

```shell
sudo openssl x509 -req -in DevDan.csr -CA /etc/kubernetes/pki/ca.crt -CAkey /etc/kubernetes/pki/ca.key -CAcreateserial -out DevDan.crt -days 45
```

Update the access config file to reference the new key and certificate. Normally we would move them to a safe directory instead of a non-root user’s home.  
```k config set-credentials DevDan --client-certificate=DevDan.crt --client-key=DevDan.key``` 

View the update to your credentials file. Use diff to compare against the copy we made earlier.  
```diff cluster-api-config .kube/config```


We will now create a context. For this we will need the name of the cluster, namespace and CN , common name, of the user we set or saw in previous steps.
```shell
k config set-context DevDan-context  --cluster=kubernetes --namespace=development  --user=DevDan
```

Attempt to view the Pods inside the DevDan-context. Be aware you will get an error.  
```kubectl --context=DevDan-context get pods```

Verify the context has been properly set.  
```kubectl config get-contexts```

Again check the recent changes to the cluster access config file  
```diff cluster-api-config .kube/config```

```vim role-dev.yaml```


Create the object. Check white space and for typos if you encounter errors.  
```kubectl create -f role-dev.yaml```


Now we create a RoleBinding to associate the Role we just created with a user. Create the object when the file has
been created.  
```vim rolebind.yaml```

```kubectl apply -f rolebind.yaml```

Test the context again. This time it should work. There are no Pods running so you should get a response of No
resources found.  
```kubectl --context=DevDan-context get pods```

Create a new pod, verify it exists, then delete it.
```shell
kubectl --context=DevDan-context create deployment nginx --image=nginx
kubectl --context=DevDan-context get pods
kubectl --context=DevDan-context delete deploy nginx
```

We will now use a different context for production systems. The Role will only have the ability to view, but not create  or delete resources. Begin by copying and editing the Role and RoleBindings YAML files  
```vim role-prod.yaml```
and 
```vim rolebindprod.yaml```

Create both new objects.  
```shell
kubectl apply -f role-prod.yaml
kubectl apply -f rolebindprod.yaml
```

Create the new context for production use.  
```shell
kubectl config set-context ProdDan-context \
--cluster=kubernetes \
--namespace=production \
--user=DevDan
```

Verify that user DevDan can view pods using the new context.  
```kubectl --context=ProdDan-context get pods```

Try to create a Pod in production. The developer should be Forbidden.  
```shell
kubectl --context=ProdDan-context create \
deployment nginx --image=nginx
```
View the details of a role.  
```kubectl -n production describe role dev-prod```