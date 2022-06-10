# CV8 大津算法

[TOC]



## 大津算法简介

大津算法（OTSU）是一种确定图像二值化分割阈值的算法，由日本学者大津于1979年提出。从大津法的原理上来讲，该方法又称作最大类间方差法，因为按照大津法求得的阈值进行图像二值化分割后，前景与背景图像的类间方差最大。

- 大津算法最终的目的，就是求一个标准的全局阈值区分前景和背景（小于该阈值的为前景。大于该阈值的为背景），使得前景和背景像素的灰度值方差之和最大。因为方差越大，相关性越小，黑白越分明。

- 大津算法的优势及劣势：对于一些光照变化的图像有较好的滤除性，但需注意，光照部分需影响图像的每一个像素，而不是渐变式影响。这里说的有些抽象，下面我将用图片去解释。



## 理论分析

我们知道，数学中方差公式为：
$$
s^2 =\frac{1}{n} \times\ [\sum_{i=1}^n (x_i-\overline{x})^2]
$$
如果我们想求整张图像的灰度值方差,那么xi就是各个像素的灰度值，x0就是整张图像的平均灰度值

但在大津算法中，我们默认将图像分为前景和背景，所以方差公式也有一定的变化

**大津算法核心公式：**
$$
s^2 = w_0 \times\ (u_0-\overline{u})^2+w_1 \times\ (u_1-\overline{u})^2
$$
化简后：
$$
g = w0\times\ w1 \times\ (u0-u1)^2
$$
分析这段公式前，我们不妨设几个参数

> h：图像的高度(已知)
>
> w：图像的宽度（已知）（h*w 得到图像的像素数量）
>
> t ：灰度阈值（我们要求的值，大于这个值的像素我们将它的灰度设置为255，小于的设置为0）
>
> n0：小于阈值的像素，前景
>
> n1：大于等于阈值的像素，背景

图像像素的个数
$$
n_0 + n_1 = h \times\ w
$$
w0：前景像素占总像素的比例
$$
w_0 = \frac {n_0}{h \times\ w }\\
$$
w1：背景像素占总像素的比例
$$
w_0 = \frac {n_0}{h \times\ w }\\
$$
u0：前景平均灰度
$$
u_0 = \frac {\sum_{i=1}^{n0} u_{0i}}{n_0}
$$
u1：背景平均灰度
$$
u_1 = \frac {\sum_{i=1}^{n1} u_{1i}}{n_1}
$$
u：平均灰度
$$
u = w_0 \times\ {u_0} + w_1 \times\ {u_1}
$$

最后，标准阈值u从0到255遍历，使得方差最大，并且找出最大方差对应得阈值



## 代码实现

### 算法库的Python实现

```python
import cv2
import numpy as np

# 大津二值化算法
def otsu(gray_img):
    h = gray_img.shape[0]
    w = gray_img.shape[1]

    threshold_t = 0
    max_g =0

    # 遍历每一个灰度层
    for t in range(255):
        # 使用numpy直接对数组进行运算
        n0 = gray_img[np.where(gray_img <t)]
        n1 = gray_img[np.where(gray_img >=t)]

        w0 = len(n0)/(h*w)
        w1 = len(n1)/(h*w)

        u0 = np.mean(n0) if len(n0) >0 else 0.
        u1 = np.mean(n1) if len(n1) >0 else 0.

        g = w0*w1*(u0-u1)**2
        if g>max_g:
            max_g=g
            threshold_t = t

    print('类间最大方差的阈值：',threshold_t)
    gray_img[gray_img < threshold_t] = 0
    gray_img[gray_img >= threshold_t] =255
    return gray_img

img = cv2.imread('c2.png',0)
otsu_img = otsu(img)
cv2.imshow('otsu_img',otsu_img)

cv2.waitKey(0)
cv2.destroyAllWindows()
```



### 大津算法优劣对比

```python
import cv2
import numpy as np
from matplotlib import pyplot as plt

img = cv2.imread('p4.png',0)

#不使用大津算法
ret1,th1 = cv2.threshold(img,127,255,cv2.THRESH_BINARY)

#使用大津算法
ret2,th2 = cv2.threshold(img,0,255,cv2.THRESH_BINARY+cv2.THRESH_OTSU)

#使用了大津算法+高斯滤波器
blur = cv2.GaussianBlur(img,(5,5),0)
ret3,th3 = cv2.threshold(blur,0,255,cv2.THRESH_BINARY+cv2.THRESH_OTSU)

# 绘制所有图像及其直方图
images = [img, 0, th1,
          img, 0, th2,
          blur, 0, th3]

titles = ['Original Noisy Image','Histogram','Global Thresholding (v=127)',
         'Original Noisy Image','Histogram',"Otsu's Thresholding",
         'Gaussian filtered Image','Histogram',"Otsu's Thresholding"]

for i in range(3):
    plt.subplot(3,3,i*3+1),plt.imshow(images[i*3],'gray')
    plt.title(titles[i * 3]), plt.xticks([]), plt.yticks([])
    plt.subplot(3, 3, i * 3 + 2), plt.hist(images[i * 3].ravel(), 256)
    plt.title(titles[i * 3 + 1]), plt.xticks([]), plt.yticks([])
    plt.subplot(3, 3, i * 3 + 3), plt.imshow(images[i * 3 + 2], 'gray')
    plt.title(titles[i * 3 + 2]), plt.xticks([]), plt.yticks([])

plt.show()
```



### 效果图

![](https://s3.bmp.ovh/imgs/2022/02/5c92c5edcea7ba3b.png)

右边依次是不使用大津算法，使用大津算法，使用了大津算法+高斯滤波器的图像处理结果，也许你看不出来有什么区别

假设现在图像环境的光照减小，亮度变低了（这里我添加一个黑色，透明度为50%的图层模拟）

![](https://s3.bmp.ovh/imgs/2022/02/fee4042caece25cb.png)

使用大津算法有着较好的处理结果，这是它最大的优势：可以滤除光照影响

但是，光照若是斜射的，那么大津算法将不适用

![](https://s3.bmp.ovh/imgs/2022/02/1827aebdca4a9806.png)













