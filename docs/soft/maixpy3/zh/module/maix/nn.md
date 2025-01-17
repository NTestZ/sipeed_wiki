---
title: MaixPy3 nn模块(maix.nn)
keywords: MaixPy3, maix.nn, MaixPy3运行模型, maix.nn API
desc: MaixPy3 nn模块 API文档, 以及使用说明
---

>! API 仍处于非稳定状态, 可能在未来会有小幅改动

## maix.nn 基本使用介绍

* 准备模型

比如从 maixhub 下载, 这里以边缘检测模型为例, 先[下载模型](https://maixhub.com/modelInfo?modelId=24)(需要先注册登录)

* 准备一张 `224 x 224` 分辨率的图像, 比如这里放到了开发板文件系统的`/root/test.png`位置

* 运行代码, 将下面的代码保存到开发板的`test_model.py`中, 然后运行`python test_model.py`

其中最重要的就是`m = nn.load`和`m.forward()`两个函数, 即加载模型, 和进行模型前向推理

```python
from maix import nn, display
from PIL import Image
import numpy as np

test_jpg = "/root/test.png"

model = {
    "param": "/root/models/sobel_int8.param",
    "bin": "/root/models/sobel_int8.bin"
}

input_size = (224, 224, 3)
output_size = (222, 222, 3)

options = {
    "model_type":  "awnn",
    "inputs": {
        "input0": input_size
    },
    "outputs": {
        "output0": output_size
    },
    "mean": [127.5, 127.5, 127.5],
    "norm": [0.0078125, 0.0078125, 0.0078125],
}
print("-- load model:", model)
m = nn.load(model, opt=options)
print("-- load ok")

print("-- read image")
img = Image.open(test_jpg).resize(input_size[:2])
print("-- read image ok")
print("-- forward model with image as input")
out = m.forward(img, quantize=True)
print("-- forward ok")
out = out.astype(np.float32).reshape(output_size)
out = (np.abs(out) * 255 / out.max()).astype(np.uint8)
img2 = Image.fromarray(out, mode="RGB")

display.show(img2)
```

## 方法 maix.nn.load()

加载模型, 返回 `maix.Model` 对象

### 参数

* `model_path`: 模型路径, 可以是字符串或者字典的形式, 目前只支持字典形式
比如:
```python
{
    "param": "/root/models/sobel_int8.param",
    "bin": "/root/models/sobel_int8.bin"
}
```

* `opt`: 设置项, 字典形式, 包括了:
  * `model_type`: 模型类别, 目前仅支持 `awnn`
  * `inputs`: 输入层, 字典形式, 关键字是层名称, 为字符串, 如果是加密模型, 需要使用整型; 值是层形状, 为一个`tuple`类型:`(h, w, c)`. 目前只支持单层输入层(未来会支持多层输入, 欢迎提交 `PR`)
  比如:
```python
    # 未加密模型
    "inputs": {
        "input0": (224, 224, 3)
    }
    # 加密模型
    "inputs": {
        0: (224, 224, 3)
    }
```
  * `outputs`: 输出层, 同理输入层. 支持多层输出
  * `mean`: 如果在`forward`使参数`quantize=True`, 则会使用这个参数对输入数据进行归一化, 计算方法为`(x - mean) * norm`; 格式为`list` 或者 `float`(未支持, 欢迎提交 PR)
  * `norm`: 看`mean`

### 返回值

返回 `maix.Model` 对象


## 类 maix.Model

包含了一系列神经网络操作,  `maix.nn.load()` 会返回其对象

### maix.Model.forward()

只能由具体的对象调用, 不能类直接调用

#### 参数

* `inputs`: 输入, 可以是`Pillow`的`Image`对象, 也可以是`HWC`排列的`bytes`对象, 也可以是`numpy.ndarray`对象, 多层输入使用`list`(还未支持)
>! 这个参数未来可能会进行优化
* `quantize`: 为`True`, 会使用 `load()` 时 `opt` 的`mean norm`参数对数据进行归一化, 并进行`int8`量化； `False`则不会对输入数据进行处理, 则输入需要先自己手动规范量化到`-128~127`范围.  
>! 这个参数未来可能会进行优化, 将归一化和量化分开

* `layout`: `"hwc"` 或者 `"chw"`(默认, 推荐)
* `debug`: 输出`debug`信息, 包含了底层`forward`用时等

#### 返回值

特征图, 如果是单层输出, 是一个浮点类型的 `numpy.ndarray` 对象, 如果是多层输出, 会是一个`list`对象, 包含了多个`numpy.ndarray`对象.


### maix.Model.__del__()

删除对象, 内存回收时会自动调用这个函数, 会释放模型占用的资源
```python
del m
```

## 模块 maix.nn.decoder

`nn` 后处理模块, 集成了常见的模型的后处理, 使用 `forward` 进行模型推理后得到特征图输出, 使用这个模块下的方法对输出的特征图进行后处理

### 类 maix.nn.decoder.Yolo2

`YOLO V2` 的后处理模块, 使用时需要创建一个对象,调用`run`方法对模型推理输出进行解码得到物体的坐标和类别.

等价于如下`python`伪代码:

```python
class Yolo2:
    def __init__(self, class_num, anchors, net_in_size=(224, 244), net_out_size=(7, 7)):
        pass

    def run(self, fmap, nms = 0.3, threshold = 0.5, img_size = None):
        boxes = []
        probs = []
        for x, y, w, h, _probs in valid_results:
            if img:
                x *= img_size[0]
                y *= img_size[1]
                w *= img_size[0]
                h *= img_size[1]
                x, y, w, h = int(x), int(y), int(w), int(h)
            boxes.append([x, y, w, h])  # item type is float if img_size == 0, else int type
            probs.append([max_probs_index, _probs]) # probs is list type, item type is float
        return [boxes, probs]

```

使用时:

```python
from maix.nn import decoder

labels = ["A", "B", "C"]
anchors = [1.19, 1.98, 2.79, 4.59, 4.53, 8.92, 8.06, 5.29, 10.32, 10.65]

yolo2_decoder = decoder.Yolo2(len(labels), anchors)
yolo2_decoder.run(bytes([0]*10))

...

out = m.forwar(img, layout="hwc")
boxes, probs = yolo2_decoder.run(out, thres=0.5, nms=0.3, img_size=(img.width, img.height))
for i, box in enumerate(boxes):
    class_id = probs[i][0]
    prob = probs[i][1][class_id]
    disp_str = "{}:{:.2f}%".format(labels[class_id], prob*100)
    print("final box: {}, {}".format(box, disp_str))

```

#### maix.nn.decoder.Yolo2.__init__()

构造对象时会自动调用

##### 参数


* `class_num`: 类别数量
* `anchors`: 预选框, `list` 类型, 数量为偶数, 必须要和训练时使用的`anchors` 相同, 也就是说跟模型绑定的参数, 如果你不知道, 请找提供模型的人提供
* `net_in_size`: 网络输入层分辨率, 默认`(224, 244)`
* `net_out_size`: 网络输出层分辨率, 默认 `(7, 7)`



#### maix.nn.decoder.Yolo2.run()

执行解码(后处理), 只能对象进行调用, 不能类直接调用

##### 参数

* `fmap`: 网路输出的特征图, 一般是`forward` 函数的结果
* `nms`: 非极大值抑制(Non-Maximum Suppression), 用来去掉重复的框, `IOU`(两个框的重叠区域)大于这个值就只取概率大的一个, 取值范围:`[0, 1]`, 默认值为 `0.3`
* `threshold`: 置信度阈值, 大于这个值才认为物体有效, 取值范围:`[0, 1]`, 默认 `0.5`
* `img_size`: 源图像大小, `tuple`类型, 比如`(320, 240)`, 这会使返回值的`box` 坐标自动计算为相对于源图的坐标, 并且类型为整型, 默认`None` 则不会计算, `box` 返回相对值(百分比), 浮点类型, 范围:`[0, 1]`


##### 返回值

`[boxes, probs]`, `list` 类型, 可以参考上面的使用例子, 其中:

* `boxes`: `list` 类型, 每个元素也是一个`list`, 包含`[x, y, w, h]`, 分别代表了框左上角坐标, 以及框的宽高
* `probs`: `list` 类型, 每个元素也是一个`list`, 包含`[max_prob_idx, all_classes_probs]`
  * `all_classes_probs`: `list` 类型, 包含了该框所有类别的置信度
  * `max_prob_idx`: 整型, 代表了`all_classes_probs`中最大值的下标, 取值范围: `[0, classes_num - 1]`



