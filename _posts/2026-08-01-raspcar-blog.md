---
title: 树莓派小车 raspCar：从网页遥控到摄像头回传
date: 2026-08-01
categories: [技术, 硬件]
tags: [树莓派, 小车, GPIO, 视频流, 嵌入式]
description: 大学时做的树莓派小车项目，最近把它重新翻出来优化了一遍：修掉方向映射 bug、加上摄像头视频流和串口通讯。
image: /assets/img/posts/landscape/2026-08-01-raspcar-blog.jpg
---

## 当初是怎么开始的

整理旧项目的时候翻到了大学时做的树莓派小车，想起来那会儿买了树莓派 3B+，配了个亚克力底盘和两个直流电机，折腾了一个多月。

当时的想法很简单：小车能不能用网页控制？于是在树莓派上跑了一个 Python 服务，手机打开浏览器就能看到控制界面，点按钮或者按键盘方向键就能让小车动起来。

## 项目结构

代码不长，分成了三层：

```
raspCar/
├── main.py                 # Bottle Web 服务入口
├── components/             # 硬件组件
│   ├── vehicle.py          # 电机/GPIO 驱动
│   ├── listener.py         # 键盘监听
│   ├── camera.py           # 摄像头 MJPEG 视频流
│   └── serial_comm.py      # 串口指令解析
├── algorithms/             # 算法
│   ├── pid_controller.py   # PID 控制器
│   └── obj_detector.py     # Haar-Cascade 目标检测
└── panels/index.html       # 网页控制界面
```

### 驱动层

电机用的是 L298N 驱动板，树莓派 GPIO 输出高低电平控制正反转，PWM 调节速度。每个轮子两个方向引脚加一个 PWM 引脚。

```python
class Wheel:
    def forward(self):
        GPIO.output(self._pin1, GPIO.HIGH)
        GPIO.output(self._pin2, GPIO.LOW)

    def backward(self):
        GPIO.output(self._pin1, GPIO.LOW)
        GPIO.output(self._pin2, GPIO.HIGH)

    def stop(self):
        GPIO.output(self._pin1, GPIO.LOW)
        GPIO.output(self._pin2, GPIO.LOW)
```

小车就是左右两个轮子的组合：同向前进、同向反转后退、一前一后原地转弯。

### 网页控制

用 Bottle 起一个 Web 服务，浏览器端发 POST 请求，后端把命令转发给电机驱动。界面就是一组方向按钮。

## 这次做了什么

### 1. 修掉一个方向 bug

整理代码时发现 `listener.py` 里键盘映射有个明显的复制粘贴错误——按 `S`（后退）和 `D`（右转）都会被发成"前进"：

```python
elif x.name == 's' or x.name == 'down':
    self.car.move('forward')   # 应该 backward
elif x.name == 'd' or x.name == 'right':
    self.car.move('forward')   # 应该 rightTurn
```

这种 bug 在写的时候很难发现，因为测试键盘控制得真连上车。修好之后顺手补了单元测试，用 mock 模拟 GPIO 和键盘，不用真硬件也能跑。

### 2. 融合摄像头视频流

当初网页界面里就预留了视频流的位子，但一直没接上。这次用 OpenCV 读摄像头，Bottle 以 MJPEG 流的形式吐给浏览器，`<img>` 标签直接显示：

```python
def mjpeg_generator(camera):
    while True:
        frame = camera.read()   # JPEG 编码的一帧
        if frame is not None:
            yield (b"--frame\r\n"
                   b"Content-Type: image/jpeg\r\n\r\n" + frame + b"\r\n")
        time.sleep(0.05)
```

现在打开控制界面就能同时看到摄像头画面和操作按钮，边看边开。

### 3. 串口通讯（Zigbee 的基础）

Zigbee 模块一般以透明串口方式工作，上位机把指令通过串口发给小车端。这次加了一个串口监听模块，后台线程持续读串口数据，按结束符切出指令再驱动小车：

```python
def _listen(self):
    while self._running:
        data = self._serial.read(256)
        if not data:
            continue
        buffer += data
        while self._endian.encode() in buffer:
            cmd, buffer = buffer.split(self._endian.encode(), 1)
            self._handle(cmd.decode(errors="ignore").strip())
```

串口指令层已经就绪，接上 Zigbee 模块就能远程遥控。

## 小车照片

![树莓派小车正面](/assets/img/posts/rasp-car/raspcar_front.jpg)

![树莓派小车侧面](/assets/img/posts/rasp-car/raspcar_side1.jpg)

## 下一步

- **语音识别**：加上麦克风，喊一声就能让小车动起来，这个还需要硬件支持
- **PID 速度控制**：PID 控制器已经写好了，还没接到电机的闭环速度控制上
- **自主循迹/避障**：加超声波传感器或者红外循迹模块

项目代码在 [xianglun918/raspCar](https://github.com/xianglun918/raspCar)，大学时写的东西比较粗糙，现在整理了一下，希望能继续玩下去。
