# Attention Driven Multi-method Assessment Fusion (VMAF)

Video Multi-method Assessment Fusion (VMAF) is a video quality metric developed by Netflix to assess automatically the quality of content compressed under different conditions. VMAF was designed on the observation that some state-of-the-art quality metrics correlate well with human judgement for some particular compression artefacts but do not perform as well when the type of distortion is different. Given that lossy compression introduces a combination of the well-known blocking, blurring, ringing and motion artefacts, VMAF runs different state-of-the-art quality metrics devoted to assess a particular artefact and then combines the scores obtained using machine learning, more precisely a Support Vector Machine (SVM) classifier. The parameters used by such SVM have been trained over content representative of on demand video streaming and are provide as baseline configuration which is used to run the metric. The training has been performed by providing the SVM with the subjective scores associated with each video along with the values of the different state-of-the-art quality metrics considered in VMAF’s design. Worth noting that the subjective score provided for each video refers to the whole duration of the clip and doesn’t explicitly carry information on which areas of video frames contributed more to the final score. Having such local information would allow to optimise compression by improving image areas likely to draw more attention than others.The project will involve the use of the VMAF’s codebase provided by Netflix to extend its workflow and accommodate the use of attention driven processing. 

-------------------------------------------------------------------------------------------------

VMAF模型是Netflix开发的一种视频质量评估标准，用于自动评估不同条件下视频压缩后的内容质量。VMAF被设计来观察那些最先进的视频质量评估标准，这些评估标准与人工评估密切相关，但当失真类型不同时表现不佳。鉴于压缩导致了一些众所周知的问题，比如内容的阻塞、模糊、振铃和运动伪影，VMAF运行不同的最先进的质量指标指标，专门用于评估特定的视频，然后结合使用机器学习后的分数，更准确地说其使用的是支持向量机（SVM）模型。此类SVM使用的参数已针对符合要求的视频流进行了训练，并作为用于运行模型的标准前置向。通过向SVM提供每个视频的主观分数以及VMAF设计中考虑的不同最先进质量指标的值来执行训练。值得注意的是，为每个视频提供的主观分数是指剪辑的整个持续时间，并没有明确包含视频帧的哪些区域对最终分数贡献更大的信息。拥有此类本地信息将允许通过改进可能比其他区域吸引更多注意力的图像区域来优化压缩。该项目将涉及使用Netflix提供的VMAF代码库来扩展其工作流程，并加入Attention模型来进行改进。


# Set Up and Usages
To improve the accuarcy of the VMAF, many Codes are modified in these two parts.Please refer to [C library](libvmaf) and [VMAF Python library](python).The VDK package offers a number of ways for a user to interact with the VMAF algorithm implementations. The core feature extraction library is written in C. The rest scripting code including the classes for machine learning regression, training and testing VMAF models and etc., is written in Python. Besides, there is C++ an implementation partially replicating the logic in the regression classes, such that the VMAF prediction (excluding training) is fully implemented.

  - C library of VMAF should be compiled and installed locally.

 [`libvmaf` - a C library](libvmaf/README.md) offers an interface to incorporate VMAF into your C/C++ code.
  - python library of VMAF should be installed.
 
 [VMAF Python library](resource/doc/VMAF_Python_library.md) offers full functionalities including running basic VMAF command line, running VMAF on a batch of video files, training and testing a VMAF model on video datasets, and visualization tools, etc.
  - run the basic VMAF command line to show the JSON output

![图片](https://github.com/Jasmine216/Project_vmaf/assets/56590213/c8adfad7-d017-4930-8020-cef054e92107)
  - Configuration of GPU Environment for Deep Learning.

# Dataset and Preprocessing

This project utilizes two datasets including the Netflix public dataset and the LIVE dataset. [Dataset files](resource/dataset) which contain basic information of datasets for the model are called LIVE.py and NFLX_dataset_public.py. In this project, they provide different DMOS by different ways of z-score normalization. DMOS should be mapped to the range 0 to 100 which matches the model prediction value interval, to be compared with prediction scores.

Before quality prediction, run the Attention-map Generation which can generate videos with attention maps.
  - [Attention model](Attention_video.ipynb) offers the videos generated by attention maps, which are noted by obj_videos in dataset files.


# Data Analysis
Files of data analysis for results are provided.

## Results in CSV files
To save results of model prediction for two datasets, codes in python library are modified to write result in csv files instead of show results direcly. Many [CSV files](results_CSV) provide data for analysis.

## Data Analysis of Original VMAF Performance
![图片](https://github.com/Jasmine216/Project_vmaf/assets/56590213/0360764f-0efc-4518-b3d4-c76b6c6554a1)

In conclusion, we get three useful information after the step of data analysis:
- Prediction scores of videos with high noise are lower than DMOS at a high level.
- Prediction scores are lower than DMOS in common.
- Prediction of videos without an object shows good performance, while the prediction of videos with a moving object does not perform well.

## Result of finding a suitable weight value of parameter p
P is the weight parameter in the new formula of VIF and ADM mentioned in section 3. To obtain a suitable parameter as p, four sampled types of videos are tested using different p from 0 to 0.4. The reason why a p-value bigger than 0.4 is not tested is R2 is negative when p is 0.4. At the same time, positive p can make the effects of the attention map on the loss map. The diverse results of experiments are shown in Table.7. When p1 values in ADM and p2 value in VIF are both 0.2, the video set with the moving object (Tennis) which has worse performance in the original VMAF model can obtain the highest R2. Thus, both p in ADM and VIF are chosen as 0.2 to see if adding the attention map relating to moving objects can result in better performance. Also, if considering the influence of the attention map on high noise, p1 values can be chosen in ADM as 0.3 and p2 in VIF as 0.1. The video set with high noise (water) which also has worse performance in the original VMAF model can obtain the highest R2.

## Original VMAF VS attention-map based VMAF
![图片](https://github.com/Jasmine216/Project_vmaf/assets/56590213/ad9e161c-15f0-4430-b538-57bdcef3a82e)
Fig.21(b) shows the result of the Netflix Public Dataset that SRCC is lower than the original one but PCC of the new model are higher than the original one when p1=0.3 and p2=0.1. Also, the RMSE of VMAF drops after adding the attention map. That is to say, the prediction scores of videos in Netflix Public Dataset from the VMAF model based on attention models are more accurate than that from the original model in this case.


# Discussion & Conclusion
To better understand the project, the strengths and weaknesses of the work should be considered. Besides, this section can help find opportunities for future work and any possibilities in this domain. 

 - Strengths

The saliency model can improve the accuracy of the prediction scores from the VMAF model to some extent, especially the prediction scores of videos with a moving object.

 - Weaknesses

The new model is not stable because it behaves differently in different databases. 
The accuracy of new model is not improved after adding the attention map. That is to say, the problem of low accuracy of quality prediction for videos with high noise cannot be solved by attention maps. 

- Possibility for future work:

Train a new model considering the video content as a new feature, where it is expected that the model can be more generic for videos with different contents.
Try to incorporate other saliency models with the VMAF model to improve the accuracy of the model.
Find a better way to get a suitable p-value used in the weight of attention maps.
Transform scores to higher prediction scores by the simple equation in the model when the video has high noises, which is a kind of solution in the phone model. 


