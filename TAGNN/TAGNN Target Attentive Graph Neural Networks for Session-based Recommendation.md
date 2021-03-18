# TAGNN: Target Attentive Graph Neural Networks for Session-based Recommendation



### 들어가기 앞서

* Session-based Recommendation

  * Sequential data에 대한 recommendation 방법 중 한가지
  * Session based recommendation with RNN 논문이 있다.
    * 사용자의 최근의 sequential한 정보를 이용해서 사용자가 다음에 어떤 컨텐츠를 소비할지 예측하는 논문
  * Session based recommendation과 sequential based recommender system은 Session 정보 혹은 사용자의 Sequential한 행동을 기반으로 다음에 어떤 것을 예측할지에 대한 것

* SESSION이란

  * 망 환경에서 사용자 간 또는 컴퓨터 간의 대화를 위한 논리적 연결
  * 프로세스 사이에서 통신을 하기 위해 메시지 교환을 통해 서로를 인식한 이후부터 통신을 마칠때 까지의 기간

  ##### 일정 시간동안 같은 사용자(정확하게 브라우저)로 부터 들어오는 일련의 요구를 하나의 상태로 보고 그 상태를 일정하게 유지시키는 기술



## Abstract

> Session-based recommendation nowadays plays a vital role in many websites, which aims to predict users’ actions based on anonymous sessions. 

Session-based recommendation은 익명의 Session을 기반으로 유저의 행동을 예측하는데 목적을 두고 있다.



> There have emerged many studies that model a session as a sequence or a graph via investigating temporal transitions of items in a session. 

시간적 전환(temporal transitions)를 통해 Session을 sequence 또는 graph로 모델링하는 많은 연구가 있었다.



> However, these methods compress a session into one fixed representation vector without considering the target items to be predicted. 

그러나 이러한 방법들은 예측해야할 target items 고려 없이 고정된 representation vector로 Session을 압축한다.



> The fixed vector will restrict the representation ability of the recommender model, considering the diversity of target items and users’ interests. In this paper, we propose a novel target attentive graph neural network (TAGNN) model for session-based recommendation. 

고정된 크기의 vector는 target items와 유저의 interests의 다양성에 대한 추천 모델의 표현능력을 제한한다.



> In TAGNN, target-aware attention adaptively activates different user interests with respect to varied target items. 

TAGNN에선, target-aware attention은 다양한 타겟 항목과 관련하여, 다양한 사용자 관심을 adaptively하게 활성화한다.



> The learned interest representation vector varies with different target items, greatly improving the expressiveness of the model. 

학습된 interest representation vector는 모델의 성능을 높히면서, 다른 target items에 대해서 다르게 나타난다.



> Moreover, TAGNN harnesses the power of graph neural networks to capture rich item transitions in sessions. 

또한 TAGNN은 그래프 신경망의 힘을 활용하여 Session에서 풍부한 항목 전환을 캡처합니다.