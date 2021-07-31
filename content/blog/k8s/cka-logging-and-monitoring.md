---
title: "[CKA] Section4. Logging & Monitoring"
date: '2021-07-31 15:00:00'
category: k8s
draft: false
---

## 📌Logging & Monitoring

### Monitoring Cluster Components
in-memroy 솔루션인 메트릭 정보는 어떻게 수집될까?
- kubelet의 cAdvisor가 메트릭 서버로 정보를 보냄

metric 설치후에 `kubectl top node`나 `kubectl top pod`하면 CPU와 Memory 정보들이 나옴!
