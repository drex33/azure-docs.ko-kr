---
title: Speech Service란?
titleSuffix: Azure Cognitive Services
description: Speech Service는 음성 텍스트 변환, 텍스트 음성 변환 및 음성 번역을 단일 Azure 구독에 통합한 것입니다. Speech SDK, Speech Devices SDK 또는 REST API를 사용하여 애플리케이션, 도구 및 디바이스에 음성을 추가합니다.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: overview
ms.date: 11/23/2020
ms.author: pafarley
ms.openlocfilehash: 8c2cf97e6aa7b4bedfa2b3b834b563116fb3434c
ms.sourcegitcommit: f2d0e1e91a6c345858d3c21b387b15e3b1fa8b4c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/07/2021
ms.locfileid: "123539482"
---
# <a name="what-is-the-speech-service"></a>Speech Service란?

Speech Service는 음성 텍스트 변환, 텍스트 음성 변환 및 음성 번역을 단일 Azure 구독에 통합한 것입니다. [Speech CLI](spx-overview.md), [Speech SDK](./speech-sdk.md), [Speech Devices SDK](./speech-devices-sdk-quickstart.md?pivots=platform-android), [Speech Studio](speech-studio-overview.md) 또는 [REST API](#reference-docs)를 통해 애플리케이션, 도구 및 디바이스를 음성으로 쉽게 사용할 수 있습니다.

> [!IMPORTANT]
> Speech Service는 Bing Speech API 및 Translator Speech를 대체했습니다. 마이그레이션 지침에 대해서는 _마이그레이션_ 섹션을 참조하세요.

Speech Service 기능 중 일부는 다음과 같습니다. 이 표의 링크를 사용하여 각 기능의 일반적인 사용 사례에 대해 자세히 알아보거나 API 참조를 찾아볼 수 있습니다.

| 서비스 | 기능 | Description | SDK) | REST (영문) |
|---------|---------|-------------|-----|------|
| [음성 텍스트 변환](speech-to-text.md) | 실시간 음성 텍스트 변환 | 음성 텍스트 변환은 오디오 스트림 또는 로컬 파일을 애플리케이션, 도구 또는 디바이스가 사용하거나 표시할 수 있는 텍스트로 실시간으로 변환합니다. [LUIS(Language Understanding)](../luis/index.yml)에서 음성 텍스트 변환을 사용하여 기록된 음성에서 사용자 의도를 파생시키고 음성 명령 작업에 따라 행동합니다. | [예](./speech-sdk.md) | [예](#reference-docs) |
| | [Batch 음성 텍스트 변환](batch-transcription.md) | Batch 음성 텍스트 변환에서는 Azure Blob Storage에 저장된 대용량 음성 오디오 데이터의 비동기 음성 텍스트 변환 기록을 사용할 수 있습니다. 음성 오디오를 텍스트로 변환하는 것 외에도 Batch 음성 텍스트 변환에서는 다이어리화 및 감정 분석도 가능합니다. | 예 | [예](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0) |
| | [다중 디바이스 대화](multi-device-conversation.md) | 대화의 다중 디바이스 또는 클라이언트를 연결하여 음성 또는 텍스트 기반 메시지를 전송하고 전사 및 번역을 간편하게 지원합니다.| 예 | 예 |
| | [대화 전사](./conversation-transcription.md) | 실시간 음성 인식, 화자 식별 및 다이어리화를 가능하게 해줍니다. 화자를 구별하는 기능을 사용하여 직접 만남을 전사하는 데 이상적입니다. | 예 | 예 |
| | [Custom Speech 모델 만들기](#customize-your-speech-experience) | 고유한 환경에서 인식 및 기록을 위해 음성 텍스트 변환을 사용하는 경우 사용자 지정 음향, 언어 및 발음 모델을 만들고 학습하여 주변 소음 또는 산업용 어휘를 처리할 수 있습니다. | 예 | [예](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0) |
| | [발음 평가](./how-to-pronunciation-assessment.md) | 발음 평가는 음성 발음을 평가하고 음성 오디오의 정확도와 능숙도에 대한 피드백을 발표자에게 제공합니다. 발음 평가를 통해 언어 학습자는 자신 있게 말하고 발표할 수 있도록 연습하고, 즉각적인 피드백을 받고, 발음을 개선할 수 있습니다. | [예](./how-to-pronunciation-assessment.md) | [예](./rest-speech-to-text.md#pronunciation-assessment-parameters) |
| [텍스트 음성 변환](text-to-speech.md) | 텍스트 음성 변환 | 텍스트 음성 변환은 [SSML(Speech Synthesis Markup Language)](speech-synthesis-markup.md)을 사용하여 입력 텍스트를 인간과 유사한 합성 음성으로 변환합니다. 심층 신경망에 의해 구동되는 인간과 유사한 음성인 인공신경망을 사용합니다. [언어 지원](language-support.md)을 참조하세요. | [예](./speech-sdk.md) | [예](#reference-docs) |
| | [사용자 지정 음성 만들기](#customize-your-speech-experience) | 브랜드 또는 제품에 고유한 사용자 지정 음성 글꼴을 만듭니다. | 예 | [예](#reference-docs) |
| [Speech Translation](speech-translation.md) | 음성 번역 | 음성 번역을 사용하면 음성에 대한 실시간 엔드투엔드 다중 언어 번역을 애플리케이션, 도구 및 디바이스에 추가할 수 있습니다. 이 서비스는 음성을 음성으로 변환 및 음성을 텍스트로 변환을 위해 사용합니다. | [예](./speech-sdk.md) | 예 |
| [음성 도우미](voice-assistants.md) | 음성 도우미 | Speech Service를 사용하는 음성 도우미를 사용하면 개발자가 자신의 애플리케이션과 환경에 맞게 자연스럽고 인간적인 대화형 인터페이스를 만들 수 있습니다. 음성 도우미는 작업 완료를 위해 Bot Framework의 Direct Line Speech 채널 또는 통합 사용자 지정 명령 서비스를 사용하는 도우미 구현과 디바이스 사이에 빠르고 안정적인 상호 작용을 제공합니다. | [예](voice-assistants.md) | 예 |
| [화자 인식](speaker-recognition-overview.md) | 화자 검증 & 식별 | 화자 인식 서비스는 고유한 음성 특성에 따라 화자를 확인 및 식별하는 알고리즘을 제공합니다. 화자 인식은 "누가 말하고 있나요?"라는 질문에 대답하는 데 사용됩니다. | 예 | [예](/rest/api/speakerrecognition/) |


[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

## <a name="try-the-speech-service-for-free"></a>Speech Service 체험해 보기

다음 단계에서는 Microsoft 계정과 Azure 계정이 모두 필요합니다. Microsoft 계정이 없으면 [Microsoft 계정 포털](https://account.microsoft.com/account)에서 무료로 한 계정에 가입할 수 있습니다. **Microsoft에 로그인** 을 선택한 다음, 로그인하라는 메시지가 표시되면 **Microsoft 계정 만들기** 를 선택합니다. 단계에 따라 새 Microsoft 계정을 만들고 확인합니다.

Microsoft 계정이 있으면 [Azure 가입 페이지](https://azure.microsoft.com/free/ai/)로 이동하고, **평가판 시작** 을 선택한 다음, Microsoft 계정을 사용하여 새 Azure 계정을 만듭니다. 다음은 [Azure 체험 계정에 등록하는 방법](https://www.youtube.com/watch?v=GWT2R1C_uUU)에 대한 비디오입니다.

> [!NOTE]
> Azure 체험 계정에 가입하면 유료 Speech Service 구독에 적용할 수 있는 $200의 서비스 크레딧이 제공되며, 최대 30일 동안 유효합니다. 크레딧이 부족하거나 30일이 만료된 경우 Azure 서비스가 비활성화됩니다. Azure 서비스를 계속 사용하려면 계정을 업그레이드해야 합니다. 자세한 내용은 [Azure 체험 계정을 업그레이드하는 방법](../../cost-management-billing/manage/upgrade-azure-subscription.md)을 참조하세요. 
>
> Speech Service에는 체험(f0) 및 구독(s0)의 두 가지 서비스 계층이 있으며, 제한 사항과 혜택이 서로 다릅니다. 적은 볼륨의 Speech Service 체험 계층을 사용하는 경우 평가판 또는 서비스 크레딧이 만료된 후에도 이 체험 구독을 유지할 수 있습니다. 자세한 내용은 [Cognitive Services 가격 - Speech Service](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)를 참조하세요.

### <a name="create-the-azure-resource"></a>Azure 리소스 만들기

Speech 서비스 리소스(체험 또는 유료 계층)를 Azure 계정에 추가하려면 다음을 수행합니다.

1. Microsoft 계정을 사용하여 [Azure Portal](https://portal.azure.com/)에 로그인합니다.

1. 포털의 왼쪽 위에서 **리소스 만들기** 를 선택합니다. **리소스 만들기** 가 표시되지 않으면 언제든지 화면의 왼쪽 위 모서리에서 접혀 있는 메뉴를 선택하여 찾을 수 있습니다.

1. **새로 만들기** 창의 검색 상자에서 "speech"를 입력하고 ENTER 키를 누릅니다.

1. 검색 결과에서 **Speech** 를 선택합니다.
   
   :::image type="content" source="media/index/speech-search.png" alt-text="Azure Portal에서 음성 리소스를 만듭니다":::.

1. **만들기** 를 선택하고 다음을 수행합니다.

   - 새 리소스에 대한 고유한 이름을 지정합니다. 이름은 동일한 서비스에 연결된 여러 구독을 구분하는 데 도움이 됩니다.
   - 새 리소스가 연결되는 Azure 구독을 선택하여 요금이 얼마나 청구되는지 확인합니다. 다음은 Azure Portal에서 [Azure 구독을 만드는 방법](../../cost-management-billing/manage/create-subscription.md#create-a-subscription-in-the-azure-portal)에 대한 소개입니다.
   - 리소스가 사용될 [지역](regions.md)을 선택합니다. Azure는 전 세계 여러 지역에서 일반적으로 사용 가능한 글로벌 클라우드 플랫폼입니다. 최상의 성능을 얻으려면 사용자에게 가장 가까운 지역이나 애플리케이션이 실행되는 위치를 선택합니다. Speech Service 가용성은 지역마다 다릅니다. 지원되는 지역에서 리소스를 만들어야 합니다. [Speech Service에 대한 지역 지원](./regions.md#speech-to-text-text-to-speech-and-translation)을 참조하세요.
   - F0(체험) 또는 S0(유료) 가격 책정 계층을 선택합니다. 각 계층의 가격 책정 및 사용 할당량에 대한 전체 정보를 보려면 **전체 가격 책정 세부 정보 보기** 를 선택하거나 [Speech Service 가격 책정](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)을 참조하세요. 리소스에 대한 제한 사항은 [Azure Cognitive Services 제한](../../azure-resource-manager/management/azure-subscription-service-limits.md#azure-cognitive-services-limits)을 참조하세요.
   - 이 Speech 구독에 대한 새 리소스 그룹을 만들거나 기존 리소스 그룹에 해당 구독을 할당합니다. 리소스 그룹은 다양한 Azure 구독의 구성을 유지하는 데 도움이 됩니다.
   - **만들기** 를 선택합니다. 그러면 배포 개요로 이동하여 배포 진행률 메시지가 표시됩니다.  
<!--
> [!NOTE]
> You can create an unlimited number of standard-tier subscriptions in one or multiple regions. However, you can create only one free-tier subscription. Model deployments on the free tier that remain unused for 7 days will be decommissioned automatically.
-->
새 Speech 리소스를 배포하는 데 몇 분 정도 걸립니다. 

### <a name="find-keys-and-locationregion"></a>키 및 위치/지역 찾기

완료된 배포의 키와 위치/지역을 찾으려면 다음 단계를 수행합니다.

1. Microsoft 계정을 사용하여 [Azure Portal](https://portal.azure.com/)에 로그인합니다.

2. **모든 리소스** 를 선택하고 Cognitive Services 리소스의 이름을 선택합니다.

3. 왼쪽 창의 **리소스 관리** 에서 **키 및 엔드포인트** 를 선택합니다.

각 구독에는 두 개의 키가 있으며, 애플리케이션에서 두 키 중 하나를 사용할 수 있습니다. 키를 코드 편집기 또는 다른 위치에 복사하여 붙여넣으려면 각 키 옆에 있는 복사 단추를 선택하고, 창을 전환하여 클립보드 내용을 원하는 위치에 붙여넣습니다.

또한 `LOCATION` 값을 복사합니다. 이 값은 SDK 호출에 대한 지역 ID(예: `westus`, `westeurope`)입니다.

> [!IMPORTANT]
> 이러한 구독 키는 Cognitive Service API에 액세스하는 데 사용됩니다. 키를 공유하지 마세요. 예를 들어 Azure Key Vault를 사용하여 안전하게 저장합니다. 또한 이러한 키는 정기적으로 다시 생성하는 것이 좋습니다. API 호출을 수행하는 데는 하나의 키만 필요합니다. 첫 번째 키를 다시 생성하는 경우 두 번째 키를 사용하여 서비스에 계속 액세스할 수 있습니다.

## <a name="complete-a-quickstart"></a>빠른 시작 완료

Microsoft는 빠른 시작을 가장 많이 사용되는 프로그래밍 언어로 제공하며, 각각 기본 디자인 패턴을 학습하고 코드를 10분 이내에 실행할 수 있도록 설계되었습니다. 각 기능에 대한 빠른 시작은 다음 목록을 참조하세요.

* [음성 텍스트 변환 빠른 시작](get-started-speech-to-text.md)
* [텍스트 음성 변환 빠른 시작](get-started-text-to-speech.md)
* [음성 번역 빠른 시작](./get-started-speech-translation.md)
* [의도 인식 빠른 시작](./get-started-intent-recognition.md)
* [화자 인식 빠른 시작](./get-started-speaker-recognition.md)

Speech Service를 시작한 후에는 다양한 시나리오를 해결하는 방법을 보여 주는 자습서를 수행해 보세요.

- [자습서: Speech SDK 및 LUIS, C#을 사용하여 음성에서 의도 인식](how-to-recognize-intents-from-speech-csharp.md)
- [자습서: 음성 SDK, C#으로 음성을 지원하도록 봇 설정](tutorial-voice-enable-your-bot-speech-sdk.md)
- [자습서: 텍스트 번역, 감정 분석 및 번역된 텍스트를 음성, REST로 합성하는 Flask 앱 빌드](../translator/tutorial-build-flask-app-translation-synthesis.md?bc=%2fazure%2fcognitive-services%2fspeech-service%2fbreadcrumb%2ftoc.json%252c%2fen-us%2fazure%2fbread%2ftoc.json&toc=%2fazure%2fcognitive-services%2fspeech-service%2ftoc.json%252c%2fen-us%2fazure%2fcognitive-services%2fspeech-service%2ftoc.json)

## <a name="get-sample-code"></a>샘플 코드 가져오기

샘플 코드는 Speech Service를 위해 GitHub에서 사용할 수 있습니다. 이러한 샘플은 파일 또는 스트림에서 오디오 읽기, 연속 및 1단계 인식 및 사용자 지정 모델 사용과 같은 일반적인 시나리오를 다룹니다. 다음 링크를 사용하여 SDK 및 REST 샘플을 확인하세요.

- [음성 텍스트 변환, 텍스트 음성 변환 및 음성 번역 샘플(SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
- [일괄 처리 기록 샘플(REST)](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/batch)
- [텍스트 음성 변환 샘플(REST)](https://github.com/Azure-Samples/Cognitive-Speech-TTS)
- [음성 도우미 샘플(SDK)](https://aka.ms/csspeech/samples)

## <a name="customize-your-speech-experience"></a>음성 환경 사용자 지정

Speech Service는 기본 제공 모델에서 효율적으로 작동하지만, 제품 또는 환경에 맞게 경험을 추가로 사용자 지정하고 튜닝할 수 있습니다. 사용자 지정 옵션은 음향 모델 조정부터 브랜드를 위한 고유한 음성 글꼴까지 다양합니다.

다른 제품은 의료 또는 보험과 같은 특정 목적에 맞게 튜닝된 음성 모델을 제공하지만, 모든 사용자가 동일하게 사용할 수 있습니다. Azure Speech의 사용자 지정은 다른 사용자 또는 고객이 사용할 수 없는 *고유한* 경쟁 우위의 일부가 됩니다. 즉, 모델은 사용 사례에만 맞게 전용 및 사용자 지정 방식으로 튜닝됩니다.

| Speech Service | 플랫폼 | Description |
| -------------- | -------- | ----------- |
| 음성 텍스트 변환 | [Custom Speech](./custom-speech-overview.md) | 사용자의 요구 사항과 사용 가능한 데이터에 맞게 음성 인식 모델을 사용자 지정합니다. 말하기 스타일, 어휘 및 배경 소음과 같은 음성 인식 장벽을 해결하세요. |
| 텍스트 음성 변환 | [Custom Voice](https://aka.ms/customvoice) | 사용 가능한 발언 데이터를 사용하여 텍스트 음성 변환 앱에 대한 다양한 종류의 음성을 만듭니다. 음성 매개 변수 세트를 조정하여 음성 출력을 세밀하게 조정할 수 있습니다. |

## <a name="deploy-on-premises-using-docker-containers"></a>Docker 컨테이너를 사용하여 온-프레미스 배포

[Speech Service 컨테이너를 사용](speech-container-howto.md)하여 온-프레미스에 API 기능을 배포합니다. 이러한 Docker 컨테이너는 규정 준수, 보안 또는 기타 운영상의 이유로 서비스를 데이터에 더 가깝게 가져올 수 있습니다. Speech Service는 다음과 같은 컨테이너를 제공합니다.

* 표준 음성 텍스트 변환
* 사용자 지정 음성 텍스트 변환
* 표준 텍스트 음성 변환
* 인공신경망 텍스트 음성 변환
* 사용자 지정 텍스트 음성 변환(미리 보기)
* 음성 언어 식별(미리 보기)

## <a name="reference-docs"></a>참조 문서

- [Speech SDK](./speech-sdk.md)
- [Speech Devices SDK](speech-devices-sdk.md)
- [REST API: 음성 텍스트 변환](rest-speech-to-text.md)
- [REST API: 텍스트 음성 변환](rest-text-to-speech.md)
- [REST API: 일괄 처리 기록 및 사용자 지정](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0)


## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [음성 텍스트 변환 시작](./get-started-speech-to-text.md)
> [텍스트 음성 변환 시작](get-started-text-to-speech.md)