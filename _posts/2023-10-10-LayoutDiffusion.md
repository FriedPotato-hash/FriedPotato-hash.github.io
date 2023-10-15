
---
title:  "LayoutDiffusion: Controllable Diffusion Model for Layout-to-image Generation"
excerpt: "How to control the image composition on generated images"

categories:
  - Paper/Diffusion/conditional_generating
tags:
  - [Layout]

toc: true
toc_sticky: true
 
date: 2023-10-10 
last_modified_at: 2023-10-10

---
#CVPR23 

## Intro
- Image generation models based on diffusion scheme show the break-through improvement in image generation domain, “However, when it comes to the layout-to-image generation where an image often has a complex scene of multiple objects, how to make strong control over both the global layout map and each detailed object remains a challenging task.”
- “To overcome the difficult multimodal fusion of image and layout, we propose to construct a structural image patch with region information and transform the patched image into a special layout to fuse with the normal layout in a unified form.”
- “Layout Fusion Module (LFM) and Object-aware Cross Attention (OaCA) are proposed to model the relationship among multiple objects and designed to be object-aware and position-sensitive, allowing for precisely controlling the spatial related information.”
![](../source/Pasted%20image%2020231013221607.png)
## Method
- “(a) layout embedding that preprocesses the layout input, (b) layout fusion module that encourages more interaction between objects of layout, (c) image-layout fusion module that constructs the structal image patch and objectaware cross attention developed with the specific design for layout and image fusion, (d) the layout-conditional diffusion model with training and accelerated sampling methods.”![](../source/Pasted%20image%2020231013221746.png)
- “consisted of bounding box b and objects categories c is transformed into embedding BL, CL, L”
- “Object-aware Cross Attention for Local Conditioning.”



## Experiment
- “COCO-Stuff” & Visual Genome for training=
![](../source/스크린샷%202023-10-13%20오후%2010.19.25.png)
## Conclusion from 🦖
- one-stage end-to-end diffusion model named LayoutDiffuison, which is novel for the task of layout-to-image generation
- By constructing a structural image patch with region information, we regrad each patch as a special object and accomplish the difficult multimodal image-layout fusion in a unified form.