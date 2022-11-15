# Docker & Kubernetes 노트 (MSP T2 10차)   (oﾟvﾟ)ノ
## Q&A

### Docker

- Docker build를 했는데 교재/강사화면과 다르네요. 왜 그런가요? ( FROM docker.io/library/node:10-alpine ... )
  - 빌드하는 시점/환경에 따라 다를 수 있습니다.
  - 뒤에 배우겠지만, 레이어에 해당하는 것이 호스트 머신에 있을 때와 없을 때 차이가 있을 수 있습니다.

- container layer ( R/W layer ) 디렉토리에서 `-init` 이 붙는건 뭔가요?
  - layer 디렉토리에는 파일/디렉토리가 있습니다.  (최 하위 레이어는 link와 diff만 있음.)
    - link (파일) : overlay2/l 디렉토리에 존재하는 링크 정보
    - diff (디렉토리) : 그 레이어의 contents (파일들) 가 존재함.
    - lower (파일) : lower layer에 대한 링크정보
    - merged (디렉토리) : lower layer(의 컨텐츠)와 자신의 레이어(의 컨텐츠)가 병합된 컨텐츠가 존재함. 
    - work (디렉토리) : storage driver에 의해 사용되는 디렉토
   - 컨테이너가 생성되면 같은 이름의 디렉토리가 두 개 생성되고, 그 중 하나는 `-init`이 뒤에 붙어있음.
   - init이 그 하위의 레이어들을 lower에 담고있고, init이 아닌 디렉토리의 link는 init까지를 lower로 담고있음. (아래 예시 참조.) -> init보다 init이 없는 것이 더 상위.
  - [How the overlay2 driver works](https://docs.docker.com/storage/storagedriver/overlayfs-driver/#how-the-overlay2-driver-works)도 참고하세요.  
  - [Docker image layer 정보 확인](https://ikcoo.tistory.com/396) 에도 설명이 잘 되어있네요.
```bash
ubuntu $ tree -L 2
.
|-- 19c812ab930a19683baf95ea2d12c403ef21aba75f5581e0004801d952de2fcf
|   |-- committed
|   |-- diff
|   |-- link
|   |-- lower
|   `-- work
|-- 3f30e4eb9605634a84e913c3d3204b624d32c8e5bb3d4f930079f20fb28d3df5
|   |-- committed
|   |-- diff
|   `-- link
|-- 67e24567f32ee7e5614688f8db47b8b01dc35411fcdccac1f0da6ca18ddd0ef3
|   |-- committed
|   |-- diff
|   |-- link
|   |-- lower
|   `-- work
|-- 95d81d5e704106af63d3e55fa0e5e35e21f84f97b2a328677d23cc63c2289448
|   |-- committed
|   |-- diff
|   |-- link
|   |-- lower
|   `-- work
|-- c605c2d746f17ac17476e1cb88719cba0c9f29d8e2e79dcf0d4beb586f4b4cda
|   |-- committed
|   |-- diff
|   |-- link
|   |-- lower
|   `-- work
|-- e95f12761991634699e80b56e9510162dc7efacaba6ae5f461d12d29295fe154
|   |-- diff
|   |-- link
|   |-- lower
|   |-- merged
|   `-- work
|-- e95f12761991634699e80b56e9510162dc7efacaba6ae5f461d12d29295fe154-init
|   |-- committed
|   |-- diff
|   |-- link
|   |-- lower
|   `-- work
|-- ec45a49fa3c5ad838aa073510debc690dae3c425252eb558414ece53c67fa244
|   |-- committed
|   |-- diff
|   |-- link
|   |-- lower
|   `-- work
`-- l
    |-- 22VBECB4VVVAPE3KULZX6UYBCM -> ../3f30e4eb9605634a84e913c3d3204b624d32c8e5bb3d4f930079f20fb28d3df5/diff
    |-- 5R2VMDDUASJCZGLXNBCX3VKSH4 -> ../e95f12761991634699e80b56e9510162dc7efacaba6ae5f461d12d29295fe154-init/diff
    |-- BIATFAJNMCEW5EZT4V4YZI6V3P -> ../19c812ab930a19683baf95ea2d12c403ef21aba75f5581e0004801d952de2fcf/diff
    |-- CEDMQDL3V5ZU3TU5JCJWDZLK4A -> ../ec45a49fa3c5ad838aa073510debc690dae3c425252eb558414ece53c67fa244/diff
    |-- GWFDJRLMH7UJYSX4DGQEE5RXWZ -> ../67e24567f32ee7e5614688f8db47b8b01dc35411fcdccac1f0da6ca18ddd0ef3/diff
    |-- OJRYLRWZC7HCV3FH6JZYFKSAAR -> ../c605c2d746f17ac17476e1cb88719cba0c9f29d8e2e79dcf0d4beb586f4b4cda/diff
    |-- PG22LLBRRZDUSR5REQDX7B34O2 -> ../95d81d5e704106af63d3e55fa0e5e35e21f84f97b2a328677d23cc63c2289448/diff
    `-- R3UKY4B6QBUYZOW2NU76WMU3QW -> ../e95f12761991634699e80b56e9510162dc7efacaba6ae5f461d12d29295fe154/diff

33 directories, 22 files

ubuntu $ cat ./e95f12761991634699e80b56e9510162dc7efacaba6ae5f461d12d29295fe154-init/lower 
l/BIATFAJNMCEW5EZT4V4YZI6V3P:l/CEDMQDL3V5ZU3TU5JCJWDZLK4A:l/PG22LLBRRZDUSR5REQDX7B34O2:l/GWFDJRLMH7UJYSX4DGQEE5RXWZ:l/OJRYLRWZC7HCV3FH6JZYFKSAAR:l/22VBECB4VVVAPE3KULZX6UYBCMubuntu $ 

ubuntu $ cat ./e95f12761991634699e80b56e9510162dc7efacaba6ae5f461d12d29295fe154/lower 
l/5R2VMDDUASJCZGLXNBCX3VKSH4:l/BIATFAJNMCEW5EZT4V4YZI6V3P:l/CEDMQDL3V5ZU3TU5JCJWDZLK4A:l/PG22LLBRRZDUSR5REQDX7B34O2:l/GWFDJRLMH7UJYSX4DGQEE5RXWZ:l/OJRYLRWZC7HCV3FH6JZYFKSAAR:l/22VBECB4VVVAPE3KULZX6UYBCMubuntu $ 
```
> `-init`이 안붙은 디렉토리의 lower가 link를 하나 더(`-init` 디렉토리까지 포함.) 가지고 있음.

- docker images 해보면 k8s.gcr.id~ 로 시작하는 이미지들이 보입니다.이것들 rmi 해도 되나요?
  - 아니되옵니다. Σ(っ °Д °;)っ ( docker desktop 환경인 경우, kubernetes 구동에 사용되는 것들입니다. )

- /var/lib 아래 docker-desktop이 보이는데 이건 뭔가요?
  - 교재에 말씀드린 `\\wsl.localhost\docker-desktop-data\data\docker` 또는 `\\wsl$\docker-desktop-data\data\docker` 가 docker root 입니다.
  - [Docker 이미지 스토리지 폴더 찾기 문제](https://learn.microsoft.com/ko-kr/windows/wsl/tutorials/wsl-containers#trouble-finding-docker-image-storage-folder) 도 참고하세요.

- run 상태의 이미지는 삭제가 안되나요?
  - 네, 안됩니다. (R/O 레이어는 사용해야 하거든요)
  - 아래 예제 참고하세요.
```bash
ubuntu@ip-10-0-1-205:~/app$ docker run -d nginx
e56ac97ef60a3923b119490978af9785f7c97c9b5d27d4f53e2c85d82dc59789
ubuntu@ip-10-0-1-205:~/app$ docker images
REPOSITORY      TAG         IMAGE ID       CREATED          SIZE
docker-101      latest      de63c73e314b   59 minutes ago   172MB
nginx           latest      76c69feac34e   2 weeks ago      142MB
my-ubuntu       v3          d863c9811ce7   3 weeks ago      231MB
nginx           <none>      51086ed63d8c   5 weeks ago      142MB
todo-test       latest      0bad600f4bd9   7 weeks ago      172MB
busybox         latest      2bd29714875d   2 months ago     1.24MB
nginx           <none>      2d389e545974   2 months ago     142MB
centos          latest      5d0da3dc9764   14 months ago    231MB
node            10-alpine   aa67ba258e18   19 months ago    82.7MB
nginx           1.18.0      c2c45d506085   19 months ago    133MB
nginx           my-tag      c2c45d506085   19 months ago    133MB
rogallo/nginx   1.18.0      c2c45d506085   19 months ago    133MB
ubuntu@ip-10-0-1-205:~/app$ docker rmi nginx
Error response from daemon: conflict: unable to remove repository reference "nginx" (must force) - container e56ac97ef60a is using its referenced image 76c69feac34e
```

---

- Volume은 docker root (/var/lib/docker/)를 사용하니 bind mount가 고성능 I/O에 더 유리하지 않나요?
  - 성능차이는 Storage driver를 거치느냐, 아니냐의 차이여서 volume과 bind mount와는 차이가 없습니다. (둘 다 host machine의 I/O를 사용)
  - R/W layer는 Storage driver를 통해서 I/O를 하기 때문에 성능 차이가 나게 됩니다.
  - [About storage drivers](https://docs.docker.com/storage/storagedriver/)와 [Use volumes](https://docs.docker.com/storage/volumes/) 참고하세요.
  - [Performance best practices](https://docs.docker.com/storage/storagedriver/overlayfs-driver/#performance-best-practices) 도 참고하세요.


---

- Docker의 Bridge가 로드밸런싱 역할을 해주는건가요?
  - 그렇지는 않습니다. 네트워크 구간에서 서브 네트워크를 가지는 G/W역할을 합니다.
  - LB는 쿠버네티스에서 더 자세히 배울겁니다.

- 컨테이너간 통신을 하려면 IP를 알아야 할 것 같은데 직접 할당 가능한가요?
  - IP보다는 네임이나 Alias를 사용하는 것이 일반적입니다.
  - docker run 명령어에 `--ip` 라는 옵션을 쓸 수도 있습니다. (e.g. `docker run --ip 172.30.100.104` )

- CIDR에서 마지막 16은 뭔가요?
  - 앞에서 부터 16 비트라는 의미입니다.
  - [CIDR](https://ko.wikipedia.org/wiki/CIDR) 참고하세요.

- docker run -p 3000:3000의 의미를 다시 설명해주세요.
  - 앞은 host machine의 포트, 뒤는 컨테이너의 포트입니다. 둘을 연결하여 publish 한다는 의미 입니다.

- dockerfile과 docker-compose는 어떻게 다른건가요?
  - dockerfile은 하나의 이미지를 만들기위한 명령어들의 집합이고,
  - docker compose는 여러개의 컨테이너를 구성할 때 사용되는 것입니다.
  - 예를들어 todo app과 mySql을 만들때는 각각의 dockerfile을 사용하고, 실행할 때 docker compose로 한 번에 실행할 수 있습니다.

- docker run 옵션에서 -d(detached)나 -i(interactive)말고 둘 다 없이 하는 경우도 실제 쓰이나요?
  - `-d`를 생략하면 기본적으로는 foreground 모드 입니다.
  - [Detached vs foreground](https://docs.docker.com/engine/reference/run/#detached-vs-foreground) 의 Foreground 부분 참고하세요~

- dockerfile도 선언형으로 생성할 수 있나요? (이미지도 선언형으로 생성할 수 있나요?)
  - ...

---

### Kubernetes

- K8s에서 pod가 도커에서 컨테이너의 의미인가요?
  - 엄밀하게는 다르긴 하지만, 거의 같은 의미로 쓰이고 있습니다.
  - 내일 Workload에서 자세히 다룰 예정입니다.

- 
