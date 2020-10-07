# TensorFlow based Quantile Regression solution - OSIC Pulmonary Fibrosis
A complete tensorflow pipeline for training, inference and feature extraction notebooks used in Kaggle competition [OSIC Pulmonary Fibrosis](https://www.kaggle.com/c/osic-pulmonary-fibrosis-progression) (July-Oct 2020)

## Table of Contents

- [Brief overview of the competition data](#Brief-overview-of-the-competition-data)
- [Notebooks description](#Notebooks-description)
  - [Feature Engineering] notebook](#Feature-Engineering)
  - [[TRAIN] notebook](#TRAIN-notebook)
  - [[INFERENCE] Submission notebook](#INFERENCE-Submission-notebook)
- [How to use](#How-to-use)

## Brief overview of the competition data
The data contained of dicom (images + metadata) data of chest X-Ray of patients along with tabular data like smoking status, age, Forced Vital Capacity (FVC) values etc.  
Slices preview of chest X-Ray of a patient are as:  
<a href="https://imgur.com/tCdcaAY"><img src="https://imgur.com/tCdcaAY.png" title="head" alt="head" /></a>  
Lung mask segmentation process deployed was (3rd image - final mask)  
<a href="https://imgur.com/sLKfFIB"><img src="https://i.imgur.com/sLKfFIB.png" title="source: imgur.com" /></a>  
3D plot of stacked 2D segmented masks to form a lung produces  
<a href="https://imgur.com/cMlrawV"><img src="https://i.imgur.com/cMlrawV.png" title="source: imgur.com" width="378" height="378" /></a>  
Apart from the dicom data the tabular data was as follows  
<a href="https://imgur.com/2dZqCNy"><img src="https://i.imgur.com/2dZqCNy.jpg" title="source: imgur.com" /></a>  

## Notebooks description
A brief content description is provided here, for detailed descriptions check the notebook 

### Feature Engineering notebook
  A major task was engineering and extracting features from the dcm slices  
  In total I engineered 5 features as follows  
  1. Chest Volume:  
    - Calculated through numpy.trapz() integration over all 2D slices using pixel count, sliceThickness and pixelSpacing (Voxel spacing) metadata in the dcm file  
    - Dealt with the inconsistencies in the data and final distplot produced was  
    <a href="https://imgur.com/qvOfgGf"><img src="https://i.imgur.com/qvOfgGf.png" title="source: imgur.com" /></a>  
    
  2. Chest Area:  
    - Maximum area of chest calculated using the average of 3 middle most slices in same fashion as Chest Volume  
    - distplot  
    <a href="https://imgur.com/OAIyyja"><img src="https://i.imgur.com/OAIyyja.png" title="source: imgur.com" /></a>  
    
  3. Lung - Tissue ratio:  
    - Ratio of pixel area of segmented lung mask to the total tissue pixel area as in original dcm file  
    - The ideology behind being this feature was to detect lung shrinkage inside chest  
    - distplot  
    <a href="https://imgur.com/KKnaP43"><img src="https://i.imgur.com/KKnaP43.png" title="source: imgur.com" /></a> 
    
  4. Chest Height:  
    - Chest height calculated using sliceThickness and number of slices forming the lung  
    - distplot  
    <a href="https://imgur.com/ipaouo5"><img src="https://i.imgur.com/ipaouo5.png" title="source: imgur.com" /></a>  
    
  5. Height of the Patient:  
    - Approximate height calculated using FVC values and age of a patient according to formulaes and observations made from [external medical research data](https://en.wikipedia.org/wiki/Vital_capacity#Formulas)  
    - distplot  
    <a href="https://imgur.com/wX6GrYz"><img src="https://i.imgur.com/wX6GrYz.png" title="source: imgur.com" /></a>  
    
  Plots of Features vs FVC / Percent  
  <a href="https://imgur.com/1dTAE1i"><img src="https://i.imgur.com/1dTAE1i.png" title="source: imgur.com" /></a>  
  
### [TRAIN] notebook
  EffNet train notebook described below, Custom tf tabular data only model listed in [INFERENCE] itself  
  1. Pre-Processing:  
    - Handled the various sizes and missing slices issues  
    - Stratified 5 fold split based on PatientID  
    
  2. Augmentations:  
    - Albumentations - RandomSizedCrop, Flips, Gaussian Blur, CoarseDropout, Rotate (0-90)  
    
  3. Configurations:  
    - Optimizer - NAdam  
    - LR Scheduler - ReduceLRonPlateau (initial LR = 0.0005, patience = 5, factor = 0.5)  
    - Model - EfficientNet B5   
    - Input Size - 512 * 512   

### [INFERENCE] Submission notebook
  Contains custom tabular data model training and inference too  
  1. Custom Net:  
    - A tiny net using given tabular data and engineered features on swish activated dense layers  
    - Pinball loss function for multiple quantiles was used, the difference in first and last quantiles was used as uncertainty measure  
    
  2. Ensemble:  
    - Final submission made using ensemble of both effnet image and custom model  
    
## How to use
Just change the directories according to your environment.  

Google Colab deployed versions are available for  
**[TRAIN] Effnet** [![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/drive/18Amk_pm1pZloknXHaco2cCva6zHb9uxo?usp=sharing)  
**[TRAIN] Base Custom Net** [![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/drive/12Dmn2BWLux_0grGURu4clzg9wjy_GqOf?usp=sharing)  

