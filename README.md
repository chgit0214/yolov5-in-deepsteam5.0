# yolov5

The Pytorch implementation is [ultralytics/yolov5](https://github.com/ultralytics/yolov5).

## Config

- Choose the model s/m/l/x by `NET` macro in yolov5.cpp
- Input shape defined in yololayer.h
- Number of classes defined in yololayer.h, **DO NOT FORGET TO ADAPT THIS, If using your own model**
- FP16/FP32 can be selected by the macro in yolov5.cpp
- GPU id can be selected by the macro in yolov5.cpp
- NMS thresh in yolov5.cpp
- BBox confidence thresh in yolov5.cpp
- Batch size in yolov5.cpp

# Geneate yolov5 engine model

use <https://github.com/wang-xinyu/tensorrtx> yolov5 to generate engine model

### Important Note:

You should replace yololayer.cu file in tensorrtx/yolov5

## How to Run, yolov5s as example

```
1. generate yolov5s.wts from pytorch with yolov5s.pt, or download .wts from model zoo

git clone https://github.com/wang-xinyu/tensorrtx.git
git clone https://github.com/ultralytics/yolov5.git
// download its weights 'yolov5s.pt'
// copy tensorrtx/yolov5/gen_wts.py into ultralytics/yolov5
// ensure the file name is yolov5s.pt and yolov5s.wts in gen_wts.py
// go to ultralytics/yolov5
python gen_wts.py
// a file 'yolov5s.wts' will be generated.

2. build tensorrtx/yolov5 and run

// put yolov5s.wts into tensorrtx/yolov5
// go to tensorrtx/yolov5
// ensure the macro NET in yolov5.cpp is s
mkdir build
cd build
cmake ..
make
sudo ./yolov5 -s             // serialize model to plan file i.e. 'yolov5s.engine'
3. check the images generated, as follows. _zidane.jpg and _bus.jpg
mv libmyplugins.so ../Deepstream5.0/
mv yolov5s.engine ../Deepstream5.0/
4. build deepstream custom lib
cd Deepstream 5.0/nvdsinfer_custom_impl_Yolo
make  //We can get libnvdsinfer_custom_impl_Yolo.so here.
5.
After build yolov5 plugin, modify 'config_infer_primary_yoloV5.txt' in Deepstream 5.0 Directory.

-- a).In Line 58. "parse-bbox-func-name=NvDsInferParseCustomYoloV5" // This is the bbox parse function name.
-- b).In Line 59. "custom-lib-path" // This is DeepStream plugin path.
-- c).In Line 56. Comment "#cluster-mode=2". Becase we use custom NMS function.
6.How to run it
LD_PRELOAD=./libcustomOp.so deepstream-app -c <app-config>
```

## More Information



