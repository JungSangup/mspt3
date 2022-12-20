# Docker & Kubernetes 노트 (MSP T2 11차) ( ﾉ ﾟｰﾟ)ﾉ

## Q&A

### Docker

- docker는 기본적으로 host의 OS 기반으로 동작한다고 하셨는데요 그럼 windows 기반에서 docker image를 만들면 이 이미지를 linux 기반 환경에서 쓸 수 있는건가요?
  - Windows는 Windows container가 따로 있습니다. Windows OS를 사용하는 컨테이너이고, 이미지를 만드는 환경 보다는 거기 사용된 Base 이미지에 따라 나뉩니다.
  - [Windows and Containers](https://docs.microsoft.com/en-us/virtualization/windowscontainers/about/) 참고하세요.


- docker commit 명령어로 새롭게 생성된 이미지를 실행하면(컨테이너를 만들면), 그 때도 새로운 컨테이너 이미지가 만들어지는 건가요?
  - 네, 맞습니다.
  - 예를들어 최초 이미지가 5개의 레이어를 가지고 있고, 그 이미지를 실행한 뒤(이 때 하나의 R/W 레이어가 만들어짐.) `docker commit`명령어를 이용해 이미지를 만들면 6개의 R/O 레이어를 가진 이미지가 됩니다.
  - 그 다음 새롭게 만들어진 이미지를 실행하면, 이번에는 6개의 R/O 레이어에 1개의 새로운 R/W 레이어가 더 만들어지게 됩니다.

- volume에 저장되는 범위는 mount된 container전체가 저장되는건가요? 혹은 DB처럼 data의 일부역역이 mount되어 저장되는건가요?
  - 일부영역 입니다.
  - 얘를들어 우리 hands-on의 내용(`docker run --detach --publish 3000:3000 --volume todo-db:/etc/todos --name my-todo-manager rogallo/101-todo-app:1.0.0`)처럼 실행하면, my-todo-manager 컨테이너 전체가 아니라 `/etc/todos`라는 디렉토리만 `todo-db` 볼륨에 마운트 됩니다.

- 하나의 Bridge network안에 여러 subnet이 있을 수 있나요?
  - 그건 안되나봅니다. ㅠㅠ 아래처럼 에러(doesn't support multiple subnets)가 발생하네요.
```
$ docker network create --driver=bridge --subnet=172.19.0.0/16 --subnet=172.20.0.0/16 br0
Error response from daemon: bridge driver doesn't support multiple subnets
```

- `docker run -d --volume todo-my-sql-data:/var/lib/mysql` 실행 후에 `ls /var/lib/mysql`해보면 조회되지 않는데, 생성되는게 아닌가요?
  - 그 위치는 컨테이너의 파일시스템 위치입니다. 호스트머신이 아닌 컨테이너에서 명령어를 실행해보면 보입니다.
  - `docker exec -it OOOO sh -c "ls /var/lib/mysql"`

- docker:Error response from daemon:pull access denied for jj/101-todo-app... 에러가 발생합니다.
  - jj/101-todo-app을 pull해오는 권한이 없어서인 것 같습니다.
  - 위의 repository를 public으로 설정하거나, 아니면 jj에 권한이 있는 계정으로 먼저 `docker login`후에 실행하시면 됩니다.

- 동일한 호스트 내에 있는 컨테이너는 외부에서 모두 같은 host ip로만 접근 가능한가요? 다른 공인 ip를 할당할 수도 있나요?
  - 방법은 여러가지가 있을 수 있습니다.
  - host 머신이 여러 ip를 가지도록 어댑터를 구성하거나
  - host 머신 앞쪽에 다른 proxy를 구성하거나
  - 하는 방법들이 있을 수 있습니다.


- my_bridge와 같은 새로운 비리지 네트워크 생성 시 subnet 대역은 지정하지 않으면 자동으로 설정되나요?
  - 지정하지 않으면 자동으로 지정되고,
  - `--subnet` 옵션으로 지정해서 생성할 수도 있습니다.
  - `docker network create --driver=bridge --subnet=192.168.0.0/16 br0` 이런 식으로 가능합니다.

- CI/CD
![](img/cicd_11.png)

---

### Kubernetes

- ...
- 
