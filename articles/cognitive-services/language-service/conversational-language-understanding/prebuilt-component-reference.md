---
title: 지원 되는 미리 작성 한 엔터티 구성 요소
titleSuffix: Azure Cognitive Services
description: 자동으로 검색 될 수 있는 엔터티에 대해 자세히 알아보세요 Language Understanding
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-clu, ignite-fall-2021
ms.openlocfilehash: fc7188b647e6e299390179c68bcf9c9742a018f8
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131102946"
---
# <a name="supported-prebuilt-entity-components"></a>지원 되는 미리 작성 한 엔터티 구성 요소

대화형 Language Understanding를 사용 하면 미리 빌드된 구성 요소를 엔터티에 추가할 수 있습니다. 미리 빌드된 구성 요소는 길이 발언에서 일반적인 형식을 자동으로 예측 합니다. 구성 요소에 대 한 자세한 내용은 [엔터티 구성 요소](concepts/entity-components.md) 문서를 참조 하세요.

## <a name="reference"></a>참고

다음의 미리 작성 된 구성 요소는 대화형 Language Understanding에서 사용할 수 있습니다.

| 형식 | 설명 | 지원되는 언어 |
| --- | --- | --- |
| 수량. 연령 | 개인 또는 사물의 연령입니다. 예: "30 년 이전", "9 개월 이전" | 영어, 중국어, 프랑스어, 이탈리아어, 독일어, 포르투갈어 (브라질), 스페인어 |
| 수량. Number | 숫자 또는 텍스트 형식의 기 수입니다. 예: "30", "23", "14.5", "Two and a" | 영어, 중국어, 프랑스어, 이탈리아어, 독일어, 포르투갈어 (브라질), 스페인어 |
| 수량. 백분율 | 기호% 또는 단어 "percent"를 사용 하는 백분율입니다. 예: "10%", "5.6%" | 영어, 중국어, 프랑스어, 이탈리아어, 독일어, 포르투갈어 (브라질), 스페인어 |
| Quantity. 서 수 | 숫자 또는 텍스트 형식의 서 수입니다. 예: "first", "second", "last", "10" | 영어, 중국어, 프랑스어, 이탈리아어, 독일어, 포르투갈어 (브라질), 스페인어 |
| Quantity. 차원 | 길이, 거리, 볼륨, 면적 및 속도와 같은 특수 한 차원입니다. 예: "2 마일", "650 평방 킬로미터", "35 km/h" | 영어, 중국어, 프랑스어, 이탈리아어, 독일어, 포르투갈어 (브라질), 스페인어 |
| 수량. 온도 | 섭씨 또는 화씨 온도입니다. 예: "32F", "34도 섭씨", "2도 C" | 영어, 중국어, 프랑스어, 이탈리아어, 독일어, 포르투갈어 (브라질), 스페인어 |
| 수량. 통화 | 통화를 포함 하는 금액 금액입니다. 예: "1000.00 미국 달러", "£20.00", "$ 67.5 B" | 영어, 중국어, 프랑스어, 이탈리아어, 독일어, 포르투갈어 (브라질), 스페인어 |
| DateTime | 날짜 및 시간입니다. 예: "June 23, 1976", "오전 7 시", "6:49 PM", "내일 오후 7 시", "다음 주" | 영어, 중국어, 프랑스어, 이탈리아어, 독일어, 포르투갈어 (브라질), 스페인어 |
| 메일 | 전자 메일 주소. 예: " user@contoso.com ", " user_name@contoso.com ", " user.name@contoso.com " | 영어, 중국어, 프랑스어, 이탈리아어, 독일어, 포르투갈어 (브라질), 스페인어 |
| 전화 번호 | 미국 전화 번호입니다. 예: "123-456-7890", "+ 1 123 456 7890", "(123) 456-7890" | 영어, 중국어, 프랑스어, 이탈리아어, 독일어, 포르투갈어 (브라질), 스페인어 |
| URL | 웹 사이트 Url 및 링크. | 영어, 중국어, 프랑스어, 이탈리아어, 독일어, 포르투갈어 (브라질), 스페인어 |

## <a name="prebuilt-components-in-multilingual-projects"></a>다국어 프로젝트의 미리 빌드된 구성 요소

다국어 대화 프로젝트에서 미리 작성 된 구성 요소를 사용 하도록 설정할 수 있습니다. 구성 요소는 쿼리 언어가 미리 작성 된에서 지원 되는 경우에만 예측 됩니다. 언어가 요청에 지정 되었거나 지정 되지 않은 경우 응용 프로그램의 주 언어로 기본 설정 되어 있습니다.

## <a name="next-steps"></a>다음 단계

[엔터티 구성 요소](concepts/entity-components.md) 
