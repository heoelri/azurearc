

# connect-agent CrashLoopBackOff
```bash
kubectl get pods --all-namespaces
```

```console
NAMESPACE        NAME                                  READY   STATUS             RESTARTS   AGE
azure-arc        config-agent-6765684d4b-w9t6d         1/1     Running            1          4d23h
azure-arc        connect-agent-859cb496f5-hdl8x        0/1     CrashLoopBackOff   76         4d23h
azure-arc        controller-manager-85c7f5f757-pgqdg   2/2     Running            36         4d23h
```

Check the logs of the `connect-agent` pod:
```bash
kubectl logs -n azure-arc connect-agent-859cb496f5-hdl8x
```

```console
2019/12/22 14:44:12 Environment validation :success
2019/12/22 14:44:12 Kubernetes API server access validation :success
2019/12/22 14:44:16 Azure Subscription access token :error :http request failed. Authentication Token URL:https://login.microsoftonline.com/72f988bf-86f1-41af-91ab-2d7cd011db47/oauth2/token Authentication Token Body:grant_type=client_credentials&client_id=7109d274-b02d-4cd0-83e9-cc7fecc5056f&client_secret=7b5b2df6-63fd-4fef-9ae8-aefdffe27f69&resource=https%3A%2F%2Fmanagement.azure.com%2F ErrorInfo: Error:Post https://login.microsoftonline.com/72f988bf-86f1-41af-91ab-2d7cd011db47/oauth2/token: dial tcp: lookup login.microsoftonline.com on 10.96.0.10:53: server misbehaving
```