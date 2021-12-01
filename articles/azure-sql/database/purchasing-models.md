---
title: 구매 모델
titleSuffix: Azure SQL Database & SQL Managed Instance
description: Azure SQL Database와 Azure SQL Managed Instance에 사용할 수 있는 구매 모델에 대해 알아봅니다.
services: sql-database
ms.service: sql-db-mi
ms.subservice: service-overview
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: LitKnd
ms.author: kendralittle
ms.reviewer: ''
ms.date: 05/28/2020
ms.openlocfilehash: 2395a6fb13daeddee3452f80acb1bcfd4212f7d3
ms.sourcegitcommit: 66b6e640e2a294a7fbbdb3309b4829df526d863d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/01/2021
ms.locfileid: "133359135"
---
# <a name="choose-between-the-vcore-and-dtu-purchasing-models---azure-sql-database-and-sql-managed-instance"></a>vCore와 DTU 구매 모델 중 선택 - Azure SQL Database 및 SQL Managed Instance
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Azure SQL Database와 Azure SQL Managed Instance를 사용하면 성능 및 비용 요구에 맞는 완전 관리형 PaaS(Platform as a Service) 데이터베이스 엔진을 쉽게 구매할 수 있습니다. Azure SQL Database에 대해 선택한 배포 모델에 따라 효과적인 구매 모델을 선택할 수 있습니다.

- [vCore(가상 코어) 기반 구매 모델](service-tiers-vcore.md)(권장) 이 구매 모델은 프로비저닝된 컴퓨팅 계층과 서버리스 컴퓨팅 계층 중에서 선택할 수 있습니다. 프로비저닝된 컴퓨팅 계층을 선택하면 워크로드에 대해 항상 프로비저닝되는 정확한 양의 컴퓨팅 리소스를 선택합니다. 서버리스 컴퓨팅 계층을 선택하면 구성 가능한 컴퓨팅 범위에 대해 컴퓨팅 리소스의 자동 스케일링을 지정합니다. 이 컴퓨팅 계층을 선택한 경우 워크로드 활동을 기반으로 데이터베이스를 자동으로 일시 중지하고 다시 시작할 수도 있습니다. 시간 단위당 vCore 단가는 서버리스 컴퓨팅 계층보다 프로비저닝된 컴퓨팅 계층에서 더 낮습니다.
- [DTU(데이터베이스 트랜잭션 단위) 기반 구매 모델](service-tiers-dtu.md). 이 구매 모델은 일반적인 워크로드에 맞춰 부하가 분산되는 컴퓨팅 및 스토리지 패키지 번들을 제공합니다.


> [!div class="nextstepaction"]
> [Azure SQL 개선을 위한 설문 조사](https://aka.ms/AzureSQLSurveyNov2021) 


다음과 같은 두 가지 구매 모델이 있습니다.

- [vCore 기반 구매 모델](service-tiers-vcore.md)은 [Azure SQL Database](sql-database-paas-overview.md)와 [Azure SQL Managed Instance](../managed-instance/sql-managed-instance-paas-overview.md) 둘 다에 사용할 수 있습니다. [하이퍼스케일 서비스 계층](service-tier-hyperscale.md)은 [vCore 기반 구매 모델](service-tiers-vcore.md)을 사용하는 단일 데이터베이스에 사용할 수 있습니다.
- [DTU 기반 구매 모델](service-tiers-dtu.md)은 [Azure SQL Database](single-database-manage.md)에 사용할 수 있습니다.

다음 표와 차트는 vCore 기반 구매 모델과 DTU 기반 구매 모델을 비교/대조합니다.

|**구매 모델**|**설명**|**적합한 대상**|
|---|---|---|
|DTU 기반|컴퓨팅, 스토리지 및 I/O 리소스를 번들로 묶은 측정값을 기반으로 합니다. 컴퓨팅 크기는 단일 데이터베이스에 대해서는 DTU, 탄력적 풀에 대해서는 eDTU(탄력적 데이터베이스 트랜잭션 단위)로 표시됩니다. DTU 및 eDTU에 대한 자세한 내용은 [DTU 및 eDTU란?](purchasing-models.md#dtu-based-purchasing-model)을 참조하세요.|미리 구성된 간단한 리소스 옵션을 원하는 고객|
|vCore 기반|이 모델을 사용하면 컴퓨팅 및 스토리지 리소스를 독립적으로 선택할 수 있습니다. vCore 기반 구매 모델을 사용하면 SQL Server용 [Azure 하이브리드 혜택](https://azure.microsoft.com/pricing/hybrid-benefit/)을 사용하여 비용을 절약할 수도 있습니다.|유연성, 제어 및 투명성을 중요시하는 고객|
||||  

![가격 책정 모델 비교](./media/purchasing-models/pricing-model.png)

클라우드 비용을 최적화하여 비용을 절감하고 싶습니까?

[!INCLUDE [cost-management-horizontal](../../../includes/cost-management-horizontal.md)]

## <a name="compute-costs"></a>컴퓨팅 비용

### <a name="provisioned-compute-costs"></a>프로비저닝된 컴퓨팅 비용

프로비저닝된 컴퓨팅 계층에서 컴퓨팅 비용은 애플리케이션에 대해 프로비저닝되는 총 컴퓨팅 용량을 반영합니다.

중요 비즈니스용 서비스 계층에서는 3개 이상의 복제본을 자동으로 할당합니다. 이러한 컴퓨팅 리소스의 추가 할당을 반영하기 위해 vCore 기반 구매 모델의 가격은 중요 비즈니스용 서비스 계층이 범용 서비스 계층보다 약 2.7배 높습니다. 마찬가지로 중요 비즈니스용 서비스 계층에서 GB당 스토리지 가격이 높을수록 SSD 스토리지의 IO 제한이 높고 대기 시간이 짧습니다.

중요 비즈니스용 서비스 계층과 범용 서비스 계층 둘 다 백업에 표준 스토리지를 사용하기 때문에 두 서비스 계층의 백업 스토리지 비용은 동일합니다.

### <a name="serverless-compute-costs"></a>서버리스 컴퓨팅 비용

서버리스 컴퓨팅 계층의 컴퓨팅 용량 정의 및 비용 계산 방법에 대한 설명은 [SQL Database 서버리스 계층](serverless-tier-overview.md)을 참조하세요.

## <a name="storage-costs"></a>스토리지 비용

다양한 유형의 스토리지에 대해 별도의 방식으로 요금이 청구됩니다. 데이터 스토리지의 경우 선택한 최대 데이터베이스 또는 풀 크기에 따라 프로비저닝된 스토리지 비용이 청구됩니다. 해당 최댓값을 줄이거나 늘리지 않는 한 비용은 변하지 않습니다. 백업 스토리지는 인스턴스의 자동 백업과 연결되고 동적으로 할당됩니다. 백업 보존 기간을 늘리면 인스턴스에서 사용되는 백업 스토리지도 늘어납니다.

기본적으로 자동 백업된 7일 분량의 데이터베이스는 RA-GRS(읽기 액세스 지역 중복 스토리지) 표준 Blob 스토리지 계정에 복사됩니다. 스토리지는 주별 전체 백업, 일별 차등 백업 및 5분마다 복사되는 트랜잭션 로그 백업에 사용됩니다. 트랜잭션 로그의 크기는 데이터베이스 변동률에 따라 다릅니다. 데이터베이스 크기의 100%와 동일한 최소 스토리지 용량이 추가 요금 없이 제공됩니다. 백업 스토리지의 추가 사용량은 GB/월 단위로 요금이 청구됩니다.

스토리지 가격에 대한 자세한 내용은 [가격 책정](https://azure.microsoft.com/pricing/details/sql-database/single/) 페이지를 참조하세요.

## <a name="vcore-based-purchasing-model"></a>vCore 기반 구매 모델

vCore(가상 코어)는 논리적 CPU를 나타내며, 하드웨어 세대와 하드웨어의 물리적 특성(예: 코어의 수, 메모리, 스토리지 크기) 간에 선택할 수 있는 옵션을 제공합니다. vCore 기반 구매 모델은 개별 리소스 사용에 대한 유연성, 제어, 투명성 및 온-프레미스 워크로드 요구 사항을 클라우드로 전환하는 직관적인 방법을 제공합니다. 이 모델을 통해 워크로드 필요에 따라 컴퓨팅, 메모리, 스토리지 리소스를 선택할 수 있습니다.

vCore 기반 구매 모델에서는 SQL Database와 SQL Managed Instance에 대해 [범용](high-availability-sla.md#basic-standard-and-general-purpose-service-tier-locally-redundant-availability) 및 [중요 비즈니스용](high-availability-sla.md#premium-and-business-critical-service-tier-locally-redundant-availability) 서비스 계층 중에서 선택할 수 있습니다.  단일 데이터베이스의 경우 [하이퍼스케일 서비스 계층](service-tier-hyperscale.md)을 선택할 수도 있습니다.

vCore 기반 구매 모델을 사용하면 컴퓨팅 및 스토리지 리소스를 독립적으로 선택하고, 온-프레미스 성능에 맞추고, 가격을 최적화할 수 있습니다. vCore 기반 구매 모델에서 지불하는 비용은 다음과 같습니다.

- 컴퓨팅 리소스(서비스 계층 + vCore 수 및 메모리 크기 + 하드웨어 세대)
- 데이터 및 로그 스토리지의 형식 및 크기
- 백업 스토리지(RA-GRS)

> [!IMPORTANT]
> 컴퓨팅 리소스, I/O, 데이터 및 로그 스토리지는 데이터베이스 또는 탄력적 풀당 요금이 부과됩니다. 백업 스토리지는 각 데이터베이스당 요금이 부과됩니다. SQL Managed Instance 요금에 대한 자세한 내용은 [SQL Managed Instance](../managed-instance/sql-managed-instance-paas-overview.md)를 참조하세요.
> **지역 제한:** 현재 지원되는 지역 목록은 [지역별로 사용 가능한 제품](https://azure.microsoft.com/global-infrastructure/services/?products=sql-database&regions=all)을 참조하세요. 현재 지원되지 않는 지역에서 관리되는 인스턴스를 만들려면 [Azure Portal을 통해 지원 요청을 보내세요](quota-increase-request.md).

데이터베이스가 DTU를 300개 이상 사용하는 경우 vCore 기반 구매 모델로 전환하면 비용을 줄일 수 있습니다. 선택한 API를 사용하거나 Azure Portal을 사용하여 가동 중지 시간 없이 변환할 수 있습니다. 그러나 전환이 필수는 아니며 자동으로 전환되지는 않습니다. DTU 기반 구매 모델이 성능 및 비즈니스 요구 사항을 충족하는 경우 이 모델을 계속 사용해야 합니다.

DTU 기반 구매 모델에서 vCore 기반 구매 모델로 변환하려면 [에서 vCore로 마이그레이션](migrate-dtu-to-vcore.md)을 참조하세요.

## <a name="dtu-based-purchasing-model"></a>DTU 기반 구매 모델

DTU(데이터베이스 트랜잭션 단위)는 CPU, 메모리, 읽기 및 쓰기의 혼합 측정값을 나타냅니다. DTU 기반 구매 모델은 다양한 수준의 애플리케이션 성능을 구동하기 위해 컴퓨팅 리소스 및 포함된 스토리지를 미리 구성된 번들로 묶은 집합을 제공합니다. 미리 구성된 번들과 매월 고정된 지불 금액의 단순성을 선호한다면 DTU 기반 모델이 필요에 더 적합할 수 있습니다.

DTU 기반 구매 모델에서는 Azure SQL Database에 대해 기본, 표준 및 프리미엄 서비스 계층 중에서 선택할 수 있습니다. Azure SQL Managed Instance에는 DTU 기반 구매 모델을 사용할 수 없습니다.

### <a name="database-transaction-units-dtus"></a>DTU(데이터베이스 트랜잭션 단위)

[서비스 계층](single-database-scale.md) 내 특정 컴퓨팅 크기의 단일 데이터베이스에 대해 Azure는 해당 데이터베이스(Azure 클라우드의 다른 데이터베이스와는 무관)에 대해 특정 수준의 리소스를 보장합니다. 이러한 보장은 예측 가능한 성능 수준을 제공합니다. 데이터베이스에 할당된 리소스 양은 DTU 수로 계산되며 컴퓨팅, 스토리지, I/O 리소스를 번들로 묶은 측정값입니다.

원래 이러한 리소스 간의 비율은 일반적인 실제 OLTP 워크로드에 맞게 디자인된 [OLTP(온라인 트랜잭션 처리) 벤치마크 워크로드](service-tiers-dtu.md)에 따라 결정되었습니다. 워크로드가 이러한 리소스의 양을 초과하면 처리량이 제한되어 성능이 느려지고 시간이 초과됩니다.

워크로드에서 사용하는 리소스는 Azure 클라우드의 다른 데이터베이스에 사용할 수 있는 리소스에 영향을 미치지 않습니다. 마찬가지로 다른 워크로드에서 사용하는 리소스는 데이터베이스에 사용할 수 있는 리소스에 영향을 주지 않습니다.

![경계 상자](./media/purchasing-models/bounding-box.png)

DTU는 여러 컴퓨팅 크기와 서비스 계층에서 데이터베이스에 할당되는 상대적 리소스를 이해하는 데 가장 유용합니다. 예를 들면 다음과 같습니다.

- 데이터베이스의 컴퓨팅 크기를 늘려 DTU를 두 배로 높이면 해당 데이터베이스에 사용할 수 있는 리소스 세트도 똑같이 두 배로 높아집니다.
- 1750개 DTU를 사용하는 프리미엄 서비스 계층 P11 데이터베이스는 DTU 5개를 사용하는 기본 서비스 계층 데이터베이스보다 350배 더 많은 DTU 컴퓨팅 성능을 제공합니다.  

워크로드의 리소스 사용량(DTU)에 대해 더 자세히 이해하려면 [쿼리 성능 Insight](query-performance-insight-use.md)를 사용하여 다음을 수행합니다.

- 향상된 성능을 위해 잠재적으로 조정될 수 있는 CPU/기간/실행 횟수별 최상위 쿼리를 식별합니다. 예를 들어 I/O를 많이 사용하는 쿼리는 [메모리 내 최적화 기술](../in-memory-oltp-overview.md)을 사용하여 특정 서비스 계층 및 컴퓨팅 크기에 사용 가능한 메모리를 보다 효율적으로 사용할 수 있는 이점이 있습니다.
- 쿼리에 대한 세부 정보로 드릴다운하여 해당 텍스트 및 리소스 사용률에 대한 기록을 확인합니다.
- [SQL Database Advisor](database-advisor-implement-performance-recommendations.md)에서 수행한 작업을 표시하는 성능 조정 권장 사항에 액세스합니다.

### <a name="elastic-database-transaction-units-edtus"></a>eDTU(탄력적 데이터베이스 트랜잭션 단위)

항상 사용할 수 있는 데이터베이스의 경우 항상 필요하지 않을 수 있는 전용 리소스 집합(DTU)을 제공하는 대신 이러한 데이터베이스를 [탄력적 풀](elastic-pool-overview.md)에 배치할 수 있습니다. 탄력적 풀의 데이터베이스는 단일 서버에 있으며 리소스 풀을 공유합니다.

탄력적 풀의 공유 리소스는 eDTU(탄력적 데이터베이스 트랜잭션 단위)로 측정됩니다. 탄력적 풀은 매우 다양하고 예측할 수 없는 사용 패턴을 지닌 여러 데이터베이스에 대한 성능 목표를 관리하기 위한 간단하고 비용 효율적인 솔루션을 제공합니다. 탄력적 풀은 풀의 한 데이터베이스에서 리소스를 모두 사용할 수 없도록 하는 동시에 풀의 각 데이터베이스에서 항상 필요한 최소량의 리소스를 사용할 수 있도록 합니다.

풀에는 정해진 가격으로 정해진 개수의 eDTU가 제공됩니다. 탄력적 풀에서 개별 데이터베이스는 구성된 경계 내에서 자동 스케일링할 수 있습니다. 부하가 높은 데이터베이스는 요구를 충족하기 위해 더 많은 eDTU를 사용합니다. 부하가 낮은 데이터베이스는 더 적은 eDTU를 사용합니다. 부하가 없는 데이터베이스는 eDTU를 사용하지 않습니다. 리소스는 데이터베이스 단위가 아니라 전체 풀에 대해 프로비저닝되므로 탄력적 풀은 관리 작업을 단순화하고 풀에 대해 예측 가능한 예산을 제공합니다.

추가 eDTU는 데이터베이스 가동 중지 시간 없이 풀의 데이터베이스에 영향을 주지 않은 채 기존 풀에 추가할 수 있습니다. 마찬가지로 추가 eDTU가 더 이상 필요하지 않은 경우 언제든지 기존 풀에서 제거하면 됩니다. 또한 언제든지 데이터베이스를 풀에 추가하거나 풀에서 뺄 수 있습니다. 다른 데이터베이스를 위한 eDTU를 예약하려면 높은 부하 상태에서 데이터베이스가 사용할 수 있는 eDTU 수를 제한합니다. 데이터베이스에서 지속적으로 리소스를 적게 사용하는 경우 해당 데이터베이스를 풀 외부로 이동하고 필요한 리소스의 양을 예측할 수 있는 단일 데이터베이스로 구성할 수 있습니다.

### <a name="determine-the-number-of-dtus-needed-by-a-workload"></a>워크로드에 필요한 DTU 수 결정

기존 온-프레미스 또는 SQL Server 가상 머신 워크로드를 Azure SQL Database로 마이그레이션하려는 경우 [DTU 계산기](https://dtucalculator.azurewebsites.net/)를 사용하여 필요한 DTU의 수를 대략 계산합니다. 기존 SQL Database 워크로드의 경우 데이터베이스 리소스 사용량(DTU)을 파악하고 워크로드 최적화 방법에 대한 더욱 심층적인 정보를 얻으려면 [쿼리 성능 Insight](query-performance-insight-use.md)를 사용합니다. [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) DMV(동적 관리 뷰)를 사용하면 지난 1시간의 리소스 소비량을 볼 수 있습니다. [sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) 카탈로그 뷰에서 지난 14일 동안의 리소스 사용량을 표시하지만 충실도가 평균 5분으로 더 낮습니다.

### <a name="determine-dtu-utilization"></a>DTU 사용률 확인

데이터베이스 또는 탄력적 풀의 DTU/eDTU 제한을 기준으로 DTU/eDTU 사용률의 평균 백분율을 확인하려면 다음 수식을 사용합니다.

`avg_dtu_percent = MAX(avg_cpu_percent, avg_data_io_percent, avg_log_write_percent)`

이 수식에 대한 입력값은 [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database), [sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database), [sys.elastic_pool_resource_stats](/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) DMV에서 얻을 수 있습니다. 즉, 데이터베이스 또는 탄력적 풀의 DTU/eDTU 한도에 대한 DTU/eDTU 사용률을 확인하려면 지정된 시간에 `avg_cpu_percent`, `avg_data_io_percent`, `avg_log_write_percent` 중 백분율 값이 가장 큰 항목을 선택합니다.

> [!NOTE]
> 데이터베이스의 DTU 한도는 데이터베이스에 사용할 수 있는 CPU, 읽기, 쓰기 및 메모리에 따라 결정됩니다. 그러나 SQL Database 엔진은 일반적으로 데이터 캐시에 사용할 수 있는 모든 메모리를 사용하여 성능을 향상시키므로 현재 데이터베이스 로드와 관계없이 `avg_memory_usage_percent` 값은 일반적으로 100%에 가깝습니다. 따라서 메모리가 DTU 한도에 간접적으로 영향을 미치더라도 이는 DTU 사용률 수식에서 고려되지 않습니다.

### <a name="workloads-that-benefit-from-an-elastic-pool-of-resources"></a>탄력적 리소스 풀의 이점을 활용할 수 있는 워크로드

풀은 리소스 사용률 평균이 낮고 사용률 급증이 상대적으로 자주 발생하지 않는 데이터베이스에 적합합니다. 자세한 내용은 [SQL Database 탄력적 풀은 언제 고려해야 하나요?](elastic-pool-overview.md)를 참조하세요.

### <a name="hardware-generations-in-the-dtu-based-purchasing-model"></a>DTU 기반 구매 모델의 하드웨어 세대

DTU 기반 구매 모델에서 고객은 해당 데이터베이스에 사용되는 하드웨어 세대를 선택할 수 없습니다. 지정된 데이터베이스는 일반적으로 오랜 시간 동안(일반적으로 몇 개월) 특정 하드웨어 세대에서 유지되지만 다른 하드웨어 세대로 데이터베이스를 이전할 수 있는 특정 이벤트가 있습니다.

예를 들어, 다른 서비스 목표로 규모를 스케일 업 또는 다운하는 경우, 데이터 센터의 현재 인프라가 용량 제한에 곧 도달하는 경우 또는 수명 종료로 인해 현재 사용 중인 하드웨어가 곧 해제되는 경우 다른 하드웨어 세대로 데이터베이스를 이전할 수 있습니다.

데이터베이스가 다른 하드웨어로 이전되면 작업 성능이 변경될 수 있습니다. DTU 모델은 서비스 목표(DTU 개수)가 동일하게 유지되는 한 데이터베이스가 다른 하드웨어 세대로 이전할 때 [DTU 벤치마크](./service-tiers-dtu.md#dtu-benchmark) 워크로드의 처리량 및 응답 시간이 동일하게 유지되도록 보장합니다.

그러나 Azure SQL Database에서 실행되는 광범위한 고객 워크로드 전반에서 동일한 서비스 목표에 대해 다른 하드웨어를 사용하는 경우에는 더 많은 영향을 줄 수 있습니다. 다양한 워크로드가 다른 하드웨어 구성 및 기능의 혜택을 받습니다. 따라서 DTU 벤치마크 이외의 워크로드의 경우 데이터베이스가 하드웨어 세대 간에 이전하면 성능 차이를 확인할 수 있습니다.

예를 들어, Gen5에서 가속화된 네트워킹을 사용하기 때문에 네트워크 대기 시간에 민감한 애플리케이션은 Gen4 하드웨어에 비해 Gen5 하드웨어에서 성능이 개선될 수 있지만, Gen4에서 코어당 메모리 비율이 더 높기 때문에 집약적 읽기 IO를 사용하는 애플리케이션은 Gen5 하드웨어에 비해 Gen4 하드웨어에서 더 높은 성능을 발휘할 수 있습니다.

하드웨어 변경에 민감한 워크로드가 있는 고객 또는 데이터베이스의 하드웨어 세대를 선택하고자 하는 고객은 [vCore](service-tiers-vcore.md) 모델을 사용하면 데이터베이스를 만들고 스케일링하는 동안 선호하는 하드웨어 세대를 선택할 수 있습니다. VCore 모델에서는 [단일 데이터베이스](resource-limits-vcore-single-databases.md)와 [탄력적 풀](resource-limits-vcore-elastic-pools.md) 둘 다에 대해 각 하드웨어 세대의 각 서비스 목표에 대한 리소스 제한이 문서화되어 있습니다. vCore 모델의 하드웨어 세대에 대한 자세한 내용은 [SQL Database용 하드웨어 세대](./service-tiers-sql-database-vcore.md#hardware-generations) 또는 [SQL Managed Instance용 하드웨어 세대](../managed-instance/service-tiers-managed-instance-vcore.md#hardware-generations)를 참조하세요.

## <a name="frequently-asked-questions-faqs"></a>FAQ(질문과 대답)

### <a name="do-i-need-to-take-my-application-offline-to-convert-from-a-dtu-based-service-tier-to-a-vcore-based-service-tier"></a>DTU 기반 서비스 계층에서 vCore 기반 서비스 계층으로 변환하려면 애플리케이션을 오프라인으로 전환해야 하나요?

아니요. 애플리케이션을 오프라인으로 전환할 필요가 없습니다. 새 서비스 계층에서는 데이터베이스를 표준 서비스 계층에서 프리미엄 서비스 계층으로 업그레이드하는 기존 프로세스와 비슷한 간단한 온라인 변환 방법 및 기타 방법을 제공합니다. 이 변환은 Azure Portal, PowerShell, Azure CLI, T-SQL 또는 REST API를 사용하여 시작할 수 있습니다. [단일 데이터베이스 관리](single-database-scale.md) 및 [탄력적 풀 관리](elastic-pool-overview.md)를 참조하세요.

### <a name="can-i-convert-a-database-from-a-service-tier-in-the-vcore-based-purchasing-model-to-a-service-tier-in-the-dtu-based-purchasing-model"></a>vCore 기반 구매 모델의 서비스 계층에서 DTU 기반 구매 모델의 서비스 계층으로 데이터베이스를 변환할 수 있나요?

예, Azure Portal, PowerShell, Azure CLI, T-SQL 또는 REST API를 사용하여 지원되는 성능 목표로 데이터베이스를 쉽게 변환할 수 있습니다. [단일 데이터베이스 관리](single-database-scale.md) 및 [탄력적 풀 관리](elastic-pool-overview.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

- vCore 기반 구매 모델에 대한 자세한 내용은 [vCore 기반 구매 모델](service-tiers-vcore.md)을 참조하세요.
- DTU 기반 구매 모델에 대한 자세한 내용은 [DTU 기반 구매 모델](service-tiers-dtu.md)을 참조하세요.