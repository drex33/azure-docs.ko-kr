---
title: Azure Digital Twins 쿼리 언어 참조-MATCH 절
titleSuffix: Azure Digital Twins
description: Azure Digital Twins 쿼리 언어 일치 절에 대 한 참조 설명서
author: baanders
ms.author: baanders
ms.date: 10/07/2021
ms.topic: article
ms.service: digital-twins
ms.openlocfilehash: 8c553d0df1f194c1de9f77e21341cb1022f98158
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130273771"
---
# <a name="azure-digital-twins-query-language-reference-match-clause"></a>Azure 디지털 Twins 쿼리 언어 참조: MATCH 절

이 문서에는 [Azure Digital Twins 쿼리 언어](concepts-query-language.md)에 대 한 **MATCH 절** 에 대 한 참조 정보가 포함 되어 있습니다.

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

### <a name="syntax"></a>Syntax

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

### <a name="syntax"></a>Syntax

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

### <a name="syntax"></a>Syntax

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

필요에 따라 절에서 관계 조건을 사용하여 트윈 간의 관계에 대한 홉 수를 지정할 수 `MATCH` 있습니다. 정확한 숫자 또는 범위를 지정할 수 있습니다. 이 선택적 값은 [관계 방향을 지정하기 위해 화살표 구문의](#specify-relationship-direction)일부로 포함됩니다.

홉 수를 제공하지 않으면 쿼리는 기본적으로 하나의 홉으로 설정됩니다.

>[!IMPORTANT]
>1보다 큰 홉 수를 지정하는 경우 관계 에 [쿼리 변수를 할당할](#assign-query-variable-to-relationship-and-specify-relationship-properties)수 없습니다. 이러한 조건 중 하나만 동일한 쿼리 내에서 사용할 수 있습니다. 

### <a name="syntax"></a>Syntax

>[!NOTE]
>이 섹션의 예제에서는 홉 수에 초점을 맞춥니다. 모두 이름을 지정하지 않고 비방향 관계를 표시합니다. 이러한 다른 조건에서 더 많은 작업을 수행하는 방법에 대한 지침은 [관계 방향 지정](#specify-relationship-direction) 및 [관계 이름 지정을 참조하세요.](#specify-relationship-name) 이러한 여러 작업을 동일한 쿼리에서 함께 사용하는 방법에 대한 자세한 내용은 [MATCH 작업 결합을 참조하세요.](#combine-match-operations)

대괄호() 내에서 절에서 트래버스할 홉 수를 `MATCH` `[]` 지정합니다.

**정확한 홉 수를** 지정하려면 다음 구문을 사용합니다. 사용자 값으로 대체해야 하는 자리 표시자 값은 `twin_1` , 및 입니다. `number_of_hops` `twin_2`

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="MatchHopsExactSyntax":::

홉 **범위를** 지정하려면 다음 구문을 사용합니다. 사용자 값으로 대체해야 하는 자리 표시자 값은 `twin_1` , 및 `starting_limit`  `ending_limit` `twin_2` 입니다. 시작 **제한은** 범위에 포함되지 않지만 끝 **제한은 포함됩니다.**

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="MatchHopsRangeSyntax":::

시작 제한을 벗어나서 끝 제한을 "모든 것"(및 포함)으로 나타낼 수도 있습니다. 끝 제한은 항상 제공되어야 합니다.

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="MatchHopsRangeEndingSyntax":::

(기본값) 기본적으로 **하나의 홉** 로 설정하려면 다음과 같이 대괄호를 홉 정보의 비워 둡니다.

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="MatchHopsOneSyntax":::

### <a name="examples"></a>예제

다음 예에서는 **정확한 홉 수를 지정합니다.** 쿼리는 정확히 3홉인 트윈 *층과* *방* 간의 관계만 반환합니다.

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="MatchHopsExactExample":::

다음 예제에서는 **홉의 범위를** 지정 합니다. 쿼리는 트윈 *층과* 1~3홉 사이의 *공간* 간의 관계를 반환합니다(홉 수가 2 또는 3임을 의미함).

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="MatchHopsRangeExample1":::

하나의 경계만 제공하여 범위를 표시할 수도 있습니다. 다음 예제에서 쿼리는 홉 수가 2개인 트윈 *층과* *방* 간의 관계를 반환합니다(홉 수가 1 또는 2임을 의미함).

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="MatchHopsRangeEndingExample":::

다음 예제에는 지정된 홉 수가 없으므로 는 기본적으로 트윈 *층과* *방* 사이에 **하나의 홉으로** 설정됩니다.

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="MatchHopsOneExample":::

## <a name="assign-query-variable-to-relationship-and-specify-relationship-properties"></a>관계에 쿼리 변수 할당(및 관계 속성 지정)

필요에 따라 쿼리 텍스트에서 이름으로 참조할 수 있도록 절에서 참조하는 관계에 `MATCH` 쿼리 변수를 할당할 수 있습니다.

이렇게 하면 절의 관계 속성을 필터링하는 기능이 `WHERE` 유용합니다.

>[!IMPORTANT]
> 관계에 쿼리 변수를 할당하는 것은 쿼리가 단일 홉을 지정하는 경우에만 지원됩니다. 쿼리 내에서 관계 변수를 지정하는 것과 [더 많은 홉 수를 지정하는](#specify-number-of-hops)중에서 선택해야 합니다.

### <a name="syntax"></a>Syntax

>[!NOTE]
>이 섹션의 예제에서는 관계에 대한 쿼리 변수에 초점을 맞춥니다. 모두 이름을 지정하지 않고 비방향 관계를 표시합니다. 이러한 다른 조건에서 더 많은 작업을 수행하는 방법에 대한 지침은 [관계 방향 지정](#specify-relationship-direction) 및 [관계 이름 지정을 참조하세요.](#specify-relationship-name) 이러한 여러 작업을 동일한 쿼리에서 함께 사용하는 방법에 대한 자세한 내용은 [MATCH 작업 결합을 참조하세요.](#combine-match-operations)

관계에 쿼리 변수를 할당하려면 대괄호()에 변수 이름을 `[]` 입력합니다. 아래에 표시된 자리 표시자 값은 , `twin_1` 및 입니다. `relationship_variable` `twin_2`

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="MatchVariableSyntax":::

### <a name="examples"></a>예제

다음 예제에서는 관계에 쿼리 변수 'r'을 할당합니다. 나중에 `WHERE` 절에서 변수를 사용하여 관계 *rel에* 값이 'child'인 name 속성이 있어야 임을 지정합니다.

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="MatchVariableExample":::

## <a name="combine-match-operations"></a>MATCH 작업 결합

동일한 쿼리에서 여러 관계 조건을 결합할 수 있습니다. 여러 관계 조건을 연결하여 양방향 관계 또는 다른 더 큰 조합을 표현할 수도 있습니다.

### <a name="syntax"></a>Syntax

단일 쿼리에서 [관계 방향, 관계](#specify-relationship-direction) [이름](#specify-number-of-hops)및 홉 수 또는 [쿼리 변수 할당](#assign-query-variable-to-relationship-and-specify-relationship-properties) **중 하나를** 결합할 수 [있습니다.](#specify-number-of-hops)

다음 구문 예제에서는 이러한 특성을 결합할 수 있는 방법을 보여줍니다. 조건의 해당 부분을 생략하기 위해 자리 표시자에 표시된 선택적 세부 정보를 생략할 수도 있습니다.

단일 쿼리 내에서 **관계 방향, 관계 이름 및 홉 수를** 지정하려면 관계 조건 내에서 다음 구문을 사용합니다. 값으로 대체해야 하는 자리 표시자 값은 `twin_1` 및 , 및 , 및 `twin_2` `optional_left_angle_bracket` `optional_right_angle_bracket` `relationship_name(s)` `number_of_hops` 입니다.

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="MatchCombinedHopsSyntax":::

단일 쿼리 내에서 **관계에 대한 관계 방향, 관계 이름 및 쿼리 변수를** 지정하려면 관계 조건 내에서 다음 구문을 사용합니다. 값으로 대체해야 하는 자리 표시자 값은 `twin_1` 및 , 및 , 및 `twin_2` `optional_left_angle_bracket` `optional_right_angle_bracket` `relationship_variable` `relationship_name(s)` 입니다.

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="MatchCombinedVariableSyntax":::

>[!NOTE]
>[관계 방향을 지정하는](#specify-relationship-direction)옵션에 따라 왼쪽에서 오른쪽 관계의 경우 왼쪽 대괄호 또는 오른쪽에서 왼쪽 관계의 오른쪽 각도 대괄호 중에서 선택해야 합니다. 동일한 화살표에 둘 다 포함할 수는 없지만 연결하여 양방향 관계를 나타낼 수 있습니다.

다음과 같이 여러 관계 조건을 함께 **연결하면** 됩니다. 값으로 대체해야 하는 자리 표시자 값은 , 및 `twin_1` 의 모든 `relationship_condition` 인스턴스입니다. `twin_2`

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="MatchChainSyntax":::

### <a name="examples"></a>예제

관계 **방향, 관계 이름 및 홉 수를** 결합하는 예제는 다음과 같습니다. 다음 쿼리는 *층과* *방* 간의 관계가 이러한 조건을 충족하는 트윈 *층과* *방* 을 찾습니다.
* 관계는 왼쪽에서 오른쪽으로, *바닥은* 원본으로, *방은* 대상으로 지정됩니다.
* 관계에 'contains' 또는 'isAssociatedWith' 이름이 있습니다.
* 관계에 4 또는 5개의 홉이 있습니다.

또한 쿼리는 트윈 *층의* 가 `$dtId` 'thermostat-15'임을 지정합니다.

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="MatchCombinedHopsExample":::

다음은 **관계 방향, 관계 이름 및 관계에 대한 명명된 쿼리 변수를 결합하는 예제입니다.** 다음 쿼리는 *층과* *방* 간의 관계가 쿼리 변수 *r에* 할당되고 이러한 조건을 충족하는 트윈 *층* 및 *방* 을 찾습니다.
* 관계는 왼쪽에서 오른쪽으로, *바닥은* 원본으로, *방은* 대상으로 지정됩니다.
* 관계에 'contains' 또는 'isAssociatedWith' 이름이 있습니다.
* 쿼리 변수 *r이* 지정된 관계에 길이 속성이 10인 경우

또한 쿼리는 트윈 *층의* 가 `$dtId` 'thermostat-15'임을 지정합니다.

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="MatchCombinedVariableExample":::

다음 예제에서는 **연결된** 관계 조건을 보여 줍니다. 쿼리는 트윈 *층,* *식당* 및 *방* 을 찾습니다.
* *층과* *방* 간의 관계는 다음 조건을 충족합니다.
    - 관계는 왼쪽에서 오른쪽으로, *바닥은* 원본으로, *는 대상으로는 café입니다.*
    - 관계에 'contains' 또는 'isAssociatedWith' 이름이 있습니다.
    - 쿼리 변수 *r이* 지정된 관계에 길이 속성이 10인 경우
* *식당과* *방* 간의 관계는 다음 조건을 충족합니다.
    - 관계는 오른쪽에서 *왼쪽이며, 방은* 원본으로, 는 *대상으로는 café입니다.*
    - 관계에 'has' 또는 'includes' 중 하나의 이름이 있습니다.
    - 관계에는 최대 3개(1, 2 또는 3) 홉이 있습니다.

또한 이 쿼리는 트윈 *층의* 온도가 `$dtId` 'thermostat-15'이고 트윈 *커피숍의* 온도가 55임을 지정합니다.

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="MatchCombinedChainExample":::

연결된 관계 조건을 사용하여 양방향 **관계를** 표현할 수도 있습니다. 다음 쿼리는 트윈 *층,* *방* 및 *건물* 를 찾습니다.
* *건물과* *층* 간의 관계는 다음 조건을 충족합니다.
    - 관계는 왼쪽에서 오른쪽으로, *빌드는* 원본으로, *바닥은* 대상으로 지정됩니다.
    - 관계의 이름은 'isAssociatedWith'입니다.
    - 관계에 쿼리 변수 *r1이* 지정됩니다.
* *층과* *방* 간의 관계는 다음 조건을 충족합니다.
    - 관계는 오른쪽에서 왼쪽으로, *방은* 원본으로, *바닥은* 대상으로 지정됩니다.
    - 관계의 이름은 'isAssociatedWith'입니다.
    - 관계에는 쿼리 변수 *r2*'가 지정됩니다.

또한 이 쿼리는 트윈 *건물의* 가 `$dtId` 'building-3'이고 *실내* 온도가 50보다 크다는 것을 지정합니다.

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="MatchCombinedChainBDExample":::

## <a name="limitations"></a>제한 사항

다음 제한은 를 사용하는 쿼리에 적용됩니다. `MATCH`
* 쿼리 `MATCH` 문당 하나의 식만 지원됩니다.
* `$dtId`절에 가 필요합니다. `WHERE`
* 관계에 쿼리 변수를 할당하는 것은 쿼리가 단일 홉을 지정하는 경우에만 지원됩니다.
* 쿼리에서 지원되는 최대 홉은 10입니다.