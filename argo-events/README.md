# Argo Events Demo

This is a simple demo. You need a Kubernetes cluster, I use kind.

```shell
kind create cluster --image=kindest/node:v1.23.3
```

Install Argo Events

```shell
kubectl create namespace argo-events

kubectl apply \
    -f https://raw.githubusercontent.com/argoproj/argo-events/stable/manifests/install.yaml

kubectl -n argo-events apply \
    -f https://raw.githubusercontent.com/argoproj/argo-events/stable/examples/eventbus/native.yaml
```

# Event Source

Event source are enteties that publish some sort of event. This is a simple webhook. Different options can be found [here](https://github.com/argoproj/argo-events/blob/master/api/event-source.md#eventsourcespec)

```yaml
apiVersion: argoproj.io/v1alpha1
kind: EventSource
metadata:
  name: webhook
spec:
  service:
    ports:
      - port: 12000
        targetPort: 12000
  webhook:
    hello-world:
      port: "12000"
      endpoint: /hello-world
      method: POST
```

Create this event source:

```shell
kubectl apply -f manifests/event-source.yaml -n argo-events
```

List your event sources

```shell
kubectl get eventsources -n argo-events
```

List the event source objects pods/service

```shell
kubectl get pods -n argo-events
kubectl get svc -n argo-events
```

Port fowrad to test hook

```shell
kubectl port-forward svc/webhook-eventsource-svc -n argo-events 12000:12000
```

Test with curl

```shell
curl -X POST -H "Content-Type: application/json" \
-d '{"message":"My first webhook"}' \
http://127.0.0.1:12000/hello-world
```

# Sensors and Triggers

Sensors detects one or many events (so you can control dependencies)
and triggers a Trigger to do some sort of task.

In this inistance, we'll just create a pod. Other [trigger tempaltes](https://github.com/argoproj/argo-events/blob/master/api/sensor.md#argoproj.io/v1alpha1.TriggerTemplate)

```yaml
apiVersion: argoproj.io/v1alpha1
kind: Sensor
metadata:
  name: webhook
spec:
  template:
    serviceAccountName: argo-events-sa
  dependencies:
  - name: payload
    eventSourceName: webhook
    eventName: hello-world
  triggers:
  - template:
      name: payload
      k8s:
        group: ""
        version: v1
        resource: pods
        operation: create
        source:
          resource:
            apiVersion: v1
            kind: Pod
            metadata:
              generateName: payload-
              labels:
                app: payload
            spec:
              containers:
              - name: hello
                image: alpine
                command: ["echo"]
                args: ["Payload received: ", "", "\n"]
              restartPolicy: Never
        parameters:
          - src:
              dependencyName: payload
              dataKey: body.message
            dest: spec.containers.0.args.1
```

Here we can take the payload and use it as an argument!

Create the sensor with the trigger template

```shell
kubectl -n argo-events apply -f manifests/sensor.yaml
```

Now curl your event source

```shell
curl -X POST \
    -H "Content-Type: application/json" \
    -d '{"message":"This is my payload"}' \
    http://127.0.0.1:12000/hello-world
```

Check that it created a pod

```shell
kubectl -n argo-events get pods
```

Look at the logs for your message

```shell
kubectl -n argo-events logs -l app=payload
```

Clean up pods to try again

```shell
kubectl -n argo-events delete pods -l app=payload
```

# Cleanup

To clean up, just kill the cluster

```shell
kind delete clusters --all
```
