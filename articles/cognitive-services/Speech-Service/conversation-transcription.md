---
title: 대화 기록(미리 보기) Speech Service
titleSuffix: Azure Cognitive Services
description: 대화 기록은 인식, 화자 ID 및 분할을 결합하여 모든 대화의 기록을 제공하는 회의용 솔루션입니다.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/26/2021
ms.author: pafarley
ms.openlocfilehash: abef7e44ad2e15bda230d28e8dae74a3fd949f88
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2021
ms.locfileid: "129706334"
---
# <a name="what-is-conversation-transcription-preview"></a>대화 기록(미리 보기)는 무엇입니까?

대화 기록은 음성 인식, 화자 식별, 각 화자에게 문장 귀속(_분할_ 이라고도 함)을 결합하여 모든 대화의 실시간 및/또는 비동기 기록을 제공하는 [음성 텍스트 변환](speech-to-text.md) 솔루션입니다. 대화 기록을 통해 대화에서 화자를 구분하여 누가 언제 무슨 말을 했는지 확인할 수 있으며, 개발자가 손쉽게 다중 화자 분할을 수행하는 애플리케이션에 음성 텍스트 변환을 차가할 수 있습니다.

## <a name="key-features"></a>주요 특징

- **타임스탬프** - 각 화자 발화에는 타임스탬프가 있으므로 언제 구문을 이야기했는지 쉽게 찾을 수 있습니다.
- **읽을 수 있는 음성 텍스트** - 음성 텍스트에 서식과 문장 부호가 자동으로 추가되어 텍스트가 실제 발화와 근접하게 일치합니다.
- **사용자 프로필** - 사용자 음성 샘플을 수집하고 이를 서명 생성으로 전송하여 사용자 프로필을 생성합니다.
- **화자 식별** - 사용자 프로필을 사용하여 화자가 식별되고 각각에게 _화자 식별자_ 가 할당됩니다.
- **다중 화자 분할** - 오디오 스트림과 각 화자 식별자를 합성하여 누가 말을 했는지 파악합니다.
- **실시간 대화 내용 기록** – 대화가 진행되는 동안 누가 언제 무슨 말을 했는지에 대한 실시간 음성 텍스트가 제공됩니다.
- **비동기 대화 내용 기록** – 다중 채널 오디오 스트림을 사용하여 정확도가 높은 음성 텍스트를 제공합니다.

> [!NOTE]
> 대화 기록에는 화자 수에 대한 제한이 없지만 세션당 2~10명의 화자에 최적화되어 있습니다.

## <a name="get-started"></a>시작

시작하려면 실시간 대화 기록 [빠른 시작](how-to-use-conversation-transcription.md)을 참조하세요.

## <a name="use-cases"></a>사용 사례

청각 장애인 및 난청 환자를 포함한 모든 참가자를 포용하는 회의가 되려면 실시간으로 대화 내용 기록을 유지하는 것이 중요합니다. 실시간 모드의 대화 기록에서는 회의 오디오를 사용하고 누가 무슨 말을 하고 있는지 파악하므로 모든 회의 참가자가 음성 텍스트를 팔로우하고 지연 없이 회의에 참여할 수 있습니다.

### <a name="improved-efficiency"></a>효율성 향상

회의 참가자는 회의에 집중할 수 있으며, 메모를 할 필요가 없습니다. 참가자는 회의 도중 메모를 남기다 내용을 놓치는 대신 음성 텍스트를 활용하여 회의에 적극적으로 참여하고 빠르게 다음 단계를 진행할 수 있습니다.

## <a name="how-it-works"></a>작동 방법

대화 기록 작동 방식의 대략적인 개요입니다.

![대화 기록 가져오기 다이어그램](media/scenarios/conversation-transcription-service.png)

## <a name="expected-inputs"></a>필요한 입력

- **다중 채널 오디오 스트림** – 사양 및 설계 세부 정보는 [Microsoft 음성 디바이스 SDK 마이크](./speech-devices-sdk-microphone.md)를 참조하세요. 개발 키트에 대해 자세히 알아보거나 구매하려면 [Microsoft 음성 디바이스 SDK 가져오기](./get-speech-devices-sdk.md)를 참조하세요.
- **사용자 음성 샘플** – 대화 전사에는 화자 식별을 위한 대화 전에 사용자 프로필이 필요합니다. 각 사용자로부터 오디오 녹음을 수집한 다음, [서명 생성 서비스](https://aka.ms/cts/signaturegenservice)로 녹음을 전송하여 오디오의 유효성을 검사하고 사용자 프로필을 생성해야 합니다.

> [!NOTE]
> 음성 서명에 대한 사용자 음성 샘플은 화자 식별에 필요합니다. 음성 샘플이 없는 화자는 "식별되지 않음"으로 인식됩니다. 식별되지 않은 화자는 속성이 활성화된 경우에도 여전히 구별할 수 `DifferentiateGuestSpeakers` 있습니다(아래 예제 참조). 그러면 전사 출력은 미리 등록된 특정 화자 이름으로 인식하는 대신 화자를 "Guest_0", "Guest_1" 등으로 표시합니다.
> ```csharp
> config.SetProperty("DifferentiateGuestSpeakers", "true");
> ```


## <a name="real-time-vs-asynchronous"></a>실시간 또는 비동기

대화 기록은 세 가지 대화 내용 기록 모드를 제공합니다.

### <a name="real-time"></a>실시간

오디오 데이터가 라이브로 처리되어 화자 식별자 + 음성 텍스트를 반환합니다. 대화 내용 기록 솔루션 요구 사항이 대화 참가자에게 진행 중인 대화의 실시간 음성 텍스트 보기를 제공하는 것인 경우 이 모드를 선택합니다. 예를 들어 청각 장애인과 난청이 있는 참가자가 회의에 더 쉽게 참가할 수 있도록 애플리케이션을 빌드하는 것이 실시간 대화 내용 기록의 이상적인 사용 사례입니다.

### <a name="asynchronous"></a>비동기

오디오 데이터가 일괄 처리되어 화자 식별자와 음성 텍스트를 반환합니다. 대화 내용 기록 솔루션 요구 사항이 실시간 음성 텍스트 보기 없이 더 높은 정확도를 제공하는 것인 경우 이 모드를 선택합니다. 예를 들어 회의 참가자가 놓친 회의 내용을 쉽게 파악할 수 있도록 애플리케이션을 빌드하려면 비동기 대화 내용 기록 모드를 사용하여 정확도가 높은 대화 내용 기록을 얻을 수 있습니다.

### <a name="real-time-plus-asynchronous"></a>실시간 + 비동기

오디오 데이터가 라이브로 처리되어 화자 식별자 + 음성 텍스트를 반환하며, 추가로 비동기 처리를 통해 정확도가 높은 음성 텍스트를 가져오는 요청이 생성됩니다. 애플리케이션에 실시간 대화 내용 기록이 필요하지만 대화 또는 회의 종료 후 사용할 더 높은 정확도의 음성 텍스트 또한 필요한 경우 이 모드를 선택합니다.

## <a name="language-support"></a>언어 지원

현재 대화 기록은 다음 지역에서 [모든 음성 텍스트 변환 언어](language-support.md#speech-to-text)를 지원합니다.  `centralus`, `eastasia`, `eastus`, `westeurope`. 추가 로캘 지원이 필요한 경우 [대화 기록 기능 크루](mailto:CTSFeatureCrew@microsoft.com)에 문의하세요.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [실시간 대화 기록](how-to-use-conversation-transcription.md)
