Instructions taken from here https://www.ibm.com/docs/en/cloud-private/3.2.0?topic=console-namespace-is-stuck-in-terminating-state

```bash
 kubectl get namespace <terminating-namespace> -o json >tmp.json
```

Edit tmp.json and remove kubernetes from finalizers field and save. It should look something like this.

```console
{
      "apiVersion": "v1",
      "kind": "Namespace",
      "metadata": {
          "creationTimestamp": "2018-11-19T18:48:30Z",
          "deletionTimestamp": "2018-11-19T18:59:36Z",
          "name": "<terminating-namespace>",
          "resourceVersion": "1385077",
          "selfLink": "/api/v1/namespaces/<terminating-namespace>",
          "uid": "b50c9ea4-ec2b-11e8-a0be-fa163eeb47a5"
      },
      "spec": {
         "finalizers": []
      },
      "status": {
          "phase": "Terminating"
      }
  }
```

Run the following two commands

```bash
kubectl proxy
```

```bash
 curl -k -H "Content-Type: application/json" -X PUT --data-binary @tmp.json http://127.0.0.1:8001/api/v1/namespaces/<terminating-namespace>/finalize
```

The namespace should now be deleted
