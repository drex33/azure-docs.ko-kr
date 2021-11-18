---
title: IoT Connector 디바이스 매핑의 IotJsonPathContentTemplate 매핑 - Azure Healthcare API
description: 이 문서에서는 IoT Connector 디바이스 매핑 템플릿에서 IotJsonPathContentTemplate 매핑을 사용하는 방법을 설명합니다.
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: how-to
ms.date: 11/16/2021
ms.author: jasteppe
ms.openlocfilehash: 6a20c1130156a18da7b18a9454b5a37232a609be
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2021
ms.locfileid: "132733572"
---
# <a name="how-to-use-iotjsonpathcontenttemplate-mappings"></a>IotJsonPathContentTemplate 매핑을 사용하는 방법

> [!IMPORTANT]
> Azure Healthcare API는 현재 미리 보기로 제공됩니다. [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)에는 베타 또는 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 약관이 포함되어 있습니다.

> [!TIP]
> [IoT 커넥터](https://github.com/microsoft/iomt-fhir/tree/master/tools/data-mapper) 디바이스 및 FHIR 대상 매핑을 편집, 테스트 및 문제 해결을 위해 IoMT 커넥터 데이터 매퍼 도구를 확인하세요. Azure Portal IoT 커넥터에 업로드하기 위한 매핑을 내보내거나 [오픈 소스 버전의](https://github.com/microsoft/iomt-fhir) IoT 커넥터와 함께 사용합니다.

이 문서에서는 IoT 커넥터 디바이스 매핑 템플릿을 사용하여 IoTJsonPathContentTemplate 매핑을 사용하는 방법을 설명합니다.

## <a name="iotjsonpathcontenttemplate"></a>IotJsonPathContentTemplate

IotJsonPathContentTemplate은 및 를 제외하고 JsonPathContentTemplate과 `DeviceIdExpression` `TimestampExpression` 유사합니다.

이 템플릿을 사용하는 경우 평가 중인 메시지가 Azure IoT [Central의](../../iot-central/core/overview-iot-central.md) [Azure IoT Hub 디바이스 SDK](../../iot-hub/iot-hub-devguide-sdks.md#azure-iot-hub-device-sdks) 또는 [데이터 내보내기(레거시)](../../iot-central/core/howto-export-data-legacy.md) 기능을 사용하여 전송되었다고 가정합니다. 

이러한 SDK를 사용하는 경우 메시지의 디바이스 ID 및 타임스탬프를 알 수 있습니다.

>[!IMPORTANT]
>대상 FHIR 서비스의 디바이스 리소스에 대한 식별자로 등록된 Azure Iot Hub 또는 Azure IoT Central의 디바이스 식별자를 사용하고 있는지 확인합니다.

Azure IoT Hub 디바이스 SDK를 사용하는 경우 디바이스 ID 또는 측정 타임스탬프에 대한 메시지 본문의 사용자 지정 속성을 사용한다고 가정하면 JsonPathContentTemplate을 계속 사용할 수 있습니다.

> [!NOTE]
> 를 사용하는 경우 `IotJsonPathContentTemplate` 는 `TypeMatchExpression` 전체 메시지로 JToken으로 확인되어야 합니다. 자세한 내용은 다음 예제를 참조하세요.

### <a name="examples"></a>예제

**심박수**

*메시지*

```json
{
    "Body": {
        "heartRate": "78"        
    },
    "Properties": {
        "iothub-creation-time-utc" : "2021-02-01T22:46:01.8750000Z"
    },
    "SystemProperties": {
        "iothub-connection-device-id" : "device123"
    }
}
```

*템플릿*

```json

    "templateType": "JsonPathContent",
    "template": {
        "typeName": "heartrate",
        "typeMatchExpression": "$..[?(@Body.heartRate)]",
        "deviceIdExpression": "$.deviceId",
        "timestampExpression": "$.endDate",
        "values": [
            {
                "required": "true",
                "valueExpression": "$.Body.heartRate",
                "valueName": "hr"
            }
        ]
    }
}
```

**혈압**

*메시지*

```json
{
    "Body": {
        "systolic": "123",
        "diastolic" : "87"
    },
    "Properties": {
        "iothub-creation-time-utc" : "2021-02-01T22:46:01.8750000Z"
    },
    "SystemProperties": {
        "iothub-connection-device-id" : "device123"
    }
}
```

*템플릿*

```json
{
    "typeName": "bloodpressure",
    "typeMatchExpression": "$..[?(@Body.systolic && @Body.diastolic)]",
    "values": [
        {
            "required": "true",
            "valueExpression": "$.Body.systolic",
            "valueName": "systolic"
        },
        {
            "required": "true",
            "valueExpression": "$.Body.diastolic",
            "valueName": "diastolic"
        }
    ]
}
```

## <a name="next-steps"></a>다음 단계

이 문서에서는 디바이스 매핑을 사용하는 방법을 배웠습니다. FHIR 대상 매핑을 사용하는 방법을 알아보려면 다음을 참조하세요.

>[!div class="nextstepaction"]
>[FHIR 대상 매핑을 사용하는 방법](how-to-use-fhir-mappings.md)

(FHIR&#174;)는 HL7의 등록 상표이며 [HL7의](https://hl7.org/fhir/) 사용 권한으로 사용됩니다.
