---
title: Azure IoT Hub Device Provisioning Service - 디바이스 개념
description: Azure IoT Hub DPS(Device Provisioning Service)와 관련된 디바이스 재프로비저닝 개념에 대해 설명합니다.
author: wesmc7777
ms.author: wesmc
ms.date: 04/16/2021
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: 29d7547404e4eaf107734bacfbfa43a0aef19f64
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114466889"
---
# <a name="iot-hub-device-reprovisioning-concepts"></a>IoT Hub 디바이스 다시 프로비전 개념

IoT 솔루션의 수명 주기 동안 IoT Hub 간에 디바이스를 이동하는 것이 일반적입니다. 이러한 이동의 이유에는 다음과 같은 시나리오가 포함될 수 있습니다.

* **지리적 위치/지리적 대기 시간**: 여러 위치 간에 디바이스를 이동함으로써 디바이스가 더 가까운 IoT Hub로 마이그레이션되면 네트워크 대기 시간이 짧아집니다.

* **다중 테넌트**: 디바이스 하나를 같은 IoT 솔루션 내에서 사용하고 새 고객 또는 고객 사이트에 다시 할당할 수 있습니다. 이러한 새 고객은 별도의 IoT 허브를 사용하여 서비스를 제공받을 수 있습니다.

* **솔루션 변경**: 디바이스는 새롭거나 업데이트된 IoT 솔루션으로 이동할 수 있습니다. 이와 같이 디바이스를 다시 할당할 때는 디바이스가 다른 백 엔드 구성 요소에 연결된 새 IoT Hub와 통신해야 할 수 있습니다.

* **격리**: 솔루션 변경과 비슷합니다. 제대로 작동하지 않거나 손상되었거나 오래된 디바이스는 IoT Hub에 다시 할당할 수 있습니다. IoT Hub에서는 이러한 모든 디바이스를 업데이트하여 규정 준수 상태로 다시 되돌릴 수 있습니다. 디바이스가 정상적으로 작동하면 해당 주 허브로 다시 마이그레이션됩니다.

Device Provisioning Service 내에서는 다시 프로비전이 지원되므로 이러한 요구를 충족할 수 있습니다. 디바이스의 등록 항목에 구성된 다시 프로비전 정책에 따라 새 IoT Hub에 디바이스를 자동으로 다시 할당할 수 있습니다.

## <a name="device-state-data"></a>디바이스 상태 데이터

디바이스 상태 데이터는 [디바이스 쌍](../iot-hub/iot-hub-devguide-device-twins.md) 및 디바이스 기능으로 구성됩니다. 이 데이터는 디바이스가 할당된 IoT Hub 및 Device Provisioning Service 인스턴스에 저장됩니다.

![Device Provisioning Service에서 프로비저닝이 작동하는 방식을 보여 주는 다이어그램](./media/concepts-device-reprovisioning/dps-provisioning.png)

Device Provisioning Service 인스턴스를 사용하여 디바이스를 처음 프로비전하면 다음 단계가 수행됩니다.

1. 디바이스에서 Device Provisioning Service 인스턴스에 프로비전 요청을 보냅니다. 서비스 인스턴스는 등록 항목을 기준으로 하여 디바이스 ID를 인증하고 디바이스 상태 데이터의 초기 구성을 만듭니다. 서비스 인스턴스는 등록 구성을 기준으로 IoT Hub에 디바이스를 할당하고 해당 IoT Hub 할당을 디바이스에 반환합니다.

2. Provisioning Service 인스턴스에서 초기 디바이스 상태 데이터의 복사본을 할당된 IoT Hub에 제공합니다. 디바이스가 할당된 IoT Hub에 연결하여 작업을 시작합니다.

시간이 지남에 따라 [디바이스 작업](../iot-hub/iot-hub-devguide-device-twins.md#device-operations) 및 [백 엔드 작업](../iot-hub/iot-hub-devguide-device-twins.md#back-end-operations)에 의해 IoT Hub의 디바이스 상태 데이터가 업데이트될 수 있습니다. Device Provisioning Service 인스턴스에 저장된 초기 디바이스 상태 정보는 그대로 보존됩니다. 그대로 보존된 이 디바이스 상태 데이터는 초기 구성입니다.

![Device Provisioning Service를 사용한 프로비전](./media/concepts-device-reprovisioning/dps-provisioning-2.png)

시나리오에 따라서는 디바이스를 IoT Hub 간에 이동할 때 이전 IoT Hub에서 업데이트된 디바이스 상태도 새 IoT Hub로 마이그레이션해야 할 수 있습니다. Device Provisioning Service의 다시 프로비전 정책은 이러한 마이그레이션을 지원합니다.

## <a name="reprovisioning-policies"></a>다시 프로비전 정책

시나리오에 따라 디바이스는 일반적으로 다시 부팅 시 프로비저닝 서비스 인스턴스에 요청을 보냅니다. 또한 요청 시 수동으로 프로비전을 트리거하는 메서드를 지원합니다. 등록 항목에 대한 다시 프로비전 정책은 디바이스 프로비저닝 서비스 인스턴스에서 이러한 프로비전 요청을 처리하는 방식을 결정합니다. 또한 프로비전 중에 디바이스 상태 데이터를 마이그레이션해야 하는지 여부를 결정합니다. 개별 등록 및 등록 그룹에 대해 동일하ㄴ 정책을 사용할 수 있습니다.

* **다시 프로비전하고 데이터 마이그레이션**: 새 등록 항목의 기본 정책입니다. 이 정책은 등록 항목과 연결된 디바이스가 새 요청을 제출(1)하면 작업을 수행합니다. 등록 항목 구성에 따라 디바이스가 다른 IoT 허브에 다시 할당될 수 있습니다. 디바이스에서 IoT 허브가 변경되면 초기 IoT 허브에 대한 디바이스 등록이 제거됩니다. 그러면 초기 IoT Hub에서 업데이트된 디바이스 상태 정보는 새 IoT Hub로 마이그레이션됩니다(2). 마이그레이션 중에는 디바이스가 **할당 중** 으로 보고됩니다.

    ![등록 항목과 연결된 디바이스가 새 요청을 제출할 때 정책이 적용됨을 보여 주는 다이어그램](./media/concepts-device-reprovisioning/dps-reprovisioning-migrate.png)

* **다시 프로비전하고 초기 구성으로 다시 설정**: 이 정책은 등록 항목과 연결된 디바이스가 새 프로비전 요청을 제출(1)하면 작업을 수행합니다. 등록 항목 구성에 따라 디바이스가 다른 IoT 허브에 다시 할당될 수 있습니다. 디바이스에서 IoT 허브가 변경되면 초기 IoT 허브에 대한 디바이스 등록이 제거됩니다. 디바이스를 프로비전할 때 Provisioning Service 인스턴스가 받은 초기 구성 데이터가 새 IoT Hub에 제공됩니다(2). 마이그레이션 중에는 디바이스가 **할당 중** 으로 보고됩니다.

    이 정책은 IoT Hub를 변경하지 않는 초기화에 사용되는 경우가 많습니다.

    ![등록 항목과 연결된 디바이스가 새 프로비저닝 요청을 제출할 때 정책이 적용됨을 보여 주는 다이어그램](./media/concepts-device-reprovisioning/dps-reprovisioning-reset.png)

* **다시 프로비전하지 않음**: 디바이스가 다른 허브에 다시 할당되지 않습니다. 이 정책은 이전 버전과 호환성을 관리하기 위한 용도로 제공됩니다.

> [!NOTE]
> DPS는 디바이스에 대한 새 [ReturnData](how-to-send-additional-data.md)가 있는 경우 재프로비저닝 정책에 관계 없이 항상 사용자 지정 할당 웹후크를 호출합니다. 재프로비저닝 정책이 **재프로비저닝되지 않음** 으로 설정된 경우 웹후크가 호출되지만 디바이스는 할당된 허브를 변경하지 않습니다.

### <a name="managing-backwards-compatibility"></a>이전 버전과의 호환성 관리

2018년 9월 이전에는 IoT Hub에 디바이스를 할당할 때 스티키 동작이 적용되었습니다. 즉, 프로비전 프로세스를 거친 디바이스는 같은 IoT Hub에만 다시 할당되었습니다.

이 동작을 사용했던 솔루션의 경우에는 Provisioning Service에서 이전 버전과의 호환성을 제공합니다. 현재는 다음 기준을 충족하는 디바이스에서 이 동작을 계속 사용할 수 있습니다.

1. 디바이스가 Device Provisioning Service에서 다시 프로비전 지원이 기본적으로 제공되기 전의 API 버전에 연결합니다. API 버전은 아래의 API 표를 참조하세요.

2. 디바이스의 등록 항목에 다시 프로비전 정책이 설정되어 있지 않습니다.

이처럼 이전 버전과의 호환성이 유지되므로 이전에 배포된 디바이스에 초기 테스트 중에 확인된 것과 동일한 동작이 계속 적용됩니다. 이전 동작을 유지하려면 이러한 등록에 다시 프로비전 정책을 저장하지 마세요. 다시 프로비전 정책을 설정하면 해당 정책이 이전 동작보다 우선적으로 적용됩니다. 다시 프로비전 정책이 우선적으로 적용되도록 하는 경우 고객이 디바이스를 이미지로 다시 설치하지 않고도 디바이스 동작을 업데이트할 수 있습니다.

다음 순서도에는 해당 동작이 적용되는 시기가 표시되어 있습니다.

![이전 버전과의 호환성 관리 순서도](./media/concepts-device-reprovisioning/reprovisioning-compatibility-flow.png)

아래 표에는 Device Provisioning Service에서 다시 프로비전 지원이 기본적으로 제공되기 전의 API 버전이 나와 있습니다.

| REST API | C SDK | Python SDK |  Node SDK | Java SDK | .NET SDK |
| -------- | ----- | ---------- | --------- | -------- | -------- |
| [2018-04-01 이하](/rest/api/iot-dps/service/individual-enrollment/create-or-update#uri-parameters) | [1.2.8 이하](https://github.com/Azure/azure-iot-sdk-c/blob/master/version.txt) | [1.4.2 이하](https://github.com/Azure/azure-iot-sdk-python/blob/0a549f21f7f4fc24bc036c1d2d5614e9544a9667/device/iothub_client_python/src/iothub_client_python.cpp#L53) | [1.7.3 이하](https://github.com/Azure/azure-iot-sdk-node/blob/074c1ac135aebb520d401b942acfad2d58fdc07f/common/core/package.json#L3) | [1.13.0 이하](https://github.com/Azure/azure-iot-sdk-java/blob/794c128000358b8ed1c4cecfbf21734dd6824de9/device/iot-device-client/pom.xml#L7) | [1.1.0 이하](https://github.com/Azure/azure-iot-sdk-csharp/blob/9f7269f4f61cff3536708cf3dc412a7316ed6236/provisioning/device/src/Microsoft.Azure.Devices.Provisioning.Client.csproj#L20)

> [!NOTE]
> 이러한 값과 링크는 변경될 수 있습니다. 이 정보는 고객이 버전을 확인할 수 있는 위치와 필요한 버전을 표시하기 위한 자리 표시자로만 사용됩니다.

## <a name="next-steps"></a>다음 단계

* [디바이스를 다시 프로비전하는 방법](how-to-reprovision.md)
