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
  - 아래는 레이어의 순서를 나타내지만, 실제로 동작이 이루어지는 순서는 조금 더 복잡합니다. 각 레이어는 pull(download)과 extract를 하게되는데 이 때 pull은 병렬로 진행되고, extract는 순차로 진행됩니다. (참고 : [Sebastiaan van Stijn](https://github.com/thaJeztah) Current versions of Docker will pull layers in parallel, but extracting would still be done sequential.)

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

- Dockerfile에서 COPY 명령 한 줄마다 layer가 지정되나요?
  - 네, 명령어(Instruction) 중 파일이 처리되는 것은 각각 레이어로 만들어집니다.
 
- Dockerfile 실습(ch05) 에서 CMD["java","HelloDocker"]를 RUN["java","HelloDocker"]로 해도 동일한 결과가 나오는 건가요?
  - 아니오, 둘은 다릅니다. RUN은 빌드(docker build) 시점에 실행되고 CMD는 런(docker run) 시점에 실행됩니다.
  - 예제에서 "java HelloDocker" command는 컨테이너가 실행되는 시점에 화면에 표시하기 위해서 사용한 것입니다.

---

## Kubernetes

- Kubernetes service뒤에 위치한 Pod는 Port가 모두 동일한가요?
  - 같은 spec.으로 만들어진 경우 동일합니다.

- 하나의 쿠버네티스 클러스터에 서비스를 여러 개 사용할 수 있나요?
  - 네, 여러개 만들어서 사용할 수 있습니다.
  - 쿠버네티스 클러스터는 다양한 프로젝트/애플리케이션들이 함께 실행되는 실행환경 입니다. 그 다양한 프로젝트/애플리케이션들이 필요한 서비스를 만들어서 쓰는 것입니다.
 
- 서비스타입을 로드밸런서로 선택하면, 클라우드 공급자의 LB를 먼저 생성하고 거기에 쿠버네티스 node ip/port를 정책으로 물려야 하나요?
  - 클라우드에 쿠버네티스 클러스터를 구성한 경우 LB타입 서비스를 생성하면 자동으로 클라우드의 LB가 생성되고 노드들로 연결됩니다.
  - 클라우드를 사용하지 않은 경우에는 다른 방법으로 (e.g. Software LB) LB타입 서비스를 사용할 수 있습니다.
 
- Label이 여러개인 Pod는 여러개의 Service와 연결될 수도 있나요?
  - 네 가능합니다.
  - 아래와 같이 app=my-nginx label이 설정된 pod를 두 개의 service에서 동일한 selector 설정으로 연결하고 있습니다.
  - nginx-clusterip-service와 nginx-nodeport-service 두 오브젝트를 describe 명령어로 확인해보면 둘 다 endpoints로 동일한 pod 세 개를 연결하고 있습니다. (아래 예제는 172.17.0.2:80,172.17.0.3:80,172.17.0.7:80 )

```bash
ubuntu@ip-172-31-8-50:~/mspt3/hands_on_files/ch10$ kubectl get all
NAME                                       READY   STATUS    RESTARTS   AGE
pod/my-nginx-deployment-55985c7fcf-hckzj   1/1     Running   0          22s
pod/my-nginx-deployment-55985c7fcf-vvx6m   1/1     Running   0          22s
pod/my-nginx-deployment-55985c7fcf-xxs5l   1/1     Running   0          22s

NAME                              TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)        AGE
service/kubernetes                ClusterIP   10.96.0.1        <none>        443/TCP        6h30m
service/nginx-clusterip-service   ClusterIP   10.104.185.140   <none>        80/TCP         15s
service/nginx-nodeport-service    NodePort    10.99.125.47     <none>        80:30007/TCP   10s

ubuntu@ip-172-31-8-50:~/mspt3/hands_on_files/ch10$ kubectl describe svc nginx-clusterip-service 
Name:              nginx-clusterip-service
Namespace:         default
Labels:            <none>
Annotations:       <none>
Selector:          app=my-nginx
Type:              ClusterIP
IP Family Policy:  SingleStack
IP Families:       IPv4
IP:                10.104.185.140
IPs:               10.104.185.140
Port:              <unset>  80/TCP
TargetPort:        80/TCP
Endpoints:         172.17.0.2:80,172.17.0.3:80,172.17.0.7:80
Session Affinity:  None
Events:            <none>
ubuntu@ip-172-31-8-50:~/mspt3/hands_on_files/ch10$ kubectl describe svc nginx-nodeport-service 
Name:                     nginx-nodeport-service
Namespace:                default
Labels:                   <none>
Annotations:              <none>
Selector:                 app=my-nginx
Type:                     NodePort
IP Family Policy:         SingleStack
IP Families:              IPv4
IP:                       10.99.125.47
IPs:                      10.99.125.47
Port:                     <unset>  80/TCP
TargetPort:               80/TCP
NodePort:                 <unset>  30007/TCP
Endpoints:                172.17.0.2:80,172.17.0.3:80,172.17.0.7:80
Session Affinity:         None
External Traffic Policy:  Cluster
Events:                   <none>
```
