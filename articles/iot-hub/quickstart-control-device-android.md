---
title: Azure IoT Hub(Android)에서 디바이스 제어 | Microsoft Docs
description: 이 빠른 시작에서는 두 개의 Java 애플리케이션 샘플을 실행합니다. 그 중 한 애플리케이션은 허브에 연결된 디바이스를 원격으로 제어할 수 있는 서비스 애플리케이션입니다. 또 다른 애플리케이션은 원격으로 제어할 수 있는 허브에 연결된 물리적 디바이스 또는 시뮬레이션된 디바이스에서 실행됩니다.
author: wesmc7777
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: quickstart
ms.custom: mvc, mqtt, devx-track-java, devx-track-azurecli, mode-other
ms.date: 06/21/2019
ms.author: wesmc
ms.openlocfilehash: fbd5f11a2a306f75ff1fece3f554df7c02817daf
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133058393"
---
# <a name="control-a-device-connected-to-an-iot-hub-android"></a>IoT 허브(Android)에 연결된 디바이스 제어

이 빠른 시작에서는 직접 메서드를 사용하여 Azure IoT Hub에 연결된 시뮬레이션된 디바이스를 제어합니다. IoT Hub는 클라우드에서 IoT 디바이스를 관리하고, 스토리지 또는 처리를 위해 클라우드로 다량의 디바이스 원격 분석 데이터를 수집할 수 있는 Azure 서비스입니다. 직접 메서드를 사용하여 IoT 허브에 연결된 디바이스의 동작을 원격으로 변경할 수 있습니다. 이 빠른 시작에서는 백 엔드 서비스 애플리케이션에서 호출된 직접 메서드에 응답하는 시뮬레이션된 디바이스 애플리케이션과 Android 디바이스에서 직접 메서드를 호출하는 서비스 애플리케이션인 두 개의 애플리케이션을 사용합니다.

## <a name="prerequisites"></a>필수 구성 요소

* 활성 구독이 있는 Azure 계정. [체험 계정 만들기](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)

* [Android SDK 27이 설치된 Android Studio](https://developer.android.com/studio/). 자세한 내용은 [Android Studio 설치](https://developer.android.com/studio/install)를 참조하세요.

* [Git](https://git-scm.com/download/)

* [Azure IoT 샘플(Java)](https://github.com/Azure-Samples/azure-iot-samples-java)에 포함된 [디바이스 SDK 샘플 Android 애플리케이션](https://github.com/Azure-Samples/azure-iot-samples-java/tree/master/iot-hub/Samples/device/AndroidSample).

* Azure IoT 샘플(Java)에 포함된 [Service SDK 샘플 Android 애플리케이션](https://github.com/Azure-Samples/azure-iot-samples-java/tree/master/iot-hub/Samples/service/AndroidSample).

* 방화벽에서 포트 8883이 열려 있습니다. 이 빠른 시작의 디바이스 샘플은 포트 8883을 통해 통신하는 MQTT 프로토콜을 사용합니다. 이 포트는 일부 회사 및 교육용 네트워크 환경에서 차단될 수 있습니다. 이 문제를 해결하는 자세한 내용과 방법은 [IoT Hub에 연결(MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub)을 참조하세요.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="create-an-iot-hub"></a>IoT Hub 만들기

이전 [빠른 시작: 원격 분석을 디바이스에서 IoT 허브로 전송](../iot-develop/quickstart-send-telemetry-iot-hub.md)을 완료한 경우 이 단계를 건너뛰고 이미 생성된 IoT 허브를 사용할 수 있습니다.

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>디바이스 등록

이전 [빠른 시작: 원격 분석을 디바이스에서 IoT 허브로 전송](../iot-develop/quickstart-send-telemetry-iot-hub.md)을 완료한 경우 이 단계를 건너뛰고 이전 빠른 시작에서 등록한 것과 동일한 디바이스를 사용할 수 있습니다.

연결을 위해 디바이스를 IoT Hub에 등록해야 합니다. 이 빠른 시작에서는 Azure Cloud Shell을 사용하여 시뮬레이션된 디바이스를 등록합니다.

1. Azure Cloud Shell에서 다음 명령을 실행하여 디바이스 ID를 만듭니다.

   **YourIoTHubName**: 이 자리 표시자를 IoT 허브용으로 선택한 이름으로 바꿉니다.

   **MyAndroidDevice**: 등록 중인 디바이스의 이름입니다. 표시된 대로 **MyAndroidDevice** 를 사용하는 것이 좋습니다. 다른 디바이스 이름을 선택하는 경우 이 문서 전체에서도 해당 이름을 사용해야 하며, 샘플 애플리케이션에서 디바이스 이름을 업데이트한 후 실행해야 합니다.

    ```azurecli-interactive
    az iot hub device-identity create \
      --hub-name {YourIoTHubName} --device-id MyAndroidDevice
    ```

2. Azure Cloud Shell에서 다음 명령을 실행하여 방금 등록한 디바이스의 _디바이스 연결 문자열_ 을 가져옵니다.

   **YourIoTHubName**: 이 자리 표시자를 IoT 허브용으로 선택한 이름으로 바꿉니다.

    ```azurecli-interactive
    az iot hub device-identity connection-string show\
      --hub-name {YourIoTHubName} \
      --device-id MyAndroidDevice \
      --output table
    ```

    다음과 같은 디바이스 연결 문자열을 기록해 둡니다.

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyAndroidDevice;SharedAccessKey={YourSharedAccessKey}`

    이 값은 빠른 시작의 뒷부분에서 사용합니다.

## <a name="retrieve-the-service-connection-string"></a>서비스 연결 문자열 검색

또한 백 엔드 서비스 애플리케이션을 IoT 허브에 연결하여 메서드를 실행하고 메시지를 검색할 수 있게 하려면 _서비스 연결 문자열_ 이 필요합니다. 다음 명령은 IoT Hub에 대한 서비스 연결 문자열을 검색합니다.

**YourIoTHubName**: 이 자리 표시자를 IoT 허브용으로 선택한 이름으로 바꿉니다.

```azurecli-interactive
az iot hub connection-string show --policy-name service --name {YourIoTHubName} --output table
```

다음과 같은 서비스 연결 문자열을 기록해 둡니다.

`HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}`

이 값은 빠른 시작의 뒷부분에서 사용합니다. 서비스 연결 문자열은 이전 단계에서 기록한 디바이스 연결 문자열과 다릅니다.

## <a name="listen-for-direct-method-calls"></a>직접 메서드 호출 수신 대기

이 빠른 시작을 위한 두 샘플은 모두 GitHub에 있는 azure-iot-samples-java 리포지토리의 일부입니다. [azure-iot-samples-java](https://github.com/Azure-Samples/azure-iot-samples-java) 리포지토리를 다운로드하거나 복제합니다.

디바이스 SDK 샘플 애플리케이션은 물리적 Android 디바이스 또는 Android 에뮬레이터에서 실행됩니다. 이 샘플은 IoT 허브의 디바이스별 엔드포인트에 연결하고, 시뮬레이션된 원격 분석 데이터를 전송하고, 허브에서 직접 메서드 호출을 수신 대기합니다. 이 빠른 시작에서 허브의 직접 메서드 호출은 디바이스에 원격 분석을 보내는 간격을 변경하도록 지시합니다. 시뮬레이션된 디바이스는 직접 메서드를 실행한 후 승인을 다시 허브로 보냅니다.

1. Android Studio에서 GitHub 샘플 Android 프로젝트를 엽니다. 프로젝트는 [azure-iot-sample-java](https://github.com/Azure-Samples/azure-iot-samples-java) 리포지토리의 복제되거나 다운로드된 사본인 *\azure-iot-samples-java\iot-hub\Samples\device\AndroidSample* 디렉터리에 있습니다.

2. Android Studio에서 샘플 프로젝트의 *gradle.properties* 를 열고 **Device_Connection_String** 자리 표시자를 이전에 기록한 디바이스 연결 문자열로 바꿉니다.

    ```
    DeviceConnectionString=HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyAndroidDevice;SharedAccessKey={YourSharedAccessKey}
    ```

3. Android Studio에서 **파일** > **Gradle 파일과 프로젝트 동기화** 를 클릭합니다. 빌드가 완료되는지 확인합니다.

   > [!NOTE]
   > 프로젝트 동기화에 실패하는 경우는 다음 이유 중 하나일 수 있습니다.
   >
   > * 프로젝트에서 참조한 Android Gradle 플러그 인 및 Gradle의 버전이 사용자의 Android Studio 버전과 맞지 않습니다. [이러한 지침](https://developer.android.com/studio/releases/gradle-plugin)에 따라 설치용 플러그 인 및 Gradle의 올바른 버전을 참조하고 설치합니다.
   > * Android SDK에 대한 사용권 계약에 서명하지 않았습니다. 빌드 출력의 지침에 따라 사용권 계약에 서명하고 SDK를 다운로드합니다.

4. 빌드가 완료되면 **실행** >  **'앱' 실행** 을 클릭합니다. 물리적 Android 디바이스 또는 Android 에뮬레이터에서 실행되도록 앱을 구성합니다. 물리적 디바이스 또는 에뮬레이터에서 Android 앱을 실행하는 방법에 대한 자세한 내용은 [앱 실행](https://developer.android.com/training/basics/firstapp/running-app)을 참조하세요.

5. 앱이 로드되면 **시작** 단추를 클릭하여 IoT Hub로 원격 분석 데이터를 보내기 시작합니다.

    ![클라이언트 디바이스 android 앱의 샘플 스크린샷](media/quickstart-control-device-android/sample-screenshot.png)

런타임 중에 원격 분석 간격을 업데이트하려면 서비스 SDK 샘플을 실행 중인 상태에서 물리적 디바이스 또는 에뮬레이터에서 앱이 실행 중인 상태여야 합니다.

## <a name="read-the-telemetry-from-your-hub"></a>허브에서 원격 분석 읽기

이 섹션에서는 [IoT 확장](/cli/azure/iot)과 함께 Azure Cloud Shell을 사용하여 Android 디바이스에서 보내는 메시지를 모니터링합니다.

1. Azure Cloud Shell을 통해 다음 명령을 실행하여 IoT 허브의 메시지를 연결하고 읽습니다.

   **YourIoTHubName**: 이 자리 표시자를 IoT 허브용으로 선택한 이름으로 바꿉니다.

    ```azurecli-interactive
    az iot hub monitor-events --hub-name {YourIoTHubName} --output table
    ```

    다음 스크린샷은 Android 디바이스가 보낸 원격 분석 데이터를 IoT 허브가 수신할 때 출력을 보여줍니다.

      ![Azure CLI를 사용하여 디바이스 메시지 읽기](media/quickstart-control-device-android/read-data.png)

기본적으로 원격 분석 앱은 Android 디바이스에서 5초마다 원격 분석 데이터를 보냅니다. 다음 섹션에서는 직접 메서드 호출을 사용하여 Android IoT 디바이스의 원격 분석 간격을 업데이트합니다.

## <a name="call-the-direct-method"></a>직접 메서드 호출

서비스 애플리케이션은 IoT Hub의 서비스 측 엔드포인트에 연결합니다. 애플리케이션은 IoT 허브를 통해 디바이스에 직접 메서드 호출을 하고 승인을 수신 대기합니다.

별도의 물리적 Android 디바이스 또는 Android 에뮬레이터에서 이 앱을 실행합니다.

IoT Hub 백 엔드 서비스 애플리케이션은 일반적으로 IoT Hub의 모든 디바이스를 제어하는 민감한 연결 문자열과 관련된 위험을 완화하기 용이한 클라우드에서 실행됩니다. 이 예제에서는 데모용 Android 앱으로 실행 중입니다. 이 빠른 시작의 다른 언어 버전에서는 일반적인 백 엔드 서비스 애플리케이션과 더욱 긴밀하게 부합하는 예제를 제공합니다.

1. Android Studio에서 GitHub 서비스 샘플 Android 프로젝트를 엽니다. 프로젝트는 [azure-iot-sample-java](https://github.com/Azure-Samples/azure-iot-samples-java) 리포지토리의 복제되거나 다운로드된 사본인 *\azure-iot-samples-java\iot-hub\Samples\service\AndroidSample* 디렉터리에 있습니다.

2. Android Studio에서 샘플 프로젝트의 *gradle.properties* 를 엽니다. **ConnectionString** 및 **DeviceId** 속성의 값을 앞서 본 서비스 연결 문자열 및 등록된 Android 디바이스 ID로 업데이트합니다.

    ```
    ConnectionString=HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}
    DeviceId=MyAndroidDevice
    ```

3. Android Studio에서 **파일** > **Gradle 파일과 프로젝트 동기화** 를 클릭합니다. 빌드가 완료되는지 확인합니다.

   > [!NOTE]
   > 프로젝트 동기화에 실패하는 경우는 다음 이유 중 하나일 수 있습니다.
   >
   > * 프로젝트에서 참조한 Android Gradle 플러그 인 및 Gradle의 버전이 사용자의 Android Studio 버전과 맞지 않습니다. [이러한 지침](https://developer.android.com/studio/releases/gradle-plugin)에 따라 설치용 플러그 인 및 Gradle의 올바른 버전을 참조하고 설치합니다.
   > * Android SDK에 대한 사용권 계약에 서명하지 않았습니다. 빌드 출력의 지침에 따라 사용권 계약에 서명하고 SDK를 다운로드합니다.

4. 빌드가 완료되면 **실행** >  **'앱' 실행** 을 클릭합니다. 별도의 물리적 Android 디바이스 또는 Android 에뮬레이터에서 실행되도록 앱을 구성합니다. 물리적 디바이스 또는 에뮬레이터에서 Android 앱을 실행하는 방법에 대한 자세한 내용은 [앱 실행](https://developer.android.com/training/basics/firstapp/running-app)을 참조하세요.

5. 앱이 로드되면 **메시징 간격 설정** 값을 **1000** 으로 업데이트하고 **호출** 을 클릭합니다.

    원격 분석 메시징 간격은 밀리초 단위입니다. 디바이스 샘플의 기본 원격 분석 간격은 5초로 설정됩니다. 이와 같이 변경하면 원격 분석 데이터를 1초마다 전송하도록 Android IoT 디바이스가 업데이트됩니다.

    ![원격 분석 간격 입력](media/quickstart-control-device-android/enter-telemetry-interval.png)

6. 앱은 메서드가 성공적으로 실행되었는지 여부를 나타내는 승인을 받습니다.

    ![직접 메서드 승인](media/quickstart-control-device-android/direct-method-ack.png)

## <a name="clean-up-resources"></a>리소스 정리

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 백 엔드 애플리케이션에서 디바이스에 직접 메서드를 호출하고, 시뮬레이션된 디바이스 애플리케이션에서 직접 메서드 호출에 응답했습니다.

디바이스-클라우드 메시지를 클라우드의 다른 대상으로 라우팅하는 방법을 알아보려면 다음 자습서로 계속 진행합니다.

> [!div class="nextstepaction"]
> [자습서: 처리를 위해 다른 엔드포인트로 원격 분석 라우팅](tutorial-routing.md)
