
---
title:  "Diffused Heads: Diffusion Models Beat Gans on talking face generation"

---
#arxiv23_Jan 

## Intro
- Despite their power, GANs have several drawbacks when applied to speech-based video synthesis. First, GAN training is notoriously difficult, often requiring extensive architectural exploration and parameter tuning to converge. The learning stability of GAN-based facial animation methods can be improved by using additional guidance such as masks or driving frames to guide the generation process. However, this limits the model to facial reenactments and reduces its ability to generate original head movements and facial expressions.  
- In addition, GAN training can often lead to mode collapse, meaning that the generator is unable to produce samples that cover the entirety of the data distribution and is instead trained to produce only a few unique samples.  
- Finally, traditional one-shot GAN-based solutions suffer from face distortion in the generated video, especially when generating videos with large head movements. This is solved by switching to a few shot approach or by relying on a pre-trained face verification model that acts as an oracle to maintain identity consistency.  
- Diffused Heads, a frame-based diffusion model that solves all of the above problems to generate realistic videos with only one identity frame and a voice recording. The generated head moves and behaves naturally while maintaining the subject's identity and plausible lip sync. Unlike most recent approaches, the authors use DDPM instead of adversarial learning, which does not require stabilizing the discriminator. To eliminate the problem of unnatural appearance, they introduce motion frames to iteratively guide the video generation. To maintain consistency between the speech and the generated frames, we assume the use of motion audio embedding, which is inserted into the model through a novel conditioning approach. Finally, instead of using a pre-trained oracle model, we made a simple modification to the loss function to maintain the consistency of lip movements.  

![|400](../source/Pasted%20image%2020231110195706.png)

## Method
- Diffused Heads generates one frame at a time from an embedded voice recording using a fixed ID frame and a pre-trained audio encoder during the entire generation process. To achieve smoother and more expressive results, we introduce motion frames and motion audio embedding to inject additional information about past movements and future expressions. We also define an additional lip sync loss function to make the model pay more attention to the mouth area.    
![](../source/Pasted%20image%2020231110195831.png)

## Experiment
- **Quantative**
![](../source/Pasted%20image%2020231110195938.png)
## Conclusion from 🦖
- 