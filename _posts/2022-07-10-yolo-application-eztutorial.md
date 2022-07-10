---
layout: post
title: 视觉教程：yolo神经网络应用简单教学
categories: [calibrate, train, test, deploy]
---

# RoboMaster YOLOv5-6.0-keypoints 教程

## 网络应用简单教学： 标定->训练->测试->部署
以装甲板四点预测为例：

### 标定部分：
可在虚拟机中使用Label程序标定

数据集文件结构：
```bash
    ——RM-keypoint-4-9-Oplus               #文件名
      ——images                            #图片文件
      ——labels                            #标签文件
      ——armor.yaml                        #解释脚本
```

数据集tag编号具体查看已有数据集readme

### 训练部分：
#### 启用环境开始训练：
可借用学校服务器GPU资源池进行训练

进入方式：vscode中ssh连接以下两个账号其一
```bash
    ssh it_stu3@sjtu-ai.simplehpc.com
    ssh it_stu3@sjtu-ai.simplehpc.com
    
    密码均为：1234567890
```

使用tmux开启独立终端，这样你退出ssh连接时训练代码仍会运行
```bash
    tmux new -s syb          #生成一个名为syb的tmux终端
    tmux ls                  #查看当前存在的所有tmux终端
    tmux a -t syb            #进入名为syb的tmux终端
    tmux kill-session -t syb #杀死名为syb的tmux终端
```

进入tmux终端后，使用gpu资源
```bash
    ~/bash_gpu.sh            #使用一个gpu资源
    ~/bash_gpu2.sh           #使用两个gpu资源(不建议，显存似乎不共享)
```

如果显示正在等待gpu资源，则说明该账户已有终端占用了所有两个gpu资源，可酌情杀死进程
```bash
    sq                       #查看当前运行作业，找到该账户(例it_stu3)作业的JOBID
    scancel jobid            #取消作业
```

现已进入tmux终端且连接上gpu资源，激活conda环境
```bash
    conda activate YOLOv5    #YOLOv5是能运行yolo的conda环境配置，你当然可以再安装一个自己的conda虚拟环境
```

进入yolo项目根目录，进行训练
```bash
    python train.py --noval    #其余参数一般都设置好了默认值
```

#### 训练调参：
train.py下的指令参数：

有default的参数使用：

    --参数名 参数值

有action的参数(bool类型)使用：

    --参数名

```bash
    def parse_opt(known=False):
        parser = argparse.ArgumentParser()
        parser.add_argument('--weights', type=str, default='', help='initial weights path')
        #预训练模型地址(包含网络结构，不建议使用，收敛速度会慢)，有了这个就不需要--cfg参数了
        parser.add_argument('--cfg', type=str, default='models/yolov5s.yaml', help='model.yaml path')
        #网络结构解释文件地址(不包含参数，从头开始训练)，有了这个就不需要--weights参数了
        parser.add_argument('--data', type=str, default='~/Data/RM-keypoint-4-9-Oplus/armor.yaml', help='dataset.yaml path')
        #数据集解释文件地址
        parser.add_argument('--hyp', type=str, default='data/hyps/RM.armor.yaml', help='hyperparameters path')
        #训练参数解释文件地址，包括但不限于学习率，loss权重，数据增强概率值
        parser.add_argument('--epochs', type=int, default=500)
        #训练总轮次
        parser.add_argument('--batch-size', type=int, default=16, help='total batch size for all GPUs')
        #一次训练中同时使用多少张图片，过小训练效果下降，过高可能超出显存
        parser.add_argument('--imgsz', '--img', '--img-size', type=int, default=640, help='train, val image size (pixels)')
        #以多少分辨率进行训练(正方形，export.py可重新裁剪分辨率导出onnx文件)，过大训练速度会很慢
        parser.add_argument('--rect', action='store_true', help='rectangular training')
        parser.add_argument('--resume', nargs='?', const=True, default=False, help='resume most recent training')
        parser.add_argument('--nosave', action='store_true', help='only save final checkpoint')
        parser.add_argument('--noval', action='store_true', help='only validate final epoch')
        #必选，因为val.py暂未修改
        parser.add_argument('--noautoanchor', action='store_true', help='disable autoanchor check')
        parser.add_argument('--evolve', type=int, nargs='?', const=300, help='evolve hyperparameters for x generations')
        parser.add_argument('--bucket', type=str, default='', help='gsutil bucket')
        parser.add_argument('--cache', type=str, nargs='?', const='ram', help='--cache images in "ram" (default) or "disk"')
        parser.add_argument('--image-weights', action='store_true', help='use weighted image selection for training')
        parser.add_argument('--device', default='', help='cuda device, i.e. 0 or 0,1,2,3 or cpu')
        parser.add_argument('--multi-scale', action='store_true', help='vary img-size +/- 50%%')
        parser.add_argument('--single-cls', action='store_true', help='train multi-class data as single-class')
        parser.add_argument('--adam', action='store_true', help='use torch.optim.Adam() optimizer')
        #可选
        parser.add_argument('--sync-bn', action='store_true', help='use SyncBatchNorm, only available in DDP mode')
        parser.add_argument('--workers', type=int, default=16, help='maximum number of dataloader workers')
        #
        parser.add_argument('--project', default='runs/train', help='save to project/name')
        #训练结果储存地址
        parser.add_argument('--name', default='exp', help='save to project/name')
        #训练结果文件名
        parser.add_argument('--exist-ok', action='store_true', help='existing project/name ok, do not increment')
        parser.add_argument('--quad', action='store_true', help='quad dataloader')
        parser.add_argument('--linear-lr', action='store_true', help='linear LR')
        #不建议训(经测试，收敛速度会变慢)
        parser.add_argument('--label-smoothing', type=float, default=0.0, help='Label smoothing epsilon')
        parser.add_argument('--patience', type=int, default=100, help='EarlyStopping patience (epochs without improvement)')
        parser.add_argument('--freeze', type=int, default=0, help='Number of layers to freeze. backbone=10, all=24')
        parser.add_argument('--save-period', type=int, default=300, help='Save checkpoint every x epochs (disabled if < 1)')
        #每多少轮次保存一次pt权重文件
        parser.add_argument('--local_rank', type=int, default=-1, help='DDP parameter, do not modify')
        parser.add_argument('--negative-path', nargs='+', default=['/cluster/home/it_stu4/Data/COCO/unlabeled2017/'], type=str)
        #负样本地址，很重要，必选
        
        # Weights & Biases arguments
        parser.add_argument('--entity', default=None, help='W&B: Entity')
        parser.add_argument('--upload_dataset', action='store_true', help='W&B: Upload dataset as artifact table')
        parser.add_argument('--bbox_interval', type=int, default=-1, help='W&B: Set bounding-box image logging interval')
        parser.add_argument('--artifact_alias', type=str, default='latest', help='W&B: Version of dataset artifact to use')
    
        opt = parser.parse_known_args()[0] if known else parser.parse_args()
        return opt
```

--hyp下的训练参数文件：
```bash
    lr0: 0.01  # initial learning rate (SGD=1E-2, Adam=1E-3)
    lrf: 0.01  # final OneCycleLR learning rate (lr0 * lrf)
    momentum: 0.937  # SGD momentum/Adam beta1
    weight_decay: 0.0005  # optimizer weight decay 5e-4
    warmup_epochs: 3.0  # warmup epochs (fractions ok)
    warmup_momentum: 0.8  # warmup initial momentum
    warmup_bias_lr: 0.1  # warmup initial bias lr
    box: 0.05  # box loss gain
    pts: 0.1   #新增参数，用于关键点回归loss，即loss.py中的lpts
    cls: 0.5  # cls loss gain
    cls_pw: 1.0  # cls BCELoss positive_weight
    obj: 1.0  # obj loss gain (scale with pixels)
    obj_pw: 1.0  # obj BCELoss positive_weight
    iou_t: 0.20  # IoU training threshold
    anchor_t: 4.0  # anchor-multiple threshold
    # anchors: 3  # anchors per output layer (0 to ignore)
    fl_gamma: 0.0  # focal loss gamma (efficientDet default gamma=1.5) 不建议使用(经测试，tag精度会下降)
    hsv_h: 0.015  # image HSV-Hue augmentation (fraction)             *
    hsv_s: 0.7  # image HSV-Saturation augmentation (fraction)        *
    hsv_v: 0.5  # image HSV-Value augmentation (fraction)             *
    degrees: 30.0  # image rotation (+/- deg)                          装甲板识别建议30度以内，能量机关设180度
    translate: 0.3  # image translation (+/- fraction)                *
    scale: 0.6  # image scale (+/- gain)                              *
    shear: 10.0  # image shear (+/- deg)                               可适当加一点切向变形
    perspective: 0.0003  # image perspective (+/- fraction), range 0-0.001
    flipud: 0.0  # image flip up-down (probability)                    不使用
    fliplr: 0.0  # image flip left-right (probability)                 不使用
    mosaic: 0.7  # image mosaic (probability)                          马赛克操作的概率
    edge_clip: 0.5 ## image clip only armors                           去除训练集背景操作的概率
    mixup: 0.0  # image mixup (probability)                            不建议使用(该效果为降低透明度然后重叠几张图，)
    copy_paste: 0.0  # segment copy-paste (probability)                不使用
    
    #带*号参数在原版yolo中是对称设置范围，即1±value,建议进入utils/augmentation.py中分别修改上下限(augment_hsv和random_perspective函数)
```

--cfg下的网络结构说明文件(简单修改网络尺寸):

以models/yolov5s.yaml为例：
```bash
nc: 80  # number of classes
#类别数量会自动从数据集yaml文件中获取并覆盖，因此无需设置
depth_multiple: 0.33  # model depth multiple
width_multiple: 0.50  # layer channel multiple
#不使用--weights参数，可调，越大自然精度越高并且收敛更快，但推理速度变慢。s大小比较合适，n需要1000+epochs收敛较好
anchors:
  - [10,13, 16,30, 33,23]  # P3/8
  - [30,61, 62,45, 59,119]  # P4/16
  - [116,90, 156,198, 373,326]  # P5/32

# YOLOv5 v6.0 backbone
backbone:
  # [from, number, module, args]
  [[-1, 1, Conv, [64, 6, 2, 2]],  # 0-P1/2
   [-1, 1, Conv, [128, 3, 2]],  # 1-P2/4
   [-1, 3, C3, [128]],
   [-1, 1, Conv, [256, 3, 2]],  # 3-P3/8
   [-1, 6, C3, [256]],
   [-1, 1, Conv, [512, 3, 2]],  # 5-P4/16
   [-1, 9, C3, [512]],
   [-1, 1, Conv, [1024, 3, 2]],  # 7-P5/32
   [-1, 3, C3, [1024]],
   [-1, 1, SPPF, [1024, 5]],  # 9
  ]

# YOLOv5 v6.0 head
head:
  [[-1, 1, Conv, [512, 1, 1]],
   [-1, 1, nn.Upsample, [None, 2, 'nearest']],
   [[-1, 6], 1, Concat, [1]],  # cat backbone P4
   [-1, 3, C3, [512, False]],  # 13

   [-1, 1, Conv, [256, 1, 1]],
   [-1, 1, nn.Upsample, [None, 2, 'nearest']],
   [[-1, 4], 1, Concat, [1]],  # cat backbone P3
   [-1, 3, C3, [256, False]],  # 17 (P3/8-small)

   [-1, 1, Conv, [256, 3, 2]],
   [[-1, 14], 1, Concat, [1]],  # cat head P4
   [-1, 3, C3, [512, False]],  # 20 (P4/16-medium)

   [-1, 1, Conv, [512, 3, 2]],
   [[-1, 10], 1, Concat, [1]],  # cat head P5
   [-1, 3, C3, [1024, False]],  # 23 (P5/32-large)

   [[17, 20, 23], 1, Detect, [nc, anchors]],  # Detect(P3, P4, P5)
  ]
  
#以上构造网络结构的参数在models/yolo.py下parse_model函数中解释使用，用的是py eval的操作，比较骚
```

### 测试部分：
detect.py下的指令参数：
```bash
def parse_opt():
    parser = argparse.ArgumentParser()
    parser.add_argument('--weights', nargs='+', type=str, default='runs/train/exp/weights/best.pt', help='model path(s)')
    #网络权重文件，如果是pt文件网络结构还是根据yolo.py重新生成，确保训练结束后没修改网络结构相关代码(如yolo.py)
    #如果是onnx文件，确保入口分辨率为正方形
    parser.add_argument('--source', type=str, default='data/images', help='file/dir/URL/glob, 0 for webcam')
    #测试集地址，笔者从训练集中挑选了一些有代表性的放在data/images中
    #支持视频识别
    parser.add_argument('--imgsz', '--img', '--img-size', nargs='+', type=int, default=[640], help='inference size h,w')
    #分辨率设置，要与网络入口分辨率一致
    parser.add_argument('--conf-thres', type=float, default=0.25, help='confidence threshold')
    parser.add_argument('--iou-thres', type=float, default=0.45, help='NMS IoU threshold')
    parser.add_argument('--max-det', type=int, default=1000, help='maximum detections per image')
    parser.add_argument('--device', default='', help='cuda device, i.e. 0 or 0,1,2,3 or cpu')
    parser.add_argument('--view-img', action='store_true', help='show results')
    parser.add_argument('--save-txt', action='store_true', help='save results to *.txt')
    parser.add_argument('--save-conf', action='store_true', help='save confidences in --save-txt labels')
    parser.add_argument('--save-crop', action='store_true', help='save cropped prediction boxes')
    parser.add_argument('--nosave', action='store_true', help='do not save images/videos')
    parser.add_argument('--classes', nargs='+', type=int, help='filter by class: --classes 0, or --classes 0 2 3')
    parser.add_argument('--agnostic-nms', action='store_true', help='class-agnostic NMS')
    parser.add_argument('--augment', action='store_true', help='augmented inference')
    parser.add_argument('--visualize', action='store_true', help='visualize features')
    parser.add_argument('--update', action='store_true', help='update all models')
    parser.add_argument('--project', default='runs/detect', help='save results to project/name')
    #测试结果储存地址
    parser.add_argument('--name', default='exp', help='save results to project/name')
    #测试结果文件名
    parser.add_argument('--exist-ok', action='store_true', help='existing project/name ok, do not increment')
    parser.add_argument('--line-thickness', default=3, type=int, help='bounding box thickness (pixels)')
    parser.add_argument('--hide-labels', default=False, action='store_true', help='hide labels')
    parser.add_argument('--hide-conf', default=False, action='store_true', help='hide confidences')
    parser.add_argument('--half', action='store_true', help='use FP16 half-precision inference')
    parser.add_argument('--dnn', action='store_true', help='use OpenCV DNN for ONNX inference')
    opt = parser.parse_args()
    opt.imgsz *= 2 if len(opt.imgsz) == 1 else 1  # expand
    print_args(FILE.stem, opt)
    return opt
```
```bash
    python detect.py             #进行测试，可以不用gpu资源
```

检查训练结果中的results.txt日志，其中记录了每轮训练后各个loss值可供参考

建议精度：第四列为lpts关键点回归loss，该值除以--hyp下文件pts权重值后建议小于0.02

例：

 |轮次|占用显存|lbox|lpts|lobj|lcsl|ltotal|labels|img_size|
 |---|-------|----|----|----|----|------|------|--------|
 |498/499|3.35G|0.01207|0.001389|0.00356|0.003527|0.02055|10|640|
 
0.001389 ÷ 0.1 < 0.02

### 部署部分：

export.py导出*_trt.onnx文件,可在netron.app网站中查看网络结构，确保为384，640入口分辨率

确保小电脑刷过机，有tensorrt环境

可使用trt-fp16-ae部署项目文件，也可使用小电脑中自瞄代码进行部署

后者：

将onnx放入CVRM2022-infantry/asset中

修改CVRM2022-infantry/script/autoaim.py中模型文件地址：'../asset/*_trt.onnx', *号为文件名称

杀死自瞄进程，确保打开风扇
```bash
    echo nvidia|sudo -S jetson_clocks --fan
    echo dji|sudo -S jetson_clocks           #飞机小电脑
```

启动自瞄代码，等待十到三十分钟不等，当asset中出现对应的.cache文件则说明部署完成

