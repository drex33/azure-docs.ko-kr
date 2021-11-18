---
title: 디바이스 오프라인으로 작동 - Azure IoT Edge | Microsoft Docs
description: IoT Edge 디바이스 및 모듈을 인터넷 연결 없이 오프라인으로 더 오래 작동하는 방법과 IoT Edge를 사용하여 일반 IoT 디바이스를 오프라인으로 작동하는 방법을 이해합니다.
author: kgremban
ms.author: kgremban
ms.date: 11/22/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: f0447d886906bc1632f51ecaf1769de2d5679bd5
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2021
ms.locfileid: "132704926"
---
# <a name="understand-extended-offline-capabilities-for-iot-edge-devices-modules-and-child-devices"></a>IoT Edge 디바이스, 모듈 및 자식 디바이스용 확장 오프라인 기능을 이해합니다.

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Azure IoT Edge는 IoT Edge 디바이스에서 확장된 오프라인 작업을 지원하고, 자식 디바이스에서도 오프라인 작업을 지원합니다. IoT Edge 디바이스를 IoT Hub에 연결할 수 있는 한, 해당 디바이스 및 자식 디바이스는 일시적으로 인터넷에 연결하여 또는 인터넷 연결 없이 계속 작동할 수 있습니다.

## <a name="how-it-works"></a>작동 방법

IoT Edge 디바이스가 오프라인으로 전환되면 IoT Edge 허브는 세 가지 역할을 맡습니다. 첫째, 업스트림으로 이동하는 모든 메시지를 저장하고 디바이스가 다시 연결될 때까지 보관합니다. 둘째, 모듈 및 자식 디바이스가 계속 작동할 수 있도록 IoT Hub를 대신하여 모듈 및 자식 디바이스를 인증합니다. 셋째, 일반적으로 IoT Hub를 통과하는 자식 디바이스 간 통신을 지원합니다.

다음 예제에서는 오프라인 모드에서 작동하는 IoT Edge 시나리오를 보여줍니다.

1. **디바이스 구성**

   IoT Edge 디바이스는 자동으로 오프라인 기능이 설정됩니다. 다른 디바이스에 해당 기능을 확장하려면 할당된 부모 디바이스를 신뢰하도록 자식 디바이스를 구성하고 게이트웨이인 부모 디바이스를 통해 디바이스-클라우드 통신을 라우팅해야 합니다.

2. **IoT Hub와 동기화**

   적어도 IoT Edge 런타임을 설치한 후 IoT Edge 디바이스를 온라인에 연결하여 IoT Hub와 동기화해야 합니다. 이 동기화에서 IoT Edge 디바이스는 할당된 모든 자식 디바이스에 대한 세부 정보를 얻습니다. 또한 IoT Edge 디바이스는 오프라인 작업을 사용하도록 설정하고 원격 분석 메시지의 로컬 스토리지에 대한 설정을 검색하도록 로컬 캐시를 안전하게 업데이트합니다.

3. **오프라인으로 전환**

   IoT Hub와의 연결이 끊어진 동안 IoT Edge 디바이스, 배포된 모듈 및 자식 디바이스는 무기한 작동할 수 있습니다. 모듈과 자식 디바이스는 오프라인 상태에서 IoT Edge 허브로 인증하여 시작 및 다시 시작할 수 있습니다. IoT Hub에 업스트림 바인딩된 원격 분석 데이터는 로컬로 저장됩니다. 모듈 간 통신 또는 자식 디바이스 간 통신은 직접 메서드 또는 메시지를 통해 유지됩니다.

4. **IoT Hub와 다시 연결하고 다시 동기화**

   IoT Hub와의 연결이 복원되면 IoT Edge 디바이스가 다시 동기화됩니다. 로컬로 저장된 메시지는 IoT Hub로 즉시 전달되지만 연결 속도, IoT Hub 대기 시간 및 관련 요인에 따라 달라집니다. 저장된 순서와 동일한 순서로 전달됩니다.

   모듈 및 디바이스의 desired 속성과 reported 속성 간 차이가 조정됩니다. IoT Edge 디바이스는 할당된 자식 디바이스에 변경 내용을 업데이트합니다.

## <a name="restrictions-and-limits"></a>제한 사항 및 제한

이 문서에서 설명한 확장된 오프라인 기능은 [IoT Edge 버전 1.0.7 이상](https://github.com/Azure/azure-iotedge/releases)에서 사용할 수 있습니다. 이보다 낮은 버전은 오프라인 기능 집합을 갖고 있습니다. 확장된 오프라인 기능이 없는 기존 IoT Edge 디바이스는 런타임 버전을 변경하여 업그레이드할 수 없지만, 새 IoT Edge 디바이스 ID를 사용하여 이러한 기능을 획득하도록 다시 구성해야 합니다.

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

비 IoT Edge 디바이스는 오직 한 대만 자식 디바이스로 추가할 수 있습니다.

:::moniker-end
<!-- end 1.1 -->

IoT Edge 디바이스 및 할당된 자식 디바이스는 초기 일회성 동기화 후 오프라인으로 무기한 작동할 수 있습니다. 그러나 메시지 스토리지는 TTL(Time to Live) 및 메시지 저장에 사용 가능한 디스크 공간에 따라 달라집니다.

## <a name="set-up-parent-and-child-devices"></a>부모 및 자식 디바이스 설정

기본적으로 부모 장치는 최대 100 개의 자식을 포함할 수 있습니다. EdgeHub 모듈에서 **MaxConnectedClients** 환경 변수를 설정 하 여이 제한을 변경할 수 있습니다. 자식 장치에는 하나의 부모만 있습니다.

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

동일한 IoT Hub에 등록된 모든 비 IoT Edge 디바이스는 자식 디바이스가 될 수 있습니다.

:::moniker-end
<!-- end 1.1 -->

<!-- 1.2 -->
:::moniker range="iotedge-2020-11"

동일한 IoT Hub에 등록된 모든 디바이스, IoT Edge 또는 비 IoT Edge는 자식 디바이스가 될 수 있습니다.

:::moniker-end
<!-- end 1.2 -->

IoT Edge 디바이스와 IoT 디바이스 간에 부모-자식 관계를 만드는 데 익숙하지 않은 경우 [Azure IoT Hub에 다운스트림 디바이스 인증](how-to-authenticate-downstream-device.md)을 참조하세요. 대칭 키, 자체 서명된 X.509 및 CA 서명된 X.509 섹션에는 Azure Portal 및 Azure CLI를 사용하여 디바이스를 만들 때 부모-자식 관계를 정의하는 방법에 대한 예제가 나와 있습니다. 기존 디바이스의 경우 부모 또는 자식 디바이스의 디바이스 세부 정보 페이지에서 관계를 선언할 수 있습니다.

<!-- 1.2 -->
:::moniker range="iotedge-2020-11"

두 IoT Edge 디바이스 간에 부모-자식 관계를 만드는 방법을 잘 모를 경우 [Azure IoT Edge 게이트웨이에 다운스트림 IoT Edge 디바이스 연결](how-to-connect-downstream-iot-edge-device.md)을 참조하세요.

:::moniker-end
<!-- end 1.2 -->

### <a name="set-up-the-parent-device-as-a-gateway"></a>부모 디바이스를 게이트웨이로 설정

부모/자식 관계를 투명 게이트웨이로 간주할 수 있습니다. 여기서 자식 디바이스는 IoT Hub에서 자체 ID를 갖지만 부모 디바이스를 거쳐 클라우드를 통해 통신합니다. 안전한 통신을 위해 자식 디바이스는 부모 디바이스가 신뢰할 수 있는 원본에서 온 것임을 확인할 수 있어야 합니다. 그렇지 않으면 제3자가 악성 디바이스를 설정하여 부모 디바이스로 가장하고 통신을 가로챌 수 있습니다.

이 트러스트 관계를 만드는 한 가지 방법은 다음 문서에 자세히 설명되어 있습니다.

* [IoT Edge 디바이스를 투명 게이트웨이로 작동하도록 구성](how-to-create-transparent-gateway.md)
* [다운스트림(자식) 디바이스를 Azure IoT Edge 게이트웨이에 연결](how-to-connect-downstream-device.md)

## <a name="specify-dns-servers"></a>DNS 서버 지정

견고성을 향상시키려면 환경에서 사용되는 DNS 서버 주소를 지정하는 것이 좋습니다. IoT Edge에 대한 DNS 서버를 설정하려면 문제 해결 문서의 [Edge 에이전트 모듈이 지속적으로 '빈 구성 파일'을 보고하고 디바이스에서 모듈이 시작되지 않음](troubleshoot-common-errors.md#edge-agent-module-reports-empty-config-file-and-no-modules-start-on-the-device)을 참조하세요.

## <a name="optional-offline-settings"></a>선택적 오프라인 설정

디바이스가 오프라인으로 전환되는 경우 연결이 다시 설정될 때까지 IoT Edge 부모 디바이스에서 모든 디바이스-클라우드 메시지를 저장합니다. IoT Edge 허브 모듈은 오프라인 메시지의 저장 및 전달을 관리합니다. 오랜 시간 동안 오프라인 상태가 될 수 있는 디바이스의 경우 두 가지 IoT Edge 허브 설정을 구성하여 성능을 최적화합니다.

먼저 IoT Edge 허브가 디바이스를 다시 연결하는 데 충분한 시간 동안 메시지를 유지할 수 있도록 TTL(TIme to live) 설정을 늘립니다. 그런 다음, 메시지 스토리지에 대한 추가 디스크 공간을 추가합니다.

### <a name="time-to-live"></a>TTL(Time to live)

TTL(Time to Live) 설정은 메시지가 만료되기 전까지 대기할 수 있는 시간의 양(초)입니다. 기본값은 7200초(2시간)입니다. 최댓값은 정수 변수의 최댓값(약 20억)에 의해서만 제한됩니다.

이 설정은 모듈 쌍에 저장되는 IoT Edge 허브의 desired 속성입니다. Azure Portal 또는 배포 매니페스트에서 직접 구성할 수 있습니다.

```json
"$edgeHub": {
    "properties.desired": {
        "schemaVersion": "1.0",
        "routes": {},
        "storeAndForwardConfiguration": {
            "timeToLiveSecs": 7200
        }
    }
}
```

### <a name="host-storage-for-system-modules"></a>시스템 모듈의 호스트 스토리지

메시지 및 모듈 상태 정보는 기본적으로 IoT Edge 허브의 로컬 컨테이너 파일 시스템에 저장됩니다. 특히 오프라인으로 작업하는 경우 안정성을 높이기 위해 호스트 IoT Edge 디바이스에 전용 스토리지를 지정할 수 있습니다. 자세한 내용은 [모듈에 디바이스의 로컬 스토리지에 대한 액세스 권한 부여](how-to-access-host-storage-from-module.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

부모/자식 디바이스 연결에 투명 게이트웨이를 설정하는 방법에 대해 자세히 알아봅니다.

* [IoT Edge 디바이스를 투명 게이트웨이로 작동하도록 구성](how-to-create-transparent-gateway.md)
* [Azure IoT Hub에 다운스트림 디바이스 인증](how-to-authenticate-downstream-device.md)
* [다운스트림 디바이스를 Azure IoT Edge 게이트웨이에 연결](how-to-connect-downstream-device.md)
