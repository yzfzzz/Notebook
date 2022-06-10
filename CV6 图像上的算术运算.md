# CV6 图像上的算术运算

[TOC]



## 图像加法

OpenCV加法和Numpy加法之间有区别。OpenCV加法是饱和运算（无论多大都只能加到max值），而Numpy加法是模运算

```python
>>> x = np.uint8([250])
>>> y = np.uint8([10])
>>> print( cv.add(x,y) ) # 250+10 = 260 => 255
[[255]]
>>> print( x+y ) # 250+10 = 260 % 256 = 4
[4]
```

建议使用Opencv去添加两个图像



## 图像融合

这也是图像加法，但是对图像赋予不同的权重，以使其具有融合或透明的感觉

下面这段代码，我将两个图像融合在一起；第一幅的图像权重为0.4，第二幅图像的权重为0.6

```python
import cv2
import numpy as np

img1 = cv2.imread('rose.jpg')
img2 = cv2.imread('homework.jpg')

img1 = cv2.resize(img1,(600,600))
dst = cv2.addWeighted(img1,0.4,img2,0.6,0)
cv2.imshow('dst',dst)
cv2.waitKey(0)
cv2.destroyAllWindows()
```



> **`cv2.resize()`函数**
>
> **功能**：裁剪图片，使得两张图片的shape（或者是大小）相同。否则，会出现报错
>
> **输入参数**：
>
> 1. 图像名	
> 2. 想要裁剪的大小
>
> **参考资料**：https://blog.csdn.net/henghuizan2771/article/details/123320952?spm=1001.2014.3001.5501



- **cv2.addWeighted函数**

功能：图片融合

输入参数：1.图像1	2.图像1的权重	3.图像2	4.图像2的权重	5.融合后，每个像素点加的标量



## 按位运算（抠图）

包括按位 AND 、 OR 、 NOT 和 XOR 操作

下面我们将看到一个例子，如何改变一个图像的特定区域。 例如：把 OpenCV 的标志放在一个图像上面

怎么实现？

在我们的PS中，如果想要将一张图片的特定部分截取出来，剪切到另外一张图片上，我们需要抠图；同样在OpenCV中，我们也需要抠图

**抠图完整代码：**

```python
import cv2
import numpy as np

# 加载图片
img1 = cv2.imread('rose.jpg')
img2 = cv2.imread('homework.jpg')
# 创建ROI
rows,cols,channels = img2.shape
roi = img1[0:rows,0:cols]

# 现在创建logo的掩码，并同时创建其相反的反码
img2gray = cv2.cvtColor(img2,cv2.COLOR_BGR2GRAY)
ret,mask = cv2.threshold(img2gray,245,255,cv2.THRESH_BINARY)
mask = cv2.bitwise_not(mask)
mask_inv = cv2.bitwise_not(mask)

# 现在将ROI中的logo涂黑
img1_bg = cv2.bitwise_and(roi,roi,mask=mask_inv)

# 仅从logo图像中提取logo区域
img2_fg = cv2.bitwise_and(img2,img2,mask = mask)

# 将logo放入ROI并修改图像
dst = cv2.add(img1_bg,img2_fg)
img1[0:rows,0:cols] = dst
cv2.imshow('res',dst)
cv2.waitKey(0)
cv2.destroyAllWindows()
```

下面我们分析一下这段代码



- 首先取出图片的shape,方便定义roi（此时两张图片的shape可以不同）

```python
rows,cols,channels = img2.shape
```

- 然后将图片灰度化，创建蒙版（官方文档上是掩码，但由于PS先入为主的关系，我更愿意叫它蒙版）

```python
#功能：一刀切，将通道>245的像素点，置为255；反之，都置0
#输入参数：1.原图像 2.进行分类的阈值 3.高于（低于）阈值时赋予的新值 4.一个方法选择参数
mask = cv2.threshold(img2gray,245,255,cv2.THRESH_BINARY)
```

常用的参数有：

• cv2.THRESH_BINARY（黑白二值）
• cv2.THRESH_BINARY_INV（黑白二值反转）
• cv2.THRESH_TRUNC （得到的图像为多像素值）
• cv2.THRESH_TOZERO
• cv2.THRESH_TOZERO_INV



- 取反操作

```python
mask = cv2.bitwise_not(mask)
mask_inv = cv2.bitwise_not(mask)
```

------

*mask的图像*

<img src="https://s3.bmp.ovh/imgs/2022/02/e3516755509db77f.png" style="zoom:50%;" />

*mask_isv的图像*

<img src="https://s3.bmp.ovh/imgs/2022/02/d54693ec08b81b2b.png" style="zoom: 50%;" />



- 与运算

```python
# 现在将ROI中的logo涂黑
img1_bg = cv2.bitwise_and(roi,roi,mask = mask_inv)

# 仅从logo图像中提取logo区域
img2_fg = cv2.bitwise_and(img2,img2,mask = mask)
```

我们重点对**cv2.bitwise_and（src1,src2,dst,mask）**分析



> **`cv2.bitwise_and()`函数**
>
> **功能**：图像与操作
>
> 输入参数：
>
> 1.  src1、src2：为输入图像或标量，标量可以为单个数值或一个四元组
> 2.  dst：可选输出变量，如果需要使用非None则要先定义，且其大小与输入变量相同
> 3.  mask：图像蒙版，可选参数，为8位单通道的灰度图像，用于指定要更改的输出图像数组的元素，**即输出图像像素只有mask对应位
>
> **示例**：`img1_bg = cv2.bitwise_and(roi,roi,mask = mask_inv)`

==置元素不为0的部分才输出，否则该位置像素的所有通道分量都设置为0==



`img1_bg = cv2.bitwise_and(roi,roi,mask = mask_inv)` 将图像涂黑，表明，这块底盘属于OpenCV Logo了。在mask_inv中，logo处的像素都是黑色的（0），与运算的时候，该像素点始终为0，所以呈现黑色

<img src="https://s3.bmp.ovh/imgs/2022/02/9d266a0663757a0c.png" style="zoom:50%;" />



`img2_fg = cv2.bitwise_and(img2,img2,mask = mask)`将图1的logo颜色抠出来；在前面的mask中，logo处的像素都是白色的，即保留输出

<img src="https://s3.bmp.ovh/imgs/2022/02/70c960418a026d15.png" style="zoom:50%;" />



- **拼接**

`dst = cv2.add(img1_bg,img2_fg)`

即将两个像素的B/G/R相加



> 个人感悟：OpenCV中的抠图，需要利用好蒙版，把想要抠出来的roi的蒙版都涂黑，不需要的就涂白，然后再与运算一下，就能抠出想要的地方











