# CV7 颜色追踪和图像阈值

[TOC]



## 颜色追踪

HSV:H（hue）是色调，S（saturation)是饱和度，V（value）表示黑暗的程度

因为在HSV中比在BGR颜色空间中更容易表示颜色，所以我们需要将BGR图像转换成HSV，用来提取一个有颜色的对象

在下面这段程序中，我们将提取一个红色的对象

方法如下：

1. 取视频的每一帧
2. 转换颜色空间（从BGR到HSV）
3. 对HSV图像设置红色的阈值
4. 单独提取红色对象

```python
import cv2
import numpy as np

cap = cv2.VideoCapture(0)

while(1):
    # 读取帧
    _,frame = cap.read()
    # 转化颜色空间BGR到HSV
    hsv = cv2.cvtColor(frame,cv2.COLOR_BGR2HSV)
    # 定义HSV蓝色的范围
    lower_blue = np.array([0,43,46])
    upper_blue = np.array([2,255,250])
    # 设置HSV的阈值使得只取蓝色
    mask = cv2.inRange(hsv,lower_blue,upper_blue)
    # 将蒙版和图像逐像素相加
    res = cv2.bitwise_and(frame,frame,mask=mask)

    cv2.imshow('frame',frame)
    cv2.imshow('res',res)
    cv2.imshow('mask',mask)

    k = cv2.waitKey(5) & 0xFF
    if k == 27:
        break

cv2.destroyAllWindows()
```

> **`cv2.inRange()`函数**
>
> **功能**：设置蒙版，在这个阈值内的颜色，在蒙版上显示白色；在阈值外的颜色，在蒙版上显示为黑色
>
> **输入参数**：
>
> 1. 图片名	
> 2. 阈值min	
> 3. 阈值max
>
> **示例**：`mask = cv2.inRange(hsv,lower_blue,upper_blue)`



`lower_blue = np.array([0,43,46])  upper_blue = np.array([2,255,250])`设定阈值上下限

效果如下：

<img src="https://s3.bmp.ovh/imgs/2022/02/28797e42d03bbd10.png"  />

> 注意 图像中有一些噪点，是因为人体皮肤的颜色有些泛红。我们将在后面的学习中看到如何删除它们。 这是对象跟踪中最简单的方法。一旦学习了颜色阈值追踪的方法，你就可以做很多事情，例如2017年全国电赛板球跟踪系统，对小球颜色定位；2021年全国电赛互联网测量系统，题目测量荧光笔的角度，线长度均可解决



### 如何找到要追踪的HSV值？

你可以使用相同的函数**`cv.cvtColor()`**。你只需传递你想要的BGR值，而不是传递图像

```python
import cv2
import numpy as np

green = np.uint8([[[0,255,0 ]]])
hsv_green = cv2.cvtColor(green,cv2.COLOR_BGR2HSV)
print( hsv_green )
```



## 全局阈值和自适应阈值

**全局阈值**：设定一个固定的值，每一个像素去比较这个值，简单暴力

```python
import cv2 as cv
import numpy as np
from matplotlib import pyplot as plt

img = cv.imread('gradient.png',0)
ret,thresh1 = cv.threshold(img,127,255,cv.THRESH_BINARY)
ret,thresh2 = cv.threshold(img,127,255,cv.THRESH_BINARY_INV)
ret,thresh3 = cv.threshold(img,127,255,cv.THRESH_TRUNC)
ret,thresh4 = cv.threshold(img,127,255,cv.THRESH_TOZERO)
ret,thresh5 = cv.threshold(img,127,255,cv.THRESH_TOZERO_INV)
titles = ['Original Image','BINARY','BINARY_INV','TRUNC','TOZERO','TOZERO_INV']
images = [img, thresh1, thresh2, thresh3, thresh4, thresh5]

for i in range(6):
	plt.subplot(2,3,i+1),plt.imshow(images[i],'gray')
	plt.title(titles[i])
	plt.xticks([]),plt.yticks([])
plt.show()
```



> **`cv.threshold`函数**
>
> **功能**：对图像进行简单的二值化
>
> **输入参数**：1.源图像，它**应该是灰度图像**
>
> ​				   2.阈值，用来对像素进行分类
>
> ​				   3.分配给超过阈值的像素值的最大值
>
> ​				   4.简单阈值的类型
>
> **示例**：`ret,thresh5 = cv.threshold(img,127,255,cv.THRESH_TOZERO_INV)`





### 注意事项Bug1

在一些资料里，常会有`for i in xrange(6):`这段代码；当你运行时会发现编译器报错`NameError: name 'xrange' is not defined`

- 原因：目前用户的python版本为python 3.5，而xrange适用于2.7版，在3版中range与xrange已经合并为range了。

- 解决方法：把用到的程序里的xrange( )函数全部换为range( ) 



### 简单阈值的类型

- cv2.THRESH_BINARY：大于阈值的部分像素值变为maxval，其他变为0 
- cv2.THRESH_BINARY_INV：大于阈值的部分变为0，其他部分变为最大值 
- cv2.THRESH_TRUNC：大于阈值的部分变为阈值，其余部分不变 
- cv2.THRESH_TOZERO：大于阈值的部分不变，其余部分变为0 
- cv2.THRESH_TOZERO_INV：大于阈值的部分变为0，其余部分不变



### 效果图

![](https://s3.bmp.ovh/imgs/2022/02/e29dd847c62695f9.png)



**自适应阈值**：根据像素的邻域块的像素值分布来确定该像素位置上的二值化阈值 。

这样做的好处：

> 1. 每个像素位置处的二值化阈值不是固定不变的，而是由其周围邻域像素的分布来决定的。
> 2.  亮度较高的图像区域的二值化阈值通常会较高，而亮度低的图像区域的二值化阈值则会相适应的变小。
> 3. 不同亮度、对比度、纹理的局部图像区域将会拥有相对应的局部二值化阈值。

这为光照度变化的图像提供了更好的结果



```python
import cv2
import numpy as np
from matplotlib import pyplot as plt

img = cv2.imread('test2.jpg',0)
img = cv2.medianBlur(img, 5)

ret,thresh1 = cv2.threshold(img,127,255,cv2.THRESH_BINARY)
thresh2 = cv2.adaptiveThreshold(img, 255, cv2.ADAPTIVE_THRESH_MEAN_C, cv2.THRESH_BINARY,11,2)
thresh3 = cv2.adaptiveThreshold(img,255,cv2.ADAPTIVE_THRESH_GAUSSIAN_C,cv2.THRESH_BINARY,11,2)

titles = ['Original Image','BINARY','Adaptive Mean Thresholding','Adaptive Gaussian Thresholding']
image = [img,thresh1,thresh2,thresh3]

for i in range(4):
    plt.subplot(2,2,i+1),plt.imshow(image[i],'gray')
    plt.title(titles[i])
    plt.xticks([]),plt.yticks([])
plt.show()
```

> **`cv2.adaptiveThreshold`函数**、
>
> 功能：自动、智能地对图像二值化
>
> 输入参数：1.源图像			2.单个像素转换后的最大值
>
> ​				   3.自适应方法：cv2.ADAPTIVE_THRESH_MEAN_C（平均法）或			 	 
>
> ​					cv2.ADAPTIVE_THRESH_GAUSSIAN_C（高斯法）
>
> ​				   4.阈值化类型，THRESH_BINARY或THRESH_BINARY_INV二选一
>
> ​				   5.计算某个像素使用的阈值时采用的窗口大小，奇数值
>
> ​				   6.C：平均值或加权平均值减去的常量值；可以为正值，0或负值

### 注意事项Bug2

输入图像一定要是灰度图`img = cv2.imread('test2.jpg',0)`，否则编译器报错

```python
 error: (-215:Assertion failed) src.type() == CV_8UC1 in function 'cv::adaptiveThreshold'
```



### 中值滤波操作

> **`cv2.medianBlur()`函数**
>
> **功能**：中值滤波操作
>
> **输入参数**：1.图片名
>
> ​				   2.中值模糊的强度（必须是奇数）；强度小噪点不能消除，强度大整个图片都会过于模糊
>
> **示例**：`img = cv2.medianBlur(img, 5)`



在Python-OpenCV中模糊函数的使用虽然简单，但真正掌握它难度还是较大的，下面我简单讲述一下

中值滤波是基于排序统计理论的一种能有效抑制噪声的非线性信号处理技术。它也是一种邻域运算，类似于卷积，但是计算的不是加权求和，而是把数字图像或数字序列中一点的值用该点的一个邻域中各点值的中值代替，让周围像素灰度值的差比较大的像素改取与周围的像素值接近的值，从而可以消除孤立的噪声点。

![](https://s3.bmp.ovh/imgs/2022/02/d5d0c5cf8dc4fe50.png)

下面是一张噪点较多的图片

<img src="D:\opencv\CV\HSV\dog.jpg" alt="dog" style="zoom:67%;" />



模糊后

<img src="https://gitee.com/yzfzzz/image-drawing-bed/raw/master/D:%5CGit%5CImage%E5%9B%BE%E5%BA%8A/f57103b1208c40ffa973b3f7915de390.png" alt="f57103b1208c40ffa973b3f7915de390" style="zoom:67%;" />

### 模糊前后对比

<img src="https://s3.bmp.ovh/imgs/2022/02/6527c9f21fdd5cee.png" style="zoom:67%;" />



### 效果图

<img src="https://s3.bmp.ovh/imgs/2022/02/6034bb380ec5e670.png" style="zoom:67%;" />





## 结语

本篇篇幅较长，所以一些算法，例如大津算法（OTSU）,三角算法（TRIANGLE），平均值算法，高斯算法下一篇再讲，感觉难度较大，先等我把它们研究通透

自学链接：https://itpcb.com/a/1875371
