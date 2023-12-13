# Create Cluster with kubeadm
## Important notes
"Turning a single control plane cluster created without --control-plane-endpoint into a highly available cluster is not supported by kubeadm."

## Prerequisites
### Linux Settings
```shell
modprobe br_netfilter
sysctl net.ipv4.ip_forward=1
sysctl net.bridge.bridge-nf-call-iptables=1
sysctl net.ipv6.conf.all.forwarding=1
sysctl net.bridge.bridge-nf-call-ip6tables=1  
```
*To persist module loading through reboot, add `br_netfilter` to `/etc/modules-load.d/br_netfilter.conf`*

### Choose IP address ranges
#### IPv4
Choose an IP address range that is not used anywhere else in the given network. It is recommended to use a /16 range;
as each pod is assigned a subnet from this network (/24 by default, which is an overkill for simple use cases).

Default values:
* service network default value: 10.96.0.0/12
* pod network does not have a default value

#### IPv6
There are some constraints:
* service subnet mask must be >= 108
* pod subnet mask and node-mask difference must not be greater than 16 (e.g.: pod: /48, node: /64)


### Chose a hostname for the control plane endpoint
It is recommended to set up a hostname for the Kubernetes cluster control plane endpoint, even if there will be a single
control plane node. This allows for flexibility, 
* should the cluster be expanded to multiple control plane nodes or the
* control plane node be moved to a different host.

A `/etc/hosts` file entry should be fine for simple clusters.

## Install Packages
```shell
apt install containerd kubelet kubeadm kubectl
```

## Configure Containerd
1. Generate default config file
   ```shell
   sudo mkdir -p /etc/containerd
   sudo containerd config default | sudo tee /etc/containerd/config.toml
   ```
2. Configure containerd to use systemd cgroup driver
   ```shell
   sudo sed -i 's/SystemdCgroup = .*/SystemdCgroup = true/' /etc/containerd/config.toml
   ```

## Create Cluster
### 1. Initialize Control Plane on Master Node
#### 1.1 Initialize control plane
 ```shell
 kubeadm init \
   --control-plane-endpoint=<control plane hostname> \
   --service-dns-domain=<service dns domain> \
   --pod-network-cidr=<pod subnet(s)> \
   --service-cidr=<service subnet(s)>
 ```

*If multiple IP address ranges are used (e.g. one IPv4 and one IPv6), specify them both as a comma separated list.*

**kubeadm will print exact commands to join further control plane and worker nodes to the cluster.
Make sure to save these commands for later use.**

#### 1.2. Make root user aware of the kubernetes config
```shell
export KUBECONFIG=/etc/kubernetes/admin.conf
```
*To persist this setting through reboot, add the above line to `/root/.bash_profile`*
#### 1.3. Create custom kubeconfig for non-root users as required*, see [Kubeconfig Additional Users](https://kubernetes.io/docs/tasks/administer-cluster/kubeadm/kubeadm-certs/#kubeconfig-additional-users)

*Do not distribute the `admin.conf` to users, it would be possible security issue.*

### 2. Install Container Network Interface
It is mandatory to install a CNI to provide networking for the cluster. Calico is a popular choice.

See [Install Calico Container Network Interface with Tigera Operator](../calico-network-interface/install-calico.md)
for details.

### 3. Join Further Control Plane or Worker Nodes
The Kubernetes cluster is functional with just a single (master + worker in one) node, but it provides no
redundancy, and for security reasons it is recommended to keep master separate from workers.

#### 3.1. Join Control Plane Node
```shell
kubeadm join <control plane hostname>:6443 \
  --token <token> \
  --discovery-token-ca-cert-hash <hash> \
  --control-plane
`
```

#### 3.2. Join Worker Node
```shell
kubeadm join <control plane hostname>:6443 \
  --token <token> \
  --discovery-token-ca-cert-hash <hash>
```

# Troubleshooting
## Control plane connection refused
Symptom
```shell
root@controller-01:~# kubectl get namespaces 
The connection to the server cluster-endpoint:6443 was refused - did you specify the right host or port?
```
Probable Causes
* Right after `kubeadm init` it takes some time to download the service images.
* Containerd got restarted - might try to restart kubelet