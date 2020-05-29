---
layout: post
title: Use YoloV3-spp on Deepstream SDK 5.0
author: Haneul Oscar Lee
date: '2020-05-29 10:22:23 +0530'
category: tech
summary: YOLOV3-spp and Deepstream SDK 5.0
thumbnail: yolov3-spp.png
---

### [See in KOR](#kor)   

### [See in ENG](#eng)

<br><br><br>


## KOR
----------

안녕하세요, 오스카입니다.   
이번 포스트에서는 NVIDIA Deepstream SDK 5.0에서 YoloV3-spp를 사용

#### Deepstream SDK 5.0

2020년 5월 1일, NVIDIA에서 Deepstream SDK 5.0 developer preview 버전과 함께 기존에 지원되지 않던 새로운  Yolo모델을 지원하기 시작했습니다.   

이전 버전인 Deepstream 4.0에서는 YOLOV3 기본 모델 이외에는 지원하지 않았기 때문에 불편함이 참 많았는데요.   
5.0 버전에서는 많은 부분이 수정 되었습니다.

그 중 가장 큰 변화는 YoloV3-spp 모델이 지원 된다는것입니다.   
또 이와 함께 많은 코드들이 수정되었는데요, 기존에 int 포멧으로 BoungingBox 위치를 결정했던 부분이나, route 레이어에서 concatnation이 2개일때만 코드가 정상 작동하는 문제나, maxpool 레이어에서 padding이 되지 않던 문제들이 수정되었습니다. 


위 수정사항은 `/opt/nvidia/deepstream/deepstream-5.0/sources/objectDetector_Yolo/nvdsinfer_custom_impl_Yolo` 안에서 확인하실수 있으며, 
이 포스트는 [NGC에 있는 독커 컨테이너](https://ngc.nvidia.com/catalog/containers/nvidia:deepstream)를 기준으로 작성할것이니 참고하시길 바랍니다.


#### YoloV3-spp 와 Yolo V4

2020년 4월 23일, Yolo의 새 버전 YoloV4가 새로 나왔습니다.

Backbone을 CSPDarkNet53, Neck을 SPP + PAN을 사용하여 성능과 속도면에서 큰 향상을 거두었으며, 실제로 사용해본 결과 Bounding Box의 정확도 역시 상당히 향상되었습니다.   
Activation function도 기존의 leakyReLU가 아닌 mish 로 수정되었습니다.   
자세한 내용은 [YoloV4 github repo](https://github.com/AlexeyAB/darknet)를 참고하시면 되며, 
평소에 자주 참고하는 Hoya012님이 [논문 리뷰](https://hoya012.github.io/blog/yolov4/)를 해주셔서 링크를 같이 참조합니다.    
안타깝게도 Deepstream SDK 5.0에서는 아직 YoloV4를 지원하지 않기 때문에, YoloV4의 핵심 방법중 하나인 YoloV3-spp를 deepstream에서 사용하는 방법을 알아보겠습니다. 

----------
#### YoloV3-spp를 Deepstream 5.0에서 사용하기

yoloV3-spp [weights](https://pjreddie.com/media/files/yolov3-spp.weights) 와 [config](https://raw.githubusercontent.com/AlexeyAB/darknet/master/cfg/yolov3-spp.cfg) 파일을 우선 다운로드 합니다.


##### 요구사항

**Ubuntu 18.04**: 위 예제는 모두 Ubuntu 18.04버전에서 실행하였습니다. 이 외의 버전에서도 작동하는지는 확인하지 않았습니다.

**nvidia-docker** 설치: [Nvidia-docker github](https://github.com/NVIDIA/nvidia-docker) 를 따라서 설치하면 됩니다.   

**NVIDIA display driver version 440+**: 엔디비아 드라이버 (버전 440+)가 host컴퓨터에 설치되어 있어야합니다.   

**Deepstream 이미지 pull**: docker pull nvcr.io/nvidia/deepstream:5.0-dp-20.04-devel 커멘트로 이미지를 pull합니다.   

##### Docker 실행

우선은 docker를 실행하기 전 host 터미널에서    
```sh
xhost +
```
를 실행합니다.   
해당 명령어는 컴퓨터를 재 시작할때마다 해주어야 합니다.   

그 후 컨테이너 생성을 해줍니다.    
```sh
docker run --gpus all -it --name 컨테이너_이름 -v /tmp/.X11-unix:/tmp/.X11-unix -e DISPLAY=$DISPLAY --env="QT_X11_NO_MITSHM=1" --shm-size=1g --ulimit memlock=-1 --ulimit stack=67108864 -w /opt/nvidia/deepstream/deepstream-5.0  nvcr.io/nvidia/deepstream:5.0-dp-20.04-devel
```
host와 container 사이의 파일공유를 하고싶으면 `/tmp/.X11-unix` 폴더를 통해서 파일 공유를 할 수 있습니다   

VS Code에서 작업하실 경우 `remote container` extension을 사용하시면 더욱 간편하게 파일공유 및 코드 수정을 할 수 있습니다.

##### 빌드

올바른 CUDA 버전을 export해줍니다.    
NGC 컨테이너를 사용하셨다면 10.2 버전입니다.

```sh
cd /opt/nvidia/deepstream/deepstream-5.0/sources/objectDetector_Yolo
export CUDA_VER=10.2
make -C nvdsinfer_custom_impl_Yolo
```

##### deepstream-app 예제 실행하기

아까 다운로드 받은 yolov3-spp.weight 파일과 yolov3-spp.cfg 파일을 현재 폴더로 옮겨줍니다.

```sh
deepstream-app -c deepstream_app_config_yoloV3.txt
```



<br><br><br>

## ENG
----------

Hello, this is Oscar.   
On this post, I will share How to use YoloV3-spp on Deepstream SDK 5.0

#### Deepstream SDK 5.0

On May 1st, 2020, NVIDIA announced Deepstream SDK 5.0 developer preview version, and support new Yolo model which does not supported on previous version.

The previous version, Deepstream 4.0, is uncomfortable because it does not support another Yolo model such as CSPnet or yolo-spp but YoloV3 model only.
But 5.0 version does.

The biggest change is that YoloV3-spp model is now supported.
And many codes are revised. 
They fixed the problem such that bounding box position is saved as int, route layer only concate 2 layer and no padding on maxpool layer. 

You can check the update on `/opt/nvidia/deepstream/deepstream-5.0/sources/objectDetector_Yolo/nvdsinfer_custom_impl_Yolo` folder.   
And this post will be based on the [NGC Docker container](https://ngc.nvidia.com/catalog/containers/nvidia:deepstream).


#### YoloV3-spp and Yolo V4

on April 23nd, 2020, YoloV4, lastest version of Yolo released.

Yolo V4 use CSPDarkNet53 as backbone, SPP+PAN as its neck. 
As a result of my actual use, the accuracy of the Bounding Box has also improved considerably.   
Activation function also changed to mish activation function not leakyReLU.   
You can see the detail on [YoloV4 github repo](https://github.com/AlexeyAB/darknet), and I attach additional link of Hoya012's [YoloV4 paper review](https://hoya012.github.io/blog/yolov4/). (This link is wrote in Korean)   
This time, I will introduce how to use YoloV3-spp on deepstream because unfortunetly deepstream 5.0 does not support YoloV4.

----------
#### Adapting YoloV3-spp on Deepstream SDK 5.0

Download yoloV4 [weights](https://pjreddie.com/media/files/yolov3-spp.weights) and [config](https://raw.githubusercontent.com/AlexeyAB/darknet/master/cfg/yolov3-spp.cfg) files

##### Run docker

At the host terminal, run the command below.
```sh
xhost +
```
This command should be run when the computer is restarted.

After that, create docker container.
```sh
docker run --gpus all -it --name CONTAINER_NAME -v /tmp/.X11-unix:/tmp/.X11-unix -e DISPLAY=$DISPLAY --env="QT_X11_NO_MITSHM=1" --shm-size=1g --ulimit memlock=-1 --ulimit stack=67108864 -w /opt/nvidia/deepstream/deepstream-5.0  nvcr.io/nvidia/deepstream:5.0-dp-20.04-devel
```
Yon can share file between host and container with `/tmp/.X11-unix` folder.

If you use VS Code, you can use `remote container` extension to share file and write code more easily.


##### Build

Export correct CUDA version (e.g. 10.2, 10.1) and make   
If you use NGC container, the version is 10.2

```sh
cd /opt/nvidia/deepstream/deepstream-5.0/sources/objectDetector_Yolo
export CUDA_VER=10.2
make -C nvdsinfer_custom_impl_Yolo
```

##### Run deepstream-app Example

move `yolov3-spp.weight` and `yolov3-spp.cfg` file to current folder.

```sh
deepstream-app -c deepstream_app_config_yoloV4.txt
```