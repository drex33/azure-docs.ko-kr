---
title: Digital Twins 및 쌍 그래프
titleSuffix: Azure Digital Twins
description: 디지털 트윈의 개념과 관계가 어떻게 그래프를 만드는지를 이해합니다.
author: baanders
ms.author: baanders
ms.date: 8/26/2021
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 42cce83683df789aeaabe53ca170f17319ec3603
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/30/2021
ms.locfileid: "123224846"
---
# <a name="understand-digital-twins-and-their-twin-graph"></a>디지털 트윈 및 트윈 그래프 이해

Azure Digital Twins 솔루션에서 환경의 엔터티는 **디지털 트윈** 으로 표시됩니다. 디지털 트윈은 사용자 정의 [모델](concepts-models.md)중 하나의 인스턴스입니다. 이는 **관계** 를 통해 다른 디지털 트윈에 연결하여 **트윈 그래프** 를 형성할 수 있습니다. 이 트윈 그래프는 전체 환경에 대한 표현입니다.

> [!TIP]
> "Azure Digital Twins"는 전체 Azure 서비스를 의미합니다. "Digital 트윈" 또는 "트윈"은 서비스 인스턴스 내의 개별 트윈 노드를 나타냅니다.

## <a name="digital-twins"></a>Digital Twins

Azure Digital Twins 인스턴스에서 디지털 트윈을 만들려면 먼저 서비스에 *모델* 을 업로드해야 합니다. 모델은 특정 트윈에 포함될 수 있는 속성, 원격 분석 메시지 및 관계 집합을 설명합니다. 모델에 정의된 정보 유형은 [사용자 지정 모델](concepts-models.md)을 참조하세요.

모델을 만들고 업로드한 후 클라이언트 앱에서 해당 형식의 인스턴스를 만들 수 있습니다. 이 인스턴스가 디지털 트윈입니다. 예를 들어, Floor의 모델을 만든 후 이 형식을 사용하는 하나 이상의 디지털 트윈을 만들 수 있습니다(예: GroundFloor라는 Floor 형식 트윈, Floor2라는 다른 트윈 등).

[!INCLUDE [digital-twins-versus-device-twins](../../includes/digital-twins-versus-device-twins.md)]

## <a name="relationships-a-graph-of-digital-twins"></a>관계: 디지털 트윈의 그래프

트윈은 해당 관계에 따라 트윈으로 연결됩니다. 트윈이 가질 수 있는 관계는 해당 모델의 일부로 정의됩니다.  

예를 들어 모델 Floor는 Room 형식의 트윈을 대상으로 하는 *contains* 관계를 정의할 수 있습니다. 이 정의를 사용하여 Azure Digital Twins는 모든 Floor 트윈에서 Room 트윈으로의 *contains* 관계(Room 하위 형식의 트윈 포함)를 만들 수 있습니다. 

이 프로세스의 결과는 그래프의 가장자리(해당 관계)를 통해 연결된 노드(디지털 트윈) 집합입니다.

[!INCLUDE [visualizing with Azure Digital Twins explorer](../../includes/digital-twins-visualization.md)]

:::image type="content" source="media/concepts-azure-digital-twins-explorer/azure-digital-twins-explorer-demo.png" alt-text="샘플 모델 및 트윈을 보여주는 Azure Digital Twins Explorer의 스크린샷." lightbox="media/concepts-azure-digital-twins-explorer/azure-digital-twins-explorer-demo.png":::

## <a name="create-with-the-apis"></a>API로 만들기

이 섹션에서는 클라이언트 애플리케이션에서 디지털 트윈 및 관계를 만드는 방법을 보여 줍니다. [DigitalTwins API](/rest/api/digital-twins/dataplane/twins)를 사용하는 .NET 코드 예제를 포함하여 이러한 각 개념 내에서 수행되는 작업에 대한 추가 컨텍스트를 제공합니다.

### <a name="create-digital-twins"></a>디지털 트윈 만들기

다음은 [DigitalTwins API](/rest/api/digital-twins/dataplane/twins)를 사용하여 인스턴스화하는 동안 정의된 `twinId`가 있는 Room 형식의 트윈을 인스턴스화하는 클라이언트 코드의 코드 조각입니다.

트윈을 만들 때 트윈의 속성을 초기화하거나 나중에 설정할 수 있습니다. 초기화된 속성이 있는 트윈을 만들려면 필요한 초기화 값을 제공하는 JSON 문서를 만듭니다.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_other.cs" id="CreateTwin_noHelper":::

사전을 사용하는 대신, `BasicDigitalTwin`이라는 도우미 클래스를 사용하여 "트윈" 개체에 속성 필드를 더 직접 저장할 수도 있습니다. 도우미 클래스 및 사용 예제에 대한 자세한 내용은 방법: 디지털 트윈 관리의 *[디지털 트윈 만들기](how-to-manage-twin.md#create-a-digital-twin)* 섹션을 참조하세요.

>[!NOTE]
>트윈 속성은 선택적으로 처리되므로 초기화할 필요가 없지만 트윈을 만들 때 트윈의 모든 [구성 요소](concepts-models.md#elements-of-a-model)를 **설정해야 합니다**. 비어 있는 개체일 수 있지만 구성 요소 자체는 존재해야 합니다.

### <a name="create-relationships"></a>관계 만들기

다음은 [DigitalTwins API](/rest/api/digital-twins/dataplane/twins)를 사용하여 하나의 디지털 트윈(“원본” 트윈)에서 다른 디지털 트윈(“대상” 트윈)에 대한 관계를 빌드하는 예제 클라이언트 코드입니다.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_other.cs" id="CreateRelationship_short":::

## <a name="json-representations-of-graph-elements"></a>그래프 요소의 JSON 표현

디지털 트윈 데이터와 관계 데이터는 둘 다 JSON 형식으로 저장됩니다. 즉, Azure Digital Twins 인스턴스에서 [트윈 그래프를 쿼리](how-to-query-graph.md)하면 생성된 디지털 트윈과 관계의 JSON 표현이 생성됩니다.

### <a name="digital-twin-json-format"></a>디지털 트윈 JSON 형식

JSON 개체로 표시되는 경우 디지털 트윈은 다음 필드를 표시합니다.

| 필드 이름 | Description |
| --- | --- |
| `$dtId` | 디지털 트윈의 ID를 나타내는 사용자 제공 문자열 |
| `$etag` | 웹 서버에서 할당한 표준 HTTP 필드 |
| `$conformance` | 이 디지털 트윈의 규칙 상태를 포함하는 열거형(*준수*, *비준수*, *알 수 없음*) |
| `<property-name>` | JSON의 속성 값(`string`, 숫자 형식 또는 개체) |
| `$relationships` | 관계 컬렉션에 대한 경로 URL입니다. 디지털 트윈에 나가는 관계 가장자리가 없으면 이 필드가 없습니다. |
| `$metadata.$model` | [선택 사항] 이 디지털 트윈의 특징을 나타내는 모델 인터페이스의 ID |
| `$metadata.<property-name>.desiredValue` | [쓰기 가능한 속성에만 해당] 지정된 속성의 원하는 값 |
| `$metadata.<property-name>.desiredVersion` | [쓰기 가능한 속성에만 해당] 원하는 값의 버전 |
| `$metadata.<property-name>.ackVersion` | 디지털 트윈을 구현하는 디바이스 앱에서 인정하는 버전 |
| `$metadata.<property-name>.ackCode` | [쓰기 가능한 속성에만 해당] 디지털 트윈을 구현하는 디바이스 앱에서 반환된 `ack` 코드 |
| `$metadata.<property-name>.ackDescription` | [쓰기 가능한 속성에만 해당] 디지털 트윈을 구현하는 디바이스 앱에서 반환된 `ack` 설명 |
| `<component-name>` | 구성 요소의 속성 값과 메타데이터를 포함하는 JSON 개체입니다(루트 개체와 유사함). 이 개체는 구성 요소에 속성이 없는 경우에도 존재합니다. |
| `<component-name>.<property-name>` | JSON의 구성 요소 속성 값(`string`, 숫자 형식 또는 개체) |
| `<component-name>.$metadata` | 루트 수준 `$metadata`와 비슷한 구성 요소에 대한 메타데이터 정보 |

JSON 개체로 형식이 지정된 디지털 트윈의 예제는 다음과 같습니다.

```json
{
  "$dtId": "Cafe",
  "$etag": "W/\"e59ce8f5-03c0-4356-aea9-249ecbdc07f9\"",
  "Temperature": 72,
  "Location": {
    "x": 101,
    "y": 33
  },
  "component": {
    "TableOccupancy": 1,
    "$metadata": {
      "TableOccupancy": {
        "desiredValue": 1,
        "desiredVersion": 3,
        "ackVersion": 2,
        "ackCode": 200,
        "ackDescription": "OK"
      }
    }
  },
  "$metadata": {
    "$model": "dtmi:com:contoso:Room;1",
    "Temperature": {
      "desiredValue": 72,
      "desiredVersion": 5,
      "ackVersion": 4,
      "ackCode": 200,
      "ackDescription": "OK"
    },
    "Location": {
      "desiredValue": {
        "x": 101,
        "y": 33,
      },
      "desiredVersion": 8,
      "ackVersion": 8,
      "ackCode": 200,
      "ackDescription": "OK"
    }
  }
}
```

### <a name="relationship-json-format"></a>관계 JSON 형식

JSON 개체로 표시되는 경우 디지털 트윈의 관계는 다음 필드를 표시합니다.

| 필드 이름 | Description |
| --- | --- |
| `$relationshipId` | 이 관계의 ID를 나타내는 사용자 제공 문자열입니다. 이 문자열은 원본 디지털 트윈의 컨텍스트에서 고유하며, 이는 `sourceId` + `relationshipId`가 Azure Digital Twins 인스턴스의 컨텍스트에서 고유한 것을 의미하기도 합니다. |
| `$etag` | 웹 서버에서 할당한 표준 HTTP 필드 |
| `$sourceId` | 원본 디지털 트윈의 ID |
| `$targetId` | 대상 디지털 트윈의 ID |
| `$relationshipName` | 관계의 이름 |
| `<property-name>` | [선택 사항] 이 관계의 속성 값(JSON)(`string`, 숫자 형식 또는 개체) |

JSON 개체로 형식이 지정된 관계의 예제는 다음과 같습니다.

```json
{
  "$relationshipId": "relationship-01",
  "$etag": "W/\"506e8391-2b21-4ac9-bca3-53e6620f6a90\"",
  "$sourceId": "GroundFloor",
  "$targetId": "Cafe",
  "$relationshipName": "contains",
  "startDate": "2020-02-04"
}
```

## <a name="next-steps"></a>다음 단계

Azure Digital Twin API를 사용하여 그래프 요소를 관리하는 방법을 참조하세요.
* [Digital Twins 관리](how-to-manage-twin.md)
* [쌍 그래프 및 관계 관리](how-to-manage-graph.md)

또는 정보에 대한 Azure Digital Twins 트윈 그래프를 쿼리하는 방법에 대해 알아봅니다.
* [쿼리 언어](concepts-query-language.md)