# MSP T3 - 2024 AWS Track

## Docker

- base image(ubuntu)와 Virtual machine의 Ubuntu는 어떻게 다른가요?
  - Virtual machine의 Ubuntu(OS)는 VM의 Guest OS에 해당합니다.
  - Container에서 Ubuntu image를 사용한다는 것은, Host 머신의 OS 커널 위에 Ubuntu 배포판을 컨테이너로 실행한다는 것입니다.

- docker commit 명령어 설명에서, 최상위 레이어에서 하위 레이어에 있는 파일을 삭제한 후 커밋하면 새롭게 만들어진 이미지에서는 그 파일이 어떻게 되는건가요?
  - 상위 레이어가 하위 레이어보다 우선합니다. 따라서, 위와 같은 경우는 최종 결과물에서 삭제된 것으로 표시욉니다.
  - 하지만, 하위 레이어에 파일이 존재하기 때문에 
 
- docker run --detach 명령어로 실행한 후 다시 해당 컨테이너로 접속이 가능한가요?
  - ...
    
---

## Kubernetes
