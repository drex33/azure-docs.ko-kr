---
title: Azure Percept DK 및 Vision 디바이스 개요
description: Azure Percept DK 및 Azure Percept Vision에 대해 자세히 알아보세요.
author: MrHamlet
ms.author: amiyouss
ms.service: azure-percept
ms.topic: conceptual
ms.date: 03/23/2021
ms.custom: template-concept, ignite-fall-2021
ms.openlocfilehash: 85e90d90e1f02686d02f435009c14f334eac6a98
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133060425"
---
# <a name="azure-percept-dk-and-vision-device-overview"></a>Azure Percept DK 및 Vision 디바이스 개요

Azure Percept DK는 [Azure Percept Studio](./overview-azure-percept-studio.md)를 사용하여 비전 및 오디오 AI 솔루션 개발 목적으로 설계된 에지 AI 개발 키트입니다. Azure Percept DK는 [Microsoft 온라인 스토어](https://go.microsoft.com/fwlink/p/?LinkId=2155270)에서 구매할 수 있습니다.

> [!div class="nextstepaction"]
> [지금 구입](https://go.microsoft.com/fwlink/p/?LinkId=2155270)

</br>

> [!VIDEO https://www.youtube.com/embed/Qj8NGn-7s5A]

## <a name="key-features"></a>주요 특징

- 에지에서 AI 실행. 기본 제공 하드웨어 가속을 사용하면 개발자 키트는 클라우드에 연결하지 않고도 AI 모델을 실행할 수 있습니다.

- 기본 제공되는 신뢰할 수 있는 루트 하드웨어의 보안입니다. [Azure Percept 보안](./overview-percept-security.md)에 대한 자세한 정보를 알아봅니다.

- [Azure Percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819) 및 기타 Azure 서비스(예: Azure IoT Hub, Azure Cognitive Services 및 [Live Video Analytics](../azure-video-analyzer/video-analyzer-docs/overview.md))의 원활한 통합.

- AI 오디오 솔루션을 빌드하기 위한 선택적 액세서리인 [Azure Percept Audio](./overview-azure-percept-audio.md)와 호환됩니다.

- ONNX 및 TensorFlow와 같은 타사 AI 도구를 지원합니다.

- 무한 디바이스 탑재 구성을 허용하는 80/20 레일 시스템과의 통합입니다. [80/20 통합](./overview-8020-integration.md)에 대한 자세한 정보를 알아봅니다.

## <a name="hardware-components"></a>하드웨어 구성 요소

- Azure Percept DK 캐리어 보드:
    - NXP iMX8m 프로세서
    - TPM(신뢰할 수 있는 플랫폼 모듈) 버전 2.0
    - Wi-Fi 및 Bluetooth 연결
    - 자세한 내용은 [Azure Percept DK 데이터시트](./azure-percept-dk-datasheet.md)를 참조하세요

- Azure Percept Vision SoM(system-on-module):
    - Intel Movidius Myriad X(MA2085) VPU(비전 처리 장치)
    - RGB 카메라 센서
    - 자세한 내용은 [Azure Percept Vision 데이터시트](./azure-percept-vision-datasheet.md)를 참조하세요.

## <a name="getting-started-with-azure-percept-dk"></a>Azure Percept DK 시작하기

- 다음과 같이 개발 키트 설정합니다.
    - [Azure Percept DK 개봉 및 조립](./quickstart-percept-dk-unboxing.md)
    - [Azure Percept DK 설정 환경 완료](./quickstart-percept-dk-set-up.md)

- 비전 및 오디오 솔루션 빌드를 시작합니다.
    - [Azure Percept Studio에서 코드 없는 비전 솔루션 만들기](./tutorial-nocode-vision.md)
    - [Azure Percept Studio에서 코드 없는 음성 솔루션 만들기(Azure Percept Audio 액세서리 필요)](./tutorial-no-code-speech.md)

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Microsoft 온라인 스토어에서 Azure Percept DK 구매](https://go.microsoft.com/fwlink/p/?LinkId=2155270)
