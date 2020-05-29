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
----------

안녕하세요, 오스카입니다.   
이번 포스트에서는 NVIDIA Deepstream SDK 5.0에서 YoloV4 모델을 이용하는 법을 알아보겠습니다.

## Deepstream SDK 5.0

2020년 5월 1일, NVIDIA에서 Deepstream SDK 5.0 developer preview 버전과 함께 기존에 지원되지 않던 새로운  Yolo모델을 지원하기 시작했습니다.   

이전 버전인 Deepstream 4.0에서는 YOLOV3 기본 모델 이외에는 지원하지 않았기 때문에 불편함이 참 많았는데요.   
5.0 버전에서는 많은 부분이 수정 되었습니다.

그 중 가장 큰 변화는 YoloV3-spp 모델이 지원 된다는것입니다.   
또 이와 함께 많은 코드들이 수정되었는데요, 기존에 int 포멧으로 BoungingBox 위치를 결정했던 부분이나, route 레이어에서 concatnation이 2개일때만 코드가 정상 작동하는 문제나, maxpool 레이어에서 padding이 되지 않던 문제들이 수정되었습니다. 

위 수정사항은 `/opt/nvidia/deepstream/deepstream-5.0/sources/objectDetector_Yolo/nvdsinfer_custom_impl_Yolo` 안에서 확인하실수 있으며, 
이 포스트는 [NGC에 있는 독커 컨테이너](https://ngc.nvidia.com/catalog/containers/nvidia:deepstream)를 기준으로 작성할것이니 참고하시길 바랍니다.


## Yolo V4

2020년 4월 23일, Yolo의 새 버전 YoloV4가 새로 나왔습니다.

Backbone을 CSPDarkNet53, Neck을 SPP + PAN을 사용하여 성능과 속도면에서 큰 향상을 거두었으며, 실제로 사용해본 결과 Bounding Box의 정확도 역시 상당히 향상되었습니다.   
자세한 내용은 [YoloV4 github repo](https://github.com/AlexeyAB/darknet)를 참고하시면 되며, 
평소에 자주 참고하는 Hoya012님이 [논문 리뷰](https://hoya012.github.io/blog/yolov4/)를 해주셔서 링크를 같이 참조합니다. 





그래서 Deepstream 5.0에서 YoloV4 모델을 사용할 수 있도록 코드를 수정하였습니다.




<br><br><br>
# ENG
----------

Hello, this is Oscar.   
On this post, I will share several code which makes available YoloV4 on Deepstream SDK 5.0

## Deepstream SDK 5.0

On May 1st, 2020, NVIDIA announced Deepstream SDK 5.0 developer preview version, and support new Yolo model which does not supported on previous version.

The previous version, Deepstream 4.0, is uncomfortable because it does not support another Yolo model such as CSPnet or yolo-spp but YoloV3 model only.
But 5.0 version does.

The biggest change is that YoloV3-spp model is now supported.
And many codes are revised. 
They fixed the problem such that bounding box position is saved as int, route layer only concate 2 layer and no padding on maxpool layer. 

You can check the update on `/opt/nvidia/deepstream/deepstream-5.0/sources/objectDetector_Yolo/nvdsinfer_custom_impl_Yolo` folder.
which is Docker container of Deepstream.
And this post will be based on the [NGC Docker container](https://ngc.nvidia.com/catalog/containers/nvidia:deepstream).


## Yolo V4

on April 23nd, 2020, YoloV4, lastest version of Yolo, released.

Backbone을 CSPDarkNet53, Neck을 SPP + PAN을 사용하여 성능과 속도면에서 큰 향상을 거두었으며, 실제로 사용해본 결과 Bounding Box의 정확도 역시 상당히 향상되었습니다.
자세한 내용은 [YoloV4 github repo](https://github.com/AlexeyAB/darknet)를 참고하시면 되며, 
평소에 자주 참고하는 Hoya012님이 [논문 리뷰](https://hoya012.github.io/blog/yolov4/)를 해주셔서 링크를 같이 참조합니다. (This link is wrote in Korean.)



* Download yolo config and weights files
    - [Download `yolov4.weights` file](https://drive.google.com/open?id=1cewMfusmPjYWbrnuJRuKhPMwRe_b9PaT)
    - [Download `yolov4.cfg` file](https://raw.githubusercontent.com/AlexeyAB/darknet/master/cfg/yolov4.cfg)

### Compile

Export correct CUDA version (e.g. 10.2, 10.1) and make


```sh
export CUDA_VER=10.2
make -C nvdsinfer_custom_impl_Yolo
```
