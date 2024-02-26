# MSP T3 - 2024 AWS Track

## Docker

- base image(ubuntu)와 Virtual machine의 Ubuntu는 어떻게 다른가요?
  - Virtual machine의 Ubuntu(OS)는 VM의 Guest OS에 해당합니다.
  - Container에서 Ubuntu image를 사용한다는 것은, Host 머신의 OS 커널 위에 Ubuntu 배포판을 컨테이너로 실행한다는 것입니다.

- docker commit 명령어 설명에서, 최상위 레이어에서 하위 레이어에 있는 파일을 삭제한 후 커밋하면 새롭게 만들어진 이미지에서는 그 파일이 어떻게 되는건가요?
  - 상위 레이어가 하위 레이어보다 우선합니다. 따라서, 위와 같은 경우는 최종 결과물에서 삭제된 것으로 표시욉니다.
  - 하지만, 하위 레이어에 파일이 여전히 존재하기 때문에 이미지를 이루는 전체 파일사이즈에는 해당 파일이 포함되어 있습니다.
  - CH06의 [multi-line arguments , reducing clutter](https://github.com/JungSangup/mspt3/blob/main/doc/%5BBook%5D%2006_Dockerfile_BestPractice.md#multi-line-arguments--reducing-clutter)에서 관련내용을 다룰 예정이니 그 때 더 자세히 설명 드리겠습니다.
 
- docker run --detach 명령어로 실행한 후 다시 해당 컨테이너로 접속이 가능한가요?
  - 네, 가능합니다.
  - [docker attach](https://docs.docker.com/reference/cli/docker/container/attach/)명령어를 실행해서 실행된 컨테이너로 attach 할 수 있습니다.
  - 반대로, attach 상태에서 detach하려면 ctrl+p, ctrl+q 를 차례로 입력하면 됩니다. (stop하지 않고 detach만)

- docker pull이나 run 시 보여지는 레이어들은 순서가 있는건가요? ( docker pull/run 시 로그로 보여지는 레이어들의 순서 )
  - docker pull 실행시 표시되는 레이어들은 하위 레이어부터 표시됩니다.
  - 교재 예시인 todo-app:1.0.0 과 todo-app:2.0.0 을 순서대로 pull 해보면 확인 가능합니다.
  - todo-app:1.0.0을 먼저 pull 한 다음, todo-app:2.0.0을 pull 할 때 변하지 않는 하위 레이어는 `Already exists`료 표시되고 중복해서 다운로드 되지 않, 변경된 레이어인 상위 레이어만 다운로드 되는것을 확인할 수 있습니다.

```bash
ubuntu@ip-172-31-8-50:~/temp$ docker pull rogallo/todo-app:1.0.0
1.0.0: Pulling from rogallo/todo-app
ddad3d7c1e96: Pull complete 
de915e575d22: Pull complete 
7150aa69525b: Pull complete 
d7aa47be044e: Pull complete 
e41580df742e: Pull complete 
6cae3b387ef1: Pull complete 
5c25663c62fe: Pull complete 
Digest: sha256:14605bb8608320518f25ad74b85b8bd8e9b5dfb873cf17147ef5eafeb57f3c8a
Status: Downloaded newer image for rogallo/todo-app:1.0.0
docker.io/rogallo/todo-app:1.0.0

ubuntu@ip-172-31-8-50:~/temp$ docker pull rogallo/todo-app:2.0.0
2.0.0: Pulling from rogallo/todo-app
ddad3d7c1e96: Already exists 
de915e575d22: Already exists 
7150aa69525b: Already exists 
d7aa47be044e: Already exists 
e41580df742e: Already exists 
c2e4fda38035: Pull complete 
15ed96d52ad1: Pull complete 
Digest: sha256:a1427b50a4190a6d1da6f605b5a07b600261e06bd09f664865562c4ecd305e58
Status: Downloaded newer image for rogallo/todo-app:2.0.0
```


---

## Kubernetes
