---
title: 지리적 재해 복구 - Azure Event Hubs| Microsoft Docs
description: 지리적 지역을 사용하여 장애 조치(Failover)하고 Azure Event Hubs에서 재해 복구를 수행하는 방법
ms.topic: article
ms.date: 06/21/2021
ms.openlocfilehash: 42057f88d76fb0822207ecaf0ece340101c6ec6d
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122536011"
---
# <a name="azure-event-hubs---geo-disaster-recovery"></a>Azure Event Hubs - 지리적 재해 복구 

데이터 처리 리소스의 치명적인 가동 중단에 대한 복원력은 많은 기업에서 요구하는 사항이며, 경우에 따라 업계 규정에도 필요합니다. 

Azure Event Hubs는 이미 개별 머신의 치명적인 오류에 대한 위험을 분산하거나 데이터 센터 내의 여러 오류 도메인에 걸쳐 있는 클러스터 간에 전체 랙을 분산하고, 그러한 오류가 발생하는 경우 서비스가 보장되는 서비스 수준 내에서 일반적으로 눈에 띄게 중단되지 않고 계속 작동하도록 투명한 오류 탐지 및 장애 조치(failover) 메커니즘을 구현합니다. [가용성 영역](../availability-zones/az-overview.md)에 대해 설정된 옵션을 사용하여 Event Hubs 네임스페이스를 만든 경우 중단 위험은 물리적으로 분리된 세 가지 기능에 추가로 분산되며, 서비스에는 전체 기능의 완전히 치명적인 손실에 즉각적으로 대처하기에 충분한 용량이 예약되어 있습니다. 

가용성 영역을 지원하는 모든 활성 Azure Event Hubs 클러스터 모델은 심각한 하드웨어 오류 및 전체 데이터 센터 시설의 심각한 손실에 대한 복원력을 제공합니다. 그러나 이러한 측정값이 충분히 방어될 수 없는 광범위한 물리적 소멸이 있는 경우에도 매우 많은 상황이 발생할 수 있습니다. 

Event Hubs 지리적 재해 복구 기능은 이러한 규모의 재해로부터 쉽게 복구하고, 애플리케이션 구성을 변경하지 않고도 실패한 Azure 지역을 중단하는 데 사용할 수 있도록 설계되었습니다. Azure 지역 중단에 일반적으로 여러 서비스가 포함되며, 이 기능은 주로 복합 애플리케이션 구성의 무결성 유지 지원을 목표로 합니다.  

지리적 재해 복구 기능을 사용하면 기본 네임스페이스와 보조 네임스페이스가 쌍을 이루는 경우 네임스페이스의 전체 구성(Event Hubs, 소비자 그룹, 설정)이 기본 네임스페이스에서 보조 네임스페이스로 지속적으로 복제되고, 언제든지 기본 데이터베이스에서 보조 데이터베이스로 장애 조치를 한 번만 시작할 수 있습니다. 장애 조치 이동은 네임스페이스에 대해 선택한 별칭 이름을 보조 네임스페이스로 다시 가리킨 다음 페어링을 끊습니다. 장애 조치가 시작된 후에는 거의 즉시 이루어집니다. 

> [!IMPORTANT]
> - 이 기능을 사용하면 동일한 구성을 사용하여 작업을 연속적으로 즉시 수행할 수 있지만 **이벤트 데이터가 복제되지 않습니다**. 재해로 인해 모든 영역이 손실된 경우가 아니면 장애 조치 후 기본 Event Hub에 유지되는 이벤트 데이터를 복구할 수 있으며, 액세스를 복원하면 기록 이벤트를 가져올 수 있습니다. 이벤트 데이터를 복제하고 활성/활성 구성에서 해당 네임스페이스를 운영하여 중단 및 재해를 처리할 수 있도록 하려면 이러한 지리적 재해 복구 기능 세트를 사용하지 않고 [복제 지침](event-hubs-federation-overview.md)을 따르세요.  
> - 기본 네임스페이스의 엔터티에 대한 Azure AD(Azure Active Directory) RBAC(역할 기반 액세스 제어) 할당은 보조 네임스페이스에 복제되지 않습니다. 보조 네임스페이스에서 수동으로 역할 할당을 만들어 액세스를 보호합니다. 

## <a name="outages-and-disasters"></a>중단 및 재해

"중단" 및 "재해" 간의 차이점을 참고하는 것이 중요합니다. **중단** 은 Azure Event Hubs를 일시적으로 사용할 수 없는 것으로, 메시징 저장소와 같은 서비스의 일부 구성 요소나 전체 데이터 센터에 영향을 줄 수 있습니다. 그렇지만 문제가 해결되면 Event Hubs를 다시 사용할 수 있게 됩니다. 일반적으로 중단으로 인해 메시지나 기타 데이터가 손실되지는 않습니다. 이러한 중단의 예로 데이터 센터의 전원 장애를 들 수 있습니다. 일부 가동 중단은 일시적인 네트워크 문제로 인해 짧은 연결 오류가 발생합니다. 

*재해* 는 Event Hubs 클러스터, Azure 지역 또는 데이터 센터의 영구적이거나 장기적인 손실을 의미합니다. 지역 또는 데이터 센터는 다시 사용할 수 있게 될 수도, 그렇지 않을 수도 있고 몇 시간 또는 며칠 동안 중지될 수도 있습니다. 재해의 예로는 화재, 홍수 또는 지진이 있습니다. 영구적 재해는 일부 메시지, 이벤트 또는 기타 데이터의 손실을 발생시킬 수 있습니다. 그러나 대부분의 경우에는 데이터 손실이 없으며 메시지는 데이터 센터를 백업하면 복구할 수 있습니다.

Azure Event Hubs의 지역 재해 복구 기능은 재해 복구 솔루션입니다. 이 문서에 설명된 개념 및 워크플로는 재해 시나리오에 적용되며 일시적이거나 임시적인 중단이 아닙니다. Microsoft Azure의 재해 복구에 대해 자세히 알아보려면 [이 문서](/azure/architecture/resiliency/disaster-recovery-azure-applications)를 참조하세요.

## <a name="basic-concepts-and-terms"></a>기본 개념 및 용어

재해 복구 기능은 메타데이터 재해 복구를 구현하며 주 및 보조 재해 복구 네임스페이스를 사용합니다. 

지리적 재해 복구 기능은 [표준 및 프리미엄 및 전용 SKU](https://azure.microsoft.com/pricing/details/event-hubs/)에만 사용할 수 있습니다. 별칭을 통해 연결이 수행되므로 연결 문자열을 변경할 필요가 없습니다.

이 문서에서는 다음 용어가 사용됩니다.

-  *별칭*: 설정하는 재해 복구 구성의 이름입니다. 별칭은 하나의 안정적인 FQDN(정규화된 도메인 이름) 연결 문자열을 제공합니다. 애플리케이션은 이 별칭 연결 문자열을 사용하여 네임스페이스에 연결합니다. 

-  *주/보조 네임스페이스*: 별칭에 해당하는 네임스페이스입니다. 기본 네임스페이스는 “활성”이며 메시지를 수신합니다(기존 또는 새 네임스페이스일 수 있음). 보조 네임스페이스는 “수동”이며 메시지를 수신하지 않습니다. 둘 간의 메타데이터가 동기화되므로 둘 다 애플리케이션 코드 또는 연결 문자열을 변경하지 않고 메시지를 원활하게 수락할 수 있습니다. 활성 네임스페이스만 메시지를 수신하는지 확인하려면 별칭을 사용해야 합니다.
-  *메타데이터*: Event Hubs 및 소비자 그룹과 같은 엔터티 및 네임스페이스와 연결된 서비스의 해당 속성입니다. 엔터티 및 해당 설정만이 자동으로 복제됩니다. 메시지 및 이벤트는 복제되지 않습니다. 
-  *장애 조치(Failover)* : 보조 네임스페이스를 활성화하는 프로세스입니다.

## <a name="supported-namespace-pairs"></a>지원되는 네임스페이스 쌍
기본 및 보조 네임스페이스의 다음과 같은 조합이 지원됩니다.  

| 기본 네임스페이스 계층 | 허용되는 보조 네임스페이스 계층 |
| ----------------- | -------------------- |
| Standard | 표준, 전용 | 
| Premium | Premium | 
| 전용 | 전용 | 

> [!NOTE]
> 동일한 전용 클러스터에 있는 네임스페이스는 쌍으로 연결할 수 없습니다. 별도의 클러스터에 있는 네임스페이스는 쌍으로 연결할 수 있습니다. 

## <a name="setup-and-failover-flow"></a>흐름 설정 및 장애 조치

다음 섹션은 장애 조치 프로세스의 개요이며 초기 장애 조치를 설정하는 방법을 설명합니다. 

:::image type="content" source="./media/event-hubs-geo-dr/geo1.png" alt-text="장애 조치(failover) 프로세스 개요를 보여주는 이미지":::


### <a name="setup"></a>설치 프로그램

먼저 기존의 기본 네임스페이스 및 새로운 보조 네임스페이스를 만들거나 사용한 다음 둘을 쌍으로 연결합니다. 이 페어링은 연결하는 데 사용할 수 있는 별칭을 제공합니다. 별칭을 사용하므로 연결 문자열을 변경할 필요가 없습니다. 새 네임스페이스에만 장애 조치(Failover) 페어링에 추가할 수 있습니다. 

1. 기본 네임스페이스를 만듭니다.
1. 다른 지역에 보조 네임스페이스를 만듭니다. 이 단계는 선택 사항입니다. 다음 단계에서 페어링을 만드는 동안 보조 네임스페이스를 만들 수 있습니다. 
1. Azure Portal에서 기본 네임스페이스로 이동합니다.
1. 왼쪽 메뉴에서 **지역 복구** 를 선택하고 도구 모음에서 **페어링 시작** 을 선택합니다. 

    :::image type="content" source="./media/event-hubs-geo-dr/primary-namspace-initiate-pairing-button.png" alt-text="기본 네임스페이스에서 페어링 시작":::    
1. **페어링 시작** 페이지에서 다음 단계를 따릅니다.
    1. 기존 보조 네임스페이스를 선택하거나 다른 지역에서 하나를 만듭니다. 이 예제에서는 기존 네임스페이스를 선택합니다.  
    1. **별칭** 에 Geo-DR(지리적 재해 복구) 페어링의 별칭을 입력합니다. 
    1. 그런 다음 **만들기** 를 선택합니다. 

    :::image type="content" source="./media/event-hubs-geo-dr/initiate-pairing-page.png" alt-text="보조 네임스페이스 선택":::        
1. **Geo-DR(지리적 재해 복구) 별칭** 페이지가 표시됩니다. 왼쪽 메뉴에서 **지역 복구** 를 선택하여 기본 네임스페이스에서 이 페이지로 이동할 수도 있습니다.

    :::image type="content" source="./media/event-hubs-geo-dr/geo-dr-alias-page.png" alt-text="Geo-DR(지리적 재해 복구) 별칭 페이지":::    
1. **Geo-DR(지리적 재해 복구) 별칭** 페이지의 왼쪽 메뉴에서 **공유 액세스 정책** 을 선택하여 별칭에 대한 기본 연결 문자열에 액세스합니다. 기본/보조 네임스페이스에 연결 문자열을 직접 사용하는 대신 이 연결 문자열을 사용합니다. 
1. 이 **개요** 페이지에서 다음 작업을 수행할 수 있습니다. 
    1. 기본 네임스페이스와 보조 네임스페이스 간의 연결을 끊습니다. 도구 모음에서 **연결 끊기** 를 선택합니다. 
    1. 보조 네임스페이스에 수동으로 장애 조치합니다. 도구 모음에서 **장애 조치(failover)** 를 선택합니다. 
    
        > [!WARNING]
        > 장애 조치는 보조 네임스페이스를 활성화하고 지리적 재해 복구 페어링에서 기본 네임스페이스를 제거합니다. 새 지리적 재해 복구 쌍을 포함하는 다른 네임스페이스를 만듭니다. 

마지막으로 장애 조치가 필요한 경우 감지할 몇 가지 모니터링을 추가해야 합니다. 대부분의 경우 서비스는 대규모 에코시스템의 일부입니다. 따라서 장애 조치가 주로 나머지 하위 시스템 또는 인프라와 동기화되어 수행되어야 할 때가 많으므로 자동 장애 조치는 거의 불가능합니다.

### <a name="example"></a>예제

이 시나리오의 예로 메시지 또는 이벤트를 내보내는 POS(Point of Sale) 솔루션을 고려합니다. Event Hubs는 일부 매핑 또는 다시 포맷 솔루션에 해당 이벤트를 전달합니다. 그런 다음 추가 처리를 위해 다른 시스템에 매핑된 데이터를 전달합니다. 해당 시점에 이러한 시스템은 모두 같은 Azure 지역에서 호스팅될 수 있습니다. 장애 조치를 수행할 시기 및 부분은 인프라의 데이터 흐름에 따라 결정됩니다. 

모니터링 시스템 또는 사용자 빌드 모니터링 솔루션을 사용하여 장애 조치를 자동화할 수 있습니다. 그러나 이러한 자동화에는 추가 계획 및 작업이 필요합니다. 이 부분은 이 문서에서 다루지 않습니다.

### <a name="failover-flow"></a>흐름 장애 조치(Failover)

장애 조치를 시작하는 경우 다음과 같은 두 단계가 필요합니다.

1. 작동 중단이 발생하면 다시 장애 조치(Failover)할 수 있어야 합니다. 따라서 다른 수동 네임스페이스를 설정하고 페어링을 업데이트합니다. 

2. 사용할 수 있으면 이전 기본 네임스페이스에서 메시지를 끌어옵니다. 그 후에 지역 복구 설정의 외부에서 일반 메시징에 해당 네임스페이스를 사용하거나 이전 기본 네임스페이스를 삭제합니다.

> [!NOTE]
> 실패 전달 의미 체계만이 지원됩니다. 이 시나리오에서는 새 네임스페이스를 사용하여 장애 조치하고 다시 페어링합니다. 예를 들어 SQL 클러스터에서 장애 복구는 지원되지 않습니다. 

:::image type="content" source="./media/event-hubs-geo-dr/geo2.png" alt-text="장애 조치(failover) 흐름을 보여주는 이미지":::

## <a name="management"></a>관리

예를 들어 초기 설정 작업 중에 잘못된 지역을 페어링하는 실수가 발생한 경우 언제든지 두 네임스페이스의 페어링을 해제할 수 있습니다. 일반 네임스페이스와 페어링된 네임스페이스를 사용하려는 경우 별칭을 삭제합니다.

## <a name="considerations"></a>고려 사항

다음 고려 사항에 유의하세요.

1. 기본적으로 Event Hubs 지리적 재해 복구는 데이터를 복제하지 않으므로 보조 이벤트 허브에서 기본 이벤트 허브의 이전 오프셋 값을 다시 사용할 수 없습니다. 다음 방법 중 하나를 사용하여 이벤트 수신기를 다시 시작하는 것이 좋습니다.

   - *EventPosition.FromStart()* - 보조 이벤트 허브의 모든 데이터를 읽으려는 경우
   - *EventPosition.FromEnd()* - 보조 이벤트 허브에 연결된 시간부터 모든 새 데이터를 읽으려는 경우
   - *EventPosition.FromEnqueuedTime(dateTime)* - 지정된 날짜 및 시간에서 시작하여 보조 이벤트 허브에서 받은 모든 데이터를 읽으려는 경우

2. 장애 조치 계획에서 시간 요소를 고려해야 합니다. 예를 들어 15~20분이 넘게 연결이 손실된 경우 장애 조치를 시작하기로 결정할 수 있습니다. 
 
3. 데이터가 복제되지 않으면 현재 활성 세션이 복제되지 않습니다. 또한 중복 검색 및 예약된 메시지가 작동하지 않을 수 있습니다. 새 세션, 예약된 메시지 및 새 중복이 작동합니다. 

4. 복잡한 분산 인프라를 장애 조치하려면 한 번 이상 [예행 연습](/azure/architecture/reliability/disaster-recovery#disaster-recovery-plan)을 수행해야 합니다. 

5. 엔터티를 동기화하는 데 분당 약 50~100개의 엔터티를 처리하므로 다소 시간이 걸릴 수 있습니다.

## <a name="availability-zones"></a>가용성 영역 
Event Hubs는 Azure 지역 내에서 오류가 없는 위치를 제공하는 [가용성 영역](../availability-zones/az-overview.md)을 지원합니다. 가용성 영역 지원은 [가용성 영역이 있는 Azure 지역](../availability-zones/az-region.md#azure-regions-with-availability-zones)에서만 사용할 수 있습니다. 메타데이터와 데이터(이벤트)가 모두 가용성 영역의 데이터 센터에 복제됩니다. 

네임스페이스를 만들 때 가용성 영역이 있는 지역을 선택하면 다음과 같은 강조 표시된 메시지가 표시됩니다. 

:::image type="content" source="./media/event-hubs-geo-dr/eh-az.png" alt-text="가용성 영역을 가진 지역이 있는 네임스페이스 만들기 페이지를 보여주는 이미지":::

## <a name="private-endpoints"></a>프라이빗 엔드포인트
이 섹션에서는 프라이빗 엔드포인트를 사용하는 네임스페이스에서 지리적 재해 복구를 사용할 때 추가로 고려해야 할 사항에 대해 설명합니다. 일반적인 Event Hubs에서 전용 엔드포인트를 사용하는 방법에 대한 자세한 내용은 [프라이빗 엔드포인트 구성](private-link-service.md)을 참조하세요.

### <a name="new-pairings"></a>새 페어링
프라이빗 엔드포인트가 없는 보조 네임스페이스와 프라이빗 엔드포인트가 있는 기본 네임스페이스 간에 페어링을 만들려고 시도하면 페어링이 실패합니다. 기본 및 보조 네임스페이스에 모두 프라이빗 엔드포인트가 있는 경우에만 페어링이 성공합니다. 기본 및 보조 네임스페이스와 프라이빗 엔드포인트가 생성되는 가상 네트워크에서 동일한 구성을 사용하는 것이 좋습니다.  

> [!NOTE]
> 프라이빗 엔드포인트가 있는 기본 네임스페이스를 보조 네임스페이스와 페어링하려고 시도하면 유효성 검사 프로세스에서는 보조 네임스페이스에 프라이빗 엔드포인트가 있는지만 검사합니다. 엔드포인트가 장애 조치(failover) 후 작동하는지 또는 작동할지는 검사하지 않습니다. 장애 조치 후 프라이빗 엔드포인트가 있는 보조 네임스페이스가 예상대로 작동하는지 검사하는 것은 사용자의 책임입니다.
>
> 기본 및 보조 네임스페이스에서 프라이빗 엔드포인트 구성이 동일한지 테스트하려면 요청(예: [Event Hub 가져오기](/rest/api/eventhub/get-event-hub))을 가상 네트워크 외부에서 보조 네임스페이스로 전송하고 서비스에서 오류 메시지가 수신되는지 확인합니다.

### <a name="existing-pairings"></a>기존 페어링
기본 네임스페이스와 보조 네임스페이스 간의 페어링이 이미 있는 경우에는 기본 네임스페이스에서 프라이빗 엔드포인트 만들기가 실패합니다. 이 문제를 해결하려면 먼저 보조 네임스페이스에 프라이빗 엔드포인트를 만든 다음, 기본 네임스페이스에 프라이빗 엔드포인트를 만듭니다.

> [!NOTE]
> 보조 네임스페이스에는 읽기 전용 액세스가 허용되며 프라이빗 엔드포인트 구성에 대한 업데이트가 허용됩니다. 

### <a name="recommended-configuration"></a>권장 구성
애플리케이션 및 Event Hubs 네임스페이스에 대한 재해 복구 구성을 만들 때 애플리케이션의 기본 및 보조 인스턴스를 모두 호스트하는 가상 네트워크에 대하여 기본 및 보조 Event Hubs 네임스페이스 둘 다에 대한 프라이빗 엔드포인트를 만들어야 합니다. 

두 개의 가상 네트워크인 VNET-1, VNET-2 및 기본 및 보조 네임스페이스인 EventHubs-Namespace1-Primary, EventHubs-Namespace2-Secondary가 있다고 가정해보겠습니다. 다음 단계를 수행해야 합니다. 

- EventHubs-Namespace1-Primary에서 VNET-1과 VNET-2의 서브넷을 사용하는 두 개의 프라이빗 엔드포인트를 만듭니다.
- EventHubs-Namespace2-Secondary에서 VNET-1과 VNET-2의 동일한 서브넷을 사용하는 두 개의 프라이빗 엔드포인트를 만듭니다. 

![프라이빗 엔드포인트 및 가상 네트워크](./media/event-hubs-geo-dr/private-endpoints-virtual-networks.png)

이 방법의 장점은 장애 조치(failover)가 Event Hubs 네임스페이스와 무관한 애플리케이션 계층에서 발생할 수 있다는 것입니다. 다음과 같은 시나리오를 고려해 보세요. 

**애플리케이션 전용 장애 조치:** 여기서 애플리케이션은 VNET-1에는 없지만 VNET-2로 이동합니다. 두 프라이빗 엔드포인트 모두 기본 및 보조 네임스페이스에 대해 VNET-1과 VNET-2 모두에 구성되어 있으므로 애플리케이션은 작동합니다. 

**Event Hubs 네임스페이스 전용 장애 조치**: 여기서도 역시 두 프라이빗 엔드포인트는 기본 및 보조 네임스페이스에 대해 두 가상 네트워크 모두에서 구성되므로 애플리케이션은 작동합니다. 

> [!NOTE]
> 가상 네트워크의 지리적 재해 복구에 대한 지침은 [Virtual Network - 비즈니스 연속성](../virtual-network/virtual-network-disaster-recovery-guidance.md)을 참조하세요.

## <a name="role-based-access-control"></a>역할 기반 액세스 제어
기본 네임스페이스의 엔터티에 대한 Azure AD(Azure Active Directory) RBAC(역할 기반 액세스 제어) 할당은 보조 네임스페이스에 복제되지 않습니다. 보조 네임스페이스에서 수동으로 역할 할당을 만들어 액세스를 보호합니다.
 
## <a name="next-steps"></a>다음 단계
다음 샘플 또는 참조 설명서를 검토합니다. 
- [.NET GeoDR 샘플](https://github.com/Azure/azure-event-hubs/tree/master/samples/Management/DotNet/GeoDRClient) 
- [Java GeoDR 샘플](https://github.com/Azure-Samples/eventhub-java-manage-event-hub-geo-disaster-recovery)
- [.NET - Azure.Messaging.EventHubs 샘플](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs/samples)
- [.NET - Microsoft.Azure.EventHubs 샘플](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet)
- [Java - azure-messaging-eventhubs 샘플](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/eventhubs/azure-messaging-eventhubs/src/samples/java/com/azure/messaging/eventhubs)
- [Java - azure-eventhubs 샘플](https://github.com/Azure/azure-event-hubs/tree/master/samples/Java)
- [Python 샘플](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventhub/azure-eventhub/samples)
- [JavaScript 샘플](https://github.com/Azure/azure-sdk-for-js/tree/main/sdk/eventhub/event-hubs/samples/v5/javascript)
- [TypeScript 샘플](https://github.com/Azure/azure-sdk-for-js/tree/main/sdk/eventhub/event-hubs/samples/v5/typescript)
- [REST API 참조](/rest/api/eventhub/)

[2]: ./media/event-hubs-geo-dr/geo2.png
