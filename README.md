# SiamPolar: Realtime Video Object Segmentation with Polar Representation in Traffic Scenes

This is the code of SiamPolar based on [mmdetection](https://github.com/open-mmlab/mmdetection).  SiamPolar is a semi-supervised real-time video object segmentation method with a novel polar representation method. 

<div align="center">
	<img src="./imgs/siam_polarmask_pipeline.png" alt="siam_polarmask_pipeline" width="800">
</div>


**Feb 14, 2021**: SiamPolar v1.0. 

**Jun 15, 2021**: Our paper is accepted by ITSC2021. 

**Jun 24, 2021:** Update SiamPolar-light. 

## Performances on DAVIS2016

| Methods         | J_M​      | J_R​      | J_D​     | F_D​      | F_R​      | F_D​     | Speed     |
| --------------- | -------- | -------- | ------- | -------- | -------- | ------- | --------- |
| SiamMask        | 71.3     | 86.8     | 3.0     | **67.8** | **79.8** | 2.1     | 55.00     |
| SiamPolar-light | 65.3     | 85.4     | 2.1     | 49.3     | 50.5     | **0.8** | **63.20** |
| SiamPolar       | **71.4** | **96.2** | **0.7** | 56.7     | 60.0     | 18.1    | 59.20     |

## Setup Environment

SiamPolar is implemented on [mmdetection](https://github.com/open-mmlab/mmdetection). It can be installed easily as following, and more details can be seen in `./INSTALL.md`. 

```shell
git clone https://github.com/JosieHong/SiamPolar.git
cd SiamPolar

# conda environment (recommend) 
conda create -n open_mmlab python=3.7 -y
conda activate open_mmlab
# or virtualenv environment
# virtualenv env
# source env/bin/activate

pip install --upgrade pip
# Please install a PyTorch version fitted your CUDA driver 
# from https://pytorch.org/get-started/locally/
pip install -r requirements.txt
pip install "git+https://github.com/cocodataset/cocoapi.git#subdirectory=PythonAPI"

python setup.py develop
# or "pip install -v -e ."
```

If there is any problem with the environment, please check the versions first: 

- CUDA == 10.1
- torch == 1.2.0 
- torchvision == 0.4.0
- mmdet == 1.0rc0+3ad0a02
- mmcv == 0.4.3

## Prepare DAVIS2016 Dataset

1. Download DAVIS2016 from [kaggle-DAVIS480p](https://www.kaggle.com/mrjb166/davis480p).

2. Convert DAVIS2016 to COCO format by `./tools/convert_datasets/davis2coco.py [path to DAVIS2016 dataset]` and organized it as following: (The files in brackets are not used in SiamPolar.)

```shell
SiamPolar
├── mmdet
├── tools
├── configs
├── data
│  ├── DAVIS
│  │  ├── Annotations
|  |  |  ├── 480p_train.json
|  |  |  ├── 480p_trainval.json
|  |  |  ├── 480p_val.json
|  |  |  ├── (db_info.yml)
|  |  |  ├── (480p)
│  │  ├── (Imageset)
|  |  |  ├── (480p)
|  |  |  |  ├── (train.txt)
|  |  |  |  ├── (trainval.txt)
|  |  |  |  ├── (val.txt)
|  |  |  |  ├── (test.txt)
│  │  ├── JPEGImages
|  |  |  ├── 480p
```

## Prepare SegTrack / SegTrack v2 Dataset

1. Download [SegTrack Dataset](http://cpl.cc.gatech.edu/projects/SegTrack/) / [SegTrack v2](https://web.engr.oregonstate.edu/~lif/SegTrack2/dataset.html).
2. Convert SegTrack / SegTrack v2 to COCO format: 

```bash
# First, please change the file name 'ground_truth' in penguin into 'ground-truth'.

python tools/convert_datasets/segtrack2coco.py [path to SegTrack dataset]
# e.g.
# python tools/convert_datasets/segtrack2coco.py /data1/datasets/SegTrack

python tools/convert_datasets/segtrack2coco_v2.py [path to SegTrack v2 dataset]
```

## Train & Test

SiamPolar can be trained and test as other mmdetection models. For more details, you can read [mmdetection-manual](https://mmdetection.readthedocs.io/en/latest/INSTALL.html) and [mmcv-manual](https://mmcv.readthedocs.io/en/latest/image.html). This is an example of SiamPolar(ResNet101 Backbone). 

Please check all the configures of SiamPolar in `./config/siampolar/`. 

```bash
# train
python tools/train.py <path to configure file> --gpus <gpu index> --resume_from <path to pretrained model>

# test
python tools/test.py <path to configure file> <path to pretrained model> \
--out <path to output pkl file> \
--eval vos
```

Here are some examples: 

```shell
# DAVIS2016
python tools/train.py ./configs/siampolar/siampolar_r101.py --gpus 1
python tools/test.py ./configs/siampolar/siampolar_r101.py ./work_dirs/asy_r101_semi/epoch_36.pth \
--out ./work_dirs/trash/res.pkl \
--eval vos

# DAVIS2016_GCN
python tools/train.py ./configs/siampolar/siampolar_r101_gcn.py --gpus 1 --resume_from ./work_dirs/trash/epoch_2.pth
python tools/test.py ./configs/siampolar/siampolar_r101_gcn.py ./work_dirs/trash/epoch_36.pth \
--out ./work_dirs/trash/res.pkl \
--eval vos

# DAVIS2016_light
python tools/train.py ./configs/siampolar/siampolar_r101_light.py --gpus 1
python tools/test.py ./configs/siampolar/siampolar_r101_light.py ./work_dirs/light/epoch_24.pth \
--out ./work_dirs/light/res.pkl \
--eval vos
python tools/test.py ./configs/siampolar/siampolar_r101_light.py ./work_dirs/trash/epoch_24.pth \
--out ./work_dirs/trash/res.pkl \
--eval vos

# TSD-max
python tools/train.py ./configs/siampolar/siampolar_r101_tsd-max.py --gpus 1
python tools/test.py ./configs/siampolar/siampolar_r101_tsd-max.py ./work_dirs/tsd_max/epoch_36.pth \
--out ./work_dirs/tsd_max/res.pkl \
--eval vos

# SegTrack
python tools/train.py ./configs/siampolar/siampolar_r101_segtrack.py --gpus 1
python tools/test.py ./configs/siampolar/siampolar_r101_segtrack.py ./work_dirs/segtrack/epoch_36.pth \
--out ./work_dirs/segtrack/res.pkl \
--eval vos

# SegTrack v2
python tools/train.py ./configs/siampolar/siampolar_r101_segtrackv2.py --gpus 1
python tools/test.py ./configs/siampolar/siampolar_r101_segtrackv2.py ./work_dirs/segtrackv2/epoch_36.pth \
--out ./work_dirs/segtrackv2/res.pkl \
--eval vos
```

## Demo

```
cd ./demo
python visualize_vos.py

python visualize_gen_polar.py <path_to_img_dir> <img_name> <polar_num>
```

## License

For academic use, this project is licensed under the 2-clause BSD License - see the LICENSE file for details. For commercial use, please contact the authors. 