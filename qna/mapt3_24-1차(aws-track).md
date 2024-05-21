# MSP T3 - mspt3_24-1차 (AWS Track)

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


### Docker commands

| Command                                                                       | Description                                                              |
|:--------------------------------------------------------------------------------------- |:------------------------------------------------------------------------ |
| [docker build](https://docs.docker.com/engine/reference/commandline/build/)             | Build an image from a Dockerfile                                         |
| [docker pull](https://docs.docker.com/engine/reference/commandline/pull/)               | Pull an image or a repository from a registry                            |
| [docker push](https://docs.docker.com/engine/reference/commandline/push/)               | Push an image or a repository to a registry                              |
| [docker images](https://docs.docker.com/engine/reference/commandline/images/)           | List images                                                              |
| [docker rmi](https://docs.docker.com/engine/reference/commandline/rmi/)                 | Remove one or more images                                                |
| [docker run](https://docs.docker.com/engine/reference/commandline/run/)         | Run a command in a new container                                                                                                         |
| [docker ps](https://docs.docker.com/engine/reference/commandline/ps/)           | List containers                                                                                                                          |
| [docker inspect](https://docs.docker.com/engine/reference/commandline/inspect/) | Return low-level information on Docker objects                                                                                           |
| [docker rm](https://docs.docker.com/engine/reference/commandline/rm/)           | Remove one or more containers                                                                                                            |
| [docker login](https://docs.docker.com/engine/reference/commandline/login/)   | Log in to a Docker registry (e.g. [Docker hub](https://hub.docker.com/)) |
| [docker logout](https://docs.docker.com/engine/reference/commandline/logout/) | Log out from a Docker registry                                           |
| [docker logs](https://docs.docker.com/engine/reference/commandline/logs/)     | Fetch the logs of a container                                            |
| [docker create](https://docs.docker.com/engine/reference/commandline/create/) | Create a new container (**without starting it**)                         |

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
  - 아래와 같이 color1=blue와 color2=red label이 설정된 pod를 두 개의 service에서 각각 다른 selector 설정으로 연결하고 있습니다.
  - nginx-clusterip-service와 nginx-nodeport-service 두 오브젝트를 describe 명령어로 확인해보면 둘 다 endpoints로 동일한 pod 세 개를 연결하고 있습니다. (아래 예제는 172.17.0.2:80,172.17.0.3:80,172.17.0.7:80 )

```bash
ubuntu@ip-172-31-8-50:~/mspt3/hands_on_files/ch10/temp$ kubectl get pods --show-labels -o wide
NAME                                   READY   STATUS    RESTARTS   AGE     IP           NODE             NOMINATED NODE   READINESS GATES   LABELS
my-nginx-deployment-6995d986df-k6zdc   1/1     Running   0          2m28s   172.17.0.2   ip-172-31-8-50   <none>           <none>            color1=blue,color2=red,pod-template-hash=6995d986df
my-nginx-deployment-6995d986df-qrfnq   1/1     Running   0          2m29s   172.17.0.7   ip-172-31-8-50   <none>           <none>            color1=blue,color2=red,pod-template-hash=6995d986df
my-nginx-deployment-6995d986df-znbhr   1/1     Running   0          2m29s   172.17.0.3   ip-172-31-8-50   <none>           <none>            color1=blue,color2=red,pod-template-hash=6995d986df

ubuntu@ip-172-31-8-50:~/mspt3/hands_on_files/ch10/temp$ kubectl get svc -o wide
NAME                      TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)        AGE     SELECTOR
kubernetes                ClusterIP   10.96.0.1        <none>        443/TCP        8h      <none>
nginx-clusterip-service   ClusterIP   10.111.173.117   <none>        80/TCP         2m28s   color1=blue
nginx-nodeport-service    NodePort    10.105.8.29      <none>        80:30007/TCP   2m23s   color2=red




ubuntu@ip-172-31-8-50:~/mspt3/hands_on_files/ch10/temp$ kubectl describe svc nginx-clusterip-service | grep Endpoints
Endpoints:         172.17.0.2:80,172.17.0.3:80,172.17.0.7:80

ubuntu@ip-172-31-8-50:~/mspt3/hands_on_files/ch10/temp$ kubectl describe svc nginx-nodeport-service | grep Endpoints
Endpoints:                172.17.0.2:80,172.17.0.3:80,172.17.0.7:80
```

- PVC spec.에서 StorageClass를 생략하면 동적 프로비져닝이라는 것을 어떻게 알 수 있나요?
  - K8s 클러스터에 default storageclass 가 지정되어 있는 경우, 생략하면 동적 프로비져닝을 우선합니다.
  - 만약 default storageclass가 지정되어 있지 않은 경우(storageclass는 있지만 default가 아닌경우)에는
    - storageClassName: OOO 과 같이 설정해서 동적으로 프로비저닝 하거나
    - label 과 selector를 이용해서 정적으로 프로비저닝 할 수 있습니다. (아래 예시)

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: nginx-pv
  labels:
    type: static
spec:
  capacity:
    storage: 3Gi
  accessModes:
    - ReadWriteOnce
  hostPath:
    path: "/mnt/data"
---
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: nginx-pvc
spec:
  selector:
    matchLabels:
      type: static
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 3Gi
```
 
```bash
$ kubectl get storageclasses
NAME         PROVISIONER             RECLAIMPOLICY   VOLUMEBINDINGMODE      ALLOWVOLUMEEXPANSION   AGE
local-path   rancher.io/local-path   Delete          WaitForFirstConsumer   false                  23d

$ kubectl get pvc -o wide
NAME        STATUS   VOLUME     CAPACITY   ACCESS MODES   STORAGECLASS   VOLUMEATTRIBUTESCLASS   AGE   VOLUMEMODE
nginx-pvc   Bound    nginx-pv   3Gi        RWO                           <unset>                 18s   Filesystem
$ kubectl get pv -o wide
NAME       CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS   CLAIM               STORAGECLASS   VOLUMEATTRIBUTESCLASS   REASON   AGE   VOLUMEMODE
nginx-pv   3Gi        RWO            Retain           Bound    default/nginx-pvc                  <unset>                          12m   Filesystem

```

### Kubernetes commands - [kubectl](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands)

| Command                                                                       | Description                                                              |
|:--------------------------------------------------------------------------------------- |:------------------------------------------------------------------------ |
| [get](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get) | Display one or many resources. |
| [delete](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete) | Delete resources by file names, stdin, resources and names, or by resources and label selector. |
| [apply](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply) | Apply a configuration to a resource by file name or stdin. |
| [edit](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#edit) | Edit a resource from the default editor. |
| [describe](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe) | Show details of a specific resource or group of resources. |
| [exec](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#exec) | Execute a command in a container. |
| [logs](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#logs) | Print the logs for a container in a pod or specified resource. |

### Helm commands - [helm](https://helm.sh/docs/helm/helm/)

| Command                                                                       | Description                                                              |
|:--------------------------------------------------------------------------------------- |:------------------------------------------------------------------------ |
| [Helm Install](https://helm.sh/docs/helm/helm_install/) | install a chart |
| [Helm Upgrade](https://helm.sh/docs/helm/helm_upgrade/) | upgrade a release |
| [Helm List](https://helm.sh/docs/helm/helm_list/) | list releases |
| [Helm Show Values](https://helm.sh/docs/helm/helm_show_values/) | show the chart's values |
| [Helm Uninstall](https://helm.sh/docs/helm/helm_uninstall/) | uninstall a release |

