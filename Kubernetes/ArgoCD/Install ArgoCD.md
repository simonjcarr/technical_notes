These instructions are taken from https://argo-cd.readthedocs.io/en/stable/getting_started/

```bash
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```
### Ingress
Next an ingress route needs to be setup. See  https://argo-cd.readthedocs.io/en/stable/operator-manual/ingress/ for all the options

#### Cloudflared Ingress
Although ArgoCD exposes both port 80 and 443 on it's argocd-server service, I could not get port 80 work at all. When I used port 443, I got TLS Errors in Cloudflared. You can ignore these TLS errors with a noTLSVerify in the cloudflared configMap.  See [[Install cloudflared in a cluster]] for details of how to implement on an ingress route.