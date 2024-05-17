# MSP T3 - 2024 SCP Track

## Docker

- multistage build에서 2-stage만 반영했을 때와 차이는 무엇인가요?
  - 1-stage의 과정을 dockerbuild시점 전에 별도로 처리한다면(e.g. jenkins에서 빌드), 2-stage만 가지고 docker build를 할 수 있습니다.
  - 두 가지 유형 중 필요한 형태를 사용하면 됩니다.

- multistage build에서 builder때문에 1-stage를 해야하는 건가요?
  - 네, 맞습니다. 빌드 패키지등 실행시점에 필요없는 것들을 1-stage에서 처리하고, 2-stage는 1-stage의 결과물(실행 시점에 필요한 것)만 이용하는 것입니다.
 
- hands-on 05 실습에서 Baseimage (FROM openjdk:8)는 어디서 어디로 다운로드(pull) 하나요?
  - Docker build가 실행되는 곳 (Docker daemon이 있는 곳)으로 다운로드(pull) 됩니다.
  - Dockerfile과 build context가 위치한 곳이 아닌, 빌드가 진행되는 곳 입니다.
  - 빌드되는 순서로 보자면 다음과 같습니다.
    - build context를 docker daemon으로 전달
    - dockerfile의 명령어를 순서대로 진행
      - FROM에 정의된 Baseimage를 Container registry에서 host 머신으로 pull
      - HelloDocker.java파일 복사 (COPY)
      - 디렉토리 이동(WORKDIR)
      - 빌드 (RUN javac ...)

### Docker Summary
![](img/docker_summary_24-scp.png)

---

## Kubernetes
