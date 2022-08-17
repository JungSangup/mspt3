
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
- 하나의 볼륨을 여러 인스턴스(컨테이너)가 사용 가능한가요?
  - 가능합니다. 위의 질문/답변 참조하세요.
