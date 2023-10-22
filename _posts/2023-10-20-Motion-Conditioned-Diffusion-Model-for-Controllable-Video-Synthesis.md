
---
title:  "Motion-Conditioned Diffusion Model for Controllable Video Synthesis"
excerpt: ""

categories:
  - Paper/Motion_conditioned_diffusion
tags:
  - [video, diffusion]

toc: true
toc_sticky: true
 
date: 2023-10-20 
last_modified_at: 2023-10-20

---
#NVIDIA #META #Google

## Intro
- Given a start frame and a set of strokes, MCDiff synthesizes a video matching the desired motion while preserving the content. 
- **MCDiff**, a conditional diffusion model that generates a video from a starting image frame and a set of strokes, which allow users to specify the intended content and dynamics for synthesis. To tackle the ambiguity of sparse motion inputs and achieve better synthesis quality, MCDiff first utilizes a flow completion model to predict the dense video motion based on the semantic understanding of the video frame and the sparse motion control. Then, the diffusion model synthesizes high-quality future frames to form the output video.![|600](../source/Pasted%20image%2020231020200820.png)

## Method
- **MCDiff** is an autoregressive video synthesis model. For each time step, the model is guided by the **previous frame** (i.e., start or previously predicted frame) and the **momentary segment of input strokes**. Our flow completion model first predicts dense flows representing per-pixel momentary motion. Then, the future-frame prediction model **synthesizes the next frame based on the previous frame and the predicted dense flow** through a conditional diffusion process.![](../source/Pasted%20image%2020231020201054.png)

## Experiment
- Qualitative![](../source/스크린샷%202023-10-20%20오후%208.14.41.png)

## Conclusion from 🦖
- I think that 'DisCo' can do much better..