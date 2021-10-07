---
author: dominicbetts
ms.author: dobett
ms.service: iot-develop
ms.topic: include
ms.date: 11/20/2020
ms.openlocfilehash: a047350f8e91a5ce25d8fe8ee60ffd6aa12aaae7
ms.sourcegitcommit: 557ed4e74f0629b6d2a543e1228f65a3e01bf3ac
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/05/2021
ms.locfileid: "129516593"
---
IoT 플러그 앤 플레이를 사용하면 기본 디바이스 구현에 대한 지식이 없어도 디바이스 기능과 상호 작용할 수 있으므로 IoT가 간소화됩니다. 이 빠른 시작에서는 Java를 사용하여 솔루션에 연결된 IoT 플러그 앤 플레이 디바이스에 연결하고 제어하는 방법을 보여줍니다.

## <a name="prerequisites"></a>필수 구성 요소

[!INCLUDE [iot-pnp-prerequisites](iot-pnp-prerequisites.md)]

Windows에서 이 빠른 시작을 완료하려면 로컬 Windows 환경에 다음 소프트웨어를 설치합니다.

* Java SE Development Kit 8. [Azure 및 Azure Stack에 대한 Java 장기 지원](/java/azure/jdk/)의 **장기 지원** 에서 **Java 8** 을 선택합니다.
* [Apache Maven 3](https://maven.apache.org/download.cgi).

### <a name="clone-the-sdk-repository-with-the-sample-code"></a>샘플 코드를 사용하여 SDK 리포지토리 복제

[자습서: Windows에서 실행되는 샘플 IoT 플러그 앤 플레이 디바이스 애플리케이션을 IoT Hub(Java)에 연결](../articles/iot-develop/tutorial-connect-device.md)을 완료한 경우 이미 리포지토리를 복제한 것입니다.

선택한 디렉터리에서 명령 프롬프트를 엽니다. 다음 명령을 실행하여 [Java용 Microsoft Azure IoT SDK](https://github.com/Azure/azure-iot-sdk-java) GitHub 리포지토리를 다음 위치에 복제합니다.

```cmd
git clone https://github.com/Azure/azure-iot-sdk-java.git
```

## <a name="build-and-run-the-sample-device"></a>샘플 디바이스 빌드 및 실행

[!INCLUDE [iot-pnp-environment](iot-pnp-environment.md)]

샘플 구성에 대해 자세히 알아보려면 [샘플 추가 정보](https://github.com/Azure/azure-iot-sdk-java/blob/main/device/iot-device-samples/readme.md)를 참조하세요.

이 빠른 시작에서는 Java로 작성한 샘플 자동 온도 조절 디바이스를 IoT 플러그 앤 플레이 디바이스로 사용합니다. 샘플 디바이스를 실행하려면 다음을 수행합니다.

1. 터미널 창을 열고 GitHub에서 복제한 Java용 Microsoft Azure IoT SDK 리포지토리가 포함된 로컬 폴더로 이동합니다.

1. 이 터미널 창은 이제 **디바이스** 터미널로 사용됩니다. 복제된 리포지토리의 루트 폴더로 이동합니다. 다음 명령을 실행하여 모든 종속 요소를 설치합니다.

1. 다음 명령을 실행하여 샘플 디바이스 애플리케이션을 빌드합니다.

    ```cmd
    mvn install -T 2C -DskipTests
    ```

1. 샘플 디바이스 애플리케이션을 실행하려면 *device\iot-device-samples\pnp-device-sample\thermostat-device-sample* 폴더로 이동하고 다음 명령을 실행합니다.

    ```cmd
    mvn exec:java -Dexec.mainClass="samples.com.microsoft.azure.sdk.iot.device.Thermostat"
    ```

이제 디바이스가 명령 및 속성 업데이트를 받을 준비가 되었으며, 허브로 원격 분석 데이터를 보내기 시작했습니다. 다음 단계를 완료하는 동안 샘플 디바이스를 계속 실행하세요.

## <a name="run-the-sample-solution"></a>샘플 솔루션 실행

[IoT 플러그 앤 플레이 빠른 시작 및 자습서](../articles/iot-develop/set-up-environment.md)에서는 IoT 허브 및 디바이스에 연결하도록 샘플을 구성하는 두 가지 환경 변수를 만들었습니다.

* **IOTHUB_CONNECTION_STRING**: 이전에 기록한 IoT 허브 연결 문자열입니다.
* **IOTHUB_DEVICE_ID**: `"my-pnp-device"`.

이 빠른 시작에서는 Java로 작성된 샘플 IoT 솔루션을 사용하여 방금 설정한 샘플 디바이스와 상호 작용합니다.

> [!NOTE]
> 이 샘플에서는 **IoT Hub 서비스 클라이언트** 에서 **com.microsoft.azure.sdk.iot.service** 네임스페이스를 사용합니다. 디지털 쌍 API를 비롯한 API에 대한 자세한 내용은 [서비스 개발자 가이드](../articles/iot-develop/concepts-developer-guide-service.md)를 참조하세요.

1. **서비스** 터미널로 사용할 또 다른 터미널 창을 엽니다.

1. 복제된 Java SDK 리포지토리에서 *service\iot-service-samples\pnp-service-sample\thermostat-service-sample* 폴더로 이동합니다.

1. 샘플 서비스 애플리케이션을 실행하려면 다음 명령을 실행합니다.

    ```cmd
    mvm exec:java -Dexec.mainClass="samples.com.microsoft.azure.sdk.iot.service.Thermostat"
    ```

### <a name="get-device-twin"></a>디바이스 쌍 가져오기

다음 코드 조각에서는 서비스에서 디바이스 쌍을 검색하는 방법을 보여 줍니다.

```java
 // Get the twin and retrieve model Id set by Device client.
DeviceTwinDevice twin = new DeviceTwinDevice(deviceId);
twinClient.getTwin(twin);
System.out.println("Model Id of this Twin is: " + twin.getModelId());
```

### <a name="update-a-device-twin"></a>디바이스 쌍 업데이트

다음 코드 조각에서는 *패치* 를 사용하여 디바이스 쌍을 통해 속성을 업데이트하는 방법을 보여줍니다.

```java
String propertyName = "targetTemperature";
double propertyValue = 60.2;
twin.setDesiredProperties(Collections.singleton(new Pair(propertyName, propertyValue)));
twinClient.updateTwin(twin);
```

디바이스 출력은 디바이스가 이 속성 업데이트에 응답하는 방법을 보여 줍니다.

### <a name="invoke-a-command"></a>명령 호출

다음 코드 조각에서는 디바이스에서 명령을 호출하는 방법을 보여 줍니다.

```java
// The method to invoke for a device without components should be "methodName" as defined in the DTDL.
String methodToInvoke = "getMaxMinReport";
System.out.println("Invoking method: " + methodToInvoke);

Long responseTimeout = TimeUnit.SECONDS.toSeconds(200);
Long connectTimeout = TimeUnit.SECONDS.toSeconds(5);

// Invoke the command.
String commandInput = ZonedDateTime.now(ZoneOffset.UTC).minusMinutes(5).format(DateTimeFormatter.ISO_DATE_TIME);
MethodResult result = methodClient.invoke(deviceId, methodToInvoke, responseTimeout, connectTimeout, commandInput);
if(result == null)
{
    throw new IOException("Method result is null");
}

System.out.println("Method result status is: " + result.getStatus());
```

디바이스 출력은 디바이스가 이 명령에 응답하는 방법을 보여 줍니다.
