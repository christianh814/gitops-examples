Quick and dirty notes, will probably remove later

List rollouts

```shell
kubectl argo rollouts list rollouts --all-namespaces
```


# Bluegreen

If I change the image in the [kustomization.yaml file patch](bg/overlays/localhost/kustomization.yaml#L21), and then sync; It'll pause until I run the promote command.

```shell
kubectl argo rollouts get rollout gobg -n bluegreen
kubectl argo rollouts -n bluegreen set image  gobg gobg=christianh814/gobg:green
kubectl argo rollouts get rollout gobg -n bluegreen
kubectl argo rollouts promote -n bluegreen gobg
kubectl argo rollouts get rollout gobg -n bluegreen
```

# Canary

Initial Argo CD sync will "pause" (suspend) Argo CD syncing. The "promote" command is based on what I have defined in the [Rollout.yaml](canary/base/rollout.yaml#L32-L38) file and will go to the next stage (from 30, to 60, to 100). Final "promote" command will mark the App as healthy.

```shell
kubectl argo rollouts get rollout rollouts-demo -n canary
kubectl argo rollouts promote rollouts-demo -n canary
```

Outside of Argo CD, you could do

```shell
kubectl argo rollouts -n canary set image rollouts-demo rollouts-demo=argoproj/rollouts-demo:yellow
```
