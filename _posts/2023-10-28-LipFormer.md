
---
title:  "LipFormer: High-fidelity and Generalizable Talking Face Generation with A Pre-learned Facial Codebook"

categories:
  - Paper/video_driventh

toc: true
toc_sticky: true
 
date: 2023-10-28 
last_modified_at: 2023-10-28

---
#CVPR23 

## Intro
- **Codebook** pre-learned on high-quality face images can serve as a useful prior that facilitates high-fidelity and generalizable talking head synthesis. ![](../source/Pasted%20image%2020231028003012.png)
- “LipFormer, a Transformer-based framework to model the audio-visual coherence and predict the lip-codes sequence based on input audio features.”
## Method
- **Overall Architecture** 1) Audio Encoder; 2) Face Encoder; 3) Adaptive Face Warping Module; 4) Transformer Network; 5) Face Decoder.![](../source/Pasted%20image%2020231028002942.png)
- “LipFormer first encodes the target face using the pre-learned codebook, and then replaces the lipregion features with a new sequence of lip-codes that are predicted by aligning with the reference audio.”
- “Before lip code prediction, we introduce an Adaptive Face Warping Module, which helps warp the reference face to the target pose in the feature (i.e., codebook) space, to alleviate the texture mismatch problem caused by different poses.”
- “Following VQVAE [33] and VQGAN [23], we first train a quantized autoencoder based on a face reconstruction task, which extracts HQ facial priors from plenty of face data and encodes them into codebooks. We learn two separate codebooks, one for upper face encoding CU , and another for bottom face encoding CB.”![](../source/Pasted%20image%2020231028002930.png)
- “An intuitive idea is to use the upper face and audio to do the prediction, but the texture details of mouth will be lost. Thus, we introduce a reference face to guide texture learning. Directly sending the reference mouth into the network may lead to texture mismatch. Consequently, we propose an adaptive face warping module to reduce the pose biases between the reference and target faces.”  

- As we claimed before, RB and TB have similar lip textures, but their poses are usually different. Feeding HRB into the Transformer directly is not a good choice, we need to reduce the pose biases between HRB and HTB, which inspires us to use the pose displacement of the upper face (i.e., HTU and HRU ) to estimate that of the bottom one (i.e., HTB and HRB ).”

- “We adopt a Transformer [34] module Tr to model the audio-lip correlations, which requires three inputs: the extracted audio feature FAud, the warped reference lip encodings c HRB and the learned bottom codebooks CB.”![](../source/Pasted%20image%2020231028002958.png)

## Experiment
- ![](../source/Pasted%20image%2020231028003003.png)

## Conclusion from 🦖
- 