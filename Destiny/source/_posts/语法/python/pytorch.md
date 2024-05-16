# pytorch

# torch Tensor

```python
import torch

x=torch.arange(12)
x
tensor([ 0,  1,  2,  3,  4,  5,  6,  7,  8,  9, 10, 11])

#张量的形状
x.shape

#含有元素个数
x.numel()

#维度自动推断
X=x.reshape(3,4)=x.reshape(-1,4)=x.reshape(3,-1)

torch.zeros((2,3,4))
torch.ones(2,3,4)
torch.randn(3,4)
```



## 转置函数

在`pytorch`中转置用的函数就只有这两个

1. `transpose()`
2. `permute()`

##### `transpose()`

```
torch.transpose(input, dim0, dim1, out=None) → Tensor
```

函数返回输入矩阵`input`的转置。交换维度`dim0`和`dim1`

参数:

- input (Tensor) – 输入张量，必填
- dim0 (int) – 转置的第一维，默认0，可选
- dim1 (int) – 转置的第二维，默认1，可选

注意**只能有两个相关的交换**的位置参数。

##### `permute()`

```
参数：

dims (int…*)-换位顺序，必填
```

## 常用函数

one_hot

![image-20220414102235414](https://gitee.com/destiny0118/picgo/raw/master/202204141022460.png)

# 图像转换

当用PIL中的**Image.open**打开RGB图像时，image.size = (w,h)
即（列，行）—>(x,y)
![在这里插入图片描述](https://raw.githubusercontent.com/destiny0118/picgo/master/img/202204251123966.png)
如果把该图载转换为易于操作的ndarray形式，则：
**image = np.asarray(image)**

转换后的image.shape=（h,w,c）c为通道数，RGB图像c=3
(h,w)为（行，列），即为（y,x）

## numpy

numpy库数组属性查看：类型、尺寸、形状、维度

```python
import numpy as np  
      
a1 = np.array([1,2,3,4],dtype=np.complex128)  
print(a1)  
print("数据类型",type(a1))           #打印数组数据类型  
print("数组元素数据类型：",a1.dtype) #打印数组元素数据类型  
print("数组元素总数：",a1.size)      #打印数组尺寸，即数组元素总数  
print("数组形状：",a1.shape)         #打印数组形状  
print("数组的维度数目",a1.ndim)      #打印数组的维度数目  
```



## PIL

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



tensor变为整数类型

```python
a = [[1.,2.],[3.,4.]]
b = torch.tensor(a)
# c = torch.tensor(b,dtype=torch.int)
c = b.clone().type(torch.int)

print(b)
print(c)
```

![image-20220413151208381](https://gitee.com/destiny0118/picgo/raw/master/202204131512427.png)

argmax：求某一维度最大值的下标

![image-20220324201852580](https://gitee.com/destiny0118/picgo/raw/master/202203242018613.png)

detach

![image-20220413110006761](https://gitee.com/destiny0118/picgo/raw/master/202204131100853.png)

# torch.nn

## Flatten

> 展平层，将1维到最后1维展开成一个向量形式

![image-20220324174809807](https://gitee.com/destiny0118/picgo/raw/master/202203241748891.png)

![image-20220324174836609](https://gitee.com/destiny0118/picgo/raw/master/202203241748637.png)

## [torch.utils.data](https://pytorch.org/docs/1.10/data.html#torch.utils.data.DataLoader)

![image-20220323213832027](https://gitee.com/destiny0118/picgo/raw/master/202203232138139.png)

![image-20220323215218953](https://gitee.com/destiny0118/picgo/raw/master/202203232152007.png)



torch.zeros

![image-20220323223004246](https://gitee.com/destiny0118/picgo/raw/master/202203232230328.png)

## 矩阵转置

![image-20220324105139852](https://gitee.com/destiny0118/picgo/raw/master/202203241051878.png)

## 损失函数

nn.CrossEntropyloss

![image-20220404215314933](https://raw.githubusercontent.com/destiny0118/picgo/master/img/202204042153058.png)

![image-20220404215358048](https://gitee.com/destiny0118/picgo/raw/master/202204042153107.png)

# optim

## SGD

![image-20220324170304790](https://gitee.com/destiny0118/picgo/raw/master/202203241703844.png)

# torchvision

## transforms

### ToTensor

![image-20220407121440273](https://gitee.com/destiny0118/picgo/raw/master/202204071214356.png)