---
title: 데이터 흐름 매핑의 필터 변환
titleSuffix: Azure Data Factory & Azure Synapse
description: Azure Data Factory 또는 Synapse Analytics 매핑 데이터 흐름에서 필터 변환을 사용하여 행을 필터링합니다.
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.subservice: data-flows
ms.topic: conceptual
ms.custom: synapse
ms.date: 09/09/2021
ms.openlocfilehash: c3cf00a5ea80278973a548a19e9ef0dc82ef877f
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/26/2021
ms.locfileid: "129060198"
---
# <a name="filter-transformation-in-mapping-data-flow"></a>데이터 흐름 매핑의 필터 변환

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

[!INCLUDE[data-flow-preamble](includes/data-flow-preamble.md)]

필터 변환은 조건에 따라 행 필터링을 허용합니다. 출력 스트림에는 필터링 조건과 일치하는 모든 행이 포함됩니다. 필터 변환은 SQL의 WHERE 절과 유사합니다.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4xnxN]

## <a name="configuration"></a>구성

데이터 흐름 식 작성기를 사용하여 필터 조건에 대한 식을 입력합니다. 식 작성기를 열려면 파란색 상자를 클릭합니다. 필터 조건은 부울 유형이어야 합니다. 식을 만드는 방법에 대한 자세한 내용은 [식 작성기](concepts-data-flow-expression-builder.md) 설명서를 참조하세요.

:::image type="content" source="media/data-flow/filter1.png" alt-text="필터 변환":::

## <a name="data-flow-script"></a>데이터 흐름 스크립트

### <a name="syntax"></a>구문

```
<incomingStream>
    filter(
        <conditionalExpression>
    ) ~> <filterTransformationName>
```

### <a name="example"></a>예제

아래 예제는 들어오는 스트림인 `CleanData`를 사용하는 `FilterBefore1960`이라는 필터 변환입니다. 필터 조건은 식 `year <= 1960`입니다.

UI에서 이 변환은 아래 이미지와 같습니다.

:::image type="content" source="media/data-flow/filter1.png" alt-text="필터 변환":::

이 변환에 대한 데이터 흐름 스크립트는 아래 코드 조각에 있습니다.

```
CleanData
    filter(
        year <= 1960
    ) ~> FilterBefore1960

```

## <a name="next-steps"></a>다음 단계

[선택 변환](data-flow-select.md)을 사용하여 열 필터링
