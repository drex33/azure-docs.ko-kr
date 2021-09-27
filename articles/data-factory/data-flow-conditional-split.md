---
title: 매핑 데이터 흐름의 조건부 분할 변환
titleSuffix: Azure Data Factory & Azure Synapse
description: Azure Data Factory 또는 Synapse Analytics의 매핑 데이터 흐름에서 조건부 분할 변환을 사용 하 여 데이터를 여러 스트림으로 분할
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.subservice: data-flows
ms.topic: conceptual
ms.custom: synapse
ms.date: 09/09/2021
ms.openlocfilehash: 15864e8dfb694478f8156d5122608dec88493285
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/26/2021
ms.locfileid: "129060293"
---
# <a name="conditional-split-transformation-in-mapping-data-flow"></a>매핑 데이터 흐름의 조건부 분할 변환

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

[!INCLUDE[data-flow-preamble](includes/data-flow-preamble.md)]

조건부 분할 변환은 일치 조건에 따라 데이터 행을 서로 다른 스트림으로 라우팅합니다. 조건부 분할 변환은 프로그래밍 언어의 CASE 결정 구조체와 비슷합니다. 변환은 식을 평가하고, 결과에 따라 데이터 행을 지정된 스트림으로 보냅니다.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4wKCX]

## <a name="configuration"></a>구성

**분할** 설정은 데이터의 행이 첫 번째 일치하는 스트림으로 흐를지 또는 일치하는 모든 스트림으로 흐를지를 결정합니다.

데이터 흐름 식 작성기를 사용하여 분할 조건에 대한 식을 입력합니다. 새 조건을 추가하려면 기존 행에서 더하기 아이콘을 클릭합니다. 어떤 조건에도 일치하지 않는 행에도 기본 스트림을 추가할 수 있습니다.

:::image type="content" source="media/data-flow/conditionalsplit1.png" alt-text="조건부 분할":::

## <a name="data-flow-script"></a>데이터 흐름 스크립트

### <a name="syntax"></a>구문

```
<incomingStream>
    split(
        <conditionalExpression1>
        <conditionalExpression2>
        ...
        disjoint: {true | false}
    ) ~> <splitTx>@(stream1, stream2, ..., <defaultStream>)
```

### <a name="example"></a>예제

아래 예제는 들어오는 스트림 `CleanData`를 사용하는 `SplitByYear`라는 조건부 분할 변환입니다. 이 변환에는 두 개의 분할 조건 `year < 1960` 및 `year > 1980`이 있습니다. `disjoint`는 데이터가 첫 번째 일치 조건으로 이동하기 때문에 false입니다. 첫 번째 조건과 일치하는 모든 행이 출력 스트림 `moviesBefore1960`으로 이동합니다. 두 번째 조건과 일치하는 나머지 행은 모두 출력 스트림 `moviesAFter1980`으로 이동합니다. 다른 모든 행은 기본 스트림 `AllOtherMovies`를 통해 흐릅니다.

서비스 UI에서이 변환은 아래 이미지와 같습니다.

:::image type="content" source="media/data-flow/conditionalsplit1.png" alt-text="조건부 분할":::

이 변환에 대한 데이터 흐름 스크립트는 아래 코드 조각에 있습니다.

```
CleanData
    split(
        year < 1960,
        year > 1980,
        disjoint: false
    ) ~> SplitByYear@(moviesBefore1960, moviesAfter1980, AllOtherMovies)
```

## <a name="next-steps"></a>다음 단계

조건부 분할에 사용되는 일반적인 데이터 흐름 변환은 [조인 변환](data-flow-join.md), [조회 변환](data-flow-lookup.md) 및 [선택 변환](data-flow-select.md)입니다.
