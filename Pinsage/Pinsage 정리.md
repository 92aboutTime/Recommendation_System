# Pinsage 정리

### Transductive한 GCN

* GCN에서 Mini-batch를 할 경우 생각보다 Complexity가 감소하지 않는다. 그래서 Sampling을 사용한다.
* Mini-batch를 하더라도 Adjeacency에서 연결된 모든 이웃을 사용하면, 메모리에 대한 Complexity가 감소하지 않는다.
* Sampling과 같은 방식을 Spatial한 방법이라 하고, GraphSAGE가 이러한 방식을 취한다.



### GraphSAGE

* GraphSAGE는 Inductive한 방법론이다.
  * Inductive한 방법론이란
    * 새로운 노드가 온다고 해도, 자기 주변의 이웃 노드만을 가지고 온다면 학습된 Larnable parameter를 통해, 최종 노드를 만들 수 있다
    * 즉, 전체 노드를 보지 않아 Inductive한 방법론이다.
  * GCN은 Transductive한 방법론이다.
    * GCN의 Weight는 전체 노드 정보를 고려한 차원 변환 parameter이다.
    * 새로운 Node가 오면 전체 그래프와 연결성을 고려해야하기 때문에 Transductive한 방법론이다.
* Network Embedding에서 DeepWalk를 사용하는데, 가장 대표적인 것으로 Node2vec이 있다.
  * DeepWalk에서 하고 싶은 것은 Window Size 내에 있으면 이웃으로 간주해, 이웃끼리는 비슷한 값을 가지게 하길 희망한다.
  * Random하게 노드들을 걸어가며 Sequence를 만들어간다.
  * Sequence에서 많이 붙어 있거나, 많이 지나가면, 해당 노드는 특정 노드와 유사하다고 간주하고, 이러한 확률적 과정에서 Embedding이 유사해진다.
  * Slide-Window에서 window-size를 정하는 것은 이웃의 영역을 지정하는 것과 같다.
* 이웃을 선택하고 나와 이웃의 정보를 어떻게 통합할 것인가에 대한 과정에서 GraphSage는 가중치 보단, Learnable parameter를 사용한다.
  * GCN은 Degree 가중치를 이용한다. (Message Passing)
  * GCN에서 Degree 가중치를 사용하는 경우, 구조적 정보를 사용하기 때문에 Spectral한 방식이다.
  * GCN을 Loss 과정에서 정규화를 이용하는 이유는 이웃한 노드는 유사한 Embedding 값을 가지게 하려고 하는 것인데, GraghSAGE에서도 Sampling된 것들은 유사하게, Sampling 안된 것들은 Negative sample로, Embedding 값이 멀어지게끔 Loss를 지정한다.
  * Unsuperviesed-Learning 방식이다.
  * FaceNet에 Tripleset Loss 방식으로 생각하면 된다.

* GraphSAGE에서 이웃의 정의
  * Full batch case는 거의 사용하지 않는다.
  * Mini batch case : Mini batch case에서도 Complexity문제 때문에 Sampling을 사용한다.
  * 가장 중요한 점은 Sub-gragh를 구성하는 것이다.
  * Graph 구조에서 2-Hope를 대부분 설정하는 이유는 Graph-Over Smoothing 때문이다.
  * GCN에서 이웃을 정의하는 방법
    * Uniform Sampling
    * 사전에 정의된 가중치를 활용하는 방법
* AggreGate 방법
  * Aggregate할 때 Node들의 순서 정보가 반영되면 안된다.
    * 때문에 Concat 방법은 사용해선 안된다. (순서정보를 가지면 안된다는 의미로 생각)
  * 방법 1. Mean
    * 자신 Node와 이웃 Node들을 Mean해서 대표 노드를 만든다.
    * 대표 Node를 Inductive 방법으로 Final Node를 만든다.
  * 방법 2. LSTM
    * 이웃으로 정해진 Node들의 순서를 random하게 하고 LSTM을 통과한 값과 자신 Node를 Concat한다.
    *  Inductive한 방법으로 Final Node를 만든다.
  * 방법 3. Pooling(Max/Mean)
    * 이웃 Node들을 Element-wise Max or Mean 하여 통합 이웃노드를 만든다.
    * 자신 Node와 Inductive한 방법으로 Final Node를 만든다.

### Pinsage

* PinSAGE는 GraphSAGE를 추천 시스템에 적용할 수 있도록 Downstram Task를 진행한 방식이다.
* 즉, 추천이 가능하게끔 그래프를 구조화된 임베딩으로 만들 수 었다.
* PinSAGE는 하나의 Node가 다양한 정보로 이우러져 있다.
  * 이미지 정보(VGG를 거침)
  * Text 정보(Word2vec을 거침)
  * Degree 상수(해당 노드가 몇 차원인지에 대한 정보)
* PinGraph의 구조는 Pin-Board-Pin(Metapath)로, 의미적 정보를 가지고 있다.
  * Pin-Pin의 직접적인 연결은 없다. Metapath2vec 논문 참조
  * Pin-Pin보다 Pin-Board-Pin이 sementic한 의미론적 정보를 더 반영하고 있다.
  * Pinterest에서 Board는 자체 정보를 가지고 있지 않기 때문에, 단일 Type의 Node로 구성된 Homogenous Network라고 생각하면 된다.
* PinSAGE에서 이웃정의 방식 : 중요도 기반 고정된 이웃
  * GraphSAGE에서 이웃을 정의할 때, Random하게 뽑는 것과 달리, Personalized Page Rank라는 스코어를 이용한다.
  * Randomwalk를 진행했을 때, 자주 들리는 Node가 있을텐데, 그 Node는 하나의 Node와 굉장히 유사하다라는 스코어가 나오고, 그 스코어가 높은 Node들을 뽑는다.
  * PinSAGE에서는 학습을 줄이기 위해, 미리 Personalized PageRank를 구해 놓았다.
    * PPR(Personalized PageRank)는 구조적 정보 기반 유사도의 중요도 반영한 개념
    * Feature 정보는 사용하지 않았다. 
  * Aggregation 과정에서 Personalized Page Rank가 높은 고정된 T개의 Node를 사용한다.
* PinSAGE에서 Aggregation : Weighted Mean(가중평균)
  * Personalized Page Rank에서 구해진 값들을 통해 가중평균을 통해 대표 Node를 만들고, 대표 Node와 자신 Node를 Inductive하게 학습하여 Final Node를 만들었다.
* PinSAGE의 Loss : Max Margin Ranking Loss
  * Query를 주고 기준이 되는 점에서 negative score는 낮게, positive에 해당하는 것의 score는 높게 만든다.
  * GraphSAGE에서 positive는 이웃 Node였는데, PinSAGE에서는 Query를 통해 Graph 구조와 상관 없이 positive를 정리해 놓았다.
  * negative는 positive에 속하지 않은 400개를 랜덤적으로 선정한다.
    * 학습의 시간 관계상 batch 단위에서 negative를 공유한다.
  * Loss에서 margin이 있는데 margin보다는 더 학습이 차이가 나기를 희망하기에 존재한다.
  * Ranking Loss는 Robust Classifier, Soft Classifier라고 불린다.
    * 일반적인 Softmax 경우보다 parameter 수가 월씬 감소한다.
    * Ranking Loss의 경우, 비교하는 대상이 차이가 많이 날수록 학습이 잘 이루어지지 않는다.
      * 얼굴과 같이 비교자하는 대상의 차이가 잘 안날수록 학습이 잘 된다.
  * Hard Negative
    * positive에서 속하지 않은, random하게 뽑힌 negative들은 positive와 관계가 없을 확률이 매우 높다.
    * 비슷한 node들을 잘 검출하고 싶은데, random하게 뽑은 negative들이 상관이 없어 학습에 진적이 없다.
    * hard negative를 조금씩 사용해 유사한 positive한 것들을 유사한 값이 나오도록, negative한 것들을 다른 값이 나올 수 있도록 학습에 이용한다.
    * 이때, hard negative는 Query 기준 Personalize Page Rank를 사용했을 때, Ranking 2000~5000 아이템 중에서 랜덤하게 뽑는다.
    * Query와 Hard negative가 뭔가 유사하다면 구별이 어려워 positive를 더 유사하게 만들어준다.
    * 학습을 진행하며 Hard negative를 점진적으로 증가시킨다.



### 호준님이 질문한 low level 코드 부분

dgl.

WeightedSAGEconv 에서 weight

fn.u_mul_e

--------------------------------------------------------------------------

g.update_all(fn.u_mul_e('n', 'w', 'm'), fn.sum('m', 'n'))

g.update_all(fn.copy_e('w', 'm'), fn.sum('m', 'ws'))



노드 정보와 엣지 정보를 업데이트하는 방식일 수 있다.

sum을 하는 이유를 모르겠다.

z = self.act(self.W(self.dropout(torch.cat([n/ ws, hdst], 1))))  ## why n/ws, h_dst????

-------------------

class ItemToItemScorer(nn.Module):

​	def forward(self, item_item_graph)에도 이해가 안되는 부분이 있다.



dgl에 중국인이 답장을 잘해줘서 인기를 받음

dgl document가 잘 되어있지만 완전 설명이 만족하지 못한다.

업데이트 rule 들을 dgl에 나와 있는데 아직 완전히 이해하지 못함



 edge값이 높으면 높을수록 추천을 권장함.



pinsage는 계속 진행을 하고 모르는게 있으면 share하면 좋겠다.

------------------------------------------------------------------------------



### 이원도님 질문

모델이랑 데이터셋은 파악했지만, sagenet, itemtoitem부분을 보지 못했다.

* torchtext가 무슨 라이브러리인지

* torchtext.legacy 로 다운그레이드

* collator :  데이터셋 불러올때 관리해주는 것, 대용량 데이터셋을 더 쉽게 handling 해주는 역할
  * model.py에서 73번째 줄



##### 업데이트 하는 부분이 core인거 같다 dgl을 공부할 필요성을 느꼈다.

------------------------



multi-sage 코드가 없어서 힘들다.

ngcf를 공부하면 좋겠다.

tagnn : time series 공부하고 싶다. 원자가 쓴 코드가 있다.



papers record 코드가 있는지 확인학호자 한다.



-------------------

* 이걸 공부하면 좋겠다. Bert_for_rec : github.com/SungMinCho/MEANTIME 
  * 파일의 구조, 코드 작성들을 참조한다.

-----------

다음주 tagnn 코드도 훓어보고 논문도 읽어보고 해볼 수 있는 것까지 해본다.

TAGNN 주소 : https://github.com/CRIPAC-DIG/TAGNN

