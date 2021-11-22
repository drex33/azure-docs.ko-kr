---
title: Azure Cognitive Service for Language의 새로운 기능은 무엇인가요?
titleSuffix: Azure Cognitive Services
description: Azure Cognitive Service for Language의 새로운 릴리스와 기능에 대해 알아보세요.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: overview
ms.date: 11/18/2021
ms.author: aahi
ms.custom: ignite-fall-2021
ms.openlocfilehash: 317d335ba2edc8aca83d0b3b296fcd2fe3d4953a
ms.sourcegitcommit: 81a1d2f927cf78e82557a85c7efdf17bf07aa642
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/19/2021
ms.locfileid: "132810029"
---
# <a name="whats-new-in-azure-cognitive-service-for-language"></a>Azure Cognitive Service for Language의 새로운 기능은 무엇인가요?

Azure Cognitive Service for Language는 지속적으로 업데이트됩니다. 최신 개발 정보를 항상 파악할 수 있게 이 문서에서는 새 릴리스 및 기능 관련 정보를 제공합니다.

## <a name="november-2021"></a>2021년 11월

* 지속적인 고객 피드백에 따라 상태에 대한 Text Analytics의 문서당 문자 제한을 5,120에서 30,720으로 늘렸습니다.

* 다음에 대한 지원을 포함하는 Azure Cognitive Service for Language:
    * [질문 답변(현재 일반 공급)](question-answering/overview.md) 
    * [감정 분석 및 오피니언 마이닝](sentiment-opinion-mining/overview.md)
    * [핵심 구 추출](key-phrase-extraction/overview.md)
    * [NER(명명된 엔터티 인식), PII(개인 식별 정보)](named-entity-recognition/overview.md)
    * [언어 감지](language-detection/overview.md)
    * [의료 분야 Text Analytics](text-analytics-for-health/overview.md)
    * [텍스트 요약 미리 보기](text-summarization/overview.md)
    * [사용자 지정 NER(사용자 지정 명명된 엔터티 인식) 미리 보기](custom-named-entity-recognition/overview.md)
    * [사용자 지정 텍스트 분류 미리 보기](custom-classification/overview.md)
    * [대화형 Language Understanding 미리 보기](conversational-language-understanding/overview.md)

* `2021-10-01-preview`감정 분석 및 오피니언 마이닝[에 대한 미리 보기 모델 버전 ](sentiment-opinion-mining/overview.md)는
    * 향상된 예측 품질을 제공합니다.
    * 의견 마이닝 기능에 대한 [추가 언어 지원](sentiment-opinion-mining/language-support.md?tabs=sentiment-analysis).
    * 자세한 내용은 [프로젝트 z-code 사이트](https://www.microsoft.com/research/project/project-zcode/)를 참조하세요.
    * 이 [모델 버전](sentiment-opinion-mining/how-to/call-api.md#specify-the-sentiment-analysis-model)을 사용하려면 모델 버전 매개 변수를 사용하여 API 호출에서 이를 지정해야 합니다. 

* 사용자 지정 모델에 요청을 보내기 위한 SDK 지원:
    * [사용자 지정 명명된 엔터티 인식](custom-named-entity-recognition/how-to/call-api.md?tabs=client#use-the-client-libraries)
    * [사용자 지정 텍스트 분류](custom-classification/how-to/call-api.md?tabs=api#use-the-client-libraries)
    * [사용자 지정 언어 이해](conversational-language-understanding/how-to/deploy-query-model.md#use-the-client-libraries)
 
## <a name="next-steps"></a>다음 단계

* [Azure Cognitive Service for Language란?](overview.md)  
