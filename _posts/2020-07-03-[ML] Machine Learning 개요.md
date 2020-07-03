---
title:  "[ML] Machine Learning 개요"
excerpt: "코로나가 기승을 부리고 몇 주가 지났던 4월 초부터 우연한 기회로 `Machine Learning`에 대해 공부할 기회가 생기게 되었다."
categories:
  - Machine Learning
tags:
  - Machine Learning
  - Python
  - Scikit-Learn
  - Hands On Machine Learning
  - '2020'
toc: true
toc_sticky: true
last_modified_at: 2020-07-03
---


![handson_ml](/assets/images/2020/07/handson-ml.png){: .align-center}


코로나가 기승을 부리고 몇 주가 지났던 4월 초부터 우연한 기회로 `Machine Learning`에 대해 공부할 기회가 생기게 되었다. 기본적인 `Machine Learning`에 대한 개념부터 `CNN`,`RNN`, `Deep Learning`, `Reinforcement Learning` 등 여러가지 개념들을 공부하였으나 개인적으로 다시 독학하는 내용을 블로그에 기록해두고자 한다. 교재는 얼마 전 2판이 나온 [Hands-On Machine Learning with Scikit-Learn, Keras & TensorFlow](https://tensorflow.blog/handson-ml2/){: target="_blank"}로 정하게 되었다. `Google Colab`을 통해 실습을 제공하고 옮긴이의 유튜브 강의도 있었기에 차근차근 따라가보고자 한다.

**해당 포스팅과 앞으로 이어질 포스팅들은 `Hands-on Machine Learning` 책의 내용과 강의 [YouTube](https://www.youtube.com/playlist?list=PLJN246lAkhQjX3LOdLVnfdFaCbGouEBeb){: target="_blank"}, 강의자료의 내용을 개인적으로 정리한 내용입니다.**

---

### Machine Learning 이란?


>`머신러닝`은 명시적인 프로그래밍 없이 컴퓨터가 학습하는 능력을 갖추게 하는 연구 분야다. - 아서 새뮤얼(Arthur Samuel, 1959)


>어떤 작업 `T`에 대한 컴퓨터 프로그램의 성능을 `P`로 측정했을 때 경험 `E`로 인해 성능이 향상됐다면, 이 컴퓨터 프로그램은 작업 `T`와 성능 측정 `P`에 대해 경험 `E`로 학습한 것이다. - 톰 미첼(Tom Mitchell, 1997)


위의 문구는 머신러닝에 대한 간단한(?) 정의를 표기한 것이다. 위쪽은 일반적인 정의, 아래는 조금 더 공학적인 정의라 볼 수 있다. 일반적으로 머신러닝이라는 말을 들었을 때 생각나는 느낌은 주인의 말을 충실히 이행하는 기계라던가 혹은 영화 터미네이터의 스카이넷, 최근 이세돌과의 바둑 대결을 펼친(그 최근도 벌써 2년이 넘어가고 있다.) 구글 딥마인드의 `AlphaGo` 등이 생각난다.

조금 더 일반적인 문장으로 표현하자면 *머신러닝은 데이터에서부터 학습하도록 컴퓨터를 프로그래밍하는 과학(또는 예술)이다.* 라는 문장이 마음에 든다.

책에서는 예로 스팸 필터를 들고 있다. 스팸 필터란 스팸 메일과 일반 메일의 샘플을 이용해 스팸 메일 구분법을 배울 수 있는 머신러닝 프로그램이다. 여기서 위 `톰 미첼`이 정의했던 머신러닝 문장에 비할 경우 작업 `T`는 `새로운 메일이 스팸인지 구분하는 것`이고 `P`는 `정확히 분류된 메일의 비율`, 마지막으로 `E`는 `스팸메일을 분류하기 위한 많은 메일 데이터`가 될 것이다.


### 왜 Machine Learning을 사용하는가?


![figure_1](/assets/images/2020/07/figure-1.png){: .align-center}


우리가 스팸 필터를 만든다고 했을 때 여러 가지 방법이 있겠지만, 큰 틀로 위의 방식을 생각할 수 있을 것이다.

1. 스팸메일에 나오는 단어들의 패턴을 관찰한다. `신용카드`, `무료`, `대출` 같은 단어들이 나올 수 있다.
2. 발견한 각 단어를 감지하는 알고리즘을 작성하여 스팸 필터 프로그램에 적용한다.
3. 충분한 성능이 나올 때까지 1,2의 과정을 반복한다.

위의 프로그램은 한눈에 보기에도 한계가 보인다. 게임에서 해커와 안티해커의 끊임없는 싸움처럼 스팸 필터에 적용된 단어를 우회하는 단어를 적용하면 무용지물이 되어 버리기 때문이다. 이러한 문제 때문에 필터링 해야 하는 단어의 수는 점점 많아지고 규칙 또한 점점 길어지고 프로그램에 대한 유지 보수가 매우 힘들어 질 것이다.


![figure_2](/assets/images/2020/07/figure-2.png){: .align-center}


반면 위 구조는 스팸 메일에 자주 나타나는 패턴을 감지하여 어떤 단어와 구절이 스팸 메일을 판단하는 데 좋은 기준인지 자동으로 학습한다. 그러므로 프로그램이 훨씬 짧아지고 유지 보수하기 쉬우며 정확도 또한 높을 것이다.

추가적으로 우리는 머신러닝을 통해 배울 수도 있다. 예를 들어 스팸 필터가 충분한 스팸 메일로 훈련되었다면 스팸을 예측하는 데 가장 좋은 단어 및 단어의 조합이 무엇인지 확인할 수 있을 것이다. 이처럼 머신러닝 기술을 적용해서 대용량의 데이터를 분석하면 겉으로 보이지 않던 패턴을 발견할 수 있는데 이를 `데이터 마이닝(data mining)`이라고 한다.



### Machine Learning의 종류


### 모델 기반 예제


### Machine Learning의 주요 도전과제


### Reference
- [핸즈온 머신러닝 YouTube](https://www.youtube.com/playlist?list=PLJN246lAkhQjX3LOdLVnfdFaCbGouEBeb){:target="_blank"}
- [1장 한눈에 보는 머신 러닝](https://docs.google.com/presentation/d/19FB1IgRqKiICNW6cUqY4wANKiuftSadqywsDNoPSc34/edit?usp=sharing){:target="_blank"}
