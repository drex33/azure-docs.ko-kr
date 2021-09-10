---
title: Kubernetes 배포용 Azure Stack Edge 디바이스에서 컴퓨팅 가속 GPU 또는 VPU 사용 | Microsoft Docs
description: Kubernetes 배포용 Azure Stack Edge Pro GPU, Azure Stack Edge Pro R 또는 Azure Stack Edge Mini Ri에서 컴퓨팅 가속 GPU 또는 VPU를 사용하는 방법을 설명합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/26/2021
ms.author: alkohli
ms.openlocfilehash: ad071118eabafdfeaddcf1e2a4738c646f62986d
ms.sourcegitcommit: bb1c13bdec18079aec868c3a5e8b33ef73200592
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/27/2021
ms.locfileid: "114719997"
---
# <a name="use-compute-acceleration-on-azure-stack-edge-pro-gpu-for-kubernetes-deployment"></a>Kubernetes 배포용 Azure Stack Edge Pro GPU에서 컴퓨팅 가속 사용

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

이 문서에서는 Kubernetes 배포를 사용할 때 Azure Stack Edge 디바이스에서 컴퓨팅 가속을 사용하는 방법을 설명합니다. 


## <a name="about-compute-acceleration"></a>컴퓨팅 가속 정보 

CPU(중앙 처리 장치)는 컴퓨터에서 실행되는 대부분의 프로세스에 대한 기본 범용 컴퓨팅입니다. 종종 일부 함수는 일반적으로 CPU의 소프트웨어에서 실행하는 것보다 특정 컴퓨터 하드웨어를 사용하여 실행할 때 더 효율적입니다. 예를 들어, GPU(그래픽 처리 장치)를 사용하여 픽셀 데이터의 처리 속도를 높일 수 있습니다.  

컴퓨팅 가속화는 GPU(그래픽 처리 장치), VPU(비전 처리 장치) 또는 FPGA(필드 프로그래머블 게이트 어레이)를 하드웨어 가속화에 사용하는 Azure Stack Edge 디바이스에 사용되는 용어입니다. Azure Stack Edge 디바이스에 배포되는 대부분의 워크로드는 중요한 타이밍, 다중 카메라 스트림 및/또는 높은 프레임 속도가 수반되며, 이러한 문제에는 특정 하드웨어 가속이 필요합니다.

이 문서에서는 다음 디바이스에 대해 GPU 또는 VPU를 사용하는 경우에 대한 컴퓨팅 가속만 다룹니다.

- **Azure Stack Edge PRO GPU** - 이러한 디바이스에는 1 또는 2 Nvidia T4 Tensor Core GPU를 사용할 수 있습니다. 자세한 내용은 [NVIDIA T4](https://www.nvidia.com/en-us/data-center/tesla-t4/)를 참조하세요.
- **Azure Stack Edge Pro R** - 이러한 디바이스에는 1 Nvidia T4 Tensor Core GPU가 있습니다. 자세한 내용은 [NVIDIA T4](https://www.nvidia.com/en-us/data-center/tesla-t4/)를 참조하세요.
- **Azure Stack Edge Mini R** - 이러한 디바이스에는 하나의 Intel Movidius Myriad X VPU가 있습니다. 자세한 내용은 [Intel Movidius Myriad X VPU](https://www.movidius.com/MyriadX)를 참조하세요.


## <a name="use-gpu-for-kubernetes-deployment"></a>Kubernetes 배포에 GPU 사용

다음 예제 `yaml`은 Azure Stack Edge PRO GPU 또는 GPU가 있는 Azure Stack Edge Pro R 디바이스에 사용할 수 있습니다.

<!--In a production scenario, Pods are not used directly and these are wrapped around higher level constructs like Deployment, ReplicaSet which maintain the desired state in case of pod restarts, failures.-->

```yml
apiVersion: v1
kind: Pod
metadata:
  name: gpu-pod
spec:
  containers:
    - name: cuda-container
      image: nvidia/cuda:9.0-devel
      resources:
        limits:
          nvidia.com/gpu: 1 # requesting 1 GPU
    - name: digits-container
      image: nvidia/digits:6.0
      resources:
        limits:
          nvidia.com/gpu: 1 # requesting 1 GPU
```


## <a name="use-vpu-for-kubernetes-deployment"></a>Kubernetes 배포에 VPU 사용

다음 예제 `yaml`은 VPU가 있는 Azure Stack Edge Mini R 디바이스에 사용할 수 있습니다.

```yml
apiVersion: batch/v1
kind: Job
metadata:
 name: intelvpu-demo-job
 labels:
   jobgroup: intelvpu-demo
spec:
 template:
   metadata:
     labels:
       jobgroup: intelvpu-demo
   spec:
     restartPolicy: Never
     containers:
       -
         name: intelvpu-demo-job-1
         image: ubuntu-demo-openvino:devel
         imagePullPolicy: IfNotPresent
         command: [ "/do_classification.sh" ]
         resources:
           limits:
             vpu.intel.com/hddl: 1
```


## <a name="next-steps"></a>다음 단계

[kubectl을 사용하여 Azure Stack Edge Pro GPU 디바이스에서 PersistentVolume으로 Kubernetes 상태 저장 애플리케이션을 실행](azure-stack-edge-gpu-deploy-stateful-application-static-provision-kubernetes.md)하는 방법을 알아봅니다.
