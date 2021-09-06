---
title: 스케일링 성능 - Azure Event Hubs | Microsoft Docs
description: 이 문서에서는 파티션 및 처리량 단위를 사용하여 Azure Event Hubs 스케일링 방법에 대한 정보를 제공합니다.
ms.topic: article
ms.date: 05/26/2021
ms.openlocfilehash: ced3d366c87d05fc1d7e8370c6e7db793dfe2535
ms.sourcegitcommit: 54d8b979b7de84aa979327bdf251daf9a3b72964
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/24/2021
ms.locfileid: "112583665"
---
# <a name="scaling-with-event-hubs"></a>Event Hubs 스케일링

Event Hubs 스케일링에 영향을 주는 두 가지 요인이 있습니다.
* 처리량 단위(표준 계층) 또는 처리 단위(프리미엄 계층) 
* 파티션

## <a name="throughput-units"></a>처리량 단위

Event Hubs의 처리량 용량은 *처리량 단위* 로 제어됩니다. 처리량 단위는 미리 구입한 용량의 단위입니다. 단일 처리량을 사용하면 다음을 수행할 수 있습니다.

* 수신: 초당 최대 1MB 또는 초당 1,000회 이벤트(둘 중 빠른 쪽 적용).
* 송신: 초당 최대 2MB 또는 4096개의 이벤트.

구입한 처리량 단위의 용량을 초과하면 수신이 제한되며 [ServerBusyException](/dotnet/api/microsoft.azure.eventhubs.serverbusyexception)이 반환됩니다. 송신은 제한 예외를 생성하지 않지만 구입한 처리량 단위의 용량으로 제한됩니다. 게시 속도 예외를 수신하거나 더 높은 송신을 예상하는 경우 네임스페이스에 대해 구입한 처리량 단위의 수를 확인해야 합니다. [Azure Portal](https://portal.azure.com)에서 네임스페이스의 **크기 조정** 블레이드에서 처리량 단위를 관리할 수 있습니다. [Event Hubs API](./event-hubs-samples.md)를 사용하여 프로그래밍 방식으로 처리량 단위를 관리할 수도 있습니다.

처리량 단위는 미리 구입하는 방식이며 시간당 요금이 청구됩니다. 구입하면, 처리량 단위는 최소 한시간으로 청구됩니다. Event Hubs 네임스페이스에 대해 최대 40개의 처리량 단위를 구입할 수 있으며, 네임스페이스의 모든 Event Hubs에서 공유할 수 있습니다.

Event Hubs의 **자동 확장** 기능은 필요한 사용량에 맞게 처리량 단위 수를 증가하여 자동으로 확장합니다. 처리량 단위를 늘리면 다음과 같은 상황에서 제한 시나리오를 예방할 수 있습니다.

- 데이터 수신 속도가 설정된 처리량 단위를 초과하는 경우.
- 데이터 송신 요청 속도가 설정된 처리량 단위를 초과하는 경우.

ServerBusy 오류로 인한 요청 실패 없이 부하가 최소 임계값을 초과하면 Event Hubs 서비스는 처리량을 높입니다. 

자동 확장 기능에 대한 자세한 내용은 [처리량 단위 자동 스케일링](event-hubs-auto-inflate.md)을 참조하세요.

## <a name="processing-units"></a>처리 단위

 [Event Hubs 프리미엄](./event-hubs-premium-overview.md)은 관리형 다중 테넌트 PaaS 환경에서 뛰어난 성능과 더 나은 격리를 제공합니다. 프리미엄 계층의 리소스는 CPU 및 메모리 수준에서 격리되므로 각 테넌트 워크로드가 격리되어 실행됩니다. 이 리소스 컨테이너를 *PU(처리 단위)* 라고 합니다. 각 Event Hubs 프리미엄 네임스페이스에 대해 1, 2, 4, 8 또는 16개의 처리 단위를 구입할 수 있습니다. 

처리 단위로 수집하고 스트림할 수 있는 양은 생산자, 소비자, 수집 및 처리 속도 등과 같은 다양한 요인에 따라 달라집니다. 하나의 처리 단위는 스토리지가 제한 요소가 되지 않도록 충분한 파티션이 있는 경우 약 5~10MB/s 수신 및 10-20MB/s 송신의 코어 용량을 제공할 수 있습니다.  

프리미엄 계층 네임스페이스에 대한 CPU를 구성하는 방법에 대한 자세한 내용은 [처리 단위 구성](configure-processing-units-premium-namespace.md)을 참조하세요.

> [!NOTE]
> 할당량 및 제한에 대한 자세한 내용은 [Azure Event Hubs - 할당량 및 제한](event-hubs-quotas.md)을 참조하세요.

## <a name="partitions"></a>파티션
[!INCLUDE [event-hubs-partitions](./includes/event-hubs-partitions.md)]




## <a name="next-steps"></a>다음 단계
Event Hubs에 대한 자세한 내용은 다음 링크를 참조하세요.

- [표준 계층 네임스페이스에 대한 처리량 단위 자동 크기 조정](event-hubs-auto-inflate.md)
- [프리미엄 계층 네임스페이스에 대한 처리 단위 구성](configure-processing-units-premium-namespace.md)
