
# [Hands-on] 09. Kubernetes Workload(2)

![](./img/hands_on.png)

<br>

# Contents

**[1. ReplicaSet을 이용해 Pod 관리하기](#1-replicaset을-이용해-pod-관리하기)**  
**[2. Deployment를 이용해 Pod 관리하기](#2-deployment를-이용해-pod-관리하기)**

💾실습파일 위치 : [hands_on_files/ch09](../hands_on_files/ch09)

미리 준비된 실습용 파일을 사용하려는 경우 아래 디렉토리 이동 명령어를 실행해주세요.
```bash
ubuntu@ip-172-31-23-60:~$ cd ~/mspt3/hands_on_files/ch09
ubuntu@ip-172-31-23-60:~/mspt3/hands_on_files/ch09$
```

> 💻 명령어
>```bash
>cd ~/mspt3/hands_on_files/ch09
>```

---

## 1. ReplicaSet을 이용해 Pod 관리하기

이제 Pod를 관리하는 다른 방법을 알아보겠습니다.

첫 번째는 **ReplicaSet** 입니다. **ReplicaSet** 생성을 위해서 아래 파일을 작성합니다.

```yaml
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: nginx-replicaset
  labels:
    app: my-nginx
    tier: frontend
spec:
  replicas: 3
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
      - image: nginx:1.19.3
        name: my-nginx
        ports:
        - containerPort: 80
```
> 파일명은 **nginx-replicaset.yaml**로 합니다.

<br><br><br>

**spec**부분을 보시면, 우리가 원하는 **Pod**에 대한 **spec**이 보이고, 그 위에 `replicas: 3` 이라는 부분이 보이네요.  
이 부분이 핵심입니다.

나는 Nginx Pod를 세 개 원한다고 선언한 것입니다.

특별한 얘기가 없으면 yaml파일을 이용한 리소스 생성은 `kubectl apply` 명령어를 쓰시면 됩니다.

**ReplicaSet**을 생성해볼까요?
```bash
ubuntu@ip-172-31-23-60:~$ kubectl apply -f nginx-replicaset.yaml
replicaset.apps/nginx-replicaset created
```

> 💻 명령어
>```bash
>kubectl apply -f nginx-replicaset.yaml
>```

<br><br><br>

조회도 해보시구요.
```bash
ubuntu@ip-172-31-23-60:~$ kubectl get replicasets -o wide
NAME               DESIRED   CURRENT   READY   AGE    CONTAINERS   IMAGES         SELECTOR
nginx-replicaset   3         3         3       119s   my-nginx     nginx:1.19.3   app=my-nginx
```

> 💻 명령어
>```bash
>kubectl get replicasets -o wide
>```

<br><br><br>

상세조회 결과는 아래와 같습니다.
```bash
ubuntu@ip-172-31-23-60:~$ kubectl describe replicasets nginx-replicaset
Name:         nginx-replicaset
Namespace:    default
Selector:     app=my-nginx
Labels:       app=my-nginx
              tier=frontend
Annotations:  <none>
Replicas:     3 current / 3 desired
Pods Status:  3 Running / 0 Waiting / 0 Succeeded / 0 Failed
Pod Template:
  Labels:  app=my-nginx
  Containers:
   my-nginx:
    Image:        nginx:1.19.3
    Port:         80/TCP
    Host Port:    0/TCP
    Environment:  <none>
    Mounts:       <none>
  Volumes:        <none>
Events:
  Type    Reason            Age    From                   Message
  ----    ------            ----   ----                   -------
  Normal  SuccessfulCreate  3m57s  replicaset-controller  Created pod: nginx-replicaset-fjjxd
  Normal  SuccessfulCreate  3m57s  replicaset-controller  Created pod: nginx-replicaset-6x5rp
  Normal  SuccessfulCreate  3m57s  replicaset-controller  Created pod: nginx-replicaset-4b52g
```

> 💻 명령어
>```bash
>kubectl describe replicasets nginx-replicaset
>```

<br><br><br>

우리는 Pod를 직접 생성하지는 않았지만 Pod도 생성됐습니다.
ReplicaSet이 하는 일이 그런거니까요.

Pod도 조회해볼까요?
```bash
ubuntu@ip-172-31-23-60:~$ kubectl get pods --show-labels
NAME                     READY   STATUS    RESTARTS   AGE     LABELS
nginx-replicaset-4b52g   1/1     Running   0          4m55s   app=my-nginx
nginx-replicaset-6x5rp   1/1     Running   0          4m55s   app=my-nginx
nginx-replicaset-fjjxd   1/1     Running   0          4m55s   app=my-nginx
```

> 💻 명령어
>```bash
>kubectl get pods --show-labels
>```

<br><br><br>

이제 뭔가 좀 자동으로 돌아가는 모양새가 나오네요~

이제 생성한 리소스들을 삭제해볼게요.
`apply` 를 `delete` 로 바꿔주시면 됩니다. (ง˙∇˙)ว

```bash
ubuntu@ip-172-31-23-60:~$ kubectl delete -f nginx-replicaset.yaml
replicaset.apps "nginx-replicaset" deleted
```

> 💻 명령어
>```bash
>kubectl delete -f nginx-replicaset.yaml
>```

<br>

정리 후 상태는 아래와 같습니다.
```bash
ubuntu@ip-172-31-23-60:~$ kubectl get replicasets
No resources found in default namespace.
```

> 💻 명령어
>```bash
>kubectl get replicasets
>```

<br><br><br><br><br>

## 2. Deployment를 이용해 Pod 관리하기

좀 더 나가서, 이번엔 **Deployment** 입니다. 먼저 YAML파일을 만들어보겠습니다.
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-nginx-deployment
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
    spec:
      containers:
      - image: nginx:1.19.3
        name: my-nginx
        ports:
        - containerPort: 80
```
> 파일명은 **nginx-deployment.yaml**로 합니다.

<br><br><br>

일단 한번 생성해볼게요.
```bash
ubuntu@ip-172-31-23-60:~/mspt3/hands_on_files$ kubectl apply -f nginx-deployment.yaml
deployment.apps/my-nginx-deployment created
```

> 💻 명령어
>```bash
>kubectl apply -f nginx-deployment.yaml
>```

<br><br><br>

이번엔 새로운 명령어 `kubectl get all`을 해볼까요?
```bash
ubuntu@ip-172-31-23-60:~$ kubectl get all
NAME                                       READY   STATUS    RESTARTS   AGE
pod/my-nginx-deployment-55985c7fcf-2b2r4   1/1     Running   0          33s
pod/my-nginx-deployment-55985c7fcf-czs5p   1/1     Running   0          32s
pod/my-nginx-deployment-55985c7fcf-jwmbt   1/1     Running   0          33s

NAME                 TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
service/kubernetes   ClusterIP   10.96.0.1    <none>        443/TCP   4d11h

NAME                                  READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/my-nginx-deployment   3/3     3            3           33s

NAME                                             DESIRED   CURRENT   READY   AGE
replicaset.apps/my-nginx-deployment-55985c7fcf   3         3         3       33s
```

> 💻 명령어
>```bash
>kubectl get all
>```

오~ 다 나오네요... ٩(ˊᗜˋ*)و

<br><br><br>

Pod들을 Label까지 같이 보려면 아래와 같이 하면 됩니다.
```bash
ubuntu@ip-172-31-23-60:~$ kubectl get pods --show-labels
NAME                                   READY   STATUS    RESTARTS   AGE   LABELS
my-nginx-deployment-55985c7fcf-2b2r4   1/1     Running   0          82s   app=my-nginx,pod-template-hash=55985c7fcf
my-nginx-deployment-55985c7fcf-czs5p   1/1     Running   0          81s   app=my-nginx,pod-template-hash=55985c7fcf
my-nginx-deployment-55985c7fcf-jwmbt   1/1     Running   0          82s   app=my-nginx,pod-template-hash=55985c7fcf
```

> 💻 명령어
>```bash
>kubectl get pods --show-labels
>```

<br><br><br>

이제 Pod 하나를 삭제(**delete**)해 볼까요?
```bash
ubuntu@ip-172-31-23-60:~$ kubectl delete pods my-nginx-deployment-55985c7fcf-2b2r4
pod "my-nginx-deployment-55985c7fcf-2b2r4" deleted
```

> 💻 명령어
>```bash
>kubectl delete pods [POD-NAME]
>```
> [POD-NAME] 에는 앞에서 조회된 POD 중 하나의 이름을 넣어주세요.

<br><br><br>

그리고, 다시 조회를 해보면...
```bash
ubuntu@ip-172-31-23-60:~$ kubectl get pods --show-labels
NAME                                   READY   STATUS    RESTARTS   AGE     LABELS
my-nginx-deployment-55985c7fcf-7tkdt   1/1     Running   0          44s     app=my-nginx,pod-template-hash=55985c7fcf
my-nginx-deployment-55985c7fcf-czs5p   1/1     Running   0          4m34s   app=my-nginx,pod-template-hash=55985c7fcf
my-nginx-deployment-55985c7fcf-jwmbt   1/1     Running   0          4m35s   app=my-nginx,pod-template-hash=55985c7fcf
```

> 💻 명령어
>```bash
>kubectl get pods --show-labels
>```

새롭게 하나의 POD가 생성된 걸 볼 수 있습니다.  
ReplicaSet이 자기 역할을 다하고 있는 듯 하네요~  
이제 든든합니다.

<br><br><br>

이번엔 scale in/out 방법을 알아보겠습니다. (**replicas**를 조정)  
**명령형 커맨드** 방식으로는 이렇게 할 수 있습니다.
```bash
ubuntu@ip-172-31-23-60:~$ kubectl scale deployment my-nginx-deployment --replicas=5
deployment.apps/my-nginx-deployment scaled
```

> 💻 명령어
>```bash
>kubectl scale deployment my-nginx-deployment --replicas=5
>```

<br><br><br>

조회결과도 보겠습니다.
```bash
ubuntu@ip-172-31-23-60:~$ kubectl get all
NAME                                       READY   STATUS    RESTARTS   AGE
pod/my-nginx-deployment-55985c7fcf-7tkdt   1/1     Running   0          2m54s
pod/my-nginx-deployment-55985c7fcf-88jwb   1/1     Running   0          52s
pod/my-nginx-deployment-55985c7fcf-czs5p   1/1     Running   0          6m44s
pod/my-nginx-deployment-55985c7fcf-jwmbt   1/1     Running   0          6m45s
pod/my-nginx-deployment-55985c7fcf-jx8hx   1/1     Running   0          52s

NAME                 TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
service/kubernetes   ClusterIP   10.96.0.1    <none>        443/TCP   4d11h

NAME                                  READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/my-nginx-deployment   5/5     5            5           6m45s

NAME                                             DESIRED   CURRENT   READY   AGE
replicaset.apps/my-nginx-deployment-55985c7fcf   5         5         5       6m45s
```

> 💻 명령어
>```bash
>kubectl get all
>```

명령형 커맨드에서 지정한 대로 Pod의 개수가 다섯개가 되었습니다.  
새롭게 생성된 두 개의 Pod를 볼 수 있습니다.

Deployment와 ReplicaSet의 내용도 변경된게 보이네요. (Pod 개수에 대한 정보들)

<br><br><br>

`kubectl edit deployment my-nginx-deployment` 명령어로 생성된 리소스를 수정할 수도 있습니다.  
마치 vi editor를 이용하여 YAML파일을 수정하는 것과 동일합니다.

> 💻 명령어
>```bash
>kubectl edit deployment my-nginx-deployment
>```

위의 명령어를 실행한 후, `replicas`를 2로 바꾸고 저장후 빠져나옵니다. (숫자 변경 후 ESC를 누르고 `:wq` 입력 후 엔터)

<br><br><br>

조회결과는 아래와 같습니다.
```bash
ubuntu@ip-172-31-23-60:~$ kubectl get all
NAME                                       READY   STATUS    RESTARTS   AGE
pod/my-nginx-deployment-55985c7fcf-czs5p   1/1     Running   0          10m
pod/my-nginx-deployment-55985c7fcf-jwmbt   1/1     Running   0          10m

NAME                 TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
service/kubernetes   ClusterIP   10.96.0.1    <none>        443/TCP   4d11h

NAME                                  READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/my-nginx-deployment   2/2     2            2           10m

NAME                                             DESIRED   CURRENT   READY   AGE
replicaset.apps/my-nginx-deployment-55985c7fcf   2         2         2       10m
```

> 💻 명령어
>```bash
>kubectl get all
>```

<br><br><br>

그리고, 마지막으로 **선언형 방법**을 적용하려면 처음 사용된 yaml파일을 수정해주시면 됩니다.  
editor(e.g. VI Editor)를 이용하여 `.spec.replicas`부분을 수정하면 됩니다. (**4**로 변경)

그리고, 마법의 주문 `kubectl apply`를 하는거죠.
```bash
ubuntu@ip-172-31-23-60:~$ kubectl apply -f nginx-deployment.yaml
deployment.apps/my-nginx-deployment configured
```

> 💻 명령어
>```bash
>kubectl apply -f nginx-deployment.yaml
>```

<br><br><br>

조회결과는 아래와 같습니다.
```bash
ubuntu@ip-172-31-23-60:~$ kubectl get all
NAME                                       READY   STATUS    RESTARTS   AGE
pod/my-nginx-deployment-55985c7fcf-bqv7g   1/1     Running   0          48s
pod/my-nginx-deployment-55985c7fcf-czs5p   1/1     Running   0          14m
pod/my-nginx-deployment-55985c7fcf-dvl2w   1/1     Running   0          48s
pod/my-nginx-deployment-55985c7fcf-jwmbt   1/1     Running   0          14m

NAME                 TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
service/kubernetes   ClusterIP   10.96.0.1    <none>        443/TCP   4d11h

NAME                                  READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/my-nginx-deployment   4/4     4            4           14m

NAME                                             DESIRED   CURRENT   READY   AGE
replicaset.apps/my-nginx-deployment-55985c7fcf   4         4         4       14m
```

> 💻 명령어
>```bash
>kubectl get all
>```

<br><br><br>

마지막으로 생성한 리소스들을 삭제하고 마치겠습니다.  ˘◡˘  

```bash
ubuntu@ip-172-31-23-60:~$ kubectl delete -f nginx-deployment.yaml
deployment.apps "my-nginx-deployment" deleted
```

> 💻 명령어
>```bash
>kubectl delete -f nginx-deployment.yaml
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