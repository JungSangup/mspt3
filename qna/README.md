# Docker & Kubernetes 노트 (MSP T2 6차)


[https://github.com/JungSangup/mspt2](https://github.com/JungSangup/mspt2)

## Q&A
- 도커 이미지에서 여러 OS가 사용되는데, 도커가 구동되는 OS와 충돌이나 호환성 이슈는 없나요?
  - 커널영역을 공유하고, 그 위에서 다양한 배포판들이 구동되기 때문에 서로의 영역이 다릅니다. ( [참고](https://bluese05.tistory.com/10) )
- 격리수준이 VM보다 떨어지면 실제 운영시 장애전파에 대한 대비책같은게 있을까요?
  - [Docker security](https://docs.docker.com/engine/security/) 참조
- EC2? 어제 생성한 것 사용 가능한가요? (어제는 Windows)
- 도커 올릴 수 있는 Host OS는 Linux만 가능한가요? 윈도우 컨테이너는?
  - [Windows and Containers](https://docs.microsoft.com/en-us/virtualization/windowscontainers/about/)
- 단일 컨테이너만 동작하는 경우 Host리소스를 최대로 사용하도록 설정 가능한가요?
  - [Runtime options with Memory, CPUs, and GPUs](https://docs.docker.com/config/containers/resource_constraints/#:~:text=By%20default%2C%20a%20container%20has,of%20the%20docker%20run%20command.) 참조
- 위와 같은 경우 한계점은?
- 컨테이너가 요청하는 총 리소스의 합이 하드웨어 총량을 초과하면?
- EC2에서 Command 'docker' not found 라고 나오면?

---

- 레이어드파일시스템이 nfs, xfs와 다른건가요?
- 컨테이너의 R/W, R/O(공유)레이어를 구분하는 기준은 뭔가요?
- nginx 이미지를 만들때 사용한 도커파일은 명령어가 6개 있어서 레이어가 6층인가요? (무한대면 무한태로 늘어나나요?)
- docker run 할 때 name은 임의로 생성되나요?
  - --name my-nginx 와 같이 지정 가능합니다. [Assign name and allocate pseudo-TTY (--name, -it)](https://docs.docker.com/engine/reference/commandline/run/#assign-name-and-allocate-pseudo-tty---name--it) 참조.
- 개발/운영할 때 레이어단위로 관리하거나 레이어별로 분석하거나 하는 경우도 있나요?
- R/O 레이어에서 필요없어진 파일을 다음 레이어에서 삭제하거나, 용량이 큰 이전버젼 레이어를 수정하는 경우에도 도커 이미지 안에는 모든 파일이 존재하는건가요?
  - [multi-line arguments , reducing clutter](https://github.com/JungSangup/mspt2/blob/main/doc/%5BBook%5D%2006_Dockerfile_BestPractice.md#multi-line-arguments--reducing-clutter) 에 사례가 있습니다. ("추가 패키지를 설치했다가 나중에 패키지를 제거할 수도 있지만 이러한 방법으로는 충분하지 않습니다. Dockerfile의 각 명령이 하나의 레이어를 생성하므로, 이미지를 만든 단계보다 나중의 단계에서 이미지의 데이터를 제거해도 전체 이미지의 크기는 줄어들지 않습니다. ***데이터가 더 깊은 레이어에 숨겨질 뿐 여전히 존재합니다.***")
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
  - 리눅스 커널 외에 추가로 필요한 것들이 포함된 것을 Base image로 해서 나의 이미지를 만들면 됩니다. 이 때 가벼운 리눅스 배포판을 base로 하거나, jdk같은것을 사용할 수 있습니다.
- Play with Docker에서는 안되나요?
- tag와 name차이?
- detach/foregroud 차이? 
- docker hub 가입해야하나요?

---

- docker stop되어도 데이터가 사라지지 않는 것 아닌가요?
  - stop되어도 R/W Layer는 사라지지 않습니다. `docker rm`명령어를 써서 삭제할 때 데이터도 사라집니다.
- volume -> 설정 (e.g. NAS같은걸 사용할 때) 어떻게 하나요?
  - [Share data among machines](https://docs.docker.com/storage/volumes/#share-data-among-machines)
- volume 용량제한 가능한가요?
  - [Driver-specific options](https://docs.docker.com/engine/reference/commandline/volume_create/#driver-specific-options)
- 회사에서 -v로 그냥 nas를 붙였는데, 아마도 bind mount였나보네요. ㅎㅎ
- killercoda 에서 cmd delete안되게 가능한가요? 전에 실습한 내용이 사라져요...
- volume경로는 어떻게 지정하나요? 아니면 정해져있나요?
  - default 경로는 지정되어 있습니다. (/var/lib/docker/volumes/)
  - 경로를 변경하려면 dockerd 실행 시 옵션 (https://docs.docker.com/engine/reference/commandline/dockerd/ , --data-root) 으로 변경할 수 있습니다.
- Unable to find image 'yss:latest'... pull access denied for yss... repository does not exist or may require 'docker login'... 해결해주세요~
- --publish 3000:3000 앞은 호스트머신 포트인건 알겠는데, 뒤의 3000는 뭔가요?
- docker attach, ctrl+c , exit 동작
  - [docker attach](https://docs.docker.com/engine/reference/commandline/attach/)  -> ctrl+c는 SIGKILL , ctrl+p ctrl+q는 container를 running상태로 두고 detach
- volume이 지워지는 시점이 다로 있거나 정책같은게 있나요?
  - [Prune unused Docker objects](https://docs.docker.com/config/pruning/) 참조. 이걸 주기적으로 실행할 수도 있을 것 같습니다.
---

- bridge / host 차이 좀 다시 설명해주세요
- ifconfig에서 bridge를 어떻게 식별하나요?
- 교재에 hands-on 링크가 있나요?
- container 한 개가 두 개 이상 bridge에 연결 가능한가요?
  - [docker network connect](https://docs.docker.com/engine/reference/commandline/network_connect/) 명령어로 가능.
  - (1) bridge network 2개(my-bridge1, my-bridge2) 생성 > (2) my-bridge1에 연결된 my-nginx 생성 > (3) my-bridge2를 my-nginx에 연결
```bash
ubuntu $ docker network create my-bridge1
28068e76692ed1a0e01894253eb2f4ba59fc9b92c8c0c9f015069dd4ed92955d
ubuntu $ docker network create my-bridge2
bc40ca60e09aa0ad1389a56df646bc5f4972d615943b91409b70518cad898f85

ubuntu $ docker network ls
NETWORK ID     NAME         DRIVER    SCOPE
085e570ce889   bridge       bridge    local
a899d8446de2   host         host      local
28068e76692e   my-bridge1   bridge    local
bc40ca60e09a   my-bridge2   bridge    local
75c0cb25cf9e   none         null      local

ubuntu $ docker run -d -p 8080:80 --name my-nginx --network my-bridge1 nginx
097e83f590e2174903b474c71a05ce22278bb0b5a01dc1c4d892f375b8c2c34b

ubuntu $ docker network connect my-bridge2 my-nginx

ubuntu $ docker inspect my-nginx
[
...생략...
            "Networks": {
                "my-bridge1": {
                    "IPAMConfig": null,
                    "Links": null,
                    "Aliases": [
                        "097e83f590e2"
                    ],
                    "NetworkID": "28068e76692ed1a0e01894253eb2f4ba59fc9b92c8c0c9f015069dd4ed92955d",
                    "EndpointID": "c2039c93b10862c906e4403acecb1e37ec8f8ea63ca62320517cc077f5527399",
                    "Gateway": "172.18.0.1",
                    "IPAddress": "172.18.0.2",
                    "IPPrefixLen": 16,
                    "IPv6Gateway": "",
                    "GlobalIPv6Address": "",
                    "GlobalIPv6PrefixLen": 0,
                    "MacAddress": "02:42:ac:12:00:02",
                    "DriverOpts": null
                },
                "my-bridge2": {
                    "IPAMConfig": {},
                    "Links": null,
                    "Aliases": [
                        "097e83f590e2"
                    ],
                    "NetworkID": "bc40ca60e09aa0ad1389a56df646bc5f4972d615943b91409b70518cad898f85",
                    "EndpointID": "0c25934cad9efd24423833d16e9222f6d66d36f9072f5ac99f3994e5ffdb9ff7",
                    "Gateway": "172.19.0.1",
                    "IPAddress": "172.19.0.2",
                    "IPPrefixLen": 16,
                    "IPv6Gateway": "",
                    "GlobalIPv6Address": "",
                    "GlobalIPv6PrefixLen": 0,
                    "MacAddress": "02:42:ac:13:00:02",
                    "DriverOpts": {}
                }
            }
        }
    }
]
```
- volume/db 저장 어디 옵션으로 결정되나요?
- docker0와 내가 만든 port가 충돌되면?
- docker run 할 때 dockerfile의 label은 동일한가요?
- 이론교재 마지막 web과 db를 다른 브릿지에 구성했는데, 다른 브릿지간 라우팅 설정도 가능한가요?
  - 위의 `docker network connect` 명령어를 이용하면 컨테이너에 다수의 bridge를 연결할 수 있습니다. 이 방법을 사용하면 가능할 것 같습니다. 더 좋은 방법은 아직 찾질 못했습니다. ㅠㅠ
