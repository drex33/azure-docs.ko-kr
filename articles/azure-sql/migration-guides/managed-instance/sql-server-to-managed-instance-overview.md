---
title: 'SQL Server를 SQL Managed Instance로: 마이그레이션 개요'
description: SQL Server 데이터베이스를 Azure SQL Managed Instance로 마이그레이션하는 데 사용할 수 있는 도구 및 옵션을 알아봅니다.
ms.service: sql-managed-instance
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: mokabiru
ms.author: mokabiru
ms.reviewer: cawrites
ms.date: 09/07/2021
ms.openlocfilehash: 62f895fdea75ff97154910c177cda04413663d68
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/01/2021
ms.locfileid: "129352052"
---
# <a name="migration-overview-sql-server-to-azure-sql-managed-instance"></a>마이그레이션 개요: SQL Server를 Azure SQL Managed Instance로
[!INCLUDE[appliesto--sqlmi](../../includes/appliesto-sqlmi.md)]

SQL Server 데이터베이스를 Azure SQL Managed Instance로 마이그레이션하기 위한 옵션 및 고려 사항을 알아봅니다. 

온-프레미스 또는 다음에서 실행되는 SQL Server 데이터베이스를 마이그레이션할 수 있습니다. 

- Azure Virtual Machines의 SQL Server  
- AWS(Amazon Web Services) EC2(Elastic Compute Cloud) 
- RDS(AWS Relational Database Service) 
- GCP(Google Cloud Platform)의 컴퓨팅 엔진  
- GCP의 SQL Server용 Cloud SQL 

다른 마이그레이션 가이드는 [데이터베이스 마이그레이션](/data-migration)을 참조하세요. 

## <a name="overview"></a>개요

[Azure SQL Managed Instance](../../managed-instance/sql-managed-instance-paas-overview.md)는 가상 머신 또는 운영 체제를 관리할 필요 없이 완전히 관리되는 서비스가 필요한 SQL Server 워크로드에 권장되는 대상 옵션입니다. SQL Managed Instance를 사용하면 애플리케이션이나 데이터베이스 변경을 최소화하면서 온-프레미스 애플리케이션을 Azure로 이동할 수 있습니다. 네이티브 가상 네트워크 지원을 통해 인스턴스를 완벽하게 격리합니다. 

[Azure SQL Managed Instance에서 사용할](../../database/features-comparison.md) 수 있는 SQL Server 데이터베이스 엔진 기능을 검토하여 마이그레이션 대상의 지원 가능성을 확인해야 합니다.  

## <a name="considerations"></a>고려 사항 

마이그레이션 옵션을 평가할 때 고려해야 할 주요 요소는 다음과 같습니다. 
- 서버 및 데이터베이스 수
- 데이터베이스 크기
- 마이그레이션 프로세스 중에 허용되는 비즈니스 가동 중지 시간 

SQL Server 데이터베이스를 SQL Managed Instance로 마이그레이션하는 주요 이점 중 하나는 전체 인스턴스를 마이그레이션하거나 개별 데이터베이스의 하위 집합만 마이그레이션하도록 선택할 수 있다는 것입니다. 마이그레이션 프로세스에 다음을 포함하도록 신중하게 계획합니다. 
- 동일한 인스턴스에 공동 배치해야 하는 모든 데이터베이스 
- 로그인, 자격 증명, SQL 에이전트 작업 및 연산자, 서버 수준 트리거 등 애플리케이션에 필요한 인스턴스 수준 개체 

> [!NOTE]
> Azure SQL Managed Instance는 중요한 시나리오 에서도 99.99%의 가용성을 보장합니다. SQL Managed Instance의 일부 기능으로 인해 발생하는 오버헤드는 비활성화할 수 없습니다. 자세한 내용은 [SQL Managed Instance와 SQL Server 간 성능 차이의 주요 원인](https://azure.microsoft.com/blog/key-causes-of-performance-differences-between-sql-managed-instance-and-sql-server/) 블로그 항목을 참조하세요. 


## <a name="choose-an-appropriate-target"></a>적절한 대상 선택

다음 일반적인 지침은 [성능 기준선](sql-server-to-managed-instance-performance-baseline.md)과 일치 하도록 SQL Managed Instance의 올바른 서비스 계층 및 특성을 선택하는 데 도움이 될 수 있습니다. 

- CPU 사용량 기준선을 사용하여 SQL Server의 인스턴스가 사용하는 코어 수와 일치하는 관리되는 인스턴스를 프로비전합니다. [하드웨어 생성 특성](../../managed-instance/resource-limits.md#hardware-generation-characteristics)에 맞게 리소스를 확장해야 할 수도 있습니다. 
- 메모리 사용량 기준선을 사용하여 메모리 할당과 적절하게 일치하는 [vCore 옵션](../../managed-instance/resource-limits.md#service-tier-characteristics)을 선택합니다. 
- 파일 하위 시스템의 기준 I/O 대기 시간을 사용하여 범용(대기 시간이 5ms 초과) 및 중요 비즈니스용(대기 시간이 3ms 미만) 서비스 계층 중에서 선택합니다. 
- 기준 처리량을 사용하여 데이터 및 로그 파일의 크기를 미리 할당하고 예상 I/O 성능을 달성할 수 있습니다. 

배포 중에 컴퓨팅 및 스토리지 리소스를 선택한 다음, 애플리케이션에 가동 중지 시간을 발생시키지 않고 [Azure Portal을 사용하여 나중에 변경](../../database/scale-resources.md)할 수 있습니다. 

> [!IMPORTANT]
> [관리되는 인스턴스의 가상 네트워크 요구 사항](../../managed-instance/connectivity-architecture-overview.md#network-requirements)에 차이가 있으면 새 인스턴스를 만들거나 기존 인스턴스를 사용하지 못할 수 있습니다. 자세한 내용은 [새 네트워크 만들기](../../managed-instance/virtual-network-subnet-create-arm-template.md) 및 [기존 네트워크 구성](../../managed-instance/vnet-existing-add-subnet.md) 을 참조하세요. 

Azure SQL Managed Instance(범용 대비 중요 비즈니스용)에서 대상 서비스 계층을 선택할 때 고려해야 할 또 다른 주요 사항은 중요 비즈니스용 계층에서만 사용할 수 있는 메모리 내 OLTP와 같은 특정 기능의 가용성입니다. 

### <a name="sql-server-vm-alternative"></a>SQL Server VM 대안

비즈니스에 [Azure Virtual Machines의 SQL Server](../../virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md)를 Azure SQL Managed Instance보다 더 적합한 대상으로 만드는 요구 사항이 있을 수 있습니다. 

다음 조건 중 하나가 비즈니스에 적용되는 경우 대신 SQL Server VM(가상 머신)으로 전환하는 것을 고려해보세요. 

- SQL Server와 동일한 가상 머신에 타사 또는 사용자 지정 에이전트를 설치하는 것과 같이 운영 체제 또는 파일 시스템에 직접 액세스해야 함 
- FileStream/FileTable, PolyBase 및 인스턴스 간 트랜잭션과 같이 아직 지원되지 않는 기능에 대한 엄격한 종속성이 있음 
- 특정 버전의 SQL Server(예: 2012)를 유지해야 함 
- 컴퓨팅 요구 사항이 관리형 인스턴스에서 제공하는 것보다 훨씬 낮고(예: 하나의 vCore) 데이터베이스 통합이 허용되지 않는 옵션임 

## <a name="migration-tools"></a>마이그레이션 도구

다음 마이그레이션 도구를 권장합니다. 

|기술 | Description|
|---------|---------|
|[Azure Data Studio용 Azure SQL 마이그레이션 확장](../../../dms/migration-using-azure-data-studio.md)  | Azure Data Studio 대한 Azure SQL 마이그레이션 확장은 Azure Data Studio SQL Server 평가 및 마이그레이션 기능을 모두 제공합니다. 온라인(최소 가동 중지 시간이 필요한 마이그레이션의 경우) 또는 오프라인(마이그레이션 기간 동안 가동 중지 시간이 지속되는 마이그레이션의 경우) 모드에서 마이그레이션을 지원합니다. |
| [Azure Migrate](../../../migrate/how-to-create-azure-sql-assessment.md) | 이 Azure 서비스는 VMware에서 대규모 SQL 데이터 자산을 검색하고 평가하는 데 도움이 됩니다. Azure SQL 배포 권장 사항, 대상 크기 조정 및 월간 예상 비용을 제공합니다. | 
|[Azure Database Migration Service](../../../dms/tutorial-sql-server-to-managed-instance.md)  | 이 Azure 서비스는 마이그레이션 프로세스 중 가동 중지 시간을 감당할 수 있는 애플리케이션에 대해 오프라인 모드에서의 마이그레이션을 지원합니다. 온라인 모드의 연속 마이그레이션과 달리 오프라인 모드 마이그레이션은 원본에서 대상으로 전체 데이터베이스 백업에 대한 일회성 복원을 실행합니다. | 
|[네이티브 백업 및 복원](../../managed-instance/restore-sample-database-quickstart.md) | SQL Managed Instance는 네이티브 SQL Server 데이터베이스 백업(.bak 파일)의 복원을 지원합니다. Azure Storage에 전체 데이터베이스 백업을 제공할 수 있는 고객에게 가장 쉬운 마이그레이션 옵션입니다. 전체 및 차등 백업도 지원되며 자세한 설명은 이 문서의 [마이그레이션 자산에 대한 섹션](#migration-assets)을 참조하세요.| 
|[로그 재생 서비스](../../managed-instance/log-replay-service-migrate.md) | 이 클라우드 서비스는 SQL Server 로그 전달 기술을 기반으로 하는 SQL Managed Instance에서 사용할 수 있습니다. Azure Storage에 전체, 부분 및 로그 데이터베이스 백업을 제공할 수 있는 고객을 위한 마이그레이션 옵션입니다. 로그 재생 서비스는 Azure Blob Storage에서 SQL Managed Instance로 백업 파일을 복원하는 데 사용됩니다.| 
| | |

다음 표에는 대체 마이그레이션 도구가 나와 있습니다. 

|**기술** |**설명**  |
|---------|---------|
|[트랜잭션 복제](../../managed-instance/replication-transactional-overview.md) | 트랜잭션 일관성을 유지하면서 게시자-구독자 유형 마이그레이션 옵션을 제공하여 원본 SQL Server 데이터베이스 테이블에서 SQL Managed Instance로 데이터를 복제합니다. | 
|[대량 복사](/sql/relational-databases/import-export/import-and-export-bulk-data-by-using-the-bcp-utility-sql-server)| [BCP(대량 복사 프로그램) 도구](/sql/tools/bcp-utility)는 SQL Server 인스턴스의 데이터를 데이터 파일로 복사합니다. 도구를 사용하여 원본에서 데이터를 내보내고 데이터 파일을 대상 SQL Managed Instance로 가져옵니다. </br></br> 고속 대량 복사 작업을 통해 데이터를 Azure SQL Managed Instance로 이동하려면 [스마트 대량 복사 도구](/samples/azure-samples/smartbulkcopy/smart-bulk-copy/)를 사용해 병렬 복사 작업을 활용하여 전송 속도를 최대화할 수 있습니다. | 
|[가져오기 내보내기 마법사/BACPAC](../../database/database-import.md?tabs=azure-powershell)| [BACPAC](/sql/relational-databases/data-tier-applications/data-tier-applications#bacpac)는 데이터베이스의 스키마와 데이터를 캡슐화하는 확장명이 .bacpac인 Windows 파일입니다. BACPAC를 사용하여 SQL Server 원본에서 데이터를 내보내고 다시 Azure SQL Managed Instance로 데이터를 가져올 수 있습니다. |  
|[Azure Data Factory](../../../data-factory/connector-azure-sql-managed-instance.md)|  Azure Data Factory의 [복사 작업](../../../data-factory/copy-activity-overview.md)은 기본 제공 커넥터와 [통합 런타임](../../../data-factory/concepts-integration-runtime.md)을 사용하여 원본 SQL Server 데이터베이스에서 SQL Managed Instance로 데이터를 마이그레이션합니다.</br> </br> Data Factory는 SQL Server 원본에서 SQL Managed Instance로 데이터를 이동하기 위해 광범위한 [커넥터](../../../data-factory/connector-overview.md)를 지원합니다. |

## <a name="compare-migration-options"></a>마이그레이션 옵션 비교

마이그레이션 옵션을 비교하여 비즈니스 요구 사항에 적합한 경로를 선택합니다. 

다음 표에서는 권장하는 마이그레이션 옵션을 비교합니다. 

|마이그레이션 옵션  |사용 시기  |고려 사항  |
|---------|---------|---------|
|[Azure Data Studio용 Azure SQL 마이그레이션 확장](../../../dms/migration-using-azure-data-studio.md) | -단일 데이터베이스 또는 여러 데이터베이스를 대규모로 마이그레이션합니다. </br> - 온라인(최소 가동 중지 시간) 및 오프라인(허용 가능한 가동 중지 시간) 모드 모두에서 실행할 수 있습니다. </br> </br> 지원되는 원본: </br> - SQL Server(2005~2019) 온-프레미스 또는 Azure VM </br> - AWS EC2 </br> - AWS RDS </br> - GCP 컴퓨팅 SQL Server VM |  - 쉽게 설치하고 시작할 수 있습니다. </br> - 온-프레미스 SQL Server 및 백업에 액세스하려면 자체 호스팅 통합 런타임을 설정해야 합니다. </br> - 평가 및 마이그레이션 기능을 모두 포함합니다. |
|[Azure Database Migration Service](../../../dms/tutorial-sql-server-to-managed-instance.md) | -단일 데이터베이스 또는 여러 데이터베이스를 대규모로 마이그레이션합니다. </br> - 마이그레이션 프로세스 중 가동 중지 시간을 수용할 수 있습니다. </br> </br> 지원되는 원본: </br> - SQL Server(2005~2019) 온-프레미스 또는 Azure VM </br> - AWS EC2 </br> - AWS RDS </br> - GCP 컴퓨팅 SQL Server VM |  - [PowerShell](../../../dms/howto-sql-server-to-azure-sql-managed-instance-powershell-offline.md)을 통해 대규모 마이그레이션을 자동화할 수 있습니다. </br> -마이그레이션 완료 시간은 데이터베이스 크기에 따라 달라지며 백업 및 복원 시간의 영향을 받습니다. </br> -충분한 가동 중지 시간이 필요할 수 있습니다. |
|[네이티브 백업 및 복원](../../managed-instance/restore-sample-database-quickstart.md) | - 개별 LOB(기간 업무) 애플리케이션 데이터베이스를 마이그레이션합니다.  </br> -별도의 마이그레이션 서비스 또는 도구 없이 빠르고 쉽게 마이그레이션할 수 있습니다.  </br> </br> 지원되는 원본: </br> - SQL Server(2005~2019) 온-프레미스 또는 Azure VM </br> - AWS EC2 </br> - AWS RDS </br> - GCP 컴퓨팅 SQL Server VM | - 데이터베이스 백업은 여러 스레드를 사용하여 Azure Blob Storage로의 데이터 전송을 최적화하지만 파트너 대역폭과 데이터베이스 크기가 전송 속도에 영향을 미칠 수 있습니다. </br> - 가동 중지 시간은 전체 백업 및 복원을 수행하는 데 필요한 시간(데이터 작업의 크기)을 수용해야 합니다.| 
|[로그 재생 서비스](../../managed-instance/log-replay-service-migrate.md) | - 개별 LOB(기간 업무) 애플리케이션 데이터베이스를 마이그레이션합니다.  </br> - 데이터베이스 마이그레이션에는 추가 컨트롤이 필요합니다.  </br> </br> 지원되는 원본: </br> - SQL Server(2008~2019) 온-프레미스 또는 Azure VM </br> - AWS EC2 </br> - AWS RDS </br> - GCP 컴퓨팅 SQL Server VM | 마이그레이션에는 SQL Server에서 전체 데이터베이스 백업을 만들고 Azure Blob Storage로 백업 파일을 복사하는 작업이 필요합니다. 로그 재생 서비스는 Azure Blob Storage에서 SQL Managed Instance로 백업 파일을 복원하는 데 사용됩니다. </br> -마이그레이션 프로세스 중에 복원되는 데이터베이스는 복원 모드에 있으며 프로세스가 완료될 때까지 읽기 또는 쓰기에 사용할 수 없습니다.| 
| | | |

다음 표에서는 대체 마이그레이션 옵션을 비교합니다. 

|메서드 또는 기술 |사용 시기 |고려 사항  |
|---------|---------|---------|
|[트랜잭션 복제](../../managed-instance/replication-transactional-overview.md) | - 원본 데이터베이스 테이블의 변경 내용을 대상 SQL Managed Instance 데이터베이스 테이블로 지속적으로 게시하여 마이그레이션합니다. </br> - 선택한 테이블(데이터베이스의 하위 집합)의 전체 또는 부분 데이터베이스 마이그레이션을 수행합니다.  </br> </br> 지원되는 원본: </br> - 몇 가지 제한 사항이 있는 SQL Server(2012~2019) </br> - AWS EC2  </br> - GCP 컴퓨팅 SQL Server VM | </br> - 다른 마이그레이션 옵션보다 설정이 비교적 복잡합니다.   </br> - 데이터베이스를 오프라인으로 전환하지 않고 데이터를 마이그레이션하는 연속 복제 옵션을 제공합니다.</br> - 트랜잭션 복제에는 원본 SQL Server 인스턴스에서 게시자를 설정할 때 고려해야 할 제한 사항이 있습니다. 자세한 내용은 [개체 게시에 대한 제한 사항](/sql/relational-databases/replication/publish/publish-data-and-database-objects#limitations-on-publishing-objects)을 참조하세요.  </br> - [복제 작업 모니터링](/sql/relational-databases/replication/monitor/monitoring-replication) 기능을 사용할 수 있습니다.    |
|[대량 복사](/sql/relational-databases/import-export/import-and-export-bulk-data-by-using-the-bcp-utility-sql-server)| - 전체 또는 부분 데이터 마이그레이션을 수행합니다. </br> - 가동 중지 시간을 수용할 수 있습니다. </br> </br> 지원되는 원본: </br> - SQL Server(2005~2019) 온-프레미스 또는 Azure VM </br> - AWS EC2 </br> - AWS RDS </br> - GCP 컴퓨팅 SQL Server VM   | - 원본에서 데이터를 내보내고 대상으로 가져올 때 가동 중지 시간이 필요합니다. </br> - 내보내기 또는 가져오기에 사용되는 파일 형식 및 데이터 형식은 테이블 스키마와 일치해야 합니다. |
|[가져오기 내보내기 마법사/BACPAC](../../database/database-import.md)| - 개별 LOB(기간 업무) 애플리케이션 데이터베이스를 마이그레이션합니다. </br>- 소규모 데이터베이스에 적합합니다.  </br>  별도의 마이그레이션 서비스 또는 도구가 필요하지 않습니다. </br> </br> 지원되는 원본: </br> - SQL Server(2005~2019) 온-프레미스 또는 Azure VM </br> - AWS EC2 </br> - AWS RDS </br> - GCP 컴퓨팅 SQL Server VM  |   </br> - 데이터를 원본에서 내보내고 대상에서 가져와야 하므로 가동 중지 시간이 필요합니다.   </br> - 내보내기 또는 가져오기에 사용되는 파일 형식 및 데이터 형식은 잘림 또는 데이터 유형 불일치 오류를 방지하기 위해 테이블 스키마와 일치해야 합니다. </br> - 개체 수가 많은 데이터베이스를 내보내는 데 걸리는 시간이 상당히 길어질 수 있습니다. |
|[Azure Data Factory](../../../data-factory/connector-azure-sql-managed-instance.md)| - 원본 SQL Server 데이터베이스에서 데이터를 마이그레이션하거나 변환합니다.</br> - 여러 데이터 원본의 데이터를 Azure SQL Managed Instance로 병합하는 것은 일반적으로 BI(비즈니스 인텔리전스) 워크로드를 위한 것입니다.   </br> -원본에서 대상으로 데이터를 이동하려면 Data Factory에서 데이터 이동 파이프라인을 만들어야 합니다.   </br> - [비용](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/)은 중요한 고려 사항이며 파이프라인 트리거, 활동 실행 및 데이터 이동 기간과 같은 요소를 기반으로 합니다. |
| | | |

## <a name="feature-interoperability"></a>기능 상호 운용성 

다른 SQL Server 기능을 사용하는 워크로드를 마이그레이션하는 경우 추가 고려 사항이 있습니다. 

### <a name="sql-server-integration-services"></a>SQL Server Integration Services

[Azure Database Migration Service](../../../dms/how-to-migrate-ssis-packages-managed-instance.md)를 사용하여 SSISDB의 SSIS(SQL Server Integration Services) 패키지 및 프로젝트를 Azure SQL Managed Instance로 마이그레이션합니다. 

SQL Server 2012부터 SSISDB의 SSIS 패키지만 마이그레이션이 지원됩니다. 마이그레이션하기 전에 이전 SSIS 패키지를 변환합니다. 자세한 내용은 [프로젝트 변환 자습서](/sql/integration-services/lesson-6-2-converting-the-project-to-the-project-deployment-model)를 참조하세요. 


### <a name="sql-server-reporting-services"></a>SQL Server Reporting Services

SSRS(SQL Server Reporting Services) 보고서를 Power BI의 페이지가 매겨진 보고서로 마이그레이션할 수 있습니다. 보고서를 쉽게 준비하고 마이그레이션하려면  [RDL 마이그레이션 도구](https://github.com/microsoft/RdlMigration)를 사용하세요. Microsoft는 고객이 SSRS 서버에서 Power BI로 RDL(Report Definition Language) 보고서를 마이그레이션할 수 있도록 이 도구를 개발했습니다. 이 도구는 GitHub에서 받을 수 있으며, 마이그레이션 시나리오를 처음부터 끝까지 단계별로 안내합니다. 

### <a name="sql-server-analysis-services"></a>SQL Server Analysis Services

SQL Server 2012 이상의 SQL Server Analysis Services 테이블 형식 모델은 Azure의 Analysis Services 테이블 형식 모델에 대한 PaaS(Platform as a Service) 배포 모델인 Azure Analysis Services로 마이그레이션할 수 있습니다. 온-프레미스 모델을 Azure Analysis Services로 마이그레이션하는 방법에 대한 자세한 내용은 [이 비디오 자습서](https://azure.microsoft.com/resources/videos/azure-analysis-services-moving-models/)에서 확인할 수 있습니다.

또는 [새 XMLA 읽기/쓰기 엔드포인트를 사용하여 Power BI Premium](/power-bi/admin/service-premium-connect-tools)으로 온-프레미스 Analysis Services 테이블 형식 모델을 마이그레이션하는 방법을 고려할 수 있습니다. 

### <a name="high-availability"></a>고가용성

SQL Server 고가용성 기능인 Always On 장애 조치(failover) 클러스터 인스턴스 및 Always On 가용성 그룹은 대상 SQL Managed Instance에서 더 이상 사용되지 않습니다. 고가용성 아키텍처는 이미 SQL Managed Instance에 대한 [범용(표준 가용성 모델)](../../database/high-availability-sla.md#basic-standard-and-general-purpose-service-tier-locally-redundant-availability) 및 [중요 비즈니스용(프리미엄 가용성 모델)](../../database/high-availability-sla.md#premium-and-business-critical-service-tier-locally-redundant-availability) 서비스 계층 모두에 이미 내장되어 있습니다. 프리미엄 가용성 모델은 읽기 전용 목적으로 보조 노드 중 하나에 연결할 수 있는 읽기 확장도 제공합니다.     

SQL Managed Instance에 포함된 고가용성 아키텍처 외에도 [자동 장애 조치(failover) 그룹](../../database/auto-failover-group-overview.md) 기능을 사용하면 관리되는 인스턴스에 있는 데이터베이스 복제 및 장애 조치(failover)를 다른 지역으로 관리할 수 있습니다. 

### <a name="sql-agent-jobs"></a>SQL 에이전트 작업

오프라인 Azure Database Migration Service 옵션을 사용하여 [SQL 에이전트 작업](../../../dms/howto-sql-server-to-azure-sql-managed-instance-powershell-offline.md)을 마이그레이션할 수 있습니다. 그렇지 않으면 SQL Server Management Studio를 사용하여 T-SQL(Transact-SQL)에서 작업을 스크립팅한 다음 대상 SQL Managed Instance에서 수동으로 다시 만듭니다. 

> [!IMPORTANT]
> 현재 Azure Database Migration Service는 T-SQL 하위 시스템 단계를 사용하는 작업만 지원합니다. SSIS 패키지 단계를 사용하는 작업은 수동으로 마이그레이션해야 합니다. 

### <a name="logins-and-groups"></a>로그인 및 그룹

오프라인 모드에서 Database Migration Service를 사용하여 SQL 로그인을 SQL Server 원본에서 Azure SQL Managed Instance로 이동합니다.  마이그레이션 마법사에서 [로그인 선택](../../../dms/tutorial-sql-server-to-managed-instance.md#select-logins) 창을 사용하여 대상 SQL Managed Instance로 로그인을 마이그레이션합니다. 

기본적으로 Azure Database Migration Service는 SQL 로그인 마이그레이션만 지원합니다. 하지만 다음과 같은 방법으로 Windows 로그인의 마이그레이션을 사용하도록 설정할 수 있습니다.

- 대상 SQL Managed Instance에 Azure AD(Azure Active Directory) 읽기 액세스 권한이 있는지 확인합니다. 전역 관리자 역할이 있는 사용자는 Azure Portal을 통해 해당 액세스를 구성할 수 있습니다.
- Windows 사용자 또는 그룹 로그인 마이그레이션을 사용하도록 Azure Database Migration Service를 구성합니다. **구성** 페이지에서 Azure Portal을 통해 설정합니다. 이 설정을 사용하도록 설정한 후 서비스를 다시 시작하면 변경 사항이 적용됩니다.

서비스를 다시 시작하면 마이그레이션에 사용할 수 있는 로그인 목록에 Windows 사용자 또는 그룹 로그인이 나타납니다. 마이그레이션하는 Windows 사용자 또는 그룹 로그인에 대해서는 연결된 도메인 이름을 입력하라는 메시지가 표시됩니다. 서비스 사용자 계정(도메인 이름이 NT AUTHORITY인 계정) 및 가상 사용자 계정(도메인 이름이 NT SERVICE인 계정)은 지원되지 않습니다. 자세한 내용은 [T-SQL을 사용하여 SQL Server 인스턴스의 Windows 사용자 및 그룹을 Azure SQL Managed Instance로 마이그레이션하는 방법](../../managed-instance/migrate-sql-server-users-to-instance-transact-sql-tsql-tutorial.md)을 참조하세요.

또는 Microsoft 데이터 마이그레이션 설계자가 특별히 설계한 [PowerShell 유틸리티](https://www.microsoft.com/download/details.aspx?id=103111)를 사용할 수 있습니다. 이 유틸리티는 PowerShell을 사용해 T-SQL 스크립트를 생성하여 로그인을 다시 만들고 원본에서 대상으로 데이터베이스 사용자를 선택합니다. 

PowerShell 유틸리티는 Windows Server Active Directory 계정을 Azure AD 계정에 자동으로 매핑하고, 원본 Active Directory 인스턴스의 각 로그인에 대해 UPN 조회를 수행할 수 있습니다. 유틸리티는 역할 멤버 자격 및 사용자 권한과 함께 사용자 지정 서버 및 데이터베이스 역할을 스크립팅합니다. 포함된 데이터베이스는 아직 지원되지 않으며 가능한 SQL Server 권한의 하위 집합만 스크립팅됩니다. 

### <a name="encryption"></a>암호화

네이티브 복원 옵션을 사용하여  [투명한 데이터 암호화](../../database/transparent-data-encryption-tde-overview.md) 로 보호되는 데이터베이스를 관리되는 인스턴스로 마이그레이션하려는 경우에는 데이터베이스를 복원하기 *전에* 원본 SQL Server 인스턴스에서 대상 SQL Managed Instance로 [해당 인증서를 마이그레이션](../../managed-instance/tde-certificate-migrate.md)합니다. 

### <a name="system-databases"></a>시스템 데이터베이스

시스템 데이터베이스의 복원은 지원되지 않습니다. master 및 msdb 데이터베이스에 저장된 인스턴스 수준 개체를 마이그레이션하려면 T-SQL을 사용하여 스크립트를 작성한 다음 대상 관리되는 인스턴스에서 다시 만듭니다. 

### <a name="in-memory-oltp-memory-optimized-tables"></a>메모리 내 OLTP(메모리 최적화 테이블)

SQL Server는 메모리 내 OLTP 기능을 제공합니다. 이를 통해 메모리 최적화 테이블, 메모리 최적화 테이블 형식 및 고유하게 컴파일된 SQL 모듈을 사용하여 트랜잭션 처리를 위한 높은 처리량과 짧은 대기 시간 요구 사항을 가진 워크로드를 실행할 수 있습니다. 

> [!IMPORTANT]
> 메모리 내 OLTP는 Azure SQL Managed Instance의 중요 비즈니스용 계층에서만 지원됩니다. 범용 계층에서 지원되지 않습니다.

온-프레미스 SQL Server 인스턴스에 메모리 최적화 테이블 또는 메모리 최적화 테이블 형식이 있고 Azure SQL Managed Instance로 마이그레이션하려는 경우 다음 중 하나를 수행해야 합니다.

- 메모리 내 OLTP를 지원하는 대상 SQL Managed Instance의 중요 비즈니스용 계층을 선택합니다.
- Azure SQL Managed Instance의 범용 계층으로 마이그레이션하려는 경우 데이터베이스를 마이그레이션하기 전에 메모리 최적화 테이블, 메모리 최적화 테이블 형식 및 메모리 최적화 개체와 상호 작용하는 고유하게 컴파일된 SQL 모듈을 제거합니다. 다음 T-SQL 쿼리를 사용하여 범용 계층으로 마이그레이션하기 전에 제거해야 하는 모든 개체를 식별할 수 있습니다.

   ```tsql
   SELECT * FROM sys.tables WHERE is_memory_optimized=1
   SELECT * FROM sys.table_types WHERE is_memory_optimized=1
   SELECT * FROM sys.sql_modules WHERE uses_native_compilation=1
   ```

메모리 내 기술에 대한 자세한 내용은 [Azure SQL Database 및 Azure SQL Managed Instance에서 메모리 내 기술을 사용하여 성능 최적화](../../in-memory-oltp-overview.md)를 참조하세요.

## <a name="advanced-features"></a>고급 기능 

SQL Managed Instance의 고급 클라우드 기반 기능을 활용해보세요. 예를 들어 서비스가 자동으로 수행되므로 백업 관리에 대해 걱정할 필요가 없습니다. [보존 기간 내의 특정 시점](../../database/recovery-using-backups.md#point-in-time-restore)으로 복원할 수 있습니다. 또한  [고가용성이 기본 제공](../../database/high-availability-sla.md)되므로 고가용성 설정에 대해 걱정할 필요가 없습니다. 

보안을 강화하려면  [Azure AD 인증](../../database/authentication-aad-overview.md), [감사](../../managed-instance/auditing-configure.md),  [위협 검색](../../database/azure-defender-for-sql.md),  [행 수준 보안](/sql/relational-databases/security/row-level-security) 및  [동적 데이터 마스킹](/sql/relational-databases/security/dynamic-data-masking) 사용을 고려하세요.

고급 관리 및 보안 기능 외에도 SQL Managed Instance는 [워크로드를 모니터링하고 튜닝](../../database/monitor-tune-overview.md)하는 데 도움이 되는 고급 도구를 제공합니다. [Azure SQL 분석](../../../azure-monitor/insights/azure-sql.md)을 사용하여 대규모로 관리되는 인스턴스 집합을 중앙 집중식으로 모니터링할 수 있습니다. 관리되는 인스턴스에서 [자동 튜닝은](/sql/relational-databases/automatic-tuning/automatic-tuning#automatic-plan-correction) SQL 계획 실행의 성능을 지속적으로 모니터링하고 식별된 성능 문제를 자동으로 수정합니다. 

일부 기능은 [데이터베이스 호환성 수준](/sql/relational-databases/databases/view-or-change-the-compatibility-level-of-a-database)이 최신 호환성 수준(150)으로 변경된 후에만 사용할 수 있습니다. 

## <a name="migration-assets"></a>마이그레이션 자산 

추가 지원이 필요한 경우 실제 마이그레이션 프로젝트용으로 개발된 다음 리소스를 참조하세요.

|자산  |Description  |
|---------|---------|
|[데이터 워크로드 평가 모델 및 도구](https://www.microsoft.com/download/details.aspx?id=103130)| 이 도구는 워크로드에 권장되는 "최적의" 대상 플랫폼, 클라우드 준비 상태 및 애플리케이션/데이터베이스 수정 수준을 제공합니다. 대상 플랫폼에 대해 자동화되고 균일한 의사 결정 프로세스를 제공하여 대규모 자산 평가를 가속화할 수 있는 간단한 원클릭 계산 및 보고서 생성을 제공합니다.|
|[온-프레미스 SQL Server 로그인을 Azure SQL Managed Instance로 이동하는 유틸리티](https://www.microsoft.com/download/details.aspx?id=103111)|PowerShell 스크립트는 T-SQL 명령 스크립트를 만들어 로그인을 다시 만들고 온-프레미스 SQL Server에서 Azure SQL Managed Instance로 데이터베이스 사용자를 선택할 수 있습니다. 이 도구를 사용하면 Windows Server Active Directory 계정을 Azure AD 계정으로 자동 매핑하고 선택적으로 SQL Server 네이티브 로그인을 마이그레이션할 수 있습니다.|
|[Logman을 사용하는 Perfmon 데이터 수집 자동화](https://www.microsoft.com/download/details.aspx?id=103114)|Logman 도구를 사용하여 Perfmon 데이터를 수집해 기준 성능을 쉽게 이해할 수 있으며 마이그레이션 대상 권장 사항을 확인할 수 있습니다. 이 도구는 logman.exe를 사용하여 원격 SQL Server 인스턴스에 설정된 성능 카운터를 생성, 시작, 중지 및 삭제하는 명령을 만듭니다.|

데이터 SQL 엔지니어링 팀이 이러한 리소스를 개발했습니다. 이 팀의 핵심 선언은 Microsoft의 Azure 데이터 플랫폼으로의 데이터 플랫폼 마이그레이션 프로젝트에 대한 복잡한 현대화의 장애물을 제거하고 속도를 높이는 것입니다.


## <a name="next-steps"></a>다음 단계

- SQL Server 데이터베이스를 Azure SQL Managed Instance로 마이그레이션하려면 [SQL Server에서 Azure SQL Managed Instance로 마이그레이션 가이드](sql-server-to-managed-instance-guide.md)를 참조하세요.

- 데이터베이스 및 데이터 마이그레이션 시나리오와 특수 작업에 도움이 되는 서비스 및 도구 매트릭스는 [데이터 마이그레이션 서비스 및 도구](../../../dms/dms-tools-matrix.md)를 참조하세요.

- Azure SQL Managed Instance에 대한 자세한 내용은 다음을 참조하세요.
   - [Azure SQL Managed Instance의 서비스 계층](../../managed-instance/sql-managed-instance-paas-overview.md#service-tiers)
   - [SQL Server와 Azure SQL Managed Instance 간의 차이점](../../managed-instance/transact-sql-tsql-differences-sql-server.md)
   - [Azure 총 소유 비용 계산기](https://azure.microsoft.com/pricing/tco/calculator/) 

- 클라우드 마이그레이션의 프레임워크 및 채택 주기에 대해 자세히 알아보려면 다음을 참조하세요.
   -  [Azure용 클라우드 채택 프레임워크](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Azure로 마이그레이션된 워크로드의 비용 계산 및 크기 조정 모범 사례](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 

- 애플리케이션 액세스 레이어를 평가하려면 [Data Access Migration Toolkit(미리 보기)](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit)를 참조하세요.

- 데이터 액세스 레이어에서 A/B 테스트를 수행하는 방법에 대한 자세한 내용은 [데이터베이스 실험 도우미](/sql/dea/database-experimentation-assistant-overview)를 참조하세요.
