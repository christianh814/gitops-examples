Quick and dirty notes, will probably remove later


# Bluegreen

```shell
 kubectl argo rollouts list rollouts --all-namespaces
 kubectl argo rollouts get rollout gobg -n bluegreen
 kubectl argo rollouts -n bluegreen set image  gobg gobg=christianh814/gobg:green
 kubectl argo rollouts get rollout gobg -n bluegreen
 kubectl argo rollouts promote -n bluegreen gobg
 kubectl argo rollouts get rollout gobg -n bluegreen
```

# Canary

The "promote" command will "pause" (suspend) Argo CD syncing. The promote command is based on what I have defined in the [Rollout.yaml](canary/base/rollout.yaml#L32-L38) file.

```shell
kubectl argo rollouts get rollout rollouts-demo -n canary
kubectl argo rollouts promote rollouts-demo -n canary
```
