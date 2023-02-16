
# 자주 사용하는 명령어 알아보기

<br>

---

- [1. 리눅스 명령어 알아보기](#1-리눅스-명령어-알아보기)
- [2. vi 편집기 명령어 알아보기](#2-vi-편집기-명령어-알아보기)
- [3. kubectl 명령어 알아보기](#3-kubectl-명령어-알아보기)

---

<br><br>

## **1. 리눅스 명령어 알아보기**

<br>

### **1-1. 디렉토리 생성하기**

- mkdir 명령어(make directory)로 test라는 이름의 디렉토리를 생성한다.
  
  ```bash
  $ mkdir test
  ```
  
  <br>
  

### **1-2. 디렉토리 이동하기**

- cd 명령어(change directory)로 test라는 디렉토리로 이동한다.
  
  ```bash
  $ cd test
  ```
  
  <br>
  
- 부모 디렉토리로 이동한다.
  

```bash
$ cd ..
```

<br>

- 홈 디렉토리로 이동한다. (/home/ubuntu)

```bash
$ cd
```

또는

```bash
$ cd ~
```

<br>

### **1-3. 디렉토리 삭제하기**

- `rmdir` 명령어(remove directory)로 test라는 디렉토리를 삭제한다.
  
  ```bash
  $ rmdir test
  ```
  
  <br>
  

### **1-4. 디렉토리 및 파일 목록 확인하기**

- `ls` 또는 별칭으로 지정된 `ll` 명령어로 현재 위치한 디렉토리에 있는 디렉토리와 파일 목록을 조회한다.
- ls의 `a` 옵션은 숨긴 파일을 조회하는 기능이고, `l` 옵션은 리스트 형식으로 출력됨을 의미한다.
  
  ```bash
  $ ls -al
  ```
  

또는

```bash
$ ll
```

<br>

### **1-5. 파일 복사하기**

- abc.txt 파일을 def.txt 파일로 복사한다.
- cp [기존 파일위치 및 파일명] [목적지 파일위치 및 파일명]
  
  ```bash
  $ cp abc.txt def.txt
  ```
  
  <br>
  

### **1-6. 파일 이동하기**

- abc.txt 파일을 def.txt 파일로 이동한다.
- mv [기존 파일위치 및 파일명] [목적지 파일위치 및 파일명]
  
  ```bash
  $ mv abc.txt def.txt
  ```
  
  <br>
  

### **1-7. 파일 내용 보기**

- abc.txt 파일 내용을 조회한다.
- cat [파일명]
  
  ```bash
  $ cat abc.txt
  ```
  

<br>

### **1-8. 파일 삭제하기**

- abc.txt 파일을 삭제한다.
- rm [파일명]
  
  ```bash
  $ rm abc.txt
  ```
  
  <br>
  

### **1-9. 현재 위치 확인하기**

```bash
$ pwd
```

<br>

### **1-10. 명령창의 출력내용 삭제하기**

```bash
$ clear
```

<br>

### **1-11. ubuntu의 패키지 목록 업데이트하기**

- ubuntu에서 새로운 패키지(소프트웨어)를 설치하는 경우에 필요한 패키지-매니저인 `apt-get`가 있음
- 필요한 소프트웨어를 설치하기 전에 패키지 매너지를 통해 설치할 수 있는 목록들을 업데이트해야 한다.
  
  ```bash
  $ apt-get update
  ```
  

<br>

- 일반적으로 관리자 권한에 한해서 패키지-매니저를 설치하도록 보안설정이 구성되어 있다. sudo(Super User DO)를 통해 관리자 권한으로 패키지 목록을 업데이트한다.
  
  ```bash
  $ sudo apt-get update
  ```
  
  <br>
  

### **1-12. 패키지 매니저로 소프트웨어 설치하기**

- 관리자 권한을 통해 awscli 소프트웨어 설치한다.
  
- sudo apt-get install -y [소프트웨어]
  
  ```bash
  $ sudo apt-get install awscli
  ```
  
  <br>
  
- 설치과정에서 사용자 의견을 일괄로 yes로 설정해서 자동 설치하려는 경우는 `-y` 옵션을 추가한다.
  

```bash
$ sudo apt-get install -y awscli
```

<br>

### **1-13. 도메인에 할당된 IP 확인하기**

- naver.com 도메인에 지정된 IP 주소를 확인한다.
- nslookup [도메인]
  
  ```bash
  $ nslookup naver.com
  ```
  
  <br>
  

### **1-14. 주기적으로 새로고침된 결과 확인하기**

- 시스템의 시간을 2초 간격으로 출력한다.
- watch [명령어]
  
  ```bash
  $ watch uptime
  ```
  

<br>

### **1-15. tar.gz 파일을 압축해제하기**

- 다수 파일이 묶여있는 파일을 푸는 경우에 tar(Tap ARchiver) 명령어를 사용한다.
- `x 옵션` : tar 파일에서 파일 추출
- `v 옵션` : 처리되는 과정을 자세하게 출력
- `f 옵션` : 대상 taf 파일 지정
- `z 옵션` : gzip으로 압축된 파일을 해제는 경우에 사용
- tar xvfz [tar.gz 확장자에 대한 파일위치 및 파일명]
  
  ```bash
  $ tar xvfz ./emarket_IaC.tar.gz 
  ```
  

<br><br>

---

<br><br>

## **2. vi 편집기 명령어 알아보기**

### **2-1. 파일 생성해서 열기**

- vi 명령어로 abc.txt 파일을 생성해서 열기
- vi [생성할 파일위치 및 파일명]
  
  ```bash
  $ vi abc.txt
  ```
  

<br>

### **2-2. 기존 파일 열기**

- vi 명령어로 abc.txt 파일을 열기
- vi [파일위치 및 파일명]
  
  ```bash
  $ vi abc.txt
  ```
  
  <br>
  

### **2-3. 내용 수정가능한 입력모드로 변경하기**

- 키보드에서 `I` 키를 입력하면, 하단에 `INSERT`로 출력되면서 파일을 수정가능한 상태로 변경된다.
  

<br>

### **2-4. 수정한 내용을 저장하기**

- **명령모드**로 변경하기 위해 `ESC`로 입력한 후 `:`를 입력하면 **실행모드**로 변경된다.
- 최종 `wq`를 입력하면 변경내역이 저장되면서 vi가 종료된다.
  

<br>

### **2-5. 수정한 내용을 저장하지 않고 실행모드로 변경하기**

- **명령모드**로 변경하기 위해 `ESC`로 입력한 후 `:` 키를 입력하면 **실행모드**로 변경된다.
- 최종 `q`를 입력하면 변경내역이 저장되지 않고 vi가 종료된다.

<br>

### **2-6. 파일 수정을 위한 각종 명령어 확인하기**

- `:se nu` 명령어로 vi 편집기의 좌측에 라인 번호를 표시한다.
- `:` 키를 입력해서 실행모드로 변경한 후에 `se nu`를 입력하는 것이다.

<br>

- 실행모드에서 5번째 라인으로 이동한다.
  
  ```vi
  :5
  ```
  

<br>

- 실행모드에서 문서의 처음인 1번 라인으로 이동한다.
  
  ```vi
  :1
  ```
  
  <br>
  
- 실행모드에서 문서의 마지막으로 이동한다.
  
  ```vi
  :$
  ```
  
- 명령모드(`esc` 키 입력)에서 아래 키를 입력하여 기존 파일 내용을 삭제한다.
  
  - `x` : 글자 하나를 지움
  - `dw` : 현재 커서의 단어 하나를 지움
  - `dd` : 라인을 지움

<br>

- 명령모드(`esc` 키 입력)에서 아래 키를 입력하여 파일 내용을 입력한다.
  - `a` : 입력모드로 변경되고 커서의 오른쪽에 글자 입력 가능
  - `i` : 입력모드로 변경되고 커서의 왼쪽에 글자 입력 가능
  - `cw` : 현재 커서의 단어 하나가 삭제되고, 글자로 입력 가능
  - `o` : 현재 라인 아래에 줄을 추가되고, 글자 입력 가능

<br><br>

---

<br><br>

## **3. kubectl 명령어 알아보기**

### **3-1. 자원 조회하기**

- 특정 네임스페이스에 있는 자원 조회하기
- `-n` [네임스페이스명] : 특정 네임스페이스에 한해서 제약 설정
- 형식 : kubectl get [자원유형] -n [네임스페이스명]

<br>

- argocd 네임스페이스에 있는 service 자원 조회하기
  
  > ```bash
  > $ kubectl get services -n argocd
  > ```
  

<br>

- default 네임스페이스에 있는 pod 자원 정보를 추가해서 조회하기
  
- `-o wide` : 추가적인 정보 포함해서 출력
  
  > ```bash
  > $ kubectl get pod -o wide
  > ```
  > 
  > <br>
  
- work nodes 정보 조회하기
  
  > ```bash
  > $ kubectl get nodes
  > ```
  

<br>

- 모든 네임스페이스에 있는 pod 자원 조회하기
  
  > ```bash
  > $ kubectl get pod -all-namespaces
  > ```
  

<br>

- default 네임스페이스에서 nginx라는 이름의 pod 자원 조회하기 (-n 옵션 없는 경우는 default 네임스페이스 의미)

> ```bash
> $ kubectl get pods nginx
> ```

<br>

- argocd 네임스페이스에 있는 모든 자원(all)을 조회하기
- kubectl get all -n [네임스페이스명]
  
  > ```bash
  > $ kubectl get all -n argocd
  > ```
  

<br>

- istio-injection 이라는 이름의 라벨이 붙여진 네임스페이스 목록 조회하기
- `-L [라벨명]` : 사전에 붙힌 라벨이름에 한해 범위 설정
  
  > ```bash
  > kubectl get namespaces -L istio-injection
  > ```
  

<br>

- jenkins 네임스페이스 안의 secret-token 이름의 secret 객체에 대한 상세 정보(describe) 조회하기
- kubectl describe [자원유형] [자원명] -n [네임스페이스]
  
  > ```bash
  > $ kubectl describe secret secret-token -n jenkins
  > ```
  

<br><br>

### **3-2. 자원 생성/변경하기**

- argocd 이라는 이름의 네임스페이스 생성하기
- kubectl create namepsace [생성할 네임스페이스명]
  
  > ```bash
  > $ kubectl create namespace argocd
  > ```
  

<br>

- default 네임스페이스 안에 nginx 이미지를 가져와서, ng라는 이름의 pod를 생성/실행하기
- kubectl run [pod명] --image=[이미지명] -n [네임스페이스명]
  
  > ```bash
  > $ kubectl run ng --image=nginx -n default
  > ```
  

<br>

- abc.yaml 파일에 정의된 자원들을 적용(apply)하기
- kubectl apply -f [yaml 파일명 및 파일위치]
  
  > ```bash
  > kubectl apply -f abc.yaml
  > ```
  

<br>

- ng 라는 이름을 가진 pod 자원을 삭제(delete)하기
- kubectl delete pods [삭제할 pod명]
  
  > ```bash
  > kubectl delete pods ng
  > ```
  

<br><br>

### **3-3. context 사용하기**

- 사용가능한 context 목록 조회하기
  
  > ```bash
  > kubectl config get-contexts
  > ```
  

<br>

- 사용하고자 하는 context 로 변경하기
- kubectl config use-context [사용할 context 명]
  
  > ```bash
  > kubectl config use-context emarket
  > ```
  

