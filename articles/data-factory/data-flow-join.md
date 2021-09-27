---
title: 데이터 흐름 매핑의 조인 변환
titleSuffix: Azure Data Factory & Azure Synapse
description: Azure Data Factory 또는 Synapse Analytics 매핑 데이터 흐름에서 조인 변환을 사용하여 두 데이터 원본의 데이터를 결합합니다.
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.subservice: data-flows
ms.topic: conceptual
ms.custom: synapse
ms.date: 09/09/2021
ms.openlocfilehash: 2a1efc21511fe665d4e54cf955244daf598d4f8b
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/26/2021
ms.locfileid: "129060179"
---
# <a name="join-transformation-in-mapping-data-flow"></a>데이터 흐름 매핑의 조인 변환

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

[!INCLUDE[data-flow-preamble](includes/data-flow-preamble.md)]

조인 변환을 사용하여 매핑 데이터 흐름에서 두 원본 또는 스트림의 데이터를 결합합니다. 출력 스트림은 조인 조건에 따라 일치하는 두 원본의 모든 열을 포함합니다. 

## <a name="join-types"></a>조인 유형

데이터 흐름 매핑은 현재 5개의 서로 다른 조인 형식을 지원합니다.

### <a name="inner-join"></a>내부 조인

내부 조인은 두 테이블에 일치하는 값을 가진 행만 출력합니다.

### <a name="left-outer"></a>왼쪽 우선 외부

왼쪽 우선 외부 조인은 왼쪽 스트림의 모든 행과 오른쪽 스트림의 일치 레코드를 반환합니다. 왼쪽 스트림의 행에 일치하는 항목이 없으면 오른쪽 스트림의 출력 열이 NULL로 설정됩니다. 출력은 내부 조인에서 반환되는 행과 왼쪽 스트림의 일치하지 않는 행을 더한 값입니다.

> [!NOTE]
> 데이터 흐름에서 사용하는 Spark 엔진은 조인 조건에서 가능한 카티전 곱으로 인해 때때로 실패할 수 있습니다. 이 경우 사용자 지정 크로스 조인으로 전환하고 조인 조건을 수동으로 입력할 수 있습니다. 이로 인해 실행 엔진이 관계 양쪽의 모든 행을 계산한 다음, 행을 필터링해야 하는 경우 데이터 흐름의 성능이 저하될 수 있습니다.

### <a name="right-outer"></a>오른쪽 우선 외부

오른쪽 우선 외부 조인은 오른쪽 스트림의 모든 행과 왼쪽 스트림의 일치 레코드를 반환합니다. 오른쪽 스트림의 행에 일치하는 항목이 없으면 왼쪽 스트림의 출력 열이 NULL로 설정됩니다. 출력은 내부 조인에서 반환되는 행과 오른쪽 스트림의 일치하지 않는 행을 더한 값입니다.

### <a name="full-outer"></a>완전 외부

완전 외부 조인은 일치하지 않는 열에 NULL 값을 사용하여 양쪽에서 모든 열 및 행을 출력합니다.

### <a name="custom-cross-join"></a>사용자 지정 크로스 조인

크로스 조인은 조건에 따라 두 스트림의 교차곱을 출력합니다. 같지 않은 조건을 사용하는 경우 사용자 지정 식을 크로스 조인 조건으로 지정합니다. 출력 스트림은 조인 조건을 충족하는 모든 행이 됩니다.

비동등 조인과 ```OR``` 조건에 이 조인 유형을 사용할 수 있습니다.

전체 카티전 곱을 명시적으로 생성하려면 조인 앞에 있는 두 개의 각 독립 스트림에서 파생 열 변환을 사용하여 일치시킬 가상 키를 만듭니다. 예를 들어 ```SyntheticKey```라는 각 스트림의 파생 열에 새 열을 만들고 ```1```과 같도록 설정합니다. 그런 다음, 사용자 지정 조인 식으로 ```a.SyntheticKey == b.SyntheticKey```를 사용합니다.

> [!NOTE]
> 사용자 지정 크로스 조인에서 왼쪽 및 오른쪽 관계의 양쪽에서 하나 이상의 열을 포함해야 합니다. 각 측의 열 대신 정적 값을 사용하여 크로스 조인을 실행하면 전체 데이터 세트가 전체 검색되어 데이터 흐름이 제대로 수행되지 않습니다.

## <a name="configuration"></a>구성

1. **오른쪽 스트림** 드롭다운에서 조인하는 데이터 스트림을 선택합니다.
1. **조인 유형** 선택
1. 조인 조건에 대해 일치시킬 키 열을 선택합니다. 기본적으로 데이터 흐름은 각 스트림의 한 열이 같은지 검색합니다. 계산된 값을 통해 비교하려면 열 드롭다운 위로 마우스를 이동하여 **계산 열** 을 선택합니다.

:::image type="content" source="media/data-flow/join.png" alt-text="조인 변환":::

### <a name="non-equi-joins"></a>비동등 조인

조인 조건에서 같지 않음(! =) 또는 보다 큼(>)과 같은 조건부 연산자를 사용하려면 두 열 사이의 연산자 드롭다운을 변경합니다. 비동등 조인에서는 **최적화** 탭에서 **고정** 브로드캐스팅을 사용하여 두 스트림 중 하나 이상을 브로드캐스트해야 합니다.

:::image type="content" source="media/data-flow/non-equi-join.png" alt-text="비동등 조인":::

## <a name="optimizing-join-performance"></a>조인 성능 최적화

SSIS와 같은 도구의 병합 조인과 달리, 조인 변환은 필수 병합 조인 작업이 아닙니다. 조인 키는 정렬할 필요가 없습니다. 조인 작업은 Spark의 최적 조인 작업(브로드캐스트 또는 맵 쪽 조인)을 기반으로 수행됩니다.

:::image type="content" source="media/data-flow/joinoptimize.png" alt-text="조인 변환 최적화":::

조인, 조회 및 있음 변환에서 하나 또는 두 데이터 스트림이 작업자 노드 메모리에 맞는 경우 **브로드캐스팅** 를 사용하도록 설정하여 성능을 최적화할 수 있습니다. 기본적으로 spark 엔진은 한쪽에서 브로드캐스트할지 여부를 자동으로 결정합니다. 브로드캐스트할 쪽을 수동으로 선택하려면 **고정** 을 선택합니다.

조인이 실행되는 동안 시간 제한 오류가 발생하는 경우에만 **해제** 옵션을 통해 브로드캐스팅을 사용하지 않도록 설정하는 것이 좋습니다.

## <a name="self-join"></a>셀프 조인

데이터 스트림을 셀프 조인하려면 선택 변환이 있는 기존 스트림에 별칭을 추가합니다. 변환 옆에 있는 더하기 아이콘을 클릭하고 **새 분기** 를 선택하여 새 분기를 만듭니다. 원본 스트림에 별칭을 추가하려면 선택 변환을 추가합니다. 조인 변환을 추가하고 원본 스트림을 **왼쪽 스트림** 으로 선택하고 선택 변환을 **오른쪽 스트림** 으로 선택합니다.

:::image type="content" source="media/data-flow/selfjoin.png" alt-text="셀프 조인":::

## <a name="testing-join-conditions"></a>조인 조건 테스트

디버그 모드에서 데이터 미리 보기를 사용하여 조인 변환을 테스트할 때 알려진 소규모 데이터 세트를 사용합니다. 대규모 데이터 세트에서 행을 샘플링하는 경우 테스트를 위해 읽을 행과 키를 예측할 수 없습니다. 따라서 결과가 명확하지 않으므로 조인 조건이 일치하는 항목을 반환하지 않을 수 있습니다.

## <a name="data-flow-script"></a>데이터 흐름 스크립트

### <a name="syntax"></a>구문

```
<leftStream>, <rightStream>
    join(
        <conditionalExpression>,
        joinType: { 'inner'> | 'outer' | 'left_outer' | 'right_outer' | 'cross' }
        broadcast: { 'auto' | 'left' | 'right' | 'both' | 'off' }
    ) ~> <joinTransformationName>
```

### <a name="inner-join-example"></a>내부 조인 예제

아래 예제는 왼쪽 스트림 `TripData` 및 오른쪽 스트림 `TripFare`를 사용하는 `JoinMatchedData`라는 조인 변환입니다.  조인 조건은 각 스트림의 `hack_license`, `medallion`, `vendor_id` 및 `pickup_datetime` 열이 일치하는 경우 true를 반환하는 식 `hack_license == { hack_license} && TripData@medallion == TripFare@medallion && vendor_id == { vendor_id} && pickup_datetime == { pickup_datetime}`입니다. `joinType`은 `'inner'`입니다. `broadcast`가 `'left'` 값을 가질 수 있도록 왼쪽 스트림에만 브로드캐스팅을 사용하도록 설정하고 있습니다.

UI에서 이 변환은 아래 이미지와 같습니다.

:::image type="content" source="media/data-flow/join-script1.png" alt-text="스크린샷에는 조인 설정 탭이 선택된 변환과 내부 조인 형식이 표시됩니다.":::

이 변환에 대한 데이터 흐름 스크립트는 아래 코드 조각에 있습니다.

```
TripData, TripFare
    join(
        hack_license == { hack_license}
        && TripData@medallion == TripFare@medallion
        && vendor_id == { vendor_id}
        && pickup_datetime == { pickup_datetime},
        joinType:'inner',
        broadcast: 'left'
    )~> JoinMatchedData
```

### <a name="custom-cross-join-example"></a>사용자 지정 크로스 조인 예제

아래 예제는 왼쪽 스트림 `LeftStream` 및 오른쪽 스트림 `RightStream`을 사용하는 `JoiningColumns`라는 조인 변환입니다. 이 변환은 열 `leftstreamcolumn`이 `rightstreamcolumn` 열보다 큰 모든 행을 결합하여 두 개의 스트림과 조인을 사용합니다. `joinType`은 `cross`입니다. 브로드캐스트를 사용할 수 없으며 `broadcast`는 `'none'` 값을 갖습니다.

UI에서 이 변환은 아래 이미지와 같습니다.

:::image type="content" source="media/data-flow/join-script2.png" alt-text="스크린샷에는 조인 설정 탭이 선택된 변환과 내부 조인 형식(크로스)이 표시됩니다.":::

이 변환에 대한 데이터 흐름 스크립트는 아래 코드 조각에 있습니다.

```
LeftStream, RightStream
    join(
        leftstreamcolumn > rightstreamcolumn,
        joinType:'cross',
        broadcast: 'none'
    )~> JoiningColumns
```

## <a name="next-steps"></a>다음 단계

데이터를 조인한 후 [파생 열](data-flow-derived-column.md)을 만들고 데이터를 대상 데이터 저장소로 [싱크](data-flow-sink.md)합니다.
