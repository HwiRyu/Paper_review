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
> 때문에 Pixel Art는 입문은 쉽지만 원하는 그림을 자유자재로 그리기는 어려운 예술 양식으로 꼽힌다.
>
![image](https://pbs.twimg.com/media/Eb5T0qwU4AA-k6z?format=jpg&name=4096x4096)
출처: (https://twitter.com/Franrekk)
>개중에 대표적인 예시로 꼽히는 Franek의 작품이다.
>
>구성하는 총 픽셀수가 100개도 안되는 극도로 제한적인 환경에선, 하나의 점도 큰 의미를 갖게 된다.
>
>때문에 Pixel Art 이미지를 알고리즘을 통해 생성하거나, High resolution image에서
>
>Very low resolution image로 Downscailing하는 것은 어려운 과제다.
>
>Nearst Method, Bicubic Method, Subsampling 등 여러가지 이미지를 Downscaling하는 방법들이 있지만
>
>기존의 방식들은 Downscaling하는 과정에서 이미지의 정보를 손실함에 따라 원본 이미지가 무엇이었는지
>
>유추하기도 힘들 정도로 훼손되는 경우가 많다.
>
>이 paper에서는 SLIC, MCDA, Bilateral filter, Laplacian smoothing 등의 기법을 사용하여
>
>보다 부드러운 Pixelation을 하는 알고리즘을 소개한다.


published : false
