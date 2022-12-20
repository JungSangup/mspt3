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


---

### Kubernetes

- ...
- 
