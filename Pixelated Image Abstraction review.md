# Pixelated Image Abstraction review

![image](https://github.com/SpiralShot/Paper_review/assets/123755711/3ededca6-b485-4967-b209-d2b127c8039e)

> 0. 초록(not green)
>    
> Pixel Art는 저해상도 화면과 같이 해상도와 색이 제한된 상황에서 나타나는 이미지 스타일을 예술로 발전시킨 디지털 아트의 한 분야다.
>
> 언뜻보면 몇개의 점으로 그래픽이 이루어지기에 특별한 기술적 테크닉이 필요 없어보이지만,
>
> 사실 제한된 색과 해상도를 가지고 원하는 인상을 주도록 하는 것은 쉽지 않다.
>
>Pixel Art는 입문은 쉽지만 원하는 그림을 자유자재로 그리기는 어려운 예술 양식으로 꼽힌다.
>
![image](https://pbs.twimg.com/media/Eb5T0qwU4AA-k6z?format=jpg&name=4096x4096)
출처: (https://twitter.com/Franrekk)
>Pixel Art에서 점 하나의 중요성을 보여주는 예시이다.
>
>이미지를 구성하는 총 픽셀수가 100개도 안되는 극도로 제한적인 환경에선, 하나의 점도 큰 의미를 갖게 된다.
>
>때문에 Pixel Art 이미지를 알고리즘을 통해 생성하거나, High resolution image에서
>
>Very low resolution image로 Downscailing하는 것은 어려운 과제다.
>
>Nearst Method, Bicubic Method, Subsampling 등 여러가지 이미지를 Downscaling하는 방법들이 있지만
>
>기존의 방식들은 Downscaling하는 과정에서 이미지의 정보를 손실함에 따라
>
>필연적으로 원본 이미지가 무엇이었는지 유추하기도 힘들 정도로 훼손된다.
>
>이 paper에서는 SLIC, MCDA, Bilateral filter, Laplacian smoothing 등의 기법을 사용하여
>
>보다 자연스러운 Pixelation을 하는 알고리즘을 소개한다.


> 1. 배경
> 해당 논문에서는 두가지 핵심 method를 사용하여 알고리즘을 구성한다.
> 이미지가 주어졌을 때 이미지를 Superpixel 단위로 쪼개고,
> 그 다음 pallet를 구성하고 refine한다.
> 이때 이미지를 Superpixel 단위로 쪼갤 때 SLIC method를,
> pallet를 refine할 때 MCDA를 사용한다. 
> ![Alt text](image.png)
> SLIC로 만들어낸 SuperPixel 이미지.
>
> 본 논문의 Task는
> 
> (1) Input 이미지, Parameter K에 대해 각 Pixel들을 K개로 자연스럽게 Clustering한다.
> 
> (2) Clustering된 Superpixel들에 대해 자연스럽게 색을 할당한다.
>
> 아래에서 설명하는 알고리즘들은 주어진 Task를 효과적으로 해결하지만,
>
> 몇가지 한계를 갖는다. 이는 후술한다.
>
> 2. 방법
>
> 보다 자연스러운 Pixelization을 위해 해당 논문에서는 Modified SLIC와 MCDA를 사용한다.
>
> 알고리즘을 간략하게 요약하면 
> 1) SLIC을 사용하여 Superpixl을 나눈다.
> 2) MCDA를 사용하여 color를 할당하고 pallet를 refine, expand한다.
>
> 1.을 자세히 보자.
>
> SLIC는 K_Means Clustering의 개선된 method다. 
> 
> 기존의 K_Means에서는 클러스터의 갯수를 설정할 수 있다는 장점을 가졌지만, M개의 input pixel에 대해 N개의 Superpixel center에 대해 M^N번의 연산을 수행해야하는 단점을 가졌다.
>
> SuperPixel을 만들 때 이런 방법은 비효율적이고 불필요하므로, SLIC는 Pixelization에 최적화하여 사용한다.
>
> SuperPixel의 Size를 S라 할때, 각 SuperPixel의 Center C_k에 대해 C_k를 중심으로 하는 2S*2S의 상자에서의 Data만 고려한다. 이렇게 하면 Pixelization에서 필요한 Local Minimum만을 효율적으로 계산할 수 있다.
>
> 이 각 상자는 초깃값 설정에서 regulr grid로 나누어지고, 각 C_k는 이 상자의 중심이 된다.
>
> distant = 
> 
> color_distant(input_pixel[r][c], C_k) + m*sqrt(N/M)*position_distant(input_pixel[r][c], C_k) 
>
> 라 하면, 위에서 설명한 2S*2S 상자 안의 input_pixel들이 위 공식 distant를 최소화하는 값으로 
>
> 각각의 C_k를 중심으로 갖는 상자로 할당된다.
> 
> 이렇게 한 뒤 C_k에 할당된 점들의 Position 무게중심으로 C_k를 재할당한다.
>
> 이때, 무게중심과 C_k와의 3:2 내분점만큼 이동시킨다. 이후 C_k의 움직임이 수렴할 때까지 이 과정을 반복한다.
>
> 이제 Laplacian smoothing과 사용하여 마지막으로 SuperPixel을 refine하고, Average Color로 색을 할당하되 Bilateral filter를 사용하여 smooth shadowed surface를 표현하기 힘든 문제를 해결하면서 Color를 할당한다.
>
>
>
> 이제 2.를 자세히 보자.
> 3가지 step으로 나눌 수 있다.
> Associating, Refine, Expand
> Associating과 Refine, 그리고 Superpixel Refine은 서로 상호보완적으로 일어난다. 때문에 이해하기 많이 힘들었다. 
> 
> 사실 아직 잘 모르겠다. 왜 확률론을 안들었는지 후회된다.
>
> 각각의 SuperPixel P_s, 그에 할당되는 색 c_k에 대하여
> P_s에 c_k가 할당될 조건부확률은 P(c_k)*exp(-distant(p_s avg color, c_k)/T)로 계산된다.
> 이때 P(c_k)는 초기화때 1의 값을 갖는다. 이후 더 색이 추가되게 되면 p_s에 대해 주변화하여
>
> P(c_k) = ∑1~N (P(c_k|p_s)P(p_s))가 된다.
>
> 이제 P(c_k)는  P(c_k|p_s)의 값에 따라 종속적으로 바뀌고, 각각의 Superpixel들은 P(c_k|p_s)가 최대인 값에 대해 c_k를 할당받는다. 각 반복마다 T에 alpha = 0.7을 곱하여 iteration을 수행하고 T가 수렴하게 되면 iteration을 멈춘다.
>
