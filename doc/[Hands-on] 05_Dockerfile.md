
# [Hands-on] 05. Dockerfile

![](img/hands_on.png)

<br>

# Contents

**[1. Dockerfile을 이용한 이미지 빌드](#1-dockerfile을-이용한-이미지-빌드)**
**[2. Multi-stage build](#2-multi-stage-build)**

---

## 1. Dockerfile을 이용한 이미지 빌드

이번 실습은 Dockerfile을 이용하여 이미지를 생성하는 방법을 알아보겠습니다.  
어떻게 하면 좀 더 효율적인 이미지를 만들 수 있는지도 알아볼게요.

먼저 간단한 Java 파일을 준비합니다.
```java
public class HelloDocker {
    public static void main(String[] args) {
        System.out.println("Hello Docker!!!");
    }
}
```
> 파일명은 **HelloDocker.java**로 합니다.

<br><br><br>

이제 Dockerfile 하나를 준비합니다.
이미지를 만드는 여러가지 방법 중 하나인 Dockerfile을 이용한 빌드를 해볼게요.
```dockerfile
FROM openjdk:8
COPY HelloDocker.java /hello/
WORKDIR /hello
RUN javac HelloDocker.java
CMD ["java","HelloDocker"]
```
> 파일명은 **Dockerfile1**로 합니다.

<br><br><br>

위에서 만든 Dockerfile을 간단히 설명하자면 다음과 같습니다.
> 1. openjdk8을 Base image로 사용하고
> 2. /hello 경로에 HelloDocker.java 파일을 복사하고
> 3. /hello 경로로 이동한 뒤
> 4. HelloDocker.java 를 컴파일하고
> 5. docker container가 구동되면 `java HelloDocker`를 실행

준비를 마친 상태는 아래와 같습니다.
```bash
ubuntu@ip-172-31-23-60:~$ tree
.
├── Dockerfile1
└── HelloDocker.java

0 directories, 2 files
```

<br><br><br>

이제 아래 명령어로 hellodocker 이미지를 생성합니다.
```bash
ubuntu@ip-172-31-23-60:~$ docker build -f Dockerfile1 -t hellodocker:v1 .
Sending build context to Docker daemon  4.096kB
Step 1/5 : FROM openjdk:8
8: Pulling from library/openjdk
001c52e26ad5: Pull complete
d9d4b9b6e964: Pull complete
2068746827ec: Pull complete
9daef329d350: Pull complete
d85151f15b66: Pull complete
52a8c426d30b: Pull complete
8754a66e0050: Pull complete
Digest: sha256:86e863cc57215cfb181bd319736d0baf625fe8f150577f9eb58bd937f5452cb8
Status: Downloaded newer image for openjdk:8
 ---> b273004037cc
Step 2/5 : COPY HelloDocker.java /hello/
 ---> 586ddfe9c462
Step 3/5 : WORKDIR /hello
 ---> Running in b6c0c7b804ce
Removing intermediate container b6c0c7b804ce
 ---> 31be5909e298
Step 4/5 : RUN javac HelloDocker.java
 ---> Running in 8cc4ee5c98f4
Removing intermediate container 8cc4ee5c98f4
 ---> a12ffad6b75b
Step 5/5 : CMD ["java","HelloDocker"]
 ---> Running in d6f4b1a2ebef
Removing intermediate container d6f4b1a2ebef
 ---> 91d22f496ae4
Successfully built 91d22f496ae4
Successfully tagged hellodocker:v1
```

> ⌨️ 명령어
>```bash
>docker build -f Dockerfile1 -t hellodocker:v1 .
>```

<br><br><br>

빌드가 성공하면 `docker images`명령어로 조회도 해보세요.
```bash
ubuntu@ip-172-31-23-60:~/mspt3/hands_on_files/dockerfile$ docker images hellodocker
REPOSITORY    TAG       IMAGE ID       CREATED              SIZE
hellodocker   v1        91d22f496ae4   About a minute ago   526MB
```

> ⌨️ 명령어
>```bash
>docker images hellodocker
>```

이미지가 준비됐으니 이제 실행을 해볼게요.
```bash
ubuntu@ip-172-31-23-60:~$ docker run --rm hellodocker:v1
Hello Docker!!!
```

> ⌨️ 명령어
>```bash
>docker run --rm hellodocker:v1
>```
- `--rm` : Automatically remove the container when it exits

결과가 예상한 것과 같은가요?

일단 첫 번째 단계는 성공입니다. (ง˙∇˙)ว

<br><br><br><br><br>

## 2. Multi-stage build

이전 Step에서 Java Application을 포함하는 docker 이미지를 생성해 보았습니다.  
Base image로 openjdk:8 (JDK)을 사용했기 때문에 이미지의 크기가 큽니다. (앞의 결과는 **526MB**)

docker는 multi-stage build 기능을 제공하기 때문에 최종 docker 이미지에는 binary만 포함될 수 있도록 할 수가 있습니다.

<br>

![h:300](img/docker_multi_stage_build.PNG)

<br>

애플리케이션 파일인 HelloDocker.java 파일은 그대로 두고 **Dockerfile**을 하나 더 준비하겠습니다.
```dockerfile
# Build stage
FROM openjdk:8 as build-stage
COPY HelloDocker.java /hello/
WORKDIR /hello
RUN javac HelloDocker.java

# Production stage
FROM openjdk:8-jre as production-stage
COPY --from=build-stage /hello/HelloDocker.class /hello/HelloDocker.class
WORKDIR /hello
CMD ["java","HelloDocker"]
```
> 파일명은 **Dockerfile2**로 합니다.

| Build stage | Production stage |
| :--- | :--- |
| 1. openjdk8을 build-stage로 정하고<br>2. /hello 경로에 HelloDocker.java파일을 복사<br>3. /hello 경로로 이동<br>4. HelloDocker.java를 컴파일 | 1. openjdk8-jre를 production-stage로 정하고<br>2. /hello/HelloDocker.class 파일 복사 (build -> production)<br>3. 작업 경로를 /hello로 변경<br>4. docker container가 구동되면 `java HelloDocker`를 실행 |

<br><br><br>

이제 아래 명령어로 hellodocker 이미지 v2를 생성합니다.
```bash
ubuntu@ip-172-31-23-60:~$ docker build -f Dockerfile2 -t hellodocker:v2 .
Sending build context to Docker daemon  4.096kB
Step 1/8 : FROM openjdk:8 as build-stage
 ---> b273004037cc
Step 2/8 : COPY HelloDocker.java /hello/
 ---> Using cache
 ---> 586ddfe9c462
Step 3/8 : WORKDIR /hello
 ---> Using cache
 ---> 31be5909e298
Step 4/8 : RUN javac HelloDocker.java
 ---> Using cache
 ---> a12ffad6b75b
Step 5/8 : FROM openjdk:8-jre as production-stage
8-jre: Pulling from library/openjdk
001c52e26ad5: Already exists
d9d4b9b6e964: Already exists
2068746827ec: Already exists
8510da692cda: Pull complete
c34215579d03: Pull complete
73d77b4774a9: Pull complete
Digest: sha256:667a15e7bc533a90fb39ddb7e5bed63162ac3c13a97e6c698bf4f139f51b7d33
Status: Downloaded newer image for openjdk:8-jre
 ---> 0c14a0e20aa3
Step 6/8 : COPY --from=build-stage /hello/HelloDocker.class /hello/HelloDocker.class
 ---> e6a283c7bcb4
Step 7/8 : WORKDIR /hello
 ---> Running in 0c29098b40e6
Removing intermediate container 0c29098b40e6
 ---> a7703fe64d5f
Step 8/8 : CMD ["java","HelloDocker"]
 ---> Running in beddc772d5b9
Removing intermediate container beddc772d5b9
 ---> 9435a2a2311d
Successfully built 9435a2a2311d
Successfully tagged hellodocker:v2
```

> ⌨️ 명령어
>```bash
>docker build -f Dockerfile2 -t hellodocker:v2 .
>```

<br><br><br>

빌드가 성공하면 `docker images`명령어를 실행해서 결과를 볼까요?
```bash
ubuntu@ip-172-31-23-60:~$ docker images hellodocker
REPOSITORY    TAG       IMAGE ID       CREATED              SIZE
hellodocker   v2        9435a2a2311d   About a minute ago   274MB
hellodocker   v1        91d22f496ae4   7 minutes ago        526MB
```

> ⌨️ 명령어
>```bash
>docker images hellodocker
>```

v1 과 v2 는 Java Application은 동일하지만, base image의 차이 때문에 이미지 전체의 사이즈가 크게 차이가 납니다.
- **v1** : 526MB -> **v2** : 274MB   （°o°；）

**Cloud native** 환경에서는 가능하면 이미지 사이즈를 작게 가져가는게 좋겠죠?

<br><br><br>

컨테이너 실행결과는 아래처럼 차이가 없습니다.
```bash
ubuntu@ip-172-31-23-60:~/mspt3/hands_on_files/dockerfile$ docker run --rm hellodocker:v2
Hello Docker!!!
```

> ⌨️ 명령어
>```bash
>docker run --rm hellodocker:v2
>```
- `--rm` : Automatically remove the container when it exits

<br><br><br>

수고하셨습니다. (〃･ิ‿･ิ)ゞ
