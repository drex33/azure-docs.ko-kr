---
title: 유지 관리 기간
description: Azure SQL Database 및 Managed Instance 유지 관리 기간을 구성하는 방법을 이해합니다.
services: sql-database
ms.service: sql-db-mi
ms.subservice: service-overview
ms.topic: conceptual
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.reviewer: mathoma
ms.custom: references_regions
ms.date: 10/05/2021
ms.openlocfilehash: ca8ed4fa480bd394196f4ca5b37c52bcc06e41c1
ms.sourcegitcommit: 57b7356981803f933cbf75e2d5285db73383947f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/05/2021
ms.locfileid: "129546174"
---
# <a name="maintenance-window-preview"></a>유지 관리 기간(미리 보기)
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

유지 관리 기간 기능을 사용하여 [Azure SQL Database](sql-database-paas-overview.md) 및 [Azure SQL Managed Instance](../managed-instance/sql-managed-instance-paas-overview.md) 리소스에 대한 유지 관리 일정을 구성하여 중대한 유지 관리 이벤트를 미리 예측하고 워크로드 중단을 줄일 수 있습니다. 

> [!Note]
> 유지 관리 기간 기능은 업그레이드 또는 예약된 유지 관리에 따른 예정된 영향으로부터만 보호합니다. 모든 장애 조치 원인으로부터 보호하지는 않습니다. 유지 관리 기간 외에 짧은 연결 중단이 발생할 수 있는 예외에는 데이터베이스 서비스 수준 목표 변경과 같은 이벤트로 인한 하드웨어 오류, 클러스터 부하 분산 및 데이터베이스 재구성이 포함됩니다. 

## <a name="overview"></a>개요

Azure는 SQL Database 및 SQL Managed Instance 리소스의 [계획된 유지 관리](planned-maintenance.md)를 정기적으로 수행합니다. Azure SQL 유지 관리 이벤트 중에는 데이터베이스를 완전히 사용할 수 있지만 [SQL Database](https://azure.microsoft.com/support/legal/sla/azure-sql-database) 및 [SQL Managed Instance](https://azure.microsoft.com/support/legal/sla/azure-sql-sql-managed-instance)에 대한 각 가용성 SLA 내에서 간단히 재구성될 수 있습니다.

유지 관리 기간은 데이터베이스 또는 인스턴스 재구성에 대해 복원력이 없으며 계획된 유지 관리 이벤트로 인해 발생하는 짧은 연결 중단을 수용할 수 없는 프로덕션 작업을 위해 고안되었습니다. 원하는 유지 관리 기간을 선택하여 사용량이 많은 업무 시간 외에 발생하는 계획된 유지 관리의 영향을 최소화할 수 있습니다. 복원력 있는 워크로드와 비프로덕션 워크로드는 Azure SQL의 기본 유지 관리 정책을 사용합니다.

유지 관리 기간은 만들 때 구성하거나 기존 Azure SQL 리소스에 대해 구성할 수 있습니다. 이 기간은 Azure Portal, PowerShell, CLI 또는 Azure API를 사용하여 구성할 수 있습니다.

> [!Important]
> 유지 관리 기간을 구성하는 작업은 Azure SQL 리소스의 서비스 계층을 변경하는 것과 유사한 장기 실행 비동기 작업입니다. 리소스는 작업이 끝날 때 발생하는 짧은 재구성을 제외하고 작업 중에 사용할 수 있으며 일반적으로 중단된 장기 실행 트랜잭션의 경우에도 최대 8초 동안 지속됩니다. 재구성의 영향을 최소화하려면 사용량이 많은 시간 외에 작업을 수행해야 합니다.

### <a name="gain-more-predictability-with-maintenance-window"></a>유지 관리 기간을 사용하여 예측 가능성 향상

기본적으로 Azure SQL 유지 관리 정책은 사용량이 많은 일반적인 업무 시간 동안 중단을 방지하기 위해 **매일 현지 시간으로 오전 8시부터 오후 5시까지의** 기간 동안 중대한 영향을 미치는 업데이트를 차단합니다. 현지 시간은 리소스를 호스트하는 [Azure 지역](https://azure.microsoft.com/global-infrastructure/geographies/)의 위치에 따라 결정되며 현지 표준 시간대 정의에 따라 일광 절약 시간을 준수할 수도 있습니다. 

두 개의 유지 관리 기간을 추가로 선택하여 Azure SQL 리소스에 적합한 시간으로 유지 관리 업데이트를 추가로 조정할 수 있습니다.
 
* 평일 기간, 월요일 - 목요일 현지 시간으로 오후 10시 ~ 오전 6시
* 주말 기간, 금요일 - 일요일 현지 시간으로 오후 10시 ~ 오전 6시

유지 관리 기간을 선택하고 서비스 구성이 완료되면 선택한 기간 동안에만 계획된 유지 관리가 수행됩니다. 유지 관리 이벤트는 일반적으로 단일 기간 내에 완료되지만, 일부는 둘 이상의 인접한 기간에 걸쳐 있을 수 있습니다.   

> [!Important]
> 중요 보안 패치를 적용하는 것과 같이 작업을 연기할 경우 심각한 영향을 야기할 수 있는 드문 경우에는 구성된 유지 관리 기간이 일시적으로 재정의될 수 있습니다. 

### <a name="cost-and-eligibility"></a>비용 및 자격

적합한 모든 [제품 유형](https://azure.microsoft.com/support/legal/offer-details/)(종량제, CSP(클라우드 솔루션 공급자), Microsoft 기업계약 또는 Microsoft 고객 계약)에 대해서는 무료로 유지 관리 기간을 구성 및 사용할 수 있습니다.

> [!Note]
> Azure 제안은 사용자가 소유한 Azure 구독의 유형을 말합니다. 예를 들어 [종량제 요금을 사용하는 구독](https://azure.microsoft.com/offers/ms-azr-0003p/), [Azure in Open](https://azure.microsoft.com/offers/ms-azr-0111p/) 및 [Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p/)는 모두 Azure 제품입니다. 제품 또는 플랜마다 조건 및 혜택이 다릅니다. 제품 또는 플랜이 구독의 개요에 표시됩니다. 구독을 다른 제품으로 전환하는 방법에 대한 자세한 내용은 [다른 제품으로 Azure 구독 변경](../../cost-management-billing/manage/switch-azure-offer.md)을 참조하세요.

## <a name="advance-notifications"></a>고급 알림

유지 관리 알림은 24시간 전에, 유지 관리 시간에, 유지 관리가 완료될 때 Azure SQL Database에 대한 예정된 유지 관리 이벤트를 경고하도록 구성할 수 있습니다. 자세한 내용은 [미리 알림](advance-notifications.md)을 참조하세요.

## <a name="availability"></a>가용성

### <a name="supported-service-level-objectives"></a>지원되는 서비스 수준 목표

기본 이외의 유지 관리 기간은 다음을 **제외한** 모든 SLO에 대해 선택할 수 있습니다.
* 하이퍼스케일 
* 인스턴스 풀
* 레거시 Gen4 vCore
* 기본, S0 및 S1 
* DC, Fsv2, M 시리즈

### <a name="azure-region-support"></a>Azure 지역 지원

기본 이외의 유지 관리 기간은 현재 다음 지역에서 선택할 수 있습니다.

| Azure 지역 | SQL Managed Instance | SQL Database | [Azure 가용성 영역에서](high-availability-sla.md) SQL Database | 
|:---|:---|:---|:---|
| 오스트레일리아 중부 1 | Yes | | |
| 오스트레일리아 중부 2 | Yes | | |
| 오스트레일리아 동부 | 예 | 예 | 예 |
| 오스트레일리아 남동부 | 예 | 예 | |
| 브라질 남부 | 예 | 예 |  |
| 캐나다 중부 | 예 | 예 | 예 |
| 캐나다 동부 | 예 | 예 | |
| 인도 중부 | 예 | 예 | |
| 미국 중부 | 예 | 예 | 예 |
| 중국 동부 2 |예 | 예 ||
| 중국 북부 2 |예|예 ||
| 미국 동부 | 예 | 예 | 예 |
| 미국 동부 2 | 예 | 예 | 예 |
| 동아시아 | 예 | 예 | |
| 프랑스 중부 | 예 | 예 | |
| 프랑스 남부 | 예 | 예 | |
| 독일 중서부 | 예 | 예 |  |
| 독일 북부 | 예 |  |  |
| 일본 동부 | 예 | 예 | 예 |
| 일본 서부 | 예 | 예 | |
| 한국 중부 | 예 | | |
| 한국 남부 | Yes | | |
| 미국 중북부 | 예 | 예 | |
| 북유럽 | 예 | 예 | 예 |
| 남아프리카 북부 | 예 | | | 
| 남아프리카 공화국 서부 | 예 | | | 
| 미국 중남부 | 예 | 예 | 예 |
| 인도 남부 | 예 | 예 | |
| 동남아시아 | 예 | 예 | 예 |
| 스위스 북부 | 예 | 예 | |
| 스위스 서부 | Yes | | |
| 아랍에미리트 중부 | Yes | | |
| 아랍에미리트 북부 | 예 | | |
| 영국 남부 | 예 | 예 | 예 |
| 영국 서부 | 예 | 예 | |
| 미국 중서부 | 예 | 예 | |
| 서유럽 | 예 | 예 | 예 |
| 인도 서부 | Yes | | |
| 미국 서부 | 예 | 예 |  |
| 미국 서부 2 | 예 | 예 | 예 |
| | | | | 

## <a name="gateway-maintenance-for-azure-sql-database"></a>Azure SQL Database에 대한 게이트웨이 유지 관리

유지 관리 기간을 최대한 활용하려면 클라이언트 애플리케이션이 리디렉션 연결 정책을 사용하고 있는지 확인합니다. 리디렉션은 권장되는 연결 정책으로, 클라이언트가 데이터베이스를 호스트하는 노드에 직접 연결을 설정하여 대기 시간을 줄이고 처리량을 높입니다.  

* Azure SQL Database에서 프록시 연결 정책을 사용하는 모든 연결은 선택한 유지 관리 기간과 게이트웨이 노드 유지 관리 기간 모두에 의해 영향을 받을 수 있습니다. 그러나 권장되는 리디렉션 연결 정책을 사용하는 클라이언트 연결은 게이트웨이 노드 유지 관리 재구성의 영향을 받지 않습니다. 

* Azure SQL Managed Instance에서 게이트웨이 노드는 [가상 클러스터 내에서](../../azure-sql/managed-instance/connectivity-architecture-overview.md#virtual-cluster-connectivity-architecture) 호스트되고 관리되는 인스턴스와 동일한 유지 관리 기간이 적용되지만, 유지 관리 이벤트 중에 중단 횟수를 최소화하려면 연결 리디렉션 정책을 사용하는 것이 좋습니다.

Azure SQL Database의 클라이언트 연결 정책에 대한 자세한 내용은 [Azure SQL Database 연결 정책](../database/connectivity-architecture.md#connection-policy)을 참조하세요. 

azure SQL Managed Instance의 클라이언트 연결 정책에 대 한 자세한 내용은 [azure SQL Managed Instance 연결 유형](../../azure-sql/managed-instance/connection-types-overview.md)을 참조 하세요.

## <a name="considerations-for-azure-sql-managed-instance"></a>Azure SQL Managed Instance에 대한 고려 사항

Azure SQL Managed Instance는 고객의 가상 네트워크 서브넷 내에서 실행되는 격리된 전용 가상 머신 세트에서 호스트되는 서비스 구성 요소로 이루어집니다. 이러한 가상 머신은 여러 관리되는 인스턴스를 호스트할 수 있는 [가상 클러스터](../managed-instance/connectivity-architecture-overview.md#high-level-connectivity-architecture)를 형성합니다. 한 서브넷의 인스턴스에 구성된 유지 관리 기간은 서브넷 내의 가상 클러스터 수, 가상 클러스터 간의 인스턴스 배포 및 가상 클러스터 관리 작업에 영향을 줄 수 있습니다. 몇 가지 결과를 고려해야 할 수 있습니다.

### <a name="maintenance-window-configuration-is-long-running-operation"></a>유지 관리 기간 구성은 장기 실행 작업입니다. 
가상 클러스터에서 호스트되는 모든 인스턴스는 유지 관리 기간을 공유합니다. 기본적으로 모든 관리되는 인스턴스는 기본 유지 관리 기간을 사용하여 가상 클러스터에서 호스트됩니다. 관리되는 인스턴스를 만드는 동안 또는 그 이후에 관리되는 인스턴스에 대해 다른 유지 관리 기간을 지정하는 것은 해당 유지 관리 기간의 가상 클러스터에 배치해야 되는 것을 의미합니다. 서브넷에 이러한 가상 클러스터가 없는 경우 인스턴스를 수용하기 위해 먼저 새 가상 클러스터를 만들어야 합니다. 기존 가상 클러스터에서 추가 인스턴스를 수용하려면 클러스터 크기 조정이 필요할 수 있습니다. 두 작업 모두 관리되는 인스턴스에 대해 유지 관리 기간을 구성하는 기간에 영향을 줍니다.
관리되는 인스턴스에 대해 유지 관리 기간을 구성하는 데 필요한 예상 기간은 [인스턴스 관리 작업의 예상 기간](../managed-instance/management-operations-overview.md#duration)을 사용하여 계산할 수 있습니다.

> [!Important]
> 유지 관리 작업이 끝나면 짧은 재구성이 발생하며 일반적으로 중단된 장기 실행 트랜잭션의 경우에도 최대 8초 동안 지속됩니다. 재구성의 영향을 최소화하려면 사용량이 많은 시간 외에 작업을 예약해야 합니다.

### <a name="ip-address-space-requirements"></a>IP 주소 공간 요구 사항
[가상 클러스터 IP 주소 할당](../managed-instance/vnet-subnet-determine-size.md#determine-subnet-size)에 따라 서브넷의 새 가상 클러스터마다 추가 IP 주소가 필요합니다. 또한 기존의 관리되는 인스턴스에 대한 유지 관리 기간을 변경하려면 해당 서비스 계층에 대한 vCores 스케일링 시나리오에서와 마찬가지로 [임시 추가 IP 용량](../managed-instance/vnet-subnet-determine-size.md#update-scenarios)이 필요합니다.

### <a name="ip-address-change"></a>IP 주소 변경
유지 관리 기간을 구성하고 변경하면 서브넷의 IP 주소 범위 내에서 인스턴스의 IP 주소가 변경됩니다.

> [!Important]
>  IP 주소 변경 후에 NSG 및 방화벽 규칙이 데이터 트래픽을 차단하지 않는지 확인합니다. 

### <a name="serialization-of-virtual-cluster-management-operations"></a>가상 클러스터 관리 작업의 직렬화

서비스 업그레이드 및 가상 클러스터 크기 조정과 같이 가상 클러스터에 영향을 주는 작업(새 컴퓨팅 노드를 추가하거나 불필요한 컴퓨팅 노드 제거)은 직렬화됩니다. 즉, 새 가상 클러스터 관리 작업은 이전 작업이 완료될 때까지 시작할 수 없습니다. 지속적인 서비스 업그레이드 또는 유지 관리 작업이 완료되기 전에 유지 관리 기간이 닫히면 그 동안 제출된 다른 가상 클러스터 관리 작업은 다음 유지 관리 기간이 열리고 서비스 업그레이드 또는 유지 관리 작업이 완료될 때까지 보류됩니다. 유지 관리 작업이 가상 클러스터당 단일 기간보다 오래 걸리는 것은 일반적이지 않지만 매우 복잡한 유지 관리 작업의 경우 이러한 상황이 발생할 수 있습니다.

가상 클러스터 관리 작업의 직렬화는 기본 유지 관리 정책에도 적용되는 일반적인 동작입니다. 유지 관리 기간 일정을 구성하면 인접한 두 기간 사이의 기간은 며칠일 수 있습니다. 유지 관리 작업이 두 개의 기간에 걸쳐 있는 경우 제출된 작업이 며칠 동안 보류될 수도 있습니다. 매우 드문 경우지만 이 기간 동안 새 인스턴스 만들기 또는 기존 인스턴스 크기 조정(추가 컴퓨팅 노드가 필요한 경우)이 차단될 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [유지 관리 기간 구성](maintenance-window-configure.md)
* [고급 알림](advance-notifications.md)

## <a name="learn-more"></a>자세한 정보

* [유지 관리 기간 FAQ](maintenance-window-faq.yml)
* [Azure SQL Database](sql-database-paas-overview.md) 
* [SQL Managed Instance](../managed-instance/sql-managed-instance-paas-overview.md)
* [Azure SQL Database 및 Azure SQL Managed Instance에서 유지 관리 이벤트 계획](planned-maintenance.md)
