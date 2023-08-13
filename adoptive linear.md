#Paper review: LEARNING ACTIVATION FUNCTIONS TO IMPROVE DEEP NEURAL NETWORKS
https://arxiv.org/pdf/1412.6830.pdf

1. 개요

일반적으로 ANN은 Fixed된 Active function을 사용한다. 정확하고 빠른 학습이 가능한 Active function을 찾는건 활발히 연구중인 주제다. 본 논문에서는 학습이 가능한 Active function을 정의하고, 이를 사용해 실험한 결과를 소개한다.

2. APL: ADAPTIVE PIECEWISE LINEAR UNITS

새로운 Active function을 다음과 같이 정의하자.

$h_i(x) = \max(x, 0) + \sum_{s=1}^{S} a_i^s \cdot \max(-x + b_i^s, 0)$

이때, a는 구간 내 기울기를 결정하고, b는 구간의 경계점을 결정한다.
이 Active function을 사용해 만든 신경망은 2SM개의 a,b parameter를 학습시켜야 한다. 이때 M은 hidden layer의 node 갯수이며, 이는 기존 parameter인 weight와 bias에 비하면 작다.
n번째 hidden layer의 node 갯수를 $d_n$개라 하면 이때의 weight와 bias 갯수는

$\sum_{k=0}^{n} d_k \cdot (d_{k+1}+1)$ 이 되므로 이는 

2SM = $2S \cdot \sum_{k=0}^{n} d_k$ 에 비하면 훨씬 크다. 

(해당 부분은 이렇게 정의한 새로운 Active function이 신경망의 연산량에 큰 영향을 주지 않는다고 말하고 싶었던 것 같다.)

Theorem: 

$h_i(x)$는 다음 조건을 만족하는 임의의 piecewise linear function을 모두 표현할 수 있다.

1) $\forall x \geq u, \exist$ u such that g(x) = x
2) $\forall x < v, \exists v$, $\alpha$ $\text{ such that }$ $\nabla$ g(x) = $\alpha$

증명은 간단하니 생략한다. 또, 충분히 큰 S에 존재하여 $h_i(x)$가 임의의 연속함수에 대해 근사 가능하다.

![Alt text](image.png)

S=1, 2로 두고 APL을 학습시킨 예시. 

의문점: hidden layer를 늘리는 것과 학습가능한 Active function을 사용하는 것의 차이가 무엇인가.
예시로 APL을 적용한 2-depth NN을 구성해보겠다.

![Alt text](image-1.png)
layer1의 node 갯수를 $d_1$ 이라 하자.

위와 같이 구성된 신경망에서, hidden layer의 각 node들은 독립적인 APL을 Active function으로 갖는다. S=1이라 하자. 이때 각 APL은 최대 S+1개의 hinge point(꺾이는 점?)를 갖는다.
여기서 학습시켜야 할 parameter는 $2S\cdot d_1$ + $d_0 \cdot d_1 + d_1 + d_1 \cdot d_2 + d_2$개가 된다.
그럼 이때 expressivity는 어떻게 되는가?
Acitve function이 Relu라면, input size와 outputsize가 1일때 memorization capacity는 $d_1$이 되고 이는 hinge point(=layer1의 node)의 개수와 같다. APL을 사용한 상황에서, S=1일때 APL은 최대 3개의 piece를 갖는 piecewise linear가 되고, 이때의 expressivity는 2$\cdot d_1$이 된다. 귀납적으로 2-depth APL 신경망은 (S+1)$\cdot d_1$의 memorization capacity를 갖는 신경망이 된다.

이제 APL을 사용하지 않고, 대신 3-depth layer Relu 신경망을 생각하자.

![Alt text](image-2.png)
이 신경망에서, parameter의 갯수는 $d_0 \cdot d_1 + d_1 + d_1 \cdot d_2 + d_2 + d_2 \cdot d_3 + d_3$개이다. APL과 Relu 두 신경망 모두 input과 output의 node, 즉 $d_0, d_2(APL), d_3(Relu) = 1$ 이라 하자.즉 APL의 경우 실질적으로 expressivity에 관여하는건 $d_1$와 active function, Relu의 경우 $d_1$과 $d_2$이다. Relu의 경우, $layer_2$가 APL에서의 Adoptive active function의 역할을 하므로, $d_2$ = S 라 하자. 그럼 이 setting에 맞춰 parameter의 갯수를 다시 보면

APL의 경우: 2S$d_1$ + $d_0 \cdot d_1$ + $d_1$ + $d_1 \cdot d_2$ + $d_2$

= (2S + 3) $\cdot d_1$ + 1

Relu의 경우: $d_0 \cdot d_1$ + $d_1$ + $d_1 \cdot d_2$ + $d_2$ + $d_2 \cdot d_3$ + $d_3$

=(2+S)$\cdot d_1$ + 2S + 1

이 되고, S가 논문에서 비교적 작게 설정한 것을 고려하면, Relu의 경우가 APL의 경우에 비해 parameter가 현저히 적다는 것을 알 수 있다. 이때 Relu의 expressivity는 S$\cdot d_1$이 된다. 

그렇다면 굳이 active function을 학습시키지 않아도, hideen layer를 한 층 늘리는 것만으로 더 적은 파라미터로 비슷한 효과를 얻어낼 수 있는데 왜 굳이 APL이 필요한 것인가?

