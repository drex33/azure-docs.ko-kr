---
title: 'SQL Server를 Azure SQL Managed Instance로: 마이그레이션 가이드'
description: 이 가이드에서는 SQL Server 데이터베이스를 Azure SQL Managed Instance로 마이그레이션하는 방법을 설명합니다.
ms.service: sql-managed-instance
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: mokabiru
ms.author: mokabiru
ms.reviewer: cawrites
ms.date: 06/25/2021
ms.openlocfilehash: d00eb47a1e366d5ae9f3ba559a65e57f5c9c9baa
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131053605"
---
# <a name="migration-guide-sql-server-to-azure-sql-managed-instance"></a>마이그레이션 가이드: SQL Server를 Azure SQL Managed Instance로
[!INCLUDE[appliesto-sqldb-sqlmi](../../includes/appliesto-sqlmi.md)]

이 가이드는 SQL Server 인스턴스를 Azure SQL Managed Instance로 마이그레이션하는 데 도움이 됩니다. 

온-프레미스 또는 다음 환경에서 실행 중인 SQL Server를 마이그레이션할 수 있습니다. 

- Virtual Machines의 SQL Server  
- AWS(Amazon Web Services) EC2 
- AWS RDS(Amazon Relational Database Service) 
- Compute Engine(Google Cloud Platform - GCP)  
- SQL Server용 Cloud SQL(Google Cloud Platform - GCP) 

마이그레이션에 대한 자세한 내용은 [마이그레이션 개요](sql-server-to-managed-instance-overview.md)를 참조하세요. 다른 마이그레이션 가이드는 [데이터베이스 마이그레이션](/data-migration)을 참조하세요. 

:::image type="content" source="media/sql-server-to-managed-instance-overview/migration-process-flow-small.png" alt-text="마이그레이션 프로세스 흐름":::

## <a name="prerequisites"></a>필수 구성 요소 

SQL Server를 Azure SQL Managed Instance로 마이그레이션하려면 다음을 수행한 상태여야 합니다. 

- [마이그레이션 방법](sql-server-to-managed-instance-overview.md#compare-migration-options) 및 방법에 해당하는 도구를 선택했습니다.
- 원본 SQL Server에 연결할 수 있는 컴퓨터에 [DMA(Data Migration Assistant)](https://www.microsoft.com/download/details.aspx?id=53595)가 설치되어 있습니다.
- 대상 [Azure SQL Managed Instance](../../managed-instance/instance-create-quickstart.md)를 만들었습니다.
- 원본과 대상 모두에 액세스할 수 있도록 연결 및 적합한 권한을 구성했습니다. 
- [Azure SQL Managed Instance에서 사용 가능](../../database/features-comparison.md)한 SQL Server 데이터베이스 엔진 기능을 검토했습니다. 


## <a name="pre-migration"></a>사전 마이그레이션

원본 환경이 지원되는지 확인한 후에 마이그레이션 전 단계부터 시작합니다. 모든 기존 데이터 원본을 검색하고, 마이그레이션 적합성을 평가하고, 마이그레이션을 방해할 수 있는 차단 문제를 식별합니다.  

### <a name="discover"></a>검색

검색 단계에서 네트워크를 스캔하여 조직에서 사용하는 모든 SQL Server 인스턴스와 기능을 식별합니다. 

[Azure Migrate](../../../migrate/migrate-services-overview.md)를 사용하여 온-프레미스 서버의 마이그레이션 적합성을 평가하고, 성능 기반 크기 조정을 수행하고, Azure에서의 해당 작업에 대한 비용 예측을 제공합니다. 

또는  [“MAP 도구 키트(Microsoft Assessment and Planning 도구 키트)”](https://www.microsoft.com/download/details.aspx?id=7826)을 사용하여 현재 IT 인프라를 평가합니다. 도구 키트는 강력한 인벤토리, 평가, 보고 도구를 제공하여 마이그레이션 계획 프로세스를 간소화합니다. 

검색 단계에서 사용할 수 있는 도구에 대한 자세한 내용은 [데이터 마이그레이션 시나리오에 사용할 수 있는 서비스 및 도구](../../../dms/dms-tools-matrix.md)를 참조하세요. 

데이터 원본을 검색한 후에는 Azure SQL Managed Instance로 마이그레이션할 수 있는 온-프레미스 SQL Server 인스턴스를 평가하여 마이그레이션 차단 또는 호환성 문제를 식별합니다.
다음 단계를 진행하여 데이터베이스를 평가하고 Azure SQL Managed Instance로 마이그레이션합니다.

:::image type="content" source="media/sql-server-to-managed-instance-overview/migration-process-sql-managed-instance-steps.png" alt-text="Azure SQL Managed Instance로 마이그레이션하는 단계":::

- [SQL Managed Instance 호환성을 평가](#assess)합니다. 여기서, 마이그레이션을 방해할 수 있는 차단 문제가 없는지 확인해야 합니다.
  이 단계에는 원본 SQL Server 인스턴스의 리소스 사용량을 판별하기 위한 [성능 기준](sql-server-to-managed-instance-performance-baseline.md#create-a-baseline) 만들기도 포함됩니다. 이 단계는 적절한 크기의 관리형 인스턴스를 배포하고 마이그레이션 후 성능이 영향을 받지 않는지 확인하려는 경우 필요합니다.
- [앱 연결 옵션을 선택](../../managed-instance/connect-application-instance.md)합니다.
- [최적으로 크기가 조정된 관리형 인스턴스에 배포](#deploy-to-an-optimally-sized-managed-instance)합니다. 여기서, 관리형 인스턴스의 기술적 특성(vCore 수, 메모리 크기)과 성능 계층(중요 비즈니스용, 범용)을 선택합니다.
- 오프라인 마이그레이션 또는 온라인 마이그레이션 옵션을 사용하여 데이터베이스를 마이그레이션하는 [마이그레이션 방법을 선택하고 마이그레이션](sql-server-to-managed-instance-overview.md#compare-migration-options)합니다.
- [애플리케이션을 모니터링 수정](#monitor-and-remediate-applications)하여 성능이 예상과 같은지 확인합니다.


### <a name="assess"></a>평가 

[!INCLUDE [assess-estate-with-azure-migrate](../../../../includes/azure-migrate-to-assess-sql-data-estate.md)]

SQL Managed Instance가 애플리케이션의 데이터베이스 요구 사항과 호환되는지 확인합니다. SQL Managed Instance는 SQL Server를 사용하는 대부분의 기존 애플리케이션에서 리프트 앤 시프트 방식으로 쉽게 마이그레이션할 수 있도록 설계되었습니다. 그러나 경우에 따라 아직 지원되지 않는 기능이 필요할 수 있으며 해결 방법을 구현하는 데 드는 비용이 너무 큽니다. 

Data Migration Assistant(버전 4.1 이상)를 사용하여 다음 데이터베이스를 평가할 수 있습니다. 

- [Azure 대상 권장 사항](/sql/dma/dma-assess-sql-data-estate-to-sqldb)
- [Azure SKU 권장 사항](/sql/dma/dma-sku-recommend-sql-db)

Database Migration Assessment를 사용하여 환경을 평가하려면 다음 단계를 수행합니다. 

1. [DMA(Data Migration Assistant)](https://www.microsoft.com/download/details.aspx?id=53595)를 엽니다. 
1. **파일** 을 선택한 다음, **새 평가** 를 선택합니다. 
1. 프로젝트 이름을 지정하고, 원본 서버 유형으로 SQL Server를 선택한 다음, 대상 서버 유형으로 Azure SQL Managed Instance를 선택합니다. 
1. 생성하려는 평가 보고서의 유형을 선택합니다. 예를 들면 데이터베이스 호환성 및 기능 패리티입니다. 평가 유형에 따라 원본 SQL Server에 필요한 권한이 다를 수 있습니다.  DMA가 평가를 실행하기 전에 선택한 관리자에 필요한 권한을 강조 표시합니다.
    - **기능 패리티** 범주는 전체 권장 사항 집합, Azure에서 사용 가능한 대체 방법 및 마이그레이션 프로젝트에 대한 작업을 계획하는 데 도움이 되는 완화 단계를 제공합니다. (sysadmin 권한 필요)
    - **호환성 문제** 범주는 마이그레이션을 차단할 수 있는 부분적으로 지원되거나 지원되지 않는 기능 호환성 문제와 해당 문제를 해결하기 위한 권장 사항을 확인합니다(`CONNECT SQL`, `VIEW SERVER STATE`, `VIEW ANY DEFINITION` 권한 필요).
1. SQL Server에 대한 원본 연결의 세부 정보를 지정하고 원본 데이터베이스에 연결합니다.
1. **평가 시작** 을 선택합니다. 
1. 프로세스가 완료되면 마이그레이션 차단 및 기능 패리티 문제에 대한 평가 보고서를 선택하고 검토합니다. 평가 보고서를 조직의 다른 팀 또는 직원과 공유할 수 있는 파일로 내보낼 수도 있습니다. 
1. 마이그레이션 후 활동을 최소화하는 데이터베이스 호환성 수준을 결정합니다.  
1. 온-프레미스 워크로드에 적합한 Azure SQL Managed Instance SKU를 파악합니다. 

자세히 알아보려면 [Data Migration Assistant를 사용하여 SQL Server 마이그레이션 평가 수행](/sql/dma/dma-assesssqlonprem)을 참조하세요.

SQL Managed Instance가 워크로드에 적합한 대상이 아닌 경우 Azure VM의 SQL Server가 비즈니스의 실행 가능한 대체 대상이 될 수 있습니다.

#### <a name="scaled-assessments-and-analysis"></a>스케일링된 평가 및 분석

Data Migration Assistant에서는 분석을 위해 평가 보고서의 스케일링된 평가 및 통합을 수행할 수 있습니다. 여러 서버 및 데이터베이스를 대규모로 평가하고 분석하여 데이터 자산을 광범위하게 확인하려면 다음 링크를 클릭하여 자세한 내용을 확인하세요.

- [PowerShell을 사용하여 스케일링된 평가 수행](/sql/dma/dma-consolidatereports)
- [Power BI를 사용하여 평가 보고서 분석](/sql/dma/dma-consolidatereports#dma-reports)

> [!IMPORTANT]
>추가 분석 및 대상의 준비 상태를 위해 결과를 [Azure Migrate](/sql/dma/dma-assess-sql-data-estate-to-sqldb#view-target-readiness-assessment-results)로 업로드하는 것을 허용하는 [DMA 명령줄 유틸리티](/sql/dma/dma-commandline)를 사용하여 여러 데이터베이스에 대한 대규모 평가 실행을 자동화할 수도 있습니다.

### <a name="deploy-to-an-optimally-sized-managed-instance"></a>최적 크기의 관리되는 인스턴스에 배포

검색 및 평가 단계의 정보에 따라 적절한 크기의 대상 SQL Managed Instance를 만듭니다. [Azure Portal](../../managed-instance/instance-create-quickstart.md), [PowerShell](../../managed-instance/scripts/create-configure-managed-instance-powershell.md)또는 [ARM(Azure Resource Manager) 템플릿](../../managed-instance/create-template-quickstart.md)을 사용하여 해당 작업을 수행할 수 있습니다.

SQL Managed Instance는 클라우드로 이동할 온-프레미스 워크로드에 맞게 조정됩니다. 작업에 적합한 수준의 리소스를 선택할 때 유연성이 높은 [구매 모델](../../database/service-tiers-vcore.md)이 도입되었습니다. 온-프레미스 환경에서는 실제 코어 및 IO 대역폭을 사용하여 이러한 작업의 크기를 조정하는 데 익숙할 것입니다. 관리형 인스턴스에 대한 구매 모델은 가상 코어 수 또는 “vCore 수”를 기반으로 하며, 추가 스토리지 및 IO를 별도로 사용할 수 있습니다. vCore 모델은 현재 온-프레미스에서 사용하는 제품과 비교하여 클라우드의 컴퓨팅 요구 사항을 더 쉽게 이해할 수 있는 방법입니다. 이 구매 모델을 사용하면 클라우드에서 대상 환경의 크기를 올바르게 조정할 수 있습니다. 올바른 서비스 계층 및 특성을 선택하는 데 도움이 될 수 있는 몇 가지 일반적인 지침이 여기에 설명되어 있습니다.

- [관리형 인스턴스가 설치된 VM 특성](../../managed-instance/resource-limits.md#hardware-generation-characteristics)과 일치하도록 CPU 특성을 스케일링해야 할 수도 있다는 점에 유의하여 기준 CPU 사용량을 기반으로 SQL Server에서 사용 중인 코어 수와 일치하는 관리형 인스턴스를 프로비저닝합니다.
- 기준 메모리 사용량에 따라 [ 일치하는 메모리가 있는 서비스 계층](../../managed-instance/resource-limits.md#hardware-generation-characteristics)을 선택합니다. 메모리 크기는 직접 선택할 수 없으므로 메모리가 일치하는 vCore 크기를 사용하여 관리형 인스턴스를 선택해야 합니다(예: Gen5의 5.1GB/vCore).
- 파일 하위 시스템의 기준 IO 대기 시간을 기반으로 범용(대기 시간이 5ms 초과) 및 중요 비즈니스용(대기 시간이 3ms 미만) 서비스 계층 중에서 선택합니다.
- 예상 IO 성능을 얻도록 기준 처리량을 기반으로 데이터 또는 로그 파일의 크기를 미리 할당합니다.

배포 시 컴퓨팅 및 스토리지 리소스를 선택한 다음, 나중에 [Azure Portal](../../database/scale-resources.md)을 사용하여 애플리케이션의 가동 중지 시간을 도입하지 않고 변경할 수 있습니다.

:::image type="content" source="media/sql-server-to-managed-instance-overview/managed-instance-sizing.png" alt-text="관리되는 인스턴스 크기 조정":::

VNet 인프라와 관리되는 인스턴스를 만드는 방법을 알아보려면 [관리되는 인스턴스 만들기](../../managed-instance/instance-create-quickstart.md)를 참조하세요.

> [!IMPORTANT]
> 대상 VNet 및 서브넷을 [관리형 인스턴스 VNet 요구 사항](../../managed-instance/connectivity-architecture-overview.md#network-requirements)에 따라 유지해야 합니다. 호환되지 않는 경우 새 인스턴스를 만들거나 이미 만든 인스턴스를 사용하지 못할 수 있습니다. [새 네트워크 만들기](../../managed-instance/virtual-network-subnet-create-arm-template.md) 및 [기존 네트워크 구성](../../managed-instance/vnet-existing-add-subnet.md)에 대해 자세히 알아보세요.

## <a name="migrate"></a>마이그레이션

마이그레이션 전 단계와 관련된 작업을 완료하면 스키마 및 데이터 마이그레이션을 수행할 준비가 된 것입니다. 

선택한 [마이그레이션 방법](sql-server-to-managed-instance-overview.md#compare-migration-options)을 사용하여 데이터를 마이그레이션합니다.

SQL Managed Instance는 온-프레미스 또는 Azure VM 데이터베이스 구현에서 대량의 데이터베이스 마이그레이션이 필요한 사용자 시나리오를 대상으로 합니다. 인스턴스 수준 및/또는 데이터베이스 간 기능을 정기적으로 사용하는 애플리케이션의 백 엔드를 리프트 앤 시프트 방식으로 이동해야 하는 경우에 최적의 선택입니다. 이러한 시나리오의 경우 애플리케이션을 다시 구성하지 않고도 Azure에서 전체 인스턴스를 해당 환경으로 이동할 수 있습니다.

SQL 인스턴스를 이동하려면 다음을 신중하게 계획해야 합니다.

- 배치해야 하는 모든 데이터베이스(같은 인스턴스에서 실행되는 데이터베이스)의 마이그레이션.
- 로그인, 자격 증명, SQL 에이전트 작업 및 연산자, 서버 수준 트리거를 포함하여 애플리케이션이 종속된 인스턴스 수준 개체의 마이그레이션.

SQL Managed Instance는 정기적인 DBA 작업의 일부를 기본 제공되는 플랫폼에 위임할 수 있도록 하는 관리되는 서비스입니다. 따라서 [고가용성](../../database/high-availability-sla.md)이 기본 제공되므로 정기 백업을 위한 유지 관리 작업 또는 Always On 구성과 같이 일부 인스턴스 수준 데이터는 마이그레이션할 필요가 없습니다.

SQL Managed Instance에서 지원하는 데이터베이스 마이그레이션 옵션은 다음과 같습니다(현재 지원되는 유일한 마이그레이션 방법임).

- Azure Database Migration Service - 거의 제로에 가까운 가동 중지 시간으로 마이그레이션.
- 네이티브 `RESTORE DATABASE FROM URL` - SQL Server의 네이티브 백업을 사용하며 약간의 가동 중지 시간 필요

이 가이드에서는 가장 인기 있는 두 가지 옵션인 Azure DMS(Database Migration Service), 그리고 네이티브 백업 및 복원에 대해 설명합니다.

### <a name="database-migration-service"></a>Database Migration Service

DMS를 사용하여 마이그레이션을 수행하려면 다음 단계를 수행합니다.

1. 이 작업을 처음 수행 하는 경우 구독에 [ **microsoft.datamigration** 리소스 공급자를 등록](../../../dms/quickstart-create-data-migration-service-portal.md#register-the-resource-provider) 합니다.
1. 원하는 위치(대상 Azure SQL Managed Instance와 동일한 지역이 좋음)에 Azure Database Migration Service 인스턴스를 만들고, 기존 가상 네트워크를 선택하거나 DMS 인스턴스를 호스트할 새 가상 네트워크를 만듭니다.
1. DMS 인스턴스를 만든 후, 새 마이그레이션 프로젝트를 만들고 원본 서버 유형을 **SQL Server** 로, 대상 서버 유형을 **Azure SQL Database Managed Instance** 로 각각 지정합니다. 프로젝트 만들기 블레이드에서 작업 유형(온라인 또는 오프라인 데이터 마이그레이션)을 선택합니다. 
1.  **마이그레이션 원본** 세부 정보 페이지에서 원본 SQL Server의 세부 정보를, **마이그레이션 대상** 세부 정보 페이지에서 대상 Azure SQL Managed Instance의 세부 정보를 각각 지정합니다. **다음** 을 선택합니다.
1. 마이그레이션하려는 데이터베이스를 선택합니다. 
1. 구성 설정을 제공하여 데이터베이스 백업 파일을 포함하는 **SMB 네트워크 공유** 를 지정합니다. DMS에서 네트워크 공유에 액세스할 수 있는 Windows 사용자 자격 증명을 사용합니다. **Azure Storage 계정 세부 정보** 를 제공합니다. 
1. 마이그레이션 요약을 검토하고 **마이그레이션 실행** 을 선택합니다. 그런 다음 마이그레이션 작업을 모니터링하고 데이터베이스 마이그레이션의 진행 상황을 확인할 수 있습니다.
1. 데이터베이스가 복원된 후에 **중단 시작** 을 선택합니다. SMB 네트워크 공유에서 사용 가능하도록 설정하면 마이그레이션 프로세스가 비상 로그 백업을 복사하고 대상에 복원합니다. 
1. 원본 데이터베이스에 들어오는 모든 트래픽을 중지하고 새 Azure SQL Managed Instance 데이터베이스에 연결 문자열을 업데이트합니다. 

이 마이그레이션 옵션에 대한 자세한 단계별 자습서는 [DMS를 사용하여 SQL Server를 Azure SQL Managed Instance로 온라인 마이그레이션](../../../dms/tutorial-sql-server-managed-instance-online.md)을 참조하세요. 
   

### <a name="backup-and-restore"></a>백업 및 복원 

신속하고 간편한 데이터베이스 마이그레이션을 가능하게 하는 Azure SQL Managed Instance의 주요 기능 중 하나는 [Azure Storage](https://azure.microsoft.com/services/storage/)에 저장된 데이터베이스 백업(`.bak`) 파일의 네이티브 복원입니다. 백업 및 복원은 데이터베이스의 크기를 기반으로 하는 비동기 작업입니다. 

다음 다이어그램은 프로세스의 상위 수준 개요를 제공합니다.

![다이어그램을 보면 화살표로 레이블이 지정된 URL에 백업/업로드 흐름이 SQL Server에서 Azure Storage로 향하며, 두 번째 화살표로 레이블이 지정된 URL에서 복원 흐름은 Azure Storage에서 SQL의 Managed Instance로 향하고 있음을 확인할 수 있습니다.](./media/sql-server-to-managed-instance-overview/migration-restore.png)

> [!NOTE]
> 백업을 수행하고 백업을 Azure Storage에 업로드하고 Azure SQL Managed Instance에 네이티브 복원 작업을 수행하는 데 소요되는 시간은 데이터베이스 크기에 따라 다릅니다. 대규모 데이터베이스의 경우 작업을 수용할 수 있도록 충분한 가동 중지 시간을 확보하세요. 

다음 표에서는 실행되는 원본 SQL Server 버전에 따라 사용할 수 있는 방법에 대한 자세한 정보를 제공합니다.

|단계|SQL 엔진 및 버전|백업/복원 방법|
|---|---|---|
|Azure Storage에 백업 저장|2012 SP1 CU2 이전|Azure Storage에 .bak 파일 직접 업로드|
| |2012 SP1 CU2~2016|사용되지 않는 [WITH CREDENTIAL](/sql/t-sql/statements/restore-statements-transact-sql) 구문을 사용하여 직접 백업|
| |2016 이상|[WITH SAS CREDENTIAL](/sql/relational-databases/backup-restore/sql-server-backup-to-url) 구문을 사용하여 직접 백업|
|Azure Storage에서 관리형 인스턴스로 복원| |[SAS CREDENTIAL을 사용하여 URL에서 복원](../../managed-instance/restore-sample-database-quickstart.md)|

> [!IMPORTANT]
>
> - 원시 복원 옵션을 사용하여 [투명한 데이터 암호화](../../database/transparent-data-encryption-tde-overview.md)로 보호된 데이터베이스를 관리형 인스턴스로 마이크레이션하는 경우에는 데이터베이스를 복원하기 전에 온-프레미스 또는 Azure VM SQL Server의 해당 인증서를 마이그레이션해야 합니다. 자세한 단계는 [TDE 인증서를 관리형 인스턴스로 마이그레이션](../../managed-instance/tde-certificate-migrate.md)을 참조하세요.
> - 시스템 데이터베이스의 복원은 지원되지 않습니다. master 또는 msdb 데이터베이스에 저장된 인스턴스 수준 개체를 마이그레이션하려면, 해당 개체를 스크립팅하고 대상 인스턴스에서 T-SQL 스크립트를 실행하는 것이 좋습니다.

백업 및 복원을 사용하여 마이그레이션하려면 다음 단계를 수행합니다. 

1. Azure Blob Storage에 데이터베이스를 백업합니다. 예를 들면 [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms)의 [URL에 백업](/sql/relational-databases/backup-restore/sql-server-backup-to-url)을 사용합니다. [Microsoft Azure 도구](https://go.microsoft.com/fwlink/?LinkID=324399)를 사용하여 SQL Server 2012 SP1 CU2 이전 버전의 데이터베이스를 지원할 수 있습니다. 
1. SQL Server Management Studio를 사용하여 Azure SQL Managed Instance에 연결합니다. 
1. 데이터베이스 백업을 통해 Azure Blob Storage 계정에 액세스하기 위해 공유 액세스 서명을 사용하여 자격 증명을 만듭니다. 예를 들면 다음과 같습니다.

   ```sql
   CREATE CREDENTIAL [https://mitutorials.blob.core.windows.net/databases]
   WITH IDENTITY = 'SHARED ACCESS SIGNATURE'
   , SECRET = 'sv=2017-11-09&ss=bfqt&srt=sco&sp=rwdlacup&se=2028-09-06T02:52:55Z&st=2018-09-04T18:52:55Z&spr=https&sig=WOTiM%2FS4GVF%2FEEs9DGQR9Im0W%2BwndxW2CQ7%2B5fHd7Is%3D'
   ```
1. Azure Storage Blob 컨테이너에서 백업을 복원합니다. 예를 들면 다음과 같습니다. 

   ```sql
   RESTORE DATABASE [TargetDatabaseName] FROM URL =
     'https://mitutorials.blob.core.windows.net/databases/WideWorldImporters-Standard.bak'
   ```

1. 복원이 완료되면 SQL Server Management Studio 내 **개체 탐색기** 에서 데이터베이스를 확인합니다. 

이 마이그레이션에 대해 자세히 알아보려면 [SSMS를 사용하여 Azure SQL Managed Instance로 데이터베이스 복원](../../managed-instance/restore-sample-database-quickstart.md)을 참조하세요.

> [!NOTE]
> 데이터베이스 복원 작업은 비동기로 진행되며 다시 시도할 수 있습니다. 연결이 끊어지거나 제한 시간이 만료되는 경우 SQL Server Management Studio에서 오류가 발생할 수 있습니다. Azure SQL Database는 백그라운드에서 데이터베이스 복원을 계속 시도하며, [sys.dm_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) 및 [sys.dm_operation_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) 보기를 사용하여 복원 진행률을 추적할 수 있습니다.


## <a name="data-sync-and-cutover"></a>데이터 동기화 및 중단

원본에서 대상으로 데이터의 변경 내용을 지속적으로 복제/동기화하는 마이그레이션 옵션을 사용하는 경우 원본 데이터와 스키마가 대상에서 변경되고 드리프트될 수 있습니다. 데이터 동기화를 진행할 때, 마이그레이션 프로세스 도중에 원본에 대한 모든 변경 내용이 대상에 캡처되고 적용되었는지 확인하세요. 

원본 및 대상의 데이터가 동일한지 확인한 후에 원본에서 대상 환경으로의 중단을 수행할 수 있습니다. 비즈니스/애플리케이션 팀과 함께 중단 프로세스를 계획하여 중단 시에 비즈니스 연속성에 영향을 미치지 않는 선에서 최소한의 중단만 발생하도록 하는 것이 중요합니다. 

> [!IMPORTANT]
> DMS를 사용하는 마이그레이션의 일부로 중단을 수행하는 것과 관련된 특정 단계에 대한 자세한 내용은 [마이그레이션 중단 수행](../../../dms/tutorial-sql-server-managed-instance-online.md#performing-migration-cutover)을 참조하세요.


## <a name="post-migration"></a>마이그레이션 후 작업

마이그레이션 단계를 성공적으로 완료한 후 모든 것이 원활하고 효율적으로 작동하게 하려면 일련의 마이그레이션 후 작업을 수행해야 합니다. 

마이그레이션 후 단계는 데이터 정확도 문제를 조정하고 완성도를 확인할 뿐만 아니라 워크로드 관련 성능 문제를 해결하는 데 매우 중요합니다. 

### <a name="monitor-and-remediate-applications"></a>애플리케이션 모니터링 및 수정 
관리형 인스턴스로의 마이그레이션을 완료하고 나면 워크로드의 애플리케이션 동작 및 성능을 추적해야 합니다. 이 프로세스에는 다음 작업이 포함됩니다.

- [관리형 인스턴스에서 실행 중인 워크로드의 성능을](sql-server-to-managed-instance-performance-baseline.md#compare-performance) [원본 SQL Server 인스턴스에서 만든 성능 기준과 비교합니다](sql-server-to-managed-instance-performance-baseline.md#create-a-baseline).
- 지속해서 [워크로드의 성능을 모니터링](sql-server-to-managed-instance-performance-baseline.md#monitor-performance)하여 잠재적인 문제와 개선 사항을 식별합니다.

### <a name="perform-tests"></a>테스트 수행

데이터베이스 마이그레이션의 테스트 접근 방식은 다음 작업으로 구성됩니다.

1. **유효성 검사 테스트 개발**: 데이터베이스 마이그레이션을 테스트하려면 SQL 쿼리를 사용해야 합니다. 원본 데이터베이스와 대상 데이터베이스 둘 다에서 실행할 유효성 검사 쿼리를 만들어야 합니다. 유효성 검사 쿼리는 정의한 범위를 포함해야 합니다.
1. **테스트 환경 설정**: 테스트 환경에는 원본 데이터베이스와 대상 데이터베이스의 복사본이 포함되어야 합니다. 테스트 환경을 격리해야 합니다.
1. **유효성 검사 테스트 실행**: 원본 및 대상에서 유효성 검사 테스트를 실행한 다음, 결과를 분석합니다.
1. **성능 테스트 실행**: 원본 및 대상에서 성능 테스트를 실행한 다음, 결과를 분석하고 비교합니다.


## <a name="leverage-advanced-features"></a>고급 기능 활용 

SQL Managed Instance에서 제공하는 고급 클라우드 기반 기능(예:[기본 제공되는 고가용성](../../database/high-availability-sla.md), [위협 탐지](../../database/azure-defender-for-sql.md), [워크로드 모니터링 및 튜닝](../../database/monitor-tune-overview.md))을 활용하는 것이 좋습니다. 

[Azure SQL 분석](../../../azure-sql/database/monitor-tune-overview.md)을 사용하여 중앙 집중 방식으로 관리되는 인스턴스의 거대한 집합을 모니터링할 수 있습니다.

일부 SQL Server 기능은 [데이터베이스 호환성 수준](/sql/relational-databases/databases/view-or-change-the-compatibility-level-of-a-database)이 최신 호환성 수준(150)으로 변경된 후에만 사용할 수 있습니다. 


## <a name="next-steps"></a>다음 단계

- 다양한 데이터베이스 및 데이터 마이그레이션 시나리오와 특수 작업을 지원하는 데 사용할 수 있는 Microsoft와 타사의 서비스 및 도구 표는 [데이터 마이그레이션에 사용할 수 있는 서비스 및 도구](../../../dms/dms-tools-matrix.md)를 참조하세요.

- Azure SQL Managed Instance에 대해 자세히 알아보려면 다음을 참조하세요.
   - [Azure SQL Managed Instance의 서비스 계층](../../managed-instance/sql-managed-instance-paas-overview.md#service-tiers)
   - [SQL Server와 Azure SQL Managed Instance 간의 차이점](../../managed-instance/transact-sql-tsql-differences-sql-server.md)
   - [Azure 총 소유 비용 계산기](https://azure.microsoft.com/pricing/tco/calculator/) 


- 클라우드 마이그레이션의 프레임워크 및 채택 주기에 대해 자세히 알아보려면 다음을 참조하세요.
   -  [Azure용 클라우드 채택 프레임워크](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Azure로 마이그레이션된 워크로드의 비용 계산 및 크기 조정 모범 사례](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 

- 애플리케이션 액세스 레이어를 평가하려면 [Data Access Migration Toolkit(미리 보기)](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit)을 참조하세요.
- 데이터 액세스 레이어 A/B 테스트를 수행하는 방법에 관한 자세한 내용은 [데이터베이스 실험 도우미](/sql/dea/database-experimentation-assistant-overview)를 참조하세요.
