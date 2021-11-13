---
title: "Kubernetes: Core Concepts Primer - Part 4"
slug: "k8s-primer-p4"
tags: ["kubernetes", "k8s-primer"]
date: 2021-08-07T09:42:31+05:30
draft: false
---
## Security Context in Kubernetes

Whenever it comes to security, always follow the `The principle of least privilege` (PlP) principle which states that **a subject should be given only those privileges needed for it to complete its task.**

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


# Resource requirements in Pods:
> Resource requirements are a way to specify the amount of resources that a container needs. It is a container level configuration. 

In containers array in pod definition, you can specify the resource requirements.
```yaml
resources:
    requests:
        cpu: <CPU_ALLOTED>
        memory: <MEMORY_ALLOTED>
    limits:
        cpu: <CPU_LIMIT>
        memory: <MEMORY_LIMIT>
``` 

Limits restricts resources extra resources to be used by the container. If a container tried to superceed the cpu limits, the system will kill it. 

### Create default resources requirements
To default the resource requirements for a namespace, create a yaml file
```yaml
apiVersion: v1
kind: LimitRange
metadata:
  name: cpu-limit-range
spec:
  limits:
  - default:
      cpu: 1
    defaultRequest:
      cpu: 0.5
    type: Container
```
Then, run `kubectl apply -f <LIMIT_RANGE_FILE> --namespace=<NAMESPACE_TO_APPLY_TO>`


### What are k8s CPU & Memory units?
- **CPU**:

    CPU unit specifies the amount of CPU time that a container can use. Refer the following examples:

    - Consider a container using 50m cpu resource. The unit suffix m stands for “thousandth of a core”, so this resources object specifies that the container process needs 50/1000 of a core (5%). Or 5% of a core.
    - Consider a container using 2000m cpu resource. In this case, requirement is 2000/1000, which is 2 cores. Instead of 2000m, we can just use integer value 2.

- **Memory**:

    Memory unit specifies the amount of memory that a container can use. It is always measured in bytes. Supported values are:

    | Unit Name | Memory Unit | Memory Value(1 unit in bytes) |
    |:-:| :-: | :-: |
    |Kilobyte| K | 1000 |
    |Megabyte| M | 1,000,000 |
    |Gigabyte| G | 1,000,000,000 |
    |Kibibyte| Ki | 1024 |
    |Mebibyte| Mi | 1,048,576 |
    |Gibibyte| Gi | 1,073,741,824 |


#### **Continue reading**: [Kubernetes: Core Concepts Primer - Part 5](../k8s-primer-p5/)
