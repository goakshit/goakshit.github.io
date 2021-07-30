---
title: "Kubernetes: Core Concepts Primer"
date: 2021-07-29T10:16:56+05:30
slug: "kubernetes core concepts primer"
tags: ["k8s", "kubernetes", "core", "primer"]
draft: true
---

#### What is Kubernetes?
> Kubernetes, also known as K8s, is an open-source system for automating deployment, scaling, and management of containerized applications.

Componenets of Kubernetes:
- **API Server**: Acts as a interface for users, cli, and other services to interact with the k8s system
- **ETCD**: Key-Value store that stores the state, metadata etc.
- **Scheduler**: A component that distributes work to nodes in the cluster.
- **Controller**: Responsible for orchestration of the cluster. Takes care when nodes go down...
- **Kubelet**: A component that runs on each node in the cluster. Acts as an agent for kubernetes.
- **Container Runtime**: Underlying technology that runs the containers like docker.

#### What is a Pod?
> A pod is a smallest unit of execution in Kubernetes. A pod is a collection of containers that are scheduled onto a node.

A pod can have a multiple containers but recommeded to have only one container.

> Cluster -> Node(n) -> Pod(n) -> Container(1 or n)

The above line represents:
- A cluster can have *n* nodes.
- A pod can have *n* containers. Normally, a pod should have 1 container.

#### Kubernetes YAML

Kubernetes yaml is a file that describes the desired state of the pod, service, replicaset or deployment.

It follows the following format:
```yaml
apiVersion: <VERSION> #Version of the specification
kind: <KIND> #Kind of the object like Deployment, Service, Pod or ReplicaSet.
metadata: <METADATA> #It can have a subfields like name, namespace, labels, annotations etc., whatever is supported by k8s.
spec: <SPECIFICATION>
```  

###### Example: Pod definition yaml with redis image:
To create the following pod, run `kubectl create -f <YAML>`
```yaml
apiVersion: v1
kind: Pod
metadata:
    name: redis-pod
    labels:
        app: cache
spec:
    containers:
        - name: redis
          image: redis
```