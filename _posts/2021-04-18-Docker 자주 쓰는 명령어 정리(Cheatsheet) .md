---
title: "2021-04-18-Docker 자주 쓰는 명령어 정리(Cheatsheet)"
excerpt: "과거 vmware나 virtualbox를 설치하여 무겁고 번거롭게 가상화 환경을 구축하였다면 현재는 Docker 로 간편하고 빠르게 원하는 환경을 구축한다. 하지만 항상 헷갈리거나 잊어먹는 명령어들이 있어 아래의 포스팅으로 정리를 해놓고자 한다."
toc: true
toc_sticky: true
categories:
  - Docker
  - Cheat sheet
tags:
  - Docker
  - Container
  - Docker Hub
  - '2021'
last_modified_at: 2021-04-18
---

나의 업무나 과업을 편하게 만들어 주는 여러 기술중에 가장 혁신적인 한 가지를 꼽자면 고르기는 쉽지 않겠지만 바로 Docker 이다.
과거 vmware나 virtualbox를 설치하여 무겁고 번거롭게 가상화 환경을 구축하였다면 현재는 Docker 로 간편하고 빠르게 원하는 환경을 구축한다. 하지만 항상 헷갈리거나 잊어먹는 명령어들이 있어 아래의 포스팅으로 정리를 해놓고자 한다. (일종의 치트시트랄까?)

**이 포스팅의 모든 내용은 [왕초보도 따라하는 도커 기초 강의](https://youtube.com/playlist?list=PLnIaYcDMsSczk-byS2iCDmQCfVU_KHWDk){: target="_blank"} 를 참고하여 작성한 것이다.**

## Docker 라이프사이클

![docker_lifecycle](/assets/images/2021/04/docker_lifecycle.png){: .align-center}

- `docker pull` : Registry로부터 Docker Image를 가져온다.
- `docker push` : Registry로 Docker Image를 업로드한다.
- `docker create` : Docker Image로부터 컨테이너를 만든다.
- `docker commit` : 컨테이너로부터 Docker Image를 만든다.
- `docker run` : 컨테이너를 생성한다. (CREATE + START)
- `docker start` : 컨테이너를 다시 실행시킨다.
- `docker stop` : 컨테이너를 정지시킨다.
- `docker rm` : 컨테이너를 삭제한다.
- `docker rmi` : Docker Image를 삭제한다.
- `docker attach` : 실행중인 컨테이너에 접속한다.

## 도커 명령어 정리
### 포트포워딩으로 톰캣 실행

tomcat 이미지를 받은 뒤에 `tc`라는 이름(--name)의 컨테이너로 내부 8080 포트를 호스트의 80 포트로 포워딩(-p) 하여 백그라운드로(-d) 실행한다.

```bash
sudo docker pull consol/tomcat-7.0
sudo docker run -d --name tc -p 80:8080 tomcat
```

### 컨테이너 내부 쉘 실행

bash 쉘을 실행하여 컨테이너 내부의 쉘 접속

```bash
sudo docker exec -it tc /bin/bash
```

### 컨테이너 로그 확인

컨테이너의 구동 이후 로그를 확인할 수 있다.
로그는 해당 컨테이너의 `stdout`, `stderr` 출력

```bash
sudo docker logs tc
```

### 호스트 및 컨테이너 간 파일 복사

호스트<->컨테이너, 컨테이너<->컨테이너 간의 파일 복사가 가능하다.

```bash
sudo docker cp <path> <to container>:<path>
sudo docker cp <from container>:<path> <path>
sudo docker cp <from container>:<path> <to container>:<path>
```

### 도커 컨테이너 모두 삭제

보통은 컨테이너의 name이나 id값으로 해당 컨테이너를 삭제하나 아래의 명령어를 통해 한번에 모든 컨테이너를 삭제할 수 있다.

참고로 `docker ps -a -a`를 하게 되면 모든 컨테이너의 id가 출력되게 된다.

```bash
sudo docker stop `sudo docker ps -a -q`
sudo docker rm `sudo docker ps -a -q`
```

### 도커 이미지 모두 삭제

위의 명령어는 도커 이미지에도 적용된다. 따라서 모든 도커 이미지를 삭제하고 싶으면 아래의 명령어를 사용한다.

```bash
docker rmi `docker images -q`
```

### 임시 컨테이너 생성

컨테이너가 stop 시 자동으로 삭제되도록 옵션을 줄 수 있다.(--rm)

```bash
sudo docker run -d -p 80:8080 --rm --name tc tomcat 
```

## 실습 1. 도커 이미지 받아서 서비스 띄워보기 

한 가지 실습을 해보자. `Jenkins`라는 서비스가 있는 이미지를 검색 후 받아서 컨테이너로 실행시키고 컨테이너 내부 포트를 외부로 포트포워딩 시켜 접속해보고자 한다.

### 1. 기존에 설치된 모든 컨테이너와 이미지 정지 및 삭제

```bash
sudo docker stop `sudo docker ps -a -q`
sudo docker rm `sudo docker ps -a -q`
sudo docker rmi `sudo docker images -q`
```

### 2. `Jenkins` 이미지 검색

```bash
sudo docker search jenkins
```

아래와 같이 여러 이미지들이 출력된다. 우리는 이 목록에서 `jenkins/jenkins`를 설치해보겠다.

![docker search jenkins](/assets/images/2021/04/docker_search_jenkins.png)

### 3. `jenkins/jenkins` 도커 이미지 받기

```bash
docker pull jenkins/jenkins
```

### 4. 해당 서비스가 어떤 포트를 사용하는 지 확인

호스트와 컨테이너 간 포트포워딩을 위해 어떤 포트들을 사용하고 있는지 찾아봐야 한다. `docker inspect`라는 명령어를 통해 이미지에 대한 설명을 볼 수 있다.

```bash
sudo docker inspect jenkins
```

![docker_inspect_jenkins](/assets/images/2021/04/docker_inspect_jenkins.png)

이미지에 대한 설명이 길게 나오지만 아래 `ExposedPorts` 부분이 우리가 참고할 부분이다. 2개의 포트를 사용하고 있고 우리가 포트 포워딩할 포트는 `8080/tcp` 이다.

### 5. Jenkins 컨테이너 구동

백그라운드로 실행시키고 컨테이너의 8080 포트를 호스트의 8080 포트와 포워딩 해준다.

```bash
docker run -d -p 8080:8080 --name jk jenkins/jenkins
```

구동한 이후 브라우저를 통해 웹을 접속해서 아래와 같은 화면이 뜨면 성공이다. 다만 초기 패스워드가 필요한 상태이다.

![docker_jenkins_web](/assets/images/2021/04/docker_jenkins_web.png)

### 6. 초기 패스워드 찾기

패스워드를 찾기 위해서는 크게 2가지 방법이 있다.

1. 초기 웹페이지에 명시된 경로(/var/jenkins_home/secrets/initialAdminPassword) 찾아가기
2. `docker log` 보기

2가지 방법을 모두 이용해 보겠다.

먼저 컨테이너 내부에 접속해야 한다. 혹은 `cat`을 이용하여서 바로 출력해보도록 하자.

```bash
sudo docker exec -it jk cat /var/jenkins_home/secrets/initialAdminPassword
```

![jenkins_password](/assets/images/2021/04/jenkins_password.png)

위와 같이 출력이 됨을 확인할 수 있다. 그러면 `docker log`를 통해 나오는 패스워드도 동일한지 확인해 주자.

```bash
sudo docker logs jk
```

![docker_jenkins_logs](/assets/images/2021/04/docker_jenkins_logs.png)

위에서 확인한 패스워드와 동일함을 확인할 수 있다.

## Reference
- [왕초보도 따라하는 도커 기초 강의](https://youtube.com/playlist?list=PLnIaYcDMsSczk-byS2iCDmQCfVU_KHWDk){: target="_blank"}
- [도커(Docker)치트 시트](https://gist.github.com/nacyot/8366310){: target="_blank"}