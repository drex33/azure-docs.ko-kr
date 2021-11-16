---
title: Azure IoT Hub 모듈 ID 및 모듈 쌍(포털 및 .NET)
description: 포털 및 .NET을 사용하여 모듈 ID를 만들고 모듈 쌍을 업데이트하는 방법을 알아봅니다.
author: eross-msft
ms.author: lizross
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 08/20/2019
ms.custom: amqp, devx-track-csharp
ms.openlocfilehash: 993a59f1c47bd0920c120a19252367489c768807
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132555076"
---
# <a name="get-started-with-iot-hub-module-identity-and-module-twin-using-the-portal-and-net-device"></a>포털 및 .NET 디바이스를 사용하여 IoT Hub 모듈 ID 및 모듈 쌍 시작

[!INCLUDE [iot-hub-selector-module-twin-getstarted](../../includes/iot-hub-selector-module-twin-getstarted.md)]

> [!NOTE]
> [모듈 ID 및 모듈 쌍](iot-hub-devguide-module-twins.md)은 Azure IoT Hub 디바이스 ID 및 디바이스 쌍과 비슷하지만 더 자세한 세분성을 제공합니다. Azure IoT Hub 장치 ID와 장치 쌍은 백 엔드 응용 프로그램에서 장치를 구성할 수 있도록 하고 장치 상태를 표시하는 반면, 모듈 ID와 모듈 쌍은 장치의 개별 구성 요소에 대해 해당 기능을 제공합니다. 운영 체제 기반 디바이스 또는 펌웨어 디바이스와 같이 여러 구성 요소가 있는 가능한 디바이스에서 모듈 ID와 모듈 쌍은 각 구성 요소에 대해 격리된 구성과 상태를 허용합니다.
>

이 자습서에서는 다음에 대해 알아봅니다.

* 포털에서 모듈 ID를 만드는 방법

* .NET 디바이스 SDK를 사용하여 디바이스에서 모듈 쌍을 업데이트하는 방법입니다.

> [!NOTE]
> 디바이스와 솔루션 백 엔드에서 실행하기 위해 두 애플리케이션을 빌드하는 데 사용할 수 있는 Azure IoT SDK에 대한 자세한 내용은 [Azure IoT SDK](iot-hub-devguide-sdks.md)를 참조하세요.
>

## <a name="prerequisites"></a>필수 구성 요소

* Visual Studio.

* 활성 Azure 계정. 계정이 없는 경우 몇 분 안에 [무료 계정](https://azure.microsoft.com/pricing/free-trial/) 을 만들 수 있습니다.

## <a name="create-a-hub"></a>허브 생성

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-hub"></a>허브에서 새 디바이스 등록

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="create-a-module-identity-in-the-portal"></a>포털에서 모듈 ID 만들기

하나의 디바이스 ID 내에 최대 20개의 모듈 ID를 만들 수 있습니다. ID를 추가하려면 다음 단계를 수행합니다.

1. 이전 섹션에서 만든 디바이스에 대해 **모듈 ID 추가** 를 선택하여 첫 번째 모듈 ID를 만듭니다.

1. 이름으로 *myFirstModule* 을 입력합니다. 모듈 ID를 저장합니다.

    ![모듈 ID 추가](./media/iot-hub-portal-csharp-module-twin-getstarted/add-module-identity.png)

    새 모듈 ID가 화면 맨 아래에 나타납니다. 새 모듈 ID를 선택하여 모듈 ID 세부 정보를 확인합니다.

    ![모듈 ID 세부 정보 확인](./media/iot-hub-portal-csharp-module-twin-getstarted/module-identity-details.png)

**연결 문자열 - 기본 키** 를 저장합니다. 다음 섹션에서 키를 사용하여 디바이스에서 모듈을 설정합니다.

## <a name="update-the-module-twin-using-net-device-sdk"></a>.NET 디바이스 SDK를 사용하여 모듈 쌍 업데이트

IoT Hub에서 모듈 ID를 성공적으로 만들었습니다. 시뮬레이트된 디바이스에서 클라우드와 통신해 보겠습니다. 모듈 ID가 만들어지면 모듈 쌍이 IoT Hub에서 암시적으로 생성됩니다. 이 섹션에서는 시뮬레이션된 디바이스에 모듈 쌍 reported 속성을 업데이트하는 .NET 콘솔 앱을 만듭니다.

### <a name="create-a-visual-studio-project"></a>Visual Studio 프로젝트 만들기

모듈 쌍의 reported 속성을 업데이트하는 앱을 만들려면 다음 단계를 수행합니다.

1. Visual Studio에서 **새 프로젝트 만들기**, **콘솔 앱(.NET Framework)** , **다음** 순으로 선택합니다.

1. **새 프로젝트 구성** 에서 **프로젝트 이름** 으로 *UpdateModuleTwinReportedProperties* 를 입력합니다. **만들기** 를 선택하여 계속합니다.

    ![Visual Studio 프로젝트 구성](./media/iot-hub-portal-csharp-module-twin-getstarted/configure-twins-project.png)

### <a name="install-the-latest-azure-iot-hub-net-device-sdk"></a>최신 Azure IoT Hub .NET 디바이스 SDK 설치

모듈 ID 및 모듈 쌍은 공개 미리 보기로 제공됩니다. IoT Hub 시험판 디바이스 SDK에서만 사용할 수 있습니다. 설치하려면 다음 단계를 수행합니다.

1. Visual Studio에서 **도구** > **NuGet 패키지 관리자** > **솔루션용 NuGet 패키지 관리** 를 엽니다.

1. **찾아보기** 를 선택한 다음 **시험판 포함** 을 선택합니다. *Microsoft.Azure.Devices.Client* 를 검색합니다. 최신 버전을 선택하고 설치합니다.

    ![Azure IoT Hub .NET 서비스 SDK 미리 보기 설치](./media/iot-hub-csharp-csharp-module-twin-getstarted/install-sdk.png)

    이제 모든 모듈 기능에 액세스할 수 있습니다.

### <a name="get-your-module-connection-string"></a>모듈 연결 문자열 가져오기

콘솔 앱의 모듈 연결 문자열이 필요합니다. 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.

1. IoT 허브로 이동한 다음 **IoT 디바이스** 를 선택합니다. **myFirstDevice** 를 열면 **myFirstModule** 이 성공적으로 생성된 것을 확인할 수 있습니다.

1. **모듈 ID** 에서 **myFirstModule** 을 선택합니다. **모듈 ID 세부 정보** 에서 **연결 문자열(기본 키)** 을 복사합니다.

    ![Azure Portal 모듈 세부 정보](./media/iot-hub-portal-csharp-module-twin-getstarted/module-identity-details.png)

### <a name="create-updatemoduletwinreportedproperties-console-app"></a>UpdateModuleTwinReportedProperties 콘솔 앱 만들기

앱을 만들려면 다음 단계를 수행합니다.

1. **Program.cs** 파일 위에 다음 `using` 문을 추가합니다.

  ```csharp
  using Microsoft.Azure.Devices.Client;
  using Microsoft.Azure.Devices.Shared;
  using Newtonsoft.Json;
  ```

2. **Program** 클래스에 다음 필드를 추가합니다. 자리 표시자 값을 모듈 연결 문자열로 바꿉니다.

  ```csharp
  private const string ModuleConnectionString = "<Your module connection string>";
  private static ModuleClient Client = null;
  ```

3. **Program** 클래스에 **OnDesiredPropertyChanged** 메서드를 추가합니다.

  ```csharp
  private static async Task OnDesiredPropertyChanged(TwinCollection desiredProperties, object userContext)
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

4. 마지막으로, **Main** 메서드를 다음 코드로 바꿉니다.

  ```csharp
  static void Main(string[] args)
  {
      Microsoft.Azure.Devices.Client.TransportType transport = Microsoft.Azure.Devices.Client.TransportType.Amqp;
  
      try
      {
          Client = ModuleClient.CreateFromConnectionString(ModuleConnectionString, transport);
          Client.SetConnectionStatusChangesHandler(ConnectionStatusChangeHandler);
          Client.SetDesiredPropertyUpdateCallbackAsync(OnDesiredPropertyChanged, null).Wait();
  
          Console.WriteLine("Retrieving twin");
          var twinTask = Client.GetTwinAsync();
          twinTask.Wait();
          var twin = twinTask.Result;
          Console.WriteLine(JsonConvert.SerializeObject(twin));
  
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
      Console.ReadKey();
      Client.CloseAsync().Wait();
  }
  
  private static void ConnectionStatusChangeHandler(ConnectionStatus status, ConnectionStatusChangeReason reason)
  {
      Console.WriteLine($"Status {status} changed: {reason}");
  }
  ```
  
  **F5** 키를 사용하여 앱을 빌드하고 실행할 수 있습니다.

이 코드 샘플에서는 AMQP 프로토콜을 사용하여 모듈 쌍을 검색하고 reported 속성을 업데이트하는 방법을 보여 줍니다. 공개 미리 보기에서는 모듈 쌍 작업에 대해서만 AMQP를 지원합니다.

## <a name="next-steps"></a>다음 단계

계속해서 IoT Hub을 시작하고 다른 IoT 시나리오를 탐색하려면 다음을 참조하세요.

* [.NET 백업 및 .NET 디바이스를 사용하여 IoT Hub 모듈 ID 및 모듈 쌍 시작](iot-hub-csharp-csharp-module-twin-getstarted.md)

* [IoT Edge 시작](../iot-edge/quickstart-linux.md)