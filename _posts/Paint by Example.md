### Exemplar-based Image Editing with Diffusion Models

created at : 2023-06-03 17:48
#arxiv22_Nov  

## Intro
- conditional image로 scene을 editing하는 태스트. reference image의 객체를, source image의  editing region (masking)에 넣음. 기본적으로 blended diffusion, shape guided diffusion과 같은 문제를 풀어내는데 마스킹된 위치에 채워넣어야할 이미지 guidance를 text가 아닌 image로 제공한다는데 있다.![[스크린샷 2023-06-03 오후 5.50.01.png]]
- self-supervised training을 leveraging해서 source image랑 exemplar를 disentangle&re-organize 할 수 있게 됨! 
- naive approach는 fusing artifacts를 유발할 수 있으니 이에 관련하여 분석을 추가했고, information bottleneck을 제안했으며 exemplar를 그냥 copy&paste하는 trivial solution을 방지하기 위해 augmentation 방법을 추가함.
- iterative optimization없고, diffusion model의 one-forward로 이미지 생성해냄.

## Method
- 해결해야되는문제  
	1) reference의 exampler의 semantic한 정보와 fine detail을 따와야 하며 그 도중에 background를 놓치면 안됨  
	2) exampler를 적절하게 trasnform해서 넣어주는게 여간 쉬운 일이 아님.  
	3) merging boundary가 어색하면 안됨  
	4) reference 이미지의 resolution이 source image보다 안좋을때가 많음. 따라서 process과정에 super-resolution을 포함해야 됨.
- condition으로  CLIP textencoder의 결과 (77토큰)을 넣어주는데 얘를 CLIP image encoder의 결과로 단순 대체해서 넣어준 결과.  training 과정에서 loss잘 떨어지는데 test set 돌려보면 unsatisfact하다.![[Pasted image 20230603175222.png]]
- training process![[Pasted image 20230603175256.png]]
- CLIP text token을 condition으로 사용한다면 semantic한 의미를 intrincsic하게 해석할 수 있게 된다. 그러나 CLIP image token은 image에 대한 정보를 그자체로도 너무 잘 유지하고 잇어서 copy/paste라는 trivial solution으로 가기가 쉬운것...
- 해당 논문에서는 그래서 one dimensional 1024 vector - class token만을 사용한다!!!  high frequency detail은 좀 버려주는 동시에, highly compressed된 semantic information을 제공한다.  
- null text condition을 주듯 learnable vector v를 20%의 비율로 reference ocndition을 대체해서 부여함. inference step에서는 CFG 식을 다음과 같이 짜둠. ![[Pasted image 20230603175409.png]]

## Experiment
- Dataset
	- Then we select OpenImages [32] as our training dataset. It contains a total of 16 million bounding boxes for 600 object classes on 1.9 million images. 
- Resources
	- During training, we preprocess the image resolution to 512×512, and train our model for 40 epochs, which takes about 7 days on 64 NVIDIA V100 GPUs.
- Comparisons ![[Pasted image 20230603175500.png]]
- Ablations![[Pasted image 20230603175511.png]]
- Cute results..![[Pasted image 20230603175522.png]]

## Conclusion from 🦖
- fine detail을 잡는데 CLIP image encoder가 확실히 도움이 되겠다 싶다.
- 나온지 꽤 시간이 지난 논문인데 demo 결과도 나쁘지 않고 재밌었음. 데모 돌려본 결과가 조금 아쉬웠던 것은, examplar에서 high-level의 semantic한 정보를 가져오는 경향이 더 컸다는 것. 예를 들어 건담 이미지를 넣고 싶은데 왠 뿌슝파슝 번쩍번쩍한 로봇을 갖다 놓았다. 
- resource 소요가 크다는 것도 아쉬운 점. 