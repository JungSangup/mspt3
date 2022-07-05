---
marp: true
author: sangup.jung@gmail.com
size: 16:9
theme: mspt2
paginate: true
header: Docker & Kubernetes - 11. Kubernetes Service
footer: Samsung SDS
---

# Contents

![bg left:40%](img/docker_k8s.png)

<br>

- **Service**
  - 

---

## Service

**Kubernetes**에서 **Service**는 **Pod**로 실행중인 애플리케이션을 네트워크 서비스로 노출하는 추상화 방법입니다.
**Pod**는 생성되면 고유의 IP를 가지지만 수시로 생성/삭제/확장되는 환경을 감안하면, 주어진 Pod의 IP로 서비스를 이용하는것은 불가능합니다.
그래서, **Kubernetes**에서는 **Service**라는 리소스를 사용하여
- 고정된 IP
- 여러개의 Pod에 대한 Loadbalancing

을 제공합니다.

![h:300](img/k8s_service1.png) 

---



![](./img/handson.png) **Hands-on :** 11_Kubernetes_Service

---

## Summary

- Deployment strategies
  - Recreate
  - RollingUpdate
  - Blue/Green deployments
  - Canary deployments

<br><br><br><br><br><br><br><br><br><br><br><br>

`문의처` : 정상업 / rogallo.jung@samsung.com