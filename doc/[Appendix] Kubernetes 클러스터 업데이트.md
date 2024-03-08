## [Appendix] Kubernetes 클러스터 업데이트 ( with kubeadm )


다음 두 가지 경우 업데이트 절차입니다.

1. Kubernetes 버젼 업데이트
2. 인증서 갱신


---

### 1. Kubernetes 버젼 업데이트


작성예정...


---

### 2. 인증서 갱신

Kubernetes 컴포넌트들에 적용된 인증서의 기간이 만료됐거나 서버 구성 변경 등으로 인해 인증서 갱신이 필요한 경우 절차입니다.


인증서 만료 확인 

```bash
$ sudo kubeadm certs check-expiration
[check-expiration] Reading configuration from the cluster...
[check-expiration] FYI: You can look at this config file with 'kubectl -n kube-system get cm kubeadm-config -o yaml'
[check-expiration] Error reading configuration from the Cluster. Falling back to default configuration

CERTIFICATE                EXPIRES                  RESIDUAL TIME   CERTIFICATE AUTHORITY   EXTERNALLY MANAGED
admin.conf                 Mar 08, 2025 03:04 UTC   364d            ca                      no
apiserver                  Mar 08, 2025 03:03 UTC   364d            ca                      no
apiserver-etcd-client      Mar 08, 2025 03:04 UTC   364d            etcd-ca                 no
apiserver-kubelet-client   Mar 08, 2025 03:04 UTC   364d            ca                      no
controller-manager.conf    Mar 08, 2025 03:04 UTC   364d            ca                      no
etcd-healthcheck-client    Mar 08, 2025 03:04 UTC   364d            etcd-ca                 no
etcd-peer                  Mar 08, 2025 03:04 UTC   364d            etcd-ca                 no
etcd-server                Mar 08, 2025 03:04 UTC   364d            etcd-ca                 no
front-proxy-client         Mar 08, 2025 03:04 UTC   364d            front-proxy-ca          no
scheduler.conf             Mar 08, 2025 03:04 UTC   364d            ca                      no
super-admin.conf           Mar 08, 2025 03:04 UTC   364d            ca                      no

CERTIFICATE AUTHORITY   EXPIRES                  RESIDUAL TIME   EXTERNALLY MANAGED
ca                      Mar 06, 2034 03:03 UTC   9y              no
etcd-ca                 Mar 06, 2034 03:04 UTC   9y              no
front-proxy-ca          Mar 06, 2034 03:04 UTC   9y              no
```

> 💻 명령어
>```bash
>sudo kubeadm certs check-expiration
>```


> 관련 문서 : [kubeadm certs check-expiration](https://kubernetes.io/docs/reference/setup-tools/kubeadm/kubeadm-certs/#cmd-certs-check-expiration)  



설정파일(*.conf), Static pod manifests(/manifests), 인증서(/pki) 백업

```bash
$ $ sudo cp -r /etc/kubernetes ~/k8s-backup/
$ tree ~/k8s-backup/
/home/ubuntu/k8s-backup/
└── kubernetes
    ├── admin.conf
    ├── controller-manager.conf
    ├── kubelet.conf
    ├── manifests  [error opening dir]
    ├── pki
    │   ├── apiserver-etcd-client.crt
    │   ├── apiserver-etcd-client.key
    │   ├── apiserver-kubelet-client.crt
    │   ├── apiserver-kubelet-client.key
    │   ├── apiserver.crt
    │   ├── apiserver.key
    │   ├── ca.crt
    │   ├── ca.key
    │   ├── etcd
    │   │   ├── ca.crt
    │   │   ├── ca.key
    │   │   ├── healthcheck-client.crt
    │   │   ├── healthcheck-client.key
    │   │   ├── peer.crt
    │   │   ├── peer.key
    │   │   ├── server.crt
    │   │   └── server.key
    │   ├── front-proxy-ca.crt
    │   ├── front-proxy-ca.key
    │   ├── front-proxy-client.crt
    │   ├── front-proxy-client.key
    │   ├── sa.key
    │   └── sa.pub
    ├── scheduler.conf
    └── super-admin.conf

5 directories, 27 files
```

> 💻 명령어
>```bash
>sudo cp -r /etc/kubernetes ~/k8s-backup/
>```
> `/etc/kubernetes` 디렉토리 전체를 `~/k8s-backup/`에 복사





설정파일(*.conf), Static pod manifests(/manifests), 인증서(/pki) 삭제


> 💻 명령어
>```bash
>sudo rm -rf /etc/kubernetes/*
>```





인증서(/pki) 재생성
```bash
$ sudo kubeadm init phase certs all
[certs] Using certificateDir folder "/etc/kubernetes/pki"
[certs] Generating "ca" certificate and key
[certs] Generating "apiserver" certificate and key
[certs] apiserver serving cert is signed for DNS names [k8s-pi1 kubernetes kubernetes.default kubernetes.default.svc kubernetes.default.svc.cluster.local] and IPs [10.96.0.1 192.168.0.98]
[certs] Generating "apiserver-kubelet-client" certificate and key
[certs] Generating "front-proxy-ca" certificate and key
[certs] Generating "front-proxy-client" certificate and key
[certs] Generating "etcd/ca" certificate and key
[certs] Generating "etcd/server" certificate and key
[certs] etcd/server serving cert is signed for DNS names [k8s-pi1 localhost] and IPs [192.168.0.98 127.0.0.1 ::1]
[certs] Generating "etcd/peer" certificate and key
[certs] etcd/peer serving cert is signed for DNS names [k8s-pi1 localhost] and IPs [192.168.0.98 127.0.0.1 ::1]
[certs] Generating "etcd/healthcheck-client" certificate and key
[certs] Generating "apiserver-etcd-client" certificate and key
[certs] Generating "sa" key and public key

$ tree /etc/kubernetes/pki
/etc/kubernetes/pki
├── apiserver-etcd-client.crt
├── apiserver-etcd-client.key
├── apiserver-kubelet-client.crt
├── apiserver-kubelet-client.key
├── apiserver.crt
├── apiserver.key
├── ca.crt
├── ca.key
├── etcd
│   ├── ca.crt
│   ├── ca.key
│   ├── healthcheck-client.crt
│   ├── healthcheck-client.key
│   ├── peer.crt
│   ├── peer.key
│   ├── server.crt
│   └── server.key
├── front-proxy-ca.crt
├── front-proxy-ca.key
├── front-proxy-client.crt
├── front-proxy-client.key
├── sa.key
└── sa.pub

2 directories, 22 files
```

> 💻 명령어
>```bash
>sudo kubeadm init phase certs all
>```





설정파일(*.conf) 재생성
```bash
$ sudo kubeadm init phase kubeconfig all
[kubeconfig] Using kubeconfig folder "/etc/kubernetes"
[kubeconfig] Writing "admin.conf" kubeconfig file
[kubeconfig] Writing "super-admin.conf" kubeconfig file
[kubeconfig] Writing "kubelet.conf" kubeconfig file
[kubeconfig] Writing "controller-manager.conf" kubeconfig file
[kubeconfig] Writing "scheduler.conf" kubeconfig file

$ tree -L 1 /etc/kubernetes
/etc/kubernetes
├── admin.conf
├── controller-manager.conf
├── kubelet.conf
├── pki
├── scheduler.conf
└── super-admin.conf

2 directories, 5 files
```

> 💻 명령어
>```bash
>sudo kubeadm init phase kubeconfig all
>```






Static pod manifests(/manifests) 재생성
```bash
$ sudo kubeadm init phase control-plane all
[control-plane] Using manifest folder "/etc/kubernetes/manifests"
[control-plane] Creating static Pod manifest for "kube-apiserver"
[control-plane] Creating static Pod manifest for "kube-controller-manager"
[control-plane] Creating static Pod manifest for "kube-scheduler"
$ sudo kubeadm init phase etcd local
[etcd] Creating static Pod manifest for local etcd in "/etc/kubernetes/manifests"

$ sudo tree /etc/kubernetes/manifests
/etc/kubernetes/manifests
├── etcd.yaml
├── kube-apiserver.yaml
├── kube-controller-manager.yaml
└── kube-scheduler.yaml

1 directory, 4 files
```

> 💻 명령어
>```bash
>sudo kubeadm init phase control-plane all
>```
>```bash
>sudo kubeadm init phase etcd local
>```








kubelet 재시작


> 💻 명령어
>```bash
>sudo systemctl restart kubelet
>```




configmap










> 관련 문서 : [kubeadm Init workflow](https://kubernetes.io/docs/reference/setup-tools/kubeadm/kubeadm-init/#init-workflow)  
> 관련 문서 : [Certificate Management with kubeadm](https://kubernetes.io/docs/tasks/administer-cluster/kubeadm/kubeadm-certs/)  
> 관련 문서 : [kubeadm certs](https://kubernetes.io/docs/reference/setup-tools/kubeadm/kubeadm-certs/)  
> 관련 문서 : [kubeadm init phase](https://kubernetes.io/docs/reference/setup-tools/kubeadm/kubeadm-init-phase/)
