---
title: "Kubernetes: Core Concepts Primer - Part 3"
slug: "k8s-primer-p3"
tags: ["kubernetes", "k8s-primer"]
date: 2021-08-02T18:47:58+05:30
draft: true
---

# Kubernetes: Core Concepts Primer - Part 3

## Notes

1. To run a custom command in a container instead of standard command that comes with image, use `command` param in pod spec.

2. To pass custom args to a container, use `args` param in pod spec.

3. Remember, you CANNOT edit specifications of an existing POD other than the below.
    ```
        spec.containers[*].image

        spec.initContainers[*].image

        spec.activeDeadlineSeconds

        spec.tolerations
    ```
4. For example you cannot edit the environment variables, service accounts, resource limits (all of which we will discuss later) of a running pod. But if you really want to, you have 2 options:

    1. Run the kubectl edit pod <pod name> command.  This will open the pod specification in an editor (vi editor). Then edit the required properties. When you try to save it, you will be denied. 
    This is because you are attempting to edit a field on the pod that is not editable. 
    A copy of the file with your changes is saved in a temporary location as shown above. 
    You can then delete the existing pod by running the command: `kubectl delete pod webapp`
    Then create a new pod with your changes using the temporary file
    `kubectl create -f /tmp/kubectl-edit-acvrq.yaml`

    2. The second option is to extract the pod definition in YAML format to a file using the command `kubectl get pod webapp -o yaml > my-new-pod.yaml`
    Then make the changes to the exported file using an editor (vi editor). Save the changes. Then delete the existing pod `kubectl delete pod webapp`
    Then create a new pod with the edited file `kubectl create -f my-new-pod.yaml`


5. Edit Deployments

    With Deployments you can easily edit any field/property of the POD template. Since the pod template is a child of the deployment specification,  with every change the deployment will automatically delete and create a new pod with the new changes. So if you are asked to edit a property of a POD part of a deployment you may do that simply by running the command

    `kubectl edit deployment my-deployment`