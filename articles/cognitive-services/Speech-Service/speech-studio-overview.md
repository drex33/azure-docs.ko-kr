---
title: Speech Studio 개요 - Speech Service
titleSuffix: Azure Cognitive Services
description: Speech Studio는 애플리케이션에서 Azure Speech Service의 기능을 빌드하고 통합하기 위한 UI 기반 도구 집합입니다.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/07/2021
ms.author: lajanuar
ms.openlocfilehash: 6042a129308b7be478b2bd1a45379ad56f62aca6
ms.sourcegitcommit: e7d500f8cef40ab3409736acd0893cad02e24fc0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122567544"
---
# <a name="what-is-speech-studio"></a>Speech Studio란?

[Speech Studio](https://speech.microsoft.com)는 애플리케이션에서 Azure Speech Service의 기능을 빌드하고 통합하기 위한 UI 기반 도구 집합입니다. 코드 없는 방법을 사용하여 Speech Studio에서 프로젝트를 만든 다음, [Speech SDK](speech-sdk.md), [Speech CLI](spx-overview.md) 또는 다양한 REST API를 사용하여 애플리케이션에서 만든 자산을 참조합니다.

## <a name="set-up-your-azure-account"></a>Azure 계정 설정

[Speech Studio](https://speech.microsoft.com)를 사용하려면 먼저 Azure 계정이 있고 Speech Service 리소스를 추가해야 합니다. 계정 및 리소스가 없는 경우 [Speech Service 평가판을 사용해 보세요](overview.md#try-the-speech-service-for-free).

Azure 계정 및 Speech Service 리소스를 만든 후:

1. Azure 계정으로 [Speech Studio](https://speech.microsoft.com)에 로그인합니다.
1. 시작하는 데 필요한 음성 서비스 리소스를 선택합니다. (상단 메뉴의 "설정"에서 언제든지 리소스를 변경할 수 있습니다.)

## <a name="speech-studio-features"></a>Speech Studio 기능

다음 Speech Service 기능은 Speech Studio에서 프로젝트 형식으로 사용할 수 있습니다.

* **실시간 음성 텍스트 변환**: 코드를 사용하지 않고 오디오 파일을 끌어서 놓아 음성 텍스트 변환으로 빠르게 테스트합니다. 이 도구는 오디오 샘플에서 음성 텍스트 변환 작동 방식을 확인하기 위한 데모 도구이지만 사용 가능한 전체 기능을 살펴보려면 음성 텍스트 변환에 대한 [개요](speech-to-text.md)를 참조하세요.
* **Custom Speech**: Custom Speech를 사용하면 특정 어휘 집합 및 말하기 스타일에 맞게 조정된 음성 인식 모델을 만들 수 있습니다. 기본 음성 인식 모델을 사용하는 것과 달리 Custom Speech 모델은 공개적으로 액세스할 수 없기 때문에 고유한 경쟁 우위의 일부가 됩니다. 샘플 오디오 업로드를 시작하여 Custom Speech 모델을 만들려면 [빠른 시작](how-to-custom-speech-test-and-train.md)을 참조하세요.
* **발음 평가**: 발음 평가는 음성 발음을 평가하고 음성 오디오의 정확도와 능숙도에 대한 피드백을 발표자에게 제공합니다. Speech Studio는 코드 없이 이 기능을 빠르게 테스트하기 위한 샌드박스를 제공하지만 애플리케이션에서 Speech SDK와 함께 기능을 사용하기 위한 [방법](how-to-pronunciation-assessment.md) 문서를 참조하세요.
* **음성 갤러리**: 자연스럽게 말하는 앱 및 서비스를 빌드합니다. 70개 이상의 언어 및 변형에서 170개 넘는 음성 중에서 선택합니다. 표현력이 높고 인간과 같은 신경망 음성을 사용하여 시나리오를 실현합니다.
* **Custom Voice**: Custom Voice를 사용하면 텍스트 음성 변환에 대한 한 가지 종류의 사용자 지정 음성을 만들 수 있습니다. 오디오 파일을 제공하고 Speech Studio에 일치하는 대화 내용 기록을 만든 다음, 애플리케이션에서 사용자 지정 음성을 사용합니다. 엔드포인트를 통해 사용자 지정 음성을 만들고 사용하는 방법에 대한 [방법](how-to-custom-voice-create-voice.md) 문서를 참조하세요. 
* **오디오 콘텐츠 만들기**: [오디오 콘텐츠 만들기](how-to-audio-content-creation.md)는 다양한 시나리오(예: 오디오 서적, 뉴스 브로드캐스트, 비디오 설명 및 채팅 봇)에 대해 매우 자연스러운 오디오 콘텐츠를 빌드할 수 있는 사용하기 쉬운 도구입니다. Speech Studio를 사용하면 애플리케이션에서 사용할 생성된 오디오 파일을 내보낼 수 있습니다.
* **사용자 지정 키워드**: 사용자 지정 키워드는 제품을 음성으로 활성화할 수 있는 단어 또는 짧은 구입니다. Speech Studio에서 사용자 지정 키워드를 만든 다음, 애플리케이션에서 [Speech SDK와 함께 사용할](custom-keyword-basics.md) 이진 파일을 생성합니다.
* **사용자 지정 명령**: 사용자 지정 명령을 사용하면 음성 기반 상호 작용 환경에 최적화된 풍부한 음성 명령 앱을 쉽게 빌드할 수 있습니다. Speech Studio에서 코드 없는 작성 환경, 자동 호스팅 모델 및 상대적으로 낮은 복잡성을 제공하여 음성 명령 시나리오에 가장 적합한 솔루션을 구축하는 데 집중할 수 있습니다. 사용자 지정 명령 애플리케이션 빌드에 대한 [방법](how-to-develop-custom-commands-application.md) 가이드를 참조하고, [사용자 지정 명령 애플리케이션을 Speech SDK와 통합](how-to-custom-commands-setup-speech-sdk.md)하기 위한 가이드도 참조하세요.

## <a name="next-steps"></a>다음 단계

[Speech Studio를 탐색](https://speech.microsoft.com)하고 프로젝트를 만듭니다.




