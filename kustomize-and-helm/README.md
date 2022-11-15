# Kustomize and Helm

In order to do this you must add `--enable-helm` to the `kustomzie` command in Argo CD. You do this by modifying the Argo CD Config Map and adding it to the `kustomizeBuildOptions` configuration.

* [Argo CD](https://argo-cd.readthedocs.io/en/stable/user-guide/kustomize/#kustomizing-helm-charts)
* [Argo CD Operator (i.e. OpenShift GitOps)](https://argocd-operator.readthedocs.io/en/latest/reference/argocd/#kustomize-build-options-example)

If you're install Argo CD using a Helm Chart, use this in your `values.yaml` file:

```yaml
configs:
  cm:
    kustomize.buildOptions: "--enable-helm"
```

# Using this repo

Added the [knh-argocdapp.yaml](knh-argocdapp.yaml) file as an example/for convenience.
