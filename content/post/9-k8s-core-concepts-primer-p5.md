---
title: "Kubernetes: Core Concepts Primer - Part 5"
slug: "k8s-primer-p5"
tags: ["kubernetes", "k8s-primer"]
date: 2021-08-14T09:30:22+05:30
draft: false
---

# Kubernetes: Core Concepts Primer - Part 5

## What are taints and tolerances?
> Taints are a mechanism to add restrictions to the scheduling of pods. Tolerance is a mechanism to allow pods to be scheduled on nodes with certain conditions.

To taint a node, use `kubectl taint nodes <NODE_NAME> <KEY>=<VALUE>:<TAINT_EFFECT>`

There are three kinds of taint effects:
- `NoSchedule`: This taint indicates that the node should not be used by pods.
- `PreferNoSchedule`: This taint indicates that the node should not be used by pods, but it is not guaranteed.
- `NoExecute`: This taint indicates that the node should not be used by pods, and if there are existing pods that do not tolerate the taint, they will be terminated.

Tolerations are added to the pod definition file:
```yaml
apiVersion: v1
kind: Pod
metadata:
    name: my-pod
spec:
    containers:
        - name: redis
          image: redis
    # All the values under tolerations have to encoded in double quotes
    tolerations:
        key: "<KEY>"
        operator: "<OPERATOR>" # <OPERATOR> can be one of these: "Equal", "LessThan", "LessThanOrEqual", "GreaterThan", "GreaterThanOrEqual", "Exists", "DoesNotExist", "In", "NotIn"
        value: "<VALUE>"
        effect: "<TAINT_EFFECT>" # <TAINT_EFFECT> can be one of these: "NoSchedule", "PreferNoSchedule", "NoExecute"
```

To remove the taint, run `kubectl taint nodes <NODE_NAME> <TAINT_NAME>:<TAINT_EFFECT>`

## What is node selector?
> NodeSelector is a label query over a set of nodes. The node that matches the label query is the one that will be used for scheduling pods.

Let's understand that with an example, say we have a cluster with 2 nodes with 1gb & 2gb memory respectively. We have two pods(A & B), where A runs more resource heavy task. We want to schedule A on node with 2gb memory. We can do that by adding `nodeSelector` to the pod definition file:
```yaml
apiVersion: v1
kind: Pod
metadata:
    name: my-pod
spec:
    containers:
        - name: redis
          image: redis
    nodeSelector:
        size: <NODE_LABEL>
```

**Note**: Ensure that nodes are already labeled with the labels that you want to match. To create a label if its not there, use `kubectl label nodes <NODE_NAME> <LABEL_NAME>=<LABEL_VALUE>`

## What is node affinity?
> Node affinity is a set of rules used by the scheduler to determine where a pod can be placed

There are currently two types of node affinity:
- `requiredDuringSchedulingIgnoredDuringExecution`: Schedule the pod only on the nodes that match the nodeSelector. If no such nodes are available, the scheduler will not schedule the pod on nodes. If the pod is already scheduled on a node, the scheduler will ignore the nodeSelector.

- `preferredDuringSchedulingIgnoredDuringExecution`: Preference is given for the node that matches the expression but if no such nodes are available, the scheduler will schedule the pod in any available node. If the pod is already scheduled on a node, the scheduler will ignore the nodeSelector.

Example:
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: with-node-affinity
spec:
  affinity:
    nodeAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
        nodeSelectorTerms:
        - matchExpressions:
          - key: size
            operator: In
            values:
            - t1.large
            - t1.medium
  containers:
  - name: with-node-affinity
    image: k8s.gcr.io/pause:2.0
```

