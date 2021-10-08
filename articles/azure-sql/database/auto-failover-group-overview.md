---
title: 자동 장애 조치 그룹
titleSuffix: Azure SQL Database & SQL Managed Instance
description: 자동 장애 조치(failover) 그룹을 사용하면 관리되는 인스턴스의 모든 데이터베이스 또는 서버에서 데이터베이스 그룹의 복제 및 자동/조정된 장애 조치를 관리할 수 있습니다.
services: sql-database
ms.service: sql-db-mi
ms.subservice: high-availability
ms.custom: sqldbrb=2
ms.topic: conceptual
author: BustosMSFT
ms.author: robustos
ms.reviewer: mathoma
ms.date: 10/06/2021
ms.openlocfilehash: a2b8498b0ff4eab174c0ae77bd29d1e562db508a
ms.sourcegitcommit: bee590555f671df96179665ecf9380c624c3a072
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/07/2021
ms.locfileid: "129667652"
---
# <a name="use-auto-failover-groups-to-enable-transparent-and-coordinated-failover-of-multiple-databases"></a>자동 장애 조치(failover) 그룹을 통해 여러 데이터베이스의 투명하고 조정된 장애 조치(failover)를 사용할 수 있습니다.
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

자동 장애 조치 그룹 기능을 사용하면 서버의 데이터베이스 그룹이나 관리되는 인스턴스의 모든 데이터베이스에서 다른 지역으로의 복제 및 장애 조치를 관리할 수 ​​있습니다. 대규모 지역 복제 데이터베이스의 배포 및 관리를 단순화하도록 디자인되었으며 기존 [활성 지역 복제](active-geo-replication-overview.md) 기능을 기반으로 하는 선언적 추상화입니다. 수동으로 장애 조치를 시작하거나, 사용자 정의 정책에 따라 Azure 서비스에 위임할 수 있습니다. 사용자 정의 정책 옵션을 사용하면 치명적인 오류 또는 계획되지 않은 다른 이벤트가 발생하여 주 지역에서 SQL Database 또는 SQL Managed Instance의 가용성이 완전히 또는 부분적으로 상실될 경우 보조 지역에서 여러 관련 데이터베이스를 자동으로 복구할 수 있습니다. 장애 조치 그룹에는 일반적으로 같은 애플리케이션에서 사용하는 하나 이상의 데이터베이스가 포함될 수 있습니다. 또한 읽을 수 있는 보조 데이터베이스를 사용하여 읽기 전용 쿼리 워크로드를 오프로드할 수 있습니다. 자동 장애 조치 그룹에 여러 데이터베이스가 포함되기 때문에 주 서버에서 이러한 데이터베이스를 구성해야 합니다. 자동 장애 조치 그룹은 그룹의 모든 데이터베이스를 다른 지역에 있는 하나의 보조 서버 또는 인스턴스로만 복제할 수 있도록 지원합니다.

>[!NOTE]
>자동 장애 조치(failover) 그룹은 현재 [하이퍼스케일](service-tier-hyperscale.md) 서비스 계층에서 지원되지 않습니다. 하이퍼스케일 데이터베이스에 대한 지리적 장애 조치(failover)의 경우 [활성 지역 복제](active-geo-replication-overview.md)를 사용합니다.

> [!NOTE]
> 같거나 다른 지역에 여러 Azure SQL Database 보조 데이터베이스가 필요한 경우 [활성 지역 복제](active-geo-replication-overview.md)를 사용합니다.

자동 장애 조치(failover) 정책과 함께 자동 장애 조치(failover) 그룹을 사용하는 경우 그룹 내 데이터베이스 중 하나 이상에 영향을 미치는 중단이 발생하면 자동 장애 조치(failover)가 수행됩니다. 일반적으로 기본 제공되는 자동 고가용성 작업을 통해 자체적으로 완화될 수 없는 인시던트입니다. 장애 조치 트리거의 예로는 여러 컴퓨팅 노드에서 OS 커널 메모리 누수로 인해 SQL Database 테넌트 링 또는 제어 링이 다운되어 발생하는 인시던트나 일상적인 하드웨어 해제 중에 잘못된 네트워크 케이블이 끊어져 하나 이상의 테넌트 링이 다운되어 발생하는 인시던트가 있습니다.  자세한 내용은 [SQL Database 고가용성](high-availability-sla.md)을 참조하세요.

또한 자동 장애 조치 그룹은 장애 조치하는 동안 변경되지 않는 읽기-쓰기 및 읽기 전용 수신기 끝점을 제공합니다. 수동 또는 자동 장애 조치 활성화를 사용하는지 여부에 관계 없이 장애 조치는 그룹의 모든 보조 데이터베이스를 주 데이터베이스로 전환합니다. 데이터베이스 장애 조치(failover)가 완료되면 엔드포인트를 새 지역으로 리디렉션하도록 DNS 레코드가 자동으로 업데이트됩니다. 특정 RPO 및 RTO 데이터에 대해서는 [비즈니스 연속성 개요](business-continuity-high-availability-disaster-recover-hadr-overview.md)를 참조하세요.

자동 장애 조치 정책과 함께 자동 장애 조치 그룹을 사용하는 경우 서버 또는 관리되는 인스턴스의 데이터베이스에 영향을 미치는 중단이 발생하면 자동 장애 조치가 수행됩니다. 다음을 사용하여 자동 장애 조치(failover) 그룹을 관리할 수 있습니다.

- [Azure Portal](geo-distributed-application-configure-tutorial.md)
- [Azure CLI: 장애 조치 그룹](scripts/add-database-to-failover-group-cli.md)
- [PowerShell: 그룹 장애 조치](scripts/add-database-to-failover-group-powershell.md)
- [REST API: 장애 조치 그룹](/rest/api/sql/failovergroups)

장애 조치 후에는 새로운 주 데이터베이스에 데이터베이스와 서버 또는 인스턴스의 인증 요구 사항이 구성되어 있는지 확인합니다. 자세한 내용은 [재해 복구 후의 SQL Database 보안](active-geo-replication-security-configure.md)을 참조하세요.

실제 비즈니스 연속성을 달성하기 위해 데이터 센터 간에 데이터베이스 중복을 추가하는 것은 솔루션의 일부입니다. 치명적인 오류 후 애플리케이션(서비스) 엔드투엔드 복구에는 서비스 및 모든 종속성 서비스를 구성하는 모든 구성 요소의 복구가 필요합니다. 이러한 구성 요소의 예에는 클라이언트 소프트웨어(예: 사용자 지정 JavaScript를 사용한 브라우저), 웹 프런트 엔드, 스토리지 및 DNS가 포함됩니다. 모든 구성 요소는 동일한 오류에 탄력적이며 애플리케이션의 복구 시간 목표(RTO) 내에서 사용할 수 있는 것이 중요합니다. 따라서 모든 종속성 서비스를 확인하고 제고하는 보장 사항 및 기능을 이해해야 합니다. 그런 다음 의존하는 서비스 장애 조치 중 서비스 기능을 확인하도록 적절한 단계를 수행해야 합니다. 재해 복구를 위한 솔루션 설계에 대한 자세한 내용은 [활성 지역 복제를 사용하여 재해 복구를 위한 클라우드 솔루션 설계](designing-cloud-solutions-for-disaster-recovery.md)를 참조하세요.

## <a name="terminology-and-capabilities"></a>용어 및 기능

- **FOG(장애 조치 그룹)**

  장애 조치 그룹은 주 지역의 중단으로 인해 주 데이터베이스를 모두 또는 일부 사용할 수 없게 될 경우 하나의 단위로 다른 지역에 장애 조치될 수 있는 관리되는 인스턴스 내에 있거나 단일 서버에서 관리하는 명명된 데이터베이스 그룹입니다. SQL Managed Instance용으로 만든 경우 장애 조치 그룹에는 인스턴스의 모든 사용자 데이터베이스가 포함되므로 한 인스턴스에 하나의 장애 조치 그룹만 구성할 수 있습니다.
  
  > [!IMPORTANT]
  > 장애 조치 그룹의 이름은 `.database.windows.net` 도메인에서 전역적으로 고유해야 합니다.

- **서버**

     서버를 사용하면 서버의 사용자 데이터베이스를 일부 또는 모두 장애 조치 그룹에 배치할 수 있습니다. 또한 서버는 단일 서버에서 여러 개의 장애 조치 그룹을 지원합니다.

- **주**

  장애 조치 그룹의 주 데이터베이스를 호스트하는 서버 또는 관리되는 인스턴스입니다.

- **보조**

  장애 조치 그룹의 보조 데이터베이스를 호스트하는 서버 또는 관리되는 인스턴스입니다. 보조 서버는 주 서버와 동일한 지역에 있을 수 없습니다.

- **장애 조치(failover) 그룹에 단일 데이터베이스 추가**

  동일한 서버에 있는 여러 개의 단일 데이터베이스를 동일한 장애 조치 그룹에 포함할 수 있습니다. 장애 조치(failover) 그룹에 단일 데이터베이스를 추가하면 동일한 버전과 컴퓨팅 크기를 사용하는 보조 데이터베이스가 보조 서버에 자동으로 만들어집니다.  장애 조치(failover) 그룹을 만들 때 해당 서버를 지정했습니다. 보조 서버에 보조 데이터베이스가 이미 있는 데이터베이스를 추가하는 경우 해당 지역 복제 링크가 그룹에 상속됩니다. 장애 조치(failover) 그룹에 속하지 않는 서버에 보조 데이터베이스가 이미 있는 데이터베이스를 추가하면 새 보조 데이터베이스가 보조 서버에 만들어집니다.

  > [!IMPORTANT]
  > 기존 보조 데이터베이스가 아닌 경우 보조 서버에 이름이 같은 데이터베이스가 없는지 확인합니다. SQL Managed Instance의 장애 조치 그룹에서 모든 사용자 데이터베이스가 복제됩니다. 장애 조치(failover) 그룹에서 복제를 위해 사용자 데이터베이스 하위 집합을 선택할 수 없습니다.

- **장애 조치(failover) 그룹에 탄력적 풀의 데이터베이스 추가**

  탄력적 풀에 있는 전체 또는 여러 개의 데이터베이스를 동일한 장애 조치(failover) 그룹에 포함할 수 있습니다. 탄력적 풀에 주 데이터베이스가 있는 경우 동일한 이름을 가진 보조 데이터베이스가 탄력적 풀에 자동으로 만들어집니다(보조 풀). 장애 조치(failover) 그룹에서 만들 보조 데이터베이스를 호스트하기에 충분한 여유 용량과 정확히 동일한 이름을 가진 탄력적 풀이 보조 서버에 있는지 확인해야 합니다. 보조 풀에 보조 데이터베이스가 이미 있는 데이터베이스를 풀에 추가하는 경우 해당 지역 복제 링크가 그룹에 상속됩니다. 장애 조치(failover) 그룹에 속하지 않는 보조 데이터베이스가 서버에 이미 있는 데이터베이스를 추가하는 경우 새 보조 데이터베이스가 보조 풀에 만들어집니다.
  
- **초기 시드**

  장애 조치 그룹에 데이터베이스, 탄력적 풀 또는 관리되는 인스턴스를 추가할 때 데이터 복제를 시작하기 전에 초기 시드 단계가 수행됩니다. 초기 시드 단계는 가장 길고 비용이 많이 드는 작업입니다. 초기 시드가 완료되면 데이터가 동기화되고 후속 데이터 변경 내용만 복제됩니다. 초기 시드를 완료하는 데 걸리는 시간은 데이터 크기, 복제된 데이터베이스 수, 주 데이터베이스에 대한 부하 및 주 데이터베이스와 보조 데이터베이스 간의 연결 속도에 따라 달라집니다. 정상적인 상황에서 가능한 시드 속도는 SQL Database의 경우 시간당 최대 500GB이고, SQL Managed Instance의 경우 시간당 최대 360GB입니다. 모든 데이터베이스에 대해 시드가 병렬로 수행됩니다.

  SQL Managed Instance의 경우 초기 시드 단계의 시간을 예측할 때 두 인스턴스 간 Express 경로 링크의 속도를 고려합니다. 두 인스턴스 간 링크 속도가 필요한 것보다 느리면 시드 시간이 특히 영향을 받을 수 있습니다. 명시된 시드 속도, 데이터베이스 수, 총 데이터 크기 및 링크 속도를 사용하여 데이터 복제가 시작되기 전에 초기 시드 단계에 걸리는 시간을 추정할 수 있습니다. 예를 들어, 단일 100GB 데이터베이스의 경우 링크가 시간당 84GB를 푸시할 수 있고 시드되는 다른 데이터베이스가 없으면 초기 시드 단계에 약 1.2시간이 걸립니다. 링크가 시간당 10GB만 전송할 수 있으면 100GB 데이터베이스를 시드하는 데 약 10시간이 걸립니다. 복제할 데이터베이스가 여러 개 있으면 시드가 병렬로 실행됩니다. 느린 링크 속도와 결합되는 경우, 특히 모든 데이터베이스의 데이터 병렬 시드가 사용 가능한 링크 대역폭을 초과하면 초기 시드 단계가 상당히 오래 걸릴 수 있습니다. 두 인스턴스 간 네트워크 대역폭이 제한되고 여러 관리되는 인스턴스를 장애 조치 그룹에 추가하는 경우 여러 관리되는 인스턴스를 장애 조치 그룹에 하나씩 순서대로 추가하는 것이 좋습니다. 관리되는 두 인스턴스 간에 적절한 크기의 게이트웨이 SKU가 있고 회사 네트워크 대역폭이 허용하는 경우 시간당 360GB의 속도를 달성할 수 있습니다.  

- **DNS 영역**

  새 SQL Managed Instance를 만들 때 자동으로 생성되는 고유 ID입니다. 이 인스턴스의 다중 도메인(SAN) 인증서는 같은 DNS 영역의 인스턴스에 대한 클라이언트 연결을 인증하기 위해 프로비저닝됩니다. 같은 장애 조치 그룹의 관리되는 두 인스턴스는 DNS 영역을 공유해야 합니다.
  
  > [!NOTE]
  > SQL Database용으로 만든 장애 조치 그룹에는 DNS 영역 ID가 필요하지 않습니다.

- **장애 조치 그룹 읽기-쓰기 수신기**

  현재 주 URL을 가리키는 DNS CNAME 레코드입니다. 장애 조치 그룹을 만들 때 자동으로 생성되며 장애 조치 후에 주 데이터베이스가 변경될 때 읽기-쓰기 워크로드를 주 데이터베이스에 투명하게 다시 연결할 수 있습니다. 서버에 장애 조치 그룹이 생성되면 리스너 URL의 DNS CNAME 레코드는 `<fog-name>.database.windows.net`으로 구성됩니다. SQL Managed Instance에 장애 조치 그룹이 생성되면 리스너 URL의 DNS CNAME 레코드는 `<fog-name>.<zone_id>.database.windows.net`으로 구성됩니다.

- **장애 조치 그룹 읽기 전용 수신기**

  보조 데이터베이스의 URL을 가리키는 읽기 전용 수신기를 가리키는 DNS CNAME 레코드가 생성됩니다. 장애 조치 그룹을 만들 때 자동으로 생성되며 읽기 전용 SQL 워크로드가 지정된 부하 분산 규칙을 사용하여 보조 데이터베이스에 투명하게 연결할 수 있습니다. 서버에 장애 조치 그룹이 생성되면 리스너 URL의 DNS CNAME 레코드는 `<fog-name>.secondary.database.windows.net`으로 구성됩니다. SQL Managed Instance에 장애 조치 그룹이 생성되면 리스너 URL의 DNS CNAME 레코드는 `<fog-name>.secondary.<zone_id>.database.windows.net`으로 구성됩니다.

- **자동 장애 조치 정책**

  기본적으로 장애 조치(failover) 그룹은 자동 장애 조치(failover) 정책을 사용하여 구성됩니다. 시스템은 오류가 감지되고 유예 기간이 만료된 후 장애 조치(failover)를 트리거합니다. 영향의 규모로 인해 기본 제공 [고가용성 인프라](high-availability-sla.md)를 통해 중단을 완화할 수 없음을 시스템에서 확인해야 합니다. 애플리케이션에서 장애 조치 워크플로를 제어하거나 수동으로 제어하려는 경우 자동 장애 조치를 해제할 수 있습니다.
  
  > [!NOTE]
  > 가동 중단 규모 및 완화할 수 있는 횟수 확인에는 사람의 작업이 포함되므로 유예 기간을 1시간 미만으로 설정할 수 없습니다. 해당 제한은 데이터 동기화 상태와 관계없이 장애 조치 그룹의 모든 데이터베이스에 적용됩니다.

- **읽기 전용 장애 조치 정책**

  기본적으로 읽기 전용 수신기에 대한 장애 조치가 사용되지 않습니다. 이렇게 하면 보조 서버가 오프라인 상태일 때 주 서버의 성능이 영향을 받지 않습니다. 그러나 보조 서버가 복구될 때까지 읽기 전용 세션이 연결할 수 없음을 의미합니다. 읽기 전용 세션의 가동 중지 시간을 허용할 수 없고 주 데이터베이스의 성능이 잠재적으로 저하되더라도 읽기 전용 및 읽기/쓰기 트래픽 둘 다에 주 데이터베이스를 사용할 수 있으면, `AllowReadOnlyFailoverToPrimary` 속성을 구성하여 읽기 전용 수신기에 장애 조치를 사용하도록 설정할 수 있습니다. 이 경우, 보조 데이터베이스를 사용할 수 없으면 읽기 전용 트래픽이 주 데이터베이스로 자동으로 리디렉션됩니다.

  > [!NOTE]
  > `AllowReadOnlyFailoverToPrimary`속성은 자동 장애 조치(failover) 정책을 사용하도록 설정되고 자동 장애 조치(failover)가 트리거된 경우에만 적용됩니다. 이 경우 속성이 True로 설정되면 새로운 주 데이터베이스에서 읽기/쓰기 및 읽기 전용 세션을 모두 제공합니다.

- **계획된 장애 조치**

   계획된 장애 조치(failover)는 보조 데이터베이스가 주 역할로 전환되기 전에 주 데이터베이스와 보조 데이터베이스 간에 전체 데이터 동기화를 수행합니다. 이렇게 하면 데이터 손실이 발생하지 않습니다. 계획된 장애 조치(failover)는 다음과 같은 시나리오에서 사용합니다.

  - 데이터 손실이 허용되지 않는 경우 프로덕션에서 DR(재해 복구) 훈련 수행
  - 데이터베이스를 다른 지역으로 재배치
  - 중단이 완화된 후 데이터베이스를 주 지역으로 반환(장애 복구(failback))

- **계획되지 않은 장애 조치(Failover)**

   계획되지 않은 장애 조치(failover) 또는 강제 장애 조치(failover)는 주 역할에서 최근 변경 내용이 전파할 때까지 기다리지 않고 보조를 주 역할로 즉시 전환합니다. 이 작업으로 인해 데이터가 손실될 수 있습니다. 계획되지 않은 장애 조치(failover)는 주 데이터베이스에 액세스할 수 없는 중단 중에 복구 방법으로 사용됩니다. 중단이 완화되면 이전 주 주 버전이 자동으로 다시 연결되고 새 보조가 됩니다. 계획된 장애 조치(failover)를 실행하여 복제본을 원래 주 역할과 보조 역할로 반환하는 장애 복구(failover)를 실행할 수 있습니다.

- **수동 장애 조치**

  자동 장애 조치 구성에 관계 없이 언제든지 장애 조치를 수동으로 시작할 수 있습니다. 주 역할에 영향을 주는 중단 시 자동 장애 조치(failover) 정책이 구성되지 않은 경우 보조를 주 역할로 승격하려면 수동 장애 조치(failover)가 필요합니다. 강제(계획되지 않은) 또는 친숙한(계획된) 장애 조치(failover)를 시작할 수 있습니다. 친숙한 장애 조치(failover)는 이전 주 지역에 액세스할 수 있는 경우에만 가능하며, 데이터 손실 없이 주 지역을 보조 지역으로 재배치하는 데 사용할 수 있습니다. 장애 조치(failover)가 완료되면 DNS 레코드가 자동으로 업데이트되어 새 주 서버에 대한 연결을 보장합니다.

- **데이터 손실이 있는 유예 기간**

  보조 데이터베이스는 비동기 복제를 사용하여 동기화되므로 자동 장애 조치(failover)로 인해 데이터가 손실될 수 있습니다. 애플리케이션의 데이터 손실 허용 오차를 반영하도록 자동 장애 조치 정책을 사용자 지정할 수 있습니다. 를 구성하면 강제 장애 `GracePeriodWithDataLossHours` 조치(failover)를 시작하기 전에 시스템이 대기하는 시간을 제어할 수 있으며 이로 인해 데이터가 손실됩니다.

- **여러 장애 조치 그룹**

  같은 서버 쌍에 대해 여러 장애 조치 그룹을 구성하여 장애 조치의 범위를 제어할 수 있습니다. 각 그룹은 독립적으로 장애 조치됩니다. 다중 테넌트 애플리케이션에서 탄력적 풀을 사용하는 경우 이 기능을 사용하여 각 풀에 주 및 보조 데이터베이스를 혼합할 수 있습니다. 이렇게 하면 테넌트의 절반에 대해서만 가동 중단에 따른 영향을 줄일 수 있습니다.

  > [!NOTE]
  > SQL Managed Instance는 여러 개의 장애 조치 그룹을 지원하지 않습니다.
  
## <a name="permissions"></a>권한

장애 조치 그룹의 사용 권한은 [Azure RBAC(Azure 역할 기반 액세스 제어)](../../role-based-access-control/overview.md)를 통해 관리합니다. [SQL Server Contributor](../../role-based-access-control/built-in-roles.md#sql-server-contributor) 역할에는 장애 조치 그룹을 관리하는 데 필요한 모든 사용 권한이 있습니다.

### <a name="create-failover-group"></a>장애 조치 그룹 만들기

장애 조치 그룹을 만들려면 주 서버와 보조 서버 및 장애 조치 그룹의 모든 데이터베이스에 대한 Azure RBAC 쓰기 액세스 권한이 필요합니다. SQL Managed Instance의 경우 주 SQL Managed Instance와 보조 SQL Managed Instance 모두에 대한 Azure RBAC 쓰기 액세스 권한이 필요하지만, 개별 SQL Managed Instance 데이터베이스를 장애 조치 그룹에 추가하거나 제거할 수 없기 때문에 개별 데이터베이스에 대한 사용 권한은 관련이 없습니다.

### <a name="update-a-failover-group"></a>장애 조치 그룹 업데이트

장애 조치 그룹을 업데이트하려면 장애 조치 그룹과 현재 주 서버 또는 관리되는 인스턴스의 모든 데이터베이스에 대한 Azure RBAC 쓰기 액세스 권한이 필요합니다.  

### <a name="fail-over-a-failover-group"></a>장애 조치 그룹에 관한 장애 조치

장애 조치 그룹을 장애 조치하려면 새로운 주 서버나 관리되는 인스턴스의 장애 조치 그룹에 대한 Azure RBAC 쓰기 권한이 필요합니다.

## <a name="best-practices-for-sql-database"></a>SQL Database의 모범 사례

자동 장애 조치 그룹은 주 서버에서 구성되어야 하며 다른 Azure 지역의 보조 서버에 연결됩니다. 그룹에는 이러한 서버에 있는 데이터베이스가 모두 또는 일부 포함될 수 있습니다. 다음 다이어그램은 여러 데이터베이스와 자동 장애 조치(failover) 그룹을 사용하는 지역 중복 클라우드 애플리케이션의 일반적인 구성을 보여 줍니다.

![다이어그램은 여러 데이터베이스와 자동 장애 조치 그룹을 사용하는 지역 중복 클라우드 애플리케이션의 일반적인 구성을 보여 줍니다.](./media/auto-failover-group-overview/auto-failover-group.png)

> [!NOTE]
> 장애 조치 그룹에 SQL Database 데이터베이스를 추가하는 자세한 단계별 자습서는 [장애 조치 그룹에 SQL Database 추가](failover-group-add-single-database-tutorial.md)를 참조하세요.

비즈니스 연속성을 고려하여 서비스를 설계하는 경우 다음과 같은 일반 지침을 따르세요.

### <a name="using-one-or-several-failover-groups-to-manage-failover-of-multiple-databases"></a>하나 이상의 장애 조치 그룹을 사용하여 여러 데이터베이스의 장애 조치 관리

다른 하위 지역의 두 서버(기본 및 보조 서버) 간에 하나 이상의 장애 조치 그룹을 만들 수 있습니다. 주 지역의 가동 중단으로 인해 주 데이터베이스 전체 또는 일부를 사용할 수 없게 되는 경우를 대비하여 각 그룹에 한 단위로 복구되는 하나 또는 여러 개의 데이터베이스가 포함될 수 있습니다. 장애 조치(failover) 그룹을 사용하는 경우 주 데이터베이스와 서비스 목표가 동일한 지역 보조 데이터베이스가 작성됩니다. 장애 조치(failover) 그룹에 기존의 지역 복제 관계를 추가하는 경우 보조 데이터베이스가 주 데이터베이스와 동일한 서비스 계층 및 컴퓨팅 크기로 구성되었는지 확인합니다.
  
### <a name="using-read-write-listener-for-oltp-workload"></a>OLTP 워크로드에 읽기-쓰기 수신기 사용

OLTP 작업을 수행할 때 `<fog-name>.database.windows.net`을 서버 URL로 사용하면 자동으로 주 데이터베이스에 연결됩니다. 이 URL은 장애 조치(failover) 후에 변경되지 않습니다. 장애 조치(failover) 시에는 DNS 레코드가 업데이트되므로 클라이언트 DNS 캐시를 새로 고친 후에만 클라이언트 연결이 새 주 데이터베이스로 리디렉션됩니다.

### <a name="using-read-only-listener-for-read-only-workload"></a>읽기 전용 워크로드에 읽기 전용 수신기 사용

특정 데이터가 부실해도 정상적으로 수행 가능한 논리적으로 격리된 읽기 전용 작업이 있는 경우에는 애플리케이션에서 보조 데이터베이스를 사용할 수 있습니다. 읽기 전용 세션의 경우 `<fog-name>.secondary.database.windows.net`을 서버 URL로 사용하면 자동으로 보조 데이터베이스에 연결됩니다. 또한 `ApplicationIntent=ReadOnly`를 사용하여 연결 문자열에 읽기 의도를 표시하는 것이 좋습니다.

> [!NOTE]
> 프리미엄, 중요 비즈니스용, 하이퍼스케일 서비스 계층에서 SQL Database는 연결 문자열의 `ApplicationIntent=ReadOnly` 매개 변수를 사용하여 읽기 전용 쿼리 워크로드를 오프로드하도록 [읽기 전용 복제본](read-scale-out.md) 사용을 지원합니다. 지역 복제된 보조 데이터베이스를 구성한 경우 이 기능을 사용하여 주 위치 또는 지역 복제된 위치에 있는 읽기 전용 복제본에 연결할 수 있습니다.
>
> - 주 위치의 읽기 전용 복제본에 연결하려면 `ApplicationIntent=ReadOnly`와 `<fog-name>.database.windows.net`을 사용합니다.
> - 보조 위치의 읽기 전용 복제본에 연결하려면 `ApplicationIntent=ReadOnly`와 `<fog-name>.secondary.database.windows.net`을 사용합니다.

### <a name="preparing-for-performance-degradation"></a>성능 저하에 대비

일반적인 Azure 애플리케이션은 여러 Azure 서비스를 사용하며 여러 구성 요소로 구성됩니다. 장애 조치 그룹의 자동 장애 조치는 Azure SQL 구성 요소에만 해당하는 상태에 따라 트리거됩니다. 주 지역의 다른 Azure 서비스는 중단의 영향을 받지 않을 수 있으며 해당 구성 요소는 해당 지역에서 계속 사용할 수 있습니다. 주 데이터베이스가 DR 지역으로 전환되면 종속된 구성 요소 사이의 대기 시간이 늘어날 수 있습니다. 대기 시간이 애플리케이션 성능에 영향을 미치지 않게 하려면 DR 지역에 있는 모든 애플리케이션 구성 요소의 중복성을 확인하고 [네트워크 보안 가이드라인](#failover-groups-and-network-security)을 따릅니다.

### <a name="preparing-for-data-loss"></a>데이터 손실에 대비

중단이 검색되면 Azure에서 `GracePeriodWithDataLossHours`에 지정된 기간을 대기합니다. 기본값은 1시간입니다. 데이터 손실을 방지하려면 `GracePeriodWithDataLossHours`를 24시간과 같이 충분히 큰 숫자로 설정해야 합니다. 수동 그룹 장애 조치(failover)를 사용하여 보조 데이터베이스에서 주 데이터베이스로 장애 복구할 수 있습니다.

> [!IMPORTANT]
> 지역 복제를 사용하는 800개 이하의 DTU 및 250개 초과의 데이터베이스가 있는 탄력적 풀에는 계획된 장애 조치 지연 및 성능 저하가 포함된 문제가 발생할 수 있습니다.  이러한 문제는 지역에서 복제 엔드포인트가 지리적으로 광범위하게 분리되어 있거나 여러 보조 엔드포인트가 각 데이터베이스에 대해 사용되는 경우 쓰기 집약적 작업에 발생할 가능성이 높습니다.  이러한 문제의 증상은 지역에서 복제 지연이 시간에 따라 증가하는 경우에 표시됩니다.  이러한 지연은 [sys.dm_geo_replication_link_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-geo-replication-link-status-azure-sql-database)를 사용하여 모니터링할 수 있습니다.  이러한 문제가 발생하는 경우 완화 방안에는 풀 DTU의 수를 늘리거나 동일한 풀에 지역에서 복제된 데이터베이스 수를 줄이는 방법이 있습니다.

### <a name="changing-secondary-region-of-the-failover-group"></a>장애 조치 그룹의 보조 지역 변경

변경 시퀀스를 설명하기 위해 서버 A는 주 서버이고, 서버 B는 기존 보조 서버이며, 서버 C는 세 번째 지역의 새로운 보조 서버라고 가정합니다.  전환을 수행하려면 다음 단계를 수행합니다.

1. [활성 지역 복제](active-geo-replication-overview.md)를 사용하여 서버 A에서 서버 C까지 각 데이터베이스의 추가 보조 데이터베이스를 만듭니다. 서버 A의 각 데이터베이스에는 서버 B와 서버 C에 하나씩 두 개의 보조 데이터베이스가 있습니다. 따라서 전환 중에 주 데이터베이스가 보호된 상태로 유지됩니다.
2. 장애 조치 그룹을 삭제합니다. 이 시점에 로그인에 실패합니다. 장애 조치 그룹 수신기의 SQL 별칭이 삭제되고 게이트웨이가 장애 조치 그룹 이름을 인식하지 못하기 때문입니다.
3. 서버 A와 C 간에 이름이 같은 장애 조치 그룹을 다시 만듭니다. 이 시점에 로그인 실패가 중지됩니다.
4. 서버 A의 모든 주 데이터베이스를 새 장애 조치 그룹에 추가합니다.
5. 서버 B를 삭제합니다. B의 모든 데이터베이스가 자동으로 삭제됩니다.

### <a name="changing-primary-region-of-the-failover-group"></a>장애 조치 그룹의 주 지역 변경

변경 시퀀스를 설명하기 위해 서버 A는 주 서버이고, 서버 B는 기존 보조 서버이며, 서버 C는 세 번째 지역의 새로운 주 서버라고 가정합니다.  전환을 수행하려면 다음 단계를 수행합니다.

1. 계획된 장애 조치를 수행하여 주 서버를 B로 전환합니다. 서버 A가 새 보조 서버가 됩니다. 장애 조치를 수행하면 몇 분의 가동 중지 시간이 발생할 수 있습니다. 실제 시간은 장애 조치 그룹의 크기에 따라 달라집니다.
2. [활성 지역 복제](active-geo-replication-overview.md)를 사용하여 서버 B에서 서버 C까지 각 데이터베이스의 추가 보조 데이터베이스를 만듭니다. 서버 B의 각 데이터베이스에는 서버 A와 서버 C에 하나씩 두 개의 보조 데이터베이스가 있습니다. 따라서 전환 중에 주 데이터베이스가 보호된 상태로 유지됩니다.
3. 장애 조치 그룹을 삭제합니다. 이 시점에 로그인에 실패합니다. 장애 조치 그룹 수신기의 SQL 별칭이 삭제되고 게이트웨이가 장애 조치 그룹 이름을 인식하지 못하기 때문입니다.
4. 서버 B와 C 간에 이름이 같은 장애 조치 그룹을 다시 만듭니다. 이 시점에 로그인 실패가 중지됩니다.
5. B의 모든 주 데이터베이스를 새 장애 조치 그룹에 추가합니다.
6. 장애 조치 그룹의 계획된 장애 조치를 수행하여 B와 C를 전환합니다. 이제 서버 C가 주 서버가 되고 B가 보조 서버가 됩니다. 서버 A의 모든 보조 데이터베이스는 C의 주 데이터베이스에 자동으로 연결됩니다. 1단계에서와 같이 장애 조치를 수행하면 몇 분 동안 가동 중지 시간이 발생할 수 있습니다.
7. 서버 A를 삭제합니다. A의 모든 데이터베이스가 자동으로 삭제됩니다.

> [!IMPORTANT]
> 장애 조치 그룹을 삭제하면 수신기 엔드포인트의 DNS 레코드도 삭제됩니다. 이 시점에 다른 사용자가 이름이 같은 장애 조치 그룹이나 서버 별칭을 만들어 다시 사용할 수 없게 만들 확률이 0이 아닙니다. 위험을 최소화하려면 일반 장애 조치 그룹 이름을 사용하지 마세요.

## <a name="best-practices-for-sql-managed-instance"></a>SQL Managed Instance의 모범 사례

자동 장애 조치(failover) 그룹은 주 인스턴스에서 구성되어야 하며 다른 Azure 지역의 보조 인스턴스에 연결됩니다.  인스턴스의 모든 사용자 데이터베이스는 보조 인스턴스에 복제됩니다. _master_ 및 _msdb와_ 같은 시스템 데이터베이스는 복제되지 않습니다.

다음 다이어그램은 관리되는 인스턴스와 자동 장애 조치(failover) 그룹을 사용하는 지역 중복 클라우드 애플리케이션의 일반적인 구성을 보여 줍니다.

![자동 장애 조치 다이어그램](./media/auto-failover-group-overview/auto-failover-group-mi.png)

> [!NOTE]
> 장애 조치 그룹을 사용하는 SQL Managed Instance를 추가하는 자세한 단계별 자습서는 [장애 조치 그룹에 관리되는 인스턴스 추가](../managed-instance/failover-group-add-instance-tutorial.md)를 참조하세요.

애플리케이션에서 SQL Managed Instance를 데이터 계층으로 사용하는 경우 비즈니스 연속성을 설계할 때 다음과 같은 일반 지침을 따릅니다.

### <a name="creating-the-secondary-instance"></a>보조 인스턴스 만들기

장애 조치 다음에 주 SQL Managed Instance에 대한 무중단 연결을 보장하려면 주 인스턴스와 보조 인스턴스가 모두 같은 DNS 영역에 있어야 합니다. 같은 다중 도메인(SAN) 인증서를 사용하여 장애 조치 그룹의 두 인스턴스 중 하나에 대한 클라이언트 연결을 인증할 수 있습니다. 애플리케이션이 프로덕션 배포에 사용할 준비가 되면 다른 지역에 보조 SQL Managed Instance를 만들고 주 SQL Managed Instance와 DNS 영역을 공유하는지 확인합니다. 만드는 중에 선택적 매개 변수를 지정하여 이 작업을 수행할 수 있습니다. PowerShell이나 REST API를 사용하는 경우 선택적 매개 변수의 이름은 `DNS Zone Partner`이고 Azure Portal에서 해당 선택적 필드의 이름은 Primary Managed Instance입니다.

> [!IMPORTANT]
> 서브넷에 만든 첫 번째 관리되는 인스턴스에 따라 같은 서브넷에 있는 모든 후속 인스턴스의 DNS 영역이 결정됩니다. 즉, 같은 서브넷의 두 인스턴스는 서로 다른 DNS 영역에 속할 수 없습니다.

주 인스턴스와 같은 DNS 영역에 보조 SQL Managed Instance를 만드는 방법에 관한 자세한 내용은 [보조 관리되는 인스턴스 만들기](../managed-instance/failover-group-add-instance-tutorial.md#create-a-secondary-managed-instance)를 참조하세요.

### <a name="using-geo-paired-regions"></a>지리적으로 쌍을 이루는 지역 사용

성능 상의 이유로 두 관리형 인스턴스를 [쌍을 이루는 지역](../../best-practices-availability-paired-regions.md)에 배포합니다. 지리적으로 쌍을 이루는 지역에 상주하는 관리되는 인스턴스는 쌍을 이루지 않는 지역에 비해 훨씬 더 나은 성능을 제공합니다. 

### <a name="enabling-replication-traffic-between-two-instances"></a>두 인스턴스 간에 복제 트래픽 사용

각 인스턴스가 자체 VNet에 격리되므로 이러한 VNet 간의 양방향 트래픽을 허용해야 합니다. [Azure VPN 게이트웨이](../../vpn-gateway/vpn-gateway-about-vpngateways.md)를 참조하세요.

### <a name="creating-a-failover-group-between-managed-instances-in-different-subscriptions"></a>다른 구독의 Managed Instance 간에 장애 조치(failover) 그룹 만들기

구독이 같은 [Azure Active Directory 테넌트](../../active-directory/fundamentals/active-directory-whatis.md#terminology)에 연결되어 있는 한 다른 두 구독에서 SQL Managed Instance 간에 장애 조치 그룹을 만들 수 있습니다. PowerShell API를 사용할 때 보조 SQL Managed Instance의 `PartnerSubscriptionId` 매개 변수를 지정하여 수행할 수 있습니다. REST API를 사용하는 경우 `properties.managedInstancePairs` 매개 변수에 포함된 각 인스턴스 ID에는 고유 subscriptionID가 있을 수 있습니다.
  
> [!IMPORTANT]
> Azure Portal은 다른 구독에서 장애 조치 그룹 만들기를 지원하지 않습니다. 또한 여러 구독 및/또는 리소스 그룹에 걸친 기존 장애 조치 그룹의 경우 주 SQL Managed Instance에서 포털을 통해 수동으로 장애 조치를 시작할 수 없습니다. 대신 지역 보조 인스턴스에서 시작합니다.

### <a name="managing-failover-to-secondary-instance"></a>보조 인스턴스로 장애 조치(failover) 관리

장애 조치(failover) 그룹은 SQL Managed Instance에 있는 모든 데이터베이스의 장애 조치(failover)를 관리합니다. 그룹을 만들면 인스턴스의 각 데이터베이스가 자동으로 보조 SQL Managed Instance에 지역 복제됩니다. 장애 조치(failover) 그룹을 사용하여 데이터베이스 하위 집합의 부분 장애 조치(failover)를 시작할 수 없습니다.

> [!IMPORTANT]
> 주 SQL Managed Instance에서 데이터베이스를 제거하면 지역 보조 SQL Managed Instance에서도 자동으로 삭제됩니다.

### <a name="using-read-write-listener-for-oltp-workload"></a>OLTP 워크로드에 읽기-쓰기 수신기 사용

OLTP 작업을 수행할 때 `<fog-name>.zone_id.database.windows.net`을 서버 URL로 사용하면 자동으로 주 데이터베이스에 연결됩니다. 이 URL은 장애 조치(failover) 후에 변경되지 않습니다. 장애 조치(failover) 시에는 DNS 레코드가 업데이트되므로 클라이언트 DNS 캐시를 새로 고친 후에만 클라이언트 연결이 새로운 주 데이터베이스로 리디렉션됩니다. 보조 인스턴스는 주 인스턴스와 DNS 영역을 공유하므로 클라이언트 애플리케이션이 같은 SAN 인증서를 사용하여 다시 연결할 수 있습니다.

### <a name="using-read-only-listener-to-connect-to-the-secondary-instance"></a>읽기 전용 수신기를 사용하여 보조 인스턴스에 연결

특정 데이터가 부실해도 정상적으로 수행 가능한 논리적으로 격리된 읽기 전용 작업이 있는 경우에는 애플리케이션에서 보조 데이터베이스를 사용할 수 있습니다. 지역 복제된 보조 데이터베이스에 직접 연결하려는 경우 `<fog-name>.secondary.<zone_id>.database.windows.net`을 서버 URL로 사용하면 지역 복제된 보조 데이터베이스에 직접 연결됩니다.

> [!NOTE]
> 중요 비즈니스용 계층에서 SQL Managed Instance는 연결 문자열의 `ApplicationIntent=ReadOnly` 매개 변수를 사용하여 읽기 전용 쿼리 워크로드를 오프로드하도록 [읽기 전용 복제본](read-scale-out.md) 사용을 지원합니다. 지역 복제된 보조 데이터베이스를 구성한 경우 이 기능을 사용하여 주 위치 또는 지역 복제된 위치에 있는 읽기 전용 복제본에 연결할 수 있습니다.
>
> - 주 위치의 읽기 전용 복제본에 연결하려면 `ApplicationIntent=ReadOnly`와 `<fog-name>.<zone_id>.database.windows.net`을 사용합니다.
> - 보조 위치의 읽기 전용 복제본에 연결하려면 `ApplicationIntent=ReadOnly`와 `<fog-name>.secondary.<zone_id>.database.windows.net`을 사용합니다.

### <a name="preparing-for-performance-degradation"></a>성능 저하에 대비

일반적인 Azure 애플리케이션은 여러 Azure 서비스를 사용하며 여러 구성 요소로 구성됩니다. 장애 조치 그룹의 자동 장애 조치는 Azure SQL 구성 요소에만 해당하는 상태에 따라 트리거됩니다. 주 지역의 다른 Azure 서비스는 중단의 영향을 받지 않을 수 있으며 해당 구성 요소는 해당 지역에서 계속 사용할 수 있습니다. 주 데이터베이스가 보조 지역으로 전환되면 종속된 구성 요소 사이의 대기 시간이 늘어날 수 있습니다. 대기 시간이 애플리케이션 성능에 영향을 미치지 않도록 보조 지역에 있는 모든 애플리케이션 구성 요소의 중복성을 보장하고 데이터베이스와 함께 애플리케이션 구성 요소를 장애 조치합니다. 구성 시 [네트워크 보안 가이드라인](#failover-groups-and-network-security)을 따라 보조 영역의 데이터베이스에 연결해야 합니다.

### <a name="preparing-for-data-loss"></a>데이터 손실에 대비

중단이 검색되면 데이터 손실이 없는 경우 읽기/쓰기 장애 조치가 트리거됩니다. 그렇지 않으면 `GracePeriodWithDataLossHours`를 사용하여 지정하는 기간에 장애 조치가 연기됩니다. `GracePeriodWithDataLossHours`를 지정한 경우 데이터 손실을 준비합니다. 일반적으로 중단 시에 Azure에서는 가용성을 우선으로 합니다. 데이터 손실을 방지하려는 경우 GracePeriodWithDataLossHours를 24시간과 같은 충분히 큰 숫자로 설정하거나 자동 장애 조치를 사용하지 않게 설정해야 합니다.

읽기/쓰기 수신기의 DNS 업데이트는 장애 조치(failover)가 시작된 후 즉시 발생합니다. 이 작업으로 인한 데이터 손실은 없습니다. 그러나 일반적인 조건에서 데이터베이스 역할을 전환하는 데 최대 5분 정도 걸릴 수 있습니다. 완료될 때까지 새로운 주 인스턴스의 일부 데이터베이스는 계속 읽기 전용입니다. PowerShell을 사용하여 장애 조치를 시작하는 경우 주 복제본 역할을 전환하는 작업은 동기식입니다. Azure Portal을 사용하여 시작하는 경우 UI에 완료 상태가 표시됩니다. REST API를 사용하여 시작하는 경우, 표준 Azure Resource Manager의 폴링 메커니즘을 사용하여 완료되었는지 모니터링합니다.

> [!IMPORTANT]
> 수동 그룹 장애 조치(failover)를 사용하여 주 데이터베이스를 원래 위치로 다시 이동할 수 있습니다. 장애 조치(failover)를 유발한 중단이 완화되면 주 데이터베이스를 원래 위치로 이동할 수 있습니다. 이렇게 하려면 그룹의 수동 장애 조치(failover)를 시작해야 합니다.
  
### <a name="changing-secondary-region-of-the-failover-group"></a>장애 조치 그룹의 보조 지역 변경

인스턴스 A는 주 인스턴스이고, 인스턴스 B는 기존 보조 인스턴스이며, 인스턴스 C는 세 번째 영역의 새 보조 인스턴스라고 가정해 보겠습니다.  전환을 수행하려면 다음 단계를 수행합니다.

1. A와 크기가 같고 DNS 영역이 같은 인스턴스 C를 만듭니다.
2. 인스턴스 A와 B 사이의 장애 조치 그룹을 삭제합니다. 이 시점에 장애 조치 그룹 수신기의 SQL 별칭이 삭제되고 게이트웨이가 장애 조치 그룹 이름을 인식하지 못하기 때문에 로그인에 실패합니다. 보조 데이터베이스는 주 데이터베이스에서 연결을 끊고 읽기/쓰기 데이터베이스가 됩니다.
3. 인스턴스 A와 C 간에 이름이 같은 장애 조치 그룹을 만듭니다. [SQL Managed Instance를 사용하는 장애 조치 그룹 자습서](../managed-instance/failover-group-add-instance-tutorial.md)의 지침을 따릅니다. 데이터 크기 작업이며, 인스턴스 A의 모든 데이터베이스가 시드되고 동기화되면 완료됩니다.
4. 불필요한 요금을 방지하기 위해 필요하지 않은 경우 인스턴스 B를 삭제합니다.

> [!NOTE]
> 2단계 이후 3단계가 완료될 때까지 인스턴스 A의 데이터베이스는 인스턴스 A의 치명적인 실패로부터 보호되지 않은 상태로 유지됩니다.

### <a name="changing-primary-region-of-the-failover-group"></a>장애 조치 그룹의 주 지역 변경

인스턴스 A는 주 인스턴스이고, 인스턴스 B는 기존 보조 인스턴스이며, 인스턴스 C는 세 번째 영역의 새 주 인스턴스라고 가정해 보겠습니다.  전환을 수행하려면 다음 단계를 수행합니다.

1. B와 크기가 같고 DNS 영역이 같은 인스턴스 C를 만듭니다.
2. 인스턴스 B에 연결하고 수동으로 장애 조치하여 주 인스턴스를 B로 전환합니다. 인스턴스 A는 자동으로 새 보조 인스턴스가 됩니다.
3. 인스턴스 A와 B 사이의 장애 조치 그룹을 삭제합니다. 이 시점에 장애 조치 그룹 수신기의 SQL 별칭이 삭제되고 게이트웨이가 장애 조치 그룹 이름을 인식하지 못하기 때문에 로그인에 실패합니다. 보조 데이터베이스는 주 데이터베이스에서 연결을 끊고 읽기/쓰기 데이터베이스가 됩니다.
4. 인스턴스 A와 C 간에 이름이 같은 장애 조치 그룹을 만듭니다. [관리되는 인스턴스를 사용하는 장애 조치 그룹 자습서](../managed-instance/failover-group-add-instance-tutorial.md)의 지침을 따릅니다. 데이터 크기 작업이며, 인스턴스 A의 모든 데이터베이스가 시드되고 동기화되면 완료됩니다.
5. 불필요한 요금을 방지하기 위해 필요하지 않은 경우 인스턴스 A를 삭제합니다.

> [!CAUTION]
> 3단계 이후 4단계가 완료될 때까지 인스턴스 A의 데이터베이스는 인스턴스 A의 치명적인 실패로부터 보호되지 않은 상태로 유지됩니다.

> [!IMPORTANT]
> 장애 조치 그룹을 삭제하면 수신기 엔드포인트의 DNS 레코드도 삭제됩니다. 이 시점에 다른 사용자가 이름이 같은 장애 조치 그룹이나 서버 별칭을 만들어 다시 사용할 수 없게 만들 확률이 0이 아닙니다. 위험을 최소화하려면 일반 장애 조치 그룹 이름을 사용하지 마세요.

### <a name="enable-scenarios-dependent-on-objects-from-the-system-databases"></a>시스템 데이터베이스의 개체에 종속된 시나리오 사용
시스템 데이터베이스는 장애 조치(failover) 그룹의 보조 인스턴스에 **복제되지 않습니다.** 시스템 데이터베이스의 개체에 의존하는 시나리오를 사용하려면 보조 인스턴스에서 동일한 개체를 만들고 주 인스턴스와 동기화된 상태로 유지해야 합니다. 예를 들어 보조 인스턴스에서 같은 로그인을 사용하려는 경우 같은 SID를 사용하여 해당 로그인을 만들어야 합니다. 
```SQL
-- Code to create login on the secondary instance
CREATE LOGIN foo WITH PASSWORD = '<enterStrongPasswordHere>', SID = <login_sid>;
``` 
### <a name="synchronize-instance-properties-and-retention-policies-between-primary-and-secondary-instance"></a>주 인스턴스와 보조 인스턴스 간에 인스턴스 속성 및 보존 정책 동기화
장애 조치(failover) 그룹의 인스턴스는 별도의 Azure 리소스로 유지되며 주 인스턴스의 구성에 대한 변경 내용은 보조 인스턴스에 자동으로 복제되지 않습니다. 주 _인스턴스와_ 보조 인스턴스 모두에서 모든 관련 변경 내용을 수행해야 합니다. 예를 들어 주 인스턴스에서 백업 스토리지 중복성 또는 장기 백업 보존 정책을 변경하는 경우 보조 인스턴스에서도 변경해야 합니다.

## <a name="failover-groups-and-network-security"></a>장애 조치 그룹 및 네트워크 보안

일부 애플리케이션의 경우 보안 규칙에 따라 데이터 계층에 대한 네트워크 액세스가 VM, 웹 서비스 등과 같은 구성 요소나 특정 구성 요소로 제한되어야 합니다. 이 요구 사항 때문에 비즈니스 연속성 디자인과 장애 조치 그룹 사용에 관한 몇 가지 문제가 발생합니다. 이러한 제한된 액세스를 구현하는 경우 다음 옵션을 고려합니다.

### <a name="using-failover-groups-and-virtual-network-rules"></a>장애 조치 그룹 및 가상 네트워크 규칙 사용

[가상 네트워크 서비스 엔드포인트 및 규칙](vnet-service-endpoint-rule-overview.md)을 사용하여 SQL Database 또는 SQL Managed Instance의 데이터베이스에 대한 액세스를 제한하는 경우 각 가상 네트워크 서비스 엔드포인트가 하나의 Azure 지역에만 적용됩니다. 엔드포인트를 사용하여 다른 지역이 서브넷에서 보낸 통신을 수락하도록 할 수 없습니다. 따라서 동일한 지역에 배포된 클라이언트 애플리케이션만 주 데이터베이스에 연결할 수 있습니다. 장애 조치가 SQL Database 클라이언트 세션을 다른 (보조) 지역의 서버로 다시 라우팅하는 결과를 발생시키므로 해당 지역 외부에 있는 클라이언트에서 시작된 경우 이러한 세션에 실패합니다. 이런 이유로 참여하는 서버나 인스턴스가 가상 네트워크 규칙에 포함된 경우 자동 장애 조치 정책을 사용할 수 없습니다. 수동 장애 조치를 지원하려면 다음 단계를 수행합니다.

1. 보조 지역에서 애플리케이션(웹 서비스, 가상 머신 등) 프런트 엔드 구성 요소의 중복 복사본을 프로비전합니다.
2. 기본 및 보조 서버에 대한 [가상 네트워크 규칙](vnet-service-endpoint-rule-overview.md)을 개별적으로 구성합니다.
3. [트래픽 관리자 구성을 사용하여 프런트 엔드 장애 조치](designing-cloud-solutions-for-disaster-recovery.md#scenario-1-using-two-azure-regions-for-business-continuity-with-minimal-downtime)를 사용하도록 설정합니다.
4. 중단이 검색되면 수동 장애 조치(failover)를 시작합니다. 이 옵션은 프런트 엔드와 데이터 계층 간에 일관된 대기 시간을 필요로 하는 애플리케이션에 대해 최적화되고 프런트 엔드, 데이터 계층 또는 둘 모두가 중단의 영향을 받는 경우 복구를 지원합니다.

> [!NOTE]
> **읽기 전용 수신기** 를 사용하여 읽기 전용 워크로드의 부하를 분산하는 경우 이 워크로드가 보조 데이터베이스에 연결할 수 있도록 보조 지역의 VM 또는 다른 리소스에서 실행되는지 확인합니다.

### <a name="use-failover-groups-and-firewall-rules"></a>장애 조치 그룹 및 방화벽 규칙 사용

비즈니스 연속성 플랜이 자동 장애 조치에서 그룹을 사용하는 장애 조치가 필요한 경우 기존 방화벽 규칙을 사용하여 SQL Database의 데이터베이스에 대한 액세스를 제한할 수 있습니다. 자동 장애 조치를 지원하려면 다음 단계를 수행합니다.

1. [공용 IP를 만듭니다.](../../virtual-network/virtual-network-public-ip-address.md#create-a-public-ip-address)
2. [공용 부하 분산 장치를 만들고](../../load-balancer/quickstart-load-balancer-standard-public-portal.md) 여기에 공용 IP를 할당합니다.
3. 프런트 엔드 구성 요소에 대한 [가상 네트워크 및 가상 머신을 만듭니다.](../../load-balancer/quickstart-load-balancer-standard-public-portal.md)
4. [네트워크 보안 그룹을 만들고](../../virtual-network/network-security-groups-overview.md) 인바운드 연결을 구성합니다.
5. ‘Sql’ [서비스 태그](../../virtual-network/network-security-groups-overview.md#service-tags)를 사용하여 아웃바운드 연결이 Azure SQL Database에 대해 열려 있는지 확인합니다.
6. [SQL 데이터베이스 방화벽 규칙](firewall-configure.md)을 만들어서 1단계에서 만든 공용 IP 주소에서 인바운드 트래픽을 허용합니다.

아웃바운드 액세스를 구성하는 방법 및 방화벽 규칙에서 사용할 IP에 대한 자세한 내용은 [부하 분산 장치 아웃바운드 연결](../../load-balancer/load-balancer-outbound-connections.md)을 참조하세요.

위의 구성에서는 자동 장애 조치가 프런트 엔드 구성 요소에서 연결을 차단하지 않는지 확인하고 애플리케이션이 프런트 엔드와 데이터 계층 간의 긴 대기 시간을 허용할 수 있다고 가정합니다.

> [!IMPORTANT]
> 지역 중단에 대한 비즈니스 연속성을 보장하기 위해 프런트 엔드 구성 요소와 데이터베이스 모두에 대한 지리적 복제를 확인해야 합니다.

## <a name="enabling-geo-replication-between-managed-instances-and-their-vnets"></a>Managed Instance와 해당 VNet 간에 지역 복제 사용

두 지역에 있는 주 SQL Managed Instance와 보조 SQL Managed Instances 간에 장애 조치 그룹을 설정하는 경우 각 인스턴스가 독립 가상 네트워크를 사용하여 격리됩니다. 이러한 VNet 간에 복제 트래픽을 허용하려면 다음 필수 조건을 충족하는지 확인합니다.

- SQL Managed Instance의 두 인스턴스는 서로 다른 Azure 지역에 있어야 합니다.
- SQL Managed Instance의 두 인스턴스는 같은 서비스 계층이어야 하고 스토리지 크기는 같아야 합니다.
- SQL Managed Instance의 보조 인스턴스는 비어 있어야 합니다(사용자 데이터베이스 없음).
- SQL Managed Instance에서 사용되는 가상 네트워크는 [VPN Gateway](../../vpn-gateway/vpn-gateway-about-vpngateways.md) 또는 [Express Route](../../expressroute/expressroute-howto-circuit-portal-resource-manager.md)를 통해 연결해야 합니다. 두 가상 네트워크가 온-프레미스 네트워크를 통해 연결되는 경우 포트 5022 및 11000-11999를 차단하는 방화벽 규칙이 없어야 합니다. 글로벌 VNet 피어링은 아래 참고에 설명된 제한 사항으로 지원됩니다.

   > [!IMPORTANT]
   > [2020년 9월 22일에 새로 만든 가상 클러스터의 글로벌 가상 네트워크 피어링에 대한 지원을 발표했습니다](https://azure.microsoft.com/updates/global-virtual-network-peering-support-for-azure-sql-managed-instance-now-available/). 즉, 발표일 이후부터는 빈 서브넷에서 생성된 SQL 관리형 인스턴스와 해당 서브넷에서 생성된 모든 후속 관리형 인스턴스에 대해 글로벌 가상 네트워크 피어링이 지원됩니다. 다른 모든 SQL 관리형 인스턴스의 경우 피어링 지원은 [글로벌 가상 네트워크 피어링의 제약 조건](../../virtual-network/virtual-network-manage-peering.md#requirements-and-constraints)으로 인해 같은 지역의 네트워크로 제한됩니다. 자세한 내용은 [Azure 가상 네트워크에 대해 자주 묻는 질문](../../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers) 문서의 관련 섹션을 참조하세요. 공지 날짜 이전에 만든 가상 클러스터에서 관리되는 인스턴스를 SQL 전역 가상 네트워크 피어링을 사용할 수 있도록 인스턴스를 글로벌 가상 네트워크 피어링을 지원하는 새 가상 클러스터로 이동하기 때문에 인스턴스에서 [기본이](./maintenance-window.md) 아닌 유지 관리 기간을 구성하는 것이 좋습니다.

- 두 개의 SQL Managed Instance VNet에 겹치는 IP 주소가 있으면 안 됩니다.
- 포트 5022, 범위 11000~12000이 다른 Managed Instance 서브넷의 연결에 대해 인바운드 및 아웃바운드로 열려 있도록 NSG(네트워크 보안 그룹)를 설정해야 합니다. 이는 인스턴스 간의 복제 트래픽을 허용하기 위한 것입니다.

   > [!IMPORTANT]
   > NSG 보안 규칙을 잘못 구성하면 데이터베이스 복사 작업이 중단됩니다.

- 보조 SQL Managed Instance는 올바른 DNS 영역 ID를 사용하여 구성됩니다. DNS 영역은 SQL Managed Instance와 기본 가상 클러스터의 속성이며 이 영역의 ID는 호스트 이름 주소에 포함됩니다. 각 VNet에서 첫 번째 SQL Managed Instance를 만들 때 영역 ID가 임의 문자열로 생성되며 같은 서브넷의 다른 모든 인스턴스에 같은 ID가 할당됩니다. ID가 할당되면 DNS 영역을 수정할 수 없습니다. 같은 장애 조치 그룹에 포함된 SQL Managed Instance는 DNS 영역을 공유해야 합니다. 보조 인스턴스를 만들 때 주 인스턴스의 영역 ID를 DnsZonePartner 매개 변수 값으로 전달하여 이 작업을 수행합니다.

   > [!NOTE]
   > SQL Managed Instance를 사용하여 장애 조치 그룹을 구성하는 데 관한 자세한 자습서는 [장애 조치 그룹에 SQL Managed Instance 추가](../managed-instance/failover-group-add-instance-tutorial.md)를 참조하세요.

## <a name="upgrading-or-downgrading-a-primary-database"></a>주 데이터베이스 업그레이드 또는 다운그레이드

보조 데이터베이스와 연결을 끊지 않고도 주 데이터베이스를 다른 컴퓨팅 크기로 업그레이드하거나 다운그레이드할 수 있습니다. 업그레이드하는 경우에는 모든 보조 데이터베이스를 먼저 업그레이드한 다음, 주 데이터베이스를 업그레이드하는 것이 좋습니다. 다운그레이드하는 경우에는 반대 순서로 주 데이터베이스를 먼저 다운그레이드한 다음, 모든 보조 데이터베이스를 다운그레이드합니다. 데이터베이스를 다른 서비스 계층으로 업그레이드하거나 다운그레이드할 때 이 권장 사항이 적용됩니다.

이 순서는 더 낮은 SKU의 보조가 오버로드되고 업그레이드 또는 다운그레이드 프로세스 중에 다시 시드되어야 하는 문제를 방지하기 위해 특히 권장됩니다. 주에 대한 모든 읽기/쓰기 워크로드의 영향을 희생하여 주를 읽기 전용으로 설정해 문제를 방지할 수도 있습니다.

> [!NOTE]
> 장애 조치 그룹 구성의 일부로 보조 데이터베이스를 만든 경우 보조 데이터베이스를 다운그레이드하지 않는 것이 좋습니다. 이렇게 하면 장애 조치가 활성화된 후 데이터 계층에서 일반 워크로드를 처리할 수 있을 만큼 충분한 용량을 갖출 수 있습니다.

## <a name="preventing-the-loss-of-critical-data"></a>중요한 데이터 손실 방지

광역 네트워크의 높은 대기 시간으로 인해 연속 복사는 비동기 복제 메커니즘을 사용합니다. 비동기 복제를 수행하면 오류가 발생하는 경우에 일부 데이터 손실은 불가피합니다. 그러나 일부 애플리케이션은 데이터 손실이 없어야 합니다. 이러한 중요한 업데이트를 보호하기 위해 애플리케이션 개발자는 트랜잭션을 커밋한 후 즉시 [sp_wait_for_database_copy_sync](/sql/relational-databases/system-stored-procedures/active-geo-replication-sp-wait-for-database-copy-sync) 시스템 프로시저를 호출할 수 있습니다. `sp_wait_for_database_copy_sync`를 호출하면 마지막으로 커밋된 트랜잭션이 보조 데이터베이스로 전송될 때까지 호출 스레드가 차단됩니다. 그러나 전송된 트랜잭션이 보조 데이터베이스에서 재생 및 커밋될 때까지 기다리지 않습니다. `sp_wait_for_database_copy_sync`는 특정 연속 복사 링크로 범위가 지정됩니다. 주 데이터베이스에 대한 연결 권한이 있는 모든 사용자는 이 프로시저를 호출할 수 있습니다.

> [!NOTE]
> `sp_wait_for_database_copy_sync`는 장애 복구 후 데이터 손실을 방지하지만, 읽기 액세스를 위한 전체 동기화를 보장하지는 않습니다. `sp_wait_for_database_copy_sync` 프로시저를 호출하면 상당 시간 지연될 수 있으며, 호출 시 트랜잭션 로그의 크기에 따라 달라집니다.

## <a name="failover-groups-and-point-in-time-restore"></a>장애 조치(failover) 그룹 및 지정 시간 복원

장애 조치(failover) 그룹과 함께 지정 시간 복원을 사용하는 방법에 대한 자세한 내용은 [PITR(지정 시간 복구)](recovery-using-backups.md#point-in-time-restore)을 참조하세요.

## <a name="limitations-of-failover-groups"></a>장애 조치 그룹의 제한 사항

다음과 같은 제한 사항을 고려해야 합니다.

- 동일한 Azure 지역의 두 서버 또는 인스턴스 간에 장애 조치(failover) 그룹을 만들 수 없습니다.
- 장애 조치 그룹의 이름을 바꿀 수 없습니다. 그룹을 삭제하고 다른 이름으로 다시 만들어야 합니다.
- 장애 조치 그룹의 인스턴스에는 데이터베이스 이름 바꾸기가 지원되지 않습니다. 데이터베이스의 이름을 바꾸려면 장애 조치 그룹을 일시적으로 삭제해야 합니다.
- 시스템 데이터베이스는 장애 조치(failover) 그룹의 보조 인스턴스에 **복제되지 않습니다.** 따라서 시스템 데이터베이스의 개체에 의존하는 시나리오에서는 보조 인스턴스에서 개체를 수동으로 만들고 주 인스턴스에서 변경한 후 수동으로 동기화를 유지해야 합니다. 유일한 예외는 장애 조치(failover) 그룹을 만드는 동안 보조 인스턴스에 자동으로 복제되는 SQL Managed Instance 대한 SMK(서비스 마스터 키)입니다. 그러나 주 인스턴스에서 SMK의 이후 변경 사항은 보조 인스턴스로 복제되지 않습니다.
- 인스턴스가 자동 장애 조치(failover) 그룹에 참여하는 경우 장애 조치(failover) 그룹 수신기 엔드포인트를 통해 설정된 연결에는 인스턴스의 연결 [형식](../managed-instance/connection-types-overview.md) 변경이 적용되지 않습니다. 연결 형식 변경이 적용하려면 자동 장애 조치(failover) 그룹을 일시적으로 삭제하고 다시 만들어야 합니다.

## <a name="programmatically-managing-failover-groups"></a>프로그래밍 방식으로 장애 조치(failover) 그룹 관리

앞에서 설명한 대로, 자동 장애 조치(failover) 그룹과 활성 지역 복제는 Azure PowerShell 및 REST API를 사용하여 프로그래밍 방식으로 관리할 수도 있습니다. 다음 표는 사용 가능한 명령의 집합을 보여 줍니다. 활성 지역 복제는 관리를 위해 [Azure SQL Database REST API](/rest/api/sql/) 및 [Azure PowerShell cmdlet](/powershell/azure/)을 비롯한 Azure Resource Manager API 세트를 포함합니다. 이러한 API는 리소스 그룹을 사용해야 하며 Azure RBAC(Azure 역할 기반 액세스 제어)를 지원합니다. 액세스 역할을 구현하는 방법에 관한 자세한 내용은 [Azure RBAC(Azure 역할 기반 액세스 제어)](../../role-based-access-control/overview.md)를 참조하세요.

### <a name="manage-sql-database-failover"></a>SQL Database 장애 조치 관리

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

| Cmdlet | 설명 |
| --- | --- |
| [New-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/new-azsqldatabasefailovergroup) |장애 조치 그룹을 만들고 주 및 보조 서버 모두에 등록합니다|
| [Remove-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/remove-azsqldatabasefailovergroup) | 서버에서 장애 조치 그룹 제거 |
| [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) | 장애 조치 그룹의 구성 검색 |
| [Set-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/set-azsqldatabasefailovergroup) |장애 조치 그룹의 구성 수정 |
| [Switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup) | 장애 조치 그룹을 보조 서버로 장애 조치하도록 트리거 |
| [Add-AzSqlDatabaseToFailoverGroup](/powershell/module/az.sql/add-azsqldatabasetofailovergroup)|장애 조치 그룹에 하나 이상의 데이터베이스 추가|

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

| 명령 | 설명 |
| --- | --- |
| [az sql failover-group create](/cli/azure/sql/failover-group#az_sql_failover_group_create) |장애 조치 그룹을 만들고 주 및 보조 서버 모두에 등록합니다|
| [az sql failover-group delete](/cli/azure/sql/failover-group#az_sql_failover_group_delete) | 서버에서 장애 조치 그룹 제거 |
| [az sql failover-group show](/cli/azure/sql/failover-group#az_sql_failover_group_show) | 장애 조치 그룹 구성 검색 |
| [az sql failover-group update](/cli/azure/sql/failover-group#az_sql_failover_group_update) |장애 조치 그룹의 구성을 수정하거나 하나 이상의 데이터베이스를 장애 조치 그룹에 추가|
| [az sql failover-group set-primary](/cli/azure/sql/failover-group#az_sql_failover_group_set_primary) | 장애 조치 그룹을 보조 서버로 장애 조치하도록 트리거 |

# <a name="rest-api"></a>[Rest API](#tab/rest-api)

| API | 설명 |
| --- | --- |
| [장애 조치(failover) 그룹 만들기 또는 업데이트](/rest/api/sql/failovergroups/createorupdate) | 장애 조치(failover) 그룹을 만들거나 업데이트합니다. |
| [장애 조치(failover) 그룹 삭제](/rest/api/sql/failovergroups/delete) | 서버에서 장애 조치 그룹 제거 |
| [장애 조치(failover)(계획됨)](/rest/api/sql/failovergroups/failover) | 전체 데이터 동기화를 사용하여 현재 주 서버에서 보조 서버로의 장애 조치를 트리거합니다.|
| [장애 조치(failover)로 인한 데이터 손실 허용](/rest/api/sql/failovergroups/forcefailoverallowdataloss) | 데이터를 동기화하지 않고 현재 주 서버에서 보조 서버로의 장애 조치를 트리거합니다. 이 작업으로 인해 데이터가 손실될 수 있습니다. |
| [장애 조치 그룹 가져오기](/rest/api/sql/failovergroups/get) | 장애 조치 그룹의 구성을 검색합니다. |
| [서버별 장애 조치(failover) 그룹 나열](/rest/api/sql/failovergroups/listbyserver) | 서버에 있는 장애 조치 그룹을 나열합니다. |
| [장애 조치(failover) 그룹 업데이트](/rest/api/sql/failovergroups/update) | 장애 조치 그룹의 구성을 업데이트합니다. |

---

### <a name="manage-sql-managed-instance-failover"></a>SQL Managed Instance 장애 조치 관리


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

| Cmdlet | 설명 |
| --- | --- |
| [New-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/new-azsqldatabaseinstancefailovergroup) |장애 조치 그룹을 만들고 주 인스턴스와 보조 인스턴스 모두에 등록합니다|
| [Set-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/set-azsqldatabaseinstancefailovergroup) |장애 조치 그룹의 구성 수정|
| [Get-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/get-azsqldatabaseinstancefailovergroup) |장애 조치 그룹의 구성 검색|
| [Switch-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/switch-azsqldatabaseinstancefailovergroup) |장애 조치 그룹을 보조 인스턴스로 장애 조치하도록 트리거|
| [Remove-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/remove-azsqldatabaseinstancefailovergroup) | 장애 조치(failover) 그룹을 제거합니다.|


# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

| 명령 | 설명 |
| --- | --- |
| [az sql failover-group create](/cli/azure/sql/failover-group#az_sql_failover_group_create) |장애 조치 그룹을 만들고 주 및 보조 서버 모두에 등록합니다|
| [az sql failover-group delete](/cli/azure/sql/failover-group#az_sql_failover_group_delete) | 서버에서 장애 조치 그룹 제거 |
| [az sql failover-group show](/cli/azure/sql/failover-group#az_sql_failover_group_show) | 장애 조치 그룹 구성 검색 |
| [az sql failover-group update](/cli/azure/sql/failover-group#az_sql_failover_group_update) |장애 조치 그룹의 구성을 수정하거나 하나 이상의 데이터베이스를 장애 조치 그룹에 추가|
| [az sql failover-group set-primary](/cli/azure/sql/failover-group#az_sql_failover_group_set_primary) | 장애 조치 그룹을 보조 서버로 장애 조치하도록 트리거 |

# <a name="rest-api"></a>[Rest API](#tab/rest-api)

| API | 설명 |
| --- | --- |
| [장애 조치(failover) 그룹 만들기 또는 업데이트](/rest/api/sql/instancefailovergroups/createorupdate) | 장애 조치 그룹의 구성을 만들거나 업데이트 |
| [장애 조치(failover) 그룹 삭제](/rest/api/sql/instancefailovergroups/delete) | 인스턴스에서 장애 조치 그룹 제거 |
| [장애 조치(failover)(계획됨)](/rest/api/sql/instancefailovergroups/failover) | 전체 데이터 동기화를 사용하여 현재 주 인스턴스에서 이 인스턴스로의 장애 조치를 트리거합니다. |
| [장애 조치(failover)로 인한 데이터 손실 허용](/rest/api/sql/instancefailovergroups/forcefailoverallowdataloss) | 데이터를 동기화하지 않고 현재 주 인스턴스에서 보조 인스턴스로의 장애 조치를 트리거합니다. 이 작업으로 인해 데이터가 손실될 수 있습니다. |
| [장애 조치 그룹 가져오기](/rest/api/sql/instancefailovergroups/get) | 장애 조치 그룹의 구성을 검색합니다. |
| [장애 조치(failover) 그룹 나열 - 위치별 목록](/rest/api/sql/instancefailovergroups/listbylocation) | 위치에 있는 장애 조치(failover) 그룹을 나열합니다. |

---

## <a name="next-steps"></a>다음 단계

- 기본 자습서는 다음을 참조하세요.
  - [장애 조치 그룹에 SQL Database 추가](failover-group-add-single-database-tutorial.md)
  - [장애 조치(failover) 그룹에 탄력적 풀 추가](failover-group-add-elastic-pool-tutorial.md)
  - [장애 조치 그룹에 SQL Managed Instance 추가](../managed-instance/failover-group-add-instance-tutorial.md)
- 샘플 스크립트에 대해서는 다음을 참조하세요.
  - [PowerShell을 사용하여 Azure SQL Database의 활성 지역 복제 구성](scripts/setup-geodr-and-failover-database-powershell.md)
  - [PowerShell을 사용하여 Azure SQL Database에 풀링된 데이터베이스에 대한 활성 지역 복제 구성](scripts/setup-geodr-and-failover-elastic-pool-powershell.md)
  - [PowerShell을 사용하여 Azure SQL Database를 장애 조치 그룹에 추가](scripts/add-database-to-failover-group-powershell.md)
- 비즈니스 연속성의 개요 및 시나리오를 보려면 [비즈니스 연속성 개요](business-continuity-high-availability-disaster-recover-hadr-overview.md)
- Azure SQL Database 자동화 백업에 대한 자세한 내용은 [SQL Database 자동화 백업](automated-backups-overview.md)을 참조하세요.
- 복구를 위해 자동화된 백업을 사용하는 방법을 알아보려면 [서비스에서 시작한 백업에서 데이터베이스 복원](recovery-using-backups.md)을 참조하세요.
- 새로운 주 서버 및 데이터베이스의 인증 요구 사항에 대해 알아보려면 [재해 복구 후의 SQL Database 보안](active-geo-replication-security-configure.md)을 참조하세요.
