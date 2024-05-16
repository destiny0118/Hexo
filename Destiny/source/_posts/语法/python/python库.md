python库

# tqdm

[__init__](https://tqdm.github.io/docs/tqdm/)

# logging

软件运行过程中跟踪一些事件发生

[python学习笔记二:(python3 logging函数中format说明) - 陌生初见 - 博客园 (cnblogs.com)](https://www.cnblogs.com/yoyoblogs/p/10948052.html)



# argparser

[argparse基本用法](https://blog.csdn.net/yy_diego/article/details/82851661)



# PIL

```python
from PIL import Image
from matplotlib import pyplot as plt

#显示图片，图片尺寸，通道数
img=Image.open("Image/1.jpg")
plt.imshow(img)
img.size,img.layers
```

```python
#图片变为numpy对象，W*H*C,范围[0-256]
import numpy as np
img_ndarray=np.asarray(img)
#W*H*C
img_ndarray.shape

```



```python
#numpy转化为torch，C*W*H
trans=torchvision.transforms.ToTensor()
img2_trans=trans(img2_n)
img2_trans.shape
```




# python程序打包成exe

```
pip install pyinstaller
pyinstaller xxx.py
```
**常用可选项及说明：**

- **-F：**打包后只生成单个exe格式文件；
- **-D：**默认选项，创建一个目录，包含exe文件以及大量依赖文件；
- **-c：**默认选项，使用控制台(就是类似cmd的黑框)；
- **-w：**不使用控制台；
- **-p：**添加搜索路径，让其找到对应的库；
- **-i：**改变生成程序的icon图标。