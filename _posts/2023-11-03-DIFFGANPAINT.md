
---
title:  "FAST INPAINTING USING DENOISING DIFFUSION GANS"

toc: true
toc_sticky: true
 
date: 2023-11-03 
last_modified_at: 2023-11-03

---
#iclr23_tinypaper
## Intro
- Free-form image inpainting is the task of reconstructing parts of an image specified by an arbitrary binary mask. In this task, it is typically desired to generalize model capabilities to unseen mask types, rather than learning certain mask distributions.
- Capitalizing on the advances in diffusion models, in this paper, we propose a Denoising Diffusion Probabilistic Model (DDPM) based model capable of filling missing pixels fast as it models the backward diffusion process using the generator of a generative adversarial network (GAN) network to reduce sampling cost in diffusion models.
## Method
- Use trained guided diffusion and GAN model on Imagenet.
- Specifically, the inpainting is performed by first denoising the input image using the diffusion process, then extracting the masked region from the original image, and finally inpainting the masked region using the GAN generator. Therefore, we can concurrently leverage the structure consistency attained by DDPM, and high-quality rapid samples achieved by the GAN generator
```python
timesteps=100 

def denoise_diffusion(x, model): 
	noise = torch.randn_like(x) 
	for i in range(timesteps): 
		eps = torch.randn_like(x) 
		x = x + torch.sqrt(torch.tensor(2.0)) * eps 
		model_input = torch.cat([x, noise]) 
		out = model(model_input) 
		x = x + out * np.sqrt(1/timesteps) 
		return x 

def test_inpainting(img, gan,mask): 
	denoised_img = denoise_diffusion(img,gan) 
	model_input = torch.cat([denoised_img, mask ]) 
	output = gan(model_input) 
	output = output * mask + masked_image * (1 - mask)
```


## Experiment
- **Qual)** Original Image, input masked image and result image![](../source/스크린샷%202023-11-04%20오전%201.10.36.png)

## Conclusion from 🦖
- Exploiting a trained diffusion model and modifying the reverse diffusion using a GAN generator to paint images with better mode coverage and sample diversity