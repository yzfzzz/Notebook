# CV4 基于鼠标回调函数及轨迹调色的简单人机交互应用

[TOC]



## 前言

**问：使用轨迹栏创建颜色和画笔半径可调的Paint应用程序？**



## 鼠标回调函数

创建鼠标回调函数具有特定的格式，该格式在所有地方都相同。它仅在功能上有所不同。

因此，下列这段代码我们的鼠标回调函数可以做一件事——在我们双击的地方绘制一个圆圈

```python
import cv2
import numpy as np

#定义一个鼠标回调函数,想要知道更多的鼠标事件，可通过以下屏蔽的代码实现
# import cv2 as cv
# events = [i for i in dir(cv) if 'EVENT' in i]
# print( events )

#定义一个鼠标回调函数的处理事件
def draw_circle(event,x,y,flags,param):
    if event == cv2.EVENT_LBUTTONDBLCLK:
        cv2.circle(img,(x,y),100,(0,0,255),-1)


img = np.zeros((512,512,3),np.uint8)
cv2.namedWindow('img')

# 鼠标事件检查，输入参数为：1.图片名，2.已定义的鼠标事件
cv2.setMouseCallback('img',draw_circle)

while(1):
    cv2.imshow('img',img)
    if cv2.waitKey(20) & 0xFF == 27:
        break
cv2.destroyAllWindows()
```



> **`cv2.setMouseCallback()`函数**
>
> **功能**：鼠标事件检查
>
> **输入参数**：
>
> 1. 图片名
> 2. 已定义的鼠标事件
>
> **示例**：`cv2.setMouseCallback('img',draw_circle)`



下列是一些常见的鼠标事件

| 事件名              | 鼠标活动 |
| ------------------- | -------- |
| EVENT_MOUSEMOVE     | 鼠标移动 |
| EVENT_LBUTTONDOWN   | 左键点击 |
| EVENT_RBUTTONDOWN   | 右键点击 |
| EVENT_MBUTTONDOWN   | 中键点击 |
| EVENT_LBUTTONUP     | 左键放开 |
| EVENT_RBUTTONUP     | 右键放开 |
| EVENT_MBUTTONUP     | 中键放开 |
| EVENT_LBUTTONDBLCLK | 左键双击 |
| EVENT_RBUTTONDBLCLK | 右键双击 |
| EVENT_MBUTTONDBLCLK | 中键双击 |



接下来，我们要进行更高级的操作——通过拖动鼠标来绘制矩形或圆形(取决于我们选择的模式)

```python
import cv2
import numpy as np

drawing = False
mode = True
ix,iy=-1,-1

#多条件鼠标判断函数
def draw_circle(event,x,y,flags,param):
    global ix,iy,drawing,mode
    if event == cv2.EVENT_LBUTTONDOWN:
        drawing = True
        ix,iy = x,y

    elif event == cv2.EVENT_MOUSEMOVE:
        if drawing == True:
            if mode == True:
                cv2.rectangle(img,(ix,iy),(x,y),(0,0,0),5)
            else:
                cv2.circle(img,(x,y),5,(0,0,255),-1)

    elif event == cv2.EVENT_LBUTTONUP:
        drawing = False
        if mode == True:
            cv2.rectangle(img,(ix,iy),(x,y),(0,255,0),5)
        else:
            cv2.circle(img,(x,y),5,(0,0,255),-1)

img = np.zeros((720,1280,3),np.uint8)
cv2.namedWindow('img')

# 鼠标事件检查，输入参数为：1.图片名，2.已定义的鼠标事件
cv2.setMouseCallback('img',draw_circle)

while(1):
    cv2.imshow('img',img)
    if cv2.waitKey(50) & 0xFF == ord('m'):
        mode=~mode
    if cv2.waitKey(20) & 0xFF == 27:
        break
cv2.destroyAllWindows()
```

- global是python里定义一个全局变量的意思
- ix,iy=-1,-1是填充图形的意思
- python里的取反操作为：mode=~mode

本质上，也是在我们def的函数那里，多加了几个条件判断而已



> 如果你的opencv-python的版本为4.5.x,那么你会出现以下报错：
>
> ```python
> error: (-27:Null pointer) NULL window: 'TrackBars' in function 'cvGetTrackbarPos'
> ```
>
> 想要消掉这个报错，我们需要把opencv-python的版本降到4.1.x
>
> 具体可以参考这篇博客
>
> https://stackoverflow.com/questions/69525848/how-can-i-fix-get-trackbar-position-error-in-pycharm
>



## 轨迹栏作为调色板

我们将创建一个简单的应用程序，以显示您指定的颜色。您有一个显示颜色的窗口，

以及三个用于指定B、G、R颜色的跟踪栏。滑动轨迹栏，并相应地更改窗口颜色。

默认情况下，初始颜色将设置为黑色

```python
import numpy as np
import cv2

def nothing(x):
    pass

image = np.zeros((300,512,3),np.uint8)
cv2.namedWindow('image')

cv2.createTrackbar('R','image',0,255,nothing)
cv2.createTrackbar('G','image',0,255,nothing)
cv2.createTrackbar('B','image',0,255,nothing)

switch = '0:OFF \n 1:ON'
cv2.createTrackbar(switch,'image',0,1,nothing)
while(1):
    cv2.imshow('image',image)
    if cv2.waitKey(20)&0xFF == ord('q'):
        break

    r = cv2.getTrackbarPos('R','image')
    g = cv2.getTrackbarPos('G','image')
    b = cv2.getTrackbarPos('B','image')
    s = cv2.getTrackbarPos(switch,'image')

    if s == 0:
        image[:] = 0
    else:
        image[:] = [b,g,r]

cv2.destroyAllWindows()
```

`cv2.namedWindow('image')`命名的image窗口名，即是下面各个函数的**‘要显示的窗口名’**，不可改变



> **`cv2.createTrackbar()`函数**
>
> **功能**：创造一个在图片上可见的轨道，可供用户调控
>
> **输入参数**：
>
> 1. 名称 
> 2. 要显示的窗口名（你必须优先定义，且名字必须相同） 
> 3. 最小值 
> 4. 最大值 
> 5. 事件函数（这里是nothing，即不做什么）
>
> **示例**：`cv2.createTrackbar('R','image',0,255,nothing)`



> **`cv2.getTrackbarPos()`函数**
>
> **功能**：实时获取用户改变的数据
>
> **输入参数**：
>
> 1. 轨道名 
> 2. 窗口名
>
> **示例**：` r = cv2.getTrackbarPos('R','image')`



## 使用轨迹栏创建颜色和画笔半径可调的Paint应用程序

```python
import numpy as np
import cv2

ix,iy=-1,-1
drawing=False

#多条件鼠标判断函数
def draw_circle(event,x,y,flags,param):
    global ix,iy,drawing,mode,b,g,r

# drawing的作用是记录轨迹什么时候开始，什么时候结束
    if event == cv2.EVENT_LBUTTONDOWN:
        drawing = True
        ix,iy = x,y
    elif event == cv2.EVENT_MOUSEMOVE:
        if drawing == True:
           cv2.circle(image,(x,y),size,(b,g,r),-1)
    elif event == cv2.EVENT_LBUTTONUP:
        drawing = False
        cv2.circle(image,(x,y),size,(b,g,r),-1)

# 为了配合cv2.createTrackbar函数使用
def nothing(x):
    pass

# 创建一个白色画布
image = np.zeros((300,512,3),np.uint8)+255
cv2.namedWindow('image')

# 创建轨道，分别是R,G,B,画笔大小
cv2.createTrackbar('R','image',0,255,nothing)
cv2.createTrackbar('G','image',0,255,nothing)
cv2.createTrackbar('B','image',0,255,nothing)
cv2.createTrackbar('SIZE','image',0,10,nothing)


# 鼠标事件检查，输入参数为：1.图片名，2.已定义的鼠标事件
cv2.setMouseCallback('image',draw_circle)

while(1):
    cv2.imshow('image',image)
    if cv2.waitKey(20)&0xFF == ord('q'):
        break

# 实时获取轨道的值
    r = cv2.getTrackbarPos('R', 'image')
    g = cv2.getTrackbarPos('G', 'image')
    b = cv2.getTrackbarPos('B', 'image')
    size = cv2.getTrackbarPos('SIZE','image')


cv2.destroyAllWindows()
```

size是画笔的大小，ix/iy是记录第一次按下的点的位置



## 效果图

![](https://s3.bmp.ovh/imgs/2022/02/100aafd865016ec1.png)

R/G/B/Size均可调，满足题目要求~