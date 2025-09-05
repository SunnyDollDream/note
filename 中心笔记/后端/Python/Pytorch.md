## 加载数据

主要使用Dataset和dataloader,其中dataset提供一种方式去获取数据及其label,dataloader为后面的网络提供不同的数据形式

```Python
from torch.utils.data import Dataset, DataLoader
from PIL import Image
import os
# root_dir = 'hymenoptera_data/train'
# label_dir = 'ants'
# paths = os.path.join(root_dir,label_dir)
class MyDataset(Dataset):
    def __init__(self, root_dir, label_dir):
        self.root_dir = root_dir
        self.label_dir = label_dir
        self.path = os.path.join(self.root_dir,self.label_dir)
        self.img_path = os.listdir(self.path)

    def __getitem__(self, idx):
        img_name = self.img_path[idx]
        img_item_path = os.path.join(self.root_dir, self.label_dir,img_name)
        img = Image.open(img_item_path)
        label = self.label_dir
        return img, label

    def __len__(self):
        return len(self.img_path)

root_dir = 'hymenoptera_data/train'
anns_label_dir = 'ants'
bees_label_dir = 'bees'
ants_dataset = MyDataset(root_dir, anns_label_dir)
bees_dataset = MyDataset(root_dir, bees_label_dir)

train_dataset = ants_dataset + bees_dataset
```

## TensorBoard

需要在ann的终端安装`pip install tensorboard`

#### SummaryWriter

构造方法有一个参数是指定事件日志输出到哪里

**常用方法:**

.add_image()

> 参数含义
> 
> tag(string):Data identifir
> 
> img_tensor(torch,Tensor,numpy.array,or string/blobname): Image data()类型需要是括号里的哪几种,正常图片类型不符合,可以利用Opencv读取图片，获得numpy型图片数据
> 
> 在ann终端中安装opencv-python
> 
> 也可以直接import numpy,调用numpy.array(PIL读取的jepg格式图片)获取numpy格式的图片,但此时图片格式还有问题,需要再add_image中加一个参数dataformats='HWC'
> 
> global step(int):Global step value to record
> 
> walltime (float): optional override default walltime (time.time())
> 
> seconds after epoch ofevent

.add_scalar(self, tag,scalar_value,global_step=None, walltime=None)

> 参数含义
> 
> tag(string):生成的图像的标题
> 
> scalar_value:需要保存的数值(y轴)
> 
> global step(int):训练到多少步(x轴)
> 
> walltime (float):optional override default walltime (time.time()) with seconds after epoch of event

```Python
from torch.utils.tensorboard import SummaryWriter
import numpy
from PIL import Image
write = SummaryWriter("logs")
img_path = "D:\\LearnPytorch\\learn_pytorch\\hymenoptera_data\\train\\ants\\0013035.jpg"
img_PIL = Image.open(img_path)
img_array = numpy.array(img_PIL)
write.add_image("test", img_array,1,dataformats='HWC')

for i in range(100):
    write.add_scalar("y = x",i,i)

write.close()
"""
生成的日志文件无法直接打开,需要在ann终端执行tensorboard --logdir(日志所在文件夹)=logs [--port=6007](指定打开的窗口的端口)
"""
```

.close():关闭

## torchvision中的transforms

导入`from torchvision import transforms`

#### 常用方法:

Class ToTensor(object):Convert a PIL Image or numpy.ndarray to tensor(这个方法似乎是直接返回另一个函数,需要用另一个变量接收一下,再进行调用)