---
title: C용 Azure IoT 디바이스 SDK | Microsoft Docs
description: C용 Azure IoT 디바이스 SDK를 시작하고 IoT Hub로 통신하는 디바이스 앱을 만드는 방법에 대해 알아봅니다.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: conceptual
ms.date: 05/17/2019
ms.author: robinsh
ms.custom:
- amqp
- mqtt
- 'Role: Cloud Development'
- 'Role: IoT Device'
ms.openlocfilehash: 4da2e985e2d8f3fbc00fe9fcf3d31d42463bb4c1
ms.sourcegitcommit: 557ed4e74f0629b6d2a543e1228f65a3e01bf3ac
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/05/2021
ms.locfileid: "129455478"
---
# <a name="azure-iot-device-sdk-for-c"></a>C용 Azure IoT 디바이스 SDK

**Azure IoT 디바이스 SDK** 는 **Azure IoT Hub** 서비스와 메시지를 보내고 받는 프로세스를 간소화하도록 설계된 라이브러리 집합입니다. 각각 특정 플랫폼을 대상으로 하는 다양하게 변형된 SDK가 제공되지만 이 문서에서는 **C용 Azure IoT 디바이스 SDK** 를 설명합니다.

> [!NOTE]
> 임베디드 C SDK는 BYON(사용자 고유의 네트워크) 접근 방식을 지원하는 제한된 디바이스에 대한 대안입니다. IoT 개발자는 MQTT 클라이언트, TLS, 선택한 소켓을 가져와서 자유롭게 디바이스 솔루션을 만들 수 있습니다. [임베디드 C SDK에 대해 자세히 알아보세요](https://github.com/Azure/azure-sdk-for-c/tree/master/sdk/docs/iot).

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

C용 Azure IoT 디바이스 SDK는 이식성을 최대화하기 위해 ANSI C(C99)로 작성됩니다. 이 기능은 특히 디스크 및 메모리 사용 공간을 최소화하는 것이 우선 순위인 경우 라이브러리가 다양한 플랫폼 및 디바이스에서 작동하기에 적합하도록 만듭니다.

이 SDK는 광범위한 플랫폼에서 테스트되었습니다(자세한 내용은 [IoT용 Azure Certified 디바이스 카탈로그](https://devicecatalog.azure.com/) 참조). 이 문서에는 Windows 플랫폼에서 실행되는 샘플 코드 연습이 포함되어 있지만, 여기서 설명하는 코드는 지원되는 플랫폼 범위 전반에 걸쳐 정확히 동일합니다.

다음 비디오는 C:에 대한 Azure IoT SDK의 개요를 제공합니다.

>[!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Azure-IoT-C-SDK-insights/Player]

이 문서에서는 C용 Azure IoT 디바이스 SDK의 아키텍처를 소개하며, 디바이스 라이브러리를 초기화하고 IoT Hub와 데이터를 보내고 메시지를 받는 방법을 보여 줍니다. 이 문서의 정보로 SDK 사용을 시작하기에 충분하지만 라이브러리에 대한 추가 정보에 대한 포인터도 제공합니다.

## <a name="sdk-architecture"></a>SDK 아키텍처

GitHub 리포지토리에서 [**C용 Azure IoT 디바이스 SDK**](https://github.com/Azure/azure-iot-sdk-c)를 찾고 [C API 참조](/azure/iot-hub/iot-c-sdk-ref/)에서 API의 세부 정보를 볼 수 있습니다.

최신 버전의 라이브러리는 리포지토리의 **기본** 분기에서 찾을 수 있습니다.

  ![리포지토리의 기본 분기 스크린샷](./media/iot-hub-device-sdk-c-intro/RepoMasterBranch.png)

* SDK의 핵심 구현은 SDK의 최하위 API 계층인 **IoTHubClient** 라이브러리의 구현을 포함하고 있는 **iothub\_client** 폴더에 있습니다. **IoTHubClient** 라이브러리에는 IoT Hub와 메시지를 보내고 받기 위한 원시 메시징을 구현하는 API가 포함되어 있습니다. 이 라이브러리를 사용할 때 메시지 직렬화를 구현해야 하며 IoT Hub와 통신하기 위한 기타 세부 사항도 직접 처리해야 합니다.

* **serializer** 폴더에는 클라이언트 라이브러리를 사용하여 데이터를 Azure IoT Hub로 보내기 전에 직렬화하는 방법을 보여 주는 도우미 함수와 샘플이 있습니다. serializer(직렬 변환기)는 반드시 사용할 필요가 없으며, 편의상 제공되는 것입니다. **serializer** 라이브러리를 사용하려면 IoT Hub로 보낼 데이터와 IoT Hub에서 받으려는 메시지를 지정하는 모델을 정의합니다. 모델이 정의되면 SDK에서 API 표면을 제공하므로 직렬화 세부 정보에 대해 고민하지 않고도 디바이스-클라우드 및 클라우드-디바이스 메시지 작업을 쉽게 수행할 수 있습니다. 라이브러리는 프로토콜(예: MQTT, AMQP)을 사용하여 전송을 구현하는 다른 오픈 소스 라이브러리를 기반으로 합니다.

* **IoTHubClient** 라이브러리는 다른 오픈 소스 라이브러리에 따라 달라집니다.

  * [Azure C 공유 유틸리티](https://github.com/Azure/azure-c-shared-utility) 라이브러리 - 여러 Azure 관련 C SDK에서 필요한 기본 작업(예: 문자열, 목록 조작, IO 등)에 공통 기능을 제공합니다.

  * [Azure uAMQP](https://github.com/Azure/azure-uamqp-c) 라이브러리 - 리소스가 제한된 디바이스에 맞게 최적화된 AMQP의 클라이언트 쪽 구현입니다.

  * [Azure uMQTT](https://github.com/Azure/azure-umqtt-c) 라이브러리 - 리소스가 제한된 디바이스에 맞게 최적화되고 MQTT 프로토콜을 구현하는 범용 라이브러리입니다.

예제 코드를 살펴보면 이러한 라이브러리를 더 쉽게 이해하여 사용할 수 있습니다. 다음 섹션에서는 SDK에 포함된 몇 가지 샘플 애플리케이션을 단계별로 안내합니다. 이 연습을 통해 SDK 아키텍처 계층의 다양한 기능에 대해 쉽게 설명하고 API 작동 방식을 소개합니다.

## <a name="before-you-run-the-samples"></a>샘플을 실행하기 전에

C 용 Azure IoT 디바이스 SDK에서 샘플을 실행하려면 먼저 Azure 구독에서 [IoT Hub 서비스의 인스턴스를 만들어야](iot-hub-create-through-portal.md) 합니다. 그리고 나서 다음 작업을 완료합니다.

* 개발 환경 준비
* 디바이스 자격 증명 가져오기

### <a name="prepare-your-development-environment"></a>개발 환경 준비

패키지는 일반적인 플랫폼(예: Windows용 NuGet 또는 Debian 및 Ubuntu용 apt_get)을 위해 제공되며, 샘플에서는 사용 가능한 경우 이러한 패키지를 사용합니다. 경우에 따라 SDK를 디바이스용으로 또는 디바이스에서 컴파일해야 합니다. SDK를 컴파일해야 하는 경우 GitHub 리포지토리에 있는 [개발 환경 준비](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md)(영문)를 참조하세요.

샘플 애플리케이션 코드를 얻으려면 GitHub에서 SDK 복사본을 다운로드합니다. [GitHub 리포지토리](https://github.com/Azure/azure-iot-sdk-c)의 **기본** 분기에서 원본의 복사본을 가져옵니다.


### <a name="obtain-the-device-credentials"></a>디바이스 자격 증명 가져오기

이제 샘플 원본 코드가 있으므로 다음으로 수행할 작업은 디바이스 자격 증명 집합을 가져오는 것입니다. IoT Hub에 액세스할 수 있는 디바이스의 경우 디바이스를 IoT Hub ID 레지스트리에 먼저 추가해야 합니다. 디바이스를 추가하면 디바이스를 IoT Hub에 연결하는 데 필요한 디바이스 자격 증명 집합을 얻게 됩니다. 다음 섹션에서 살펴볼 샘플 애플리케이션에서는 이러한 자격 증명에 대해 **디바이스 연결 문자열** 형식을 필요로 합니다.

IoT Hub를 관리하는 데 도움이 되는 몇 가지 오픈 소스 도구가 있습니다.

* [Azure IoT Explorer](https://github.com/Azure/azure-iot-explorer)라는 Windows 애플리케이션입니다.

* [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)라는 플랫폼 간 Visual Studio Code 확장

* [Azure CLI에 대한 IoT 확장](https://github.com/Azure/azure-iot-cli-extension)이라고 하는 플랫폼 간 Python CLI 도구

이 자습서에서는 그래픽 *디바이스 탐색기* 도구를 사용합니다. VS Code에서 개발하는 경우 *Azure IoT Tools for VS Code* 를 사용할 수 있습니다. CLI 도구를 사용하려는 경우 *Azure CLI 2.0용 IoT 확장* 도구를 사용할 수도 있습니다.

디바이스 탐색기 도구는 Azure IoT 서비스 라이브러리를 사용하여 IoT Hub에서 디바이스 추가를 포함하여 다양한 기능을 수행합니다. 디바이스 탐색기 도구를 사용하여 디바이스를 추가하면 디바이스에 대한 연결 문자열을 얻습니다. 이 연결 문자열은 샘플 애플리케이션을 실행하는 데 필요합니다.

디바이스 탐색기 도구에 익숙하지 않은 경우 다음 절차에서 이 도구를 사용하여 디바이스를 추가하고 디바이스 연결 문자열을 얻는 방법에 대해 설명합니다.

1. 디바이스 탐색기 도구를 설치하려면 [IoT Hub 디바이스용 디바이스 탐색기를 사용하는 방법](https://github.com/Azure/azure-iot-sdk-csharp/tree/main/tools/)(영문)을 참조하세요.

1. 프로그램을 실행하면 다음 인터페이스가 표시됩니다.

   ![Device Explorer 쌍 스크린샷](./media/iot-hub-device-sdk-c-intro/DeviceExplorerTwinConfigTab.png)

1. 첫 번째 필드에 **IoT Hub 연결 문자열** 을 입력하고 **업데이트** 를 클릭합니다. 이 단계에서는 IoT Hub와 통신할 수 있도록 도구를 구성합니다. 

**연결 문자열** 은 **IoT Hub Service** > **설정** > **공유 액세스 정책** > **iothubowner** 에서 찾을 수 있습니다.

1. IoT Hub 연결 문자열이 구성되었으면 **관리** 탭을 클릭합니다.

   ![Device Explorer 쌍/관리 스크린샷](./media/iot-hub-device-sdk-c-intro/DeviceExplorerTwinManagementTab.png)

이 탭에서는 IoT Hub에 등록된 디바이스를 관리할 수 있습니다.

1. **만들기** 단추를 클릭하여 디바이스를 만듭니다. 미리 채워진 키 집합(기본 및 보조)을 포함한 대화 상자가 표시됩니다. **디바이스 ID** 를 입력한 다음 **만들기** 를 클릭합니다.

   ![디바이스 만들기 스크린샷](./media/iot-hub-device-sdk-c-intro/CreateDevice.png)

1. 디바이스를 만들었으면 방금 만든 디바이스를 포함하여 등록된 모든 디바이스로 [디바이스] 목록이 업데이트됩니다. 새 디바이스를 마우스 오른쪽 단추로 클릭하면 다음 메뉴가 표시됩니다.

   ![Device Explorer 쌍 마우스 오른쪽 단추 클릭 결과](./media/iot-hub-device-sdk-c-intro/DeviceExplorerTwinManagementTab_RightClick.png)

1. **선택한 디바이스에 대한 연결 문자열 복사** 를 선택하면 디바이스 연결 문자열이 클립보드에 복사됩니다. 디바이스 연결 문자열의 복사본을 보관하세요. 다음 섹션에서 설명하는 샘플 애플리케이션을 실행할 때 필요합니다.

위 단계를 완료하면 일부 코드를 실행할 준비가 된 것입니다. 대부분의 샘플에는 주요 원본 파일의 맨 위에 연결 문자열을 입력할 수 있는 상수가 포함되어 있습니다. 예를 들어 **iothub_client\_samples\_iothub_convenience_sample** 애플리케이션의 해당 줄은 다음과 같이 표시됩니다.

```c
static const char* connectionString = "[device connection string]";
```

## <a name="use-the-iothubclient-library"></a>IoTHubClient 라이브러리 사용

[azure-iot-sdk-c](https://github.com/azure/azure-iot-sdk-c) 리포지토리의 **iothub\_client** 폴더 내에는 **iothub\_client\_sample\_mqtt** 라는 애플리케이션이 포함된 **samples** 폴더가 있습니다.

Windows 버전의 **iothub_client\_samples\_iothub_convenience_sample** 애플리케이션은 다음과 같은 Visual Studio 솔루션을 포함합니다.

  ![Visual Studio 솔루션 탐색기](./media/iot-hub-device-sdk-c-intro/iothub-client-sample-mqtt.png)

> [!NOTE]
> Visual Studio에서 프로젝트 대상을 최신 버전으로 변경하도록 요청하는 경우 표시되는 메시지를 수락합니다.

이 솔루션에는 다음의 단일 프로젝트가 포함됩니다. 이 솔루션에 설치되어 있는 4개의 NuGet 패키지는 다음과 같습니다.

* Microsoft.Azure.C.SharedUtility
* Microsoft.Azure.IoTHub.MqttTransport
* Microsoft.Azure.IoTHub.IoTHubClient
* Microsoft.Azure.umqtt

SDK를 사용하여 작업하는 경우 항상 **Microsoft.Azure.C.SharedUtility** 패키지가 필요합니다. 이 샘플은 MQTT 프로토콜을 사용하므로 **Microsoft.Azure.umqtt** 및 **Microsoft.Azure.IoTHub.MqttTransport** 패키지(AMQP 및 HTTPS에 해당하는 패키지가 있음)도 포함해야 합니다. 이 샘플에서는 **IoTHubClient** 라이브러리를 사용하므로 솔루션에 **Microsoft.Azure.IoTHub.IoTHubClient** 패키지도 포함해야 합니다.

**iothub_client\_samples\_iothub_convenience_sample** 원본 파일에서 샘플 애플리케이션에 대한 구현을 찾을 수 있습니다.

다음 단계에서는 이 샘플 애플리케이션을 사용하여 **IoTHubClient** 라이브러리를 사용하는 데 필요한 내용을 안내합니다.

### <a name="initialize-the-library"></a>라이브러리 초기화

> [!NOTE]
> 라이브러리 작업을 시작하기 전에 일부 플랫폼별 초기화를 수행해야 할 수도 있습니다. 예를 들어 Linux에서 AMQP를 사용할 계획인 경우 OpenSSL 라이브러리를 초기화해야 합니다. [GitHub 리포지토리](https://github.com/Azure/azure-iot-sdk-c)의 샘플은 클라이언트가 시작될 때 **platform\_init** 유틸리티 함수를 호출하고, 종료하기 전에 **platform\_deinit** 함수를 호출합니다. 이러한 함수는 "platform.h" 헤더 파일에 선언되어 있습니다. [리포지토리](https://github.com/Azure/azure-iot-sdk-c)에서 대상 플랫폼에 대해 이러한 함수의 정의를 확인하여 클라이언트에 플랫폼별 초기화 코드를 포함해야 하는지 여부를 결정합니다.

라이브러리 작업을 시작하려면 먼저 IoT Hub 클라이언트 핸들을 할당합니다.

```c
if ((iotHubClientHandle = 
  IoTHubClient_LL_CreateFromConnectionString(connectionString, MQTT_Protocol)) == NULL)
{
    (void)printf("ERROR: iotHubClientHandle is NULL!\r\n");
}
else
{
    ...
```

디바이스 탐색기 도구에서 얻은 디바이스 연결 문자열의 복사본을 이 함수에 전달합니다. 또한 사용할 통신 프로토콜도 지정합니다. 이 예제에서는 MQTT를 사용하지만 AMQP와 HTTPS도 옵션입니다.

유효한 **IOTHUB\_CLIENT\_HANDLE** 이 있으면 API 호출을 시작하여 IoT Hub와 메시지를 보내고 받을 수 있습니다.

### <a name="send-messages"></a>메시지 보내기

샘플 애플리케이션은 IoT Hub에 메시지를 보내도록 루프를 설정합니다. 코드 조각은 다음과 같습니다.

- 메시지를 만듭니다.
- 메시지에 속성을 추가합니다.
- 메시지를 보냅니다.

먼저, 메시지를 만듭니다.

```c
size_t iterator = 0;
do
{
    if (iterator < MESSAGE_COUNT)
    {
        sprintf_s(msgText, sizeof(msgText), "{\"deviceId\":\"myFirstDevice\",\"windSpeed\":%.2f}", avgWindSpeed + (rand() % 4 + 2));
        if ((messages[iterator].messageHandle = IoTHubMessage_CreateFromByteArray((const unsigned char*)msgText, strlen(msgText))) == NULL)
        {
            (void)printf("ERROR: iotHubMessageHandle is NULL!\r\n");
        }
        else
        {
            messages[iterator].messageTrackingId = iterator;
            MAP_HANDLE propMap = IoTHubMessage_Properties(messages[iterator].messageHandle);
            (void)sprintf_s(propText, sizeof(propText), "PropMsg_%zu", iterator);
            if (Map_AddOrUpdate(propMap, "PropName", propText) != MAP_OK)
            {
                (void)printf("ERROR: Map_AddOrUpdate Failed!\r\n");
            }

            if (IoTHubClient_LL_SendEventAsync(iotHubClientHandle, messages[iterator].messageHandle, SendConfirmationCallback, &messages[iterator]) != IOTHUB_CLIENT_OK)
            {
                (void)printf("ERROR: IoTHubClient_LL_SendEventAsync..........FAILED!\r\n");
            }
            else
            {
                (void)printf("IoTHubClient_LL_SendEventAsync accepted message [%d] for transmission to IoT Hub.\r\n", (int)iterator);
            }
        }
    }
    IoTHubClient_LL_DoWork(iotHubClientHandle);
    ThreadAPI_Sleep(1);

    iterator++;
} while (g_continueRunning);
```

메시지를 보낼 때마다 데이터를 보낼 때 호출되는 콜백 함수에 대한 참조를 지정합니다. 이 예제에서 콜백 함수는 **SendConfirmationCallback** 이라고 합니다. 다음 코드 조각은 이 콜백 함수를 보여 줍니다.

```c
static void SendConfirmationCallback(IOTHUB_CLIENT_CONFIRMATION_RESULT result, void* userContextCallback)
{
    EVENT_INSTANCE* eventInstance = (EVENT_INSTANCE*)userContextCallback;
    (void)printf("Confirmation[%d] received for message tracking id = %zu with result = %s\r\n", callbackCounter, eventInstance->messageTrackingId, MU_ENUM_TO_STRING(IOTHUB_CLIENT_CONFIRMATION_RESULT, result));
    /* Some device specific action code goes here... */
    callbackCounter++;
    IoTHubMessage_Destroy(eventInstance->messageHandle);
}
```

메시지 작업을 완료했으면 **IoTHubMessage\_Destroy** 함수에 대한 호출에 유의하세요. 이 함수는 메시지를 만들 때 할당된 리소스를 해제합니다.

### <a name="receive-messages"></a>메시지 받기

메시지 수신은 비동기 작업입니다. 먼저 디바이스에서 메시지를 받을 때 호출할 콜백을 등록합니다.

```c
if (IoTHubClient_LL_SetMessageCallback(iotHubClientHandle, ReceiveMessageCallback, &receiveContext) != IOTHUB_CLIENT_OK)
{
    (void)printf("ERROR: IoTHubClient_LL_SetMessageCallback..........FAILED!\r\n");
}
else
{
    (void)printf("IoTHubClient_LL_SetMessageCallback...successful.\r\n");
    ...
```

마지막 매개 변수는 원하는 항목에 대한 void 포인터입니다. 이 샘플에서는 정수에 대한 포인터이지만 더 복잡한 데이터 구조에 대한 포인터일 수 있습니다. 이 매개 변수는 콜백 함수의 호출자와 공유된 상태에서 해당 콜백 함수가 작동할 수 있게 합니다.

디바이스에서 메시지를 받으면 등록된 콜백 함수가 호출됩니다. 이 콜백 함수에서 검색하는 항목은 다음과 같습니다.

* 메시지 ID 및 해당 메시지의 상관 관계 ID.
* 메시지 내용
* 메시지의 모든 사용자 지정 속성

```c
static IOTHUBMESSAGE_DISPOSITION_RESULT ReceiveMessageCallback(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
{
    int* counter = (int*)userContextCallback;
    const char* buffer;
    size_t size;
    MAP_HANDLE mapProperties;
    const char* messageId;
    const char* correlationId;

    // Message properties
    if ((messageId = IoTHubMessage_GetMessageId(message)) == NULL)
    {
        messageId = "<null>";
    }

    if ((correlationId = IoTHubMessage_GetCorrelationId(message)) == NULL)
    {
        correlationId = "<null>";
    }

    // Message content
    if (IoTHubMessage_GetByteArray(message, (const unsigned char**)&buffer, &size) != IOTHUB_MESSAGE_OK)
    {
        (void)printf("unable to retrieve the message data\r\n");
    }
    else
    {
        (void)printf("Received Message [%d]\r\n Message ID: %s\r\n Correlation ID: %s\r\n Data: <<<%.*s>>> & Size=%d\r\n", *counter, messageId, correlationId, (int)size, buffer, (int)size);
        // If we receive the work 'quit' then we stop running
        if (size == (strlen("quit") * sizeof(char)) && memcmp(buffer, "quit", size) == 0)
        {
            g_continueRunning = false;
        }
    }

    // Retrieve properties from the message
    mapProperties = IoTHubMessage_Properties(message);
    if (mapProperties != NULL)
    {
        const char*const* keys;
        const char*const* values;
        size_t propertyCount = 0;
        if (Map_GetInternals(mapProperties, &keys, &values, &propertyCount) == MAP_OK)
        {
            if (propertyCount > 0)
            {
                size_t index;

                printf(" Message Properties:\r\n");
                for (index = 0; index < propertyCount; index++)
                {
                    (void)printf("\tKey: %s Value: %s\r\n", keys[index], values[index]);
                }
                (void)printf("\r\n");
            }
        }
    }

    /* Some device specific action code goes here... */
    (*counter)++;
    return IOTHUBMESSAGE_ACCEPTED;
}
```

**IoTHubMessage\_GetByteArray** 함수를 사용하여 메시지를 검색합니다. 이 예제에서는 문자열입니다.

### <a name="uninitialize-the-library"></a>라이브러리 초기화 취소

이벤트 보내기 및 메시지 받기를 완료했으면 IoT 라이브러리 초기화를 취소할 수 있습니다. 이렇게 하려면 다음 함수 호출을 실행합니다.

```c
IoTHubClient_LL_Destroy(iotHubClientHandle);
```

이 호출은 **IoTHubClient\_CreateFromConnectionString** 함수로 이전에 할당된 리소스를 해제합니다.

여기서 볼 수 있듯이 **IoTHubClient** 라이브러리를 사용하여 메시지를 보내고 받는 것이 쉽습니다. 라이브러리가 사용할 프로토콜 등 IoT Hub와의 통신에 대한 세부 사항을 처리합니다(개발자 관점에서는 간단한 구성 옵션).

또한 **IoTHubClient** 라이브러리는 디바이스에서 IoT Hub로 보내는 데이터를 직렬화하는 방법도 정밀하게 제어합니다. 어떤 경우에는 이러한 수준의 제어가 장점이지만, 다른 경우에는 사용자가 관계하지 않으려는 구현 세부 사항입니다. 이 경우 다음 섹션에서 설명하는 **serializer** 라이브러리를 사용하는 것이 좋습니다.

## <a name="use-the-serializer-library"></a>serializer(직렬 변환기) 사용

개념적으로 **serializer** 라이브러리는 SDK의 **IoTHubClient** 라이브러리 위쪽에 있습니다. IoT Hub와의 기본 통신에 **IoTHubClient** 를 사용하지만 개발자가 메시지 직렬화를 처리하는 부담을 없애주는 모델링 기능을 추가합니다. 이 라이브러리가 동작하는 방식은 예제를 통해 가장 잘 이해할 수 있습니다.

[azure-iot-sdk-c 리포지토리](https://github.com/Azure/azure-iot-sdk-c)의 **serializer** 폴더 내에는 **simplesample\_mqtt** 라는 애플리케이션이 포함된 **samples** 폴더가 있습니다. Windows 버전의 이 샘플은 다음과 같은 Visual Studio 솔루션을 포함합니다.

  ![mqtt 샘플용 Visual Studio 솔루션](./media/iot-hub-device-sdk-c-intro/simplesample_mqtt.png)

> [!NOTE]
> Visual Studio에서 프로젝트 대상을 최신 버전으로 변경하도록 요청하는 경우 표시되는 메시지를 수락합니다.

이전 샘플과 마찬가지로 이 하나에는 여러 NuGet 패키지가 포함됩니다.

* Microsoft.Azure.C.SharedUtility
* Microsoft.Azure.IoTHub.MqttTransport
* Microsoft.Azure.IoTHub.IoTHubClient
* Microsoft.Azure.IoTHub.Serializer
* Microsoft.Azure.umqtt

이전 샘플에서 대부분의 패키지를 살펴보았지만 **Microsoft.Azure.IoTHub.Serializer** 는 새로운 패키지입니다. 이 패키지는 **serializer** 라이브러리를 사용할 때 필요합니다.

**iothub_client\_samples\_iothub_convenience_sample** 파일에서 샘플 애플리케이션에 대한 구현을 찾을 수 있습니다.

다음 섹션에서는 이 샘플의 주요 부분을 안내합니다.

### <a name="initialize-the-library"></a>라이브러리 초기화

**serializer** 라이브러리 작업을 시작하려면 초기화 API를 호출합니다.

```c
if (serializer_init(NULL) != SERIALIZER_OK)
{
    (void)printf("Failed on serializer_init\r\n");
}
else
{
    IOTHUB_CLIENT_LL_HANDLE iotHubClientHandle = IoTHubClient_LL_CreateFromConnectionString(connectionString, MQTT_Protocol);
    srand((unsigned int)time(NULL));
    int avgWindSpeed = 10;

    if (iotHubClientHandle == NULL)
    {
        (void)printf("Failed on IoTHubClient_LL_Create\r\n");
    }
    else
    {
        ContosoAnemometer* myWeather = CREATE_MODEL_INSTANCE(WeatherStation, ContosoAnemometer);
        if (myWeather == NULL)
        {
            (void)printf("Failed on CREATE_MODEL_INSTANCE\r\n");
        }
        else
        {
...
```

**serializer\_init** 함수에 대한 호출은 일회성 호출이며, 기본 라이브러리를 초기화합니다. 그런 다음 **IoTHubClient** 샘플과 동일한 API인 **IoTHubClient\_LL\_CreateFromConnectionString** 함수를 호출합니다. 이 호출은 디바이스 연결 문자열을 설정하며, 사용하려는 프로토콜을 선택하는 위치이기도 합니다. 이 샘플은 MQTT를 전송으로 사용하지만, AMQP 또는 HTTPS를 사용할 수도 있습니다.

마지막으로 **CREATE\_MODEL\_INSTANCE** 함수를 호출합니다. **WeatherStation** 은 모델의 네임스페이스이며, **ContosoAnemometer** 는 모델의 이름입니다. 모델 인스턴스가 만들어지면 이를 사용하여 메시지 보내기 및 받기를 시작할 수 있습니다. 하지만 모델을 이해하는 것이 중요합니다.

### <a name="define-the-model"></a>모델 정의

**serializer** 라이브러리의 모델은 디바이스에서 IoT Hub로 보낼 수 있는 이벤트와 모델링 언어로 *작업*(action)이라고 하는 받을 수 있는 메시지를 정의합니다. **iothub_client\_samples\_iothub_convenience_sample** 샘플 애플리케이션과 같이 C 매크로 세트를 사용하여 모델을 정의합니다.

```c
BEGIN_NAMESPACE(WeatherStation);

DECLARE_MODEL(ContosoAnemometer,
WITH_DATA(ascii_char_ptr, DeviceId),
WITH_DATA(int, WindSpeed),
WITH_ACTION(TurnFanOn),
WITH_ACTION(TurnFanOff),
WITH_ACTION(SetAirResistance, int, Position)
);

END_NAMESPACE(WeatherStation);
```

**BEGIN\_NAMESPACE** 및 **END\_NAMESPACE** 매크로 모두 인수로 모델의 네임스페이스를 사용합니다. 이러한 매크로에는 모델의 정의와 모델에서 사용하는 데이터 구조가 필요합니다.

이 예에서는 **ContosoAnemometer** 라는 단일 모델이 있습니다. 이 모델은 디바이스에서 IoT Hub로 보낼 수 있는 두 가지 데이터, 즉 **DeviceId** 및 **WindSpeed** 를 정의합니다. 또한 디바이스에서 수신할 수 있는 세 가지 동작(메시지)으로 **TurnFanOn**, **TurnFanOff** 및 **SetAirResistance** 를 정의합니다. 각 데이터 요소에는 형식이 있고, 각 작업에는 이름(필요에 따라 매개 변수 집합)이 있습니다.

모델에 정의된 이벤트 및 작업은 메시지를 IoT Hub로 보내고 디바이스로 보낸 메시지에 응답하는 데 사용할 수 있는 API 표면을 정의합니다. 이 모델의 사용은 예제를 통해 가장 잘 이해할 수 있습니다.

### <a name="send-messages"></a>메시지 보내기

모델은 IoT Hub로 보낼 수 있는 데이터를 정의합니다. 이 예제에서는 **WITH_DATA** 매크로를 사용하여 정의된 두 데이터 항목 중 하나입니다. **DeviceId** 및 **WindSpeed** 값을 IoT Hub에 보내려면 여러 단계가 필요합니다. 먼저 다음과 같이 보내려는 데이터를 설정합니다.

```c
myWeather->DeviceId = "myFirstDevice";
myWeather->WindSpeed = avgWindSpeed + (rand() % 4 + 2);
```

앞에서 정의한 모델을 사용하면 **구조체** 의 멤버를 설정하여 값을 설정할 수 있습니다. 다음으로 보내려는 메시지를 직렬화합니다.

```c
unsigned char* destination;
size_t destinationSize;
if (SERIALIZE(&destination, &destinationSize, myWeather->DeviceId, myWeather->WindSpeed) != CODEFIRST_OK)
{
    (void)printf("Failed to serialize\r\n");
}
else
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
    free(destination);
}
```

다음 코드는 디바이스-클라우드 메시지를 버퍼에 직렬화합니다( **대상** 기준으로 참조됨). 그런 다음 코드에서 **sendMessage** 함수를 호출하여 해당 메시지를 IoT Hub로 보냅니다.

```c
static void sendMessage(IOTHUB_CLIENT_LL_HANDLE iotHubClientHandle, const unsigned char* buffer, size_t size)
{
    static unsigned int messageTrackingId;
    IOTHUB_MESSAGE_HANDLE messageHandle = IoTHubMessage_CreateFromByteArray(buffer, size);
    if (messageHandle == NULL)
    {
        printf("unable to create a new IoTHubMessage\r\n");
    }
    else
    {
        if (IoTHubClient_LL_SendEventAsync(iotHubClientHandle, messageHandle, sendCallback, (void*)(uintptr_t)messageTrackingId) != IOTHUB_CLIENT_OK)
        {
            printf("failed to hand over the message to IoTHubClient");
        }
        else
        {
            printf("IoTHubClient accepted the message for delivery\r\n");
        }
        IoTHubMessage_Destroy(messageHandle);
    }
    messageTrackingId++;
}
```

**IoTHubClient\_LL\_SendEventAsync** 의 끝에서 두 번째 매개 변수는 데이터를 성공적으로 보낼 때 호출되는 콜백 함수에 대한 참조입니다. 다음은 샘플의 콜백 함수입니다.

```c
void sendCallback(IOTHUB_CLIENT_CONFIRMATION_RESULT result, void* userContextCallback)
{
    unsigned int messageTrackingId = (unsigned int)(uintptr_t)userContextCallback;

    (void)printf("Message Id: %u Received.\r\n", messageTrackingId);

    (void)printf("Result Call Back Called! Result is: %s \r\n", MU_ENUM_TO_STRING(IOTHUB_CLIENT_CONFIRMATION_RESULT, result));
}
```

두 번째 매개 변수는 사용자 컨텍스트에 대한 포인터이며, **IoTHubClient\_LL\_SendEventAsync** 로 전달된 포인터와 동일합니다. 이 경우 컨텍스트는 간단한 카운터이지만, 사용자가 원하는 모든 것일 수 있습니다.

즉 디바이스-클라우드 메시지를 보내는 것입니다. 이제 메시지를 수신하는 방법을 알아보겠습니다.

### <a name="receive-messages"></a>메시지 받기

메시지 수신은 **IoTHubClient** 라이브러리에서 메시지가 작동하는 방식과 유사하게 작동합니다. 먼저 메시지 호출 함수를 등록합니다.

```c
if (IoTHubClient_LL_SetMessageCallback(iotHubClientHandle, 
  IoTHubMessage, myWeather) != IOTHUB_CLIENT_OK)
{
    printf("unable to IoTHubClient_SetMessageCallback\r\n");
}
else
{
...
```

그런 다음 메시지를 수신할 때 호출되는 호출 함수를 작성합니다.

```c
static IOTHUBMESSAGE_DISPOSITION_RESULT IoTHubMessage(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
{
    IOTHUBMESSAGE_DISPOSITION_RESULT result;
    const unsigned char* buffer;
    size_t size;
    if (IoTHubMessage_GetByteArray(message, &buffer, &size) != IOTHUB_MESSAGE_OK)
    {
        printf("unable to IoTHubMessage_GetByteArray\r\n");
        result = IOTHUBMESSAGE_ABANDONED;
    }
    else
    {
        /*buffer is not zero terminated*/
        char* temp = malloc(size + 1);
        if (temp == NULL)
        {
            printf("failed to malloc\r\n");
            result = IOTHUBMESSAGE_ABANDONED;
        }
        else
        {
            (void)memcpy(temp, buffer, size);
            temp[size] = '\0';
            EXECUTE_COMMAND_RESULT executeCommandResult = EXECUTE_COMMAND(userContextCallback, temp);
            result =
                (executeCommandResult == EXECUTE_COMMAND_ERROR) ? IOTHUBMESSAGE_ABANDONED :
                (executeCommandResult == EXECUTE_COMMAND_SUCCESS) ? IOTHUBMESSAGE_ACCEPTED :
                IOTHUBMESSAGE_REJECTED;
            free(temp);
        }
    }
    return result;
}
```

다음 코드는 상용구로 모든 솔루션에 대해 동일합니다. 이 함수는 메시지를 수신하고 **EXECUTE\_COMMAND** 호출을 통해 적절한 함수로 라우팅합니다. 이 시점에서 호출되는 함수는 모델의 작업 정의에 따라 다릅니다.

모델에서 동작을 정의할 때 디바이스에서 해당 메시지를 수신할 때 호출되는 함수를 구현해야 합니다. 예를 들어 모델에서 다음 동작을 정의하는 경우:

```c
WITH_ACTION(SetAirResistance, int, Position)
```

다음 서명을 사용하는 함수를 정의합니다.

```c
EXECUTE_COMMAND_RESULT SetAirResistance(ContosoAnemometer* device, int Position)
{
    (void)device;
    (void)printf("Setting Air Resistance Position to %d.\r\n", Position);
    return EXECUTE_COMMAND_SUCCESS;
}
```

함수의 이름이 모델의 작업 이름과 일치하고, 함수의 매개 변수가 작업에 대해 지정된 매개 변수와 일치하는지 확인합니다. 첫 번째 매개 변수는 항상 필수이며, 모델 인스턴스에 대한 포인터를 포함합니다.

디바이스에서 이 서명과 일치하는 메시지를 수신하면 해당 함수가 호출됩니다. 따라서 **IoTHubMessage** 의 상용구 코드를 포함해야 하는 것 외에도 메시지 수신은 모델에 정의된 각 작업에 대한 간단한 함수를 정의하는 정도의 문제입니다.

### <a name="uninitialize-the-library"></a>라이브러리 초기화 취소

데이터 보내기 및 메시지 받기를 완료했으면 IoT 라이브러리 초기화를 취소할 수 있습니다.

```c
...
        DESTROY_MODEL_INSTANCE(myWeather);
    }
    IoTHubClient_LL_Destroy(iotHubClientHandle);
}
serializer_deinit();
```

이러한 세 함수 각각은 앞에서 설명한 세 초기화 함수에 맞춰집니다. 이러한 API를 호출하면 이전에 할당된 리소스를 해제합니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 **C용 Azure IoT 디바이스 SDK** 에서 라이브러리를 사용하는 기본 사항에 대해 설명합니다. SDK에 포함된 내용, 아키텍처, Windows 샘플을 사용하여 작업을 시작하는 방법을 이해하는 데 충분한 정보를 제공합니다. 다음 문서에서는 [IoTHubClient 라이브러리에 대한 자세한 정보](iot-hub-device-sdk-c-iothubclient.md)를 설명하여 SDK를 계속 설명합니다.

IoT Hub를 개발하는 방법에 대한 자세한 내용은 [Azure IoT SDK](iot-hub-devguide-sdks.md)를 참조하세요.

IoT Hub의 기능을 추가로 탐색하려면 다음을 참조하세요.

* [Azure IoT Edge를 사용하여 에지 디바이스에 AI 배포](../iot-edge/quickstart-linux.md)
