---
title: "[CKA] Section3. Scheduling"
date: '2021-07-29 23:00:00'
category: k8s
draft: false
---


## 📌Scheduling

### 1. manual scheduling

#### scheduler가 없을 때 나타나는 상태값

![components](./images/no-scheduler.png)

#### node할당안된 pod에 nodeName으로 node 할당하기
Delete the existing pod first. Run the below command:

`$ kubectl delete po nginx`

To list and know the names of available nodes on the cluster:

`$ kubectl get nodes`

Add the nodeName field under the spec section in the nginx.yaml file with node01 as the value:

```
---
apiVersion: v1
kind: Pod
metadata:
  name: nginx
spec:
  nodeName: node01
  containers:
  -  image: nginx
     name: nginx
```
Then run the command kubectl create -f nginx.yaml to create a pod from the definition file.

To check the status of a nginx pod and to know the node name:

`$ kubectl get pods -o wide`

<br/>

### 2. Labels and Selectors
label을 지정하고 selector로 매칭시키기 :)

>label이 env=prod이고 tier=frontend인 pod 다 조회해주세요!
>
>`kubectl get po -l env=prod,tier=frontend`


### Taints and Tolerations

### Node Selectors

### Node Affinity

### DaemonSets

### Static Pods

### Multiple Schedulers

### Configuring Kubernetes Scheduler
