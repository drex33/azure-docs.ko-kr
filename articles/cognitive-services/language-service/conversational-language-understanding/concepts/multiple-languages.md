---
title: 다국어 프로젝트
titleSuffix: Azure Cognitive Services
description: 대화형 언어 이해에서 다국어 프로젝트를 사용 하는 방법에 대해 알아봅니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: aahi
ms.custom: ignite-fall-2021
ms.openlocfilehash: 4bbad9e51e26d643ff121e9b80b5da3fbafd89b9
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131053574"
---
# <a name="multilingual-projects"></a>다국어 프로젝트

대화형 언어 이해를 통해 프로젝트를 한 번에 여러 언어로 쉽게 확장할 수 있습니다. 프로젝트에서 여러 언어를 사용 하도록 설정 하면 프로젝트에 언어별 길이 발언 및 동의어를 추가 하 고 해당 사용자 및 엔터티에 대 한 다국어 예측을 얻을 수 있습니다. 

## <a name="multilingual-intent-and-learned-entity-components"></a>다국어 의도 및 학습 한 엔터티 구성 요소

프로젝트에서 여러 언어를 사용 하도록 설정 하는 경우 프로젝트를 주로 한 언어로 학습 하 고 다른 언어로 예측을 즉시 가져올 수 있습니다. 

예를 들어 영어 길이 발언을 사용 하 여 프로젝트를 전체적으로 학습 하 고 프랑스어, 독일어, 북경어, 일본어, 한국어 등에서 쿼리할 수 있습니다. 대화형 언어 이해를 통해 모델을 학습 하기 위해 다국어 기술을 사용 하 여 프로젝트를 여러 언어로 쉽게 확장할 수 있습니다.

특정 언어가 다른 언어와 함께 수행 되지 않는 것을 확인할 때마다 프로젝트에 해당 언어에 대 한 길이 발언를 추가할 수 있습니다. Language Studio의 [tag 길이 발언](../how-to/tag-utterances.md) 페이지에서 추가 중인 utterance의 언어를 선택할 수 있습니다. 모델에 해당 언어에 대 한 예제를 도입 하는 경우 해당 언어의 구문에 대 한 추가 정보를 제공 하 고 더 나은 예측을 학습 합니다.

모든 언어에 대해 동일한 양의 길이 발언를 추가할 필요는 없습니다. 대부분의 프로젝트를 하나의 언어로 빌드해야 하며 관찰 하는 언어에서 몇 가지 길이 발언 추가 하면 됩니다. 주로 영어로 제공 되는 프로젝트를 만들고 프랑스어, 독일어 및 스페인어로 테스트를 시작 하는 경우 독일어가 다른 두 언어를 수행 하지 않는다는 것을 알 수 있습니다. 이 경우 원본 영어의 5%를 독일어로 추가 하 고, 새 모델을 학습 하 고, 독일어로 다시 테스트 하는 것이 좋습니다. 독일어 쿼리에 대해 더 나은 결과를 볼 수 있습니다. 더 많은 길이 발언 더 많은 결과를 얻을 가능성이 높습니다. 

다른 언어로 데이터를 추가 하는 경우 다른 언어에 부정적인 영향을 줄 수 있습니다. 

## <a name="list-and-prebuilt-components-in-multiple-languages"></a>여러 언어로 작성 되는 구성 요소 목록 및 미리 작성

여러 언어를 사용 하도록 설정 된 프로젝트를 사용 하면 모든 목록 키에 대해 **언어별** 동의어를 지정할 수 있습니다. 프로젝트를 쿼리 하는 언어에 따라 해당 언어의 동의어가 포함 된 목록 구성 요소에 대 한 일치 항목만 가져옵니다. 프로젝트를 쿼리하면 요청 본문에서 언어를 지정할 수 있습니다.

```json
"query": "{query}"
"language": "{language code}"
```

언어를 제공 하지 않으면 프로젝트의 기본 언어로 대체 됩니다. 다른 언어 코드 목록은 [언어 지원](../language-support.md) 문서를 참조 하세요.

미리 작성 된 구성 요소는 유사 하며, 특정 언어로 제공 되는 미리 작성 된 구성 요소에 대 한 예측을 가져와야 합니다. 요청 언어에서 예측 하려는 구성 요소를 다시 결정 합니다. 미리 작성 된 각 구성 요소의 언어 지원에 대해서는 미리 작성 된 [구성 요소](../prebuilt-component-reference.md) 참조 문서를 참조 하세요.

## <a name="next-steps"></a>다음 단계

* [발화 태그 지정](../how-to/tag-utterances.md) 
* [모델 학습](../how-to/train-model.md)
