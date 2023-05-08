# VehicleReID

Kód bol z modifikovaný z [AIC21-MTMC](https://github.com/LCFractal/AIC21-MTMC)

## inštalácia
`pip install -r requirements.txt`

## Datasety

dataset pre re-identifikáciu vozidiel [AIC21_Track2_ReID](https://www.aicitychallenge.org/2021-track2-download/) a [VeRi-776](https://nextcloud.fit.vutbr.cz/s/mWm5sjkA5mFBe3y)

dataset pre MTMC vozidiel [AIC21_Track3_MTMC_Tracking](https://www.aicitychallenge.org/2021-track3-download/)

dataset pre MTMC ľudí [AIC23](https://www.aicitychallenge.org/2023-track1-download/)


## Tvorba datasetu

vytvorenie datasetu vozidiel `python SplitDatasetAIC21.py VehicleReID/reid_training/datasets/AIC21` 

vytvorenie datasetu ľudí `python label.py` a `python createPeopleDataset.py` (potrebné nastaviť cesty root_dir)

## Detekcia
`cd ./detector/yolov5/ && pip install -r requirements.txt`

potrebné stiahnuť yolov5x model predtrénovaný na COCO [yolov5x model](https://github.com/ultralytics/yolov5/releases/download/v4.0/yolov5x.pt)

potrebná modifikácia ciest v `aic.yml`

generovanie obrázkov z videí: `bash gen_images_aic.py aic.yml`

spustenie detekcie: `bash gen_det.sh aic.yml`

## Re-identifikácia
`cd ./reid_training && pip install -r requirements.txt`

stiahnuť predtrénovane modely (resnet101_ibn_a-59ea0ac6.pth, densenet169_ibn_a-9f32c161.pth, resnext101_ibn_a-6ace051d.pth and se_resnet101_ibn_a-fabed4e2.pth) [tu](https://github.com/XingangPan/IBN-Net)

transformerový model (jx_vit_base_p16_224-80ecf9dd.pth) [tu](https://github.com/rwightman/pytorch-image-models/releases/download/v0.1-vitjx/jx_vit_base_p16_224-80ecf9dd.pth)

### Trénovanie a testovanie

potrebná modifikáciaciest v yml súboroch `configs/stage1/*.yml` a `configs/stage2/*.yml`

#### ResNext101-IBN-a
trénovanie stage1 (baseline model) `python train.py --config_file configs/stage1/resnext101a_384.yml MODEL.DEVICE_ID "('0')"`

trénovanie stage2 (UDA) `python train_stage2_v1.py --config_file configs/stage2/resnext101a_384.yml MODEL.DEVICE_ID "('0')" OUTPUT_DIR './logs/stage2/resnext101a_384/v1'`

testovanie `python test.py --config_file configs/stage2/resnext101a_384.yml MODEL.DEVICE_ID "('0')" TEST.WEIGHT './logs/stage2/resnext101a_384/v1/resnext101_ibn_a_2.pth' OUTPUT_DIR './logs/stage2/resnext101a_384/v1'`

#### trénovanie modelu kamery a orientácie

trénovanie kamery `python train_cam.py --config_file configs/camera_view/camera_101a.yml`

trénovanie orientácie `python train_view.py --config_file configs/camera_view/view_101a.yml`

získanie výsledkov `python ensemble.py`

vizualizácia výsledkov `python vis_txt_result.py`

## MCMVT

Potrebné modifikovať yml súbory v priečinku `./config`.

Pre generovanie výsledkov z MCMVT spusti `bash MCMVT.sh`. Výsledky sú uložené v `./reid_bidir/reid-matching/tools/`
