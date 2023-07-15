
# [Hands-on] 06. Environment setup - Kubernetes

![](./img/hands_on.png)

<br>

# Contents

**[1. Minikube 설치하기](#1-minikube-설치하기)**  
**[2. Kubectl 설치하기](#2-kubectl-설치하기--리눅스에-kubectl-설치-및-설정-)**  
**[3. Helm 설치하기](#3-helm-설치하기--헬름-설치하기-)**  

---

<br>

## 1. Minikube 설치하기
Kubernetes 실습을 위해서 **K8s cluster**를 구성합니다.  
K8s cluster는 다양한 방법으로 구성할 수 있지만, 우리 실습과정은 **단일노드 cluster**인 [Minikube](https://minikube.sigs.k8s.io/)를 이용합니다.

우리가 앞에서 사용한 VM Instance는 Minikube의 [설치조건](https://minikube.sigs.k8s.io/docs/start/#what-youll-need)을 만족하도록 구성되어 있습니다.

minikube 설치파일을 다운로드 하고 설치를 진행해볼까요?
```bash
ubuntu@ip-172-31-23-60:~$ curl -LO https://github.com/kubernetes/minikube/releases/download/v1.28.0/minikube-linux-amd64
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
  0     0    0     0    0     0      0      0 --:--:-- --:--:-- --:--:--     0
100 73.1M  100 73.1M    0     0  92.3M      0 --:--:-- --:--:-- --:--:--  141M
ubuntu@ip-172-31-23-60:~$ sudo install minikube-linux-amd64 /usr/local/bin/minikube
```

> 💻 명령어
>```bash
>curl -LO https://github.com/kubernetes/minikube/releases/download/v1.28.0/minikube-linux-amd64
>sudo install minikube-linux-amd64 /usr/local/bin/minikube
>
>```

<br><br><br>

이제 minikube CLI를 이용해서 minikube cluster를 시작합니다.
```bash
ubuntu@ip-172-31-23-60:~$ minikube start --driver=none --kubernetes-version=v1.23.0 --addons=ingress,metrics-server
😄  minikube v1.28.0 on Ubuntu 20.04
✨  Using the none driver based on user configuration
👍  Starting control plane node minikube in cluster minikube
🤹  Running on localhost (CPUs=2, Memory=3863MB, Disk=19662MB) ...
🎉  minikube 1.29.0 is available! Download it: https://github.com/kubernetes/minikube/releases/tag/v1.29.0
💡  To disable this notice, run: 'minikube config set WantUpdateNotification false'

ℹ️  OS release is Ubuntu 20.04.5 LTS
🐳  Preparing Kubernetes v1.23.0 on Docker 20.10.23 ...
    ▪ kubelet.resolv-conf=/run/systemd/resolve/resolv.conf
    > kubelet.sha256:  64 B / 64 B [-------------------------] 100.00% ? p/s 0s
    > kubeadm.sha256:  64 B / 64 B [-------------------------] 100.00% ? p/s 0s
    > kubectl.sha256:  64 B / 64 B [-------------------------] 100.00% ? p/s 0s
    > kubeadm:  43.11 MiB / 43.11 MiB [----------] 100.00% 281.72 MiB p/s 400ms
    > kubectl:  44.42 MiB / 44.42 MiB [----------] 100.00% 470.78 MiB p/s 300ms
    > kubelet:  118.73 MiB / 118.73 MiB [---------] 100.00% 142.07 MiB p/s 1.0s
    ▪ Generating certificates and keys ...
    ▪ Booting up control plane ...
    ▪ Configuring RBAC rules ...
🤹  Configuring local host environment ...

❗  The 'none' driver is designed for experts who need to integrate with an existing VM
💡  Most users should use the newer 'docker' driver instead, which does not require root!
📘  For more information, see: https://minikube.sigs.k8s.io/docs/reference/drivers/none/

❗  kubectl and minikube configuration will be stored in /home/ubuntu
❗  To use kubectl or minikube commands as your own user, you may need to relocate them. For example, to overwrite your own settings, run:

    ▪ sudo mv /home/ubuntu/.kube /home/ubuntu/.minikube $HOME
    ▪ sudo chown -R $USER $HOME/.kube $HOME/.minikube

💡  This can also be done automatically by setting the env var CHANGE_MINIKUBE_NONE_USER=true
🔎  Verifying Kubernetes components...
    ▪ Using image k8s.gcr.io/ingress-nginx/kube-webhook-certgen:v1.1.1
    ▪ Using image k8s.gcr.io/ingress-nginx/kube-webhook-certgen:v1.1.1
    ▪ Using image k8s.gcr.io/ingress-nginx/controller:v1.2.1
    ▪ Using image docker.io/metallb/speaker:v0.9.6
    ▪ Using image gcr.io/k8s-minikube/storage-provisioner:v5
    ▪ Using image k8s.gcr.io/metrics-server/metrics-server:v0.6.1
    ▪ Using image docker.io/metallb/controller:v0.9.6
🔎  Verifying ingress addon...
🌟  Enabled addons: default-storageclass, storage-provisioner, metrics-server, metallb, ingress
💡  kubectl not found. If you need it, try: 'minikube kubectl -- get pods -A'
🏄  Done! kubectl is now configured to use "minikube" cluster and "default" namespace by default
```

> 💻 명령어
>```bash
>minikube start --driver=none --kubernetes-version=v1.23.0 --addons=ingress,metrics-server
>```

<br>

문제없이 시작된 경우 다음과 같이 표시됩니다.

```bash
ubuntu@ip-172-31-23-60:~$ minikube status
minikube
type: Control Plane
host: Running
kubelet: Running
apiserver: Running
kubeconfig: Configured
```

> 💻 명령어
>```bash
>minikube status
>```

<br>

<details><summary>❗minikube start 중 conntrack 관련 에러가 발생하면</summary>
<p>

아래 명령어를 실행해서 conntrack 패키지를 설치 한 다음 다시 앞의 `minikube start ...` 명령어를 실행해주세요.

> 💻 명령어
>```bash
>sudo apt-get update
>sudo apt-get install conntrack
>
>```

</p>
</details>

<br><br><br><br><br>

## 2. Kubectl 설치하기 ( [리눅스에 kubectl 설치 및 설정](https://kubernetes.io/ko/docs/tasks/tools/install-kubectl-linux/) )  
Kubernetes command-line tool인 [kubectl](https://kubernetes.io/docs/tasks/tools/#kubectl)을 다운로드 하고 설치합니다.
```bash
ubuntu@ip-172-31-23-60:~$ curl -LO https://dl.k8s.io/release/v1.23.0/bin/linux/amd64/kubectl
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100   138  100   138    0     0   3066      0 --:--:-- --:--:-- --:--:--  3066
100 44.4M  100 44.4M    0     0   141M      0 --:--:-- --:--:-- --:--:--  141M
ubuntu@ip-172-31-23-60:~$ sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl
```

> 💻 명령어
>```bash
>curl -LO https://dl.k8s.io/release/v1.23.0/bin/linux/amd64/kubectl
>sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl
>
> ```

<br><br><br>

아래와 같이 표시되면 정상입니다.
```bash
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
>kubectl version --output yaml
>```

<br><br><br>

편리한 사용을 위해서 [리눅스에서 bash 자동 완성 사용하기](https://kubernetes.io/ko/docs/tasks/tools/included/optional-kubectl-configs-bash-linux/) 을 진행합니다. (명령어 자동완성, alias 적용)  
필수사항은 아니니 필요없는 경우 생략해도 됩니다.
```bash
ubuntu@ip-172-31-23-60:~$ echo 'source <(kubectl completion bash)' >>~/.bashrc
ubuntu@ip-172-31-23-60:~$ echo 'alias k=kubectl' >>~/.bashrc
ubuntu@ip-172-31-23-60:~$ echo 'complete -o default -F __start_kubectl k' >>~/.bashrc
ubuntu@ip-172-31-23-60:~$ exec bash
```

> 💻 명령어
>```bash
>echo 'source <(kubectl completion bash)' >>~/.bashrc
>echo 'alias k=kubectl' >>~/.bashrc
>echo 'complete -o default -F __start_kubectl k' >>~/.bashrc
>exec bash
>
>```

<br><br><br>

이제 명령어 작성 중 `TAB`키를 눌러 자동완성을 사용하거나, `kubectl` 대신 Alias인 `k`를 사용할 수 있습니다.
```bash
ubuntu@ip-172-31-23-60:~$ k version
Client Version: version.Info{Major:"1", Minor:"23", GitVersion:"v1.23.0", GitCommit:"ab69524f795c42094a6630298ff53f3c3ebab7f4", GitTreeState:"clean", BuildDate:"2021-12-07T18:16:20Z", GoVersion:"go1.17.3", Compiler:"gc", Platform:"linux/amd64"}
Server Version: version.Info{Major:"1", Minor:"23", GitVersion:"v1.23.0", GitCommit:"ab69524f795c42094a6630298ff53f3c3ebab7f4", GitTreeState:"clean", BuildDate:"2021-12-07T18:09:57Z", GoVersion:"go1.17.3", Compiler:"gc", Platform:"linux/amd64"}
```

> 💻 명령어
>```bash
>k version
>```

<br><br><br><br><br>

## 3. Helm 설치하기 ( [헬름 설치하기](https://helm.sh/ko/docs/intro/install/) )

Kubernetes 패키지 매니저인 Helm을 설치합니다.

```bash
ubuntu@ip-172-31-23-60:~$ curl -LO https://get.helm.sh/helm-v3.11.0-linux-amd64.tar.gz
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100 14.3M  100 14.3M    0     0   106M      0 --:--:-- --:--:-- --:--:--  106M
ubuntu@ip-172-31-23-60:~$ tar -zxvf helm-v3.11.0-linux-amd64.tar.gz
linux-amd64/
linux-amd64/helm
linux-amd64/LICENSE
linux-amd64/README.md
ubuntu@ip-172-31-23-60:~$ sudo mv linux-amd64/helm /usr/local/bin/helm
```

> 💻 명령어
>```bash
>curl -LO https://get.helm.sh/helm-v3.11.0-linux-amd64.tar.gz
>tar -zxvf helm-v3.11.0-linux-amd64.tar.gz
>sudo mv linux-amd64/helm /usr/local/bin/helm
>
>```

<br><br><br>

설치 후 아래와 같이 확인합니다.
```bash
ubuntu@ip-172-31-23-60:~$ helm version
version.BuildInfo{Version:"v3.11.0", GitCommit:"472c5736ab01133de504a826bd9ee12cbe4e7904", GitTreeState:"clean", GoVersion:"go1.18.10"}
```

> 💻 명령어
>```bash
>helm version
>```

<br><br><br>

여기까지 하시면 Kubernetes와 Helm을 공부할 준비는 됐습니다.  ˘◡˘
