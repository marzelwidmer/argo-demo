# argo-demo
Argo CD - Demo Project

## Install Argo CLI
```
brew install argocd
brew install sops
```
## Sealed Secrets
Install Sealed Secrets with Helm to manage Secrets. 
see: https://hub.kubeapps.com/charts/stable/sealed-secrets
```
helm install sealed --namespace kube-system stable/sealed-secrets
```
### Install client-side tool into /usr/local/bin/
```
GOOS=$(go env GOOS)
GOARCH=$(go env GOARCH)
wget https://github.com/bitnami-labs/sealed-secrets/releases/download/v0.12.4/kubeseal-$GOOS-$GOARCH
sudo install -m 755 kubeseal-$GOOS-$GOARCH /usr/local/bin/kubeseal
```
### Create a sealed secret file for Redis
```
kubectl create secret generic redis --dry-run=client --from-literal=database-password=verySecurePassword -o yaml | \
 kubeseal \
 --controller-name=sealed-sealed-secrets \
 --controller-namespace=kube-system \
 --format yaml > sealed-redis-secret.yaml
```
### Apply the sealed secret
```
kubectl create -f sealed-redis-secret.yaml
```
### Get Secret
Running `kubectl get secret redis -o yaml` will show the decrypted secret that was generated from the sealed secret.
Both the SealedSecret and generated Secret must have the same name and namespace.
```
kubectl get secret redis -o yaml
```





## Install ARGO
```
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```


## Port Forwarding to ARGO Server
```
kubectl port-forward svc/argocd-server -n argocd 8080:443
```


## Change Argo Admin Password
The default Password is the Argo Server name.
```
kubectl get pod -n argocd | grep argocd-server
argocd-server-5bc896856-xvt92                    1/1     Running   0          71m
```
Use the Argo CLI to login with user `admin` and change the password.
```
argocd login localhost:8080
argocd account update-password
```

# Create App with directory-recurse
```
argocd app create kboot --repo https://github.com/marzelwidmer/argo-demo.git --path manifest  --directory-recurse --dest-server https://kubernetes.default.svc --dest-namespace default
```
## Test Contracts inside K8s
```
kubectl run -i --rm --restart=Never curl-client --image=tutum/curl:alpine --command -- curl -s 'http://contracts/api/persons' -H 'Content-Type: application/json' -w "\n"

[{"firstName":"John"}]
pod "curl-client" deleted
```



#### Create Redis only
```
argocd app create redis --repo https://github.com/marzelwidmer/argo-demo.git --path manifest/redis --dest-server https://kubernetes.default.svc --dest-namespace default
```
#### Create Contracts only
```
argocd app create contracts --repo https://github.com/marzelwidmer/argo-demo.git --path manifest/contracts --dest-server https://kubernetes.default.svc --dest-namespace default
```

 


See:
https://github.com/bitnami-labs/sealed-secrets
