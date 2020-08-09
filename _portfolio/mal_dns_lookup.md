---
title: "클린DNS 서비스 안전운용을 위한 모니터링 시스템 개발"
excerpt: "우회된 DNS IP 목록에 대해 정상적으로 응답을 주는지 주기적으로 모니터링"
header:
  teaser: /assets/images/portfolio/mal_dns_lookup/mal_dns_flow.png
sidebar:
  - title: "Tech"
    image: /assets/images/portfolio/mal_dns_lookup/mal_dns_flow.png
    image_alt: "logo"
    text: "Python, Flask, Javascript"
  - title: "Github"
    text: https://github.com/monetd/mal_nslookup
gallery:
  - url: /assets/images/portfolio/mal_dns_lookup/mal_dns_monitor.png
    image_path: assets/images/portfolio/mal_dns_lookup/mal_dns_monitor.png
    alt: "malicious dns monitor"
  - url: /assets/images/portfolio/mal_dns_lookup/mal_dns_ipmgmt.png
    image_path: assets/images/portfolio/mal_dns_lookup/mal_dns_ipmgmt.png
    alt: "malicious dns add/del"
  - url: /assets/images/portfolio/mal_dns_lookup/mal_dns_settings.png
    image_path: assets/images/portfolio/mal_dns_lookup/mal_dns_settings.png
    alt: "monitor settings"
  - url: /assets/images/portfolio/mal_dns_lookup/mal_dns_mailing.png
    image_path: assets/images/portfolio/mal_dns_lookup/mal_dns_mailing.png
    alt: "alert mailing"
---

- 클린DNS : 유해DNS IP(Pharming DNS)를 Backbone에서 정상응답을 주는 클린DNS로 라우팅 해주는 서비스
- 우회된 IP 목록에 대해 정상적인 응답을 주는지 주기적으로 확인
- 등록된 신규 유해DNS IP에 대해 lookup 모니터링 등록/삭제 가능
- 메일링/가청 경보 기능

{% include gallery caption="Screenshots" %}