# Session-Based Recommendation with RNN

__GRU4Rec이라고도 불린다.__

### Abstract

* RNN을 추천시스템에 적용한다.

* Short session-based 상황에서 Matrix Factorization은 정확도가 떨어진다.

  * Neflix 같은 데이터의 경우 유저 한명이 가지는 데이터의 크기는 session 데이터에 비해서 크다.
  * session 데이터의 경우 데이터의 크기가 작은게 많다.
    * 잠깐 홈페이지 들어왔다 나가는 사람들이 많은 것을 생각하면 된다.
  * 이렇게 짧은 short session-based 상황에서 Matrix Factorization의 정확도가 낮았다.
  * 그래서 Item-to-Item recommendation이 주로 일어 났었다.
    * user 한 사람을 위한 추천이 아니라 많이 아쉬웠다.

  

* Item-to-item 추천방식을 short session에서 활용하기도 한다.

* RNN-based 방식을 통해 session-based 추천시스템을 구현할 수 있다.

* RNN의 ranking loss를 변경하여 Session-based recommendation task에서 좋은 성능을 얻을 수 있다.



### Introduction

* 현재까지 대부분의 session-based 추천은 간단한 방법을 사용할 수 밖에 없었다.
  * User profile을 사용하지 못하고 item간 유사도, co-occurrence 등을 활용했다.
    * 방금 들어온 user에 대한 profile을 사용하지 못한다는 의미로, user의 정보를 사용하지 못한다고 생각하면 된다.
    * 그래서 item간 유사도, co-occurrence를 사용할 수 밖에 없었다.
  * E-commerce 분야에서 특히 session-based 추천을 적용하기 어려웠다.
* 주로 사용되는 모델은 factor model과 neighborhood method이다.
  * 위의 이유 때문에 item간 유사도를 주로 사용하기 때문에 factor model이 그나마 성능이 좋았다.
  * Factor model은 user profile이 없기 때문에 session-based 추천문제를 풀 수 없다.
  * Neighborhood method는 유사도와 co-occurrence기반이기 때문에 session-based 추천에서 적절히 사용할 수 있다.
* RNN의 sequential한 특징 덕분에 session-based 추천에 활용할 수 있다.
* Sparse한 sequential 데이터를 모델링하고 추천시스템에 RNN을 적용한다.
  * 예를 들어, 원래 데이터가 A-B-C-D인데, A- - -D만 남아 있는 경우에도 잘 된다.
* 새로운 ranking loss를 task에 맞게 다시 제안한다.
* 학습할때,
  * 사용자가 웹사이트에 들어왔을 때 하는 첫번째 클릭 = initial input
  * 이전 클릭에 따라 사용자는 다음 클릭을 연이어서 진행한다 = output
    * 이전 클릭에 따라 사용자가 행하는 다음 클릭(output)을 예측한다.
* 위의 click-stream 데이터셋을 활용하는데, 데이터 사이즈와 scalability(확장성)가 매우 중요하다.
  * 어떤 사람은 연속해서 100번 클릭 할 수 있는데, 어떤 사람은 3번만에 홈페이지를 나갈 수 있다.



### Recommendations with RNNs

__RNN__

* variable-length sequence data를 다루기 위해서 고안(devised) 되었다.
* g는 sigmoid와 같은 smooth and bounded function
* RNN의 output은 next element of the sequence에 대한 probability distribution이다.
* RNN의 단점은 vanishing gradient problem이다.

__GRU__

https://yjjo.tistory.com/18

* Gated Recurrent Unit(GRU)
* dealing with the vanishing gradient problem.
* GRU gates essentially learn when and by how much to update the hidden state of the unit.



### Customizing the GRU Model

* input은 actual item에 대한 내용이 들어오고, 내용이 들어 왔을 때, Embedding layer, GRU layer 쭉 태워서 마지막에 Feedforward layers를 나와, item의 score를 예측하는, 즉 그 아이템을 얼마나 preference 할지 score를 예측하는게 모델의 핵심 구조이다.



* Input : Actual state of the session
* Output : item of the next of the event in the session
* State of the session 정의
  * 이건 처음에 Input으로 들어올 때 상태를 아래 두 가지 중 하나를 선택할 수 있다.
  * Item of the actual event : one-hot vector
    * 아이템이 100개가 있을 때, 3번 아이템의 경우, [ 0, 0, 1, 0 ···· 0, 0 ]으로 표현하는 것
    * 아래의 방법보다 주로 이 방법을 사용한다.
  * Events in the session : weighted sum of these representations
    * 이 모든 것을 모두 가중합을 통해, event에 대한 state of the session을 정의하는 방법
    * 이거 강사가 잘 설명을 못했다. 말을 먹었으니, 찾아서 공부해보자.



### Session-Parallel Mini-batch creation

https://www.researchgate.net/figure/Session-parallel-mini-batch-creation_fig2_284579100

* 기존 RNN batch 방식을 적용할 수 없다.
  1. Session 길이가 매우 다르다.
  2. 시간 변화에 따라 session이 어떻게 생겨나는지를 학습하는 것이 목적이다.
* Session-parallel mini-batches 방법을 선택한다.
* Session은 모두 독립이며, hidden state를 reset한다.



### Sampling on the output 

__이거 강사가 대충 설명해서 공부하는 것이 필요하다.__

* 매 step 마다 모든 item에 대해서 점수를 계산하는 것은 비 효율적이다.
* 아이템 중 subset만 뽑아서(sampling) 점수를 계산하는 것이 좋다.
* 다음과 같이 sampling할 때는 popularity를 고려해야한다.
  * 아이템이 존재하는지 몰랐기 때문에 관심 갖지 않았다.
  * 유명한데도 안봤으면 좋아하지 않을 가능성이 높다
* 따라서 mini-batch에서 다른 학습 데이터를 negative example로 활용한다.
  * 즉, mini-batch에 포함되지 않은 다른 학습 데이터를 negative example로 활용하는 것이다.
* 이는, mini-batch에서 다른 학습 데이터에 아이템이 존재할 likelihood는 popularity와 비례한다.



### Ranking Loss

* Pointwise: 각 아이템에 점수를 매긴다.
* Pairwise : 유저가 선택한 아이템과 그렇지 않은 아이템을 고르고, 유저가 선택한 아이템을 선택하지 않은 아이템보다 더 선호할 확률을 최대화 한다.
* Listwise : 모든아이템에 점수와 랭킹을 부여하고 랭킹을 바탕으로 정답과 비교한다.
* 그러나 논문에서는 Pairwise Loss가 가장 성능이 좋다고 한다.
  * BPR(Bayesian Personalized Ranking)
  * TOP1 : relative rank of the relevant item



### Experiments

* AdaGrad > RMSProp
* LSTM, RNN 보다 GRU가 더 우수하다
* Session이 그렇게 길지 않기 때문에 single layer GRU도 충분하다.
* GRU의 사이즈를 키우는 것은 도움 된다.
* 세션의 모든 이전 이벤트를 input으로 사용하는 것은 이전 하나만 넣는 것에 비해 성능향상 없다. GRU 역시 long short term memory이슈가 있기 때문이다.



### Conclusions

* Session-based Recommendation with Recurrent Neural Network(GRU)를 제안했다.
* Session-based Recommendation을 위한 다음과 같은 방법을 제안했다.
  * Session-parallel mini-batches
  * Mini-batch based output sampling
  * Ranking Loss function
* 이 task에 대해 다른 baseline보다 성능이 우수함을 확인했다.



### One more thing to learn

* BERT4Rec : Sequential Recommendation with Bidirectional Encoder Representaions from Transformer
* BERT의 Masked Language Model을 활용하여 next item을 예측하는 모델