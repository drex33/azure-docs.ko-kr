---
title: Azure IoT Edge용 모듈 개발 | Microsoft Docs
description: 런타임 및 IoT Hub과 통신할 수 있는 Azure IoT Edge용 사용자 지정 모듈을 개발합니다.
author: kgremban
ms.author: kgremban
ms.date: 09/03/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 364ecfadf28d4b4b1d52544945f73ed48bdcb20e
ms.sourcegitcommit: 66b6e640e2a294a7fbbdb3309b4829df526d863d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/01/2021
ms.locfileid: "133364305"
---
# <a name="develop-your-own-iot-edge-modules"></a>사용자 고유의 IoT Edge 모듈 개발

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

Azure IoT Edge 모듈은 다른 Azure 서비스와 연결하고 더 큰 클라우드 데이터 파이프라인에 속할 수 있습니다. 이 문서에서는 IoT Edge 런타임 및 IoT Hub, 나머지 Azure 클라우드와 통신하기 위한 모듈을 개발하는 방법을 설명합니다.

## <a name="iot-edge-runtime-environment"></a>IoT Edge 런타임 환경

IoT Edge 런타임은 여러 IoT Edge 모듈의 기능을 통합하고 이를 IoT Edge 디바이스에 배포하기 위한 인프라를 제공합니다. 모든 프로그램을 IoT Edge 모듈로 패키지할 수 있습니다. IoT Edge 통신 및 관리 기능을 활용하기 위해 모듈에서 실행되는 프로그램은 Azure IoT 디바이스 SDK를 사용하여 로컬 IoT Edge 허브에 연결할 수 있습니다.
::: moniker range=">=iotedge-2020-11"
모듈은 또한 MQTT 클라이언트를 사용하여 로컬 IoT Edge 허브 MQTT 브로커에 연결할 수 있습니다.
::: moniker-end

### <a name="packaging-your-program-as-an-iot-edge-module"></a>프로그램을 IoT Edge 모듈로 패키징

IoT Edge 디바이스에 프로그램을 배포하려면 먼저 Docker 호환 엔진으로 컨테이너화되고 실행되어야 합니다. IoT Edge는 Docker를 지원하는 오픈 소스 프로젝트인 [Moby](https://github.com/moby/moby)를 Docker 호환 엔진으로 사용합니다. Docker에 사용한 것과 동일한 매개변수를 IoT Edge 모듈에 전달할 수 있습니다. 자세한 내용은 [IoT Edge 모듈에 대한 컨테이너 만들기 옵션 구성 방법](how-to-use-create-options.md)을 참조하세요.

## <a name="using-the-iot-edge-hub"></a>IoT Edge 허브 사용

IoT Edge 허브는 IoT Hub에 프록시 및 로컬 통신이라는 두 가지 주요 기능을 제공합니다.

### <a name="connecting-to-iot-edge-hub-from-a-module"></a>모듈에서 IoT Edge 허브에 연결

모듈에서 로컬 IoT Edge 허브에 연결하기 위해서는 클라이언트와 동일한 연결 단계가 사용됩니다. 자세한 내용은 [IoT Edge 허브에 연결](iot-edge-runtime.md#connecting-to-the-iot-edge-hub)을 참조하세요.

AMQP 또는 MQTT로 IoT Edge 라우팅을 사용하려면 Azure IoT SDK에서 ModuleClient를 사용할 수 있습니다. DeviceClient 인스턴스가 IoT Hub에 IoT 디바이스를 연결하는 방법과 비슷하게 디바이스에서 실행 중인 IoT Edge 허브에 모듈을 연결하는 ModuleClient 인스턴스를 만듭니다. ModuleClient 클래스 및 해당 통신 방법에 대한 자세한 내용은 원하는 SDK 언어([C#](/dotnet/api/microsoft.azure.devices.client.moduleclient), [C](/azure/iot-hub/iot-c-sdk-ref/iothub-module-client-h), [Python](/python/api/azure-iot-device/azure.iot.device.iothubmoduleclient), [Java](/java/api/com.microsoft.azure.sdk.iot.device.moduleclient) 또는 [Node.js](/javascript/api/azure-iot-device/moduleclient))에 대한 API 참조를 확인하세요.

<!-- <1.2> -->
::: moniker range=">=iotedge-2020-11"

IoT Edge MQTT 브로커를 사용하려면 고유 MQTT 클라이언트를 가져오고 IoT Edge 데몬 워크로드 API에서 검색한 정보를 사용하여 연결을 직접 시작해야 합니다. <!--Need to add details here-->

MQTT 브로커를 사용한 라우팅 또는 게시/구독 중 무엇을 선택해야 하는지에 대한 자세한 내용은 [로컬 통신](iot-edge-runtime.md#local-communication)을 참조하세요.

### <a name="mqtt-broker-primitives"></a>MQTT 브로커 기본 형식

#### <a name="send-a-message-on-a-user-defined-topic"></a>사용자 정의 항목에 대한 메시지 보내기

IoT Edge MQTT 브로커를 사용하면 사용자 정의 항목에 대해 메시지를 게시할 수 있습니다. 이렇게 하려면 특정 항목으로 게시하도록 모듈에 권한을 부여한 후 MQTT 브로커에 연결할 때 암호로 사용할 토큰을 워크로드 API에서 가져오고, 마지막으로 선택한 MQTT 클라이언트에 권한 부여된 항목에 대한 메시지를 게시합니다.

#### <a name="receive-messages-on-a-user-defined-topic"></a>사용자 정의 항목에 대한 메시지 수신

IoT Edge MQTT 브로커에서 메시지 수신도 이와 비슷합니다. 먼저 모듈이 특정 항목을 구독하도록 권한 부여되었는지 확인한 후 MQTT 브로커에 연결할 때 암호로 사용할 토큰을 워크로드 API에서 가져오고, 마지막으로 선택한 MQTT 클라이언트에 권한 부여된 항목에 대한 메시지를 구독합니다.

::: moniker-end

### <a name="iot-hub-primitives"></a>IoT Hub 기본 형식

IoT Hub는 다음과 같은 점에서 디바이스와 비슷하게 모듈 인스턴스를 표시합니다.

* [디바이스-클라우드 메시지](../iot-hub/iot-hub-devguide-messaging.md)을 보낼 수 있습니다.
* 특별히 해당 ID에서 대상으로 지정된 [직접 메서드](../iot-hub/iot-hub-devguide-direct-methods.md)를 받을 수 있습니다.
* [디바이스 쌍](../iot-hub/iot-hub-devguide-device-twins.md) 및 해당 디바이스의 다른 모듈 쌍과 별개이며 격리된 모듈 쌍이 있습니다.

현재까지 모듈은 클라우드-디바이스 메시지를 수신하거나 파일 업로드 기능을 사용할 수 없습니다.

모듈을 작성할 때 IoT Edge 허브에 연결하고 디바이스 애플리케이션에 IoT Hub를 사용할 때와 같이 IoT Hub 기본 형식을 사용할 수 있습니다. IoT Edge 모듈과 IoT 디바이스 애플리케이션 간의 유일한 차이는 디바이스 ID 대신 모듈 ID를 참조해야 한다는 것입니다.

#### <a name="device-to-cloud-messages"></a>디바이스-클라우드 메시지

IoT Edge 모듈은 로컬 브로커로 작동하고 메시지를 클라우드로 전달하는 IoT Edge 허브를 통해 클라우드에 메시지를 전파할 수 있습니다. 디바이스-클라우드 메시지의 복잡한 처리를 사용하도록 설정하기 위해 IoT Edge 모듈은 다른 모듈 또는 디바이스에서 해당 로컬 IoT Edge 허브로 보내는 메시지를 가로채서 처리하고 처리된 데이터가 포함된 새 메시지를 보낼 수 있습니다. 따라서 IoT Edge 모듈 체인을 만들어서 로컬 처리 파이프라인을 빌드할 수 있습니다.

라우팅을 사용 하 여 장치-클라우드 원격 분석 메시지를 보내려면 Azure IoT SDK의 ModuleClient를 사용 합니다. Azure IoT SDK에서 각 모듈은 특수 MQTT 항목에 매핑되는 *입력* 및 *출력* 엔드포인트 개념을 포함합니다. `ModuleClient.sendMessageAsync` 메서드를 사용하여 모듈의 출력 엔드포인트에서 메시지를 보냅니다. 그런 후 edgeHub에서 경로를 구성하여 이 출력 엔드포인트를 IoT Hub로 보냅니다.

<!-- <1.2> -->
::: moniker range=">=iotedge-2020-11"

MQTT 브로커로 디바이스-클라우드 원격 분석 메시지를 보내는 것은 사용자 정의 항목으로 메시지를 게시하는 것과 비슷하지만 해당 모듈에 대해 `devices/<device_name>/modules/<module_name>/messages/events` IoT Hub 특수 항목이 사용됩니다. 권한 부여를 적절 하 게 설정 해야 합니다. 또한 이 항목의 메시지를 클라우드로 전달하도록 MQTT 브리지를 구성해야 합니다.

::: moniker-end

라우팅을 사용하여 메시지를 처리하려면 먼저 다른 엔드포인트(모듈 또는 디바이스)에서 수신되는 메시지를 모듈의 입력 엔드포인트로 전송하도록 경로를 설정한 후 모듈의 입력 엔드포인트에서 메시지를 수신 대기해야 합니다. 새 메시지가 돌아올 때마다 Azure IoT SDK에서 콜백 함수가 트리거됩니다. 이 콜백 함수로 메시지를 처리하고 선택적으로 모듈 엔드포인트 큐에서 새 메시지를 보냅니다.

<!-- <1.2> -->
::: moniker range=">=iotedge-2020-11"

MQTT 브로커를 사용하여 메시지를 처리하는 것은 사용자 정의 항목으로 메시지를 구독하는 것과 비슷하지만 모듈의 출력 큐의 `devices/<device_name>/modules/<module_name>/messages/events` IoT Edge 특수 항목을 사용합니다. 권한 부여를 적절 하 게 설정 해야 합니다. 선택적으로 선택한 항목으로 새 메시지를 보낼 수 있습니다.

::: moniker-end

#### <a name="twins"></a>쌍

트윈은 IoT Hub에서 제공되는 기본 형식 중 하나입니다. 메타 데이터, 구성 및 조건을 포함 하 여 상태 정보를 저장 하는 JSON 문서가 있습니다. 각 모듈 또는 디바이스에는 고유한 트윈이 포함됩니다.

Azure IoT SDK로 모듈 트윈을 가져오려면 `ModuleClient.getTwin` 메서드를 호출합니다.

<!-- <1.2> -->
::: moniker range=">=iotedge-2020-11"

MQTT 클라이언트를 사용 하 여 모듈 쌍을 가져오려면 쌍 가져오기는 일반적인 MQTT 패턴이 아니기 때문에 약간 더 많은 작업이 수반 됩니다. 먼저 모듈이 IoT Hub 특수 항목 `$iothub/twin/res/#`를 구독해야 합니다. 이 항목 이름은 IoT Hub에서 상속되며, 모든 디바이스/모듈이 동일한 항목을 구독해야 합니다. 디바이스가 서로 트윈을 수신하지는 않습니다. 모든 장치가 동일한 토픽 이름을 수신 하는 경우에도 IoT Hub에서 어떤 쌍을 전달 해야 하는지 알 수 있습니다. 구독을 만든 후에는 모듈이 ID 요청 ID가 `$iothub/twin/GET/?$rid=1234`인 IoT Hub 특수 항목에 메시지를 게시하여 트윈을 요청해야 합니다. 이 요청 ID는 요청된 데이터와 함께 IoT Hub에서 다시 전송되는 임의 ID(즉, GUID)입니다. 클라이언트는 이 방법으로 요청을 응답과 연결합니다. 결과 코드는 성공이 200으로 인코딩되는 HTTP와 비슷한 상태 코입니다.

::: moniker-end

Azure IoT SDK로 모듈 트윈 패치를 받으려면 콜백 함수를 구현하고 트윈 패치가 들어올 때마다 콜백 함수가 트리거되도록 Azure IoT SDK에서 `ModuleClient.moduleTwinCallback` 메서드로 등록합니다.

<!-- <1.2> -->
::: moniker range=">=iotedge-2020-11"

MQTT 클라이언트를 사용 하 여 모듈 쌍 패치를 수신 하기 위해 프로세스는 전체 쌍을 받는 것과 비슷합니다. 클라이언트는 특수 IoT Hub 항목을 구독 해야 `$iothub/twin/PATCH/properties/desired/#` 합니다. 구독이 수행된 후 IoT Hub가 원하는 트윈 섹션 변경 사항을 보내면 클라이언트가 이를 수신합니다.

::: moniker-end

#### <a name="receive-direct-methods"></a>직접 메서드 수신

Azure IoT SDK로 직접 메서드를 수신하려면 콜백 함수를 구현하고 직접 메서드가 수신될 때마다 콜백 함수가 트리거되도록 Azure IoT SDK에서 `ModuleClient.methodCallback` 메서드로 이를 등록합니다.

<!-- <1.2> -->
::: moniker range=">=iotedge-2020-11"

MQTT 클라이언트로 직접 메서드를 수신하는 프로세스는 트윈 패치를 받는 것과 매우 비슷합니다. 클라이언트가 호출 수신을 다시 확인해야 하고 동시에 일부 정보를 다시 보낼 수 있습니다. 구독할 특수 IoT Hub 항목은 `$iothub/methods/POST/#`입니다.

::: moniker-end

## <a name="language-and-architecture-support"></a>언어 및 아키텍처 지원

IoT Edge는 요구에 맞는 시나리오를 빌드할 수 있도록 여러 가지 운영 체제, 디바이스 아키텍처 및 개발 언어를 지원합니다. 이 섹션에서는 사용자 지정 IoT Edge 모듈 개발을 위한 여러 옵션들을 살펴볼 수 있습니다. 각 언어의 지원 도구 및 요구 사항에 대한 자세한 내용은 [IoT Edge를 위한 개발 및 테스트 환경 준비](development-environment.md)를 참조하세요.

### <a name="linux"></a>Linux

다음 표의 모든 언어에 대해 IoT Edge는 AMD64 및 ARM32 Linux 컨테이너 개발을 지원합니다.

| 개발 언어 | 개발 도구 |
| -------------------- | ----------------- |
| C | Visual Studio Code<br>Visual Studio 2017/2019 |
| C# | Visual Studio Code<br>Visual Studio 2017/2019 |
| Java | Visual Studio Code |
| Node.js | Visual Studio Code |
| Python | Visual Studio Code |

>[!NOTE]
>AMD64 development 컴퓨터에서 ARM32 IoT Edge 모듈을 컴파일하는 것과 같은 플랫폼 간 컴파일의 경우 IoT Edge 모듈과 일치 하는 대상 장치 아키텍처에서 코드를 컴파일하기 위해 개발 컴퓨터를 구성 해야 합니다. 자세한 내용은 [원격 디바이스에서 IoT Edge 모듈 빌드 및 디버그를](https://devblogs.microsoft.com/iotdev/easily-build-and-debug-iot-edge-modules-on-your-remote-device-with-azure-iot-edge-for-vs-code-1-9-0/) 참조하여 IoT Edge 모듈과 일치하는 대상 디바이스 아키텍처에서 코드를 컴파일하도록 개발 머신을 구성합니다.
>
>또한 ARM64 Linux 컨테이너에 대한 지원은 [공개 미리 보기로](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)제공됩니다. 자세한 내용은 [Visual Studio Code(미리 보기)에서 ARM64 IoT Edge 모듈 개발 및 디버그](https://devblogs.microsoft.com/iotdev/develop-and-debug-arm64-iot-edge-modules-in-visual-studio-code-preview)를 참조하세요.

### <a name="windows"></a>Windows

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"
다음 표의 모든 언어에 대해 IoT Edge는 AMD64 Windows 컨테이너를 위한 개발을 지원합니다.

| 개발 언어 | 개발 도구 |
| -------------------- | ----------------- |
| C | Visual Studio 2017/2019 |
| C# | Visual Studio Code(디버깅 기능 없음)<br>Visual Studio 2017/2019 |
:::moniker-end
<!-- end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

IoT Edge 1.1 LTS는 Windows 컨테이너를 지원하는 마지막 릴리스 채널입니다. 버전 1.2부터 Windows 컨테이너가 지원되지 않습니다.

Windows 컨테이너를 사용한 개발에 대한 자세한 내용은 이 문서의 [IoT Edge 1.1](?view=iotedge-2018-06&preserve-view=true) 버전을 참조하세요.

:::moniker-end
<!-- end 1.2 -->

## <a name="module-security"></a>모듈 보안

보안을 염두에 두고 모듈을 개발해야 합니다. 모듈 보안에 대한 자세한 내용은 [Docker 보안을](https://docs.docker.com/engine/security/)참조하세요.

모듈 보안을 개선하기 위해 IoT Edge 기본적으로 일부 컨테이너 기능을 사용하지 않도록 설정합니다. 필요한 경우 기본값을 재정의하여 권한 있는 기능을 모듈에 제공할 수 있습니다.

### <a name="allow-elevated-docker-permissions"></a>관리자 권한 Docker 권한 허용

IoT Edge 디바이스의 구성 파일에 라는 매개 변수가 `allow_elevated_docker_permissions` 있습니다. **true로** 설정하면 이 플래그는 `--privileged` 컨테이너에서 Docker HostConfig의 필드에 정의한 추가 기능뿐만 아니라 `CapAdd` [플래그를 허용합니다.](how-to-use-create-options.md)

>[!NOTE]
>현재 이 플래그는 기본적으로 **true이며,** 이를 통해 배포에서 모듈에 권한 있는 권한을 부여할 수 있습니다. 디바이스 보안을 향상하려면 이 플래그를 false로 설정하는 것이 좋습니다. 나중에 이 플래그는 기본적으로 **false로** 설정됩니다.

### <a name="enable-cap_chown-and-cap_setuid"></a>CAP_CHOWN 및 CAP_SETUID 사용

docker 기능 **CAP_CHOWN** 및 **CAP_SETUID** 기본적으로 사용하지 않도록 설정됩니다. 이러한 기능을 사용하여 호스트 디바이스에서 파일을 보호하고 잠재적으로 루트 액세스 권한을 얻을 수 있습니다.

이러한 기능이 필요한 경우 컨테이너 만들기 옵션에서 CapADD를 사용하여 수동으로 다시 사용하도록 설정할 수 있습니다.

## <a name="next-steps"></a>다음 단계

[IoT Edge를 위한 개발 및 테스트 환경 준비](development-environment.md)

[Visual Studio를 사용하여 IoT Edge를 위한 C# 모듈 개발](how-to-visual-studio-develop-module.md)

[Visual Studio Code를 사용하여 IoT Edge를 위한 모듈 개발](how-to-vs-code-develop-module.md)

[Azure IoT Hub SDK 이해 및 사용](../iot-hub/iot-hub-devguide-sdks.md)
