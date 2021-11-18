---
title: IoT Connector 디바이스 매핑 - Azure Healthcare API
description: 이 문서에서는 Azure Healthcare API IoT Connector 디바이스 매핑 템플릿을 구성하고 사용하는 방법을 설명합니다.
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: how-to
ms.date: 11/16/2021
ms.author: jasteppe
ms.openlocfilehash: 96b110b03f211d62a0ed778e40aa7a6ad8c8c3ce
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2021
ms.locfileid: "132733493"
---
# <a name="how-to-use-device-mappings"></a>디바이스 매핑을 사용하는 방법

> [!IMPORTANT]
> Azure Healthcare API는 현재 미리 보기로 제공됩니다. [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)에는 베타 또는 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 약관이 포함되어 있습니다.

> [!TIP]
> [IoT 커넥터](https://github.com/microsoft/iomt-fhir/tree/master/tools/data-mapper) 디바이스 및 FHIR 대상 매핑을 편집, 테스트 및 문제 해결을 위해 IoMT 커넥터 데이터 매퍼 도구를 확인하세요. Azure Portal IoT 커넥터에 업로드하기 위한 매핑을 내보내거나 [오픈 소스 버전의](https://github.com/microsoft/iomt-fhir) IoT 커넥터와 함께 사용합니다.

이 문서에서는 디바이스 매핑을 사용하여 IoT 커넥터를 구성하는 방법을 설명합니다.

IoT 커넥터에는 두 가지 유형의 JSON 기반 매핑이 필요합니다. 첫 번째 형식인 **디바이스 매핑은** `devicedata` Azure Event Hub 엔드포인트에 전송된 디바이스 페이로드를 매핑하는 것을 담당합니다. 형식, 디바이스 식별자, 측정 날짜 시간 및 측정값을 추출합니다. 

두 번째 형식인 **전자 의료 기록 교환(FHIR&#174;) 대상 매핑은** FHIR 리소스에 대한 매핑을 제어합니다. 관찰 기간의 길이, 값을 저장하는 데 사용되는 FHIR 데이터 형식 및 용어 코드를 구성할 수 있습니다. 

두 가지 유형의 매핑은 형식에 따라 JSON 문서로 구성됩니다. 그런 다음, 이러한 JSON 문서는 Azure Portal 통해 IoT 커넥터에 추가됩니다. 디바이스 매핑 문서는 **디바이스 매핑** 페이지와 FHIR 대상 매핑 문서를 통해 대상 페이지를 통해 **추가됩니다.**

> [!NOTE]
> 매핑은 기본 Blob Storage에 저장되고 컴퓨팅 실행당 Blob에서 로드됩니다. 업데이트되면 즉시 적용됩니다. 

## <a name="device-mappings-overview"></a>디바이스 매핑 개요

디바이스 매핑은 추가 평가를 위해 디바이스 메시지 콘텐츠를 일반적인 형식으로 추출하는 기능을 제공합니다. 받은 각 디바이스 메시지는 모든 디바이스 매핑 템플릿에 대해 평가됩니다. 

단일 인바운드 디바이스 메시지를 나중에 FHIR 서비스의 다른 관찰에 매핑되는 여러 아웃바운드 메시지로 구분할 수 있습니다. 

결과는 템플릿에서 구문 분석된 값 또는 값을 나타내는 정규화된 데이터 개체입니다. 

정규화된 데이터 모델에는 찾아서 추출해야 하는 몇 가지 필수 속성이 있습니다.

|속성|설명|
|--------|-----------|
|**Type**|측정값을 분류할 이름/형식입니다. 이 값은 필요한 FHIR 대상 매핑에 바인딩하는 데 사용됩니다. 여러 매핑이 동일한 형식으로 출력되어 여러 디바이스의 다양한 표현을 단일 공통 출력에 매핑할 수 있습니다.|
|**OccurenceTimeUtc**|측정이 발생한 시간입니다.|
|**DeviceId**|디바이스의 식별자입니다. 이 값은 대상 FHIR 서비스에 있는 디바이스 리소스의 식별자와 일치해야 합니다.|
|**속성**|생성된 Observation 리소스에 값을 저장할 수 있도록 하나 이상의 속성을 추출합니다. 속성은 정규화 중에 추출된 키 값 쌍의 컬렉션입니다.|

> [!IMPORTANT]
> 정규화된 전체 모델은 [IMeasurement](https://github.com/microsoft/iomt-fhir/blob/master/src/lib/Microsoft.Health.Fhir.Ingest.Schema/IMeasurement.cs) 인터페이스에 의해 정의됩니다.

다음은 IoT 커넥터 내에서 정규화 및 변환 프로세스 중에 발생하는 작업의 개념 예제입니다.

:::image type="content" source="media/iot-data-normalization-high-level.png" alt-text="IoT 데이터 정규화 흐름 예제1" lightbox="media/iot-data-normalization-high-level.png":::

:::image type="content" source="media/concepts-iot-mapping-templates/normalization-example.png" alt-text="IoT 데이터 정규화 흐름 예제2" lightbox="media/concepts-iot-mapping-templates/normalization-example.png":::

콘텐츠 페이로드 자체는 Azure Event Hub 메시지로, 본문, 속성 및 SystemProperties의 세 부분으로 구성됩니다. `Body`는 UTF-8로 인코딩된 문자열을 나타내는 바이트 배열입니다. 템플릿을 평가하는 동안 바이트 배열은 문자열 값으로 자동으로 변환됩니다. `Properties` 는 메시지 작성자가 사용할 키 값 컬렉션입니다. `SystemProperties` 는 Azure Event Hub 프레임워크에서 예약한 키 값 컬렉션이기도 하며 항목이 자동으로 채워집니다.

```json
{
    "Body": {
        "content": "value"
    },
    "Properties": {
        "key1": "value1",
        "key2": "value2"
    },
    "SystemProperties": {
        "x-opt-sequence-number": 1,
        "x-opt-enqueued-time": "2021-02-01T22:46:01.8750000Z",
        "x-opt-offset": 1,
        "x-opt-partition-key": "1"
    }
}
```
## <a name="mapping-with-jsonpath"></a>JSONPath를 통해 매핑

현재 지원되는 5가지 디바이스 콘텐츠 매핑 형식은 JSONPath를 사용하여 필요한 매핑 및 추출된 값과 일치합니다. JSONPath에 대한 자세한 내용은 여기에서 찾을 수 [있습니다.](https://goessner.net/articles/JsonPath/) 5가지 템플릿 형식은 모두 JSONPath 식을 해결하기 위해 JSON [.NET 구현을](https://www.newtonsoft.com/json/help/html/QueryJsonSelectTokenJsonPath.htm) 사용합니다.

디바이스 매핑 템플릿 내에서 하나 이상의 템플릿을 정의할 수 있습니다. 받은 각 이벤트 허브 디바이스 메시지는 모든 디바이스 매핑 템플릿에 대해 평가됩니다. 

단일 인바운드 디바이스 메시지를 나중에 FHIR 서비스의 다른 관찰에 매핑되는 여러 아웃바운드 메시지로 구분할 수 있습니다. 

다양한 템플릿 형식이 있으며 디바이스 매핑 파일을 빌드할 때 사용할 수 있습니다.

|이름                                                                     | 설명                                                                   |  
|-------------------------------------------------------------------------|-------------------------------------------------------------------------------|
|[JsonPathContentTemplate](./how-to-use-jsonpath-content-mappings.md)     |JsonPath를 사용하여 식 작성을 지원하는 템플릿                  
|[CollectionContentTemplate](./how-to-use-collection-content-mappings.md) |정규화 중에 사용할 템플릿 목록을 나타내는 데 사용되는 템플릿입니다.                                                            |                                                           
|[CalculatedContentTemplate](./how-to-use-calculated-functions-mappings.md)|여러 식 언어 중 하나를 사용하여 식을 작성할 수 있는 템플릿입니다. JmesPath 함수를 사용하여 데이터 변환을 지원합니다.|
|[IotJsonPathContentTemplate](./how-to-use-iot-jsonpath-content-mappings.md)|Azure Iot Hub 또는 Azure Iot Central의 레거시 데이터 내보내기 기능에서 보낸 메시지를 지원하는 템플릿입니다.|
|[IotCentralJsonPathContentTemplate](./how-to-use-iot-central-json-content-mappings.md)|Azure Iot Central의 데이터 내보내기 기능을 통해 전송된 메시지를 지원하는 템플릿입니다.|  

## <a name="next-steps"></a>다음 단계

이 문서에서는 디바이스 매핑을 사용하는 방법을 배웠습니다. FHIR 대상 매핑을 사용하는 방법을 알아보려면 다음을 참조하세요.

>[!div class="nextstepaction"]
>[FHIR 대상 매핑을 사용하는 방법](how-to-use-fhir-mappings.md)

(FHIR&#174;)는 HL7의 등록 상표이며 [HL7의](https://hl7.org/fhir/) 사용 권한으로 사용됩니다.
