
<div align="center">    

# CelloType   


<!--
ARXIV   
[![Paper](http://img.shields.io/badge/arxiv-math.co:1480.1111-B31B1B.svg)](https://www.nature.com/articles/nature14539)
-->

<!--  
Conference   
-->   
</div>

# Description   
An end-to-end Transformer-based method for automated cell/nucleus segmentation and cell type classification  

![overview](figures/overview.png)

## Feature Highlights:
- Improved Precision: For both segmentation and classification
- Wide Applicability: Various types of images  (fluorescent, brighfield, natural)
- Multi-scale: Capable of classifying diverse cell types and microanatomical structures

![example](figures/codex_example.png)

Our code is based on open-source projects [Detectron2](https://github.com/facebookresearch/detectron2), [Mask DINO](https://github.com/IDEA-Research/MaskDINO).

## Installation
First, install dependencies 
- Linux with Python = 3.8 
- Detectron2: follow [Detectron2](https://detectron2.readthedocs.io/en/latest/tutorials/install.html) installation instructions. 

```bash
# create conda environment
conda create --name cellotype python=3.8
conda activate cellotype
# install pytorch and detectron2
conda install pytorch==1.9.0 torchvision==0.10.0 cudatoolkit=11.1 -c pytorch -c nvidia
python -m pip install 'git+https://github.com/facebookresearch/detectron2.git'
# (add --user if you don't have permission)

# clone and install the project   
git clone https://github.com/maxpmx/CelloType.git
cd CelloType
pip install -r requirements.txt

# Compile Deformable-DETR CUDA operators
git clone https://github.com/fundamentalvision/Deformable-DETR.git
cd Deformable-DETR
cd ./models/ops
sh ./make.sh
```
 <!-- Next, navigate to the folder and run it.   
 ```bash
# module folder
cd example

# run module (example: mnist as your main contribution)   
python main.py    
 ``` -->

# Quick started
Pretrained models can be downloaded from the [Drive](https://upenn.box.com/s/str98paa7p40ns32mchhjsc4ra92pumv) folder.
```python
from skimage import io
from cellotype.predict import CelloTypePredictor

img = io.imread('data/example/example_tissuenet.png') # [H, W, 3]

model = CelloTypePredictor(model_path='cellotype/models/tissuenet_model_0019999.pth',
  confidence_thresh=0.3, 
  max_det=1000, 
  device='cuda', 
  config_path='cellotype/configs/maskdino_R50_bs16_50ep_4s_dowsample1_2048.yaml')

mask = model.predict(img) # [H, W]
```

Example Notebook: 

[1. CelloType Segmentation Example](notebooks/cell_segmentation.ipynb)

[2. CelloType Classification Example](notebooks/tissue_annotation.ipynb)

## Inference Cell Segmentation for Large image using Slicing Aided Hyper Inference (SAHI)

```bash
python sahi_inference.py 
```

# Model Training
## Workflow
1. Prepare data to the format Detectron2 required
2. Train the model
3. Test the model and visualize results

## Cell Segmentation (TissueNet Dataset)
### 1. Download data and pretrained models weights

#### 1.1 Download the processed data

IMPORTANT: Note that the raw data is from [TissueNet](https://datasets.deepcell.org/), this processed data is for demo purpose ONLY!

Download ```data/example_tissuenet.zip``` from the [Drive](https://upenn.box.com/s/str98paa7p40ns32mchhjsc4ra92pumv) and put it in the ```data``` folder. Then unzip it.
```bash
cd data
unzip example_tissuenet.zip
cd ..
```


#### 1.2 Download pretrained models weights

Download ```models/maskdino_swinl_50ep_300q_hid2048_3sd1_instance_maskenhanced_mask52.3ap_box59.0ap.pth``` from the [Drive](https://upenn.box.com/s/str98paa7p40ns32mchhjsc4ra92pumv) and put it in the ```cellotype/models``` folder.
<!-- ```bash
cd models

cd ..
``` -->

### 2. Train model

```bash
python train_tissuenet.py --num-gpus 4
```

The parameters are optimized for 4*A100 (40GB) environment, if your machine does not have enough GPU memory, you can reduce the batch size by changing the ```IMS_PER_BATCH``` in ```configs/Base-COCO-InstanceSegmentation.yaml```.

### 3. Test model and visualize results

For reference, our trained weights ```models/tissuenet_model_0019999.pth``` can be downloaded from the [Drive](https://upenn.box.com/s/str98paa7p40ns32mchhjsc4ra92pumv) folder.
```bash
python test_tissuenet.py --num-gpus 1
```

## Cell Annotation (CODEX CRC Dataset)
### 1. Download data and pretrained models weights

#### 1.1 Download the processed data

IMPORTANT: Note that the raw data is from [Garry P. Nolan Lab](https://doi.org/10.7937/tcia.2020.fqn0-0326), this processed data is for demo purpose ONLY!

Download ```data/example_codex_crc.zip``` from the [Drive](https://upenn.box.com/s/str98paa7p40ns32mchhjsc4ra92pumv) and put it in the ```data``` folder. Then unzip it.
```bash
cd data
unzip example_codex_crc.zip
cd ..
```

#### 1.2 Download pretrained models weights

Download ```models/maskdino_swinl_50ep_300q_hid2048_3sd1_instance_maskenhanced_mask52.3ap_box59.0ap.pth``` from the [Drive](https://upenn.box.com/s/str98paa7p40ns32mchhjsc4ra92pumv) and put it in the ```cellotype/models``` folder.

### 2. Train model

```bash
python train_crc.py --num-gpus 4
```

The parameters are optimized for 4*A100 (40GB) environment, if your machine does not have enough GPU memory, you can reduce the batch size by changing the ```IMS_PER_BATCH``` in ```configs/Base-COCO-InstanceSegmentation.yaml```. For reference, the training take ~12 hours on 4\*A100 (40GB) environment.


### 3. Test model and visualize results
For reference, our trained weights ```models/crc_model_00059999.pth``` can be downloaded from the [Drive](https://upenn.box.com/s/str98paa7p40ns32mchhjsc4ra92pumv) folder.
```bash
python test_crc.py --num-gpus 1
```


### Citation   
```
@article{YourName,
  title={Your Title},
  author={Your team},
  journal={Location},
  year={Year}
}
```
### Acknowledgement
Many thanks to these projects
- [Detectron2](https://github.com/facebookresearch/detectron2)
- [Mask DINO](https://github.com/IDEA-Research/MaskDINO)
