---
title: DTDL 모델
titleSuffix: Azure Digital Twins
description: Azure 디지털 쌍이 사용자 지정 모델을 사용 하 여 사용자 환경에서 엔터티를 설명 하는 방법과 DTDL (디지털 쌍 정의 언어)를 사용 하 여 이러한 모델을 정의 하는 방법에 대해 알아봅니다.
author: baanders
ms.author: baanders
ms.date: 10/7/2021
ms.topic: conceptual
ms.service: digital-twins
ms.custom: fasttrack-edit
ms.openlocfilehash: e754e78be5b4cff3f69d5f811e4b91def456b5e5
ms.sourcegitcommit: 66b6e640e2a294a7fbbdb3309b4829df526d863d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/01/2021
ms.locfileid: "133356723"
---
# <a name="learn-about-twin-models-and-how-to-define-them-in-azure-digital-twins"></a>쌍 모델 및 Azure Digital Twins에서 쌍을 정의 하는 방법에 대해 알아봅니다.

Azure Digital Twins의 주요 특징은 고유한 어휘를 정의하고 자체 정의된 비즈니스 조건에서 트윈 그래프를 작성하는 기능입니다. 이 기능은 사용자 제공 **모델** 을 통해 제공됩니다. 사용자 설명에서 모델을 명사로 간주할 수 있습니다. Azure Digital Twins 모델은 JSON-LD 기반의 **DTDL(디지털 트윈 정의 언어)** 로 표시됩니다. 

모델은 개체 지향 프로그래밍 언어의 **클래스** 와 비슷하며 실제 작업 환경에서 하나의 특정 개념에 대한 데이터 모양을 정의합니다. 모델에는 이름(예: *Room* 또는 *TemperatureSensor*)이 있고 속성, 원격 분석/이벤트, 환경에서 이 유형의 엔터티를 설명하는 명령 등의 요소가 포함되어 있습니다. 나중에 이러한 모델을 사용하여 이 유형 설명을 충족하는 특정 엔터티를 나타내는 [디지털 트윈](concepts-twins-graph.md)을 만듭니다.

## <a name="digital-twin-definition-language-dtdl-for-models"></a>모델에 대한 DTDL(디지털 트윈 정의 언어)

Azure Digital Twins의 모델은 DTDL(디지털 트윈 정의 언어)을 사용하여 정의됩니다. 

DTDL에 대한 전체 언어 사양은 GitHub: [DTDL(디지털 트윈 정의 언어) - 버전 2](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md)에서 확인할 수 있습니다. 이 페이지에는 사용자 고유의 DTDL 모델 작성을 시작 하는 데 도움이 되는 자세한 DTDL 참조와 예제가 포함 되어 있습니다.

DTDL은 JSON-LD를 기반으로 하며 프로그래밍 언어와 독립적입니다. DTDL은 Azure Digital Twins에만 국한되는 것이 아니라 [IoT 플러그 앤 플레이](../iot-develop/overview-iot-plug-and-play.md)와 같은 다른 IoT 서비스에서 디바이스 데이터를 표시하는 데도 사용됩니다. Azure Digital Twins는 DTDL **버전 2** 를 사용합니다(Azure Digital Twins에서 DTDL 버전 1은 이제 사용되지 않음). 

이 문서의 나머지 부분에는 Azure Digital Twins에서 언어를 사용하는 방법이 요약되어 있습니다.

### <a name="azure-digital-twins-dtdl-implementation-specifics"></a>Azure Digital Twins DTDL 구현 세부 사항

DTDL을 사용하는 모든 서비스가 DTDL과 똑같은 기능을 구현하는 것은 아닙니다. 다음을 포함 하 여 Azure Digital Twins에서 현재 지원 하지 않는 몇 가지 DTDL 기능이 있습니다.
* DTDL 명령
* `writable`속성 또는 관계에 대 한 특성입니다. 이 특성은 DTDL 사양에 따라 설정할 수 있지만 Azure Digital Twins에서 사용되지 않습니다. 대신, 이러한 특성은 항상 Azure Digital Twins 서비스에 대한 일반 쓰기 권한이 있는 외부 클라이언트에서 쓰기 가능으로 처리됩니다.
* `minMultiplicity` `maxMultiplicity` 관계에 대 한 및 속성입니다. 이러한 특성은 DTDL 사양에 따라 설정할 수 있지만 Azure Digital Twins에 의해 값이 적용 되지 않습니다.

DTDL 모델이 Azure Digital Twins와 호환 되려면 다음 요구 사항도 충족 해야 합니다.

* 모델의 모든 최상위 DTDL 요소는 *인터페이스* 형식이어야 합니다. 이 요구 사항의 이유는 Azure Digital Twins 모델 API가 인터페이스 또는 인터페이스 배열을 나타내는 JSON 개체를 받을 수 있기 때문입니다. 따라서 최상위 수준에서 다른 DTDL 요소 형식이 허용되지 않습니다.
* Azure Digital Twins의 DTDL은 *명령* 을 정의하지 않아야 합니다.
* Azure Digital Twins는 단일 수준의 구성 요소 중첩만 허용합니다. 즉, 구성 요소로 사용되는 인터페이스는 구성 요소 자체를 가질 수 없습니다. 
* 인터페이스는 다른 DTDL 인터페이스 내에서 인라인으로 정의될 수 없습니다. 자체 ID를 사용하여 별도의 최상위 엔터티로 정의해야 합니다. 그런 다음 다른 인터페이스가 해당 인터페이스를 구성 요소나 상속을 통해 포함하려는 경우 해당 ID를 참조할 수 있습니다.

## <a name="model-overview"></a>모델 개요

### <a name="elements-of-a-model"></a>모델의 요소

모델 정의 내에서 최상위 코드 항목은 **인터페이스** 입니다. 이 형식은 전체 모델을 캡슐화하며 나머지 모델은 인터페이스 내에서 정의됩니다. 

DTDL 모델 인터페이스는 다음의 각 필드를 0개, 1개 또는 여러 개 포함할 수 있습니다.
* **속성** - 속성은 엔터티의 상태를 나타내는 데이터 필드입니다(예: 많은 개체 지향 프로그래밍 언어의 속성). 속성에는 백업 스토리지가 있으며 언제든지 읽을 수 있습니다. 자세한 내용은 아래의 [속성 및 원격 분석](#properties-and-telemetry)을 참조하세요.
* **원격 분석** - 원격 분석 필드는 측정값이나 이벤트를 나타내며 디바이스 센서 판독값을 설명하는 데 종종 사용됩니다. 속성과 달리 원격 분석은 디지털 트윈에 저장되지 않습니다. 발생 시 처리해야 하는 일련의 시간 제한 데이터 이벤트입니다. 자세한 내용은 아래의 [속성 및 원격 분석](#properties-and-telemetry)을 참조하세요.
* **관계** - 관계를 사용하면 디지털 트윈이 다른 디지털 트윈과 관련되는 방식을 나타낼 수 있습니다. 관계는 *contains*("바닥은 방을 포함""), *cools*("hvac는 방을 식힘"), *isBilledTo*("압축기는 사용자에게 청구됨") 등 다양한 의미 체계를 나타낼 수 있습니다. 관계를 통해 솔루션은 상호 관련 엔터티의 그래프를 제공할 수 있습니다. 관계에도 고유한 속성이 있을 수 있습니다. 자세한 내용은 아래의 [관계](#relationships)를 참조하세요.
* **구성 요소** - 구성 요소를 사용하면 원하는 경우 모델 인터페이스를 다른 인터페이스의 어셈블리로 빌드할 수 있습니다. 구성 요소의 예로는 *phone* 에 대한 모델을 정의하는 데 사용되는 *frontCamera* 인터페이스(및 다른 구성 요소 인터페이스 *backCamera*)가 있습니다. 먼저 *frontCamera* 의 인터페이스를 자체 모델인 것처럼 정의한 다음 *Phone* 을 정의할 때 참조합니다.

    구성 요소를 사용하여 솔루션의 핵심 부분이지만 별도의 ID가 필요하지 않은 항목을 설명할 수 있으며 트윈 그래프에서 독립적으로 생성, 삭제 또는 다시 정렬할 필요가 없습니다. 트윈 그래프에서 엔터티가 독립적으로 존재하도록 하려면 **관계** 로 연결된 서로 다른 모델의 별개 디지털 트윈으로 표시합니다.
    
    >[!TIP] 
    >구성 요소를 사용하여 모델 인터페이스 내에서 관련된 속성 집합을 그룹화할 수도 있습니다. 이 경우 각 구성 요소를 인터페이스 내의 네임스페이스 또는 "폴더"로 간주할 수 있습니다.

    자세한 내용은 아래의 [구성 요소](#components)를 참조하세요.


> [!NOTE]
> [DTDL의 사양](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md)에서는 디지털 트윈에서 실행할 수 있는 메서드인 **명령**(예: 리셋 명령, 팬을 켜거나 끄는 명령)도 정의합니다. 그러나 *Azure Digital Twins에서는 현재 명령이 지원되지 않습니다.*

### <a name="model-code"></a>모델 코드

트윈 형식 모델은 텍스트 편집기로 작성할 수 있습니다. DTDL 언어는 JSON 구문을 따르므로 .json 확장명을 사용하여 모델을 저장해야 합니다. JSON 확장명을 사용하면 많은 프로그래밍 텍스트 편집기에서 DTDL 문서에 대한 기본 구문 검사 및 강조 표시를 제공할 수 있습니다. [Visual Studio Code](https://code.visualstudio.com/)에 사용할 수 있는 [DTDL 확장명](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-dtdl)도 있습니다.

모델의 필드는 다음과 같습니다.

| 필드 | Description |
| --- | --- |
| `@id` | 모델의 식별자입니다. `dtmi:<domain>:<unique-model-identifier>;<model-version-number>` 형식이어야 합니다. |
| `@type` | 설명하는 정보의 종류를 식별합니다. 인터페이스의 경우 형식은 *인터페이스* 입니다. |
| `@context` | JSON 문서에 대한 [컨텍스트](https://niem.github.io/json/reference/json-ld/context/)를 설정합니다. 모델은 `dtmi:dtdl:context;2`를 사용해야 합니다. |
| `displayName` | [선택 사항] 모델의 친숙한 이름을 정의하는 옵션을 제공합니다. |
| `contents` | 나머지 모든 인터페이스 데이터는 특성 정의의 배열로 여기에 배치됩니다. 각 특성은 `@type`(**속성**, **원격 분석**, **명령**, **관계** 또는 **구성 요소**)을 제공하여 설명하는 인터페이스 정보의 종류를 식별한 다음, 실제 특성을 정의하는 속성 집합(예: **속성** 을 정의하는 `name` 및 `schema`)을 제공해야 합니다. |

#### <a name="example-model"></a>예제 모델

이 섹션에는 DTDL 인터페이스로 작성된 기본 모델의 예제가 포함되어 있습니다. 

이 모델은 ID에 대해 하나의 **속성** 을 사용하여 Home을 설명합니다. 또한 Home 모델은 Floor 모델에 대한 **관계** 를 정의합니다. 이는 Home 트윈이 특정 Floor 트윈에 연결되었음을 나타내는 데 사용할 수 있습니다.

:::code language="json" source="~/digital-twins-docs-samples-getting-started/models/basic-home-example/IHome.json":::

## <a name="properties-and-telemetry"></a>속성 및 원격 분석

이 섹션에서는 DTDL 모델의 **속성** 및 **원격 분석** 에 대해 자세히 설명합니다.

속성의 일부로 표시될 수 있는 전체 필드 목록은 [DTDL v2 사양의 속성](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md#property)을 참조하세요. 원격 분석의 일부로 표시될 수 있는 전체 필드 목록은 [DTDL v2 사양의 원격 분석](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md#telemetry)을 참조하세요.

> [!NOTE]
> `writable`속성의 dtdl 특성은 현재 Azure Digital Twins에서 지원 되지 않습니다. 모델에 추가할 수 있지만 Azure Digital Twins는이를 적용 하지 않습니다. 자세한 내용은 [Azure Digital Twins DTDL 구현 세부](#azure-digital-twins-dtdl-implementation-specifics)정보를 참조 하세요.

### <a name="difference-between-properties-and-telemetry"></a>속성과 원격 분석의 차이점

다음은 Azure Digital Twins의 DTDL **속성** 과 **원격 분석** 을 개념적으로 구분하는 몇 가지 지침입니다.
* **속성** 에는 백업 스토리지가 있어야 합니다. 즉, 언제든지 속성을 읽고 값을 검색할 수 있습니다. 속성에 쓸 수 있으면 속성에 값을 저장할 수도 있습니다.  
* **원격 분석** 은 수명이 짧은 데이터 메시지 집합인 이벤트 스트림과 매우 유사합니다. 이벤트 수신 대기 및 이벤트 발생 시 수행할 작업을 설정하지 않은 경우 나중에 이벤트를 추적하지 않습니다. 나중에 다시 돌아가서 읽을 수 없습니다. 
  - C# 용어로 원격 분석은 C# 이벤트와 비슷합니다. 
  - IoT 용어로 원격 분석은 일반적으로 디바이스에서 전송되는 단일 측정값입니다.

대부분의 디바이스는 생성하는 측정값을 저장할 수 없거나 저장하지 않으므로 IoT 디바이스에서 **원격 분석** 을 사용하는 경우가 많습니다. 대신, 디바이스는 "원격 분석" 이벤트 스트림으로 측정값을 전송합니다. 이 경우 언제든지 원격 분석 필드의 최신 값에 대해 디바이스를 쿼리할 수 없습니다. 디바이스에서 메시지를 수신 대기하고 메시지가 도착할 때 작업을 수행해야 합니다. 

결과적으로, Azure Digital Twins에서 모델을 설계할 때 대부분의 경우 **속성** 을 사용하여 트윈을 모델링합니다. 이렇게 하면 백업 스토리지 및 데이터 필드를 읽고 쿼리하는 기능을 사용할 수 있습니다.

원격 분석 및 속성은 종종 디바이스에서 수신되는 데이터를 처리하기 위해 함께 작동합니다. Azure Digital Twins로의 모든 수신은 [API](concepts-apis-sdks.md)를 통해 발생하므로 일반적으로 수신 함수를 사용하여 디바이스에서 원격 분석 또는 속성 이벤트를 읽고 Azure Digital Twins의 속성을 응답으로 설정합니다. 

Azure Digital Twins API에서 원격 분석 이벤트를 게시할 수도 있습니다. 다른 원격 분석과 마찬가지로 이는 수신기가 처리해야 하는 수명이 짧은 이벤트입니다.

### <a name="schema"></a>스키마

DTDL에 따라 **속성** 및 **원격 분석** 특성의 스키마는 표준 기본 형식(`integer`, `double`, `string` 및 `boolean`) 및 기타 형식(`dateTime` 및 `duration`)이 될 수 있습니다. 

기본 형식 외에도 속성 및 원격 분석 필드에는 다음과 같은 [복합 형식](#complex-object-type-example)이 있을 수 있습니다.
* `Object`
* `Map`
* `Enum`
* (**원격 분석** 만 해당) `Array`

단위로 값에 주석을 지정할 수 있는 [의미 체계 형식](#semantic-type-example)일 수도 있습니다.

### <a name="basic-property-and-telemetry-examples"></a>기본 속성 및 원격 분석 예제

다음은 DTDL 모델의 **속성** 에 대한 기본 예입니다. 이 예제에서는 Home의 ID 속성을 보여 줍니다.

:::code language="json" source="~/digital-twins-docs-samples-getting-started/models/basic-home-example/IHome.json" highlight="7-11":::

다음은 DTDL 모델에서 **원격 분석** 필드의 기본 예입니다. 이 예제에서는 센서의 온도 원격 분석을 보여 줍니다.

:::code language="json" source="~/digital-twins-docs-samples-getting-started/models/basic-home-example/ISensor.json" highlight="7-11":::

### <a name="complex-object-type-example"></a>복합(개체) 형식 예제

속성 및 원격 분석은 `Object` 형식을 비롯한 복합 형식일 수 있습니다.

다음 예제에서는 해당 주소에 대한 속성이 있는 Home 모델의 다른 버전을 보여 줍니다. `address`는 거리, 도시, 주 및 우편 번호에 대한 자체 필드가 있는 개체입니다.

:::code language="json" source="~/digital-twins-docs-samples-getting-started/models/advanced-home-example/IHome.json" highlight="8-31":::

### <a name="semantic-type-example"></a>의미 체계 형식 예제

의미 체계 형식을 통해 단위로 값을 표현할 수 있습니다. 속성 및 원격 분석은 DTDL에서 지원하는 의미 체계 형식으로 나타낼 수 있습니다. DTDL의 의미 체계 형식 및 지원되는 값에 대한 자세한 내용은 [DTDL v2 사양의 의미 체계 형식](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md#semantic-types)을 참조하세요.

다음 예제에서는 온도에 대한 의미 체계 형식 원격 분석이 있는 센서 모델 및 습도에 대한 의미 체계 형식 속성을 보여 줍니다.

:::code language="json" source="~/digital-twins-docs-samples-getting-started/models/advanced-home-example/ISensor.json" highlight="7-18":::

## <a name="relationships"></a>관계

이 섹션에서는 DTDL 모델의 **관계** 에 대해 자세히 설명합니다.

관계의 일부로 표시될 수 있는 전체 필드 목록은 [DTDL v2 사양의 관계](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md#relationship)를 참조하세요.

> [!NOTE]
> `writable`관계에 `minMultiplicity` 대한 , 및 `maxMultiplicity` DTDL 특성은 현재 Azure Digital Twins 지원되지 않습니다. 모델에 추가할 수 있지만 Azure Digital Twins 적용하지 않습니다. 자세한 내용은 [Azure Digital Twins DTDL 구현 세부 정보를 참조하세요.](#azure-digital-twins-dtdl-implementation-specifics)

### <a name="basic-relationship-example"></a>기본 관계 예제

다음은 DTDL 모델의 관계에 대한 기본 예입니다. 이 예제에서는 Home 모델에서 Floor 모델에 연결할 수 있는 관계를 보여 줍니다.

:::code language="json" source="~/digital-twins-docs-samples-getting-started/models/basic-home-example/IHome.json" highlight="12-18":::

>[!NOTE]
>관계의 경우 `@id`는 선택적 필드입니다. `@id`를 제공하지 않으면 디지털 쌍 인터페이스 프로세서에서 하나를 할당합니다.

### <a name="targeted-and-non-targeted-relationships"></a>대상 관계 및 대상이 아닌 관계

관계는 **대상** 을 지정하거나 지정하지 않고 정의할 수 있습니다. 대상은 관계가 도달할 수 있는 트윈 유형을 지정합니다. 예를 들어 Home 모델이 Floor 트윈인 트윈과 *rel_has_floors* 관계만 가질 수 있도록 지정하는 대상을 포함할 수 있습니다. 

경우에 따라 관계가 다양한 유형의 트윈에 연결할 수 있도록 특정 대상 없이 관계를 정의할 수 있습니다.

다음은 대상이 없는 DTDL 모델의 관계 예입니다. 이 예제에서 관계는 Room에 있을 수 있는 센서를 정의하기 위한 것이며 관계는 모든 형식에 연결할 수 있습니다.

:::code language="json" source="~/digital-twins-docs-samples-getting-started/models/advanced-home-example/IRoom.json" range="2-27" highlight="20-25":::

### <a name="properties-of-relationships"></a>관계의 속성

또한 DTDL은 **관계** 에 속성을 가질 수 있도록 합니다. DTDL 모델 내에서 관계를 정의하는 경우 관계는 사용자 지정 속성을 정의하여 관계 상태를 설명하는 고유한 `properties` 필드를 가질 수 있습니다.

다음 예제에서는 `rel_has_floors` 관계에 관련 Floor가 마지막으로 점유된 시점을 나타내는 속성이 있는 홈 모델의 다른 버전을 보여 줍니다.

:::code language="json" source="~/digital-twins-docs-samples-getting-started/models/advanced-home-example/IHome.json" highlight="39-45":::

## <a name="components"></a>구성 요소

이 섹션에서는 DTDL 모델의 **구성 요소** 에 대해 자세히 설명합니다.

구성 요소의 일부로 표시될 수 있는 전체 필드 목록은 [DTDL v2 사양의 구성 요소](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md#component)를 참조하세요.

### <a name="basic-component-example"></a>기본 구성 요소 예제

다음은 DTDL 모델의 구성 요소에 대한 기본 예입니다. 이 예제에서는 자동 온도 조절기 모델을 구성 요소로 사용하는 Room 모델을 보여 줍니다.

:::code language="json" source="~/digital-twins-docs-samples-getting-started/models/advanced-home-example/IRoom.json" highlight="15-19, 28-41":::

이 솔루션의 다른 모델에도 자동 온도 조절기를 포함해야 하는 경우에는 Room 모델처럼 동일한 자동 온도 조절기 모델을 자체 정의에서 구성 요소로 참조할 수 있습니다.

> [!IMPORTANT]
> 구성 요소 참조를 찾기 위해 구성 요소 인터페이스(위 예제의 자동 온도 조절기)는 사용하는 인터페이스와 동일한 배열에서 정의해야 합니다.

## <a name="model-inheritance"></a>모델 상속

경우에 따라 모델을 더 특수화할 수 있습니다. 예를 들어 일반 모델 Room및 특수화된 변형 ConferenceRoom 및 Gym을 포함하는 것이 유용할 수 있습니다. 특수화를 표현하기 위해 **DTDL은 상속을 지원합니다**. 인터페이스는 하나 이상의 인터페이스에서 상속할 수 있습니다. 모델에 `extends` 필드를 추가하면 됩니다.

`extends` 섹션은 인터페이스 이름이거나 인터페이스 이름의 배열입니다(확장 인터페이스가 여러 상위 모델에서 상속할 수 있도록 허용). 단일 부모가 여러 확장 인터페이스의 기본 모델로 사용될 수 있습니다.

다음 예제에서는 이전 DTDL 예제의 Home 모델을 더 큰 "Core" 모델의 하위 형식으로 다시 재구상합니다. 부모 모델을 먼저 정의한 다음, `extends`를 사용하여 이 모델에 자식 모델(Home)을 작성합니다.

:::code language="json" source="~/digital-twins-docs-samples-getting-started/models/advanced-home-example/ICore.json":::

:::code language="json" source="~/digital-twins-docs-samples-getting-started/models/advanced-home-example/IHome.json" range="1-8" highlight="6":::

이 경우 Core는 ID와 이름을 Home에 제공합니다. 다른 모델도 Core 모델을 확장하여 이러한 속성을 얻을 수 있습니다. 동일한 부모 인터페이스를 확장하는 Room 모델은 다음과 같습니다.

:::code language="json" source="~/digital-twins-docs-samples-getting-started/models/advanced-home-example/IRoom.json" range="2-9" highlight="6":::

상속이 적용되면 확장 인터페이스는 전체 상속 체인의 모든 속성을 노출합니다.

확장 인터페이스는 상위 인터페이스의 정의를 변경할 수 없고 추가만 할 수 있습니다. 또한 기능이 동일하게 정의된 경우에도 상위 인터페이스에 이미 정의된 기능을 다시 정의할 수 없습니다. 예를 들어 상위 인터페이스가 `double` 속성 *mass* 를 정의하는 경우 확장 인터페이스 또한 `double`인 경우에도 *mass* 선언을 포함할 수 없습니다.

## <a name="modeling-best-practices"></a>모델링 모범 사례

사용자 환경에서 엔터티를 반영하도록 모델을 설계할 때 설계의 [쿼리](concepts-query-language.md) 의미를 미리 확인하는 것이 유용할 수 있습니다. 그래프 순회에서 많은 결과 집합을 방지하는 방식으로 속성을 설계할 수 있습니다. 단일 쿼리에서 단일 수준 관계로 응답해야 하는 관계를 모델링할 수도 있습니다.

### <a name="validating-models"></a>모델 유효성 검사

[!INCLUDE [Azure Digital Twins: validate models info](../../includes/digital-twins-validate.md)]

## <a name="modeling-tools"></a>모델링 도구

모델 및 온톨로지를 더 쉽게 처리할 수 있는 몇 가지 샘플이 있습니다. 이 샘플은 [DTDL(디지털 트윈 정의 언어) 도구](https://github.com/Azure/opendigitaltwins-tools) 리포지토리에 있습니다.

이 섹션에서는 최신 샘플 집합에 대해 자세히 설명합니다.

### <a name="model-uploader"></a>모델 업로더 

모델 만들기, 확장 또는 선택이 완료되면 솔루션에서 사용할 수 있도록 모델을 Azure Digital Twins 인스턴스에 업로드할 수 있습니다. 이 작업은 [방법: DTDL 모델 관리](how-to-manage-model.md#upload-models)에 설명된 대로 [Azure Digital Twins API](concepts-apis-sdks.md)를 사용하여 수행합니다.

그러나 업로드할 모델이 많이 있거나 개별 업로드를 복잡하게 하는 상호 종속성이 많은 경우 [Azure Digital Twins 모델 업로더 샘플](https://github.com/Azure/opendigitaltwins-tools/tree/master/ADTTools#uploadmodels)을 사용하여 여러 모델을 한 번에 업로드할 수 있습니다. 샘플에서 제공하는 지침에 따라 이 프로젝트를 구성하고 사용하여 사용자 고유의 인스턴스에 모델을 업로드합니다.

### <a name="model-visualizer"></a>모델 시각화 도우미 

Azure Digital Twins 인스턴스에 모델을 업로드한 후에는 [Azure Digital Twins 모델 시각화 도우미](https://github.com/Azure/opendigitaltwins-building-tools/tree/master/AdtModelVisualizer)를 사용하여 Azure Digital Twins 인스턴스에서 상속 및 모델 관계를 포함하여 모델을 볼 수 있습니다. 이 샘플은 현재 초안 상태입니다. 당사는 디지털 트윈 개발 커뮤니티가 샘플을 확장하고 기여하도록 권장합니다. 

## <a name="next-steps"></a>다음 단계

* 업계 표준 온톨로지를 기반으로 모델을 만드는 방법에 대해 알아봅니다. [온톨로지란?](concepts-ontologies.md)

* API 작업을 사용하여 모델 관리에 대해 자세히 알아봅니다. [DTDL 모델 관리](how-to-manage-model.md)

* 모델을 사용하여 디지털 트윈을 만드는 방법에 대해 알아봅니다. [디지털 트윈 및 트윈 그래프](concepts-twins-graph.md)

