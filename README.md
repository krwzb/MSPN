# MSPN
This repository contains the official code implementation for the paper .

## Installation
Check INSTALL.md for installation instructions.

## Dataset
Check DATASET.md for instructions of dataset preprocessing.

Organize all the files like this:

```python
datasets
  |-- vg
    |--detector_model
      |--pretrained_faster_rcnn
        |--model_final.pth       
    |--glove
      |--.... (glove files, will autoly download)
    |--VG_100K
      |--.... (images)
    |--VG-SGG-with-attri.h5 
    |--VG-SGG-dicts-with-attri.json
    |--image_data.json    
```

## Train
```python
CUDA_VISIBLE_DEVICES=1 python MSPN/tools/relation_train_net.py \
--config-file "MSPN/configs/e2e_relation_X_101_32_8_FPN_1x.yaml" \
MODEL.ROI_RELATION_HEAD.USE_GT_BOX True \
MODEL.ROI_RELATION_HEAD.USE_GT_OBJECT_LABEL True \
MODEL.ROI_RELATION_HEAD.PREDICT_USE_BIAS True \
MODEL.ROI_RELATION_HEAD.PREDICTOR MSPN \
DTYPE "float32" \
SOLVER.IMS_PER_BATCH 8 TEST.IMS_PER_BATCH 1 \
SOLVER.MAX_ITER 60000 SOLVER.BASE_LR 1e-3 \
SOLVER.SCHEDULE.TYPE WarmupMultiStepLR \
MODEL.ROI_RELATION_HEAD.BATCH_SIZE_PER_IMAGE 512 \
SOLVER.STEPS "(28000, 48000)" SOLVER.VAL_PERIOD 30000 \
SOLVER.CHECKPOINT_PERIOD 60000 GLOVE_DIR /home/MSPN/datasets/vg/glove \
MODEL.PRETRAINED_DETECTOR_CKPT /home/MSPN/datasets/vg/detector_model/pretrained_faster_rcnn/model_final.pth \
OUTPUT_DIR PENET/checkpoints/MSPN_PreCls \
SOLVER.PRE_VAL False \
SOLVER.GRAD_NORM_CLIP 5.0
```
## Test
```python
CUDA_VISIBLE_DEVICES=1 python MSPN/tools/relation_test_net.py \
--config-file "configs/e2e_relation_X_101_32_8_FPN_1x.yaml" \
MODEL.ROI_RELATION_HEAD.USE_GT_BOX True \
MODEL.ROI_RELATION_HEAD.USE_GT_OBJECT_LABEL True \
MODEL.ROI_RELATION_HEAD.PREDICTOR MSPN \
TEST.IMS_PER_BATCH 1 \
DTYPE "float32" \
GLOVE_DIR /home/MSPN/datasets/vg/glove \
MODEL.PRETRAINED_DETECTOR_CKPT /home/MSPN/datasets/vg/detector_model/pretrained_faster_rcnn/model_final.pth \
MODEL.WEIGHT checkpoints/MSPN_PredCls/model_final.pth \
OUTPUT_DIR checkpoints/MSPN_PredCls \
TEST.ALLOW_LOAD_FROM_CACHE False
```
## Device
All our experiments are conducted on one NVIDIA GeForce RTX 3090, if you wanna run it on your own device, make sure to follow distributed training instructions in Scene-Graph-Benchmark.pytorch.

## The Trained Model Weights
We provide the weights for the model. Due to random seeds and machines, they are not completely consistent with those reported in the paper, but they are within the allowable error range.

| Model |	mR@20 |	mR@50 |	mR@100 |	Google Drive |
| :--: | :--: | :--: | :--: | :--: |
|PE-Net (PredCls)	| 29.8 |	35.4 |	37.6 |	Model Link | Log Link |
|PE-Net (SGCls) |	16.8 |	20.1 |	21.4 |	Model Link | Log Link |
|PE-Net (SGDet) |	13.5 |	15.8 |	16.2 |	Model Link | Log Link |

## Tips
We use the rel_nms operation provided by RU-Net and HL-Net in PredCls and SGCls to filter the predicted relation predicates, which encourages diverse prediction results.

## Help
Be free to contact me () if you have any questions!

## Acknowledgement

The code is implemented based on Scene-Graph-Benchmark.pytorch.

## Citation
```python
@inproceedings{
  title={},
  author={},
  booktitle={},
  pages={},
  year={}

}
```