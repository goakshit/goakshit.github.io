---
title: "Kubernetes: Core Concepts Primer - Part 4"
slug: "k8s-primer-p4"
tags: ["kubernetes", "k8s-primer"]
date: 2021-08-07T09:42:31+05:30
draft: true
---

# Kubernetes: Core Concepts Primer - Part 4

## Security Context in Kubernetes

Whenever it comes to security, always follow the `The principe of least privilege` (PlP) principle which states that **a subject should be given only those privileges needed for it to complete its task.**

Applying the same principle to kubernetes objects, we should grant a user the privileges needed to perform a task. Thats all, nothing more or nothing less. Giving root permissions to containers is not recommended. 

#### NOTE: Permissions at container level will always override permisions at pod level

- To set permissions container, use `securityContext` field in the container definition.
- To set permissions pod, use `securityContext` field in the pod definition file.
- Capabilities can be added only to the container which are container's user permission

Example for pod level security context:
```yaml
apiVersion: v1
kind: Pod
metadata:
    name: my-pod
securityContext:
    runAsUser: <USER_ID>
spec:
    containers:
        - name: redis
          image: redis
```

Example for container level security context:
```yaml
apiVersion: v1
kind: Pod
metadata:
    name: my-pod
spec:
    containers:
        - name: redis
          image: redis
          securityContext:
            runAsUser: <USER_ID>
            capbilities:
                add: ["SYS_ADMIN"]
```

# What are user & service accounts?
> User Accounts are used by developers/adminstrators to perform some administrative tasks. Service accounts are used by applications to perform some tasks. For eg: Jenkins uses service accounts for deployment purposes.

To create a service account, run `kubectl create serviceaccount <SERVICE_ACCOUNT_NAME>`

Creating a service account creates a token as a secret, that should be used for authentication by the service.

To add service account in a pod, use `serviceAccount` field in the same level as `containers` definition.