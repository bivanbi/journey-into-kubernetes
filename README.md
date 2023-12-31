# Journey Into Kubernetes

First year into learning Kubernetes - from Minikube through manual operation to automation.

## Foreword

Kubernetes is a great platform for managing complex containerized applications and the nodes they run on.
The desired state of the cluster - networking, storage, security, monitoring, logging, and many other aspects -
are described through so-called objects, in a declarative way. Kubernetes and all the different 'plugins' then take care
of converging the cluster to this desired state.

It promotes good patterns and practices for building highly available, scalable and distributed applications.
While it might seem overly complicated at first, the concept is really not that hard to grasp.
And with all the available plugins, many tedious tasks become much simpler.

## Important Notes

### Mind the Terminology

There are certain terms that are used in Kubernetes that have a very specific meaning. For
example, `Workload`, `Service` and `Deployment` are exact object kinds, not just generic terms. If in doubt, check
the [Terminology](terminology.md).

### Always verify the state of the cluster

It can (and will) happen, that we describe a state that is not possible to achieve. We might not get an error message
when applying the manifest. To find out if there is an error and what the error is, query the state
and check logs of the affected components.

## Components used

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

## 3. Run Simple Workloads Manually

In this section, we are creating `Pods`, `Services`, `PersistentVolumeClaims` manually, to realize our
first 'application' on Kubernetes. Especially pods are not meant to be managed this way, but it is crucial
to have a good understanding of the building blocks that will be later managed by higher-level objects like
`Deployments`, `StatefulSets` etc.

3.0 [Create Namespace](namespaces/create-namespace.md) - create a namespace to keep things organized.

3.1 [Single Container Pod](pods/single-container-pod.md) - no network, no persistent storage, just a simple
container.

3.2 [Pod with Service](pods/single-container-pod-with-service.md) - expose a pod to the outside world on a specific
port.

## 4. Simple Deployment

In the previous section, we manually created pods.
This is not how it is meant to be. Pods are sort of 'one-shot' objects, they are meant to be managed
by higher-level objects like `Deployments`, `StatefulSets` etc., and come with some limitations:

- pods are not automatically re-created if they are killed.
- only some properties of pods can be updated by applying updated Pod manifest, for everything else they need to be
  deleted and re-created.

This is where Deployments come into the picture. Deployments can create, re-create / update and even roll back
the pods. They can also manage ReplicaSets to maintain a certain number of replicas of a pod.

4.1 [Simple Deployment](deployments/simple-deployment.md) - create a deployment with a single replica.

## Tools

- [k9s](https://k9scli.io/) - Terminal UI to interact with your Kubernetes clusters. Absolute must-have.

## Terminology

See [Terminology](terminology.md) for a list of Kubernetes terms and concepts.

# TODO

## 3. pods manually

- multicontainer pod with initcontainer, emptyDir volume and multiple ports

## 4. persistent storage
