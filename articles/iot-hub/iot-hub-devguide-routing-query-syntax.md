---
title: Azure IoT Hub 메시지 라우팅에 대한 쿼리 | Microsoft Docs
description: 중요한 데이터를 수신하기 위해 메시지에 다양한 쿼리를 적용하는 데 사용할 수 있는 IoT Hub 메시지 라우팅 쿼리 언어에 대해 알아봅니다.
author: ash2017
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/13/2018
ms.author: asrastog
ms.custom:
- 'Role: Cloud Development'
- 'Role: Data Analytics'
ms.openlocfilehash: 7d56a9747627bd81e9bc0cc72fce804a64af91e4
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/12/2021
ms.locfileid: "132398082"
---
# <a name="iot-hub-message-routing-query-syntax"></a>IoT Hub 메시지 라우팅 쿼리 구문

메시지 라우팅을 사용하면 사용자가 디바이스 원격 분석 메시지, 디바이스 수명 주기 이벤트 및 디바이스 쌍 변경 이벤트 등의 여러 데이터 형식을 다양한 엔드포인트로 라우팅할 수 있습니다. 또한 이 데이터를 라우팅하기 전에 다양한 쿼리를 적용하여 사용자에게 중요한 데이터를 수신할 수 있습니다. 이 문서에서는 IoT Hub 메시지 라우팅 쿼리 언어를 설명하고 몇 가지 일반적인 쿼리 패턴을 제공합니다.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

메시지 라우팅을 사용하면 메시지 속성 및 메시지 본문뿐만 아니라 디바이스 쌍 태그 및 디바이스 쌍 속성에 대해서도 쿼리할 수 있습니다. 메시지 본문이 JSON이 아닌 경우 메시지 라우팅에서 메시지를 계속 라우팅할 수 있지만 메시지 본문에 쿼리를 적용할 수는 없습니다.  쿼리는 부울 식으로 기술되는데, 부울 true는 쿼리가 모든 들어오는 데이터의 라우팅에 성공한 것이고, 부울 false는 쿼리에 실패하여 라우팅된 데이터가 없는 것입니다. 식이 null 또는 정의되지 않은 것으로 평가되면 false로 처리되고 실패한 경우 IoT Hub [경로 리소스 로그](monitor-iot-hub-reference.md#routes)에 오류가 생성됩니다. 저장하고 평가할 경로에 대해 올바른 쿼리 구문을 사용해야 합니다.  

## <a name="message-routing-query-based-on-message-properties"></a>메시지 속성에 따른 메시지 라우팅 쿼리 

IoT Hub는 프로토콜 전체에서의 상호 운용성을 위해 모든 디바이스-클라우드 메시징에 대해 [일반적인 형식](iot-hub-devguide-messages-construct.md)을 정의합니다. IoT Hub 메시지는 메시지의 다음 JSON 표현을 가정합니다. 시스템 속성은 모든 사용자에 대해 추가되며 메시지의 콘텐츠를 식별합니다. 사용자는 선택적으로 메시지에 애플리케이션 속성을 추가할 수 있습니다. IoT Hub 디바이스-클라우드 메시징이 대/소문자를 구분하지 않으므로 고유한 속성 이름을 사용하는 것이 좋습니다. 예를 들어, 이름이 같은 속성이 여러 개 있는 경우 IoT Hub는 속성 중 하나만 전송합니다.  

```json
{ 
  "message": { 
    "systemProperties": { 
      "contentType": "application/json", 
      "contentEncoding": "UTF-8", 
      "iothub-message-source": "deviceMessages", 
      "iothub-enqueuedtime": "2017-05-08T18:55:31.8514657Z" 
    }, 
    "appProperties": { 
      "processingPath": "{cold | warm | hot}", 
      "verbose": "{true, false}", 
      "severity": 1-5, 
      "testDevice": "{true | false}" 
    }, 
    "body": "{\"Weather\":{\"Temperature\":50}}" 
  } 
} 
```

### <a name="system-properties"></a>시스템 속성

시스템 속성을 사용하면 메시지의 콘텐츠 및 소스를 식별할 수 있습니다. 

| 속성 | 유형 | Description |
| -------- | ---- | ----------- |
| contentType | 문자열 | 사용자가 메시지의 콘텐츠 형식을 지정합니다. 메시지 본문에 대한 쿼리를 허용하려면 이 값이 application/JSON으로 설정되어야 합니다. |
| contentEncoding | 문자열 | 사용자가 메시지의 인코딩 형식을 지정합니다. 허용되는 값은 contentType이 application/JSON으로 설정된 경우 UTF-8, UTF-16, UTF-32입니다. |
| iothub-connection-device-id | 문자열 | 이 값은 IoT Hub에 의해 설정되며 디바이스의 ID를 식별합니다. 쿼리하려면 `$connectionDeviceId`을 사용합니다. |
| iothub-connection-module-id | 문자열 | 이 값은 IoT Hub에 의해 설정되며 에지 모듈의 ID를 식별합니다. 쿼리하려면 `$connectionModuleId`을 사용합니다. |
| iothub-enqueuedtime | 문자열 | 이 값은 IoT Hub에 의해 설정되며 UTC에서 메시지를 큐에 넣는 실제 시간을 나타냅니다. 쿼리하려면 `enqueuedTime`을 사용합니다. |
| dt-dataschema | 문자열 |  이 값은 디바이스-클라우드 메시지의 IoT 허브에 의해 설정됩니다. 디바이스 연결에 설정된 디바이스 모델 ID 세트를 포함합니다. 쿼리하려면 `$dt-dataschema`을 사용합니다. |
| dt-subject | 문자열 | 디바이스-클라우드 메시지를 전송하는 구성 요소의 이름입니다. 쿼리하려면 `$dt-subject`을 사용합니다. |

[IoT Hub 메시지](iot-hub-devguide-messages-construct.md)에 설명된 대로, 메시지에 추가적인 시스템 속성에 있습니다. 위의 표에 나와 있는 위의 속성 외에도 **connectionDeviceId**, **connectionModuleId** 를 쿼리할 수 있습니다.

### <a name="application-properties"></a>애플리케이션 속성

애플리케이션 속성은 메시지에 추가할 수 있는 사용자 정의 문자열입니다. 이러한 필드는 선택 사항입니다.  

### <a name="query-expressions"></a>쿼리 식

메시지 시스템 속성에 대한 쿼리는 접두사로 `$` 기호를 사용해야 합니다. 애플리케이션 속성에 대한 쿼리는 이름으로 액세스하며 `$` 기호를 접두사로 사용하지 않아야 합니다. 애플리케이션 속성 이름이 `$`로 시작하는 경우, IoT Hub는 시스템 속성에서 해당 항목을 검색하며, 찾을 수 없으면 애플리케이션 속성에서 찾습니다. 예를 들면 다음과 같습니다. 

시스템 속성 contentEncoding에서 쿼리 

```sql
$contentEncoding = 'UTF-8'
```

애플리케이션 속성 processingPath에서 쿼리:

```sql
processingPath = 'hot'
```

이러한 쿼리를 결합하려면 부울 식 및 함수를 사용하면 됩니다.

```sql
$contentEncoding = 'UTF-8' AND processingPath = 'hot'
```

지원되는 연산자와 함수의 전체 목록은 [식 및 조건](iot-hub-devguide-query-language.md#expressions-and-conditions)을 참조하세요.

## <a name="message-routing-query-based-on-message-body"></a>메시지 본문에 따른 메시지 라우팅 쿼리

메시지 본문에 대한 쿼리를 사용하려면 메시지가 UTF-8, UTF-16 또는 UTF-32로 인코딩된 JSON 형식이어야 합니다. `contentType`은 `application/JSON`으로 설정되고 `contentEncoding`은 시스템 속성에서 지원되는 UTF 인코딩 중 하나로 설정되어야 합니다. 이러한 속성을 지정하지 않은 경우 IoT Hub는 메시지 본문에 대해 쿼리 식을 평가하지 않습니다. 

다음 예제에서는 올바르게 구성되고 인코딩된 JSON 본문을 사용하여 메시지를 만드는 방법을 보여 줍니다. 

```javascript
var messageBody = JSON.stringify(Object.assign({}, {
    "Weather": {
        "Temperature": 50,
        "Time": "2017-03-09T00:00:00.000Z",
        "PrevTemperatures": [
            20,
            30,
            40
        ],
        "IsEnabled": true,
        "Location": {
            "Street": "One Microsoft Way",
            "City": "Redmond",
            "State": "WA"
        },
        "HistoricalData": [
            {
                "Month": "Feb",
                "Temperature": 40
            },
            {
                "Month": "Jan",
                "Temperature": 30
            }
        ]
    }
}));

// Encode message body using UTF-8  
var messageBytes = Buffer.from(messageBody, "utf8");

var message = new Message(messageBytes);

// Set message body type and content encoding 
message.contentEncoding = "utf-8";
message.contentType = "application/json";

// Add other custom application properties   
message.properties.add("Status", "Active");

deviceClient.sendEvent(message, (err, res) => {
    if (err) console.log('error: ' + err.toString());
    if (res) console.log('status: ' + res.constructor.name);
});
```

> [!NOTE] 
> javascript에서 본문 인코딩을 처리하는 방법을 보여 줍니다. C#에서 샘플을 보려는 경우 [Azure IoT C# 샘플](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/main.zip)을 다운로드합니다. master.zip 파일의 압축을 풉니다. Visual Studio solution *SimulatedDevice* 의 Program.cs 파일은 메시지를 인코딩하여 IoT Hub로 제출하는 방법을 보여 줍니다. [메시지 라우팅 자습서](tutorial-routing.md)에 설명된 것처럼 메시지 라우팅을 테스트하는 데 사용되는 것과 동일한 샘플입니다. 또한 Program.cs의 맨 아래에는 인코딩된 파일 중 하나에서 읽고 디코드하고 ASCII로 다시 작성하여 읽을 수 있도록 하는 메서드가 있습니다. 

### <a name="query-expressions"></a>쿼리 식

메시지 본문에 대 한 쿼리에는 접두사가와 야 `$body` 합니다. 쿼리 식에 본문 참조, 본문 배열 참조 또는 여러 본문 참조를 사용할 수 있습니다. 또한 쿼리 식은 본문 참조를 메시지 시스템 속성 및 메시지 애플리케이션 속성 참조와 결합할 수 있습니다. 예를 들어 다음은 모든 유효한 쿼리 식입니다.

```sql
$body.Weather.HistoricalData[0].Month = 'Feb' 
```

```sql
$body.Weather.Temperature = 50 AND $body.Weather.IsEnabled 
```

```sql
length($body.Weather.Location.State) = 2 
```

```sql
$body.Weather.Temperature = 50 AND processingPath = 'hot'
```

> [!NOTE]
> 변경 된 내용에 따라 쌍 알림 페이로드를 필터링 하려면 메시지 본문에 대해 쿼리를 실행 합니다.
>
> ```sql
> $body.properties.desired.telemetryConfig.sendFrequency
> ```

> [!NOTE]
> 쿼리 및 함수는 본문 참조의 속성에 대해서만 실행할 수 있습니다. 전체 본문 참조에서 쿼리 또는 함수를 실행할 수 없습니다. 예를 들어 다음 쿼리는 *지원되지* 않으며 `undefined`를 반환합니다.
>
> ```sql
> $body[0] = 'Feb'
> ```

## <a name="message-routing-query-based-on-device-twin"></a>디바이스 쌍에 따른 메시지 라우팅 쿼리 

메시지 라우팅을 사용하면 JSON 개체인 [디바이스 쌍](iot-hub-devguide-device-twins.md) 태그 및 속성에서 쿼리를 사용할 수 있습니다. 모듈 쌍에 대한 쿼리도 지원됩니다. 디바이스 쌍 태그 및 속성의 샘플은 다음과 같습니다.

```JSON
{
    "tags": { 
        "deploymentLocation": { 
            "building": "43", 
            "floor": "1" 
        } 
    }, 
    "properties": { 
        "desired": { 
            "telemetryConfig": { 
                "sendFrequency": "5m" 
            }, 
            "$metadata" : {...}, 
            "$version": 1 
        }, 
        "reported": { 
            "telemetryConfig": { 
                "sendFrequency": "5m", 
                "status": "success" 
            },
            "batteryLevel": 55, 
            "$metadata" : {...}, 
            "$version": 4 
        } 
    } 
} 
```

### <a name="query-expressions"></a>쿼리 식

메시지 쌍에 대한 쿼리는 접두사로 `$twin`을 사용해야 합니다. 또한 쿼리 식은 쌍 태그 또는 속성 참조를 본문 참조, 메시지 시스템 속성 및 메시지 애플리케이션 속성 참조와 결합할 수 있습니다. 쿼리가 대/소문자를 구분하지 않으므로 태그 및 속성에 고유한 이름을 사용하는 것이 좋습니다. 이는 디바이스 쌍과 모듈 쌍 둘 다에 적용됩니다. 또한 속성 이름으로 `twin`, `$twin`, `body` 또는 `$body`를 사용하지 않도록 합니다. 예를 들어 다음은 모든 유효한 쿼리 식입니다. 

```sql
$twin.properties.desired.telemetryConfig.sendFrequency = '5m'
```

```sql
$body.Weather.Temperature = 50 AND $twin.properties.desired.telemetryConfig.sendFrequency = '5m'
```

```sql
$twin.tags.deploymentLocation.floor = 1 
```

페이로드 또는 속성 이름에 마침표가 있는 디바이스 쌍 또는 본문에 대한 라우팅 쿼리는 지원되지 않습니다.

## <a name="next-steps"></a>다음 단계

* [메시지 라우팅](iot-hub-devguide-messages-d2c.md)에 대해 알아봅니다.
* [메시지 라우팅 자습서](tutorial-routing.md)를 사용해 봅니다.
