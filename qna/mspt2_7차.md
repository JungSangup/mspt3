
# Docker & Kubernetes 노트 (MSP T2 7차)   (❁´◡`❁)

## Q&A

### Docker

- 컨테이너별 이미지가 있는건가요?
- 컨테이너 로그파일은 컨테이너 내부에 생성되나요? 외부에 생성되나요? 내부라면 컨테이너 레이어인가요?
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
  - [Docker Registry](https://docs.docker.com/registry/) 도 

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
- killercoda의 포트접속 다시 한 번 보여주세요.
- Docker desktop for Windows 사용 시 \\wsl$\Ubuntu\home\사용자명 참조.
