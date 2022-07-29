Quick and dirty notes, will probably remove later

```shell
 kubectl argo rollouts list rollouts --all-namespaces
 kubectl argo rollouts get rollout gobg -n bluegreen
 kubectl argo rollouts -n bluegreen set image  gobg gobg=christianh814/gobg:green
 kubectl argo rollouts get rollout gobg -n bluegreen
 kubectl argo rollouts promote -n bluegreen gobg
 kubectl argo rollouts get rollout gobg -n bluegreen
```
