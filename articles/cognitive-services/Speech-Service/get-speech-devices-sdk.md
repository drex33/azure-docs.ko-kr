---
title: 음성 디바이스 SDK 가져오기
titleSuffix: Azure Cognitive Services
description: Speech Service는 다양한 디바이스 및 오디오 원본에서 작동합니다. 이제 일치하는 하드웨어 및 소프트웨어를 사용하여 음성 애플리케이션을 한 단계 업그레이드할 수 있습니다. 이 문서에서는 Speech Devices SDK에 대한 액세스를 가져오고 개발을 시작하는 방법을 알아봅니다.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/14/2019
ms.author: pafarley
ms.openlocfilehash: fe91a9d288b6fb512693b909098d2e2d5130d9e4
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124771938"
---
# <a name="get-the-cognitive-services-speech-devices-sdk"></a>Cognitive Services Speech Devices SDK 가져오기

Speech Devices SDK는 특수 작성된 개발 키트 및 다양한 마이크 배열 구성과 함께 작동하도록 설계된 사전 튜닝된 라이브러리입니다.

## <a name="choose-a-development-kit"></a>개발 키트 선택

|디바이스|사양|Description|시나리오|
|--|--|--|--|
|[Azure Percept Audio DK](../../azure-percept/overview-azure-percept-audio.md)<br>[설정](../../azure-percept/quickstart-percept-dk-unboxing.md) / [빠른 시작](../../azure-percept/quickstart-percept-audio-setup.md)![Azure Percept Audio DK](./media/speech-devices-sdk/azure-percept-audio.png)|XMOS 코덱이 있는 4 마이크 선형 배열. <br> Linux| 에지 디바이스에 음성 AI(인공 지능) 기능을 추가하는 액세서리 디바이스입니다. 여기에는 미리 구성된 오디오 프로세서와 4 마이크 선형 배열이 포함되어 있으며, Azure Cognitive Services를 활용하여 음성 명령, 키워드 포착 및 원거리 음성 지원을 사용할 수 있습니다. 이 디바이스는 가장 강력하고 간결한 일체형 음성 디바이스 SDK와 원활하게 통합할 수 있도록 Azure Percept DK, Azure Percept Studio 및 기타 Azure 에지 관리 서비스와 함께 제공됩니다.|대화 기록, 로봇 공학, 스마트 빌딩, 제조업, 농업|
|[Azure Kinect DK](https://azure.microsoft.com/services/kinect-dk/)<br>[설치](../../kinect-dk/set-up-azure-kinect-dk.md) / [빠른 시작](./speech-devices-sdk-quickstart.md?pivots=platform-windows%253fpivots%253dplatform-windows)![Azure Kinect DK](media/speech-devices-sdk/device-azure-kinect-dk.jpg)|7 마이크 배열 RGB 및 깊이 카메라 <br>[Windows](./speech-devices-sdk-quickstart.md?pivots=platform-windows%253fpivots%253dplatform-windows)/[Linux](./speech-devices-sdk-quickstart.md?pivots=platform-linux%253fpivots%253dplatform-linux)|정교한 컴퓨터 비전 및 음성 모델을 구축하기 위한 고급 AI(인공 지능) 센서가 포함된 공간 컴퓨팅 개발자 키트입니다. 이 키트는 동급 최고의 공간 마이크 배열과 깊이 카메라를 비디오카메라 및 방향 센서와 결합하며, Azure Cognitive Services와 원활하게 통합하는 여러 모드, 옵션 및 SDK가 소형 디바이스에 모두 포함되어 있습니다.|대화 전사, 로봇 공학, 스마트 빌딩|
|[Urbetter Dev Kit](http://www.urbetter.com/products_56/278.html)![URbetter DDK](media/speech-devices-sdk/device-urbetter.jpg)|7 마이크 배열, ARM SOC, WIFI, 이더넷, HDMI, USB 카메라 <br>Linux|Microsoft 마이크 배열을 조정하고 HDMI/이더넷 및 기타 USB 주변 디바이스와 같은 확장 I/O를 지원하는 업계 수준의 Speech Devices SDK <br> [Urbetter에 문의](http://www.urbetter.com/products_56/278.html)|대화 전사, 교육, 병원, 로봇, OTT Box, 음성 에이전트, 드라이브 스루|
|[Roobo Smart Audio Dev Kit](http://ddk.roobo.com)<br>[설치](speech-devices-sdk-roobo-v1.md) / [빠른 시작](./speech-devices-sdk-quickstart.md?pivots=platform-android%253fpivots%253dplatform-android)![Roobo Smart Audio Dev Kit](media/speech-devices-sdk/device-roobo-v1.jpg)|7 마이크 배열, ARM SOC, WIFI, 오디오 출력, IO <br>[Android](./speech-devices-sdk-quickstart.md?pivots=platform-android%253fpivots%253dplatform-android)|고품질 전사 및 음성 시나리오를 개발하기 위해 Microsoft 마이크 배열 및 전면 처리 SDK를 적용하는 최초의 Speech Devices SDK|대화 전사, 스마트 스피커, 음성 에이전트, 착용식|
|Roobo Smart Audio Dev Kit 2<br>[설치](speech-devices-sdk-roobo-v2.md)<br>![Roobo Smart Audio Dev Kit 2](media/speech-devices-sdk/device-roobo-v2.jpg)|7 Mic 배열, ARM SOC, WIFI, Bluetooth, IO <br>Linux|비용 효율적인 참조 디자인에서 대체 OS 및 더 많은 기능을 제공하는 2세대 Speech Devices SDK입니다.|대화 전사, 스마트 스피커, 음성 에이전트, 착용식|


## <a name="download-the-speech-devices-sdk"></a>Speech Devices SDK 다운로드

[Speech Devices SDK](./speech-devices-sdk.md)를 다운로드합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Speech Devices SDK 시작](./speech-devices-sdk-quickstart.md?pivots=platform-android)