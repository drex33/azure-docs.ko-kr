---
title: Azure IoT Edge for Linux on Windows의 GPU 가속 | Microsoft Docs
description: 호스트 디바이스 GPU를 사용하도록 Azure IoT Edge for Linux on Windows 가상 머신을 구성하는 방법에 관해 알아봅니다.
author: kgremban
manager: kgremban
ms.author: kgremban
ms.date: 06/22/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
monikerRange: =iotedge-2018-06
ms.openlocfilehash: 5d484f143d15acb561bf81558412f02ad71b1c64
ms.sourcegitcommit: 8178cd2d9a47a67bb324483bd0879a57591706a1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/25/2021
ms.locfileid: "133109894"
---
# <a name="gpu-acceleration-for-azure-iot-edge-for-linux-on-windows-preview"></a>Azure IoT Edge for Linux on Windows의 GPU 가속(미리 보기)

GPU는 병렬 처리 기능을 제공하고 일반적으로 비전 기반 추론을 CPU보다 더 빠르게 실행할 수 있기 때문에 AI 계산에 널리 사용됩니다. AI 및 기계 학습 애플리케이션을 더 효과적으로 지원하기 위해 Azure IoT Edge for Linux on Windows는 가상 머신의 Linux 모듈에 GPU를 노출할 수 있습니다.

> [!NOTE]
> 아래 자세히 설명된 GPU 가속 기능은 미리 보기 상태이며 변경될 수 있습니다.

Azure IoT Edge for Linux on Windows는 다음을 포함한 여러 가지 GPU 통과 기술을 지원합니다.

* **DDA(직접 디바이스 할당)** - GPU 코어가 Linux 가상 머신이나 호스트에 할당됩니다.

* **GPU-PV(GPU-반가상화)** - GPU가 Linux 가상 머신과 호스트 간에 공유됩니다.

디바이스 GPU 하드웨어의 지원되는 기능과 일치하도록 배포하는 동안 적절한 통과 방법을 선택해야 합니다.

> [!IMPORTANT]
> 이 기능에는 NVIDIA Corporation이나 해당 사용 허가자가 개발하고 소유하는 구성 요소가 포함될 수 있습니다. 구성 요소 사용은 [NVIDIA 웹 사이트에](https://www.nvidia.com/content/DriverDownload-March2009/licence.php?lang=us) 있는 NVIDIA 최종 사용자 사용권 계약으로 관리됩니다.
>
> GPU 가속 기능을 사용하면 NVIDIA 최종 사용자 사용권 계약을 수락하고 이에 동의하는 것입니다.

## <a name="prerequisites"></a>필수 구성 요소

Azure IoT Edge for Linux on Windows의 GPU 가속 기능에서는 현재 특정한 GPU 하드웨어 세트를 지원합니다. 또한 이 기능을 사용하려면 구성에 따라 특정 버전의 Windows 필요할 수 있습니다.

지원되는 GPU와 필수 Windows 버전이 아래에 나열되어 있습니다.

* NVIDIA T4(DDA 지원)

  * Windows Server 2019, 빌드 17763 및 현재 누적 업데이트가 모두 설치되어 있습니다.
  * Windows Server 2022
  * Windows 11(Pro, Enterprise, IoT Enterprise)

* NVIDIA GeForce/Quadro(GPU-PV 지원)

  * Windows 10(Pro, Enterprise, IoT Enterprise), 최소 빌드 19044.1263 이상
  * Windows 11(Pro, Enterprise, IoT Enterprise)

* Intel 통합 GPU 선택(GPU-PV 지원)

  * Windows 10(Pro, Enterprise, IoT Enterprise), 최소 빌드 19044.1263 이상
  * Windows 11(Pro, Enterprise, IoT Enterprise)

Intel iGPU 지원은 선택한 프로세서에 사용할 수 있습니다. 자세한 내용은 Intel 드라이버 설명서를 참조하세요.

Windows 10 사용자는 [2021년 11월 업데이트 를](https://blogs.windows.com/windowsexperience/2021/11/16/how-to-get-the-windows-10-november-2021-update/)사용해야 합니다. 설치 후 명령 프롬프트에서 를 실행하여 빌드 버전을 확인할 수 `winver` 있습니다.

## <a name="system-setup-and-installation"></a>시스템 설정 및 설치

다음 섹션에는 설치 및 설치와 관련된 정보가 포함되어 있습니다.

### <a name="nvidia-t4-gpus"></a>NVIDIA T4 GPU

**T4 GPU** 의 경우 Microsoft는 GPU 공급업체의 디바이스 완화 드라이버를 권장합니다. 선택 사항이지만 완화 드라이버를 설치하면 배포의 보안을 강화할 수 있습니다. 자세한 내용은 [직접 디바이스 할당을 사용하여 그래픽 디바이스 배포](/windows-server/virtualization/hyper-v/deploy/deploying-graphics-devices-using-dda#optional---install-the-partitioning-driver)를 참조하세요.

> [!WARNING]
> 하드웨어 디바이스 통과를 사용하도록 설정하면 보안 위험이 증가할 수 있습니다. Microsoft는 해당되는 경우 GPU 공급업체의 디바이스 완화 드라이버를 권장합니다. 자세한 내용은 [개별 디바이스 할당을 사용하여 그래픽 디바이스 배포](/windows-server/virtualization/hyper-v/deploy/deploying-graphics-devices-using-dda)를 참조하세요.

### <a name="nvidia-geforcequadro-gpus"></a>NVIDIA GeForce/Quadro GPU

**GeForce/Quadro GPU** 의 경우 기존 CUDA ML 워크플로와 함께 사용하도록 [WSL(Linux용 Windows 하위 시스템)에 대한 NVIDIA CUDA 지원 드라이버](https://developer.nvidia.com/cuda/wsl)를 다운로드하고 설치합니다. 원래 WSL용으로 개발된 WSL용 CUDA 드라이버는 Azure IoT Edge for Linux on Windows에도 사용됩니다.

Windows 일부 라이브러리는 [WSL과](/windows/wsl/install) linux용 Azure IoT Edge 간에 공유되므로 Windows 10 사용자가 WSL을 설치해야 합니다. 

### <a name="intel-igpus"></a>Intel iG CPU

**Intel iG CPU의** 경우 [WSL GPU가 지원된 Intel 그래픽 드라이버를](https://www.intel.com/content/www/us/en/download-center/home.html?wapkw=quicklink:download-center)다운로드하여 설치합니다.

Windows 일부 라이브러리는 [WSL과](/windows/wsl/install) linux용 Azure IoT Edge 간에 공유되므로 Windows 10 사용자가 WSL을 설치해야 합니다. 

## <a name="using-gpu-acceleration-for-your-linux-on-windows-deployment"></a>Linux on Windows 배포에 GPU 가속 사용

이제 Azure IoT Edge for Linux on Windows를 통해 Windows 환경에서 GPU 가속 Linux 모듈을 배포하고 실행할 준비가 되었습니다. 배포 프로세스에 대한 자세한 내용은 대칭 키를 사용하거나 [X.509 인증서를](how-to-provision-single-device-linux-on-windows-x509.md) [사용하여 Windows 디바이스에서 Linux용 단일 IoT Edge 프로비전하기 위한 가이드에서](how-to-provision-single-device-linux-on-windows-symmetric.md) 찾을 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [Azure IoT Edge for Linux on Windows 배포 만들기](how-to-install-iot-edge-on-windows.md)

* 고유한 비전 기반 기계 학습 애플리케이션을 빌드하는 방법을 보여 주는 솔루션 템플릿인 [Edge의 Vision을 사용하는 GPU 지원 샘플](https://github.com/Azure-Samples/azure-intelligent-edge-patterns/blob/master/factory-ai-vision/Tutorial/Eflow.md)을 사용해 보세요.

* [DDA 설명서](/windows-server/virtualization/hyper-v/plan/plan-for-gpu-acceleration-in-windows-server#discrete-device-assignment-dda) 및 [GPU-PV 블로그 게시물](https://devblogs.microsoft.com/directx/directx-heart-linux/#gpu-virtualization)을 방문하여 GPU 통과 기술에 관해 자세히 알아보세요.
