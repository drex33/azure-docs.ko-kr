---
title: Event Hubs Premium 개요
description: 이 문서에서는 고급 스트리밍 요구 사항에 대해 Event Hubs 다중 테넌트 배포를 제공하는 Azure Event Hubs 프리미엄을 간략히 설명합니다.
ms.topic: article
ms.date: 10/20/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 8822b44f5f2a6d5cbc367da9a8ac5d397f87e9f7
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131073019"
---
# <a name="overview-of-event-hubs-premium"></a>Event Hubs Premium 개요

Event Hubs 프리미엄 계층은 예측 가능한 대기 시간으로 탄력적이고 성능이 우수한 고급 시나리오에 맞도록 설계되었습니다. 관리형 다중 테넌트 PaaS 환경에서 테넌트 간 간섭을 최소화하는 예약된 컴퓨팅, 메모리 및 스토리지 리소스를 제공하여 우수한 성능을 달성합니다. 

Event Hubs Premium에는 내구성 손상 없이 이전 세대 보다 훨씬 더 예측 가능 하 고 훨씬 낮은 전송 및 통과 대기 시간을 제공 하는 2 계층의 새로운 네이티브 코드 로그 엔진이 도입 되었습니다. Event Hubs 프리미엄은 각 이벤트를 Azure 가용성 영역(사용 가능한 경우)에 분산된 세 개의 복제본에 복제하며, 모든 복제본은 전송 작업 완료가 보고되기 전에 기본 고속 스토리지에 동기식으로 플러시됩니다. 즉시 읽지 않거나 나중에 다시 읽어야 하는 이벤트는 가용성 영역 중복 스토리지 계층에 투명하게 최대 90일까지 보존할 수 있습니다. 고속 스토리지와 보존 스토리지 계층 모두에서 이벤트는 암호화됩니다. Event Hubs 프리미엄에서는 암호화 키를 사용자가 제공할 수 있습니다. 

이러한 스토리지 관련 기능과 Event Hubs 표준 서비스의 모든 기능 및 프로토콜 지원 외에도 Event Hubs 프리미엄의 격리 모델을 사용하면 동적 파티션 수직 확장 등의 새로운 기능과 향후 추가될 기능을 사용할 수 있습니다. 또한 훨씬 더 많은 할당량을 할당받을 수 있습니다. Event Hubs 캡처가 추가 비용 없이 포함됩니다.

프리미엄 서비스의 요금은 기본 인프라에서 격리된 리소스(CPU, 메모리 및 스토리지)의 공유에 해당하는 [PU(처리 단위)](event-hubs-scalability.md#processing-units)를 기준으로 청구됩니다. 

Dedicated 제품과 달리, Event Hubs 프리미엄은 리소스를 신속하게 이동할 수 있는 대규모 다중 테넌트 환경 내에서 격리를 제공하기 때문에 훨씬 더 탄력적이고 빠르게 확장할 수 있으며 PU를 동적으로 조정할 수 있습니다. 따라서 Event Hubs 프리미엄은 Event Hubs Dedicated와 비교 시 특히 하루 또는 한 주 동안 부하가 변화하는 경우에 중간 범위(<120MB/초) 처리량 요구 사항보다 더 비용 효율적일 때가 많은 옵션입니다. 
> [!NOTE]
> Event Hubs Premium은 TLS 1.2 이상만 지원합니다. 

가용성 영역 지원을 통한 추가적인 성능 강화를 위해 Event Hubs Dedicated 최소 배포 규모는 8CU(용량 단위)이지만, 모든 AZ 지역의 첫 번째 PU부터 Event Hubs 프리미엄에서 가용성 영역 지원이 제공됩니다. 

네임스페이스별로 1, 2, 4, 8 및 16개의 처리 단위를 구입할 수 있습니다. Event Hubs 프리미엄은 용량에 기반한 서비스이기 때문에 달성 가능한 처리량이 Event Hubs 표준처럼 제한을 통해 설정되는 것이 아니라 Event Hubs Dedicated와 비슷하게 사용자가 Event Hubs에 요청한 작업에 따라 달라집니다. PU당 유효한 수집 및 스트림 처리량은 다음을 비롯한 다양한 요인에 따라 달라집니다.

* 생산자와 소비자의 수
* 페이로드 크기 
* 파티션 수
* 송신 요청 속도 
* Event Hubs 캡처, 스키마 레지스트리 및 기타 고급 기능 사용

자세한 내용은 [Event Hubs SKU 간 비교](event-hubs-quotas.md)를 참조하세요.


> [!NOTE]
> 모든 Event Hubs 네임스페이스는 기존 Kafka 기반 애플리케이션에서 기본적으로 Apache Kafka RPC 프로토콜에 사용하도록 설정됩니다. 클러스터에서 Kafka을 사용하도록 설정하면 Kafka 이외의 사용 사례에 영향을 주지 않습니다. 클러스터에서 Kafka를 사용하지 않도록 설정하는 옵션은 없으며, 그럴 필요도 없습니다.

## <a name="why-premium"></a>프리미엄을 선택해야 하는 이유

Event Hubs 프리미엄은 대기 시간은 낮고 처리량 데이터 수집 요구는 높은 다중 테넌트 환경에서 더 뛰어난 격리가 필요한 고객에게 세 가지 강력한 이점을 제공합니다.

#### <a name="superior-performance-with-the-new-two-tier-storage-engine"></a>새로운 2계층 스토리지 엔진을 통해 뛰어난 성능 제공

Event Hubs 프리미엄은 내구성에 대한 보장을 훼손시키지 않으면서 전체 대기 시간 및 대기 시간 지터를 상당히 줄여 데이터 수신 성능을 크게 향상시키는 새로운 2계층 로그 스토리지 엔진을 사용합니다. 

#### <a name="better-isolation-and-predictability"></a>격리 및 예측 가능성 향상

Event Hubs 프리미엄은 격리된 컴퓨팅 및 메모리 용량을 제공하여 다중 테넌트 배포에서 대기 시간을 더 예측 가능하게 만들고 *잡음이 많은 네트워크 환경* 에 미치는 영향의 위험을 훨씬 줄입니다.

Event Hubs 프리미엄은 다중 테넌트 클러스터에서 *클러스터 내 클러스터* 모델을 구현하여 관리형 다중 테넌트 PaaS 환경의 모든 이점을 유지하면서 예측 가능성과 성능을 향상시킵니다. 


#### <a name="cost-savings-and-scalability"></a>비용 절감 및 확장성
Event Hubs 프리미엄은 다중 테넌트 서비스이므로 보다 유연하고 신속하게 동적으로 확장할 수 있습니다. 용량은 클러스터 내에서 격리된 CPU/메모리 Pod를 할당하는 처리 단위별로 할당됩니다. 네임스페이스당 이러한 Pod의 수는 확장/축소할 수 있습니다. 따라서 Event Hubs 프리미엄은 전체 처리량 범위가 120MB/초보다 낮고 표준 SKU로 달성할 수 있는 것보다 높은 메시징 시나리오에 맞는 저렴한 옵션입니다.  

## <a name="quotas-and-limits"></a>할당량 및 제한
프리미엄 계층은 표준 요금제의 모든 기능을 제공하지만 더 나은 성능, 격리 및 더 많은 할당량을 제공합니다. 할당량 및 제한에 대한 자세한 내용은 [Event Hubs 할당량 및 제한](event-hubs-quotas.md)을 참조하세요.


## <a name="faqs"></a>FAQ

[!INCLUDE [event-hubs-dedicated-clusters-faq](./includes/event-hubs-premium-faq.md)]

## <a name="next-steps"></a>다음 단계

[Azure Portal](https://portal.azure.com/#create/Microsoft.EventHub)를 통해 Event Hubs Premium를 사용 하 여 시작할 수 있습니다. 가격 책정 및 [Event Hubs FAQ](event-hubs-faq.yml)에 대한 자세한 내용은 [Event Hubs 프리미엄 가격 책정](https://azure.microsoft.com/pricing/details/event-hubs/)에서 Event Hubs에 대한 몇 가지 질문과 대답을 참조하세요. 
