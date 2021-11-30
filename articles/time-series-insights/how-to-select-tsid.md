---
title: 시계열 ID 선택 모범 사례 - Azure Time Series Insights | Microsoft Docs
description: Azure Time Series Insights Gen2에서 시계열 ID를 선택할 때의 모범 사례에 대해 알아봅니다.
author: tedvilutis
ms.author: tvilutis
manager: cnovak
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 03/23/2021
ms.custom: seodec18
ms.openlocfilehash: 2789cb0082a229369f03f619a407a2b0de82403a
ms.sourcegitcommit: dcf3424d7149fceaea0340eb0657baa2c27882a5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/30/2021
ms.locfileid: "133271026"
---
# <a name="best-practices-for-choosing-a-time-series-id"></a>시계열 ID 선택 모범 사례

이 문서에서는 Azure Time Series Insights Gen2 환경에 대한 시계열 ID의 중요도와 이를 선택하는 모범 사례를 요약합니다.

## <a name="choose-a-time-series-id"></a>시계열 ID 선택

적절한 시계열 ID 선택이 중요합니다. 시계열 ID 선택은 데이터베이스에 대한 파티션 키를 선택하는 것과 같습니다. Azure Time Series Insights Gen2 환경을 만들 때 필요합니다.

시계열 ID에 대한 자세한 설명은 환경 프로비저닝 자습서를 시청하세요. 두 가지 JSON 원격 분석 페이로드 예제와 각 페이로드에 대한 올바른 시계열 ID 선택을 볼 수 있습니다.</br>

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RWzk3P]


> [!IMPORTANT]
> 시계열 ID는 다음과 같습니다.
>
> * *대/소문자를 구분하는 문자열* 속성: 문자 및 문자 대/소문자는 검색, 비교, 업데이트 및 분할 시 사용됩니다.
> * *변경이 불가능한* 속성: 만든 후에는 변경할 수 없습니다.

> [!TIP]
> 이벤트 원본이 IoT Hub인 경우 Time Series ID는 ***iothub-connection-device-id*** 일 수 있습니다. IoT 플러그 앤 플레이 디바이스 모델을 사용하거나 구성 요소 없이 사용하려는 경우 나중에 필요할 때를 대비해서 ***dt-subject*** 를 복합 키의 일부로 포함해야 합니다.

주요 모범 사례는 다음과 같습니다.

* 고유한 값이 많은(수백 개 또는 수천 개) 파티션 키를 사용하면 좋습니다. 많은 경우 JSON의 디바이스 ID, 센서 ID 또는 태그 ID일 수 있습니다.
* 시계열 ID는 [시계열 모델](./concepts-model-overview.md)의 리프 노드 수준에서 고유해야 합니다.
* 시계열 ID의 속성 이름 문자열의 문자 수 제한은 128자입니다. 시계열 ID의 속성 값에 대한 문자 수 제한은 1,024자입니다.
* 시계열 ID의 고유한 속성 값이 누락된 경우 Null 값으로 처리되고 고유성 제약 조건의 동일한 규칙을 따릅니다.
* 시계열 ID가 복잡한 JSON 개체 내에 중첩된 경우 속성 이름을 작성할 때 수신 [평면화 규칙](./concepts-json-flattening-escaping-rules.md)을 따라야 합니다. 예제 [B](concepts-json-flattening-escaping-rules.md#example-b)를 확인해 보세요.
* 또한 최대 *3개* 의 키 속성을 시계열 ID로 선택할 수 있습니다. 해당 조합은 시계열 ID를 나타내는 복합 키가 됩니다.  
  > [!NOTE]
  > 3개의 키 속성은 문자열이어야 합니다.
  > 한 번에 하나의 속성 대신 이 복합 키에 쿼리해야 합니다.

## <a name="select-more-than-one-key-property"></a>키 속성을 두 개 이상 선택

다음 시나리오에서는 둘 이상의 키 속성을 시계열 ID로 선택하는 방법을 설명합니다.  

### <a name="example-1-time-series-id-with-a-unique-key"></a>예제 1: 고유 키가 있는 시계열 ID

* 레거시 자산 집합이 있습니다. 각 집합에는 고유한 키가 있습니다.
* 한 집합은 **deviceId** 속성에 의해 고유하게 식별됩니다. 다른 집합의 고유 속성은 **objectId** 입니다. 두 집합 모두, 다른 집합의 고유 속성은 없습니다. 이 예제에서는 두 개의 키인 **deviceId** 와 **objectId** 를 고유 키로 선택합니다.
* Null 값이 허용되며, 이벤트 페이로드에 속성이 없을 경우 Null 값으로 산출됩니다. 이는 각 이벤트 원본의 데이터에 고유한 시계열 ID가 있는 두 개의 이벤트 원본에 대한 데이터 전송을 처리하는 적절한 방법이기도 합니다.

### <a name="example-2-time-series-id-with-a-composite-key"></a>예제 2: 복합 키가 있는 시계열 ID

* 동일한 자산 집합 내에서 여러 속성이 고유해야 합니다.
* 스마트 건물의 제조업체가 모든 방에 센서를 배포한다고 가정합니다. 일반적으로 각 방의 **sensorId** 값은 동일합니다. 예를 들자면 **sensor1**, **sensor2**, **sensor3** 가 있습니다.
* 또한 빌딩의 **flrRm** 속성에는 사이트 간에 겹치는 층 및 방 번호가 있습니다. 해당 숫자에는 **1a**, **2b**, **3a** 와 같은 값이 있습니다.
* **Redmond**, **Barcelona**, **Tokyo** 와 같은 값을 포함하는 **location** 속성이 있습니다. 고유한 속성을 만들려면 **sensorId**, **flrRm**, **location** 의 세 가지 속성을 시계열 ID 키로 지정합니다.

원시 이벤트 예제

```JSON
{
  "sensorId": "sensor1",
  "flrRm": "1a",
  "location": "Redmond",
  "temperature": 78
}
```

Azure Portal에서 다음과 같이 복합 키를 입력할 수 있습니다.

[![환경에 대한 시계열 ID를 구성합니다.](media/v2-how-to-tsid/configure-environment-key.png)](media/v2-how-to-tsid/configure-environment-key.png#lightbox)

  > [!NOTE]
  > Azure Portal에서 쉼표로 구분된 속성 이름을 하나의 텍스트 상자에 입력하지 않으면 쉼표를 포함하는 단일한 속성 이름으로 처리됩니다.
  > 자체 텍스트 상자에 각 속성의 이름을 입력합니다.

## <a name="next-steps"></a>다음 단계

* 이벤트를 저장하는 방법을 알아보려면 [JSON 평면화 및 이스케이프 규칙](./concepts-json-flattening-escaping-rules.md) 을 참조하세요.

* [Azure Time Series Insights Gen2 환경](./how-to-plan-your-environment.md) 플랜을 짜 보세요.
