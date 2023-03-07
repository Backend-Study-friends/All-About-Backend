이번 시간에는 `Docker`가 무엇인지? 그리고 `Docker`의 기본 명령어에 대해서 공부해보겠습니다.

## Docker란?

애플리케이션 실행에 필요한 환경을 하나의 이미지로 만들고, 그 이미지를 사용하여 다양한 환경에서 애플리케이션 실행 환경을 구축 및 운용하기 위한 오픈소스 플랫폼

## Docker의 기능

### Docker 이미지를 만드는 기능 (Build)

Docker는 애플리케이션 실행에 필요한 프로그램 본체, 라이브러리, 미들웨어, OS나 네트워크 설정 등을 하나로 모아서 Docker 이미지를 만든다.
Docker 이미지는 애플리케이션 실행에 필요한 파일들이 저장된 디렉토리이다.

### Docker 이미지를 공유하는 기능 (Ship)

Docker의 이미지는 Docker 레지스트리를 이용하여 공유할 수 있다.
Docker Hub를 이용하면 기본 베이스 이미지(jdk, node 등)을 사용할 수 있고 공식 이미지 말고도 개인적으로 만든 이미지를 자유롭게 공개하여 공유할 수 있다.

### Docker 컨테이너를 작동시키는 기능 (Run)

Docker는 Linux 상에서 컨테이너 단위로 서버 기능을 작동시킨다.
컨테이너의 바탕이 되는 것이 Docker 이미지이다.

## Docker의 기본 명령어

### Docker Hub

```docker
Docker hub 로그인
docker login -u [username] -p [userpassword]

Docker hub 로그아웃
docker logout
```

### image

```docker
image 받아오기
docker image pull [OPTIONS] NAME[:TAG]

[OPTIONS]
--all-tags, -a : 리포지토리에서 태그가 지정된 모든 이미지 다운로드
--disable-content-trust : 이미지 확인 건너뛰기 (기본값 : true)
--platform : 서버가 다중 플랫폼을 지원하는 경우 플랫폼 설정
--quiet, -q : 자세한 출력 억제

image 목록 보기
docker image ls [OPTIONS] [REPOSITORY[:TAG]]
docker images [OPTIONS] [REPOSITORY[:TAG]]

[OPTIONS]
--all, -a : 모든 이미지 표시
--digests : 다이제스트 표시
--filter, -f : 조건에 따라 출력 필터링
--format : 사용자 지정 템플릿을 사용하여 출력 형식 지정
--no-trunc : 출력을 자르지 않고 모두 출력
--quiet, -q : 이미지 ID만 표시

image 삭제하기
docker image rm [OPTIONS] IMAGE [IMAGE...]

[OPTIONS]
--force, -f : 이미지 강제 삭제
--no-prune : 태그가 지정되지 않은 부모를 삭제하지 않음

image tag 붙이기
docker image tag SOURCE_IMAGE[:TAG] TARGET_IMAGE[:TAG]

image dockerhub에 올리기
docker image push [OPTIONS] NAME[:TAG]

[OPTIONS]
--all-tags, -a : 이미지의 모든 태그를 리포지토리로 푸시
--disable-content-trust : 이미지 서명 건너뛰기 (기본값 : true)
--quiet, -q : 자세한 출력 억제

image 만들기
docker image build [OPTIONS] PATH | URL | -

[OPTIONS]
참고 - https://docs.docker.com/engine/reference/commandline/image_build/
```

### Container

```docker
contanier 목록 보기
docker container ps [OPTIONS]

[OPTIONS]
--all,-a : 모든 컨테이너 표시 (기본값 : 실행되고 있는 것만 표시됨)
--filter, -f : 제공된 조건에 따라 출력 필터링
--format : 사용자 지정 템플릿을 사용하여 출력 형식 지정
--last, -n : 마지막에 생성된 n개의 컨테이너 표시 (모든 상태 포함)
--latest, -l : 최근 생성된 컨테이너 모두 표시 (모든 상태 포함)
--no-trunc : 출력을 자르지 않고 모두 출력
--quiet, -q : 컨테이너 ID만 표시
--size, -s : 총 파일 크기 표시


container 만들기
docker container create [OPTIONS] IMAGE [COMMAND] [ARG...]

[OPTIONS]
참고 - https://docs.docker.com/engine/reference/commandline/container_create/

container 실행하기
docker container run [OPTIONS] IMAGE [COMMAND] [ARG...]

[OPTIONS]
참고 - https://docs.docker.com/engine/reference/commandline/container_run/

container 삭제하기
docker container rm [OPTIONS] CONTAINER [CONTAINER...]

[OPTIONS]
--force, -f : 실행중인 컨테이너 강제 제거
--link, -l : 지정된 링크 제거
--volumes, -v : 컨테이너와 연결된 익명의 볼륨 제거

container 멈추기
docker container stop [OPTIONS] CONTAINER [CONTAINER...]

[OPTIONS]
--signal, -s : 컨테이너로 보내는 신호
--time, -t : 컨테이너를 종료하기 전에 대기하는 시간 (초 단위)

container 시작하기
docker container start [OPTIONS] CONTAINER [CONTAINER...]

[OPTIONS]
--attach, -a : STDOUT/STDERR 및 순방향 신호 연결
--detach-keys : 컨테이너 분리를 위한 키 시퀀스 재정의
--interactive, -i : 컨테이너의 STDIN 연결

container 다시 시작하기
docker container restart [OPTIONS] CONTAINER [CONTAINER...]

[OPTIONS]
--signal, -s : 컨테이너로 보내는 신호
--time, -t : 컨테이너를 종료하기 전에 대기하는 시간 (초 단위)

container 로그 보기
docker container logs [OPTIONS] CONTAINER

[OPTIONS]
--details : 로그에 제공된 추가 세부정보 표시
--follow, -f : 로그 출력 따르기
--since : 타임스탬프 또는 상대적 기간 동안의 로그 표시
--tail, -n : 로그 끝에서 표시할 줄 개수
--timestamps, -t	: 타임스탬프 표시
--until : 타임스탬프 또는 상대적 기간 앞에 로그 표시

```

---

#### 이번시간에는 `도커`의 개념과 간단한 도커 명령어(image, container)에 대해서 공부했습니다. 명령어가 너무 많아 다 알아보지는 못하였지만 추가적으로 궁금한 명령어는 알려주시면 추가한 후 올리겠습니다. 다음 시간에는 docker에서 사용하는 volume과 network에 대해서 알아보도록 하겠습니다

---

**references**

- https://ko.wikipedia.org/wiki/%EB%8F%84%EC%BB%A4_(%EC%86%8C%ED%94%84%ED%8A%B8%EC%9B%A8%EC%96%B4)
- https://docs.docker.com/
