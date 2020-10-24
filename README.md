# argo-demo
Argo CD - Demo Project



## Install Argo CLI
```
brew install argocd
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

# Create App
```
argocd app create kboot --repo https://github.com/marzelwidmer/argo-demo.git --path manifest --dest-server https://kubernetes.default.svc --dest-namespace default
```
