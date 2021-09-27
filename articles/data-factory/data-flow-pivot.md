---
title: 데이터 흐름 매핑의 피벗 변환
titleSuffix: Azure Data Factory & Azure Synapse
description: Azure Data Factory 및 Synapse Analytics 파이프라인에서 데이터 흐름 피벗 변환 매핑을 사용 하 여 행에서 열로 데이터를 피벗 합니다.
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.subservice: data-flows
ms.topic: conceptual
ms.custom: synapse
ms.date: 09/09/2021
ms.openlocfilehash: c6aa95864ad12d5776a8e90eb2b1bd8dac1f04a2
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/26/2021
ms.locfileid: "129059932"
---
# <a name="pivot-transformation-in-mapping-data-flow"></a>데이터 흐름 매핑의 피벗 변환

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

[!INCLUDE[data-flow-preamble](includes/data-flow-preamble.md)]

피벗 변환을 사용하여 단일 열의 고유한 행 값에서 여러 열을 만들 수 있습니다. Pivot은 열별 그룹화를 선택하고 [집계 함수](data-flow-expression-functions.md#aggregate-functions)를 사용하여 피벗 열을 생성하는 집계 변환입니다.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4C4YN]

## <a name="configuration"></a>구성

피벗 변환에는 열별 그룹화, 피벗 키, 피벗된 열 생성 방법의 세 가지 입력이 필요합니다.

### <a name="group-by"></a>Group By

:::image type="content" source="media/data-flow/pivot2.png" alt-text="옵션으로 그룹화":::

피벗된 열을 집계할 열을 선택합니다. 출력 데이터는 값으로 동일하게 그룹화된 모든 행을 단일 행으로 그룹화합니다. 피벗 열에서 수행되는 집계는 각 그룹에 대해 발생합니다.

이 섹션은 선택 사항입니다. 열별 그룹화를 선택하지 않으면 전체 데이터 스트림이 집계되고 한 행만 출력됩니다.

### <a name="pivot-key"></a>피벗 키

:::image type="content" source="media/data-flow/pivot3.png" alt-text="피벗 키":::

피벗 키는 행 값이 새 열로 피벗되는 열입니다. 기본적으로 피벗 변환은 고유한 각 행 값에 대해 새 열을 만듭니다.

레이블이 지정된 **값** 섹션에서 피벗될 특정 행 값을 입력할 수 있습니다. 이 섹션에 입력된 행 값만 피벗됩니다. **Null 값** 을 사용하도록 설정하면 열에 null 값에 대한 피벗 열이 생성됩니다.

### <a name="pivoted-columns"></a>피벗 열

:::image type="content" source="media/data-flow/pivot4.png" alt-text="피벗 열":::

열이 되는 각각의 고유 피벗 키 값의 경우 각 그룹에 대해 집계된 행 값을 생성합니다. 피벗 키 당 여러 열을 만들 수 있습니다. 각 피벗 열은 [집계 함수](data-flow-expression-functions.md#aggregate-functions)를 하나 이상 포함해야 합니다.

**열 이름 패턴:** 각 피벗 열의 열 이름의 서식을 지정하는 방법을 선택합니다. 출력 열 이름은 피벗 키 값, 열 접두사 및 선택적 접두사, 문자 수, 가운데 문자를 조합한 것입니다. 

**열 정렬:** 피벗 키마다 피벗 열을 두 개 이상 생성하는 경우 열을 정렬할 방법을 선택합니다. 

**열 접두사:** 피벗 키마다 피벗 열을 두 개 이상 생성하는 경우 각 열에 대해 열 접두사를 입력합니다. 피벗 열이 하나만 있는 경우 이 설정은 선택 사항입니다.

## <a name="help-graphic"></a>도움말 그래픽

아래 도움말 그래픽에서는 다른 피벗 구성 요소가 상호 작용하는 방법을 보여 줍니다.

:::image type="content" source="media/data-flow/pivot5.png" alt-text="피벗 도움말 그래픽":::

## <a name="pivot-metadata"></a>피벗 메타데이터

피벗 키 구성에 값이 지정되지 않은 경우 피벗된 열은 런타임에 동적으로 생성됩니다. 피벗된 열 수는 피벗 열 수를 곱한 고유 피벗 키 값의 수와 같습니다. 이 값은 변하는 수일 수 있으므로 UX는 **검사** 탭에 열 메타데이터를 표시하지 않으며 열 전파가 발생하지 않습니다. 이러한 열을 변환하려면 매핑 데이터 흐름의 [열 패턴](concepts-data-flow-column-pattern.md) 기능을 사용합니다. 

특정 피벗 키 값이 설정된 경우 피벗된 열이 메타데이터에 표시됩니다. 검사 및 싱크 매핑에서 열 이름을 사용할 수 있습니다.

### <a name="generate-metadata-from-drifted-columns"></a>드리프트 열에서 메타데이터 생성

피벗은 행 값에 따라 새 열 이름을 동적으로 생성합니다. 데이터 흐름에서 나중에 참조할 수 있는 메타데이터에 이러한 새 열을 추가할 수 있습니다. 이렇게 하려면 데이터 미리 보기에서 [맵 드리프트](concepts-data-flow-schema-drift.md#map-drifted-columns-quick-action) 빠른 작업을 사용합니다. 

:::image type="content" source="media/data-flow/newpivot1.png" alt-text="열 피벗":::

### <a name="sinking-pivoted-columns"></a>피벗된 열 싱크

피벗된 열은 동적이지만 여전히 대상 데이터 저장소에 쓸 수 있습니다. 싱크 설정에서 **스키마 드리프트 허용** 을 사용하도록 설정합니다. 이렇게 하면 메타데이터에 포함되지 않은 열을 작성할 수 있습니다. 열 메타데이터에는 새 동적 이름이 표시되지 않지만 스키마 드리프트 옵션을 사용하면 데이터를 연결할 수 있습니다.

### <a name="rejoin-original-fields"></a>원본 필드를 다시 조인하는 방법

피벗 변환은 열별 그룹화 및 피벗된 열만 보호할 것입니다. 출력 데이터에 다른 입력 열을 포함하려면 [자체 조인](data-flow-join.md#self-join) 패턴을 사용합니다.

## <a name="data-flow-script"></a>데이터 흐름 스크립트

### <a name="syntax"></a>구문

```
<incomingStreamName>
    pivot(groupBy(Tm),
        pivotBy(<pivotKeyColumn, [<specifiedColumnName1>,...,<specifiedColumnNameN>]),
        <pivotColumnPrefix> = <pivotedColumnValue>,
        columnNaming: '< prefix >< $N | $V ><middle >< $N | $V >< suffix >',
        lateral: { 'true' | 'false'}
    ) ~> <pivotTransformationName
```
### <a name="example"></a>예제

구성 섹션에 표시된 화면에는 다음과 같은 데이터 흐름 스크립트가 있습니다.

```
BasketballPlayerStats pivot(groupBy(Tm),
    pivotBy(Pos),
    {} = count(),
    columnNaming: '$V$N count',
    lateral: true) ~> PivotExample

```

## <a name="next-steps"></a>다음 단계

[피벗 해제 변환](data-flow-unpivot.md)을 시도하여 열 값을 행 값으로 변환합니다. 
