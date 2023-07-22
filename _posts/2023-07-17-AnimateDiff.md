### Animate Your Personalized Text-to-Image Diffusion Models without Specific Tuning

created at : 2023-07-17 10:15
#arxiv23_Jul 

## Intro
- Without specific tuning, most personalized T2I models could be directly animated by inserting the well-trained motion modeling module.
- hugging face dreambooth weights, CIVIT ai weights를 그대로 가져다가 motion module weight만 갈아 끼워줘서 general하게 적용할 수 있는 모델을 만들었다는 것이 장점

## Method
- motion modeling module을 video dataset에 대해 training 해주고, motion prior가 distil될 수 있도록 함. T2I base parameter는 frozen 시켜서 feature space를 유지할 수 있도록 함.![[스크린샷 2023-07-17 오전 10.18.02.png]]
- temporal and spatial axes are reshaped into the batch axis separately. motion module은 vanilla temporal transformer이고, zero-initialized output project layer가 달려있음![[스크린샷 2023-07-17 오전 10.21.09.png]]
- loss 식은 latent diffusion 이랑 비슷.![[스크린샷 2023-07-17 오전 10.28.23.png|400]]

## Experiment
- 256x256, 16 frames video clip. 
- During experiments, we discovered that using a diffusion schedule slightly different from the original schedule where the base T2I model was trained helps achieve better visual quality and avoid artifacts such as low saturability and flickering. Thus, we used a linear beta sched- ule, where βstart = 0.00085 and βend = 0.012.
- **Text2Video-zero vs. AnimateDiff** - It is noticeable that while the baseline results lack fine-grain consistency, our method maintains a better temporal smoothness.![[스크린샷 2023-07-17 오전 10.32.47.png]]
## Conclusion from 🦖
- LVDM + Pose diffusion 을 섞은 느낌.
- rerender a video가 더 잘될 거 같다.