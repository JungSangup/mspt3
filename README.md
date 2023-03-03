
![](doc/img/banner.png)

이 Repository는 **MSP T3** 과정 중 **Docker & Kubernetes** 학습을 위한 교재와 실습파일들의 저장소 입니다.  



## 디렉토리 구조 및 설명

| 디렉토리             | 설명                                   |
|:---------------- |:------------------------------------ |
| ./doc            | 이론/실습 교재 (markdown)                  |
| ./doc/themes     | 교재(md)의 slides 형식을 위한 themes (css적용) |
| ./doc/img        | 교재에 사용된 이미지                          |
| ./doc/pdf        | 이론교재의 slides 형식 출력본(pdf)             |
| ./hands_on_files | 실습에 사용되는 파일들                         |
| ./qna            | 각 차수별 메모와 질문/답변                      |


## 학습방법
[Contents](https://github.com/JungSangup/mspt3/tree/main/doc#contents) 의 순서에 따라 **이론**과 **실습**을 진행합니다.  
각 이론교재는 기본 내용을 설명하고 있고, 교재의 링크를 통해 보다 더 깊이있는 내용을 학습할 수 있습니다.  


## 실습환경
실습교재의 내용을 직접 실행해볼 수 있는 환경은 다음과 같습니다.  
환경에 따라 교재의 내용 중 일부는 실행되지 않을 수도 있습니다. 

| 실습환경 | 설명 |
| :--- | :--- |
| [Docker engine](https://docs.docker.com/engine/install/) + [Minikube](https://minikube.sigs.k8s.io/) | 단일 노드 실행환경 입니다.<br>실습교재에는 **AWS EC2 Instance**를 이용하여 Docker와 Minikube를 구성하는 내용이 포함되어 있습니다. |
| [Killercoda](https://killercoda.com/brian) | 설치없이 간단히 실습해볼 수 있는 환경입니다.<br>이 과정의 실습교재 내용 대부분을 동일하게 구성해 놓았습니다. |
| [Docker Desktop on Windows](https://docs.docker.com/desktop/install/windows-install/) | 학습용 환경인 경우 Windows에 Docker Desktop을 설치해서 Docker와 Kubernetes(Single-node)까지 실습해볼 수 있습니다.<br>설치 가이드 : [[Appendix] Docker_desktop.md](./doc/%5BAppendix%5D%20Docker_desktop.md) |
| [Play with Docker](https://labs.play-with-docker.com/) | 설치없이 간단히 실습해볼 수 있는 환경입니다. (Docker) |
| [Play with Kubernetes](https://labs.play-with-k8s.com/) | 설치없이 간단히 실습해볼 수 있는 환경입니다. (Kubernetes) |  

> WSL2기반의 Docker Desktop on Windows인 경우 Docker root dir.의 위치가 아래와 같습니다.
> 탐색기에서 `\\wsl.localhost\docker-desktop-data\data\docker` 로 탐색  

<br><br><br><br>

-------------------- 절취선 ----------------------  ( 이하 내용은 삭제 예정 )

MSP T3 강의계획

- 1일차
  - 이론 : Docker overview ~ network
  - 실습 : Docker 설치 ~ network
- 2일차
  - 이론 : Dockerfile ~ K8s workload(2)
  - 실습 : Dockerfile ~ K8s workload(2)
- 3일차
  - 이론 : K8s service ~ Helm
  - 실습 : K8s service ~ Helm

순서조정 (K8s)
Overview > Workload(1) > Workload(2) > Service > Storage > Configuration > Deployment strategy > HPA > Helm

### 개선사항

- [x] A반/B반으로 나누어서 진행함. -> 교재에 내용 반영완료.

- [x] ubuntu 20.04 사용. -> 교재에 반영완료.

- [x] mspt3.pem 파일처리 명령어로 안될 때 어떻게 할지... ( `icacls.exe mspt3.pem /GRANT:R "SDS:(R)"` )  -> 강의 중 이슈 발생 시 안내.

- [x] powershell, mac 기본 terminal 기준으로  -> 선행과정에서 MobaXTerm을 설치/사용하므로, MobaXTerm을 기본으로 안내

- [x] 리눅스 명령어 정리 -> https://github.com/JungSangup/mspt3/blob/main/doc/%5BAppendix%5D%20%EB%A6%AC%EB%88%85%EC%8A%A4%EB%AA%85%EB%A0%B9%EC%96%B4.md 추가

- [x] 각 실습 마지막 정리하기 추가 -> 교재에 내용 반영완료.

- [x] pdf, powershell 복사/붙여넣기 이슈 -> 실습교재는 Github의 md파일로 진행. (code block 복사하기 버튼 활용)  

- [x] Editor는 VI를 사용. (가이드 활용) -> 강의 중 기본 사용법 안내. 

- [x] minikube start 시 오류 (/etc/docker)  -> Ubuntu, Docker, Minikube, Kubernetes 버젼을 특정해서 진행. (오류가 발생하지 않는 버젼의 조합 사용)

- [x] 터미널 프로그램 통일  -> 선행과정에서 MobaXTerm을 설치/사용하므로, MobaXTerm을 기본으로 안내

- [x] 실습파일 챕터별로 번호(?) 부여  -> hands_on_files/chxx 로 챕터별 실습파일 위치를 나누고, 교재에 반영함.

- [x] 전체 교육과정 맵 제공  -> 각 차수별 qna에 맵을 제공하고, 진행 중 공유

- [x] 사전준비 ( Docker Hub가입, 터미널 툴 설치 )   -> 두 가지 포함하여 사전준비 안내 준비.

- [x] 실습 시작 전 체크사항 정리 (EC2접속해서 어느 디렉토리로 이동하고... 등) -> https://github.com/JungSangup/mspt3/blob/main/doc/README.md 에 정리함.(매일 실습 전 함께 체크하고 진행)  

- [x] 일자가 바뀌어도 실습환경 유지가 되도록 주의. (EC2 stop하지 말고 3일단 유지) -> 가능하면 stop하지 않도록 안내하고, stop해도 다시 시작 가능한 방안(실습시작 전 체크사항) 제공. 

- [x] 진행 시 화면 해상도 적정하게 

- [x] A,B반 동일리전 사용 ( 버지니아 ) -> 교재 수정 완료

- [ ] 실습화면에서 예제와 명령어의 순서 변경? 또는, 예제는 복사되지 않도록 (의견)

- [ ] 실습내용 중 VI가 필요한 부분에 내용 추가 ( vi 뒤에 파일명 넣기, i눌러서 편집, :wq로 종료, mkdir로 파일 dir만들기 등등. Dockerfile 생성하는 부분이나, yaml 생성하는 부분들에 필요함.)

- [x] 11장의 보너스 실습에서  http://todo-app.info/에 접속할 때 hosts파일에 수정하라는 부분이 명시 되어 있었는데, 이건 보너스 실습이어서 수행 안하는 사람도 있을것으로 추정 됩니다. 그래서 12장에서 http://todo-app.info/에 접속하는 부분이 나오기 전, hosts파일을 삭제 하라고 함께 명시 해 주면 좋을 것 같습니다. -> 11,12장 hosts 관련부분 수정했습니다.

- [x] 실습파일 위치 : hands_on_files/ch05 에서 mspt3/hands_on_files/ch05 까지 해서 넣어주는게 좋을 것 같습니다. (처음으로 실습 위치가 바뀌는 챕터다 보니 참고용으로 넣으면 좋을 것 같습니다.) / Ch05, 08, 09, 10 등 상단 수행 디렉토리 위치에 mspt3 디렉토리도 표시되면 좋을 것 같습니다. (ex. mspt3/hands_on_files/ch05) -> 실습파일이 있는 모든 챕터에 디렉토리 이동명령어 추가함.

- [ ] [Hands-on] 07. 에서 kubectl logs -n kube-system [KUBE-SCHEDULER-NAME]
확인 하는 부분에서 logs 확인하면 failed 가 나와서 수강생들이 잘못 한 것으로 오해할 수 있으니 log 확인이므로 fail 가 나와도 상관없이 정상 처리 된 것임을 알려주시면 좋을 것 같습니다.

- [ ] 수업 중간에 쉬는 시간을 공지하고 해당 시간 동안은 화면을 정지한 상태로 유지해 주세요

- [ ] Ch07. Kubernetes Overview > 1. kubectl 기본 명령어 사용> [KUBE-SCHEDULER-NAME] 은 앞의 조회한 결과의 어디를 말하는 것인지 앞의 조회한 결과에 표시를 해두거나, 교재에 표시된 부분에서 어디인지 예시(kube-scheduler-ip-xx-xx-x-xx)를 알려줘도 좋을 것 같아요.

- [ ] 추가 학습이나 심화 학습을 원하는 경우에 대한 내용 추가가 있으면 좋겠습니다

[01.md]
- [ ] 후속과정인 실전프로젝트에서는 윈도우 터미널 클라이언트도 사용하므로 해당 내용을 가이드 해주는 부분도 의미가 있을 것 같습니다
- [x] aws_ec2_11.png 내용을 실제 설정과 동일하게 변경할 필요가 있습니다. -> 이미지 변경 (ubuntu 22.04 -> ubuntu 20.04)
- [x] tree 등 패키지 설치시 어떤 내용인지 간단한 설명을 덧붙여도 좋을 것 같습니다
- [ ] 교재 등 전체 내용이 아닌 실습에 필요한 파일만 다운로드 할 수 있도록 해도 좋을 것 같습니다

[02.md]
- [ ] 개발 영역의 랩업 과정이 진행되지 않은 경우 git 관련 내용에 대한 추가 설명이 필요할 수 있습니다
- [x] 리눅스 환경에서 도커 허브 로그인시 패스워드 입력에 대한 안내가 필요합니다 (패스워드 입력시 화면 상에 보이지 않는다는 내용도 언급할 필요가 있습니다)

[03.md]
- [x] rm -f 옵션에 대한 추가 설명이 있어도 좋을 것 같습니다
- [x] 자원 정리 후 정상적으로 정리 되었는지 확인하는 방법이 항상 가이드 되어도 좋을 것 같습니다 -> 자원 정리하는 내용이 있는 곳에는 모두 정리 후 상태확인 방법을 추가함.

[04.md]
- [ ] network 명칭을 다른 이름으로 주어도 좋지 않을까 생각합니다
- [ ] SQL 로그인을 별도 터미널 세션으로 진행해도 좋을 것 같습니다

[05.md]
- [ ] 교재에 제공되는 파일 사용법에 대한 언급이 간략히 있어도 좋을 것 같습니다 (디렉토리 이동 방법 등)

[06.md]
- [ ] 현재 디렉토리 설정에 대한 내용이 있어도 좋을 것 같습니다

[07.md]
- [ ] 공식 웹사이트 링크, 치트쉬트 등 참조가 가능한 학습 자료를 제시해 주어도 좋을 것 같습니다

[08.md]
- [ ] liveness-http 컨테이너 재시작과 관련하여 상태 변경 등을 살펴보는 명령어 및 추가 설명이 교재에 있어도 좋을 것 같습니다

[09.md]
- [ ] 강의시에 설명이 잘 되고 있지만 처음으로 vi 에디터 등을 사용하는 입과자를 감안하여 준비된 참고 링크 등을 교재에 안내해 주어도 좋겠습니다

[10.md]
- [ ] 서비스 등과 같이 인그레스 생성 후에도 확인하는 명령문이 추가되면 좋겠습니다
- [ ] 호스트 파일 변경에 대한 간단한 가이드가 필요할 수 있습니다
- [ ] my-nginx.info URL이 퍼블릭 DNS에 등록되어 있고 위험한 사이트로 보여져서 다른 URL로 변경하는게 좋을 것 같습니다
- [ ] 교육 과정내 실습에 도메인을 발급받고 연결하는 과정에 대한 내용이 있다는 것을 언급해 주셔도 좋을 것 같습니다다

[11.md]
- [x] html 문서에 "Hello kubernetes" 보다는 재미있는 문구나 이미지가 들어가도 좋을 것 같습니다 -> 재ㅁㅣ 있는 페이지 구성으로 변경했습니다. 🤣
- [x] 보너스 실습에서 파드 삭제 후에도 데이터 유지되는지 확인하는 가이드가 필요합니다 -> 관련 문구를 추가했습니다.

[12.md]
- [x] MySQL 데이터베이스 내용 확인하는 명령문은 위에 실행 내용을 참조하라는 가이드가 있으면 좋겠습니다 -> 내용 추가함.

[13.md]
- [x] 지난번 데모에서 처럼 화면 분리를 통해서 모니터링 내용이 항상 보이도록 해 주면 좋겠습니다 -> 지난 데모(Windows terminal 사용)와 비슷하게 MobaXTerm을 이용해서 분할창 형태로 실습할 수 있도록 가이드 하겠습니다.

[14.md]
- [ ] 지정된 인스턴스 타입에서 빠른시간에 확인이 가능하도록 오토스케일링 구성이나 부하 생성기 설정을 조정하면 좋겠습니다
- [ ] HPA 자동 스케일링도 워치 명령어 등을 통해 다른 세션에서 모니터링을 하도록 가이드 하면 좋을 것 같습니다
  (watch -n 0.1 kubectl get all 등)
- [ ] 추가 과제로 HPA를 운영 환경에서 적용할 때의 고려사항을 확인할 수 있는 예제가 하나 더 있어도 좋을 것 같습니다

[15.md]
- [ ] 후속 과정에서 헬름 차트를 사용하는 방식을 고려하여 필요한 이론, 실습 내용이 보완되었으면 합니다
- [ ] 쿠버네티스에 익숙한 입과자를 위한 심화 학습 과제가 있으면 좋겠습니다

[16.md]
- [x] 반별 리전 구분 안내 문구는 변경이 필요합니다 -> 수정했습니다. 
- [x] EC2 인스턴스 삭제 전 시큐리티그룹 삭제가 안되는 경우에 대해서 안내가 있으면 좋겠습니다 -> 관련문구 추가했습니다.
