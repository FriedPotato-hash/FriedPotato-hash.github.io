### Abstract

First, we introduce a simple perspective-aware data augmentation that synthesizes new training examples with more diverse views by perturbing the existing ones in a geometrically consistent manner. Second, we propose a conditional model that exploits the per-image camera pose as prior knowledge by encoding it as a part of the input.

⇒ 뎁스 예측 정확도 향상, unseen camera poses에 대해서도 가능.

---

### Introduction

`**기존 방법들의 문제점**`

뎁스 데이터셋들의 카메라 pose가 biased 되어 있어서 흔하지 않은 각도에서 촬영된 사진은 뎁스 예측 정확도가 떨어짐.
![](../source/Pasted%20image%2020231202002556.png)
**`Contribution 정리`**

depth prediction under diverse test-time camera poses의 정확도 향상시키는 두가지 방법 제안.

1. CDA 말고, PDA로 추가 데이터셋 생성
![](../source/Pasted%20image%2020231202002542.png)
1. camera pose를 prior로 사용해서 conditional depth predictor를 학습

---

## PDA

훈련에 사용되는 이미지의 RGB, detph map을 모두 사용해서 augmentation 진행.

first perturbs the camera pose, re-projects all pixels to a new image, and recomputes the depth values for the new image using the new camera pose and the original depth map.
![](../source/Pasted%20image%2020231202002528.png)
![](../source/Pasted%20image%2020231202002520.png)
depth value for each pixel in the new depth map $D_s(q')$
![](../source/Pasted%20image%2020231202002508.png)
그러나 실제로는 Backward warping과 bilinear interpolation을 사용해서 depth map을 구해낸다.

[warping - 내 맘대로 보는 세상](https://b.mytears.org/tag/warping/)

## Depth Prediction with camera pose prior

depth map은 view-dependent representation으로, scene geometry & camera pose 에 의존한다. camera pose는 본질적으로 depth에 대한 사전지식 (prior knowledge)를 제공하니까, 이 논문에서는 conditional depth predictor를 camera pose의 사전지식을 제공하여 학습시켜보려고 한 것.

`Naive encoding approach`

3 DOF (height h, pitch of the camera $\theta$, any roll w)

나이브한 접근으로는, 위의 3 DOF를 RGB input과 함께 넣어줘서 depth를 예측해내는 방법이 있겠다.

우선, 3DOF를 2D map으로 변환해서 이미지 인풋에 concat해서 넣어준다. (걍 이미지 사이즈로 매트릭스 만들어서 붙여주는 식.)
![](../source/Pasted%20image%2020231202002456.png)
여기에는 문제가 있는데, translation-equivariant convolution이 pose에 적합하지 않다는 것. 따라서 CPP encoding을 아래와 같이 제안했다.

`CPP encoding`
![](../source/Pasted%20image%2020231202002449.png)
Intuitively, it encodes the camera pose by intersecting rays from the camera center with pre- defined ground/ceiling planes and recording the depth.

To train a conditional depth prediction, we simply con- catenate the CPP encoded map with the corresponding RGB as a four-channel input. This implies that our CPP encoding approach applies to _any_ network architectures with a simple modification on the first convolution layer.

⇒ CPP로 3DOF를 인코딩해서 RGB img 뒤에 붙여준 다음에 인풋으로 넣어준다. 다른 모델들의 첫번째 conv layer 입력 channel만 변경해주면 폭넓게 적용 가능.

---

## Experiments
![](../source/Pasted%20image%2020231202002441.png)
![](../source/Pasted%20image%2020231202002436.png)
아래 실험 결과를 보면 camera pose 자체가 scene의 depth에 대한 강한 prior knowledge를 갖고 있다는 것을 알 수 있다.
![](../source/Pasted%20image%2020231202002430.png)