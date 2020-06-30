---
title:  "asciinema를 이용해 터미널 화면 녹화하기"
excerpt: "포스팅을 하다보면 소스코드나 실행결과를 업로드 해야 할 경우가 있는데 그떄 쓰면 유용한 툴을 하나 찾게 되어서 포스팅해보고자 한다."
toc: true
toc_sticky: true
categories:
  - Tools
tags:
  - Tech
  - Shell
  - Asciinema
  - '2019'
last_modified_at: 2019-12-04
---

포스팅을 하다보면 소스코드나 실행결과를 업로드 해야 할 경우가 있는데 그떄 쓰면 유용한 툴을 하나 찾게 되어서 포스팅해보고자 한다.

![asciinema_main]({{site.url}}/assets/images/2019/12/asciinema-main.png)
*asciinema*

바로 [asciinema](https://asciinema.org/){: target="_blank"}라는 툴이다. 기능은 매우 Simple하다. 터미널에서 내가 타이핑한 내용과 출력물들을 모두 녹화해주고, 업로드 후에 공유할 수 있다.

아래는 공식 홈페이지에서 제공하는 데모인데, 한번 보면 무엇인지 이해가 될 것이다.

<script id="asciicast-113463" src="https://asciinema.org/a/113463.js" async></script>

실제 공식 홈페이지에 가보면 여러 public record 들을 볼 수가 있는데, 일반적인 콘솔 명령어서부터 star wars 같은 굉장히 고급(?)진 아스키아트들도 있다. 직관적으로 녹화할 수 있고, 간편하게 업로드하여 쉽게 공유할 수 있는 굉장히 편리한 도구이다.

### 어떻게 작동하지?

`asciinema`를 처음 접하고 들었던 의문은 어떻게 동작하는가? 였다. 물론 친절하게 [문서](https://asciinema.org/docs/how-it-works){:target="_blank"}로 정리되어 있어서 문서의 내용을 대충 요약해 정리하면 아래와 같다. 정확한 내용은 해당 문서를 참고하자.

> asciinema 프로젝트는 아래의 요소로 구성되어 있다.
> - **asciinema recorder**
> - **asciinema.org API**
> - **javascript player**
>
> **asciinema recorder**는 터미널로 가는 모든 출력을 캡처하는 역할을 한다. 사용자의 입력과 화면에 출력되는 내용부터 출력되는 시간까지 캡처하여 `.ascii` 라는 포맷으로 메모리에 저장된다고 한다. 캡처된 출력에는 모든 text와 보이지 않는 이스케이프(eof) 문자나 제어(ex. ^c) 문자열이 포함된다. 모든 녹화가 끝나면 해당 출력을 `asciicast`포맷으로 **asciinema.org**에 업로드한다.
> (상세한 구현 세부사항은 **asciinema recorder** 의 소스코드 참조)
>
> 녹화된 원시 스트림은 `ANSI-compatible video terminals parser`라는 것을 이용하여 파싱하게된다. 이때 녹화된 원본의 텍스트는 물론 출력되는 시간,색상,커서이동 등을 해석하고 출력을 해준다.
>
> 최종적으로 모든 텍스트 속성(bold, underline, inverse, ...)과 256색이 완벽하게 렌더링 된 터미널이 재생되게 된다.

### 설치하기

홈페이지에는 Linux나 MacOS는 물론 도커 기반의 설치법까지 설명되어 있다. 여튼 패키지 관리자를 통해 설치하는 거라 대부분 간단하다. 자세한 사용법은 [문서](https://asciinema.org/docs/installation){:target="_blank"}를 참고하자.

```
# Pip를 통한 설치
# python만 설치되어 있다면 모든 O/S에서 사용가능
sudo pip3 install asciinema
```
```
# Debian 계열
sudo apt-get install asciinema
```
```
# Fedora 계열 (<22)
sudo yum install asciinema

# Fedora 계열 (>=22)
sudo dnf install asciinema
```
```
# Ubuntu
sudo apt-add-repository ppa:zanchey/asciinema
sudo apt-get update
sudo apt-get install asciinema
```
```
# Homebrew (MacOS)
brew install asciinema
```
```
# Docker container 실행
docker pull asciinema/asciinema_main
```

컨테이너 실행 시 psudo-TTY를 할당하고(-t), STDIN을 열린 상태로 유지하고(-i) 설정파일이 있는 디렉토리 볼륨을 마운트(-v)해야 한다.

```
docker run --rm -ti -v "$HOME/.config/asciinema":/root/.config/asciinema asciinema/asciinema
```

해당 컨테이너는 Ubuntu 16.04 기반으로 녹화 시 필요한 소프트웨어가 있다면 별도로 사용자 정의 `Dockerfile`을 이용하거나 혹은 `/bin/bash`를 사용하여 컨테이너를 시작하고 수동으로 추가 패키지를 설치하면 된다.

```
docker run --rm -ti -v "$HOME/.config/asciinema":/root/.config/asciinema asciinema/asciinema /bin/bash
root@6689517d99a1:~# apt-get install foobar
root@6689517d99a1:~# asciinema rec
```


### 녹화하기

녹화도 정말 간편하다.

1. `asciinema rec` 명령어로 녹화를 시작한다.
2. 녹화를 할 터미널 작업을 한다.
3. 녹화를 끝내려면 터미널에 `exit`를 입력하거나 `ctrl+d`를 입력한다.
4. 후에 로컬에 저장할지 아니면 `asciinema.org`에 업로드할지 결정해야 하는데 `Enter`를 누르면 업로드 후 해당 링크의 주소가 나온다. `ctrl+c`를 누를 경우 로컬에 저장되며 해당 파일의 경로가 나온다.

참고로 온라인 저장소에 업로드하기 위해서는 `asciinema.org`에 계정이 있어야한다. 별도의 개인정보를 요구하지도 않고 메일주소만 입력하면 인증하면 가입이 완료된다. 가입이후에 업로드를 할 단말에서 아래의 명령을 실행하면 해당 게정과 단말이 연결된다.

```
$ asciinema auth
```

아래는 테스트로 녹화해본 영상이다. 참고로 나는 `asciinema rec`이라는 녹화 명령어를 alias로 `clirec`으로 설정하였다.


<script id="asciicast-285196" src="https://asciinema.org/a/285196.js" async></script>


생성된 링크로 들어가보면 게시물의 제목과 설명 그리고 출력되는 쉘의 종류와 썸네일을 설정할 수 있다.


![settings_page]({{site.url}}/assets/images/2019/12/settings-page.png)


공유도 HTML은 물론 마크다운으로도 공유할 수 있게 제공된다.


![share_option]({{site.url}}/assets/images/2019/12/share-option.png)


이외에 명령어에 대한 상세한 사용법은 [문서](https://asciinema.org/docs/usage){:target="_blank"}를 참고하자.

### Config

환경변수나 녹화속도, Idle time 등의 설정을 할 수도 있다.
해당 설정파일 위치는 다음과 같다.

```
$HOME/.config/asciinema/config
```

아래를 참고해서 설정파일을 설정하자.


```
[api]

; API server URL, default: https://asciinema.org
; If you run your own instance of asciinema-server then set its address here
; It can also be overriden by setting ASCIINEMA_API_URL environment variable
url = https://asciinema.example.com

[record]

; Command to record, default: $SHELL
command = /bin/bash -l

; Enable stdin (keyboard) recording, default: no
stdin = yes

; List of environment variables to capture, default: SHELL,TERM
env = SHELL,TERM,USER

; Limit recorded terminal inactivity to max n seconds, default: off
idle_time_limit = 2

; Answer "yes" to all interactive prompts, default: no
yes = true

; Be quiet, suppress all notices/warnings, default: no
quiet = true

[play]

; Playback speed (can be fractional), default: 1
speed = 2

; Limit replayed terminal inactivity to max n seconds, default: off
idle_time_limit = 1
```


### Reference
- [ascinema](https://asciinema.org){:target="_blank"}
