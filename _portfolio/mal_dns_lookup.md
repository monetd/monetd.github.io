---
title: "클린DNS 서비스 안전운용을 위한 모니터링 시스템 개발"
excerpt: "유해 IP에 대한 포트 스캔 기능을 웹 페이지로 구현"
header:
  <!-- image: /assets/images/portfolio/port_scanner/scanner_list.PNG -->
  teaser: /assets/images/portfolio/port_scanner/scanner_list.PNG
sidebar:
  - title: "Tech"
    image: http://placehold.it/350x250
    image_alt: "logo"
    text: "Python, Django, Celery, Redis, Docker, Javascript"
  - title: "Github"
    text: https://github.com/monetd/port_scanner
gallery:
  - url: /assets/images/portfolio/port_scanner/scanner_list.PNG
    image_path: assets/images/portfolio/port_scanner/scanner_list.PNG
    alt: "scan job list"
  - url: /assets/images/portfolio/port_scanner/scanner_post.PNG
    image_path: assets/images/portfolio/port_scanner/scanner_post.PNG
    alt: "scan job post"
  - url: /assets/images/portfolio/port_scanner/scanner_results.PNG
    image_path: assets/images/portfolio/port_scanner/scanner_results.PNG
    alt: "scan job result"
  - url: /assets/images/portfolio/port_scanner/scanner_excel.PNG
    image_path: assets/images/portfolio/port_scanner/scanner_excel.PNG
    alt: "scan job download"
---

- 유해 IP에 대한 포트 스캔 기능을 웹 페이지로 구현
- 동시에 여러 IP에 대해 포트 스캔 가능
- 여러 Scan job을 동시에 비동기식 방식으로 수행 가능
- 스캔 결과에 대해 .xlsx(Microsoft Excel) 형식으로 저장 가능
- 아래 스캔 옵션 지원
    - 스캔 안함 : 해당 IP의 국가/ISP/노드/서비스 정보만 출력
    - 빠른 스캔 : 주요 포트 100 개에 대해서 Scan(`nmap -F`)
    - 상세 스캔 : 주요 포트 1,000 개에 대해서 Scan(`nmap default scan`)
    - Ping 스캔 : Ping Scan(`nmap -sP`)

{% include gallery caption="Screenshots" %}

- 스캔 결과는 아래 정보 출력
    - 국가 : 국가 정보(`use GeoLite2 DB`)
    - ISP : ISP 정보(`use GeoLite2 DB`)
    - 노드 : (KT IP 일 경우) 노드정보
    - 서비스 : (KT IP 일 경우) Static/Dynamic IP
    - Status : Host Up/Down
    - TCP Open Port
    - UDP Open Port
    - HTTP Fingerprint : (HTTP일 경우) Title