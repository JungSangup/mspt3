## [Appendix] Kubernetes 클러스터 업데이트 ( with kubeadm )


다음 경우의 업데이트 절차입니다.

1. Kubernetes 버젼 업데이트
2. Controlplane/Worker node의 IP Address 변경
3. K8s 컴포넌트 인증서 갱신


---

### 1. Kubernetes 버젼 업데이트


작성 예정...


---

### 2. Controlplane/Worker node의 IP Address 변경

K8s 노드 (controlplane, node) 의 IP 가 변경된 경우 다음과 같은 조치가 필요합니다.  

1. (CP) 기존 설정 백업
2. (CP) PKI 인증서(Certificates) 업데이트
3. (CP) 설정파일 (kubeconfig) 업데이트
4. (CP) Control-plane 컴포넌트 (Static pod) 업데이트
5. (CP) Configmap 업데이트
6. (CP) Kube-proxy 
7. (CP) bootstrap token 업데이트
8. (WK) kubeadm reset
9. (WK) kubeadm join
>(CP) : Control-plane node에서 할 작업, (WK) : (Worker) node 에서 할 작업



#### 1. (CP) 기존 설정 백업
변경 전 설정이 적용되어 있는 파일들을 모두 백업합니다.  
- 인증서 ( 위치 : /etc/kubernetes/pki )
- 설정파일 ( 위치 : /etc/kubernetes)
- Control-plane 컴포넌트 (Static pod) manifests ( 위치 : /etc/kubernetes/manifests)

```bash
$ sudo cp -r /etc/kubernetes ~/k8s-backup/
$ tree ~/k8s-backup/kubernetes
/home/ubuntu/k8s-backup/kubernetes
├── admin.conf
├── controller-manager.conf
├── kubelet.conf
├── manifests
│   ├── etcd.yaml
│   ├── kube-apiserver.yaml
│   ├── kube-controller-manager.yaml
│   └── kube-scheduler.yaml
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

3 directories, 31 files
```

> 💻 명령어
>```bash
>sudo cp -r /etc/kubernetes ~/k8s-backup/
>```
> `/etc/kubernetes` 디렉토리 전체를 `~/k8s-backup/`에 복사, `tree`명령어로 확인한 결과는 위와 유사함.



#### 2. (CP) PKI 인증서(Certificates) 업데이트
`/etc/kubernetes/pki` 경로에 모든 pki 인증서와 키파일이 저장되어 있습니다.  
이 중 다음 인증서를 다시 생성해야 합니다.  
- kube-apiserver 인증서/키 ( /etc/kubernetes/pki/apiserver.* )
- etcd peer 인증서/키 ( /etc/kubernetes/pki/etcd/peer.* )
- etcd server 인증서/키 ( /etc/kubernetes/pki/etcd/server.* )

> 참조 : 인증서 SAN에 Host_IP가 있는 것들이 대상임.  [PKI certificates and requirements - All certificates](https://kubernetes.io/docs/setup/best-practices/certificates/#all-certificates) 참조  

순서는 기존 인증서/키 삭제 후 [kubeadm init phase certs](https://kubernetes.io/docs/reference/setup-tools/kubeadm/kubeadm-init-phase/#cmd-phase-certs)명령어를 이용하여 다시 생성합니다.

먼저 삭제는 다음과 같이 합니다.
```bash
$ sudo rm -rf /etc/kubernetes/pki/apiserver.* /etc/kubernetes/pki/etcd/peer.* /etc/kubernetes/pki/etcd/server.*
$ tree /etc/kubernetes/pki/
/etc/kubernetes/pki/
├── apiserver-etcd-client.crt
├── apiserver-etcd-client.key
├── apiserver-kubelet-client.crt
├── apiserver-kubelet-client.key
├── ca.crt
├── ca.key
├── etcd
│   ├── ca.crt
│   ├── ca.key
│   ├── healthcheck-client.crt
│   └── healthcheck-client.key
├── front-proxy-ca.crt
├── front-proxy-ca.key
├── front-proxy-client.crt
├── front-proxy-client.key
├── sa.key
└── sa.pub

1 directory, 16 files
```
> kube-apiserver, etcd/peer, etcd/server 만 삭제하고, 나머지는 그대로 사용합니다.

그리고, 아래와 같이 다시 생성합니다.
```bash
$ sudo kubeadm init phase certs all
[certs] Using certificateDir folder "/etc/kubernetes/pki"
[certs] Using existing ca certificate authority
[certs] Generating "apiserver" certificate and key
[certs] apiserver serving cert is signed for DNS names [k8s-pi1 kubernetes kubernetes.default kubernetes.default.svc kubernetes.default.svc.cluster.local] and IPs [10.96.0.1 192.168.0.18]
[certs] Using existing apiserver-kubelet-client certificate and key on disk
[certs] Using existing front-proxy-ca certificate authority
[certs] Using existing front-proxy-client certificate and key on disk
[certs] Using existing etcd/ca certificate authority
[certs] Generating "etcd/server" certificate and key
[certs] etcd/server serving cert is signed for DNS names [k8s-pi1 localhost] and IPs [192.168.0.18 127.0.0.1 ::1]
[certs] Generating "etcd/peer" certificate and key
[certs] etcd/peer serving cert is signed for DNS names [k8s-pi1 localhost] and IPs [192.168.0.18 127.0.0.1 ::1]
[certs] Using existing etcd/healthcheck-client certificate and key on disk
[certs] Using existing apiserver-etcd-client certificate and key on disk
[certs] Using the existing "sa" key
```
> 삭제된 인증서/키만 재생성됩니다. 이 때 변경된 정보(IP Address)가 반영되어 생성됩니다.

> 💻 명령어
>```bash
>sudo rm -rf /etc/kubernetes/pki/apiserver.* /etc/kubernetes/pki/etcd/peer.* /etc/kubernetes/pki/etcd/server.*
>```
>```bash
>sudo kubeadm init phase certs all
>```

> [참고] PKI 인증서 확인방법
> 아래와 같이 인증서(.crt)의 내용을 확인할 수 있음. ( openssl x509 -text -in OOO.crt )
> X509v3 Subject Alternative Name 를 보면 적용된 IP Address 확인 가능
>```
>$ openssl x509 -text -in /etc/kubernetes/pki/apiserver.crt
>Certificate:
>    Data:
>        Version: 3 (0x2)
>        Serial Number: 6604927811259478652 (0x5ba96ad99538327c)
>        Signature Algorithm: sha256WithRSAEncryption
>        Issuer: CN = kubernetes
>        Validity
>            Not Before: Mar 10 12:42:34 2024 GMT
>            Not After : Mar 11 08:51:08 2025 GMT
>        Subject: CN = kube-apiserver
>        Subject Public Key Info:
>            Public Key Algorithm: rsaEncryption
>                Public-Key: (2048 bit)
>                Modulus:
>                    00:ba:07:b0:12:b2:0e:37:ee:b1:1a:5c:a0:cd:23:
>                    06:63:ef:02:48:aa:2b:f9:4b:18:13:50:ef:b9:d0:
>                    76:b9:ca:f8:ad:c6:4e:73:89:29:8b:96:dc:ee:26:
>                    6e:90:e8:15:95:8b:4c:f3:6b:25:44:90:d6:17:29:
>                    48:37:e3:26:9e:e3:b9:c1:24:1b:49:b7:f0:a3:e2:
>                    2f:12:fb:6d:48:34:da:8d:c4:e0:5b:0a:d3:de:c1:
>                    59:16:2a:06:4a:00:14:85:1e:ef:e8:d6:e6:5c:27:
>                    2a:97:d0:5a:f4:16:33:28:92:43:eb:d7:81:47:da:
>                    e8:08:b4:67:fb:59:09:55:1f:59:b0:1e:b1:ba:fa:
>                    7c:56:f9:df:20:e6:ec:95:48:c6:44:ba:18:f1:23:
>                    a8:dd:89:0d:3e:96:75:67:11:f2:5d:26:97:c3:f2:
>                    4d:43:c4:d4:60:01:d7:ea:03:76:53:f6:4c:ca:61:
>                    3c:c6:38:e2:66:61:6c:8e:65:49:42:49:55:e8:5f:
>                    92:59:21:19:d7:b5:a0:83:9b:69:2d:a5:51:5e:d8:
>                    60:91:61:a8:b1:75:33:45:05:10:25:d9:48:94:8f:
>                    08:fd:41:5e:ed:fd:c5:d1:c8:8e:73:1c:86:7c:af:
>                    1c:36:ea:ea:5c:b7:ac:44:7f:81:14:b7:f2:5f:ad:
>                    93:d1
>                Exponent: 65537 (0x10001)
>        X509v3 extensions:
>            X509v3 Key Usage: critical
>                Digital Signature, Key Encipherment
>            X509v3 Extended Key Usage:
>                TLS Web Server Authentication
>            X509v3 Basic Constraints: critical
>                CA:FALSE
>            X509v3 Authority Key Identifier:
>                14:48:8D:96:82:13:A1:DD:C8:97:02:58:86:B5:39:CA:A1:19:F0:22
>            X509v3 Subject Alternative Name:
>                DNS:k8s-pi1, DNS:kubernetes, DNS:kubernetes.default, DNS:kubernetes.default.svc, DNS:kubernetes.>default.svc.cluster.local, IP Address:10.96.0.1, IP Address:192.168.0.18
>    Signature Algorithm: sha256WithRSAEncryption
>    Signature Value:
>        1c:55:9e:43:bf:9c:cc:78:53:23:8c:6e:e8:d8:d0:f3:09:61:
>        e5:49:b1:10:ec:74:e9:09:f8:21:b5:61:91:85:9d:1f:ce:79:
>        9c:2a:54:c7:7d:fe:1f:15:cf:2a:2d:f6:31:f4:14:82:7f:a0:
>        33:b6:33:61:9e:f2:0b:b9:a9:9f:32:d9:b9:8b:92:c4:4c:69:
>        d9:b9:bf:44:15:4a:b9:f0:55:7d:82:98:f9:d3:c1:3c:8c:70:
>        3b:ab:cb:d1:cc:8f:42:cc:e0:32:b6:b2:43:11:bf:ab:e5:11:
>        4b:8b:d6:95:66:9b:fe:ba:0b:49:38:72:cd:f3:b9:bd:2f:20:
>        f5:12:d7:46:9c:e4:07:7d:e2:4b:d7:16:b4:a7:6a:b5:d2:2e:
>        38:a0:9f:8b:a4:7e:7b:4c:33:e3:61:81:94:92:4d:14:8b:bf:
>        74:7e:13:7e:80:d6:25:08:b6:78:82:57:38:75:2c:65:98:b7:
>        1a:bd:f9:06:0c:21:c4:39:05:5a:13:7c:e6:de:1c:bd:bf:70:
>        74:ff:03:49:54:96:fc:be:11:73:54:50:a3:34:86:9f:db:3a:
>        23:34:d3:42:8d:9b:e2:77:10:0e:28:07:97:8e:2e:cb:3c:59:
>        cd:58:21:e0:fb:51:48:52:fb:d3:07:88:93:ef:c1:32:78:4a:
>        84:4c:a8:26
>-----BEGIN CERTIFICATE-----
>MIIDhjCCAm6gAwIBAgIIW6lq2ZU4MnwwDQYJKoZIhvcNAQELBQAwFTETMBEGA1UE
>AxMKa3ViZXJuZXRlczAeFw0yNDAzMTAxMjQyMzRaFw0yNTAzMTEwODUxMDhaMBkx
>FzAVBgNVBAMTDmt1YmUtYXBpc2VydmVyMIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8A
>MIIBCgKCAQEAugewErION+6xGlygzSMGY+8CSKor+UsYE1DvudB2ucr4rcZOc4kp
>i5bc7iZukOgVlYtM82slRJDWFylIN+MmnuO5wSQbSbfwo+IvEvttSDTajcTgWwrT
>3sFZFioGSgAUhR7v6NbmXCcql9Ba9BYzKJJD69eBR9roCLRn+1kJVR9ZsB6xuvp8
>VvnfIObslUjGRLoY8SOo3YkNPpZ1ZxHyXSaXw/JNQ8TUYAHX6gN2U/ZMymE8xjji
>ZmFsjmVJQklV6F+SWSEZ17Wgg5tpLaVRXthgkWGosXUzRQUQJdlIlI8I/UFe7f3F
>0ciOcxyGfK8cNurqXLesRH+BFLfyX62T0QIDAQABo4HVMIHSMA4GA1UdDwEB/wQE
>AwIFoDATBgNVHSUEDDAKBggrBgEFBQcDATAMBgNVHRMBAf8EAjAAMB8GA1UdIwQY
>MBaAFBRIjZaCE6HdyJcCWIa1OcqhGfAiMHwGA1UdEQR1MHOCB2s4cy1waTGCCmt1
>YmVybmV0ZXOCEmt1YmVybmV0ZXMuZGVmYXVsdIIWa3ViZXJuZXRlcy5kZWZhdWx0
>LnN2Y4Ika3ViZXJuZXRlcy5kZWZhdWx0LnN2Yy5jbHVzdGVyLmxvY2FshwQKYAAB
>hwTAqAASMA0GCSqGSIb3DQEBCwUAA4IBAQAcVZ5Dv5zMeFMjjG7o2NDzCWHlSbEQ
>7HTpCfghtWGRhZ0fznmcKlTHff4fFc8qLfYx9BSCf6AztjNhnvILuamfMtm5i5LE
>TGnZub9EFUq58FV9gpj508E8jHA7q8vRzI9CzOAytrJDEb+r5RFLi9aVZpv+ugtJ
>OHLN87m9LyD1EtdGnOQHfeJL1xa0p2q10i44oJ+LpH57TDPjYYGUkk0Ui790fhN+
>gNYlCLZ4glc4dSxlmLcavfkGDCHEOQVaE3zm3hy9v3B0/wNJVJb8vhFzVFCjNIaf
>2zojNNNCjZvidxAOKAeXji7LPFnNWCHg+1FIUvvTB4iT78EyeEqETKgm
>-----END CERTIFICATE-----
>```


#### 3. (CP) 설정파일 (kubeconfig) 업데이트
kubeconfig 파일들(/etc/kubernetes/*.conf)을 업데이트 합니다.

삭제는
```bash
$ sudo rm -rf /etc/kubernetes/*.conf
```

생성은
```bash
$ sudo kubeadm init phase kubeconfig all
[kubeconfig] Using kubeconfig folder "/etc/kubernetes"
[kubeconfig] Writing "admin.conf" kubeconfig file
[kubeconfig] Writing "super-admin.conf" kubeconfig file
[kubeconfig] Writing "kubelet.conf" kubeconfig file
[kubeconfig] Writing "controller-manager.conf" kubeconfig file
[kubeconfig] Writing "scheduler.conf" kubeconfig file
```

> 💻 명령어
>```bash
>sudo rm -rf /etc/kubernetes/*.conf
>```
>```bash
>sudo kubeadm init phase kubeconfig all
>```

> 생성결과는 아래와 같이 확인할 수 있습니다.
>```bash
>$ sudo grep "192.168.0." /etc/kubernetes/*.conf
>/etc/kubernetes/admin.conf:    server: https://192.168.0.18:6443
>/etc/kubernetes/controller-manager.conf:    server: https://192.168.0.18:6443
>/etc/kubernetes/kubelet.conf:    server: https://192.168.0.18:6443
>/etc/kubernetes/scheduler.conf:    server: https://192.168.0.18:6443
>/etc/kubernetes/super-admin.conf:    server: https://192.168.0.18:6443
>```
> 각 파일에 변경된 IP Address가 제대로 반영되어 있는지 확인합니다.  
> server: 는 API Server의 IP:Port 입니다.


admin.conf는 `kubectl`에서 사용되므로, 필요한 곳으로 복사합니다. (아래 예시 참조)
```bash
$ sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
cp: overwrite '/home/ubuntu/.kube/config'? y
$ sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

> 💻 명령어
>```bash
>sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
>```
>```bash
>sudo chown $(id -u):$(id -g) $HOME/.kube/config
>```

> 아직까지는 kube-apiserver를 업데이트 하지 않았기 때문에 kubectl 을 사용할 수 없습니다.


#### 4. (CP) Control-plane 컴포넌트 (Static pod) 업데이트
다음 Control-plane 컴포넌트 (Static pod)를 업데이트 합니다.
- etcd
- kube-apiserver

> [Static Pods](https://kubernetes.io/docs/concepts/workloads/pods/#static-pods)는 kubelet에 의해 관리되므로, 특정 경로의 manifest 파일을 업데이트 하면 Pod가 업데이트 됨.

먼저 etcd와 kube-apiserver manifests파일을 삭제합니다.
```bash
$ sudo rm /etc/kubernetes/manifests/etcd.yaml /etc/kubernetes/manifests/kube-apiserver.yaml
$ sudo tree /etc/kubernetes/manifests/
/etc/kubernetes/manifests/
├── kube-controller-manager.yaml
└── kube-scheduler.yaml

0 directories, 2 files
```

> 💻 명령어
>```bash
>sudo rm /etc/kubernetes/manifests/etcd.yaml /etc/kubernetes/manifests/kube-apiserver.yaml
>```

다음은 새로운 manifests 파일을 생성합니다.
```bash
$ sudo kubeadm init phase etcd local
[etcd] Creating static Pod manifest for local etcd in "/etc/kubernetes/manifests"
$ sudo kubeadm init phase control-plane apiserver
[control-plane] Creating static Pod manifest for "kube-apiserver"

$ $ sudo ls -al /etc/kubernetes/manifests/
total 24
drwxrwxr-x 2 root root 4096 Mar 11 18:11 .
drwxrwxr-x 4 root root 4096 Mar 11 17:58 ..
-rw-r--r-- 1 root root    0 Feb 14 20:33 .kubelet-keep
-rw------- 1 root root 2396 Mar 11 18:11 etcd.yaml
-rw------- 1 root root 3882 Mar 11 18:11 kube-apiserver.yaml
-rw------- 1 root root 3393 Mar 11 15:15 kube-controller-manager.yaml
-rw------- 1 root root 1463 Mar 11 15:15 kube-scheduler.yaml
```
> 결과는 위와 같이 네 개의 manifest 파일이 있어야 합니다. (etcd와 kube-apiserver는 신규)

> 💻 명령어
>```bash
>sudo kubeadm init phase etcd local
>```
>```bash
>sudo kubeadm init phase control-plane apiserver
>```

그리고, kubelet 서비스도 재시작 합니다.
```bash
$ sudo systemctl daemon-reload
sudo systemctl restart kubelet
$
```

> 💻 명령어
>```bash
>sudo systemctl daemon-reload
>sudo systemctl restart kubelet
>```

이제 kube-apiserver를 재시작했기 때문에, 아래와 같이 kubectl을 사용할 수 있습니다.
```bash
$ kubectl cluster-info
Kubernetes control plane is running at https://192.168.0.18:6443
CoreDNS is running at https://192.168.0.18:6443/api/v1/namespaces/kube-system/services/kube-dns:dns/proxy

To further debug and diagnose cluster problems, use 'kubectl cluster-info dump'.
```



#### 5. (CP) Configmap 업데이트
ConfigMap 중에 기존 정보를 담고있는 것들이 있습니다.  
이 ConfigMap들을 업데이트 합니다.
- cluster-info (kube-public 네임스페이스)의
  - data.kubeconfig.clusters.cluster.certificate-authority-data
  - data.kubeconfig.clusters.cluster.server
- kube-proxy (kube-system 네임스페이스)의
  - Config 의 Clusters.cluster.server

>CA Data와 Server 정보는 admin.conf나 .kube/config 를 참조합니다.


> 위 정보는 admin.conf 또는 .kube/config를 참조합니다.


ConfigMap 업데이트 방법은 여러가지가 있을 수 있습니다. 익숙한 방법을 사용하세요.  
- (방법 1) [kubectl edit
](https://kubernetes.io/docs/reference/kubectl/generated/kubectl_edit/) 명령어 이용  
- (방법 2) [kubectl get](https://kubernetes.io/docs/reference/kubectl/generated/kubectl_get/) 에 `--output yaml`옵션을 이용하여 manifest 파일 수정 후 반영


kube-proxy ConfigMap이 업데이트 되었기 때문에, kube-proxy pod를 재시작 해줍니다.  
kube-proxy-OOO pods는 kube-proxy daemonset에 의해 관리되므로, 단순히 pod를 삭제하면 다시 생성됩니다. (아래 예시 참조)
```bash
$ kubectl delete pods -n kube-system kube-proxy-5t9bf kube-proxy-fk4w7
pod "kube-proxy-5t9bf" deleted
pod "kube-proxy-fk4w7" deleted
```


이것도???
```bash
$ sudo kubeadm init phase addon all
[addons] Applied essential addon: CoreDNS
[addons] Applied essential addon: kube-proxy
```


#### 7. (CP) bootstrap token 업데이트
bootstrap token을 다시 생성합니다.


삭제
```bash
$ sudo kubeadm token list
TOKEN                     TTL         EXPIRES                USAGES                   DESCRIPTION                                                EXTRA GROUPS
hbd6l5.x7r5t57xve0l3d8h   20h         2024-03-12T06:04:19Z   authentication,signing   The default bootstrap token generated by 'kubeadm init'.   system:bootstrappers:kubeadm:default-node-token
$ sudo kubeadm token delete hbd6l5.x7r5t57xve0l3d8h
bootstrap token "hbd6l5" deleted
```

> 💻 명령어
>```bash
>sudo kubeadm token list
>```
>```bash
>sudo kubeadm token delete [TOKEN-NAME]
>```

생성
```bash
$ sudo kubeadm init phase bootstrap-token
[bootstrap-token] Using token: ztewih.ok4gnn98i134tdgw
[bootstrap-token] Configuring bootstrap tokens, cluster-info ConfigMap, RBAC Roles
[bootstrap-token] Configured RBAC rules to allow Node Bootstrap tokens to get nodes
[bootstrap-token] Configured RBAC rules to allow Node Bootstrap tokens to post CSRs in order for nodes to get long term certificate credentials
[bootstrap-token] Configured RBAC rules to allow the csrapprover controller automatically approve CSRs from a Node Bootstrap Token
[bootstrap-token] Configured RBAC rules to allow certificate rotation for all node client certificates in the cluster
[bootstrap-token] Creating the "cluster-info" ConfigMap in the "kube-public" namespace
```

> 💻 명령어
>```bash
>sudo kubeadm init phase bootstrap-token
>```



#### 8. (WK) kubeadm reset
(Worker) node를 초기화합니다.  
이 과정은 (Worker) node에서 실행합니다. 주의하세요.  
```bash
$ sudo kubeadm reset
W0311 18:51:17.623777    1743 preflight.go:56] [reset] WARNING: Changes made to this host by 'kubeadm init' or 'kubeadm join' will be reverted.
[reset] Are you sure you want to proceed? [y/N]: y
[preflight] Running pre-flight checks
W0311 18:51:18.569572    1743 removeetcdmember.go:106] [reset] No kubeadm config, using etcd pod spec to get data directory
[reset] Deleted contents of the etcd data directory: /var/lib/etcd
[reset] Stopping the kubelet service
[reset] Unmounting mounted directories in "/var/lib/kubelet"
[reset] Deleting contents of directories: [/etc/kubernetes/manifests /var/lib/kubelet /etc/kubernetes/pki]
[reset] Deleting files: [/etc/kubernetes/admin.conf /etc/kubernetes/super-admin.conf /etc/kubernetes/kubelet.conf /etc/kubernetes/bootstrap-kubelet.conf /etc/kubernetes/controller-manager.conf /etc/kubernetes/scheduler.conf]

The reset process does not clean CNI configuration. To do so, you must remove /etc/cni/net.d

The reset process does not reset or clean up iptables rules or IPVS tables.
If you wish to reset iptables, you must do so manually by using the "iptables" command.

If your cluster was setup to utilize IPVS, run ipvsadm --clear (or similar)
to reset your system's IPVS tables.

The reset process does not clean your kubeconfig files and you must remove them manually.
Please, check the contents of the $HOME/.kube/config file.
```

> 💻 명령어
>```bash
>sudo kubeadm reset
>```


#### 9. (WK) kubeadm join
(Worker) node를 K8s cluster에 join 시킵니다.  
이 절차는 K8s cluster 초기구성과 동일합니다.

다음 세 가지 정보 확인 후 `kubeadm join` 명령어를 실행합니다.

(1) api-server-endpoint
```bash
$ kubectl cluster-info
Kubernetes control plane is running at https://192.168.0.18:6443
CoreDNS is running at https://192.168.0.18:6443/api/v1/namespaces/kube-system/services/kube-dns:dns/proxy

To further debug and diagnose cluster problems, use 'kubectl cluster-info dump'.
```
> `192.168.0.18:6443` 이 API Server의 endpoint 입니다.

> 💻 명령어
>```bash
>kubectl cluster-info
>```


(2) token
```bash
$ kubeadm token list
TOKEN                     TTL         EXPIRES                USAGES                   DESCRIPTION                                                EXTRA GROUPS
ztewih.ok4gnn98i134tdgw   23h         2024-03-12T09:48:20Z   authentication,signing   The default bootstrap token generated by 'kubeadm init'.   system:bootstrappers:kubeadm:default-node-token
```
> `ztewih.ok4gnn98i134tdgw` 이 token name 입니다.

> 💻 명령어
>```bash
>kubeadm token list
>```


(3) discovery-token-ca-cert-hash
```bash
$ $ openssl x509 -pubkey -in /etc/kubernetes/pki/ca.crt | openssl rsa -pubin -outform der 2>/dev/null | openssl dgst -sha256 -hex | sed 's/^.* //'
9347df09927b...
```
> `9347df09927b...` 이 CA Cert 의 Hash 입니다.

> 💻 명령어
>```bash
>openssl x509 -pubkey -in /etc/kubernetes/pki/ca.crt | openssl rsa -pubin -outform der 2>/dev/null | openssl dgst -sha256 -hex | sed 's/^.* //'
>```


위의 정보를 조합하여 아래와 같이 `kubeadm join` 명령어를 실행합니다.  
```bash
$ sudo kubeadm join 172.31.30.145:6443 --token ev57z0.770ldkw... --discovery-token-ca-cert-hash sha256:3d98992e...

[preflight] Running pre-flight checks
[preflight] Reading configuration from the cluster...
[preflight] FYI: You can look at this config file with 'kubectl -n kube-system get cm kubeadm-config -o yaml'
[kubelet-start] Writing kubelet configuration to file "/var/lib/kubelet/config.yaml"
[kubelet-start] Writing kubelet environment file with flags to file "/var/lib/kubelet/kubeadm-flags.env"
[kubelet-start] Starting the kubelet
[kubelet-start] Waiting for the kubelet to perform the TLS Bootstrap...

This node has joined the cluster:
* Certificate signing request was sent to apiserver and a response was received.
* The Kubelet was informed of the new secure connection details.

Run 'kubectl get nodes' on the control-plane to see this node join the cluster.
```

> 💻 명령어
>```bash
>sudo kubeadm join [API-SERVER Endpoint] --token [TOKEN-NAME] --discovery-token-ca-cert-hash sha256:[CA-CERT Hash]
>```
> 주의 : `kubeadm join` 명령어는 Control-plane node가 아닌, (Worker) node 에서 실행합니다.






> 관련 문서 : [kubeadm Init workflow](https://kubernetes.io/docs/reference/setup-tools/kubeadm/kubeadm-init/#init-workflow)  
> 관련 문서 : [Certificate Management with kubeadm](https://kubernetes.io/docs/tasks/administer-cluster/kubeadm/kubeadm-certs/)  
> 관련 문서 : [kubeadm certs](https://kubernetes.io/docs/reference/setup-tools/kubeadm/kubeadm-certs/)  
> 관련 문서 : [kubeadm init phase](https://kubernetes.io/docs/reference/setup-tools/kubeadm/kubeadm-init-phase/)


---

### 3. K8s 컴포넌트 인증서 갱신


작성 중...



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
> 관련 문서 : [kubeadm을 사용한 인증서 관리 - 수동 인증서 갱신](https://kubernetes.io/ko/docs/tasks/administer-cluster/kubeadm/kubeadm-certs/#%EC%88%98%EB%8F%99-%EC%9D%B8%EC%A6%9D%EC%84%9C-%EA%B0%B1%EC%8B%A0)
