
### Install Kind
```bash
curl -Lo ./kind https://kind.sigs.k8s.io/dl/v0.18.0/kind-linux-amd64
chmod +x ./kind
sudo mv ./kind /usr/local/bin/kind
```

### Create a simple cluster
```bash
kind create cluster --name my-cluster
```

### Create a cluster with a specific version of kubernetes
```bash
kind create cluster --name my-cluster --image kindest/node:v1.24.12
```

### Full instructions
https://kind.sigs.k8s.io/
