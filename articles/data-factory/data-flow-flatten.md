---
title: 데이터 흐름 매핑의 평면화 변환
titleSuffix: Azure Data Factory & Azure Synapse
description: Azure Data Factory 및 Synapse Analytics 파이프라인에서 평면화 변환을 사용 하 여 계층적 데이터를 비 정규화 합니다.
author: kromerm
ms.author: makromer
ms.review: daperlov
ms.service: data-factory
ms.subservice: data-flows
ms.custom: synapse
ms.topic: conceptual
ms.date: 09/29/2021
ms.openlocfilehash: 4fb8d5ea1bfaa9534f7db27296d3cb7f61d0c4fb
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129274958"
---
# <a name="flatten-transformation-in-mapping-data-flow"></a>데이터 흐름 매핑의 평면화 변환

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

[!INCLUDE[data-flow-preamble](includes/data-flow-preamble.md)]

평면화 변환을 사용하여 JSON과 같은 계층 구조 내부의 배열 값을 가져와서 개별 행으로 언롤합니다. 해당 프로세스를 비정규화라고 합니다.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RWLX9j]

## <a name="configuration"></a>구성

평면화 변환에는 다음 구성 설정이 포함됩니다.

:::image type="content" source="media/data-flow/flatten1.png" alt-text="평면화 설정":::

### <a name="unroll-by"></a>언롤 기준

언롤할 배열을 선택합니다. 출력 데이터에는 각 배열의 항목당 하나의 행이 포함됩니다. 입력 행의 언롤 기준에 따른 언롤이 Null이거나 비어 있으면 언롤된 값이 Null인 출력 행 1개가 있게 됩니다.

### <a name="unroll-root"></a>언롤 루트

기본적으로 평면화 변환은 배열이 존재하는 계층 구조의 맨 위로 배열을 언롤합니다. 필요에 따라 배열을 언롤 루트로 선택할 수 있습니다. 언롤 루트는 언롤 기준 배열을 포함하는 복합 개체의 배열이어야 합니다. 언롤 루트를 선택하는 경우 출력 데이터는 언롤 루트의 항목당 하나 이상의 행을 포함합니다. 입력 행에 언롤 루트의 항목이 없으면 출력 데이터에서 삭제됩니다. 언롤 루트를 선택하면 항상 기본 동작보다 작거나 같은 수의 행이 출력됩니다.

### <a name="flatten-mapping"></a>평면화 매핑

선택 변환과 마찬가지로 들어오는 필드와 비정규화된 배열의 새 구조 프로젝션을 선택합니다. 비정규화된 배열이 매핑되는 경우 출력 열은 배열과 동일한 데이터 형식이 됩니다. 언롤 기준 언롤이 하위 배열을 포함한 복합 개체의 배열인 경우 하위 배열의 항목을 매핑하면 하나의 배열이 출력됩니다.

매핑 출력을 확인하려면 검사 탭 및 데이터 미리 보기를 참조하세요.

## <a name="rule-based-mapping"></a>규칙 기반 매핑

평면화 변환은 규칙 기반 매핑을 지원하여 규칙에 따라 배열을 평면화하고 계층 수준을 기반으로 구조를 평면화하는 동적 및 유연한 변환을 생성할 수 있습니다.

:::image type="content" source="media/data-flow/flatten-pattern.png" alt-text="평면화 패턴":::

### <a name="matching-condition"></a>일치 조건

정확한 일치 또는 패턴을 사용하여 평면화할 열에 대한 패턴 일치 조건을 입력합니다. 예: ```like(name,'cust%')```

### <a name="deep-column-traversal"></a>심도 있는 열 통과

복합 개체를 전체 열로 처리 하는 대신 개별적으로 복합 개체의 모든 하위 열을 처리 하도록 서비스에 지시 하는 선택적 설정입니다.

### <a name="hierarchy-level"></a>계층 구조 수준

확장할 계층 구조 수준을 선택합니다.

### <a name="name-matches-regex"></a>이름 일치(regex)

필요에 따라 위의 일치 조건을 사용하는 대신 이 상자에서 이름 일치를 정규식으로 표현하도록 선택합니다.

## <a name="examples"></a>예

평면화 변환의 아래 예제에 대해서는 다음 JSON 개체를 참조하세요.

``` json
{
  "name":"MSFT","location":"Redmond", "satellites": ["Bay Area", "Shanghai"],
  "goods": {
    "trade":true, "customers":["government", "distributer", "retail"],
    "orders":[
        {"orderId":1,"orderTotal":123.34,"shipped":{"orderItems":[{"itemName":"Laptop","itemQty":20},{"itemName":"Charger","itemQty":2}]}},
        {"orderId":2,"orderTotal":323.34,"shipped":{"orderItems":[{"itemName":"Mice","itemQty":2},{"itemName":"Keyboard","itemQty":1}]}}
    ]}}
{"name":"Company1","location":"Seattle", "satellites": ["New York"],
  "goods":{"trade":false, "customers":["store1", "store2"],
  "orders":[
      {"orderId":4,"orderTotal":123.34,"shipped":{"orderItems":[{"itemName":"Laptop","itemQty":20},{"itemName":"Charger","itemQty":3}]}},
      {"orderId":5,"orderTotal":343.24,"shipped":{"orderItems":[{"itemName":"Chair","itemQty":4},{"itemName":"Lamp","itemQty":2}]}}
    ]}}
{"name": "Company2", "location": "Bellevue",
  "goods": {"trade": true, "customers":["Bank"], "orders": [{"orderId": 4, "orderTotal": 123.34}]}}
{"name": "Company3", "location": "Kirkland"}
```

### <a name="no-unroll-root-with-string-array"></a>문자열 배열이 있는 언롤 루트 없음

| 언롤 기준 | 언롤 루트 | 프로젝션 |
| --------- | ----------- | ---------- |
| goods.customers | 없음 | name <br> customer = goods.customer |

#### <a name="output"></a>출력

```
{ 'MSFT', 'government'}
{ 'MSFT', 'distributer'}
{ 'MSFT', 'retail'}
{ 'Company1', 'store'}
{ 'Company1', 'store2'}
{ 'Company2', 'Bank'}
{ 'Company3', null}
```

### <a name="no-unroll-root-with-complex-array"></a>복합 배열이 있는 언롤 루트 없음

| 언롤 기준 | 언롤 루트 | 프로젝션 |
| --------- | ----------- | ---------- |
| goods.orders.shipped.orderItems | 없음 | name <br> orderId = goods.orders.orderId <br> itemName = goods.orders.shipped.orderItems.itemName <br> itemQty = goods.orders.shipped.orderItems.itemQty <br> location = location |

#### <a name="output"></a>출력

```
{ 'MSFT', 1, 'Laptop', 20, 'Redmond'}
{ 'MSFT', 1, 'Charger', 2, 'Redmond'}
{ 'MSFT', 2, 'Mice', 2, 'Redmond'}
{ 'MSFT', 2, 'Keyboard', 1, 'Redmond'}
{ 'Company1', 4, 'Laptop', 20, 'Seattle'}
{ 'Company1', 4, 'Charger', 3, 'Seattle'}
{ 'Company1', 5, 'Chair', 4, 'Seattle'}
{ 'Company1', 5, 'Lamp', 2, 'Seattle'}
{ 'Company2', 4, null, null, 'Bellevue'}
{ 'Company3', null, null, null, 'Kirkland'}
```

### <a name="same-root-as-unroll-array"></a>언롤 배열과 동일한 루트

| 언롤 기준 | 언롤 루트 | 프로젝션 |
| --------- | ----------- | ---------- |
| goods.orders | goods.orders | name <br> goods.orders.shipped.orderItems.itemName <br> goods.customers <br> 위치 |

#### <a name="output"></a>출력

```
{ 'MSFT', ['Laptop','Charger'], ['government','distributer','retail'], 'Redmond'}
{ 'MSFT', ['Mice', 'Keyboard'], ['government','distributer','retail'], 'Redmond'}
{ 'Company1', ['Laptop','Charger'], ['store', 'store2'], 'Seattle'}
{ 'Company1', ['Chair', 'Lamp'], ['store', 'store2'], 'Seattle'}
{ 'Company2', null, ['Bank'], 'Bellevue'}
```

### <a name="unroll-root-with-complex-array"></a>복합 배열이 있는 언롤 루트

| 언롤 기준 | 언롤 루트 | 프로젝션 |
| --------- | ----------- | ---------- |
| goods.orders.shipped.orderItem | goods.orders |name <br> orderId = goods.orders.orderId <br> itemName = goods.orders.shipped.orderItems.itemName <br> itemQty = goods.orders.shipped.orderItems.itemQty <br> location = location |

#### <a name="output"></a>출력

```
{ 'MSFT', 1, 'Laptop', 20, 'Redmond'}
{ 'MSFT', 1, 'Charger', 2, 'Redmond'}
{ 'MSFT', 2, 'Mice', 2, 'Redmond'}
{ 'MSFT', 2, 'Keyboard', 1, 'Redmond'}
{ 'Company1', 4, 'Laptop', 20, 'Seattle'}
{ 'Company1', 4, 'Charger', 3, 'Seattle'}
{ 'Company1', 5, 'Chair', 4, 'Seattle'}
{ 'Company1', 5, 'Lamp', 2, 'Seattle'}
{ 'Company2', 4, null, null, 'Bellevue'}
```

## <a name="data-flow-script"></a>데이터 흐름 스크립트

### <a name="syntax"></a>구문

```
<incomingStream>
foldDown(unroll(<unroll cols>),
    mapColumn(
        name,
        each(<array>(type == '<arrayDataType>')),
        each(<array>, match(true())),
        location
    )) ~> <transformationName>
```

### <a name="example"></a>예제

```
source foldDown(unroll(goods.orders.shipped.orderItems, goods.orders),
    mapColumn(
        name,
        orderId = goods.orders.orderId,
        itemName = goods.orders.shipped.orderItems.itemName,
        itemQty = goods.orders.shipped.orderItems.itemQty,
        location = location
    ),
    skipDuplicateMapInputs: false,
    skipDuplicateMapOutputs: false) 
```    

## <a name="next-steps"></a>다음 단계

* [피벗 변환](data-flow-pivot.md)을 사용하여 행을 열로 피벗합니다.
* [피벗 해제 변환](data-flow-unpivot.md)을 사용하여 열을 행으로 피벗합니다.
