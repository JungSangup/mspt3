

# Docker & Kubernetes 노트 (MSP T2 8차)   (●'◡'●)
## Q&A

### Docker

- docker architecture 그림을 보면 image가 host에 있고, 원본(?)은 registry에 있는 것 같은데 컨테이너를 생성하게 되면 host에 있는 image를 사용하는 건가요?
  - 네, 맞습니다. 컨테이너 생성은 host머신에 있는 image를 이용합니다. host머신에 image가 없으면 registry에서 먼저 pull해서 host머신에 가져온 다음 실행을 합니다.
- 레이어를 구분하는 기준은 무엇인가요? 기능인가요? 레이어를 세팅하면서 기능이든 다른 기준이든 세팅을 할 수 있는건가요?
  - 5장 6장에 dockerfile과 BP를 배울텐데요, 그때 자세히 다루어집니다. 조금만 참아주세요~ ^^
- Github로 비유하자면 dockerfile은 개발코드, image는 feature brench, docker registry는 github의 branch(운영/개발/qa)같은거라고 보면 될까요? devops로 각 서버로 빌드하는게 container run개념과 비슷해보여서요.
  - Github의 브랜치와 비유하기는 좀... 어렵네요. -_-;
  - 수업중에 DevOps 과정을 한 번 설명드리겠습니다. (컨테이너인 경우 어떻게 DevOps, CI/CD를 하는지)
- windows에 ubuntu를 설치한 후 Docker Desktop을 통해 Docker를 설치해야 하는데요, Ubuntu에 apt로 설치하는 것과 차이가 있는지 궁금합니다.
  - 둘 다 docker architecture( cli, daemon )를 이용하는데, Docker desktop은 CLI뿐만 아니라 GUI를 가진 형태이고 kubernetes까지 손쉽게 학습환경을 만들 수 있다는 장점이 있습니다.
  - apt로 설치하시는 방법은 교재로 보자면 docker engine을 설치하는 것입니다. (cli로 daemon과 통신하면서 실행하는 방식)

---
