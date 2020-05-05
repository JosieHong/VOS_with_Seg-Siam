# SiamPolarMask: Single Shot Video Object Segmentation with Polar Representation

This is an implement of SiamPolarMask on [mmdetection](https://github.com/open-mmlab/mmdetection). The code is coming soon.

![siam_polarmask_pipeline](./imgs/siam_polarmask_pipeline.png)

## Highlights

- **One-step**: It is an anchor free method that we track the objects as points and segment them at the same time. 
- **Speed**: It is a fast method for VOS because of the distance regression of key points in polar coordinates.

## Performances

![demo](./imgs/demo.gif)

| backbone   | $AP$ | $AP_{50}$ | $AP_{75}$ | $AP_{S}$ | $AP_{M}$ | $AP_{L}$ | Speed/fps |
| ---------- | ---- | --------- | --------- | -------- | -------- | -------- | --------- |
| ResNet-50  | 35.1 | 83.1      | 25.5      | -1.0     | 27.5     | 35.4     | 30        |
| ResNet-101 | 35.3 | 83.8      | 27.4      | -1.0     | 28.3     | 35.4     | 12        |

*Results of other backbones are coming soon.*

## Setup Environment

Our SiamPolarMask is based on [mmdetection](https://github.com/open-mmlab/mmdetection). It can be installed easily as following. 

```shell
git clone ...
cd SiamPolarMask
conda create -n open_mmlab python=3.7 -y
conda activate open_mmlab

pip install --upgrade pip
pip install cython torch==1.4.0 torchvision==0.5.0 mmcv
pip install -r requirements.txt # ignore the errors
pip install "git+https://github.com/cocodataset/cocoapi.git#subdirectory=PythonAPI"
pip install -v -e . 
```

## Prepare DAVIS Dataset

1. Download DAVIS from [kaggle-DAVIS480p](https://www.kaggle.com/mrjb166/davis480p).

2. Convert DAVIS to coco format by `/tools/conver_datasets/davis2coco.py` and organized it as following

```shell
mmdetection
├── mmdet
├── tools
├── configs
├── data
│  ├── DAVIS
│  │  ├── Annotations
|  |  |  ├── 480p_train.json
|  |  |  ├── 480p_trainval.json
|  |  |  ├── 480p_val.json
|  |  |  ├── db_info.yml
│  │  ├── Imageset
│  │  ├── JPEGImages
```

## Train & Test

It can be trained and test as other mmdetection models. For more details, you can read [mmdetection manual](https://mmdetection.readthedocs.io/en/latest/INSTALL.html) and [mmcv-manual](https://mmcv.readthedocs.io/en/latest/image.html).

```shell
python tools/train.py ./configs/siam_polarmask/siampolar_r50.py --gpus 1

python tools/test.py ./configs/siam_polarmask/siampolar_r50.py \
./work_dirs/siam_polarmask_r50/epoch_12.pth \
--out ./work_dirs/siam_polarmask_r50/res.pkl \
--eval segm
```

## Demo

```
cd ./demo
python visualize_vos.py
```

## License

For academic use, this project is licensed under the 2-clause BSD License - see the LICENSE file for details. For commercial use, please contact the authors. 
