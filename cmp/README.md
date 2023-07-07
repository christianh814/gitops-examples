# CMP Example

Install the manifests in the order listed

```shell
kubectl apply -f manifests/1.argocd-cm-update.yaml
kubectl -n argocd patch deployments/argocd-repo-server --patch-file manifests/2.argocd-repo-server-patch.yaml
kubectl apply -f manifests/3.argocd-sample.app.yaml
```

Verify 

```shell
kubectl get cm -n default
```
