---
title: "머신러닝을 활용한 악성사이트 탐지"
excerpt: "국제 구간의 웹 패킷 및 웹 사이트의 주요 Feature를 학습하여 악성 여부 판별"
header:
  <!-- image: /assets/images/portfolio/ml_malicious_web_detect/gsp_web.png -->
  teaser: /assets/images/portfolio/ml_malicious_web_detect/gsp_web.png
sidebar:
  - title: "Tech"
    image: /assets/images/portfolio/ml_malicious_web_detect/gsp_web.png
    image_alt: "logo"
    text: "Python, Scikit-Learn, Flask, Celery, Redis, Docker, Javascript"
  - title: "Github"
    text: https://github.com/ktaisec/Malicious-Web-Detection-Using-Machine-Learning
gallery:
  - url: /assets/images/portfolio/ml_malicious_web_detect/data_collect_flow.png
    image_path: assets/images/portfolio/ml_malicious_web_detect/data_collect_flow.png
    alt: "Data Collect Flow"
  - url: /assets/images/portfolio/ml_malicious_web_detect/data_preprocess_flow.png
    image_path: assets/images/portfolio/ml_malicious_web_detect/data_preprocess_flow.png
    alt: "Data Pre-Process Flow"
  - url: /assets/images/portfolio/ml_malicious_web_detect/data_train_ratio.png
    image_path: assets/images/portfolio/ml_malicious_web_detect/data_train_ratio.png
    alt: "Data Train Ratio"
  - url: /assets/images/portfolio/ml_malicious_web_detect/model_verify.png
    image_path: assets/images/portfolio/ml_malicious_web_detect/model_verify.png
    alt: "Model Verify"
  - url: /assets/images/portfolio/ml_malicious_web_detect/mailing.png
    image_path: assets/images/portfolio/ml_malicious_web_detect/mailing.png
    alt: "Mailing(PoC)"
  - url: /assets/images/portfolio/ml_malicious_web_detect/gsp_web.png
    image_path: assets/images/portfolio/ml_malicious_web_detect/gsp_web.png
    alt: "Web(PoC)"
---

- 국제/IX 구간의 웹 패킷 및 웹 사이트의 주요 Feature(URL, Domain, HTML, JavaScript, Whois 정보 등)를 학습하여 악성 여부 판별
- 개선사항
  - (기존) 수집된 웹 페이지(URI) 중 파일이 있는 경우에만 악성여부 분석
  - (개선) 파일이 없는 웹 페이지(URI)의 경우에도 악성유무 판별 가능한 서비스 구현

  - (기존)악성여부 검사건수 : 6천 건/일 -> 300만 건/일(기존대비 약 500배 증가)
  - (개선)악성 검출건수 : 50건/일 -> 300건/일 (기존대비 약 6배 증가)

- 데이터 수집
  - KT GiGA Secure Platform(GSP)를 이용한 국제구간 웹 패킷 수집(일 350만 건)
  - Google Safe Browsing API를 이용한 데이터 라벨


- 데이터 전처리
  - 수집한 웹 페이지로부터 총 183개 Feature 추출
    - URI 기반 Feature(96개)
    - HTML 기반 Feature(43개)
    - JavaScript 기반 Feature(28개)
    - Host 기반 Feature(16개)
  - Message Queue(Celery + Redis)를 활용하여 Feature 추출 프로세스를 병렬로 수행
    - (기존)초당 0.3개 Tasks 수행
    - (개선)초당 4.95개 Tasks 수행(처리속도 약 16배 증가)

- 모델 생성/평가/개선
  - 머신러닝 지도 학습 분류 알고리즘 사용(Scikit-learn)
  - 하이퍼파라미터 Fine-Tune을 위해 GridSearchCV() 사용
  - 보안전문가 2차 검증을 통해 오탐/미탐 여부 확인
  - 최종으로 Random Forest(F1 Score: 99.98) 알고리즘 사용

- 프로토타입 및 성과 검증
  - 모델 판정 악성 URL 메일링 발송기능 개발
  - 보안관제/운용 실무자가 직접 웹 페이지의 악성여부 확인해 볼 수 있는 웹 페이지 PoC 개발


{% include gallery caption="Screenshots" %}
