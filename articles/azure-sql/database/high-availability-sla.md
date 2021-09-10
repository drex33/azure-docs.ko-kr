---
title: 고가용성
titleSuffix: Azure SQL Database and SQL Managed Instance
description: Azure SQL Database 및 SQL Managed Instance 서비스의 고가용성 기능과 특징에 대해 알아봅니다.
services: sql-database
ms.service: sql-db-mi
ms.subservice: high-availability
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: emlisa
ms.author: emlisa
ms.reviewer: mathoma, emlisa
ms.date: 10/28/2020
ms.openlocfilehash: 97085073a566d525eedeb6bd2e72273dad36e1d0
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122535677"
---
# <a name="high-availability-for-azure-sql-database-and-sql-managed-instance"></a>Azure SQL Database 및 SQL Managed Instance에 대한 고가용성
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Azure SQL Database 및 SQL Managed Instance에서 고가용성 아키텍처의 목표는 유지 관리 작업 및 가동 중단의 영향에 대해 걱정하지 않고 데이터베이스가 최소 99.99%의 시간 동안 가동 및 실행되도록 보장하는 것입니다. (다른 계층의 특정 SLA에 대한 자세한 내용은 [Azure SQL Database 및 SQL Managed Instance에 대한 SLA](https://azure.microsoft.com/support/legal/sla/azure-sql-database)를 참조하세요.) Azure는 패치, 백업, Windows 및 Azure SQL 업그레이드와 같은 중요한 서비스 작업뿐만 아니라 기본 하드웨어, 소프트웨어 또는 네트워크 오류와 같은 계획되지 않은 이벤트도 자동으로 처리합니다.  Azure SQL Database의 기본 데이터베이스가 패치되거나 장애 조치(failover)될 때 앱에서 [다시 시도 논리를 사용](develop-overview.md#resiliency)하는 경우 가동 중지 시간의 영향은 별로 크지 않습니다. SQL Database 및 SQL Managed Instance는 가장 심각한 상황에서도 빠르게 복구할 수 있으므로 데이터를 항상 사용할 수 있습니다.

고가용성 솔루션은 커밋된 데이터가 오류로 인해 손실되지 않고, 유지 관리 작업이 워크로드에 영향을 주지 않으며, 데이터베이스가 소프트웨어 아키텍처에서 단일 실패 지점이 되지 않도록 설계되었습니다. 데이터베이스를 업그레이드하거나 유지 관리하는 동안 워크로드를 중지해야 하는 유지 관리 기간이나 가동 중지 시간이 없습니다.

다음 두 가지의 고가용성 아키텍처 모델이 있습니다.

- **표준 가용성 모델** - 컴퓨팅과 스토리지의 분리를 기반으로 합니다.  원격 스토리지 계층의 고가용성 및 안정성을 기반으로 합니다. 이 아키텍처는 유지 관리 작업 중에 일부 성능 저하를 허용할 수 있는 예산 중심 비즈니스 애플리케이션을 대상으로 합니다.
- **프리미엄 가용성 모델** - 데이터베이스 엔진 프로세스 클러스터를 기반으로 합니다. 항상 사용 가능한 데이터베이스 엔진 노드의 쿼럼이 있다는 사실을 기반으로 합니다. 이 아키텍처는 높은 IO 성능, 높은 트랜잭션 속도를 갖춘 중요 업무용 애플리케이션을 대상으로 하며, 유지 관리 작업 중에 워크로드에 미치는 성능 영향을 최소화합니다.

SQL Database와 SQL Managed Instance는 모두 안정적인 최신 버전의 SQL Server 데이터베이스 엔진과 Windows 운영 체제에서 실행되며, 대부분의 사용자는 업그레이드가 지속적으로 수행된다는 사실을 인식하지 못합니다.

## <a name="basic-standard-and-general-purpose-service-tier-locally-redundant-availability"></a>기본, 표준 및 범용 서비스 계층의 로컬 중복 가용성

기본, 표준 및 범용 서비스 계층은 표준 가용성 아키텍처를 서버리스 컴퓨팅과 프로비전된 컴퓨팅 모두에 활용합니다. 다음 그림에서는 컴퓨팅 레이어와 스토리지 레이어가 분리된 서로 다른 4개의 노드를 보여 줍니다.

![컴퓨팅과 스토리지의 분리](./media/high-availability-sla/general-purpose-service-tier.png)

표준 가용성 모델에는 다음 두 개의 레이어가 포함됩니다.

- 상태 비저장 컴퓨팅 레이어 - `sqlservr.exe` 프로세스를 실행하고, 일시적이고 캐시된 데이터만 포함합니다(예: TempDB, 연결된 SSD의 모델 데이터베이스, 메모리의 계획 캐시, 버퍼 풀 및 columnstore 풀). 이 상태 비저장 노드는 `sqlservr.exe`를 초기화하고, 노드 상태를 제어하며, 필요한 경우 다른 노드로 장애 조치(failover)를 수행하는 Azure Service Fabric을 통해 작동합니다.
- 상태 저장 데이터 레이어 - Azure Blob 스토리지에 저장된 데이터베이스 파일(.mdf/.ldf)을 포함합니다. Azure Blob 스토리지에는 기본 제공되는 데이터 가용성 및 중복성 기능이 있습니다. `sqlservr.exe` 프로세스가 중단되더라도 로그 파일 또는 데이터 파일의 모든 레코드가 유지되도록 합니다.

데이터베이스 엔진 또는 운영 체제가 업그레이드되거나 오류가 검색될 때마다 Azure Service Fabric에서 상태 비저장 `sqlservr.exe` 프로세스를 사용 가능한 용량이 충분한 다른 상태 비저장 컴퓨팅 노드로 이동합니다. Azure Blob 스토리지의 데이터는 이동의 영향을 받지 않으며, 데이터/로그 파일은 새로 초기화된 `sqlservr.exe` 프로세스에 연결됩니다. 이 프로세스는 99.99%의 가용성을 보장하지만 새 `sqlservr.exe` 프로세스가 콜드 캐시로 시작되므로 전환 중에 과도한 워크로드로 인해 성능이 약간 저하될 수 있습니다.

## <a name="general-purpose-service-tier-zone-redundant-availability-preview"></a>범용 서비스 계층의 영역 중복 가용성(미리 보기)

범용 서비스 계층에 대한 영역 중복 구성은 서버리스 컴퓨팅과 프로비전된 컴퓨팅 모두에 제공됩니다. 이 구성은 [Azure 가용성 영역](../../availability-zones/az-overview.md)  을 활용하여 Azure 지역 내의 여러 물리적 위치에서 데이터베이스를 복제합니다.영역 중복성을 선택하면 애플리케이션 논리를 변경하지 않고도 신규 및 기존 서버리스 및 프로비전된 단일 범용 데이터베이스 및 탄력적 풀을 훨씬 더 큰 오류 집합(심각한 데이터 센터 중단 포함)으로 복원할 수 있습니다.

범용 계층에 대한 영역 중복 구성에는 다음 두 개의 레이어가 있습니다.  

- 상태 저장 데이터 레이어 - ZRS(영역 중복 스토리지)에 저장된 데이터베이스 파일(.mdf/.ldf)을 포함합니다. [ZRS](../../storage/common/storage-redundancy.md)를 사용하면 데이터 및 로그 파일이 물리적으로 격리된 세 개의 Azure 가용성 영역에 동기적으로 복사됩니다.
- 상태 비저장 컴퓨팅 레이어 - sqlservr.exe 프로세스를 실행하고, 일시적이고 캐시된 데이터만 포함합니다(예: TempDB, 연결된 SSD의 모델 데이터베이스, 메모리의 계획 캐시, 버퍼 풀 및 columnstore 풀). 이 상태 비저장 노드는 sqlservr.exe를 초기화하고, 노드 상태를 제어하며, 필요한 경우 다른 노드로 장애 조치(failover)를 수행하는 Azure Service Fabric을 통해 작동합니다. 영역 중복 서버리스 및 프로비전된 범용 데이터베이스의 경우 장애 조치(failover)를 위해 여유 용량이 있는 노드를 다른 가용성 영역에서 쉽게 사용할 수 있습니다.

다음 다이어그램에서는 범용 서비스 계층에 대한 고가용성 아키텍처의 영역 중복 버전을 보여 줍니다.

![범용 서비스 계층에 대한 영역 중복 구성](./media/high-availability-sla/zone-redundant-for-general-purpose.png)

> [!IMPORTANT]
> 영역 중복 구성은 5세대 컴퓨팅 하드웨어를 선택한 경우에만 사용할 수 있습니다. 이 기능은 SQL Managed Instance에서 사용할 수 없습니다. 서버리스 및 프로비전된 범용 계층에 대한 영역 중복 구성은 미국 동부, 미국 동부 2, 미국 서부 2, 북유럽, 서유럽, 동남아시아, 오스트레일리아 동부, 일본 동부, 영국 남부 및 프랑스 중부 지역에서만 사용할 수 있습니다.

> [!NOTE]
> 80개 vcore 크기의 범용 데이터베이스는 영역 중복 구성으로 인해 성능이 저하될 수 있습니다. 또한 백업, 복원, 데이터베이스 복사, Geo-DR(지역 재해 복구) 관계 설정 및 중요 비즈니스용에서 범용으로 영역 중복 데이터베이스 다운그레이드와 같은 작업에서는 1TB보다 큰 단일 데이터베이스의 성능이 저하될 수 있습니다. 자세한 내용은 [데이터베이스 스케일링에 대한 대기 시간 설명서](single-database-scale.md)를 참조하세요.
> 
> [!NOTE]
> 미리 보기는 예약 인스턴스에서 다루지 않습니다.

## <a name="premium-and-business-critical-service-tier-locally-redundant-availability"></a>프리미엄 및 중요 비즈니스용 서비스 계층의 로컬 중복 가용성

프리미엄 및 중요 비즈니스용 서비스 계층은 단일 노드에서 컴퓨팅 리소스(`sqlservr.exe` 프로세스)와 스토리지(로컬로 연결된 SSD)를 통합하는 프리미엄 가용성 모델을 활용합니다. 고가용성은 컴퓨팅과 스토리지를 모두 추가 노드에 복제하여 3~4개 노드 클러스터를 만듦으로써 달성됩니다.

![데이터베이스 엔진 노드의 클러스터](./media/high-availability-sla/business-critical-service-tier.png)

기본 데이터베이스 파일(.mdf/.ldf)은 연결된 SSD 스토리지에 배치되어 매우 짧은 대기 시간 IO를 워크로드에 제공합니다. 고가용성은 SQL Server [Always On 가용성 그룹](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server)과 비슷한 기술을 사용하여 구현됩니다. 클러스터에는 읽기/쓰기 고객 워크로드에 액세스할 수 있는 단일 주 복제본 및 데이터 복사본을 포함하는 최대 3개의 보조 복제본(컴퓨팅 및 스토리지)이 포함됩니다. 주 노드는 변경 내용을 순서대로 보조 노드에 계속 푸시하고, 각 트랜잭션을 커밋하기 전에 데이터가 하나 이상의 보조 복제본에 동기화되도록 합니다. 이 프로세스는 어떤 이유로든 주 노드가 중단되는 경우 장애 조치(failover)할 수 있는 완전히 동기화된 노드가 항상 있도록 보장합니다. 장애 조치(failover)는 Azure Service Fabric에서 시작됩니다. 보조 복제본이 새 주 노드가 되면 클러스터에 충분한 노드(쿼럼 집합)가 있도록 하기 위해 다른 보조 복제본이 만들어집니다. 장애 조치(failover)가 완료되면 Azure SQL 연결이 자동으로 새 주 노드로 리디렉션됩니다.

추가 혜택으로 프리미엄 가용성 모델에는 읽기 전용 Azure SQL 연결을 보조 복제본 중 하나로 리디렉션하는 기능이 포함됩니다. 이 기능을 [읽기 스케일 아웃](read-scale-out.md)이라고 합니다. 주 복제본에서 분석 워크로드와 같은 읽기 전용 작업을 오프로드하기 위해 추가 비용 없이 100% 추가 컴퓨팅 용량을 제공합니다.

## <a name="premium-and-business-critical-service-tier-zone-redundant-availability"></a>프리미엄 및 중요 비즈니스용 서비스 계층의 영역 중복 가용성 

기본적으로 프리미엄 가용성 모델에 대한 노드 클러스터는 동일한 데이터 센터에 만들어집니다. [Azure 가용성 영역](../../availability-zones/az-overview.md)을 도입하는 경우 SQL Database에서 중요 비즈니스용 데이터베이스의 다른 복제본을 동일한 지역의 다른 가용성 영역에 배치할 수 있습니다. 단일 실패 지점을 제거하기 위해 제어 링은 세 개의 GW(게이트웨이 링)로 여러 영역에 걸쳐 복제됩니다. 특정 게이트웨이 링에 대한 라우팅은 [ATM(Azure Traffic Manager)](../../traffic-manager/traffic-manager-overview.md)에서 제어됩니다. 프리미엄 또는 중요 비즈니스용 서비스 계층의 영역 중복 구성은 데이터베이스 중복성을 추가로 만들지 않으므로 추가 비용 없이 사용하도록 설정할 수 있습니다. 영역 중복 구성을 선택하면 애플리케이션 논리를 변경하지 않고도 훨씬 프리미엄 또는 중요 비즈니스용 데이터베이스를 더 큰 오류 집합(심각한 데이터 센터 중단 포함)으로 복원할 수 있습니다. 기존 프리미엄 또는 중요 비즈니스용 데이터베이스 또는 풀을 영역 중복 구성으로 변환할 수도 있습니다.

영역 중복 데이터베이스에는 복제본이 서로 약간 떨어져 있는 서로 다른 데이터 센터에 있으므로 네트워크 대기 시간이 증가하면 커밋 시간이 증가하고, 이에 따라 일부 OLTP 워크로드의 성능에 영향을 줄 수 있습니다. 언제든지 영역 중복 설정을 사용하지 않도록 설정하여 단일 영역 구성으로 돌아갈 수 있습니다. 이 프로세스는 일반 서비스 계층 업그레이드와 비슷한 온라인 작업입니다. 프로세스가 완료되면 데이터베이스 또는 풀이 영역 중복 링에서 단일 영역 링으로 또는 그 반대로 마이그레이션됩니다.

> [!IMPORTANT]
> 중요 비즈니스용 계층을 사용하는 경우 영역 중복 구성은 5세대 컴퓨팅 하드웨어를 선택한 경우에만 사용할 수 있습니다. 영역 중복 데이터베이스를 지원하는 지역에 대한 최신 정보는 [지역별 서비스 지원](../../availability-zones/az-region.md)을 참조하세요.

> [!NOTE]
> 이 기능은 SQL Managed Instance에서 사용할 수 없습니다.

다음 다이어그램에서는 고가용성 아키텍처의 영역 중복 버전을 보여 줍니다.

![고가용성 아키텍처 영역 중복](./media/high-availability-sla/zone-redundant-business-critical-service-tier.png)


## <a name="hyperscale-service-tier-availability"></a>하이퍼스케일 서비스 계층의 가용성

하이퍼스케일 서비스 계층 아키텍처는 [분산 기능 아키텍처](./service-tier-hyperscale.md#distributed-functions-architecture)에 설명되어 있으며, 현재 SQL Managed Instance가 아니라 SQL Database에서만 사용할 수 있습니다.

![하이퍼스케일 기능 아키텍처](./media/high-availability-sla/hyperscale-architecture.png)

하이퍼스케일의 가용성 모델에는 다음 4개의 레이어가 포함됩니다.

- 상태 비저장 컴퓨팅 레이어 - `sqlservr.exe` 프로세스를 실행하고, 일시적이고 캐시된 데이터만 포함합니다(예: 연결된 SSD의 비포함 RBPEX 캐시, TempDB, 모델 데이터베이스 등, 메모리의 계획 캐시, 버퍼 풀 및 columnstore 풀). 이 상태 비저장 레이어에는 주 컴퓨팅 복제본 및 장애 조치(failover) 대상으로 사용할 수 있는 여러 선택적 보조 컴퓨팅 복제본이 포함됩니다.
- 페이지 서버에서 구성된 상태 비저장 스토리지 레이어 - 이 레이어는 컴퓨팅 복제본에서 실행되는 `sqlservr.exe` 프로세스에 대한 분산 스토리지 엔진입니다. 각 페이지 서버에는 연결된 SSD의 포함 RBPEX 캐시 및 메모리에 캐시된 데이터 페이지와 같은 일시적이고 캐시된 데이터만 포함됩니다. 각 페이지 서버에는 부하 분산, 중복성 및 고가용성을 제공하기 위해 활성-활성 구성으로 쌍을 이루는 페이지 서버가 있습니다.
- 상태 저장 로그 스토리지 레이어 - 로그 서비스 프로세스를 실행하는 컴퓨팅 노드, 트랜잭션 로그 랜딩 존 및 트랜잭션 로그 장기 스토리지로 구성됩니다. 랜딩 존 및 장기 스토리지는 트랜잭션 로그에 대한 가용성과 [중복성](../../storage/common/storage-redundancy.md)을 제공하는 Azure Storage를 사용하여 커밋된 트랜잭션에 대한 데이터 내구성을 보장합니다.
- 상태 저장 레이어 - Azure Storage에 저장되고 페이지 서버에서 업데이트되는 데이터베이스 파일(.mdf/.ndf)을 포함합니다. 이 레이어는 Azure Storage의 데이터 가용성 및 [중복성](../../storage/common/storage-redundancy.md) 기능을 사용합니다. 이는 하이퍼스케일 아키텍처의 다른 레이어에 있는 프로세스가 중단되거나 컴퓨팅 노드가 실패하는 경우에도 데이터 파일의 모든 페이지가 유지되도록 보장합니다.

모든 하이퍼스케일 레이어의 컴퓨팅 노드는 각 노드의 상태를 제어하고 필요에 따라 사용 가능한 정상 노드에 대한 장애 조치(failover)를 수행하는 Azure Service Fabric에서 실행됩니다.

하이퍼스케일의 고가용성에 대한 자세한 내용은 [하이퍼스케일의 데이터베이스 고가용성](./service-tier-hyperscale.md#database-high-availability-in-hyperscale)을 참조하세요.


## <a name="accelerated-database-recovery-adr"></a>ADR(가속 데이터베이스 복구)

특히 장기 실행 트랜잭션이 있는 경우 [ADR(가속 데이터베이스 복구)](../accelerated-database-recovery.md)은 데이터베이스 가용성을 크게 향상시키는 새로운 데이터베이스 엔진 기능입니다. ADR은 현재 Azure SQL Database, Azure SQL Managed Instance 및 Azure Synapse Analytics에서 사용할 수 있습니다.

## <a name="testing-application-fault-resiliency"></a>애플리케이션 오류 복원력 테스트

고가용성은 데이터베이스 애플리케이션에 대해 투명하게 작동하는 SQL Database 및 SQL Managed Instance 플랫폼의 기본 부분입니다. 그러나 애플리케이션을 프로덕션 환경에 배포하기 전에 계획되거나 계획되지 않은 이벤트 중에 시작된 자동 장애 조치(failover) 작업이 애플리케이션에 어떤 영향을 미치는지 테스트해보고 싶을 수 있습니다. 특수 API를 호출하여 데이터베이스, 탄력적 풀 또는 관리되는 인스턴스를 다시 시작하여 장애 조치(failover)를 수동으로 트리거할 수 있습니다. 영역 중복 서버리스 또는 프로비전된 범용 데이터베이스 또는 탄력적 풀의 경우 API 호출로 인해 클라이언트 연결이 이전 주 데이터베이스의 가용성 영역과 다른 가용성 영역의 새 주 데이터베이스로 리디렉션됩니다. 따라서 장애 조치(failover)에서 기존 데이터베이스 세션에 미치는 영향을 테스트하는 것 외에도 네트워크 대기 시간의 변경으로 인해 엔드투엔드 성능이 변경되는지도 확인할 수 있습니다. 다시 시작 작업은 방해가 되고 많은 수의 이러한 작업으로 인해 플랫폼에서 스트레스가 발생할 수 있으므로 각 데이터베이스, 탄력적 풀 또는 관리되는 인스턴스에 대해 15분마다 하나의 장애 조치(failover) 호출만 허용됩니다.

장애 조치(failover)는 PowerShell, REST API 또는 Azure CLI를 사용하여 시작할 수 있습니다.

|배포 유형|PowerShell|REST API| Azure CLI|
|:---|:---|:---|:---|
|데이터베이스|[Invoke-AzSqlDatabaseFailover](/powershell/module/az.sql/invoke-azsqldatabasefailover)|[데이터베이스 장애 조치(failover)](/rest/api/sql/databases/failover)|[az rest](/cli/azure/reference-index#az_rest)를 사용하여 Azure CLI에서 REST API 호출을 호출할 수 있습니다.|
|탄력적 풀|[Invoke-AzSqlElasticPoolFailover](/powershell/module/az.sql/invoke-azsqlelasticpoolfailover)|[탄력적 풀 장애 조치(failover)](/javascript/api/@azure/arm-sql/elasticpools#failover_string__string__string__msRest_RequestOptionsBase)|[az rest](/cli/azure/reference-index#az_rest)를 사용하여 Azure CLI에서 REST API 호출을 호출할 수 있습니다.|
|관리되는 인스턴스|[Invoke-AzSqlInstanceFailover](/powershell/module/az.sql/Invoke-AzSqlInstanceFailover/)|[관리되는 인스턴스 - 장애 조치(failover)](/rest/api/sql/managed%20instances%20-%20failover/failover)|[az sql mi failover](/cli/azure/sql/mi/#az_sql_mi_failover)|

> [!IMPORTANT]
> 장애 조치(failover) 명령은 하이퍼스케일 데이터베이스의 읽기 가능한 보조 복제본에 사용할 수 없습니다.

## <a name="conclusion"></a>결론

Azure SQL Database 및 Azure SQL Managed Instance는 Azure 플랫폼과 긴밀하게 통합되는 기본 제공 고가용성 솔루션을 제공합니다. 이는 장애 검색 및 복구를 위한 Service Fabric, 데이터 보호를 위한 Azure Blob 스토리지 및 더 높은 내결함성을 위한 가용성 영역에 따라 달라집니다(문서의 앞부분에서 설명한 대로 Azure SQL Managed Instance에는 아직 적용되지 않음). 또한 SQL Database와 SQL Managed Instance는 복제 및 장애 조치(failover)를 위해 SQL Server 인스턴스의 Always On 가용성 그룹 기술을 활용합니다. 이러한 기술의 조합을 통해 애플리케이션에서 혼합 스토리지 모델의 이점을 완전히 실현하고 요구 사항이 가장 많은 SLA를 지원할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [Azure 가용성 영역](../../availability-zones/az-overview.md) 알아보기
- [Service Fabric](../../service-fabric/service-fabric-overview.md) 알아보기
- [Azure Traffic Manager](../../traffic-manager/traffic-manager-overview.md) 알아보기
- [SQL Managed Instance에서 수동 장애 조치(failover)를 시작하는 방법](../managed-instance/user-initiated-failover.md) 알아보기
- 고가용성 및 재해 복구의 추가 옵션은 [비즈니스 연속성](business-continuity-high-availability-disaster-recover-hadr-overview.md)을 참조하세요.
