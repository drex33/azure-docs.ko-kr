---
title: SQL Server에서 Azure VM의 SQL Server로(마이그레이션 개요)
description: SQL Server를 Azure VM의 SQL Server로 마이그레이션하려고 할 때 사용할 수 있는 다양한 마이그레이션 전략에 관해 알아봅니다.
ms.custom: ''
ms.service: virtual-machines-sql
ms.subservice: migration-guide
ms.devlang: ''
ms.topic: how-to
author: markjones-msft
ms.author: markjon
ms.reviewer: chadam
ms.date: 09/07/2021
ms.openlocfilehash: 163fb5cba55248fcc478e219a6b7c014fedbb689
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/12/2021
ms.locfileid: "132397797"
---
# <a name="migration-overview-sql-server-to-sql-server-on-azure-vms"></a>마이그레이션 개요: SQL Server에서 Azure VM의 SQL Server로
[!INCLUDE[appliesto--sqlmi](../../includes/appliesto-sqlvm.md)]

SQL Server를 Azure VMs(Virtual Machines)의 SQL Server로 마이그레이션하는 다양한 마이그레이션 전략에 관해 알아봅니다. 

온-프레미스 또는 다음에서 실행 중인 SQL Server를 마이그레이션할 수 있습니다.

- Virtual Machines의 SQL Server  
- AWS(Amazon Web Services) EC2 
- AWS RDS(Amazon Relational Database Service) 
- 컴퓨팅 엔진(Google Cloud Platform - GCP)

다른 마이그레이션 가이드는 [데이터베이스 마이그레이션](/data-migration)을 참조하세요. 

## <a name="overview"></a>개요

OS 제어와 함께 익숙한 SQL Server 환경을 사용하고 기본 제공 VM 고가용성, [자동화된 백업](../../virtual-machines/windows/automated-backup.md), [자동화된 패치](../../virtual-machines/windows/automated-patching.md)와 같은 클라우드 제공 기능을 사용하려면 [Azure VMs(Virtual Machines)의 SQL Server](../../virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md)로 마이그레이션합니다. 

[Azure 하이브리드 혜택 라이선스 모델](../../virtual-machines/windows/licensing-model-azure-hybrid-benefit-ahb-change.md)을 통해 사용자 라이선스를 가져와 비용을 절감하거나 [무료 보안 업데이트](../../virtual-machines/windows/sql-server-2008-extend-end-of-support.md)를 받아 SQL Server 2008 및 SQL Server 2008 R2에 대한 지원을 확장합니다. 


## <a name="choose-appropriate-target"></a>적절한 대상 선택

Azure Virtual Machines는 Azure의 여러 지역에서 실행되며 다양한 [머신 크기](../../../virtual-machines/sizes.md) 및 [스토리지 옵션](../../../virtual-machines/disks-types.md)도 제공합니다. SQL Server 워크로드에 올바른 VM 및 스토리지 크기를 결정할 때 [Azure Virtual Machines의 SQL Server에 대한 성능 지침](../../virtual-machines/windows/performance-guidelines-best-practices-checklist.md#vm-size)을 참조하세요. 워크로드에 맞는 VM 크기 및 스토리지 요구 사항을 확인합니다. 성능 기반 [Azure Migrate 평가](../../../migrate/concepts-assessment-calculation.md#types-of-assessments)를 통해 크기를 조정하는 것이 좋습니다. 이 옵션을 사용할 수 없는 경우 [성능을 위한 고유 기준](https://azure.microsoft.com/services/virtual-machines/sql-server/)을 만드는 방법에 관한 다음 문서를 참조하세요.

VM에서 SQL Server를 올바르게 설치하고 구성하는 것도 고려해야 합니다. [Azure SQL 가상 머신 이미지 갤러리](../../virtual-machines/windows/create-sql-vm-portal.md)를 사용하면 적합한 버전, 에디션 및 운영 체제로 SQL Server VM을 만들 수 있기 때문에 해당 갤러리를 사용하는 것이 좋습니다. 그러면 자동화된 백업 및 자동화된 패치와 같은 기능을 사용할 수 있도록 SQL Server [리소스 공급자](../../virtual-machines/windows/create-sql-vm-portal.md)에 자동으로 Azure VM도 등록합니다.

## <a name="migration-strategies"></a>마이그레이션 전략

사용자 데이터베이스를 Azure VM의 SQL Server 인스턴스로 마이그레이션하는 두 가지 마이그레이션 전략은 **마이그레이션** 과 **리프트 앤 시프트** 입니다. 

비즈니스에 적합한 접근 방식은 일반적으로 다음 요소에 따라 달라집니다. 

- 마이그레이션 크기 및 규모
- 마이그레이션 속도
- 애플리케이션의 코드 변경 지원
- SQL Server 버전, 운영 체제 또는 둘 다 변경해야 합니다.
- 기존 제품의 지원 가능성 수명 주기
- 마이그레이션 중 애플리케이션 가동 중지 시간

:::image type="content" source="media/sql-server-to-sql-on-azure-vm-individual-databases-guide/virtual-machine-migration-downtime.png" alt-text="가상 머신 마이그레이션 가동 중지 시간":::

다음 표에서는 두 마이그레이션 전략의 차이점을 설명합니다.
<br />

| **마이그레이션 전략** | **설명** | **사용하는 경우** |
| --- | --- | --- |
| **리프트 앤 시프트** | 리프트 앤 시프트 마이그레이션 전략을 사용하여 운영 체제 또는 SQL Server 버전을 변경하지 않고 전체 실제 또는 가상 SQL Server를 현재 위치에서 Azure VM의 SQL Server 인스턴스로 이동합니다. 리프트 앤 시프트 마이그레이션을 완료하려면 [Azure Migrate](../../../migrate/migrate-services-overview.md)를 참조하세요. <br /><br /> 원본 서버는 온라인 상태를 유지하고 요청을 서비스하는 반면, 원본과 대상 서버는 거의 원활한 마이그레이션을 위해 데이터를 동기화합니다. | 단일 마이그레이션부터 대규모 마이그레이션까지 사용하며, 데이터 센터 종료와 같은 시나리오에도 적용됩니다. <br /><br /> 사용자 SQL 데이터베이스 또는 애플리케이션에 필요한 코드 변경이 없거나 최소한이므로, 전체 마이그레이션이 빨라집니다. <br /><br />[SSIS](/sql/integration-services/sql-server-integration-services), [SSRS](/sql/reporting-services/create-deploy-and-manage-mobile-and-paginated-reports) 및 [SSAS](/analysis-services/analysis-services-overview)와 같은 비즈니스 인텔리전스 서비스를 마이그레이션하는 데 추가 단계가 필요하지 않습니다. |
|**마이그레이션** | 대상 SQL Server 및/또는 운영 체제 버전을 업그레이드하려는 경우 마이그레이션 전략을 사용합니다. <br /> <br /> Azure Marketplace에서 Azure VM을 선택하거나 원본 SQL Server 버전과 일치하는 준비된 SQL Server 이미지를 선택합니다. <br/> <br/> Azure Data Studio [Azure SQL 마이그레이션 확장을](../../../dms/migration-using-azure-data-studio.md) 사용하여 가동 중지 시간을 최소화하면서 SQL Server 데이터베이스를 Azure 가상 머신에서 SQL Server 마이그레이션합니다. | 최신 버전의 SQL Server에서 사용할 수 있는 기능을 사용해야 하거나 사용하려는 경우 또는 더는 지원되지 않는 레거시 SQL Server 및/또는 OS 버전을 업그레이드해야 하는 경우에 사용합니다.  <br /> <br /> SQL Server 업그레이드를 지원하기 위해 일부 애플리케이션 또는 사용자 데이터베이스 변경이 필요할 수 있습니다. <br /><br />마이그레이션 범위에 있는 경우 [비즈니스 인텔리전스](#business-intelligence) 서비스 마이그레이션에 관한 추가 고려 사항이 있을 수 있습니다. |


## <a name="lift-and-shift"></a>리프트 앤 시프트  

다음 표에는 SQL Server 데이터베이스를 Azure VM의 SQL Server로 마이그레이션하기 위해 **리프트 앤 시프트** 마이그레이션 전략에 사용할 수 있는 방법이 자세히 나와 있습니다.
<br />

|**방법** | **최소 원본 버전** | **최소 대상 버전** | **원본 백업 크기 제약 조건** |  **참고** |
| --- | --- | --- | --- | --- |
| [Azure Migrate](../../../migrate/index.yml) | SQL Server 2008 SP4| SQL Server 2008 SP4| [Azure VM 스토리지 제한](../../../index.yml) |  기존 SQL Server를 현재 상태 그대로 Azure VM의 SQL Server 인스턴스로 이동합니다. 최대 35,000개 VM의 마이그레이션 워크로드를 스케일링할 수 있습니다. <br /><br /> 원본 서버는 서버 데이터를 동기화하는 중에 온라인 상태를 유지하고 요청을 처리하므로, 가동 중지 시간을 최소화합니다. <br /><br /> **자동화 및 스크립팅**: [Azure Site Recovery 스크립트](../../../migrate/how-to-migrate-at-scale.md) 및 [Azure의 스케일링된 마이그레이션 및 계획 예제](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)|

> [!NOTE]
> 이제 Azure Migrate를 사용하여 [장애 조치(failover) 클러스터 인스턴스](sql-server-failover-cluster-instance-to-sql-on-azure-vm.md) 및 [가용성 그룹](sql-server-availability-group-to-sql-on-azure-vm.md) 솔루션을 모두 Azure VM의 SQL Server로 리프트 앤 시프트할 수 있습니다. 

## <a name="migrate"></a>마이그레이션  

설치가 쉽기 때문에 권장되는 마이그레이션 접근 방식은 원시 SQL Server [백업](/sql/t-sql/statements/backup-transact-sql)을 로컬로 가져온 다음, 파일을 Azure에 복사하는 것입니다. 이 방법은 2008부터 모든 버전의 SQL Server에 대해 더 큰 데이터베이스(1TB 초과)와 더 큰 데이터베이스 백업(1TB 초과)을 지원합니다. 그러나 SQL Server 2014부터 시작하고 1TB 미만이며 Azure에 대한 연결이 안정된 데이터베이스의 경우 [URL에 SQL Server 백업](/sql/relational-databases/backup-restore/sql-server-backup-to-url)이 더 나은 접근 방식입니다. 

SQL Server 데이터베이스를 Azure VM의 SQL Server 인스턴스로 마이그레이션할 때 애플리케이션 가동 중지 시간에 영향을 미치므로 대상 서버로 전환해야 할 때 적합한 접근 방식을 선택하는 것이 중요합니다.

다음 표에서는 SQL Server 데이터베이스를 Azure VM의 SQL Server로 마이그레이션하는 데 사용할 수 있는 모든 방법을 자세히 설명합니다.
<br />

|**방법** | **최소 원본 버전** | **최소 대상 버전** | **원본 백업 크기 제약 조건** | **참고** |
| --- | --- | --- | --- | --- |
| **[Azure Data Studio용 Azure SQL 마이그레이션 확장](../../../dms/migration-using-azure-data-studio.md)** | SQL Server 2005 | SQL Server 2008 | [Azure VM 스토리지 제한](../../../index.yml) |  이 확장은 Azure Data Studio SQL Server 데이터베이스를 Azure 가상 머신의 SQL Server 마이그레이션하는 데 사용하기 쉬운 마법사 기반 확장입니다. 압축을 사용하여 전송할 백업 크기를 최소화합니다. <br /><br /> Azure Data Studio 대한 Azure SQL 마이그레이션 확장은 간단한 사용자 인터페이스에서 평가 및 마이그레이션 기능을 모두 제공합니다.  |
| **[파일에 백업](sql-server-to-sql-on-azure-vm-individual-databases-guide.md#migrate)** | SQL Server 2008 SP4 | SQL Server 2008 SP4| [Azure VM 스토리지 제한](../../../index.yml) |  머신 간에 데이터베이스를 이동하는 간단하고 검증된 방법입니다. 압축을 사용하여 전송할 백업 크기를 최소화합니다. <br /><br /> **자동화 및 스크립팅**: [T-SQL(Transact-SQL)](/sql/t-sql/statements/backup-transact-sql) 및 [Blob Storage에 AzCopy](../../../storage/common/storage-use-azcopy-v10.md)  |
| **[URL에 백업](/sql/relational-databases/backup-restore/sql-server-backup-to-url)** | SQL Server 2012 SP1 CU2 | SQL Server 2012 SP1 CU2| SQL Server 2016의 경우 12.8TB, 그렇지 않으면 1TB | Azure Storage를 사용하여 VM에 백업 파일을 이동하는 대체 방법입니다. 압축을 사용하여 전송할 백업 크기를 최소화합니다. <br /><br /> **자동화 및 스크립팅**: [T-SQL 또는 유지 관리 플랜](/sql/relational-databases/backup-restore/sql-server-backup-to-url) |
| **[DMA(Database Migration Assistant)](/sql/dma/dma-overview)** | SQL Server 2005| SQL Server 2008 SP4| [Azure VM 스토리지 제한](../../../index.yml) |  [DMA](/sql/dma/dma-overview)는 온-프레미스에서 SQL Server를 평가한 다음, 최신 버전의 SQL Server로 원활하게 업그레이드하거나 Azure VM, Azure SQL Database 또는 Azure SQL Managed Instance의 SQL Server로 마이그레이션합니다. <br /><br /> 파일 스트림 지원 사용자 데이터베이스에서 사용하지 않아야 합니다.<br /><br /> DMA에는 [SQL 및 Windows 로그인](/sql/dma/dma-migrateserverlogins)을 마이그레이션하고 [SSIS 패키지](/sql/dma/dma-assess-ssis)를 평가하는 기능도 포함되어 있습니다. <br /><br /> **자동화 및 스크립팅**: [명령줄 인터페이스](/sql/dma/dma-commandline) |
| **[분리 및 연결](../../virtual-machines/windows/migrate-to-vm-from-sql-server.md#detach-and-attach-from-a-url)** | SQL Server 2008 SP4 | SQL Server 2014 | [Azure VM 스토리지 제한](../../../index.yml) | [Azure Blob Storage 서비스를 사용하여 이 파일을 저장](/sql/relational-databases/databases/sql-server-data-files-in-microsoft-azure)하고 Azure VM의 SQL Server 인스턴스에 연결하려는 경우 이 방법을 사용하고, 데이터베이스가 매우 크거나 백업 및 복원 시간이 너무 길 때 특히 유용합니다. <br /><br /> **자동화 및 스크립팅**: [T-SQL](/sql/relational-databases/databases/detach-a-database#TsqlProcedure) 및 [Blob Storage에 AzCopy](../../../storage/common/storage-use-azcopy-v10.md)|
|**[로그 전달](sql-server-to-sql-on-azure-vm-individual-databases-guide.md#migrate)** | SQL Server 2008 SP4(Windows만 해당) | SQL Server 2008 SP4(Windows만 해당) | [Azure VM 스토리지 제한](../../../index.yml) | 로그 전달은 온-프레미스의 트랜잭션 로그 파일을 Azure VM의 SQL Server 인스턴스로 복제합니다. <br /><br /> 그러면 장애 조치(failover) 중 가동 중지 시간을 최소화하고 Always On 가용성 그룹을 설정하는 것보다 구성 오버헤드가 적습니다. <br /><br /> **자동화 및 스크립팅**: [T-SQL](/sql/database-engine/log-shipping/log-shipping-tables-and-stored-procedures)  |
| **[분산형 가용성 그룹](../../virtual-machines/windows/business-continuity-high-availability-disaster-recovery-hadr-overview.md#hybrid-it-disaster-recovery-solutions)** | SQL Server 2016| SQL Server 2016 | [Azure VM 스토리지 제한](../../../index.yml) |  [분산 가용성 그룹](/sql/database-engine/availability-groups/windows/distributed-availability-groups)은 별도의 두 가용성 그룹에 걸쳐 있는 특별한 형식의 가용성 그룹입니다. 분산 가용성 그룹에 참여하는 가용성 그룹은 같은 위치에 있을 필요가 없으며 도메인 간 지원을 포함합니다. <br /><br /> 이 방법은 가동 중지 시간을 최소화하는 데 사용하며 온-프레미스에 가용성 그룹을 구성한 경우 사용합니다. <br /><br /> **자동화 및 스크립팅**: [T-SQL](/sql/t-sql/statements/alter-availability-group-transact-sql)  |
| | | | | |

&nbsp; &nbsp; &nbsp;&nbsp; &nbsp; &nbsp;
 
> [!TIP]
> - 네트워크 옵션이 제한되거나 없는 대용량 데이터 전송의 경우 [연결이 제한된 대용량 데이터 전송](../../../storage/common/storage-solution-large-dataset-low-network.md)을 참조하세요.
> - 이제 Azure Migrate를 사용하여 [장애 조치(failover) 클러스터 인스턴스](sql-server-failover-cluster-instance-to-sql-on-azure-vm.md) 및 [가용성 그룹](sql-server-availability-group-to-sql-on-azure-vm.md) 솔루션을 모두 Azure VM의 SQL Server로 리프트 앤 시프트할 수 있습니다. 

### <a name="considerations"></a>고려 사항

다음은 마이그레이션 방법을 검토할 때 고려해야 할 주요 사항 목록입니다.

- 최적의 데이터 전송 성능을 위해 압축된 백업 파일을 사용하여 데이터베이스 및 파일을 Azure VM의 SQL Server 인스턴스로 마이그레이션합니다. 대용량 데이터베이스의 경우 백업 및 전송 중에 성능 향상을 위해 압축을 수행하는 외에도, [백업 파일을 더 작은 파일로 분할](/sql/relational-databases/backup-restore/back-up-files-and-filegroups-sql-server)합니다. 
- SQL Server 2014 이상에서 마이그레이션하는 경우 [백업을 암호화](/sql/relational-databases/backup-restore/backup-encryption)하여 네트워크 전송 중 데이터를 보호하는 것이 좋습니다.
- 데이터베이스를 마이그레이션하는 동안 가동 중지 시간을 최소화하려면 Always On 가용성 그룹 옵션을 사용합니다. 
- 가용성 그룹을 구성하는 오버헤드 없이 가동 중지 시간을 최소화하려면 로그 전달 옵션을 사용합니다. 
- 네트워크 옵션을 사용하지 않도록 제한된 경우 백업 및 복원과 같은 오프라인 마이그레이션 방법 또는 Azure에서 사용할 수 있는 [디스크 전송 서비스](../../../storage/common/storage-solution-large-dataset-low-network.md)를 사용합니다.
- 또한 Azure VM에서 SQL Server의 SQL Server 버전을 변경하려면 [SQL Server 버전 변경](../../virtual-machines/windows/change-sql-server-edition.md)을 참조하세요.

## <a name="business-intelligence"></a>비즈니스 인텔리전스 

데이터베이스 마이그레이션 범위를 벗어나는 SQL Server Business Intelligence 서비스를 마이그레이션할 때 추가 고려 사항이 있을 수 있습니다. 

### <a name="sql-server-integration-services"></a>SQL Server Integration Services

아래 두 가지 방법 중 하나를 사용하여 SSISDB의 SSIS(SQL Server Integration Services) 패키지 및 프로젝트를 Azure VM의 SQL Server 마이그레이션할 수 있습니다. 

- 원본 SQL Server 인스턴스에서 Azure VM의 SQL Server SSISDB를 백업하고 복원합니다. 그러면 SSISDB의 패키지가 [Azure VM의 대상 SQL Server Integration Services 카탈로그로](/sql/integration-services/catalog/ssis-catalog)복원됩니다.
- 배포 옵션 중 하나를 사용하여 Azure VM의 대상 SQL Server SSIS 패키지를 다시 [배포합니다.](/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages)

SSIS 패키지를 패키지 배포 모델로 배포한 경우 마이그레이션 전에 변환할 수 있습니다. 자세한 내용은 [프로젝트 변환 자습서](/sql/integration-services/lesson-6-2-converting-the-project-to-the-project-deployment-model)를 참조하세요. 


### <a name="sql-server-reporting-services"></a>SQL Server Reporting Services
SQL Server Reporting Services(SSRS) 보고서를 Azure VM의 대상 SQL Server 마이그레이션하려면 [Reporting Services 설치 마이그레이션(기본 모드)을](/sql/reporting-services/install-windows/migrate-a-reporting-services-installation-native-mode) 참조하세요.

또는 SSRS 보고서를 Power BI 페이지가 지난 보고서로 마이그레이션할 수도 있습니다. 보고서를 쉽게 준비하고 마이그레이션하려면  [RDL 마이그레이션 도구](https://github.com/microsoft/RdlMigration)를 사용하세요. Microsoft는 고객이 SSRS 서버에서 Power BI로 RDL(Report Definition Language) 보고서를 마이그레이션할 수 있도록 이 도구를 개발했습니다. 이 도구는 GitHub에서 받을 수 있으며, 마이그레이션 시나리오를 처음부터 끝까지 단계별로 안내합니다. 

### <a name="sql-server-analysis-services"></a>SQL Server Analysis Services
다음 옵션 중 하나를 사용하여 SQL Server Analysis Services 데이터베이스(다차원 또는 테이블 형식 모델)를 원본 SQL Server Azure VM의 SQL Server 마이그레이션할 수 있습니다.

-   SSMS를 사용하여 대화식으로 이동
-   프로그래밍 방식으로 AMO(AMO(Analysis Management Object) 사용)
-   XMLA를 사용하는 스크립트 사용(XML for Analysis)

자세한 내용은 [Analysis Services 데이터베이스 이동을](/analysis-services/multidimensional-models/move-an-analysis-services-database?view=asallproducts-allversions) 참조하세요.

또는 [새 XMLA 읽기/쓰기 엔드포인트](/power-bi/admin/service-premium-connect-tools)를 사용하여 온-프레미스 Analysis Services 테이블 형식 모델을 [Azure Analysis Services](https://azure.microsoft.com/resources/videos/azure-analysis-services-moving-models/) 또는 Power BI Premium 마이그레이션하는 것이 좋습니다. 

## <a name="server-objects"></a>서버 개체

원본 SQL Server 설정에 따라 T-SQL(Transact-SQL)에서 스크립트를 생성하여 Azure VM에서 SQL Server 마이그레이션하기 위해 수동 개입이 필요한 추가 SQL Server 기능이 있을 수 SQL Server Management Studio  그런 다음, Azure VM의 대상 SQL Server 스크립트를 실행합니다. 일반적으로 사용되는 기능 중 일부는 다음과 같습니다.

- 로그인 및 역할
- 연결된 서버
- 외부 데이터 원본
- 에이전트 작업
- 경고
- 데이터베이스 메일
- 복제

## <a name="supported-versions"></a>지원되는 버전

Azure VM에서 SQL Server 데이터베이스를 SQL Server로 마이그레이션하기 위해 준비할 때 지원되는 SQL Server 버전을 고려해야 합니다. Azure VM에서 현재 지원되는 SQL Server 버전 목록은 [Azure VM의 SQL Server](../../virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md#get-started-with-sql-server-vms)를 참조하세요.

## <a name="migration-assets"></a>마이그레이션 자산 

추가 지원이 필요하면 실제 마이그레이션 프로젝트용으로 개발된 다음 리소스를 참조하세요.

|자산  |Description  |
|---------|---------|
|[데이터 워크로드 평가 모델 및 도구](https://www.microsoft.com/download/details.aspx?id=103130)| 이 도구는 권장되는 “최적의” 대상 플랫폼, 클라우드 준비 상태 및 애플리케이션/데이터베이스 수정 수준을 지정된 워크로드에 제공합니다. 또한 자동화되고 균일한 대상 플랫폼 결정 프로세스를 제공하여 대규모 자산 평가를 가속화하는 데 도움이 되는 간단한 원클릭 계산 및 보고서 생성 기능을 제공합니다.|
|[Logman을 사용하는 Perfmon 데이터 수집 자동화](https://www.microsoft.com/download/details.aspx?id=103114)|마이그레이션 대상 권장 사항에 도움이 되는 기준 성능을 이해하기 위해 Perform 데이터를 수집하는 도구입니다. 이 도구는 logman.exe를 사용하여 원격 SQL Server에 설정된 성능 카운터를 생성, 시작, 중지 및 삭제하는 명령을 만듭니다.|
|[Multiple-SQL-VM-VNet-ILB](https://www.microsoft.com/download/details.aspx?id=103104)|이 백서에서는 SQL Server Always On 가용성 그룹 구성에서 여러 Azure 가상 머신을 설정하는 단계를 간략하게 설명합니다.|
|[지역별 울트라 SSD를 지원하는 Azure VM(가상 머신)](https://www.microsoft.com/download/details.aspx?id=103105)|이 PowerShell 스크립트에서는 울트라 SSD를 지원하는 Azure 가상 머신을 지원하는 지역 목록을 검색하는 프로그래매틱 옵션을 제공합니다.|

데이터 SQL 엔지니어링 팀이 이러한 리소스를 개발했습니다. 이 팀의 핵심 선언은 Microsoft의 Azure 데이터 플랫폼으로의 데이터 플랫폼 마이그레이션 프로젝트에 대한 복잡한 현대화의 장애물을 제거하고 속도를 높이는 것입니다.

## <a name="next-steps"></a>다음 단계

SQL Server 데이터베이스를 Azure VM의 SQL Server로 마이그레이션하려면 [개별 데이터베이스 마이그레이션 가이드](sql-server-to-sql-on-azure-vm-individual-databases-guide.md)를 참조하세요. 

- 다양한 데이터베이스 및 데이터 마이그레이션 시나리오와 특수 작업을 지원하는 데 사용할 수 있는 Microsoft와 타사의 서비스 및 도구 표는 [Service and tools for data migration](../../../dms/dms-tools-matrix.md)(데이터 마이그레이션을 위한 서비스 및 도구) 문서를 참조하세요.

- Azure SQL에 관한 자세한 내용은 다음을 참조하세요.
   - [배포 옵션](../../azure-sql-iaas-vs-paas-what-is-overview.md)
   - [Azure VM의 SQL Server](../../virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md)
   - [Azure 총 소유 비용 계산기](https://azure.microsoft.com/pricing/tco/calculator/) 


- 클라우드 마이그레이션의 프레임워크 및 채택 주기에 대해 자세히 알아보려면 다음을 참조하세요.
   -  [Azure용 클라우드 채택 프레임워크](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Azure로 마이그레이션된 워크로드의 비용 계산 및 크기 조정 모범 사례](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 

- 라이선스에 관한 자세한 내용은 다음을 참조하세요.
   - [Azure 하이브리드 혜택을 사용하여 사용자 라이선스 가져오기](../../virtual-machines/windows/licensing-model-azure-hybrid-benefit-ahb-change.md)
   - [SQL Server 2008 및 SQL Server 2008 R2에 관한 무료 지원 확장 받기](../../virtual-machines/windows/sql-server-2008-extend-end-of-support.md)


- 애플리케이션 액세스 레이어를 평가하려면 [데이터 액세스 마이그레이션 도구 키트(미리 보기)](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit)를 참조하세요.
- 데이터 액세스 레이어 A/B 테스트를 수행하는 방법에 관한 자세한 내용은 [데이터베이스 실험 도우미](/sql/dea/database-experimentation-assistant-overview)를 참조하세요.
