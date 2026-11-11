### **Disclaimer !!!**
### ˗ˏˋ ★ ˎˊ˗
### Consider this my official breakup with AI-as-a-mathematical-construct. I still find intelligence fascinating; I just don't want to spend my life treating it as a collection of equations, metrics, and optimization problems. I'm here for cognition, perception, and the mechanisms of thinking. Apparently, I came for the mind and got a lot of matrix multiplication... <br>
### ˗ˏˋ ★ ˎˊ˗
# Satellite Image Segmentation & Land-Cover Analysis

> A brief GLIMPSE into my internship work on deep learning, semantic segmentation, and multi-temporal satellite imagery.
>
> This repository documents an attempt to use computer vision to interpret historical satellite images of Babol, Iran. The project achieved promising results on its training dataset, but its predictions on real Babol imagery exposed substantial domain-generalization and data-quality limitations.
>
> This is an experimental engineering record, not a validated urban-monitoring system.

---

## 1. Project Overview

The objective of this internship project was to explore how deep learning could be used to identify land-cover categories in satellite imagery and investigate possible changes across different years.

The geographical focus was Babol, Iran.

The project followed two main stages:

1. Training and experimenting with semantic segmentation models using an annotated satellite-image dataset.
2. Applying the trained models to historical satellite imagery of Babol to investigate their ability to recognize land-cover categories in a different geographical environment.

The segmentation task involved five classes:

| Class | Description |
|---|---|
| Background | Other or unclassified regions |
| Plants | Vegetation and green areas |
| Buildings | Built-up structures |
| Water | Rivers and water bodies |
| Roads | Road networks and transportation surfaces |

The intended application was multi-temporal land-cover analysis, with historical imagery covering selected years between 2006 and 2025.

**Important distinction:** The implemented models performed semantic segmentation. Comparing their predictions across years was an exploratory approach to change analysis, not a validated change-detection methodology.

## 2. Motivation

Satellite imagery provides an opportunity to observe geographical environments from a different perspective.

Urban development, vegetation distribution, transportation infrastructure, and changes in natural landscapes can potentially be studied through historical imagery.

I wanted to explore whether a neural network could learn visual representations of these land-cover categories and apply them to an actual geographical area.

Rather than treating the project as a simple model-training exercise, I wanted to take the model beyond its original dataset.

The central question became:

**Can a model trained to recognize land-cover patterns in one geographical environment reliably interpret satellite imagery from another?**

This question ultimately became the most important part of the project.

## 3. Methodology

### Semantic Segmentation

Semantic segmentation is a computer vision task in which a model assigns a class label to every pixel of an image.

Unlike image classification, which produces a label for an entire image, semantic segmentation produces a spatial map of predicted categories.

For this project, each input satellite image was transformed into a five-class segmentation mask.

### U-Net

U-Net was the primary architecture explored during development.

Its encoder-decoder structure allows the network to learn increasingly abstract visual features while recovering spatial information through skip connections.

The implementation used:

- Convolutional layers
- Double convolution blocks
- Batch normalization
- ReLU activation
- Max pooling
- Transposed convolution
- Skip connections
- Pixel-level classification

### ResNet34-Based U-Net

A U-Net architecture with a ResNet34 encoder was also investigated.

The intention was to improve feature extraction using pretrained visual representations.

This approach produced promising validation-loss results on randomly separated patches from the original dataset.

### Additional Experiments

The development process included several alternative approaches:

- Class-weighted Cross-Entropy Loss
- Dice Loss
- Focal Loss
- Combined Dice and Cross-Entropy Loss
- Focal and Dice loss combinations
- Dynamic data augmentation
- Learning-rate scheduling
- Fine-tuning
- DeepLabV3+ with MobileNetV2
- SegFormer experiments
- SAM2-guided segmentation experiments

These experiments were exploratory. They did not all produce reliable improvements, and their metrics should not be interpreted as results from one unified model.

## 4. Dataset and Image Acquisition

### Training Dataset

The initial training experiments used the LandCover.ai dataset.

The dataset provided annotated aerial imagery that could be used to train semantic segmentation models.

The images and corresponding masks were processed into smaller patches for model training.

The preprocessing workflow included:

1. Loading image-mask pairs.
2. Matching images with their corresponding annotations.
3. Extracting smaller image patches.
4. Converting masks into class-index representations.
5. Normalizing image values.
6. Creating training and validation loaders.

### Babol Satellite Imagery

The target geographical area was Babol, Iran.

Historical imagery was collected using the historical imagery feature in Google Earth.

Selected years included:

- 2006
- 2012
- 2019
- 2025

The availability and quality of imagery were not consistent across all years.

Several practical problems emerged:

- Different color characteristics between historical images.
- Inconsistent lighting and contrast.
- Differences in visible detail and image quality.
- Difficulties obtaining consistent geographical coverage.
- Additional graphical elements in some source images.
- Insufficient high-quality reference imagery for Babol.

The original intention was to create comparable segmentation maps across these dates.

However, the limitations of the available imagery affected the reliability of this process.

**Data availability note:** Original training datasets and model checkpoints are not included in this repository. Google Earth imagery is subject to its applicable usage and redistribution terms.

## 5. Image Preprocessing

Several preprocessing approaches were investigated to reduce inconsistencies between images.

### Patch Extraction

Large satellite images could not always be processed directly because of GPU memory limitations.

Images were therefore divided into smaller patches, commonly using 256 × 256 image regions.

This made it possible to process larger geographical areas through multiple inference operations.

### Color Standardization

Historical images frequently had different color distributions.

Two approaches were investigated:

- Histogram matching
- LAB color-space standardization

The LAB approach allowed brightness and color information to be handled separately.

Although these methods reduced some visual differences, they could not guarantee consistent representations across different acquisition conditions.

### Image Enhancement and Postprocessing

Additional experiments involved:

- Image normalization
- Contrast and color adjustments
- Sliding-window inference
- Patch stitching
- Connected-component cleanup
- Boundary refinement

These operations were intended to improve segmentation consistency and the visual quality of the resulting maps.

They could not compensate for missing geographical information or unreliable reference data.

## 6. Experimental Results

The experiments produced several different results depending on the architecture, training configuration, loss function, and evaluation procedure.

It is important to distinguish validation loss, training-sample metrics, and performance on the target geographical imagery.

### ResNet34-U-Net Validation

One ResNet34-U-Net experiment used 1,994 active image patches and an 80/20 random patch-level training/validation split.

The recorded validation loss was:

| Epoch | Validation Loss |
|---:|---:|
| 1 | 0.2587 |
| 2 | 0.2524 |
| 3 | 0.2491 |
| 4 | 0.2401 |
| 5 | 0.2367 |
| 6 | 0.2395 |
| 7 | 0.2274 |
| 8 | 0.2327 |
| 9 | 0.2288 |
| 10 | 0.2295 |
| 11 | 0.2306 |
| 12 | 0.2307 |
| 13 | 0.2298 |
| 14 | 0.2330 |
| 15 | 0.2344 |

The best recorded validation loss was **0.2274**.

This was encouraging evidence that the model could learn the original dataset's visual patterns.

However, the split was performed at patch level. It was not a geographically independent evaluation, and it did not establish generalization to Babol.

### Reported mIoU Experiment

A later fine-tuning experiment recorded the following IoU and Dice metrics:

| Class | IoU | Dice |
|---|---:|---:|
| Background | 91.17% | — |
| Plants | 73.22% | — |
| Buildings | 95.05% | — |
| Water | 96.06% | — |
| Roads | 73.97% | — |
| **Mean IoU** | **85.89%** | — |

**Evaluation limitation:** The notebook independently selected 500 samples for training and evaluation using the same random seed and source collection. The selected samples were therefore the same.

Consequently, the 85.89% mIoU is a training-sample evaluation, not an independent test-set result.

It is preserved here as an experimental result, not as evidence of generalization.

### Later Evaluation Snapshot

Another notebook experiment reported:

| Class | IoU | Dice |
|---|---:|---:|
| Background | 75.46% | 86.01% |
| Plants | 29.95% | 46.09% |
| Buildings | 86.28% | 92.63% |
| Water | 83.95% | 91.28% |
| Roads | 31.67% | 48.11% |
| **Mean IoU** | **61.46%** | — |

The corresponding evaluation code used `train_loader`.

These values should also be interpreted as training-loader metrics rather than independent test performance.

The difference between these experimental results illustrates why evaluation methodology matters as much as the numerical score itself.

## 7. Visual Results

### Training Loss

The following figure illustrates the class-weighted U-Net training loss across 20 epochs.

![Class-weighted U-Net training loss](<training curve.png>)

The decreasing loss indicates that the model was optimizing its training objective.

It does not independently establish generalization performance.

### Model Predictions and Ground Truth

The following figure compares input satellite patches, reference masks, and U-Net predictions.

![Satellite image segmentation examples](<mioi.png>)

The examples illustrate both successful region recognition and errors in spatial boundaries.

### Babol 2025 Inference

An example of the model applied to Babol imagery.

![Babol 2025 segmentation result](<Babol 2025 segmentation.png>)

This is an experimental model prediction, not a verified land-cover map.

### Multi-temporal Comparison

An example of the intended temporal-analysis workflow.

![Temporal satellite imagery and segmentation masks](<temporal comparison.png>)

The figure illustrates the comparison of satellite imagery from different years and their corresponding predicted masks.

Differences between segmentation masks cannot automatically be interpreted as geographical changes.

They can also result from image quality, acquisition conditions, alignment problems, or model errors.

## 8. What Happened When the Model Reached Babol?

This was the most challenging stage of the project.

The model had learned useful representations from the training dataset, and some validation experiments showed encouraging results.

However, applying the model to Babol imagery produced substantially less reliable predictions.

### Domain Shift

The training dataset and Babol imagery came from different geographical environments.

The visual characteristics of buildings, vegetation, roads, water, and surrounding landscapes were not necessarily represented in the same way.

The model had learned patterns from its original training environment, but those patterns did not consistently transfer to Babol.

### Prediction Collapse

Some Babol experiments produced highly unbalanced predictions.

For example, one experiment reported:

| Year | Buildings | Plants | Roads |
|---|---:|---:|---:|
| 2006 | 99.59% | 0% | 0% |
| 2012 | 100% | 0% | 0% |
| 2019 | 99.98% | 0% | 0% |
| 2025 | 99.99% | 0% | 0% |

These predictions clearly did not provide a meaningful representation of the actual land-cover distribution.

Other checkpoints and preprocessing configurations produced substantially different distributions.

This inconsistency made the resulting temporal statistics unreliable.

### The Data Problem

One of the most important limitations was the quality of the target imagery.

Historical images obtained through Google Earth were not perfectly consistent.

Different years contained different color characteristics and visual conditions.

Image enhancement and color normalization were attempted, including LAB standardization and histogram matching.

However, these techniques could not reconstruct information that was missing from the source imagery.

Access to higher-quality, geographically consistent satellite imagery and reliable reference maps would have made a substantial difference.

### Why This Matters

A model can learn to recognize patterns in its training dataset without being reliable in a different environment.

The ability to produce a segmentation mask is not equivalent to the ability to interpret a new geographical scene correctly.

This project demonstrated that distinction directly.

## 9. Development History

My Original repository(not this one) contains notebooks from different stages of the experimentation process.

| Notebook | Main Focus |
|---|---|
| `01_data_eda_and_boundaries.ipynb` | Dataset exploration and initial preparation |
| `02_pytorch_pipeline_augmentation.ipynb` | PyTorch pipeline and patch extraction |
| `03_landcover_semantic_segmentation_model_training.ipynb` | Initial U-Net implementation and training |
| `4_trying_to_decrease_loss.ipynb` | Loss optimization and ResNet34-U-Net experiments |
| `05_Babol_paused.ipynb` | Initial Babol inference experiments |
| `06_U-net_again.ipynb` | U-Net experiments, class weighting, and IoU evaluation |
| `07_Babol.ipynb` | Babol image processing and color standardization |
| `08_hope.ipynb` | Further inference, alternative approaches, and troubleshooting |
| `09_the_end.ipynb` | Fine-tuning, checkpoint evaluation, and later experiments |


## 10. Internship Experience

This project was developed as part of my internship in artificial intelligence and computer vision.

Throughout the internship, I worked on implementing segmentation architectures, experimenting with training strategies, processing satellite imagery, managing computational resources, and investigating model performance.

One of the biggest challenges was not necessarily the model itself, but the data available for applying it to Babol.

I submitted weekly progress reports and repeatedly communicated my concerns about the quality of the satellite imagery. The model was learning reasonably well on its original dataset, but the historical images I collected for Babol from Google Earth's historical imagery feature had noticeably different color characteristics and visual conditions across years. Even after experimenting with histogram matching, LAB color standardization, and other preprocessing techniques, these inconsistencies remained a significant obstacle.

I was aware of this limitation throughout the project and raised it repeatedly. I spent considerable time trying different approaches to compensate for a problem that was increasingly becoming a data-acquisition issue rather than simply a model-optimization issue.

During the final presentation, I was told that we could potentially have approached the university's surveying and mapping department to obtain higher-quality geographical data and historical maps of Babol covering the previous twenty years.

That suggestion was particularly frustrating to hear at that stage. Access to such resources could have fundamentally changed the direction of the project, especially considering how frequently I had reported the difficulties with the available imagery.

It was a lesson in more than computer vision. The project made me realize how important early access to domain expertise, appropriate data sources, and responsive technical supervision can be. A considerable amount of experimentation might have been directed differently if those resources and conversations had been available earlier.

I am not including individual names or turning this section into a personal dispute. I would rather document what happened, acknowledge the limitations, and move forward with what I learned.

Ultimately, I did my best with the data and resources available to me. The results were not what I originally envisioned, but the experience gave me a much more realistic understanding of what it takes to bring an AI model from a controlled dataset into the real world.


