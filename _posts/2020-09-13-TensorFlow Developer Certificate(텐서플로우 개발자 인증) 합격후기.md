---
title: "TensorFlow Developer Certificate(텐서플로우 개발자 인증) 합격후기"
excerpt: "올해 초 운이 좋게 AI 분야의 일을 할 기회가 생겼고 아직은 미숙하지만 해당 분야에 대해 지속적으로 학습하고 있다."
toc: true
toc_sticky: true
categories:
  - Review
tags:
  - Tensorflow
  - Google
  - Deep learning
  - AI
  - '2020'
last_modified_at: 2020-09-13
---

올해 초 운이 좋게 AI 분야의 일을 할 기회가 생겼고 아직은 미숙하지만 해당 분야에 대해 지속적으로 학습하고 있다. 그러던 중 올해 `TensorFlow 개발자 인증` 시험이 생겼다는 소식을 듣게되었고 해당 시험을 준비하게 되었다. 개인적으로 IT 분야의 자격증은 사실 실력의 척도가 된다고 생각하지 않는다. **'특히나 개발 영역의 자격증 이라면...''**

다만 이번 자격증을 준비하면서 다시 한번 머리속의 지식들을 정리하고 구글의 머신러닝 라이브러인 `TensorFlow` 사용법에 대하여 실습할 수 있었던 좋은 기회였던 것 같다. 이전의 합격후기와 마찬가지로 개인적인 기록을 남김과 동시에 혹시라도 시험을 준비하는 사람들에게 도움이 되고 싶은 마음에 포스팅을 남긴다.

### Tensorflow

아마 이 포스팅을 읽는 사람들은 `TensorFlow`에 대해 자세히 알지는 못하지만 대략적으로 어떤 용어인지는 알고 있을 것이라 생각된다. `TensorFlow`는 구글이 2011년에 개발을 시작하여 2015년에 오픈소스로 공개한 기계학습 라이브러리(혹은 프레임워크)이다.
더욱 더 자세한 내용은 [Tensorflow 공식 홈페이지](https://www.tensorflow.org/?hl=ko){: target="_blank"} 를 참고하자.

더 간단히 말하자면 나 같은(?) 일반인도 딥러닝, 강화학습 등 여러가지 머신러닝 관련 프로젝트를 수행할 수 있도록 구글에서 내놓은 일종의 Tool이라고 보면 될 것 같다. 현재 `2.3.0` 버전까지 나와 있으며(*2020-09-13 기준*), 참고로 버전 `1.0` 대와 `2.0` 대는 많은 차이가 있으니 제일 큰 변화는 `Keras`라는 라이브러리를 통합했다는 것이다.(`Keras` 또한 별개의 머신러닝 라이브러리 였다.) 기존에 `1.0` 버전은 코드가 복잡하고 때문에 간단한 모델링에 `Keras`를 사용하였었지만 `2.0` 으로의 버전업을 통해 `Keras`가 `TensorFlow`에 통합되게 되었다. 따라서 입문을 할 생각이라면 `2.0`을 추천한다.

![pt_vs_ts](/assets/images/2020/09/pt-vs-ts.png)
*PyTorch vs TensorFlow*

최근에는 Facebook에서 밀고 있는 `PyTorch`란 라이브러도 많이 뜨고 있다. 아무래도 유저 친화적으로 되어버린 `TensorFlow`에 비해 조금 더 Low-Level로 세밀하게 모델링을 할 수 있기에 학계에서 많이 사용되는 듯하다. `TensorFlow`와 달리 Facebook이 직접 해당 라이브러리를 관리하는 포럼도 있다. 보다 더 학구적으로 머신러닝을 연구해보고 싶다면 해당 라이브러리로 입문하는 것도 좋은 생각이다.

`TensorFlow` 시험의 경우 최신버전인 `2.3.0`이 아닌 `2.1.0` 버전 환경에서 수행된다.

### TensorFlow Developer Certification
#### 기술 체크리스트
![tensorflow_cert](/assets/images/2020/09/tensorflow-cert.png)

`TensorFlow Developer Certification`는 응시자가 `TensorFlow 2.x`를 통해 모델을 빌드하여 문제를 해결할 수 있는지 확인하는 시험이다. [응시자 참고자료](https://www.tensorflow.org/site-assets/downloads/marketing/cert/TF_Certificate_Candidate_Handbook_ko.pdf?hl=ko){: target="_blank"}에서 본 세부적인 항목은 아래와 같다.

>**1. TensorFlow 2.x를 활용하여 신경망 모델 빌드 및 훈련**
>
> TensorFlow 2.x를 활용하여 머신러닝(ML)과 딥러닝(DL)의 가장 중요하고 기본적인 원칙을 이해해야 합니다. 다음을 할 줄 알아야 합니다.
> - TensorFlow 2.x 활용.
> - TensorFlow 2.x를 활용하여 머신러닝(ML) 모델 빌드, 컴파일 및 훈련.
> - 모델에서 활용할 수 있도록 데이터 전처리.
> - 모델을 활용하여 결과 예측.
> - 다양한 레이어로 순차적인 모델 빌드.
> - 바이너리 분류에 대한 모델 빌드 및 훈련.
> - 멀티클래스 분류에 대한 모델 빌드 및 훈련.
> - 훈련된 모델의 플롯 손실 및 정확도.
> - 확장 및 드롭아웃을 비롯한 전략 파악하여 과적합 예방.
> - 훈련된 모델 활용(전이 학습).
> - 사전 훈련된 모델에서 특성 추출.
> - 모델에 대한 입력값이 올바른 형태인지 확인.
> - 테스트 데이터와 신경망의 입력 형태를 일치시킬 수 있어야 함.
> - 신경망의 출력 데이터를 테스트 데이터의 지정된 입력 형태와 일치시킬 수 있어야 함.
> - 대용량 데이터 로드 이해.
> - 콜백을 사용하여 훈련 주기 마지막을 트리거.
> - 여러 다른 소스의 데이터세트 활용.
> - JSON 및 CSV을 포함한 다양한 형식의 데이터세트 활용.
> - tf.data.datasets의 데이터세트 활용.
>
>**2. 이미지 분류**
>
> TensorFlow 2.x를 활용하여 심층신경망 및 합성곱 신경망(CNN)으로 이미지 인식 및 객체 탐지 모델을 빌드하는 방법을 이해해야 합니다. 다음을 할 줄 알아야 합니다.
> - Conv2D 및 풀링 레이어로 합성곱 신경망(CNN) 정의.
> - 실제 이미지 데이터세트를 처리하기 위한 모델 빌드 및 훈련.
> - 합성곱 활용 방법을 이해하여 신경망 개선.
> - 다양한 형태와 크기의 실제 이미지 활용.
> - 이미지 확장을 이용하여 과적합 예방.
> - ImageDataGenerator 활용.
> - 디렉터리 구조를 기반으로 ImageDataGenerator에서 이미지의 라벨을 지정하는 >방법 이해.
>
>**3. 자연어 처리(NLP)**
>
> TensorFlow를 통해 신경망을 활용하는 방법을 이해하여 자연어 처리 문제를 처리>해야 합니다. 다음을 할 줄
알아야 합니다.
> - TensorFlow를 활용하여 자연어 처리 시스템 빌드.
> - TensorFlow 모델에서 사용할 텍스트 준비.
> - 바이너리 분류를 활용해 텍스트의 카테고리를 파악하는 모델 빌드.
> - 멀티클래스 분류를 활용해 텍스트의 카테고리를 파악하는 모델 빌드.
> - TensorFlow 모델의 단어 삽입 기능 활용.
> - 모델의 LSTM을 활용하여 텍스트가 바이너리 분류인지 멀티클래스 분류인지 구>분.
> - 모델에 RNN 및 GRU 레이어 추가.
> - 텍스트에 맞는 RNN, LSTM, GRU, CNN을 모델에 이용.
> - 기존 텍스트에서 LSTM 훈련하여 노래 및 시 등의 텍스트 생성.
>
>**4. 시계열, 시퀀스, 예상**
>
> TensorFlow에서 시계열 및 예측 문제를 해결하는 방법을 이해해야 합니다. 다음을 할 줄 알아야 합니다.
> - 시계열, 시퀀스, 예상 모델을 훈련, 조정 및 활용.
> - 시계열을 학습하기 위한 데이터 준비.
> - 평균 절대 오차(MAE)를 이해하고 시퀀스 모델의 정확성을 평가하는 데 어떻게 평균 절대 오차가 사용되는지 이해.
> - 시계열, 시퀀스, 예상 모델의 RNN 및 CNN 활용.
> - 중앙 창과 후행 창을 언제 사용해야 하는지 파악.
> - 예측을 위해 TensorFlow 활용.
> - 기능 및 라벨 준비.
> - 시퀀스 편향 파악 및 보완.
> - 시계열, 시퀀스, 예상 모델에서 동적으로 학습율을 조정.

전반적으로 많은 기준이 있는 것으로 보이나... 결론적으로 얘기하면 `TensorFlow에서 제공되는 Sample Datasets`들을 적절하게 잘 모델링하면 된다.

#### 응시정보

이 외의 기본적인 응시정보는 아래와 같다.

- 응시료 : $100 (해외 결제가 가능한 카드)
- 시험 시간 : 300분
- 시험 환경 : 인터넷이 연결되어 있는 어디에서나 가능
- IDE : [PyCharm](https://www.jetbrains.com/help/pycharm/installation-guide.html){: target="_blank"}에 시험용 Plugin(*Google Certification*) 설치 (유료가 아닌 Communiy 버전도 가능)
- 시험 준비물 : 여권이나 영문 운전면허증. 셀카
- 재응시 기간
  - 1번째 불합격 시, 14일 이후 재응시 가능
  - 2번째 불합격 시, 2개월 이후 재응시 가능
  - 3번째 불합격 시, 1년 이후 재응시 가능
- 합격점수 : 총 100점 만점에 90점 이상
- 결과발표 : 응시자가 시험 제출 시 채점 과정을 거쳐 응시자 포털에 점수 게시. 시험 합격 후에는 등록된 이메일 주소로 인증서가 발송(영업일 기준 10일 이내)
- 인증서 유효기간 : 36개월(갱신하려면 등록절차와 인증과정을 다시 거쳐야 함)

#### 혜택

시험에 혜택이라고 하면 제일 먼저 자신의 머신러닝/딥러닝 지식을 검증할 수 있고 해당 자격증을 **공식적으로** `GitHub`, `LinkedIn` 페이지에 기입할 수 있다는 점이다. 그리고 다소 특이했던 것은 전 세계의 [Certificate Network](https://developers.google.com/certification/directory/tensorflow?hl=ko){: target="_blank"}에 이름이 게재된다.

![cert_network](/assets/images/2020/09/cert-network.png)
*각 대륙별로 취득 순서대로 이름이 게제된다. 이른바 명예의 전당?*

### 시험준비

시험에 대한 소개를 했으니 이제 내가 어떻게 시험을 준비했는지 과정이다. 일단 개인적으로 준비할 수 있겠지만, 회사에서 관련 교육을 지원해줘서 이틀에 걸쳐 관련강의를 수강하였고, 다시 이틀동안 혼자 모델을 정리하고 오늘(09/13) 시험에 응시해 합격하였다. 혼자서 준비하는 것보다 시간적인 측면에서 굉장한 효율을 보았다고 생각한다.. 수강부터 자격증 취득의 텀이 일주일도 되지 않았으니 말이다.

아마 평소에 `머신러닝/딥러닝` 이나 `Data Science` 관련 업무를 수행하거나 한번이라도 접해보았다면 크게 어려운 시험은 아니라고 생각한다. 처음 접하면 다소 개념과 문법에 적응할 시간이 필요하다. 하지만 결코 **2주를 넘길 시험은 아니다.**. 이런 류의 시험은 오래 끌면 끌수록 본인에게 손해인 법이다. 철은 가장 뜨거울 때 두드려야 하듯 빠르게 공부하고 빠르게 취득해야 하는 시험이다.

시험은 총 5문제로 이루어져 있다. 각 문제별로 설명하는 모델에 맞게 코드를 작성하고 모델을 생성해야 하며 해당 모델이 구글에서 정한 `Test Case`를 통과하여야 한다. 각 문제별로 배점은 모두 다르다고 알고 있다.(아마 난이도에 비례할 듯)일반적인 코딩 테스트의 과정이랑 비슷하다고 보면 된다. 다만 코드가 아닌 생성된 모델을 제출해야 한다는 점이 다르다.

각 문제들은 새로운 문제는 아니고 `TensorFlow` 공개 데이터셋으로 이루어져 있다. 이 말은 구글링 등을 통해 쉽게 잘 만들어진 모델을 찾을 수 있다는 얘기다.(`tf Datasets` + `Kaggle` 의 키워드로 구글링 한다면 `Kaggle`의 순위권안의 노트북들을 참고해 볼 수 있다. ) 공식적으로 시험 중 참고할 수 있는 자료는 `TensorFlow Documentation` 뿐이지만... 기출되는 모델에 의해서 1~2번씩의 실습만 해본다면 그리 어려운 시험은 아닐 것이다.

#### 각 문제(Category) 별 정리

시험을 준비하기 앞서 `Google Colaboratory`를 이용해 실습하는 것이 매우 추천한다.(`Google Colaboratory`가 뭔지 모르겠다면.. [이 링크](https://colab.research.google.com/notebooks/welcome.ipynb?hl=ko){: target="_blank"}를 참고하자!)
학습 Datasets 중 GPU가 없으면 학습만 몇 시간이 걸리는 것이 있기에.. 그리고 생 `.py` 보다 노트북 형태의 파일인 `ipyb`로 실습하는 것이 코드 플로우나 필요한 필기를 `markdown`을 이용해 기록할 수 있기에 학습에 더 좋을 것이다.

나는 모든 실습 파일들을 Google Driver에 폴더별로 정리하고 각 문제별로 노트북 파일과 출력 모델들을 정리하였다.

![google_drive](/assets/images/2020/09/google-drive.png)

또한 실습에 활용한 모델에 대해서 각각의 성능지표(val_loss, val_acc)를 작성하여 기록해 두었다.

![model_evaluate_spreadsheet](/assets/images/2020/09/model-evaluate-spreadsheet.png)

#### 시험 환경 셋팅

시험은 앞서 말한 것 같이 `Pycharm` 에 별도 Plug-in을 설치하여 진행하여야 한다. 이전에 `PyCharm`을 사용해보아서 별다른 어려움 없이 진행할 수 있었다.

또한 `tensorflow 2.1.0` 이 설치된 가상환경을 미리 설정해주었는데, `virtualenv`를 활용하거나 아니면 `anaconda`를 설치하여 진행하면 된다.
따로 여기서 설명하지는 않겠다. 필요한 라이브러리는 아래와 같다.

```bash
pip install tensorflow==2.1.0
pip install tensorflow-datasets
pip install numpy
pip install Pillow
pip install urllib3
```

더욱 더 자세한 셋팅방법은 [이 문서](https://www.tensorflow.org/extras/cert/Setting_Up_TF_Developer_Certificate_Exam.pdf?authuser=4){:target="_blank"}를 참고하자.

모두 올바르게 셋팅되었고, 해당 프로젝트 폴더를 import 해 보면 아래의 화면과 같을 것이다. 여기서 `Take the certification exam`을 누르면 시험이 시작된다.

![pycharm-test](/assets/images/2020/09/pycharm-test.png)
*환경 셋팅 후, Pycharm 실행 화면*

#### 시험 당일

전에 미리 시험 접수를 해 놓았기 때문에 날라온 메일의 링크를 타고 들어가 시험 응시를 시작하면 된다.

![cert_mail](/assets/images/2020/09/cert-mail.png)
*시험 접수 후 날라온 메일*

해당 링크를 타고 들어가서 시험 응시권(?)을 확인하고 Redeem 버튼을 누르면 시험 환경구성이 시작된다. 완료되었을 때, `Pycharm`으로 가 시험을 시작하면 된다.

![provisioning](/assets/images/2020/09/provisioning.png)
*해당 대시보드에서 'Redeem' 버튼을 클릭 시 시험을 위한 환경이 생성된다.*

오른쪽 상단에 `End Exam` 버튼 옆의 화살표를 누르면 남은 시험 시간을 확인할 수 있으며, 오른쪽 하단에는 각 문제별로 테스트를 해 볼 수 있는 `Submit and Test` 버튼이 있다. 해당 버튼을 눌르면 생성된 모델을 업로드해 `5/5` 식으로 결과를 알려준다.

![cert_time](/assets/images/2020/09/cert-time.png)
*남은 시험 시간*

![submit_and_test](/assets/images/2020/09/submit-and-test.png)
*각 문제별로 모델의 Test-case를 수행할 수 있다.*

나의 경우 각 문제별로 해당 버튼을 최소 5번은 눌러서 결과가 모두 `5/5`인 경우에만 정합한 모델로 판단했다. 5번중에 한번이라도 만점이 나오지 않는다면 다시 모델링을 하여 모델을 추출하였다. 각 모델별로 넘어야하는 최소 성능수치는 문제 설명에 기입되어 있다.

모델을 추출하는 작업은 위에서 말했다시피 로컬보다는 `Google Colaboratory`를 활용하였다.

시험시간이 5시간이라 매우 넉넉해보이지만 모델링에 소요되는 시간도 포함이기에 미리 연습을 많이 해보아야한다. 준비한 모델링이 만점을 받지 못한다면 멘붕에 빠질 수도 있기 때문이다.

모든 문제를 모두 작성하였다면 `End Exam`버튼을 누르고 정말 종료하겠냐는 물음에 `Yes`를 눌러 시험을 종료해주면 된다.

### 합격 후

![pass_exam_mail](/assets/images/2020/09/pass-exam-mail.png)
*시험 종료 후 받은 메일*

시험을 끝내면 (체감 상) 10초도 안되서 합격여부를 알려주는 메일이 온다. 해당 메일에는인증서 발급을 위해 정보를 요구하는 링크가 들어있다. 해당 링크를 접속하여 자신의 정보를 입력하면 10 영업일 이내에 인증서가 발급된다. 인증 네트워크는 매달 Update 되는 것 같다. Simple해서 좋다. 조금 허무하기도 하고...

### 마치며

간단한 시험이지만 앞으로도 이 분야에 대해 계속 정진해야 할 텐데, 끝이 아닌 시작이라고 생각한다. 자격증 획득에 의의가 있는 것이 아닌 `AI`에 입문하려고 하는 사람들에게 매우 좋은 튜토리얼이자 이정표라는 생각이 든다.

나온지 얼마 안 된 자격증이라 자료가 많이 없어 준비가 난감할 것이다. 찾아보니 Coursera에 무료 강의도 있는 것 같으니 이런 것도 참고해보면 좋을 것 같다. ([TensorFlow in Practice(Coursera)](https://www.coursera.org/professional-certificates/tensorflow-in-practice#howItWorks){: target="_blank"})

## Reference

- [텐서플로우 - 나무위키](https://namu.wiki/w/%ED%85%90%EC%84%9C%ED%94%8C%EB%A1%9C%EC%9A%B0?from=Tensorflow){: target="_blank"}
- [TensorFlow 개발자 인증](https://www.tensorflow.org/certificate?hl=ko){: target="_blank"}
- [텐서플로우 개발자 자격증 (Tensorflow Developer Certification) 시험 그리고 취득방법](https://teddylee777.github.io/thoughts/tensorflow-2-certification){: target="_blank"}
