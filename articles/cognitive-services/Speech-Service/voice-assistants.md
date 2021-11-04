---
title: 음성 도우미 - Speech Service
titleSuffix: Azure Cognitive Services
description: Speech SDK(소프트웨어 개발 키트)를 사용하는 음성 도우미의 기능, 성능 및 제한 사항에 대한 개요입니다.
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: travisw
ms.openlocfilehash: fff03859cb41226e068380dd485d6b5d372aa6b9
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131510064"
---
# <a name="what-is-a-voice-assistant"></a>음성 도우미란?

Speech Service를 사용하는 음성 도우미를 사용하면 개발자가 자신의 애플리케이션과 환경에 맞게 자연스럽고 인간적인 대화형 인터페이스를 만들 수 있습니다.

음성 도우미 서비스는 (1) 봇에 음성 기능을 추가하기 위한 [Direct Line Speech](direct-line-speech.md)(Azure Bot Service를 통해) 또는 (2) 음성 명령 시나리오를 위한 사용자 지정 명령을 사용하여 디바이스와 도우미 구현 간에 빠르고 안정적인 상호 작용을 제공합니다.

## <a name="choosing-an-assistant-solution"></a>도우미 솔루션 선택

음성 도우미를 만드는 첫 번째 단계는 수행할 작업을 결정하는 것입니다. Speech Service는 도우미 상호 작용을 만들기 위한 여러 보완 솔루션을 제공합니다. [Direct Line Speech](direct-line-speech.md) 채널과 함께 Azure Bot Service를 사용하여 구축된 유연하고 다양한 봇에 음성 입력 및 음성 출력 기능을 추가 할 수 있습니다. 또는 간단한 음성 명령 시나리오를 위해 [사용자 지정 명령](custom-commands.md) 앱을 작성하는 단순성을 활용할 수 있습니다.

| 원하는 경우... | 고려할 사항... | 예... |
|-------------------|------------------|----------------|
|강력한 기술 통합 및 완전한 배포 제어를 통한 개방형 대화 | [Direct Line Speech](direct-line-speech.md) 채널이 있는 Azure Bot Service 봇 | <ul><li>"I need to go to Seattle"</li><li>"What kind of pizza can I order?"</li></ul>
|음성 명령 또는 간편한 작성 및 호스팅을 통한 간단한 작업 기반 대화 | [사용자 지정 명령](custom-commands.md) | <ul><li>"Turn on the overhead light"</li><li>"Make it 5 degrees warmer"</li><li>기타 샘플은 [여기에서 사용 가능](https://speech.microsoft.com/customcommands)</li></ul>

도우미가 무엇을 처리할 것인지 아직 확실하지 않은 경우 [Direct Line Speech](direct-line-speech.md)를 최선의 기본 선택으로 권장합니다. [가상 도우미 솔루션 및 엔터프라이즈 템플릿](/azure/bot-service/bot-builder-enterprise-template-overview) 및 [QnA Maker 서비스](../qnamaker/overview/overview.md)와 같은 다양한 도구 및 작성 도구와의 통합을 제공하여 공통 패턴을 구축하고 기존 지식 소스를 사용합니다.

[사용자 지정 명령](custom-commands.md)을 사용하면 음성 기반 상호 작용 환경에 최적화된 풍부한 음성 명령 앱을 쉽게 빌드할 수 있습니다. 통합된 작성 환경, 자동 호스팅 모델 및 상대적으로 낮은 복잡성을 제공하여 음성 명령 시나리오에 가장 적합한 솔루션을 구축하는 데 집중할 수 있습니다.

   ![도우미 솔루션 비교](media/voice-assistants/assistant-solution-comparison.png "도우미 솔루션 비교")


## <a name="reference-architecture-for-building-a-voice-assistant-using-the-speech-sdk"></a>Speech SDK를 사용하여 음성 도우미를 빌드하기 위한 참조 아키텍처

   ![음성 도우미 오케스트레이션 서비스 흐름의 개념 다이어그램](media/voice-assistants/overview.png "음성 도우미 흐름")

## <a name="core-features"></a>핵심 기능

도우미 상호 작용을 만들기 위해 [Direct Line Speech](direct-line-speech.md) 또는 [사용자 지정 명령](custom-commands.md)을 선택하여 다양한 사용자 지정 기능을 통해 도우미를 브랜드, 제품 및 개성에 맞게 사용자 지정할 수 있습니다.

| 범주 | 기능 |
|----------|----------|
|[사용자 지정 키워드](./custom-keyword-basics.md) | 사용자는 "Hey Contoso"와 같은 사용자 지정 키워드를 사용하여 도우미와 대화를 시작할 수 있습니다. 앱은 Speech SDK의 사용자 지정 키워드 엔진을 사용하여 이를 수행하며, [여기에서 생성할 수 있는](./custom-keyword-basics.md) 사용자 지정 키워드로 구성할 수 있습니다. 음성 도우미는 서비스 측 키워드 확인을 사용하여 키워드 활성화의 정확성을 향상시킬 수 있습니다(디바이스 단독이 아닌 경우).
|[음성 텍스트 변환](speech-to-text.md) | 음성 도우미는 음성 서비스의 [음성 텍스트 변환](speech-to-text.md)을 사용하여 실시간 오디오를 인식된 텍스트로 변환합니다. 이 텍스트는 전사된 대로 도우미 구현과 클라이언트 애플리케이션 모두에서 사용할 수 있습니다.
|[텍스트 음성 변환](text-to-speech.md) | 도우미의 텍스트 응답은 Speech Service의 [텍스트 음성 변환](text-to-speech.md)을 사용하여 합성됩니다. 이 합성은 클라이언트 애플리케이션에서 오디오 스트림으로 사용할 수 있습니다. Microsoft는 브랜드에 음성을 제공하는 고유한 사용자 지정 고품질 인공신경망 TTS 음성을 구축할 수 있는 기능을 제공합니다. 자세한 내용을 알아보려면 [문의](mailto:mstts@microsoft.com)하세요.

## <a name="getting-started-with-voice-assistants"></a>음성 도우미 시작

10분 이내에 코드를 실행할 수 있도록 설계된 빠른 시작을 사용할 수 있습니다. 이 표에는 언어별로 구성된 음성 도우미 빠른 시작 목록이 포함되어 있습니다.

* [빠른 시작: Direct Line Speech를 사용하여 사용자 지정 음성 도우미 만들기](quickstarts/voice-assistants.md)
* [빠른 시작: 사용자 지정 명령을 사용하여 음성 명령 앱 빌드](quickstart-custom-commands-application.md)

## <a name="sample-code-and-tutorials"></a>샘플 코드 및 자습서

음성 도우미를 만드는 샘플 코드는 GitHub에서 사용할 수 있습니다. 이러한 샘플을 사용하면 클라이언트 애플리케이션에서 여러 인기 프로그래밍 언어로 도우미에 연결할 수 있습니다.

* [GitHub의 음성 도우미 샘플](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant)
* [자습서: C# Speech SDK와 Azure Bot Service를 사용하여 빌드된 도우미를 음성을 지원하도록 설정](tutorial-voice-enable-your-bot-speech-sdk.md)
* [자습서: 간단한 음성 명령으로 사용자 지정 명령 애플리케이션 만들기](./how-to-develop-custom-commands-application.md)

## <a name="customization"></a>사용자 지정

Azure Speech Services를 사용하여 빌드된 음성 도우미는 전체 범위의 사용자 지정 옵션을 사용할 수 있습니다.

* [Custom Speech](./custom-speech-overview.md)
* [Custom Voice](how-to-custom-voice.md)
* [사용자 지정 키워드](keyword-recognition-overview.md)

> [!NOTE]
> 사용자 지정 옵션은 언어/로캘에 따라 다릅니다([지원되는 언어](language-support.md) 참조).

## <a name="next-steps"></a>다음 단계

* [평가판 Speech Service 구독 키 받기](overview.md#try-the-speech-service-for-free)
* [사용자 지정 명령에 대한 자세한 정보](custom-commands.md)
* [Direct Line Speech에 대한 자세한 정보](direct-line-speech.md)
* [Speech SDK 가져오기](speech-sdk.md)
