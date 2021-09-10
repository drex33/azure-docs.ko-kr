---
title: Azure VM의 SQL Server 백업에 대한 Azure Backup 지원 매트릭스
description: Azure Backup 서비스를 사용하여 Azure VM에서 SQL Server를 백업할 때의 지원 설정 및 제한 사항에 대한 요약을 제공합니다.
ms.topic: conceptual
ms.date: 06/07/2021
ms.custom: references_regions
ms.openlocfilehash: 678a3e63205d986681016fe64971e9bd874f9c71
ms.sourcegitcommit: 0af634af87404d6970d82fcf1e75598c8da7a044
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/15/2021
ms.locfileid: "112119939"
---
# <a name="support-matrix-for-sql-server-backup-in-azure-vms"></a>Azure VM의 SQL Server Backup에 대한 지원 매트릭스

Azure Backup을 사용하여 Microsoft Azure 클라우드 플랫폼에서 호스트되는 Azure VM의 SQL Server 데이터베이스를 백업할 수 있습니다. 이 문서에는 Azure VM의 SQL Server Backup에 대한 시나리오 및 배포에 대한 일반 지원 설정과 제한 사항이 요약되어 있습니다.

## <a name="scenario-support"></a>시나리오 지원

**지원** | **세부 정보**
--- | ---
**지원되는 배포** | SQL Marketplace Azure VM 및 비 Marketplace VM(SQL Server가 수동 설치됨)이 지원됩니다.
**지원되는 지역** | SQL Server 데이터베이스용 Azure Backup은 프랑스 남부(FRS), 영국 북부(UKN), 영국 남부 2(UKS2), 미국 아이오와(UGI) 및 독일(Black Forest)을 제외한 모든 지역에서 사용할 수 있습니다.
**지원되는 운영 체제** | Windows Server 2019, Windows Server 2016, Windows Server 2012, Windows Server 2008 R2 SP1 <br/><br/> Linux는 현재 지원되지 않습니다.
**지원되는 SQL Server 버전** | [제품 수명 주기 페이지 검색](https://support.microsoft.com/lifecycle/search?alpha=SQL%20server%202017)에 설명된 SQL Server 2019, SQL Server 2017, [제품 수명 주기 페이지 검색](https://support.microsoft.com/lifecycle/search?alpha=SQL%20server%202016%20service%20pack)에 설명된 SQL Server 2016 및 SP, SQL Server 2014, SQL Server 2012, SQL Server 2008 R2, SQL Server 2008 <br/><br/> Enterprise, Standard, Web, Developer, Express<br><br>Express Local DB 버전은 지원되지 않습니다.
**지원되는 .NET 버전** | VM에 설치된 .NET Framework 4.5.2 이상

## <a name="feature-considerations-and-limitations"></a>기능 고려 사항 및 제한 사항

|설정  |최대 제한 |
|---------|---------|
|서버 및 자격 증명 모음에서 보호할 수 있는 데이터베이스 수    |   2000      |
|지원되는 데이터베이스 크기(이 크기를 넘을 경우 성능 문제가 발생할 수 있음)   |   6TB*      |
|데이터베이스에서 지원되는 파일 수    |   1000      |

_* 데이터베이스 크기 제한은 지원되는 데이터 전송 속도와 백업 시간 제한 구성에 따라 달라지며, 고정된 한도가 아닙니다. [백업 처리량 성능](#backup-throughput-performance)에 대해 자세히 알아보세요._

* SQL Server 백업은 Azure Portal 또는 **PowerShell** 에서 구성할 수 있습니다. CLI는 지원되지 않습니다.
* 솔루션은 Azure Resource Manager VM과 클래식 VM의 두 종류 [배포](../azure-resource-manager/management/deployment-models.md)에서 모두 지원됩니다.
* 모든 백업 형식(전체/차등/로그) 및 복구 모델(단순/전체/대량 로그)이 지원됩니다.
* **읽기 전용** 데이터베이스의 경우: 전체 및 복사 전용 전체 백업만 지원되는 백업 형식입니다.
* SQL 네이티브 압축은 백업 정책에서 사용자가 명시적으로 사용하도록 설정한 경우에만 지원됩니다. Azure Backup은 사용자가 설정한 대로 이 컨트롤의 값에 따라 COMPRESSION/NO_COMPRESSION 절을 사용하여 인스턴스 수준 기본값을 재정의합니다.
* TDE 지원 데이터베이스 백업이 지원됩니다. TDE 암호화된 데이터베이스를 다른 SQL Server로 복원하려면, 먼저 [대상 서버로 인증서를 복원](/sql/relational-databases/security/encryption/move-a-tde-protected-database-to-another-sql-server)해야 합니다. SQL Server 2016 이상 버전에 대한 TDE 지원 데이터베이스의 백업 압축을 사용할 수 있지만 [여기](https://techcommunity.microsoft.com/t5/sql-server/backup-compression-for-tde-enabled-databases-important-fixes-in/ba-p/385593)에 설명된 대로 낮은 전송 크기만 사용할 수 있습니다.
* 미러 데이터베이스와 데이터베이스 스냅샷에 대한 백업 및 복원 작업은 지원되지 않습니다.
* SQL Server **FCI(장애 조치(Failover) 클러스터 인스턴스)** 는 지원되지 않습니다.
* 백업 솔루션을 2개 이상 사용하여 독립 실행형 SQL Server 인스턴스 또는 SQL Always On 가용성 그룹을 백업하면 오류가 발생할 수 있습니다. 그렇게 하지 마세요. 같은 솔루션 또는 다른 솔루션을 사용하여 한 가용성 그룹의 두 노드를 개별적으로 백업해도 오류가 발생할 수 있습니다.
* 가용성 그룹을 구성할 때 백업은 몇 가지 요소에 따라 다른 노드에서 수행됩니다. 아래는 가용성 그룹에 대한 백업 동작을 요약한 것입니다.

### <a name="back-up-behavior-with-always-on-availability-groups"></a>Always On 가용성 그룹을 사용하여 백업 동작

AG(가용성 그룹)의 한 노드에서만 백업을 구성하는 것이 좋습니다. 항상 주 노드와 동일한 지역에 백업을 구성합니다. 즉, 백업을 구성하는 지역에 항상 주 노드가 있어야 합니다. AG의 모든 노드가 백업이 구성되어 있는 곳과 동일한 지역에 있는 경우 문제가 없습니다.

#### <a name="for-cross-region-ag"></a>지역 간 AG

* 백업 기본 설정에 관계 없이 백업이 구성되어 있는 곳과 동일한 지역에 있는 노드에서만 백업이 일어납니다. 지역 간 백업이 지원되지 않기 때문입니다. 노드가 두 개뿐이고 보조 노드가 다른 지역에 있는 경우, 백업은 계속 주 노드에서 발생합니다(백업 기본 설정이 ‘보조만’이 아닌 경우에 한함).
* 백업이 구성되어 있는 곳과 다른 지역으로 노드 장애 조치(failover)가 발생하는 경우 장애 조치 지역의 노드에서 백업이 실패합니다.

백업 기본 설정 및 백업 유형(전체/차등/로그/복사 전용 전체)에 따라 특정 노드(주/보조)에서 백업이 수행됩니다.

#### <a name="backup-preference-primary"></a>백업 기본 설정: 기본

**백업 유형** | **Node**
--- | ---
전체 | 기본
차등 | 기본
로그 |  기본
복사 전용 전체 |  기본

#### <a name="backup-preference-secondary-only"></a>백업 기본 설정: 보조만

**백업 유형** | **Node**
--- | ---
전체 | 기본
차등 | 기본
로그 |  보조
복사 전용 전체 |  보조

#### <a name="backup-preference-secondary"></a>백업 기본 설정: 보조

**백업 유형** | **Node**
--- | ---
전체 | 기본
차등 | 기본
로그 |  보조
복사 전용 전체 |  보조

#### <a name="no-backup-preference"></a>백업 기본 설정 없음

**백업 유형** | **Node**
--- | ---
전체 | 기본
차등 | 기본
로그 |  보조
복사 전용 전체 |  보조

## <a name="backup-throughput-performance"></a>백업 처리량 성능

Azure Backup은 대형 SQL 데이터베이스(500GB)의 전체 및 차등 백업에 대해 일률적 데이터 전송 속도인 200Mbps를 지원합니다. 최적의 성능을 활용하려면 다음을 확인하세요.

- 기본 VM(데이터베이스를 호스트하는 SQL Server 인스턴스 포함)은 필수 네트워크 처리량으로 구성됩니다. VM의 최대 처리량이 200Mbps 미만인 경우 Azure Backup는 최적의 속도로 데이터를 전송할 수 없습니다.<br>또한 데이터베이스 파일이 포함된 디스크에는 충분한 처리량이 프로비전되어야 합니다. Azure VM의 디스크 처리량 및 성능에 대해 [자세히 알아보세요](../virtual-machines/disks-performance.md). 
- VM에서 실행되는 프로세스는 VM 대역폭을 소비하지 않습니다. 
- 백업 일정은 데이터베이스 하위 집합 전체에 분산됩니다. VM에서 동시에 실행되는 여러 백업은 백업 간에 네트워크 소비량을 공유합니다. 동시 백업 수를 제어하는 방법에 대해 [자세히 알아보세요](faq-backup-sql-server.yml#can-i-control-how-many-concurrent-backups-run-on-the-sql-server-).

>[!NOTE]
> [자세한 Resource Planner를 다운로드](https://download.microsoft.com/download/A/B/5/AB5D86F0-DCB7-4DC3-9872-6155C96DE500/SQL%20Server%20in%20Azure%20VM%20Backup%20Scale%20Calculator.xlsx)하여 VM 리소스, 대역폭 및 백업 정책에 따라 서버별로 권장되는 보호된 데이터베이스의 대략적인 수를 계산합니다.

## <a name="next-steps"></a>다음 단계

Azure VM에서 실행 중인 [SQL Server 데이터베이스를 백업](backup-azure-sql-database.md)하는 방법을 알아봅니다.