# Single Container Pod with Service

One step further: Expose a pod to the outside world on a specific port.
Still no persistent storage.

## Manifest

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
        - containerPort: 80
          name: http
---
apiVersion: v1
kind: Service
metadata:
  name: static-web-service
  namespace: web-app-namespace
spec:
  type: NodePort
  selector:
    app.kubernetes.io/name: static-web-service
  ports:
    - name: http-web-srv
      port: 80
      protocol: TCP
      targetPort: http
      nodePort: 30080
```

Apply it with `kubectl apply -f static-web-service.yaml`

## Verify

From any host that can reach the worker nodes:

```shell
curl <worker node hostname or ip>:30080
```

## Notes

We used `NodePort` service type here, as it makes the service available on all worker nodes, with the node's own IP
address. It is the simplest way to expose a service to the outside world through, for example, a manually managed
reverse proxy.

Other possible values are `ClusterIP` (default) and `LoadBalancer` and `ExternalName`.
See [Service](https://kubernetes.io/docs/concepts/services-networking/service/) for details.

Notice that even though our Pod runs on a single node, the service is available on all nodes. This is the
default behavior of `NodePort` services. The `.spec.internalTrafficPolicy` and
`.spec.externalTrafficPolicy` fields to control how Kubernetes routes traffic to healthy (“ready”) backends.
See [Traffic Policies](https://kubernetes.io/docs/reference/networking/virtual-ips/#traffic-policies) for details.

## Troubleshooting

### Connection refused - Service has no endpoints

```shell
Checking the iptables rules on the worker node itself reveals an error message within the comment:
```shell
root@worker-02:~# iptables-save  | grep 30080
-A KUBE-EXTERNAL-SERVICES -p tcp -m comment --comment "web-app-namespace/static-web-service has no endpoints" -m addrtype --dst-type LOCAL -m tcp --dport 30080 -j REJECT --reject-with icmp-port-unreachable
```

Possible reasons:

- The container is missing a label that the service `selector`
  references (`app.kubernetes.io/name: static-web-service`)
