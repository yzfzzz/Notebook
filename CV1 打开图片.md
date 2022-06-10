# CV1 计算机眼中的图像



## 1.基本读写图像文件

无论哪种格式，每一个像素都会有一个值，但不同格式表示像素的方式有所不同

- **numpy.zero(）函数**

调用该函数，可以通过二维NumPy数组来简单创建一个黑色的正方形图像

```python
img=numpy.zeros((3,3),dtype=numpy.uint8)
```

控制台print一下，可得
$$
[[0 0 0]
 [0 0 0]
 [0 0 0]]
$$
该输出有两个[],说明该图像有两条通道，每一个像素都由一个8-bit整数表示，每个像素的范围是（0~255），其中，0表示最黑，255表示最白



- **cv2.cvtColor函数**

使用该函数，可以把图像转化为BGR格式

```python
img=cv2.cvtColor(img,cv2.COLOR_GRAY2BGR)
```

控制台print一下，可得
$$
[[[0 0 0]
  [0 0 0]
  [0 0 0]]

 [[0 0 0]
  [0 0 0]
  [0 0 0]]

 [[0 0 0]
  [0 0 0]
  [0 0 0]]]
$$
现在，每个像素点都由一个三元数组，并且每一个二维数组/整形（integer）分别表示一个B/G/R通道



- **img.shape参数**

可以通过shape属性来查看图像的结构，它会返回行和列，如果有2个及以上的通道，还会返回通道数

```pY
print(img.shape)
```

返回（414，500，3）；414代表高【h】，500代表宽【w】，3是通道数【c】/或者说像素红绿蓝也行



- **图像的读取及保存**

```python
image = cv2.imread('D:/opencv/imgurl/1.png')
cv2.imwrite('D:/opencv/imgurl/1.png',image)
```

这段代码，读取一张png图片，然后把它保存为jpg格式



- **将图片转换成灰度图，并保存**

```python
grayImage = cv2.imread('D:/opencv/imgurl/1.png',cv2.IMREAD_GRAYSCALE)
cv2.imwrite('D:/opencv/imgurl/1.png',grayImage)
```



## 2.打开图像

```python
import cv2
import numpy

img = cv2.imread('D:/opencv/imgurl/2.jpg')
cv2.imshow('p',img)
cv2.waitKey()
cv2.destroyAllWindows()
```



- **cv2.imread()函数**

```
cv2.imread('D:/opencv/imgurl/1.png')
```

功能：读取图片

输入参数1：图片的路径（仅英文）



- **cv2.imshow()函数**

```
cv2.imshow('openpng',img)
```

功能：窗口展示图像

输入参数1是给窗口命名(命名只能是英文)，输入参数2是已imread的图像



- **cv2.waitKey()函数**

```
#等待时间，毫秒级别，0表示任意键终止
cv2.waitKey(1000)
```

功能：等待键盘输入，函数WaitKey无限地等待一个键事件，如果没有键，则返回-1；触发这段代码，窗口消失

输入参数1：等待的时间，0表示任意键



- **cv2.destroyAllWindow（）函数**

```
cv2.destroyAllWindows()
```

功能：关掉所有窗口，销毁内存



## 3.查询图像的信息

```python
print(type(img1)) #查询底层格式
print(img1.size) #计算像素点的个数
print(img1.dtype) #查询数据的类型
```







