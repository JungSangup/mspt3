
# Docker & Kubernetes 노트 (MSP T2 7차)   (❁´◡`❁)

## Q&A

### Docker

- 컨테이너별 이미지가 있는건가요?
  - 이미지는 컨테이너를 생성하기위한 패키지 입니다. 이미지가 있으면 컨테이너를 얼마든지 만들 수 있습니다. 
- 컨테이너 로그파일은 컨테이너 내부에 생성되나요? 외부에 생성되나요? 내부라면 컨테이너 레이어인가요?
  - 기본적으로는 실행된 컨테이너의 컨테이너 레이어(R/W layer)에 생성됩니다.
  - 하지만, 외부에 생성할 수도 있습니다. Volume이나 Bind mount를 이용해서 호스트 머신의 영역에 데이터를 저장할 수 있습니다.
- /var/lib/docker/overlay2를 조회하니 cannot access...no such file or directory라고 나옵니다. Storage driver는 overlay2 입니다. (추가. /var/lib/docker 도 없네요.)

---

- volume은 컨테이너 하나만 접근 가능한가요? 컨테이너별로 RW/RO 설정해서 사용 가능한가요?
  - 여러개의 인스턴스(컨테이너)에서 공유 가능합니다. RW/RO는 마운트 시점(docker run할 때)에 결정됩니다.
- docker volume prune의 기준(미사용 컨테이너)은 뭔가요?
  - [docker volume prune](https://docs.docker.com/engine/reference/commandline/volume_prune/)  
  - Remove all unused local volumes. Unused local volumes are those which are not referenced by any containers
- todo 애플리케이션에서 어떻게 DB만 볼륨에 저장한건가요?
  - 해결됐습니다. ^^;
- Docker desktop 설치 시 에러 메시지가 발생하고, 설치가 안됩니다. (Windows 10 최신버젼)
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
  - [typora](https://typora.io/) 유사한 [MarkText](https://github.com/marktext/marktext) 도 있네요.
- killercoda의 포트접속 다시 한 번 보여주세요.
  - Killercoda의 [For creators](https://killercoda.com/creators) 문서에서 `Network Traffic into Environments` 부분 참조하세요.
- Docker desktop for Windows 사용 시 \\\\wsl$\Ubuntu\home\사용자명 참조.
- Docker desktop for windows 사용 시 \\\\wsl$\docker-desktop-data\ 참조. (e.g. overlay2)



### Kubernetes
- CSP 상품으로 K8S구성할 경우 사용자는 어디에서 YAML파일을 작성하고 kubectl을 실행하나요? CMP에서 모두 가능한가요?
  - 보통은 Bastion server를 별도로 하나 두고, Bastion server로 ssh 접속해서 kubectl 을 사용할 수 있습니다.
  - 또는 CSP마다 제공되는 Web shell(Browser-based shell)상품같은 것을 사용할 수도 있습니다.

---

- Probe는 복수 개 지정 가능한가요?
- Probe가 pod의 상태를 확인한다고 이해했는데요, 어떤 pod가 문제인지 확인하는 방법은 뭔가요?
- Probe를 만들 때 대상 컨테이너를 지정할 수 있나요?
- 지금 실습하는 pod들은 도커 이미지 없이 기동되는건가요? pod기동 후 docker image나 ps 조회가 되나요?
- K8S 1.20 이후에는 Docker가 deprecated 되었다고 했는데, 하위버젼을 계속사용하는경우 문제가 될까요?
- Probe를 지정해서 workload를 생성했을 때 probe의 로그도 따로 확인할 수 있나요?
