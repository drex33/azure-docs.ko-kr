---
title: Azure Digital Twins 쿼리 언어 참조 - 연산자
titleSuffix: Azure Digital Twins
description: Azure Digital Twins 쿼리 언어 연산자에 대한 참조 설명서
author: baanders
ms.author: baanders
ms.date: 03/22/2021
ms.topic: article
ms.service: digital-twins
ms.openlocfilehash: 06945efc6be8700955b1a475d00d21951102c214
ms.sourcegitcommit: bc29cf4472118c8e33e20b420d3adb17226bee3f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/08/2021
ms.locfileid: "113492796"
---
# <a name="azure-digital-twins-query-language-reference-operators"></a>Azure Digital Twins 쿼리 언어 참조: 연산자

이 문서에는 [Azure Digital Twins 쿼리 언어](concepts-query-language.md)의 **연산자** 에 대한 참조 정보가 포함되어 있습니다.

## <a name="comparison-operators"></a>비교 연산자

비교 제품군의 다음 연산자가 지원됩니다.

* `=`, `!=`: 식의 같음을 비교하는 데 사용됩니다.
* `<`, `>`: 식의 순서가 지정된 비교에 사용됩니다.
* `<=`, `>=`: 같음을 포함하여 식의 순서가 지정된 비교에 사용됩니다.

### <a name="example"></a>예제

`=`을 사용하는 예제는 다음과 같습니다. 다음 쿼리는 온도 값이 80인 트윈을 반환합니다.

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="EqualityExample":::

`<`을 사용하는 예제는 다음과 같습니다. 다음 쿼리는 온도 값이 80 미만인 트윈을 반환합니다.

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="ComparisonExample":::

`<=`을 사용하는 예제는 다음과 같습니다. 다음 쿼리는 온도 값이 80보다 작거나 같은 트윈을 반환합니다.

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="OrderedComparisonExample":::

## <a name="contains-operators"></a>연산자 포함

포함 제품군의 다음 연산자가 지원됩니다.

* `IN`: 지정된 값이 값 세트에 있으면 true로 평가됩니다.
* `NIN`: 지정된 값이 값 세트에 없으면 true로 평가됩니다.

### <a name="example"></a>예

`IN`을 사용하는 예제는 다음과 같습니다. 다음 쿼리는 `owner` 속성이 목록의 여러 옵션 중 하나인 트윈을 반환합니다.

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="InExample":::

## <a name="logical-operators"></a>논리 연산자

논리 제품군의 다음 연산자가 지원됩니다.
* `AND`: 두 식을 연결하는 데 사용되며 둘 다 true인 경우 true로 평가됩니다.
* `OR`: 두 식을 연결하는 데 사용되며 둘 중 하나 이상이 true이면 true로 평가됩니다.
* `NOT`: 식을 부정하는 데 사용되며 식 조건이 충족되지 않으면 true로 평가됩니다.

### <a name="example"></a>예

`AND`를 사용하는 예제는 다음과 같습니다. 다음 쿼리는 온도가 80 미만이고 습도가 50 미만인 두 조건을 모두 충족하는 트윈을 반환합니다.

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="AndExample":::

`OR`를 사용하는 예제는 다음과 같습니다. 다음 쿼리는 온도가 80 미만이고 습도가 50 미만인 조건 중 하나 이상을 충족하는 트윈을 반환합니다.

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="OrExample":::

`NOT`을 사용하는 예제는 다음과 같습니다. 다음 쿼리는 온도가 80 미만인 조건을 충족하지 않는 트윈을 반환합니다.

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="NotExample":::

## <a name="limitations"></a>제한 사항

연산자를 사용하는 쿼리에는 다음과 같은 제한이 적용됩니다.
* [IN/NIN 제한](#limit-for-innin)

자세한 내용은 아래 섹션을 참조하세요.

### <a name="limit-for-innin"></a>IN/NIN 제한

`IN` 또는 `NIN` 집합에 포함될 수 있는 값 수의 제한은 100개입니다.
