---
title: Azure IoT Central의 아키텍처 개념 | Microsoft Docs
description: 이 문서에서는 Azure IoT Central의 아키텍처와 관련된 주요 개념을 소개합니다.
author: dominicbetts
ms.author: dobett
ms.date: 08/31/2021
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 37e4224ae1347647d15959a55d19d2c6487935d7
ms.sourcegitcommit: 7b6ceae1f3eab4cf5429e5d32df597640c55ba13
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/31/2021
ms.locfileid: "123273216"
---
# <a name="azure-iot-central-architecture"></a>Azure IoT Central 아키텍처

이 문서에서는 Azure IoT Central 아키텍처의 주요 개념에 대한 개요를 제공합니다.

## <a name="devices"></a>디바이스

디바이스는 Azure IoT Central 애플리케이션과 데이터를 교환합니다. 디바이스는 다음을 수행할 수 있습니다.

- 원격 분석 데이터 같은 측정값을 보냅니다.
- 애플리케이션과 설정을 동기화합니다.

Azure IoT Central에서 디바이스가 애플리케이션으로 교환할 수 있는 데이터는 디바이스 템플릿에 지정됩니다. 디바이스 템플릿에 대한 자세한 내용은 [디바이스 템플릿](concepts-device-templates.md)을 참조하세요.

디바이스가 Azure IoT Central 애플리케이션에 연결하는 방법에 대한 자세한 내용은 [디바이스 연결](concepts-get-connected.md)을 참조하세요.

### <a name="azure-iot-edge-devices"></a>Azure IoT Edge 디바이스

[Azure IoT SDK](https://github.com/Azure/azure-iot-sdks)를 사용하여 만든 디바이스 뿐만 아니라 [Azure IoT Edge 디바이스](../../iot-edge/about-iot-edge.md)도 IoT Central 애플리케이션에 연결할 수 있습니다. IoT Edge를 사용하면 IoT Central에서 관리하는 IoT 디바이스에서 직접 클라우드 인텔리전스 및 사용자 지정 논리를 실행할 수 있습니다. IoT Edge를 게이트웨이로 사용하여 다른 다운스트림 디바이스가 IoT Central 연결하도록 할 수도 있습니다.

자세한 내용은 [Azure IoT Central 애플리케이션에 Azure IoT Edge 디바이스 연결](concepts-iot-edge.md)을 참조하세요.

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