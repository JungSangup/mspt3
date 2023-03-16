# Docker & Kubernetes 노트 (MSP T2 6차)   ٩(ˊᗜˋ*)و

## Q&A

### Docker

- 도커 이미지에서 여러 OS가 사용되는데, 도커가 구동되는 OS와 충돌이나 호환성 이슈는 없나요?
  - 커널영역을 공유하고, 그 위에서 다양한 배포판들이 구동되기 때문에 서로의 영역이 다릅니다. ( [참고](https://bluese05.tistory.com/10) )
- 격리수준이 VM보다 떨어지면 실제 운영시 장애전파에 대한 대비책같은게 있을까요?
  - [Docker security](https://docs.docker.com/engine/security/) 참조
- EC2? 어제 생성한 것 사용 가능한가요? (어제는 Windows)
- 도커 올릴 수 있는 Host OS는 Linux만 가능한가요? 윈도우 컨테이너는?
  - [Windows and Containers](https://docs.microsoft.com/en-us/virtualization/windowscontainers/about/)
- 단일 컨테이너만 동작하는 경우 Host리소스를 최대로 사용하도록 설정 가능한가요?
  - [Runtime options with Memory, CPUs, and GPUs](https://docs.docker.com/config/containers/resource_constraints/#:~:text=By%20default%2C%20a%20container%20has,of%20the%20docker%20run%20command.) 참조
- 위와 같은 경우 한계점은?
- 컨테이너가 요청하는 총 리소스의 합이 하드웨어 총량을 초과하면?
- EC2에서 Command 'docker' not found 라고 나오면?

---

- 레이어드파일시스템이 nfs, xfs와 다른건가요?
- 컨테이너의 R/W, R/O(공유)레이어를 구분하는 기준은 뭔가요?
- nginx 이미지를 만들때 사용한 도커파일은 명령어가 6개 있어서 레이어가 6층인가요? (무한대면 무한태로 늘어나나요?)
- docker run 할 때 name은 임의로 생성되나요?
  - --name my-nginx 와 같이 지정 가능합니다. [Assign name and allocate pseudo-TTY (--name, -it)](https://docs.docker.com/engine/reference/commandline/run/#assign-name-and-allocate-pseudo-tty---name--it) 참조.
- 개발/운영할 때 레이어단위로 관리하거나 레이어별로 분석하거나 하는 경우도 있나요?
- R/O 레이어에서 필요없어진 파일을 다음 레이어에서 삭제하거나, 용량이 큰 이전버젼 레이어를 수정하는 경우에도 도커 이미지 안에는 모든 파일이 존재하는건가요?
  - [multi-line arguments , reducing clutter](https://github.com/JungSangup/mspt2/blob/main/doc/%5BBook%5D%2006_Dockerfile_BestPractice.md#multi-line-arguments--reducing-clutter) 에 사례가 있습니다. ("추가 패키지를 설치했다가 나중에 패키지를 제거할 수도 있지만 이러한 방법으로는 충분하지 않습니다. Dockerfile의 각 명령이 하나의 레이어를 생성하므로, 이미지를 만든 단계보다 나중의 단계에서 이미지의 데이터를 제거해도 전체 이미지의 크기는 줄어들지 않습니다. ***데이터가 더 깊은 레이어에 숨겨질 뿐 여전히 존재합니다.***")
- 도커는 결국 리눅스 기반의 앱만 쓸 수 있나요? 윈도우 앱은 어떻게???
- OS를 공유한다면 같은 OS기반의 애플리케이션만 하나의 도커에 올릴 수 있나요?
- 실습 마지막 커스텀 이미지 추가할 때(docker commit) 레이어가 하나 더 추가되는 이유는요?

---

- AWS EC2 Instance에 docker 설치과정 보여주세요.
- (다시질문) 같은 호스트에 리눅스 app과 윈도우 app이 올라갈 수 있나요?
- 교육 들으면서 이렇게 질문 많은적은... ㅋㅋ
- WAS등 각종 로그는 R/W레이어에 저장되나요? 사라지면 어케요?
- run, rm과 start, start차이는요?
- detach/attach, background/foreground 차이를 모르겠어요.
- docker ps -aq로 검색해서 모두 삭제하면 안되지 않나요?
- docker exec -it my-ubuntu bash 에서 bash를 빼면요?

---

- AWS Public subnet을 새로 생성해야 하나요?
- Connection timeout 조치??? (아마도 EC2에 SSH로 접속)
- docker ps -aq 질문 -> 실행중이지 않은 컨테이너도 삭제되는 것 같아 질문 드렸습니다.
- 설명을 듣다보니 컨테이너에 OS(e.g. Ubuntu)이야기가 나와서 VM과 구분이??? 공유되는 os커널은 도커를 실행시키기 위한것이고, 컨테이너에서 app이 실행되기 위해서 가벼운 os를 포함시켜야 하나요?
  - 리눅스 커널 외에 추가로 필요한 것들이 포함된 것을 Base image로 해서 나의 이미지를 만들면 됩니다. 이 때 가벼운 리눅스 배포판을 base로 하거나, jdk같은것을 사용할 수 있습니다.
- Play with Docker에서는 안되나요?
- tag와 name차이?
- detach/foregroud 차이? 
- docker hub 가입해야하나요?

---

- docker stop되어도 데이터가 사라지지 않는 것 아닌가요?
  - stop되어도 R/W Layer는 사라지지 않습니다. `docker rm`명령어를 써서 삭제할 때 데이터도 사라집니다.
- volume -> 설정 (e.g. NAS같은걸 사용할 때) 어떻게 하나요?
  - [Share data among machines](https://docs.docker.com/storage/volumes/#share-data-among-machines)
- volume 용량제한 가능한가요?
  - [Driver-specific options](https://docs.docker.com/engine/reference/commandline/volume_create/#driver-specific-options)
- 회사에서 -v로 그냥 nas를 붙였는데, 아마도 bind mount였나보네요. ㅎㅎ
- killercoda 에서 cmd delete안되게 가능한가요? 전에 실습한 내용이 사라져요...
- volume경로는 어떻게 지정하나요? 아니면 정해져있나요?
  - default 경로는 지정되어 있습니다. (/var/lib/docker/volumes/)
  - 경로를 변경하려면 dockerd 실행 시 옵션 (https://docs.docker.com/engine/reference/commandline/dockerd/ , --data-root) 으로 변경할 수 있습니다.
- Unable to find image 'yss:latest'... pull access denied for yss... repository does not exist or may require 'docker login'... 해결해주세요~
- --publish 3000:3000 앞은 호스트머신 포트인건 알겠는데, 뒤의 3000는 뭔가요?
- docker attach, ctrl+c , exit 동작
  - [docker attach](https://docs.docker.com/engine/reference/commandline/attach/)  -> ctrl+c는 SIGKILL , ctrl+p ctrl+q는 container를 running상태로 두고 detach
- volume이 지워지는 시점이 다로 있거나 정책같은게 있나요?
  - [Prune unused Docker objects](https://docs.docker.com/config/pruning/) 참조. 이걸 주기적으로 실행할 수도 있을 것 같습니다.
---

- bridge / host 차이 좀 다시 설명해주세요
- ifconfig에서 bridge를 어떻게 식별하나요?
- 교재에 hands-on 링크가 있나요?
- container 한 개가 두 개 이상 bridge에 연결 가능한가요?
  - [docker network connect](https://docs.docker.com/engine/reference/commandline/network_connect/) 명령어로 가능.
  - (1) bridge network 2개(my-bridge1, my-bridge2) 생성 > (2) my-bridge1에 연결된 my-nginx 생성 > (3) my-bridge2를 my-nginx에 연결
```bash
ubuntu $ docker network create my-bridge1
28068e76692ed1a0e01894253eb2f4ba59fc9b92c8c0c9f015069dd4ed92955d
ubuntu $ docker network create my-bridge2
bc40ca60e09aa0ad1389a56df646bc5f4972d615943b91409b70518cad898f85

ubuntu $ docker network ls
NETWORK ID     NAME         DRIVER    SCOPE
085e570ce889   bridge       bridge    local
a899d8446de2   host         host      local
28068e76692e   my-bridge1   bridge    local
bc40ca60e09a   my-bridge2   bridge    local
75c0cb25cf9e   none         null      local

ubuntu $ docker run -d -p 8080:80 --name my-nginx --network my-bridge1 nginx
097e83f590e2174903b474c71a05ce22278bb0b5a01dc1c4d892f375b8c2c34b

ubuntu $ docker network connect my-bridge2 my-nginx

ubuntu $ docker inspect my-nginx
[
...생략...
            "Networks": {
                "my-bridge1": {
                    "IPAMConfig": null,
                    "Links": null,
                    "Aliases": [
                        "097e83f590e2"
                    ],
                    "NetworkID": "28068e76692ed1a0e01894253eb2f4ba59fc9b92c8c0c9f015069dd4ed92955d",
                    "EndpointID": "c2039c93b10862c906e4403acecb1e37ec8f8ea63ca62320517cc077f5527399",
                    "Gateway": "172.18.0.1",
                    "IPAddress": "172.18.0.2",
                    "IPPrefixLen": 16,
                    "IPv6Gateway": "",
                    "GlobalIPv6Address": "",
                    "GlobalIPv6PrefixLen": 0,
                    "MacAddress": "02:42:ac:12:00:02",
                    "DriverOpts": null
                },
                "my-bridge2": {
                    "IPAMConfig": {},
                    "Links": null,
                    "Aliases": [
                        "097e83f590e2"
                    ],
                    "NetworkID": "bc40ca60e09aa0ad1389a56df646bc5f4972d615943b91409b70518cad898f85",
                    "EndpointID": "0c25934cad9efd24423833d16e9222f6d66d36f9072f5ac99f3994e5ffdb9ff7",
                    "Gateway": "172.19.0.1",
                    "IPAddress": "172.19.0.2",
                    "IPPrefixLen": 16,
                    "IPv6Gateway": "",
                    "GlobalIPv6Address": "",
                    "GlobalIPv6PrefixLen": 0,
                    "MacAddress": "02:42:ac:13:00:02",
                    "DriverOpts": {}
                }
            }
        }
    }
]
```
- volume/db 저장 어디 옵션으로 결정되나요?
- docker0와 내가 만든 port가 충돌되면?
- docker run 할 때 dockerfile의 label은 동일한가요?
- 이론교재 마지막 web과 db를 다른 브릿지에 구성했는데, 다른 브릿지간 라우팅 설정도 가능한가요?
  - 위의 `docker network connect` 명령어를 이용하면 컨테이너에 다수의 bridge를 연결할 수 있습니다. 이 방법을 사용하면 가능할 것 같습니다. 더 좋은 방법은 아직 찾질 못했습니다. ㅠㅠ
  
  
---

### Kubernetes

- Node = Host machine 인가요?
  - 네, 같은 개념으로 생각하시면 됩니다. K8S에서 컨테이너가 실행되는 곳(머신)입니다.
- 실습환경은 언제까지 쓸 수 있나요?
  - Killercoda가 사라지는 그날까지요.
- K8S cluster는 물리적인 서버 안에 있고, Node들도 하나의 물리적인 서버에 올라가있는 VM 머신인가요?
  - 맞습니다. Control plane과 (worker)node 모두 일반적으로 VM에 구성됩니다. Node중 특별한 경우(e.g. 고성능) baremetal 서버를 쓰기도 합니다.

---

- Status가 헬스체크를 의미하는 것 같은데, 애매한 상태일 때도 세부적으로 감지하는 방식이 있나요? 아니면, 그런 상황에서는 관리자 개입이 필요한가요?
  - 다음 장에 자세한 내용을 다루고 있습니다. Probe라는걸 지정해서 세부적인 상태체크를 할 수 있습니다.
- Declarative과정들이 작동하기 위해서는 Imparative 과정들이 내부적으로 작동해야 될 것 같은데, 이 내부적인 과정은 최적화해서 만들어주는건가요? (ai?)
  - 여러가지가 복합적으로 작동하게 됩니다. 어떤 명령을 처리할지, 처리할 때 어떤 알고리즘을 적용할지 등이 적용되는데, 자세한건 쿠버네티스 문서를 참조하세요.

---

- 선언형처리 시 생성/변경/삭제가 어떻게 내부적으로 감지되어 처리되는지 알려주세요.
  - 생성/변경은 k8s에서 내부적으로 diff한 후 없으면 create, 있으면 update(만약에 바뀐게 있으면) 또는 아무일도 안함.(바뀐게 없으면)
  - 삭제만 `kubectl delete -f OOO.yaml` 로 실행. (삭제도 apply로 할 수도 있지만 권장방법이 아님.) 
  - [구성 파일을 이용한 쿠버네티스 오브젝트의 선언형 관리](https://kubernetes.io/ko/docs/tasks/manage-kubernetes-objects/declarative-config/) 참조
- label의 key는 정해진 key만 사용해야 하나요?
  - 아니오, 자유롭게 정의할 수 있습니다.
  - [Recommended Labels](https://kubernetes.io/docs/concepts/overview/working-with-objects/common-labels/)이 이있긴 하지만, 이 외에도 자유롭게 정의해서 쓰면 됩니다.
- Label관리가 중요할 것 같은데, 관리 방법이 있을까요?
  - 위 질문의 답을 참조하세요. 일반적인 label 작성방법이나 사용규칙을 따르면 됩니다.
- etcd가 중요한 것 같은데, 임의로 삭제할 수 있나요? 또는 그런경우 원복 가능한가요?
  - 그래서, 아래 링크와 같이 고가용성 구성을 합니다. 당연히 백업/복구도 가능하구요.
  - [Set up a High Availability etcd Cluster with kubeadm](https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/setup-ha-etcd-with-kubeadm/)
- yaml형식에서 -(dash)표시를 하는 이유나 기준이 있나요?
  - 복수형 key-value 쌍인경우 사용됩니다. (e.g. containers: 다음 에 -를 붙여서 복수개의 container spec.을 정의합니다.)
- probe가 비정상적인것을 감지하는 것도 있나요?
- yaml파일을 쓴다고 다 선언형은 아닌 것 같아요. yaml을 쓰더라도 create명 명령형 apply면 선언형 이네요.
  - 네, 맞습니다. 선언형은 `kubectl apply -f OOO.yaml` 형태로 씁니다.
- Control plane은 운영환경에서 복수개 구성하나요? 장애가 생기면 문제가 될 것 같아서요.
  - 네, 맞습니다. control plane의 경우 고가용성 구성을 할 수 있습니다.
  - [Creating Highly Available Clusters with kubeadm](https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/high-availability/)
  - [고가용성 토폴로지 선택](https://kubernetes.io/ko/docs/setup/production-environment/tools/kubeadm/ha-topology/)
- minikube 는 하나의 노드에 control plane과 node가 다 구성되는건가요?
  - 네, 맞습니다. 일반적으로는 Control plane에는 kube-system의 컴포넌트들만 배치되고, node에는 사용자의 pod들만 배치되도록 합니다.
  - minikube는 위의 제약을 없애고 간단하게 실습을 해볼수 있도록 한 환경입니다. (하나의 노드에 control plane컴포넌트들과 사용자 pod들이 모두 배치됨.)

---

- workload(2)의 deployment yaml에서 deployment의 label과 pod template의 label이 같은데, 그러면 selector가 선택할 때 둘 다 선택되는 것 같아요.
  - 네, 그럴수도 있을 것 같습니다. 실제 업무에서는 이 예제보다는 더 정교하게 label을 구성해서 사용하는 것이 좋습니다.
- key-value 구성 시 value가 비어있으면 어떻게 되나요?
  - 안되네요.
- Pod를 만들어주는건 ReplicaSet인가요 Deployment인가요?
  - ReplicaSet 입니다. Deployment는 ReplicaSet을 만들고, ReplicaSet이 정해진 Spec에 따라 Pod를 만들게 됩니다.
- Recreate / Rollingupdate 중 default는?
  - RollingUpdate 입니다. (@박민선 프로님 감사합니다.)
- 선언형은 `kubectl apply`만 있나요? 명령형과 큰 차이가 없는 것 같은데(명령어 자체만 봐서는), 선언형을 중시하는 이유는 뭔가요?
  - 참고 : [쿠버네티스 오브젝트 관리](https://kubernetes.io/ko/docs/concepts/overview/working-with-objects/object-management/)
- cpu,memory에 따라 replicas를 조정하는 전략도 있나요?
  - [Horizontal Pod Autoscaler(HPA)](https://kubernetes.io/ko/docs/tasks/run-application/horizontal-pod-autoscale/)를 사용하면 됩니다. 

---

- 쿠버네티스 DNS나 방화벽 같은 보안 서비스는 어떻게 구성되나요?
  - 아래 두 개의 글 참조 바랍니다.
  - [서비스 및 파드용 DNS](https://kubernetes.io/ko/docs/concepts/services-networking/dns-pod-service/)
  - [클라우드 네이티브 보안 개요](https://kubernetes.io/ko/docs/concepts/security/overview/)
- 하나의 ReplicaSet에 여러 종류의 pod를 서로 다른 개수로 실행할 수 있나요?
  - ReplicaSet의 template은 단수 지정 가능합니다.(Pod는 한 가지만) template내의 Container는 복수지정 가능합니다.
- EC2 같은경우 inbound/outbound security group을 설정해서 제어하는데, k8s에 이런 개념이 있나요?
  - [클라우드 네이티브 보안 개요](https://kubernetes.io/ko/docs/concepts/security/overview/) 참조 바랍니다. in/out 제한은 4C 중 Cloud 영역에서 제한을 두는것이 맞는 것 같습니다.
- 개발/운영 환경을 격리시키려고 할 때 어떤 구성방식을 쓰나요?
  - 클러스터를 구분하기도 하고, 네임스페이스를 구분하기도 합니다. 가능하다면 클러스터를 구분하는 것이 좋을 것 같습니다.
- 클러스터 안에 노드가 있고, 그 안에 pod(container)가 있는걸로 이해했는데 왜 NodePort가 ClusterIP보다 밖에 있나요?
  - 교재의 그림은 Service 타입에 따른 노출 범위를 나타낸 것입니다. NodePort타입(Cluster외부에서 접근 가능)이 ClusterIP타입(Cluster 내부에서만 접근 가능)보다 넓은 범위를 가진다는 것을 표현한 것입니다. 단, ClusterIP타입도 다른 Proxy를 사용한다면 외부로도 노출시킬수 있습니다.
- Pod가 ReplicaSet에 의해서 여러 노드에 걸쳐 존재하고, Service가 Nodeport로 구성되면 특정 노드의 NodePort로 서비스가 요청되면 해당 노드에 존재하는 Pod에 대해서만 LB가 되나요? 아니면 전체 노드에 대해서 되나요?
  - NodePort타입의 서비스를 생성하면 지정한 Port로 모든 Node에서 접근 가능합니다. 어떤 node로 접근해도 그 클러스터 내에 분산되어 있는 Pod로 모두 연결 가능합니다.
  - [Kubernetes - Services Explained in 15 Minutes!](https://youtu.be/5lzUpDtmWgM) 참고하세요.
- Service의 Port방향이 헷갈리네요. (e.g. NodePort타입의 경우 80:30007/TCP) 어디가 외부로 연결되는건가요?
  - 30007이 노드의 포트이고 80은 서비스의 포트입니다. 30007이 외부에서 접근가능한 포트입니다.
- Pod에 Container가 여러개 일때 그 중 하나의 Container로 통신하려면? (selector로 ?)
  - Pod spec.에 여러개의 Container를 정의할 수 있고, 그 때 각 Container마다 사용할 Port를 지정할 수 있습니다. Service에서는 Selector로 Pod를 선택하고 Port로 Container를 지정하면 됩니다.

---

- 각 노드의 포트는 동일해야하나요? (NodePort?)
  - nodeport타입의 service를 생성하면 모든 노드에 동일하게 같은 port로 노출됩니다. (Service를 하나만 만들어도 모든 Node에 동일한 Port로 노출됨.)
- 노드포트에서 svc의 포트는 어떻게 정해지나요?
  - port는 svc의 port, targetPort는 pod의 port, nodePort는 node의 port입니다.
- replicaset의 label은 pod의 label과 같아야하나요?
  - 달라도 됩니다.

---

- kubectl apply 로 deployment 생성 후 kubectl get all 해서 나오는 pod가 다 앞의 결과인가요?
  - 그렇지는 않습니다. get all하면 다른 deployment에 의해서 만들어진 것도 다 보입니다.
  - 특정해서 보려면, label같은걸로 필터링해서 보거나 해야 합니다.
- 버티컬바(|)는 어떤 의미인가요?
  - 리눅스의 파이프라인 입니다. [Pipeline (Unix)](https://en.wikipedia.org/wiki/Pipeline_(Unix))
- env와 envFrom의 차이는 뭔가요?
  - env는 개별 환경변수를, envFrom은 환경변수 리스트를 적용합니다. 아래 두 가지 비교해서 보세요.
  - [Define container environment variables using ConfigMap data](https://kubernetes.io/docs/tasks/configure-pod-container/configure-pod-configmap/#configure-all-key-value-pairs-in-a-configmap-as-container-environment-variables)
  - [Configure all key-value pairs in a ConfigMap as container environment variables](https://kubernetes.io/docs/tasks/configure-pod-container/configure-pod-configmap/#configure-all-key-value-pairs-in-a-configmap-as-container-environment-variables)
- configMap을 사용할 수 있는 바운더리는 어떻게 되나요? (e.g. namespace?)
  - Namespace내에서 사용됩니다.
- kubectl exec -it 를 이용해서 pod 에서 docker ps -a 명령을 실행할 수 있나요? (또는 런타임이 다르니 안되나요?)
  - Container에 Docker가 존재한다면 가능하지만, 아니라면 안됩니다.
  - docker명령어를 사용하려는 이유가 컨테이너에 대한 처리와 확인때문이라면, pod의 container에 대해서는 kubectl 명령어로도 대부분 커버됩니다.(e.g. 로그확인, console연결 etc.)
- 최근 이슈(클라우드 사용료 과다 부과)가 configmap이나 secret이 git을통해 노출되어 그런건가요?
  - 말씀하신 사례는 아마도 클라우드에 사용되는 키나 개인정보가 누출되어 발생한 것 같습니다.
  - 하지만, ConfigMap이나 Secret도 중요정보가 있다면 보안에 유의해서 관리하셔야 합니다. (e.g. git repo는 private으로 설정, 2FA설정 etc.)
- metric정보를 사용자정의 할 수 있나요? (e.g. queue의 잔여 메시지 수)
  - [사용자 정의 메트릭을 이용하는 스케일링](https://kubernetes.io/ko/docs/tasks/run-application/horizontal-pod-autoscale/#%EC%82%AC%EC%9A%A9%EC%9E%90-%EC%A0%95%EC%9D%98-%EB%A9%94%ED%8A%B8%EB%A6%AD%EC%9D%84-%EC%9D%B4%EC%9A%A9%ED%95%98%EB%8A%94-%EC%8A%A4%EC%BC%80%EC%9D%BC%EB%A7%81)을 참조하세요.
  - 사용자정의 메트릭이나 외부 메트릭도 사용 가능합니다. (저도 이번에 알았습니다. 꾸벅)
- scale-out 할 때 노드의 가용성 부분이 확보되어야 할 텐데, 그 부분은 k8s가 체크를 하나요?
  - 결국 Node에 Pod를 배치하는 것은 kube-scheduler가 하게되는데, 이 때 가용한 자원이 없다면 배치에 실패하게 됩니다.
- hpa max replication값이 서버 자원을 초과하면 자동으로 scale-out을 중단하나요? 아니면 오류가 발생하나요?
  - 위의 내용 참조 바랍니다.

---

- 실무에서 helm을 많이 사용하나요? 
  - 네, 많이 사용됩니다. K8S리소스들을 개별 관리하는것 보다는 Helm chart(패키지)로 관리하는 것이 훨씬 장점이 많습니다.
  - 사내 시스템들도 모두 Helm chart를 기반으로 동작하고 있습니다.
- values.yaml에서 대소문자를 구분하나요?
  - 네, 구분합니다. [Values](https://helm.sh/docs/chart_best_practices/values/) 도 참고하세요.

---

## Playlist
- [[MV] 김연지(Kim Yeon-Ji) - Whisky on the Rock | 우리들의 블루스(Our Blues) OST Part 1](https://youtu.be/7h3ndp05JN0)  
- [아내에게 게임기 사자고 하는 노래](https://youtu.be/3oYZ9rlF08M)   
- [린(LYN)의 킬링보이스를 라이브로! - My Destiny, …사랑했잖아…, 곰인형, 자기야 여보야 사랑아, 시간을 거슬러, 통화연결음](https://youtu.be/0vvCe4EHtus)  
- [아이유(IU)의 킬링보이스를 라이브로! - 하루 끝, 너의 의미, 스물셋, 밤편지, 팔레트, 가을 아침, 삐삐, Blueming, 에잇, Coin, 라일락](https://youtu.be/wDfqXR_5yyQ)  
- [장기하의 킬링벌스를 라이브로! | 그건 니 생각이고, 싸구려커피, 땡큐땡큐, 그렇고 그런 사이, 빠지기는 빠지더라, ㅋ, 얼마나 가겠어, 부럽지가 않어 등](https://youtu.be/ImapK-6lOvA)  
- [[가정의 달 특집] 어른이들을 위한 1990's 0.1세기 힛-트쏭 | KBS 방송](https://youtu.be/4zPpLcMNZT4)  
