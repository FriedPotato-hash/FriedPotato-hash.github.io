
---
title:  "Panoptic FPN"
 
date: 2023-11-24 
last_modified_at: 2023-11-24

---
# 📣 At a Glance

<aside> 🫒 semantic segmentation + instance segmentation = _panoptic segmentation_

</aside>
![](../source/스크린샷%202023-11-24%20오후%209.15.08.png)
- Semantic segmentation involves detecting objects within an image and **grouping them based on defined categories**. As stuff is amorphous and uncountable, this task is defined as simply assigning a class label to each pixel in an image.
    - 이미지 상의 모든 픽셀에 대해 클래스 카테고리를 부여
    - 중첩은 분할하지 않고 인식
    - U-NET을 이용한 Auto Encoder 방식 주로 사용.
- Instance segmentation takes semantic segmentation one step further and **involves detecting objects within defined categories**.
    - Object detection과 같은 물체의 인식을 pixel level에서 수행하는 task
    - 중첩의 경우 분할하여 인식
    - 겹쳐진 물체를 각각 검출하는 점과 하늘이나 도로 등 정해진 형태가 없는 물체의 경우는 클래스 라벨을 부여하지 않는 점
    - Mask R-CNN이 주요 모델 예이며, detection 기법을 사용하며 instance 영역을 취득 후, 각각의 영역에 대해 mask를 예측한다.
- Panoptic segmentation semantically **distinguishes different objects** as well as **identifies separate instances of each kind of object**. The task format we adopt for panoptic segmentation is simple: each pixel of an image must be assigned a semantic label and an instance id. Pixels with the same label and id belong to the same object; for stuff labels the instance id is ignored.
    - semantic segmentation과 instance segmentation을 결합한 방식이다.
    - 이미지 안의 모든 화소에 대해 클래스 라벨을 예측하고 임의의 ID를 부여한다.
    - Countable class(eg. car, human)을 Thing class → Instance Segmentation
    - Uncountable class(eg. sky, roads)은 stuff class → Semantic Segmentation
    - Stuff classifiers are usually built on fully convolutional nets with dilations while object detectors often use ROI proposals and are region-based.

![](../source/스크린샷%202023-11-24%20오후%209.14.55.png)
# 🛹 Motivation

- **Panoptic segmentation** which **unifies the tasks of instance segmentation (for thing classes)** and **semantic segmentation (for stuff classes).**
- Current SOTA methods for this joint task use separate and similar networks for instance and semantic segmentation, without performing any shared computation.
- In this work, aiming to **unify these methods at the architectural level**, designing a single network for both tasks.

> Mask R-CNN (instance segmentation method) + Feature Pyramid Network backbone (semantic segmantation branch)

---

# 🎱 Key Components

**For semantic segmentation,** FCNs with specialized backbones enhanced by dilated convolutions dominate popular leaderboards.

**For instance segmentation,** the region-based Mask R-CNN [24] with a Feature Pyramid Network (FPN) [36] backbone has been used as a foundation for all top entries in recent recognition challenges.

We show a simple, flexible, and effective architecture that can match accuracy *_for both tasks using _a single network__ that simultaneously **generates region-based outputs** (for instance segmentation) and **dense-pixel outputs** (for semantic segmentation)*. FPN was designed for instance segmentation, and it serves as the default backbone for Mask R-CNN. We show that _without changes, **FPN can also be highly effective for semantic segmentation**._

**⇒ 즉, strong instance segmentation model인 Mask R-CNN + FPN 모델을 기반으로 모델과 학습 방법에 약간의 변형을 줘서 semantic segmentation 까지 수행할 수 있도록 했다는 점. 이렇게 하면 memory efficient하게 기존의 SOTA를 각각의 task에서 달성할 수 있을 뿐 아니라, panoptic segmentation task에서도 SOTA 달성 가능.**

## Architecture

<aside> 🫒 Panoptic FPN is an **intuitive extension of Mask R- CNN** with **FPN**

</aside>

- approach starts with the **FPN backbone** popular for instance-level recognition and **adds a branch for performing semantic segmentation** in parallel with the existing region-based branch for instance segmentation.
- We make **no changes to the FPN backbone** when adding the dense-prediction branch, making it compatible with existing instance segmentation methods. Our method, which we call _Panoptic FPN_ for its ability to generate both instance and semantic segmentations via FPN, is easy to implement given the Mask R-CNN framework.

### Feature Pyramid Network

FPN takes a standard network with features at multiple spatial resolutions (e.g., ResNet), and adds a light top-down pathway with lateral connections.
![](../source/스크린샷%202023-11-24%20오후%209.14.42.png)
### Instance segmentation branch

Faster R-CNN performs region of interest (RoI) pooling on different pyramid levels and applies a shared network branch to predict a refined box and class label for each region.

To output instance segmentations, we use Mask R-CNN, which extends Faster R-CNN by adding an FCN branch to predict a binary segmentation mask for each candidate region.

### Panoptic FPN

Approach is to **modify Mask R-CNN with FPN to enable pixel-wise semantic segmentation prediction**. Although FPN was designed for object detection, these requirements – high-resolution, rich, multi-scale features – identify exactly the characteristics of FPN.

We thus propose to attach to FPN a simple and fast semantic segmentation branch, described next.
a simple design to merge the information from all levels of the FPN pyramid into a single output.
![](../source/스크린샷%202023-11-24%20오후%209.14.18.png)
a simple design to merge the information from all levels of the FPN pyramid into a single output.

## Training

### instance segmentation

$L_c$ (classification loss), $L_b$ (bounding-box loss), and $L_m$ (mask loss)

### semantic segmentation

$L_s$ is computed as a per-pixel cross entropy loss between the predicted and the GT labels.

# ⌛️ Key takeaways / Experiment insights / Limitations

**`dataset` COCO & Cityscapes**

**`Metric`**

- **Instance segmentation** - AP (average precision averaged over categories and IoU thresholds)
- **Semantic segmentation** - mIoU (mean intersection-over-Union)
- **Panoptic segmentation** - PQ (panoptic quality), PQ_St and PQ_Th to report stuff and thing performance separately.

## 🎏 Key takeaways

- The method starts with Mask R-CNN with FPN and adds to it a lightweight semantic segmentation branch for dense-pixel prediction.
- 3 tasks are all available (instance, semantic, panoptic segmentation) with seperate training process.
- For instance segmentation, this is expected as our method in this case is equivalent to Mask R-CNN. For semantic segmentation, our simple dense-prediction branch attached to FPN yields accuracy on par with the latest dilation-based methods, such as the recent DeepLabV3+.
- For panoptic segmentation [30], we demonstrate that with proper training, _using a single FPN for solving both tasks simultaneously yields accuracy equivalent to training two separate FPNs_, with roughly half the compute.
- **With the _same_ compute, a joint network for the two tasks outperforms two independent networks by a healthy margin.**

## 🪬 Qualitative Results

![](../source/스크린샷%202023-11-24%20오후%209.13.59.png)
## 📊 Quantative Results

![](../source/스크린샷%202023-11-24%20오후%209.13.48.png)
**`Panoptic FPN results`**

- **Main results** : 3a, 3b
    
    In Table 3a we compare two networks trained separately to Panoptic FPN with a single backbone. _Panoptic FPN yields comparable accuracy but with roughly half the compute_ (the backbone dominates compute, so the reduction is almost 50%). We also balance computational budgets by comparing two separate networks with ResNet-50 backbones each and Panoptic FPN with ResNet-101, see Table 3b. _Using roughly equal computational budget, Panoptic FPN significantly outperforms two separate networks_. Taken together, these results demonstrate that the joint approach is strictly beneficial, and that our Panoptic FPN can serve as a solid baseline for the joint task.
    
- **Ablation results** : 3c, 3d
    
    We perform additional ablations on Panoptic FPN with ResNet-50. First, by default, we combine the instance and semantic losses together during each gradi- ent update. A different strategy is to alternate the losses on each iteration (this may be useful as different augmentation strategies can be used for the two tasks). We compare these two options in Table 3c; the combined loss demonstrates better performance.
    
    Next, in Table 3d we compare with an architecture where FPN channels are grouped into two sets, and each task uses one of the two features sets as its input. While the results are mixed, we expect more sophisticated multi-task approaches could give stronger gains.
    
![|500](../source/Pasted%20image%2020231124211336.png))

`**Comparisons of ResNet-101 Panoptic FPN to the SOTA**`

- **Comparisons Results** : 4a, 4b
- ![|600](../source/Pasted%20image%2020231124211318.png)