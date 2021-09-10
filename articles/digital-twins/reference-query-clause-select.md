---
title: Azure Digital Twins 쿼리 언어 참조 - SELECT 절
titleSuffix: Azure Digital Twins
description: Azure Digital Twins 쿼리 언어 SELECT 절에 대한 참조 설명서
author: baanders
ms.author: baanders
ms.date: 03/31/2021
ms.topic: article
ms.service: digital-twins
ms.openlocfilehash: eeba36b200028d4e90e22cd32c9bab6dbe3799e0
ms.sourcegitcommit: bc29cf4472118c8e33e20b420d3adb17226bee3f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/08/2021
ms.locfileid: "113492868"
---
# <a name="azure-digital-twins-query-language-reference-select-clause"></a>Azure Digital Twins 쿼리 언어 참조: SELECT 절

이 문서에는 [Azure Digital Twins 쿼리 언어](concepts-query-language.md)의 **SELECT 절** 에 대한 참조 정보가 포함되어 있습니다.

SELECT 절은 쿼리의 첫 번째 부분입니다. 쿼리에서 반환되는 열 목록을 지정합니다.

모든 쿼리에 이 절이 필요합니다.

## <a name="select-"></a>SELECT *

SELECT 문에 `*` 문자를 사용하여 결과 집합의 속성에 할당하지 않고 디지털 트윈 문서를 있는 그대로 프로젝션합니다.

>[!NOTE]
> `SELECT *`는 쿼리에서 `JOIN`을 사용하지 않는 경우에만 유효한 구문입니다. `JOIN`을 사용하는 쿼리에 대한 자세한 내용은 [Azure Digital Twins 쿼리 언어 참조: JOIN 절](reference-query-clause-join.md)을 참조하세요.

### <a name="syntax"></a>구문

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="SelectSyntax":::

### <a name="returns"></a>반환

쿼리에서 반환되는 속성 세트입니다.

### <a name="example"></a>예제

다음 쿼리는 인스턴스에 모든 디지털 트윈을 반환합니다. 

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="SelectExample":::

## <a name="select-columns-with-projections"></a>프로젝션이 있는 SELECT 열

SELECT 절에서 프로젝션을 사용하여 쿼리에서 반환되는 열을 선택할 수 있습니다. 트윈 및 관계의 명명된 컬렉션 또는 트윈 및 관계의 속성을 지정할 수 있습니다.

프로젝션은 이제 기본 속성 및 복합 속성 모두에 대해 지원됩니다.

### <a name="syntax"></a>구문

컬렉션을 프로젝트하려면:

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="SelectProjectCollectionSyntax":::

속성을 프로젝트하려면:

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="SelectProjectPropertySyntax":::

### <a name="returns"></a>반환

프로젝션에 지정된 트윈, 속성 또는 관계의 컬렉션입니다.

프로젝션에 포함된 속성이 특정 데이터 행에 대해 존재하지 않으면 속성은 마찬가지로 결과 집합에 표시되지 않습니다. 이 동작의 예제는 [프로젝트 속성 예제: 데이터 행에 대해 존재하지 않는 속성](#project-property-example-property-not-present-for-a-data-row)을 참조하세요.

### <a name="examples"></a>예

#### <a name="example-scenario"></a>예제 시나리오

다음 예제에서는 다음 데이터 요소를 포함하는 트윈 그래프를 살펴보겠습니다.
* FactoryA라는 팩터리 트윈
    - 값이 `FactoryA`인 `name`이라는 속성을 포함합니다.
* Contoso라는 소비자 트윈
    - 값이 `Contoso`인 `name`이라는 속성을 포함합니다.
* FactoryA에서 Contoso로의 consumerRelationship 관계, `FactoryA-consumerRelationship-Contoso`라고 함
    - 값이 `Jeff`인 `managedBy`라는 속성을 포함합니다.

이 시나리오를 보여 주는 다이어그램은 다음과 같습니다.

:::row:::
    :::column:::
        :::image type="content" source="media/reference-query-clause-select/projections-graph.png" alt-text="위에서 설명한 샘플 그래프를 보여 주는 다이어그램":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

#### <a name="project-collection-example"></a>프로젝트 컬렉션 예제

다음은 이 그래프에서 컬렉션을 프로젝트하는 예제 쿼리입니다. 다음 쿼리는 전체 트윈 컬렉션의 이름을 `T`로 지정하고 반환할 컬렉션으로 `T`를 프로젝트하여 인스턴스의 모든 디지털 트윈을 반환합니다. 

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="SelectProjectCollectionExample":::

이 쿼리에서 반환되는 JSON 페이로드는 다음과 같습니다.

```json
{
  "value": [
    {
      "result": [
        {
          "T": {
            "$dtId": "FactoryA",
            "$etag": "W/\"d22267a0-fd4f-4f6b-916d-4946a30453c9\"",
            "$metadata": {
              "$model": "dtmi:contosocom:DigitalTwins:Factory;1",
              "name": {
                "lastUpdateTime": "2021-04-19T17:15:54.4977151Z"
              }
            },
            "name": "FactoryA"
          }
        },
        {
          "T": {
            "$dtId": "Contoso",
            "$etag": "W/\"a96dc85e-56ae-4061-866b-058a149e03d8\"",
            "$metadata": {
              "$model": "dtmi:com:contoso:Consumer;1",
              "name": {
                "lastUpdateTime": "2021-04-19T17:16:30.2154166Z"
              }
            },
            "name": "Contoso"
          }
        }
      ]
    }
  ],
  "continuationToken": "null"
}
```

#### <a name="project-with-join-example"></a>JOIN이 있는 프로젝트 예제

프로젝션은 일반적으로 `JOIN`에 지정된 컬렉션을 반환하는 데 사용됩니다. 다음 쿼리는 프로젝션을 사용하여 소비자, 팩터리 및 관계의 데이터를 반환합니다. 예제에 사용된 `JOIN` 구문에 대한 자세한 내용은 [Azure Digital Twins 쿼리 언어 참조: JOIN 절](reference-query-clause-join.md)을 참조하세요.

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="SelectProjectJoinExample":::

이 쿼리에서 반환되는 JSON 페이로드는 다음과 같습니다.

```json
{
  "value": [
    {
      "result": [
        {
          "Consumer": {
            "$dtId": "Contoso",
            "$etag": "W/\"a96dc85e-56ae-4061-866b-058a149e03d8\"",
            "$metadata": {
              "$model": "dtmi:com:contoso:Consumer;1",
              "name": {
                "lastUpdateTime": "2021-04-19T17:16:30.2154166Z"
              }
            },
            "name": "Contoso"
          },
          "Factory": {
            "$dtId": "FactoryA",
            "$etag": "W/\"d22267a0-fd4f-4f6b-916d-4946a30453c9\"",
            "$metadata": {
              "$model": "dtmi:contosocom:DigitalTwins:Factory;1",
              "name": {
                "lastUpdateTime": "2021-04-19T17:15:54.4977151Z"
              }
            },
            "name": "FactoryA"
          },
          "Relationship": {
            "$etag": "W/\"f01e07c1-19e4-4bbe-a12d-f5761e86d3e8\"",
            "$relationshipId": "FactoryA-consumerRelationship-Contoso",
            "$relationshipName": "consumerRelationship",
            "$sourceId": "FactoryA",
            "$targetId": "Contoso",
            "managedBy": "Jeff"
          }
        }
      ]
    }
  ],
  "continuationToken": "null"
}
```

#### <a name="project-property-example"></a>프로젝트 속성 예제

다음은 속성을 프로젝트하는 예제입니다. 다음 쿼리는 프로젝션을 사용하여 소비자 트윈의 `name` 속성과 관계의 `managedBy` 속성을 반환합니다.

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="SelectProjectPropertyExample":::

이 쿼리에서 반환되는 JSON 페이로드는 다음과 같습니다.

```json
{
  "value": [
    {
      "result": [
        {
          "managedBy": "Jeff",
          "name": "Contoso"
        }
      ]
    }
  ],
  "continuationToken": "null"
}
```

#### <a name="project-property-example-property-not-present-for-a-data-row"></a>프로젝트 속성 예제: 데이터 행에 대해 존재하지 않는 속성

프로젝션에 포함된 속성이 특정 데이터 행에 대해 존재하지 않으면 속성은 마찬가지로 결과 집합에 표시되지 않습니다.

이 예제에서는 사람을 나타내는 쌍 트윈을 고려합니다. 일부 트윈에는 연결된 나이가 있지만 다른 트윈은 그렇지 않습니다.

다음은 `name` 및 `age` 속성을 프로젝트하는 쿼리입니다.

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="SelectProjectPropertyNotPresentExample":::

결과는 트윈에 이 속성이 없는 결과의 일부 트윈에서 누락된 `age` 속성으로 표시될 수 있습니다.

```json
{
  "value": [
    {
      "result": [
        {
          "name": "John",
          "age": 27
        },
        {
          "name": "Keanu"
        }
      ]
    }
  ],
  "continuationToken": "null"
}
```

## <a name="select-count"></a>SELECT COUNT

이 메서드를 사용하여 결과 집합의 항목 수를 계산하고 해당 숫자를 반환합니다.

### <a name="syntax"></a>구문

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="SelectCountSyntax":::

### <a name="arguments"></a>인수

없음

### <a name="returns"></a>반환

`int` 값입니다.

### <a name="example"></a>예제

다음 쿼리는 인스턴스에 있는 모든 디지털 트윈의 개수를 반환합니다.

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="SelectCountExample":::

다음 쿼리는 인스턴스에 있는 모든 관계의 개수를 반환합니다.

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="SelectCountRelationshipsExample":::

## <a name="select-top"></a>SELECT TOP

이 메서드를 사용하여 쿼리 요구 사항을 충족하는 상위 항목 중 일부만 반환합니다.

### <a name="syntax"></a>구문

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="SelectTopSyntax":::

### <a name="arguments"></a>인수

선택할 상위 항목 수를 지정하는 `int` 값입니다.

### <a name="returns"></a>반환

트윈의 컬렉션입니다.

### <a name="example"></a>예제

다음 쿼리는 인스턴스에서 처음 5개의 디지털 트윈만 반환합니다.

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="SelectTopExample":::