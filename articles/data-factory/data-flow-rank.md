---
title: 매핑 데이터 흐름의 순위 변환
description: 매핑 데이터 흐름 순위 변환을 사용하여 Azure Data Factory 또는 Synapse Analytics 파이프라인에서 순위 열을 생성하는 방법을 알아봅니다.
titleSuffix: Azure Data Factory & Azure Synapse
author: kromerm
ms.author: makromer
ms.reviewer: makromer
ms.service: data-factory
ms.subservice: data-flows
ms.topic: conceptual
ms.custom: synapse
ms.date: 09/09/2021
ms.openlocfilehash: c3af0355a02badb5aaa5d5b13a51b74392c931d8
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/26/2021
ms.locfileid: "129059884"
---
# <a name="rank-transformation-in-mapping-data-flow"></a>매핑 데이터 흐름의 순위 변환 

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

[!INCLUDE[data-flow-preamble](includes/data-flow-preamble.md)]

순위 변환을 사용하여 사용자가 지정한 정렬 조건에 따라 정렬된 순위를 생성합니다. 

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4GGJo]

## <a name="configuration"></a>구성

:::image type="content" source="media/data-flow/rank-configuration.png" alt-text="순위 설정":::

**대/소문자 구분 안 함:** 정렬 열이 문자열 형식인 경우 대/소문자가 순위 지정에 반영됩니다. 

**조밀성:** 사용하도록 설정된 경우 순위 열이 조밀성 순위를 가집니다. 각 순위 개수는 연속되는 숫자이며, 연결 후에는 순위 값을 건너뛰지 않습니다.

**순위 열:** 생성된 순위 열의 이름입니다. 이 열은 long 형식입니다.

**정렬 조건:** 정렬 기준이 되는 열 및 정렬 순서를 선택합니다. 순서는 정렬 우선 순위를 결정합니다.

위의 구성은 수신 농구 데이터를 사용하고 ‘pointsRanking’이라는 순위 열을 만듭니다. *PTS* 열의 값이 가장 높은 행의 *pointsRanking* 값은 1입니다.

## <a name="data-flow-script"></a>데이터 흐름 스크립트

### <a name="syntax"></a>구문

```
<incomingStream>
    rank(
        desc(<sortColumn1>),
        asc(<sortColumn2>),
        ...,
        caseInsensitive: { true | false }
        dense: { true | false }
        output(<rankColumn> as long)
    ) ~> <sortTransformationName<>
```

### <a name="example"></a>예제

:::image type="content" source="media/data-flow/rank-configuration.png" alt-text="순위 설정":::

위의 순위 구성에 대한 데이터 흐름 스크립트는 아래 코드 조각에 나와 있습니다.

```
PruneColumns
    rank(
        desc(PTS, true),
        caseInsensitive: false,
        output(pointsRanking as long),
        dense: false
    ) ~> RankByPoints
```

## <a name="next-steps"></a>다음 단계

[필터 변환](data-flow-filter.md)을 사용하여 순위 값을 기준으로 행을 필터링합니다.
