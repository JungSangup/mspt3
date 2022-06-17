---
marp: true
author: sangup.jung@gmail.com
size: 16:9
theme: mspt2
header: MSP T2 - Docker & Kubernetes
footer: Samsung SDS
---

# H1, 12345, ABCDE, 동해물과 백두산이
## H2, 12345, ABCDE, 동해물과 백두산이
### H3, 12345, ABCDE, 동해물과 백두산이
#### H4, 12345, ABCDE, 동해물과 백두산이
##### H5, 12345, ABCDE, 동해물과 백두산이


![](./img/handson.png) **Hands-on :** 01_Docker_Intro

![](./img/hyperlink.png)[Docker architecture](https://docs.docker.com/get-started/overview/#docker-architecture)

---

# Cloud Native

![w:200](./img/cloud_native_1.png)

동해물과 백우산이
마르고 닳도록
하느님이 보우사하
우리나라 만세


---

# Cloud Native

## Cloud (Orchestration)
## Microservices
## DevOps

---

# Cloud Native

| ![w:200](./img/cloud_native_1.png) | 동해물과 백두산이<br>마르고 닳도록 |
|-----|-----|
| ![w:200](./img/cloud_native_1.png) | 동해물과 백두산이<br>마르고 닳도록 |
|Bar  |101  |



---

# Docker

| Syntax      | Description | Test Text     |
| :---        |    :----:   |          ---: |
| Header      | Title       | Here's this   |
| Paragraph   | Text        | And more      |

---

# Docker

|||
|-----|-----|
| ![w:200](./img/cloud_native_1.png) | 동해물과 백두산이<br>마르고 닳도록 |
|Bar  |101  |

---

## [Hands-on] 00. Docker ...


다음 실습을 해보겠습니다.
```bash
ubuntu@ip-10-0-1-14:~$ docker rm --force $(docker ps --filter "label=color=blue" --quiet)
670302b2287f
```
> **명령어** : `docker rm --force $(docker ps --filter "label=color=blue" --quiet)`