---
author: dominicbetts
ms.author: dobett
ms.service: iot-develop
ms.topic: include
ms.date: 11/20/2020
ms.openlocfilehash: 15aeb7ca8c98b5e32ab662e4083cc842d19d5235
ms.sourcegitcommit: 557ed4e74f0629b6d2a543e1228f65a3e01bf3ac
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/05/2021
ms.locfileid: "129516670"
---
이 빠른 시작에서는 샘플 IoT 플러그 앤 플레이 디바이스 애플리케이션을 빌드하고, IoT 허브에 연결하고, Azure IoT 탐색기 도구를 사용하여 전송되는 원격 분석을 확인하는 방법을 보여줍니다. 샘플 애플리케이션은 Java로 작성되었으며 Java용 Azure IoT 디바이스 SDK에 포함되어 있습니다. 솔루션 빌더는 디바이스 코드를 볼 필요 없이 Azure IoT 탐색기 도구를 사용하여 IoT 플러그 앤 플레이 디바이스의 기능을 이해할 수 있습니다.

[![코드 찾아보기](../articles/iot-central/core/media/common/browse-code.svg)](https://github.com/Azure/azure-iot-sdk-java/tree/main/device/iot-device-samples/pnp-device-sample)

## <a name="prerequisites"></a>필수 조건

[!INCLUDE [iot-pnp-prerequisites](iot-pnp-prerequisites.md)]

Windows에서 이 빠른 시작을 완료하려면 로컬 Windows 환경에 다음 소프트웨어를 설치합니다.

* Java SE Development Kit 8. [Azure 및 Azure Stack에 대한 Java 장기 지원](/java/azure/jdk/)의 **장기 지원** 에서 **Java 8** 을 선택합니다.
* [Apache Maven 3](https://maven.apache.org/download.cgi).

## <a name="download-the-code"></a>코드 다운로드

이 빠른 시작에서는 Azure IoT Hub Device Java SDK를 복제하고 빌드하는 데 사용할 수 있는 개발 환경을 준비합니다.

선택한 디렉터리에서 명령 프롬프트를 엽니다. 다음 명령을 실행하여 [Azure IoT Java SDK 및 라이브러리](https://github.com/Azure/azure-iot-sdk-java) GitHub 리포지토리를 이 위치에 복제합니다.

```cmd
git clone https://github.com/Azure/azure-iot-sdk-java.git
```

## <a name="build-the-code"></a>코드 빌드

Windows에서 복제된 Java SDK 리포지토리의 루트 폴더로 이동합니다.

다음 명령을 실행하여 샘플 애플리케이션을 빌드합니다.

```cmd
mvn install -T 2C -DskipTests
```

## <a name="run-the-device-sample"></a>디바이스 샘플 실행

[!INCLUDE [iot-pnp-environment](iot-pnp-environment.md)]

샘플 구성에 대해 자세히 알아보려면 [샘플 추가 정보](https://github.com/Azure/azure-iot-sdk-java/blob/main/device/iot-device-samples/pnp-device-sample/readme.md)를 참조하세요.

*\device\iot-device-samples\pnp-device-sample\thermostat-device-sample* 폴더로 이동합니다.

샘플 애플리케이션을 실행하려면 다음 명령을 실행합니다.

```cmd
mvn exec:java -Dexec.mainClass="samples.com.microsoft.azure.sdk.iot.device.Thermostat"
```

이제 디바이스가 명령 및 속성 업데이트를 받을 준비가 되었으며, 허브로 원격 분석 데이터를 보내기 시작했습니다. 다음 단계를 완료하는 동안 샘플을 계속 실행하세요.

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>Azure IoT 탐색기를 사용하여 코드의 유효성을 검사합니다.

디바이스 클라이언트 샘플이 시작된 후 Azure IoT 탐색기 도구를 사용하여 작동하는지 확인합니다.

[!INCLUDE [iot-pnp-iot-explorer.md](iot-pnp-iot-explorer.md)]

## <a name="review-the-code"></a>코드 검토

이 샘플은 간단한 IoT 플러그 앤 플레이 온도 조절 디바이스를 구현합니다. 이 샘플에서 구현하는 모델은 IoT 플러그 앤 플레이 [구성 요소](../articles/iot-develop/concepts-modeling-guide.md)를 사용하지 않습니다. [온도 조절 디바이스용 DTDL 모델 파일](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/Thermostat.json)은 디바이스에서 구현하는 원격 분석, 속성 및 명령을 정의합니다.

디바이스 코드는 표준 `DeviceClient` 클래스를 사용하여 IoT 허브에 연결합니다. 디바이스는 연결 요청에서 구현하는 DTDL 모델의 모델 ID를 보냅니다. 모델 ID를 보내는 디바이스는 IoT 플러그 앤 플레이 디바이스입니다.

```java
private static void initializeDeviceClient() throws URISyntaxException, IOException {
    ClientOptions options = new ClientOptions();
    options.setModelId(MODEL_ID);
    deviceClient = new DeviceClient(deviceConnectionString, protocol, options);

    deviceClient.registerConnectionStatusChangeCallback((status, statusChangeReason, throwable, callbackContext) -> {
        log.debug("Connection status change registered: status={}, reason={}", status, statusChangeReason);

        if (throwable != null) {
            log.debug("The connection status change was caused by the following Throwable: {}", throwable.getMessage());
            throwable.printStackTrace();
        }
    }, deviceClient);

    deviceClient.open();
}
```

모델 ID는 다음 코드 조각과 같이 코드에 저장됩니다.

```java
private static final String MODEL_ID = "dtmi:com:example:Thermostat;1";
```

속성을 업데이트하고, 명령을 처리하고, 원격 분석을 보내는 코드는 IoT 플러그 앤 플레이 규칙을 사용하지 않는 디바이스의 코드와 동일합니다.

이 샘플에서는 JSON 라이브러리를 사용하여 IoT 허브에서 전송된 페이로드의 JSON 개체를 구문 분석합니다.

```java
import com.google.gson.Gson;

...

Date since = new Gson().fromJson(jsonRequest, Date.class);
```
