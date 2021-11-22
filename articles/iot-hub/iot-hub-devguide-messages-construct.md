---
title: Azure IoT Hub 메시징 형식 이해 | Microsoft Docs
description: 개발자 가이드 - IoT Hub 메시지의 형식 및 예상된 콘텐츠를 설명합니다.
author: ash2017
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 11/19/2021
ms.author: asrastog
ms.custom:
- 'Role: Cloud Development'
- 'Role: IoT Device'
ms.openlocfilehash: 048f478094e2397d18adb285af9dd969ea5daa0b
ms.sourcegitcommit: 6f30424a4ab8dffc4e690086e898ab52bc4da777
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/22/2021
ms.locfileid: "132901722"
---
# <a name="create-and-read-iot-hub-messages"></a>IoT Hub 메시지 만들기 및 읽기

프로토콜을 통해 원활한 상호 운용성을 지원하기 위해 IoT Hub는 모든 디바이스 지향 프로토콜에 대한 일반적인 메시지 형식을 정의합니다. 이 메시지 형식은 [디바이스-클라우드 라우팅](iot-hub-devguide-messages-d2c.md) 및 [클라우드-디바이스](iot-hub-devguide-messages-c2d.md) 메시지에 둘 다 사용됩니다. 

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

IoT Hub는 스트리밍 메시징 패턴을 사용하여 디바이스-클라우드 메시징을 구현합니다. IoT Hub의 디바이스-클라우드 메시지는 여러 독자가 읽을 수 있는 서비스를 통과하는 많은 양의 이벤트가 있다는 점에서 [Service Bus](../service-bus-messaging/index.yml) ‘메시지’보다는 [Event Hubs](../event-hubs/index.yml) ‘이벤트’에 가깝습니다. 

IoT Hub 메시지는 다음으로 구성됩니다.

* 아래에 나열된 미리 결정된 *시스템 속성* 집합

* *애플리케이션 속성* 집합. 메시지 본문을 역직렬화할 필요 없이 애플리케이션이 정의하고 액세스할 수 있는 문자열 속성 사전입니다. IoT Hub는 절대로 이러한 속성을 수정하지 않습니다.

* 불투명한 이진 본문.

HTTPS 프로토콜을 사용하여 디바이스-클라우드 메시지를 보내거나 클라우드-디바이스 메시지를 보낼 때 속성 이름과 값에는 ASCII 영숫자 문자와 ``{'!', '#', '$', '%, '&', ''', '*', '+', '-', '.', '^', '_', '`', '|', '~'}``만 포함할 수 있습니다.

IoT Hub를 사용한 디바이스-클라우드 메시징의 특징은 다음과 같습니다.

* 디바이스-클라우드 메시지는 영구적이며 IoT Hub의 기본 **messages/events** 엔드포인트에 최대 7일 동안 보관됩니다.

* 디바이스-클라우드 메시지는 최대 256KB까지 가능하며 보내기를 최적화하기 위해 일괄 처리로 그룹화할 수 있습니다. Batch는 최대 256KB가 될 수 있습니다.

* IoT Hub는 임의 분할을 허용하지 않습니다. 디바이스-클라우드 메시지는 원래 **deviceId** 와 관련하여 분할됩니다.

* [IoT Hub에 대한 액세스 제어](iot-hub-devguide-security.md)에서 설명한 것처럼, IoT Hub는 디바이스 단위 인증 및 액세스 제어를 허용합니다.

* 애플리케이션 속성에 제공되는 정보를 사용하여 메시지를 스탬프할 수 있습니다. 자세한 내용은 [메시지 보강](iot-hub-message-enrichments-overview.md)을 참조하세요.

다른 프로토콜을 사용하여 보낸 메시지를 인코딩하고 디코딩하는 방법에 대한 자세한 내용은 [Azure IoT SDK](iot-hub-devguide-sdks.md)를 참조하세요.

## <a name="system-properties-of-d2c-iot-hub-messages"></a>**D2C** IoT Hub 메시지의 시스템 속성

| 속성 | Description  |사용자 설정 가능 여부|for 키워드 </br>라우팅 쿼리|
| --- | --- | --- | --- |
| message-id |사용자가 설정할 수 있는 메시지에 대한 식별자는 요청-회신 패턴에 사용됩니다. 형식: ASCII 7 비트 영숫자 문자 + `{'-', ':', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '$', '''}`의 대/소문자 구분 문자열(최대 길이 128자)입니다.  | 예 | messageId |
| iothub-enqueuedtime |IoT Hub에서 [디바이스-클라우드](iot-hub-devguide-d2c-guidance.md) 메시지를 수신한 날짜 및 시간입니다. | 예 | enqueuedTime |
| user-id |메시지의 원본을 지정하는 데 사용되는 ID입니다. 메시지가 IoT Hub에서 생성되면 `{iot hub name}`로 설정합니다. | 예 | userId |
| iothub-connection-device-id |IoT Hub에서 디바이스-클라우드 메시지에 설정하는 ID입니다. 메시지를 보낸 디바이스의 **deviceId** 를 포함합니다. | 예 | connectionDeviceId |
| iothub-connection-module-id |IoT Hub에서 디바이스-클라우드 메시지에 설정하는 ID입니다. 메시지를 보낸 디바이스의 **moduleId** 를 포함합니다. | 예 | connectionModuleId |
| iothub-connection-auth-generation-id |IoT Hub에서 디바이스-클라우드 메시지에 설정하는 ID입니다. 메시지를 보낸 디바이스의 **connectionDeviceGenerationId**([디바이스 ID 속성](iot-hub-devguide-identity-registry.md#device-identity-properties)당)를 포함합니다. | 예 |connectionDeviceGenerationId |
| iothub-connection-auth-method |IoT Hub에서 디바이스-클라우드 메시지에 설정하는 인증 방법입니다. 이 속성에는 메시지를 보내는 디바이스를 인증하는 데 사용되는 인증 방법에 대한 정보가 포함됩니다.| 예 | connectionAuthMethod |
| iothub-iothub-utc | 장치에서 일괄 처리로 데이터를 보낼 때 이벤트 생성 시간을 보낼 수 있습니다. | 예 | 만든 시간-utc |
| iothub-creation-time-utc | 장치에서 한 번에 하나의 메시지를 보낼 때 이벤트 생성 시간을 보낼 수 있습니다. | 예 | 만든 시간-utc |
| dt-dataschema | 이 값은 디바이스-클라우드 메시지의 IoT 허브에 의해 설정됩니다. 디바이스 연결에 설정된 디바이스 모델 ID 세트를 포함합니다. | 예 | $dt-dataschema |
| dt-subject | 디바이스-클라우드 메시지를 전송하는 구성 요소의 이름입니다. | 예 | $dt-subject |

## <a name="application-properties-of-d2c-iot-hub-messages"></a>**D2C** IoT Hub 메시지의 애플리케이션 속성

애플리케이션 속성의 일반적인 용도는 `iothub-creation-time-utc` 속성을 사용하여 디바이스에서 메시지를 보낸 시간을 기록하는 타임스탬프를 디바이스에서 보내는 것입니다. 이 타임스탬프의 형식은 표준 시간대 정보가 없는 UTC여야 합니다. 예를 들어 `2021-04-21T11:30:16Z`는 유효하고 `2021-04-21T11:30:16-07:00`은 잘못된 것입니다.

```json
{
  "applicationId":"5782ed70-b703-4f13-bda3-1f5f0f5c678e",
  "messageSource":"telemetry",
  "deviceId":"sample-device-01",
  "schema":"default@v1",
  "templateId":"urn:modelDefinition:mkuyqxzgea:e14m1ukpn",
  "enqueuedTime":"2021-01-29T16:45:39.143Z",
  "telemetry":{
    "temperature":8.341033560421833
  },
  "messageProperties":{
    "iothub-creation-time-utc":"2021-01-29T16:45:39.021Z"
  },
  "enrichments":{}
}
```

## <a name="system-properties-of-c2d-iot-hub-messages"></a>**C2D** IoT Hub 메시지의 시스템 속성

| 속성 | Description  |사용자 설정 가능 여부|
| --- | --- | --- |
| message-id |사용자가 설정할 수 있는 메시지에 대한 식별자는 요청-회신 패턴에 사용됩니다. 형식: ASCII 7 비트 영숫자 문자 + `{'-', ':', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '$', '''}`의 대/소문자 구분 문자열(최대 길이 128자)입니다.  |예|
| sequence-number |숫자(디바이스 큐 별로 고유함)는 IoT Hub에서 각 클라우드-디바이스 메시지에 할당됩니다. |예|
| to |[클라우드-디바이스](iot-hub-devguide-c2d-guidance.md) 메시지에 지정된 대상입니다. |예|
| absolute-expiry-time |메시지 만료 날짜 및 시간입니다. |예| 
| correlation-id |일반적으로 요청-응답 패턴으로 요청의 MessageId가 포함된 응답 메시지의 String 속성입니다. |예|
| user-id |메시지의 원본을 지정하는 데 사용되는 ID입니다. 메시지가 IoT Hub에서 생성되면 `{iot hub name}`로 설정합니다. |예|
| iothub-ack |피드백 메시지 생성기입니다. 이 속성은 디바이스에서 소비하는 메시지의 결과로 피드백 메시지를 생성하는 IoT Hub를 요청하기 위해 클라우드-디바이스 메시지에서 사용됩니다. 가능한 값: **none**(기본값): 피드백 메시지가 생성되지 않습니다. **positive**: 메시지가 완료된 경우 피드백 메시지를 받습니다. **negative**: 디바이스에서 완료되지 않고 메시지가 만료(또는 최대 전달 횟수에 도달)되면 피드백 메시지를 받습니다. **full**: positive 및 negative 모두입니다. |예|

### <a name="system-property-names"></a>시스템 속성 이름

시스템 속성 이름은 메시지가 라우팅되는 엔드포인트에 따라 다릅니다. 이러한 이름에 대한 자세한 내용은 아래 표를 참조하세요.

|시스템 속성 이름|Event Hubs|Azure Storage|Service Bus|Event Grid|
|--------------------|----------|-------------|-----------|----------|
|메시지 ID|message-id|messageId|MessageId|message-id|
|User id|user-id|userId|UserId|user-id|
|Connection device id|iothub-connection-device-id| connectionDeviceId|iothub-connection-device-id|iothub-connection-device-id|
|Connection module id|iothub-connection-module-id|connectionModuleId|iothub-connection-module-id|iothub-connection-module-id|
|Connection auth generation id|iothub-connection-auth-generation-id|connectionDeviceGenerationId| iothub-connection-auth-generation-id|iothub-connection-auth-generation-id|
|Connection auth method|iothub-connection-auth-method|connectionAuthMethod|iothub-connection-auth-method|iothub-connection-auth-method|
|contentType|content-type|contentType|ContentType|iothub-content-type|
|contentEncoding|content-encoding|contentEncoding|ContentEncoding|iothub-content-encoding|
|iothub-enqueuedtime|iothub-enqueuedtime|enqueuedTime| 해당 없음 |iothub-enqueuedtime|
|CorrelationId|correlation-id|correlationId|CorrelationId|correlation-id|
|dt-dataschema|dt-dataschema|dt-dataschema|dt-dataschema|dt-dataschema|
|dt-subject|dt-subject|dt-subject|dt-subject|dt-subject|

## <a name="message-size"></a>메시지 크기

IoT Hub는 실제 페이로드만을 고려하여 프로토콜 진단 방식으로 메시지 크기를 측정합니다. 크기(바이트 단위)는 다음 값의 합계로 계산됩니다.

* 본문 크기(바이트)입니다.
* 모든 메시지 시스템 속성 값의 크기(바이트)입니다.
* 모든 사용자 속성 이름 및 값의 크기(바이트 단위)

속성 이름과 값은 ASCII 문자로 제한되므로 문자열의 길이는 바이트 단위의 크기와 같습니다.

## <a name="anti-spoofing-properties"></a>스푸핑 방지 속성

디바이스-클라우드 메시지에서 스푸핑된 디바이스를 피하려면 IoT Hub는 다음 속성을 사용하여 모든 메시지를 보여줍니다.

* **iothub-connection-device-id**
* **iothub-connection-auth-generation-id**
* **iothub-connection-auth-method**

처음 두 가지는 [디바이스 ID 속성](iot-hub-devguide-identity-registry.md#device-identity-properties)에 따라 원래 디바이스의 **deviceId** 및 **generationId** 를 포함합니다.

**iothub-connection-auth-method** 속성은 다음 속성을 가진 JSON으로 직렬화된 개체를 포함합니다.

```json
{
  "scope": "{ hub | device }",
  "type": "{ symkey | sas | x509 }",
  "issuer": "iothub"
}
```

## <a name="next-steps"></a>다음 단계

* IoT Hub의 메시지 크기 제한에 대한 자세한 내용은 [IoT Hub 할당량 및 제한](iot-hub-devguide-quotas-throttling.md)을 참조하세요.

* 다양한 프로그래밍 언어로 IoT Hub 메시지를 만들고 읽는 방법을 알아보려면 [빠른 시작](../iot-develop/quickstart-send-telemetry-iot-hub.md?pivots=programming-language-nodejs)을 참조하세요.