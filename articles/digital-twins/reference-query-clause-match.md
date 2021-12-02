---
title: Azure Digital Twins 쿼리 언어 참조-MATCH 절 (미리 보기)
titleSuffix: Azure Digital Twins
description: Azure Digital Twins 쿼리 언어 일치 절에 대 한 참조 설명서
author: baanders
ms.author: baanders
ms.date: 12/01/2021
ms.topic: article
ms.service: digital-twins
ms.openlocfilehash: 96ccfc2d2c5954564e6cea5604e4e79114799660
ms.sourcegitcommit: 93c7420c00141af83ed3294923b4826dd4dc6ff2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/02/2021
ms.locfileid: "133439936"
---
# <a name="azure-digital-twins-query-language-reference-match-clause-preview"></a>Azure 디지털 Twins 쿼리 언어 참조: MATCH 절 (미리 보기)

이 문서에는 [Azure Digital Twins 쿼리 언어](concepts-query-language.md)에 대 한 **MATCH 절** 에 대 한 참조 정보가 포함 되어 있습니다. 이 절은 현재 미리 보기 상태입니다.

`MATCH`절은 Azure Digital Twins 쿼리 언어에서 [from 절의](reference-query-clause-from.md)일부로 사용 됩니다. `MATCH` Azure Digital Twins 그래프에서 관계를 트래버스하는 동안 따라야 하는 패턴을 지정할 수 있습니다 ("변수 홉" 쿼리 패턴이 라고도 함).

이 절은 쿼리하는 동안 선택 사항입니다.

## <a name="core-syntax-match"></a>핵심 구문: MATCH

`MATCH` 특정 관계 조건에 따라 예측할 수 없는 홉 수를 포함 하는 쌍 간 경로를 찾는 쿼리를 지원 합니다. 

**관계 조건** 에는 다음 세부 정보 중 하나 이상이 포함 될 수 있습니다.
* [관계 방향](#specify-relationship-direction) (왼쪽에서 오른쪽, 오른쪽에서 왼쪽 또는 비 방향)
* [관계 이름](#specify-relationship-name) (단일 이름 또는 가능성 목록)
* 한 쌍에서 다른 쌍으로 [의 "홉" 수](#specify-number-of-hops) (정확한 숫자 또는 범위)
* 쿼리 텍스트 내에서 관계를 나타내는 [쿼리 변수 할당](#assign-query-variable-to-relationship-and-specify-relationship-properties) 입니다. 이를 통해 관계 속성을 필터링 할 수도 있습니다.

절이 있는 쿼리는 `MATCH`   또한 [where 절](reference-query-clause-where.md) 을 사용 하 여 참조 하는 쌍 `$dtId` 중 하나 이상에 대해를 지정 해야 합니다.

>[!NOTE]
>`MATCH``JOIN`쿼리 저장소에서 수행할 수 있는 모든 쿼리의 상위 집합입니다.

### <a name="syntax"></a>구문

기본 구문은 다음과 같습니다 `MATCH` . 

값으로 바꾸어야 하는 절에 표시 되는 자리 표시자 값은 `MATCH` `twin_1` , `relationship_condition` 및 `twin_2` 입니다. `WHERE`값으로 바꾸어야 하는 절의 자리 표시자 값은 `twin_or_twin_collection` 및 `twin_ID` 입니다.

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="MatchSyntax":::

쌍 이름이 해당 지점에서 작동 하도록 허용 하기 위해 쌍의 이름을 그대로 둘 수 있습니다.

관계 조건 수를 변경 하 여 연결 된 관계 [조건을 여러 개](#combine-match-operations) 포함 하거나 관계 조건을 전혀 갖지 않을 수도 있습니다.

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="MatchChainSyntax":::

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="MatchNodeSyntax":::

각 유형의 관계 조건 및 결합 하는 방법에 대 한 자세한 내용은이 문서의 다른 섹션을 참조 하십시오.

### <a name="example"></a>예제

다음은를 사용 하는 예제 쿼리입니다 `MATCH` .

이 쿼리는 [관계 방향을](#specify-relationship-direction)지정 하 고 빌드 및 센서 쌍을 검색 합니다.
* 센서는 Building21의를 사용 하 여 빌딩의 모든 관계를 대상으로 합니다. `$dtId` 
* 센서의 온도는 50 이상입니다.
빌드 및 센서는 모두 쿼리 결과에 포함 됩니다.

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="MatchExample":::

## <a name="specify-relationship-direction"></a>관계 방향 지정

절에서 관계 조건을 사용 하 여 쌍 `MATCH` 간 관계 방향을 지정 합니다. 가능한 방향에는 왼쪽에서 오른쪽, 오른쪽에서 왼쪽 또는 양방향이 포함 됩니다. 순환 관계는 자동으로 검색 되므로 관계는 한 번만 트래버스 됩니다.

> [!NOTE]
> [체인](#combine-match-operations)을 사용 하 여 양방향 관계를 나타낼 수 있습니다.

### <a name="syntax"></a>구문

>[!NOTE]
> 이 섹션의 예제에서는 관계 방향에 대해 집중적으로 설명 합니다. 관계 이름을 지정 하지 않으며 기본값은 단일 홉으로 지정 되 고 관계에 쿼리 변수를 할당 하지 않습니다. 이러한 다른 조건으로 더 많은 작업을 수행 하는 방법에 대 한 지침은 [관계 이름 지정](#specify-relationship-name), [홉 수 지정](#specify-number-of-hops)및 [관계에 쿼리 변수 할당](#assign-query-variable-to-relationship-and-specify-relationship-properties)을 참조 하세요. 동일한 쿼리에서 이러한 여러 가지를 함께 사용 하는 방법에 대 한 자세한 내용은 [결합 일치 작업](#combine-match-operations)을 참조 하세요.

방향 관계 설명 화살표의 시각적 표시를 사용 하 여 관계의 방향을 표시 합니다. 화살표는 `[]` 선택적 [관계 이름](#specify-number-of-hops)에 대괄호 ()를 포함 하는 공백 집합을 포함 합니다. 

이 섹션에서는 다양 한 관계 방향에 대 한 구문을 보여 줍니다. 값으로 바꾸어야 하는 자리 표시자 값은 `source_twin` 및 `target_twin` 입니다.

**왼쪽에서 오른쪽으로** 의 관계의 경우 다음 구문을 사용 합니다.

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="MatchDirectionLRSyntax":::

오른쪽에서 **왼쪽** 관계의 경우 다음 구문을 사용 합니다.

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="MatchDirectionRLSyntax":::

방향성이 **아닌** 관계의 경우 다음 구문을 사용 합니다. 이렇게 하면 관계에 대 한 방향이 지정 되지 않으므로 모든 방향에 대 한 관계가 결과에 포함 됩니다.

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="MatchDirectionNDSyntax":::

>[!TIP]
>방향성이 아닌 쿼리에는 추가 처리가 필요 하며,이로 인해 대기 시간 및 비용이 증가할 수 있습니다.

### <a name="examples"></a>예제

첫 번째 예제에서는 **왼쪽에서 오른쪽** 방향 순회를 보여 줍니다. 이 *쿼리는 쌍* 을 *찾은 다음*
* *대화방* 대상 *팩터리* (관계 이름 포함)
* *대화방* 에 50 보다 큰 온도 값이 있습니다.
* *팩터리에* `$dtId` ' ABC '가 있습니다.

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="MatchDirectionLRExample":::

>[!NOTE]
> 일치 순회에 대해 시작 쌍이 아닌 모든 쌍의 필터를 포함 하는 일치 쿼리에는 `$dtId` 빈 결과가 표시 될 수 있습니다. 이 `factory.$dtId` 는 위의 예제에서에 적용 됩니다. 자세한 내용은 [제한 사항](#limitations)을 참조하세요.

다음 예에서는 **오른쪽에서 왼쪽** 방향 트래버스를 보여 줍니다. 이 쿼리는 위의 쿼리와 비슷하지만 *실내* 와 *팩터리* 간의 관계에 대 한 방향이 반대입니다. 이 *쿼리는 쌍* 을 *찾은 다음*
* *팩터리* 대상 *공간* (관계 이름 포함)
* *팩터리에* `$dtId` ' ABC '가 있습니다.
* *대화방* 에 50 보다 큰 온도 값이 있습니다.

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="MatchDirectionRLExample":::

다음 예제에서는 **비 방향** 트래버스를 보여 줍니다. 이 *쿼리는 쌍* 을 *찾은 다음*
* *방* 및 *팩터리* 는 어느 방향으로 나 관계 없이 모든 관계 이름을 공유 합니다.
* *팩터리에* `$dtId` ' ABC '가 있습니다.
* *대화방* 에 70 보다 큰 습도 값이 있습니다.

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="MatchDirectionNDExample":::

## <a name="specify-relationship-name"></a>관계 이름 지정

필요에 따라 절에서 관계 조건을 사용 하 여 쌍 `MATCH` 간 관계의 이름을 지정할 수 있습니다. 단일 이름 또는 가능한 이름 목록을 지정할 수 있습니다. 선택적 관계 이름이 [관계 방향을 지정 하는 화살표 구문의](#specify-relationship-direction)일부로 포함 됩니다.

관계 이름을 제공 하지 않으면 기본적으로 쿼리에 모든 관계 이름이 포함 됩니다.

>[!TIP]
>쿼리에서 관계 이름을 지정 하면 성능을 향상 시키고 결과를 보다 예측 가능 하 게 만들 수 있습니다.

### <a name="syntax"></a>구문

>[!NOTE]
> 이 단원의 예제에서는 관계 이름에 대해 집중적으로 설명 합니다. 이러한 관계는 모두 방향성이 아닌 관계를 표시 하 고, 기본값은 단일 홉으로, 쿼리 변수를 관계에 할당 하지 않습니다. 이러한 다른 조건을 사용 하 여 더 많은 작업을 수행 하는 방법에 대 한 지침은 [관계 방향 지정](#specify-relationship-direction), [홉 수 지정](#specify-number-of-hops)및 [관계에 쿼리 변수 할당](#assign-query-variable-to-relationship-and-specify-relationship-properties)을 참조 하세요. 동일한 쿼리에서 이러한 여러 가지를 함께 사용 하는 방법에 대 한 자세한 내용은 [결합 일치 작업](#combine-match-operations)을 참조 하세요.

대괄호 () 내의 절에서 트래버스할 관계의 이름을 지정 `MATCH` `[]` 합니다. 이 섹션에서는 명명 된 관계를 지정 하는 구문을 보여 줍니다.

**단일 이름** 에 대해 다음 구문을 사용 합니다. 값으로 바꾸어야 하는 자리 표시자 값은 `twin_1` , `relationship_name` 및 `twin_2` 입니다.

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="MatchNameSingleSyntax":::

**가능한 여러 이름** 에 대해 다음 구문을 사용 합니다. 값으로 바꾸어야 하는 자리 표시자 값은,, `twin_1` , 및 메모를 사용 하 여 `relationship_name_option_1` 입력 하려는 `relationship_name_option_2` `twin_2` 관계 이름 수에 필요한 만큼 패턴을 계속 합니다.

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="MatchNameMultiSyntax":::

기본 이름을 **지정 되지 않은** 상태로 유지 하려면 다음과 같이 이름 정보를 비워 둡니다.

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="MatchNameAllSyntax":::

### <a name="examples"></a>예제

다음 예에서는 **단일 관계 이름을** 보여 줍니다. 이 쿼리는 wins *빌드* 및 *센서* 위치를 찾습니다.
* *빌드* 에 *센서* 에 대 한 ' 포함 ' 관계가 있습니다 (두 방향으로 이동).
* *빌드* 에는 `$dtId` ' Seattle21 '가 있습니다.

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="MatchNameSingleExample":::

다음 예에서는 **가능한 여러 관계 이름을** 보여 줍니다. 이 쿼리는 위의 쿼리와 비슷하지만 결과에 포함 되는 여러 개의 가능한 관계 이름이 있습니다. 이 쿼리는 wins *빌드* 및 *센서* 위치를 찾습니다.
* *빌드* 에 *센서* 에 대 한 ' Contains ' 또는 ' isAssociatedWith ' 관계가 있습니다 (양쪽 방향으로 이동).
* *빌드* 에는 `$dtId` ' Seattle21 '가 있습니다.

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="MatchNameMultiExample":::

다음 예에는 **지정 된 관계 이름이 없습니다**. 따라서 임의의 이름을 가진 관계가 쿼리 결과에 포함 됩니다. 이 쿼리는 wins *빌드* 및 *센서* 위치를 찾습니다.
* *빌드* 에는 임의의 이름을 가진 *센서* 에 대 한 관계가 있습니다 (한 방향으로 이동).
* *빌드* 에는 `$dtId` ' Seattle21 '가 있습니다.

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="MatchNameAllExample":::

## <a name="specify-number-of-hops"></a>홉 수 지정

필요에 따라 절에서 관계 조건을 사용 하 여 쌍 `MATCH` 간의 관계에 대 한 홉 수를 지정할 수 있습니다. 정확한 숫자나 범위를 지정할 수 있습니다. 이 선택적 값은 [관계 방향을 지정 하기 위해 화살표 구문의](#specify-relationship-direction)일부로 포함 됩니다.

홉 수를 제공 하지 않으면 쿼리는 기본적으로 하나의 홉이 됩니다.

>[!IMPORTANT]
>1 보다 큰 홉 수를 지정 하면 [관계에 쿼리 변수를 할당할](#assign-query-variable-to-relationship-and-specify-relationship-properties)수 없습니다. 이러한 조건 중 하나만 동일한 쿼리 내에서 사용할 수 있습니다. 

### <a name="syntax"></a>구문

>[!NOTE]
>이 섹션의 예제는 홉 수에 초점을 둡니다. 이름 지정 없이 모든 사용자에 게 방향성이 아닌 관계를 표시 합니다. 이러한 다른 조건을 사용 하 여 더 많은 작업을 수행 하는 방법에 대 한 지침은 [관계 방향 지정](#specify-relationship-direction) 및 [관계 이름 지정](#specify-relationship-name)을 참조 하세요. 동일한 쿼리에서 이러한 여러 가지를 함께 사용 하는 방법에 대 한 자세한 내용은 [결합 일치 작업](#combine-match-operations)을 참조 하세요.

대괄호 () 내의 절에서 트래버스할 홉 수를 지정 `MATCH` `[]` 합니다.

**정확한 홉 수** 를 지정 하려면 다음 구문을 사용 합니다. 값으로 바꾸어야 하는 자리 표시자 값은 `twin_1` , `number_of_hops` 및 `twin_2` 입니다.

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="MatchHopsExactSyntax":::

**홉 범위** 를 지정 하려면 다음 구문을 사용 합니다. 값으로 바꾸어야 하는 자리 표시자 값은 `twin_1` , `starting_limit`  `ending_limit` 및 `twin_2` 입니다. 시작 한도는 범위에 포함 **되지** 않지만 끝 제한은 포함 **됩니다** .

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="MatchHopsRangeSyntax":::

시작 한도를 생략 하 여 종료 한도를 "모든 항목" (및 포함)으로 나타낼 수도 있습니다. 끝 제한은 항상 제공 되어야 합니다.

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="MatchHopsRangeEndingSyntax":::

기본 기본 홉을 **1 홉** 으로 지정 하려면 다음과 같이 대괄호 정보를 비워 둡니다.

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="MatchHopsOneSyntax":::

### <a name="examples"></a>예제

다음 예에서는 **정확한 홉 수** 를 지정 합니다. 이 쿼리는 정확히 3 개의 홉 인 쌍 *층* 과 *대화방* 간의 관계만 반환 합니다.

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="MatchHopsExactExample":::

다음 예에서는 **홉의 범위** 를 지정 합니다. 이 쿼리는 1 개에서 3 개의 홉 사이에 있는 쌍과 *실내* 사이의 관계를 반환 합니다. *즉, 홉* 수는 2 또는 3입니다.

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="MatchHopsRangeExample1":::

하나의 경계를 제공 하 여 범위를 표시할 수도 있습니다. 다음 예제에서 쿼리는 쌍이 2 개 (홉 수는 1 또는 2) 인 쌍 *층* 과 *대화방* 간의 관계를 반환 합니다.

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="MatchHopsRangeEndingExample":::

다음 예제에는 지정 된 수의 홉이 없으므로 기본 홉 *과* *대화방* 사이에 **1 홉** 이 지정 됩니다.

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="MatchHopsOneExample":::

## <a name="assign-query-variable-to-relationship-and-specify-relationship-properties"></a>관계에 쿼리 변수 할당 (및 관계 속성 지정)

필요에 따라 `MATCH` 쿼리 텍스트에서 이름으로 참조할 수 있도록 절에서 참조 되는 관계에 쿼리 변수를 할당할 수 있습니다.

이 작업을 수행 하는 데 유용한 결과는 절의 관계 속성을 필터링 하는 기능입니다 `WHERE` .

>[!IMPORTANT]
> 쿼리 변수를 관계에 할당 하는 것은 쿼리에서 단일 홉을 지정 하는 경우에만 지원 됩니다. 쿼리 내에서 관계 변수를 지정 하 고 [더 많은 홉 수를 지정](#specify-number-of-hops)하도록 선택 해야 합니다.

### <a name="syntax"></a>구문

>[!NOTE]
>이 섹션의 예에서는 관계에 대 한 쿼리 변수를 집중적으로 다룹니다. 이름 지정 없이 모든 사용자에 게 방향성이 아닌 관계를 표시 합니다. 이러한 다른 조건을 사용 하 여 더 많은 작업을 수행 하는 방법에 대 한 지침은 [관계 방향 지정](#specify-relationship-direction) 및 [관계 이름 지정](#specify-relationship-name)을 참조 하세요. 동일한 쿼리에서 이러한 여러 가지를 함께 사용 하는 방법에 대 한 자세한 내용은 [결합 일치 작업](#combine-match-operations)을 참조 하세요.

관계에 쿼리 변수를 할당 하려면 변수 이름을 대괄호 ()로 입력 `[]` 합니다. 아래에 표시 된 자리 표시자 값은 `twin_1` , `relationship_variable` 및 `twin_2` 입니다.

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="MatchVariableSyntax":::

### <a name="examples"></a>예제

다음 예에서는 쿼리 변수 ' r '을 관계에 할당 합니다. 이후에는 절에서 `WHERE` 변수를 사용 하 여 *rel* 관계에 값이 ' child ' 인 name 속성이 있어야 함을 지정 합니다.

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="MatchVariableExample":::

## <a name="combine-match-operations"></a>일치 작업 결합

동일한 쿼리에서 여러 관계 조건을 결합할 수 있습니다. 여러 관계 조건을 연결 하 여 양방향 관계 또는 다른 큰 조합을 표현할 수도 있습니다.

### <a name="syntax"></a>구문

단일 쿼리에서 [관계 방향](#specify-relationship-direction), [관계 이름](#specify-number-of-hops)및 [홉 수](#specify-number-of-hops) 또는 [쿼리 변수 할당](#assign-query-variable-to-relationship-and-specify-relationship-properties)중 **하나** 를 결합할 수 있습니다.

다음 구문 예제에서는 이러한 특성을 결합 하는 방법을 보여 줍니다. 또한 자리 표시자에 표시 되는 선택적 세부 정보 중 하나를 생략 하 여 조건 부분을 생략할 수 있습니다.

단일 쿼리 내에서 **관계 방향, 관계 이름 및 홉 수** 를 지정 하려면 관계 조건 내에서 다음 구문을 사용 합니다. 값으로 바꾸어야 하는 자리 표시자 값은 `twin_1` 및,, `twin_2` 및 `optional_left_angle_bracket` `optional_right_angle_bracket` `relationship_name(s)` `number_of_hops` 입니다.

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="MatchCombinedHopsSyntax":::

단일 쿼리 내의 관계 **에 대 한 관계 방향, 관계 이름 및 쿼리 변수** 를 지정 하려면 관계 조건 내에서 다음 구문을 사용 합니다. 값으로 바꾸어야 하는 자리 표시자 값은 `twin_1` 및,, `twin_2` 및 `optional_left_angle_bracket` `optional_right_angle_bracket` `relationship_variable` `relationship_name(s)` 입니다.

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="MatchCombinedVariableSyntax":::

>[!NOTE]
>[관계 방향을 지정 하](#specify-relationship-direction)는 옵션에 따라 왼쪽에서 오른쪽으로의 관계에 대해 왼쪽 꺾쇠 괄호를 선택 하거나 오른쪽에서 왼쪽 관계에 대 한 오른쪽 꺾쇠 괄호를 선택 해야 합니다. 같은 화살표에 둘 다 포함할 수는 없지만 연결 하 여 양방향 관계를 나타낼 수 있습니다.

이와 같이 **여러 관계** 조건을 함께 연결할 수 있습니다. 값으로 바꾸어야 하는 자리 표시자 값은, `twin_1` 의 모든 인스턴스 `relationship_condition` 및 `twin_2` 입니다.

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="MatchChainSyntax":::

### <a name="examples"></a>예제

**관계 방향, 관계 이름 및 홉 수** 를 결합 하는 예제는 다음과 같습니다. 다음 쿼리는 *층* *과 실내* 간의 관계가 다음 조건을 *충족 하는 쌍으로* 된 *쌍을 찾습니다* .
* 관계는 왼쪽에서 오른쪽 이며, *바닥* 은 *원본으로,는 대상으로 사용* 됩니다.
* 관계의 이름이 ' contains ' 또는 ' isAssociatedWith '입니다.
* 이 관계에는 4 개 또는 5 개의 홉이 있습니다.

또한이 쿼리는 쌍 *바닥* `$dtId` 의가 ' 자동 온도 조절기-15 ' 임을 지정 합니다.

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="MatchCombinedHopsExample":::

관계 **에 대 한 관계 방향, 관계 이름 및 명명 된 쿼리 변수** 를 결합 하는 예는 다음과 같습니다. 다음 쿼리는 m과 *대화방* *간의 관계가* 쿼리 변수 *r* 에 할당 되 고 다음 조건을 충족 하는 쌍으로 이루어진 *바닥* 및 *대화방* 을 찾습니다.
* 관계는 왼쪽에서 오른쪽 이며, *바닥* 은 *원본으로,는 대상으로 사용* 됩니다.
* 관계의 이름이 ' contains ' 또는 ' isAssociatedWith '입니다.
* 쿼리 변수 *r* 이 지정 된 관계의 길이 속성은 10과 같습니다.

또한이 쿼리는 쌍 *바닥* `$dtId` 의가 ' 자동 온도 조절기-15 ' 임을 지정 합니다.

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="MatchCombinedVariableExample":::

다음 **예에서는 연결 된 관계 조건을** 보여 줍니다. 이 쿼리는 쌍 *floor*, *카페* 및 *대화방* 을 찾습니다. 여기에서 ...
* *층* 과 *실내* 간의 관계는 다음 조건을 충족 합니다.
    - 관계는 왼쪽에서 오른쪽으로, *바닥* 은 원본으로, *카페* 는 대상으로 합니다.
    - 관계의 이름이 ' contains ' 또는 ' isAssociatedWith '입니다.
    - 쿼리 변수 *r* 이 지정 된 관계의 길이 속성은 10과 같습니다.
* *카페* 와 *대화방* 간의 관계는 다음 조건을 충족 합니다.
    - 관계가 원본으로 사용 되는 오른쪽에서 왼쪽 이며 *대상으로* *카페* 입니다.
    - 관계에 ' 있는 ' 또는 ' include '의 이름이 있습니다.
    - 관계에는 최대 3 개의 홉 (즉, 1, 2 또는 3)이 있습니다.

또한이 쿼리는 쌍 *바닥* `$dtId` 의가 ' 자동 온도 조절기-15 '이 고 쌍 *카페* 의 온도가 55 임을 지정 합니다.

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="MatchCombinedChainExample":::

연결 된 관계 조건을 사용 하 여 **양방향 관계** 를 표현할 수도 있습니다. 다음 쿼리는 *쌍으로* 구성 *된, 장소* 및 *건물* 을 찾습니다.
* *건물과* *층* 간의 관계는 다음 조건을 충족 합니다.
    - 관계는 원본 및 *층* 을 대상으로 하 여 왼쪽에서 오른쪽으로 *작성* 됩니다.
    - 관계의 이름이 ' isAssociatedWith '입니다.
    - 이 관계에는 *r1* 쿼리 변수가 지정 됩니다.
* *층* 과 *실내* 간의 관계는 다음 조건을 충족 합니다.
    - 관계는 원본 및 *층* 을 대상으로 하는 오른쪽에서 왼쪽으로의 *공간* 입니다.
    - 관계의 이름이 ' isAssociatedWith '입니다.
    - 관계는 쿼리 변수 *r2*'로 지정 됩니다.

또한이 쿼리는 쌍을 *빌드하* 는 `$dtId` 중에 ' 빌딩-3 '이 있고 *방* 에 50 보다 큰 온도가 있음을 지정 합니다.

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="MatchCombinedChainBDExample":::

## <a name="limitations"></a>제한 사항

를 사용 하는 쿼리에는 다음 제한이 적용 됩니다 `MATCH` .
* `MATCH`쿼리 문 마다 식이 하나만 지원 됩니다.
* `$dtId``WHERE`는 절에 필요합니다.
* 관계에 쿼리 변수를 할당하는 것은 쿼리가 단일 홉을 지정하는 경우에만 지원됩니다.
* 쿼리에서 지원되는 최대 홉은 10개입니다.
* MATCH 순회에 대한 시작 쌍 이외의 트윈에 대한 필터를 포함하는 MATCH 쿼리는 `$dtId` 빈 결과를 표시할 수 있습니다. 예를 들어 다음 쿼리에는 이 제한이 적용됩니다.

    ```sql
    SELECT A, B, C FROM DIGITALTWINS 
    MATCH A-[contains]->B-[is_part_of]->C 
    WHERE B.$dtId = 'Device01'
    ```

    시나리오에서 다른 트윈에서 를 사용해야 하는 경우 `$dtId` [JOIN 절을](reference-query-clause-join.md) 대신 사용하는 것이 좋습니다.
* 동일한 트윈을 여러 번 트래버스하는 MATCH 쿼리는 결과에서 이 트윈을 예기치 않게 제거할 수 있습니다.