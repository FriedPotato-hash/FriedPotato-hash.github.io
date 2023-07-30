
### Conformer-based hybrid ASR system for Switchboard dataset 
ICASSP2022
**Contribution**
Conformer가 end2end automatic ASR SOTA인데, hybrid ASR에 관련해서는 사용된 바 없음. 여러 구조에서 실험해보면서 word-error-rate를 줄이고 training speed를 향상시켰다.
- time downsampling methods for efficient training
- transposed convolutions to upsample the out sequence
**Conformer: Convolution-augmented Transformer for Speech Recognition**
Transformer models are good at capturing content-based global interactions, while CNNs exploit local features effectively.

```
Intermediate Loss – training stability 

Parameter sharing – reducing model size

Long Skip– reusing learning features

Focal Loss – making model to focus to the misclassified targets

Spec Augment – masking out blocks of frequency channels and blocks of time steps

Sequence Discriminative Training – reducing mismatch between training and recognition
```

red block = long skip
yellow block = focal loss
![[스크린샷 2023-06-11 오후 12.33.09.png]]
![[스크린샷 2023-06-08 오후 12.56.28.png]]
![[스크린샷 2023-06-08 오후 12.57.29.png]]
**result**
- Improved the WER
- Improved he training speed
- Improved memory efficiency


### Squeezeformer: An efficient transformer for automatic speech recognition
Conformer 구조에서 convolution을 사용하는 방식이 optimal이 아님을 이야기하며 구조적 변화를 실험함.
> Conformer 단점:
> 1) quadratic complexity of the attention mechanism limiting its efficiency on long sequence lengths. This problem is further highlighted by the long sequence lengths of typical audio inputs as also pointed out in [46]. 
> 2) the Conformer architecture is relatively more complicated than Transformer architectures used in other domains. For instance, the Conformer architecture incorporates multiple different normalization schemes and activation functions, the Macaron structure [34], as well as back-to-back multi-head attention (MHA) and convolution modules. This level of complexity makes it difficult to efficiently deploy the model on dedicated hardware platforms for inference [26, 39, 55]. More importantly, this raises the question of whether such design choices are necessary and optimal for achieving good performance in ASR tasks.

![[스크린샷 2023-06-08 오후 1.20.17.png]]
Conformer Block = FMCF 
Squeezeformer = MF / CF
**Macro**
- temporal U-Net structure를 사용해서, long sequences에 대한 multi-head attention의 cost 절감 -> high temporal redunancy를 temporal U-Net 구조를 사용함으로써 해결함.
	- Temporal U-Net을 사용하는 이유가 뭘까여? Conformer의 block이 upper (1->16)으로 갈수록 vector는 더 유사해짐. 그렇다면 왜 2N개 씩이나 conformer block을 유지해야 하는가? 이를 N개로 블록 개수를 아예 줄어버린 것.
- Conformer의 macaron structure 대신 multi-head attention 또는 convolution modules에 대해 더 simple한 block structure를 제안. ![[스크린샷 2023-06-11 오후 2.50.03.png]]
**Micro**
- convolution block에서 activations을 simplify.
- redundant Layer Normalization operation을 제거
- efficient한 depth wise downsampling layer를 사용해서 input signal을 sub-sample함. (Conformer의 convolution subsampling -> Depth wise separable convolution subsampling)![[스크린샷 2023-06-11 오후 2.53.13.png]]

### Contrastive siamese network for semi-supervised speech recognition
ICASSP 2022
**Overview**
- Input masking하고 Output이랑 Contrastive loss로 matching
- Stop gradient operation on the target branch
- Augmented branch에서 learnable transformation 해줌
- Shortcut learning problem을 방지하기 위해 temporal augment function 해줌.![[Pasted image 20230611231648.png|500]]
- Reconstruction-based Masked Modeling
	- BERT가 대표적이고, Wav2vec2.0이랑 TERA가 있다. 그러나 input-level 혹은 intermediate level의 representation을 target하는데 그친다는 한계가 있음.
	- input-level의 feature를 recon하는 것은 noise와 같이 unnecessary한 detail까지 학습하게 한다는 단점이 있다. (TERA) WAV2VEC은 이 문제를 해결하기 위해 vector quantization을 진행했지만, supervised tasks에 적용하는 경우 성능이 그렇게 잘 나오지는 않음.
- Output-based Self supervised learning
	- input/intermediate representation을 타겟으로 학습하는 것이 위와 같은 한계가 있으므로, target branch (loss 계산에 사용되는 target을 제공하는 역할)를 사용한다.![[Pasted image 20230611234008.png]]
	- Siamese Network(SimCLR) vs. Momentum Networks (MoCo)
		- momentum network는 main encoder로 부터 moving average를 통해 target branch를 구축한다. 이렇게 할때 stable한 training이 가능하지만 main encoder의 copy가 하나 더 생기는 만큼 parameter의 개수가 두배가 된다.
		- Siamese network는 이름값을 하듯이 main encoder 두개가 같은 weights를 공유한다. 그래서 더 적은 모델 파라미터 수를 갖지만, collapsing problem으로 인해 학습이 어렵다. 진짜 어렵다.
- **Collapsing Problem&Solutions**
	- *Collapsing Problem*이 무엇이고 하니, siamese network가 similarity learning을 하기에 발생하는 문제이다. Network로 하여금 서로 다른 이미지를 가까이 mapping할 수 있도록 하는데, 이 때 network는 same input으로 모든 것을 mapping 해버리는 cheating을 하게 된다.
	- *Collapsing Solution* 
		- Contrastive Leaning (eg. SimCLR)을 사용하거나
		- Asymmetry Network를 사용하는 방법이 있다. collapsing이 발생하는 이유 중 하나가 두개의 main encoder가 같은 weights를 sharing하고 있기 떄문에 발생하는데 main encoder를 그래서 서로 asymmetric하게 해두는 것. (eg. BYOL, MoCo)
		- Stop-Gradient Operation 방법을 써줄수도 있음. 이는 근데 assymetric과 비슷한 느낌이긴 한듯![[스크린샷 2023-06-11 오후 11.54.29.png]]
		- Temporal Augment Function
			- target이랑 augmented branch의 feature가 matching 되도록 학습함. ![[Pasted image 20230611235644.png|300]]
			  - Transformer를 사용하는 것도 이 논문이 제안하는 바인데, Positional Embedding 할 때 Contrastive loss를 최소화하는 방향으로 학습시키면 position embedding으로 collapsing한다. 이에 temporal augmentation을 적용해주는것. 이는 uniform, non-uniform한 technique으로 분류된다.![[Pasted image 20230611235830.png|300]]
			    - Uniform TempoAug
			    - Non-Uniform TempoAug
		- Time-aligned Contrastive Loss
			- Temporal augmentation으로 positive paris가 different timestep으로부터 오게 함.![[스크린샷 2023-06-12 오전 2.01.14.png]]
- **Experiments**
	- Metrics "Number of errors divided by the total words"![[Pasted image 20230612020351.png]]

### A complementary joint training approach using unpaired speech and text for low-resource automatic speech recognition
Interspeech 2022
**Paper Overview**
- Data Preparation & Generation
	- Unpaired speech-only, text-only 각각 준비
	- Paired speech-text data 준비
	- unpaired data에서 missing part를 generation해서 paired data로 만들어주기 (ASR, TTS 사용해서!)
- Basic CJT (1st round training) - Complementary Joint training이라는 뜻!
	- two paired generated dataset에 대해 모델을 alternative하게 학습
	- x-speech, y-text, star-pseudo ![[Pasted image 20230611212939.png]]
- CJT++ (2nd round training)
	- 생성된 pseudo label (texts) Masking 
		- Context modeling이 향상됨
	- 생성된 speech data에 대해서는 random하게 layer에 대해 gradient restriction
	- Model re-training
	- 생성된 pseudo label에 너무 tuning되면 오히려 ASR, TTS의 성능이 upper bound가 되어버린다. 따라서 pseudo label들을 가리거나 pseudo label에 대해 발생하는 gradient을 흘리지 않거나 해서 문제를 해결![[Pasted image 20230611213240.png|500]]
**Experiment**
- ![[스크린샷 2023-06-11 오후 9.37.40.png]]

### FluentTTS: Text-dependent fine-grained style control for multi-style TTS
Interspeech 2022
**Contributions**
- Proposed a fine-grained style control method for multi-style TTS.
- Successfully generated phoneme-level F0 values using an internal aligner, enabling fine-grained prosody control in our multi-style TTS model without external modules.
**TTS - introduction**
Neural TTS Model
	- Predict acoustic feature(mel-spectrograms) from input phonetic features
	- Acoustic features are used to synthesize speech wave-forms
Multi-style TTS Model
	- Model in speech synthesis that allows the generated speech to incorporate different styles
	- Styles : various speaker identities, emotions, accents, and prosodic patterns
**One-To-Many Mapping Problem**
- input text는 하나인데, style options이 굉장히 많음. 즉 input text 하나에 의해 결정되어야 하는 요소가 여러개라는 의미
- How to solve: Global Style Embedding (target speaker로부터 identity, prosodic information을 뽑아낸 embedding)을 사용한다.
**Global Style Embedding**
- Obtained by compressing frame-level embeddings along the time axis
- word-level의 local feature를 표현하진 못함.
- fine scale에서 prosody를 조정하는데 직접 쓰는데에는 적절하지 않음.
**FFT가 제안하는 것**
- Use fundamental frequency (F0) for representing local style
- Estimate multi-style embeddings using a multi-style encoder
- Control prosody styles at the word-level using multi-style embeddings
- Enhance the naturalness and expressiveness of synthesized speech ![[스크린샷 2023-06-11 오후 3.01.16.png]]
- Mel & Spk ID에서 global style embedding을 뽑고 여기에 text embedding을 같이 고려해서 F0 embedding을 뽑는다.![[스크린샷 2023-06-11 오후 3.02.40.png|500]]

**Training**
- Internal Aligner 
- F0 values: fundamental frequency를 의미. A_hard란 text-symbols과 mel-spectogram frames 간의 one-to-one correspondence를 의미한다.
- F0 predictor는 text embedding과 global style embedding을 사용해서 normalized된 F0 값을 적절하게 생성해낸다. Conv1d layer를 통해 F0 values를 F0 embedding으로 인코딩해줌.
- Multi-style encoder는 text dependent한 local prosodic information을 통해 global style embedding의 부족한 디테일을 보충해줌.
- (각 모듈의 input output 신경써서 보기)![[스크린샷 2023-06-11 오후 5.17.28.png]]![[스크린샷 2023-06-11 오후 3.14.45.png]]
- training loss
![[스크린샷 2023-06-11 오후 3.08.45.png]]
**Conclusion**
- Both of the word-level and phoneme-level F0 control is available.
- local prosodic variation을 조정하는데 능함

### Adversarial multi-task learning for disentangling timbre and pitch in singing voice synthesis
Interspeech 2022
- **Overview**
	- Singing voice synthesis(SVS)를 위한 input representation 제안
	- source filter 기반의 SVS 방법론 제안
	- *Traditional TTS vs. SVS?*
		- TTS는 다량의 script-audio paired data로 학습된 모델이 text를 입력으로 받을 때 자연스러운 음성을 출력함.
		- SVS는 구체적으로 불러주면 하는 곡(eg. 김광석, 서른즈음에)이 있고, 곡의 가사에 따른 음정(`pitch`), 박자(`duration`)를 맞춰줘야 함.
	- 어떻게 pitch(음정)과 duration(박자)를 표현할까? -> `F0 Contour`.
		- F0 (fundamental frequency)를 구하는 것은 주어진 음성을 분석해서 찾아주는 것. 불러지지 않은 곡에 대해서는 F0을 구할 수 없겟죠
		- 각 음절을 (syllable) midi로 표현해주는 방법. 이는 F0 contour에 비해 discrete한 representation이기에 학습 안정성이 더 크고, controllability가 향상됨.
	- 그럼 결국 input을 어떻게 만들어 줄거냐는 질문. 
		- text에서 phoneme을 뽑아주고, 
		  MIDI에서 duration이랑 pitch를 뽑아주고,
		  wave에서 mel input을 뽑아준다.![[스크린샷 2023-06-12 오전 2.24.43.png]]
	- 결국 SVS는 기반 TTS 모델에 pitch information을 추가로 활용하여 개발된다. 해당 논문에서는 DCTTS라는 TTS 모델 사용![[스크린샷 2023-06-12 오전 2.27.04.png]]
- **Architecture**
	- PEM Decoder, Pitch Encoder를 제하고는 baseline TTS 모델과 거의 유사함.![[스크린샷 2023-06-12 오전 8.09.33.png]]
	- text input, mel input을 각각 encoder에서 처리
	- Mel vector를 query, text vector를 key, value로 하여 attention 연산을 수행하고, TTS에서는 align 맞추는게 중요하지만 SVS에서는 text-alinged가 이미 되어있으므로 manually aligned된 text를 더 자연스럽게 맞춰주는 역할만 한다고 보면됨.
	- Mel decoder에서 다음 frame의 mel spectogram generation. 이때 pitch embedding이 condition으로 들어가서 mel-aligned pitch 정보를 반영시킴. 이 때 발음이 흐릿하다는 문제가 있었음.
	- mel-aligned text가 더 있으므로, 이를 condition으로 하여 발음을 강조해줌. Text Encoder에서 embedding을 뽑고 P.E.M.(phonetic enhancement mask)에서 masking 처리를 해서 text-conditioned mask를 이전에 생성한 mel-spectogram과 곱해준다.
- **Conclusion**
	- 해당 과정은 실제 사람 발화과정인 `source-Filter`과 매우 유사하다. 
		- 인간: vocal cord(성대) - source, vocal tract (구강구조) - filter
		- 모델: Pitch+text+Mel - source, Text only conditioned phonetic mask - filter
	- 정리하자면,
		- Midi 기반으로 text, pitch input을 modeling함.
		- adversarial loss를 추가하여 자연스러움 향상
		- Text conditioned formant mask -> source filter 모델에서 filter 역할

### Self supervised learning for robust voice cloning
Interspeech 2022
- **Overview**
	- voice cloning: 주어진 음성 데이터의 특징 학습해서 speaker 목소리 재현하는 태스크
	- Input representation 추출: BYOL-A + some augmentations extended
	- mel spectogram generator: NAT
	- vocoder: LPCNet
	- Overall Architecture![[Pasted image 20230612023510.png]]
- **Augmentations**
	- BYOL-A![[Pasted image 20230612023644.png]]
	- This paper![[Pasted image 20230612023717.png]]
- **NAT**
	- NAT를 사용하는 이유; attnetion을 활용한 auto regressive seq2seq model은 명시적인 duration prediction이 불필요하나, 이러한 접근은 하나의 프레임만 잘못 예측해도 조기에 종료되거나 decoder와 encoder의 attention이 순서대로 걸리지 않는 문제가 발생함. 각 input tokeㅜ의 duration을 명시적으로 예측하여 조기 종료, 잘못 attention의 순서가 걸리는 경우를 방지할 수 있음.

### A unified system for voice cloning and voice conversion through diffusion probabilistic modeling
Interspeech 2022
**Task 설명**
- Voice Cloning: TTS로 text->speech 만들고 speaker의 identity를 따와서 입혀주는 방식. speaker에 따른 fine tuning이 일반적임
- Voice Conversion: A 화자의 음성을 B 화자의 음성으로 변환하는 기술. 
- Voice cloning은 학습 데이터를 Audio->text 추출해서 audio-text pair들을 만들어야 한다. 그러나 voice conversion의 경우 입력화자의 음성 특징을 추출하여 다른 화자의 음성에 적용하는 것이므로 음성 데이터만을 통해 정보 추출이 가능하여 텍스트 정보의 제약을 상대적으로 덜 받음.
**Intro**
- Voice Cloning + Voice Conversion을 위한 multi-modal system을 제안했다.
- Mel encoder, Text encoder, 이 둘에서 나온 embedding들을 모두 받는 Decoder 각각 훈련. 
- 디퓨전 기반 학습 방법 사용. Average Mel (X bar)에서 원본과 유사한 (X_pred)를 만들어 나가는 과정으로 denosing 함.![[스크린샷 2023-06-11 오후 5.29.28.png]]
![[스크린샷 2023-06-11 오후 5.30.09.png|300]]
**Training**
결국 source voice를 reconstruction 하는 mel-spectogram를 생성해내는게 목적임. Noise 뿌려져서 들어가는 input은 X_t voice이고, 여기에 concat해서 들어가는 애가 source voice를 averaged voice화 한 mel-spectogram이다. 이는 결국 soice voice의 `content` (말 내용)에 source voice의 `목소리`를 입혀 recon하는 식으로 학습하기 때문.
정리하자면, `noising process`는 X_0 (source voice)에서 X_bar (averaged voice)로 가는 방향을 nosing이라고 한다. `denoising process`는 gradient를 구하는건데 X_t(t step noised input), t(timestep), g_t(Y) (speaker identity extracted from Speak encodeer), X_bar(averaged voice)(content)를 concat해서 Unet에 넣어주고 여기서 X_pred (source voice)로 가는 방향으로 denoising gradient를 구한다. 
MSE Loss는 gradient끼리.
- Mel Encoder
	- MSE loss로 학습. Mel 스펙토그램의 출력 결과와 평균 mel spectogram 차이를 최소화. 
	- 입력 mel에 있는 각 음소에 대응하는 특성을 전체 데이터셋에서 해당 음소에 대한 평균값으로 대체함. 동일 음소와 관련된 프레임들은 유사한 특성을 공유하게 된다. 음소의 특성을 보다 general한 표현형으로 만들 수 있음.![[스크린샷 2023-06-11 오후 5.50.35.png]]
- Text encoder
	- Non attentive tecotron에서 encoder부분만 사용함.
	- NAT는 기존의 Tacotron (text input, mel spectogram output)이 주요한 text에 대해 attention 처리를 한 후 mel spectogram을 생성하는 것과는 달리, attention을 가하지 않고 순차적으로 들어오는 text에 대해 mel spectogram을 생성함. 
	- 정확하게는 text data -> 음소를 추출하여 입력으로 사용
- Decoder
	- Decoder는 Unet 기반 score-matching network. averaged voice화된 mel spectogram으로 부터 나온 feature를 target mel-spectogram으로 변환함.
	- loss를 주는게 좀 신기하긴 하다. Source Voice에 noise를 뿌리고 averaged voice 방향으로의 gradient를 줘서 noise를 추가해준다. ![[스크린샷 2023-06-11 오후 5.32.59.png]]
**Conclusion**
- Diffusion model을 사용하여 projection되지 않은 데이터에 대해 학습 가능
- 학습 데이터 구축이 까다롭지 않다.

### NVC-Net: End-to-end adversarial voice conversion
ICASSP 2022
- **Contribution**
	- 기존의 Voice conversion 웍들의 문제점. 1) parallel training data 모아야 됨. 2) raw audio wavform을 lower dimension으로 보내줘야 됨. 3) Vocoder가 필요함 -> feature mismatch problem이 발생 4) unseen speaker에 대해 generalize 불가
	- raw audio waveform에서 바로 voice conversion
	- no vocoder
	- zero-shot voice conversion problem 해결
- **Architecture**
	- Content Encoder, Speaker Encoder, Generator, Discriminator로 구성됨![[Pasted image 20230612020724.png]]
	- Speaker encoder에서 `average pooling`은 temporal dimension을 제거해서 two dense layer로 넘겨준다. (mean, variance를 각각 출력함)
	- Generator는 content embedding을 input으로 받고, speaker embedding을 condition으로 받는다. output은 raw audio. 
	- Discriminator는 여러 resolution의 audio 를 받아서 각각에 대해 binary classification 해 줌 (real/fake 여부)
	- downsampling은 strided conv, upsampling은 transposed conv.
- **Training Objective**
	- Final loss 2개!![[Pasted image 20230612021208.png]]
	- Recon loss specific![[Pasted image 20230612021319.png]]![[Pasted image 20230612021406.png]]
	- Content preservation loss specific![[Pasted image 20230612021450.png]]![[Pasted image 20230612021532.png]]
	- adversarial loss specific![[Pasted image 20230612021233.png]]
- **Conclusion**
	- Adversarial Neural Network (end2end)를 voice conversion 분야에 적용
	- adversarial feedback과 recon loss 등 loss를 잘 설계해서 good internal representation을 뽑아낼 수 있었음.
	- speak identity를 linguistic content로 부터 disentangle해내고 zero-shot에서도 사용 가능한 모델

### Robust disentangled variational speech representation learning for zero-shot voice conversion
ICASSP 202
**Task**
- Disentangled 
	- speech = Speaker + Content
- Speech Representation Learning
	- High dimension에서는 모두 entangle되어 있으나, 이를 Low Dimension으로 전사하면 여자/남자 정도로는 구분 가능. 즉 낮은 디멘션으로 representation을 보낼 수록 speaker에 대한 disentangle은 잘된다는 의미인듯? 
	- Highdim=X space / Lowdim=Z space![[스크린샷 2023-06-11 오후 6.13.45.png|300]]
- Zero-shot voice conversion
	- 처음보는 speech 두개에 대해(zero shot) 서로의 speaker와 content를 섞는 것 (voice conversion).![[스크린샷 2023-06-11 오후 6.15.12.png]]
**Intro**
- KL divergence![[스크린샷 2023-06-11 오후 6.16.24.png]]
- Mutual Information![[스크린샷 2023-06-11 오후 6.16.48.png]]
- VAE
	- X - data dimension, Z - pre determined dimension(gaussian)
	- X -> Z : `Encoder`, Z -> X: `Decoder`
	- p(z): 사전 확률, p(z|x): posterior prob / Posterior을 알 수 없기 때문에 Encoder q(z|x)를 bayesian해서 근사화한다.
**Contribution**
- VAE loss 확장해서 disentangle (speaker/content 분리)를 잘 해낼 수 있다.
- Noise가 있어도 clean하게 만들어 낼 수 있다.
**Training**
- 엄청 간단함. ![[스크린샷 2023-06-11 오후 6.56.36.png]]![[스크린샷 2023-06-11 오후 6.58.13.png]]![[스크린샷 2023-06-11 오후 6.58.26.png]]
- VAE loss![[스크린샷 2023-06-11 오후 6.59.02.png]]
	- Recon loss: X recon잘 하도록
	- KL div1: Speaker Zs에 대한 prior와 Encoder가 예측한 probability가 유사하도록
	- KL div2: Content Zc에 대한 prior와 Encoder가 예측한 probability가 유사하도록
		- Content Zc 또한 LSTM의 결과라고 볼 수 있음. 
	- Mutual Information을 사용해서 VAE loss를 extending함. ![[스크린샷 2023-06-11 오후 7.02.16.png]]
- VAE loss -> MI loss![[스크린샷 2023-06-11 오후 7.05.50.png]]
	- KL divergence 두 항을 MI 항으로 바꿔줌
- red block 예시는 (8)에 대한 예시![[스크린샷 2023-06-11 오후 7.07.59.png]]
- Z_s는 time invariant, Z_c는 time variant한 정보를 Encoding한다.
**Experiment**
- eval metric 한번 확인하고 들어가기![[스크린샷 2023-06-11 오후 8.54.18.png]]
- ![[스크린샷 2023-06-11 오후 8.59.38.png]]