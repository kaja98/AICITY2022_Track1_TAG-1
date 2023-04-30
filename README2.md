# inštalácia
`pip install -r requirements.txt`

## Detekcia
`cd ./detector/yolov5/ && pip install -r requirements.txt`

potrebné stiahnuť yolov5x model predtrénovaný na COCO [yolov5x model](https://github.com/ultralytics/yolov5/releases/download/v4.0/yolov5x.pt)

generovanie obrázkov z videí: `bash gen_images_aic.py aic.yml`

spustenie detekcie: `bash gen_det.sh aic.yml`

## Re-identifikácia
`cd ./reid_training && pip install -r requirements.txt`

stiahnuť predtrénovane modely (resnet101_ibn_a-59ea0ac6.pth, densenet169_ibn_a-9f32c161.pth, resnext101_ibn_a-6ace051d.pth and se_resnet101_ibn_a-fabed4e2.pth) [tu](https://github.com/XingangPan/IBN-Net)

transformerový model (jx_vit_base_p16_224-80ecf9dd.pth) [tu](https://github.com/rwightman/pytorch-image-models/releases/download/v0.1-vitjx/jx_vit_base_p16_224-80ecf9dd.pth)

### Trénovanie a testovanie
#### ResNext101-IBN-a
`python train.py --config_file configs/stage1/resnext101a_384.yml MODEL.DEVICE_ID "('0')"`

`python train_stage2_v1.py --config_file configs/stage2/resnext101a_384.yml MODEL.DEVICE_ID "('0')" OUTPUT_DIR './logs/stage2/resnext101a_384/v1'`

`python test.py --config_file configs/stage2/resnext101a_384.yml MODEL.DEVICE_ID "('0')" TEST.WEIGHT './logs/stage2/resnext101a_384/v1/resnext101_ibn_a_2.pth' OUTPUT_DIR './logs/stage2/resnext101a_384/v1'`

trénovanie modelu kamery a orientácie

`python train_cam.py --config_file configs/camera_view/camera_101a.yml`

`python train_view.py --config_file configs/camera_view/view_101a.yml`

získanie výsledkov
`python ensemble.py`

vizualizácia výsledkov `python vis_txt_result.py`

## MCMVT

Potrebné modifikovať yml súbory v priečinku `./config`.

Pre generovanie výsledkov z MCMVT spusti `bash MCMVT.sh`. Výsledky sú uložené v `./reid_bidir/reid-matching/tools/`

## Tvorba datasetu

vytvorenie datasetu vozidiel `python SplitDatasetAIC21.py`

vytvorenie datasetu ľudí `python label.py` a `python createPeopleDataset.py`

