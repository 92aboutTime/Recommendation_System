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



## YOOCHOOSE Data

https://recsys.yoochoose.net/challenge.html

> YOOCHOOSE is providing a collection of sequences of click events: click sessions. 
>
> For some of the sessions, there are also buying events.
>
> The goal is hence to predict whether the user (a session) is going to buy something or not, and if he is buying, what would be the items he is going to buy.

YOOCHOOSE 데이터는 click sessions을 제공한다.

어떠한 세션에선 구매가 이뤄지기도 한다. 

목적은 user가 상품을 살지 안살지를 예측하는 것이고, 산다면 어떠한 상품을 살지 예측해야 한다.



> ### The Task
>
> 1. Is the user going to buy items in this session? Yes|No
> 2. If yes, what are the items that are going to be bought?

> ### The Data 
>
> 	##### Train Data
>
> 1. yoochoose-clicks.dat - Click events. Each record/line in the file has the following fields:
>    1. Session ID – the id of the session. In one session there are one or many clicks.
>    2. Timestamp – the time when the click occurred.
>    3. Item ID – the unique identifier of the item.
>    4. Category – the category of the item.
> 2. yoochoose-buys.dat - Buy events. Each record/line in the file has the following fields:
>    1. Session ID - the id of the session. In one session there are one or many buying events.
>    2. Timestamp - the time when the buy occurred.
>    3. Item ID – the unique identifier of item.
>    4. Price – the price of the item.
>    5. Quantity – how many of this item were bought.
>
> The Session ID in yoochoose-buys.dat will always exist in the yoochoose-clicks.dat file – the records with the same Session ID together form the sequence of click events of a certain user during the session. The session could be short (few minutes) or very long (few hours), it could have one click or hundreds of clicks. All depends on the activity of the user.
>
> 
>
> ##### Test Data
>
> 1. yoochoose-test.dat - identically structured as the yoochoose-clicks.dat of the training data
>    1. Session ID
>    2. Timestamp
>    3. Item ID
>    4. Category
>
> 
>
> ### Solution File
>
> 

Session 이라는게 한번 클릭하고 다음 클릭까지의 과정으로 생각하면 될거 같다.

Session ID란 어떠한 값일까??? 내 생각에는 켜있는 브라우져 하나당 하나의 Session ID 가 부여될거 같다. 맞는지 확인 필요하다.





## Introduction

> ![TAGNN Overview](C:\Users\wq_ysw\Desktop\Project\Recommendation_System\TAGNN\TAGNN Overview.png)
>
> Figure 1 gives an overview of the TAGNN method. 
>
> We first construct session graphs using items in historical sessions. 

Session Graphs를 만든다.

> After that, we obtain corresponding embeddings using graph neural networks to capture complex item transitions based on session graphs.
> Given the item embeddings, we employ a target-aware attentive network to activate specific user interests with respect to a target item. 

complex item transitions : 무슨 뜻인지 찾아본다.

> Following that, we construct session embeddings. 
>
> At last, for each session, we can infer the user’s next action based on item embeddings and the session embedding. 
>
> The main contribution of this work is threefold. Firstly, we model items in sessions as session graphs to capture complex item transitions within sessions. 
>
> Then, we employ graph neural networks to obtain item embeddings. 
>
> Secondly, to adaptively activate users’ diverse interests in sessions, we propose a novel target attentive network. 
>
> The proposed target attentive module can reveal the relevance of historical actions given a certain target item, which further improves session representations. 
>
> Finally, we conduct extensive experiments on real-world datasets. The empirical studies show that our method achieves state-of-the-art performance.