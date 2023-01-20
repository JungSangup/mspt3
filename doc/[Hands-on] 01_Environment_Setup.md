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
입과 전 받은 **Account ID**와 **IAM user name**을 이용해서 `sign in` 합니다.

![h:230](img/aws_vpc_3.png)
[MFA](https://docs.aws.amazon.com/ko_kr/singlesignon/latest/userguide/enable-mfa.html) 구성을 한 경우, 위 그림과 같은 화면에서 MFA Code를 입력해야 합니다.

---

![h:230](img/aws_vpc_4.png)
Sign in 후에는 가장 먼저 어느 **Region**에 VPC를 구성할지 결정해야 합니다.
우측 상단 Region 선택 메뉴에서 안내받은 Region을 선택합니다.

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
완료되면 그림과 같이 **Your VPCs**메뉴에 생성된 VPC(**mspt3-vpc**)가 조회되고, **Available** 상태로 되어있습니다.

---

## VM Instance 만들기

이제 앞에서 만든 VPC에 VM Instance인 EC2 Instance를 하나 만듭니다.

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
Key pair name에 **mspt3**를 입력하고,  **RSA** type과 **.pem** format 선택 후 `Create key pair`버튼을 클릭합니다.

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
















---

## Docker 설치하기
