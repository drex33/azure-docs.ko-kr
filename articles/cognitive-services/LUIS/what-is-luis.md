---
title: LUIS(Language Understanding) 개요
description: LUIS(Language Understanding) - 의미를 예측하고 정보를 추출하기 위해 대화형 자연어에 기계 학습을 사용하는 클라우드 기반 API 서비스입니다.
keywords: Azure, 인공 지능, ai, 자연어 처리, nlp, 자연어 이해, nlu, LUIS, 대화형 AI, ai 챗봇, nlp ai, azure luisl
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: overview
ms.date: 10/20/2021
ms.custom: cog-serv-seo-aug-2020, ignite-fall-2021
ms.openlocfilehash: 1595831e4da756e16df257ce93d28b03bb7c6eab
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131069257"
---
# <a name="what-is-language-understanding-luis"></a>LUIS(Language Understanding)란?

> [!NOTE]
> 최신 버전의 Language Understanding 기능을 이제 Azure Cognitive Service for Language의 일부로 사용할 수 있습니다. 자세한 내용은 [Azure Cognitive Service for Language 설명서](../language-service/index.yml)를 참조하세요. 언어 서비스 내의 Language Understanding 기능은 [대화형 Language Understanding](../language-service/conversational-language-understanding/overview.md), [사용자 지정 명명된 엔터티 인식](../language-service/custom-named-entity-recognition/overview.md) 및 [사용자 지정 분류](../language-service/custom-classification/overview.md)를 참조하세요.

LUIS(Language Understanding)는 사용자의 대화형 자연어 텍스트에 사용자 지정 기계 학습 인텔리전스를 적용하여 전체적인 의미를 예측하고 관련된 자세한 정보를 추출하는 클라우드 기반 대화형 AI 서비스입니다. LUIS는 [사용자 지정 포털](https://www.luis.ai), [API][endpoint-apis] 및 [SDK 클라이언트 라이브러리](client-libraries-rest-api.md)를 통해 액세스를 제공합니다.

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

처음 사용하는 경우 다음 단계에 따라 [LUIS 포털에 로그인](sign-in-luis-portal.md "LUIS 포털에 로그인")합니다. 시작하려면 LUIS의 [미리 빌드된 도메인](luis-get-started-create-app.md) 앱을 사용해 보면 됩니다.

이 설명서에는 다음과 같은 문서 유형이 포함되어 있습니다.  

* [**빠른 시작**](luis-get-started-create-app.md)은 서비스에 대한 요청을 수행하는 과정을 안내하는 시작 지침입니다.  
* [**방법 가이드**](luis-how-to-start-new-app.md)에는 보다 구체적이거나 사용자 지정된 방식으로 서비스를 사용하기 위한 지침이 포함되어 있습니다.  
* [**개념**](artificial-intelligence.md)은 서비스 기능에 대한 심층적인 설명을 제공합니다.  
* [**자습서**](tutorial-intents-only.md)는 보다 광범위한 비즈니스 솔루션에서 서비스를 구성 요소로 사용하는 방법을 보여주는 긴 가이드입니다.  

## <a name="what-does-luis-offer"></a>LUIS가 제공하는 이점 

* **단순성**: LUIS는 사내 AI 전문가나 이전의 기계 학습 지식이 없어도 사용할 수 있습니다. 마우스를 몇 번만 클릭하면 고유한 대화형 AI 애플리케이션을 빌드할 수 있습니다. [빠른 시작](luis-get-started-create-app.md) 중 하나를 진행하여 사용자 지정 애플리케이션을 빌드하거나 [미리 빌드된 도메인](luis-get-started-create-app.md) 앱 중 하나를 사용할 수 있습니다.
* **보안, 프라이버시 및 규정 준수**: Azure 인프라 기반의 LUIS는 엔터프라이즈급 보안, 프라이버시 및 규정 준수를 제공합니다. 데이터의 소유권은 그대로 유지됩니다. 언제든지 데이터를 삭제할 수 있습니다. 데이터는 스토리지에 있는 동안 암호화됩니다. 이 기능에 대한 자세한 내용은 [여기](https://azure.microsoft.com/support/legal/cognitive-services-compliance-and-privacy)를 참조하세요.
* **통합**: LUIS 앱을 [Microsoft Bot Framework](/composer/tutorial/tutorial-luis), [QnA Maker](../QnAMaker/choose-natural-language-processing-service.md), [Speech Service](../speech-service/get-started-intent-recognition.md) 등의 다른 Microsoft 서비스와 쉽게 통합할 수 있습니다.


## <a name="luis-scenarios"></a>LUIS 시나리오
* [엔터프라이즈급 대화형 봇 빌드](/azure/architecture/reference-architectures/ai/conversational-bot): 이 참조 아키텍처는 Azure Bot Framework를 사용하는 엔터프라이즈급 대화형 봇(챗봇)을 빌드하는 방법에 대해 설명합니다.
* [상거래 챗봇](/azure/architecture/solution-ideas/articles/commerce-chatbot): 개발자는 Azure Bot Service 및 Language Understanding 서비스를 함께 사용하여 은행, 여행 및 엔터테인먼트와 같은 다양한 시나리오에 대한 대화형 인터페이스를 만들 수 있습니다.
* [음성 도우미를 사용하여 IoT 디바이스 제어](/azure/architecture/solution-ideas/articles/iot-controlling-devices-with-voice-assistant): 연결된 TV 또는 냉장고에서 연결된 발전소의 디바이스에 이르기까지 인터넷에 액세스할 수 있는 모든 디바이스와의 원활한 대화형 인터페이스를 만듭니다.


## <a name="application-development-life-cycle"></a>애플리케이션 개발 수명 주기

![LUIS 앱 개발 수명 주기](./media/luis-overview/luis-dev-lifecycle.png "LUIS 애플리케이션 개발 수명 주기")

-   **계획**: 사용자가 애플리케이션을 사용할 수 있는 시나리오를 식별합니다. 작업 및 알아 두어야 할 관련 정보를 정의합니다.
-   **빌드**: 제작 리소스를 사용하여 앱을 개발합니다. 먼저 [의도](luis-concept-intent.md) 및 [엔터티](luis-concept-entity-types.md)를 정의합니다. 그런 다음, 각 의도의 학습 [발화](luis-concept-utterance.md)를 추가합니다. 
-   **테스트 및 개선**: 다른 발화로 모델 테스트를 시작하여 앱의 작동 방식을 파악하고, 향상된 기능이 필요한지 여부를 결정할 수 있습니다. 이러한 [모범 사례](luis-concept-best-practices.md)에 따라 애플리케이션을 개선할 수 있습니다. 
-   **게시**: 예측을 위해 앱을 배포하고 예측 리소스를 사용하여 엔드포인트를 쿼리합니다. [여기](luis-how-to-azure-subscription.md)에서 제작 및 예측 리소스에 대해 자세히 알아보세요. 
-   **연결**: [Microsoft Bot Framework](/composer/tutorial/tutorial-luis), [QnA Maker](../QnAMaker/choose-natural-language-processing-service.md) 및 [Speech Service](../speech-service/get-started-intent-recognition.md) 등의 다른 서비스에 연결합니다. 
-   **구체화**: [엔드포인트 발화를 검토](luis-concept-review-endpoint-utterances.md)하여 실제 예제로 애플리케이션을 개선합니다.

애플리케이션 계획 및 빌드에 대한 자세한 내용은 [여기](luis-how-plan-your-app.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

* 서비스 및 설명서의 [새로운 기능](whats-new.md "새로운 기능")
* [LUIS 앱 빌드](tutorial-intents-only.md)
* [API 참조][endpoint-apis]
* [모범 사례](luis-concept-best-practices.md)
* LUIS의 [개발자 리소스](developer-reference-resource.md "개발자 리소스").
* [의도](luis-concept-intent.md "의도") 및 [엔터티](luis-concept-entity-types.md "엔터티")를 사용하여 [앱을 계획](luis-how-plan-your-app.md "앱 계획")합니다.

[bot-framework]: /bot-framework/
[flow]: /connectors/luis/
[authoring-apis]: https://go.microsoft.com/fwlink/?linkid=2092087
[endpoint-apis]: https://go.microsoft.com/fwlink/?linkid=2092356
[qnamaker]: https://qnamaker.ai/
