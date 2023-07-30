### Blind Video Deflickering by Neural Flitering with a Flawed Atlas

created at : 2023-06-17 14:22
#video #post-processing #CVPR23 

## Intro
- 대부분의 video processign algorithm이 갖고 있는 문제; flickering artifacts를 해결하기 위해 제시된 post-processing 논문.
- 이전 work들은 flickering frequency, manual annotation등을 사람이 일일히 제공하거나 여분의 consistent video를 추가로 제공해야 됐음. 해당 논문은 추가적인 정보나 영상없이 하나의 input video만 있어도 de-flickering할 수 있도록 방법론 제시
- neural atlas를 neural filtering strategy와 함께 사용하는데, neural atlas는 하나의 비디오 안에서의 unified representation이고 temporal consistency guidance를 제공함. ![[스크린샷 2023-06-17 오후 3.03.16.png]]

## Method
- video에서 unified representatoin(atlas)를 뽑아내고, deflickering하는데 있어 consistent guidance를 제공함. atlas가 flawed하기 때문에 neural filtering strategy로 flaws를 필터링한다.
- Overall architecture![[Pasted image 20230617142555.png]]
- atlas를 만들어내는 Mapping MLP와 Atlas MLP를 jointly 학습시킴. 그러나 atlas representation이 rapid한 움직임에서는 flaw를 보이므로 이를 보완해줄 방법으로 neural filtering & refinement를 제안함. 
- Phase 1에서는 filter network F에 임의의 t번째 frame I_t와 t번째 frame을 입력으로 해서 atlas를 뽑은 A_t를 입력으로 넣는다. 그러면 O^f_t가 출력으로 나오는데 이를 모든 frame에 대해서 반복해서, T장의 O^f_t를 만들어 낸다. *How to train filter network?*
  augmentation을 appearance와 structure에 대해 때려주고, 원본 X를 recon하는 loss로 학습시킨다. ![[스크린샷 2023-06-17 오후 3.27.21.png|400]]
- Phase 2에서는 local refine을 진행하는데 phase1에서 filtering된 비디오 frames들은 short term&long term 모두 global하게 consistent하다. 그러나 input과 atlas-based frames간의 mismatch로 인해 local flicker가 남아 있을 수 있음. 이를 해결하기 위해 제안. 이전 work에서는 optical flow를 사용해서 local flicker를 잡았다. 다음 식과 같이 temporal consistency loss를 사용해서 학습시켰고, 추가적으로 reconstruction loss를 줬음.![[스크린샷 2023-06-17 오후 3.38.57.png|300]]
- First, we utilize a unified video representation named neural atlas [26] to solve the major challenge of solving long-term inconsistency. This neural atlas tracks all pixels in the video, and correspondences in different frames share the same pixel in this atlas. Hence, a sequence of consistent frames can be obtained by sampling from the shared atlas. Secondly, while the frames from the shared atlas are consistent, the structures of images are flawed: the neural atlas cannot easily model dynamic objects with large motion; the optical flow used to construct the atlas is not perfect. Hence, we propose a neural filtering strategy to take the treasure and throw the trash from the flawed atlas. A neural network is trained to learn the invariant under two types of distortion, which mimics the artifacts in the atlas and the flicker in the video, respectively. At test time, this network works well as a filter to preserve the consistency property and block the artifacts from the flawed atlas.

## Experiment
- The *network F* is trained on the MS-COCO dataset as we only need images for training. We train it for 20 epochs with a batch size of 8. 
- The *network L*, we train it on the DAVIS dataset for 50 epochs with a batch size of 8. We adopt the Adam optimizer and set the learning rate to 0.0001.
- Quantitative comparison ![[스크린샷 2023-06-17 오후 3.40.41.png]]
- Qualitative Comparisons![[스크린샷 2023-06-17 오후 3.41.28.png]]
- Local refinement network ablation![[스크린샷 2023-06-17 오후 3.44.16.png]]
## Conclusion from 🦖
- Atlas, unified representation from video, 를 사용하여 long term & short term global temporal consistency를 잡아주고 오류가 있는 부분은 filter network, local refine은 local refine network로 잡아주는 논문.
- filter network가 효과가 있는지는 ablation이 부족함.
- Atlas를 뽑아내는 과정이 모호하게 이해됨. Base가 되는 논문[Atlas 소개 논문](https://arxiv.org/pdf/2109.11418.pdf)을 읽어보면 좋을 거 같다.