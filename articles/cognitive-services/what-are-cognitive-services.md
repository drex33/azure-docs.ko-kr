---
title: Azure Cognitive Services란?
titleSuffix: Azure Cognitive Services
description: Cognitive Services를 사용하면 기계 학습 및 데이터 과학 전문 지식 없이도 모든 개발자가 AI에 액세스할 수 있습니다. 앱에서 보고(고급 이미지 검색 및 인식), 듣고, 말하고, 검색하고 의사 결정을 내리는 기능을 추가하려면 애플리케이션에서 API를 호출하기만 하면 됩니다.
services: cognitive-services
author: nitinme
manager: nitinme
keywords: 인지적 서비스, 인지적 인텔리전스, 인지적 솔루션, AI 서비스, 인지적 이해, 인지적 기능
ms.service: cognitive-services
ms.subservice: ''
ms.topic: overview
ms.date: 10/08/2021
ms.author: nitinme
ms.custom: cog-serv-seo-aug-2020
ms.openlocfilehash: 2b5519d5a71f9a431bd5948283ea12ae79647196
ms.sourcegitcommit: 216b6c593baa354b36b6f20a67b87956d2231c4c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2021
ms.locfileid: "129730036"
---
# <a name="what-are-azure-cognitive-services"></a>Azure Cognitive Services란?

Azure Cognitive Services는 애플리케이션에 인식 인텔리전스를 빌드하는 데 도움이 되는 REST API 및 클라이언트 라이브러리 SDK가 포함된 클라우드 기반 서비스입니다. AI(인공 지능) 또는 데이터 과학 기술 없이도 애플리케이션에 인식 기능을 추가 할 수 있습니다. Azure Cognition Services는 보고, 듣고, 말하고, 이해하고, 심지어 결정을 내릴 수 있는 인식 솔루션을 구축할 수 있도록 해주는 다양한 AI 서비스로 구성되어 있습니다.

## <a name="categories-of-cognitive-services"></a>Cognitive Services의 범주

인지적 이해를 제공하는 Cognitive Services 카탈로그는 5개 주 범주로 분류됩니다.

* 시각
* 음성
* 언어
* 의사 결정
* 검색

이 문서의 다음 섹션에서는 5개 범주에 속하는 서비스 목록을 제공합니다.

## <a name="vision-apis"></a>Vision API

|서비스 이름|서비스 설명|
|:-----------|:------------------|
|[Computer Vision](./computer-vision/index.yml "Computer Vision")|Computer Vision 서비스는 이미지를 처리하고 정보를 반환하는 고급 인지 알고리즘에 대한 액세스를 제공합니다. 서비스를 시작하려면 [Computer Vision 빠른 시작](./computer-vision/quickstarts-sdk/client-library.md)을 참조하세요.|
|[Custom Vision Service](./custom-vision-service/index.yml "사용자 지정 시각 서비스")|Custom Vision Service를 사용하면 사용자 고유의 이미지 분류자를 빌드, 배포 및 개선할 수 있습니다. 이미지 분류자는 해당 시각적 특성에 따라 이미지에 레이블을 적용하는 AI 서비스입니다. |
|[Face](./face/index.yml "Face")| Face 서비스는 개선된 얼굴 인식 알고리즘에 액세스할 수 있도록 하여 얼굴 특성 감지 및 인식을 지원합니다. 서비스를 시작하려면 [Face 빠른 시작](./face/quickstarts/client-libraries.md)을 참조하세요.|

## <a name="speech-apis"></a>Speech API

|서비스 이름|서비스 설명|
|:-----------|:------------------|
|[Speech Service](./speech-service/index.yml "Speech Service")|Speech Service는 애플리케이션에 음성 지원 기능을 추가합니다. Speech Service에는 음성 텍스트 변환, 텍스트 음성 변환, 음성 번역 등과 같은 다양한 기능이 포함되어 있습니다.|
<!--
|[Speaker Recognition API](./speech-service/speaker-recognition-overview.md "Speaker Recognition API") (Preview)|The Speaker Recognition API provides algorithms for speaker identification and verification.|
|[Bing Speech](./speech-service/how-to-migrate-from-bing-speech.md "Bing Speech") (Retiring)|The Bing Speech API provides you with an easy way to create speech-enabled features in your applications.|
|[Translator Speech](/azure/cognitive-services/translator-speech/ "Translator Speech") (Retiring)|Translator Speech is a machine translation service.|
-->
## <a name="language-apis"></a>Language API

|서비스 이름|서비스 설명|
|:-----------|:------------------|
|[Language Understanding LUIS](./luis/index.yml "언어 이해")|LUIS(Language Understanding)는 사용자의 대화형 자연어 텍스트에 사용자 지정 기계 학습 인텔리전스를 적용하여 전체적인 의미를 예측하고 관련된 자세한 정보를 추출하는 클라우드 기반 대화형 AI 서비스입니다. 서비스를 시작하려면 [LUIS 빠른 시작을 참조](./luis/luis-get-started-create-app.md)하세요.|
|[QnA Maker](./qnamaker/index.yml "QnA Maker")|QnA Maker를 사용하여 반구조화된 콘텐츠로부터 질문 및 답변 서비스를 작성할 수 있습니다. 서비스를 시작하려면 [QnA Maker 빠른 시작을 참조](./qnamaker/quickstarts/create-publish-knowledge-base.md)하세요.|
|[Text Analytics](./text-analytics/index.yml "텍스트 분석")| Text Analytics는 감정 분석, 핵심 구 추출 및 언어 감지를 위한 원시 텍스트의 자연어 처리를 제공합니다. 서비스를 시작하려면 [Text Analytics 빠른 시작](./text-analytics/quickstarts/client-libraries-rest-api.md)을 참조하세요.|
|[Translator](./translator/index.yml "변환기")|Translator는 거의 실시간으로 머신 기반 텍스트 번역을 제공합니다.|

## <a name="decision-apis"></a>Decision API

|서비스 이름|서비스 설명|
|:-----------|:------------------|
|[Anomaly Detector](./anomaly-detector/index.yml "Anomaly Detector") |Anomaly Detector를 사용하면 시계열 데이터에서 변칙을 모니터링하고 감지할 수 있습니다. 서비스를 시작하려면 [Anomaly Detector 빠른 시작](./anomaly-detector/quickstarts/client-libraries.md)을 참조하세요.|
|[Content Moderator](./content-moderator/overview.md "Content Moderator")|Content Moderator는 불쾌감을 주거나, 원치 않거나, 위험한 콘텐츠를 모니터링하는 프로세스를 제공합니다. 서비스를 시작하려면 [Content Moderator 빠른 시작](./content-moderator/client-libraries.md)을 참조하세요.|
|[Personalizer](./personalizer/index.yml "Personalizer")|Personalizer를 사용하면 사용자의 실시간 동작을 통해 학습함으로써 사용자에게 표시할 최상의 환경을 선택할 수 있습니다. 서비스를 시작하려면 [Personalizer 빠른 시작을](./personalizer/quickstart-personalizer-sdk.md) 참조하세요.|

## <a name="search-apis"></a>Search API

> [!NOTE]
> [Azure Cognitive Search](../search/index.yml)를 찾고 있나요? 일부 작업에는 Cognitive Services를 사용하지만 다른 시나리오를 지원하는 다른 검색 기술입니다.

|서비스 이름|서비스 설명|
|:-----------|:------------------|
|[Bing News Search](/azure/cognitive-services/bing-news-search/ "Bing 뉴스 검색")|Bing News Search는 사용자의 쿼리와 관련된 것으로 확인된 뉴스 기사 목록을 반환합니다.|
|[Bing Video Search](/azure/cognitive-services/Bing-Video-Search/ "Bing 비디오 검색")|Bing Video Search는 사용자의 쿼리와 관련된 것으로 확인된 비디오 목록을 반환합니다.|
|[Bing Web Search](./bing-web-search/index.yml "Bing 웹 검색")|Bing Web Search는 사용자의 쿼리와 관련된 것으로 확인된 검색 결과 목록을 반환합니다.|
|[Bing Autosuggest](/azure/cognitive-services/Bing-Autosuggest "Bing Autosuggest")|Bing Autosuggest를 통해 부분 검색 쿼리 용어를 Bing에 전송하고 제안된 쿼리 목록을 다시 가져올 수 있습니다.|
|[Bing Custom Search](/azure/cognitive-services/bing-custom-search "Bing 사용자 지정 검색")|Bing Custom Search를 사용하면 관심 있는 토픽에 대한 맞춤형 검색 경험을 만들 수 있습니다.|
|[Bing Entity Search](/azure/cognitive-services/bing-entities-search/ "Bing Entity Search")|Bing Entity Search는 Bing이 사용자의 쿼리와 관련되었다고 결정하는 엔터티에 대한 정보를 반환합니다.|
|[Bing Image Search](/azure/cognitive-services/bing-image-search "Bing 이미지 검색")|Bing Image Search는 사용자의 쿼리와 관련된 것으로 확인된 이미지 표시를 반환합니다.|
|[Bing Visual Search](/azure/cognitive-services/bing-visual-search "Bing Visual Search")|Bing Visual Search는 시각적으로 유사한 이미지, 이미지에서 찾은 제품의 쇼핑 원본과 관련 검색 등 이미지에 대한 인사이트를 반환합니다.|
|[Bing Local Business Search](/azure/cognitive-services/bing-local-business-search/ "Bing Local Business Search")| Bing Local Business Search API를 사용하면 애플리케이션에서 검색 쿼리를 기반으로 로컬 비즈니스에 대한 연락처 및 위치 정보를 찾을 수 있습니다.|
|[Bing Spell Check](/azure/cognitive-services/bing-spell-check/ "Bing 맞춤법 검사")|Bing Spell Check API를 사용하여 상황별 문법 및 맞춤법 검사를 수행할 수 있습니다.|

## <a name="get-started-with-cognitive-services"></a>Cognitive Services 시작

다음을 사용하는 실습 빠른 시작으로 Cognitive Services 리소스를 만들어 시작합니다.

* [Azure Portal](cognitive-services-apis-create-account.md?tabs=multiservice%2Cwindows "Azure portal")
* [Azure CLI](cognitive-services-apis-create-account-cli.md?tabs=windows "Azure CLI")
* [Azure SDK 클라이언트 라이브러리](cognitive-services-apis-create-account-cli.md?tabs=windows "cognitive-services-apis-create-account-client-library?pivots=programming-language-csharp")
* [ARM(Azure Resource Manager) 템플릿](./create-account-resource-manager-template.md?tabs=portal "ARM(Azure Resource Manager) 템플릿")

## <a name="using-cognitive-services-in-different-development-environments"></a>다양한 개발 환경에서 Cognitive Services 사용

Azure 및 Cognitive Services를 사용하여 다음과 같은 몇 가지 개발 옵션에 액세스할 수 있습니다.

* Logic Apps 및 Power Automate와 같은 자동화 및 통합 도구
* Azure Functions 및 App Service와 같은 배포 옵션입니다. 
* 안전한 액세스를 위한 Cognitive Services Docker 컨테이너입니다.
* Apache Spark, Azure Databasericks, Azure Synapse Analytics 및 Azure Kubernetes Service for Big Data 시나리오와 같은 도구입니다. 

자세히 알아보려면 [Cognitive Services 개발 옵션](./cognitive-services-development-options.md)을 참조하세요.

<!--
## Subscription management

Once you are signed in with your Microsoft Account, you can access [My subscriptions](https://www.microsoft.com/cognitive-services/subscriptions "My subscriptions") to show the products you are using, the quota remaining, and the ability to add additional products to your subscription.

## Upgrade to unlock higher limits

All APIs have a free tier, which has usage and throughput limits.  You can increase these limits by using a paid offering and selecting the appropriate pricing tier option when deploying the service in the Azure portal. [Learn more about the offerings and pricing](https://azure.microsoft.com/pricing/details/cognitive-services/ "offerings and pricing"). You'll need to set up an Azure subscriber account with a credit card and a phone number. If you have a special requirement or simply want to talk to sales, click "Contact us" button at the top the pricing page.
-->

## <a name="using-cognitive-services-securely"></a>Cognitive Services 안전하게 사용

Azure Cognitive Services는 Azure Active Directory 자격 증명을 통한 [인증](authentication.md "인증"), 유효한 리소스 키 및 [Azure Virtual Networks](cognitive-services-virtual-networks.md "Azure Virtual Networks")를 비롯한 계층화된 보안 모델을 제공합니다.

## <a name="containers-for-cognitive-services"></a>Cognitive Services용 컨테이너

 Azure Cognitive Services는 Azure, 온-프레미스에서 사용할 수 있는 것과 동일한 API를 사용할 수 있는 여러 Docker 컨테이너를 제공합니다. 이러한 컨테이너를 사용하면 규정 준수, 보안 또는 기타 운영상의 이유로 Cognitive Services를 데이터에 더 가깝게 가져올 수 있는 유연성이 제공됩니다. [Cognitive Services 컨테이너](cognitive-services-container-support.md "Cognitive Services 컨테이너")에 대해 자세히 알아봅니다.

## <a name="regional-availability"></a>국가별 가용성

Cognitive Services에서 API는 증가하는 Microsoft 운영 데이터 센터의 네트워크에서 호스팅됩니다. [Azure 지역 목록](https://azure.microsoft.com/regions "Azure 지역 목록")에서 각 API에 대한 지역별 가용성을 확인할 수 있습니다.

아직 지원하지 않는 지역을 찾습니까? [UserVoice 포럼](https://feedback.azure.com/forums/932041-azure-cognitive-services "UserVoice 포럼")에서 기능 요청을 제출하여 Microsoft에 알려주세요.

## <a name="supported-cultural-languages"></a>지원되는 문화 언어

Cognitive Services는 서비스 수준에서 광범위한 문화 언어를 지원합니다. [지원되는 언어 목록](language-support.md "지원되는 언어 목록")에서 각 API에서 사용할 수 있는 언어를 찾을 수 있습니다.

## <a name="certifications-and-compliance"></a>인증 및 규정 준수

Cognitive Services은 CSA STAR 인증, FedRAMP Moderate 및 HIPAA BAA와 같은 인증을 받았습니다. 해당하는 감사 및 보안 검토에 대한 인증을 [다운로드](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942 "다운로드로 사용 가능한 제품 설명서에서 데이터 공급자 설치 섹션을 참조하세요")할 수 있습니다.

개인 정보 및 데이터 관리를 이해하려면 [보안 센터](https://servicetrust.microsoft.com/ "보안 센터")로 이동하세요.

## <a name="support"></a>지원

Cognitive Services는 지능형 애플리케이션 생성을 진행하는 데 도움이 되는 몇 가지 지원 옵션을 제공합니다. Cognitive Services에는 또한 특정 질문에 답변할 수 있는 강력한 개발자 커뮤니티가 있습니다. 사용 가능한 전체 옵션 목록은 [Cognitive Services 지원 및 도움말 옵션](cognitive-services-support-options.md "Cognitive Services 지원 및 도움말 옵션")을 참조하세요.

## <a name="next-steps"></a>다음 단계

* [Cognitive Services 계정 만들기](cognitive-services-apis-create-account.md "Cognitive Services 계정 만들기")
* [Cognitive Services에 대한 비용 계획 및 관리](plan-manage-costs.md)
