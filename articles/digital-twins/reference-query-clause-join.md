---
title: Azure Digital Twins 쿼리 언어 참조-조인 절
titleSuffix: Azure Digital Twins
description: Azure Digital Twins 쿼리 언어 JOIN 절에 대한 참조 설명서
author: baanders
ms.author: baanders
ms.date: 03/31/2021
ms.topic: article
ms.service: digital-twins
ms.openlocfilehash: ad9447966916098a5ca0834d593242e365b8a088
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130246759"
---
# <a name="azure-digital-twins-query-language-reference-join-clause"></a>Azure Digital Twins 쿼리 언어 참조: JOIN 절

이 문서에는 [Azure Digital Twins 쿼리 언어](concepts-query-language.md)의 **JOIN 절** 에 대한 참조 정보가 포함되어 있습니다.

`JOIN` 절은 Azure Digital Twins 그래프를 통과하기 위해 쿼리하려는 경우 [FROM 절](reference-query-clause-from.md)의 일부로 Azure Digital Twins 쿼리 언어에서 사용됩니다.

이 절은 쿼리하는 동안 선택 사항입니다.

## <a name="core-syntax-join--related"></a>핵심 구문: JOIN ... RELATED 
Azure Digital Twins의 관계는 독립 엔터티가 아니라 디지털 트윈의 일부이므로 `JOIN` 쿼리에서 `RELATED` 키워드를 사용하여 트윈 컬렉션에서 특정 유형의 관계 집합을 참조하세요(유형은 [DTDL 정의](concepts-models.md#basic-relationship-example)에서 관계의 **이름** 필드를 사용하여 지정됨). 쿼리 내에서 관계 집합에 컬렉션 이름을 할당할 수 있습니다.

그런 다음 쿼리는 `WHERE` 절을 사용하여 관계 쿼리를 지원하는 데 사용되는 특정 트윈을 지정해야 합니다. 이 작업은 원본 또는 대상 트윈의 `$dtId` 값을 필터링하여 수행됩니다.

### <a name="syntax"></a>구문

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="JoinSyntax":::

### <a name="example"></a>예제

다음 쿼리는 *포함* 관계를 통해 ID가 *ABC* 인 트윈과 관련된 모든 디지털 트윈을 선택합니다.

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="JoinExample":::

## <a name="multiple-joins"></a>여러 JOIN

단일 쿼리에서 최대 5개의 `JOIN`이 지원되므로 한 번에 여러 수준의 관계를 탐색할 수 있습니다.

### <a name="syntax"></a>구문

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="MultiJoinSyntax":::

### <a name="example"></a>예제

다음 쿼리는 LightPanels를 포함하는 Room을 기반으로 하며 각 LightPanel에는 여러 LightBulbs가 포함되어 있습니다. 이 쿼리는 룸 1과 2의 LightPanels에 포함된 모든 LightBulbs를 가져옵니다.

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="MultiJoinExample":::

## <a name="limitations"></a>제한 사항

`JOIN`을 사용하는 쿼리에는 다음과 같은 제한이 적용됩니다.
* [5개의 깊이 제한](#depth-limit-of-five)
* [OUTER JOIN 의미 체계 없음](#no-outer-join-semantics)
* [원본 트윈 필요](#twins-required)

자세한 내용은 다음 섹션을 참조하세요.

### <a name="depth-limit-of-five"></a>5개의 깊이 제한

그래프 순회 깊이는 쿼리당 5개의 `JOIN` 수준으로 제한됩니다.

#### <a name="example"></a>예제

다음 쿼리는 Azure Digital Twins 쿼리에서 가능한 최대 `JOIN` 절 수를 보여 줍니다. Buliding1의 모든 LightBulbs를 가져옵니다.

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="MaxJoinExample":::

### <a name="no-outer-join-semantics"></a>OUTER JOIN 의미 체계 없음

`OUTER JOIN` 의미 체계가 지원되지 않습니다. 즉, 관계의 순위가 0이면 전체 "row"가 출력 결과 집합에서 제거됩니다.

#### <a name="example"></a>예제

건물 순회를 설명하는 다음 쿼리를 고려하세요.

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="NoOuterJoinExample":::

Building1에 층이 없는 경우 이 쿼리는 빈 결과 집합을 반환합니다(Building에 대한 값과 Floor에 대한 `undefined`가 있는 행 하나를 반환하는 대신).

### <a name="twins-required"></a>트윈 필요

Azure Digital Twins의 관계는 독립 엔터티로 쿼리할 수 없습니다. 또한 관계의 원본 트윈에 대한 정보를 제공해야 합니다. 이 기능은 `RELATED` 키워드를 통해 Azure Digital Twins에서 기본 `JOIN` 사용의 일부로 포함됩니다. 

또한 `JOIN` 절이 있는 쿼리는 `WHERE` 절에서 트윈의 `$dtId` 속성으로 필터링하여 관계 쿼리를 지원하는 데 사용되는 트윈을 명확하게 해야 합니다.