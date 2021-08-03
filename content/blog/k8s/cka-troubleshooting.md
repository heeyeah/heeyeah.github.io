---
title: "[CKA] Trouble Shooting!"
date: '2021-08-02 09:10:00'
category: k8s
draft: false
---

## Application Failure

application failure은, (서비스-파드) 관계가 있을 때

- 서비스가 파드를 잘 select하고 있는지
- 노출된 port는 잘 맞는지
- 주입된 환경변수(env)는 잘 들어갔는지

이런 것에 대해서 질의한다😎


## Control Plane Failure

### scheduler yaml 고치기
/etc/kubernetes/manifest 밑에 있었는데, 이거 경로 찾는 과정이 있었음 (찾아보기!)

kube-scheduler.yaml

``` yaml

spec:
  containers:
  - command:
    - kube-scheduler
    - --authentication-kubeconfig=/etc/kubernetes/scheduler.conf
    - --authorization-kubeconfig=/etc/kubernetes/scheduler.conf
    - --bind-address=127.0.0.1
    - --kubeconfig=/etc/kubernetes/scheduler.conf
    - --leader-elect=true
    - --port=0

```

### controller-manager 고치기

`ps -aux | grep kubelet` 무조건 치고 들어가서 config 파일 확인하고 static pod directory 확인!!

1. static pod yaml 고치는 문제!

2. 일단 에러 난 파드 log 확인했더니 이렇게 나옵니당

``` shell

root@controlplane:/etc/kubernetes# k logs -f kube-controller-manager-controlplane -n kube-system
Flag --port has been deprecated, see --secure-port instead.
I0803 13:49:18.220043       1 serving.go:331] Generated self-signed cert in-memory
unable to load client CA file "/etc/kubernetes/pki/ca.crt": open /etc/kubernetes/pki/ca.crt: no such file or directory

```


## Worker Node Failure

worker node failure인건 다 kubelet 문제네

*<span style="color:#e449c7"> 😈kubelet 역할 : The kubelet is the primary "node agent" that runs on each node. It can register the node with the apiserver using one of: the hostname; a flag to override the hostname; or specific logic for a cloud provider.</span>*

상태 확인

`service kubelet status`

로그 확인

`sudo journalctl -u kubelet`

인증서 확인

`openssl x509 -in /var/lib/kubelet/worker-1.crt -text`


status가 False나 true이면, master에 연결은 돼있는거고, Unknown이면 master랑도 끊어진것 :)

### fix node!

Step1: Check the status of the nodes:

``` shell
root@controlplane:~# kubectl get nodes
NAME           STATUS     ROLES                  AGE     VERSION
controlplane   Ready      control-plane,master   6m38s   v1.20.0
node01         NotReady   <none>                 4m59s   v1.20.0
root@controlplane:~#
```

Step 2: SSH to node01 and check the status of container runtime (docker, in this case) and the kubelet service.

``` shell
root@node01:~# systemctl status kubelet
● kubelet.service - kubelet: The Kubernetes Node Agent
   Loaded: loaded (/lib/systemd/system/kubelet.service; enabled; vendor preset: enabled)
  Drop-In: /etc/systemd/system/kubelet.service.d
           └─10-kubeadm.conf
   Active: inactive (dead) since Sun 2021-07-25 07:46:58 UTC; 5min ago
     Docs: https://kubernetes.io/docs/home/
  Process: 1917 ExecStart=/usr/bin/kubelet $KUBELET_KUBECONFIG_ARGS $KUBELET_CONFIG_ARGS $KUBELET_KUBEADM_ARGS $KUBELET_EXTRA_ARGS (code=exited,
 Main PID: 1917 (code=exited, status=0/SUCCESS)
```

Since the kubelet is not running, attempt to start it by running:

``` shell
root@node01:~# systemctl start kubelet
root@node01:~# systemctl status kubelet
● kubelet.service - kubelet: The Kubernetes Node Agent
   Loaded: loaded (/lib/systemd/system/kubelet.service; enabled; vendor preset: enabled)
  Drop-In: /etc/systemd/system/kubelet.service.d
           └─10-kubeadm.conf
   Active: active (running) since Sun 2021-07-25 07:53:35 UTC; 2s ago
     Docs: https://kubernetes.io/docs/home/

```

node01 should go back to ready state now.

### fix cluster - 1

생각을 해야할 게, not ready가 된 상태의 노드로 가서 점검을 해야함!

kubelet has stopped running on node01 again. Since this is a systemd managed system, we can check the kubelet log by running journalctl. Here is a snippet showing the error with kubelet:

```
root@node01:~# journalctl -u kubelet
.
.
Jul 25 07:54:50 node01 kubelet[5681]: F0725 07:54:50.831238    5681 server.go:257] unable to load client CA file /etc/kubernetes/pki/WRONG-CA-FILE.crt: open /etc/kubernetes/pki/WRONG-CA-FILE.crt: no such file or directory
Jul 25 07:55:01 node01 kubelet[5710]: F0725 07:55:01.339531    5710 server.go:257]
.
.

```

There appears to be a mistake path used for the CA certificate in the kubelet configuration. This can be corrected by updating the file `/var/lib/kubelet/config.yaml`.
Once this is fixed, restart the kubelet service, (like we did in the previous question) and node01 should return back to a working state.

### fix cluster - 2

Once again the kubelet service has stopped working. Checking the logs, we can see that this time, it is not able to reach the kube-apiserver.

```
root@node01:~# journalctl -u kubelet
.
.
Jul 25 08:05:26 node01 kubelet[7966]: E0725 08:05:26.426155    7966 reflector.go:138] k8s.io/kubernetes/pkg/kubelet/config/apiserver.go:46: Failed to watch *v1.Pod: failed to list *v1.Pod: Get "https://controlplane:6553/api/v1/pods?fieldSelector=spec.nodeName%3Dnode01&limit=500&resourceVersion=0": dial tcp 10.1.126.9:6553: connect: connection refused
.
.
```
As we can clearly see, kubelet is trying to connect to the API server on the <span style="color:#ef2323"> controlplane node on port 6553. This is incorrect.</span>
To fix, correct the port on the kubeconfig file used by the kubelet.

``` yaml
apiVersion: v1
clusters:
- cluster:
    certificate-authority-data:
    --REDACTED---
    server: https://controlplane:6443
```

Restart the kubelet after this change.



## Network Troubleshooting
