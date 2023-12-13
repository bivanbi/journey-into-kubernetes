# Install Calico Container Network Interface with Tigera Operator
Official documentation: [Calico for Kubernetes](https://docs.tigera.io/calico/latest/getting-started/kubernetes/self-managed-onprem/onpremises)

## 1. Install Tigera Operator
Tigera Operator is used to install and manage Calico.

```shell
kubectl create -f https://docs.projectcalico.org/manifests/tigera-operator.yaml
```
## 2. Download Calico Custom Resource Definitions
```shell
wget https://raw.githubusercontent.com/projectcalico/calico/v3.26.4/manifests/custom-resources.yaml
```

## 3 Edit Custom Resource Definitions
The single parameter that absolutely need to be set: `spec.calicoNetwork.ipPools`. It needs fall within
the `--pod-network-cidr` used when creating cluster.

Pay attention to `natOutgoing` and `encapsulation`.
An example with outgoing nat and full VXLAN encapsulation:
```yaml
apiVersion: operator.tigera.io/v1
kind: Installation
metadata:
  name: default
spec:
  # Configures Calico networking.
  calicoNetwork:
    # Note: The ipPools section cannot be modified post-install.
    ipPools:
    - blockSize: 26
      cidr: 10.100.0.0/16
      encapsulation: VXLAN
      natOutgoing: Enabled
      nodeSelector: all

---

# This section configures the Calico API server.
# For more information, see: https://projectcalico.docs.tigera.io/master/reference/installation/api#operator.tigera.io/v1.APIServer
apiVersion: operator.tigera.io/v1
kind: APIServer
metadata:
  name: default
spec: {}
```

## 4. Install Calico
```shell
kubectl create -f custom-resources.yaml
```

## 5. Verify Installation
Query pods in `calico-system` and `calico-apiserver` namespaces. All pods should be in `Running` state.

```shell
root@controller-01:~# kubectl get pods -n calico-system 
NAME                                      READY   STATUS    RESTARTS      AGE
calico-kube-controllers-87879d9d7-5crtp   1/1     Running   0             10d
calico-node-dhdhj                         1/1     Running   1 (10d ago)   10d
calico-node-wzgk5                         1/1     Running   0             10d
calico-typha-556cd6874b-8z8wv             1/1     Running   0             10d
csi-node-driver-4kl77                     2/2     Running   0             10d
csi-node-driver-nhwkf                     2/2     Running   0             10d
root@controller-01:~# kubectl get pods -n calico-system -n calico-apiserver 
NAME                                READY   STATUS    RESTARTS   AGE
calico-apiserver-68b66f75c4-7v8t8   1/1     Running   0          10d
calico-apiserver-68b66f75c4-qr9vt   1/1     Running   0          10d
```

# Troubleshooting
## Calico Installation Issue - Network plugin returns error: cni plugin not initialized

* Ubuntu version: 22.04
* Containerd version: 1.7.2
* Kubernetes version: 1.28.4
* Calico version: 3.26.4

To reproduce:

1. Install Kubernetes with kubeadm
2. Install Calico with Tigera Operator
3. After installing Calico,
   * master node remained in `Ready: False` state
     ```
     root@controller-01:~# kubectl describe node controller-01 | grep -A 10 Cond
     Conditions:
       Type                 Status  LastHeartbeatTime                 LastTransitionTime                Reason                       Message
       ----                 ------  -----------------                 ------------------                ------                       -------
       NetworkUnavailable   False   Sat, 02 Dec 2023 17:17:34 +0000   Sat, 02 Dec 2023 17:17:34 +0000   CalicoIsUp                   Calico is running on this node
       MemoryPressure       False   Sun, 03 Dec 2023 06:46:59 +0000   Sat, 02 Dec 2023 14:27:10 +0000   KubeletHasSufficientMemory   kubelet has sufficient memory available
       DiskPressure         False   Sun, 03 Dec 2023 06:46:59 +0000   Sat, 02 Dec 2023 14:27:10 +0000   KubeletHasNoDiskPressure     kubelet has no disk pressure
       PIDPressure          False   Sun, 03 Dec 2023 06:46:59 +0000   Sat, 02 Dec 2023 14:27:10 +0000   KubeletHasSufficientPID      kubelet has sufficient PID available
       Ready                False   Sun, 03 Dec 2023 06:46:59 +0000   Sat, 02 Dec 2023 14:27:10 +0000   KubeletNotReady              container runtime network not ready: NetworkReady=false reason:NetworkPluginNotReady message:Network plugin returns error: cni plugin not initialized
     ```
   * Some Calico pods remain in `ContainerCreating`

### Solution
After hours of investigations without luck, a simple **containerd restart** worked around the issue.
It could be reproduced again, but still do not know the root cause. It could be observed on
worker nodes as well that later joined the cluster.

