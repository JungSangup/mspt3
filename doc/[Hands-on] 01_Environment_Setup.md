---
marp: true
author: sangup.jung@gmail.com
size: 16:9
theme: mspt2
paginate: true
header: Docker & Kubernetes - [Hands-on] 01. Environment setup
footer: Samsung SDS
---

![bg left:40%](img/hands_on.png)

<br>

# Contents

<br>

- **VPC 만들기**
- **VM Instance 만들기**
- **Docker 설치하기**

---

## VPC 만들기

실습을 위한 환경구성을 진행합니다.

먼저 AWS에 [VPC(Virtual Private Cloud)](https://docs.aws.amazon.com/vpc/index.html) 를 만들어 보겠습니다. 실습을 위해 격리된 네트워크 공간을 만드는 것입니다.
천천히 아래 순서대로 진행하면 됩니다.

먼저 AWS Console에 로그인합니다.
[https://aws.amazon.com/console/](https://aws.amazon.com/console/)

![h:320](img/aws_vpc_1.png)
화면 우측 상단의 `Sign in to the Console`을 클릭합니다.

---

![h:230](img/aws_vpc_2.png)
**Account ID**와 **IAM user name**을 이용해서 `sign in` 합니다.

![h:230](img/aws_vpc_3.png)
[MFA](https://docs.aws.amazon.com/ko_kr/singlesignon/latest/userguide/enable-mfa.html) 구성을 한 경우, 위 그림과 같은 화면에서 MFA Code를 입력해야 합니다.

---

![h:230](img/aws_vpc_4.png)
Sign in 후에는 가장 먼저 어느 **Region**에 VPC를 구성할지 결정해야 합니다.
우측 상단 Region 선택 메뉴에서 Region을 선택합니다.

![h:230](img/aws_vpc_5.png)
다음은 **서비스** 중에서 **VPC**를 검색하고 이동합니다.

---

![h:300](img/aws_vpc_6.png)
**VPC dashboard** 화면에서 `Create VPC`버튼을 클릭합니다.

---

![h:550](img/aws_vpc_7.png)
위 그림과 같이 입력하고 `Create VPC`버튼을 클릭합니다. (상세 내용은 다음페이지에 있습니다.)

---

### VPC 구성 (VPC Settings)

| **구분**                                 | **설정**                            |
| -------------------------------------- | --------------------------------- |
| **Resources to create**                | VPC and more를 선택                  |
| **Name tag auto-generation**           | Auto-generate를 체크하고, 이름은 mspt3 입력 |
| **IPv4 CIDR block**                    | 10.0.0.0/16 (default)             |
| **Tenancy**                            | Default                           |
| **Number of Availability Zones (AZs)** | 1                                 |
| **Number of public subnets**           | 1                                 |
| **Number of private subnets**          | 1                                 |
| **NAT gateways ($)**                   | In 1 AZ                           |
| **VPC endpoints**                      | None                              |

> 실습에 필요한 최소한의 구성만 진행함.

---

![h:280](img/aws_vpc_8.png)
잠시 기다리면 VPC와 관련 구성이 완료됩니다.

![h:230](img/aws_vpc_9.png)
완료되면 그림과 같이 **Your VPCs**메뉴에 VPC(**mspt3-vpc**)가 조회되고, **Available** 상태로 되어있습니다.

---

## VM Instance 만들기

이제 앞에서 만든 VPC에 VM Instance인 EC2 Instance를 하나 생성합니다.

![h:200](img/aws_ec2_1.png)
**서비스** 중에서 **EC2**를 검색하고 이동합니다.

![h:200](img/aws_ec2_2.png)
**EC2 dashboard** 화면에서 `Launch instance`버튼을 클릭합니다.

---

![h:350](img/aws_ec2_3.png)
Instance 이름을 **mspt3**로 하고, **AMI(Amazon Machine Image)** 중에서 **Ubuntu**를 선택합니다.

![h:120](img/aws_ec2_4.png)
그 다음 Instance type은 `t3.medium`을 선택합니다.

---

![h:120](img/aws_ec2_5.png)
**Key pair**는 기존의 것을 사용하거나, 없는 경우에는 `Create new key pair`를 눌러 **Key pair** 생성 화면으로 이동합니다.

![h:320](img/aws_ec2_6.png)
신규 생성이 필요한 경우 Key pair name에 **mspt3**를 입력하고,  **RSA** type과 **.pem** format 선택 후 `Create key pair`버튼을 클릭합니다.

> 생성되면 브라우저를 통해서 **mspt3.pem** 파일이 다운로드 됩니다. 잘 보관해두세요.

---

![h:120](img/aws_ec2_7.png)
다시 Instance 생성 화면으로 돌아오면, 앞에서 생성한 **Key pair**를 선택할 수 있습니다.

![h:350](img/aws_ec2_8.png)
그 다음 Network settings에서 `Edit`버튼을 클릭하여 상세 설정을 진행합니다.

---

![h:550](img/aws_ec2_9.png)
위 그림과 같이 입력합니다. (상세 내용은 다음페이지에 있습니다.)

---

### Network 구성 (Network Settings)

| **구분**                            | **설정**                |
| --------------------------------- | --------------------- |
| **VPC**                           | mspt3-vpc             |
| **Subnet**                        | publi subnet 선택       |
| **Auto-assign public IP**         | Enable                |
| **Firewall (security groups)**    | Create security group |
| **Security group name**           | mspt3-sg              |
| **Description**                   | sg for mspt3          |
| **Inbound security groups rules** | ssh (TCP,22) , My IP  |
> Security group은 우선 꼭 필요한 ssh (TCP,22) 만 My IP로 설정합니다. (이후에 추가로 설정합니다.)

---

![h:150](img/aws_ec2_10.png)
Storage를 20GiB로 설정합니다.

![h:350](img/aws_ec2_11.png)
Number of instances를 1로 하고 `Launch instance`버튼을 클릭합니다.

---

![h:400](img/aws_ec2_12.png)
정상적으로 EC2 Instance가 생성되면 화면과 같이 표시됩니다.

---

생성된 VM Instance의 접속은 ...

---

## Docker 설치하기

실습을 위해서 Docker 설치를 진행합니다.
설치하는 방법은 아래 두 가지 방법이 있습니다.
- [Docker Desktop](https://docs.docker.com/desktop/) : One-click-install application for your Mac, Linux, or Windows
- [Docker Engine](https://docs.docker.com/engine/) : Open source containerization platform

우리 실습에는 앞에서 만든 **Ubuntu linux**에 **Docker Engine**을 설치해서 진행하도록 하겠습니다.

> 설치를 위해서는 [OS requirements](https://docs.docker.com/engine/install/ubuntu/#os-requirements)를 만족하는 조건이어야 합니다.
> 우리가 만든 환경은 이 조건에 맞는 **Ubuntu 22.04(LTS)** 64bit 버젼 입니다.

### [Uninstall old versions](https://docs.docker.com/engine/install/ubuntu/#uninstall-old-versions)
기존에 설치된 버젼이 있거나 다시 설치를 진행하려고 하는 경우 먼저 기존버젼 삭제를 진행합니다.
처음 설치를 하는 경우라면, 생략하고 다음 단계를 진행합니다.
```bash
ubuntu@ip-10-0-2-33:~$ sudo apt-get remove docker docker-engine docker.io containerd runc
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
E: Unable to locate package docker-engine
```
> **명령어** : `sudo apt-get remove docker docker-engine docker.io containerd runc`

---

### [Install using the repository](https://docs.docker.com/engine/install/ubuntu/#install-using-the-repository)
Ubuntu의 [Advanced Packaging Tool (APT)](https://ubuntu.com/server/docs/package-management)를 이용해서 설치를 진행합니다.

먼저 package index를 업데이트 합니다.
```bash
ubuntu@ip-10-0-2-33:~$ sudo apt-get update
Hit:1 http://us-east-1.ec2.archive.ubuntu.com/ubuntu jammy InRelease
Get:2 http://us-east-1.ec2.archive.ubuntu.com/ubuntu jammy-updates InRelease [114 kB]
Get:3 http://us-east-1.ec2.archive.ubuntu.com/ubuntu jammy-backports InRelease [99.8 kB]
Get:4 http://security.ubuntu.com/ubuntu jammy-security InRelease [110 kB]
Get:5 http://us-east-1.ec2.archive.ubuntu.com/ubuntu jammy-updates/main amd64 Packages [831 kB]
Get:6 http://us-east-1.ec2.archive.ubuntu.com/ubuntu jammy-updates/restricted amd64 Packages [566 kB]
Get:7 http://us-east-1.ec2.archive.ubuntu.com/ubuntu jammy-updates/universe amd64 Packages [785 kB]
Fetched 2507 kB in 1s (3302 kB/s)
Reading package lists... Done
```
> **명령어** : `sudo apt-get update`

---

다음은, HTTPS를 이용하기 위해서 몇 가지 패키지를 설치합니다.
```bash
ubuntu@ip-10-0-2-33:~$ sudo apt-get install -y ca-certificates curl gnupg lsb-release
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
lsb-release is already the newest version (11.1.0ubuntu4).
lsb-release set to manually installed.
ca-certificates is already the newest version (20211016ubuntu0.22.04.1).
ca-certificates set to manually installed.
curl is already the newest version (7.81.0-1ubuntu1.7).
curl set to manually installed.
gnupg is already the newest version (2.2.27-3ubuntu2.1).
gnupg set to manually installed.
0 upgraded, 0 newly installed, 0 to remove and 3 not upgraded.
```
> **명령어** : `sudo apt-get install -y ca-certificates curl gnupg lsb-release`

Docker 공식 GPG key를 추가합니다.
```bash
ubuntu@ip-10-0-2-33:~$ sudo mkdir -p /etc/apt/keyrings
ubuntu@ip-10-0-2-33:~$ curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
```
> **명령어** : `sudo mkdir -p /etc/apt/keyrings`
> **명령어** : `curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg`

---

Docker 설치를 위서 APT Repository를 설정합니다.
```bash
ubuntu@ip-10-0-2-33:~$ echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
  $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```
> **명령어** : `echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null`

다시 package index를 업데이트 합니다.
```bash
ubuntu@ip-10-0-2-33:~$ sudo apt-get update
Hit:1 http://us-east-1.ec2.archive.ubuntu.com/ubuntu jammy InRelease
Get:2 http://us-east-1.ec2.archive.ubuntu.com/ubuntu jammy-updates InRelease [114 kB]
Get:3 http://us-east-1.ec2.archive.ubuntu.com/ubuntu jammy-backports InRelease [99.8 kB]
Get:4 https://download.docker.com/linux/ubuntu jammy InRelease [48.9 kB]
Get:5 http://security.ubuntu.com/ubuntu jammy-security InRelease [110 kB]
Get:6 https://download.docker.com/linux/ubuntu jammy/stable amd64 Packages [11.9 kB]
Fetched 385 kB in 1s (652 kB/s)
Reading package lists... Done
```
> **명령어** : `sudo apt-get update`

---

그리고, 마지막으로 Docker를 설치합니다.

```bash
ubuntu@ip-10-0-2-33:~$ sudo apt-get install -y docker-ce docker-ce-cli containerd.io docker-compose-plugin
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
The following additional packages will be installed:
  docker-ce-rootless-extras docker-scan-plugin libltdl7 libslirp0 pigz slirp4netns
Suggested packages:
  aufs-tools cgroupfs-mount | cgroup-lite
The following NEW packages will be installed:
  containerd.io docker-ce docker-ce-cli docker-ce-rootless-extras docker-compose-plugin docker-scan-plugin libltdl7 libslirp0 pigz slirp4netns
0 upgraded, 10 newly installed, 0 to remove and 3 not upgraded.
Need to get 113 MB of archives.
After this operation, 431 MB of additional disk space will be used.
Get:1 http://us-east-1.ec2.archive.ubuntu.com/ubuntu jammy/universe amd64 pigz amd64 2.6-1 [63.6 kB]
Get:2 http://us-east-1.ec2.archive.ubuntu.com/ubuntu jammy/main amd64 libltdl7 amd64 2.4.6-15build2 [39.6 kB]
Get:3 http://us-east-1.ec2.archive.ubuntu.com/ubuntu jammy/main amd64 libslirp0 amd64 4.6.1-1build1 [61.5 kB]
Get:4 http://us-east-1.ec2.archive.ubuntu.com/ubuntu jammy/universe amd64 slirp4netns amd64 1.0.1-2 [28.2 kB]
Get:5 https://download.docker.com/linux/ubuntu jammy/stable amd64 containerd.io amd64 1.6.15-1 [27.7 MB]
Get:6 https://download.docker.com/linux/ubuntu jammy/stable amd64 docker-ce-cli amd64 5:20.10.23~3-0~ubuntu-jammy [42.6 MB]
Get:7 https://download.docker.com/linux/ubuntu jammy/stable amd64 docker-ce amd64 5:20.10.23~3-0~ubuntu-jammy [20.5 MB]
Get:8 https://download.docker.com/linux/ubuntu jammy/stable amd64 docker-ce-rootless-extras amd64 5:20.10.23~3-0~ubuntu-jammy [8390 kB]
Get:9 https://download.docker.com/linux/ubuntu jammy/stable amd64 docker-compose-plugin amd64 2.15.1-1~ubuntu.22.04~jammy [9570 kB]
Get:10 https://download.docker.com/linux/ubuntu jammy/stable amd64 docker-scan-plugin amd64 0.23.0~ubuntu-jammy [3623 kB]
Fetched 113 MB in 1s (81.5 MB/s)

... 생략 ...
```
> **명령어** : `sudo apt-get install -y docker-ce docker-ce-cli containerd.io docker-compose-plugin`

---

설치 후 다음 설정을 진행합니다. ([Docker Engine post-installation steps](https://docs.docker.com/engine/install/linux-postinstall/))

Docker daemon은 root 유저로 동작하고, Docker CLI(/usr/bin/docker)는 root 그룹/계정 권한을 가지고 있습니다.
```bash
ubuntu@ip-10-0-2-33:~$ ps -ef | grep -i dockerd
root        9282       1  0 07:09 ?        00:00:00 /usr/bin/dockerd -H fd:// --containerd=/run/containerd/containerd.sock
ubuntu      9514    8611  0 07:18 pts/1    00:00:00 grep --color=auto -i dockerd
ubuntu@ip-10-0-2-33:~$ ls -al /usr/bin/docker
-rwxr-xr-x 1 root root 50717552 Jan 19 17:42 /usr/bin/docker
```

root가 아닌 계정(우리 실습환경의 user는 `ubuntu` 입니다.)을 이용하여 Docker CLI를 사용하기 위해서 다음과 같이 진행합니다.

```bash
ubuntu@ip-10-0-2-33:~$ sudo groupadd docker
groupadd: group 'docker' already exists
```
> **명령어** : `sudo groupadd docker`
> 이미 docker 그룹이 있을수도 있습니다.

다음은 사용중인 User(`ubuntu`)를 docker 그룹에 추가하고, 적용(logout/in)합니다.
```bash
ubuntu@ip-10-0-2-33:~$ sudo usermod -aG docker $USER
ubuntu@ip-10-0-2-33:~$ newgrp docker
```
> **명령어** : `sudo usermod -aG docker $USER`
> **명령어** : `newgrp docker`

---

모두 정상적으로 설치되고 설정된 경우 다음과 같이 동작해야 합니다.
한 번 테스트 해보세요.

```bash
ubuntu@ip-10-0-2-33:~$ docker run hello-world
Unable to find image 'hello-world:latest' locally
latest: Pulling from library/hello-world
2db29710123e: Pull complete
Digest: sha256:aa0cc8055b82dc2509bed2e19b275c8f463506616377219d9642221ab53cf9fe
Status: Downloaded newer image for hello-world:latest

Hello from Docker!
This message shows that your installation appears to be working correctly.

To generate this message, Docker took the following steps:
 1. The Docker client contacted the Docker daemon.
 2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
    (amd64)
 3. The Docker daemon created a new container from that image which runs the
    executable that produces the output you are currently reading.
 4. The Docker daemon streamed that output to the Docker client, which sent it
    to your terminal.

To try something more ambitious, you can run an Ubuntu container with:
 $ docker run -it ubuntu bash

Share images, automate workflows, and more with a free Docker ID:
 https://hub.docker.com/

For more examples and ideas, visit:
 https://docs.docker.com/get-started/
```
> **명령어** : `docker run hello-world`