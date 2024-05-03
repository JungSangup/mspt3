
# [Hands-on] 03. Docker storage

![](./img/hands_on.png)

<br>

# Contents

**[1. Container layer (R/W layer)](#1-container-layer-rw-layer)**  
**[2. Volume을 이용하여 Todo app. 실행하기](#2-volume을-이용하여-todo-app-실행하기)**

---

## 1. Container layer (R/W layer)

앞서 우리는 도커가 사용하는 레이어 파일시스템에 대해 알아보았습니다.  
컨테이너가 실행될 때 마다 새로운 레이어(Container layer, R/W layer)가 생성되고, 컨테이너가 삭제될 경우 그 레이어의 내용은 사라지게 됩니다.

직접 한번 컨테이너를 생성해서 볼까요?  
먼저 ubuntu 컨테이너를 하나 실행합니다.
```bash
ubuntu@ip-172-31-23-60:~$ docker run --name my-ubuntu1 --detach ubuntu bash -c "echo 'Hello Docker...' > /test.txt && tail -f /dev/null"
69a984336a12e4d186399730f1ff3f51300caf95ea9d70a59a235c334da26ea2
```

> 💻 명령어
>```bash
>docker run --name my-ubuntu1 --detach ubuntu bash -c "echo 'Hello Docker...' > /test.txt && tail -f /dev/null"
>```

"Hello Docker..." 라는 문자열을 담은 txt파일(**/test.txt**)을 하나 만들고, 컨테이너를 **running**상태로 두기 위해서 `tail`명령을 실행했습니다.

<br><br><br>

이제 우리가 만든 파일을 한 번 볼까요?
```bash
ubuntu@ip-172-31-23-60:~$ docker exec my-ubuntu1 cat /test.txt
Hello Docker...
```

> 💻 명령어
>```bash
>docker exec my-ubuntu1 cat /test.txt
>```

<br>

우리가 적어놓은 테스트문구("Hello Docker...")가 보일거예요.  
컨테이너가 실행되는 동안 처리된 내용이기 때문에 Container layer에 이 내용이 기록되게 됩니다.

<br><br><br>

이제 같은 ubuntu이미지를 이용해서 새로운 컨테이너를 실행하고, test.txt파일이 있나 살펴봅시다.
```bash
ubuntu@ip-172-31-23-60:~$ docker run -it --name my-ubuntu2 ubuntu ls /
bin   dev  home  lib32	libx32	mnt  proc  run	 srv  tmp  var
boot  etc  lib	 lib64	media	opt  root  sbin  sys  usr
```

> 💻 명령어
>```bash
>docker run -it --name my-ubuntu2 ubuntu ls /
>```

당연히 없겠죠... 왜 그럴까요? (◔,◔o)

우리가 앞에서 알아본 **레이어**의 개념을 잘 떠올려 보세요.

앞에서 테스트한 컨테이너는 삭제하고 다음으로 넘어갈게요.  
아래 명령어를 실행해주세요.  
```bash
ubuntu@ip-172-31-23-60:~$ docker rm --force $(docker ps -a -f "name=my-ubuntu1" -f "name=my-ubuntu2" -q)
06388b0ee44f
69a984336a12
```

> 💻 명령어
>```bash
>docker rm --force $(docker ps -a -f "name=my-ubuntu1" -f "name=my-ubuntu2" -q)
>```
❗ `--force`옵션 ([Force-remove a running container (--force)](https://docs.docker.com/engine/reference/commandline/rm/#force))은 running상태의 컨테이너를 강제로 삭제하는 옵션입니다.

<br>

정리 후 상태는 아래와 같습니다.
```bash
ubuntu@ip-172-31-23-60:~/app$ docker ps --all
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES
ubuntu@ip-172-31-23-60:~/app$
```

> 💻 명령어
>```bash
>docker ps --all
>```

<br><br><br><br><br>

## 2. Volume을 이용하여 Todo app. 실행하기

이제 도커 볼륨(Volume)을 이용해서 데이터를 유지하는 방법을 알아보겠습니다.

우리 샘플 애플리케이션(Todo List Manager)은 SQLite database를 사용하고 있습니다.  
데이터는 `/etc/todos/todo.db` 에 파일로 저장이 되고 있구요.

이제 도커 볼륨을 이용해서 데이터가 저장되는 위치를 host 머신의 경로로 바꿔보겠습니다.

먼저 도커 볼륨을 하나 생성합니다.
```bash
ubuntu@ip-172-31-23-60:~$ docker volume create todo-db
todo-db
```

> 💻 명령어
>```bash
>docker volume create todo-db
>```

생성된 볼륨을 확인하려면 아래 명령어를 사용하면 됩니다.
```bash
ubuntu@ip-172-31-23-60:~$ docker volume list
DRIVER    VOLUME NAME
local     todo-db
```

> 💻 명령어
>```bash
>docker volume list
>```
>또는
>```bash
>docker volume ls
>```

<br><br><br>

그리고, 볼륨의 더 자세한 정보를 알아보려면 아래 명령어를 사용하면 됩니다.
```bash
ubuntu@ip-172-31-23-60:~$ docker volume inspect todo-db
[
    {
        "CreatedAt": "2023-02-12T05:10:45Z",
        "Driver": "local",
        "Labels": {},
        "Mountpoint": "/var/lib/docker/volumes/todo-db/_data",
        "Name": "todo-db",
        "Options": {},
        "Scope": "local"
    }
]
```

> 💻 명령어
>```bash
>docker volume inspect todo-db
>```

**Mountpoint**가 바로 실제 데이터가 저장되는 Host 머신의 위치입니다.

<br><br><br>

이제 방금 생성한 볼륨을 우리 애플리케이션의 데이터 저장경로로 마운트해서 실행해 보겠습니다.  
- Private repository의 이미지를 사용할 경우 로그인(`docker login -u [USER-NAME]`)이 필요합니다.
```bash
ubuntu@ip-172-31-23-60:~$ docker run --detach --publish 3000:3000 --volume todo-db:/etc/todos --name my-todo-manager rogallo/todo-app:1.0.0
9649bfa4eea279378bd7ddd21804ffc0028adb873759b7efa83053ca1627dc9f
```

> 💻 명령어
>```bash
>docker run --detach --publish 3000:3000 --volume todo-db:/etc/todos --name my-todo-manager [USER-NAME]/todo-app:1.0.0
>```
> [USER-NAME] 에는 여러분의 정보로 채워넣어 주세요.
- **[USER-NAME]/todo-app:1.0.0** 이 준비가 안된 경우, **rogallo/101-todo-app:1.0.0**을 이용해주세요. login 없이 사용가능한 public repository의 이미지입니다.

<br>

`--volume todo-db:/etc/todos`에서 콜론(:)을 구분자로 사용해서 앞에는 volume의 이름을, 뒤에는 마운트할 컨테이너의 경로를 적어줍니다.
> 또는 `--mount source=todo-db,target=/etc/todos` 로 옵션을 설정해도 됩니다.

<br><br><br>

이제 실행된 애플리케이션에 접속하고 오늘 할 일을 몇 개 적어볼까요?  
- 실습환경이 AWS인 경우
  - EC2 인스턴스의 **Public IPv4 address**로 접속합니다. (e.g. http://[Public IPv4 address]:3000/)
  - Security group의 Inbound rule에 3000번 포트에 대한 규칙이 있어야 합니다.
- 실습환경이 SCP인 경우
  - Virtual Server의 **NAT IP**로 접속하면 됩니다. (e.g. http://[NAT IP]:3000/)
  - Firewall과 Security group의 Inbound rule에 3000번 포트에 대한 규칙이 있어야 합니다.

![h:200](./img/todo-list-sample3.png)

그리고, 컨테이너를 멈추고 삭제합니다.
```bash
ubuntu@ip-172-31-23-60:~$ docker stop my-todo-manager
my-todo-manager
ubuntu@ip-172-31-23-60:~$ docker rm my-todo-manager
my-todo-manager
```

> 💻 명령어
>```bash
>docker stop my-todo-manager
>```
>```bash
>docker rm my-todo-manager
>```
> - 컨테이너는 생성할때 --name 옵션으로 이름을 정하면, 이후에 이 이름을 이용할 수 있습니다.

<br><br><br>

이전 같으면(Volume을 사용하지 않았을 때는) 방금 저장한 할 일이 모두 사라지고 없겠죠?

이제 다시한번 같은 명령어로 우리 애플리케이션을 실행해 볼까요?
```bash
ubuntu@ip-172-31-23-60:~$ docker run --detach --publish 3000:3000 --volume todo-db:/etc/todos --name my-todo-manager rogallo/todo-app:1.0.0
c6859ec898566de2e194acd1dd7b1df8832fe035e7e1179deb21c587e66502c9
```

> 💻 명령어
>```bash
>docker run --detach --publish 3000:3000 --volume todo-db:/etc/todos --name my-todo-manager [USER-NAME]/todo-app:1.0.0
>```
> [USER-NAME] 에는 여러분의 정보로 채워넣어 주세요.
- **[USER-NAME]/todo-app:1.0.0** 이 준비가 안된 경우, **rogallo/101-todo-app:1.0.0**을 이용해주세요. login 없이 사용가능한 public repository의 이미지입니다.

그리고 다시 우리 애플리케이션으로 접속해보세요.

어떤가요? 오늘 할 일 목록이 그대로 남아있나요? **정국이와 저녁식사**도 **장보기**도 잊지않고 할 수 있게 되었습니다.

![h:250](./img/todo-list-sample3.png)

이제, 좀 제대로 된 애플리케이션이 된 것 같네요.... (ง˙∇˙)ว

마찬가지로 정리하고 마칠게요.
```bash
ubuntu@ip-172-31-23-60:~$ docker rm --force my-todo-manager
my-todo-manager
ubuntu@ip-172-31-23-60:~$ docker volume rm todo-db
todo-db
```

> 💻 명령어
>```bash
>docker rm --force my-todo-manager
>```
>```bash
>docker volume rm todo-db
>```

<br>

정리 후 상태는 아래와 같습니다.
```bash
ubuntu@ip-172-31-23-60:~/app$ docker ps --all
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES
ubuntu@ip-172-31-23-60:~/app$
```

> 💻 명령어
>```bash
>docker ps --all
>```