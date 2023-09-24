### Consistent Diffusion Features for Consistent Video Editing

#arxiv23_Jul #w/o_finetuning

## Intro
- Project webpage: https://diffusion-tokenflow.github.io
- Video semantic editing without SD finetuning. Specifically, given a source video and a target text-prompt, this model generates a video that adheres to the target text, while preserving the spatial layout and motion of the input video. ![[스크린샷 2023-07-23 오전 11.14.23.png]]
- Existing works have been editing videos frame by frame based on the editing ability of SD, but this inevitably leads to content inconsistency. This paper attempts to solve this inconsistency by enforcing consistency in the editing process of *internal diffusion features across frames*.
- This paper is based on a key observation that consistency in the edited video can be obtained by enforcing consistency in the diffusion feature space. (maybe "DIFT"). Maintaining consistency by propagating diffusion features based on inter-frame correspondences. If you edit on a frame-by-frame basis(Per-frame editing in below figure), the RGB-level consistency breaks, showing that the feature level is also inconsistent. We can infer from this that the feature level consistency directly affects the RGB level consistency.![[스크린샷 2023-07-23 오후 12.02.38.png]]

## Method
- That is, the level of redundancy and temporal consistency of the frames in the RGB space and in the diffusion feature space are tightly correlated.
- **Fine-grained feature correspondence**. The features extracted from the source frame (tokens from the self-attention module) are used to reconstruct neighboring frames.  They can be used in two ways: (a) by simply swapping each feature in the target (for all layers at all sampling time steps), and (b) by performing simple pixel-level warping in RGB space. For warping, the Nearest-Neighbor Field (c) is used.  (c) is the difference computed between the source frame feature and the target frame feature extracted from the decoder layer (highest resolution).  You can see that (b) is reconstructed more naturally.  ![[스크린샷 2023-07-23 오후 2.06.37.png]]
- **Pre-processings: extracting diffusion features**. Apply DDIM inversion to each frame and input the noisy frames into the diffusion model to extract tokens (extract the tokens φ(x) from the self-attention module of every layer in the network εθ), which will be used to establish inter-frame correspondences between diffusion features.

## Experiment
- 

## Conclusion from 🦖
- 