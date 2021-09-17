---
title: Azure IoT Central용 디바이스 개발 | Microsoft Docs
description: Azure IoT Central은 IoT 솔루션 만들기를 간소화하는 IoT 애플리케이션 플랫폼입니다. 이 문서에서는 IoT Central 애플리케이션에 연결할 디바이스를 개발하는 방법에 대한 개요를 제공합니다. 디바이스는 원격 분석을 사용하여 스트리밍 데이터 및 속성을 전송하여 디바이스 상태를 보고합니다. Iot Central은 쓰기 가능한 속성을 사용하여 디바이스 상태를 설정하고 디바이스에서 명령을 호출할 수 있습니다.
author: dominicbetts
ms.author: dobett
ms.date: 08/30/2021
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.custom:
- mvc
- device-developer
ms.openlocfilehash: f2131ec5a0b939172097494dcd457b9d661614ad
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/04/2021
ms.locfileid: "123473499"
---
# <a name="iot-central-device-development-guide"></a>IoT Central 디바이스 개발 가이드

IoT Central 애플리케이션을 사용하면 수백만 대의 디바이스를 수명 주기 전체 동안 모니터링하고 관리할 수 있습니다. 이 가이드는 IoT Central에 연결하는 디바이스에서 실행되는 코드를 구현하는 디바이스 개발자를 위해 작성되었습니다.

디바이스는 다음과 같은 기본 원칙을 사용하여 IoT Central 애플리케이션과 상호 작용합니다.

- _원격 분석 데이터_ 는 디바이스가 IoT Central로 전송하는 데이터입니다. 온보드 센서의 온도 값 스트림을 예로 들 수 있습니다.
- _속성_ 은 디바이스가 IoT Central에 보고하는 상태 값입니다. 디바이스의 현재 펌웨어 버전을 예로 들 수 있습니다. IoT Central이 대상 온도와 같이 디바이스에서 업데이트할 수 있는 쓰기 가능 속성도 사용할 수 있습니다.
- _명령_ 은 디바이스의 동작을 제어하기 위해 IoT Central에서 호출됩니다. 예를 들어 IoT Central 애플리케이션에서 디바이스 재부팅 명령을 호출할 수 있습니다.

솔루션 빌더는 원격 분석 데이터를 시각화하고, 속성을 관리하고, 명령을 호출할 수 있도록 IoT Central 웹 UI에서 대시보드및 디바이스는 보기를 구성하는 역할을 담당합니다.

## <a name="types-of-device"></a>디바이스 유형

다음 섹션에서는 IoT Central 애플리케이션에 연결할 수 있는 주요 디바이스 유형을 설명합니다.

### <a name="iot-device"></a>IoT 디바이스

IoT 디바이스는 IoT Central에 직접 연결하는 독립 실행형 디바이스입니다. IoT 디바이스는 일반적으로 온보드 또는 연결된 센서의 원격 분석 데이터를 IoT Central 애플리케이션으로 보냅니다. 또한 독립 실행형 디바이스는 속성 값을 보고하고, 쓰기 가능한 속성 값을 수신하고, 명령에 응답할 수 있습니다.

### <a name="iot-edge-device"></a>IoT Edge 디바이스

IoT Edge 디바이스는 IoT Central에 직접 연결합니다. 뿐만 아니라 IoT Edge 디바이스는 자체 원격 분석 데이터를 전송하고, 속성을 보고하고, 쓰기 가능한 속성 업데이트 및 명령에 응답할 수 있습니다. IoT Edge 모듈은 IoT Edge 디바이스에서 로컬로 데이터를 처리할 수 있습니다. IoT Edge 디바이스는 리프 디바이스라고 하는 다른 디바이스의 중개자 역할도 합니다. 다음은 IoT Edge 디바이스를 사용하는 시나리오입니다.

- IoT Central로 전송되기 전에 원격 분석 데이터를 집계 또는 필터링합니다. 이 방법은 IoT Central로 데이터를 보내는 비용을 줄이는 데 도움이 될 수 있습니다.
- IoT Central에 직접 연결할 수 없는 디바이스는 IoT Edge 디바이스를 통해 연결하도록 설정합니다. 예를 들어 리프 디바이스는 bluetooth를 사용하여 IoT Edge 디바이스에 연결한 다음, 인터넷을 통해 IoT Central에 연결할 수 있습니다.
- 인터넷을 통해 IoT Central에 연결할 때 발생하는 대기 시간을 없애기 위해 리프 디바이스를 로컬로 제어합니다.

IoT Central은 IoT Edge 디바이스만 볼 수 있고, IoT Edge 디바이스에 연결된 리프 디바이스는 볼 수 없습니다.

자세한 내용은 [Azure IoT Central 애플리케이션에 Azure IoT Edge 디바이스 추가](./tutorial-add-edge-as-leaf-device.md)를 참조하세요.

### <a name="gateways"></a>게이트웨이

게이트웨이 디바이스는 IoT Central 애플리케이션에 연결하는 하나 이상의 다운스트림 디바이스를 관리합니다. IoT Central을 사용하여 다운스트림 디바이스와 게이트웨이 디바이스 간의 관계를 구성합니다. IoT 디바이스와 IoT Edge 디바이스는 게이트웨이 역할을 할 수 있습니다. 자세한 내용은 [Azure IoT Central 애플리케이션에서 새 IoT 게이트웨이 디바이스 유형 정의](./tutorial-define-gateway-device-type.md)를 참조하세요.

## <a name="connect-a-device"></a>디바이스 연결

Azure IoT Central은 모든 디바이스 등록 및 연결에 [Azure IoT Hub DPS(Device Provisioning Service)](../../iot-dps/about-iot-dps.md)를 사용합니다.

DPS를 사용하면 다음과 같은 장점이 있습니다.

- IoT Central이 디바이스를 대규모로 온보딩하고 연결하는 것을 지원합니다.
- IoT Central UI를 통해 디바이스를 등록할 필요 없이 오프라인으로 디바이스 자격 증명을 생성하고 디바이스를 구성할 수 있습니다.
- 사용자의 고유한 디바이스 ID를 사용하여 IoT Central에서 디바이스를 등록할 수 있습니다. 사용자의 고유한 디바이스 ID를 사용하면 기존 백 오피스 시스템과 간편하게 통합할 수 있습니다.
- 디바이스를 IoT Central에 연결하는 한 가지 일관적인 방법이 있습니다.

자세한 내용은 [Azure IoT Central에 연결](./concepts-get-connected.md) 및 [모범 사례](concepts-best-practices.md)를 참조하세요.

### <a name="security"></a>보안

디바이스와 IoT Central 애플리케이션 간의 연결은 [공유 액세스 서명](./concepts-get-connected.md#sas-group-enrollment) 또는 업계 표준 [X.509 인증서](./concepts-get-connected.md#x509-group-enrollment)를 사용하여 보호됩니다.

### <a name="communication-protocols"></a>통신 프로토콜

디바이스에서 IoT Central에 연결하는 데 사용할 수 있는 통신 프로토콜은 MQTT, AMQP 및 HTTPS입니다. 내부적으로 IoT Central은 디바이스를 연결하기 위해 IoT 허브를 사용합니다. IoT Hub가 디바이스 연결에 대해 지원하는 통신 프로토콜에 대한 자세한 내용은 [통신 프로토콜 선택](../../iot-hub/iot-hub-devguide-protocols.md)을 참조하세요.

## <a name="implement-the-device"></a>디바이스 구현

IoT Central 디바이스 템플릿에는 해당 유형의 디바이스에서 구현해야 하는 동작을 지정하는 _모델_ 이 포함되어 있습니다. 동작에는 원격 분석, 속성 및 명령이 포함됩니다.

모델을 편집하는 모범 사례에 대한 자세한 내용은 [기존 디바이스 템플릿 편집](howto-edit-device-template.md)을 참조하세요.

> [!TIP]
> 모델은 IoT Central에서 [DTDL(디지털 트윈 정의 언어) v2](https://github.com/Azure/opendigitaltwins-dtdl) JSON 파일로 내보낼 수 있습니다.

각 모델에는 `dtmi:com:example:Thermostat;1`과 같은 고유한 _DTMI(디바이스 쌍 모델 식별자)_ 가 있습니다. 디바이스가 IoT Central에 연결되면 해당 디바이스에서 구현하는 모델의 DTMI를 보냅니다. 그러면 IoT Central에서 올바른 디바이스 템플릿을 디바이스에 연결할 수 있습니다.

[IoT 플러그 앤 플레이](../../iot-develop/overview-iot-plug-and-play.md)는 디바이스에서 DTDL 모델을 구현할 때 따라야 하는 [규칙](../../iot-develop/concepts-convention.md) 세트를 정의합니다.

[Azure IoT 디바이스 SDK](#languages-and-sdks)에는 IoT 플러그 앤 플레이 규칙에 대한 지원이 포함되어 있습니다.

### <a name="device-model"></a>디바이스 모델

디바이스 모델은 [DTDL](https://github.com/Azure/opendigitaltwins-dtdl)을 사용하여 정의됩니다. 이 언어를 사용하여 다음을 정의할 수 있습니다.

- 디바이스에서 보내는 원격 분석. 정의에는 원격 분석의 이름 및 데이터 형식이 포함됩니다. 예를 들어 디바이스는 온도 원격 분석을 double 형식으로 보냅니다.
- 디바이스에서 IoT Central에 보고하는 속성. 속성 정의에는 해당 이름 및 데이터 형식이 포함됩니다. 예를 들어 디바이스는 밸브의 상태를 부울 형식으로 보고합니다.
- 디바이스에서 IoT Central로부터 받을 수 있는 속성. 필요에 따라 속성을 쓰기 가능으로 표시할 수 있습니다. 예를 들어 IoT Central은 대상 온도를 double 형식으로 디바이스에 보냅니다.
- 디바이스에서 응답하는 명령. 정의에는 명령 이름과 매개 변수의 이름 및 데이터 형식이 포함됩니다. 예를 들어 디바이스는 다시 부팅하기 전에 대기하는 시간(초)을 지정하는 다시 부팅 명령에 응답합니다.

DTDL 모델은 _구성 요소가 없는 모델_ 또는 _다중 구성 요소 모델_ 일 수 있습니다.

- 구성 요소가 없는 모델: 단순 모델은 포함 구성 요소 또는 계단식 구성 요소를 사용하지 않습니다. 모든 원격 분석, 속성 및 명령은 단일 _루트 구성 요소_ 로 정의됩니다. 예제는 [자동 온도 조절기](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/Thermostat.json) 모델을 참조하세요.
- 다중 구성 요소 모델: 둘 이상의 구성 요소가 포함된 더 복잡한 모델입니다. 이러한 구성 요소에는 단일 루트 구성 요소 및 하나 이상의 추가 중첩 구성 요소가 포함됩니다. 예제는 [온도 조절기](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json) 모델을 참조하세요.

자세히 알아보려면 [IoT 플러그 앤 플레이 모델링 가이드](../../iot-develop/concepts-modeling-guide.md)를 참조하세요.

### <a name="conventions"></a>규칙

디바이스는 IoT Central과 데이터를 교환할 때 IoT 플러그 앤 플레이 규칙을 따라야 합니다. 규칙은 다음과 같습니다.

- IoT Central에 연결되면 DTMI를 보냅니다.
- 올바른 형식의 JSON 페이로드 및 메타데이터를 IoT Central에 보냅니다.
- IoT Central의 쓰기 가능 속성 및 명령에 올바르게 응답합니다.
- 구성 요소 명령에 대한 명명 규칙을 따릅니다.

> [!NOTE]
> 현재 IoT Central은 DTDL **배열** 및 **지리 공간적** 데이터 형식을 완전히 지원하지 않습니다.

디바이스에서 IoT Central과 교환하는 JSON 메시지의 형식에 대한 자세한 내용은 [원격 분석, 속성 및 명령 페이로드](concepts-telemetry-properties-commands.md)를 참조하세요.

IoT 플러그 앤 플레이 규칙에 대한 자세한 내용은 [IoT 플러그 앤 플레이 규칙](../../iot-develop/concepts-convention.md)을 참조하세요.

### <a name="device-sdks"></a>디바이스 SDK

[Azure IoT 디바이스 SDK](../../iot-hub/iot-hub-devguide-sdks.md#azure-iot-hub-device-sdks) 중 하나를 사용하여 디바이스의 동작을 구현할 수 있습니다. 코드는 다음을 수행해야 합니다.

- IoT Central 애플리케이션에서 디바이스를 DPS에 등록하고 DPS의 정보를 사용하여 내부 IoT 허브에 연결합니다.
- 디바이스에서 구현하는 모델의 DTMI를 알립니다.
- 원격 분석을 디바이스 모델에서 지정하는 형식으로 보냅니다. IoT Central은 디바이스 템플릿의 모델을 사용하여 원격 분석을 시각화 및 분석에 사용하는 방법을 결정합니다.
- 디바이스와 IoT Central 간에 속성 값을 동기화합니다. 모델은 IoT Central에서 정보를 표시할 수 있도록 속성 이름과 데이터 형식을 지정합니다.
- 모델에 지정된 명령에 대한 명령 처리기를 구현합니다. 모델은 디바이스에서 사용해야 하는 명령 이름과 매개 변수를 지정합니다.

디바이스 템플릿의 역할에 대한 자세한 내용은 [디바이스 템플릿이란?](./concepts-device-templates.md)을 참조하세요.

일부 샘플 코드는 [클라이언트 애플리케이션 만들기 및 연결](./tutorial-connect-device.md)을 참조하세요.

### <a name="languages-and-sdks"></a>언어 및 SDK

지원되는 언어와 SDK에 대한 자세한 내용은 [Azure IoT Hub 디바이스 SDK의 이해 및 사용](../../iot-hub/iot-hub-devguide-sdks.md#azure-iot-hub-device-sdks)을 참조하세요.

## <a name="next-steps"></a>다음 단계

디바이스 개발자이고 몇 가지 코드에 대해 자세히 알아보려면 다음 단계를 [클라이언트 애플리케이션을 만들고 Azure IoT Central 애플리케이션에 연결](./tutorial-connect-device.md)하는 것이 좋습니다.

IoT Central 사용에 대해 자세히 알아보려면 [Azure IoT Central 애플리케이션 만들기](./quick-deploy-iot-central.md)부터 시작하여 빠른 시작을 완료하세요.
