# Docker & Kubernetes 노트 (MSP T2 10차)   (oﾟvﾟ)ノ
## Q&A

### Docker

- Docker build를 했는데 교재/강사화면과 다르네요. 왜 그런가요? ( FROM docker.io/library/node:10-alpine ... )
  - 빌드하는 시점/환경에 따라 다를 수 있습니다.
  - 뒤에 배우겠지만, 레이어에 해당하는 것이 호스트 머신에 있을 때와 없을 때 차이가 있을 수 있습니다.

-  container layer ( R/W layer ) 디렉토리에서 -init 이 붙는건 뭔가요?

- docker images 해보면 k8s.gcr.id~ 로 시작하는 이미지들이 보입니다.이것들 rmi 해도 되나요?
  - 아니되옵니다. ( docker desktop 환경인 경우, kubernetes 구동에 사용되는 것들입니다. )

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

- 

