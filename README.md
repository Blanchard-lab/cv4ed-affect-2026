# Evaluating Web-trained Facial Expression Recognition Models in Collaborative Problem-Solving

![teaser image](assets/teaser_new.png)

This repository contains the code used in our study evaluating pretrained facial expression recognition (FER) models in collaborative problem-solving settings. The goal of the study is to examine whether common FER outputs—categorical basic emotions and dimensional valence–arousal representations—align with epistemic affective states such as curiosity, confusion, and frustration observed in collaborative problem-solving. The study will be published in the proceedings of the IEEE/CVF Computer Society Conference on Computer Vision and Pattern Recognition Workshops (CVPRW) 2026.

The repository provides tools for dataset preprocessing, running inference using multiple FER models, and performing analyses including cross-taxonomy alignment, dimensional affect structure, and cross-model agreement.

## Overview

Most FER models are trained on large web-scale datasets such as AffectNet. These datasets are annotated using basic emotion categories (e.g., happy, sad, anger) and sometimes dimensional representations (valence and arousal).

However, affective states relevant to collaborative problem-solving are often **epistemic emotions** -- which means they are learning relevant, including (but not limited to):

- Curious
- Confused
- Frustrated
- Optimistic
- Disengaged
- Surprised
- Conflicted

Here, we evaluates whether pretrained FER models produce meaningful signals for these states when applied to collaborative learning data.

The experimental workflow includes:

1. Extracting and sampling face crops around affect reports
2. Running pretrained FER models
3. Aggregating predictions per affect instance
4. Evaluating alignment between model outputs and epistemic labels
5. Comparing agreement across different FER architectures
6. Comparing behavior on educational data vs AffectNet benchmark data

## Pipeline

### 1. Frame Sampling

For each affect report at time \(t\), we collect all cropped face images within a ±5 second window:

From this pool we randomly sample **K = 10 frames** to represent the instance.

This creates a dataset of labeled face crops used for model inference.

### 2. Model Inference

We evaluate several pretrained FER models.

#### Categorical emotion models

- OpenFace 3.0
- LibreFace
- POSTER++
- DDAMFN
- EmotiEffLib
- Qwen-2.5-VL (Foundational VLM Baseline)
  
Each frame produces a categorical emotion prediction.

Instance-level prediction is obtained via:

- **OpenFace 3.0:** mean probability vector
- **Other models:** majority vote across sampled frames
  
#### Dimensional models

- EmotiEffLib

Instance-level predictions are computed by averaging across frames.

### 3. Evaluation

We perform three main analyses.

#### Cross-taxonomy alignment

Compare predicted basic emotions with epistemic labels using confusion matrices.

Goal: determine whether epistemic states map to consistent emotion predictions.

#### Dimensional affect structure

Visualize valence–arousal outputs:

- scatter plots
- box plots
- clustering behavior

Goal: determine whether epistemic states occupy distinct regions in affect space.

#### Cross-model agreement

Compare predictions between different FER models.

Example:
OpenFace vs LibreFace


Low agreement indicates instability under domain shift.

### 4. AffectNet Control Experiment

To isolate domain shift effects, we run the same models on a subset of AffectNet.

Procedure:

1. Randomly sample N images from AffectNet
2. Run OpenFace and LibreFace
3. Compute cross-model confusion matrix


## Setup

We follow a few steps to setup our codebase for reproducing these experiments.

### Environments

Since we're using different systems with unique package requirements, we've set up multiple virtual environments using conda.

To create and activate a particular conda environment `[environment_name]`, go into `environments/` and run 

```conda env create [environment_name].yml```

```conda activate [environment_name]```

Here are the environments we are currently using:

1) `mist` - for tabular preprocessing, analysis and plotting
2) `openface3` - for running scripts that use OpenFace 3
3) `libreface` - for running scripts that use LibreFace
4) `poster2` - for running scripts that use POSTER++ and DDAMFN (no package conflicts between the two)
5) `hsemotion` - for running scripts that use EmotiEffLib

If you are getting package dependency errors, most likely you are not using the right environment.

### Data
We use two datasets for our experiments: Epistemic Emotions in Collaborative Problem Solving: Weights Task (EECPS-WT) and AffectNet+. EECPS-WT (our contribution) and AffectNet+ are both available to academic researchers only and not for commercial use. 

We will share information about how to access our data to the public after the double-blind review process. Once access is granted, move the dataset directory to the root of the repository and rename it `data`. For AffectNet+, access information is provided here: https://www.mohammadmahoor.com/pages/databases/affectnet/


### Models
We did not use CAGE as we were unable to retrieve the pretrained weights from their github. For the models we have used and reported in the paper, here are the instructions to retrieve the code and weights:

1) OpenFace 3.0 -- inside models/, clone `https://github.com/CMU-MultiComp-Lab/OpenFace-3.0.git`, and follow the instructions to retrieve model weights from their readme. You will also have to clone PyTorch_Retinaface and STAR since they haven't added them as submodules. PyTorch_Retinaface: `https://github.com/biubug6/Pytorch_Retinaface.git`, STAR: `https://github.com/ZhenglinZhou/STAR.git`
2) LibreFace -- inside models/, clone `https://github.com/ihp-lab/LibreFace.git`, that's all. Their pip release takes care of everything else.
3) POSTER++ -- inside models/, clone `https://github.com/Talented-Q/POSTER_V2.git`. Download the checkpoints and put them in their respective locations.
4) DDAMFN - inside models/, clone `https://github.com/SainingZhang/DDAMFN.git`. Checkpoints are available in the git repo.
5) EmotiEffLib - inside models/, clone `https://github.com/sb-ai-lab/EmotiEffLib.git`. Checkpoints are available in the git repo.

We would like to add CAGE in future. If you are a researcher who have been able to run CAGE locally, feel free to open an issue describing how you did that.

### Running

Use the scripts to run specific parts of our experimental pipeline. Some depend on outputs from previous scripts, so best to run them in order. Feel free to adjust the pipeline as you explore the codebase, in many cases the ordering was arbitrary and was based on the timeline of our experimental design decisions.



**To reproduce our experiments, run the scripts in order.**

To modify/extend, consult src/ and write additional scripts to apply your added functionality in src.   


# References

[1] A. Mollahosseini, B. Hasani, and M. H. Mahoor,
"Affectnet: A database for facial expression, valence, and arousal computing in the wild,"
*IEEE Transactions on Affective Computing*, vol. 10, no. 1, pp. 18–31, 2017.

---

[2] T. Baltrušaitis, P. Robinson, and L.-P. Morency,
"Openface: an open source facial behavior analysis toolkit,"
in *2016 IEEE Winter Conference on Applications of Computer Vision (WACV)*. IEEE, 2016, pp. 1–10.

---

[3] J. Hu, L. Mathur, P. P. Liang, and L.-P. Morency,
"Openface 3.0: A lightweight multitask system for comprehensive facial behavior analysis,"
in *2025 IEEE 19th International Conference on Automatic Face and Gesture Recognition (FG)*. IEEE, 2025, pp. 1–11.

---

[4] C. Fabian Benitez-Quiroz, R. Srinivasan, and A. M. Martinez,
"Emotionet: An accurate, real-time algorithm for the automatic annotation of a million facial expressions in the wild,"
in *Proceedings of the IEEE Conference on Computer Vision and Pattern Recognition*, 2016, pp. 5562–5570.

---

[5] J. Mao, R. Xu, X. Yin, Y. Chang, B. Nie, A. Huang, and Y. Wang,
"Poster++: A simpler and stronger facial expression recognition network,"
*Pattern Recognition*, vol. 157, p. 110951, 2025.

---

[6] K. Ahuja, D. Kim, F. Xhakaj, V. Varga, A. Xie, S. Zhang, J. E. Townsend, C. Harrison, A. Ogan, and Y. Agarwal,
"Edusense: Practical classroom sensing at scale,"
*Proceedings of the ACM on Interactive, Mobile, Wearable and Ubiquitous Technologies*, vol. 3, no. 3, pp. 1–26, 2019.

---

[7] P. Goldberg, Ö. Sümer, K. Stürmer, W. Wagner, R. Göllner, P. Gerjets, E. Kasneci, and U. Trautwein,
"Attentive or not? Toward a machine learning approach to assessing students' visible engagement in classroom instruction,"
*Educational Psychology Review*, vol. 33, no. 1, pp. 27–49, 2021.

---

[8] A. Joshi, D. Allessio, J. Magee, J. Whitehill, I. Arroyo, B. Woolf, S. Sclaroff, and M. Betke,
"Affect-driven learning outcomes prediction in intelligent tutoring systems,"
in *2019 14th IEEE International Conference on Automatic Face & Gesture Recognition (FG 2019)*. IEEE, 2019, pp. 1–5.

---

[9] X. Tang, Y. Gong, Y. Xiao, J. Xiong, and L. Bao,
"Facial expression recognition for probing students' emotional engagement in science learning,"
*Journal of Science Education and Technology*, vol. 34, no. 1, pp. 13–30, 2025.

---

[10] I. J. Goodfellow, D. Erhan, P. L. Carrier, A. Courville, M. Mirza, B. Hamner, W. Cukierski, Y. Tang, D. Thaler, D.-H. Lee et al.,
"Challenges in representation learning: A report on three machine learning contests,"
in *International Conference on Neural Information Processing*. Springer, 2013, pp. 117–124.

---

[11] A. P. Fard, M. M. Hosseini, T. D. Sweeny, and M. H. Mahoor,
"Affectnet+: A database for enhancing facial expression recognition with soft-labels,"
*IEEE Transactions on Affective Computing*, 2025.

---

[12] J. A. Russell,
"A circumplex model of affect,"
*Journal of Personality and Social Psychology*, vol. 39, no. 6, p. 1161, 1980.

---

[13] F. Zhou, S. Kong, C. C. Fowlkes, T. Chen, and B. Lei,
"Fine-grained facial expression analysis using dimensional emotion model,"
*Neurocomputing*, vol. 392, pp. 38–49, 2020.

---

[14] S. D'Mello, R. W. Picard, and A. Graesser,
"Toward an affect-sensitive autotutor,"
*IEEE Intelligent Systems*, vol. 22, no. 4, pp. 53–61, 2007.

---

[15] R. S. Baker, S. K. D'Mello, M. M. T. Rodrigo, and A. C. Graesser,
"Better to be frustrated than bored: The incidence, persistence, and impact of learners' cognitive–affective states during interactions with three different computer-based learning environments,"
*International Journal of Human-Computer Studies*, vol. 68, no. 4, pp. 223–241, 2010.

---

[16] E. B. Cloude, A. Munshi, J. A. Andres, J. Ocumpaugh, R. S. Baker, and G. Biswas,
"Exploring confusion and frustration as non-linear dynamical systems,"
in *Proceedings of the 14th Learning Analytics and Knowledge Conference*, 2024, pp. 241–252.

---

[17] R. A. Calvo and S. D'Mello,
"Affect detection: An interdisciplinary review of models, methods, and their applications,"
*IEEE Transactions on Affective Computing*, vol. 1, no. 1, pp. 18–37, 2010.

---

[18] D. M. Russell and M. Oren,
"Retrospective cued recall: a method for accurately recalling previous user behaviors,"
in *2009 42nd Hawaii International Conference on System Sciences*. IEEE, 2009, pp. 1–9.

---

[19] S. Anindho, V. Venkatesha, and N. Blanchard,
"A methodological framework for capturing cognitive-affective states in collaborative learning,"
*arXiv preprint arXiv:2507.01166*, 2025.

---

[20] S. Li, W. Deng, and J. Du,
"Reliable crowdsourcing and deep locality-preserving learning for expression recognition in the wild,"
in *Proceedings of the IEEE Conference on Computer Vision and Pattern Recognition*, 2017, pp. 2852–2861.

---

[21] S. Zhang, Y. Zhang, Y. Zhang, Y. Wang, and Z. Song,
"A dual-direction attention mixed feature network for facial expression recognition,"
*Electronics*, vol. 12, no. 17, p. 3595, 2023.

---

[22] D. Chang, Y. Yin, Z. Li, M. Tran, and M. Soleymani,
"Libreface: An open-source toolkit for deep facial expression analysis,"
in *Proceedings of the IEEE/CVF Winter Conference on Applications of Computer Vision*, 2024, pp. 8205–8215.

---

[23] G. A. Miller,
"Wordnet: a lexical database for English,"
*Communications of the ACM*, vol. 38, no. 11, pp. 39–41, 1995.

---

[24] M. K. Underwood,
"Peer social status and children's understanding of the expression and control of positive and negative emotions,"
*Merrill-Palmer Quarterly (1982-)*, pp. 610–634, 1997.

---

[25] L. Isenbarger and M. Zembylas,
"The emotional labour of caring in teaching,"
*Teaching and Teacher Education*, vol. 22, no. 1, pp. 120–134, 2006.

---

[26] P. Ekman,
"An argument for basic emotions,"
*Cognition & Emotion*, vol. 6, no. 3-4, pp. 169–200, 1992.

---

[27] M. P. Cross, A. M. Acevedo, and J. F. Hunter,
"A critique of automated approaches to code facial expressions: What do researchers need to know?"
*Affective Science*, vol. 4, no. 3, pp. 500–505, 2023.

---

[28] A. Horvers, N. Tombeng, T. Bosse, A. W. Lazonder, and I. Molenaar,
"Detecting emotions through electrodermal activity in learning contexts: A systematic review,"
*Sensors*, vol. 21, no. 23, p. 7869, 2021.

---

[29] J. W. Y. Chung, H. C. F. So, M. M. T. Choi, V. C. M. Yan, and T. K. S. Wong,
"Artificial intelligence in education: Using heart rate variability (HRV) as a biomarker to assess emotions objectively,"
*Computers and Education: Artificial Intelligence*, vol. 2, p. 100011, 2021.

---

[30] I. Khebour, R. Brutti, I. Dey, R. Dickler, K. Sikes, K. Lai, M. Bradford, B. Cates, P. Hansen, C. Jung et al.,
"When text and speech are not enough: A multimodal dataset of collaboration in a situated task,"
*Journal of Open Humanities Data*, vol. 10, 2024.

---

[31] S. Anindho, V. Venkatesha, M. Bradford, A. M. Cleary, and N. Blanchard,
"An exploration of internal states in collaborative problem solving,"
in *International Conference on Human-Computer Interaction*. Springer, 2025, pp. 135–150.

---

[32] J. Deng, J. Guo, E. Ververas, I. Kotsia, and S. Zafeiriou,
"Retinaface: Single-shot multi-level face localisation in the wild,"
in *Proceedings of the IEEE/CVF Conference on Computer Vision and Pattern Recognition*, 2020, pp. 5203–5212.

---

[33] A. Savchenko,
"Facial expression recognition with adaptive frame rate based on multiple testing correction,"
in *Proceedings of the 40th International Conference on Machine Learning (ICML)*, ser. Proceedings of Machine Learning Research, vol. 202. PMLR, 23–29 Jul 2023, pp. 30119–30129.
Available: <https://proceedings.mlr.press/v202/savchenko23a.html>

---

[34] P. Wang, S. Bai, S. Tan, S. Wang, Z. Fan, J. Bai, K. Chen, X. Liu, J. Wang, W. Ge et al.,
"Qwen2-vl: Enhancing vision-language model's perception of the world at any resolution,"
*arXiv preprint arXiv:2409.12191*, 2024.

---

[35] S. I. Serengil and A. Ozpinar,
"Hyperextended lightface: A facial attribute analysis framework,"
in *2021 International Conference on Engineering and Emerging Technologies (ICEET)*. IEEE, 2021, pp. 1–4.
Available: <https://ieeexplore.ieee.org/document/9659697>

---

[36] D. Kollias and S. Zafeiriou,
"Aff-wild2: Extending the aff-wild database for affect recognition,"
*arXiv preprint arXiv:1811.07770*, 2018.

---

[37] T. Karras, S. Laine, and T. Aila,
"A style-based generator architecture for generative adversarial networks,"
in *Proceedings of the IEEE/CVF Conference on Computer Vision and Pattern Recognition*, 2019, pp. 4401–4410.

---

[38] S. M. Mavadati, M. H. Mahoor, K. Bartlett, P. Trinh, and J. F. Cohn,
"Disfa: A spontaneous facial action intensity database,"
*IEEE Transactions on Affective Computing*, vol. 4, no. 2, pp. 151–160, 2013.

---

[39] A. Chaubey, X. Guan, and M. Soleymani,
"Face-llava: Facial expression and attribute understanding through instruction tuning,"
in *Proceedings of the IEEE/CVF Winter Conference on Applications of Computer Vision*, 2026, pp. 2648–2660.
