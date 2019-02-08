---
owner_name: Sumit Binnani
owner_url: https://sumitbinnani.github.io
---

# Getting Started with Kubernetes
---

## Setup

### Account Creation and Validation
1. [Login to Nautilus](https://nautilus.optiputer.net/) using your eng.ucsd.edu email and Google as your identity provider.
2. Fill [this form](https://goo.gl/forms/9f5g22XmrsftheXC3) to get your account validated.
3. Once you are validated as users, you may use the server farm.


### System Setup
1. Install the kubectl tool
2. Click the "Get Config" link on top and get your config file
3. Put the file to your <home>/.kube folder
4. Make sure you're promoted from guest, and have a namespace assigned to you (namespace will be `guru-research`)
5. Test kubectl can connect to the cluster:  ```kubectl get pods -n your_namespace```
6. Run busybox container in your namespace to check your setup: ```kubectl run busybox -n your_namespace -it --rm --image=busybox -- sh```. It will quit once you log out from the console.
  
## Kubernetes Pods
Kubernetes Pods are the smallest deployable computing units in the open source Kubernetes container scheduling and orchestration environment. A Pod is a grouping of one or more containers that operate together. The specifications of the pod are defined in the .yaml file. 

### Pod Specification
Let's create a sample pod specification. The following .yaml file specifies the name of the pod as `gpu-pod` and grabs the docker image from docker hub under [sbinnani/pytorch](https://hub.docker.com/r/sbinnani/pytorch) with the tag ``cuda10.0-cudnn7-runtime-ubuntu18.04``, and then the container will simply linger around forever because of the sleep infinity command. This is actually kind of useful as you can hop into the container. The docker image has cuda10.0, cudnn7-runtime and run ubuntu18.04 and has conda preinstalled. Also, the docker container has root access (go crazy ;-)).

```
apiVersion: v1
kind: Pod
metadata:
  name: sample-pod
spec:
  containers:
  - name: pytorch-container
    image: sbinnani/pytorch:cuda10.0-cudnn7-runtime-ubuntu18.04
    args: ["sleep", "infinity"]
    resources:
      requests:
        memory: "24Gi"
      limits:
        memory: "64Gi"
        nvidia.com/gpu: 1
```

Let the above file be stored as `pytorch-gpu-pod.yaml (can be downloaded from [here](sample_scripts/pytorch-gpu-pod.yaml).

### Launching Pod
You can launch your pod on luberenetes cluster by executing: ```kubectl create -f pytorch-gpu-pod.yaml -n <name_space>```. You can check the status of the pod using: ```kubectl get pods -n <name_space>```. Once the pod status is "Running", your pod is ready to be used.

### SSH into the Containiner
Once the pod status is "Running", you can can ssh into the the container using: ```kubectl exec -it sample-pod -- /bin/bash```. If your pod is running multiple containers, you can ssh into specific container, say, `pytorch-container` here, by executing: ```kubectl exec -it sample-pod -c pytorch-container -- /bin/bash```.

### About Data
Note that the data is lost, once the container is killed/terminated. So, we need to create persistent volume. The post will be updated to elaborate on the same soon.

## To Do
- Example for file transfer between containers and localhost
- Example for persistent storage

## Other
- Read the Wiki: https://wiki.nautilus.optiputer.net/wiki/Main_Page
- Register in our Rocket Chat: https://rocket.nautilus.optiputer.net
- Ask Question on rocket chat :-)
