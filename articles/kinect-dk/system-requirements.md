---
title: Azure Kinect 센서 SDK 시스템 요구 사항
description: Windows 및 Linux에서의 Azure Kinect 센서 SDK에 대한 시스템 요구 사항을 이해합니다.
author: qm13
ms.author: quentinm
ms.custom: CI 115266, CSSTroubleshooting
manager: dcscontentpm
ms.prod: kinect-dk
ms.date: 03/05/2021
ms.topic: article
keywords: azure, kinect, 시스템 요구 사항, CPU, GPU, USB, 설정, 설치, 최소, 요구 사항
ms.openlocfilehash: ba6782ad0a0278f1f66266c8abbe91d33cb5bf68
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131085851"
---
# <a name="azure-kinect-sensor-sdk-system-requirements"></a>Azure Kinect 센서 SDK 시스템 요구 사항

이 문서에서는 센서 SDK를 설치하고 Azure Kinect DK를 성공적으로 배포하는 데 필요한 시스템 요구 사항에 대한 세부 정보를 제공합니다.

## <a name="supported-operating-systems-and-architectures"></a>지원되는 운영 체제 및 아키텍처

- Windows 10 2018년 4월(버전 1803, OS 빌드 17134) 릴리스(x64) 이상 버전
- Linux Ubuntu 18.04(x64), OpenGL v4.4 이상 버전을 사용하는 GPU 드라이버 사용

센서 SDK는 네이티브 C/C++ Windows 애플리케이션용 Windows API(Win32)에 사용 가능합니다. SDK는 현재 UWP 애플리케이션에 사용할 수 없습니다. Azure Kinect DK는 S 모드의 Windows 10에 대해서는 지원되지 않습니다.

## <a name="development-environment-requirements"></a>개발 환경 요구 사항

센서 SDK 개발에 기여하려면 [GitHub](https://github.com/Microsoft/Azure-Kinect-Sensor-SDK)를 방문하세요.

## <a name="minimum-host-pc-hardware-requirements"></a>호스트 PC 하드웨어 최소 요구 사항

호스트 PC 하드웨어 요구 사항은 호스트 PC에서 실행되는 애플리케이션/알고리즘/센서 프레임 속도/해상도에 따라 달라집니다. Windows에 대해 권장되는 센서 SDK 최소 구성은 다음과 같습니다.

- 7세대 Intel&reg; CoreTM i3 프로세서(듀얼 코어 2.4GHz, HD620 GPU 이상)
- 4GB 메모리
- 전용 USB3 포트
- OpenGL 4.4 또는 DirectX 11.0을 지원하는 그래픽 드라이버

더 낮은 급이거나 더 오래된 CPU도 사용 사례에 따라서 작동할 수도 있습니다.

사용 중인 Windows/Linux 운영 체제와 그래픽 드라이버 간에 성능도 다릅니다.

## <a name="body-tracking-host-pc-hardware-requirements"></a>신체 추적 호스트 PC 하드웨어 요구 사항

신체 추적 PC의 호스트 요구 사항은 일반 PC의 호스트 요구 사항보다 더 엄격합니다. Windows에 대해 권장되는 신체 추적 SDK 최소 구성은 다음과 같습니다.

- 7세대 Intel&reg; coreTM i5 프로세서 (쿼드 코어 2.4GHz 이상)
- 4GB 메모리
- NVIDIA GEFORCE GTX 1050 또는 동급
- 전용 USB3 포트

권장되는 최소 구성은 5명을 추적하는 30fps의 K4A_DEPTH_MODE_NFOV_UNBINNED 깊이 모드를 가정합니다. 더 낮은 급이거나 더 오래된 CPU 및 NVIDIA GPU도 사용 사례에 따라서 작동할 수도 있습니다.

## <a name="usb3"></a>USB3

USB 호스트 컨트롤러에는 알려진 호환성 문제가 있습니다. 자세한 내용은 [문제 해결 페이지](troubleshooting.md#usb3-host-controller-compatibility)에서 확인할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [Azure Kinect DK 개요](about-azure-kinect-dk.md)

- [Azure Kinect DK 설정](set-up-azure-kinect-dk.md)

- [Azure Kinect 신체 추적 설정](body-sdk-setup.md)
