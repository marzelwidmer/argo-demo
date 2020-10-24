# argo-demo
Argo CD - Demo Project


# Create App
```
argocd app create kboot --repo https://github.com/marzelwidmer/argo-demo.git --path manifest --dest-server https://kubernetes.default.svc --dest-namespace default
```
