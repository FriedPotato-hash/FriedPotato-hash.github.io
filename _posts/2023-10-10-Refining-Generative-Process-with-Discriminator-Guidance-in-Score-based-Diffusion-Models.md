
---
title:  "Refining Generative Process with Discriminator Guidance in Score-based Diffusion Models"
excerpt: "Imply discriminator for guidance generation "

categories:
  - Paper/sampling_guidance
tags:
  - [sampling_guidance]

toc: true
toc_sticky: true
 
date: 2023-10-10 
last_modified_at: 2023-10-10

---
#sampling_guidance #discriminator #ICML23

## Intro
- Discriminator Guidance, aims to improve sample generation of **pre-trained diffusion models** (DDPM).
- Unlike GANs, our approach does not require joint training of score and discriminator networks. In- stead, we train the discriminator after score train- ing, making discriminator training stable and fast to converge.
- Discriminator Guidance adjusts the score function by estimating the gap cφ between the predicted model score and the true data score.![](../source/스크린샷%202023-10-10%20오후%209.32.42.png)

## Method
- In sample generation, we add an auxiliary term to the pre-trained score to deceive the discriminator. This term corrects the model score to the data score at the optimal discriminator, which implies that the discriminator helps better score estimation in a complementary way.
- After score training, we can synthesize samples. Now, how to train discriminator?![](../source/스크린샷%202023-10-10%20오후%209.36.51.png)![](../source/스크린샷%202023-10-10%20오후%209.51.40.png)
- Sampling algorithm using discriminator score![](../source/스크린샷%202023-10-10%20오후%209.42.39.png)
## Experiment
- Classifier Guidance generates high-fidelity but mode degenerated samples. But using classifier guidance with well-trained discriminator, it alleviates the model collapse problem![](../source/스크린샷%202023-10-10%20오후%209.33.49.png)
- Quantitative Experiments![](../source/스크린샷%202023-10-10%20오후%209.40.57.png)
- Qualitative experiments![](../source/스크린샷%202023-10-10%20오후%209.42.14.png)

## Conclusion from 🦖
- Giving generation guidance via pre-trained discriminator. 
- Training discriminator is much more stable than GAN training scheme.