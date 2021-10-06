---
author: philmea
ms.author: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: include
ms.custom:
- mvc
- mqtt
- 'Role: Cloud Development'
- devx-track-azurecli
ms.date: 03/04/2020
ms.openlocfilehash: dfca5613659969485a97b0c9ff50c7739821df5d
ms.sourcegitcommit: 557ed4e74f0629b6d2a543e1228f65a3e01bf3ac
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/05/2021
ms.locfileid: "129516659"
---
빠른 시작은 두 가지 미리 작성된 .NET 애플리케이션을 사용합니다.

* 서비스 애플리케이션에서 호출된 직접 메서드에 응답하는 시뮬레이션된 디바이스 애플리케이션입니다. 직접 메서드 호출을 수신하기 위해 이 애플리케이션을 IoT 허브의 디바이스별 엔드포인트에 연결합니다.

* 시뮬레이션된 디바이스에서 직접 메서드를 호출하는 서비스 애플리케이션입니다. 디바이스에서 직접 메서드를 호출하려면 이 애플리케이션을 IoT 허브의 서비스 측 엔드포인트에 연결합니다.

## <a name="prerequisites"></a>필수 구성 요소

* 활성 구독이 있는 Azure 계정. [체험 계정 만들기](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)

* 이 빠른 시작에서 실행하는 두 개의 샘플 애플리케이션은 C#을 사용하여 작성되었습니다. 개발 머신에 .NET Core SDK 3.1 이상이 필요합니다.

    [.NET](https://dotnet.microsoft.com/download)에서 여러 플랫폼에 대한 .NET Core SDK를 다운로드할 수 있습니다.

    다음 명령을 사용하여 개발 컴퓨터에서 C#의 현재 버전을 확인할 수 있습니다.

    ```cmd/sh
    dotnet --version
    ```
* 아직 그렇게 하지 않았다면 https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/main.zip에서 Azure IoT C# 샘플을 다운로드하고 ZIP 보관 파일을 추출합니다.

* 방화벽에서 포트 8883이 열려 있는지 확인합니다. 이 빠른 시작의 디바이스 샘플은 포트 8883을 통해 통신하는 MQTT 프로토콜을 사용합니다. 이 포트는 일부 회사 및 교육용 네트워크 환경에서 차단될 수 있습니다. 이 문제를 해결하는 자세한 내용과 방법은 [IoT Hub에 연결(MQTT)](../articles/iot-hub/iot-hub-mqtt-support.md#connecting-to-iot-hub)을 참조하세요.

[!INCLUDE [azure-cli-prepare-your-environment.md](azure-cli-prepare-your-environment-no-header.md)]

[!INCLUDE [iot-hub-cli-version-info](iot-hub-cli-version-info.md)]

## <a name="create-an-iot-hub"></a>IoT Hub 만들기

이전 [빠른 시작: 원격 분석을 디바이스에서 IoT 허브로 전송](../articles/iot-develop/quickstart-send-telemetry-iot-hub.md?pivots=programming-language-csharp)을 완료한 경우 이 단계를 건너뛸 수 있습니다.

[!INCLUDE [iot-hub-include-create-hub](iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>디바이스 등록

이전 [빠른 시작: 원격 분석을 디바이스에서 IoT 허브로 전송](../articles/iot-develop/quickstart-send-telemetry-iot-hub.md?pivots=programming-language-csharp)을 완료한 경우 이 단계를 건너뛸 수 있습니다.

연결을 위해 디바이스를 IoT Hub에 등록해야 합니다. 이 빠른 시작에서는 Azure Cloud Shell을 사용하여 시뮬레이션된 디바이스를 등록합니다.

1. Azure Cloud Shell에서 다음 명령을 실행하여 디바이스 ID를 만듭니다.

   **YourIoTHubName**: 이 자리 표시자를 IoT 허브용으로 선택한 이름으로 바꿉니다.

   **MyDotnetDevice**: 등록 중인 디바이스의 이름입니다. 표시된 대로 **MyDotnetDevice** 를 사용하는 것이 좋습니다. 다른 디바이스 이름을 선택하는 경우 이 문서 전체에서도 해당 이름을 사용해야 하며, 샘플 애플리케이션에서 디바이스 이름을 업데이트한 후 실행해야 합니다.

    ```azurecli-interactive
    az iot hub device-identity create \
      --hub-name {YourIoTHubName} --device-id MyDotnetDevice
    ```

2. Azure Cloud Shell에서 다음 명령을 실행하여 방금 등록한 디바이스의 _디바이스 연결 문자열_ 을 가져옵니다.

   **YourIoTHubName**: 이 자리 표시자를 IoT 허브용으로 선택한 이름으로 바꿉니다.

    ```azurecli-interactive
    az iot hub device-identity connection-string show \
      --hub-name {YourIoTHubName} \
      --device-id MyDotnetDevice \
      --output table
    ```

    다음과 같은 디바이스 연결 문자열을 기록해 둡니다.

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyNodeDevice;SharedAccessKey={YourSharedAccessKey}`

    이 값은 빠른 시작의 뒷부분에서 사용합니다.

## <a name="retrieve-the-service-connection-string"></a>서비스 연결 문자열 검색

또한 서비스 애플리케이션을 허브에 연결하여 메시지를 검색할 수 있게 하려면 IoT 허브 _서비스 연결 문자열_ 이 필요합니다. 다음 명령은 IoT Hub에 대한 서비스 연결 문자열을 검색합니다.

```azurecli-interactive
az iot hub connection-string show --policy-name service --hub-name {YourIoTHubName} --output table
```

다음과 같은 서비스 연결 문자열을 기록해 둡니다.

   `HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}`

이 값은 빠른 시작의 뒷부분에서 사용합니다. 서비스 연결 문자열은 이전 단계에서 기록한 디바이스 연결 문자열과 다릅니다.

## <a name="listen-for-direct-method-calls"></a>직접 메서드 호출 수신 대기

시뮬레이션된 디바이스 애플리케이션은 IoT 허브의 디바이스별 엔드포인트에 연결하고, 시뮬레이션된 원격 분석을 전송하고, 허브에서 직접 메서드 호출을 수신 대기합니다. 이 빠른 시작에서 허브의 직접 메서드 호출은 디바이스에 원격 분석을 보내는 간격을 변경하도록 지시합니다. 시뮬레이션된 디바이스는 직접 메서드를 실행한 후 승인을 다시 허브로 보냅니다.

1. 로컬 터미널 창에서 샘플 C# 프로젝트의 루트 폴더로 이동합니다. 그런 다음, **iot-hub\Quickstarts\SimulatedDeviceWithCommand** 폴더로 이동합니다.

2. 로컬 터미널 창에서 다음 명령을 실행하여 시뮬레이션된 디바이스 애플리케이션에 필요한 패키지를 설치합니다.

    ```cmd/sh
    dotnet restore
    ```

3. 로컬 터미널 창에서 다음 명령을 실행하여 시뮬레이션된 디바이스 애플리케이션을 빌드하고 실행합니다. 이전에 기록한 디바이스 연결 문자열로 `{DeviceConnectionString}`을 바꿉니다.

    ```cmd/sh
    dotnet run -- {DeviceConnectionString}
    ```

    다음 스크린샷에서는 시뮬레이션된 디바이스 애플리케이션에서 IoT 허브에 원격 분석을 보낼 때의 출력을 보여 줍니다.

    ![시뮬레이션된 디바이스 실행](./media/quickstart-control-device-dotnet/SimulatedDevice-1.png)

## <a name="call-the-direct-method"></a>직접 메서드 호출

서비스 애플리케이션은 IoT Hub의 서비스 측 엔드포인트에 연결합니다. 애플리케이션은 IoT 허브를 통해 디바이스에 직접 메서드 호출을 하고 승인을 수신 대기합니다. IoT Hub 서비스 애플리케이션은 일반적으로 클라우드에서 실행됩니다.

1. 또 다른 로컬 터미널 창에서 샘플 C# 프로젝트의 루트 폴더로 이동합니다. 그런 다음, **iot-hub\Quickstarts\InvokeDeviceMethod** 폴더로 이동합니다.

2. 로컬 터미널 창에서 다음 명령을 실행하여 서비스 애플리케이션에 필요한 라이브러리를 설치합니다.

    ```cmd/sh
    dotnet restore
    ```

3. 로컬 터미널 창에서 다음 명령을 실행하여 서비스 애플리케이션을 빌드하고 실행합니다. 이전에 기록한 서비스 연결 문자열로 `{ServiceConnectionString}`을 바꿉니다.

    ```cmd/sh
    dotnet run -- {ServiceConnectionString}
    ```

    다음 스크린샷에서는 애플리케이션에서 디바이스에 직접 메서드를 호출하고 승인을 받을 때의 출력을 보여 줍니다.

    ![서비스 애플리케이션 실행](./media/quickstart-control-device-dotnet/BackEndApplication.png)

    서비스 애플리케이션을 실행한 후 시뮬레이션된 디바이스를 실행하는 콘솔 창에 메시지가 표시되고 메시지를 보내는 속도가 변경됩니다.

    ![시뮬레이션된 클라이언트에서 변경](./media/quickstart-control-device-dotnet/SimulatedDevice-2.png)
