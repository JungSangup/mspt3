# Contents

| No. | 이론                                                          | 실습                                                 |
| --- | ----------------------------------------------------------- | -------------------------------------------------- |
| 1   | [Book] 01_Docker_Overview                                   | -                                                  |
| 2   | [Book] 02_Docker_Commands                                   | [Hands-on] 02_Docker_Commands 💿                   |
| 3   | [Book] 03_Docker_Storage                                    | [Hands-on] 03_Docker_Storage                       |
| 4   | [Book] 04_Docker_Network                                    | [Hands-on] 04_Docker_Network                       |
| 5   | [Book] 05_Dockerfile                                        | -                                                  |
| 6   | [Book] 06_Dockerfile_BestPractice                           | [Hands-on] 05_Dockerfile                           |
| 7   | [Book] 07_Kubernetes_Overview                               | [Hands-on] 07_Kubernetes_Overview 💿               |
| 8   | [Book] 08_Kubernetes_Workload(1)                            | [Hands-on] 08_Kubernetes_Workload(1)               |
| 9   | [Book] 09_Kubernetes_Workload(2)                            | [Hands-on] 09_Kubernetes_Workload(2)               |
| 10  | [Book] 10_Kubernetes_Service                                | [Hands-on] 10_Kubernetes_Service                   |
| 11  | [Book] 11_Kubernetes_Storage                                | [Hands-on] 11_Kubernetes_Storage 💿                |
| 12  | [Book] 12_Kubernetes_Configuration                          | [Hands-on] 12_Kubernetes_Configuration             |
| 13  | [Book] 13_Kubernetes_Deployment_strategies                  | [Hands-on] 13_Kubernetes_Deployment_strategies     |
| 14  | [Book] 14_Kubernetes_Horizontal Pod Autoscaler              | [Hands-on] 14_Kubernetes_Horizontal Pod Autoscaler |
| 15  | [Book] 15_Helm                                              | [Hands-on] 15_Helm 💿                              |

💿 : Self Study 포함.

## 실습 시작 전 확인사항

실습을 시작하기 전 실습환경을 확인하는 방법입니다.  
이전 실습을 진행한 후 환경에 변화가 있다면 아래 내용을 체크해주세요.

- Docker 실습인 경우
  
  - [ ] 실습용 VM(EC2 또는 Virtual Server) Instance가 시작되어 있나요?
    - 아니라면, [AWS Console](https://aws.amazon.com/console/) 또는 [SCP Console](https://cloud.samsungsds.com/console)에서 VM Instance를 시작해주세요.
  - [ ] 터미널 S/W(e.g. Windows terminal, MobaXTerm etc.)로 실습용 VM Instance에 접속해주세요.
    - 혹시 안된다면 Security group을 확인해보세요. (지금 내 환경에서 22번 포트로 접속 가능한 조건인지)

- Kubernetes 실습인 경우
  
  - [ ] 실습용 VM(EC2 또는 Virtual Server) Instance가 시작되어 있나요?
    - 아니라면, [AWS Console](https://aws.amazon.com/console/) 또는 [SCP Console](https://cloud.samsungsds.com/console)에서 VM Instance를 시작해주세요.
  - [ ] 터미널 S/W(e.g. Windows terminal, MobaXTerm etc.)로 실습용 VM Instance에 접속해주세요.
    - 혹시 안된다면 Security group을 확인해보세요. (지금 내 환경에서 22번 포트로 접속 가능한 조건인지)
  - [ ] Minikube가 시작되어 있나요? (`minikube status`명령어로 확인가능)
    - 아니라면, 아래 명령어로 Minikube를 시작해주세요.
    - `minikube start --driver=none --kubernetes-version=v1.23.0 --addons=ingress,metrics-server`
