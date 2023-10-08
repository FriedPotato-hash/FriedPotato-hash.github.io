
---
title:  "Directed-Diffusion: Direct Control of Object Placement through Attention Guidance"

categories:
  - Paper/Editing

toc: true
toc_sticky: true
 
date: 2023-10-08
last_modified_at: 2023-10-08

---
#stable_diffusion
## Intro
- Capability to “direct” the placement of characters and objects both within and across images is crucial for storytelling or something. And this is the first diffusion technique that provides positional control over multiple objects, while making use of an existing pre-trained model and maintaining a coherent blend between the positioned objects and the background.![|400](../Pasted%20image%2020231009001534.png)
- They inject “activation” at desired positions in the cross-attention maps corresponding to the objects under control, while attenuating the remainder of the map. The position of the cat is evident early in the process (red box), however the details that define it as a cat are not yet clear.![|400](../Pasted%20image%2020231009001549.png)
## Method
- The overview of this paper, which is quite simple.![|500](../Pasted%20image%2020231009001620.png)
- Cross-Attention Map Guidance ![|400](../Pasted%20image%2020231009001707.png)![|500](../스크린샷%202023-10-09%20오전%2012.21.13.png)

## Experiment
- Qualitative Results
If you wanna place the hamburger at the left down position, you should find out the proper trade-off between the number of editing steps and the number of trailing attention maps.![|400](../Pasted%20image%2020231009001724.png)![|400](../Pasted%20image%2020231009001753.png)

## Conclusion from 🦖
- This paper first attempt to explicitly position the objects, or animals, etc.
- The idea of manipulating cross-attention map can be novel. 