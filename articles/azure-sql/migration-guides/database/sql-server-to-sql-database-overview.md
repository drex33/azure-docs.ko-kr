---
title: 'SQL Server를 Azure SQL Database로: 마이그레이션 개요'
description: SQL Server 데이터베이스를 Azure SQL Database로 마이그레이션하는 데 사용할 수 있는 도구 및 옵션을 알아봅니다.
ms.service: sql-database
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: mokabiru
ms.author: mokabiru
ms.reviewer: cawrites
ms.date: 11/06/2020
ms.openlocfilehash: 04bd20e233bac44194c66e772db19f022458383d
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/06/2021
ms.locfileid: "129615527"
---
# <a name="migration-overview-sql-server-to-azure-sql-database"></a>마이그레이션 개요: SQL Server를 Azure SQL Database로
[!INCLUDE[appliesto--sqldb](../../includes/appliesto-sqldb.md)]

SQL Server 데이터베이스를 Azure SQL Database로 마이그레이션하기 위한 옵션 및 고려 사항을 알아봅니다. 

온-프레미스 또는 다음에서 실행되는 SQL Server 데이터베이스를 마이그레이션할 수 있습니다. 

- Azure Virtual Machines의 SQL Server  
- AWS(Amazon Web Services) EC2(Elastic Compute Cloud)
- RDS(AWS Relational Database Service)
- GCP(Google Cloud Platform)의 컴퓨팅 엔진  
- GCP의 SQL Server용 Cloud SQL 

다른 마이그레이션 가이드는 [데이터베이스 마이그레이션](/data-migration)을 참조하세요. 

## <a name="overview"></a>개요

[Azure SQL Database](../../database/sql-database-paas-overview.md)는 완전 관리형 PaaS(Platform as a Service)가 필요한 SQL Server 워크로드에 권장되는 대상 옵션입니다. SQL Database는 대부분의 데이터베이스 관리 기능을 처리합니다. 또한 다양한 애플리케이션 유형에 적합한 고가용성, 지능형 쿼리 처리, 확장성 및 성능 기능이 기본 제공됩니다. 

SQL Database는 다양한 유형의 애플리케이션 또는 워크로드에 맞는 여러 [배포 모델](../../database/sql-database-paas-overview.md#deployment-models) 및 [서비스 계층](../../database/service-tiers-vcore.md#service-tiers)을 통해 유연성을 제공합니다.

SQL Database로 마이그레이션할 때 주요 이점 중 하나는 PaaS 기능을 사용하여 애플리케이션을 현대화할 수 있다는 것입니다. 그러면 SQL 에이전트 작업과 같이 인스턴스 수준에서 범위가 지정된 기술 구성 요소에 대한 종속성을 제거할 수 있습니다.

SQL Server에 대한 [Azure 하이브리드 혜택](https://azure.microsoft.com/pricing/hybrid-benefit/)을 이용하여 SQL Server 온-프레미스 라이선스를 Azure SQL Database로 마이그레이션하면 비용을 절감할 수도 있습니다. 이 옵션은 [vCore 기반 구매 모델](../../database/service-tiers-vcore.md)을 선택한 경우에 사용할 수 있습니다.

[Azure SQL Database에서 사용할](../../database/features-comparison.md) 수 있는 SQL Server 데이터베이스 엔진 기능을 검토하여 마이그레이션 대상의 지원 가능성을 확인해야 합니다.  

## <a name="considerations"></a>고려 사항 

마이그레이션 옵션을 평가할 때 고려해야 할 주요 요소는 다음과 같습니다. 

- 서버 및 데이터베이스 수
- 데이터베이스 크기
- 마이그레이션 프로세스 중에 허용되는 비즈니스 가동 중지 시간 

이 가이드에 나온 마이그레이션 옵션은 이러한 요소를 고려합니다. Azure SQL Database로의 논리적 데이터 마이그레이션의 경우 마이그레이션 시간은 데이터베이스의 개체 수와 데이터베이스 크기에 따라 다를 수 있습니다. 

도구는 다양한 워크로드 및 사용자 기본 설정에 사용할 수 있습니다. 일부 도구는 UI 기반 도구를 통해 단일 데이터베이스의 빠른 마이그레이션을 수행하는 데 사용할 수 있습니다. 또 다른 도구는 여러 데이터베이스의 마이그레이션을 자동화하여 대규모 마이그레이션을 처리할 수 있습니다. 

## <a name="choose-an-appropriate-target"></a>적절한 대상 선택

Azure SQL Database의 올바른 배포 모델 및 서비스 계층을 선택하는 데 도움이 되는 일반 지침을 고려하세요. 배포 중에 컴퓨팅 및 스토리지 리소스를 선택한 다음, 애플리케이션에 가동 중지 시간을 발생시키지 않고 [Azure Portal을 사용하여 나중에 변경](../../database/scale-resources.md)할 수 있습니다.

**배포 모델**: 애플리케이션 워크로드와 사용 패턴을 파악하여 단일 데이터베이스 또는 탄력적 풀 중에서 결정합니다. 

- [단일 데이터베이스](../../database/single-database-overview.md)는 대부분의 최신 클라우드 애플리케이션 및 마이크로 서비스에 적합한 완전 관리형 데이터베이스를 나타냅니다.
- [탄력적 풀](../../database/elastic-pool-overview.md)은 CPU, 메모리 등의 공유 리소스 집합이 포함된 단일 데이터베이스 컬렉션입니다. 동일한 리소스 집합을 효과적으로 공유할 수 있는 예측 가능한 사용 패턴과 풀의 데이터베이스를 결합하는 데 적합합니다.

**모델 구매**: vCore, DTU(데이터베이스 트랜잭션 단위) 또는 서버리스 구매 모델 중에서 선택합니다. 

- [vCore 모델](../../database/service-tiers-vcore.md)을 사용하면 Azure SQL Database용 vCore 수를 선택할 수 있으므로 온-프레미스 SQL Server에서 변환할 때 가장 쉬운 방법입니다. 이는 [Azure 하이브리드 혜택](https://azure.microsoft.com/pricing/hybrid-benefit/)으로 라이선스 비용을 절감할 수 있는 유일한 옵션입니다. 
- [DTU 모델](../../database/service-tiers-dtu.md)은 기본 컴퓨팅, 메모리 및 I/O 리소스를 추상화하여 혼합 DTU를 제공합니다. 
- [서버리스 모델](../../database/serverless-tier-overview.md)은 초당 사용 요금이 청구되는 컴퓨팅 리소스로 자동 주문형 확장이 필요한 워크로드를 위한 것입니다. 서버리스 컴퓨팅 계층은 비활성 기간(스토리지만 청구됨) 동안 데이터베이스를 자동으로 일시 중지합니다. 작업이 반환되면 데이터베이스를 자동으로 다시 시작합니다. 

**서비스 계층**: 다양한 유형의 애플리케이션을 위해 설계된 세 가지 서비스 계층 중에서 선택합니다.

- [범용/표준 서비스 계층](../../database/service-tier-general-purpose.md)은 중간 계층 및 하위 계층에서 애플리케이션을 제공하는 데 적합한 컴퓨팅 및 스토리지와 함께 균형 잡힌 예산 중심 옵션을 제공합니다. 중복도는 스토리지 계층에서 기본 제공되어 오류를 복구합니다. 대부분의 데이터베이스 워크로드를 위해 설계되었습니다. 
- [중요 비즈니스용/프리미엄 서비스 계층](../../database/service-tier-business-critical.md)은 높은 트랜잭션 속도, 낮은 대기 시간 I/O 및 높은 수준의 복원력이 필요한 상위 계층 애플리케이션을 위한 것입니다. 보조 복제본은 장애 조치(failover)에 사용할 수 있으며 읽기 워크로드를 오프로드합니다.
- [하이퍼스케일 서비스 계층](../../database/service-tier-hyperscale.md)은 데이터 볼륨이 증가하여 데이터베이스 크기를 100TB까지 자동으로 확장해야 하는 데이터베이스용입니다. 대규모 데이터베이스용으로 설계되었습니다. 

> [!IMPORTANT]
> [트랜잭션 로그 속도](../../database/resource-limits-logical-server.md#transaction-log-rate-governance)는 높은 수집 속도를 제한하기 위해 Azure SQL Database에서 관리됩니다. 따라서 마이그레이션 중에 CPU 또는 처리량에 대한 부담을 줄이기 위해 대상 데이터베이스 리소스(vCore 또는 DTU)를 확장해야 할 수 있습니다. 적절한 크기의 대상 데이터베이스를 선택하지만 필요 시 마이그레이션을 위해 리소스를 확장할 계획입니다. 


### <a name="sql-server-vm-alternative"></a>SQL Server VM 대안

비즈니스에 [Azure Virtual Machines의 SQL Server](../../virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md)를 Azure SQL Database보다 더 적합한 대상으로 만드는 요구 사항이 있을 수 있습니다. 

다음 조건 중 하나가 비즈니스에 적용되는 경우 대신 SQL Server VM(가상 머신)으로 전환하는 것을 고려해보세요. 

- SQL Server와 동일한 가상 머신에 타사 또는 사용자 지정 에이전트를 설치하는 것과 같이 운영 체제 또는 파일 시스템에 직접 액세스해야 함 
- FileStream/FileTable, PolyBase 및 인스턴스 간 트랜잭션과 같이 아직 지원되지 않는 기능에 대한 엄격한 종속성이 있음 
- 특정 버전의 SQL Server(예: 2012)를 유지해야 함 
- 컴퓨팅 요구 사항이 관리형 인스턴스에서 제공하는 것보다 훨씬 낮고(예: 하나의 vCore) 데이터베이스 통합이 허용되지 않는 옵션임 


## <a name="migration-tools"></a>마이그레이션 도구 

다음 마이그레이션 도구를 권장합니다. 

|기술 | 설명|
|---------|---------|
| [Azure Migrate](../../../migrate/how-to-create-azure-sql-assessment.md) | 이 Azure 서비스는 VMware에서 대규모 SQL 데이터 자산을 검색하고 평가하는 데 도움이 됩니다. Azure SQL 배포 권장 사항, 대상 크기 조정 및 월간 예상 비용을 제공합니다. | 
|[데이터 Migration Assistant](/sql/dma/dma-migrateonpremsqltosqldb)|Microsoft의 이 데스크톱 도구는 SQL Server 및 Azure SQL Database로의 단일 데이터베이스 마이그레이션(스키마 및 데이터 모두)에 대한 원활한 평가를 제공합니다. </br></br>이 도구는 온-프레미스 서버 또는 원본 데이터베이스에 연결된 로컬 컴퓨터에 설치할 수 있습니다. 마이그레이션 프로세스는 원본 및 대상 데이터베이스의 개체 간의 논리적 데이터 이동입니다.|
|[Azure Database Migration Service](../../../dms/tutorial-sql-server-to-azure-sql.md)|이 Azure 서비스는 Azure Portal을 통해 또는 PowerShell을 통해 자동으로 SQL Server 데이터베이스를 Azure SQL Database로 마이그레이션할 수 있습니다. Database Migration Service에서는 원본 SQL Server 데이터베이스에 대한 연결을 보장하기 위해 프로비전 중에 선호하는 Azure 가상 네트워크를 선택해야 합니다. 단일 데이터베이스 또는 대규모로 마이그레이션할 수 있습니다. |
| | |


다음 표에는 대체 마이그레이션 도구가 나와 있습니다. 

|기술 |설명  |
|---------|---------|
|[트랜잭션 복제](../../database/replication-to-sql-database.md)|트랜잭션 일관성을 유지하면서 게시자-구독자 유형 마이그레이션 옵션을 제공하여 원본 SQL Server 데이터베이스 테이블에서 Azure SQL Database로 데이터를 복제합니다. 증분 데이터 변경 사항은 게시자에서 발생하면 구독자에게 전파됩니다.|
|[가져오기 내보내기 서비스/BACPAC](../../database/database-import.md)|[BACPAC](/sql/relational-databases/data-tier-applications/data-tier-applications#bacpac)는 데이터베이스의 스키마와 데이터를 캡슐화하는 확장명이 .bacpac인 Windows 파일입니다. BACPAC를 사용하여 SQL Server 원본에서 데이터를 내보내고 Azure SQL Database로 데이터를 가져올 수 있습니다. BACPAC 파일은 Azure Portal을 통해 새 SQL 데이터베이스로 가져올 수 있습니다. </br></br> 데이터베이스 크기가 크거나 데이터베이스 수가 많은 경우 규모와 성능을 얻으려면 [SqlPackage](../../database/database-import.md#using-sqlpackage) 명령줄 도구를 사용하여 데이터베이스를 내보내고 가져오는 것이 좋습니다.|
|[대량 복사](/sql/relational-databases/import-export/import-and-export-bulk-data-by-using-the-bcp-utility-sql-server)|[BCP(대량 복사 프로그램) 도구](/sql/tools/bcp-utility)는 SQL Server 인스턴스의 데이터를 데이터 파일로 복사합니다. 도구를 사용하여 원본에서 데이터를 내보내고 데이터 파일을 대상 SQL 데이터베이스로 가져옵니다. </br></br> 고속 대량 복사 작업을 통해 데이터를 Azure SQL Database로 이동하려면 [스마트 대량 복사 도구](/samples/azure-samples/smartbulkcopy/smart-bulk-copy/)를 사용해 병렬 복사 작업을 활용하여 전송 속도를 최대화할 수 있습니다.|
|[Azure Data Factory](../../../data-factory/connector-azure-sql-database.md)|Azure Data Factory의 [복사 작업](../../../data-factory/copy-activity-overview.md)은 기본 제공 커넥터와 [통합 런타임](../../../data-factory/concepts-integration-runtime.md)을 사용하여 원본 SQL Server 데이터베이스에서 Azure SQL Database로 데이터를 마이그레이션합니다.</br> </br> Data Factory는 SQL Server 원본에서 Azure SQL Database로 데이터를 이동하기 위해 광범위한 [커넥터](../../../data-factory/connector-overview.md)를 지원합니다.|
|[SQL 데이터 동기화](../../database/sql-data-sync-data-sql-server-sql-database.md)|SQL 데이터 동기화는 온-프레미스 및 클라우드 모두에서 여러 데이터베이스에서 양방향으로 선택한 데이터를 동기화할 수 있도록 Azure SQL Database에 구축된 서비스입니다.</br>데이터 동기화는 Azure SQL Database 또는 SQL Server의 여러 데이터베이스에서 데이터를 최신 상태로 업데이트해야 하는 경우에 유용합니다.|
| | |

## <a name="compare-migration-options"></a>마이그레이션 옵션 비교

마이그레이션 옵션을 비교하여 비즈니스 요구 사항에 적합한 경로를 선택합니다. 

다음 표에서는 권장하는 마이그레이션 옵션을 비교합니다. 

|마이그레이션 옵션  |사용 시기  |고려 사항  |
|---------|---------|---------|
|[데이터 Migration Assistant](/sql/dma/dma-migrateonpremsqltosqldb) | - 단일 데이터베이스를 마이그레이션합니다(스키마와 데이터 모두).  </br> - 데이터 마이그레이션 프로세스 중 가동 중지 시간을 수용할 수 있습니다. </br> </br> 지원되는 원본: </br> - SQL Server(2005~2019) 온-프레미스 또는 Azure VM </br> - AWS EC2 </br> - AWS RDS </br> - GCP 컴퓨팅 SQL Server VM | - 마이그레이션 작업은 데이터베이스 개체 간의 데이터 이동(원본에서 대상으로)을 수행하므로 사용량이 적은 시간에 실행하는 것이 좋습니다. </br> - Data Migration Assistant는 마이그레이션된 행 수를 포함하여 데이터베이스 개체별 마이그레이션 상태를 보고합니다.  </br> - 대규모 마이그레이션(데이터베이스 수 또는 데이터베이스 크기)의 경우 Azure Database Migration Service를 사용합니다.|
|[Azure Database Migration Service](../../../dms/tutorial-sql-server-to-azure-sql.md)| - 단일 데이터베이스 또는 대규모로 마이그레이션합니다. </br> - 마이그레이션 프로세스 중 가동 중지 시간을 수용할 수 있습니다. </br> </br> 지원되는 원본: </br> - SQL Server(2005~2019) 온-프레미스 또는 Azure VM </br> - AWS EC2 </br> - AWS RDS </br> - GCP 컴퓨팅 SQL Server VM | - [PowerShell](../../../dms/howto-sql-server-to-azure-sql-powershell.md)을 통해 대규모 마이그레이션을 자동화할 수 있습니다. </br> - 마이그레이션을 완료하는 데 걸리는 시간은 데이터베이스 크기와 데이터베이스의 개체 수에 따라 다릅니다. </br> - 원본 데이터베이스를 읽기 전용으로 설정해야 합니다. |
| | | |

다음 표에서는 대체 마이그레이션 옵션을 비교합니다. 

|메서드 또는 기술 |사용 시기    |고려 사항  |
|---------|---------|---------|
|[트랜잭션 복제](../../database/replication-to-sql-database.md)| - 원본 데이터베이스 테이블의 변경 내용을 대상 SQL Database 테이블로 지속적으로 게시하여 마이그레이션합니다. </br> - 선택한 테이블(데이터베이스의 하위 집합)의 전체 또는 부분 데이터베이스 마이그레이션을 수행합니다.  </br> </br> 지원되는 원본: </br> - [몇 가지 제한 사항이 있는 SQL Server(2016~2019)](/sql/relational-databases/replication/replication-backward-compatibility) </br> - AWS EC2  </br> - GCP 컴퓨팅 SQL Server VM  | - 다른 마이그레이션 옵션보다 설정이 비교적 복잡합니다.   </br> - 데이터베이스를 오프라인으로 전환하지 않고 데이터를 마이그레이션하는 연속 복제 옵션을 제공합니다.  </br> - 트랜잭션 복제에는 원본 SQL Server 인스턴스에서 게시자를 설정할 때 고려해야 할 제한 사항이 있습니다. 자세한 내용은 [개체 게시에 대한 제한 사항](/sql/relational-databases/replication/publish/publish-data-and-database-objects#limitations-on-publishing-objects)을 참조하세요. </br>- [복제 작업을 모니터링](/sql/relational-databases/replication/monitor/monitoring-replication)할 수 있습니다.    |
|[가져오기 내보내기 서비스/BACPAC](../../database/database-import.md)| - 개별 LOB(기간 업무) 애플리케이션 데이터베이스를 마이그레이션합니다. </br>- 소규모 데이터베이스에 적합합니다.  </br>  - 별도의 마이그레이션 서비스 또는 도구가 필요하지 않습니다. </br> </br> 지원되는 원본: </br> - SQL Server(2005~2019) 온-프레미스 또는 Azure VM </br> - AWS EC2 </br> - AWS RDS </br> - GCP 컴퓨팅 SQL Server VM  |  - 데이터를 원본에서 내보내고 대상에서 가져와야 하므로 가동 중지 시간이 필요합니다.   </br> - 내보내기 또는 가져오기에 사용되는 파일 형식 및 데이터 형식은 잘림 또는 데이터 유형 불일치 오류를 방지하기 위해 테이블 스키마와 일치해야 합니다.  </br> - 개체 수가 많은 데이터베이스를 내보내는 데 걸리는 시간이 상당히 길어질 수 있습니다.       |
|[대량 복사](/sql/relational-databases/import-export/import-and-export-bulk-data-by-using-the-bcp-utility-sql-server)| - 전체 또는 부분 데이터 마이그레이션을 수행합니다. </br> - 가동 중지 시간을 수용할 수 있습니다. </br> </br> 지원되는 원본: </br> - SQL Server(2005~2019) 온-프레미스 또는 Azure VM </br> - AWS EC2 </br> - AWS RDS </br> - GCP 컴퓨팅 SQL Server VM   | - 원본에서 데이터를 내보내고 대상으로 가져올 때 가동 중지 시간이 필요합니다. </br> - 내보내기 또는 가져오기에 사용되는 파일 형식 및 데이터 형식은 테이블 스키마와 일치해야 합니다. |
|[Azure Data Factory](../../../data-factory/connector-azure-sql-database.md)| - 원본 SQL Server 데이터베이스에서 데이터를 마이그레이션하거나 변환합니다. </br> - 여러 데이터 원본의 데이터를 Azure SQL Database로 병합하는 것은 일반적으로 BI(비즈니스 인텔리전스) 워크로드를 위한 것입니다.  |  -원본에서 대상으로 데이터를 이동하려면 Data Factory에서 데이터 이동 파이프라인을 만들어야 합니다.   </br> - [비용](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/)은 중요한 고려 사항이며 파이프라인 트리거, 활동 실행 및 데이터 이동 기간과 같은 요소를 기반으로 합니다. |
|[SQL 데이터 동기화](../../database/sql-data-sync-data-sql-server-sql-database.md)| - 원본 데이터베이스와 대상 데이터베이스 간에 데이터를 동기화합니다.</br> - 양방향 흐름에서 Azure SQL Database와 온-프레미스 SQL Server 간의 지속적인 동기화를 실행하는 데 적합합니다. | - Azure SQL Database는 구성원 데이터베이스로 온-프레미스 SQL Server 데이터베이스와 동기화하기 위한 허브 데이터베이스여야 합니다.</br> - 트랜잭션 복제와 비교하여 SQL 데이터 동기화는 온-프레미스와 Azure SQL Database 간의 양방향 데이터 동기화를 지원합니다. </br> - 워크로드에 따라 성능에 더 큰 영향을 줄 수 있습니다.|
| | | |

## <a name="feature-interoperability"></a>기능 상호 운용성 

다른 SQL Server 기능을 사용하는 워크로드를 마이그레이션하는 경우 추가 고려 사항이 있습니다.

### <a name="sql-server-integration-services"></a>SQL Server Integration Services
[Azure Data Factory](../../../data-factory/introduction.md)의 Azure-SSIS 런타임에 패키지를 다시 배포하여 SSIS(SQL Server Integration Services) 패키지를 Azure로 마이그레이션합니다. Azure Data Factory는 Azure에서 SSIS 패키지를 실행하도록 빌드된 런타임을 제공하여 [SSIS 패키지의 마이그레이션을 지원](../../../data-factory/scenario-ssis-migration-overview.md#azure-sql-database-as-database-workload-destination)합니다. 또는 [데이터 흐름](../../../data-factory/concepts-data-flow-overview.md)을 사용하여 Azure Data Factory에서 기본적으로 SSIS ETL(추출, 변환,로드) 논리를 다시 작성할 수 있습니다.


### <a name="sql-server-reporting-services"></a>SQL Server Reporting Services
SSRS(SQL Server Reporting Services) 보고서를 Power BI의 페이지가 매겨진 보고서로 마이그레이션합니다. 보고서를 쉽게 준비하고 마이그레이션하려면  [RDL 마이그레이션 도구](https://github.com/microsoft/RdlMigration)를 사용하세요. Microsoft는 고객이 SSRS 서버에서 Power BI로 RDL(Report Definition Language) 보고서를 마이그레이션할 수 있도록 이 도구를 개발했습니다. 이 도구는 GitHub에서 받을 수 있으며, 마이그레이션 시나리오를 처음부터 끝까지 단계별로 안내합니다. 

### <a name="high-availability"></a>고가용성
Always On 장애 조치(failover) 클러스터 인스턴스 및 Always On 가용성 그룹과 같은 SQL Server 고 가용성 기능의 수동 설정은 대상 SQL 데이터베이스에서 더 이상 사용되지 않습니다. 고가용성 아키텍처는 이미 Azure SQL Database에 대한 [범용(표준 가용성 모델)](../../database/high-availability-sla.md#basic-standard-and-general-purpose-service-tier-locally-redundant-availability) 및 [중요 비즈니스용(프리미엄 가용성 모델)](../../database/high-availability-sla.md#premium-and-business-critical-service-tier-locally-redundant-availability) 서비스 계층 모두에 이미 내장되어 있습니다. 중요 비즈니스용/프리미엄 서비스 계층은 읽기 전용 목적으로 보조 노드 중 하나에 연결할 수 있는 읽기 스케일 아웃도 제공합니다. 

Azure SQL Database에 포함된 고가용성 아키텍처 외에도 [자동 장애 조치(failover) 그룹](../../database/auto-failover-group-overview.md) 기능을 사용하면 관리형 인스턴스에서 다른 지역으로의 데이터베이스 복제 및 장애 조치(failover)를 관리할 수 있습니다. 

### <a name="logins-and-groups"></a>로그인 및 그룹

Azure SQL Database에서는 Windows 로그인이 지원되지 않습니다. 대신 Azure Active Directory 로그인을 생성합니다. SQL 로그인을 수동으로 다시 생성합니다. 

### <a name="sql-agent-jobs"></a>SQL 에이전트 작업
SQL 에이전트 작업은 Azure SQL Database에서 직접 지원되지 않으며 [탄력적 데이터베이스 작업(미리 보기)](../../database/job-automation-overview.md)에 배포해야 합니다.

### <a name="system-databases"></a>시스템 데이터베이스
Azure SQL Database의 경우 해당 시스템 데이터베이스만 [master](/sql/relational-databases/databases/master-database) 및 tempdb입니다. 자세히 알아보려면 [Azure SQL Database의 Tempdb](/sql/relational-databases/databases/tempdb-database#tempdb-database-in-sql-database)를 참조하세요.

## <a name="advanced-features"></a>고급 기능 

SQL Database의 고급 클라우드 기반 기능을 활용해보세요. 예를 들어 서비스가 자동으로 수행되므로 백업 관리에 대해 걱정할 필요가 없습니다. [보존 기간 내의 특정 시점](../../database/recovery-using-backups.md#point-in-time-restore)으로 복원할 수 있습니다. 

보안을 강화하려면  [Azure AD 인증](../../database/authentication-aad-overview.md), [감사](../../database/auditing-overview.md),  [위협 검색](../../database/azure-defender-for-sql.md),  [행 수준 보안](/sql/relational-databases/security/row-level-security) 및  [동적 데이터 마스킹](/sql/relational-databases/security/dynamic-data-masking) 사용을 고려하세요.

SQL Database는 고급 관리 및 보안 기능 외에도 [워크로드를 모니터링 및 튜닝](../../database/monitor-tune-overview.md)할 수 있는 도구를 제공합니다. [Azure SQL 분석(미리 보기)](../../../azure-monitor/insights/azure-sql.md)는 Azure SQL Database의 모든 데이터베이스 성능을 대규모로 단일 보기에서 여러 구독에 걸쳐 모니터링하기 위한 고급 솔루션입니다. Azure SQL 분석은 성능 문제 해결을 위한 기본 제공 인텔리전스를 사용하여 주요 성능 메트릭을 수집하고 시각화합니다.

[자동 튜닝](/sql/relational-databases/automatic-tuning/automatic-tuning#automatic-plan-correction)  은 SQL 실행 계획의 성능을 지속적으로 모니터링하고 식별된 성능 문제를 자동으로 수정합니다. 


## <a name="migration-assets"></a>마이그레이션 자산 

추가 지원이 필요한 경우 실제 마이그레이션 프로젝트용으로 개발된 다음 리소스를 참조하세요.

|자산  |Description  |
|---------|---------|
|[데이터 워크로드 평가 모델 및 도구](https://www.microsoft.com/download/details.aspx?id=103130)| 이 도구는 워크로드에 권장되는 "최적의" 대상 플랫폼, 클라우드 준비 상태 및 애플리케이션/데이터베이스 수정 수준을 제공합니다. 대상 플랫폼에 대해 자동화되고 균일한 의사 결정 프로세스를 제공하여 대규모 자산 평가를 가속화할 수 있는 간단한 원클릭 계산 및 보고서 생성을 제공합니다.|
|[PowerShell을 사용하여 대량 데이터베이스 만들기](https://www.microsoft.com/download/details.aspx?id=103107)|리소스 그룹(create_rg.ps1), [Azure의 논리 서버](../../database/logical-servers.md)(create_sqlserver.ps1) 및 SQL Database(create_sqldb.ps1)를 만드는 세 가지 PowerShell 스크립트 집합을 사용할 수 있습니다. 스크립트에는 반복 기능이 포함되어 있어 필요한 만큼 서버와 데이터베이스를 반복하고 만들 수 있습니다.|
|[MSSQL-Scripter 및 PowerShell을 사용하여 대량 스키마 배포](https://www.microsoft.com/download/details.aspx?id=103032)|이 자산은 리소스 그룹을 만들고, Azure SQL Database를 호스팅하기 위해 하나 이상의 [논리 서버](../../database/logical-servers.md)를 만들고, 온-프레미스 SQL Server 인스턴스(또는 여러 SQL Server 2005+ 인스턴스)에서 모든 스키마를 내보내고, Azure SQL Database에 스키마를 가져옵니다.|
|[SQL Server 에이전트 작업을 탄력적 데이터베이스 작업으로 변환](https://www.microsoft.com/download/details.aspx?id=103123)|이 스크립트는 원본 SQL Server 에이전트 작업을 탄력적 데이터베이스 작업으로 마이그레이션합니다.|
|[온-프레미스 SQL Server 로그인을 Azure SQL Database로 이동하는 유틸리티](https://www.microsoft.com/download/details.aspx?id=103111)|PowerShell 스크립트는 T-SQL 명령 스크립트를 만들어 로그인을 다시 만들고 온-프레미스 SQL Server에서 Azure SQL Database로 데이터베이스 사용자를 선택할 수 있습니다. 이 도구를 사용하면 Windows Server Active Directory 계정을 Azure AD 계정으로 자동 매핑하고 선택적으로 SQL Server 네이티브 로그인을 마이그레이션할 수 있습니다.|
|[Logman을 사용하는 Perfmon 데이터 수집 자동화](https://www.microsoft.com/download/details.aspx?id=103114)|Logman 도구를 사용하여 Perfmon 데이터를 수집해 기준 성능을 쉽게 이해할 수 있으며 마이그레이션 대상 권장 사항을 확인할 수 있습니다. 이 도구는 logman.exe를 사용하여 원격 SQL Server 인스턴스에 설정된 성능 카운터를 생성, 시작, 중지 및 삭제하는 명령을 만듭니다.|

데이터 SQL 엔지니어링 팀이 이러한 리소스를 개발했습니다. 이 팀의 핵심 선언은 Microsoft의 Azure 데이터 플랫폼으로의 데이터 플랫폼 마이그레이션 프로젝트에 대한 복잡한 현대화의 장애물을 제거하고 속도를 높이는 것입니다.

## <a name="next-steps"></a>다음 단계

- SQL Server 데이터베이스를 Azure SQL Database로 마이그레이션하려면 [SQL Server에서 Azure SQL Database로 마이그레이션 가이드](sql-server-to-sql-database-guide.md)를 참조하세요.

- 데이터베이스 및 데이터 마이그레이션 시나리오와 특수 작업에 도움이 되는 서비스 및 도구 매트릭스는 [데이터 마이그레이션 서비스 및 도구](../../../dms/dms-tools-matrix.md)를 참조하세요.

- SQL Database에 대해 자세히 알아보려면 다음을 참조하세요.
   - [Azure SQL Database 개요](../../database/sql-database-paas-overview.md)
   - [Azure 총 소유 비용 계산기](https://azure.microsoft.com/pricing/tco/calculator/) 

- 클라우드 마이그레이션의 프레임워크 및 채택 주기에 대해 자세히 알아보려면 다음을 참조하세요.
   -  [Azure용 클라우드 채택 프레임워크](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Azure로 마이그레이션된 워크로드의 비용 계산 및 크기 조정 모범 사례](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 

- 애플리케이션 액세스 레이어를 평가하려면 [Data Access Migration Toolkit(미리 보기)](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit)를 참조하세요.

- 데이터 액세스 레이어에 대한 A/B 테스트를 수행하는 방법에 대한 자세한 내용은 [데이터베이스 실험 도우미](/sql/dea/database-experimentation-assistant-overview)를 참조하세요.
