---
title: IoT Connector 장치 매핑의 IotJsonPathContentTemplate 매핑-Azure 의료 api
description: 이 문서에서는 IoT Connector 장치 매핑 템플릿과 함께 IotJsonPathContentTemplate 매핑을 사용 하는 방법을 설명 합니다.
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: how-to
ms.date: 11/22/2021
ms.author: jasteppe
ms.openlocfilehash: 56304f99e786a06abdfa67495bea061d21ebabb4
ms.sourcegitcommit: 3d04177023a3136832adb561da831ccc8e9910c7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/23/2021
ms.locfileid: "132936509"
---
# <a name="how-to-use-iotjsonpathcontenttemplate-mappings"></a>IotJsonPathContentTemplate 매핑을 사용 하는 방법

> [!IMPORTANT]
> Azure 의료 Api는 현재 미리 보기로 제공 됩니다. [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)에는 베타 또는 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 약관이 포함되어 있습니다.

> [!TIP]
> IoT 커넥터 장치 및 FHIR 대상 매핑의 편집, 테스트 및 문제 해결을 위해 [IoMT 커넥터 데이터 매퍼](https://github.com/microsoft/iomt-fhir/tree/master/tools/data-mapper) 도구를 확인 하세요. Azure Portal에서 IoT connector로 업로드 하기 위한 매핑을 내보내거나, IoT 커넥터의 [오픈 소스 버전과](https://github.com/microsoft/iomt-fhir) 함께 사용 합니다.

이 문서에서는 IoT 커넥터 장치 매핑 템플릿과 함께 IoTJsonPathContentTemplate 매핑을 사용 하는 방법을 설명 합니다.

## <a name="iotjsonpathcontenttemplate"></a>IotJsonPathContentTemplate

IotJsonPathContentTemplate는를 제외 하 고 JsonPathContentTemplate와 유사 `DeviceIdExpression` 하며 `TimestampExpression` 반드시 필요한 것은 아닙니다.

이 템플릿을 사용할 때 평가 되는 메시지는 [Azure IoT 허브 장치 sdk](../../iot-hub/iot-hub-devguide-sdks.md#azure-iot-hub-device-sdks) 또는 [Azure IoT Central](../../iot-central/core/overview-iot-central.md)의 [데이터 내보내기 (레거시)](../../iot-central/core/howto-export-data-legacy.md) 기능을 사용 하 여 전송 된 것입니다. 

이러한 Sdk를 사용 하는 경우 메시지의 장치 id 및 타임 스탬프를 알 수 있습니다.

>[!IMPORTANT]
>대상 fhir 서비스의 장치 리소스에 대 한 식별자로 등록 된 Azure Iot Hub 또는 Azure IoT Central의 장치 식별자를 사용 하 고 있는지 확인 합니다.

Azure IoT 허브 장치 sdk를 사용 하는 경우 장치 id 또는 측정 타임 스탬프에 대 한 메시지 본문에 사용자 지정 속성을 사용 한다고 가정 하 고 JsonPathContentTemplate을 계속 사용할 수 있습니다.

> [!NOTE]
> 를 사용 하 `IotJsonPathContentTemplate` 는 경우는 `TypeMatchExpression` 전체 메시지를 jtoken로 확인 해야 합니다. 자세한 내용은 다음 예제를 참조 하세요.

### <a name="examples"></a>예

**하트 요금**

*Message*

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

**블러드 압력**

*Message*

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

> [!TIP]
> 일반적인 오류 및 문제 해결에 대 한 지원은 IoT 커넥터 [문제 해결 가이드](./iot-troubleshoot-guide.md) 를 참조 하세요.

## <a name="next-steps"></a>다음 단계

이 문서에서는 장치 매핑을 사용 하는 방법을 알아보았습니다. FHIR 대상 매핑을 사용 하는 방법에 대 한 자세한 내용은 다음을 참조 하세요.

>[!div class="nextstepaction"]
>[FHIR 대상 매핑을 사용 하는 방법](how-to-use-fhir-mappings.md)

(FHIR&#174;)는 [HL7](https://hl7.org/fhir/) 의 등록 상표 이며 HL7의 사용 권한과 함께 사용 됩니다.
