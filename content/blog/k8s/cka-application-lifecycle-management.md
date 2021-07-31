---
title: "[CKA] Section5. Application Lifecycle Management"
date: '2021-07-31 20:00:00'
category: k8s
draft: false
---

## 📌Application Lifecycle Management


### Rolling Updates and Rollbacks


`kubectl rollout status deployment/myapp-deployment`

`kubectl rollout history deployment/myapp-deployment`


StrategyType
- Recreate
- RollingUpdate
- Upgrades
- Rollback
  `kubectl rollout undo deployment/myapp-deployment`


### Commands and Arguments

- Docker의 Entrypoint랑 cmd 차이
- pod의 command와 argument의 문법(?) - double qoutes
- Docker entrypoint나 cmd에 상관없이 pod에 설정해놓은 것 따라감

### Environment Variables

요렇게 pod에다가 `configMapKeyRef`를 설정해서 사용하고!
``` yaml

apiVersion: v1
kind: Pod
metadata:
  name: dapi-test-pod
spec:
  containers:
    - name: test-container
      image: k8s.gcr.io/busybox
      command: [ "/bin/echo", "$(SPECIAL_LEVEL_KEY) $(SPECIAL_TYPE_KEY)" ]
      env:
        - name: SPECIAL_LEVEL_KEY
          valueFrom:
            configMapKeyRef:
              name: special-config
              key: SPECIAL_LEVEL
        - name: SPECIAL_TYPE_KEY
          valueFrom:
            configMapKeyRef:
              name: special-config
              key: SPECIAL_TYPE
  restartPolicy: Never
```

configmap은 이렇게 만듭니다.

``` yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: game-demo
data:
  # property-like keys; each key maps to a simple value
  player_initial_lives: "3"
  ui_properties_file_name: "user-interface.properties"

  # file-like keys
  game.properties: |
    enemy.types=aliens,monsters
    player.maximum-lives=5    
  user-interface.properties: |
    color.good=purple
    color.bad=yellow
    allow.textmode=true    
```
### Secrets

create secret!

`kubectl create secret generic db-secret --from-literal=DB_Host=sql01  --from-literal=DB_User=root --from-literal=DB_Password=password123`



이건 secret참고하는 pod 만들 때 사용하는 yaml
``` yaml
apiVersion: v1
kind: Pod
metadata:
  labels:
    name: webapp-pod
  name: webapp-pod
  namespace: default
spec:
  containers:
  - image: kodekloud/simple-webapp-mysql
    imagePullPolicy: Always
    name: webapp
    envFrom:
    - secretRef:
        name: db-secret
```
### Multi Container PODs


`kubectl -n elastic-stack exec -it app -- cat /log/app.log`

**Q. Edit the pod to add a sidecar container to send logs to Elastic Search. Mount the log volume to the sidecar container.**

*아니 이거 사이드카 붙이는 건데, 이런 것도 다 외워야하는거 아니겠지???????*


**Only add a new container. Do not modify anything else. Use the spec provided below.**

<span style="color:#c90035">k8s.io/docs 문서에서 Configure a Pod to Use a Volume for Storage 참고 (search keyword for me : volume mount😃) </span>

>**CheckCompleteIncomplete**
```
Name: app
Container Name: sidecar
Container Image: kodekloud/filebeat-configured
Volume Mount: log-volume
Mount Path: /var/log/event-simulator/
Existing Container Name: app
Existing Container Image: kodekloud/event-simulator
```
``` yaml
apiVersion: v1
kind: Pod
metadata:
  name: app
  namespace: elastic-stack
  labels:
    name: app
spec:
  containers:
  - name: app
    image: kodekloud/event-simulator
    volumeMounts:
    - mountPath: /log
      name: log-volume

  - name: sidecar
    image: kodekloud/filebeat-configured
    volumeMounts:
    - mountPath: /var/log/event-simulator/
      name: log-volume

  volumes:
  - name: log-volume
    hostPath:
      # directory location on host
      path: /var/log/webapp
      # this field is optional
      type: DirectoryOrCreate
```
### Init Containers

pod만드는데 여러개의 컨테이너가 뜰 때, 우선적으로 실행돼야하는 컨테이너가 있을 때 `initContainers`에 지정해서 사용. 안뜨면 pod가 계속 시도한다고 함!
``` yaml

spec:
  containers:
  - name: blah
  ...

  initContainers:
  - name: init-for-pod
  ...
```
