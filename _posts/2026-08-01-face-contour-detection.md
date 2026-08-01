---
title: 简单的人脸轮廓识别 -- Haar + Canny 的入门实践
date: 2026-08-01
categories: [技术, 图像处理]
tags: [OpenCV, 图像处理, 人脸检测, Python]
description: 数字图像处理课的大作业，用 Haar 级联分类器和 Canny 边缘检测实现了一个简单的人脸轮廓识别。
---

## 起因

数字图像处理课的期末大作业，要求实现一个图像处理相关的算法。当时对计算机视觉正有兴趣，就选了人脸相关的方向。

目标很简单：给定一张包含人脸的图片，自动检测人脸位置并提取面部轮廓。不需要很精确，能跑通就行。

## 思路

整体分两步走：

1. **人脸检测**：找到人脸在图片中的位置
2. **轮廓提取**：在人脸区域内提取边缘轮廓

### 第一步：人脸检测

最直接的方法是用 OpenCV 自带的 **Haar 级联分类器**。OpenCV 提供了预训练好的人脸检测模型，一个 XML 文件搞定。

```python
import cv2

face_cascade = cv2.CascadeClassifier(
    cv2.data.haarcascades + 'haarcascade_frontalface_default.xml'
)

img = cv2.imread('photo.jpg')
gray = cv2.cvtColor(img, cv2.COLOR_BGR2GRAY)

faces = face_cascade.detectMultiScale(
    gray,
    scaleFactor=1.1,
    minNeighbors=5,
    minSize=(30, 30)
)
```

`detectMultiScale` 返回一个人脸矩形列表，每个矩形是 `(x, y, w, h)`。参数调一下 `scaleFactor` 和 `minNeighbors` 可以平衡检测率和误检率。

### 第二步：轮廓提取

拿到人脸区域后，用 **Canny 边缘检测** 提取轮廓：

```python
for (x, y, w, h) in faces:
    roi = gray[y:y+h, x:x+w]

    # 先高斯模糊去噪
    blurred = cv2.GaussianBlur(roi, (5, 5), 0)

    # Canny 边缘检测
    edges = cv2.Canny(blurred, threshold1=50, threshold2=150)
```

Canny 的两个阈值是关键：低阈值控制弱边缘，高阈值控制强边缘。调了半天，最后发现 50/150 对大多数照片效果还行。

## 效果

说不上完美，但作为入门级的课程项目，效果可以接受：

- 正脸、光线好的照片检测比较准
- 侧脸、遮挡、光线暗的情况下就容易翻车
- 多人脸照片也能处理，每张脸分别提取轮廓

## 改进方向

回头看不难发现一堆可以改进的地方：

- **Haar 级联分类器太老了**：现在都是 MTCNN、RetinaFace、MediaPipe 的时代，检测率和鲁棒性完全不在一个量级
- **Canny 阈值是硬编码的**：不同图片需要的阈值不一样，应该用 Otsu 自适应阈值
- **轮廓很粗糙**：可以加形态学操作（膨胀、腐蚀）让轮廓更平滑
- **没有考虑肤色**：如果能结合肤色检测做预处理，可以减少背景干扰

不过作为大作业来说够了。当时最大的收获不是算法本身，而是第一次完整地跑通了一个计算机视觉的 pipeline：加载图片 -> 预处理 -> 检测 -> 后处理 -> 输出结果。这种端到端的体验对理解整个流程很有帮助。
