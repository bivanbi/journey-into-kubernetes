# Simple Deployment

Re-do the [Single Container Pod with Service](../pods/single-container-pod-with-service.md) example, but this time
manage the pod with a Deployment. 

Notice that it does not affect the Service object.

## 1. Try to Update the Pod Without Deployment
Let's change the port number, for instance.

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: static-web-service
  namespace: web-app-namespace
  labels:
    app.kubernetes.io/name: static-web-service
spec:
  containers:
    - name: static-web-service
      image: nginx
      ports:
        - containerPort: 81
          name: http
```

Notice the error message when trying to apply it directly:
```shell
root@controller-01:~# kubectl apply -f single-container-pod-with-service.yml 
The Pod "static-web-service" is invalid: spec: Forbidden: pod updates may not change fields other than `spec.containers[*].image`,`spec.initContainers[*].image`,`spec.activeDeadlineSeconds`,`spec.tolerations` (only additions to existing tolerations),`spec.terminationGracePeriodSeconds` (allow it to be set to 1 if it was previously negative)
```

We could, of course, delete the pod and re-create it like this:
```shell
root@controller-01:~# kubectl delete -f single-container-pod-with-service.yml 
pod "static-web-service" deleted
root@controller-01:~# kubectl apply -f single-container-pod-with-service.yml 
pod/static-web-service created
```

But that is a bit tedious, and we would have to do it every time we want to update the pod. Deployment to the rescue!

## Manifest
Let's do it with a Deployment. Notice that the Service object is not affected.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: static-web-service
  namespace: web-app-namespace
  labels:
    app.kubernetes.io/name: static-web-service
spec:
  replicas: 1
  selector:
    matchLabels:
      app.kubernetes.io/name: static-web-service
  template:
    metadata:
      name: static-web-service
      namespace: web-app-namespace
      labels:
        app.kubernetes.io/name: static-web-service
    spec:
      containers:
        - name: static-web-service
          image: nginx
          ports:
            - containerPort: 80
              name: http
```

Notice that the `template` key holds the Pod manifest that we used before, without the `apiVersion` and `kind` keys.

## Apply

```shell
root@controller-01:~# kubectl apply -f single-container-pod-deployment.yml 
deployment.apps/static-web-service created
```
Verify the status:
```shell
root@controller-01:~# kubectl rollout status deployment/static-web-service -n web-app-namespace
deployment "static-web-service" successfully rolled out
root@controller-01:~# kubectl get deployments -n web-app-namespace -o wide
NAME                 READY   UP-TO-DATE   AVAILABLE   AGE   CONTAINERS           IMAGES   SELECTOR
static-web-service   1/1     1            1           14m   static-web-service   nginx    app.kubernetes.io/name=static-web-service
```

## Operate the Deployment
### Delete and Re-create the Pod
Notice that there are two pods now. The one without the unique ID is the one we created directly:
```shell
root@controller-01:~# kubectl get pods -n web-app-namespace 
NAME                                  READY   STATUS    RESTARTS   AGE
static-web-service                    1/1     Running   0          10m
static-web-service-5c88646989-lbzk4   1/1     Running   0          97s
```
Let's delete the one without the unique ID:
```shell
root@controller-01:~# kubectl delete pod static-web-service -n web-app-namespace
pod "static-web-service" deleted
```

Now there should be only one pod:
```shell
root@controller-01:~# kubectl get pods -n web-app-namespace
NAME                                  READY   STATUS    RESTARTS   AGE
static-web-service-5c88646989-lbzk4   1/1     Running   0          2m
```

Let's try to delete the pod created by the deployment:
```shell
root@controller-01:~# kubectl delete pod static-web-service-5c88646989-lbzk4 -n web-app-namespace
pod "static-web-service-5c88646989-lbzk4" deleted
```
The pod should be recreated automatically:
```shell
root@controller-01:~# kubectl get pods -n web-app-namespace
NAME                                  READY   STATUS    RESTARTS   AGE
static-web-service-5c88646989-ch8tt   1/1     Running   0          4s
```
We can see that it is a new pod as the unique ID is different this time.

### Scale the Deployment
Let's scale the deployment to 3 replicas:
```shell
root@controller-01:~# kubectl scale deployment static-web-service --replicas=3 -n web-app-namespace
deployment.apps/static-web-service scaled
root@controller-01:~# kubectl get deployments -n web-app-namespace -o wide
NAME                 READY   UP-TO-DATE   AVAILABLE   AGE   CONTAINERS           IMAGES   SELECTOR
static-web-service   3/3     3            3           16m   static-web-service   nginx    app.kubernetes.io/name=static-web-service
```

### 'Vacate' All Pods by Scaling to 0
Some operations require all pods to be stopped. This can be achieved by scaling to 0:
```shell
root@controller-01:~# kubectl scale deployment static-web-service --replicas=0 -n web-app-namespace
deployment.apps/static-web-service scaled
root@controller-01:~# kubectl get deployments -n web-app-namespace -o wide
NAME                 READY   UP-TO-DATE   AVAILABLE   AGE   CONTAINERS           IMAGES   SELECTOR
static-web-service   0/0     0            0           18m   static-web-service   nginx    app.kubernetes.io/name=static-web-service
root@controller-01:~# kubectl get pods -n web-app-namespace
No resources found in web-app-namespace namespace.
```
(Scale back to at least 1 replica for the rest of this document)

### Update Pod in the Deployment
Let's change the port number, through `kubectl edit`:
```yaml
kubectl edit deployment/static-web-service -n web-app-namespace
```
Hint: you can choose your editor by using the `EDITOR` environment variable:
```shell
EDITOR=nano kubectl edit deployment/static-web-service -n web-app-namespace
```

Save and observe that the pods are recreated.
