---
title: IoT Connector 디바이스 매핑의 IotCentralJsonPathContentTemplate 매핑 - Azure Healthcare API
description: 이 문서에서는 IoT Connector 디바이스 매핑 템플릿을 통해 IotCentralJsonPathContent를 매핑하는 방법을 설명합니다.
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: how-to
ms.date: 11/22/2021
ms.author: jasteppe
ms.openlocfilehash: 9fb3331e70d6a9e132f2d7cd96768249f0de83fb
ms.sourcegitcommit: 3d04177023a3136832adb561da831ccc8e9910c7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/23/2021
ms.locfileid: "132938857"
---
# <a name="how-to-use-iotcentraljsonpathcontenttemplate-mappings"></a>IotCentralJsonPathContentTemplate 매핑을 사용하는 방법

> [!IMPORTANT]
> Azure Healthcare API는 현재 미리 보기로 제공됩니다. [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)에는 베타 또는 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 약관이 포함되어 있습니다.

> [!TIP]
> [IoT 커넥터](https://github.com/microsoft/iomt-fhir/tree/master/tools/data-mapper) 디바이스 및 FHIR 대상 매핑을 편집, 테스트 및 문제 해결을 위해 IoMT 커넥터 데이터 매퍼 도구를 확인하세요. Azure Portal IoT 커넥터에 업로드하기 위한 매핑을 내보내거나 오픈 [소스 버전의](https://github.com/microsoft/iomt-fhir) IoT 커넥터와 함께 사용합니다.

이 문서에서는 IoT 커넥터 디바이스 매핑을 사용하여 IoTCentralJsonPathContentTemplate 매핑을 사용하는 방법을 설명합니다.

## <a name="iotcentraljsonpathcontenttemplate"></a>IotCentralJsonPathContentTemplate

IotCentralJsonPathContentTemplate에도 DeviceIdExpression 및 TimestampExpression이 필요하지 않습니다. 평가 중인 메시지가 [Azure IoT Central의](../../iot-central/core/overview-iot-central.md)데이터 [내보내기](../../iot-central/core/howto-export-data.md) 기능을 통해 전송될 때 사용됩니다. 

디바이스 ID 또는 측정 타임스탬프에 대한 메시지 본문에 Azure IoT Central의 데이터 내보내기 기능 및 사용자 지정 속성을 사용하는 경우에도 JsonPathContentTemplate을 사용할 수 있습니다.

> [!NOTE]
> 를 사용하는 경우 `IotCentralJsonPathContentTemplate` `TypeMatchExpression` 전체 메시지를 JToken으로 확인해야 합니다. 자세한 내용은 다음 예제를 참조하세요.
 
### <a name="examples"></a>예

**심박수**

*Message*

```json
{
    "applicationId": "1dffa667-9bee-4f16-b243-25ad4151475e",
    "messageSource": "telemetry",
    "deviceId": "1vzb5ghlsg1",
    "schema": "default@v1",
    "templateId": "urn:qugj6vbw5:___qbj_27r",
    "enqueuedTime": "2020-08-05T22:26:55.455Z",
    "telemetry": {
        "Activity": "running",
        "BloodPressure": {
            "Diastolic": 7,
            "Systolic": 71
        },
        "BodyTemperature": 98.73447010562934,
        "HeartRate": 88,
        "HeartRateVariability": 17,
        "RespiratoryRate": 13
    },
    "enrichments": {
      "userSpecifiedKey": "sampleValue"
    },
    "messageProperties": {
      "messageProp": "value"
    }
}
```

*템플릿*

```json
{
    "templateType": "IotCentralJsonPathContent",
    "template": {
        "typeName": "heartrate",
        "typeMatchExpression": "$..[?(@telemetry.HeartRate)]",
        "values": [
            {
                "required": "true",
                "valueExpression": "$.telemetry.HeartRate",
                "valueName": "hr"
            }
        ]
    }
}
```

**혈압**

*Message*

```json
{
    "applicationId": "1dffa667-9bee-4f16-b243-25ad4151475e",
    "messageSource": "telemetry",
    "deviceId": "1vzb5ghlsg1",
    "schema": "default@v1",
    "templateId": "urn:qugj6vbw5:___qbj_27r",
    "enqueuedTime": "2020-08-05T22:26:55.455Z",
    "telemetry": {
        "Activity": "running",
        "BloodPressure": {
            "Diastolic": 7,
            "Systolic": 71
        },
        "BodyTemperature": 98.73447010562934,
        "HeartRate": 88,
        "HeartRateVariability": 17,
        "RespiratoryRate": 13
    },
    "enrichments": {
      "userSpecifiedKey": "sampleValue"
    },
    "messageProperties": {
      "messageProp": "value"
    }
}
```

*템플릿*

```json
{
    "templateType": "IotCentralJsonPathContent",
    "template": {
        "typeName": "bloodPressure",
        "typeMatchExpression": "$..[?(@telemetry.BloodPressure.Diastolic && @telemetry.BloodPressure.Systolic)]",
        "values": [
            {
                "required": "true",
                "valueExpression": "$.telemetry.BloodPressure.Diastolic",
                "valueName": "bp_diastolic"
            },
            {
                "required": "true",
                "valueExpression": "$.telemetry.BloodPressure.Systolic",
                "valueName": "bp_systolic"
            }
        ]
    }
}
```

> [!TIP]
> 일반적인 오류 및 문제를 해결하는 데 도움이 필요하면 IoT 커넥터 [문제 해결 가이드를](./iot-troubleshoot-guide.md) 참조하세요.

## <a name="next-steps"></a>다음 단계

이 문서에서는 디바이스 매핑을 사용하는 방법을 배웠습니다. FHIR 대상 매핑을 사용하는 방법을 알아보려면 다음을 참조하세요.

>[!div class="nextstepaction"]
>[FHIR 대상 매핑을 사용하는 방법](how-to-use-fhir-mappings.md)

(FHIR&#174;)는 HL7의 등록 상표이며 [HL7의](https://hl7.org/fhir/) 사용 권한으로 사용됩니다.
