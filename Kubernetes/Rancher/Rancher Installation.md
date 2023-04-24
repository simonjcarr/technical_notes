These instructions are taken from https://ranchermanager.docs.rancher.com/v2.5/pages-for-subheaders/install-upgrade-on-a-kubernetes-cluster

Make sure you have a kubernetes cluster up and running

Ensure helm is installed [[Install Helm]]

### Add the Helm Chart Repository

```bash
helm repo add rancher-stable https://releases.rancher.com/server-charts/stable
```

### Create a namespace in the cluster

```bash
kubectl create namespace cattle-system
```

### Install Cert Manager
Choose the SSL Configuration. These instructions will simply use a rancher generated certificate, but other options documented on the website include
* Lets's Encrypt
* Certificates from Files

#### Install CRD's
```bash
kubectl apply -f https://github.com/jetstack/cert-manager/releases/download/v1.5.1/cert-manager.crds.yaml

```

#### Add the Jetstack Helm Repository

```bash
helm repo add jetstack https://charts.jetstack.io
```

Update the local helm chart repo cache

```bash
helm repo update
```

Install the cert-manager Helm Chart
```bash
helm install cert-manager jetstack/cert-manager --namespace cert-manager --create-namespace --version v1.5.1
```

Once you’ve installed cert-manager, you can verify it is deployed correctly by checking the cert-manager namespace for running pods:

```bash
kubectl get pods --namespace cert-manager
```

### Install Rancher
```
helm install rancher rancher-stable/rancher --namespace cattle-system --set hostname=rancher.my.org --set replicas=1
```

Wait for Rancher to be rolled out
```bash
kubectl -n cattle-system rollout status deploy/rancher
```

### Get the Initial Admin Password
```bash
kubectl get secret -n cattle-system bootstrap-secret -o jsonpath='{.data.bootstrapPassword}' | base64 --decode
```


### Ingress
The rancher install above will create a default ingress, but if you want to cloudflared following these instructions [[Install cloudflared in a cluster]]
