

# Docker & Kubernetes 노트 (MSP T2 8차)   (oﾟvﾟ)ノ

## Q&A

### Docker

- 회사에서 사용하는 VDI가 버츄얼 머신이고, sac같은것이 컨테이너일까요?
  - 네, VDI가 버츄얼머신 형태입니다.
- 리눅스에 docker 설치 시 windows에서와 같이 가상 OS(Linux kernel)가 별도로 있는 상태에서 container가 올라가나요?
  - 리눅스에서는 가상OS를 별도로 생성하지는 않고, 커널을 직접 사용합니다.
- 사내에서 개발환경으로 docker를 구성하려면 어떻게 해야하나요?
  - 1. WSL2나 Hyper-V, Virtualbox같은 가상 환경에 리눅스를 구성한 다음, [Install Docker Engine](https://docs.docker.com/engine/install/)의 방법을 적용하시면 됩니다.
  - 2. 또는 Docker desktop for windows를 비용을 지불하고 사용하는 것도 방법입니다. 

---

- Docker enterprise는 비용과 계약방식이 어떻게 되나요?
  - [Pricing & Subscriptions](https://www.docker.com/pricing/) 을 참고하세요.
- ls: cannot access '/var/lib/docker/....' 라고 에러메시지가 나옵니다.
  - /var/lib/docker 의 소유자가 root여서 그렇습니다.
  - root로 해보시거나, sudo 를 붙여서 해보세요. (교재는 sudo를 붙여서 하고 있습니다.)
```bash
ubuntu@ip-10-0-1-205:~$ ls -al /var/lib/docker
ls: cannot open directory '/var/lib/docker': Permission denied
ubuntu@ip-10-0-1-205:~$ sudo ls -al /var/lib/docker
total 52
drwx--x--- 13 root root 4096 Sep 20 00:25 .
drwxr-xr-x 42 root root 4096 Aug  9 06:31 ..
drwx--x--x  4 root root 4096 Aug  9 06:31 buildkit
drwx--x---  2 root root 4096 Sep 20 02:41 containers
drwx------  3 root root 4096 Aug  9 06:31 image
drwxr-x---  3 root root 4096 Aug  9 06:31 network
drwx--x---  9 root root 4096 Sep 20 02:41 overlay2
drwx------  4 root root 4096 Aug  9 06:31 plugins
drwx------  2 root root 4096 Sep 20 00:25 runtimes
drwx------  2 root root 4096 Aug  9 06:31 swarm
drwx------  2 root root 4096 Sep 20 02:36 tmp
drwx------  2 root root 4096 Aug  9 06:31 trust
drwx-----x  4 root root 4096 Sep 20 00:25 volumes
```

- Thin R/W Layer도 이미지인가요? 생성된 Container가 아니고
  - 이미지도 컨테이너도 레이어들로 구성되어 있습니다.
  - 이미지는 R/O 레이어들의 집합이고, 컨테이너는 이미지 레이어(R/O)에 Thin R/W Layer(Container layer)를 하나 더해서 만들어져 있습니다.
- 레이어는 데몬에서 식별해서 관리한다고 보면 되나요? 저장소는 같으니...
  - 네, 맞습니다.
  - 다른 이미지도 동일 레이어를 가지고 있을 수 있는데, 그런것들으 모두 데몬에서 구분해서 관리합니다.
- -it 옵션으로 하면 컨테이너 안으로 들어가는데(구체적으로는 컨테이너에 실행된 프로세스와 interactive하게 연결되는데), 거기서 나갈때 container가 종료되나요?
  - 종료되기도 하고(exit), 아니기도 합니다. (ctrl+p,q)
  - 아래 box1은 exit로 나온 경우이고(container가 stop된 상태), box2는 ctrl+p,q로 나온 경우(container가 여전히 running상태)입니다.
```bash
ubuntu@ip-10-0-1-205:~$ docker run -it --name box1 busybox sh
/ # exit
ubuntu@ip-10-0-1-205:~$ docker run -it --name box2 busybox sh
/ # ubuntu@ip-10-0-1-205:~$
ubuntu@ip-10-0-1-205:~$ docker ps -a
CONTAINER ID   IMAGE     COMMAND   CREATED          STATUS                      PORTS     NAMES
99afe3cc6025   busybox   "sh"      8 seconds ago    Up 7 seconds                          box2
508b37c96942   busybox   "sh"      28 seconds ago   Exited (0) 25 seconds ago             box1
```
- -d로 백그라운드 모드로 실행한 후에 컨테이너 안으로 들어가고 싶을때는 어떻게 하나요?
  - `docker exec`명령을 이용할 수 있습니다.
```bash
ubuntu@ip-10-0-1-205:~$ docker run -d --name my-nginx1 nginx
65f03883d5cf20337c93e413e035df468bc0870e3f79bd90a80e473a9f7679a6
ubuntu@ip-10-0-1-205:~$ docker exec -it my-nginx sh
Error: No such container: my-nginx
ubuntu@ip-10-0-1-205:~$ docker exec -it my-nginx1 sh
# whoami
root
# pwd
/
```

- 교재 -it의 경우 /bin/bash를 실행한다고 되어있는데, /bin/bash가 없는 이미지의 경우, 컨테이너 실행만 되나요? 맞다면 이 경우는 -d와 같다고 보면 되나요?
  - 마지막 command(/bin/bash)를 실행할 수 없는 경우는 아래와 같이 에러를 발생시킵니다. (-d로 실행되지는 않습니다.)

```bash
ubuntu@ip-10-0-1-205:~$ docker run -it busybox /bin/bash
docker: Error response from daemon: failed to create shim task: OCI runtime create failed: runc create failed: unable to start container process: exec: "/bin/bash": stat /bin/bash: no such file or directory: unknown.
ERRO[0000] error waiting for container: context canceled
ubuntu@ip-10-0-1-205:~$ docker run -it busybox sh
/ # exit
```

- 컨테이너는 OS커널영역을 공유한다고 했는데, 그럼 다른 OS가 설치된 H/W에서 OS까지 동일버젼으로 맞추려면 H/W별로 동일 OS VM을 생성해서 커널버젼을 맞추고 VM안에서 도커를 설치해야 하나요?
  - 네, 이론적으로는 그렇습니다. 

### Kubernetes
