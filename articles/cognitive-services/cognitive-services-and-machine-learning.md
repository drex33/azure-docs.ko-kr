---
title: Cognitive Services 및 Machine Learning
titleSuffix: Azure Cognitive Services
description: Azure Cognitive Services가 기계 학습을 위한 다른 Azure 제품과 어디에 적합한지 알아봅니다.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 10/28/2021
ms.openlocfilehash: 58025ba9bd0fb8a90395de48c8ab9d24e8746438
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131439237"
---
# <a name="cognitive-services-and-machine-learning"></a>Cognitive Services 및 기계 학습

Cognitive Services는 텍스트 분석을 통한 정서적 감정 분석이나 이미지 분석을 통한 개체 또는 얼굴 인식과 같은 일반적인 문제를 해결하기 위한 기계 학습 기능을 제공합니다. 이 서비스를 사용하기 위해 특별한 기계 학습 또는 데이터 과학 지식이 필요하지 않습니다. 

[Cognitive Services](./what-are-cognitive-services.md)는 각각 서로 다른 일반화된 예측 기능을 지원하는 서비스 그룹입니다. 서비스는 올바른 서비스를 찾는 데 도움이 되는 여러 범주로 나뉩니다. 

|서비스 범주|목적|
|--|--|
|[의사 결정](https://azure.microsoft.com/services/cognitive-services/directory/decision/)|합리적이고 효율적인 의사 결정을 위해 권장 사항을 표시하는 앱을 빌드하세요.|
|[언어](https://azure.microsoft.com/services/cognitive-services/directory/lang/)|앱에서 사전 구축된 스크립트를 통해 자연어를 처리하고, 감성을 평가하며, 사용자가 원하는 것을 인식하는 방법을 학습하도록 지원하세요.|
|[검색](https://azure.microsoft.com/services/cognitive-services/directory/search/)|Add Bing Search API를 앱에 추가하고 단일 API 호출 기능을 활용하여 수십억 개의 웹 페이지, 이미지, 동영상 및 뉴스를 철저히 검색하는 기능을 활용해 보세요.|
|[Speech](https://azure.microsoft.com/services/cognitive-services/directory/speech/)|음성을 텍스트로 변환하고 텍스트를 자연스러운 음성으로 변환합니다. 한 언어를 다른 언어로 번역하고 화자 검증 및 인식 기능을 사용하도록 설정할 수 있습니다.|
|[Vision](https://azure.microsoft.com/services/cognitive-services/directory/vision/)|사진, 동영상 및 디지털 잉크 콘텐츠를 인식 및 식별하고 캡션을 추가하고 인덱싱 및 조정합니다.|

다음의 경우 Cognitive Services를 사용합니다.

* 일반화된 솔루션을 사용할 수 있습니다.
* 프로그래밍 REST API 또는 SDK에서 솔루션에 액세스합니다. 

다음의 경우 다른 기계 학습 솔루션을 사용합니다.

* 알고리즘을 선택하고 매우 구체적인 데이터를 학습해야 합니다.

## <a name="what-is-machine-learning"></a>기계 학습이란 무엇인가요?

기계 학습은 데이터와 알고리즘을 함께 가져와서 특정 요구를 해결하는 개념입니다. 데이터 및 알고리즘을 학습한 후 출력은 다른 데이터로 다시 사용할 수 있는 모델입니다. 학습된 모델은 새 데이터를 기반으로 하는 인사이트를 제공합니다. 

기계 학습 시스템을 빌드하는 과정에는 기계 학습 또는 데이터 과학에 대한 지식이 필요합니다.

기계 학습은 [AML(Azure Machine Learning) 제품 및 서비스](/azure/architecture/data-guide/technology-choices/data-science-and-machine-learning?context=azure%2fmachine-learning%2fstudio%2fcontext%2fml-context)를 사용하여 제공됩니다.

## <a name="what-is-a-cognitive-service"></a>Cognitive Service란?

Cognitive Service는 기계 학습 솔루션(데이터, 알고리즘 및 학습된 모델)의 구성 요소 중 일부 또는 전부를 제공합니다. 이러한 서비스는 기계 학습 또는 데이터 과학을 사용하지 않고도 데이터에 대한 일반적인 지식이 필요합니다. 이러한 서비스는 REST API 및 언어 기반 SDK를 모두 제공합니다. 따라서 서비스를 사용하려면 프로그래밍 언어 지식이 있어야 합니다.

## <a name="how-are-cognitive-services-and-azure-machine-learning-aml-similar"></a>Cognitive Services 및 AML(Azure Machine Learning)은 어떻게 유사합니까?

둘 다 AI(인공 지능)를 적용하여 비즈니스 작업을 향상시킬 수 있는 최종 목표를 가지지만 각 제품에서 이러한 기능을 제공하는 방법은 서로 다릅니다. 

일반적으로 대상 그룹은 다릅니다.

* Cognitive Services는 기계 학습 환경이 없는 개발자를 위한 것입니다.
* Azure Machine Learning은 데이터 과학자에 맞게 조정됩니다. 

## <a name="how-is-a-cognitive-service-different-from-machine-learning"></a>Cognitive Service는 기계 학습과 어떻게 다른가요?

Cognitive Service는 학습된 모델을 제공합니다. 이를 통해 데이터와 알고리즘을 함께 가져와 REST API 또는 SDK에서 사용할 수 있습니다. 시나리오에 따라 몇 분 내에 이 서비스를 구현할 수 있습니다.  Cognitive Service는 이미지의 텍스트 또는 항목 ID의 키 구와 같은 일반적인 문제에 대한 답변을 제공합니다. 

기계 학습은 성공적으로 구현하는 데 더 오랜 시간이 걸리는 프로세스입니다. 이 시간은 Cognitive Service에서 제공하는 것과 동일한 수준의 기능을 얻기 위해 데이터 수집, 정리, 변환, 알고리즘 선택, 모델 학습 및 배포에 소요됩니다. 기계 학습을 사용하면 매우 특수화된 및/또는 특정 문제에 대한 답변을 제공할 수 있습니다. 기계 학습 문제는 고려해야 하는 문제의 특정 주제와 데이터뿐만 아니라 데이터 과학의 전문 지식을 잘 알고 있어야 합니다.

## <a name="what-kind-of-data-do-you-have"></a>어떤 종류의 데이터가 있습니까?

서비스 그룹으로 Cognitive Services에는 학습된 모델에 대해 사용자 지정 데이터가 전혀 필요하지 않거나 일부 또는 모두가 필요할 수 있습니다. 

### <a name="no-additional-training-data-required"></a>추가 학습 데이터가 필요하지 않음

완전히 학습된 모델을 제공하는 서비스를 _불투명 상자_ 로 취급할 수 있습니다. 작동 방식 또는 학습에 사용된 데이터를 알 필요가 없습니다. 데이터를 완벽하게 학습된 모델로 가져와 예측을 얻을 수 있습니다. 

### <a name="some-or-all-training-data-required"></a>일부 또는 모든 학습 데이터가 필요함

일부 서비스를 사용하면 사용자 고유의 데이터를 가져온 다음, 모델을 학습시킬 수 있습니다. 이렇게 하면 사용자 고유의 데이터와 함께 서비스의 데이터 및 알고리즘을 사용하여 모델을 확장할 수 있습니다. 출력은 요구 사항과 일치합니다. 사용자 고유의 데이터를 가져올 때 서비스에 특정 한 방식으로 데이터에 태그를 지정해야 할 수 있습니다. 예를 들어 꽃을 식별하는 모델을 학습하는 경우 각 이미지에서 꽃의 위치와 함께 꽃 이미지 카탈로그를 제공하여 모델을 학습할 수 있습니다. 

서비스를 _사용_ 하면 데이터를 제공하여 자신의 데이터를 향상시킬 수 있습니다. 서비스에서 데이터를 제공 _해야 할 수도 있습니다_. 

### <a name="real-time-or-near-real-time-data-required"></a>실시간 또는 거의 실시간 데이터가 필요함

서비스는 효과적인 모델을 만들기 위해 실시간 또는 거의 실시간 데이터를 필요로 할 수 있습니다. 이러한 서비스는 상당한 양의 모델 데이터를 처리합니다. 

## <a name="service-requirements-for-the-data-model"></a>데이터 모델에 대한 서비스 요구 사항

다음 데이터는 각 서비스를 허용하거나 필요로 하는 데이터 종류로 범주화합니다.

|Cognitive Service|학습 데이터가 필요하지 않음|일부 또는 모든 학습 데이터를 제공함|실시간 또는 거의 실시간 데이터 수집|
|--|--|--|--|
|[Anomaly Detector](./Anomaly-Detector/overview.md)|x|x|x|
|Bing Search |x|||
|[Computer Vision](./computer-vision/overview.md)|x|||
|[Content Moderator](./Content-Moderator/overview.md)|x||x|
|[Custom Vision](./custom-vision-service/overview.md)||x||
|[Face](./Face/Overview.md)|x|x||
|[Ink Recognizer](/previous-versions/azure/cognitive-services/Ink-Recognizer/overview)|x|x||
|[언어 이해(LUIS)](./LUIS/what-is-luis.md)||x||
|[Personalizer](./personalizer/what-is-personalizer.md)|x*|x*|x|
|[QnA Maker](./QnAMaker/Overview/overview.md)||x||
|[화자 인식기](./speech-service/speaker-recognition-overview.md)||x||
|[TTS(텍스트 음성 변환)](speech-service/text-to-speech.md)|x|x||
|[STT(음성 텍스트 변환)](speech-service/speech-to-text.md)|x|x||
|[Speech Translation](speech-service/speech-translation.md)|x|||
|[언어 서비스](./language-service/overview.md)|x|||
|[Translator](./translator/translator-overview.md)|x|||
|[Translator - 사용자 지정 번역기](./translator/custom-translator/overview.md)||x||

*Personalizer는 정책 및 데이터를 평가하기 위해 서비스에서 수집하는 학습 데이터(실시간으로 작동함)만 필요합니다. Personalizer에는 선행 또는 일괄 학습을 위한 대량 기록 데이터 세트가 필요하지 않습니다. 

## <a name="where-can-you-use-cognitive-services"></a>Cognitive Services은 어디에서 사용할 수 있나요?
 
서비스는 REST API 또는 SDK 호출을 수행할 수 있는 모든 애플리케이션에서 사용됩니다. 애플리케이션의 예제로는 웹 사이트, 봇, 가상 또는 혼합 현실, 데스크톱 및 모바일 애플리케이션 등이 있습니다. 

## <a name="how-is-azure-cognitive-search-related-to-cognitive-services"></a>Azure Cognitive Search는 Cognitive Services와 어떤 관련이 있나요?

[Azure Cognitive Search](../search/search-what-is-azure-search.md)는 선택적으로 Cognitive Services를 사용하여 인덱싱 워크로드에 이미지 및 자연어 처리를 추가하는 별도의 클라우드 검색 서비스입니다. Cognitive Services는 개별 API를 래핑하는 [기본 기술](../search/cognitive-search-predefined-skills.md)을 통해 Azure Cognitive Search에서 노출됩니다. 연습에 무료 리소스를 사용할 수 있지만 더 큰 볼륨에 대해 [청구 가능한 리소스](../search/cognitive-search-attach-cognitive-services.md)를 만들고 연결하는 계획을 수립할 수 있습니다.

## <a name="how-can-you-use-cognitive-services"></a>Cognitive Services를 어떻게 사용할 수 있나요?

각 서비스는 데이터에 대한 정보를 제공합니다. 음성(오디오)을 텍스트로 변환하고 텍스트를 여러 언어로 번역한 다음, 번역된 언어를 사용하여 기술 자료에서 답변을 가져오는 등의 솔루션을 결합하여 서비스를 함께 결합할 수 있습니다. Cognitive Services를 사용하여 지능형 솔루션을 만들 수 있지만 모델을 보완하거나 개발 프로세스를 가속화하기 위해 기존 기계 학습 프로젝트와 결합할 수도 있습니다. 

다른 기계 학습 도구에 대해 내보낸 모델을 제공하는 Cognitive Services:

|Cognitive Service|모델 정보|
|--|--|
|[Custom Vision](./custom-vision-service/overview.md)|Android용 Tensorflow에 대한 [내보내기](./custom-vision-service/export-model-python.md), iOS11용 CoreML, Windows ML용 ONNX|

## <a name="learn-more"></a>자세한 정보

* [아키텍처 가이드 - Microsoft의 기계 학습 제품이란?](/azure/architecture/data-guide/technology-choices/data-science-and-machine-learning)
* [기계 학습 - 딥 러닝 및 기계 학습 소개](../machine-learning/concept-deep-learning-vs-machine-learning.md)

## <a name="next-steps"></a>다음 단계

* [Azure Portal](cognitive-services-apis-create-account.md) 또는 [Azure CLI](./cognitive-services-apis-create-account-cli.md)를 사용하여 Cognitive Service 계정을 만듭니다.
* Cognitive Service에 [인증](authentication.md)하는 방법에 대해 알아봅니다.
* 문제 식별 및 디버깅에 [진단 로깅](diagnostic-logging.md)을 사용합니다. 
* Docker [컨테이너](cognitive-services-container-support.md)에 Cognitive Service를 배포합니다.
* [서비스 업데이트](https://azure.microsoft.com/updates/?product=cognitive-services)를 사용하여 최신 상태로 유지합니다.