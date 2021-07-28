---
title: "[CKA] Section2. Core Concepts"
date: '2021-07-26 23:00:00'
category: k8s
draft: false
---

## 📌Cluster Architecture

![components](./images/kubeadm-docker-ps.png)
>잘 보면 etcd, kube-apiserver, kube-controller-manager, kube-scheduler, kube-proxy 컴포넌트가 다 있다. flannel은 네트워크 관련인데, coredns는 뭐지?
> 그리고 왜 2세트가 떠있지..?

### 1. ETCD
모든 클러스터 데이터를 담는 쿠버네티스 뒷단의 저장소로 사용되는 일관성·고가용성 키-값 저장소.

쿠버네티스 클러스터에서 etcd를 뒷단의 저장소로 사용한다면, 이 데이터를 백업하는 계획은 필수!

### 2. kube api server
컨트롤 타워같이 cluster에 명령하는 역할

### 3. kube controller manager
pod, service, namespace 등 k8s의 리소스를 컨트롤하는 역할

### 4. kube scheduler
pod가 어떤 node에 떠야할지 스케줄링하는 애. 최적의 node를 선택하여 pod를 배포해주려고 하는 역할

### 5. kubelet
kube api server가 주는 신호(?)를 각 노드의 kubelet이 잘 받아서 처리해줌

### 6. kube proxy
k8s가 서로 어디 파드가 떠있는지 등 네트워킹을 하려면 필요한 역할을 kube proxy가 함. iptable이라고 지나가다 들었는데, 이게 맞는지는 모르겠음!




## 📌Main k8s resources

### 1. Pods

Create an NGINX Pod

`kubectl run nginx --image=nginx`



Generate POD Manifest YAML file (-o yaml). Don't create it(--dry-run)

`kubectl run nginx --image=nginx --dry-run=client -o yaml`



Create a deployment

`kubectl create deployment --image=nginx nginx`



Generate Deployment YAML file (-o yaml). Don't create it(--dry-run)

`kubectl create deployment --image=nginx nginx --dry-run=client -o yaml`



Generate Deployment YAML file (-o yaml). Don't create it(--dry-run) with 4 Replicas (--replicas=4)

`kubectl create deployment --image=nginx nginx --dry-run=client -o yaml > nginx-deployment.yaml`

Save it to a file, make necessary changes to the file (for example, adding more replicas) and then create the deployment.

### 2. ReplicaSets

### 3. Deployments

### 4. Namespaces

### 5. Services (ClusterIP, Loadbalancer)

[tip - service파트 참고하기](#service)

`kubectl expose pod ...` 커맨드 한 줄로, pod 배포랑 서비스 expose까지 다 할 수 있음!

### point. Imperative Commands
Declarative말고 커맨드로 생성, 배포하는 것도 할 줄 알아야 한다:)
>Create a pod called httpd using the image httpd:alpine in the default namespace. Next, create a service of type ClusterIP by the same name (httpd). The target port for the service should be 80.
>
>`kubectl run httpd --image=httpd:alpine --port=80 --expose`

## 📌Certification Tip!

###kubectl conventions docs 참고하기 :)

https://kubernetes.io/docs/reference/kubectl/conventions/

###사용자 설정

```
# .vimrc
set et
set ts=2
set sw=2
set nu

# .bashrc
alias k='kubectl'
source <(k completion bash) # auto completion enable
complete -F __start_kubectl k # auto completion with command 'k'
```

###축약어

```
pod : po
replicationcontroller : rc
replicaset : rs
deployment : deploy
namespace : ns
service : svc
certificatesigningrequest : csr
ingress : ing
networkpolicies : netpol
node: no
persistentvolumeclaim : pvc
persistentvolume : pv
serviceaccount : sa
daemonset : ds

--namespace : -n
--selector : -l
```

### Certification Tips - Imperative Commands with Kubectl

While you would be working mostly the declarative way - using definition files, imperative commands can help in getting one time tasks done quickly, as well as generate a definition template easily. This would help save considerable amount of time during your exams.

Before we begin, familiarize with the two options that can come in handy while working with the below commands:

--dry-run: By default as soon as the command is run, the resource will be created. If you simply want to test your command , use the --dry-run=client option. This will not create the resource, instead, tell you whether the resource can be created and if your command is right.

-o yaml: This will output the resource definition in YAML format on screen.



Use the above two in combination to generate a resource definition file quickly, that you can then modify and create resources as required, instead of creating the files from scratch.



#### POD
Create an NGINX Pod

`kubectl run nginx --image=nginx`



Generate POD Manifest YAML file (-o yaml). Don't create it(--dry-run)

`kubectl run nginx --image=nginx --dry-run=client -o yaml`



#### Deployment
Create a deployment

`kubectl create deployment --image=nginx nginx`



Generate Deployment YAML file (-o yaml). Don't create it(--dry-run)

`kubectl create deployment --image=nginx nginx --dry-run=client -o yaml`



Generate Deployment with 4 Replicas

`kubectl create deployment nginx --image=nginx --replicas=4`



You can also scale a deployment using the kubectl scale command.

`kubectl scale deployment nginx --replicas=4`

Another way to do this is to save the YAML definition to a file and modify

`kubectl create deployment nginx --image=nginx --dry-run=client -o yaml > nginx-deployment.yaml`



You can then update the YAML file with the replicas or any other field before creating the deployment.



####Service
Create a Service named redis-service of type ClusterIP to expose pod redis on port 6379

`kubectl expose pod redis --port=6379 --name redis-service --dry-run=client -o yaml`

(This will automatically use the pod's labels as selectors)

Or

`kubectl create service clusterip redis --tcp=6379:6379 --dry-run=client -o yaml` (This will not use the pods labels as selectors, instead it will assume selectors as app=redis. You cannot pass in selectors as an option. So it does not work very well if your pod has a different label set. So generate the file and modify the selectors before creating the service)



Create a Service named nginx of type NodePort to expose pod nginx's port 80 on port 30080 on the nodes:

`kubectl expose pod nginx --type=NodePort --port=80 --name=nginx-service --dry-run=client -o yaml`

(This will automatically use the pod's labels as selectors, but you cannot specify the node port. You have to generate a definition file and then add the node port in manually before creating the service with the pod.)

Or

`kubectl create service nodeport nginx --tcp=80:80 --node-port=30080 --dry-run=client -o yaml`

(This will not use the pods labels as selectors)

Both the above commands have their own challenges. While one of it cannot accept a selector the other cannot accept a node port. I would recommend going with the kubectl expose command. If you need to specify a node port, generate a definition file using the same command and manually input the nodeport before creating the service.

Reference:
https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands

https://kubernetes.io/docs/reference/kubectl/conventions/


<br/>
<br/>


---


### 😴
영어 강의 노트 작성하는 식이어서, 개념을 제대로 알고 싶으면 official docs를 꼭 참고할 것.

### 참고
[CKA 강의 by udemy](https://www.udemy.com/course/certified-kubernetes-administrator-with-practice-tests/learn/lecture/16214516#content)

[k8s.io - components overview](https://kubernetes.io/ko/docs/concepts/overview/components/)

[💡CKA 학습과정 및 후기](https://blog.dudaji.com/kubernetes/2019/06/24/cka-acceptance-review-soonbee.html)
