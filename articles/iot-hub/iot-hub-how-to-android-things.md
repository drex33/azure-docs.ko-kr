---
title: Azure IoT SDK를 사용하여 Android Things 플랫폼 개발 | Microsoft Docs
description: 개발자 가이드 - Azure IoT Hub SDK를 사용하여 Android Things에서 솔루션을 개발하는 방법을 알아봅니다.
author: eross-msft
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/30/2019
ms.author: lizross
ms.openlocfilehash: 29f30fbdc722fdfedf68fb49f0280e06247041f3
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132552701"
---
# <a name="develop-for-android-things-platform-using-azure-iot-sdks"></a>Azure IoT SDK를 사용하여 Android Things 플랫폼 개발

[Azure IoT Hub SDK](./iot-hub-devguide-sdks.md)는 Windows, Linux, OSX, MBED, 그리고 Android 및 iOS와 같은 모바일 플랫폼을 포함하여 인기 있는 플랫폼에 대한 첫 번째 계층 지원을 제공합니다.  Java SDK는 IoT 배포에서 더 나은 선택과 유연성을 지원한다는 당사 약속의 일환으로 [Android Things](https://developer.android.com/things/) 플랫폼도 지원합니다.  개발자는 디바이스 쪽에서 Android Things 운영 체제의 이점을 활용함과 동시에 [Azure IoT Hub](about-iot-hub.md)를 수백만 개의 동시 연결 디바이스로 확장하는 중앙 메시지 허브로 사용할 수 있습니다.

이 자습서에서는 Azure IoT Java SDK를 사용하여 Android Things에서 디바이스 쪽 애플리케이션을 빌드하는 단계를 설명합니다.

## <a name="prerequisites"></a>사전 요구 사항

* Android Things는 Android Things OS를 실행하는 하드웨어를 지원합니다.  Android Things OS를 플래시하는 방법은 [Android Things 설명서](https://developer.android.com/things/get-started/kits#flash-at)를 참조하세요.  키보드, 디스플레이 및 마우스 등과 같은 필수 주변 장치가 연결된 상태에서 Android Things 디바이스가 인터넷에 연결되었는지 확인합니다.  이 자습서에서는 Raspberry Pi 3을 사용합니다.

* [Android Studio](https://developer.android.com/studio/)의 최신 버전

* [Git](https://git-scm.com/)의 최신 버전

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="create-an-iot-hub"></a>IoT Hub 만들기

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>디바이스 등록

연결을 위해 디바이스를 IoT Hub에 등록해야 합니다. 이 빠른 시작에서는 Azure Cloud Shell을 사용하여 시뮬레이션된 디바이스를 등록합니다.

1. Azure Cloud Shell에서 다음 명령을 실행하여 IoT Hub CLI 확장을 추가하고 디바이스 ID를 만듭니다.

   **YourIoTHubName**: 이 자리 표시자를 IoT 허브용으로 선택한 이름으로 바꿉니다.

   **MyAndroidThingsDevice**: 등록된 디바이스에 지정된 이름입니다. MyAndroidThingsDevice를 표시된 것처럼 사용합니다. 다른 디바이스 이름을 선택하는 경우 이 문서 전체에서 해당 이름을 사용해야 하고, 샘플 애플리케이션에서 디바이스 이름을 업데이트한 후 실행해야 합니다.

    ```azurecli-interactive
    az extension add --name azure-iot
    az iot hub device-identity create --hub-name YourIoTHubName --device-id MyAndroidThingsDevice
    ```

2. Azure Cloud Shell에서 다음 명령을 실행하여 방금 등록한 디바이스의 ‘디바이스 연결 문자열’을 가져옵니다. 아래 `YourIoTHubName`을 IoT 허브에 대해 선택한 이름으로 바꿉니다.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name YourIoTHubName --device-id MyAndroidThingsDevice --output table
    ```

    다음과 같은 디바이스 연결 문자열을 기록해 둡니다.

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyAndroidThingsDevice;SharedAccessKey={YourSharedAccessKey}`

    이 값은 빠른 시작의 뒷부분에서 사용합니다.

## <a name="building-an-android-things-application"></a>Android Things 애플리케이션 빌드

1. Android Things 애플리케이션을 빌드하는 첫 번째 단계는 Android Things 디바이스에 연결하는 것입니다. Android Things 디바이스를 디스플레이에 연결하고 인터넷에 연결합니다. Android Things는 WiFi에 연결하는 방법에 관한 [설명서](https://developer.android.com/things/get-started/kits)를 제공합니다. 인터넷에 연결한 후 네트워크 아래에 나열된 IP 주소를 기록해 둡니다.

2. [adb](https://developer.android.com/studio/command-line/adb) 도구를 사용하여 위에서 기록한 IP 주소로 Android Things 디바이스에 연결합니다. 터미널에서 이 명령을 사용하여 연결을 다시 한 번 확인합니다. “연결됨”으로 나열된 디바이스가 나타납니다.

   ```
   adb devices
   ```

3. 이 [리포지토리](https://github.com/Azure-Samples/azure-iot-samples-java)에서 Android/Android Things 샘플을 다운로드하거나 Git를 사용합니다.

   ```
   git clone https://github.com/Azure-Samples/azure-iot-samples-java.git
   ```

4. Android Studio에서 "\azure-iot-samples-java\iot-hub\Samples\device\AndroidSample"에 있는 Android 프로젝트를 엽니다.

5. gradle.properties 파일을 열고 "Device_connection_string"을 앞에서 기록해 둔 디바이스 연결 문자열로 바꿉니다.
 
6. 실행 - 디버그를 클릭하고 디바이스를 선택하여 이 코드를 Android Things 디바이스에 배포합니다.

7. 애플리케이션이 성공적으로 시작되면 Android Things 디바이스에서 실행 중인 애플리케이션을 확인할 수 있습니다. 이 샘플 애플리케이션은 임의로 생성된 온도 판독값을 보냅니다.

## <a name="read-the-telemetry-from-your-hub"></a>허브에서 원격 분석 읽기

IoT Hub를 통해 데이터를 받은 상태 그대로 볼 수 있습니다. IoT Hub CLI 확장은 IoT Hub의 서비스 쪽 **이벤트** 엔드포인트에 연결할 수 있습니다. 이 확장은 시뮬레이트된 디바이스에서 보낸 디바이스-클라우드 메시지를 받습니다. IoT Hub 백 엔드 애플리케이션은 일반적으로 클라우드에서 실행되며 디바이스-클라우드 메시지를 수신하고 처리합니다.

Azure Cloud Shell에서 다음 명령을 실행하고, `YourIoTHubName`을 해당 IoT 허브 이름으로 바꿉니다.

```azurecli-interactive
az iot hub monitor-events --device-id MyAndroidThingsDevice --hub-name YourIoTHubName
```

## <a name="clean-up-resources"></a>리소스 정리

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>다음 단계

* IoT Hub 디바이스 SDK를 사용하여 [연결 및 신뢰할 수 있는 메시지를 관리하는 방법](iot-hub-reliability-features-in-sdks.md)에 대해 알아보세요.
* iOS 및 Android와 같은 [모바일 플랫폼용 개발](iot-hub-how-to-develop-for-mobile-devices.md) 방법에 대해 알아보세요.
