# CV2 在OpenCV中加载彩色图像并将其显示在Matplotlib中



[TOC]

## 我的答案

```python
import cv2
import numpy as np
from matplotlib import pyplot as plt

img1 = cv2.imread('rose.jpg',cv2.COLOR_BGR2GRAY)		#以BGR格式读取图片

# opencv:BGR;  Matplotlib:RGB
# opencv:蓝0绿1红2

cv2.imwrite('homework.jpg',img1[:,:,[2,1,0]])		#将原红色通道的值写到BGR的蓝色通道，原蓝色通道的值写进BGR的R通道
												    #简单说就是将BGR改为RGB格式，并保存
    
img2 = cv2.imread('homework.jpg',cv2.COLOR_BGR2GRAY)	#读取图片
plt.imshow(img2,cmap='gray',interpolation='bicubic')	
plt.xticks([]),plt.yticks([])						#隐藏x，y轴
plt.show()

cv2.waitKey(0)
cv2.destroyAllWindows()
```

简而言之，我的答案非常low，就不对此分析



## 索引法

```python
import cv2
import numpy as np
from matplotlib import pyplot as plt

img1 = cv2.imread('rose.jpg',cv2.COLOR_BGR2GRAY)

# opencv:BGR;  Matplotlib:RGB
# opencv:蓝0绿1红2

img1 = img1[:,:,::-1]		#使用 NumPy 索引，图片的B，R通道翻转

plt.imshow(img1,cmap='gray',interpolation='bicubic')	
plt.xticks([]),plt.yticks([])
plt.show()

cv2.waitKey(0)
cv2.destroyAllWindows()
```

索引和切片操作，最后一维是颜色，-1表示翻转，颜色由红变蓝;白色颠倒不会发生变化

```python
cv2.imshow('rose', rose[:, :, ::-1])      #颜色颠倒
cv2.imshow('rose', rose[::-1, :,:])       #上下颠倒
cv2.imshow('rose', rose[:,::-1,:])        #左右颠倒
```



## 切片法

```python
import cv2
import numpy as np
import matplotlib.pyplot as plt

img = cv2.imread('rose.jpg')
b,g,r = cv2.split(img)
img2 = cv2.merge([r,g,b])
plt.subplot(121);plt.imshow(img) # expects distorted color
plt.subplot(122);plt.imshow(img2) # expect true color
plt.show()

cv2.imshow('bgr image',img) # expects true color
cv2.imshow('rgb image',img2) # expects distorted color
cv2.waitKey(0)
cv2.destroyAllWindows()
```



> **`cv2.split(img)`函数**
>
> **功能**：通过将三维数组降维的方法，分离出B/G/R的通道
>
> **输入参数**：图片
>
> **示例**：`b,g,r = cv2.split(img)`



> **`cv2.merge([r,g,b])`函数**
>
> **功能**：通过将一维数组重组升维的方法，合并出三个通道。[]里面的顺序即是图片的颜色格式
>
> **输入参数**：三个一维数组
>
> **示例**：`img2 = cv2.merge([r,g,b])`





## cvtColor法（最优解）

```python
import cv2
import numpy as np
import matplotlib.pyplot as plt

img = cv2.imread('rose.jpg')
img1=cv2.cvtColor(img,cv2.COLOR_BGR2RGB)

plt.imshow(img1,cmap='gray',interpolation='bicubic')
plt.xticks([]),plt.yticks([])
plt.show()

cv2.waitKey(0)
cv2.destroyAllWindows()
```



> **`cv2.cvtColor(img,cv2.COLOR_BGR2RGB)`函数**
>
> **功能**：简要将图像从一种颜色空间转换为另一种颜色空间
>
> **输入参数**：
>
> 1. img：已经imread的图片
> 2. cv2.COLOR_BGR2RGB：颜色转化的参数
>
> **示例**：`img1=cv2.cvtColor(img,cv2.COLOR_BGR2RGB)`

说明：在发生转变的情况下。对于RGB颜色空间，应该显式地指定通道的顺序(RGB或BGR).注意。OpenCV中的默认颜色格式通常被称为RGB，但实际上是BGR。字节反转)。因此，标准(24位)彩色图像中的第一个字节将是8位蓝色。组件，第二个字节将是绿色，第三个字节将是红色。第四，第五，和。第六个字节是第二个像素(蓝色，然后是绿色，然后是红色)，依此类推。

R、G和B通道值的常规范围是：-0到255的CV_8bit图像。-65535到65535的CV_16bit图像。-0到1的CV_32bit图像



## 代码

[在OpenCV中加载彩色图像并将其显示在Matplotlib中](https://stackoverflow.com/questions/15072736/extracting-a-region-from-an-image-using-slicing-in-python-opencv/15074748#15074748)
