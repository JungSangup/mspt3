---
marp: true
author: sangup.jung@gmail.com
size: 16:9
theme: mspt2
paginate: true
header: Docker & Kubernetes - [Hands-on] 11. Kubernetes Storage
---

![bg left:40%](img/hands_on.png)

<br>

# Contents

<br>

- **PersistentVolumeClaim(PVC), PersistentVolume(PV) 생성하기**
- **Volume을 사용하여 Pod 생성하기**

---

## PersistentVolumeClaim(PVC), PersistentVolume(PV) 생성하기

도커에서와 마찬가지로 컨테이너의 데이터 저장을 위해서 Volume을 생성해 보겠습니다.  
이번 실습은 다양한 방법 중에서

- Storage class를 이용한 [Dynamic Volume Provisioning](https://kubernetes.io/ko/docs/concepts/storage/dynamic-provisioning/) 을 적용하고,
- [hostPath](https://kubernetes.io/ko/docs/concepts/storage/volumes/#hostpath) 유형의 Volume을 사용  

해서 진행해 보겠습니다.

먼저 우리 환경이 준비가 되어있는지 확인해볼게요.
우리 Cluster에서 사용가능한 Storageclass가 있는지 확인합니다.
```bash
ubuntu@ip-172-31-20-30:~$ kubectl get storageclasses
NAME                 PROVISIONER                RECLAIMPOLICY   VOLUMEBINDINGMODE   ALLOWVOLUMEEXPANSION   AGE
standard (default)   k8s.io/minikube-hostpath   Delete          Immediate           false                  11h
```
> **명령어** : `kubectl get storageclasses`

Minikube는 기본적으로 위와같은 StorageClass가 있습니다.
간단히 테스트해볼 수 있도록, hostPath 타입의 Volume을 만들 수 있습니다.

---

이제 PVC를 만들어볼게요.
아래와 같은 파일을 준비합니다.
```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: nginx-pvc
spec:
  storageClassName: standard
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 3Gi
```
> 파일명은 **nginx-pvc.yaml**로 합니다.

**standard** 라는 이름의 StorageClass를 이용해서, 3Gi 용량의 **PV**를 요청(Claim)하는 것입니다.

그리고, 아래와 같이 적용합니다.
```yaml
ubuntu@ip-172-31-20-30:~$ kubectl apply -f nginx-pvc.yaml
persistentvolumeclaim/nginx-pvc created
```
> **명령어** : `kubectl apply -f nginx-pvc.yaml`

---

만들어진 K8s 리소스들을 볼까요?
먼저 PVC를 확인해볼게요.
```yaml
ubuntu@ip-172-31-20-30:~$ kubectl get persistentvolumeclaims
NAME        STATUS   VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS   AGE
nginx-pvc   Bound    pvc-fc09de90-0ef1-43aa-8c83-bb2d26bb514b   3Gi        RWO            standard       79s
```
> **명령어** : `kubectl apply -f nginx-pvc.yaml`












---

## Volume을 사용하여 Pod 생성하기

---

이번 실습은 여기까지 입니다.  ˘◡˘
