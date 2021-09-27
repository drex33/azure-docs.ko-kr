---
title: 데이터 흐름 매핑의 정렬 변환
description: Azure Data Factory 및 Synapse Analytics 파이프라인의 매핑 데이터 정렬 변환에 대해 알아봅니다.
titleSuffix: Azure Data Factory & Azure Synapse
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.subservice: data-flows
ms.topic: conceptual
ms.custom: synapse
ms.date: 09/09/2021
ms.openlocfilehash: 8781836be58403dfef130f55fa698b281360e641
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/26/2021
ms.locfileid: "129059891"
---
# <a name="sort-transformation-in-mapping-data-flow"></a>데이터 흐름 매핑의 정렬 변환

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

[!INCLUDE[data-flow-preamble](includes/data-flow-preamble.md)]

정렬 변환을 사용하면 현재 데이터 스트림의 들어오는 행을 정렬할 수 있습니다. 개별 열을 선택하고 오름차순이나 내림차순으로 정렬할 수 있습니다.

> [!NOTE]
> 데이터 흐름 매핑은 여러 노드 및 파티션에 데이터를 분산하는 Spark 클러스터에서 실행됩니다. 후속 변환에서 데이터를 다시 분할하는 경우 데이터가 다시 섞여 정렬이 흐트러질 수 있습니다. 데이터 흐름에서 정렬 순서를 유지하는 가장 좋은 방법은 변환의 최적화 탭에서 단일 파티션을 설정하고 가능한 한 싱크에 가깝게 정렬 변환을 유지하는 것입니다.

## <a name="configuration"></a>구성

:::image type="content" source="media/data-flow/sort.png" alt-text="정렬 설정":::

**대/소문자 구분:** 문자열 또는 텍스트 필드를 정렬할 때 대/소문자를 구분할지 여부

**파티션 내에서만 정렬:** 데이터 흐름이 Spark에서 실행될 때 각 데이터 스트림은 파티션으로 나누어집니다. 해당 설정은 전체 데이터 스트림을 정렬하지 않고 들어오는 파티션 내에서만 데이터를 정렬합니다. 

**정렬 조건:** 정렬할 열과 해당 정렬을 진행할 순서를 선택합니다. 순서는 정렬 우선 순위를 결정합니다. 데이터 스트림의 시작 부분 또는 끝 부분에 Null을 표시할지 여부를 선택합니다.

### <a name="computed-columns"></a>계산 열

정렬을 적용하기 전에 열 값을 수정하거나 추출하려면 열 위로 마우스를 이동하고 “계산 열”을 선택합니다. 그러면 식 작성기가 열 값을 사용하는 대신 정렬 작업에 대한 식을 만듭니다.

## <a name="data-flow-script"></a>데이터 흐름 스크립트

### <a name="syntax"></a>구문

```
<incomingStream>
    sort(
        desc(<sortColumn1>, { true | false }),
        asc(<sortColumn2>, { true | false }),
        ...
    ) ~> <sortTransformationName<>
```

### <a name="example"></a>예제

:::image type="content" source="media/data-flow/sort.png" alt-text="정렬 설정":::

위의 정렬 구성에 대한 데이터 흐름 스크립트는 아래 코드 조각에 나와 있습니다.

```
BasketballStats sort(desc(PTS, true),
    asc(Age, true)) ~> Sort1
```

## <a name="next-steps"></a>다음 단계

정렬 후 [집계 변환](data-flow-aggregate.md)을 사용하는 것이 좋습니다.
