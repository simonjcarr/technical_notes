### To see the properties of a secret

```bash
kubectl describe secret -n yournamespace secretname
```

Example response

```console
Name:         postgres.acid-minimal-cluster.credentials.postgresql.acid.zalan.do
Namespace:    keycloak
Labels:       application=spilo
              cluster-name=acid-minimal-cluster
              team=acid
Annotations:  <none>

Type:  Opaque

Data
====
password:  64 bytes
username:  8 bytes
```

### Decode a secret
```bash
kubectl get secret -n yournamespace secretname -o jsonpath='{.data.password}' | base64 --decode
```

Example output

```console
DxGsJNFOhM3o7J8lupB6bBZh7PlroLkX35e30uQCwqgprsM8wTTsjMvahLro4Zjj
```
