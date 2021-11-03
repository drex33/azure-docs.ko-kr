---
title: 오디오 처리 - Speech Service
titleSuffix: Azure Cognitive Services
description: Microsoft 오디오 스택의 오디오 처리 및 기능에 대한 개요입니다.
services: cognitive-services
author: hasyashah
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/17/2021
ms.author: hasshah
ms.custom: devx-track-csharp, ignite-fall-2021
ms.openlocfilehash: f0bc80a9c248b9171a89bead1971cb7d5f4ce0fe
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131102747"
---
# <a name="audio-processing"></a>오디오 처리

오디오 처리는 오디오 품질 향상을 목표로 오디오 스트림에 적용되는 향상된 기능을 나타냅니다. 향상된 기능 집합이 결합된 것을 종종 오디오 처리 스택이라고 합니다. 오디오 품질을 개선하는 목표는 음성 처리 및 통신과 같은 다양한 시나리오로 더욱 세분화될 수 있습니다. 일반적인 향상된 기능의 예로는 AGC(자동 게인 제어), 노이즈 비제어 및 AEC(음향 에코 취소)가 있습니다.

시나리오/사용 사례에 따라 오디오 처리 스택의 동작에 영향을 주는 다양한 최적화가 필요합니다. 예를 들어 전화 통화와 같은 통신 시나리오에서는 처리가 적용된 후 오디오 신호에 약간의 왜곡이 있을 수 있습니다. 사람이 높은 정확도로 음성을 계속 이해할 수 있기 때문입니다. 그러나 사람이 에코에서 자신의 음성을 들은 것은 허용되지 않으며 방해가 될 수 없습니다. 이는 왜곡된 오디오가 기계 학습 음성 인식 모델의 정확도에 부정적인 영향을 줄 수 있지만 사소한 수준의 에코 잔차를 가질 수 있는 음성 처리 시나리오와 대조됩니다.

## <a name="microsoft-audio-stack"></a>Microsoft 오디오 스택

Microsoft 오디오 스택은 음성 처리 시나리오에 최적화된 향상된 기능 집합입니다. 여기에는 키워드 인식 및 음성 인식과 같은 예제가 포함됩니다. 입력 오디오 신호에서 작동하는 다양한 향상된 기능/구성 요소로 구성됩니다.

* **노이즈 비제어** - 배경 노이즈 수준을 줄입니다.
* **운형** - 소리의 원점 지역화 및 여러 마이크를 사용하여 오디오 신호를 최적화합니다.
* **Dereverberation** - 환경의 표면에서 소리의 반사를 줄입니다.
* **음향 에코 취소** - 마이크 입력이 활성 상태인 동안 디바이스에서 오디오가 재생되지 않도록 합니다.
* **자동 게인 제어** - 소프트 스피커, 긴 거리 또는 보정되지 않은 마이크를 고려하도록 사용자의 음성 수준을 동적으로 조정합니다.

[![Microsoft Audio Stack의 향상된 기능 ](media/audio-processing/mas-block-diagram.png) 다이어그램 차단 ](media/audio-processing/mas-block-diagram.png#lightbox)

Microsoft 오디오 스택은 다양한 Microsoft 제품을 제공합니다.
* **Windows** - Microsoft Audio Stack은 Speech 오디오 범주를 사용할 때 기본 음성 처리 파이프라인입니다. 
* **Microsoft Teams 표시 및 Microsoft Teams Room 디바이스** - Microsoft Teams 디스플레이 및 Teams Room 디바이스는 Microsoft Audio Stack을 사용하여 Cortana 고품질의 음성 기반 환경을 사용하도록 설정합니다.

### <a name="pricing"></a>가격 책정

Speech SDK와 함께 Microsoft Audio Stack을 사용하는 비용은 없습니다.

### <a name="minimum-requirements-to-use-microsoft-audio-stack"></a>Microsoft 오디오 스택을 사용하기 위한 최소 요구 사항

Microsoft Audio Stack은 다음 요구 사항을 충족할 수 있는 모든 제품 또는 애플리케이션에서 사용할 수 있습니다.
* **원시 오디오** - Microsoft Audio Stack은 최상의 결과를 생성하기 위해 입력으로 원시(즉, 처리되지 않은) 오디오가 필요합니다. 이미 처리된 오디오를 제공해도 오디오 스택이 높은 품질로 향상된 기능을 수행할 수 있는 기능이 제한됩니다.
* **마이크 기하 도형** - Microsoft Audio Stack에서 제공하는 모든 향상된 기능을 올바르게 수행하려면 디바이스의 각 마이크에 대한 기하 도형 정보가 필요합니다. 정보에는 마이크 수, 마이크의 물리적 배열 및 좌표가 포함됩니다. 최대 16개의 입력 마이크 채널이 지원됩니다. 
* **루프백 또는 참조 오디오** - 어쿠스틱 에코 취소를 수행하려면 디바이스에서 재생되는 오디오를 나타내는 오디오 채널이 필요합니다. 
* **입력 형식** - Microsoft Audio Stack은 16kHz의 정수 배수인 샘플 속도에 대한 다운샘플링을 지원합니다. 최소 샘플링 속도는 16kHz가 필요합니다. 또한 지원되는 형식은 32비트 IEEE little endian float, 32비트 little endian signed int, 24비트 little endian signed int, 16비트 little endian signed int 및 8비트 signed int입니다.

## <a name="next-steps"></a>다음 단계

* [Microsoft Audio Stack의 Speech SDK 통합에 대해 자세히 알아봅니다.](audio-processing-speech-sdk.md)
