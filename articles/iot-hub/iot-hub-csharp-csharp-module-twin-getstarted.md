---
title: Azure IoT Hub 모듈 ID 및 모듈 쌍 시작(.NET)
description: .NET용 IoT SDK를 사용하여 모듈 ID를 만들고 모듈 쌍을 업데이트하는 방법을 알아봅니다.
author: eross-msft
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 08/07/2019
ms.author: lizross
ms.custom: amqp, devx-track-csharp
ms.openlocfilehash: 22fb3c9959f3f71df0b95b41bebb9ccc445815fd
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132555186"
---
# <a name="get-started-with-iot-hub-module-identity-and-module-twin-net"></a>Azure IoT Hub 모듈 ID 및 모듈 쌍 시작(.NET)

[!INCLUDE [iot-hub-selector-module-twin-getstarted](../../includes/iot-hub-selector-module-twin-getstarted.md)]

> [!NOTE]
> [모듈 ID 및 모듈 쌍](iot-hub-devguide-module-twins.md)은 Azure IoT Hub 디바이스 ID 및 디바이스 쌍과 비슷하지만 더 자세한 세분성을 제공합니다. Azure IoT Hub 장치 ID와 장치 쌍은 백 엔드 응용 프로그램에서 장치를 구성할 수 있도록 하고 장치 상태를 표시하는 반면, 모듈 ID와 모듈 쌍은 장치의 개별 구성 요소에 대해 해당 기능을 제공합니다. 운영 체제 기반 장치 또는 펌웨어 장치와 같이 여러 구성 요소가 있는 가능한 장치에서 모듈 ID와 모듈 쌍은 각 구성 요소에 대해 격리된 구성과 상태를 허용합니다.

이 자습서를 마치면 다음과 같은 두 개의 .NET 콘솔 앱이 생깁니다.

* **CreateIdentities**. 이 앱은 장치 ID, 모듈 ID 및 관련 보안 키를 만들어 장치 및 모듈 클라이언트를 연결합니다.

* **UpdateModuleTwinReportedProperties**. 이 앱은 업데이트된 모듈 쌍 reported 속성을 IoT Hub에 보냅니다.

> [!NOTE]
> 디바이스와 솔루션 백 엔드에서 실행하기 위해 두 애플리케이션을 빌드하는 데 사용할 수 있는 Azure IoT SDK에 관한 정보는 [Azure IoT SDK](iot-hub-devguide-sdks.md)를 참조하세요.

## <a name="prerequisites"></a>필수 구성 요소

* Visual Studio.

* 활성 Azure 계정. 계정이 없는 경우 몇 분 안에 [무료 계정](https://azure.microsoft.com/pricing/free-trial/) 을 만들 수 있습니다.

## <a name="create-a-hub"></a>허브 생성

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="get-the-iot-hub-connection-string"></a>IoT Hub 연결 문자열 가져오기

[!INCLUDE [iot-hub-howto-module-twin-shared-access-policy-text](../../includes/iot-hub-howto-module-twin-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-registryrw-connection-string](../../includes/iot-hub-include-find-registryrw-connection-string.md)]

[!INCLUDE [iot-hub-get-started-create-module-identity-csharp](../../includes/iot-hub-get-started-create-module-identity-csharp.md)]

## <a name="update-the-module-twin-using-net-device-sdk"></a>.NET 디바이스 SDK를 사용하여 모듈 쌍 업데이트

이 섹션에서는 시뮬레이션된 디바이스에 모듈 쌍 reported 속성을 업데이트하는 .NET 콘솔 앱을 만듭니다.

시작하기 전에 모듈 연결 문자열을 가져옵니다. [Azure Portal](https://portal.azure.com/)에 로그인합니다. 허브로 이동하여 **IoT 장치** 를 선택합니다. **myFirstDevice** 를 찾습니다. **myFirstDevice** 를 선택하여 연 다음 **myFirstModule** 을 선택하여 엽니다. **모듈 ID 세부 정보** 에서 다음 절차에 따라 필요한 경우 **연결 문자열(기본 키)** 을 복사합니다.

   ![Azure Portal 모듈 세부 정보](./media/iot-hub-csharp-csharp-module-twin-getstarted/module-identity-detail.png)

1. Visual Studio에서 **파일** > **신규** > **프로젝트** 를 선택하여 새 프로젝트를 솔루션에 추가합니다. 새 프로젝트 생성하기에서 **콘솔 앱(.NET Framework)** 을 선택하고 **다음** 을 선택합니다.

1. 프로젝트 이름을 *UpdateModuleTwinReportedProperties* 로 지정합니다. **솔루션** 의 경우, **솔루션에 추가** 를 선택합니다. .NET Framework 버전이 4.6.1 이상인지 확인합니다.

    ![Visual Studio 프로젝트 만들기](./media/iot-hub-csharp-csharp-module-twin-getstarted/configure-update-twins-csharp1.png)

1. **만들기** 를 선택하여 프로젝트를 만듭니다.

1. Visual Studio에서 **도구** > **NuGet 패키지 관리자** > **솔루션용 NuGet 패키지 관리** 를 엽니다. **찾아보기** 탭을 선택합니다.

1. **Microsoft.Azure.Devices.Client** 를 검색하여 선택하고 **설치** 를 선택합니다.

    !["Microsoft.Azure.Devices.Client"가 선택되고 "설치" 단추가 강조 표시된 스크린샷.](./media/iot-hub-csharp-csharp-module-twin-getstarted/install-client-sdk.png)

1. **Program.cs** 파일 위에 다음 `using` 문을 추가합니다.

    ```csharp
    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Shared;
    using System.Threading.Tasks;
    using Newtonsoft.Json;
    ```

1. **Program** 클래스에 다음 필드를 추가합니다. 자리 표시자 값을 모듈 연결 문자열로 바꿉니다.

    ```csharp
    private const string ModuleConnectionString = "<Your module connection string>";
    private static ModuleClient Client = null;
    static void ConnectionStatusChangeHandler(ConnectionStatus status, 
      ConnectionStatusChangeReason reason)
    {
        Console.WriteLine("Connection Status Changed to {0}; the reason is {1}", 
          status, reason);
    }
    ```

1. **Program** 클래스에 **OnDesiredPropertyChanged** 메서드를 추가합니다.

    ```csharp
    private static async Task OnDesiredPropertyChanged(TwinCollection desiredProperties, 
      object userContext)
        {
            Console.WriteLine("desired property change:");
            Console.WriteLine(JsonConvert.SerializeObject(desiredProperties));
            Console.WriteLine("Sending current time as reported property");
            TwinCollection reportedProperties = new TwinCollection
            {
                ["DateTimeLastDesiredPropertyChangeReceived"] = DateTime.Now
            };

            await Client.UpdateReportedPropertiesAsync(reportedProperties).ConfigureAwait(false);
        }
    ```

1. **Main** 메서드에 다음 줄을 추가합니다.

    ```csharp
    static void Main(string[] args)
    {
        Microsoft.Azure.Devices.Client.TransportType transport = 
          Microsoft.Azure.Devices.Client.TransportType.Amqp;

        try
        {
            Client = 
              ModuleClient.CreateFromConnectionString(ModuleConnectionString, transport);
            Client.SetConnectionStatusChangesHandler(ConnectionStatusChangeHandler);
            Client.SetDesiredPropertyUpdateCallbackAsync(OnDesiredPropertyChanged, null).Wait();

            Console.WriteLine("Retrieving twin");
            var twinTask = Client.GetTwinAsync();
            twinTask.Wait();
            var twin = twinTask.Result;
            Console.WriteLine(JsonConvert.SerializeObject(twin.Properties)); 

            Console.WriteLine("Sending app start time as reported property");
            TwinCollection reportedProperties = new TwinCollection();
            reportedProperties["DateTimeLastAppLaunch"] = DateTime.Now;

            Client.UpdateReportedPropertiesAsync(reportedProperties);
        }
        catch (AggregateException ex)
        {
            Console.WriteLine("Error in sample: {0}", ex);
        }

        Console.WriteLine("Waiting for Events.  Press enter to exit...");
        Console.ReadLine();
        Client.CloseAsync().Wait();
    }
    ```

    이 코드 샘플에서는 AMQP 프로토콜을 사용하여 모듈 쌍을 검색하고 reported 속성을 업데이트하는 방법을 보여 줍니다. 공개 미리 보기에서는 모듈 쌍 작업에 대해서만 AMQP를 지원합니다.

1. 필요에 따라 **Main** 메서드에 이러한 문을 추가하여 모듈에서 IoT Hub 이벤트를 보낼 수 있습니다. `try catch`블록 아래에 다음 줄을 추가합니다.

    ```csharp
    Byte[] bytes = new Byte[2];
    bytes[0] = 0;
    bytes[1] = 1;
    var sendEventsTask = Client.SendEventAsync(new Message(bytes));
    sendEventsTask.Wait();
    Console.WriteLine("Event sent to IoT Hub.");
    ```

## <a name="run-the-apps"></a>앱 실행

이제 앱을 실행할 수 있습니다.

1. **솔루션 탐색기** 의 Visual Studio에서 솔루션을 마우스 오른쪽 단추로 클릭하고 **시작 프로젝트 설정** 을 선택합니다.

1. **공용 속성** 에서 **시작 프로젝트** 를 선택합니다.

1. **여러 시작 프로젝트** 를 선택한 다음, 앱에 대한 작업으로 **시작** 을 선택하고, 변경 내용을 적용하려면 **OK** 를 선택합니다.

1. **F5** 키를 눌러 앱을 시작합니다.

## <a name="next-steps"></a>다음 단계

계속해서 IoT Hub을 시작하고 다른 IoT 시나리오를 탐색하려면 다음을 참조하세요.

* [디바이스 관리 시작](iot-hub-node-node-device-management-get-started.md)

* [IoT Edge 시작](../iot-edge/quickstart-linux.md)