---
title: 사용자 지정 명령 - Speech Service
titleSuffix: Azure Cognitive Services
description: 음성 애플리케이션을 만들기 위한 솔루션인 사용자 지정 명령의 기능 및 제한 사항에 대한 개요입니다.
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: travisw
ms.openlocfilehash: db0f78f2f3aef3295837d921d7d70866ed8b8769
ms.sourcegitcommit: d01c2b2719e363178720003b67b968ac2a640204
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2021
ms.locfileid: "122568311"
---
# <a name="what-is-custom-commands"></a>사용자 지정 명령이란?

[음성 도우미](voice-assistants.md)와 같은 애플리케이션은 사용자의 말을 듣고 대응 조치를 수행하며, 되묻는 경우가 많습니다. [음성 텍스트 변환](speech-to-text.md)을 사용하여 사용자의 음성을 전사한 다음, 텍스트에 대한 자연어 이해에 대한 작업을 수행합니다. 이 작업에는 [텍스트 음성 변환](text-to-speech.md)으로 생성된 도우미의 음성 출력이 포함되는 경우가 많습니다. 디바이스는 Speech SDK의 `DialogServiceConnector` 개체를 사용하여 도우미에 연결합니다.

**사용자 지정 명령** 을 사용하면 음성 기반 상호 작용 환경에 최적화된 풍부한 음성 명령 앱을 쉽게 빌드할 수 있습니다. 통합된 작성 환경, 자동 호스팅 모델 및 상대적으로 낮은 복잡성을 제공하여 음성 명령 시나리오에 가장 적합한 솔루션을 구축하는 데 집중할 수 있습니다.

사용자 지정 명령은 작업 완료 또는 명령 및 제어 시나리오에 가장 적합하며, 특히 IoT(사물 인터넷) 디바이스, 주변/헤드리스 디바이스와 잘 일치합니다. 예로는 게스트를 위한 음성 제어 환경, 매장 내 재고 관리 또는 자동차 내 기능이 필요한 병원, 소매, 자동차 산업용 솔루션이 포함됩니다.

복잡한 대화형 앱을 빌드하는 데 관심이 있는 경우 [Virtual Assistant 솔루션](/azure/bot-service/bot-builder-enterprise-template-overview)을 통해 Bot Framework를 사용해 보는 것이 좋습니다. Direct Line Speech를 사용하여 모든 봇 프레임워크 봇에 음성을 추가할 수 있습니다.

사용자 지정 명령에 적합한 후보에는 잘 정의된 변수 세트가 있는 고정된 어휘가 있습니다. 예를 들어 자동 온도 조절기 제어와 같은 홈 자동화 작업이 적합합니다.

   ![작업 완료 시나리오의 예](media/voice-assistants/task-completion-examples.png "작업 완료 예")

## <a name="getting-started-with-custom-commands"></a>사용자 지정 명령 시작

사용자 지정 명령의 목표는 인지 부하를 줄여 다양한 기술을 배우고 음성 명령 앱을 구축하는 데 집중하는 것입니다. 사용자 지정 명령을 사용하여 <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices" target="_blank">Azure Speech 리소스를 만드</a>는 첫 번째 단계입니다. Speech Studio에서 사용자 지정 명령 앱을 작성하여 게시할 수 있습니다. 그 후에는 디바이스 애플리케이션이 Speech SDK를 사용하여 통신할 수 있습니다.

#### <a name="authoring-flow-for-custom-commands"></a>사용자 지정 명령의 작성 흐름
   ![사용자 지정 명령의 작성 흐름](media/voice-assistants/custom-commands-flow.png "사용자 지정 명령 작성 흐름")

빠른 시작에 따라 첫 번째 사용자 지정 명령 앱에서 코드를 10분 이내에 실행할 수 있습니다.

* [사용자 지정 명령을 사용하여 음성 도우미 만들기](quickstart-custom-commands-application.md)

빠른 시작을 완료한 후에는 사용자 지정 명령 애플리케이션을 디자인, 개발, 디버깅, 배포 및 통합하는 자세한 단계에 대한 방법 가이드를 살펴보세요.

## <a name="building-voice-assistants-with-custom-commands"></a>사용자 지정 명령을 사용하여 음성 도우미 빌드
> [!VIDEO https://www.youtube.com/embed/1zr0umHGFyc]

## <a name="next-steps"></a>다음 단계

* [평가판 Speech Service 구독 키 받기](overview.md#try-the-speech-service-for-free)
* [샘플에 대한 GitHub의 음성 도우미 리포지토리 보기](https://aka.ms/speech/cc-samples)
* [Speech Studio로 이동하여 사용자 지정 명령 사용해 보기](https://speech.microsoft.com/customcommands)
* [Speech SDK 가져오기](speech-sdk.md)