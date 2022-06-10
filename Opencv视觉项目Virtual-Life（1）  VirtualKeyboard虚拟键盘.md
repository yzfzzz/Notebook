# CV视觉项目VirtualKeyboard（1）

## 前言

前段时间一直在查官方文档学CV，感觉进度较慢，而且内心空虚感觉什么都没学到，于是产生了边做边学的想法，正好也能借此发发朋友圈装个逼（事后证明效果还是不错滴）



## 效果

[VirtualKeyboard虚拟键盘效果](https://mp.weixin.qq.com/s?__biz=Mzg3MDY4MTcyMw==&mid=2247483665&idx=1&sn=23ddc64cd5209698219b1c38228be68e&chksm=ce8b5c05f9fcd5133db726922624f73fcd837f502d74e20b5656a6dab55860ef489639e1c595&token=26722070&lang=zh_CN#rd)

<img src="D:\desktop\装逼.png" alt="装逼" style="zoom:67%;" />



## 知识体系

![未命名-1](D:\浏览器下载\未命名-1.png)



## 库的安装及版本提示

库的版本及其重要，因为不同的版本，库函数的内容也会在做更改。有时，编译器会因此报错

```python
import cv2
import cvzone
import math
from cvzone.HandTrackingModule import HandDetector
import mediapipe as mp
from time import sleep
import numpy as np
from pynput.keyboard import Controller  #为了使虚拟键盘工作
```

**库的版本**

| 软件包                | 版本     |
| --------------------- | -------- |
| cvzone                | 1        |
| mediapipe             | 0.8.8    |
| numpy                 | 1.21.5   |
| opencv-python         | 4.1.0.25 |
| pynput                | 1.7.6    |
| opencv-contrib-python | 4.1.0.25 |

> 注意：
>
> 如果mediapipe的版本为最新，那么编译器极有可能报错：<u>TypeError: create_int(): incompatible function arguments. The following argument types are supported: 1.(arg0: int) -> mediapipe.python.</u>
>
> 解决方法：mediapipe版本降为  0.8.8



## 知识点详解

#### 1.链接摄像头

```python
#设定计算机的摄像头为图像输入
cap = cv2.VideoCapture(0,cv2.CAP_DSHOW)

#设置分辨率为1280*720
cap.set(3,1280)
cap.set(4,720)
```



- **摄像头的编号对应的功能**

```python
0. CV_CAP_PROP_POS_MSEC 视频文件的当前位置(毫秒)。
1. CV_CAP_PROP_POS_FRAMES 下一个解码/捕获的帧基于0的索引
2. CV_CAP_PROP_POS_AVI_RATIO 视频文件的相对位置
3. CV_CAP_PROP_FRAME_WIDTH 视频流帧的宽度
4. CV_CAP_PROP_FRAME_HEIGHT 视频流中帧的高度
5. CV_CAP_PROP_FPS 帧速率
6. CV_CAP_PROP_FOURCC 编解码器4字符代码
7. CV_CAP_PROP_FRAME_COUNT 视频文件帧数
8. CV_CAP_PROP_FORMAT 由retrieve()返回的Mat对象的格式
9. CV_CAP_PROP_MODE 后端特定值，表示当前捕获模式
10. CV_CAP_PROP_BRIGHTNESS 图像的亮度(仅用于相机)
11. CV_CAP_PROP_CONTRAST 图像的对比度(仅用于相机)
12. CV_CAP_PROP_SATURATION 图像的饱和度(仅用于相机)
13. CV_CAP_PROP_HUE 图像的色调(仅用于相机)
14. CV_CAP_PROP_GAIN  图像的增益(仅用于相机)
15. CV_CAP_PROP_EXPOSURE 曝光(仅用于相机)
16. CV_CAP_PROP_CONVERT_RGB Boolean标志，指示图像是否应该转换为RGB
17. CV_CAP_PROP_WHITE_BALANCE 目前不支持的
18. CV_CAP_PROP_RECTIFICATION 立体声相机的校正标志(注意:只有DC1394 v 2支持。x目前后台)
```





#### 2.绘制键盘

###### 使用一个列表存储字符

```python
# 根据键盘的布局创建一个列表数组，并定义一个空字符串来存储键入的键
keyboard_keys = [["Q","W","E","R","T","Y","U","I","O","P"],
                 ["A","S","D","F","G","H","J","K","L",";"],
                 ["Z","X","C","V","B","N","M",",",".","/"]]
final_text = ""
```

我们还需要将虚拟键盘接入笔记本上的键盘的功能，使虚拟键盘工作

```python
keyboard = Controller()
```



###### 绘制键盘

- 定义按键的属性

```python
class Button():
    def __init__(self,pos,text,size=[85,85]):
        self.pos = pos
        self.size = size
        self.text = text
```



- for循环依次定义20个按键的x，y轴坐标

```python
buttonList = []
for k in range(len(keyboard_keys)):
    # 遍历列表元素
    for x,key in enumerate(keyboard_keys[k]):
        # 增加元素到列表的末尾
        buttonList.append(Button([100*x+25,100*k+50],key))
```



- `draw()函数`接受两个参数（图像和按钮列表）输入，同时返回图像

```python
def draw(img,buttonlist):
    for button in buttonList:
        x, y = button.pos
        w, h = button.size

        # 在每个键的角落绘制矩形边缘。这是为了让我们的键盘布局看起来更好看
        cvzone.cornerRect(img, (button.pos[0], button.pos[1],
                                button.size[0], button.size[0]), 20, rt=0)
        cv2.rectangle(img,(int(x),int(y)), (int(x + w), int(y + h)), (255, 144, 30), cv2.FILLED)

        # 位置参数：1.图片 2.需显示的文字 3.文字添加到图片的位置 4.字体类型 5.字体大小 6.字体颜色 7.字体粗细
        cv2.putText(img, button.text, (x + 20, y + 65),
                    cv2.FONT_HERSHEY_PLAIN, 4, (0, 0, 0), 4)
    return img
```

图形绘制可参考这篇博客：

[CV3 绘制OpenCV的徽标](https://blog.csdn.net/henghuizan2771/article/details/122779089?spm=1001.2014.3001.5501)





#### 3.识别手势

```python
#只在图像中寻找手
img = detector.findHands(img)

#返回20个手势点的坐标和边界框坐标
lmList, bboxInfo = detector.findPosition(img)
```



> **`detector.findHands（）函数`**
>
> **功能**：只在图像中寻找手
>
> **输入参数**：img（返回也是img，只不过手的节点被标记了）



> **`detector.findPosition（）函数`**
>
> **功能**：返回20个手势点的坐标和边界框坐标
>
> **输入参数**：img



#### 4.根据坐标，返回字母

```python
 # lmList为手部节点坐标
    if lmList:
        for button in buttonList:
            x,y = button.pos
            w,h = button.size

            # lmList[8]为食指指尖坐标，lmList[12]为中指指尖坐标
            if x < lmList[8][1] < x+w and y < lmList[8][2] < y+h:

                cv2.rectangle(img,(int(x),int(y)),(int(x+w),int(y+h)),  	(0,255,255),cv2.FILLED)
                cv2.putText(img,button.text,(x+20,y+65),cv2.FONT_HERSHEY_PLAIN,4,(0,0,0),4)

                l, _, _ = detector.findDistance(8,12,img,draw = False)
                # print(l)
                if l <30:
                    count=1
                    if count == 1 :
                        keyboard.press(button.text)

                        # 用户UI反馈，选中后修改字体和底部矩形颜色
                        cv2.rectangle(img,(int(x),int(y)),(int(x+w),int(y+h)),(0,255,0),cv2.FILLED)
                        cv2.putText(img,button.text,(x+20,y+65),cv2.FONT_HERSHEY_PLAIN,4,(0,0,0),4)
                        final_text += button.text

                        # 设定间隔时间，避免多次选择同一字母
                        sleep(0.2)
                        count=0
```





#### 5.扩展，修改键盘背景

```python
def transparent_layout(img,buttonlist):
    imgNew = np.zeros_like(img,np.uint8)
    for button in buttonlist:
        x,y = button.pos

        cvzone.cornerRect(imgNew, (button.pos[0], button.pos[1], button.size[0], button.size[0]), 20, rt=0)
        cv2.rectangle(imgNew, (int(x),int(y)), (int(x + button.size[0]), int(y + button.size[1])), (255, 144, 30), cv2.FILLED)
        cv2.putText(imgNew, button.text, (x + 20, y + 65), cv2.FONT_HERSHEY_PLAIN, 4, (0, 0, 0), 4)

    # 复制一份图像
    out = img.copy()

    alpaha = 0.5
    mask = imgNew.astype(bool)

    # 图像融合：1.图像1  2.图像1的权重  3.图像2 4.图像2的权重 5.融合后，每个像素点加的标量
    out[mask] = cv2.addWeighted(img,alpaha,imgNew,1-alpaha,0)[mask]
    return out
```



蒙版知识可参考这篇博客：

[CV6 图像的算术运算(以简单的抠图为例)](https://blog.csdn.net/henghuizan2771/article/details/122815165?spm=1001.2014.3001.5502)

正如astype的中文意思，作为[布尔类型](https://so.csdn.net/so/search?q=布尔类型&spm=1001.2101.3001.7020)，也就是true or false。

`mask = imgNew.astype(bool)` 将图片转化为布尔值

结果

```python
[[ True True True]
 [ True True True]]
 
[[False False False]
 [ True True True]]
```



## 程序代码

```python
'''
1.链接摄像头
2.识别手势
3.绘制键盘
 3.1创建键盘字母List
 3.2通过循环绘制键盘
4.根据坐标，取得返回字母
 4.1 利用lmList[8]食指之间坐标，判断选中的字母
 4.2 利用食指与中指之间的距离，确认输入的字母
 5.扩展，修改键盘背景
 6.利用pynput模拟真实键盘输入
'''

import cv2
import cvzone
import math
from cvzone.HandTrackingModule import HandDetector
import mediapipe as mp
from time import sleep
import numpy as np
from pynput.keyboard import Controller  #为了使虚拟键盘工作

#设定计算机的摄像头为图像输入
cap = cv2.VideoCapture(0,cv2.CAP_DSHOW)

#设置分辨率为1280*720
cap.set(3,1280)
cap.set(4,720)

# 以 0.8 的检测置信度初始化 HandDetector 并将其分配给检测器
detector = HandDetector(detectionCon=0.8)

# 根据键盘的布局创建一个列表数组，并定义一个空字符串来存储键入的键
keyboard_keys = [["Q","W","E","R","T","Y","U","I","O","P"],
                 ["A","S","D","F","G","H","J","K","L",";"],
                 ["Z","X","C","V","B","N","M",",",".","/"]]
final_text = ""

# 为了使虚拟键盘工作
keyboard = Controller()


# 绘制键盘（实心）
# 接受两个参数（图像和按钮列表），并返回图像
def draw(img,buttonlist):
    for button in buttonList:
        x, y = button.pos
        w, h = button.size

        # 在每个键的角落绘制矩形边缘。这是为了让我们的键盘布局看起来更好看
        cvzone.cornerRect(img, (button.pos[0], button.pos[1],
                                button.size[0], button.size[0]), 20, rt=0)
        cv2.rectangle(img,(int(x),int(y)), (int(x + w), int(y + h)), (255, 144, 30), cv2.FILLED)

        # 位置参数：1.图片 2.需显示的文字 3.文字添加到图片的位置 4.字体类型 5.字体大小 6.字体颜色 7.字体粗细
        cv2.putText(img, button.text, (x + 20, y + 65),
                    cv2.FONT_HERSHEY_PLAIN, 4, (0, 0, 0), 4)
    return img


def transparent_layout(img,buttonlist):
    imgNew = np.zeros_like(img,np.uint8)
    for button in buttonlist:
        x,y = button.pos

        cvzone.cornerRect(imgNew, (button.pos[0], button.pos[1], button.size[0], button.size[0]), 20, rt=0)
        cv2.rectangle(imgNew, (int(x),int(y)), (int(x + button.size[0]), int(y + button.size[1])), (255, 144, 30), cv2.FILLED)
        cv2.putText(imgNew, button.text, (x + 20, y + 65), cv2.FONT_HERSHEY_PLAIN, 4, (0, 0, 0), 4)

    # 复制一份图像
    out = img.copy()

    alpaha = 0.5
    mask = imgNew.astype(bool)

    # 图像融合：1.图像1  2.图像1的权重  3.图像2 4.图像2的权重 5.融合后，每个像素点加的标量
    out[mask] = cv2.addWeighted(img,alpaha,imgNew,1-alpaha,0)[mask]
    return out

# 定义按键的属性
class Button():
    def __init__(self,pos,text,size=[85,85]):
        self.pos = pos
        self.size = size
        self.text = text

buttonList = []
for k in range(len(keyboard_keys)):
    # 遍历列表元素
    for x,key in enumerate(keyboard_keys[k]):
        # 增加元素到列表的末尾
        buttonList.append(Button([100*x+25,100*k+50],key))

while True:
    success,img = cap.read()

    #只在图像中寻找手
    img = detector.findHands(img)

    # 输入图像
    #返回20个手势点的坐标和边界框坐标
    lmList, bboxInfo = detector.findPosition(img)

    img = transparent_layout(img,buttonList)

    # lmList为手部节点坐标
    if lmList:
        for button in buttonList:
            x,y = button.pos
            w,h = button.size

            # lmList[8]为食指指尖坐标，lmList[12]为中指指尖坐标
            if x < lmList[8][1] < x+w and y < lmList[8][2] < y+h:

                cv2.rectangle(img,(int(x),int(y)),(int(x+w),int(y+h)),(0,255,255),cv2.FILLED)
                cv2.putText(img,button.text,(x+20,y+65),cv2.FONT_HERSHEY_PLAIN,4,(0,0,0),4)

                l, _, _ = detector.findDistance(8,12,img,draw = False)
                # print(l)
                if l <30:
                    count=1
                    if count == 1 :
                        keyboard.press(button.text)

                        # 用户UI反馈，选中后修改字体和底部矩形颜色
                        cv2.rectangle(img,(int(x),int(y)),(int(x+w),int(y+h)),(0,255,0),cv2.FILLED)
                        cv2.putText(img,button.text,(x+20,y+65),cv2.FONT_HERSHEY_PLAIN,4,(0,0,0),4)
                        final_text += button.text

                        # 设定间隔时间，避免多次选择同一字母
                        sleep(0.2)
                        count=0

    # 输入结果显示于图像
    cv2.rectangle(img,(25,350),(700,450),(255,255,255),cv2.FILLED)
    cv2.putText(img,final_text,(60,425),cv2.FONT_HERSHEY_PLAIN,4,(0,0,0),4)

    cv2.imshow("keyboard", img)
    if cv2.waitKey(1) == ord('q'):
        break
cap.release()
cv2.destroyAllWindows()
```



## 结语

这个小项目需要改进的地方还蛮多的，比如可以按下按键表示输入字母；键盘和笔记软件Markdown，Word，OneNote的联动；手写字的识别这些。希望日后能改进~