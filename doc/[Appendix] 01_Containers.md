---
marp: true
author: sangup.jung@gmail.com
size: 16:9
theme: mspt2
paginate: true
header: Docker & Kubernetes - Appendix 01. Containers
footer: Samsung SDS
---

![bg left:40%](img/docker_k8s.png)

<br>

# Contents

<br>

- **Kernel Space**
- **Container Runtimes**
- **Container Images**



---

## [Kernel Space](https://medium.com/@saschagrunert/demystifying-containers-part-i-kernel-space-2c53d6979504)

**Containers** are only isolated groups of processes running on a single host, which fulfill a set of “common” features.


### chroot, pivot_root
파일시스템 root를 변경. (/ 를 다른 경로로 설정.)  
이것만으로는 프로세스, 네트워크 격리를 할 수 없음.

![h:300](./img/chroot.png)

> `jail` : chroot를 이용하여 해커활동 모니터링에 사용되어 Jail이라는 용어를 사용함. (1991)  

---

### Linux Namespaces
**Namespaces** are a Linux kernel feature which were introduced back in 2002 with Linux 2.4.19.  

Not all available namespaces were implemented from the beginning. A full support for what we now understand as **“container ready”** was finished in **kernel version 3.8** back in 2013 with the introduction of the user namespace.

- seven distinct namespaces (current) : *mnt, pid, net, ipc, uts, user and cgroup.*
- two additional namespaces (not fully implemented yet) : *time and syslog.* 

- Namespace APIs
    - clone : creates a new child process
    - unshare : disassociate parts of the execution context
    - setns : reassociates the calling thread with the provided namespace file descriptor




---

## Container Runtimes



---

## Container Images


---

### References
- [Demystifying Containers - Part I: Kernel Space](https://medium.com/@saschagrunert/demystifying-containers-part-i-kernel-space-2c53d6979504)
- [Demystifying Containers - Part II: Container Runtimes](https://medium.com/@saschagrunert/demystifying-containers-part-ii-container-runtimes-e363aa378f25)
- [Demystifying Containers — Part III: Container Images](https://medium.com/@saschagrunert/demystifying-containers-part-iii-container-images-244865de6fef)