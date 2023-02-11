
# [Hands-on] 01. Environment setup - Docker

![](img/hands_on.png)

<br>

# Contents

**[1. VM Instance 만들기](#1-vm-instance-만들기)**  
**[2. VM Instance 접속하기](#2-vm-instance-접속하기)**  
**[3. Docker 설치하기](#3-docker-설치하기)**  
**[4. Git clone하기 (실습파일 다운로드)](#4-git-clone하기-실습파일-다운로드)**  

---

<br>

## 1. VM Instance 만들기

실습을 위한 환경구성을 진행합니다.

먼저 AWS Console에 로그인합니다.  
- AWS Console : [https://aws.amazon.com/console/](https://aws.amazon.com/console/)

![](img/aws_signin_1.png)
> 화면 우측 상단의 `Sign in to the Console`을 클릭합니다.

<br><br><br>

![](img/aws_signin_2.png)
> **Account ID**와 **IAM user name**을 이용해서 `sign in` 합니다.

<br><br><br>

![](img/aws_signin_3.png)  
> [MFA](https://docs.aws.amazon.com/ko_kr/singlesignon/latest/userguide/enable-mfa.html) 구성을 한 경우, 위 그림과 같은 화면에서 `MFA Code`를 입력해야 합니다.

<br><br><br>

![](img/aws_signin_4.png)
> Sign in 후에는 가장 먼저 어느 **Region**에 VM Instance를 구성할지 결정해야 합니다.
우측 상단 Region 선택 메뉴에서 Region을 선택합니다.

- 반(별로 정해진 **Region**이 있습니다. 확인 후 진행해주세요.

<br><br><br>

이제 EC2 Instance를 만들어 보겠습니다.

![](img/aws_ec2_1.png)
> **서비스** 중에서 **EC2**를 검색하고 이동합니다.

<br><br><br>

![](img/aws_ec2_2.png)
> **EC2 dashboard** 화면에서 `Launch instance`버튼을 클릭합니다.

<br><br><br>

![](img/aws_ec2_3.png)
> Instance 이름을 **mspt3**로 하고, **AMI(Amazon Machine Image)** 중에서 **Ubuntu**를 선택합니다.  
> Ubuntu 버젼은 **Ubuntu Server 20.04 LTS(HVM), SSD Volume Type** 을, Architecture는 **64-bit (x86)** 을 선택합니다.

<br><br><br>

![](img/aws_ec2_4.png)
> 그 다음 Instance type은 `t3.medium`을 선택합니다.

<br><br><br>

![](img/aws_ec2_5.png)
> **Key pair**는 기존의 것을 사용하거나, 없는 경우에는 `Create new key pair`를 눌러 **Key pair** 생성 화면으로 이동합니다.

<br><br><br>

![](img/aws_ec2_6.png)
> 신규 생성이 필요한 경우 Key pair name에 **mspt3**를 입력하고,  **RSA** type과 **.pem** format 선택 후 `Create key pair`버튼을 클릭합니다.  
- 생성되면 브라우저를 통해서 **mspt3.pem** 파일이 다운로드 됩니다. 잘 보관해두세요.

<br><br><br>


![](img/aws_ec2_7.png)
> 다시 Instance 생성 화면으로 돌아오면, 앞에서 생성한 **Key pair**를 선택할 수 있습니다.

<br><br><br>

![](img/aws_ec2_8.png)
> 그 다음 Network settings에서 `Edit`버튼을 클릭하여 상세 설정을 진행합니다.

<br><br><br>

![](img/aws_ec2_9.png)
> 위 그림과 같이 입력합니다. (상세 내용은 아래 표에 있습니다.)

<br>

### Network 구성 (Network Settings)

| **구분**                            | **설정**                          |
| --- | --- |
| **VPC**                           | default VPC를 선택                  |
| **Subnet**                        | subnet 중 하나를 선택 (public subnet) |
| **Auto-assign public IP**         | Enable                            |
| **Firewall (security groups)**    | Create security group             |
| **Security group name**           | mspt3-sg                          |
| **Description**                   | sg for mspt3                      |
| **Inbound security groups rules** | ssh (TCP,22) , My IP              |
> **Inbound security groups rules**은 우선 꼭 필요한 ssh (TCP,22) 만 My IP로 설정합니다. (이후에 추가로 설정합니다.)

<br><br><br>

![](img/aws_ec2_10.png)
> Storage를 20GiB로 설정합니다.

<br><br><br>

![](img/aws_ec2_11.png)
> Number of instances를 1로 하고 `Launch instance`버튼을 클릭합니다.

<br><br><br>

![](img/aws_ec2_12.png)
> 정상적으로 EC2 Instance가 생성되면 화면과 같이 표시됩니다.

- SSH 접속을 위해 필요한 **Public IPv4 address** 또는 **Public IPv4 DNS** 정보는 이 화면에서 확인 가능합니다. (이후 실습에서 수시로 확인이 필요합니다.)

<br><br><br>

과정중에는 SSH를 이용한 Instance 접속 외에도, 실행되는 애플리케이션 접속도 필요합니다.  
애플리케이션 접속을 위해서 추가적인 **Security group** 설정을 진행합니다.

![](img/aws_ec2_13.png)
> 위와같이 EC2 Instance의 **Security** 탭에서 해당 **Security group**으로 이동합니다. (Security group명 옆의 아이콘 클릭)

<br><br><br>

![](img/aws_ec2_14.png)
> Serurity group화면의 Inbound rules 탭에서 `Edit inbound rules` 버튼을 클릭합니다.

<br><br><br>

![](img/aws_ec2_15.png)
> `Add rule` 버튼을 누르면 규칙을 추가할 수 있습니다. 아래 규칙을 추가해주세요.

| **Type**   | **Port range** | Source |
| --- | --- | --- |
| Custom TCP | 80             | My IP  |
| Custom TCP | 443            | My IP  |
| Custom TCP | 8080           | My IP  |
| Custom TCP | 3000           | My IP  |
| Custom TCP | 30000-32767    | My IP  |

<br><br><br><br><br>

## 2. VM Instance 접속하기

생성된 VM Instance의 접속(SSH)은 다음의 방법 중 하나를 사용하면 됩니다.

- **[접속방법1]** Terminal 프로그램 (e.g. [Windows terminal](https://docs.microsoft.com/ko-KR/windows/terminal/install), PowerShell, [cmder](https://cmder.app/), [iTerm2](https://iterm2.com/), etc.)
- **[접속방법2]** MobaXterm

SSH 접속을 위해서는 다음을 먼저 확인해야 합니다.

- VM Instance의 **Public IPv4 address** 또는 **Public IPv4 DNS**
- Key pair (**mspt3.pem** 파일)
- SSH 접속을 위한 Inbound traffic (Port:22) 이 허용되어 있는지 확인 (앞에서 이미 진행함.)

준비가 됐으면 **[접속방법1]** 또는 **[접속방법2]** 중 하나를 선택하고 해당부분으로 이동하세요.

<br>

> 다양한 접속방법에 대한 자세한 설명은 AWS 문서인 [Linux 인스턴스에 연결합니다](https://docs.aws.amazon.com/ko_kr/AWSEC2/latest/UserGuide/AccessingInstances.html)를 참고하세요.

<br><br><br>

**[접속방법1]** Terminal 프로그램  
기본적인 터미널 프로그램을 사용한 접속방법 입니다.

OS마다 제공되는 기본 툴을 사용해도 되고, 별도로 설치해서 사용해도 됩니다.  
윈도우즈인 경우 [Windows Terminal](https://apps.microsoft.com/store/detail/windows-terminal/9N0DX20HK701?hl=ko-kr&gl=kr&rtc=1)를 사용하면 여러가지 기능을 사용할 수 있어 편리합니다. (선택사항)

AWS Console에서 EC2 > Instances 화면으로 이동합니다.
![](img/terminal1.png)
> 접속하려는 EC2 Instance를 선택하고, `Connect`버튼을 클릭합니다.

<br><br><br>

![](img/terminal2.png)
> 위 그림과 같이 **Connect to instance** 화면에서 **SSH client** 탭을 클릭하고, 아래 표시되는 절차에 따라 접속을 진행합니다.

- 윈도우즈 환경에서는 3번 절차 (chomod 400 mspt3.pem)를 진행할 수 없습니다. 터미널 프로그램(e.g. PowerShell)에서 다음과 같이 실행합니다.
```shell
> icacls.exe mspt3.pem /reset
처리된 파일: mspt3.pem
1 파일을 처리했으며 0 파일은 처리하지 못했습니다.
> icacls.exe mspt3.pem /GRANT:R "$($env:USERNAME):(R)"
처리된 파일: mspt3.pem
1 파일을 처리했으며 0 파일은 처리하지 못했습니다.
> icacls.exe mspt3.pem /inheritance:r
처리된 파일: mspt3.pem
1 파일을 처리했으며 0 파일은 처리하지 못했습니다.
```
![](img/command.png)
> ```bash
> icacls.exe mspt3.pem /reset
> icacls.exe mspt3.pem /GRANT:R "$($env:USERNAME):(R)"
> icacls.exe mspt3.pem /inheritance:r
>
> ```

<br>

![h:200](img/terminal3.png)
> 그림과 같이 mspt3.pem 파일의 권한을 변경하는 것입니다.  
> 자세한 내용은은 [오류: 보호되지 않는 프라이빗 키 파일](https://docs.aws.amazon.com/ko_kr/AWSEC2/latest/UserGuide/TroubleshootingInstancesConnecting.html#troubleshoot-unprotected-key)의 내용을 참고하세요.

<br><br><br>

![](img/terminal4.png)
> 필요한 모든 절차를 거치고 정상적으로 접속되면 위와같은 화면이 표시됩니다.

<br><br><br>

**[접속방법2]**  MobaXterm
MobaXterm을 이용하여 VM Instance에 접속하는 방법입니다.

![](img/mobaxterm1.png)
> MobaXterm을 실행하고 `Session` 버튼을 클릭합니다.

<br><br><br>

![](img/mobaxterm2.png)
접속방식은 `SSH`를 선택하고 다음 정보를 입력한 다음 `OK`버튼을 클릭하여 접속합니다.

- **Remote host** : EC2 Instance의 **Public IPv4 address** 또는 **Public IPv4 DNS**
- **Specify username** : ubuntu
- **Use private key** : mspt3.pem

<br><br><br>

![](img/mobaxterm3.png)
> 접속되면 위와같은 화면이 표시됩니다.  
> 다음 번 접속부터는 **Quick connect**의 **User session**을 이용할 수 있습니다.

<br><br><br><br><br>

## 3. Docker 설치하기

실습을 위해서 Docker 설치를 진행합니다.
설치하는 방법은 아래 두 가지 방법이 있습니다.

- [Docker Desktop](https://docs.docker.com/desktop/) : One-click-install application for your Mac, Linux, or Windows
- [Docker Engine](https://docs.docker.com/engine/) : Open source containerization platform

우리 실습에는 앞에서 만든 **Ubuntu linux**에 **Docker Engine**을 설치해서 진행하도록 하겠습니다.

> 설치를 위해서는 [OS requirements](https://docs.docker.com/engine/install/ubuntu/#os-requirements)를 만족하는 조건이어야 합니다.  
> 우리가 만든 환경은 이 조건에 맞는 **Ubuntu 20.04(LTS)** 64bit 버젼 입니다.

### [Uninstall old versions](https://docs.docker.com/engine/install/ubuntu/#uninstall-old-versions)

기존에 설치된 버젼이 있거나 다시 설치를 진행하려고 하는 경우 먼저 기존버젼 삭제를 진행합니다.  
처음 설치를 하는 경우라면, 생략하고 다음 단계를 진행합니다.
```bash
ubuntu@ip-172-31-23-60:~$ sudo apt-get remove docker docker-engine docker.io containerd runc
Reading package lists... Done
Building dependency tree
Reading state information... Done
Package 'docker.io' is not installed, so not removed
E: Unable to locate package docker
E: Unable to locate package docker-engine
```

![](img/command.png)
>```bash
>sudo apt-get remove docker docker-engine docker.io containerd runc
>```

<br>

### [Install using the repository](https://docs.docker.com/engine/install/ubuntu/#install-using-the-repository)

Ubuntu의 [Advanced Packaging Tool (APT)](https://ubuntu.com/server/docs/package-management)를 이용해서 설치를 진행합니다.

먼저 package index를 업데이트 합니다.
```bash
ubuntu@ip-172-31-23-60:~$ sudo apt-get update
Get:1 http://security.ubuntu.com/ubuntu focal-security InRelease [114 kB]
Hit:2 http://us-east-1.ec2.archive.ubuntu.com/ubuntu focal InRelease
Get:3 http://us-east-1.ec2.archive.ubuntu.com/ubuntu focal-updates InRelease [114 kB]
Get:4 http://us-east-1.ec2.archive.ubuntu.com/ubuntu focal-backports InRelease [108 kB]
Get:5 http://security.ubuntu.com/ubuntu focal-security/main amd64 Packages [1993 kB]
Get:6 http://security.ubuntu.com/ubuntu focal-security/main Translation-en [326 kB]
Get:7 http://security.ubuntu.com/ubuntu focal-security/main amd64 c-n-f Metadata [12.2 kB]
Get:8 http://security.ubuntu.com/ubuntu focal-security/restricted amd64 Packages [1495 kB]
Get:9 http://security.ubuntu.com/ubuntu focal-security/restricted Translation-en [211 kB]
Get:10 http://us-east-1.ec2.archive.ubuntu.com/ubuntu focal/universe amd64 Packages [8628 kB]
...생략...
Fetched 25.7 MB in 4s (6120 kB/s)
Reading package lists... Done
```

![](img/command.png)
>```bash
>sudo apt-get update
>```

<br><br><br>

다음은, HTTPS를 이용하기 위해서 몇 가지 패키지를 설치합니다.
```bash
ubuntu@ip-172-31-23-60:~$ sudo apt-get install -y ca-certificates curl gnupg lsb-release
Reading package lists... Done
Building dependency tree
Reading state information... Done
lsb-release is already the newest version (11.1.0ubuntu2).
lsb-release set to manually installed.
ca-certificates is already the newest version (20211016ubuntu0.20.04.1).
ca-certificates set to manually installed.
curl is already the newest version (7.68.0-1ubuntu2.15).
curl set to manually installed.
gnupg is already the newest version (2.2.19-3ubuntu2.2).
gnupg set to manually installed.
0 upgraded, 0 newly installed, 0 to remove and 11 not upgraded.
```

![](img/command.png)
>```bash
>sudo apt-get install -y ca-certificates curl gnupg lsb-release
>```

<br><br><br>

Docker GPG key를 추가합니다.
```bash
ubuntu@ip-172-31-23-60:~$ sudo mkdir -m 0755 -p /etc/apt/keyrings
ubuntu@ip-172-31-23-60:~$ curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
```

![](img/command.png)
>```bash
>sudo mkdir -m 0755 -p /etc/apt/keyrings
>curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
>
>```

<br><br><br>

Docker 설치를 위해서 APT Repository를 설정합니다.
```bash
ubuntu@ip-172-31-23-60:~$ echo \
>   "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
>   $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```

![](img/command.png)
>```bash
>echo \
>  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
>  $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
>```

<br><br><br>

다시 Package index를 업데이트 합니다.
```bash
ubuntu@ip-172-31-23-60:~$ sudo apt-get update
Hit:1 http://us-east-1.ec2.archive.ubuntu.com/ubuntu focal InRelease
Hit:2 http://us-east-1.ec2.archive.ubuntu.com/ubuntu focal-updates InRelease
Hit:3 http://us-east-1.ec2.archive.ubuntu.com/ubuntu focal-backports InRelease
Get:4 https://download.docker.com/linux/ubuntu focal InRelease [57.7 kB]
Get:5 http://security.ubuntu.com/ubuntu focal-security InRelease [114 kB]
Get:6 https://download.docker.com/linux/ubuntu focal/stable amd64 Packages [24.5 kB]
Fetched 196 kB in 1s (264 kB/s)
Reading package lists... Done
```

![](img/command.png)
>```bash
>sudo apt-get update
>```

<br><br><br>

그리고, 마지막으로 Docker를 설치합니다. (**Docker version : 20.10.23**)
```bash
ubuntu@ip-172-31-23-60:~$ sudo apt-get install -y docker-ce=5:20.10.23~3-0~ubuntu-focal docker-ce-cli=5:20.10.23~3-0~ubuntu-focal containerd.io docker-buildx-plugin docker-compose-plugin
Reading package lists... Done
Building dependency tree
Reading state information... Done
The following additional packages will be installed:
  docker-ce-rootless-extras docker-scan-plugin pigz slirp4netns
Suggested packages:
  aufs-tools cgroupfs-mount | cgroup-lite
The following NEW packages will be installed:
  containerd.io docker-buildx-plugin docker-ce docker-ce-cli docker-ce-rootless-extras docker-compose-plugin docker-scan-plugin pigz slirp4netns
0 upgraded, 9 newly installed, 0 to remove and 11 not upgraded.
Need to get 139 MB of archives.
After this operation, 505 MB of additional disk space will be used.
Get:1 http://us-east-1.ec2.archive.ubuntu.com/ubuntu focal/universe amd64 pigz amd64 2.4-1 [57.4 kB]
Get:2 http://us-east-1.ec2.archive.ubuntu.com/ubuntu focal/universe amd64 slirp4netns amd64 0.4.3-1 [74.3 kB]
Get:3 https://download.docker.com/linux/ubuntu focal/stable amd64 containerd.io amd64 1.6.16-1 [27.7 MB]
Get:4 https://download.docker.com/linux/ubuntu focal/stable amd64 docker-buildx-plugin amd64 0.10.2-1~ubuntu.20.04~focal [25.9 MB]
Get:5 https://download.docker.com/linux/ubuntu focal/stable amd64 docker-ce-cli amd64 5:20.10.23~3-0~ubuntu-focal [42.6 MB]
Get:6 https://download.docker.com/linux/ubuntu focal/stable amd64 docker-ce amd64 5:20.10.23~3-0~ubuntu-focal [20.5 MB]
Get:7 https://download.docker.com/linux/ubuntu focal/stable amd64 docker-ce-rootless-extras amd64 5:23.0.1-1~ubuntu.20.04~focal [8765 kB]
...생략...
Processing triggers for man-db (2.9.1-1) ...
Processing triggers for systemd (245.4-4ubuntu3.19) ...
```

![](img/command.png)
>```bash
>sudo apt-get install -y docker-ce=5:20.10.23~3-0~ubuntu-focal docker-ce-cli=5:20.10.23~3-0~ubuntu-focal containerd.io docker-buildx-plugin docker-compose-plugin
>```

<br><br><br>


설치 후 다음 설정을 진행합니다. ([Docker Engine post-installation steps](https://docs.docker.com/engine/install/linux-postinstall/))

Docker daemon은 root 유저로 동작하고, Docker CLI(/usr/bin/docker)는 root 그룹/계정 권한을 가지고 있습니다.

```bash
ubuntu@ip-172-31-23-60:~$ ps -ef | grep -i dockerd
root        3039       1  0 08:56 ?        00:00:00 /usr/bin/dockerd -H fd:// --containerd=/run/containerd/containerd.sock
ubuntu      4078    1407  0 08:58 pts/0    00:00:00 grep --color=auto -i dockerd
ubuntu@ip-172-31-23-60:~$ ls -al /usr/bin/docker
-rwxr-xr-x 1 root root 50722320 Jan 19 17:34 /usr/bin/docker
```

root가 아닌 계정(우리 실습환경의 user는 `ubuntu` 입니다.)을 이용하여 Docker CLI를 사용하기 위해서 다음과 같이 진행합니다.

먼저 `docker`그룹을 추가합니다.

```bash
ubuntu@ip-172-31-23-60:~$ sudo groupadd docker
```

![](img/command.png)
>```bash
>sudo groupadd docker
>```
- 이미 docker 그룹이 있을수도 있습니다. (groupadd: group 'docker' already exists)

<br><br><br>

다음은 사용 중인 User(`ubuntu`)를 docker 그룹에 추가하고, 적용(docker 그룹으로 로그인)합니다.

```bash
ubuntu@ip-172-31-23-60:~$ sudo usermod -aG docker $USER
ubuntu@ip-172-31-23-60:~$ newgrp docker
```

![](img/command.png)
>```bash
>sudo usermod -aG docker $USER
>newgrp docker
>
>```

<br><br><br>

모두 정상적으로 설치되고 설정된 경우 다음과 같이 동작해야 합니다.
한 번 테스트 해보세요.
```bash
ubuntu@ip-172-31-23-60:~$ docker run --rm hello-world
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

![](img/command.png)
>```bash
>docker run --rm hello-world
>```

<br><br><br>

그리고, 실습에 필요한 몇 가지 패키지를 추가로 더 설치할게요.  
```bash
ubuntu@ip-172-31-23-60:~$ sudo apt-get update
Hit:1 http://us-east-1.ec2.archive.ubuntu.com/ubuntu focal InRelease
Hit:2 http://us-east-1.ec2.archive.ubuntu.com/ubuntu focal-updates InRelease
Hit:3 http://us-east-1.ec2.archive.ubuntu.com/ubuntu focal-backports InRelease
Get:4 http://security.ubuntu.com/ubuntu focal-security InRelease [114 kB]
Hit:5 https://download.docker.com/linux/ubuntu focal InRelease
Fetched 114 kB in 1s (150 kB/s)
Reading package lists... Done
ubuntu@ip-172-31-23-60:~$ sudo apt-get install -y net-tools tree conntrack
Reading package lists... Done
Building dependency tree
Reading state information... Done
Suggested packages:
  nftables
The following NEW packages will be installed:
  conntrack net-tools tree
0 upgraded, 3 newly installed, 0 to remove and 13 not upgraded.
Need to get 270 kB of archives.
After this operation, 1083 kB of additional disk space will be used.
Get:1 http://us-east-1.ec2.archive.ubuntu.com/ubuntu focal/main amd64 conntrack amd64 1:1.4.5-2 [30.3 kB]
Get:2 http://us-east-1.ec2.archive.ubuntu.com/ubuntu focal/main amd64 net-tools amd64 1.60+git20180626.aebd88e-1ubuntu1 [196 kB]
Get:3 http://us-east-1.ec2.archive.ubuntu.com/ubuntu focal/universe amd64 tree amd64 1.8.0-1 [43.0 kB]
Fetched 270 kB in 0s (12.2 MB/s)
Selecting previously unselected package conntrack.
(Reading database ... 62118 files and directories currently installed.)
Preparing to unpack .../conntrack_1%3a1.4.5-2_amd64.deb ...
Unpacking conntrack (1:1.4.5-2) ...
Selecting previously unselected package net-tools.
Preparing to unpack .../net-tools_1.60+git20180626.aebd88e-1ubuntu1_amd64.deb ...
Unpacking net-tools (1.60+git20180626.aebd88e-1ubuntu1) ...
Selecting previously unselected package tree.
Preparing to unpack .../tree_1.8.0-1_amd64.deb ...
Unpacking tree (1.8.0-1) ...
Setting up net-tools (1.60+git20180626.aebd88e-1ubuntu1) ...
Setting up conntrack (1:1.4.5-2) ...
Setting up tree (1.8.0-1) ...
Processing triggers for man-db (2.9.1-1) ...
```

![](img/command.png)
>```bash
>sudo apt-get update
>sudo apt-get install -y net-tools tree conntrack
>
>```

<br><br><br><br><br>

## 4. Git clone하기 (실습파일 다운로드)
이후 진행되는 실습과정들에 사용되는 파일들을 다운로드 하겠습니다.

```bash
ubuntu@ip-172-31-23-60:~$ git clone https://github.com/JungSangup/mspt3.git
Cloning into 'mspt3'...
remote: Enumerating objects: 3210, done.
remote: Counting objects: 100% (857/857), done.
remote: Compressing objects: 100% (481/481), done.
remote: Total 3210 (delta 406), reused 775 (delta 374), pack-reused 2353
Receiving objects: 100% (3210/3210), 328.05 MiB | 51.34 MiB/s, done.
Resolving deltas: 100% (1786/1786), done.
```

![](img/command.png)
>```bash
>git clone https://github.com/JungSangup/mspt3.git
>```
- **hands_on_files** 디렉토리 아래에 실습에 필요한 파일들이 있습니다.
