
---
title:  "PhotoVerse"
excerpt: "

categories:
  - Paper/{category}
tags:
  - [Blog, jekyll, Github, Git]

toc: true
toc_sticky: true
 
---

## Intro
- Editing face-centric images preserving identity of person is quite as difficult problem among personalization tasks. Because Stable Diffusion lacks of generating power in precise and sophiscated objects, such as, nose, eyes, etc. As you can see these kinds of particles affects the representation of human face. And most of the people easily tell if the generated facial images are awful...
- This Paper solve this problem. Poor facial-identity preserving personalization.
- And most of the personalization methods encounter multiple challenges, such as long tuning times, large storage requirements, the necessity for multiple input images per identity. 

## Method
- To address the aforementioned issues, we present a novel methodology that leverages a dual-branch conditioning mechanism, combining improved identity textual embeddings and spatial concept cues through dual-modality adapters in both the text and image domains.
- This involves designing adapters to project the reference image into a pseudo word and image feature that accurately represents the concept.
- Subsequently, a multiadapter architecture is employed to translate the image features from each layer into multi-word embeddings, resulting in S∗ = S1, ..., Sm.![|400](https://photoverse2d.github.io/figs/framework.png)
- Added conditions and fine-tune the weights in the cross-attention module ![[Pasted image 20230918234815.png|400]]

## Experiment
- ![|400](https://photoverse2d.github.io/figs/results/supp-res1.png)
![[Pasted image 20230918234905.png|400]]
## Conclusion from 🦖
- I think there's few novelty in this work, InstantBooth ver2?
- They solve personalization problem with training method, using CLIP image encoder to encode reference identity image. And input it into MLP projection layers and LoRA layer. Prior one convey the general style of reference image with token injection via CLIP text encoder, and later one can convey the specific image feature into T2I model.