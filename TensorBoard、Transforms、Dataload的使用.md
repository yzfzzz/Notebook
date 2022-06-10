# TensorBoard&Transforms&Dataload的使用



[TOC]

⭐本文内容：TensorBoard、Transforms、Dataload

## TensorBoard

```python
from torch.utils.tensorboard import SummaryWriter
writer = SummaryWriter("logs")
for i in range(100):
  writer.add_scalar("y=x",i,i)

writer.close()
```

```
%load_ext tensorboard
%tensorboard --logdir /content/logs #注意路径
```

`%tensorboard --logdir /content/logs`:保存这个插件，到logs文件夹中

`writer = SummaryWriter("logs")`:()里面的logs，是%tensorboard --logdir /content/logs的logs

类“SummaryWriter”提供了创建事件文件的高级API 并添加摘要和事件。类更新 异步文件内容。这允许培训程序调用方法 直接从训练循环向文件中添加数据，而不会减慢速度训练

> **writer.add_image()函数**
>
> - 示例：`writer.add_image("test",img,1,dataformats='HWC') #注意dataformats='HWC'表示通道、长、宽的排序`
>
> - 作用：添加image到tensorBoard中
> - 参数：
>   - img_tensor：数据类型（torch.Tensor, numpy.array, or string/blobname）
>
> <img src="https://yzfzzz.oss-cn-shenzhen.aliyuncs.com/image/202205061956751.png" alt="image-20220506195554069" style="zoom:50%;" />



## Transforms

Transforms：图像预处理的工具



![image-20220506202035724](https://yzfzzz.oss-cn-shenzhen.aliyuncs.com/image/202205062020778.png)



变换数据类型：`tensor_trans = transforms.ToTensor()(img)`，注意在（）后面加上（img）



## Dataload

```python
import torchvision
from torch.utils.data import DataLoader
# 准备测试集 
test_data = torchvision.datasets.CIFAR10("/content/drive/MyDrive/Learn-	                     pytorch/dataset",train=False,download=True,transform=torchvision.transforms.ToTensor())

test_loader = DataLoader(dataset=test_data,batch_size=64,shuffle=True,num_workers=0)
```



> **torchvision.datasets()函数**
>
> - 示例：`test_data = torchvision.datasets.CIFAR10("/content/drive/MyDrive/Learn-pytorch/dataset",train=False,download=True,transform=torchvision.transforms.ToTensor())`
> - 作用：加载官方自带的数据集，返回img,target（图片+标签）
> - 参数：
>   - .CIFAR10：数据集名称
>   - "/content/drive/MyDrive/Learn-pytorch/dataset"：数据集的路径，如没有则新建一个文件夹
>   - train=False：是不是用来训练的，如：train=False表示是测试集
>   - download=True：是否下载
>   - transform=torchvision.transforms.ToTensor()：数据格式的转换



> **DataLoader()**函数
>
> - 示例：`test_loader = DataLoader(dataset=test_data,batch_size=64,shuffle=True,num_workers=0)`
> - 作用：和torchvision.datasets()联用，将数据集以一定的方式打包
> - 参数：
>   - batch_size=64：一个test_data随机抓取64张图片

```python
writer = SummaryWriter("dataloader")
step = 0
for data in test_loader:
  imgs,targets = data
  writer.add_images("test_image",imgs,step)
  step = step+1

writer.close()
```

🚀注意SummaryWriter("==dataloader==")、writer.add_image==s==("test_image",imgs,step)

