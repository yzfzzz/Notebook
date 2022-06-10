# Numpy入门及图像的基本操作

[TOC]



## 访问和改变像素点的值

- **单独对一个像素操作**

```python
# 通过行列坐标来访问像素值
# 定义图片的一个像素点
px = img[100,100]
# 打印出这个像素的BGR数值
print(px)
```

- **仅访问像素点的单个通道**

```python
# 仅访问蓝色像素
blue = img[100,100,0]
print(blue)
```

- **改变像素点的颜色**

```python
# 对像素点单独变色
img[100,100] = [255,255,255]
print(img[100,100])
```

- **调用函数改变像素**

```python
print(img.item(10,10,2))
img.itemset((10,10,2),100)
print(img.item(10,10,2))
```



## 访问图片属性

- **返回图片大小（长宽像素点个数），颜色通道数**

```python
print(img.shape)
```

- **返回总像素点个数**

```python
print(img.size)
```

- **返回图像数据类型**

```python
print(img.dtype)
```



## 为图像设置边框（填充）

如果要在图像周围创建边框（如相框），则可以使用 **cv.copyMakeBorder()** 函数

对于这个函数

```python
def copyMakeBorder(src: Any,
                   top: Any,
                   bottom: Any,
                   left: Any,
                   right: Any,
                   borderType: Any,
                   dst: Any = None,
                   value: Any = None) -> None
```



> **`copyMakeBorder()`函数**
>
> **功能**：如果要在图像周围创建边框（如相框）
>
> **输入参数**：
>
> 1. src:需要填充的图像,即图像名
> 2. top：图像上面填充边界的长度
> 3. bottom：图像上下面填充边界的长度
> 4. left：图像左面填充边界的长度
> 5. right ：图像右面填充边界的长度
> 6. borderType：边界的类型
>
> **示例**：
>
> `replicate = cv2.copyMakeBorder(img,300,300,300,300,cv2.BORDER_REPLICATE)`
> `reflect = cv2.copyMakeBorder(img,100,100,100,100,cv2.BORDER_REFLECT)`



### 边界类型borderType

- **BORDER_REPLICATE**

复制法，即复制最边缘的像素。例如：aaaa|abcdefg|ggggg

<img src="https://s3.bmp.ovh/imgs/2022/02/6f62d91d145ec04b.png" style="zoom:67%;" />

可以看到四边的像素点颜色都一样；原因是程序把最靠边的那个像素点都复制粘贴过去了



- **BORDER_REFLECT**

反射法,即以最边缘的像素为对称轴。例如：fedcba|abcdefg|gfedec

<img src="https://s3.bmp.ovh/imgs/2022/02/466beb64ee1be84f.png" style="zoom:67%;" />

可以看到，以边界为轴，各个像素点镜像复制过去



- **BORDER_REFLECT_101**

反射法,也是最边缘的像素为对称轴，但与BORDER_REFLECT有区别。例如：fedcb|abcdefg|fedec

<img src="https://s3.bmp.ovh/imgs/2022/02/9147ec2050c8209a.png" style="zoom:67%;" />



- **BORDER_WRAP**

外包装法，即以图像的左边界与右边界相连，上下边界相连。例如：cdefgh|abcdefgh|abcdefg

<img src="https://s3.bmp.ovh/imgs/2022/02/691fa3c972069e4c.png" style="zoom:67%;" />



- **BORDER_CONSTANT**

常量法

<img src="https://s3.bmp.ovh/imgs/2022/02/b6209ab02bb12db9.png" style="zoom:67%;" />





### 完整代码

```python
import cv2 as cv
import numpy as np
from matplotlib import pyplot as plt
BLUE = [255,0,0]
img1 = cv.imread('opencv-logo.png')
replicate = cv.copyMakeBorder(img1,10,10,10,10,cv.BORDER_REPLICATE)
reflect = cv.copyMakeBorder(img1,10,10,10,10,cv.BORDER_REFLECT)
reflect101 = cv.copyMakeBorder(img1,10,10,10,10,cv.BORDER_REFLECT_101)
wrap = cv.copyMakeBorder(img1,10,10,10,10,cv.BORDER_WRAP)
constant= cv.copyMakeBorder(img1,10,10,10,10,cv.BORDER_CONSTANT,value=BLUE)
plt.subplot(231),plt.imshow(img1,'gray'),plt.title('ORIGINAL')
plt.subplot(232),plt.imshow(replicate,'gray'),plt.title('REPLICATE')
plt.subplot(233),plt.imshow(reflect,'gray'),plt.title('REFLECT')
plt.subplot(234),plt.imshow(reflect101,'gray'),plt.title('REFLECT_101')
plt.subplot(235),plt.imshow(wrap,'gray'),plt.title('WRAP')
plt.subplot(236),plt.imshow(constant,'gray'),plt.title('CONSTANT')
plt.show()
```

