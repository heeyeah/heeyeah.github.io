---
title: "[CKA] Section2. Core Concepts"
date: '2021-07-26 23:00:00'
category: k8s
draft: false
---

##Cluster Architecture

![components](./images/kubeadm-docker-ps.png)
>잘 보면 etcd, kube-apiserver, kube-controller-manager, kube-scheduler, kube-proxy 컴포넌트가 다 있다. flannel은 네트워크 관련인데, coredns는 뭐지?
> 그리고 왜 2세트가 떠있지..?

### ETCD
모든 클러스터 데이터를 담는 쿠버네티스 뒷단의 저장소로 사용되는 일관성·고가용성 키-값 저장소.

쿠버네티스 클러스터에서 etcd를 뒷단의 저장소로 사용한다면, 이 데이터를 백업하는 계획은 필수!

### kube api server
컨트롤 타워같이 cluster에 명령하는 역할

### kube controller manager
pod, service, namespace 등 k8s의 리소스를 컨트롤하는 역할

### kube scheduler
pod가 어떤 node에 떠야할지 스케줄링하는 애. 최적의 node를 선택하여 pod를 배포해주려고 하는 역할

### kubelet
kube api server가 주는 신호(?)를 각 노드의 kubelet이 잘 받아서 처리해줌

### kube proxy
k8s가 서로 어디 파드가 떠있는지 등 네트워킹을 하려면 필요한 역할을 kube proxy가 함. iptable이라고 지나가다 들었는데, 이게 맞는지는 모르겠음!


---


### 😴
영어 강의 노트 작성하는 식이어서, 개념을 제대로 알고 싶으면 official docs를 꼭 참고할 것.

### 참고
[CKA 강의 by udemy](https://www.udemy.com/course/certified-kubernetes-administrator-with-practice-tests/learn/lecture/16214516#content)

[k8s.io - components overview](https://kubernetes.io/ko/docs/concepts/overview/components/)
