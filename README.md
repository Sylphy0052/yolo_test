# YOLO v3 のテスト

## How to Install

### darknet

```shell
git clone https://github.com/pjreddie/darknet
cd darknet
make
wget https://pjreddie.com/media/files/yolov3.weights
```

画像の認識

`./darknet detect cfg/yolov3.cfg yolov3.weights data/dog.jpg`

`./darknet detector test cfg/coco.data cfg/yolov3.cfg yolov3.weights data/dog.jpg`

複数画像の認識

`./darknet detect cfg/yolov3.cfg yolov3.weights`

Threshold(閾値)の変更

`./darknet detect cfg/yolov3.cfg yolov3.weights data/dog.jpg -thresh 0`

Tiny YOLO

```shell
wget https://pjreddie.com/media/files/yolov3-tiny.weights
./darknet detect cfg/yolov3-tiny.cfg yolov3-tiny.weights data/dog.jpg
```

動画の認識

`./darknet detector demo cfg/coco.data cfg/yolov3.cfg yolov3.weights`

`./darknet detector demo cfg/coco.data cfg/yolov3.cfg yolov3.weights <video file>`

### pytorch

```shell
git clone https://github.com/ayooshkathuria/pytorch-yolo-v3.git
cd pytorch-yolo-v3
wget https://pjreddie.com/media/files/yolov3.weights
```

画像

`python detect.py --images imgs --det det`

動画

`python video_demo.py --video <動画ファイル>`

内蔵カメラで認識

`python cam_demo.py`

## Training

### From scratch

Download Pascal VOC Data(2007-2012)

```shell
wget https://pjreddie.com/media/files/VOCtrainval_11-May-2012.tar
wget https://pjreddie.com/media/files/VOCtrainval_06-Nov-2007.tar
wget https://pjreddie.com/media/files/VOCtest_06-Nov-2007.tar
tar xf VOCtrainval_11-May-2012.tar
tar xf VOCtrainval_06-Nov-2007.tar
tar xf VOCtest_06-Nov-2007.tar
```

ラベルファイルの作成

```
*.txtファイル
<object-class> <x> <y> <width> <height>
```

```
cd scripts
wget https://pjreddie.com/media/files/voc_label.py
python voc_label.py
```

トレーニングリストの作成

`cat 2007_train.txt 2007_val.txt 2012_*.txt > train.txt`

cfgの変更

`vi cfg/voc.data`

```
1 classes= 20
2 train  = <path-to-voc>/train.txt
3 valid  = <path-to-voc>2007_test.txt
4 names = data/voc.names
5 backup = backup
```

事前学習済み重みのダウンロード

`wget https://pjreddie.com/media/files/darknet53.conv.74`

モデルのトレーニング

`./darknet detector train cfg/voc.data cfg/yolov3-voc.cfg darknet53.conv.74`

### COCOを用いてトレーニング

cocoデータの取得

```
cp scripts/get_coco_dataset.sh data
cd data
bash get_coco_dataset.sh
```

`vi cfg/coco.data`

```
1 classes= 80
2 train  = <path-to-coco>/trainvalno5k.txt
3 valid  = <path-to-coco>/5k.txt
4 names = data/coco.names
5 backup = backup
```

`vi cfg/yolo.cfg`

```
[net]
# Testing
# batch=1
# subdivisions=1
# Training
batch=64
subdivisions=8
....
```

モデルをトレーニングする

`./darknet detector train cfg/coco.data cfg/yolov3.cfg darknet53.conv.74`

with GPU

`./darknet detector train cfg/coco.data cfg/yolov3.cfg darknet53.conv.74 -gpus 0,1,2,3`

from checkpoint

`./darknet detector train cfg/coco.data cfg/yolov3.cfg backup/yolov3.backup -gpus 0,1,2,3`

open image dataset

```shell
wget https://pjreddie.com/media/files/yolov3-openimages.weights
./darknet detector test cfg/openimages.data cfg/yolov3-openimages.cfg yolov3-openimages.weights
```