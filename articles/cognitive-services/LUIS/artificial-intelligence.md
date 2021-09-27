---
title: AI(인공 지능)
description: LUIS는 AI(인공 지능)를 사용하여 사용자가 정의한 스키마에 따라 데이터에 대한 언어 이해를 제공합니다.
ms.topic: conceptual
ms.service: cognitive-services
ms.subservice: language-understanding
ms.date: 08/07/2020
ms.custom: cog-serv-seo-aug-2020
ms.openlocfilehash: 623b50bee72f99be4d31159165f1af5b98f67b88
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124750741"
---
# <a name="artificial-intelligence-in-language-understanding-luis"></a>LUIS(Language Understanding)의 인공 지능

LUIS는 AI(인공 지능)를 사용하여 사용자가 정의한 스키마에 따라 데이터에 대한 NLU(자연어 이해)를 제공합니다.

## <a name="natural-language-processing-nlp"></a>NLP(자연어 처리)

NLU(자연어 이해)는 NLP(자연어 처리)의 특정 하위 주제입니다.

자연어 처리는 모든 형태의 텍스트 데이터 처리를 다루는 광범위한 개념입니다. 여기에는 다음과 같은 항목이 포함됩니다.

* 토큰화,
* 음성 부분(pos) 태그 지정
* 분할
* 형태학적 분석
* 의미 체계 유사성
* 담론(Discourse)
* Translation

## <a name="natural-language-processing-in-luis"></a>LUIS에서 자연어 처리

자연어 처리는 다음과 같은 방법으로 LUIS 앱에서 사용할 수 있습니다.
* [자연어 인식](#natural-language-processing-nlp)(LUIS)
* LUIS에서 구성 가능한 NLP 측면:
    * [토큰화](luis-language-support.md#tokenization)
    * 분음 부호, 문장 부호 및 단어 형태 [API 설정](luis-reference-application-settings.md)을 통한 형태학
* 다음과 같이 다른 [Cognitive Services](../what-are-cognitive-services.md)에서 제공하는 쿼리 발화의 사전 또는 사후 처리
    * [번역](../translator/translator-overview.md)

## <a name="natural-language-understanding-nlu"></a>NLU(자연어 인식)

NLU는 언어적 문장을 사용자가 자연스럽게 이해할 수 있도록 하는 표현으로 _변환_ 하는 기능입니다. 자연어를 이해하는 것은 매우 어려운 문제이며 _AI-hard_ 문제로 정의됩니다.

LUIS는 의도 및 추출에 초점을 맞추기 위한 것이며, 다음과 같은 사항을 식별하는 것도 포함됩니다.
* 사용자가 원하는 항목
* 이야기하고 있는 대상

LUIS에는 예에 명시적인 식별이 없는 의미 체계 유사성과 같은 광범위한 _NLP_ 측면에 대한 정보가 거의 없습니다. 예를 들어 다음 토큰(단어)은 제공된 예의 유사한 컨텍스트에서 사용될 때까지는 세 개의 다른 항목입니다.

* 구입
* 구입 중(buying)
* 구입함(bought)

## <a name="next-steps"></a>다음 단계

* LUIS 앱의 [개발 수명 주기](luis-concept-app-iteration.md)에 대해 자세히 알아봅니다.