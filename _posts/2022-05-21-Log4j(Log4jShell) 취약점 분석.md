---
title: "Log4j(Log4Shell) 취약점 분석"
excerpt: "작년말을 뜨겁게 달구었던 Log4j(Log4jshell) 취약점에 대한 내용을 포스팅하려고 한다."
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
|Logger|로깅 메시지를 Appender에 전달 어느 레벨까지 Logging 을 수행할지 결정|로그 필터링|
|Appender|로그의 출력위치를 결정|로그 기록|
|Layout|로그가 어떤 형식으로 출력될 것인지 결졍|로그 포맷팅|

## History

### Log4j Lookups

### JNDI(Java Naming and Directory Interface)

## Attack Flow

## Why CVSS 10.0?

## 실습

## Reference