# mspt2 - Docker & Kubernetes

### 디렉토리 구조 및 설명
| 디렉토리 | 설명 |
| :--- | :--- |
| ./doc | 이론/실습 교재 (markdown) |
| ./doc/themes | 교재(md)의 slides 형식을 위한 theme (css적용) |
| ./doc/img | 교재에 사용된 이미지 |
| ./doc/pdf | 교재의 slides 형식 출력본(pdf) |
| ./hands_on_files | 실습교재에 사용되는 파일들 |
| ./qna | 각 차수별 Note와 질문/답변 |


---

### 이론/실습 교재 Contents

| No. | 이론교재 | 실습교재 | 진행시간 (min) |
| :---: | :--- | :--- | :---: |
|  1 | [[Book] 01_Docker_Overview.md](./doc/%5BBook%5D%2001_Docker_Overview.md)                                               | [[Hands-on] 01_Docker_Intro.md](./doc/%5BHands-on%5D%2001_Docker_Intro.md)<br>[[Hands-on] 02_Docker_Layers.md](./doc/%5BHands-on%5D%2002_Docker_Layers.md) | 50 |
|  2 | [[Book] 02_Docker_Commands.md](./doc/%5BBook%5D%2002_Docker_Commands.md)                                               | [[Hands-on] 03_Docker_Commands.md](./doc/%5BHands-on%5D%2003_Docker_Commands.md)                                                                           | 40 |
|  3 | [[Book] 03_Docker_Storage.md](./doc/%5BBook%5D%2003_Docker_Storage.md)                                                 | [[Hands-on] 04_Docker_Volumes.md](./doc/%5BHands-on%5D%2004_Docker_Volumes.md)                                                                             | 40 |
|  4 | [[Book] 04_Docker_Network.md](./doc/%5BBook%5D%2004_Docker_Network.md)                                                 | [[Hands-on] 05_Docker_Network.md](./doc/%5BHands-on%5D%2005_Docker_Network.md)                                                                             | 40 |
|  5 | [[Book] 05_Dockerfile.md](./doc/%5BBook%5D%2005_Dockerfile.md)                                                         | -                                                                                                                                                          | 30 |
|  6 | [[Book] 06_Dockerfile_BestPractice.md](./doc/%5BBook%5D%2006_Dockerfile_BestPractice.md)                               | [[Hands-on] 06_Dockerfile.md](./doc/%5BHands-on%5D%2006_Dockerfile.md)                                                                                     | 40 |
|  7 | [[Book] 07_Kubernetes_Overview.md](./doc/%5BBook%5D%2007_Kubernetes_Overview.md)                                       | [[Hands-on] 07_Kubernetes_Overview.md](./doc/%5BHands-on%5D%2007_Kubernetes_Overview.md)                                                                   | 50 |
|  8 | [[Book] 08_Kubernetes_Workload(1).md](./doc/%5BBook%5D%2008_Kubernetes_Workload(1).md)                                 | [[Hands-on] 08_Kubernetes_Workload(1).md](./doc/%5BHands-on%5D%2008_Kubernetes_Workload(1).md)                                                             | 50 |
|  9 | [[Book] 09_Kubernetes_Workload(2).md](./doc/%5BBook%5D%2009_Kubernetes_Workload(2).md)                                 | [[Hands-on] 09_Kubernetes_Workload(2).md](./doc/%5BHands-on%5D%2009_Kubernetes_Workload(2).md)                                                             | 40 |
| 10 | [[Book] 10_Kubernetes_Service.md](./doc/%5BBook%5D%2010_Kubernetes_Service.md)                                         | [[Hands-on] 10_Kubernetes_Service.md](./doc/%5BHands-on%5D%2010_Kubernetes_Service.md)                                                                     | 40 |
| 11 | [[Book] 11_Kubernetes_Deployment_strategies.md](./doc/%5BBook%5D%2011_Kubernetes_Deployment_strategies.md)             | [[Hands-on] 11_Kubernetes_Deployment_strategies.md](./doc/%5BHands-on%5D%2011_Kubernetes_Deployment_strategies.md)                                         | 30 |
| 12 | [[Book] 12_Kubernetes_Configmaps & Secrets](./doc/%5BBook%5D%2012_Kubernetes_ConfigMaps%20&%20Secrets.md)              | [[Hands-on] 12_Kubernetes_ConfigMaps & Secrets.md](./doc/%5BHands-on%5D%2012_Kubernetes_ConfigMaps%20&%20Secrets.md)                                       | 30 |
| 13 | [[Book] 13_Kubernetes_Horizontal Pod Autoscaler.md](./doc/%5BBook%5D%2013_Kubernetes_Horizontal%20Pod%20Autoscaler.md) | [[Hands-on] 13_Kubernetes_Horizontal Pod Autoscaler.md](./doc/%5BHands-on%5D%2013_Kubernetes_Horizontal%20Pod%20Autoscaler.md)                             | 30 |
| 14 | [[Book] 14_Kubernetes_Volume.md](./doc/%5BBook%5D%2014_Kubernetes_Volume.md)                                           | [[Hands-on] 14_Kubernetes_Volume.md](./doc/%5BHands-on%5D%2014_Kubernetes_Volume.md)                                                                       | 30 |
| 15 | [[Book] 15_Helm.md](./doc/%5BBook%5D%2015_Helm.md)                                                                     | [[Hands-on] 15_Helm.md](./doc/%5BHands-on%5D%2015_Helm.md)                                                                                                 | 40 |

---

### 실습환경
실습교재의 내용을 직접 실행해볼 수 있는 환경은 다음과 같습니다.  
환경에 따라 교재의 내용 중 일부는 실행되지 않을 수도 있습니다. (대부분은 문제없이 실행 가능합니다.)

| 실습환경 | 설명 |
| :--- | :--- |
| [Docker Desktop on Windows](https://docs.docker.com/desktop/install/windows-install/) | 학습용 환경인 경우 Windows에 Docker Desktop을 설치해서 Docker와 Kubernetes(Single-node)까지 실습해볼 수 있습니다.<br>설치 가이드 : [[Hands-on] 00_Environment_setup.md](./doc/%5BHands-on%5D%2000_Environment_setup.md) |
| [Killercoda](https://killercoda.com/brian) | 설치없이 간단히 실습해볼 수 있는 환경입니다.<br>이 과정의 실습교재 내용 대부분을 동일하게 구성해 놓았습니다. |
| [Minikube](https://minikube.sigs.k8s.io/) | 단일 노드 실행환경 입니다. 리눅스 환경(e.g. EC2 Instance)을 하나 준비한 다음 아래 가이드를 참고하여 준비합니다.<br>이 과정의 실습교재는 EC2 + Minikube를 기준으로 작성되었습니다.<br>[minikube start](https://minikube.sigs.k8s.io/docs/start/) |
| [Play with Docker](https://labs.play-with-docker.com/) | 설치없이 간단히 실습해볼 수 있는 환경입니다. (Docker) |
| [Play with Kubernetes](https://labs.play-with-k8s.com/) | 설치없이 간단히 실습해볼 수 있는 환경입니다. (Kubernetes) |  

> WSL2기반의 Docker Desktop on Windows인 경우 Docker root dir.의 위치가 아래와 같습니다.
> 탐색기에서 `\\wsl.localhost\docker-desktop-data\data\docker` 로 탐색  

> Kubernetes [프로덕션 환경](https://kubernetes.io/ko/docs/setup/production-environment/)은 더 많은 내용을 고려해야 합니다.  

