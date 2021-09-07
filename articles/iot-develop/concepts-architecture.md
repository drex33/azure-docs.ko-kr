---
title: IoT 플러그 앤 플레이 아키텍처 | Microsoft Docs
description: 솔루션 빌더로서 IoT 플러그 앤 플레이의 핵심 아키텍처 요소를 이해합니다.
author: ridomin
ms.author: rmpablos
ms.date: 09/15/2020
ms.topic: conceptual
ms.custom: mvc
ms.service: iot-develop
services: iot-develop
ms.openlocfilehash: 94376e1bc5192a0aa437066cb8a460e9aecd27a6
ms.sourcegitcommit: 0ede6bcb140fe805daa75d4b5bdd2c0ee040ef4d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2021
ms.locfileid: "122607423"
---
# <a name="iot-plug-and-play-architecture"></a>IoT 플러그 앤 플레이 아키텍처

IoT 플러그 앤 플레이를 사용하면 솔루션 빌더가 수동으로 구성하지 않고 IoT 디바이스를 솔루션과 통합할 수 있습니다. IoT 플러그 앤 플레이의 핵심에는 IoT 플러그 앤 플레이 사용 애플리케이션에 대한 디바이스의 기능을 설명하는 디바이스 _모델_ 이 있습니다. 이 모델은 다음을 정의하는 인터페이스 세트로 구성됩니다.

- 속성 - 디바이스 또는 다른 엔터티의 읽기 전용 또는 쓰기 가능 상태를 나타냅니다. 예를 들어 디바이스 일련 번호는 읽기 전용 속성일 수 있으며, 자동 온도 조절기의 목표 온도는 쓰기 가능 속성일 수 있습니다.
- 원격 분석 - 데이터가 규칙적인 센서 판독 스트림, 가끔 발생하는 오류 또는 정보 메시지인지 여부에 관계 없이 디바이스가 내보내는 데이터입니다.
- 디바이스에서 수행할 수 있는 함수 또는 작업을 설명하는 _명령_. 예를 들어 명령으로 게이트웨이를 다시 부팅하거나 원격 카메라를 사용하여 사진을 찍을 수 있습니다.

모든 모델 및 인터페이스에는 고유 ID가 있습니다.

다음 다이어그램은 IoT 플러그 앤 플레이 솔루션의 주요 요소를 보여줍니다.

:::image type="content" source="media/concepts-architecture/pnp-architecture.png" alt-text="IoT 플러그 앤 플레이 아키텍처":::

## <a name="model-repository"></a>모델 리포지토리

[모델 리포지토리](./concepts-model-repository.md) 는 모델 및 인터페이스 정의 저장소입니다. [DTDL(디지털 트윈 정의 언어)](https://github.com/Azure/opendigitaltwins-dtdl)를 사용하여 장치 모델과 인터페이스를 정의합니다.

웹 UI를 사용하여 모델 및 인터페이스를 관리할 수 있습니다.

모델 리포지토리에는 인터페이스 정의 액세스를 제한할 수 있도록 하는 기본 제공 역할 기반 액세스 제어가 있습니다.

## <a name="devices"></a>디바이스

디바이스 빌더는 [Azure IoT 디바이스 SDK](./libraries-sdks.md)에서 하나를 사용하여 IoT 디바이스에서 실행되는 코드를 구현합니다. 장치 SDK는 다음과 같이 장치 빌더를 지원합니다:

- 장치를 IoT Hub에 연결.
- IoT Hub에 장치를 등록하고 장치에서 구현하는 DTDL 인터페이스의 컬렉션을 식별하는 모델 ID를 발표합니다.
- 장치 및 IoT Hub 간에 DTDL 인터페이스에 정의된 속성을 동기화합니다.
- DTDL 인터페이스에 정의된 명령에 대한 명령 처리기를 추가합니다.
- IoT Hub에 원격 분석을 보냅니다.

## <a name="iot-edge-gateway"></a>IoT Edge 게이트웨이

IoT Edge 게이트웨이는 IoT Hub에 직접 연결할 수 없는 IoT 플러그 앤 플레이 장치를 연결하기 위한 중개자 역할을 합니다. 자세한 정보는 [IoT Edge 장치를 게이트웨이로 사용하는 방법](../iot-edge/iot-edge-as-gateway.md)을 참조하세요.

## <a name="iot-edge-modules"></a>IoT Edge 모듈

_IoT Edge 모듈_ 을 사용하면 에지에서 비즈니스 논리를 모듈 형태로 배포하고 관리할 수 있습니다. Azure IoT Edge 모듈은 IoT Edge가 관리하는 계산의 최소 단위이며 Azure 서비스(예: Azure Stream Analytics) 또는 고유한 솔루션별 코드를 포함할 수 있습니다.

이 _IoT Edge 허브_ 는 Azure IoT Edge 런타임을 구성하는 두 가지 모듈 중 하나입니다. IoT Hub와 동일한 프로토콜 엔드포인트를 노출하여 IoT Hub에 대한 로컬 프록시 역할을 합니다. 이 일관성은 클라이언트(디바이스 또는 모듈)에서 IoT Hub와 마찬가지로 IoT Edge 런타임에 연결할 수 있음을 의미합니다.

장치 SDK는 다음과 같이 모듈 빌더를 보조합니다:

- IoT Edge 허브를 사용하여 IoT hub에 안전하게 연결합니다.
- IoT Hub를 사용하여 모듈을 등록하고 장치에서 구현하는 DTDL 인터페이스의 컬렉션을 식별하는 모델 ID를 발표합니다.
- 장치 및 IoT Hub 간에 DTDL 인터페이스에 정의된 속성을 동기화합니다.
- DTDL 인터페이스에 정의된 명령에 대한 명령 처리기를 추가합니다.
- IoT Hub에 원격 분석을 보냅니다.

## <a name="iot-hub"></a>IoT Hub

[IoT Hub](../iot-hub/about-iot-hub.md) 클라우드 호스팅 서비스이며, IoT 응용 프로그램과 이를 통해 관리하는 장치 간의 양방향 통신을 위한 중앙 메시지 허브 역할을 합니다.

IoT Hub:

- 장치에서 구현된 모델 ID를 백 엔드 솔루션에 사용할 수 있도록 합니다.
- 허브에 연결된 각 플러그 앤 플레이 장치와 연결된 디지털 트윈을 유지 관리합니다.
- 처리 또는 스토리지를 위해 원격 분석 스트림을 다른 서비스에 전달합니다.
- 디지털 트윈 변경 이벤트를 다른 서비스로 라우팅하여 장치 모니터링을 사용하도록 설정합니다.

## <a name="backend-solution"></a>백 엔드 솔루션

백 엔드 솔루션은 IoT Hub에서 디지털 트윈과 상호 작용하여 연결된 장치를 모니터링하고 제어합니다. 서비스 SDK 중 하나를 사용하여 백 엔드 솔루션을 구현합니다. 연결된 장치의 기능을 이해하려면 솔루션 백 엔드:

1. IoT Hub에 등록된 장치의 모델 ID를 검색합니다.
1. 모델 ID를 사용하여 모든 모델 리포지토리에서 인터페이스 정의를 검색합니다.
1. 모델 파서를 사용하여 인터페이스 정의에서 정보를 추출합니다.

백 엔드 솔루션은 인터페이스 정의의 정보를 사용하여 다음을 수행할 수 있습니다.

- 장치에서 보고한 속성 값을 읽습니다.
- 장치에서 쓰기 가능한 속성을 업데이트합니다.
- 장치에서 구현된 명령을 호출합니다.
- 장치에서 보낸 원격 분석의 형식을 해석합니다.

## <a name="next-steps"></a>다음 단계

이제 IoT 플러그 앤 플레이 솔루션의 아키텍처를 간략하게 설명했으므로 다음 단계는 다음에 대해 자세히 알아보는 것입니다.

- [모델 리포지토리](./concepts-model-repository.md)
- [디지털 트윈 모델 통합](./concepts-model-discovery.md)
- [IoT 플러그 앤 플레이 개발](./concepts-developer-guide-device.md)
