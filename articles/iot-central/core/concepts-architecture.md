---
title: Azure IoT Central의 아키텍처 개념 | Microsoft Docs
description: 이 문서에서는 Azure IoT Central의 아키텍처와 관련된 주요 개념을 소개합니다.
author: dominicbetts
ms.author: dobett
ms.date: 12/19/2020
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 46b8cdc7fa33c8ddd382decb49eaa148093c99fe
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122567015"
---
# <a name="azure-iot-central-architecture"></a>Azure IoT Central 아키텍처

이 문서에서는 Azure IoT Central 아키텍처의 주요 개념에 대한 개요를 제공합니다.

## <a name="devices"></a>디바이스

디바이스는 Azure IoT Central 애플리케이션과 데이터를 교환합니다. 디바이스는 다음을 수행할 수 있습니다.

- 원격 분석 데이터 같은 측정값을 보냅니다.
- 애플리케이션과 설정을 동기화합니다.

Azure IoT Central에서 디바이스가 애플리케이션으로 교환할 수 있는 데이터는 디바이스 템플릿에 지정됩니다. 디바이스 템플릿에 대한 자세한 내용은 [디바이스 템플릿](concepts-device-templates.md)을 참조하세요.

디바이스가 Azure IoT Central 애플리케이션에 연결하는 방법에 대한 자세한 내용은 [디바이스 연결](concepts-get-connected.md)을 참조하세요.

## <a name="azure-iot-edge-devices"></a>Azure IoT Edge 디바이스

[Azure IoT SDK](https://github.com/Azure/azure-iot-sdks)를 사용하여 만든 디바이스 뿐만 아니라 [Azure IoT Edge 디바이스](../../iot-edge/about-iot-edge.md)도 IoT Central 애플리케이션에 연결할 수 있습니다. IoT Edge를 사용하면 IoT Central에서 관리하는 IoT 디바이스에서 직접 클라우드 인텔리전스 및 사용자 지정 논리를 실행할 수 있습니다. IoT Edge 런타임을 사용하면 다음을 수행할 수 있습니다.

- 디바이스에 워크로드를 설치하고 업데이트합니다.
- 디바이스에서 IoT Edge 보안 표준을 유지합니다.
- IoT Edge 모듈이 항상 실행되도록 합니다.
- 원격 모니터링을 위해 모듈 상태를 클라우드에 보고합니다.
- 다운스트림 리프 디바이스와 IoT Edge 디바이스 간, IoT Edge 디바이스의 모듈 간, IoT Edge 디바이스와 클라우드 간의 통신을 관리합니다.

![Azure IoT Edge를 사용하는 Azure IoT Central](./media/concepts-architecture/iotedge.png)

IoT Central은 IoT Edge 디바이스에 대해 다음 기능을 지원합니다.

- 다음과 같은 IoT Edge 디바이스의 기능을 설명하는 디바이스 템플릿:
  - 대량의 디바이스에 대한 매니페스트를 관리할 수 있는 배포 매니페스트 업로드 기능
  - IoT Edge 디바이스에서 실행되는 모듈
  - 각 모듈에서 전송하는 원격 분석 데이터
  - 각 모듈이 보고하는 속성
  - 각 모듈이 응답하는 명령
  - IoT Edge 게이트웨이 디바이스와 다운스트림 디바이스 간의 관계
  - IoT Edge 디바이스에 저장되지 않는 클라우드 속성
  - UI가 디바이스 기능을 표시하는 방식을 변경하는 사용자 지정
  - 디바이스 보기 및 양식

  자세한 내용은 [Azure IoT Central 애플리케이션에 Azure IoT Edge 디바이스 연결](./concepts-iot-edge.md) 문서를 참조하세요.

- Azure IoT 디바이스 프로비저닝 서비스를 사용하여 IoT Edge 디바이스를 대규모로 프로비저닝하는 기능
- 규칙 및 동작
- 사용자 지정 대시보드 및 분석
- IoT Edge 디바이스에서 원격 분석의 지속적인 데이터 내보내기

### <a name="iot-edge-device-types"></a>IoT Edge 디바이스 유형

IoT Central은 다음과 같이 IoT Edge 디바이스 유형을 분류합니다.

- 리프 디바이스. IoT Edge 디바이스는 다운스트림 리프 디바이스를 포함할 수 있지만 이러한 디바이스는 IoT Central에서 프로비저닝되지 않습니다.
- 다운스트림 디바이스를 사용하는 게이트웨이 디바이스 게이트웨이 디바이스와 다운스트림 디바이스 둘 다 IoT Central에 프로비저닝됩니다.

![IoT Edge가 있는 IoT Central 개요](./media/concepts-architecture/gatewayedge.png)

> [!NOTE]
> IoT Central은 현재 IoT Edge 디바이스를 IoT Edge 게이트웨이에 대한 다운스트리밍 디바이스로 연결하는 것을 지원하지 않습니다. IoT Central에 연결하는 모든 디바이스는 DPS(디바이스 프로비저닝 서비스)를 사용하여 프로비저닝되고 DPS는 중첩된 IoT Edge 시나리오를 지원하지 않기 때문입니다.

### <a name="iot-edge-patterns"></a>IoT Edge 패턴

IoT Central은 다음과 같은 IoT Edge 디바이스 패턴을 지원합니다.

#### <a name="iot-edge-as-leaf-device"></a>리프 디바이스인 IoT Edge

![리프 디바이스인 IoT Edge](./media/concepts-architecture/edgeasleafdevice.png)

IoT Edge 디바이스는 IoT Central 및 모든 다운스트림 디바이스에서 프로비저닝되며 해당 원격 분석은 IoT Edge 디바이스에서 발생한 것으로 표시됩니다. IoT Edge 디바이스에 연결된 다운스트림 디바이스는 IoT Central에서 프로비저닝되지 않습니다.

#### <a name="iot-edge-gateway-device-connected-to-downstream-devices-with-identity"></a>ID가 있는 다운스트림 디바이스에 연결된 IoT Edge 게이트웨이 디바이스

![다운스트림 디바이스 ID가 있는 IoT Edge](./media/concepts-architecture/edgewithdownstreamdeviceidentity.png)

IoT Edge 디바이스는 IoT Edge 디바이스에 연결된 다운스트림 디바이스와 함께 IoT Central에 프로비저닝됩니다. 게이트웨이를 통해 다운스트림 디바이스를 프로비저닝하는 런타임 지원은 현재 지원되지 않습니다.

#### <a name="iot-edge-gateway-device-connected-to-downstream-devices-with-identity-provided-by-the-iot-edge-gateway"></a>IoT Edge 게이트웨이에서 제공하는 ID가 있고 다운스트림 디바이스에 연결된 IoT Edge 게이트웨이 디바이스

![ID가 없고 다운스트림 디바이스를 사용하는 IoT Edge](./media/concepts-architecture/edgewithoutdownstreamdeviceidentity.png)

IoT Edge 디바이스는 IoT Edge 디바이스에 연결된 다운스트림 디바이스와 함께 IoT Central에 프로비저닝됩니다. 다운스트림 디바이스에 ID를 제공하고 다운스트림 디바이스를 프로비저닝하는 게이트웨이 런타임 지원이 현재 지원되지 않습니다. 자체 ID 변환 모듈을 가져오면 IoT Central에서 이 패턴을 지원할 수 있습니다.

## <a name="cloud-gateway"></a>클라우드 게이트웨이

Azure IoT Central은 디바이스 연결을 가능하게 하는 클라우드 게이트웨이로 Azure IoT Hub를 사용합니다. IoT Hub의 기능은 다음과 같습니다.

- 클라우드에서 대규모 데이터 수집.
- 디바이스 관리.
- 디바이스 연결 보안.

IoT Hub에 대한 자세한 내용은 [Azure IoT Hub](../../iot-hub/index.yml)를 참조하세요.

Azure IoT Central의 디바이스 연결에 대한 자세한 내용은 [디바이스 연결](concepts-get-connected.md)을 참조하세요.

## <a name="data-stores"></a>데이터 저장소

Azure IoT Central은 애플리케이션 데이터를 클라우드에 저장합니다. 다음과 같은 애플리케이션 데이터가 저장됩니다.

- 디바이스 템플릿.
- 디바이스 ID.
- 디바이스 메타데이터.
- 사용자 및 역할 데이터.

Azure IoT Central은 디바이스에서 보낸 측정값 데이터에 시계열 저장소를 사용합니다. 분석 서비스에서 사용하는 디바이스의 시계열 데이터.

## <a name="data-export"></a>데이터 내보내기

Azure IoT Central 애플리케이션에서 데이터를 Azure Event Hubs 및 Azure Service Bus 인스턴스로 [지속적으로 내보낼](howto-export-data.md) 수 있습니다. 주기적으로 데이터를 Azure Blob Storage 계정으로 내보낼 수도 있습니다. IoT Central은 측정, 디바이스 및 디바이스 템플릿을 내보낼 수 있습니다.

## <a name="batch-device-updates"></a>배치 디바이스 업데이트

Azure IoT Central 애플리케이션에서 [작업을 만들고 실행](howto-manage-devices-in-bulk.md)하여 연결된 디바이스를 관리할 수 있습니다. 이러한 작업을 통해 디바이스 속성 또는 설정에 대한 대량 업데이트를 수행하거나 명령을 실행할 수 있습니다. 예를 들어 여러 냉장 자동 판매기의 팬 속도를 높이기 위한 작업을 만들 수 있습니다.

## <a name="role-based-access-control-rbac"></a>RBAC(역할 기반 액세스 제어)

모든 IoT Central 애플리케이션에는 자체 기본 제공 RBAC 시스템이 있습니다. 관리자는 사전 정의된 역할 중 하나를 사용하거나 사용자 지정 역할을 만들어 Azure IoT Central 애플리케이션에 대한 [액세스 규칙을 정의](howto-manage-users-roles.md)할 수 있습니다. 역할은 사용자가 액세스할 수 있는 애플리케이션의 영역과 수행할 수 있는 작업을 결정합니다.

## <a name="security"></a>보안

Azure IoT Central에는 다음과 같은 보안 기능이 있습니다.

- 전송 및 저장 시 데이터가 암호화됩니다.
- Azure Active Directory 또는 Microsoft 계정을 통해 인증이 제공됩니다. 2단계 인증이 지원됩니다.
- 테넌트가 완전히 격리됩니다.
- 디바이스 수준 보안을 제공합니다.

## <a name="next-steps"></a>다음 단계

Azure IoT Central의 아키텍처에 대해 배웠으므로 다음 단계로 Azure IoT Central에서 [디바이스 연결](concepts-get-connected.md)에 대해 알아보세요.