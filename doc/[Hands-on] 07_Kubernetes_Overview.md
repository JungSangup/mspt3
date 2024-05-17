
# [Hands-on] 07. Kubernetes Overview

![](./img/hands_on.png)

<br>

# Contents

**[1. kubectl 기본 명령어 사용](#1-kubectl-기본-명령어-사용)**

---

## 1. kubectl 기본 명령어 사용

기본적인 쿠버네티스 명령어들에 대해 알아보겠습니다.  
제일 먼저 도움말을 볼까요?
```bash
ubuntu@ip-172-31-23-60:~$ kubectl --help
kubectl controls the Kubernetes cluster manager.

 Find more information at: https://kubernetes.io/docs/reference/kubectl/overview/

Basic Commands (Beginner):
  create        Create a resource from a file or from stdin
  expose        Take a replication controller, service, deployment or pod and expose it as a new Kubernetes service
  run           Run a particular image on the cluster
  set           Set specific features on objects

Basic Commands (Intermediate):
  explain       Get documentation for a resource
  get           Display one or many resources
  edit          Edit a resource on the server
  delete        Delete resources by file names, stdin, resources and names, or by resources and label selector

Deploy Commands:
  rollout       Manage the rollout of a resource
  scale         Set a new size for a deployment, replica set, or replication controller
  autoscale     Auto-scale a deployment, replica set, stateful set, or replication controller

Cluster Management Commands:
  certificate   Modify certificate resources.
  cluster-info  Display cluster information
  top           Display resource (CPU/memory) usage
  cordon        Mark node as unschedulable
  uncordon      Mark node as schedulable
  drain         Drain node in preparation for maintenance
  taint         Update the taints on one or more nodes

Troubleshooting and Debugging Commands:
  describe      Show details of a specific resource or group of resources
  logs          Print the logs for a container in a pod
  attach        Attach to a running container
  exec          Execute a command in a container
  port-forward  Forward one or more local ports to a pod
  proxy         Run a proxy to the Kubernetes API server
  cp            Copy files and directories to and from containers
  auth          Inspect authorization
  debug         Create debugging sessions for troubleshooting workloads and nodes

Advanced Commands:
  diff          Diff the live version against a would-be applied version
  apply         Apply a configuration to a resource by file name or stdin
  patch         Update fields of a resource
  replace       Replace a resource by file name or stdin
  wait          Experimental: Wait for a specific condition on one or many resources
  kustomize     Build a kustomization target from a directory or URL.

Settings Commands:
  label         Update the labels on a resource
  annotate      Update the annotations on a resource
  completion    Output shell completion code for the specified shell (bash, zsh or fish)

Other Commands:
  alpha         Commands for features in alpha
  api-resources Print the supported API resources on the server
  api-versions  Print the supported API versions on the server, in the form of "group/version"
  config        Modify kubeconfig files
  plugin        Provides utilities for interacting with plugins
  version       Print the client and server version information

Usage:
  kubectl [flags] [options]

Use "kubectl <command> --help" for more information about a given command.
Use "kubectl options" for a list of global command-line options (applies to all commands).
```

> 💻 명령어
>```bash
>kubectl --help
>```

여러가지 명령어들을 볼 수 있고, 사용법을 알 수 있습니다.

<br><br><br>

몇 가지 명령어들을 알아볼까요?

버젼을 알아보려면,
```bash
ubuntu@ip-172-31-23-60:~$ kubectl version
Client Version: version.Info{Major:"1", Minor:"23", GitVersion:"v1.23.0", GitCommit:"ab69524f795c42094a6630298ff53f3c3ebab7f4", GitTreeState:"clean", BuildDate:"2021-12-07T18:16:20Z", GoVersion:"go1.17.3", Compiler:"gc", Platform:"linux/amd64"}
Server Version: version.Info{Major:"1", Minor:"23", GitVersion:"v1.23.0", GitCommit:"ab69524f795c42094a6630298ff53f3c3ebab7f4", GitTreeState:"clean", BuildDate:"2021-12-07T18:09:57Z", GoVersion:"go1.17.3", Compiler:"gc", Platform:"linux/amd64"}
ubuntu@ip-172-31-23-60:~$ kubectl version --output yaml
clientVersion:
  buildDate: "2021-12-07T18:16:20Z"
  compiler: gc
  gitCommit: ab69524f795c42094a6630298ff53f3c3ebab7f4
  gitTreeState: clean
  gitVersion: v1.23.0
  goVersion: go1.17.3
  major: "1"
  minor: "23"
  platform: linux/amd64
serverVersion:
  buildDate: "2021-12-07T18:09:57Z"
  compiler: gc
  gitCommit: ab69524f795c42094a6630298ff53f3c3ebab7f4
  gitTreeState: clean
  gitVersion: v1.23.0
  goVersion: go1.17.3
  major: "1"
  minor: "23"
  platform: linux/amd64
```

> 💻 명령어
>```bash
>kubectl version
>```
>또는
>```bash
>kubectl version --output yaml
>```

<br><br><br>

쿠버네티스 클러스터 정보를 확인하려면,
```bash
ubuntu@ip-172-31-23-60:~$ kubectl cluster-info
Kubernetes control plane is running at https://172.31.23.60:8443
CoreDNS is running at https://172.31.23.60:8443/api/v1/namespaces/kube-system/services/kube-dns:dns/proxy

To further debug and diagnose cluster problems, use 'kubectl cluster-info dump'.
```

> 💻 명령어
>```bash
>kubectl cluster-info
>```

<br><br><br>

우리 클러스터의 노드 목록은 아래 명령어로 알아볼 수 있습니다.
```bash
ubuntu@ip-172-31-23-60:~$ kubectl get nodes
NAME              STATUS   ROLES                  AGE   VERSION
ip-172-31-23-60   Ready    control-plane,master   2d    v1.23.0
```

> 💻 명령어
>```bash
>kubectl get nodes
>```

<br><br><br>

`--output wide`옵션(또는, `-o wide`)을 사용하면 더 많은 정보를 보여줍니다.
```bash
ubuntu@ip-172-31-23-60:~$ kubectl get nodes --output wide
NAME              STATUS   ROLES                  AGE   VERSION   INTERNAL-IP    EXTERNAL-IP   OS-IMAGE             KERNEL-VERSION    CONTAINER-RUNTIME
ip-172-31-23-60   Ready    control-plane,master   2d    v1.23.0   172.31.23.60   <none>        Ubuntu 20.04.5 LTS   5.15.0-1028-aws   docker://20.10.23
```

> 💻 명령어
>```bash
>kubectl get nodes --output wide
>```

<br><br><br>

이제 우리 클러스터에 존재한는 리소스를 조회하는 방법을 알아볼게요.  

먼저 현재 존재하는 **POD**목록은 아래와 같이 조회합니다.
```bash
ubuntu@ip-172-31-23-60:~$ kubectl get pods
No resources found in default namespace.
```

> 💻 명령어
>```bash
>kubectl get pods
>```

<br><br><br>

음. 아무것도 없군요...  
지금은 **default 네임스페이스**에서 조회를 한 경우입니다.

`--namespace`로 네임스페이스를 지정하지 않으면 **default 네임스페이스**를 기본으로 합니다.

다른 네임스페이스는 뭐가 있을까요?  
네임스페이스를 보려면 아래 명령어를 사용하면 됩니다.
```bash
ubuntu@ip-172-31-23-60:~$ kubectl get namespaces
NAME              STATUS   AGE
default           Active   2d
ingress-nginx     Active   2d
kube-node-lease   Active   2d
kube-public       Active   2d
kube-system       Active   2d
metallb-system    Active   2d
```

> 💻 명령어
>```bash
>kubectl get namespaces
>```

<br><br><br>

이번에는 Pod목록을 조회하는데, `--all-namespaces`옵션을 추가해볼까요?
```bash
ubuntu@ip-172-31-23-60:~$ kubectl get pods --all-namespaces --output wide
NAMESPACE        NAME                                        READY   STATUS      RESTARTS   AGE   IP             NODE              NOMINATED NODE   READINESS GATES
ingress-nginx    ingress-nginx-admission-create-q4cpv        0/1     Completed   0          2d    172.17.0.2     ip-172-31-23-60   <none>           <none>
ingress-nginx    ingress-nginx-admission-patch-9q2vm         0/1     Completed   0          2d    172.17.0.3     ip-172-31-23-60   <none>           <none>
ingress-nginx    ingress-nginx-controller-56f5896d55-l87j8   1/1     Running     0          2d    172.17.0.7     ip-172-31-23-60   <none>           <none>
kube-system      coredns-64897985d-dpf6h                     1/1     Running     0          2d    172.17.0.4     ip-172-31-23-60   <none>           <none>
kube-system      etcd-ip-172-31-23-60                        1/1     Running     2          2d    172.31.23.60   ip-172-31-23-60   <none>           <none>
kube-system      kube-apiserver-ip-172-31-23-60              1/1     Running     2          2d    172.31.23.60   ip-172-31-23-60   <none>           <none>
kube-system      kube-controller-manager-ip-172-31-23-60     1/1     Running     2          2d    172.31.23.60   ip-172-31-23-60   <none>           <none>
kube-system      kube-proxy-8rjz2                            1/1     Running     0          2d    172.31.23.60   ip-172-31-23-60   <none>           <none>
kube-system      kube-scheduler-ip-172-31-23-60              1/1     Running     2          2d    172.31.23.60   ip-172-31-23-60   <none>           <none>
kube-system      metrics-server-7c57dd9c77-j9866             1/1     Running     0          2d    172.17.0.6     ip-172-31-23-60   <none>           <none>
kube-system      storage-provisioner                         1/1     Running     0          2d    172.31.23.60   ip-172-31-23-60   <none>           <none>
metallb-system   controller-5cd84968b6-bfp98                 1/1     Running     0          2d    172.17.0.5     ip-172-31-23-60   <none>           <none>
metallb-system   speaker-z6phb                               1/1     Running     0          2d    172.31.23.60   ip-172-31-23-60   <none>           <none>
```

> 💻 명령어
>```bash
>kubectl get pods --all-namespaces --output wide
>```

<br><br><br>

시스템이 사용하는 Pod들을 보려면 **kube-system** 네임스페이스를 보면 됩니다.
```bash
ubuntu@ip-172-31-23-60:~$ kubectl get pods --namespace kube-system --output wide
NAME                                      READY   STATUS    RESTARTS   AGE   IP             NODE              NOMINATED NODE   READINESS GATES
coredns-64897985d-dpf6h                   1/1     Running   0          2d    172.17.0.4     ip-172-31-23-60   <none>           <none>
etcd-ip-172-31-23-60                      1/1     Running   2          2d    172.31.23.60   ip-172-31-23-60   <none>           <none>
kube-apiserver-ip-172-31-23-60            1/1     Running   2          2d    172.31.23.60   ip-172-31-23-60   <none>           <none>
kube-controller-manager-ip-172-31-23-60   1/1     Running   2          2d    172.31.23.60   ip-172-31-23-60   <none>           <none>
kube-proxy-8rjz2                          1/1     Running   0          2d    172.31.23.60   ip-172-31-23-60   <none>           <none>
kube-scheduler-ip-172-31-23-60            1/1     Running   2          2d    172.31.23.60   ip-172-31-23-60   <none>           <none>
metrics-server-7c57dd9c77-j9866           1/1     Running   0          2d    172.17.0.6     ip-172-31-23-60   <none>           <none>
storage-provisioner                       1/1     Running   0          2d    172.31.23.60   ip-172-31-23-60   <none>           <none>
```

> 💻 명령어
>```bash
>kubectl get pods --namespace kube-system --output wide
>```

<br><br><br>

그 중에 하나, **kube-scheduler**를 좀 더 자세히 볼까요?
정보를 yaml형태로 볼 수 도 있구요. ( `--output yaml` 옵션을 사용 )
```bash
ubuntu@ip-172-31-23-60:~$ kubectl get pod kube-scheduler-ip-172-31-23-60 --namespace kube-system --output yaml
apiVersion: v1
kind: Pod
metadata:
  annotations:
    kubernetes.io/config.hash: e192e0a2bd6cfd4396155e893051c21a
    kubernetes.io/config.mirror: e192e0a2bd6cfd4396155e893051c21a
    kubernetes.io/config.seen: "2023-02-11T14:35:09.119073050Z"
    kubernetes.io/config.source: file
    seccomp.security.alpha.kubernetes.io/pod: runtime/default
  creationTimestamp: "2023-02-11T14:35:09Z"
  labels:
    component: kube-scheduler
    tier: control-plane
  name: kube-scheduler-ip-172-31-23-60
  namespace: kube-system
  ownerReferences:
  - apiVersion: v1
    controller: true
    kind: Node
    name: ip-172-31-23-60
    uid: 92e2bd7a-497f-42b3-a4c4-6f253531a0ab
  resourceVersion: "365"
  uid: d0a91b00-d0fd-4d2e-9fea-e548279544be
spec:
  containers:
  - command:
    - kube-scheduler
    - --authentication-kubeconfig=/etc/kubernetes/scheduler.conf
    - --authorization-kubeconfig=/etc/kubernetes/scheduler.conf
    - --bind-address=127.0.0.1
    - --kubeconfig=/etc/kubernetes/scheduler.conf
    - --leader-elect=false
    image: k8s.gcr.io/kube-scheduler:v1.23.0
    imagePullPolicy: IfNotPresent
...생략...
```

> 💻 명령어
>```bash
>kubectl get pod [KUBE-SCHEDULER-NAME] --namespace kube-system --output yaml
>```
>[KUBE-SCHEDULER-NAME] 에는 앞에서 조회한 결과 중 Kube-scheculer의 이름을 적어줍니다.

<br><br><br>

`kubectl describe` 명령으로 오브젝트의 자세한 정보를 조회할 수도 있습니다.
```bash
ubuntu@ip-172-31-23-60:~$ kubectl describe pod kube-scheduler-ip-172-31-23-60 --namespace kube-system
Name:                 kube-scheduler-ip-172-31-23-60
Namespace:            kube-system
Priority:             2000001000
Priority Class Name:  system-node-critical
Node:                 ip-172-31-23-60/172.31.23.60
Start Time:           Sat, 11 Feb 2023 14:35:09 +0000
Labels:               component=kube-scheduler
                      tier=control-plane
Annotations:          kubernetes.io/config.hash: e192e0a2bd6cfd4396155e893051c21a
                      kubernetes.io/config.mirror: e192e0a2bd6cfd4396155e893051c21a
                      kubernetes.io/config.seen: 2023-02-11T14:35:09.119073050Z
                      kubernetes.io/config.source: file
                      seccomp.security.alpha.kubernetes.io/pod: runtime/default
Status:               Running
IP:                   172.31.23.60
IPs:
  IP:           172.31.23.60
Controlled By:  Node/ip-172-31-23-60
Containers:
  kube-scheduler:
    Container ID:  docker://d3c593d7742adedba586e1bbf0d0553363f3b93dd527ab92d53cc467326a226a
    Image:         k8s.gcr.io/kube-scheduler:v1.23.0
    Image ID:      docker-pullable://k8s.gcr.io/kube-scheduler@sha256:af8166ce28baa7cb902a2c0d16da865d5d7c892fe1b41187fd4be78ec6291c23
    ... 생략 ...
```

> 💻 명령어
>```bash
>kubectl describe pod [KUBE-SCHEDULER-NAME] --namespace kube-system
>```
>[KUBE-SCHEDULER-NAME] 에는 앞에서 조회한 결과 중 Kube-scheculer의 이름(e.g. kube-scheduler-ip-172-31-23-60)을 적어줍니다.

<br><br><br>

Pod의 로그를 보려면 아래와 같이 하시면 됩니다.
```bash
ubuntu@ip-172-31-23-60:~$ kubectl logs -n kube-system kube-scheduler-ip-172-31-23-60
I0211 14:35:03.396821       1 serving.go:348] Generated self-signed cert in-memory
W0211 14:35:05.622247       1 requestheader_controller.go:193] Unable to get configmap/extension-apiserver-authentication in kube-system.  Usually fixed by 'kubectl create rolebinding -n kube-system ROLEBINDING_NAME --role=extension-apiserver-authentication-reader --serviceaccount=YOUR_NS:YOUR_SA'
W0211 14:35:05.622351       1 authentication.go:345] Error looking up in-cluster authentication configuration: configmaps "extension-apiserver-authentication" is forbidden: User "system:kube-scheduler" cannot get resource "configmaps" in API group "" in the namespace "kube-system"
W0211 14:35:05.622419       1 authentication.go:346] Continuing without authentication configuration. This may treat all requests as anonymous.
W0211 14:35:05.622446       1 authentication.go:347] To require authentication configuration lookup to succeed, set --authentication-tolerate-lookup-failure=false
I0211 14:35:05.680550       1 server.go:139] "Starting Kubernetes Scheduler" version="v1.23.0"
I0211 14:35:05.683072       1 secure_serving.go:200] Serving securely on 127.0.0.1:10259
I0211 14:35:05.683418       1 configmap_cafile_content.go:201] "Starting controller" name="client-ca::kube-system::extension-apiserver-authentication::client-ca-file"
I0211 14:35:05.683564       1 shared_informer.go:240] Waiting for caches to sync for client-ca::kube-system::extension-apiserver-authentication::client-ca-file
I0211 14:35:05.683681       1 tlsconfig.go:240] "Starting DynamicServingCertificateController"

... 생략 ...
```

> 💻 명령어
>```bash
>kubectl logs -n kube-system [KUBE-SCHEDULER-NAME]
>```
>[KUBE-SCHEDULER-NAME] 에는 앞에서 조회한 결과 중 Kube-scheculer의 이름(e.g. kube-scheduler-ip-172-31-23-60)을 적어줍니다.

<br>

여기까지, 기본적인 kubectl 명령어들을 알아보았습니다. 더 많은 내용은 차차 알아볼게요~ ٩(ˊᗜˋ*)و


<br><br><br>

---

<br><br><br>

## 💿 Self Study


우리가 사용한 [kubectl](https://kubernetes.io/ko/docs/reference/kubectl/) 은 [Kubernetes API](https://kubernetes.io/docs/reference/kubernetes-api/)를 이용해서 컨트롤플레인과 통신하는데 사용되는 명령줄 도구(Command line tool) 입니다.

API가 있다는 것은, kubectl이 아닌 다른 방법으로도 API로 요청을 보내고 응답을 받을 수 있다는 것입니다.  
예를들면, HTTP GET이나 POST 같은 것 말입니다.  

그럼, 이 부분을 한 번 자세히 들여다볼까요?  

<br>

앞에서 우리는 이런 명령어를 실행해봤습니다.  
클러스터에 존재하는 노드의 목록을 조회하는 것이었죠.

```bash
ubuntu@ip-172-31-23-60:~$ kubectl get nodes
NAME              STATUS   ROLES                  AGE   VERSION
ip-172-31-23-60   Ready    control-plane,master   2d    v1.23.0
```

> 💻 명령어
>```bash
>kubectl get nodes
>```

이걸, 다른 방법으로 (kubectl 을 사용하지 않고) 해볼게요.

<br><br><br>

먼저 kubectl 에 사용된 설정정보를 볼게요.  
기본 위치는 사용자 home(~ 또는 $HOME) 아래 `.kube/config` 입니다.
```bash
ubuntu@ip-172-31-23-60:~$ cat ~/.kube/config
apiVersion: v1
clusters:
- cluster:
    certificate-authority: /home/ubuntu/.minikube/ca.crt
    extensions:
    - extension:
        last-update: Mon, 27 Mar 2023 13:08:03 UTC
        provider: minikube.sigs.k8s.io
        version: v1.28.0
      name: cluster_info
    server: https://172.31.29.188:8443
  name: minikube
contexts:
- context:
    cluster: minikube
    extensions:
    - extension:
        last-update: Mon, 27 Mar 2023 13:08:03 UTC
        provider: minikube.sigs.k8s.io
        version: v1.28.0
      name: context_info
    namespace: default
    user: minikube
  name: minikube
current-context: minikube
kind: Config
preferences: {}
users:
- name: minikube
  user:
    client-certificate: /home/ubuntu/.minikube/profiles/minikube/client.crt
    client-key: /home/ubuntu/.minikube/profiles/minikube/client.key
```

> 💻 명령어
>```bash
>cat ~/.kube/config
>```

이 부분을 잘 봐주세요.
- clusters.cluster.server: https://172.31.29.188:8443
- clusters.cluster.certificate-authority: /home/ubuntu/.minikube/ca.crt
- users.user.client-certificate: /home/ubuntu/.minikube/profiles/minikube/client.crt
- users.user.client-key: /home/ubuntu/.minikube/profiles/minikube/client.key

첫 번째 server가 api server의 주소이고, 아래 세 개는 api server로 요청을 보낼 때 사용되는 인증서와 키 파일 입니다.

<br><br><br>

다음은 node object에 대한 정보를 알아볼게요.
```bash
ubuntu@ip-172-31-23-60:~$ kubectl explain node
KIND:     Node
VERSION:  v1

DESCRIPTION:
     Node is a worker node in Kubernetes. Each node will have a unique
     identifier in the cache (i.e. in etcd).

FIELDS:
   apiVersion	<string>
     APIVersion defines the versioned schema of this representation of an
     object. Servers should convert recognized schemas to the latest internal
     value, and may reject unrecognized values. More info:
     https://git.k8s.io/community/contributors/devel/sig-architecture/api-conventions.md#resources

   kind	<string>
     Kind is a string value representing the REST resource this object
     represents. Servers may infer this from the endpoint the client submits
     requests to. Cannot be updated. In CamelCase. More info:
     https://git.k8s.io/community/contributors/devel/sig-architecture/api-conventions.md#types-kinds

   metadata	<Object>
     Standard object's metadata. More info:
     https://git.k8s.io/community/contributors/devel/sig-architecture/api-conventions.md#metadata

   spec	<Object>
     Spec defines the behavior of a node.
     https://git.k8s.io/community/contributors/devel/sig-architecture/api-conventions.md#spec-and-status

   status	<Object>
     Most recently observed status of the node. Populated by the system.
     Read-only. More info:
     https://git.k8s.io/community/contributors/devel/sig-architecture/api-conventions.md#spec-and-status
```

> 💻 명령어
>```bash
>kubectl explain node
>```

node 의 API Version은 v1 입니다.  
[Node](https://kubernetes.io/docs/reference/kubernetes-api/cluster-resources/node-v1/)의 정보도 참고하세요.


<br><br><br>

자, 이제 우리가 API를 통해서 node의 정보를 알아보기 위해서 필요한 사항들은 모두 준비가 됐습니다.  
정리해보자면,

- Node의 API Version : v1
- clusters.cluster.server: https://172.31.29.188:8443
- clusters.cluster.certificate-authority: ~/.minikube/ca.crt
- users.user.client-certificate: ~/.minikube/profiles/minikube/client.crt
- users.user.client-key: ~/.minikube/profiles/minikube/client.key

그럼, 위의 정보를 조합해서 curl 명령을 실행해볼까요?

```bash
ubuntu@ip-172-31-23-60:~$ curl https://172.31.29.188:8443/api/v1/nodes --cacert /home/ubuntu/.minikube/ca.crt --cert /home/ubuntu/.minikube/profiles/minikube/client.crt --key /home/ubuntu/.minikube/profiles/minikube/client.key
{
  "kind": "NodeList",
  "apiVersion": "v1",
  "metadata": {
    "resourceVersion": "2624875"
  },
  "items": [
    {
      "metadata": {
        "name": "ip-172-31-29-188",
        "uid": "0ca16cf4-b220-441c-9570-bc2367298e4d",
        "resourceVersion": "2624695",
        "creationTimestamp": "2023-03-15T06:04:09Z",
        "labels": {
          "beta.kubernetes.io/arch": "amd64",
          "beta.kubernetes.io/os": "linux",
          "kubernetes.io/arch": "amd64",
          "kubernetes.io/hostname": "ip-172-31-29-188",
          "kubernetes.io/os": "linux",
          "minikube.k8s.io/commit": "986b1ebd987211ed16f8cc10aed7d2c42fc8392f",
          "minikube.k8s.io/name": "minikube",
          "minikube.k8s.io/primary": "true",
          "minikube.k8s.io/updated_at": "2023_03_15T06_04_12_0700",
          "minikube.k8s.io/version": "v1.28.0",
          "node-role.kubernetes.io/control-plane": "",
          "node-role.kubernetes.io/master": "",
          "node.kubernetes.io/exclude-from-external-load-balancers": ""
        },
        "annotations": {
          "kubeadm.alpha.kubernetes.io/cri-socket": "/var/run/dockershim.sock",
          "node.alpha.kubernetes.io/ttl": "0",
          "volumes.kubernetes.io/controller-managed-attach-detach": "true"
        },
        "managedFields": [
          {
            "manager": "Go-http-client",
            "operation": "Update",
            "apiVersion": "v1",
            "time": "2023-03-15T06:04:11Z",
            "fieldsType": "FieldsV1",
            "fieldsV1": {
              "f:metadata": {
                "f:annotations": {
                  ".": {},
                  "f:kubeadm.alpha.kubernetes.io/cri-socket": {},
                  "f:volumes.kubernetes.io/controller-managed-attach-detach": {}
                },
                "f:labels": {
                  ".": {},
                  "f:beta.kubernetes.io/arch": {},
                  "f:beta.kubernetes.io/os": {},
                  "f:kubernetes.io/arch": {},
                  "f:kubernetes.io/hostname": {},
                  "f:kubernetes.io/os": {},
                  "f:node-role.kubernetes.io/control-plane": {},
                  "f:node-role.kubernetes.io/master": {},
                  "f:node.kubernetes.io/exclude-from-external-load-balancers": {}
                }
              }
            }
          },
          {
            "manager": "kubectl-label",
            "operation": "Update",
            "apiVersion": "v1",
            "time": "2023-03-15T06:04:13Z",
            "fieldsType": "FieldsV1",
            "fieldsV1": {
              "f:metadata": {
                "f:labels": {
                  "f:minikube.k8s.io/commit": {},
                  "f:minikube.k8s.io/name": {},
                  "f:minikube.k8s.io/primary": {},
                  "f:minikube.k8s.io/updated_at": {},
                  "f:minikube.k8s.io/version": {}
                }
              }
            }
          },
          {
            "manager": "kube-controller-manager",
            "operation": "Update",
            "apiVersion": "v1",
            "time": "2023-03-15T06:04:25Z",
            "fieldsType": "FieldsV1",
            "fieldsV1": {
              "f:metadata": {
                "f:annotations": {
                  "f:node.alpha.kubernetes.io/ttl": {}
                }
              },
              "f:spec": {
                "f:podCIDR": {},
                "f:podCIDRs": {
                  ".": {},
                  "v:\"10.244.0.0/24\"": {}
                }
              }
            }
          },
          {
            "manager": "Go-http-client",
            "operation": "Update",
            "apiVersion": "v1",
            "time": "2023-03-15T06:04:43Z",
            "fieldsType": "FieldsV1",
            "fieldsV1": {
              "f:status": {
                "f:conditions": {
                  "k:{\"type\":\"DiskPressure\"}": {
                    "f:lastHeartbeatTime": {}
                  },
                  "k:{\"type\":\"MemoryPressure\"}": {
                    "f:lastHeartbeatTime": {}
                  },
                  "k:{\"type\":\"PIDPressure\"}": {
                    "f:lastHeartbeatTime": {}
                  },
                  "k:{\"type\":\"Ready\"}": {
                    "f:lastHeartbeatTime": {},
                    "f:lastTransitionTime": {},
                    "f:message": {},
                    "f:reason": {},
                    "f:status": {}
                  }
                },
                "f:images": {}
              }
            },
            "subresource": "status"
          }
        ]
      },
      "spec": {
        "podCIDR": "10.244.0.0/24",
        "podCIDRs": [
          "10.244.0.0/24"
        ]
      },
      "status": {
        "capacity": {
          "cpu": "2",
          "ephemeral-storage": "20134592Ki",
          "hugepages-1Gi": "0",
          "hugepages-2Mi": "0",
          "memory": "3956716Ki",
          "pods": "110"
        },
        "allocatable": {
          "cpu": "2",
          "ephemeral-storage": "20134592Ki",
          "hugepages-1Gi": "0",
          "hugepages-2Mi": "0",
          "memory": "3956716Ki",
          "pods": "110"
        },
        "conditions": [
          {
            "type": "MemoryPressure",
            "status": "False",
            "lastHeartbeatTime": "2023-04-20T08:09:11Z",
            "lastTransitionTime": "2023-03-15T06:04:08Z",
            "reason": "KubeletHasSufficientMemory",
            "message": "kubelet has sufficient memory available"
          },
          {
            "type": "DiskPressure",
            "status": "False",
            "lastHeartbeatTime": "2023-04-20T08:09:11Z",
            "lastTransitionTime": "2023-03-15T06:04:08Z",
            "reason": "KubeletHasNoDiskPressure",
            "message": "kubelet has no disk pressure"
          },
          {
            "type": "PIDPressure",
            "status": "False",
            "lastHeartbeatTime": "2023-04-20T08:09:11Z",
            "lastTransitionTime": "2023-03-15T06:04:08Z",
            "reason": "KubeletHasSufficientPID",
            "message": "kubelet has sufficient PID available"
          },
          {
            "type": "Ready",
            "status": "True",
            "lastHeartbeatTime": "2023-04-20T08:09:11Z",
            "lastTransitionTime": "2023-03-15T06:04:22Z",
            "reason": "KubeletReady",
            "message": "kubelet is posting ready status. AppArmor enabled"
          }
        ],
        "addresses": [
          {
            "type": "InternalIP",
            "address": "172.31.29.188"
          },
          {
            "type": "Hostname",
            "address": "ip-172-31-29-188"
          }
        ],
        "daemonEndpoints": {
          "kubeletEndpoint": {
            "Port": 10250
          }
        },
        "nodeInfo": {
          "machineID": "ec2f2cd4f7f51d90a9f7d9865f2f3c72",
          "systemUUID": "ec2f2cd4-f7f5-1d90-a9f7-d9865f2f3c72",
          "bootID": "4b314966-61e3-431d-8acc-d5d026cad0a9",
          "kernelVersion": "5.15.0-1028-aws",
          "osImage": "Ubuntu 20.04.5 LTS",
          "containerRuntimeVersion": "docker://20.10.23",
          "kubeletVersion": "v1.23.0",
          "kubeProxyVersion": "v1.23.0",
          "operatingSystem": "linux",
          "architecture": "amd64"
        },
        "images": [
          {
            "names": [
              "k8s.gcr.io/etcd@sha256:64b9ea357325d5db9f8a723dcf503b5a449177b17ac87d69481e126bb724c263",
              "k8s.gcr.io/etcd:3.5.1-0"
            ],
            "sizeBytes": 292558922
          },
          {
            "names": [
              "k8s.gcr.io/ingress-nginx/controller@sha256:5516d103a9c2ecc4f026efbd4b40662ce22dc1f824fb129ed121460aaa5c47f8"
            ],
            "sizeBytes": 289315470
          },
          {
            "names": [
              "rogallo/todo-app:1.0.0"
            ],
            "sizeBytes": 172325131
          },
          {
            "names": [
              "nginx@sha256:63b44e8ddb83d5dd8020327c1f40436e37a6fffd3ef2498a6204df23be6e7e94",
              "nginx:latest"
            ],
            "sizeBytes": 142151046
          },
          {
            "names": [
              "k8s.gcr.io/kube-apiserver@sha256:d10db42c2353539ce15006854edfb6707ba6025f282d59d962729ed3b6039004",
              "k8s.gcr.io/kube-apiserver:v1.23.0"
            ],
            "sizeBytes": 135154064
          },
          {
            "names": [
              "nginx@sha256:e90ac5331fe095cea01b121a3627174b2e33e06e83720e9a934c7b8ccc9c55a0",
              "nginx:1.18"
            ],
            "sizeBytes": 132899597
          },
          {
            "names": [
              "nginx@sha256:ed7f815851b5299f616220a63edac69a4cc200e7f536a56e421988da82e44ed8",
              "nginx:1.19.3"
            ],
            "sizeBytes": 132861270
          },
          {
            "names": [
              "k8s.gcr.io/kube-controller-manager@sha256:0bfbb13e5e9cec329523b6f654687af8ce058adbc90b42e5af7a929ac22e2a53",
              "k8s.gcr.io/kube-controller-manager:v1.23.0"
            ],
            "sizeBytes": 124963795
          },
          {
            "names": [
              "k8s.gcr.io/kube-proxy@sha256:2e8292d30042bb75f745d2a90d0fc4fbc3a3b1bdbe5b9d3bf50dd866c62b2ba7",
              "k8s.gcr.io/kube-proxy:v1.23.0"
            ],
            "sizeBytes": 112319647
          },
          {
            "names": [
              "node@sha256:dc98dac24efd4254f75976c40bce46944697a110d06ce7fa47e7268470cf2e28",
              "node:10-alpine"
            ],
            "sizeBytes": 82736775
          },
          {
            "names": [
              "ubuntu@sha256:67211c14fa74f070d27cc59d69a7fa9aeff8e28ea118ef3babc295a0428a6d21",
              "ubuntu:latest"
            ],
            "sizeBytes": 77810806
          },
          {
            "names": [
              "ubuntu@sha256:24a0df437301598d1a4b62ddf59fa0ed2969150d70d748c84225e6501e9c36b9",
              "ubuntu:20.04"
            ],
            "sizeBytes": 72785566
          },
          {
            "names": [
              "k8s.gcr.io/metrics-server/metrics-server@sha256:5ddc6458eb95f5c70bd13fdab90cbd7d6ad1066e5b528ad1dcb28b76c5fb2f00"
            ],
            "sizeBytes": 68793965
          },
          {
            "names": [
              "ubuntu@sha256:8aa9c2798215f99544d1ce7439ea9c3a6dfd82de607da1cec3a8a2fae005931b",
              "ubuntu:18.04"
            ],
            "sizeBytes": 63146040
          },
          {
            "names": [
              "k8s.gcr.io/kube-scheduler@sha256:af8166ce28baa7cb902a2c0d16da865d5d7c892fe1b41187fd4be78ec6291c23",
              "k8s.gcr.io/kube-scheduler:v1.23.0"
            ],
            "sizeBytes": 53476049
          },
          {
            "names": [
              "k8s.gcr.io/coredns/coredns@sha256:5b6ec0d6de9baaf3e92d0f66cd96a25b9edbce8716f5f15dcd1a616b3abd590e",
              "k8s.gcr.io/coredns/coredns:v1.8.6"
            ],
            "sizeBytes": 46829283
          },
          {
            "names": [
              "gcr.io/k8s-minikube/storage-provisioner@sha256:18eb69d1418e854ad5a19e399310e52808a8321e4c441c1dddad8977a0d7a944",
              "gcr.io/k8s-minikube/storage-provisioner:v5"
            ],
            "sizeBytes": 31465472
          },
          {
            "names": [
              "busybox@sha256:b5d6fe0712636ceb7430189de28819e195e8966372edfc2d9409d79402a0dc16",
              "busybox:latest"
            ],
            "sizeBytes": 4863138
          },
          {
            "names": [
              "k8s.gcr.io/pause@sha256:3d380ca8864549e74af4b29c10f9cb0956236dfb01c40ca076fb6c37253234db",
              "k8s.gcr.io/pause:3.6"
            ],
            "sizeBytes": 682696
          }
        ]
      }
    }
  ]
}
```

> 💻 명령어 ( AWS Track )
>```bash
>curl [SERVER]/api/v1/nodes --cacert ~/.minikube/ca.crt --cert ~/.minikube/profiles/minikube/client.crt --key ~/.minikube/profiles/minikube/client.key
>```
> [SERVER]에는 앞에서 조회한 결과 중 여러분 환경의 서버정보를 넣어주세요.

<br>

위의 결과과 아래 명령어의 결과를 한 번 비교도 해보세요.

> 💻 명령어
>```bash
>kubectl get nodes --output json
>```

<br>

node 의 name이나 InternalIP등의 정보들을 확인할 수 있습니다.  

<br><br><br>

간단한 조회 API만 확인해봤지만, 이 외에도 생성/수정/삭제 동작들이 모두 API를 통해서 실행됩니다.  
User뿐만 아니라, Kubernetes의 각 컴포넌트들이나 API로 접근 가능한 다른 시스템들도 동일하게 이 API들을 사용하고 있습니다.

이번 실습은 여기까지 입니다.  
API에 대해 더 자세히 공부해보고 싶으시면 아래 링크들을 참고하세요.

- [API 개요](https://kubernetes.io/ko/docs/reference/using-api/)  
- [API 접근 제어](https://kubernetes.io/ko/docs/reference/access-authn-authz/)  
- [Kubernetes API](https://kubernetes.io/docs/reference/kubernetes-api/)
