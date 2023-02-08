# mspt3 - Docker & Kubernetes

### 디렉토리 구조 및 설명

| 디렉토리             | 설명                                   |
|:---------------- |:------------------------------------ |
| ./doc            | 이론/실습 교재 (markdown)                  |
| ./doc/themes     | 교재(md)의 slides 형식을 위한 themes (css적용) |
| ./doc/img        | 교재에 사용된 이미지                          |
| ./doc/pdf        | 교재의 slides 형식 출력본(pdf)               |
| ./hands_on_files | 실습에 사용되는 파일들                         |
| ./qna            | 각 차수별 메모와 질문/답변                      |

---

### 이론 교재 Contents

| No. | 이론교재                                                                                                                   |
|:---:|:---------------------------------------------------------------------------------------------------------------------- |
| 1   | [[Book] 01_Docker_Overview.md](./doc/%5BBook%5D%2001_Docker_Overview.md)                                               |
| 2   | [[Book] 02_Docker_Commands.md](./doc/%5BBook%5D%2002_Docker_Commands.md)                                               |
| 3   | [[Book] 03_Docker_Storage.md](./doc/%5BBook%5D%2003_Docker_Storage.md)                                                 |
| 4   | [[Book] 04_Docker_Network.md](./doc/%5BBook%5D%2004_Docker_Network.md)                                                 |
| 5   | [[Book] 05_Dockerfile.md](./doc/%5BBook%5D%2005_Dockerfile.md)                                                         |
| 6   | [[Book] 06_Dockerfile_BestPractice.md](./doc/%5BBook%5D%2006_Dockerfile_BestPractice.md)                               |
| 7   | [[Book] 07_Kubernetes_Overview.md](./doc/%5BBook%5D%2007_Kubernetes_Overview.md)                                       |
| 8   | [[Book] 08_Kubernetes_Workload(1).md](./doc/%5BBook%5D%2008_Kubernetes_Workload(1).md)                                 |
| 9   | [[Book] 09_Kubernetes_Workload(2).md](./doc/%5BBook%5D%2009_Kubernetes_Workload(2).md)                                 |
| 10  | [[Book] 10_Kubernetes_Service.md](./doc/%5BBook%5D%2010_Kubernetes_Service.md)                                         |
| 11  | [[Book] 11_Kubernetes_Storage.md](./doc/%5BBook%5D%2011_Kubernetes_Storage.md)                                         |
| 12  | [[Book] 12_Kubernetes_Configuration](./doc/%5BBook%5D%2012_Kubernetes_Configuration.md)                                |
| 13  | [[Book] 13_Kubernetes_Deployment_strategies.md](./doc/%5BBook%5D%2013_Kubernetes_Deployment_strategies.md)             |
| 14  | [[Book] 14_Kubernetes_Horizontal Pod Autoscaler.md](./doc/%5BBook%5D%2014_Kubernetes_Horizontal%20Pod%20Autoscaler.md) |
| 15  | [[Book] 15_Helm.md](./doc/%5BBook%5D%2015_Helm.md)                                                                     |

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
