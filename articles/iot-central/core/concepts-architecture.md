---
title: Azure IoT Central의 아키텍처 개념 | Microsoft Docs
description: 이 문서에서는 Azure IoT Central의 아키텍처와 관련된 주요 개념을 소개합니다.
author: dominicbetts
ms.author: dobett
ms.date: 08/31/2021
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 724c7e65d04b635dcaa635d29483da756cf47bfc
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128675874"
---
# <a name="azure-iot-central-architecture"></a>Azure IoT Central 아키텍처

이 문서에서는 IoT Central 솔루션 아키텍처의 주요 요소에 대한 개요를 제공합니다.

:::image type="content" source="media/concepts-architecture/architecture.png" alt-text="IoT Central 솔루션의 고급 아키텍처" border="false":::

IoT Central 애플리케이션:

- 솔루션에서 IoT 디바이스를 관리할 수 있습니다.
- 디바이스에서 데이터를 보고 분석할 수 있습니다.
- 솔루션의 일부인 다른 서비스로 내보내고 통합할 수 있습니다.

## <a name="iot-central"></a>IoT Central

IoT Central IoT 솔루션 개발을 위한 즉시 사용할 수 있는 환경입니다. PaaS(Platform as a Service) IoT 솔루션이며 기본 인터페이스는 웹 UI입니다. 프로그래밍 방식으로 애플리케이션과 상호 작용할 수 있는 [REST API](#rest-api) 있습니다.

이 섹션에서는 IoT Central 애플리케이션의 주요 기능에 대해 설명합니다.

### <a name="manage-devices"></a>디바이스 관리

IoT Central 사용하면 솔루션에 데이터를 전송하는 [IoT 디바이스를](#devices) 관리할 수 있습니다. 예를 들어, 다음을 수행할 수 있습니다.

- 애플리케이션에 [연결할](concepts-get-connected.md) 수 있는 디바이스 및 인증 방법을 제어합니다.
- [디바이스 템플릿을](concepts-device-templates.md) 사용하여 애플리케이션에 연결할 수 있는 디바이스 유형을 정의합니다.
- 속성을 설정하거나 연결된 디바이스에서 명령을 호출하여 디바이스를 관리합니다. 예를 들어 자동 온도 조절기 디바이스에 대한 대상 온도 속성을 설정하거나 명령을 호출하여 디바이스를 트리거하여 펌웨어를 업데이트합니다. 속성을 설정하고 다음에서 명령을 호출할 수 있습니다.
  - 사용자 지정 [가능한](concepts-device-templates.md#views) 웹 UI를 통한 개별 디바이스.
  - 예약된 작업 또는 주문형 [작업이](howto-manage-devices-in-bulk.md)있는 여러 디바이스.
- 고객 주소 또는 마지막 서비스 [날짜와 같은 디바이스 메타데이터를](concepts-device-templates.md#cloud-properties) 유지 관리합니다.

### <a name="view-and-analyze-data"></a>데이터 보기 및 분석

IoT Central 애플리케이션에서 개별 디바이스 또는 여러 디바이스의 집계된 데이터에 대한 데이터를 보고 분석할 수 있습니다.

- 디바이스 템플릿을 사용하여 특정 유형의 개별 디바이스에 대한 [사용자 지정 보기를](howto-set-up-template.md#views) 정의합니다. 예를 들어 개별 자동 온도 조절기의 시간별 온도를 그리거나 배달 트럭의 라이브 위치를 표시할 수 있습니다.
- 기본 제공 [분석을](tutorial-use-device-groups.md) 사용하여 여러 디바이스에 대한 집계 데이터를 봅니다. 예를 들어 여러 소매점의 총 점유율 또는 점유율이 가장 높거나 낮은 매장 식별을 확인할 수 있습니다.
- 디바이스를 관리하는 데 도움이 되는 사용자 지정 [대시보드를](howto-manage-dashboards.md) 만듭니다. 예를 들어 맵, 타일 및 차트를 추가하여 디바이스 원격 분석을 표시할 수 있습니다.  

### <a name="secure-your-solution"></a>솔루션 보안

IoT Central 애플리케이션에서 솔루션의 다음 보안 측면을 관리할 수 있습니다.

- [디바이스 연결:](concepts-get-connected.md)디바이스에서 애플리케이션에 대한 연결을 설정하는 데 사용하는 보안 키를 만들고, 해지하고, 업데이트합니다.
- [앱 통합:](howto-authorize-rest-api.md#get-an-api-token)다른 애플리케이션에서 애플리케이션과의 보안 연결을 설정하는 데 사용하는 보안 키를 만들고, 해지하고, 업데이트합니다.
- [사용자 관리:](howto-manage-users-roles.md)애플리케이션에 로그인할 수 있는 사용자와 해당 사용자가 가진 권한을 결정하는 역할을 관리합니다.
- [조직:](howto-create-organizations.md)IoT Central 애플리케이션에서 디바이스를 볼 수 있는 사용자를 관리하는 계층 구조를 정의합니다.

### <a name="rest-api"></a>REST API

다른 애플리케이션 및 서비스에서 애플리케이션을 관리할 수 있는 통합을 빌드합니다. 예를 들어 프로그래밍 방식으로 [애플리케이션의 디바이스를 관리하거나](howto-control-devices-with-rest-api.md) [사용자 정보를](howto-manage-users-roles-with-rest-api.md) 외부 시스템과 동기화합니다.

## <a name="devices"></a>디바이스

디바이스는 센서에서 데이터를 수집하여 IoT Central 애플리케이션에 원격 분석 스트림으로 보냅니다. 예를 들어 냉동 장치는 온도 값 스트림을 보내거나 배달 트럭은 해당 위치를 스트림합니다.

디바이스는 속성을 사용하여 밸브가 열려 있는지 또는 닫혀 있는지와 같은 상태를 보고할 수 있습니다. 또한 IoT Central 애플리케이션은 속성을 사용하여 디바이스 상태를 설정할 수 있습니다(예: 자동 온도 조절기의 대상 온도 설정).

IoT Central 디바이스에서 명령을 호출하여 디바이스를 제어할 수도 있습니다. 예를 들어 펌웨어 업데이트를 다운로드하고 설치하도록 디바이스에 지시합니다.

디바이스에서 [구현하는 원격 분석, 속성 및 명령을](concepts-telemetry-properties-commands.md) 통칭하여 디바이스 기능이라고 합니다. 디바이스와 IoT Central 애플리케이션 간에 공유되는 모델에서 이러한 기능을 정의합니다. IoT Central 이 모델은 특정 유형의 디바이스를 정의하는 디바이스 템플릿의 일부입니다.

[디바이스 구현은](tutorial-connect-device.md) IoT Central 통신할 수 있도록 [IoT](../../iot-develop/concepts-convention.md) 플러그 앤 플레이 규칙을 따라야 합니다. 자세한 내용은 다양한 언어 [SDK 및 샘플을 참조하세요.](../../iot-develop/libraries-sdks.md)

디바이스는 [MQTT, AMQP 또는 HTTP](../../iot-hub/iot-hub-devguide-protocols.md)프로토콜 중 하나를 사용하여 IoT Central 연결합니다.

## <a name="gateways"></a>게이트웨이

로컬 디바이스 게이트웨이는 다음과 같은 여러 시나리오에서 유용합니다.

- 디바이스는 인터넷에 연결할 수 없으므로 IoT Central 직접 연결할 수 없습니다. 예를 들어 게이트웨이를 통해 연결해야 하는 Bluetooth 사용 가능한 선분 센서 컬렉션이 있을 수 있습니다.
- 디바이스에서 생성된 데이터의 양이 많을 수 있습니다. 비용을 줄이기 위해 IoT Central 애플리케이션으로 전송되기 전에 로컬 게이트웨이의 데이터를 결합하거나 집계할 수 있습니다.
- 솔루션에는 데이터의 변칙에 대한 빠른 응답이 필요할 수 있습니다. IoT Central 애플리케이션에 데이터를 보낼 필요 없이 변칙을 식별하고 로컬에서 작업을 수행하도록 게이트웨이에서 규칙을 실행할 수 있습니다.

자세한 내용은 [Azure IoT Central 애플리케이션에 Azure IoT Edge 디바이스 연결](concepts-iot-edge.md)을 참조하세요.

## <a name="data-export"></a>데이터 내보내기

IoT Central 기본 제공 분석 기능이 있지만 다른 서비스 및 애플리케이션으로 데이터를 내보낼 수 있습니다. 데이터를 내보내는 이유는 다음과 같습니다.

### <a name="storage-and-analysis"></a>Storage 및 분석

장기 스토리지의 경우 보관 및 보존 정책에 대한 제어를 위해 지속적으로 데이터를 다른 스토리지 대상으로 [내보낼](howto-export-data.md) 수 있습니다. 별도의 스토리지를 사용하면 다른 분석 도구를 사용하여 인사이트를 도출하고 솔루션의 데이터를 볼 수도 있습니다.

### <a name="business-automation"></a>비즈니스 자동화

IoT Central [규칙을](howto-configure-rules-advanced.md) 사용하면 IoT Central 내의 조건에 따라 이메일을 보내거나 이벤트를 발생시키는 등의 외부 작업을 트리거할 수 있습니다. 예를 들어 디바이스의 주변 온도가 임계값에 도달하면 엔지니어에게 알릴 수 있습니다.

### <a name="additional-computation"></a>추가 계산

IoT Central 또는 다른 서비스에서 데이터를 사용하려면 데이터를 [변환하거나 계산해야](howto-transform-data.md) 할 수 있습니다. 예를 들어 배달 트럭에서 보고한 위치 데이터에 현지 날씨 정보를 추가할 수 있습니다.

## <a name="next-steps"></a>다음 단계

Azure IoT Central의 아키텍처에 대해 배웠으므로 다음 단계로 Azure IoT Central에서 [디바이스 연결](concepts-get-connected.md)에 대해 알아보세요.
