---
title: Azure IoT Hub 리소스의 장치 업데이트 이해 | Microsoft Docs
description: Azure IoT Hub 리소스의 장치 업데이트 이해
author: vimeht
ms.author: vimeht
ms.date: 2/11/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: e8194a269aec09f087632d2f6069d0624d7a835b
ms.sourcegitcommit: c385af80989f6555ef3dadc17117a78764f83963
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2021
ms.locfileid: "111410660"
---
# <a name="device-update-resources"></a>장치 업데이트 리소스

IoT Hub의 장치 업데이트를 사용하려면 장치 업데이트 계정 및 인스턴스 리소스를 만들어야 합니다. 

## <a name="device-update-account"></a>장치 업데이트 계정

장치 업데이트 계정은 Azure 구독 내에서 만드는 리소스입니다. 장치 업데이트 계정 수준에서 장치 업데이트 계정을 만들 지역을 선택할 수 있습니다. 장치 업데이트에 액세스할 사용자에게 부여할 권한을 설정할 수도 있습니다.


## <a name="device-update-instance"></a>장치 업데이트 인스턴스
계정을 만든 후에는 장치 업데이트 인스턴스를 만들어야 합니다. 인스턴스는 특정 IoT hub와 연결된 업데이트 및 배포가 포함된 논리적 컨테이너입니다. 장치 업데이트는 IoT hub를 장치 디렉터리로 사용하고 장치와의 통신 채널을 사용합니다. 

공개 미리 보기 중에는 구독마다 두 개의 장치 업데이트 계정을 만들 수 있습니다. 또한 계정마다 두 개의 장치 업데이트 인스턴스를 만들 수 있습니다.

## <a name="configuring-device-update-linked-iot-hub"></a>장치 업데이트 연결된 IoT Hub 구성 

장치 업데이트는 IoT Hub에서 변경 알림을 받을 수 있도록 "기본 제공" Event Hub와 통합됩니다. 인스턴스 내에서 "IoT Hub 구성" 단추를 클릭하면 IoT 디바이스와 통신하는 데 필요한 메시지 경로, 소비자 그룹 및 액세스 정책이 구성됩니다. 

### <a name="message-routing"></a>메시지 라우팅

장치 업데이트에 대해 다음 메시지 경로가 구성됩니다.

|   경로 이름    | 데이터 원본 | 라우팅 쿼리  | 엔드포인트 | 설명  |
| :--------- | :---- |:---- |:---- |:---- |
|  DeviceUpdate.DigitalTwinChanges | DigitalTwinChangeEvents | true | events | 디지털 쌍 변경 이벤트 수신 대기  |
|  DeviceUpdate.DeviceLifecycle | DeviceLifecycleEvents | opType = 'deleteDeviceIdentity' OR opType = 'deleteModuleIdentity'  | events | 삭제된 장치 수신 대기 |
|  DeviceUpdate.DeviceTwinEvents| TwinChangeEvents | (opType = 'updateTwin' OR opType = 'replaceTwin') 및 IS_DEFINED($body.tags.ADUGroup) | events | 새 장치 업데이트 그룹 수신 대기 |

> [!NOTE]
> 이러한 경로를 구성할 때 경로 이름은 중요하지 않습니다. 이름이 일관되고 디바이스 업데이트에 사용되고 있음을 쉽게 식별할 수 있도록 DeviceUpdate를 접두사로 포함합니다. 디바이스 업데이트가 제대로 작동하려면 아래 표와 같이 나머지 경로 속성을 구성해야 합니다. 

### <a name="consumer-group"></a>소비자 그룹

IoT Hub를 구성하면 디바이스 업데이트 관리 서비스에 필요한 이벤트 허브 소비자 그룹도 만들어집니다. 

:::image type="content" source="media/device-update-resources/consumer-group.png" alt-text="소비자 그룹의 스크린샷." lightbox="media/device-update-resources/consumer-group.png":::

### <a name="access-policy"></a>액세스 정책

"Deviceupdateservice"라는 공유 액세스 정책은 디바이스 업데이트 관리 서비스에서 업데이트 가능 디바이스를 쿼리하는 데 필요합니다. "deviceupdateservice" 정책이 생성되고 IoT Hub를 구성하는 과정에서 다음과 같은 사용 권한이 제공됩니다.
- 레지스트리 읽기
- 서비스 연결
- 디바이스 연결

:::image type="content" source="media/device-update-resources/access-policy.png" alt-text="액세스 정책의 스크린샷." lightbox="media/device-update-resources/access-policy.png":::

## <a name="next-steps"></a>다음 단계

[장치 업데이트 리소스 만들기](./create-device-update-account.md)
