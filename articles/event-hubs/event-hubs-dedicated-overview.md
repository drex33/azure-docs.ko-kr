---
title: 전용 이벤트 허브 개요 - Azure Event Hubs | Microsoft Docs
description: 이 문서에서는 Event Hub의 단일 테넌트 배포를 제공하는 전용 Azure Event Hubs에 대한 개요를 제공합니다.
ms.topic: article
ms.date: 09/23/2021
ms.openlocfilehash: 696df96c9ac833d937fb89c52842b09ace4c4ec5
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129210722"
---
# <a name="overview-of-event-hubs-dedicated"></a>Event Hubs Dedicated의 개요

*Event Hubs 클러스터* 는 스트리밍 요구 사항이 가장 까다로운 고객을 위한 단일 테넌트 배포를 제공합니다. 이 단일 테넌트 제품은 99.99% SLA를 보장하며 당사의 전용 가격 책정 계층에서만 사용할 수 있습니다. 보장된 용량을 제공하고 대기 시간이 1초 미만에 불과한 Event Hubs 클러스터는 초당 수백만 개의 이벤트를 수신할 수 있습니다. 전용 클러스터 내에서 생성된 네임스페이스 및 이벤트 허브는 프리미엄 제품의 모든 기능을 포함하지만 수신 제한은 없습니다. 여기에는 또한 추가 비용 없이 인기 있는 [Event Hubs 캡처](event-hubs-capture-overview.md) 기능이 포함됩니다. 이 기능을 사용하면 데이터 스트림을 자동으로 일괄처리하고 Azure Storage 또는 Azure Data Lake에 기록할 수 있습니다. 

클러스터는 미리 할당된 CPU 및 메모리 리소스의 양을 의미하는 **CU(용량 단위)** 로 프로비저닝 및 청구됩니다. 각 클러스터에 1, 2, 4, 8, 12, 16 또는 20개 CU를 구매할 수 있습니다. CU당 수집하고 스트리밍할 수 있는 양은 다음과 같은 다양한 요인에 따라 다릅니다. 

- 생산자와 소비자의 수
- 페이로드 셰이프
- 송신 속도

> [!NOTE]
> 모든 Event Hubs 클러스터는 기본값으로 Kafka를 사용하도록 설정되며, 기존 Kafka 기반 애플리케이션에서 사용할 수 있는 Kafka 엔드포인트를 지원합니다. 클러스터에서 Kafka을 사용하도록 설정하면 Kafka 이외의 사용 사례에 영향을 주지 않습니다. 클러스터에서 Kafka를 사용하지 않도록 설정하는 옵션은 없으며, 그럴 필요도 없습니다.

## <a name="why-dedicated"></a>전용이어야 하는 이유

전용 Event Hubs는 엔터프라이즈급 용량을 필요로 하는 고객을 위해 다음 세 가지 강력한 혜택을 제공합니다.

#### <a name="single-tenancy-guarantees-capacity-for-better-performance"></a>성능 향상을 위해 단일 테넌트의 용량 보장

전용 클러스터는 전체 규모에서 용량을 보장합니다. 이는 트래픽 버스트를 수용하기 위해 완전 지속성 스토리지와 1초 미만의 대기 시간으로 최대 기가바이트의 스트리밍 데이터를 수신할 수 있습니다. 

#### <a name="inclusive-and-exclusive-access-to-features"></a>기능에 대한 포괄적이고 독점적인 액세스

전용 제품에는 추가 비용 없이 캡처와 같은 기능과 BYOK(Bring Your Own Key)와 같은 기능에 대한 독점적 액세스가 포함됩니다. 또한 이 서비스가 부하 분산, OS 업데이트, 보안 패치 및 분할을 관리합니다. 따라서 인프라 유지 관리에 더 적은 시간을 소비하고, 클라이언트 쪽 기능 빌드에 더 많은 시간을 소비할 수 있습니다.  

## <a name="event-hubs-dedicated-quotas-and-limits"></a>Event Hubs Dedicated 할당량 및 제한
Event Hubs Dedicated 제품은 고정된 월별 가격으로 청구되며, 최소 사용량은 4시간입니다. 전용 계층은 프리미엄 요금제의 모든 기능을 제공하지만, 워크로드가 까다로운 고객을 위해 엔터프라이즈급 용량 및 제한을 제공합니다. 

할당량 및 제한에 대한 자세한 내용은 [Event Hubs 할당량 및 제한](event-hubs-quotas.md)을 참조하세요.

## <a name="how-to-onboard"></a>등록 방법

[Azure Portal](https://aka.ms/eventhubsclusterquickstart)를 통해 [Event Hubs 클러스터를 만드는](event-hubs-dedicated-cluster-create-portal.md) 자체 서비스 환경은 이제 미리 보기로 제공됩니다. Event Hubs Dedicated에 대한 온보딩에 대한 질문이 있거나 도움이 필요한 경우, [Event Hubs 팀](mailto:askeventhubs@microsoft.com)에 문의하세요.

## <a name="faqs"></a>FAQ

[!INCLUDE [event-hubs-dedicated-clusters-faq](./includes/event-hubs-dedicated-clusters-faq.md)]

## <a name="next-steps"></a>다음 단계

Event Hubs Dedicated에 대한 추가 정보를 얻으려면, Microsoft 영업 담당자 또는 Microsoft 지원에 문의하세요. 다음 링크에서도 Event Hubs 가격 책정 계층에 대한 자세한 내용을 확인할 수 있습니다.

- [Azure Portal](https://aka.ms/eventhubsclusterquickstart)을 통해 Event Hub 클러스터 만들기 
- [Event Hubs Dedicated 가격 책정](https://azure.microsoft.com/pricing/details/event-hubs/) Microsoft 영업 담당자 또는 Microsoft 지원 서비스에서 Event Hubs Dedicated 용량에 대한 추가 정보를 얻을 수도 있습니다.
- [Event Hubs FAQ](event-hubs-faq.yml)에서는 가격 책정 정보를 설명하고 이벤트 허브에 대해 일부 자주 묻는 질문에 대한 답변을 제공합니다.
