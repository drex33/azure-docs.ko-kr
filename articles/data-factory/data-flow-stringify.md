---
title: 데이터 흐름 매핑의 json.stringify 데이터 변환
description: Json.stringify 복합 데이터 형식
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.subservice: data-flows
ms.topic: conceptual
ms.date: 10/05/2021
ms.openlocfilehash: 6e9146767643adf22cc4b185d30acbe7c3018e8f
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/06/2021
ms.locfileid: "129622420"
---
# <a name="stringify-transformation-in-mapping-data-flow"></a>데이터 흐름 매핑의 json.stringify 변환

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Json.stringify 변환을 사용 하 여 복합 데이터 형식을 문자열로 변환 합니다. 이는 구조체, 맵 또는 배열 형식으로 시작 될 수 있는 단일 문자열 엔터티로 열 데이터를 저장 하거나 보내야 하는 경우에 매우 유용할 수 있습니다.

## <a name="configuration"></a>구성

Json.stringify 변환 구성 패널에서 먼저 인라인으로 구문 분석 하려는 열에 포함 된 데이터의 형식을 선택 합니다. 구문 분석 변환에는 다음과 같은 구성 설정도 포함됩니다.

:::image type="content" source="media/data-flow/stringify.png" alt-text="Json.stringify 설정":::

### <a name="column"></a>열

파생 열 및 집계와 마찬가지로 여기에서 종료되는 열을 드롭다운 선택기에서 선택하여 수정할 수 있습니다. 또는 여기에 새 열의 이름을 입력할 수 있습니다. ADF는이 열에 stringifies 원본 데이터를 저장 합니다. 대부분의 경우 들어오는 복합 필드 형식을 stringifies 하는 새 열을 정의 하는 것이 좋습니다.

### <a name="expression"></a>식

식 작성기를 사용 하 여 문자열 형식 할 원본 복합 필드를 설정 합니다. 이는 json.stringify 자체 포함 된 데이터를 사용 하 여 원본 열을 선택 하는 것 처럼 간단할 수도 있고, 복잡 한 식을 만들어 구문 분석할 수도 있습니다.

:::image type="content" source="media/data-flow/stringify-2.png" alt-text="Json.stringify 식":::

#### <a name="example-expression"></a>식 예제

이 예제에서 ```body.properties.periods``` 는 REST 소스에서 반환 되는 구조 내의 배열입니다.

```
body.properties.periods
```

## <a name="data-flow-script"></a>데이터 흐름 스크립트

```
stringify(mydata = body.properties.periods ? string,
    format: 'json') ~> Stringify1
```

## <a name="next-steps"></a>다음 단계

* [평면화된 변환](data-flow-flatten.md)을 사용하여 행을 열로 피벗합니다.
* [구문 분석 변환을](data-flow-parse.md) 사용 하 여 복잡 한 포함 형식을 개별 열로 변환할 수 있습니다.
* [파생 열 변환](data-flow-derived-column.md)을 사용하여 열을 행으로 피벗합니다.
