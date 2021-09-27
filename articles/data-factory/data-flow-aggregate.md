---
title: 매핑 데이터 흐름의 집계 변환
titleSuffix: Azure Data Factory & Azure Synapse
description: 매핑 데이터 흐름 집계 변환을 사용하여 Azure Data Factory 및 Synapse Analyatics에서 대규모로 데이터를 집계하는 방법을 알아봅니다.
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.subservice: data-flows
ms.topic: conceptual
ms.custom: synapse
ms.date: 09/09/2021
ms.openlocfilehash: 7edf2ededec8ff15f50ea25f274d0163bc8b0980
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/26/2021
ms.locfileid: "129060369"
---
# <a name="aggregate-transformation-in-mapping-data-flow"></a>매핑 데이터 흐름의 집계 변환

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

[!INCLUDE[data-flow-preamble](includes/data-flow-preamble.md)]

집계 변환에서는 데이터 스트림의 열 집계를 정의합니다. 식 작성기를 사용하여 SUM, MIN, MAX, COUNT 같은 다양한 유형의 집계를 기존 열 또는 계산 열로 그룹화하여 정의할 수 있습니다.

## <a name="group-by"></a>Group By

집계에서 group by 절로 사용할 새 계산 열을 만들거나 기존 열을 선택합니다. 기존 열을 사용하려면 드롭다운에서 선택합니다. 새 계산 열을 만들려면 절을 마우스로 가리키고 **계산 열** 을 클릭합니다. [데이터 흐름 식 작성기](concepts-data-flow-expression-builder.md)가 열립니다. 계산 열을 만든 후에는 **다음으로 이름 지정** 필드에 출력 열 이름을 입력합니다. group by 절을 더 추가하려는 경우 기존 절을 마우스로 가리키고 더하기 아이콘을 클릭합니다.

:::image type="content" source="media/data-flow/agg.png" alt-text="집계 변환 group by 설정":::

집계 변환에서 group by 절은 선택 사항입니다.

## <a name="aggregate-columns"></a>집계 열

**집계** 탭으로 이동하여 집계 식을 작성합니다. 기존 열을 집계로 덮어쓰거나 새 필드를 새 이름으로 만들 수 있습니다. 열 이름 선택기 옆의 오른쪽 상자에 집계 식이 입력됩니다. 식을 편집하려면 텍스트 상자를 클릭하고 식 작성기를 엽니다. 집계 열을 더 추가하려면 열 목록 위의 **추가** 를 클릭하거나 기존 집계 열 옆에 있는 더하기 아이콘을 클릭합니다. **열 추가** 또는 **열 패턴 추가** 를 선택합니다. 각 집계 식은 집계 함수를 하나 이상 포함해야 합니다.

:::image type="content" source="media/data-flow/aggregate-columns.png" alt-text="집계 설정":::

> [!NOTE]
> 디버그 모드의 식 작성기에서는 집계 함수로 데이터 미리 보기를 생성할 수 없습니다. 집계 변환의 데이터 미리 보기를 보려면 식 작성기를 닫고 ‘데이터 미리 보기’ 탭을 통해 데이터를 확인합니다.

### <a name="column-patterns"></a>열 패턴

[열 패턴](concepts-data-flow-column-pattern.md)을 사용하여 열 집합에 동일한 집계를 적용할 수 있습니다. 이 기능은 기본적으로 삭제되는 입력 스키마의 많은 열을 유지하려는 경우에 유용합니다. `first()`와 같은 휴리스틱을 사용하여 집계를 통해 입력 열을 유지할 수 있습니다.

## <a name="reconnect-rows-and-columns"></a>행 및 열 다시 연결

집계 변환은 SQL 집계 select 쿼리와 유사합니다. group by 절이나 집계 함수에 포함되지 않은 열은 집계 변환의 출력으로 이동하지 않습니다. 집계된 출력에 다른 열을 포함하려는 경우 다음 방법 중 하나를 수행합니다.

* `last()` 또는 `first()`와 같은 집계 함수를 사용하여 추가 열을 포함합니다.
* [자체 조인 패턴](https://mssqldude.wordpress.com/2018/12/20/adf-data-flows-self-join/)을 사용하여 열을 출력 스트림에 다시 조인합니다.

## <a name="removing-duplicate-rows"></a>중복 행 제거

집계 변환은 일반적으로 원본 데이터에서 중복 항목을 제거하거나 식별하는 데 사용됩니다. 이 프로세스를 중복 제거라고 합니다. group by 키 집합을 기준으로 선택한 휴리스틱을 사용하여 유지할 중복 행을 확인합니다. 일반적인 휴리스틱은 `first()`, `last()`, `max()`, `min()`입니다. [열 패턴](concepts-data-flow-column-pattern.md)을 사용하여 group by 열을 제외한 모든 열에 규칙을 적용할 수 있습니다.

:::image type="content" source="media/data-flow/agg-dedupe.png" alt-text="중복 제거":::

위의 예제에서는 `ProductID` 및 `Name` 열이 그룹화에 사용됩니다. 두 열의 값이 같은 두 행은 중복 항목으로 간주됩니다. 이 집계 변환에서는 일치하는 첫 번째 행의 값만 유지되고 다른 모든 값은 삭제됩니다. 열 패턴 구문을 사용하여 이름이 `ProductID` 및 `Name`이 아닌 모든 열은 기존 열 이름에 매핑되고 일치하는 첫 번째 행의 값이 지정됩니다. 출력 스키마는 입력 스키마와 같습니다.

데이터 유효성 검사 시나리오에서는 `count()` 함수를 사용하여 중복 항목 수를 계산할 수 있습니다.

## <a name="data-flow-script"></a>데이터 흐름 스크립트

### <a name="syntax"></a>구문

```
<incomingStream>
    aggregate(
           groupBy(
                <groupByColumnName> = <groupByExpression1>,
                <groupByExpression2>
               ),
           <aggregateColumn1> = <aggregateExpression1>,
           <aggregateColumn2> = <aggregateExpression2>,
           each(
                match(matchExpression),
                <metadataColumn1> = <metadataExpression1>,
                <metadataColumn2> = <metadataExpression2>
               )
          ) ~> <aggregateTransformationName>
```

### <a name="example"></a>예제

아래 예제에서는 들어오는 스트림 `MoviesYear`를 사용하고 `year` 열을 기준으로 행을 그룹화합니다. 이 변환은 `Rating` 열의 평균으로 계산되는 집계 열 `avgrating`을 만듭니다. 집계 변환의 이름은 `AvgComedyRatingsByYear`입니다.

UI에서 이 변환은 아래 이미지와 같습니다.

:::image type="content" source="media/data-flow/agg-script1.png" alt-text="group by 예제":::

:::image type="content" source="media/data-flow/agg-script2.png" alt-text="집계 예제":::

이 변환의 데이터 흐름 스크립트는 아래 코드 조각에 나와 있습니다.

```
MoviesYear aggregate(
                groupBy(year),
                avgrating = avg(toInteger(Rating))
            ) ~> AvgComedyRatingByYear
```

:::image type="content" source="media/data-flow/aggdfs1.png" alt-text="집계 데이터 흐름 스크립트":::

```MoviesYear```: year 및 title 열을 정의하는 파생 열 ```AvgComedyRatingByYear```: 연도별로 그룹화된 코메디 평균 등급의 집계 변환 ```avgrating```: 집계된 값을 포함하기 위해 생성되는 새 열의 이름

```
MoviesYear aggregate(groupBy(year),
    avgrating = avg(toInteger(Rating))) ~> AvgComedyRatingByYear
```

## <a name="next-steps"></a>다음 단계

* [창 변환](data-flow-window.md)을 사용하여 창 기반 집계 정의
