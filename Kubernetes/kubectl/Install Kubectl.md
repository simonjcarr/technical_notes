Taken from https://kubernetes.io/docs/tasks/tools/install-kubectl-linux/

```bash
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
```


```bash
curl -LO "https://dl.k8s.io/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl.sha256"
```

```bash
echo "$(cat kubectl.sha256)  kubectl" | sha256sum --check
```

```bash
sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl
```