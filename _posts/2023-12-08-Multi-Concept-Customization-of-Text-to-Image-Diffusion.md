![](../source/Pasted%20image%2020231208211640.png)
We find that only optimizing a few parameters in the text-to-image conditioning mechanism is sufficiently powerful to represent new concepts while enabling fast tuning (∼ 6 minutes). Additionally, we can jointly train for multiple concepts or combine multiple fine-tuned models into one via closed-form constrained optimization.

Given a set of target images, our method first retrieves (generates) regularization images with similar captions as target images. The final training dataset is union of target and regularization images. During fine-tuning we update the key and value projection matrices of the cross-attention blocks in the diffusion model with the standard diffusion training loss.
![|500](../source/Pasted%20image%2020231208211629.png)As shown in Figure 7, restricting the weight update to cross-attention key and value parameters leads to significantly better results for composing two concepts compared to methods like DreamBooth, which fine-tune all the weights
![](../source/Pasted%20image%2020231208211608.png)
![|400](../source/Pasted%20image%2020231208211602.png)
![|400](../source/Pasted%20image%2020231208211552.png)
### Results
![](../source/Pasted%20image%2020231208211542.png)