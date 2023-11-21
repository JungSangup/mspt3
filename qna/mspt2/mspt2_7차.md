
# Docker & Kubernetes 노트 (MSP T2 7차)   (❁´◡`❁)

## Q&A

### Docker

- 컨테이너별 이미지가 있는건가요?
  - 이미지는 컨테이너를 생성하기위한 패키지 입니다. 이미지가 있으면 컨테이너를 얼마든지 만들 수 있습니다. 
- 컨테이너 로그파일은 컨테이너 내부에 생성되나요? 외부에 생성되나요? 내부라면 컨테이너 레이어인가요?
  - 기본적으로는 실행된 컨테이너의 컨테이너 레이어(R/W layer)에 생성됩니다.
  - 하지만, 외부에 생성할 수도 있습니다. Volume이나 Bind mount를 이용해서 호스트 머신의 영역에 데이터를 저장할 수 있습니다.
- /var/lib/docker/overlay2를 조회하니 cannot access...no such file or directory라고 나옵니다. Storage driver는 overlay2 입니다. (추가. /var/lib/docker 도 없네요.)
  - [Docker 이미지 스토리지 폴더 찾기 문제](https://docs.microsoft.com/ko-kr/windows/wsl/tutorials/wsl-containers#trouble-finding-docker-image-storage-folder) 를 참고하세요.
  - [Announcing Windows 10 Insider Preview Build 21354](https://blogs.windows.com/windows-insider/2021/04/07/announcing-windows-10-insider-preview-build-21354/) 의 "Changes and Improvements" 부분에도 \\\\wsl.localhost 에 대한 설명이 있습니다.
  - Docker Root Dir 위치 : \\\\wsl.localhost\docker-desktop-data\data\docker ( -> 탐색기에서 이 경로로 검색)
  - Overlay2 위치 : \\\\wsl.localhost\docker-desktop-data\data\docker\overlay2
  - volumes 위치 : \\\\wsl.localhost\docker-desktop-data\data\docker\volumes

---

- volume은 컨테이너 하나만 접근 가능한가요? 컨테이너별로 RW/RO 설정해서 사용 가능한가요?
  - 여러개의 인스턴스(컨테이너)에서 공유 가능합니다. RW/RO는 마운트 시점(docker run할 때)에 결정됩니다.
- docker volume prune의 기준(미사용 컨테이너)은 뭔가요?
  - [docker volume prune](https://docs.docker.com/engine/reference/commandline/volume_prune/)  
  - Remove all unused local volumes. Unused local volumes are those which are not referenced by any containers
- todo 애플리케이션에서 어떻게 DB만 볼륨에 저장한건가요?
  - 해결됐습니다. ^^;
- Docker desktop 설치 시 에러 메시지가 발생하고, 설치가 안됩니다. (Windows 10 최신버젼)
  - [WSL을 사용하여 Windows에 Linux 설치](https://docs.microsoft.com/ko-kr/windows/wsl/install) 를 참고해서 다시한 번 해보세요.
  - [WSL 개발 환경 설정](https://docs.microsoft.com/ko-kr/windows/wsl/setup/environment) 도 참고하면 좋을 듯 합니다.
  - 만약, 계속 안된다면 힘들게 하려고 하지 마시고 다른 방법을 써도 됩니다. (e.g. 사내 Dogether에 올라와 있는 도커 설치방법 등을 참조하세요.)
- 왜 다 Linux 기반인가요?
  - 컨테이너 기술의 시작이 Linux이고, 더 다양한 컨테이너들을 만들고 사용할 수 있기 때문이 아닐까 합니다.
  - [Windows container](https://docs.microsoft.com/en-us/virtualization/windowscontainers/about/)도 있지만, 제한적인 [Container Base Images](https://docs.microsoft.com/en-us/virtualization/windowscontainers/manage-containers/container-base-images) 를 사용해야 하는 등 제약이 많습니다.
- 하나의 볼륨을 여러 인스턴스(컨테이너)가 사용 가능한가요?
  - 가능합니다. 이 구역의 첫 번째 질문/답변 참조하세요.
- hub없이 repo를 리눅스 로컬에 만드는 방법도 알려주시나요?
  - [E2E개발과정](https://devops.sdsdev.co.kr/confluence/x/t4ITAw) 의 교재 참고하세요.
  - [Docker Registry](https://docs.docker.com/registry/) 도 참고하세요.

---

- 윈도우 가상환경에서는 docker0가 안보입니다.
  - [There is no docker0 bridge on the host](https://docs.docker.com/desktop/networking/#there-is-no-docker0-bridge-on-the-host)
- docker run 의 옵션 중 --network-alias의 사용처에 대해 설명해주세요.
  - [Differences between user-defined bridges and the default bridge](https://docs.docker.com/network/bridge/#differences-between-user-defined-bridges-and-the-default-bridge) 참조.
  - User-defined bridge network를 생성하면, 그 네트워크에 속한 컨테이너들은 이름 또는 Alias를 이용하여 resolve가능합니다. (User-defined bridges provide automatic DNS resolution between containers.)  

---

- MSA관점에서의 컨테이너 설계, Bounded context기준의 그룹핑?
- AWS 사용료는 어떻게 결재되나요?
- MD파일 어떻게 작성하셨나요? typora는 유료화되어서 못쓰네요. ㅠㅠ
  - VS Code를 사용하신다면, [Markdown and Visual Studio Code](https://code.visualstudio.com/docs/languages/markdown) 참고하세요.
  - [typora](https://typora.io/)와 유사한 [MarkText](https://github.com/marktext/marktext) 도 있네요.
- killercoda의 포트접속 다시 한 번 보여주세요.
  - Killercoda의 [For creators](https://killercoda.com/creators) 문서에서 `Network Traffic into Environments` 부분 참조하세요.

---

### Kubernetes

[Kubernetes Overview Diagrams](https://shipit.dev/posts/kubernetes-overview-diagrams.html) 

---

- CSP 상품으로 K8S구성할 경우 사용자는 어디에서 YAML파일을 작성하고 kubectl을 실행하나요? CMP에서 모두 가능한가요?
  - 보통은 Bastion server를 별도로 하나 두고, Bastion server로 ssh 접속해서 kubectl 을 사용할 수 있도록 구성합니다.
  - 또는 CSP마다 제공되는 Web shell(Browser-based shell)상품같은 것을 사용할 수도 있습니다.

---

- Probe는 복수 개 지정 가능한가요?
  - 원칙은 종류별로 하나의 probe만 지정 가능합니다. (e.g. liveness probe 하나 + readiness probe 하나)
  - Workaround로 여러개의 probe를 사용하는 것과 같이 환경을 만들 수는 있습니다. (e.g. 여러 http request를 체크하는 shell프로그램을 만들고, probe는 shell을 exec로 체크)
- Probe가 pod의 상태를 확인한다고 이해했는데요, 어떤 pod가 문제인지 확인하는 방법은 뭔가요?
  - probe에의해 fail로 판단되면 restart를 하게되는데, `kubectl get pods`명령어로 조회되는 pod들 중 RESTARTS 횟수가 비정상적으로 많은경우 확인을 해보면 됩니다.
- Probe를 만들 때 대상 컨테이너를 지정할 수 있나요?
  - probe spec.은 container spec.하위에 있습니다. 즉, 컨테이너 마다 지정할 수 있습니다.
- 지금 실습하는 pod들은 도커 이미지 없이 기동되는건가요? pod기동 후 docker image나 ps 조회가 되나요?
  - 동일하게 도커 이미지가 pull되고 run 됩니다. Container runtime이 docker인 경우 docker명령어로 조회도 가능합니다.
- K8S 1.20 이후에는 Docker가 deprecated 되었다고 했는데, 하위버젼을 계속사용하는경우 문제가 될까요?
  - [버전 차이(skew) 정책](https://kubernetes.io/ko/releases/version-skew-policy/) 참조 바랍니다.
  - 기본기능은 그대로 사용 가능하나, 패치지원 불가와 같은 문제들은 감안하셔야 합니다.
- Probe를 지정해서 workload를 생성했을 때 probe의 로그도 따로 확인할 수 있나요?
  - Pod의 event log를 참조하시면 됩니다.

---

- containerd가 대세인가요? docker명령어 대신 익혀야 하는지?
  - [컨테이너 런타임](https://kubernetes.io/ko/docs/setup/production-environment/container-runtimes/)
  - [Google trend](https://trends.google.com/trends/explore?date=today%205-y&q=containerd,cri-o)
  - containerd의 경우 이미지 관리등을 위해 [podman](https://podman.io/)과 같은 툴을 사용할 수 있습니다.
- replicas를 변경할 때(줄일 때) pod 삭제 우선순위가 있나요?
  - [파드 삭제 비용](https://kubernetes.io/ko/docs/concepts/workloads/controllers/replicaset/#%ED%8C%8C%EB%93%9C-%EC%82%AD%EC%A0%9C-%EB%B9%84%EC%9A%A9) 문서를 참조 바랍니다.
  - 비용을 기준으로 삭제 우선순위 지정 가능합니다. (저도 이번에 알았네요. 감사합니다. 꾸벅)
  - 추가로 [Annotation](https://kubernetes.io/ko/docs/concepts/overview/working-with-objects/annotations/) 에 대해 아셔야 합니다.
- replicas를 2로 설정하면 pod도 무조건 2개 생기는건가요?
  - K8S는 그렇게 하려고 노력하겠지만, 안되는 경우(e.g. 클러스터에 pod를 생성할 수 있는 리소스가 부족한 경우)에는 어쩔 수 없겠죠? 이 겨우에는 생성 못한다고 이벤트 로그에 표시됩니다. (no nodes available to schedule pods ) 
- pod개수 조정할 때 node를 지정할 수 있나요?
  - 할 수도 있습니다.  nodeSelector를 사용하면 특정 노드를 지정할 수 있습니다.
  - [노드에 파드 할당하기](https://kubernetes.io/ko/docs/concepts/scheduling-eviction/assign-pod-node/) 참조하세요.
- LoadBalancer의 의미상 뒷 단의 Service가 복수개 필요할 것 같은데요...
  - [Kubernetes Services explained](https://youtu.be/T4Z7visMM4E?t=1282) 참고하세요.
- 10.Kubernetes service교재의 7페이지 그림에서 (2)ClusterIP를 안쓰면 어떻게 되나요?
  - 여러가지 타입 중 한 가지를 사용하면 됩니다. ClusterIP < NodePort < LoadBalancer 타입 순으로 범위가 넓어지는 구조입니다. (e.g. NodePort타입은 ClusterIP타입의 특징을 포함하고, 추가로 Node의 Port를 할당합니다.) 
- Kubernetes에 containerd를 사용하려면 별도로 설치해야 하나요?
  - 네, 설치해야 합니다. 
  - [컨테이너 런타임](https://kubernetes.io/ko/docs/setup/production-environment/container-runtimes/) 를 참고하여 선택 후 설치하시면 됩니다.
  - 설치 방법은 [Getting started with containerd](https://github.com/containerd/containerd/blob/main/docs/getting-started.md)를 참고하세요.
- service도 여러개 만들 수 있는 것 같은데요, metadata의 이름은 중복되면 안되겠죠?
  - 해볼까요?
  - 안되네요. (예상했던 대로...) 아래처럼 에러 메시지(`services OOO already exists`)를 보여주면서 생성에 실패합니다.
```bash
ubuntu@ip-10-0-1-84:~/mspt2/hands_on_files$ cat nginx-clusterip-service.yaml
apiVersion: v1
kind: Service
metadata:
  name: nginx-clusterip-service
spec:
  type: ClusterIP
  selector:
    app: my-nginx
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
ubuntu@ip-10-0-1-84:~/mspt2/hands_on_files$ cat nginx-clusterip-service2.yaml
apiVersion: v1
kind: Service
metadata:
  name: nginx-clusterip-service
spec:
  type: ClusterIP
  selector:
    app: my-nginx
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
ubuntu@ip-10-0-1-84:~/mspt2/hands_on_files$ kubectl create -f nginx-clusterip-service.yaml
service/nginx-clusterip-service created
ubuntu@ip-10-0-1-84:~/mspt2/hands_on_files$ kubectl create -f nginx-clusterip-service2.yaml
Error from server (AlreadyExists): error when creating "nginx-clusterip-service2.yaml": services "nginx-clusterip-service" already exists

```

---

- NodeIP 타임은 node의 ip가 열려있다면 어디서든 이 아이피로 접근 가능한가요?
  - 교재내용을 업데이트 해 놓겠습니다. External IP가 할당되고 그 아이피가 열려있다면 어디서든 접근 가능하다가 맞습니다. (감사합니다.)
- DNS에 어떻게 기록되어 있는지 알 수 있나요?
  - [서비스 및 파드용 DNS](https://kubernetes.io/ko/docs/concepts/services-networking/dns-pod-service/) 참조 바랍니다.
  - Service와 Pod 가 생성되면 DNS (현재는 CoreDNS를 사용합니다.)에 자동으로 레코드가 생성됩니다.
  - Service 레코드 형식 : my-svc.my-namespace.svc.cluster-domain.example (e.g. nginx-svc.default.svc.cluster.local)
  - Pod 레코드 형식 : pod-ip-address.my-namespace.pod.cluster-domain.example
- Nodeport생성 시 ClusterIP가 필수인건가요? 아니면 없어도 되나요?
  - Nodeport타입은 ClusterIP타입의 특징을 그대로 가지면서 추가로 nodeport (30000~32767)를 할당합니다. ClusterIP타입을 추가로 만들 필요는 없습니다.
- Deployment에서 Scale-out 말고 Scale-up은 안되나요? (추가질문, Spec.에 cpu, memory등 설정 가능한가요?)
  - 가능합니다. 아래 링크 참조하세요. (교재에는 다루지 않았지만 spec.에 리소스 request/limit을 설정할 수 있고 변경할 수도 있습니다.)
  - [파드 및 컨테이너 리소스 관리](https://kubernetes.io/ko/docs/concepts/configuration/manage-resources-containers/)
- Loadbalancer는 ingress와 같이 rule을 등록하여 path별 호출 분리가 불가능한거죠? 서비스를 LB를 따로 구성하는 방법으로만 구현 가능한건가요?
  - [AWS Load Balancer Controller](https://github.com/kubernetes-sigs/aws-load-balancer-controller) 참고하세요.
  - [로드 밸런서 유형](https://docs.aws.amazon.com/ko_kr/AmazonECS/latest/developerguide/load-balancer-types.html) 문서도 참고하면 좋겠네요.
- 필드에서 가장 많이 구성하는 서비스는 어떤 유형인가요? CSP별로 달라서 CSP 디펜던트 한가요?
  - ClusterIP타입이 default이고, 앞에 proxy역할을 하는 ingress resource를 두는 형태를 가장 많이 씁니다.
  - ingress controller는 Nginx같은걸 쓸 수도 있고, CSP에서 제공하는 상품으로 구성할 수도 있습니다. (e.g. AWS ALB)

