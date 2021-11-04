---
title: Speech Devices SDK - Speech Services
titleSuffix: Azure Cognitive Services
description: Speech Devices SDK를 시작합니다. Speech Service는 다양한 디바이스 및 오디오 원본에서 작동합니다. Speech Devices SDK는 특별히 만들어진 마이크 배열 개발 키트와 쌍으로 연결된 미리 조정된 라이브러리입니다.
services: cognitive-services
author: eric-urban
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: eur
ms.openlocfilehash: 220d925eb774b14d0a79940381774cf3c89335d2
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131506413"
---
# <a name="what-is-the-speech-devices-sdk"></a>Speech Devices SDK란?

[Speech Service](overview.md)는 다양한 디바이스 및 오디오 원본에서 작동합니다. 이제 일치하는 하드웨어 및 소프트웨어를 사용하여 음성 애플리케이션을 한 단계 업그레이드할 수 있습니다. Speech Devices SDK는 특별히 만들어진 마이크 배열 개발 키트와 쌍으로 연결된 미리 조정된 라이브러리입니다.

Speech Devices SDK는 다음을 수행하는 데 유용합니다.

- 새 음성 시나리오를 신속하게 테스트합니다.
- 클라우드 기반 Speech Service를 디바이스에 더 쉽게 통합합니다.
- 고객을 위한 탁월한 사용자 환경을 만듭니다.

Speech Devices SDK는 [Speech SDK](speech-sdk.md)를 사용합니다. 디바이스의 마이크 배열과 함께 고급 오디오 처리 알고리즘을 사용하여 오디오를 [Speech Service](overview.md)로 보냅니다. 잡음 제거, 반향 제거, 빔 형성 및 탈반향을 통해 정확한 원거리 [음성 인식](speech-to-text.md)을 제공합니다.

또한 Speech Devices SDK를 사용하여 고유한 [사용자 지정 키워드](./custom-keyword-basics.md)가 있는 주변 장치를 구축할 수 있습니다. 사용자 지정 키워드는 브랜드 고유의 사용자 상호 작용을 시작하는 단서를 제공합니다.

Speech Devices SDK는 [음성 도우미](./voice-assistants.md), 드라이브 스루 주문 시스템, [대화 전사](./conversation-transcription.md) 및 스마트 스피커와 같은 다양한 음성 지원 시나리오를 지원합니다. 예를 들어, 사용자에게 텍스트로 응답하고, 기본 또는 [사용자 지정 음성](./how-to-custom-voice-create-voice.md)으로 응답하고, 검색 결과를 제공하고, 다른 언어로 [번역](speech-translation.md)할 수 있습니다. 여러분이 무엇을 빌드할지 기대가 됩니다!

## <a name="get-the-speech-devices-sdk"></a>음성 디바이스 SDK 가져오기

### <a name="android"></a>Android

Android용 Speech Devices SDK는 [Roobo v1](speech-devices-sdk-roobo-v1.md) 및 이와 동등한 디바이스를 지원합니다. 이러한 디바이스에서는 최신 버전의 [Android Speech Devices SDK](https://aka.ms/sdsdk-download-android)를 다운로드할 수 있습니다.


휴대폰이나 모바일 디바이스 등의 다른 Android 디바이스가 있는 경우 [Android Speech SDK](speech-sdk.md)로 시작합니다.


### <a name="windows"></a>Windows

Windows의 경우 샘플 애플리케이션은 플랫폼 간 Java 애플리케이션으로 제공됩니다. [JRE Speech Devices SDK](https://aka.ms/sdsdk-download-JRE)의 최신 버전을 다운로드합니다.
애플리케이션은 Speech SDK 패키지와 64비트 Windows 기반의 Eclipse Java IDE(v4)를 사용하여 빌드됩니다. 64비트 Java 8 JRE(Java Runtime Environment)에서 실행됩니다.

### <a name="linux"></a>Linux

Linux의 경우 샘플 애플리케이션은 플랫폼 간 Java 애플리케이션으로 제공됩니다. [JRE Speech Devices SDK](https://aka.ms/sdsdk-download-JRE)의 최신 버전을 다운로드합니다.
애플리케이션은 Speech SDK 패키지와 64비트 Linux(Ubuntu 16.04, Ubuntu 18.04, Debian 9, RHEL 8, CentOS 8) 기반의 Eclipse Java IDE(v4)를 사용하여 빌드됩니다. 64비트 Java 8 JRE(Java Runtime Environment)에서 실행됩니다.

향후 출시될 디바이스인 [Roobo v2 DDK](https://aka.ms/sdsdk-download-roobov2), [Urbetter DDK](https://aka.ms/sdsdk-download-urbetter), [GGEC Speaker](https://aka.ms/sdsdk-download-speaker), [Linux ARM32](https://aka.ms/sdsdk-download-linux-arm32) 및 [Linux ARM64](https://aka.ms/sdsdk-download-linux-arm64)를 지원하기 위해 추가 이진 파일이 제공됩니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [음성 디바이스 선택](get-speech-devices-sdk.md)
> [!div class="nextstepaction"]
> [평가판 Speech Service 구독 키 받기](overview.md#try-the-speech-service-for-free)
