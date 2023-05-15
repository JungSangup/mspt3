
# [Hands-on] 13. Kubernetes Deployment strategies

![](./img/hands_on.png)

<br>

# Contents

**[1. Recreate 방법으로 업데이트 해보기](#1-recreate-방법으로-업데이트-해보기)**  
**[2. Recreate 방법으로 롤백 해보기](#2-recreate-방법으로-롤백-해보기)**  
**[3. RollingUpdate 방법으로 업데이트 해보기](#3-rollingupdate-방법으로-업데이트-해보기)**

💾실습파일 위치 : [hands_on_files/ch13](../hands_on_files/ch13)

미리 준비된 실습용 파일을 사용하려는 경우 아래 디렉토리 이동 명령어를 실행해주세요.
```bash
ubuntu@ip-172-31-23-60:~$ cd ~/mspt3/hands_on_files/ch13
ubuntu@ip-172-31-23-60:~/mspt3/hands_on_files/ch13$
```

> 💻 명령어
>```bash
>cd ~/mspt3/hands_on_files/ch13
>```

---

## 1. Recreate 방법으로 업데이트 해보기

이번 실습은 Deployment의 업데이트 방법 두 가지를 비교해보는 실습입니다.

그리고, 이번 실습은 Terminal이 두 개 필요합니다.  
미리 준비해주세요.  

Tab을 추가할 수 있는 툴(e.g. Windows Terminal, iTerm, MobaXTerm etc.)은 탭을 하나 더 열어서 준비하고, 아닌경우 [tmux](https://github.com/tmux/tmux/wiki)등을 활용할 수도 있습니다.

<br>

첫 번째는 **Recreate** 입니다.  
말 그대로 **다시 생성**하는 방법입니다.  
기존에 서비스되고 있던 Pod들을 모두 정지하고, 새로운 Pod를 실행하는거죠.

Deployment 의 `.spec.strategy`를 아래와 같이 지정하면 됩니다.
```yaml
spec:
  strategy:
    type: Recreate
```

<br><br><br>

아래는 **Recreate** 방식을 적용한 **Deployment** 예제파일입니다.
실습을 위해 아래 파일을 만들어주세요.
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  labels:
    app: my-nginx
    tier: frontend
spec:
  replicas: 3
  strategy:
    type: Recreate
  selector:
    matchLabels:
      app: my-nginx
  template:
    metadata:
      labels:
        app: my-nginx
      name: my-nginx
    spec:
      containers:
      - image: nginx:1.18
        name: my-nginx
        ports:
        - containerPort: 80
```
> 파일명은 **nginx-recreate.yaml**로 합니다.

<br><br><br>

그리고, 다음과 같이 **Deployment**를 생성합니다.
```bash
ubuntu@ip-172-31-23-60:~$ kubectl apply -f nginx-recreate.yaml
deployment.apps/nginx-deployment created
```

> 💻 명령어
>```bash
>kubectl apply -f nginx-recreate.yaml
>```

<br><br><br>

그리고, 생성된 Object들도 확인해 보겠습니다.
```bash
ubuntu@ip-172-31-23-60:~$ kubectl get all
NAME                                    READY   STATUS    RESTARTS   AGE
pod/nginx-deployment-85fc747956-4jgxq   1/1     Running   0          15s
pod/nginx-deployment-85fc747956-vzsbg   1/1     Running   0          15s
pod/nginx-deployment-85fc747956-x6zmj   1/1     Running   0          15s

NAME                 TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
service/kubernetes   ClusterIP   10.96.0.1    <none>        443/TCP   4d15h

NAME                               READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/nginx-deployment   3/3     3            3           17s

NAME                                          DESIRED   CURRENT   READY   AGE
replicaset.apps/nginx-deployment-85fc747956   3         3         3       16s
```

> 💻 명령어
>```bash
>kubectl get all
>```

Spec에 정의된 대로 세 개의 Nginx Pod가 생성되어 있습니다.

<br><br><br>

우리가 생성한 nginx 버젼을 알아볼까요?
```bash
ubuntu@ip-172-31-23-60:~$ kubectl describe deployment nginx-deployment | grep -i image
    Image:        nginx:1.18
```

> 💻 명령어
>```bash
>kubectl describe deployment nginx-deployment | grep -i image
>```

사용된 Image는 `nginx:1.18` 입니다.


<br><br><br>

이제 버젼을 변경하려고 합니다.  
앞에서 배웠으니 **선언형**(**Declarative**)으로 해볼게요.

yaml파일의 버젼부분을 수정합니다. (`image: nginx:1.18` -> `image: nginx:1.19` , [sed](https://www.gnu.org/software/sed/) 명령어 사용)
```bash
ubuntu@ip-172-31-23-60:~$ sed -i 's/image: nginx:1.18/image: nginx:1.19/g' nginx-recreate.yaml
```

> 💻 명령어
>```bash
>sed -i 's/image: nginx:1.18/image: nginx:1.19/g' nginx-recreate.yaml
>```

<br><br><br>

그리고, Pod들이 어떻게 변하는지 살펴보기 위해서 다음 명령어를 실행해주세요.  
이 명령어는 **두 번째 Terminal**에서 실행해주세요.
```bash
ubuntu@ip-172-31-23-60:~$ kubectl get pods --watch
NAME                                READY   STATUS    RESTARTS   AGE
nginx-deployment-85fc747956-4jgxq   1/1     Running   0          4m23s
nginx-deployment-85fc747956-vzsbg   1/1     Running   0          4m23s
nginx-deployment-85fc747956-x6zmj   1/1     Running   0          4m23s

```

> 💻 명령어 (Terminal 2)
>```bash
>kubectl get pods --watch
>```
> `--watch` 는 앞의 명령어를 실행한 후 변경(Change)사항을 지속적으로 보여주는 Flag입니다.  
> Watch를 멈추려면 Ctrl + c 를 입력합니다.

<br><br><br>

이제 다시 첫 번째 Terminal에서 아래와 같이 변경사항을 적용합니다.

그리고 변경된 yaml파일을 이용해서 업데이트를 합니다.
```bash
ubuntu@ip-172-31-23-60:~$ kubectl apply -f nginx-recreate.yaml
deployment.apps/nginx-deployment configured
```

> 💻 명령어
>```bash
>kubectl apply -f nginx-recreate.yaml
>```

<br><br><br>

두 번째 Terminal에서 어떤 일이 일어나는지 유심히 보세요. 아마도, 있던 Pod들이 모두 삭제되고 새로운 Pod들이 생길거예요.
```bash
ubuntu@ip-172-31-23-60:~$ kubectl get pods --watch
NAME                                READY   STATUS    RESTARTS   AGE
nginx-deployment-85fc747956-4jgxq   1/1     Running   0          4m23s
nginx-deployment-85fc747956-vzsbg   1/1     Running   0          4m23s
nginx-deployment-85fc747956-x6zmj   1/1     Running   0          4m23s

nginx-deployment-85fc747956-vzsbg   1/1     Terminating   0          4m58s
nginx-deployment-85fc747956-4jgxq   1/1     Terminating   0          4m58s
nginx-deployment-85fc747956-x6zmj   1/1     Terminating   0          4m58s
nginx-deployment-85fc747956-4jgxq   0/1     Terminating   0          4m59s
nginx-deployment-85fc747956-vzsbg   0/1     Terminating   0          4m59s
nginx-deployment-85fc747956-x6zmj   0/1     Terminating   0          4m59s
nginx-deployment-85fc747956-4jgxq   0/1     Terminating   0          4m59s
nginx-deployment-85fc747956-4jgxq   0/1     Terminating   0          4m59s
nginx-deployment-85fc747956-vzsbg   0/1     Terminating   0          4m59s
nginx-deployment-85fc747956-vzsbg   0/1     Terminating   0          4m59s
nginx-deployment-85fc747956-x6zmj   0/1     Terminating   0          4m59s
nginx-deployment-85fc747956-x6zmj   0/1     Terminating   0          4m59s
nginx-deployment-7dd48c557f-6b9b8   0/1     Pending       0          0s
nginx-deployment-7dd48c557f-6b9b8   0/1     Pending       0          0s
nginx-deployment-7dd48c557f-k6czs   0/1     Pending       0          0s
nginx-deployment-7dd48c557f-s9msd   0/1     Pending       0          0s
nginx-deployment-7dd48c557f-k6czs   0/1     Pending       0          0s
nginx-deployment-7dd48c557f-s9msd   0/1     Pending       0          0s
nginx-deployment-7dd48c557f-6b9b8   0/1     ContainerCreating   0          2s
nginx-deployment-7dd48c557f-k6czs   0/1     ContainerCreating   0          2s
nginx-deployment-7dd48c557f-s9msd   0/1     ContainerCreating   0          2s

nginx-deployment-7dd48c557f-6b9b8   1/1     Running             0          6s
nginx-deployment-7dd48c557f-s9msd   1/1     Running             0          6s
nginx-deployment-7dd48c557f-k6czs   1/1     Running             0          6s
```

<br><br><br>

Deployment에 어떤 변화가 생겼나 볼까요?  
첫 번째 터미널에서 실행해주세요.
```bash
ubuntu@ip-172-31-23-60:~$ kubectl describe deployment nginx-deployment | grep -i image
    Image:        nginx:1.19
```

> 💻 명령어
>```bash
>kubectl describe deployment nginx-deployment | grep -i image
>```

<br><br><br>

그리고, 새로 생성된 Pod도 한번 보구요.
```bash
ubuntu@ip-172-31-23-60:~$ kubectl get pods
NAME                                READY   STATUS    RESTARTS   AGE
nginx-deployment-7dd48c557f-6b9b8   1/1     Running   0          92s
nginx-deployment-7dd48c557f-k6czs   1/1     Running   0          92s
nginx-deployment-7dd48c557f-s9msd   1/1     Running   0          92s
ubuntu@ip-172-31-23-60:~$ kubectl describe pod nginx-deployment-7dd48c557f-6b9b8 | grep -i image
    Image:          nginx:1.19
    Image ID:       docker-pullable://nginx@sha256:df13abe416e37eb3db4722840dd479b00ba193ac6606e7902331dcea50f4f1f2
  Normal  Pulling    115s  kubelet            Pulling image "nginx:1.19"
  Normal  Pulled     112s  kubelet            Successfully pulled image "nginx:1.19" in 3.453818458s
```

> 💻 명령어
>```bash
>kubectl get pods
>```
>```bash
>kubectl describe pod [POD-NAME] | grep -i image
>```
> [POD-NAME] 에는 앞에서 조회된 POD 중 하나의 이름을 넣어주세요.

어떤가요? 업데이트가 잘 이루어졌나요?

이제 두 번째 터미널은 Ctrl + c 를 눌러 Watch를 멈추겠습니다.

<br><br><br><br><br>

## 2. Recreate 방법으로 롤백 해보기

이번에는 Deployment의 **롤백** 방법을 알아보겠습니다.  
먼저 업데이트 **History**는 아래와 같이 확인해볼 수 있습니다.
```bash
ubuntu@ip-172-31-23-60:~$ kubectl rollout history deployment nginx-deployment
deployment.apps/nginx-deployment
REVISION  CHANGE-CAUSE
1         <none>
2         <none>
```

> 💻 명령어
>```bash
>kubectl rollout history deployment nginx-deployment
>```

<br><br><br>

최초 생성된 **Revision #1**과 한 번 업데이트 후의 **Revision #2**가 보입니다.  
그 중 하나의 Revision을 콕 집어서 자세히 볼 수도 있습니다.
```bash
ubuntu@ip-172-31-23-60:~$ kubectl rollout history deployment nginx-deployment --revision=1
deployment.apps/nginx-deployment with revision #1
Pod Template:
  Labels:	app=my-nginx
	pod-template-hash=85fc747956
  Containers:
   my-nginx:
    Image:	nginx:1.18
    Port:	80/TCP
    Host Port:	0/TCP
    Environment:	<none>
    Mounts:	<none>
  Volumes:	<none>
```

> 💻 명령어
>```bash
>kubectl rollout history deployment nginx-deployment --revision=1
>```

<br><br><br>

```bash
ubuntu@ip-172-31-23-60:~$ kubectl rollout history deployment nginx-deployment --revision=2
deployment.apps/nginx-deployment with revision #2
Pod Template:
  Labels:	app=my-nginx
	pod-template-hash=7dd48c557f
  Containers:
   my-nginx:
    Image:	nginx:1.19
    Port:	80/TCP
    Host Port:	0/TCP
    Environment:	<none>
    Mounts:	<none>
  Volumes:	<none>
```

> 💻 명령어
>```bash
>kubectl rollout history deployment nginx-deployment --revision=2
>```

<br><br><br>

역시 **두 번째 Terminal**에 어떤 변화가 일어날지 확인 할 준비를 하고,
```bash
ubuntu@ip-172-31-23-60:~$ kubectl get pods --watch
NAME                                READY   STATUS    RESTARTS   AGE
nginx-deployment-7dd48c557f-6b9b8   1/1     Running   0          4m47s
nginx-deployment-7dd48c557f-k6czs   1/1     Running   0          4m47s
nginx-deployment-7dd48c557f-s9msd   1/1     Running   0          4m47s

```

> 💻 명령어 (Terminal 2)
>```bash
>kubectl get pods --watch
>```
> `--watch` 는 앞의 명령어를 실행한 후 변경(Change)사항을 지속적으로 보여주는 Flag입니다.
> Watch를 멈추려면 Ctrl + c 를 입력합니다.

<br><br><br>

**첫 번째 Terminal**에서 **revision1**으로 롤백 합니다.
```bash
ubuntu@ip-172-31-23-60:~$ kubectl rollout undo deployment nginx-deployment --to-revision=1
deployment.apps/nginx-deployment rolled back
```

> 💻 명령어
>```bash
>kubectl rollout undo deployment nginx-deployment --to-revision=1
>```

<br><br><br>

**두 번째 Terminal**에는 업데이트 할 때와 비슷한 변경내용을 볼 수 있을겁니다.  
Pod들을 먼저 삭제하고, 새로은 Pod들을 만드는 걸 볼 수 있습니다.
```bash
ubuntu@ip-172-31-23-60:~$ kubectl get pods --watch
NAME                                READY   STATUS    RESTARTS   AGE
nginx-deployment-7dd48c557f-6b9b8   1/1     Running   0          4m47s
nginx-deployment-7dd48c557f-k6czs   1/1     Running   0          4m47s
nginx-deployment-7dd48c557f-s9msd   1/1     Running   0          4m47s

nginx-deployment-7dd48c557f-s9msd   1/1     Terminating   0          5m12s
nginx-deployment-7dd48c557f-k6czs   1/1     Terminating   0          5m12s
nginx-deployment-7dd48c557f-6b9b8   1/1     Terminating   0          5m12s
nginx-deployment-7dd48c557f-k6czs   0/1     Terminating   0          5m12s
nginx-deployment-7dd48c557f-6b9b8   0/1     Terminating   0          5m12s
nginx-deployment-7dd48c557f-s9msd   0/1     Terminating   0          5m12s
nginx-deployment-7dd48c557f-s9msd   0/1     Terminating   0          5m12s
nginx-deployment-7dd48c557f-s9msd   0/1     Terminating   0          5m12s
nginx-deployment-7dd48c557f-k6czs   0/1     Terminating   0          5m13s
nginx-deployment-7dd48c557f-k6czs   0/1     Terminating   0          5m13s
nginx-deployment-7dd48c557f-6b9b8   0/1     Terminating   0          5m13s
nginx-deployment-7dd48c557f-6b9b8   0/1     Terminating   0          5m13s
nginx-deployment-85fc747956-n62jm   0/1     Pending       0          0s
nginx-deployment-85fc747956-n62jm   0/1     Pending       0          0s
nginx-deployment-85fc747956-7vgpg   0/1     Pending       0          0s
nginx-deployment-85fc747956-rkhf8   0/1     Pending       0          0s
nginx-deployment-85fc747956-7vgpg   0/1     Pending       0          0s
nginx-deployment-85fc747956-rkhf8   0/1     Pending       0          0s
nginx-deployment-85fc747956-n62jm   0/1     ContainerCreating   0          1s
nginx-deployment-85fc747956-7vgpg   0/1     ContainerCreating   0          1s
nginx-deployment-85fc747956-rkhf8   0/1     ContainerCreating   0          2s

nginx-deployment-85fc747956-n62jm   1/1     Running             0          2s
nginx-deployment-85fc747956-rkhf8   1/1     Running             0          2s
nginx-deployment-85fc747956-7vgpg   1/1     Running             0          3s
```

<br><br><br>

이전 버젼으로 롤백이 잘 됐는지 아래 명령어로 확인해보세요.
```bash
ubuntu@ip-172-31-23-60:~$ kubectl get pods
NAME                                READY   STATUS    RESTARTS   AGE
nginx-deployment-85fc747956-7vgpg   1/1     Running   0          92s
nginx-deployment-85fc747956-n62jm   1/1     Running   0          92s
nginx-deployment-85fc747956-rkhf8   1/1     Running   0          92s
ubuntu@ip-172-31-23-60:~$ kubectl describe pod nginx-deployment-85fc747956-7vgpg | grep -i image
    Image:          nginx:1.18
    Image ID:       docker-pullable://nginx@sha256:e90ac5331fe095cea01b121a3627174b2e33e06e83720e9a934c7b8ccc9c55a0
  Normal  Pulled     108s  kubelet            Container image "nginx:1.18" already present on machine
```

> 💻 명령어
>```bash
>kubectl get pods
>```
>```bash
>kubectl describe pod [POD-NAME] | grep -i image
>```
> [POD-NAME] 에는 앞에서 조회된 POD 중 하나의 이름을 넣어주세요.

<br><br><br>

다 해보셨으면 다음 실습을 위해 Object들을 삭제해주세요.  
```bash
ubuntu@ip-172-31-23-60:~$ kubectl delete -f nginx-recreate.yaml
deployment.apps "nginx-deployment" deleted
```

> 💻 명령어
>```bash
>kubectl delete -f nginx-recreate.yaml
>```

<br>

정리 후 상태는 아래와 같습니다.
```bash
ubuntu@ip-172-31-23-60:~$ kubectl get deployments
No resources found in default namespace.
```

> 💻 명령어
>```bash
>kubectl get deployments
>```

<br>

이제 두 번째 터미널은 Ctrl + c 를 눌러 Watch를 멈추겠습니다.

<br><br><br><br><br>

## 3. RollingUpdate 방법으로 업데이트 해보기

이번엔 **RollingUpdate** 입니다.  
기존에 서비스되고 있던 Pod들을 새로운 Pod로 조금씩(N개씩) 업데이트 하는 방식입니다.

Deployment 의 `.spec.strategy`를 아래와 같이 지정하면 됩니다.
```yaml
spec:
  strategy:
    type: RollingUpdate
```

<br>

아래는 **RollingUpdate** 방식을 적용한 **Deployment** 예제파일입니다.  
실습을 위해 아래 파일을 만들어주세요.
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  labels:
    app: my-nginx
    tier: frontend
spec:
  replicas: 3
  strategy:
    type: RollingUpdate
  selector:
    matchLabels:
      app: my-nginx
  template:
    metadata:
      labels:
        app: my-nginx
      name: my-nginx
    spec:
      containers:
      - image: nginx:1.18
        name: my-nginx
        ports:
        - containerPort: 80
```
> 파일명은 **nginx-rollingupdate.yaml**로 합니다.

<br><br><br>

다음과 같이 Deployment를 생성합니다.
```bash
ubuntu@ip-172-31-23-60:~$ kubectl apply -f nginx-rollingupdate.yaml
deployment.apps/nginx-deployment created
```

> 💻 명령어
>```bash
>kubectl apply -f nginx-rollingupdate.yaml
>```

<br><br><br>

그리고, 생성된 Object들도 확인해 보겠습니다.
```bash
ubuntu@ip-172-31-23-60:~$ kubectl get all
NAME                                    READY   STATUS    RESTARTS   AGE
pod/nginx-deployment-85fc747956-4fld8   1/1     Running   0          14s
pod/nginx-deployment-85fc747956-cjtgn   1/1     Running   0          14s
pod/nginx-deployment-85fc747956-hkvgs   1/1     Running   0          14s

NAME                 TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
service/kubernetes   ClusterIP   10.96.0.1    <none>        443/TCP   4d16h

NAME                               READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/nginx-deployment   3/3     3            3           14s

NAME                                          DESIRED   CURRENT   READY   AGE
replicaset.apps/nginx-deployment-85fc747956   3         3         3       14s
```

> 💻 명령어
>```bash
>kubectl get all
>```

<br><br><br>

생성된 Deployment의 정보를 보고 현재 실행된 이미지를 확인해봅니다.
```bash
ubuntu@ip-172-31-23-60:~$ kubectl describe deployment nginx-deployment | grep -i image
    Image:        nginx:1.18
```

> 💻 명령어
>```bash
>kubectl describe deployment nginx-deployment | grep -i image
>```

사용된 Image는 `nginx:1.18` 입니다.

<br><br><br>

업데이트를 위해서 Deployment yaml파일에서 버젼을 변경하구요.
```bash
ubuntu@ip-172-31-23-60:~$ sed -i 's/image: nginx:1.18/image: nginx:1.19/g' nginx-rollingupdate.yaml
```

> 💻 명령어
>```bash
>sed -i 's/image: nginx:1.18/image: nginx:1.19/g' nginx-rollingupdate.yaml
>```

<br><br><br>

**두 번째 Terminal**에는 확인 할 명령어를 실행한 후에
```bash
ubuntu@ip-172-31-23-60:~$ kubectl get pods --watch
NAME                                READY   STATUS    RESTARTS   AGE
nginx-deployment-85fc747956-4fld8   1/1     Running   0          92s
nginx-deployment-85fc747956-cjtgn   1/1     Running   0          92s
nginx-deployment-85fc747956-hkvgs   1/1     Running   0          92s

```

> 💻 명령어 (Terminal 2)
>```bash
>kubectl get pods --watch
>```

<br><br><br>

**첫 번재 Terminal**에서 업데이트를 합니다.
```bash
ubuntu@ip-172-31-23-60:~$ kubectl apply -f nginx-rollingupdate.yaml
deployment.apps/nginx-deployment configured
```

> 💻 명령어
>```bash
>kubectl apply -f nginx-rollingupdate.yaml
>```

<br><br><br>

**두 번째 Terminal**은 아래와 비슷한 걸 볼 수 있을겁니다. **Recreate**때와는 달리 Pod들이 순차적으로 변경되는 걸 볼 수 있습니다.
```bash
ubuntu@ip-172-31-23-60:~$ kubectl get pods --watch
NAME                                READY   STATUS    RESTARTS   AGE
nginx-deployment-85fc747956-4fld8   1/1     Running   0          92s
nginx-deployment-85fc747956-cjtgn   1/1     Running   0          92s
nginx-deployment-85fc747956-hkvgs   1/1     Running   0          92s

nginx-deployment-7dd48c557f-4k4zh   0/1     Pending   0          0s
nginx-deployment-7dd48c557f-4k4zh   0/1     Pending   0          0s
nginx-deployment-7dd48c557f-4k4zh   0/1     ContainerCreating   0          0s
nginx-deployment-7dd48c557f-4k4zh   1/1     Running             0          1s
nginx-deployment-85fc747956-4fld8   1/1     Terminating         0          111s
nginx-deployment-7dd48c557f-s99mz   0/1     Pending             0          0s
nginx-deployment-7dd48c557f-s99mz   0/1     Pending             0          0s
nginx-deployment-7dd48c557f-s99mz   0/1     ContainerCreating   0          0s
nginx-deployment-85fc747956-4fld8   0/1     Terminating         0          112s
nginx-deployment-85fc747956-4fld8   0/1     Terminating         0          112s
nginx-deployment-85fc747956-4fld8   0/1     Terminating         0          112s
nginx-deployment-7dd48c557f-s99mz   1/1     Running             0          1s
nginx-deployment-85fc747956-cjtgn   1/1     Terminating         0          112s
nginx-deployment-7dd48c557f-bvsl8   0/1     Pending             0          0s
nginx-deployment-7dd48c557f-bvsl8   0/1     Pending             0          0s
nginx-deployment-7dd48c557f-bvsl8   0/1     ContainerCreating   0          0s
nginx-deployment-7dd48c557f-bvsl8   1/1     Running             0          1s
nginx-deployment-85fc747956-cjtgn   0/1     Terminating         0          113s
nginx-deployment-85fc747956-hkvgs   1/1     Terminating         0          113s
nginx-deployment-85fc747956-cjtgn   0/1     Terminating         0          113s
nginx-deployment-85fc747956-cjtgn   0/1     Terminating         0          113s
nginx-deployment-85fc747956-hkvgs   0/1     Terminating         0          114s
nginx-deployment-85fc747956-hkvgs   0/1     Terminating         0          114s
nginx-deployment-85fc747956-hkvgs   0/1     Terminating         0          114s
```

<br><br><br>

첫 번째 Terminal에서 아래와 같이 업데이트 이후의 변경사항도 확인해보세요.
```bash
ubuntu@ip-172-31-23-60:~$ kubectl describe deployment nginx-deployment | grep -i image
    Image:        nginx:1.19
```

> 💻 명령어
>```bash
>kubectl describe deployment nginx-deployment | grep -i image
>```

<br><br><br>

새로 생성된 Pod의 정보도 확인해봅니다.
```bash
ubuntu@ip-172-31-23-60:~$ kubectl get pods
NAME                                READY   STATUS    RESTARTS   AGE
nginx-deployment-7dd48c557f-4k4zh   1/1     Running   0          87s
nginx-deployment-7dd48c557f-bvsl8   1/1     Running   0          85s
nginx-deployment-7dd48c557f-s99mz   1/1     Running   0          86s
ubuntu@ip-172-31-23-60:~$ kubectl describe pod nginx-deployment-7dd48c557f-4k4zh | grep -i image
    Image:          nginx:1.19
    Image ID:       docker-pullable://nginx@sha256:df13abe416e37eb3db4722840dd479b00ba193ac6606e7902331dcea50f4f1f2
  Normal  Pulled     102s  kubelet            Container image "nginx:1.19" already present on machine
```

> 💻 명령어
>```bash
>kubectl get pods
>```
>```bash
>kubectl describe pod [POD-NAME] | grep -i image
>```
> [POD-NAME] 에는 앞에서 조회된 POD 중 하나의 이름을 넣어주세요.

<br><br><br>

앞에서와 마찬가지로 롤백도 해보세요.  
자세한 설명은 생략하고 명령어들만 알려드릴게요.

> 💻 명령어
>```bash
>kubectl rollout history deployment nginx-deployment
>```
>```bash
>kubectl rollout undo deployment nginx-deployment --to-revision=1
>```
>```bash
>kubectl get pods
>```
>```bash
>kubectl describe pod [POD-NAME] | grep -i image
>```

<br><br><br>

다 해보셨으면 다음 실습을 위해 Object들을 삭제해주세요.  
```bash
ubuntu@ip-172-31-23-60:~$ kubectl delete -f nginx-rollingupdate.yaml
deployment.apps "nginx-deployment" deleted
```

> 💻 명령어
>```bash
>kubectl delete -f nginx-rollingupdate.yaml
>```

<br>

정리 후 상태는 아래와 같습니다.
```bash
ubuntu@ip-172-31-23-60:~$ kubectl get deployments
No resources found in default namespace.
```

> 💻 명령어
>```bash
>kubectl get deployments
>```

<br>

두 번째 터미널은 Ctrl + c 를 눌러 Watch를 멈추겠습니다.

<br>

끝~
