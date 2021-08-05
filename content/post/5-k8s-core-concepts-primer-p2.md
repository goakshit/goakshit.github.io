---
title: "Kubernetes: Core Concepts Primer - Part 2"
slug: "k8s-primer-p2"
tags: ["kubernetes", "k8s-primer"]
date: 2021-08-05T18:14:56+05:30
draft: false
---

# Kubernetes: Core Concepts Primer - Part 2

## What are ReplicaSets?
> A ReplicaSet's purpose is to maintain a stable set of replica Pods running at any given time.

In short, a ReplicaSet is a monitoring and management tool for Pods that ensures a desired number of Pods are running at any given time based on the selector criteria specified in the ReplicaSet. 

Example:
```yaml
# Create a ReplicaSet
apiVersion: apps/v1   # Api Version that made replicaset available
kind: ReplicaSet      # Kind of resource
metadata:
    name: redis-rs
    labels:
        type: redis-backend
spec:
    replicas: 3 # Desired number of replicas
    selector:
        matchLabels:
            type: redis-backend     # Label selector used for filtering pods
    template:
        metadata:
            name: redis-pod
            labels:
                type: redis-backend # Label used by replicaset for identifying pods
        spec:
            containers:
                - name: redis
                  image: redis
```

Commands associated with ReplicaSet:
- Create ReplicaSet `kubectl create -f <RS_DEFINITION_FILE>`
- Delete ReplicaSet `kubectl delete replicaset <RS_NAME>`
- List ReplicaSet `kubectl get <RS_NAME>`
- Update ReplicaSet:
    - By updating the replicas in the definition file, `kubectl replace -f <RS_DEFINITION_FILE>`
    - By updating the replicas in the running ReplicaSet, `kubectl scale replicaset <RS_NAME> --replicas=<NUM_REPLICAS>`. **Note: This won't update the ReplicaSet definition file.**


## What are Deployments?
> A Kubernetes deployment is a resource object in Kubernetes that provides declarative updates to applications. A deployment allows you to describe an application’s life cycle, such as which images to use for the app, the number of pods there should be, and the way in which they should be updated. 

Example:
```yaml
# Create a deployment
apiVersion: apps/v1   # Api Version that made deployment available
kind: Deployment      # Kind of resource
metadata:
    name: redis-deployment
    labels:
        type: redis-backend
spec:
    replicas: 3 # Desired number of replicas
    selector:
        matchLabels:
            type: redis-backend     # Label selector used for filtering pods
    template:
        metadata:
            name: redis-pod
            labels:
                type: redis-backend # Label used by replicaset for identifying pods
        spec:
            containers:
                - name: redis
                  image: redis
```

Imperative command: `kubectl create deployment <DEPLOYMENT_NAME> --image=<IMAGE_NAME> --replicas=<REPLICA_COUNT>`


## What are Namespaces?
> Namespaces are a way to organize clusters into virtual sub-clusters — they can be helpful when different teams or projects share a Kubernetes cluster.

By default, a Kubernetes cluster has a single namespace called `default`. Unless explicitely specified, all resources created in the cluster will be in the `default` namespace. 

To create a k8s object in a different namespace, you need to specify the namespace as argument. Eg: `kubectl create -f <DEFINITION_FILE> --namespace=<NAMESPACE>`

To create a namespace: 
- Run `kubectl create namespace <NAMESPACE>`
- Run `kubectl create -f <NAMESPACE_DEFINITION_FILE>`

Example:
```yaml
# To Create a namespace
apiVersion: v1
kind: Namespace
metadata:
    name: dev
```

To view pods in all namespaces: Run `kubectl get pods --all-namespaces`

### Resources in a namespace can be restricted using the following yaml:
`kubectl create -f <RESOURCE_QUOTE_DEFINITION_FILE>`
```yaml
apiVersion: v1
kind: ResourceQuota
metadata:
    name: dev-rq
    namespace: dev
spec:
    hard:
        pods: "10"
        requests.cpu: "100m"
```

## **Continue reading**: [Kubernetes: Core Concepts Primer - Part 3](../k8s-primer-p3/)