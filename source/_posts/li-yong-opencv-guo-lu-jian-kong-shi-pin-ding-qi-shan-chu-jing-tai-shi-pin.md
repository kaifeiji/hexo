---
title: '利用OpenCV过滤监控视频，定期删除静态视频'
date: 2021-03-03 13:28:24
categories:
- 折腾
tags:
- 视频监控
- OpenCV
- Python
---

![OpenCV](/post-images/li-yong-opencv-guo-lu-jian-kong-shi-pin-ding-qi-shan-chu-jing-tai-shi-pin.png)

孩子出生以后，在客厅安了一个小米摄像头，没有用小米的付费存储，视频存储到 NAS 存储。
时间长了，发现很多静态画面的视频，占用了太多空间，写了一个脚本检测视频，定期删除无意义的视频，并且把视频的动态截取成缩略图，另存一文件夹，方便后续调取时查看。

<!-- more -->

# 安装 OpenCV

OpenCV 的安装，不同的平台会有一些差异，我选择的是 OpenCV-Python。

Ubuntu 自带 Python，如果是 Windows 需要先安装 Python。

通过命令行，直接安装 opencv 和 imutils：

```bash
pip install opencv-python
pip install imutils
```

Windows 系统可能会遇到“pip 不是内部或外部命令”的问题，自行搜索解决吧……

# 脚本代码

脚本很简单，核心是利用 OpenCV 形成灰阶图像并高斯模糊，对比前后两张图像，获取变化斑块，如果面积超过阈值（接近一个人的大小），则判断为动态视频，否则为静态视频。

```python
#!/usr/bin/python
# -*- coding: UTF-8 -*-

import os
import datetime
import imutils
import time
import cv2

// 设置视频所在的根目录，Windows示例：C:\\Users\\abc\\desktop
base_path = '/share/xiaomi_camera_videos/788b2a9faf02'

// 获取文件列表函数
def get_filelist(dir):
    Filelist = []
    for home, dirs, files in os.walk(dir):
        for file_name in files:
            Filelist.append(os.path.join(home, file_name))
    return Filelist

// 检测视频是否有活动画面
def is_video_occupied(video_path):
    camera = cv2.VideoCapture(video_path)
    total = camera.get(cv2.CAP_PROP_FRAME_COUNT)  # 总帧数
    fps = camera.get(cv2.CAP_PROP_FPS)  # 帧率
    # print(int(total / fps))   # 视频时间
    step = (total - 100) / 9  # 间隔时间

    # 初始化视频流的第一帧
    lastFrame = None
    occupied = False

    # 遍历视频的每一帧
    for i in range(100, int(total), int(step)):
        # 获取当前帧并初始化
        camera.set(cv2.CAP_PROP_POS_FRAMES, i)
        camera.grab()  # 解码并返回捕获的视频帧
        (grabbed, frame) = camera.read()

        # 如果不能抓取到一帧，说明我们到了视频的结尾
        if not grabbed:
            break

        # 调整该帧的大小，转换为灰阶图像并且对其进行高斯模糊
        frame = imutils.resize(frame, width=500)
        camera_area = frame.shape[0] * frame.shape[1]
        gray = cv2.cvtColor(frame, cv2.COLOR_BGR2GRAY)
        gray = cv2.GaussianBlur(gray, (21, 21), 0)

        # 如果第一帧是None，对其进行初始化
        if lastFrame is None:
            lastFrame = gray
            continue

        # 计算当前帧和第一帧的不同
        frameDelta = cv2.absdiff(lastFrame, gray)
        thresh = cv2.threshold(frameDelta, 25, 255, cv2.THRESH_BINARY)[1]

        # 扩展阀值图像填充孔洞，然后找到阀值图像上的轮廓
        thresh = cv2.dilate(thresh, None, iterations=2)
        (cnts, _) = cv2.findContours(thresh.copy(), cv2.RETR_EXTERNAL,
            cv2.CHAIN_APPROX_SIMPLE)

        # 将当前帧置为上一帧
        lastFrame = gray

        # 遍历轮廓
        for c in cnts:
            # 如果变化太小，忽略
            ca = cv2.contourArea(c)
            if  ca < 500 or ca > camera_area/2:
                continue
            occupied = True
            break
        else:
            continue
        break

    # 清理摄像机资源
    camera.release()
    return occupied,frame

// 获取文件列表
filelist = get_filelist(base_path)
// 创建缩略图文件夹
snap_path = os.path.join(os.path.abspath(os.path.join(base_path,os.path.pardir)),'snapshots')
if not os.path.exists(snap_path):
    os.makedirs(snap_path)
delete = 0
snap = 0
left = len(filelist)
// 循环文件列表
for video_path in filelist:
    date_name = os.path.basename(os.path.dirname(video_path))
    file_name = os.path.splitext(os.path.basename(video_path))[0]
    jpg_path = os.path.join(snap_path,date_name+'_'+file_name+'.jpg')
    if os.path.exists(jpg_path):
        snap = snap + 1
    else:
        occupied, snapshot = is_video_occupied(video_path)
        // 动态视频截图保存
        if (occupied):
            cv2.imwrite(jpg_path, snapshot)
            snap = snap + 1
        // 静态视频直接删除
        else:
            os.remove(video_path)
            delete = delete + 1

    left = left - 1
    // 屏幕显示进度
    cur_time = datetime.datetime.now().strftime("%Y.%m.%d-%H:%M:%S")
    print(f'{cur_time} -> {snap} snap, {delete} delete, {left} left; {date_name}_{file_name}')
// 删除空文件夹
for root, dirs, files in os.walk(base_path):
    if dirs == files:
        os.rmdir(root)
```
