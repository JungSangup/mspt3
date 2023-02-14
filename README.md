```
 ██████   ██████  █████████  ███████████     ███████████  ████████                                  
░░██████ ██████  ███░░░░░███░░███░░░░░███   ░█░░░███░░░█ ███░░░░███                                 
 ░███░█████░███ ░███    ░░░  ░███    ░███   ░   ░███  ░ ░░░    ░███                                 
 ░███░░███ ░███ ░░█████████  ░██████████        ░███       ██████░                                  
 ░███ ░░░  ░███  ░░░░░░░░███ ░███░░░░░░         ░███      ░░░░░░███                                 
 ░███      ░███  ███    ░███ ░███               ░███     ███   ░███                                 
 █████     █████░░█████████  █████              █████   ░░████████                                  
░░░░░     ░░░░░  ░░░░░░░░░  ░░░░░              ░░░░░     ░░░░░░░░                                   
```

# Docker & Kubernetes

이 Repository는 MSP T3 과정 중 **Docker & Kubernetes** 학습을 위한 교재와 파일들의 저장소 입니다.  

### 디렉토리 구조 및 설명

| 디렉토리             | 설명                                   |
|:---------------- |:------------------------------------ |
| ./doc            | 이론/실습 교재 (markdown)                  |
| ./doc/themes     | 교재(md)의 slides 형식을 위한 themes (css적용) |
| ./doc/img        | 교재에 사용된 이미지                          |
| ./doc/pdf        | 이론교재의 slides 형식 출력본(pdf)             |
| ./hands_on_files | 실습에 사용되는 파일들                         |
| ./qna            | 각 차수별 메모와 질문/답변                      |

---



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

- A반/B반으로 나누어서 진행함.
- ubuntu 20.04 사용.
- mspt3.pem 파일처리 명령어로 안될 때 어떻게 할지... ( `icacls.exe mspt3.pem /GRANT:R "SDS:(R)"` )
- powershell, mac 기본 terminal 기준으로 
- 리눅스 명령어 정리
- 각 실습 마지막 정리하기 추가
- pdf, powershell 복사/붙여넣기 이슈
  - 실습교재는 Github의 md파일로 진행. (code block 복사하기 버튼 활용)
- Editor는 VI를 사용. (가이드 활용)
- minikube start 시 오류 (/etc/docker)
