---
title: Azure Digital Twins 쿼리 언어 참조 - FROM 절
titleSuffix: Azure Digital Twins
description: Azure Digital Twins 쿼리 언어 FROM 절에 대한 참조 설명서
author: baanders
ms.author: baanders
ms.date: 03/31/2021
ms.topic: article
ms.service: digital-twins
ms.openlocfilehash: 1cbdbad379887687c71bd8c52b5791bde4e08a08
ms.sourcegitcommit: bc29cf4472118c8e33e20b420d3adb17226bee3f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/08/2021
ms.locfileid: "113492886"
---
# <a name="azure-digital-twins-query-language-reference-from-clause"></a>Azure Digital Twins 쿼리 언어 참조: FROM 절

이 문서에는 [Azure Digital Twins 쿼리 언어](concepts-query-language.md)의 **FROM 절** 에 대한 참조 정보가 포함되어 있습니다.

FROM 절은 쿼리의 두 번째 부분입니다. 쿼리가 적용되는 컬렉션과 모든 조인을 지정합니다.

모든 쿼리에 이 절이 필요합니다.

## <a name="select--from-digitaltwins"></a>SELECT ... FROM DIGITALTWINS

`FROM DIGITALTWINS`(대/소문자 구분 안 함)를 사용하여 인스턴스에서 Digital Twins의 전체 컬렉션을 참조합니다.

필요에 따라 문의 끝에 이름을 추가하여 Digital Twins의 컬렉션에 이름을 추가할 수 있습니다.

### <a name="syntax"></a>구문

기본:

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="FromDigitalTwinsSyntax":::

컬렉션의 이름을 지정하려면 다음을 수행합니다.

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="FromDigitalTwinsNamedSyntax":::

### <a name="examples"></a>예

다음은 기본 쿼리입니다. 다음 쿼리는 인스턴스에 모든 디지털 트윈을 반환합니다. 

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="FromDigitalTwinsExample":::

다음은 명명된 컬렉션을 사용하는 쿼리입니다. 다음 쿼리는 컬렉션에 `T` 이름을 할당하고 인스턴스에 모든 Digital Twins를 반환합니다.

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="FromDigitalTwinsNamedExample":::

## <a name="select--from-relationships"></a>SELECT ... FROM RELATIONSHIPS

`FROM RELATIONSHIPS`(대/소문자 구분 안 함)를 사용하여 인스턴스에서 전체 관계 컬렉션을 참조합니다.

필요에 따라 문의 끝에 이름을 추가하여 관계 컬렉션에 이름을 추가할 수 있습니다.

>[!NOTE]
> 이 기능은 `JOIN`과 함께 사용할 수 없습니다.

### <a name="syntax"></a>구문

기본:

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="FromRelationshipsSyntax":::

컬렉션의 이름을 지정하려면 다음을 수행합니다.

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="FromRelationshipsNamedSyntax":::

### <a name="examples"></a>예

다음은 인스턴스에 모든 관계를 반환하는 쿼리입니다. 

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="FromRelationshipsExample":::

다음은 `A`, `B`, `C` 또는 `D` twins에서 제공하는 모든 관계를 반환하는 쿼리입니다.

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="FromRelationshipsFilteredExample":::

## <a name="using-from-and-join-together"></a>FROM과 JOIN을 함께 사용

`FROM` 절을 `JOIN` 절과 함께 사용하여 Azure Digital Twins 그래프에서 엔터티 간 순회를 나타낼 수 있습니다.

`JOIN` 절에 대한 자세한 내용과 그래프 순회 쿼리를 작성하는 방법에 대한 자세한 내용은 [Azure Digital Twins 쿼리 언어 참조: JOIN 절](reference-query-clause-join.md)을 참조하세요.

## <a name="limitations"></a>제한 사항

`FROM`을 사용하는 쿼리에는 다음과 같은 제한이 적용됩니다.
* [하위 쿼리 없음](#no-subqueries)
* [FROM RELATIONSHIPS 또는 JOIN 선택](#choose-from-relationships-or-join)

자세한 내용은 다음 섹션을 참조하세요.

### <a name="no-subqueries"></a>하위 쿼리 없음

`FROM` 문 내에서는 하위 쿼리가 지원되지 않습니다.

#### <a name="example-negative"></a>예(음수)

다음 쿼리는 이 제한에 따라 수행할 수 **없는** 작업의 예를 보여 줍니다.

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="FromNegativeExample":::

### <a name="choose-from-relationships-or-join"></a>FROM RELATIONSHIPS 또는 JOIN 선택

`FROM RELATIONSHIPS` 기능은 `JOIN`과 함께 사용할 수 없습니다. 다음 옵션 중 선택하려는 정보에 가장 적합한 옵션을 선택해야 합니다.


