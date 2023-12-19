# First Pod with Alpine Linux
In this section we are creating a simple pod with Alpine Linux.
No persistent storage, no networking, just a simple pod with a single container.

## Resource Definition
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: alpine
  namespace: web-app-namespace
spec:
  containers:
    - name: alpine
      image: alpine
      command: [ "sleep", "1000000" ]
```

## Create Pod
```shell
kubectl apply -f alpine.yaml
```

Verify that the pod is running:
```shell
root@controller-01:~# kubectl get pods -n web-app-namespace
NAME     READY   STATUS    RESTARTS   AGE
alpine   1/1     Running   0          89s
```

## Run Shell in Pod
```shell
`kubectl exec -it -n web-app-namespace alpine -- /bin/sh
````
