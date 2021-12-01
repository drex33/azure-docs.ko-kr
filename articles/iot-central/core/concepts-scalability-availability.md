---
title: Azure IoT 중앙 확장성 및 고가용성 | Microsoft Docs
description: 이 문서에서는 IoT Central 더 많은 장치와 고가용성을 처리 하도록 자동으로 크기를 조정 하는 방법을 설명 합니다.
author: dominicbetts
ms.author: dobett
ms.date: 10/14/2021
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.openlocfilehash: bb58191c9725c0b74b3687d84964bb93435b49b1
ms.sourcegitcommit: 9567c42d1e5270af16a1a8090f11a3b12131010d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/01/2021
ms.locfileid: "133425812"
---
# <a name="scalability-and-high-availability"></a>확장성 및 고가용성

IoT Central 응용 프로그램은 IoT Hub 및 DPS (장치 프로 비전 서비스)와 같은 여러 Azure 서비스를 내부적으로 사용 합니다. 이러한 기본 서비스는 대부분 멀티 테 넌 트입니다. 그러나 고객 데이터의 전체 격리를 보장 하기 위해 IoT Central는 단일 테 넌 트 IoT hub를 사용 합니다. IoT Central는 여러 인스턴스를 자동으로 관리 하 여 IoT Central 응용 프로그램을 확장 하 고 항상 사용 가능한 상태로 만듭니다.

IoT Central는 응용 프로그램의 부하 프로필을 기반으로 IoT hub의 크기를 자동으로 조정 합니다. IoT Central는 개별 IoT hub를 확장 하 고 IoT hub의 수를 확장할 수 있습니다. 항상 사용 가능한 장치 연결의 경우 모든 IoT Central에는 항상 두 개 이상의 IoT 허브가 있습니다. IoT Central IoT hub를 관리 하지만 여러 IoT hub가 장치 펌웨어 구현에 영향을 줍니다.

IoT Central 응용 프로그램의 IoT hub는 모두 동일한 Azure 지역에 있습니다. 이러한 이유로, 격리 된 중단이 발생 하면 다중 IoT hub 아키텍처에서 항상 사용 가능한 장치 연결을 제공 합니다. 전체 Azure 지역을 사용할 수 없게 되 면 재해 복구 절차에서 전체 IoT Central 응용 프로그램을 다른 지역으로 장애 조치 (failover) 합니다.

## <a name="device-provisioning"></a>디바이스 프로비전

장치가 IoT Central에 연결 하기 전에 기본 서비스에서 등록 및 프로 비전 되어야 합니다. IoT Central 응용 프로그램에 장치를 추가 하는 경우 IoT Central DPS 등록 그룹에 항목을 추가 합니다. ID 범위, 장치 ID 및 키와 같은 등록 그룹의 정보는 IoT Central UI에 표시 됩니다.

장치가 IoT Central 응용 프로그램에 처음으로 연결 될 때 DPS는 등록 그룹의 연결 된 IoT hub 중 하나에서 장치를 프로 비전 합니다. DPS는 할당 정책을 사용 하 여 응용 프로그램의 IoT hub에서 프로 비전의 부하를 분산 합니다. 이 프로세스를 통해 각 IoT hub에 비슷한 수의 프로 비전 된 장치가 있습니다.

IoT Central에서 등록 및 프로 비전에 대해 자세히 알아보려면 [Azure IoT Central에 연결](concepts-get-connected.md)을 참조 하세요.

## <a name="device-connections"></a>디바이스 연결

DPS가 IoT hub에 장치를 프로 비전 한 후 장치는 항상 해당 허브에 연결을 시도 합니다. 장치가 프로 비전 된 IoT hub에 연결할 수 없는 경우 IoT Central 응용 프로그램에 연결할 수 없습니다. 이 시나리오를 처리 하려면 장치 펌웨어에서 재시도 전략을 포함 해야 합니다.

장치 펌웨어가 연결 오류를 처리 하 고 다른 허브에 연결 하는 방법에 대 한 자세한 내용은 [장치 개발을 위한 모범 사례](concepts-best-practices.md)를 참조 하세요.

장치 펌웨어가 연결 오류를 처리할 수 있는지 확인 하는 방법에 대 한 자세한 내용은 [테스트 장애 조치 (failover) 기능](concepts-best-practices.md#test-failover-capabilities)을 참조 하세요.

## <a name="data-export"></a>데이터 내보내기

IoT Central 응용 프로그램은 다른 사용자 구성 서비스를 사용 하는 경우가 많습니다. 예를 들어 Azure Event Hubs 및 Azure Blob Storage와 같은 서비스로 데이터를 지속적으로 내보내도록 IoT Central 응용 프로그램을 구성할 수 있습니다.

구성 된 데이터 내보내기가 해당 대상에 쓸 수 없는 경우 IoT Central는 최대 15 분 동안 데이터를 다시 전송 하려고 시도 합니다. 그 후에는 IoT Central에서 대상을 실패 한 것으로 표시 합니다. 실패 한 대상은 정기적으로 검사 하 여 쓰기 가능한 지 확인 합니다.

데이터 내보내기를 사용 하지 않도록 설정 하 고 다시 사용 하도록 설정 하 여 실패 한 내보내기를 강제로 다시 시작할 IoT Central 수 있습니다.

사용 중인 데이터 내보내기 대상 서비스에 대 한 고가용성 및 확장성 모범 사례를 검토 합니다.

- Azure Blob Storage: [Blob 저장소에 대 한 중복성 및 성능 및 확장성 검사 목록](../../storage/blobs/storage-performance-checklist.md) [Azure Storage](../../storage/common/storage-redundancy.md)
- Azure Event Hubs: Event Hubs를 사용 하 여 Event Hubs 및 [크기 조정](../../event-hubs/event-hubs-scalability.md) [에서 가용성 및 일관성](../../event-hubs/event-hubs-availability-and-consistency.md)
- azure Service Bus: [Service Bus 중단 및 재해 로부터 응용 프로그램을 보호](../../service-bus-messaging/service-bus-outages-disasters.md) 하 고 [azure Service Bus 네임 스페이스의 메시징 단위를 자동으로 업데이트](../../service-bus-messaging/automate-update-messaging-units.md) 하기 위한 모범 사례

## <a name="limitations"></a>제한 사항

현재 여러 IoT hub 아키텍처로 마이그레이션되지 않은 4 월 2021 일 이전에 만든 몇 가지 레거시 IoT Central 응용 프로그램이 있습니다. 명령을 사용 `az iot central device manual-failover` 하 여 응용 프로그램에서 여전히 단일 IoT hub를 사용 하는지 확인 합니다.

현재 IoT Edge 장치는 IoT hub 간에 이동할 수 없습니다.

## <a name="next-steps"></a>다음 단계

Azure IoT central의 확장성 및 고가용성에 대해 알아보았습니다. 이제 권장 되는 다음 단계는 Azure IoT central의 [할당량 및 제한](concepts-quotas-limits.md) 에 대해 학습 하는 것입니다.
