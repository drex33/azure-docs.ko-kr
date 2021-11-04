---
title: Direct Line Speech - Speech Service
titleSuffix: Azure Cognitive Services
description: Speech SDK(소프트웨어 개발 키트)와 함께 Direct Line Speech를 사용하는 음성 도우미의 기능, 성능 및 제한 사항에 대한 개요입니다.
services: cognitive-services
author: eric-urban
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: eur
ms.openlocfilehash: e696e32a889bb1e15877ff1504c9a83092fc9f25
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131508706"
---
# <a name="what-is-direct-line-speech"></a>Direct Line Speech란?

**Direct Line Speech** 는 유연하고 확장 가능한 음성 도우미를 만들기 위한 강력한 엔드투엔드 솔루션입니다. Bot Framework 및 해당 Direct Line Speech 채널을 통해 구동되며, 봇과의 음성 입력, 출력 상호 작용에 최적화되어 있습니다.

[음성 도우미](voice-assistants.md)는 사용자의 말을 듣고 대응 조치를 수행하며, 되묻는 경우가 많습니다. [음성 텍스트 변환](speech-to-text.md)을 사용하여 사용자의 음성을 전사한 다음, 텍스트에 대한 자연어 이해에 대한 작업을 수행합니다. 이 작업에는 [텍스트 음성 변환](text-to-speech.md)으로 생성된 도우미의 음성 출력이 포함되는 경우가 많습니다.

Direct Line Speech는 음성 도우미에 대해 가장 높은 수준의 사용자 지정 및 정교함을 제공합니다. 작업 완료 또는 명령 및 제어 사용이 있는 두 가지의 무한, 자연적 또는 하이브리드인 대화형 시나리오를 위해 설계되었습니다. 이러한 높은 수준의 유연성은 더 큰 복잡성을 가져오며, 자연어 입력을 사용하여 잘 정의된 작업으로 범위가 지정되는 시나리오는 간소화된 솔루션 환경을 위한 [사용자 지정 명령](custom-commands.md)을 고려할 수 있습니다.

## <a name="getting-started-with-direct-line-speech"></a>Direct Line Speech 시작

Direct Line Speech를 사용하여 음성 도우미를 만드는 첫 번째 단계는 [음성 구독 키를 가져오고](overview.md#try-the-speech-service-for-free), 해당 구독과 연결된 새 봇을 만들고, 봇을 Direct Line Speech 채널에 연결하는 것입니다.

   ![Direct Line Speech 오케스트레이션 서비스 흐름의 개념 다이어그램](media/voice-assistants/overview-directlinespeech.png "음성 채널 흐름")

Direct Line Speech를 사용하여 간단한 음성 도우미를 만드는 전체 단계별 가이드는 [Speech SDK 및 Direct Line Speech 채널을 사용하여 음성을 지원하도록 봇 설정에 대한 자습서](tutorial-voice-enable-your-bot-speech-sdk.md)를 참조하세요.

또한 코드를 실행하고 API를 빠르게 학습할 수 있도록 설계된 빠른 시작도 제공합니다. 이 표에는 언어 및 플랫폼별로 구성된 음성 도우미 빠른 시작 목록이 포함되어 있습니다.

| 빠른 시작 | 플랫폼 | API 참조 |
|------------|----------|---------------|
| C#, UWP | Windows | [찾아보기](/dotnet/api/microsoft.cognitiveservices.speech) |
| Java | Windows, macOS, Linux | [찾아보기](/java/api/com.microsoft.cognitiveservices.speech) |
| Java | Android | [찾아보기](/java/api/com.microsoft.cognitiveservices.speech) |

## <a name="sample-code"></a>샘플 코드

음성 도우미를 만드는 샘플 코드는 GitHub에서 사용할 수 있습니다. 이러한 샘플을 사용하면 클라이언트 애플리케이션에서 여러 인기 프로그래밍 언어로 도우미에 연결할 수 있습니다.

* [음성 도우미 샘플(SDK)](https://aka.ms/csspeech/samples/#voice-assistants-quickstarts)
* [자습서: 음성 SDK, C#으로 음성을 지원하도록 도우미 설정](tutorial-voice-enable-your-bot-speech-sdk.md)

## <a name="customization"></a>사용자 지정

Speech Service를 사용하여 빌드된 음성 도우미는 [음성 텍스트 변환](speech-to-text.md), [텍스트 음성 변환](text-to-speech.md) 및 [사용자 지정 키워드 선택](./custom-keyword-basics.md)에 사용할 수 있는 모든 사용자 지정 옵션을 사용할 수 있습니다.

> [!NOTE]
> 사용자 지정 옵션은 언어/로캘에 따라 다릅니다([지원되는 언어](./language-support.md) 참조).

음성 도우미에 대한 Direct Line Speech 및 관련 기능은 [Virtual Assistant 솔루션 및 엔터프라이즈 템플릿](/azure/bot-service/bot-builder-enterprise-template-overview)에 적합합니다. Direct Line Speech는 호환 봇에서 작동할 수 있지만 이러한 리소스는 고품질의 대화형 환경뿐만 아니라 빠르게 시작할 수 있는 일반적인 지원 기술 및 모델에 재사용 가능한 기준을 제공합니다.

## <a name="reference-docs"></a>참조 문서

* [Speech SDK](./speech-sdk.md)
* [Azure Bot Service](/azure/bot-service/)

## <a name="next-steps"></a>다음 단계

* [평가판 Speech Service 구독 키 받기](overview.md#try-the-speech-service-for-free)
* [Speech SDK 가져오기](speech-sdk.md)
* [기본 봇 만들기 및 배포](/azure/bot-service/bot-builder-tutorial-basic-deploy)
* [Virtual Assistant 솔루션 및 엔터프라이즈 템플릿 가져오기](https://github.com/Microsoft/AI)
