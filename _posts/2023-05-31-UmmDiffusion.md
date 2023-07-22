### Unified Multi-Modal Latent Diffusion for Joint Subject and Text Conditional Image Generation

#arxiv23_Mar #CLIPspace #w/o_finetuning

## Intro
- novel task임. caption과 함께 이미지가 주어진다면, caption에 대한 image subject를 파악해서 대상에 대한 editing을 하도록 함.

## Method
- MLP만 trainable하며 loss는 diffusion recon으로 해서 TIUE를 학습시킨다. 
- ![[Pasted image 20230530094434.png]]![[Pasted image 20230530094450.png]]
- 피겨를 간단하게 설명하면 image는 image encoder를 통해 embedding을 추출하고 trainable한 MLP layer를 통과시켜 CLIP text space 쪽으로 떨궈준다. 아마도 CLIP space가 이미 text&image align이 잘 맞아있어서 단순 MLP로도 projection이 가능한듯 보임.  
- h_u는 h_y(original hidden state of text)에서 대상의 위치 부분만 h_r(rearranged hidden state)로 대체해준 latent이며, inference 단에서 h_y와 함께 trade-off를 고려해서 condition으로 넣어준다.
- ![[Pasted image 20230530094917.png|400]]![[Pasted image 20230530094932.png|200]]
- CFG까지 고려해주면 다음과 같은 sampling 식 완성
![[Pasted image 20230530095002.png]]

## Experiment
- style도 학습 가능하네여?![[Pasted image 20230530095310.png]]
- Umm-Diffusion이 가장 SD와 비슷하게 나옴.![[Pasted image 20230530095449.png]]

![[Pasted image 20230530095500.png]]
- failure case...![[Pasted image 20230530095506.png]]
	- multiple subjects에 대해서는 blending 되어 나올때가 있네
	- rare한 subject에 대해서는 잘 못함.

## Conclusion from 🦖
- 기존 personalization 논문들은 subject에 optim시키면 원래 SD의 결과처럼 잘 안나왔다. 그 특유의 artistic함이 잘 안나왔음. 이 말은 pretrained SD의 능력을 fully exploit하지 못한다는 말과 비슷함. Dreambooth로 오바마 특정 사진들에 fitting시킨다고 하면 그 사진들에 화질에 upperbound느낌으로 한계가 생긴다. INIT night 데이터셋에 학습시켜도 그러함.
- 위와 같은 문제를 좀 해결해준 느낌이 든다. 결국 CLIP space 위에서 SD가 학습된 거를 SD의 입장에서 가장 이질감 없이 받아들 수 있는 subject dependent한 latent를 제공해준 느낌
- 근데 우리 태스크에 적용하기에는 좀 한계가 있음. subject 자체의 semantic한 latent를 찾아서 blending해주는게 목표인 논문 같으넫 우리는 subject 내부의 것을 fine-detail하게 manipulation해줘야 하는걸.