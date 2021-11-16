---
title: Azure Cognitive Service for Language란?
titleSuffix: Azure Cognitive Services
description: 정보를 추출하고 작성된 언어를 이해할 수 있는 애플리케이션에 AI를 통합하는 방법을 알아봅니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: overview
ms.date: 11/02/2021
ms.author: aahi
ms.custom: ignite-fall-2021
ms.openlocfilehash: 1b4a23b3c59050b99db2938b33091d359f3c34ea
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131502942"
---
# <a name="what-is-azure-cognitive-service-for-language"></a>Azure Cognitive Service for Language란?

Azure Cognitive Service for Language는 텍스트를 이해하고 분석하기 위한 NLP(자연어 처리) 기능을 제공하는 클라우드 기반 서비스입니다. 이 서비스를 사용하면 웹 기반 Language Studio, REST API 및 클라이언트 라이브러리를 사용하여 지능형 애플리케이션을 빌드할 수 있습니다.  

이 언어 서비스는 Text Analytics, QnA Maker 및 LUIS를 통합하고 몇 가지 새로운 기능도 제공합니다. 이러한 기능은 다음 중 하나일 수 있습니다.
* 미리 구성되어 있습니다. 즉, 기능에서 사용하는 AI 모델은 사용자 지정할 수 없습니다. 데이터를 보내고 애플리케이션에서 기능의 출력을 사용하기만 하면 됩니다.
* 사용자 지정 가능. 즉, 데이터를 구체적으로 맞추는 도구를 사용하여 AI 모델을 학습시킬 수 있습니다.

## <a name="migrate-from-text-analytics-qna-maker-or-language-understanding"></a>Text Analytics, QnA Maker 또는 Language Understanding에서 마이그레이션하나요?

Azure Cognitive Services for Language는 Cognitive Services에 Text Analytics, QnA Maker 및 LUIS(Language Understanding)의 세 가지 개별 언어 서비스를 통합합니다. 이러한 세 가지 서비스를 사용한 경우 새 Azure Cognitive Services for Language로 쉽게 마이그레이션할 수 있습니다. 지침에 대해서는 [Azure Cognitive Services for Language로 마이그레이션](concepts/migrate.md)을 참조하세요.  

## <a name="available-features"></a>사용 가능한 기능

Azure Cognitive Service for Language는 다음과 같은 기능을 제공합니다.

> [!div class="mx-tdCol2BreakAll"]
> |기능  |설명  | 배포 옵션| 
> |---------|---------|---------|
> | [NER(명명된 엔터티 인식)](named-entity-recognition/overview.md)     | 미리 구성된 이 기능은 미리 정의된 여러 범주에서 텍스트의 엔터티를 식별합니다.        | • [Language Studio](language-studio.md) <br> • [REST API 및 클라이언트 라이브러리](named-entity-recognition/quickstart.md) |
> | [PII(개인 식별 정보) 검색](personally-identifiable-information/overview.md)     | 미리 구성된 이 기능은 계정 정보와 같은 중요한 정보의 미리 정의된 여러 범주에서 텍스트의 엔터티를 식별합니다.        | • [Language Studio](language-studio.md) <br> • [REST API 및 클라이언트 라이브러리](named-entity-recognition/quickstart.md) |
> | [핵심 구 추출](key-phrase-extraction/overview.md)     | 미리 구성된 이 기능은 비정형 텍스트를 평가하고 각 입력 문서에 대해 텍스트의 핵심 구 및 주요 지점 목록을 반환합니다. | • [Language Studio](language-studio.md) <br> • [REST API 및 클라이언트 라이브러리](key-phrase-extraction/quickstart.md) <br> • [Docker 컨테이너](key-phrase-extraction/how-to/use-containers.md)  |
> |[엔터티 연결](entity-linking/overview.md)    | 미리 구성된 이 기능은 텍스트에 있는 엔터티의 ID를 명확히 하고 Wikipedia의 엔터티에 대한 링크를 제공합니다.        | • [Language Studio](language-studio.md) <br> • [REST API 및 클라이언트 라이브러리](entity-linking/quickstart.md) |
> | [의료 분야 Text Analytics](text-analytics-for-health/overview.md)    | 미리 구성된 이 기능은 임상 메모 및 의사 메모와 같은 비정형 의료 텍스트에서 정보를 추출합니다.  | • [Language Studio](language-studio.md) <br> • [REST API 및 클라이언트 라이브러리](text-analytics-for-health/quickstart.md) <br> • [Docker 컨테이너](text-analytics-for-health/how-to/use-containers.md) |
> | [사용자 지정 NER](custom-named-entity-recognition/overview.md)    | 사용자가 제공하는 비정형 텍스트를 사용하여 사용자 지정 엔터티 범주를 추출하는 AI 모델을 빌드합니다. |  • [Language Studio](custom-named-entity-recognition/quickstart.md?pivots=language-studio) <br> • [REST API](custom-named-entity-recognition/quickstart.md?pivots=rest-api) |
> | [감정 및 의견 분석](sentiment-opinion-mining/overview.md)     | 미리 구성된 이 기능은 문장 및 문서에 대한 감정 레이블(예: "*부정*", "*중립*" 및 "*긍정*")을 제공합니다. 이 기능은 제품 또는 서비스의 특성과 같이 텍스트에 표시되는 단어와 관련된 의견에 대한 세부적인 정보를 추가로 제공할 수 있습니다. |  • [Language Studio](language-studio.md) <br> • [REST API 및 클라이언트 라이브러리](sentiment-opinion-mining/quickstart.md) <br> • [Docker 컨테이너](sentiment-opinion-mining/how-to/use-containers.md)
> |[언어 감지](language-detection/overview.md)    | 미리 구성된 이 기능은 텍스트를 평가하고 작성된 언어를 결정합니다. 분석의 강도를 나타내는 언어 식별자와 점수를 반환합니다.        | • [Language Studio](language-studio.md) <br> • [REST API 및 클라이언트 라이브러리](language-detection/quickstart.md) <br> • [Docker 컨테이너](language-detection/how-to/use-containers.md) |
> |[사용자 지정 텍스트 분류(미리 보기)](custom-classification/overview.md)    | AI 모델을 빌드하여 비정형 텍스트를 사용자가 정의한 사용자 지정 클래스로 분류합니다.         | • [Language Studio](custom-classification/quickstart.md?pivots=language-studio)<br> • [REST API](language-detection/quickstart.md?pivots=rest-api) |
> | [텍스트 요약(미리 보기)](text-summarization/overview.md)     | 미리 구성된 이 기능은 문서의 핵심을 전체적으로 전달하는 주요 문장을 추출합니다. | • [Language Studio](language-studio.md) <br> • [REST API 및 클라이언트 라이브러리](text-summarization/quickstart.md) |
> | [대화형 언어 이해(미리 보기)](conversational-language-understanding/overview.md)   | AI 모델을 빌드하여 자연어를 이해하는 기능을 앱, 봇 및 IoT 디바이스로 가져옵니다. | • [Language Studio](conversational-language-understanding/quickstart.md)
> | [질문 답변](question-answering/overview.md)     | 미리 구성된 이 기능은 FAQ, 설명서 및 문서와 같은 비정형 콘텐츠를 사용하여 텍스트 입력에서 추출된 질문에 대한 답변을 제공합니다. | • [Language Studio](language-studio.md) <br> • [REST API 및 클라이언트 라이브러리](question-answering/quickstart/sdk.md) |

## <a name="tutorials"></a>자습서

언어 서비스를 시작한 후에는 다양한 시나리오를 해결하는 방법을 보여주는 자습서를 수행해 보세요.

* [Power BI에 저장된 텍스트에서 핵심 구 추출](key-phrase-extraction/tutorials/integrate-power-bi.md)
* [Power Automate를 사용하여 Microsoft Excel 정보 정렬](named-entity-recognition/tutorials/extract-excel-information.md) 
* [Flask를 사용하여 텍스트 번역, 감정 분석 및 음성 합성](/translator/tutorial-build-flask-app-translation-synthesis?context=/azure/cognitive-services/language-service/context/context)
* [캔버스 앱에서 Cognitive Services 사용](/powerapps/maker/canvas-apps/cognitive-services-api?context=/azure/cognitive-services/language-service/context/context)
* [FAQ Bot 만들기](question-answering/tutorials/bot-service.md)

## <a name="additional-code-samples"></a>추가 코드 샘플

GitHub에서 다음 언어에 대한 추가 코드 샘플을 찾을 수 있습니다.

* [C#](https://github.com/Azure/azure-sdk-for-net/tree/main/sdk/textanalytics/Azure.AI.TextAnalytics/samples)
* [Java](https://github.com/Azure/azure-sdk-for-java/tree/main/sdk/textanalytics/azure-ai-textanalytics/src/samples)
* [JavaScript](https://github.com/Azure/azure-sdk-for-js/tree/main/sdk/textanalytics/ai-text-analytics/samples)
* [Python](https://github.com/Azure/azure-sdk-for-python/tree/main/sdk/textanalytics/azure-ai-textanalytics/samples)

## <a name="deploy-on-premises-using-docker-containers"></a>Docker 컨테이너를 사용하여 온-프레미스 배포 
언어 서비스 컨테이너를 사용하여 온-프레미스에 API 기능을 배포합니다. 이러한 Docker 컨테이너를 사용하면 규정 준수, 보안 또는 기타 운영상의 이유로 서비스를 데이터에 더 가깝게 가져올 수 있습니다. 언어 서비스는 다음과 같은 컨테이너를 제공합니다.

* [감정 분석](sentiment-opinion-mining/how-to/use-containers.md)
* [언어 감지](language-detection/how-to/use-containers.md)
* [핵심 구 추출](key-phrase-extraction/how-to/use-containers.md) 
* [의료 분야 Text Analytics](text-analytics-for-health/how-to/use-containers.md)


## <a name="responsible-ai"></a>Responsible AI 

AI 시스템에는 기술뿐만 아니라 기술을 사용하는 사람, 영향을 받는 사람, 배포되는 환경이 포함됩니다. 시스템에서 책임 있는 AI 사용 및 배포에 대해 알아보려면 다음 문서를 읽어보세요.

* [언어 서비스에 대한 투명도 참고 사항](/legal/cognitive-services/text-analytics/transparency-note)
* [윤리 및 책임 있는 사용](/legal/cognitive-services/text-analytics/guidance-integration-responsible-use)
* [데이터, 개인 정보 및 보안](/legal/cognitive-services/text-analytics/data-privacy)
