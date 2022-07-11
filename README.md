# mspt2 - Docker & Kubernetes

### 디렉토리 구조 및 설명
| 디렉토리 | 설명 |
| :--- | :--- |
| ./doc | 이론/실습 교재 (markdown) |
| ./doc/themes | 교재(md)의 slides 형식을 위한 theme (css적용) |
| ./doc/img | 교재에 사용된 이미지 |
| ./doc/ppt | 교재의 slides 형식 출력본(ppt) |
| ./doc/pdf | 교재의 slides 형식 출력본(pdf) |
| ./hands_on_files | 실습교재에 사용되는 파일들 |



---



### 이론/실습 교재 Contents

| No. | 이론교재 | 실습교재 |
| :---: | :--- | :--- |
| 1 | [Book] 01_Docker_Overview.md | [Hands-on] 01_Docker_Intro.md<br>[Hands-on] 02_Docker_Layers.md |
| 2 | [Book] 02_Docker_Commands.md | [Hands-on] 03_Docker_Commands.md |
| 3 | [Book] 03_Docker_Storage.md | [Hands-on] 04_Docker_Volumes.md |
| 4 | [Book] 04_Docker_Network.md | [Hands-on] 05_Docker_Network.md |
| 5 | [Book] 05_Dockerfile.md | - |
| 6 | [Book] 06_Dockerfile_BestPractice.md | [Hands-on] 06_Dockerfile.md |
| 7 | [Book] 07_Kubernetes_Overview.md | [Hands-on] 07_Kubernetes_Overview.md |
| 8 | [Book] 08_Kubernetes_Workload(1).md | [Hands-on] 08_Kubernetes_Workload(1).md |
| 9 | [Book] 09_Kubernetes_Workload(2).md | [Hands-on] 09_Kubernetes_Workload(2).md |
| 10 | [Book] 10_Kubernetes_Service.md | [Hands-on] 10_Kubernetes_Service.md |
| 11 | [Book] 11_Kubernetes_Deployment_strategies.md | [Hands-on] 11_Kubernetes_Deployment_strategies.md |
| 12 | [Book] 12_Kubernetes_ConfigMaps & Secrets.md | [Hands-on] 12_Kubernetes_ConfigMaps & Secrets.md |
| 13 | [Book] 13_Kubernetes_Horizontal Pod Autoscaler.md | [Hands-on] 13_Kubernetes_Horizontal Pod Autoscaler.md |
| 14 | [Book] 14_Kubernetes_Volume.md | [Hands-on] 14_Kubernetes_Volume.md |
| 15 | [Book] 15_Helm.md | [Hands-on] 15_Helm.md |





---



### 교재(markdown with marp) 작성/수정 및 출력(ppt,pdf) 방법

교재(.doc/*.md)는 모두 [markdown](https://www.markdownguide.org/)문서로 작성되었고, slide 형식(ppt, pdf)으로 출력하기 위해서 [Marp](https://marp.app/)를 사용함.

본 교재의 수정과 출력이 필요한 경우

- [Visual Studio Code](https://code.visualstudio.com/)
- [Marp for VS Code](https://marketplace.visualstudio.com/items?itemName=marp-team.marp-vscode)

설치가 필요함.

Visual Studio Code 설치/실행 후 Extension 에서 marp 검색 후 Install
![](doc/img/marp.png)


#### 교재 작성/수정 방법

Visual Studio Code 에서 File > Open Folder 실행 후 본 Repository 최상위 경로를 선택

![](doc/img/VSCode1.png)

열기를 했을 때 아래와 같아야 함. (.vscode , doc , hands_on_files 디렉토리가 보여야 함.)

![](doc/img/VSCode2.png)


이후 편집 과정은 일반적인 Markdown 문서와 동일함.
편집 과정 중 문서 우측상단의 **Preview** 버튼을 클릭하여 출력형태를 확인할 수 있음.

![](doc/img/VSCode4.gif)


#### Slide 형식의 파일(ppt, pdf)로 출력하는 방법

편집을 마친 Markdown 문서의 출력(e.g. ppt, pdf)은 아래와 같이 Marp 아이콘 클릭 후 Export Slide Deck 선택  
이후 출력형식과 디렉토리 선택 후 실행.

![](doc/img/marp_export.gif)