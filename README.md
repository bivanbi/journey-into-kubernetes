# Journey Into Kubernetes

First year into learning Kubernetes - from Minikube through manual operation to automation.

### Components used:

* Ubuntu 22.04
* containerd: v1.7.2 from default Ubuntu repository (Other container runtimes are also available,
  see [Container Runtimes](https://kubernetes.io/docs/setup/production-environment/container-runtimes/))
* Kubernetes: v1.28.4 from pkgs.k8s.io repository
    * Container Network Interface: [Calico](https://docs.tigera.io/calico/latest/getting-started/kubernetes/quickstart)

## 1. Minikube

Create a single-node Kubernetes cluster with just a few simple commands. Excellent for learning and local development.

* [Minikube Commands](minikube/minikube-commands.md)

Official Tutorial: [Hello Minikube](https://kubernetes.io/docs/tutorials/hello-minikube/)

## 2. Create Cluster with kubeadm

2.1. [Create Cluster with kubeadm](kubeadm/create-cluster-with-kubeadm.md)

2.2. [Install Calico Container Network Interface](calico-network-interface/install-calico.md)

Official documentation:
[Creating a cluster with kubeadm](https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/create-cluster-kubeadm/)

## Tools

- [k9s](https://k9scli.io/) - Terminal UI to interact with your Kubernetes clusters. Absolute musthave.

## Terminology

See [Terminology](terminology.md) for a list of Kubernetes terms and concepts.
