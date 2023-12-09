![](../source/Pasted%20image%2020231208211214.png)
FastComposer, a tuning-free, personalized multi-subject text-to-image generation method. Our key idea is to replace the generic word tokens, such as "person", by an embedding that captures an individual’s unique identity in the text conditioning.

### Existing Limitations

Existing methods are inefficient due to the subject-specific fine-tuning, which is computationally intensive and hampers efficient deployment. Moreover, existing methods struggle with multi-subject generation as they often blend features among subjects.
![](../source/Pasted%20image%2020231208211303.png)

Firstly, current methods blend the distinct characteristics of different subjects (identity blending), shown by the right figure where Newton resembles Einstein. Cross-attention localization (Sec 4.2) solves this problem. Secondly, they suffer from subject overfitting, where they overfit the input image and ignore the text instruction. Delayed subject conditioning (Sec 4.3) addresses this issue.

### Details

We use a vision encoder to derive this identity embedding from a referenced image, and then augment the generic text tokens with features from this identity embedding. This enables image generation based on subject-augmented conditioning. Our design allows the generation of images featuring specified subjects with only forward passes and can be further integrated with model compression techniques [2, 11, 40] to boost deployment efficiency.

![](../source/Pasted%20image%2020231208211328.png)
Training: (CLIP)Image Encoder & MLP - blue colored & U-Net - red colored

Segmentation maps and cross-attention regularization are only used during training, not at test time.

![](../source/Pasted%20image%2020231208211350.png)

Figure 3: Training and inference pipeline of FastComposer. Given a text description and images of multiple subjects, FastComposer uses an image encoder to extract the features of the subjects and augments the corresponding text tokens. The diffusion model is trained to generate multi-subject images with augmented conditioning. We use cross-attention localization (Sec. 4.2) to boost multi-subject generation quality, and delayed subject conditioning to avoid subject overfitting (Sec. 4.3).

`Localizing Cross-Attention with Segmentation Masks.`
![](../source/Pasted%20image%2020231208211401.png)

`Delayed Subject Conditioning in Iterative Denoising`
During inference, using the augmented text representation directly often leads to images that closely resemble the subjects while ignoring the textual directives. This occurs because the image layout forms at the early phases of the denoising process, and premature augmentation from the reference image causes the resulting image to stray from the text instructions.
![](../source/Pasted%20image%2020231208211422.png)
![](../source/Pasted%20image%2020231208211435.png)
### Results
![](../source/Pasted%20image%2020231208211446.png)
![](../source/Pasted%20image%2020231208211454.png)
![](../source/Pasted%20image%2020231208211500.png)