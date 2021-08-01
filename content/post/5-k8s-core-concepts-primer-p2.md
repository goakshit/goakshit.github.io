---
title: "Kubernetes: Core Concepts Primer - Part 2"
slug: "kubernetes core concepts primer part 2"
tags: ["kubernetes", "k8s-primer"]
date: 2021-08-01T18:14:56+05:30
draft: true
---

# Kubernetes: Core Concepts Primer - Part 2

## What are ReplicaSets?
> A ReplicaSet's purpose is to maintain a stable set of replica Pods running at any given time.

In short, a ReplicaSet is a monitoring and management tool for Pods that ensures a desired number of Pods are running at any given time based on the selector criteria specified in the ReplicaSet. 

Example:
```yaml
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

