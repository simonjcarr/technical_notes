Clone the Kubernetes repository https://github.com/simonjcarr/kubernetes

### Create a Namespace
```bash
kubectl create ns postgres-operator
```


### Apply the manifests

```bash
# apply the following manifests in this order
kubectl create -f manifests/configmap.yaml -n postgres-operator  # configuration
kubectl create -f manifests/operator-service-account-rbac.yaml -n postgres-operator   # identity and permissions
kubectl create -f manifests/postgres-operator.yaml -n postgres-operator   # deployment
kubectl create -f manifests/api-service.yaml -n postgres-operator   # operator API to be used by UI
```
### Check that the Operator is running

```bash
kubectl get pod -l name=postgres-operatorcd -n postgres-operator
```

### Create a Postgres cluster
Edit the minimal-postgres-manifest.yaml file to ensure the settings match your requirements
```bash
kubectl create -f manifests/minimal-postgres-manifest.yaml -n yournamespace
```

Delete a Postgres cluster

```bash
kubectl delete postgresql acid-minimal-cluster -n yournamespace
```

