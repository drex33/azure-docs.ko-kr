---
title: 범용 및 중요 비즈니스용 서비스 계층
titleSuffix: Azure SQL Database & SQL Managed Instance
description: 이 문서에서는 Azure SQL Database 및 Azure SQL Managed Instance에서 사용하는 vCore 기반 구매 모델의 범용 및 중요 비지니스용 서비스 계층에 대해 설명합니다.
services: sql-database
ms.service: sql-db-mi
ms.subservice: service-overview
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: dimitri-furman
ms.author: dfurman
ms.reviewer: mathoma
ms.date: 7/7/2021
ms.openlocfilehash: 9fbcf03159e11aa9d2951f3f951290eb6e51b511
ms.sourcegitcommit: bee590555f671df96179665ecf9380c624c3a072
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/07/2021
ms.locfileid: "129670141"
---
# <a name="azure-sql-database-and-azure-sql-managed-instance-service-tiers"></a>Azure SQL Database 및 Azure SQL Managed Instance 서비스 계층
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

 Azure SQL Database와 Azure SQL Managed Instance 모두에서 다음 두 [vCore](service-tiers-vcore.md) 서비스 계층을 사용할 수 있습니다.

- [범용](service-tier-general-purpose.md)은 일반적인 성능 및 가용성 요구 사항이 있는 대부분의 워크로드용으로 설계된 예산 친화적 계층입니다.
- [중요 비즈니스](service-tier-business-critical.md) 계층은 엄격한 가용성 요구 사항이 있는 성능이 중요한 워크로드를 위해 설계되었습니다.

Azure SQL Database 하이퍼스케일 서비스 계층도 제공합니다. 

- [하이퍼스케일](service-tier-hyperscale.md)은 대부분의 비즈니스 워크로드에 맞게 설계되었으며 확장성이 뛰어난 스토리지, 읽기 스케일아웃, 신속한 크기 조정 및 빠른 데이터베이스 복원 기능을 제공합니다.

## <a name="service-tier-comparison"></a>서비스 계층 비교

다음 표에서는 서비스 계층의 주요 차이점에 대해 설명합니다.

|-| 리소스 종류 | 범용 | 하이퍼스케일 | 중요 비즈니스용 |
|:---:|:---:|:---:|:---:|:---:|
| **적합한 대상** | |  예산 중심의 균형 잡힌 컴퓨팅 및 스토리지 옵션을 제공합니다. | 대부분의 비즈니스 워크로드. 스토리지 크기 최대 100TB까지 자동 확장, 유연한 수직 및 수평 컴퓨팅 확장, 빠른 데이터베이스 복원 | 트랜잭션 속도가 높고 IO 대기 시간이 적은 OLTP 애플리케이션 동시에 업데이트된 여러 복제본을 사용하여 가장 높은 오류 복원 및 빠른 장애 조치를 제공|
| **리소스 유형에서 사용 가능:** ||Azure SQL Database/SQL Managed Instance | 단일 Azure SQL 데이터베이스 | Azure SQL Database/SQL Managed Instance |
| **컴퓨팅 크기**| SQL Database | vCore 1~80개 | vCore 1~80개 | vCore 1~128개 |
| | SQL Managed Instance | 4, 8, 16, 24, 32, 40, 64, 80 vCore | 해당 없음 | 4, 8, 16, 24, 32, 40, 64, 80 vCore |
| | SQL Managed Instance 풀 | 2, 4, 8, 16, 24, 32, 40, 64, 80  vCore | 해당 없음 | 해당 없음 |
| **스토리지 유형** | 모두 | 원격 스토리지 | 계층형 원격 및 로컬 SSD 스토리지 | 로컬 SSD 스토리지 |
| **데이터베이스 크기** | SQL Database | 1GB – 4TB | 40GB - 100TB | 1GB – 4TB |
| | SQL Managed Instance  | 32GB~8TB </br> 16 TB (미리 보기) 코어 수에 따라 Gen5만| 해당 없음 | 32GB~4TB |
| **스토리지 크기** | SQL Database | 1GB – 4TB | 40GB - 100TB | 1GB – 4TB |
| | SQL Managed Instance  | 32GB~8TB | 해당 없음 | 32GB~4TB |
| **TempDB 크기** | SQL Database | [vCore당 32GB](resource-limits-vcore-single-databases.md) | [vCore당 32GB](resource-limits-vcore-single-databases.md) | [vCore당 32GB](resource-limits-vcore-single-databases.md) |
| | SQL Managed Instance  | [vCore당 24GB](../managed-instance/resource-limits.md#service-tier-characteristics) | 해당 없음 | [스토리지 크기에 따라](../managed-instance/resource-limits.md#service-tier-characteristics) 최대 4TB 한도 |
| **로그 쓰기 처리량** | SQL Database | 단일 데이터베이스: [vCore당 4.5MB/s(최대 50MB/s)](resource-limits-vcore-single-databases.md) <br> 탄력적 풀: [vCore당 6MB/s(최대 62.5MB/s)](resource-limits-vcore-elastic-pools.md)| 100MB/초 | 단일 데이터베이스: [vCore당 12MB/s(최대 96MB/s)](resource-limits-vcore-single-databases.md) <br> 탄력적 풀: [vCore당 15MB/s(최대 120MB/s)](resource-limits-vcore-elastic-pools.md)|
| | SQL Managed Instance | [vCore 당 3MB/s(최대 22MB/초)](../managed-instance/resource-limits.md#service-tier-characteristics) | 해당 없음 | [vCore 당 4MB/s(최대 48MB/초)](../managed-instance/resource-limits.md#service-tier-characteristics) |
|**가용성**|모두| 99.99% |  [보조 복제본이 하나면 99.95%, 추가 복제본이 있으면 99.99%](service-tier-hyperscale-frequently-asked-questions-faq.yml#what-slas-are-provided-for-a-hyperscale-database-) | 99.99% <br/> [영역 중복 단일 데이터베이스가 포함되면 99.995%](https://azure.microsoft.com/blog/understanding-and-leveraging-azure-sql-database-sla/) |
|**Backup**|모두|RA-GRS, 1-35일(기본값: 7일) | RA-GRS, 7일, 빠른 PITR(특정 시점 복원) | RA-GRS, 1-35일(기본값: 7일) |
|**메모리 내 OLTP** | | 해당 없음 | 일부 지원. 메모리 최적화 테이블 형식, 테이블 변수 및 고유하게 컴파일된 모듈이 지원됩니다. | 사용 가능 |
|**읽기 전용 복제본**| | 0 기본 제공 <br> 0-4 [지역에서 복제](active-geo-replication-overview.md)사용 | 0-4 기본 제공 | 1 기본 제공, 가격에 포함 <br> 0-4 [지역에서 복제](active-geo-replication-overview.md)사용 |
|**가격 책정 및 청구** | SQL Database | [vCore, 예약된 스토리지 및 백업 스토리지](https://azure.microsoft.com/pricing/details/sql-database/single/)가 청구됩니다. <br/>IOPS에는 요금이 부과되지 않습니다. | [각 복제본 및 사용된 스토리지에 대한 vCore](https://azure.microsoft.com/pricing/details/sql-database/single/)에는 요금이 부과됩니다. <br/>IOPS는 아직 청구되지 않습니다. | [vCore, 예약된 스토리지 및 백업 스토리지](https://azure.microsoft.com/pricing/details/sql-database/single/)가 청구됩니다. <br/>IOPS에는 요금이 부과되지 않습니다. |
|| SQL Managed Instance | [vCore, 예약된 스토리지 및 백업 스토리지](https://azure.microsoft.com/pricing/details/sql-database/managed/)에 대한 요금이 청구됩니다. <br/>IOPS는 요금이 청구되지 않음| 해당 없음 | [vCore, 예약된 스토리지 및 백업 스토리지](https://azure.microsoft.com/pricing/details/sql-database/managed/)에 대한 요금이 청구됩니다. <br/>IOPS에는 요금이 부과되지 않습니다.| 
|**할인 모델**| | [예약 인스턴스](reserved-capacity-overview.md)<br/>[Azure 하이브리드 혜택](../azure-hybrid-benefit.md)(개발/테스트 구독에서 사용할 수 없음)<br/>[Enterprise](https://azure.microsoft.com/offers/ms-azr-0148p/) 및 [종량제](https://azure.microsoft.com/offers/ms-azr-0023p/) 개발/테스트 구독| [Azure 하이브리드 혜택](../azure-hybrid-benefit.md)(개발/테스트 구독에서 사용할 수 없음)<br/>[Enterprise](https://azure.microsoft.com/offers/ms-azr-0148p/) 및 [종량제](https://azure.microsoft.com/offers/ms-azr-0023p/) 개발/테스트 구독| [예약 인스턴스](reserved-capacity-overview.md)<br/>[Azure 하이브리드 혜택](../azure-hybrid-benefit.md)(개발/테스트 구독에서 사용할 수 없음)<br/>[Enterprise](https://azure.microsoft.com/offers/ms-azr-0148p/) 및 [종량제](https://azure.microsoft.com/offers/ms-azr-0023p/) 개발/테스트 구독|

세부 정보는 [Azure SQL Database(vCore)](resource-limits-vcore-single-databases.md)의 서비스 계층, [단일 Azure SQL Database(DTU)](resource-limits-dtu-single-databases.md), [풀링된 Azure SQL Database(DTU)](resource-limits-dtu-single-databases.md) 및 [Azure SQL Managed Instance](../managed-instance/resource-limits.md) 페이지 간의 자세한 차이점을 참조하세요.

> [!NOTE]
> 하이퍼스케일 서비스 계층에 대한 정보는 [하이퍼스케일 서비스 계층](service-tier-hyperscale.md)을 참조하세요. vCore 기반 구매 모델과 DTU 기반 구매 모델을 비교하려면 [모델 및 리소스 구매](purchasing-models.md)를 참조하세요.

## <a name="data-and-log-storage"></a>데이터 및 로그 스토리지

다음 요소는 데이터 및 로그 파일에 사용되는 스토리지의 양에 영향을 주며 범용 및 중요 비즈니스 계층에 적용됩니다. 하이퍼스케일의 데이터 및 로그 스토리지에 대한 세부 정보는 [하이퍼스케일 서비스 계층](service-tier-hyperscale.md)을 참조하세요.

- 각각의 컴퓨팅 크기는 최대 데이터 크기를 지원하면 기본값은 32GB입니다.
- 최대 데이터 크기를 구성하면 로그 파일에 대 해 추가 30%의 스토리지가 자동으로 추가됩니다.
- 1GB ~ 지원되는 스토리지 크기의 최대 데이터 크기 범위에서 최대 데이터 크기를 1GB 단위로 선택할 수 있습니다.
- 범용 서비스 계층의 경우 `tempdb`에서 로컬 SSD를 사용하며, 이 스토리지의 비용이 vCore 가격에 포함됩니다.
- 중요 비즈니스 서비스 계층의 경우 `tempdb`에서 데이터 및 로그 파일이 포함된 로컬 및 SSD 스토리지를 사용하고 `tempdb` 스토리지의 비용이 vCore 가격에 포함됩니다.
- SQL Managed Instance의 최대 스토리지 크기는 32GB의 배수 단위로 지정해야 합니다.

> [!IMPORTANT]
> 범용 및 중요 비즈니스용 계층에서 데이터베이스, 탄력적 풀 또는 관리되는 인스턴스에 대해 구성된 최대 스토리지 크기에 대해 요금이 청구됩니다. 하이퍼스케일 계층에서는 할당된 데이터 스토리지에 대한 요금이 청구됩니다.

SQL Database에서 현재 할당 및 사용된 데이터 스토리지 크기를 모니터링하려면 각각 *allocated_data_storage* 및 *storage* Azure Monitor [메트릭](../../azure-monitor/essentials/metrics-supported.md#microsoftsqlserversdatabases)을 사용합니다. SQL Managed Instance에 사용한 총 인스턴스 스토리지를 모니터링하려면 *storage_space_used_mb* [메트릭](../../azure-monitor/essentials/metrics-supported.md#microsoftsqlmanagedinstances)을 사용합니다. T-SQL을 사용하는 데이터베이스에서 개별 데이터 및 로그 파일의 현재 할당 및 사용 스토리지 크기를 모니터링하려면 [sys.database_files](/sql/relational-databases/system-catalog-views/sys-database-files-transact-sql) view and the [FILEPROPERTY(... , 'SpaceUsed')](/sql/t-sql/functions/fileproperty-transact-sql) 함수를 사용합니다.

> [!TIP]
> 경우에 따라 사용하지 않는 공간을 회수하기 위해 데이터베이스를 축소해야 할 수도 있습니다. 자세한 내용은 [Azure SQL Database의 파일 공간 관리](file-space-manage.md)를 참조하세요.

## <a name="backups-and-storage"></a>백업 및 스토리지

데이터베이스 백업용 스토리지는 SQL Database 및 SQL Managed Instance의 [PITR(특정 시점 복원)](recovery-using-backups.md) 및 [LTR(장기 보존)](long-term-retention-overview.md) 기능을 지원하기 위해 할당됩니다. 이 스토리지는 데이터 및 로그 파일 스토리지와는 별개이며 별도로 청구됩니다.

- **PITR**: 범용 및 중요 비즈니스 계층에서 개별 데이터베이스 백업은 [RA-GRS(읽기 액세스 지역 중복) 스토리지](../../storage/common/geo-redundant-design.md)에 자동으로 복사됩니다. 스토리지 크기는 새 백업이 생성될 때 동적으로 늘어납니다. 전체, 차등 및 트랜잭션 로그 백업에 스토리지를 사용합니다. 스토리지 사용량은 백업에 대해 구성된 데이터베이스 변동률과 보존 기간에 따라 다릅니다. 각 데이터베이스마다 SQL Database에 대해 1~35일 범위, SQL Managed Instance에 대해 0~35일 범위의 개별 보존 기간을 구성할 수 있습니다. 구성된 최대 데이터 크기와 같은 백업 스토리지 용량이 추가 요금 없이 제공됩니다.
- **LTR**: 최대 10년 동안 전체 백업의 장기 보존을 구성하는 옵션도 있습니다. LTR 정책을 설정하는 경우 이러한 백업은 RA-GRS 스토리지에 자동으로 저장되지만 백업 복사 빈도를 제어할 수 있습니다. 서로 다른 준수 요구 사항을 충족하려면 주별, 월별 또는 연도별 백업에 대해 다른 보존 기간을 선택할 수 있습니다. 선택한 구성에 따라 LTR 백업에 사용되는 스토리지의 양이 결정됩니다. 자세한 내용은 [장기 백업 보존](long-term-retention-overview.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

vCore 서비스 계층에서 사용 가능한 특정 컴퓨팅 및 스토리지 크기에 대한 세부 정보는 다음을 참조하세요. 

- [Azure SQL Database용 vCore 기반 리소스 한도](resource-limits-vcore-single-databases.md)
- [Azure SQL Database에서 풀링된 데이터베이스용 vCore 기반 리소스 한도](resource-limits-vcore-elastic-pools.md)
- [Azure SQL Managed Instance용 vCore 기반 리소스 한도](../managed-instance/resource-limits.md)