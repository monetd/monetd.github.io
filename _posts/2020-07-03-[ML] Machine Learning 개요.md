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


머신러닝 시스템의 종류는 굉장히 많으나 넓은 범주에서 분류하면 다음과 같다.

- 사람의 감독하에 훈련하는 것인지 그렇지 않은 것인지?
  - 지도 / 비지도 / 준지도 / 강화 학습
- 실시간으로 점진적인 학습을 하는 지 아닌지
  - 온라인 학습 / 배치 학습
- 단순하게 알고 있는 데이터 포인트와 새 데이터 포인트를 비교하는 것인지 아니면 과학자들이 하는 것처럼 훈련 데이터셋에서 패턴을 발견하여 예측 모델을 만드는지?
  - 사례 기반 학습 / 모델 기반 학습


#### 지도 학습


`지도 학습(supervised learning)`은 알고리즘에 주입하는 훈련 데이터에 `레이블(Label)`이라는 원하는 답이 있는 경우이다.

`분류(classification)`가 대표적인 지도 학습이며 앞서 예로 들었던 스팸 필터가 이 경우에 해당될 것이다.

![figure_3](/assets/images/2020/07/figure-3.png){: .align-center}

또 다른 전형적인 작업은 `특성(feature)`을 이용하여 중고차 가격같은 `타겟(target)` 수치를 에측하는 것이다. 이런 종류의 작업을 `회귀(Regression)`라고 한다.

![figure_4](/assets/images/2020/07/figure-4.png){: .align-center}

일부 회귀 알고리즘은 분류에 사용할 수도 있고 반대로 일부 분류 알고리즘을 회귀에 사용할 수도 있다. 예를 들면 `로지스틱 회귀`는 클래스에 속할 확률을 출력하는데 이를 이용하면 특정 메일이 스팸일 확률을 출력할 수도 있다.

아래는 대표적인 지도 학습 알고리즘들이다.

- k-최근접 이웃<sup>k-nearest neighbors</sup>
- 선형 회귀<sup>linear regression</sup>
- 로지스틱 회귀<sup>logistic regression</sup>
- 서포트 벡터 머신<sup>support vector machine(SVM)</sup>
- 결정 트리<sup>decesion tree</sup>와 랜덤 포레스트<sup>random forest</sup>
- 신경망<sup>neural networks</sup>


#### 비지도 학습


`비지도 학습(unsupervised learning)`은 말 그대로 훈련 데이터에 레이블이 없는 경우이다.

예를 들어 고양이/호랑이/기린을 사진을 보고 분류하는 알고리즘을 만든다고 치자. 각 사진에는 무슨 동물인지 정답(label)이 없다. 이 경우 사진의 동물이 무엇이라고 기계가 정의할 수는 없지만 비슷한 동물끼리 군집을 시켜줄 수는 있다.


> 다리가 4개고 줄무늬가 있는 그룹 (호랑이)
> 다리가 4개고 줄무늬가 없는 그룹 (고양이)
> *줄무늬 있는 고양이는 예외로 두자*
> 다리가 4개고 목이 긴 그룹 (기린)


![figure_5](/assets/images/2020/07/figure-5.png){: .align-center}


또 하나의 중요한 비지도 학습은 `이상치 탐지(outlier detection)`이다. 예를 들어 부정 거래를 막기 위해 이상한 신용카드 거래를 탐지하고, 제조 결함을 잡아내고, 학습 알고리즘에 주입하기 전에 데이터셋에서 이상한 값을 자동으로 제거하는 것 등이다.

그 밖에 대량의 데이터에서 특성 간의 흥미로운 관계를 찾는 `연관 규칙 학습(association rule learning)`에도 사용될 수 있다.

예를 들어 슈퍼마켓을 운영하는 사장의 입장에서 판매 기록에 연관 규칙을 적용하면 바비큐 소스와 감자를 구매한 사람이 스테이크도 구매하는 경향이 있다는 것을 찾을지도 모른다. 이 추론으로 해당 상품들을 서로 가까이 진열할 수도 있을 것이다.

대표적인 비지도 학습 알고리즘은 아래와 같다.

- k-평균<sup>k-means</sup>
- DBSCAN
- PCA
- 가우시안 혼합<sup>gaussian mixture</sup>
- 오토인코더<sup>auto encoder</sup>


#### 준지도 학습


일부만 레이블이 있는 데이터를 다루는 것을 `준지도 학습(semisupervised learning)`이라고 한다.

구글 포토 호스팅 서비스가 좋은 예이다. 이 서비스에 가족사진을 모두 올리면 사람 A는 사진 1, 5, 11에 있고, 사람 B는 2, 5, 7에 있다고 자동으로 인식한다. 이는 비지도 학습(군집)이다. 이제 시스템에 필요한 것은 이 사람들이 누구인가 하는 정보이다. 이 때 사람마다 레이블을 하나만 추가하면 사진에 있는 모든 사람의 이름을 알 수 있고, 편리하게 사진을 찾을 수 있다.

![figure-6](/assets/images/2020/07/figure-6.png){: .align-center}


#### 강화 학습


구글 딥마인드에서 만든 AlphaGo로도 유명한 `강화 학습(reinforcement learning)`은 매우 다른 종류의 알고리즘이다. 여기서는 학습하는 시스템을 `에이전트`라고 부르며 환경을 관찰해서 행동을 실행하고 그 결과로 `보상(reward)`을 얻는다. 시간이 지나면서 가장 큰 보상을 얻기 위해 `정책(policy)`라고 부르는 최상의 전략을 스스로 학습한다. 정책은 주어진 상황에서 에이전트가 어떤 행동을 선택해야 할지 정의한다.


![figure-7](/assets/images/2020/07/figure-7.png){: .align-center}


아래는 atari 게임을 강화학습을 통해 학습시킨 화면이다. 최근에는 `Doom`이라던지 `Sonic` 등의 복잡한 게임도 강화학습을 통해 학습시키는 모습을 볼 수 있다.


![figure-8](/assets/images/2020/07/figure-8.gif){: .align-center}


#### 온라인 학습 vs 배치 학습


머신러닝 시스템을 분류하는 데 사용하는 다른 기준은 입력 데이터의 스트림부터 점진적으로 학습할 수 있는지 여부이다.


##### 온라인 학습
- 적은 데이터를 사용해 점진적으로 훈련
- 실시간 시스템이나 메모리가 부족한 경우에 사용


##### 배치 학습
- 전체 데이터를 사용해 오프라인에서 훈련
- 컴퓨팅 자원이 풍부한 경우에 사용


#### 사례 기반 학습 vs 모델 기반 학습


머신러닝 시스템은 어떻게 `일반화(generalize)`되는가에 따라 분류할 수도 있다. 대부분의 머신러닝 작업은 예측을 만드는 것이다. 이 말은 주어진 훈련 데이터로 학습하고 훈련 데이터에서는 본 적 없는 새로운 데이터에서 좋은 예측을 만들어야(일반화되어야) 한다는 뜻이다. 훈련 데이터에서 높은 성능을 내는 것이 좋지만 그게 전부는 아니다.

진짜 목표는 *새로운 샘플에 잘 작동하는 모델*이다.


##### 사례 기반 학습
- 샘플을 기억하는 것이 훈련
- 예측을 위해 샘플 사이의 유사도를 측정

![figure-9](/assets/images/2020/07/figure-9.png){: .align-center}

##### 모델 기반 학습
- 샘플을 사용해 모델을 훈련
- 훈련된 모델을 사용해 예측

![figure-10](assets/images/2020/07/figure-10.png){: .align-center}


다음 포스팅에서 주어진 조건에 대한 예측값을 산출간단한 선형모델을 만드는 실습을 해보도록 하겠다. 그리고 머신러닝에서 발생할 수 있는 주요문제들과 이 문제들을 해결하기 위해 어떤 노력이 필요한 지 알아보도록 하자.


### Reference
- [핸즈온 머신러닝 YouTube](https://www.youtube.com/playlist?list=PLJN246lAkhQjX3LOdLVnfdFaCbGouEBeb){:target="_blank"}
- [1장 한눈에 보는 머신 러닝](https://docs.google.com/presentation/d/19FB1IgRqKiICNW6cUqY4wANKiuftSadqywsDNoPSc34/edit?usp=sharing){:target="_blank"}
- [새로운 인공지능 기술 GAN ① - 스스로 학습하는 인공지능](https://www.samsungsds.com/global/ko/support/insights/Generative-adversarial-network-AI.html){:target="_blank"}
