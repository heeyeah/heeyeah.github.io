---
title: "[CKA] Section6. Cluster Maintenance"
date: '2021-08-01 09:00:00'
category: k8s
draft: false
---

## 📌Cluster Maintenance

### OS Upgrades


``` shell
kubectl cordon my-node      # my-node를 스케줄링할 수 없도록 표기
kubectl drain my-node       # 유지 보수를 위해서 my-node를 준비 상태로 비움
kubectl uncordon my-node    # my-node를 스케줄링할 수 있도록 표기
```

### Kubernetes Software Versions

k8s의 version 체계
- {major version}.{minor version}.{patch version}

### Cluster Upgrade

`kubeadm upgrade plan`

kubeadm은 kubelet의 업그레이드까지 해주지 않기 때문에, 따로 kubelet upgrade해줘야 함

Master와 node-1(worker)이 있다고 가정했을 때

``` shell

(Master) $ kubectl drain node-1

(node-1) $ apt-get upgrade -y kubeadm=1.12.0-00

(node-1) $ apt-get upgrade -y kubelet=1.12.0-00

(node-1) $ kubeadm upgrade node config --kubelet-version v1.12.0

(node-1) $ systemctl restart kubelet

(Master) $ kubectl uncordon node-1
```

### Backup and Restore Methods

#### ETCD 백업과 복구
이 내용은 공식 문서에 나오지 않는다. 따라서 반드시 암기 해야 한다. 긴 명령어를 완전히 암기한다기 보다 눈에 익혀두는 게 맞겠다.
일단 --help 명령어를 쳐보면 기본값을 써도 되는 것도 있고, 문제에서 Certificates 파일 경로를 알려주기 때문에 눈치가 있다면 그것들에 해당하는 옵션만 넣어도 쉽게 해결할 수 있다.

ETCD 스냅샷 백업하기

`etcdctl snapshot save -h` 이걸로 먼저 help 스크립트 볼 것!

```
Since our ETCD database is TLS-Enabled, the following options are mandatory:

--cacert           verify certificates of TLS-enabled secure servers using this CA bundle

--cert             identify secure client using this TLS certificate file

--endpoints=[127.0.0.1:2379]          This is the default as ETCD is running on master node and exposed on localhost 2379.

--key              identify secure client using this TLS key file
```

```
$ etcdctl \
  # --endpoints=https://[127.0.0.1]:2379 \  # 기본값
  --cacert=/etc/kubernetes/pki/etcd/ca.crt \
  --cert=/etc/kubernetes/pki/etcd/server.crt \
  --key=/etc/kubernetes/pki/etcd/server.key \
  snapshot save /tmp/some-snapshot.db
```
ETCD 스냅샷 복구하기
```
$ etcdctl \
  # --endpoints=https://[127.0.0.1]:2379 \  # 기본값
  --cacert=/etc/kubernetes/pki/etcd/ca.crt \
  --cert=/etc/kubernetes/pki/etcd/server.crt \
  --key=/etc/kubernetes/pki/etcd/server.key \
  --name=master \
  --data-dir /var/lib/etcd-from-backup \
  --initial-cluster=master=https://127.0.0.1:2380 \
  --initial-advertise-peer-urls=https://127.0.0.1:2380 \
  snapshot restore /tmp/some-snapshot.db
```

---

### ✨Certification Exam Tip!

Here's a quick tip. In the exam, you won't know if what you did is correct or not as in the practice tests in this course. You must verify your work yourself. For example, if the question is to create a pod with a specific image, you must run the the kubectl describe pod command to verify the pod is created with the correct name and correct image.


<br/>
<br/>

### 참고
[CKA 강의 by udemy](https://www.udemy.com/course/certified-kubernetes-administrator-with-practice-tests/learn/lecture/16214516#content)

[조은우 개발 블로그-CKA후기](https://jonnung.dev/kubernetes/2020/08/24/cka-challenge-and-spoiler-free-tips/)
