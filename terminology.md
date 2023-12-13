# Kubernetes Terminology

### Basics

* [Resource](https://kubernetes.io/docs/concepts/overview/working-with-objects/kubernetes-objects/) - A
  *resource* is an endpoint in the Kubernetes API that stores a collection of API objects of a
  certain kind; for example, the built-in *pods* resource contains a collection of Pod objects.
  Resources describe the (anticipated) state of the cluster.
* [Workload](https://kubernetes.io/docs/concepts/workloads/) - A workload is an application running on Kubernetes;
  running inside a a set of [Pods](https://kubernetes.io/docs/concepts/workloads/pods/).

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

### Advanced

* [Operator](https://kubernetes.io/docs/concepts/extend-kubernetes/operator/) - Application that
  *operates* on the Kubernetes cluster itself; adding automation and/or features to Kubernetes, much like
  plugins any ordinary application.

* [Custom Resource](https://kubernetes.io/docs/concepts/extend-kubernetes/api-extension/custom-resources/) - A
  *custom resource* is an extension of the Kubernetes API that is not necessarily available in a default Kubernetes
  installation. It represents a customization of a particular Kubernetes installation. It can be provided
  by an operator, for example.
