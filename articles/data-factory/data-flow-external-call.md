---
title: 매핑 데이터 흐름의 외부 호출 데이터 변환
description: 데이터 흐름 매핑을 위해 외부 사용자 지정 엔드포인트 호출
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.subservice: data-flows
ms.topic: conceptual
ms.date: 11/24/2021
ms.openlocfilehash: f5bb3192719a82362c6700f31492c55f1f40e511
ms.sourcegitcommit: 5b25f76d0fd0ffb6784a2afab808fa55b3eac07b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/03/2021
ms.locfileid: "133518116"
---
# <a name="external-call-transformation-in-mapping-data-flow"></a>매핑 데이터 흐름의 외부 호출 변환

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

[!INCLUDE[data-flow-preamble](includes/data-flow-preamble.md)]

외부 호출 변환을 사용하면 데이터 엔지니어가 사용자 지정 또는 타사 결과를 데이터 흐름 스트림에 추가하기 위해 외부 REST 엔드포인트를 행 단위로 호출할 수 있습니다.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RWPXGN]

## <a name="configuration"></a>구성

외부 호출 변환 구성 패널에서 먼저 연결하려는 외부 엔드포인트 유형을 선택한 다음 들어오는 열을 매핑하고 마지막으로 다운스트림 변환에서 사용할 출력 데이터 구조를 정의합니다.

:::image type="content" source="media/data-flow/external-call-001.png" alt-text="외부 호출":::

### <a name="settings"></a>설정

인라인 데이터 세트 형식 및 연결된 서비스를 선택합니다. 현재 REST만 지원됩니다. 그러나 SQL 저장 프로시저 및 기타 연결된 서비스 유형도 사용할 수 있게 됩니다. 설정 속성에 대한 설명은 [REST 원본 구성을](connector-rest.md) 참조하세요.

### <a name="mapping"></a>매핑

자동 매핑을 선택하여 모든 입력 열을 엔드포인트에 전달할 수 있습니다. 필요에 따라 수동으로 열을 설정하고 여기서 대상 엔드포인트로 전송되는 열의 이름을 바꿀 수 있습니다.

### <a name="output"></a>출력

다음은 다운스트림 데이터 변환에서 사용할 외부 호출의 출력에 대한 데이터 구조를 정의하는 위치입니다. ADF 데이터 흐름 구문을 사용하여 수동으로 데이터 구조를 정의하여 열 이름 및 데이터 형식을 정의하거나 "프로젝션 가져오기"를 클릭하고 ADF가 외부 호출에서 스키마 출력을 검색하도록 허용할 수 있습니다. 다음은 날씨 REST API GET 호출의 출력으로 예제 스키마 정의 구조입니다.

```
({@context} as string[],
        geometry as (coordinates as string[][][],
        type as string),
        properties as (elevation as (unitCode as string,
        value as string),
        forecastGenerator as string,
        generatedAt as string,
        periods as (detailedForecast as string, endTime as string, icon as string, isDaytime as string, name as string, number as string, shortForecast as string, startTime as string, temperature as string, temperatureTrend as string, temperatureUnit as string, windDirection as string, windSpeed as string)[],
        units as string,
        updateTime as string,
        updated as string,
        validTimes as string),
        type as string)
```

## <a name="examples"></a>예제

### <a name="samples-including-data-flow-script"></a>데이터 흐름 스크립트를 포함한 샘플

:::image type="content" source="media/data-flow/external-call-002.png" alt-text="외부 호출 샘플":::

```
source(output(
        id as string
    ),
    allowSchemaDrift: true,
    validateSchema: false,
    ignoreNoFilesFound: false) ~> source1
Filter1 call(mapColumn(
        id
    ),
    skipDuplicateMapInputs: false,
    skipDuplicateMapOutputs: false,
    output(
        headers as [string,string],
        body as (name as string)
    ),
    allowSchemaDrift: true,
    store: 'restservice',
    format: 'rest',
    timeout: 30,
    httpMethod: 'POST',
    entity: 'api/Todo/',
    requestFormat: ['type' -> 'json'],
    responseFormat: ['type' -> 'json', 'documentForm' -> 'documentPerLine']) ~> ExternalCall1
source1 filter(toInteger(id)==1) ~> Filter1
ExternalCall1 sink(allowSchemaDrift: true,
    validateSchema: false,
    skipDuplicateMapInputs: true,
    skipDuplicateMapOutputs: true,
    store: 'cache',
    format: 'inline',
    output: false,
    saveOrder: 1) ~> sink1
```

## <a name="data-flow-script"></a>데이터 흐름 스크립트

```
ExternalCall1 sink(allowSchemaDrift: true,
    validateSchema: false,
    skipDuplicateMapInputs: true,
    skipDuplicateMapOutputs: true,
    store: 'cache',
    format: 'inline',
    output: false,
    saveOrder: 1) ~> sink1
```    

## <a name="next-steps"></a>다음 단계

* [평면화된 변환](data-flow-flatten.md)을 사용하여 행을 열로 피벗합니다.
* 파생 [열 변환을](data-flow-derived-column.md) 사용하여 행을 변환합니다.
* REST 설정에 대한 자세한 내용은 REST [원본을](connector-rest.md) 참조하세요.
