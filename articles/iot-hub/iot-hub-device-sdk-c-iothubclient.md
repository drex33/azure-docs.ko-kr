---
title: C용 Azure IoT 디바이스 SDK – IoTHubClient | Microsoft Docs
description: C용 Azure IoT 디바이스 SDK에서 IoTHubClient 라이브러리를 사용하여 IoT Hub와 통신하는 디바이스 앱을 만드는 방법입니다.
author: eross-msft
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: conceptual
ms.date: 08/29/2017
ms.author: lizross
ms.custom: amqp
ms.openlocfilehash: 7ee5cc4f068eefcf5da13fac7f2cfc1eddee2ca8
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132555986"
---
# <a name="azure-iot-device-sdk-for-c--more-about-iothubclient"></a>C용 Azure IoT 디바이스 SDK – IoTHubClient에 대한 자세한 정보

[C용 Azure IoT 디바이스 SDK](iot-hub-device-sdk-c-intro.md)는 이 시리즈의 첫 번째 문서로, **C용 Azure IoT 디바이스 SDK** 를 소개합니다. 이 문서에서는 SDK에 두 개의 아키텍처 계층이 있음을 설명했습니다. 맨 하단은 IoT Hub와의 통신을 직접 관리하는 **IoTHubClient** 라이브러리입니다. 직렬화 서비스를 제공하기 위해 위쪽에 구축되는 **serializer** 라이브러리도 있습니다. 이 문서에서는 **IoTHubClient** 라이브러리에 대한 추가 세부 정보를 제공합니다.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

이전 문서에서는 **IoTHubClient** 라이브러리를 사용하여 이벤트를 IoT Hub로 전송하고 메시지를 수신하는 방법을 설명했습니다. 이 문서에서는 *하위 수준 API* 를 소개하여 데이터를 전송 및 수신하는 **시간** 을 보다 정확하게 관리하는 방법에 대한 설명으로 논의를 확대합니다. 또한 **IoTHubClient** 라이브러리의 속성 처리 기능을 사용하여 이벤트에 속성을 첨부하고 메시지에서 검색하는 방법도 설명합니다. 마지막으로 IoT Hub에서 수신한 메시지를 처리하는 다양한 방법에 대한 추가 설명을 제공합니다.

디바이스 자격 증명에 대한 자세한 정보 및 구성 옵션을 통해 **IoTHubClient** 의 동작을 변경하는 방법 등 몇 가지 기타 항목을 다루는 것으로 문서를 마무리합니다.

이 항목을 설명하기 위해 **IoTHubClient** SDK 샘플을 사용하겠습니다. 따라서 따라하려면 C용 Azure IoT 디바이스 SDK에 포함된 **iothub\_client\_sample\_http** 및 **iothub\_client\_sample\_amqp** 애플리케이션을 참조하세요. 다음 섹션에 설명된 모든 내용은 이 샘플에 나와 있습니다.

GitHub 리포지토리에서 [**C용 Azure IoT 디바이스 SDK**](https://github.com/Azure/azure-iot-sdk-c)를 찾고 [C API 참조](/azure/iot-hub/iot-c-sdk-ref/)에서 API의 세부 정보를 볼 수 있습니다.

## <a name="the-lower-level-apis"></a>하위 수준 API

이전 문서에서는 **iothub\_client\_sample\_amqp** 애플리케이션 컨텍스트 내에 있는 **IotHubClient** 의 기본적인 동작을 설명했습니다. 예를 들어 다음 코드를 사용하여 라이브러리를 초기화하는 방법을 설명했습니다.

```C
IOTHUB_CLIENT_HANDLE iotHubClientHandle;
iotHubClientHandle = IoTHubClient_CreateFromConnectionString(connectionString, AMQP_Protocol);
```

또한 다음 함수 호출을 사용하여 이벤트를 전송하는 방법도 설명했습니다.

```C
IoTHubClient_SendEventAsync(iotHubClientHandle, message.messageHandle, SendConfirmationCallback, &message);
```

이 문서에서는 콜백 함수를 등록하여 메시지를 수신하는 방법도 설명했습니다.

```C
int receiveContext = 0;
IoTHubClient_SetMessageCallback(iotHubClientHandle, ReceiveMessageCallback, &receiveContext);
```

이 문서에서는 다음과 같은 코드를 사용하여 리소스를 확보하는 방법도 보여줬습니다.

```C
IoTHubClient_Destroy(iotHubClientHandle);
```

이러한 각 API에는 도우미 함수가 있습니다.

* IoTHubClient\_LL\_CreateFromConnectionString
* IoTHubClient\_LL\_SendEventAsync
* IoTHubClient\_LL\_SetMessageCallback
* IoTHubClient\_LL\_Destroy

이러한 함수는 모두 API 이름에 **LL** 을 포함합니다. 이름의 **LL** 부분 외에, 이러한 각 함수의 매개 변수는 비-LL 해당 항목과 동일합니다. 그러나 이러한 함수의 동작은 한 가지 중요한 방식에서 서로 다릅니다.

**IoTHubClient\_CreateFromConnectionString** 을 호출하면 기본 라이브러리에서 백그라운드로 실행되는 새 스레드를 만듭니다. 이 스레드는 IoT Hub로 이벤트 전송 및 메시지 수신을 처리합니다. 이러한 스레드는 **LL** API로 작업할 때 만들어지지 않습니다. 백그라운드 스레드를 생성하면 개발자가 편리합니다. 이벤트 전송 및 IoT Hub에서 메시지 수신을 명시적으로 지정하는 것에 대해 신경 쓰지 않아도 됩니다. 백그라운드로 자동으로 이루어지기 때문입니다. 이와 반대로, **LL** API는 필요한 경우 IoT Hub와의 통신을 명시적으로 제어합니다.

이 개념을 보다 정확하게 이해하기 위해 다음 예제를 살펴보겠습니다.

**IoTHubClient\_SendEventAsync** 를 호출하는 경우 실제로 수행하는 작업은 이벤트를 버퍼에 넣는 것입니다. **IoTHubClient\_CreateFromConnectionString** 호출 시 생성되는 백그라운드 스레드는 이 버퍼를 지속적으로 모니터하고 여기에 포함된 모든 데이터를 IoT Hub로 전송합니다. 메인 스레드에서 다른 작업을 수행하는 동안 이 작업이 백그라운드로 수행됩니다.

마찬가지로 **IoTHubClient\_SetMessageCallback** 을 사용하여 메시지에 대한 콜백 함수를 등록하면 사용자는 SDK를 통해, 메시지를 수신할 때 백그라운드 스레드가 메인 스레드와 별개로 콜백 함수를 호출하도록 합니다.

**LL** API는 백그라운드 스레드를 만들지 않습니다. 대신, IoT Hub에서 데이터를 명시적으로 전송 및 수신하는 새 API를 호출해야 합니다. 다음 예제에 이 내용이 나와 있습니다.

SDK에 포함된 **iothub\_client\_sample\_http** 애플리케이션은 하위 수준 API를 보여줍니다. 이 샘플에서는 다음과 같은 코드로 IoT Hub에 이벤트를 전송합니다.

```C
EVENT_INSTANCE message;
sprintf_s(msgText, sizeof(msgText), "Message_%d_From_IoTHubClient_LL_Over_HTTP", i);
message.messageHandle = IoTHubMessage_CreateFromByteArray((const unsigned char*)msgText, strlen(msgText));

IoTHubClient_LL_SendEventAsync(iotHubClientHandle, message.messageHandle, SendConfirmationCallback, &message)
```

처음 세 줄은 메시지를 작성하고 마지막 줄은 이벤트를 전송합니다. 그러나 이전에 언급한 대로 이벤트를 전송하는 것은 데이터를 단순히 버퍼에 넣는 것을 의미합니다. **IoTHubClient\_LL\_SendEventAsync** 를 호출할 때 네트워크에 전송되는 것은 아무 것도 없습니다. IoT Hub로 데이터를 실제로 수신하려면 이 예에서처럼 **IoTHubClient\_LL\_DoWork** 를 호출해야 합니다.

```C
while (1)
{
    IoTHubClient_LL_DoWork(iotHubClientHandle);
    ThreadAPI_Sleep(100);
}
```

이 코드(**iothub\_client\_sample\_http** 애플리케이션에 있음)는 **IoTHubClient\_LL\_DoWork** 를 반복적으로 호출합니다. **IoTHubClient\_LL\_DoWork** 가 호출될 때마다 버퍼에서 IoT Hub로 몇 개의 이벤트가 전송되고 디바이스로 전송된 대기 중인 메시지를 가져옵니다. 후자의 경우 메시지에 대한 콜백 함수를 등록한 후 콜백을 호출했음을 의미합니다(모든 메시지가 대기 상태임을 가정). 다음과 같은 코드로 이러한 콜백 함수를 등록했습니다.

```C
IoTHubClient_LL_SetMessageCallback(iotHubClientHandle, ReceiveMessageCallback, &receiveContext)
```

루프에서 **IoTHubClient\_LL\_DoWork** 를 자주 호출하는 이유는 호출할 때마다 *일부* 버퍼링된 이벤트를 IoT Hub로 전송하고 디바이스에 대해 대기 상태인 *다음* 메시지를 가져오기 때문입니다. 호출 시마다 버퍼링된 모든 이벤트를 전송하거나 대기 상태인 모든 메시지를 가져온다고 보장할 수 없습니다. 버퍼의 모든 이벤트를 전송한 후 다른 처리를 진행하려면 다음과 같은 코드로 이 루프를 바꾸면 됩니다.

```C
IOTHUB_CLIENT_STATUS status;

while ((IoTHubClient_LL_GetSendStatus(iotHubClientHandle, &status) == IOTHUB_CLIENT_OK) && (status == IOTHUB_CLIENT_SEND_STATUS_BUSY))
{
    IoTHubClient_LL_DoWork(iotHubClientHandle);
    ThreadAPI_Sleep(100);
}
```

이 코드는 버퍼의 모든 이벤트가 IoT Hub로 전송될 때까지 **IoTHubClient\_LL\_DoWork** 를 호출합니다. 이 또한 대기 상태인 모든 메시지를 수신함을 의미하지 않습니다. 이것은 "모든" 메시지를 확인하는 것으로 동작이 결정되는 것은 아니기 때문입니다. 가령, "모든" 메시지를 가져온 직후 디바이스에 다른 메시지가 전송된다면 어떻게 될까요? 이를 처리하기 위한 더 나은 방법은 프로그래밍 방식의 시간 제한을 사용하는 것입니다. 예를 들어, 메시지 콜백 함수가 호출될 때마다 타이머를 재설정할 수 있습니다. 그런 다음 예를 들어, 마지막 *X* 초에 수신된 메시지가 없으면 처리를 진행하는 논리를 작성할 수 있습니다.

이벤트 수신 및 메시지 수신을 완료했으면 해당 함수를 호출하여 리소스를 정리해야 합니다.

```C
IoTHubClient_LL_Destroy(iotHubClientHandle);
```

기본적으로 백그라운드 스레드로 데이터를 전송 및 수신하는 API 집합이 하나만 있으며 백그라운드 스레드 없이 동일한 작업을 수행하는 API 집합도 있습니다. 많은 개발자들이 비-LL API를 선호하지만, 개발자가 네트워크 전송을 명시적으로 제어하고자 하는 경우에는 하위 수준 API가 유용합니다. 예를 들어, 일부 디바이스에서 시간에 따라 데이터를 수집하고 지정된 간격(예: 1시간에 한 번, 하루에 한 번)으로만 이벤트를 수신합니다. 하위 수준 API는 IoT Hub에서 데이터 전송 및 수신 시 명시적으로 제어하는 기능을 제공합니다. 하위 수준 API가 제공하는 단순성만을 선호하는 사람도 있습니다. 일부 작업이 백그라운드로 수행되지 않고 모두 메인 스레드에서 수행됩니다.

어떤 모델을 선택하든 사용하는 API와 일관되어야 합니다. **IoTHubClient\_LL\_CreateFromConnectionString** 을 호출하여 시작하는 경우 후속 작업에 해당하는 하위 수준 API만 사용해야 합니다.

* IoTHubClient\_LL\_SendEventAsync
* IoTHubClient\_LL\_SetMessageCallback
* IoTHubClient\_LL\_Destroy
* IoTHubClient\_LL\_DoWork

반대의 경우도 마찬가지입니다. **IoTHubClient\_CreateFromConnectionString** 으로 시작한 후 비-LL API를 사용하여 추가 처리를 계속합니다.

C용 Azure IoT 디바이스 SDK에서 하위 수준 API에 대한 전체 예제는 **iothub\_client\_sample\_http** 애플리케이션을 참조하세요. 비-LL API의 전체 예제는 **iothub\_client\_sample\_amqp** 애플리케이션을 참조하면 됩니다.

## <a name="property-handling"></a>속성 처리

지금까지 데이터 전송에 대해 설명할 때 메시지 본문을 참조했습니다. 다음 코드를 예로 들 수 있습니다.

```C
EVENT_INSTANCE message;
sprintf_s(msgText, sizeof(msgText), "Hello World");
message.messageHandle = IoTHubMessage_CreateFromByteArray((const unsigned char*)msgText, strlen(msgText));
IoTHubClient_LL_SendEventAsync(iotHubClientHandle, message.messageHandle, SendConfirmationCallback, &message)
```

이 예에서는 "Hello World"라는 텍스트와 함께 IoT Hub에 메시지를 전송합니다. 그리고 IoT Hub는 각 메시지에 속성을 첨부할 수도 있습니다. 속성은 메시지에 첨부할 수 있는 이름/값 쌍입니다. 예를 들어, 메시지에 속성을 추가하도록 이전 코드를 수정할 수 있습니다.

```C
MAP_HANDLE propMap = IoTHubMessage_Properties(message.messageHandle);
sprintf_s(propText, sizeof(propText), "%d", i);
Map_AddOrUpdate(propMap, "SequenceNumber", propText);
```

**IoTHubMessage\_Properties** 를 호출하고 메시지의 핸들을 전달하는 것으로 시작합니다. 다음으로 볼 것은 속성 추가를 시작할 수 있는 **MAP\_HANDLE** 참조입니다. 후자는 MAP\_HANDLE에 대한 참조, 속성 이름 및 속성 값을 사용하는 **Map\_AddOrUpdate** 를 호출하여 수행됩니다. 이 API를 통해 속성을 원하는 만큼 추가할 수 있습니다.

**Event Hubs** 에서 이벤트를 읽을 때 수신기는 속성을 열거하고 해당 값을 가져옵니다. 예를 들어, .NET에서는 [EventData 개체의 속성 컬렉션](/dotnet/api/microsoft.servicebus.messaging.eventdata)에 액세스하여 이를 수행합니다.

이전 예제에서 IoT Hub로 전송하는 이벤트에 속성을 첨부합니다. IoT Hub에서 수신한 메시지에도 속성을 첨부할 수 있습니다. 메시지에서 속성을 가져오려면 메시지 콜백 함수에 다음과 같은 코드를 사용할 수 있습니다.

```C
static IOTHUBMESSAGE_DISPOSITION_RESULT ReceiveMessageCallback(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
{
    . . .

    // Retrieve properties from the message
    MAP_HANDLE mapProperties = IoTHubMessage_Properties(message);
    if (mapProperties != NULL)
    {
        const char*const* keys;
        const char*const* values;
        size_t propertyCount = 0;
        if (Map_GetInternals(mapProperties, &keys, &values, &propertyCount) == MAP_OK)
        {
            if (propertyCount > 0)
            {
                printf("Message Properties:\r\n");
                for (size_t index = 0; index < propertyCount; index++)
                {
                    printf("\tKey: %s Value: %s\r\n", keys[index], values[index]);
                }
                printf("\r\n");
            }
        }
    }

    . . .
}
```

**IoTHubMessage\_Properties** 를 호출하면 **MAP\_HANDLE** 참조를 반환합니다. 그런 다음 **Map\_GetInternals** 로 참조를 전달하여 이름/값 쌍의 배열에 대한 참조를 얻습니다(속성 개수 포함). 이 때 속성을 열거하여 원하는 값을 얻는 것은 간단합니다.

애플리케이션에서 속성을 사용할 필요가 없습니다. 하지만 이벤트에 속성을 설정하거나 메시지에서 속성을 가져와야 하는 경우 **IoTHubClient** 라이브러리로 쉽게 작업할 수 있습니다.

## <a name="message-handling"></a>메시지 처리

앞서 설명한 것처럼 IoT Hub에서 메시지가 도착할 때 **IoTHubClient** 라이브러리가 등록된 콜백 함수를 호출하여 응답합니다. 이 함수에는 반환 매개 변수가 있으며 이에 대한 추가 설명이 필요합니다. 다음은 **iothub\_client\_sample\_http** 애플리케이션 예제에 있는 콜백 함수를 발췌한 것입니다.

```C
static IOTHUBMESSAGE_DISPOSITION_RESULT ReceiveMessageCallback(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
{
    . . .
    return IOTHUBMESSAGE_ACCEPTED;
}
```

반환 형식은 **IOTHUBMESSAGE\_DISPOSITION\_RESULT** 이며 이 특수한 경우에서는 **IOTHUBMESSAGE\_ACCEPTED** 를 반환합니다. 이 함수에서 다른 값을 반환할 수 있으며 이 값으로 **IoTHubClient** 라이브러리가 메시지 콜백에 반응하는 방식을 변경합니다. 옵션은 다음과 같습니다.

* **IOTHUBMESSAGE\_ACCEPTED** – 메시지가 성공적으로 처리되었습니다. **IoTHubClient** 라이브러리가 동일한 메시지로 콜백 함수를 다시 호출하지 않습니다.

* **IOTHUBMESSAGE\_REJECTED** – 메시지가 처리되지 않았으며 나중에도 처리되지 않습니다. **IoTHubClient** 라이브러리는 같은 메시지로 콜백 함수를 다시 호출하지 않아야 합니다.

* **IOTHUBMESSAGE\_ABANDONED** – 메시지가 성공적으로 처리되지 않았지만 **IoTHubClient** 라이브러리는 같은 메시지로 콜백 함수를 다시 호출해야 합니다.

처음 두 반환 코드에서 **IoTHubClient** 라이브러리는 IoT Hub에 메시지를 전송하며 해당 메시지를 디바이스의 큐에서 삭제하고 다시 전달하지 않아야 함을 나타냅니다. 최종 결과는 동일하지만(디바이스의 큐에서 메시지가 삭제됨) 메시지를 수락 또는 거부할지 여부는 계속 기록됩니다.  이러한 구분을 기록하는 것은 피드백을 수신 대기하고 디바이스가 특정 메시지를 수락 또는 거부했는지를 파악하는 메시지 보낸 사람에게 유용합니다.

마지막 경우에서 메시지는 IoT Hub로 전송되지만 메시지를 다시 전달해야 함을 나타냅니다. 일반적으로 일부 오류가 발생하지만 메시지를 다시 처리하려는 경우 메시지를 중단합니다. 이와 반대로, 복구할 수 없는 메시지가 발생하는 메시지를 거부하는 것이 적합합니다(또는 메시지를 처리하지 않기로 결정한 경우).

어떤 경우든 다양한 반환 코드를 파악하여 **IoTHubClient** 라이브러리에서 원하는 동작을 유도할 수 있습니다.

## <a name="alternate-device-credentials"></a>대체 디바이스 자격 증명

이전에 설명한 것처럼 **IoTHubClient** 라이브러리로 작업할 때 가장 먼저 할 일은 다음과 같은 호출로 **IOTHUB\_CLIENT\_HANDLE** 을 얻는 것입니다.

```C
IOTHUB_CLIENT_HANDLE iotHubClientHandle;
iotHubClientHandle = IoTHubClient_CreateFromConnectionString(connectionString, AMQP_Protocol);
```

**IoTHubClient\_CreateFromConnectionString** 인수는 IoT Hub와 통신하는 데 사용할 프로토콜을 나타내는 디바이스 연결 문자열 및 매개 변수입니다. 디바이스 연결 문자열은 다음과 같은 형식입니다.

```C
HostName=IOTHUBNAME.IOTHUBSUFFIX;DeviceId=DEVICEID;SharedAccessKey=SHAREDACCESSKEY
```

이 문자열에는 IoT Hub 이름, IoT Hub 접미사, 디바이스 ID 및 공유 액세스 키의 네 가지 정보가 있습니다. Azure 포털에서 IoT Hub 인스턴스를 만들 때 IoT Hub의 정규화된 도메인 이름(FQDN)을 가져옵니다. 여기서 IoT Hub 이름(FQDN의 첫 번째 부분) 및 IoT Hub 접미사(FQDN의 나머지 부분)가 제공됩니다. 디바이스를 IoT Hub에 등록할 때 디바이스 ID 및 공유 액세스 키를 가져옵니다([이전 문서](iot-hub-device-sdk-c-intro.md)에서 설명).

**IoTHubClient\_CreateFromConnectionString** 은 라이브러리를 초기화하는 한 가지 방법을 제공합니다. 원하는 경우 디바이스 연결 문자열 대신 개별 매개 변수를 사용하여 새 **IOTHUB\_CLIENT\_HANDLE** 을 만들 수 있습니다. 다음 코드로 이 작업을 수행합니다.

```C
IOTHUB_CLIENT_CONFIG iotHubClientConfig;
iotHubClientConfig.iotHubName = "";
iotHubClientConfig.deviceId = "";
iotHubClientConfig.deviceKey = "";
iotHubClientConfig.iotHubSuffix = "";
iotHubClientConfig.protocol = HTTP_Protocol;
IOTHUB_CLIENT_HANDLE iotHubClientHandle = IoTHubClient_LL_Create(&iotHubClientConfig);
```

**IoTHubClient\_CreateFromConnectionString** 과 동일하게 수행됩니다.

보다 구체적인 초기화 메서드보다 **IoTHubClient\_CreateFromConnectionString** 을 사용하려는 것이 분명해 보일 수 있습니다. 하지만 디바이스를 IoT Hub에 등록할 때 연결 문자열이 아닌, 디바이스 ID 및 디바이스 키를 얻게 되는 것을 유의해야 합니다. [이전 문서](iot-hub-device-sdk-c-intro.md)에서 소개한 *디바이스 Explorer* SDK 도구는 **Azure IoT 서비스 SDK** 의 라이브러리를 사용하여 디바이스 ID, 디바이스 키 및 IoT Hub 호스트 이름에서 디바이스 연결 문자열을 만듭니다. 따라서 **IoTHubClient\_LL\_Create** 를 호출하면 연결 문자열을 생성하는 단계가 필요 없으므로 더 적합할 수 있습니다. 어떤 것이든 편한 방법을 사용하세요.

## <a name="configuration-options"></a>구성 옵션

지금까지 **IoTHubClient** 라이브러리가 작동하는 방법에 대해 설명한 모든 내용은 기본 동작에 대한 것입니다. 하지만 라이브러리가 작동하는 방식을 변경하도록 설정 가능한 몇 가지 옵션이 있습니다. 이 작업은 **IoTHubClient\_LL\_SetOption** API를 활용하여 수행합니다. 다음 예제를 고려해 보세요.

```C
unsigned int timeout = 30000;
IoTHubClient_LL_SetOption(iotHubClientHandle, "timeout", &timeout);
```

일반적으로 사용되는 몇 가지 옵션이 있습니다.

* **SetBatching**(bool) – **true** 이면 IoT Hub로 전송된 데이터가 일괄 처리로 전송됩니다. **false** 이면 메시지가 개별적으로 전송됩니다. 기본값은 **false** 입니다. D2C 메시지에 시스템 속성을 추가하는 것은 물론 AMQP/AMQP-WS를 통한 일괄 처리가 지원됩니다.

* **Timeout** (unsigned int) – 이 값은 밀리초 단위로 표시됩니다. HTTPS 요청을 전송하고 응답을 수신하는 경우 이 시간보다 오래 걸리면 연결이 시간 초과됩니다.

일괄 처리 옵션은 중요합니다. 기본적으로 라이브러리는 개별적으로 이벤트를 수신합니다(**IoTHubClient\_LL\_SendEventAsync** 에 전달한 단일 이벤트). 일괄 처리 옵션이 **true** 인 경우 라이브러리는 버퍼에서 가져올 수 있는 만큼 이벤트를 수집합니다(IoT Hub에서 수락하는 최대 메시지 크기까지).  이벤트 일괄 처리는 단일 HTTPS 호출로 IoT Hub에 전송됩니다(개별 이벤트는 JSON 배열에 번들됨). 일괄 처리를 설정하면 네트워크 왕복이 감소되므로 성능상 이점이 상당합니다. 각 개별 이벤트에 대한 헤더 집합이 아닌, 이벤트 일괄 처리가 포함된 한 집합의 HTTPS 헤더를 전송하므로 대역폭도 크게 감소합니다. 다른 이유가 없는 한, 일반적으로 일괄 처리를 사용하도록 설정하는 것이 좋습니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 **C용 Azure IoT 디바이스 SDK** 에 있는 **IoTHubClient** 라이브러리의 동작에 대해 자세히 설명합니다. 이 정보를 사용하여 **IoTHubClient** 라이브러리의 기능에 대해 잘 알고 있어야 합니다. 

IoT Hub를 개발하는 방법에 대한 자세한 내용은 [Azure IoT SDK](iot-hub-devguide-sdks.md)를 참조하세요.

IoT Hub의 기능을 더 자세히 살펴보려면 [Azure IoT Edge를 사용하여 Edge 디바이스에 AI 배포](../iot-edge/quickstart-linux.md)를 참조하세요.