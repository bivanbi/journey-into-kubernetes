# Kubernetes Terminology

### Basics

* [Workload](https://kubernetes.io/docs/concepts/workloads/) - A workload is an application running on Kubernetes;
  running inside a set of [Pods](https://kubernetes.io/docs/concepts/workloads/pods/).
* [Object](https://kubernetes.io/docs/concepts/overview/working-with-objects/kubernetes-objects/) - Objects describe
  the (anticipated) state of the cluster.
* [Manifest](https://kubernetes.io/docs/concepts/overview/working-with-objects/kubernetes-objects/#object-spec-and-status)
  - A manifest is a YAML or JSON file that describes a Kubernetes object.
* [Pod](https://kubernetes.io/docs/concepts/workloads/pods/) - The smallest deployable unit of computing that can be
  created and managed in Kubernetes. A Pod is similar to a set of containers with shared namespaces and shared
  filesystem volumes.
* [ReplicaSet](https://kubernetes.io/docs/concepts/workloads/controllers/replicaset/) - A ReplicaSet ensures that a
  specified number of pod replicas are running at any given time.
* [Deployment](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/) -
  Provides a declarative update to a set of [Pods](https://kubernetes.io/docs/concepts/workloads/pods/)
  pr [ReplicaSet](https://kubernetes.io/docs/concepts/workloads/controllers/replicaset/).
* [Service](https://kubernetes.io/docs/concepts/services-networking/service/) - A method for exposing a
  network application that is running as one or more [Pods](https://kubernetes.io/docs/concepts/workloads/pods/)
  in your cluster.
* [PersistentVolume](https://kubernetes.io/docs/concepts/storage/persistent-volumes/) - A cluster-wide storage
  volume that is automatically mounted as a volume in a [Pod](https://kubernetes.io/docs/concepts/workloads/pods/).
* [PersistentVolumeClaim](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#persistentvolumeclaims) -
  A request for a persistent volume that can be dynamically provisioned by a storage class.

### Advanced

* [Operator](https://kubernetes.io/docs/concepts/extend-kubernetes/operator/) - Application that
  *operates* on the Kubernetes cluster itself; adding automation and/or features to Kubernetes, much like
  plugins any ordinary application.

* [CustomResourceDefinition](https://kubernetes.io/docs/concepts/extend-kubernetes/api-extension/custom-resources/) -
  A *custom resource definition* (CRD) is an extension of the Kubernetes API that
  is not necessarily available in a default Kubernetes installation. It represents a customization of a particular
  Kubernetes installation. It can be provided by an `Operator`, for example.
