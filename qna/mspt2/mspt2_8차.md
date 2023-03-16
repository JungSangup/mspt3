

# Docker & Kubernetes 노트 (MSP T2 8차)   (oﾟvﾟ)ノ

## Q&A

### Docker

- 회사에서 사용하는 VDI가 버츄얼 머신이고, sac(?)같은것이 컨테이너일까요?
  - 네, VDI가 버츄얼머신 형태입니다.
  - [데스크탑 가상화란?](https://www.citrix.com/ko-kr/solutions/vdi-and-daas/what-is-desktop-virtualization.html) 참고하세요.
  - "인기 있는 유형의 데스크탑 가상화는 가상 데스크탑 인프라(VDI)입니다. VDI는 호스트 기반 가상 머신(VM)을 사용하여 영구적 및 비영구적인 가상 데스크탑을 모든 종류의 연결된 기기에 제공하는 데스크탑 가상화 클라이언트-서버 모델의 변형입니다." - Citrix
- 리눅스에 docker 설치 시 windows에서와 같이 가상 OS(Linux kernel)가 별도로 있는 상태에서 container가 올라가나요?
  - 리눅스에서는 가상OS를 별도로 생성하지는 않고, 커널을 직접 사용합니다.
- 사내에서 개발환경으로 docker를 구성하려면 어떻게 해야하나요?
  - 1. WSL2나 Hyper-V, Virtualbox같은 가상 환경에 리눅스를 구성한 다음, [Install Docker Engine](https://docs.docker.com/engine/install/)의 방법을 적용하시면 됩니다.
  - 2. 또는 Docker desktop for windows를 비용을 지불하고 사용하는 것도 방법입니다. [Pricing & Subscriptions](https://www.docker.com/pricing/) 참고.

---

- Docker enterprise는 비용과 계약방식이 어떻게 되나요?
  - [Pricing & Subscriptions](https://www.docker.com/pricing/) 을 참고하세요.
- ls: cannot access '/var/lib/docker/....' 라고 에러메시지가 나옵니다.
  - /var/lib/docker 의 소유자가 root여서 그렇습니다.
  - root로 해보시거나, `sudo` 를 붙여서 해보세요. (교재는 sudo를 붙여서 하고 있습니다.)
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
- `-it` 옵션으로 하면 컨테이너 안으로 들어가는데(구체적으로는 컨테이너에 실행된 프로세스와 interactive하게 연결되는데), 거기서 나갈 때 container가 종료되나요?
  - 종료(stop)되기도 하고(exit), 아니기도 합니다. (ctrl+p,q)
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
  - [참고](https://docs.docker.com/engine/install/binaries/#prerequisites)

---

- Bind mount 시 host머신과 컨테이너에 동일한 파일이 있으면 mount가 실패하나요? 아니면 override가 되나요?
  - override 되네요. 아래 참고하세요.
  - Container의 /tmp/test.txt에는 'Test : Container'라는 문자열을 기록하고 Host의 /tmp/test.txt에는 'Test : Host'라고 기록한 후, /tmp를 마운트 하면 Host의 내용을 우선하게 됩니다.
  - [Mount into a non-empty directory on the container](https://docs.docker.com/storage/bind-mounts/#mount-into-a-non-empty-directory-on-the-container) 도 참고하세요.
```bash
ubuntu@ip-10-0-1-205:~/app$ cat Dockerfile2
FROM node:10-alpine
WORKDIR /app
COPY . .
RUN yarn install --production
RUN echo 'Test : Container' > /tmp/test.txt
CMD ["node", "/app/src/index.js"]
ubuntu@ip-10-0-1-205:~/app$ docker build -f Dockerfile2 -t todo-test .
Sending build context to Docker daemon  6.475MB
...
생략
...
 ---> Running in b2a50a769e2a
Successfully built 0bad600f4bd9
Successfully tagged todo-test:latest
ubuntu@ip-10-0-1-205:~/app$ cat /tmp/test.txt
Test : Host
ubuntu@ip-10-0-1-205:~/app$ docker run -d -v /tmp:/tmp --name my-todo todo-test
188a41a7a25ce379e2e0100bed0d73f1712a9145fd82fee44104ace2c33e8eea
ubuntu@ip-10-0-1-205:~/app$ docker exec -it my-todo cat /tmp/test.txt
Test : Host
```
  
- `docker exec`말고 -it로 run했을 때(foreground), container prompt나오는 화면으로 어떻게 들어가나요?
  - [docker attach](https://docs.docker.com/engine/reference/commandline/attach/)도 참고하세요.
  
- Volume 사용시 단점도 있나요?
  - 딱히 단점이라면, 잘 못 사용했을 경우(남발하거나, 정리를 제대로 하지 않거나) host나 다른 storage공간의 낭비요소가 될 수도 있다는 것 정도가 있을 것 같습니다.
  
- docker0가 Gateway인가요? IP는 고정인가요?
  - 네, 맞습니다. docker0는 기본 bridge network이고 g/w로 동작하며 (내부)아이피를 가지고 있습니다.

- host방식은 publish옵션으로 포워딩 설정한 것 말고, 컨테이너 내부 포트도 중복이 불가능한가요?
  - 네, 그렇습니다. (e.g. 80을 사용하는 nginx를 host방식으로 하나 실행하고 나면, 두 번째는 동일포트를 사용하기 때문에 문제가 발생합니다. (아래 예제 참고)
  - [Use host networking](https://docs.docker.com/network/host/) 참고하세요.
```bash
ubuntu@ip-10-0-1-205:~$ docker run -d --network host --name my-nginx1 nginx
0124f07b7c32f4ac0ceb1c0e59f9c13339c8991c45fcb0a9f9a002b7990a3d56
ubuntu@ip-10-0-1-205:~$ docker run -d --network host --name my-nginx2 nginx
524a51d9230ca1419f49749c79290a226c601b8e62bdd3bff3bac7ff1e99b008
ubuntu@ip-10-0-1-205:~$ docker ps -a
CONTAINER ID   IMAGE     COMMAND                  CREATED          STATUS                     PORTS     NAMES
524a51d9230c   nginx     "/docker-entrypoint.…"   8 seconds ago    Exited (1) 5 seconds ago             my-nginx2
0124f07b7c32   nginx     "/docker-entrypoint.…"   15 seconds ago   Up 14 seconds                        my-nginx1
ubuntu@ip-10-0-1-205:~$ docker logs my-nginx1
/docker-entrypoint.sh: /docker-entrypoint.d/ is not empty, will attempt to perform configuration
/docker-entrypoint.sh: Looking for shell scripts in /docker-entrypoint.d/
/docker-entrypoint.sh: Launching /docker-entrypoint.d/10-listen-on-ipv6-by-default.sh
10-listen-on-ipv6-by-default.sh: info: Getting the checksum of /etc/nginx/conf.d/default.conf
10-listen-on-ipv6-by-default.sh: info: Enabled listen on IPv6 in /etc/nginx/conf.d/default.conf
/docker-entrypoint.sh: Launching /docker-entrypoint.d/20-envsubst-on-templates.sh
/docker-entrypoint.sh: Launching /docker-entrypoint.d/30-tune-worker-processes.sh
/docker-entrypoint.sh: Configuration complete; ready for start up
2022/09/20 13:10:48 [notice] 1#1: using the "epoll" event method
2022/09/20 13:10:48 [notice] 1#1: nginx/1.23.1
2022/09/20 13:10:48 [notice] 1#1: built by gcc 10.2.1 20210110 (Debian 10.2.1-6)
2022/09/20 13:10:48 [notice] 1#1: OS: Linux 5.15.0-1019-aws
2022/09/20 13:10:48 [notice] 1#1: getrlimit(RLIMIT_NOFILE): 1048576:1048576
2022/09/20 13:10:48 [notice] 1#1: start worker processes
2022/09/20 13:10:48 [notice] 1#1: start worker process 32
2022/09/20 13:10:48 [notice] 1#1: start worker process 33
ubuntu@ip-10-0-1-205:~$ docker logs my-nginx2
/docker-entrypoint.sh: /docker-entrypoint.d/ is not empty, will attempt to perform configuration
/docker-entrypoint.sh: Looking for shell scripts in /docker-entrypoint.d/
/docker-entrypoint.sh: Launching /docker-entrypoint.d/10-listen-on-ipv6-by-default.sh
10-listen-on-ipv6-by-default.sh: info: Getting the checksum of /etc/nginx/conf.d/default.conf
10-listen-on-ipv6-by-default.sh: info: Enabled listen on IPv6 in /etc/nginx/conf.d/default.conf
/docker-entrypoint.sh: Launching /docker-entrypoint.d/20-envsubst-on-templates.sh
/docker-entrypoint.sh: Launching /docker-entrypoint.d/30-tune-worker-processes.sh
/docker-entrypoint.sh: Configuration complete; ready for start up
2022/09/20 13:10:55 [emerg] 1#1: bind() to 0.0.0.0:80 failed (98: Address already in use)
nginx: [emerg] bind() to 0.0.0.0:80 failed (98: Address already in use)
2022/09/20 13:10:55 [emerg] 1#1: bind() to [::]:80 failed (98: Address already in use)
nginx: [emerg] bind() to [::]:80 failed (98: Address already in use)
2022/09/20 13:10:55 [notice] 1#1: try again to bind() after 500ms
2022/09/20 13:10:55 [emerg] 1#1: bind() to 0.0.0.0:80 failed (98: Address already in use)
nginx: [emerg] bind() to 0.0.0.0:80 failed (98: Address already in use)
2022/09/20 13:10:55 [emerg] 1#1: bind() to [::]:80 failed (98: Address already in use)
nginx: [emerg] bind() to [::]:80 failed (98: Address already in use)
2022/09/20 13:10:55 [notice] 1#1: try again to bind() after 500ms
2022/09/20 13:10:55 [emerg] 1#1: bind() to 0.0.0.0:80 failed (98: Address already in use)
nginx: [emerg] bind() to 0.0.0.0:80 failed (98: Address already in use)
2022/09/20 13:10:55 [emerg] 1#1: bind() to [::]:80 failed (98: Address already in use)
nginx: [emerg] bind() to [::]:80 failed (98: Address already in use)
2022/09/20 13:10:55 [notice] 1#1: try again to bind() after 500ms
2022/09/20 13:10:55 [emerg] 1#1: bind() to 0.0.0.0:80 failed (98: Address already in use)
nginx: [emerg] bind() to 0.0.0.0:80 failed (98: Address already in use)
2022/09/20 13:10:55 [emerg] 1#1: bind() to [::]:80 failed (98: Address already in use)
nginx: [emerg] bind() to [::]:80 failed (98: Address already in use)
2022/09/20 13:10:55 [notice] 1#1: try again to bind() after 500ms
2022/09/20 13:10:55 [emerg] 1#1: bind() to 0.0.0.0:80 failed (98: Address already in use)
nginx: [emerg] bind() to 0.0.0.0:80 failed (98: Address already in use)
2022/09/20 13:10:55 [emerg] 1#1: bind() to [::]:80 failed (98: Address already in use)
nginx: [emerg] bind() to [::]:80 failed (98: Address already in use)
2022/09/20 13:10:55 [notice] 1#1: try again to bind() after 500ms
2022/09/20 13:10:55 [emerg] 1#1: still could not bind()
nginx: [emerg] still could not bind()
```
  
- 가상 G/W로 외부접속하는건 bridge, none은 미사용, host는 직접 host 포트사용 맞나요?
  - 네, 맞습니다. 정리를 잘 해주셨네요. ^^

- 브릿지간 통신이 가능하게 할 수 있나요?
  - 하나의 컨테이너를 다수의 브릿지에 연결할 수 있습니다.
  - 아래 예제는 box1 과 box2를 다른 브릿지에 생성하여 둘 간의 통신을 테스트하고(ping 실패), box1에 추가로 브릿지 네트워크에 연결해서 통신을 테스트(ping 성공)하는 예제입니다.
  - 마지막에 box1의 `ip addr`결과를 보세요. 두 bridge network(my-bridge1, my-bridge2)의 서브넷 아이피를 각각 가지고 있습니다. (172.20.0.2, 172.21.0.3)
  - [Use user-defined bridge networks](https://docs.docker.com/network/network-tutorial-standalone/#use-user-defined-bridge-networks)도 참고하세요.
  - 그리고, 질문주신 방법 (reverse proxy...)도 가능은 할 것 같습니다.
```bash
ubuntu@ip-10-0-1-205:~$ docker network ls
NETWORK ID     NAME         DRIVER    SCOPE
76b4b5660956   bridge       bridge    local
c475ecd75c2d   host         host      local
192a4aeccb5f   my-bridge1   bridge    local
5f7b6c129036   my-bridge2   bridge    local
03735c3155ea   none         null      local
ubuntu@ip-10-0-1-205:~$ docker run -itd --network my-bridge1 --name box1 busybox
681383e45a054a6383db2472d3f8d45a7931552c8c9f3058986c1d4788d5d19b
ubuntu@ip-10-0-1-205:~$ docker run -itd --network my-bridge2 --name box2 busybox
461be088169506c6a7d260c79749d905ea86424a512594dcb574774b2f89cc40
ubuntu@ip-10-0-1-205:~$ docker exec -it box1 ping box2
ping: bad address 'box2'
ubuntu@ip-10-0-1-205:~$ docker network connect my-bridge2 box1
ubuntu@ip-10-0-1-205:~$ docker exec -it box1 ping box2
PING box2 (172.21.0.2): 56 data bytes
64 bytes from 172.21.0.2: seq=0 ttl=64 time=0.155 ms
64 bytes from 172.21.0.2: seq=1 ttl=64 time=0.098 ms
64 bytes from 172.21.0.2: seq=2 ttl=64 time=0.092 ms
64 bytes from 172.21.0.2: seq=3 ttl=64 time=0.103 ms
64 bytes from 172.21.0.2: seq=4 ttl=64 time=0.097 ms
64 bytes from 172.21.0.2: seq=5 ttl=64 time=0.101 ms
64 bytes from 172.21.0.2: seq=6 ttl=64 time=0.091 ms
64 bytes from 172.21.0.2: seq=7 ttl=64 time=0.097 ms
64 bytes from 172.21.0.2: seq=8 ttl=64 time=0.103 ms
^C
--- box2 ping statistics ---
9 packets transmitted, 9 packets received, 0% packet loss
round-trip min/avg/max = 0.091/0.104/0.155 ms
ubuntu@ip-10-0-1-205:~$ docker exec -it box1 ip addr
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
111: eth0@if112: <BROADCAST,MULTICAST,UP,LOWER_UP,M-DOWN> mtu 1500 qdisc noqueue
    link/ether 02:42:ac:14:00:02 brd ff:ff:ff:ff:ff:ff
    inet 172.20.0.2/16 brd 172.20.255.255 scope global eth0
       valid_lft forever preferred_lft forever
115: eth1@if116: <BROADCAST,MULTICAST,UP,LOWER_UP,M-DOWN> mtu 1500 qdisc noqueue
    link/ether 02:42:ac:15:00:03 brd ff:ff:ff:ff:ff:ff
    inet 172.21.0.3/16 brd 172.21.255.255 scope global eth1
       valid_lft forever preferred_lft forever
```

- host네트워크는 직접 통신하니 bridge와 달리 docker0와 veth가 없나요?
  - 네, 맞습니다.

---

- EXPOSE에 설정된 포트와 -p옵션으로 지정한 포트가 다를경우 실제 포트 사용이 불가능한가요?
  - 네, 맞습니다.

- EXPOSE포트 설정을 build시에 하지 않을경우 -p에서만 지정하면 되나요? 아님, 둘 다 맞춰줘야 하나요?
  - Dockerfile에 EXPOSE instruction이 없어도, 컨테이너가 리슨하는 포트를 -p로 맞춰주면 정상적으로 노출됩니다.

- 도커가 host의 커널을 공유한다고 했는데, base image로 alpine같은 리눅스를 지정하는 이유는 뭔가요?
  - 커널외에 리눅스 배포판마다 달라지는 부분들이 있습니다. (예를들어 배포판 마다 달라지는 패키지 관리자나, 유틸리티 들)
  - [Small, Simple, and Secure: Alpine Linux under the Microscope](https://youtu.be/sIG2P9k6EjA) 참고하세요.

### Kubernetes

- log는 stdout이 보이는건가요?
  - 네 맞습니다.
  - [로깅 아키텍처](https://kubernetes.io/ko/docs/concepts/cluster-administration/logging/) 참조하세요.
  - [kubectl logs](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#logs) 도 참고하시구요.

- pod가 crash로 stop되었을때도 kubectl logs가 유효한가요? 아니라면 어떻게 디버깅 할 수 있나요?
  - pod파트에서 좀 더 자세히 다룰텐데요, 일단 running상태일때는 logs를 볼 수 있고 아닌 경우는 다른쪽을 봐야합니다.

---

- (Worker)node를 한 개의 Docker (host machine)으로 생각해도 되나요?
  - 네, 맞습니다. Runtime을 다른걸 쓰긴 하지만 같은 개념(컨테이너가 실행되는 머신)으로 보시면 됩니다.
  - 추가로 K8S에서는 Kublet, Kube-proxy같은 것들이 그 Node에 있습니다.

- Container의 Reasion에서 CrashLoopBackOff와 Error의 차이는 뭔가요?
  - ...
  - [What is Kubernetes CrashLoopBackOff? And how to fix it](https://sysdig.com/blog/debug-kubernetes-crashloopbackoff/)
  - [컨테이너 재시작 정책](https://kubernetes.io/ko/docs/concepts/workloads/pods/pod-lifecycle/#restart-policy)

- Pod가 먼저 생성되고 Node로 스케쥴 되나요? , 

- Try 카운트는 몇회까지 하나요? 제한할 수 있나요?  (Probe)

- Docker는 빌드/런 하는 도구이고, Containerd, CRI-O는 Runtime만 제공하는 도구라면, Docker말고 다른 빌드 도구도 있나요?
  - 네, 있습니다.

- http check는 response code로만 되나요? method는 get만 지원하나요?

---

- YAML파일로 정의한 POD Spec.의 container version(e.g. nginx의 버젼)이 달라지면 nginx 버젼만 업데이트를 하나요? 아니면, 컨테이너를 삭제 후 다시 생성하나요?
  - POD를 다시 생성하게 됩니다.

- ReplicaSet에서 배포전략이 추가된게 Deployment인가요?
  - Spec. 을 보면 말씀하신게 맞습니다.
  - 하지만, 교재에 설명드린 것 과 같이 각자의 역할이 다르긴 합니다.

- Deployment에는 node의 정의는 없나요?
  - 일반적인 경우는 스케쥴링은 K8S에 맡깁니다.
  - 하지만, 특별한 경우 Node를 지정할 수도 있습니다. (nodeSelector , affinity 를 사용하면 됩니다.)
  - [노드에 파드 할당하기](https://kubernetes.io/ko/docs/concepts/scheduling-eviction/assign-pod-node/) 참고하세요.

- Pod개수를 줄일 때 삭제되는 Pod의 기준이 있나요? (어떤것 부터?)
  - [파드 삭제 비용](https://kubernetes.io/ko/docs/concepts/workloads/controllers/replicaset/#%ED%8C%8C%EB%93%9C-%EC%82%AD%EC%A0%9C-%EB%B9%84%EC%9A%A9) 문서를 참조 바랍니다.
  - 비용을 기준으로 삭제 우선순위 지정 가능합니다.
  - 추가로 [Annotation](https://kubernetes.io/ko/docs/concepts/overview/working-with-objects/annotations/) 에 대해 아셔야 합니다.

- ReplicaSet과 Deployment가 거의 동일한 구조인 것 같은데, replicaset을 사용할 이유가 있을까요?
  - 네, 맞습니다. 결국 Deployment만 만들면 나머지가 만들어지는 구조입니다.

---

- Deployment에 pod, replicaset이 모두 포함되어 있으니 일반적인 운영환경에서는 주로 deployment를 정의해서 사용하나요?
  - 네, 맞습니다.
  - 더 나아가서는 helm이라는 패키지관리자를 사용하고, 그 패키지관리자에서 deployment를 관리합니다. (마지막시간에 배웁니다.)

- 하위개념 리소스(e.g. pod, replicaset)의 정의를 상위개념 리소스(deployment)의 정의에 포함시키지 않고, 다른 yaml파일의 내용을 참조할 수도 있나요?
  - Deployment의 Template에 Pod spec.을 정의하면 되는데, 별도의 파일로 작성하고 참조하는건 안되는 것 같습니다.
  - 혹시라도 방법을 찾으면 여기 업데이트 해 놓겠습니다.

- deployment를 삭제하면 하위 리소스들(replicaset, pod)이 함께 삭제되나요?
  - 네, 맞습니다. 함게 알아서 삭제됩니다. (scaling된 경우까지 알아서 다 삭제해줍니다.)

- Docker의 Namespace가 Service와 유사한 의미인가요?
  - 도커의 Namespace는 격리를 위해서 사용되는 개념입니다. (Process namespace, network namespace, mnt namespace...)
  - K8S의 Service는 ReplicaSet으로 관리되는 복수개의 Pod들을 네트워크 상에서 연결하기 위해서(네트워킹) 사용되는 리소스 입니다.

- Ingress는 Kubernetes의 ALB 개념인가요?
  - [AWS Load Balancer Controller](https://github.com/kubernetes-sigs/aws-load-balancer-controller#readme)

- Jeus WAS를 사용하는 Pod의 경우, 소스는 Git에서 가져올테고 Jeus는 어디서 가져오나요?
  - Jeus base image를 Registry에서 가져와서, 소스(from Git)를 빌드하고 패키징하고 더해서 최종적으로 우리 시스템의 container image를 만듭니다.
  - 만들어진 우리 시스템 image는 다시 Registry에 저장하고 배포에 사용하게 됩니다.

- POD의 경우는 OS 패치 및 관련 S/W 업그레이드를 어떻게 하나요?
  - Base image와 실행환경에 대한 활동을 동일하게 해야합니다.
  - 예를들어 보안 패치가 있다면 실행환경과 Base image에 반영해줘야 합니다.
  
- Docker 의 EXPOSE와 K8S의 Service간 연결점은 어디인가요?
  - Service object를 생성하면 TargetPort가 바로 컨테이너의 포트(EXPOSE에서 지정한 포트)가 됩니다.
  - Service는 NodePort, Port, TargetPort 정보를 가지고 있습니다. (Type에 따라 달라지기는 합니다.)
  
---

- PVC를 업데이트(용량증설) 하면 pv를 다시 만드나요?
  - [볼륨 확장 허용](https://kubernetes.io/ko/docs/concepts/storage/storage-classes/#%EB%B3%BC%EB%A5%A8-%ED%99%95%EC%9E%A5-%ED%97%88%EC%9A%A9) 을 하면 확장 가능합니다.
  - [PV(PersistentVolume) 용량 증설(Scale Up) 하기](https://support.skdt.co.kr/ko/support/solutions/articles/42000063508-pv-persistentvolume-%EC%9A%A9%EB%9F%89-%EC%A6%9D%EC%84%A4-scale-up-%ED%95%98%EA%B8%B0) 도 참고하세요.



---

## 설문요약

- 실습을 같이 해볼 수 있으면 좋겠다.
  - 제한된 시간(이틀)에 많은 양을 하다보니, 수업 중 실습시간을 따로 드리기는 어려울 것 같습니다. ㅠㅠ
  - 대신 따로 해볼 수 있는 환경 (e.g. killercoda)을 계속 업데이트 하고, 정보도 공유하도록 하겠습니다.
- 모든 질문에 대한 답변은 불필요한 것 같다. (수업시간 부족)
  - 질문에 대한 답은 이곳(깃헙-Q&A)에서 모두 드리도록 하고, 수업 중에는 중요한 것들만 공유하도록 하겠습니다. ( -> 질문/답변 시간을 줄이고 수업시간 확보)
- 수업시간 분배 (처음은 느리고, 후반부는 빠르고)
  - 항상 염두에 두고 진행한다고 하는데, 역시 생각대로 잘 안되는 것 같습니다. ㅠㅠ
  - 앞에 말씀드린 질문/답변 시간도 좀 줄이고, 전체 강의시간 배분을 잘 해서 문제없도록 개선하겠습니다. (다음 차수부터는 시간표-챕터별 시간-를 작성해서 진행하겠습니다.)
- 정리를 해주면 좋겠다. (내용이 많아서 정리가 잘 안되고, 어떤게 중요한지 잘 모르겠다.)
  - 말로하는 Summary말고, 전체 요약장표를 추가해 보겠습니다. (한 장 요약. - 가능할지 모르겠네요..)
    - [Cloud Native landscape](https://landscape.cncf.io/) , [Cloud Native Trail Map](https://github.com/cncf/trailmap), [쿠버네티스 알아보기](https://www.samsungsds.com/kr/insights/220222_kubernetes1.html) 등등 참조.
  - 각 챕터의 전반부에 학습목표도 추가하겠습니다.
