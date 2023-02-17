
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
[Contents](https://github.com/JungSangup/mspt3/tree/main/doc#contents) 의 순서에 따라 이론과 실습을 진행합니다.  
각 이론교재는 기본 내용을 포함하고 있고, 심화학습은 교재의 링크를 참고하시면 됩니다.  


## 실습환경
...

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

- [ ] powershell, mac 기본 terminal 기준으로  

- [x] 리눅스 명령어 정리 -> https://github.com/JungSangup/mspt3/blob/main/doc/%5BAppendix%5D%20%EB%A6%AC%EB%88%85%EC%8A%A4%EB%AA%85%EB%A0%B9%EC%96%B4.md 추가

- [x] 각 실습 마지막 정리하기 추가 -> 교재에 내용 반영완료.

- [x] pdf, powershell 복사/붙여넣기 이슈 -> 실습교재는 Github의 md파일로 진행. (code block 복사하기 버튼 활용)  

- [x] Editor는 VI를 사용. (가이드 활용) -> 강의 중 기본 사용법 안내. 

- [x] minikube start 시 오류 (/etc/docker)  -> Ubuntu, Docker, Minikube, Kubernetes 버젼을 특정해서 진행. (오류가 발생하지 않는 버젼의 조합 사용)

- [ ] 터미널 프로그램 통일  

- [x] 실습파일 챕터별로 번호(?) 부여  -> hands_on_files/chxx 로 챕터별 실습파일 위치를 나누고, 교재에 반영함.

- [x] 전체 교육과정 맵 제공  -> 각 차수별 qna에 맵을 제공하고, 진행 중 공유

- [ ] 사전준비 ( Docker Hub가입, 터미널 툴 설치 )  

- [x] 실습 시작 전 체크사항 정리 (EC2접속해서 어느 디렉토리로 이동하고... 등) -> https://github.com/JungSangup/mspt3/blob/main/doc/README.md 에 정리함.(매일 실습 전 함께 체크하고 진행)  

- [x] 일자가 바뀌어도 실습환경 유지가 되도록 주의. (EC2 stop하지 말고 3일단 유지) -> 가능하면 stop하지 않도록 안내하고, stop해도 다시 시작 가능한 방안(실습시작 전 체크사항) 제공. 

- [ ] 진행 시 화면 해상도 적정하게 
