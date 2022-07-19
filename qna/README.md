# Docker & Kubernetes 노트 (MSP T2 6차)


[https://github.com/JungSangup/mspt2](https://github.com/JungSangup/mspt2)

## Q&A
- 도커 이미지에서 여러 OS가 사용되는데, 도커가 구동되는 OS와 충돌이나 호환성 이슈는 없나요?
- 격리수준이 VM보다 떨어지면 실제 운영시 장애전파에 대한 대비책같은게 있을까요?
- EC2? 어제 생성한 것 사용 가능한가요? (어제는 Windows)
- 도커 올릴 수 있는 Host OS는 Linux만 가능한가요? 윈도우 컨테이너는?
- 단일 컨테이너만 동작하는 경우 Host리소스를 최대로 사용하도록 설정 가능한가요?
- 위와 같은 경우 한계점은?
- 컨테이너가 요청하는 총 리소스의 합이 하드웨어 총량을 초과하면?
- EC2에서 Command 'docker' not found 라고 나오면?

---

- 레이어드파일시스템이 nfs, xfs와 다른건가요?
- 컨테이너의 R/W, R/O(공유)레이어를 구분하는 기준은 뭔가요?
- nginx 이미지를 만들때 사용한 도커파일은 명령어가 6개 있어서 레이어가 6층인가요? (무한대면 무한태로 늘어나나요?)
- docker run 할 때 name은 임의로 생성되나요?
- 개발/운영할 때 레이어단위로 관리하거나 레이어별로 분석하거나 하는 경우도 있나요?
- R/O 레이어에서 필요없어진 파일을 다음 레이어에서 삭제하거나, 용량이 큰 이전버젼 레이어를 수정하는 경우에도 도커 이미지 안에는 모든 파일이 존재하는건가요?
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
- Play with Docker에서는 안되나요?
- tag와 name차이?
- detach/foregroud 차이? 
- docker hub 가입해야하나요?

---

- docker stop되어도 데이터가 사라지지 않는 것 아닌가요?
- volume -> 설정 (e.g. NAS같은걸 사용할 때) 어떻게 하나요?
- volume 용량제한 가능한가요?
- 회사에서 -v로 그냥 nas를 붙였는데, 아마도 bind mount였나보네요. ㅎㅎ
- killercoda 에서 cmd delete안되게 가능한가요? 전에 실습한 내용이 사라져요...
- volume경로는 어떻게 지정하나요? 아니면 정해져있나요?
- Unable to find image 'yss:latest'... pull access denied for yss... repository does not exist or may require 'docker login'... 해결해주세요~
- --publish 3000:3000 앞은 호스트머신 포트인건 알겠는데, 뒤의 3000는 뭥미?
- docker attach, ctrl+c , exit 동작 
- volume이 지워지는 시점이 다로 있거나 정책같은게 있나요?
