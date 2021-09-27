---
title: 데이터 흐름 매핑의 있음 변환
titleSuffix: Azure Data Factory & Azure Synapse
description: Azure Data Factory 및 Synapse Analytics 매핑 데이터 흐름에서 exists 변환을 사용하여 기존 행을 확인합니다.
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.subservice: data-flows
ms.topic: conceptual
ms.custom: synapse
ms.date: 09/09/2021
ms.openlocfilehash: 747a873e64bd143d988173f236f27d7246f6deeb
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/26/2021
ms.locfileid: "129058731"
---
# <a name="exists-transformation-in-mapping-data-flow"></a>데이터 흐름 매핑의 있음 변환

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

[!INCLUDE[data-flow-preamble](includes/data-flow-preamble.md)]

있음 변환은 데이터가 다른 원본 또는 스트림에 있는지 확인하는 행 필터링 변환입니다. 출력 스트림에는 오른쪽 스트림에 존재하거나 존재하지 않는 왼쪽 스트림의 모든 행이 포함됩니다. 있음 변환은 ```SQL WHERE EXISTS``` 및 ```SQL WHERE NOT EXISTS```과 유사합니다.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4vZKz]

## <a name="configuration"></a>구성

1. **오른쪽 스트림** 드롭다운에서 있는지 확인할 데이터 스트림을 선택합니다.
1. **있음 형식** 에 존재하거나 존재하지 않는 데이터를 찾고 있는지 여부를 지정합니다.
1. **사용자 지정 식** 을 사용할지 여부를 선택합니다.
1. 있음 조건으로 비교할 키 열을 선택합니다. 기본적으로 데이터 흐름은 각 스트림의 한 열이 같은지 검색합니다. 계산된 값을 통해 비교하려면 열 드롭다운 위로 마우스를 이동하여 **계산 열** 을 선택합니다.

:::image type="content" source="media/data-flow/exists.png" alt-text="있음 설정":::

### <a name="multiple-exists-conditions"></a>여러 개의 있음 조건

각 스트림에서 여러 열을 비교하려면 기존 행 옆에 있는 더하기 아이콘을 클릭하여 새 있음 조건을 추가합니다. 각 추가 조건은 “and” 문으로 조인됩니다. 두 열 비교는 다음 식과 동일합니다.

`source1@column1 == source2@column1 && source1@column2 == source2@column2`

### <a name="custom-expression"></a>사용자 지정 식

“and” 및 “equals to” 이외의 연산자를 포함하는 자유 형식 식을 만들려면 **사용자 지정 식** 필드를 선택합니다. 파란색 상자를 클릭하여 데이터 흐름 식 작성기를 통해 사용자 지정 식을 입력합니다.

:::image type="content" source="media/data-flow/exists1.png" alt-text="있음 사용자 지정 설정":::

## <a name="broadcast-optimization"></a>브로드캐스트 최적화

:::image type="content" source="media/data-flow/broadcast.png" alt-text="브로드캐스트 조인":::

조인, 조회 및 있음 변환에서 하나 또는 두 데이터 스트림이 작업자 노드 메모리에 맞는 경우 **브로드캐스팅** 를 사용하도록 설정하여 성능을 최적화할 수 있습니다. 기본적으로 spark 엔진은 한쪽에서 브로드캐스트할지 여부를 자동으로 결정합니다. 브로드캐스트할 쪽을 수동으로 선택하려면 **고정** 을 선택합니다.

조인이 실행되는 동안 시간 제한 오류가 발생하는 경우에만 **해제** 옵션을 통해 브로드캐스팅을 사용하지 않도록 설정하는 것이 좋습니다.

## <a name="data-flow-script"></a>데이터 흐름 스크립트

### <a name="syntax"></a>구문

```
<leftStream>, <rightStream>
    exists(
        <conditionalExpression>,
        negate: { true | false },
        broadcast: { 'auto' | 'left' | 'right' | 'both' | 'off' }
    ) ~> <existsTransformationName>
```

### <a name="example"></a>예제

아래 예제는 왼쪽 스트림 `NameNorm2` 및 오른쪽 스트림 `TypeConversions`를 사용하는 `checkForChanges`라는 있음 변환입니다.  있음 조건은 각 스트림의 `EMPID` 및 `Region` 열이 일치하는 경우 True를 반환하는 식 `NameNorm2@EmpID == TypeConversions@EmpID && NameNorm2@Region == DimEmployees@Region`입니다. 존재 여부를 확인하는 동안 `negate`는 False입니다. 최적화 탭에서 브로드캐스팅을 사용하도록 설정하지 않으므로 `broadcast` 값이 `'none'`입니다.

UI 경험에서 이 변환은 아래 이미지와 같습니다.

:::image type="content" source="media/data-flow/exists-script.png" alt-text="있음 예제":::

이 변환에 대한 데이터 흐름 스크립트는 아래 코드 조각에 있습니다.

```
NameNorm2, TypeConversions
    exists(
        NameNorm2@EmpID == TypeConversions@EmpID && NameNorm2@Region == DimEmployees@Region,
        negate:false,
        broadcast: 'auto'
    ) ~> checkForChanges
```

## <a name="next-steps"></a>다음 단계

유사 변환으로는 [조회](data-flow-lookup.md) 및 [조인](data-flow-join.md)이 있습니다.
