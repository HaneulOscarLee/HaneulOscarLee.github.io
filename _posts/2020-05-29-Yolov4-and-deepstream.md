---
layout: post
title: Adapt YOLOV4 on Deepstream SDK 5.0
author: Haneul Oscar Lee
date: '2020-05-29 10:22:23 +0530'
category: tech
summary: YOLOV4 and Deepstream SDK 5.0
thumbnail: yolov4.gif
---

* [KOR](#kor)
* [ENG](#eng)


# KOR

안녕하세요, 오스카입니다.   


https://forums.developer.nvidia.com/c/accelerated-computing/intelligent-video-analytics/deepstream-sdk/




# ENG


* Download yolo config and weights files
    - [Download `yolov4.weights` file](https://drive.google.com/open?id=1cewMfusmPjYWbrnuJRuKhPMwRe_b9PaT)
    - [Download `yolov4.cfg` file](https://raw.githubusercontent.com/AlexeyAB/darknet/master/cfg/yolov4.cfg)

### Compile

Export correct CUDA version (e.g. 10.2, 10.1) and make


```sh
export CUDA_VER=10.2
make -C nvdsinfer_custom_impl_Yolo
```
