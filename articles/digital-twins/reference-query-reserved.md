---
title: Azure Digital Twins 쿼리 언어 참조-예약 키워드
titleSuffix: Azure Digital Twins
description: Azure Digital Twins 쿼리 언어 예약 키워드에 대 한 참조 설명서
author: baanders
ms.author: baanders
ms.date: 9/1/2021
ms.topic: article
ms.service: digital-twins
ms.openlocfilehash: 6ffba46cacf452a4769709b115177bab41d5eca7
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124838686"
---
# <a name="azure-digital-twins-query-language-reference-reserved-keywords"></a>Azure 디지털 Twins 쿼리 언어 참조: 예약 키워드

이 문서에는 [Azure Digital Twins 쿼리 언어](concepts-query-language.md)의 **예약 된 키워드** 목록이 포함 되어 있습니다. 이러한 단어는 [이중 대괄호로 이스케이프](#escaping-reserved-keywords-in-queries)되지 않는 한 쿼리에서 식별자로 사용할 수 없습니다. 

## <a name="list-of-reserved-keywords"></a>예약 된 키워드 목록

Azure Digital Twins 쿼리 언어의 예약 된 키워드는 다음과 같습니다.

* ALL 
* AND
* AS
* ASC
* 평균
* BY
* 개수
* DESC
* DEVICES_JOBS
* DEVICES_MODULES
* 장치
* ENDS_WITH
* FALSE
* FROM
* GROUP
* IN
* IS_BOOL
* IS_DEFINED
* IS_NULL
* IS_NUMBER
* IS_OBJECT
* IS_PRIMITIVE
* IS_STRING
* 최대
* 최소
* NOT
* NOT_IN
* NULL
* 또는
* ORDER
* SELECT
* STARTS_WITH
* 합계
* 맨 위로 이동
* TRUE
* WHERE
* IS_OF_MODEL

## <a name="escaping-reserved-keywords-in-queries"></a>쿼리에서 예약 된 키워드 이스케이프

쿼리에서 예약 된 키워드를 식별자로 사용 하려면 다음과 같이 이중 대괄호로 묶어 키워드를 이스케이프 합니다. `[[<keyword>]]`

예를 들어, 예약 키워드인 라는 속성을 사용 하 여 디지털 쌍 집합을 가정 합니다 `GROUP` . 속성 값을 필터링 하려면 아래와 같이 쿼리에서 사용 되는 경우 속성 이름을 이스케이프 해야 합니다.

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="ReservedKeywordExample":::