---
title: 탄력적 풀을 사용한 여러 데이터베이스 관리
description: 탄력적 풀을 사용하여 수백, 수천 개의 Azure SQL Database를 관리하고 스케일링합니다. 필요한 경우 배포할 수는 리소스에 대한 단일 가격
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: arvindshmicrosoft
ms.author: arvindsh
ms.reviewer: mathoma
ms.date: 06/23/2021
ms.openlocfilehash: 669b8610cba44be369ba805834eca2f700d211e0
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132548884"
---
# <a name="elastic-pools-help-you-manage-and-scale-multiple-databases-in-azure-sql-database"></a>탄력적 풀이 Azure SQL Database의 여러 데이터베이스를 관리하고 크기를 조정하는 데 도움을 주는 방식
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Azure SQL Database 탄력적 풀은 사용량 요구가 다양하고 예측하기 어려운 여러 데이터베이스를 관리하고 스케일링하기 위한 간단하고 비용 효율적인 솔루션입니다. 탄력적 풀의 데이터베이스는 단일 서버에 있으며 설정된 가격에 일정한 수의 리소스를 공유합니다. Azure SQL Database의 탄력적 풀을 사용하면 SaaS 개발자가 각각의 데이터베이스에 성능 탄력성을 제공하는 동시에 규정된 예산 내의 데이터베이스 그룹에 가격 성능을 최적화할 수 있습니다.

> [!div class="nextstepaction"]
> [Azure SQL 개선을 위한 설문 조사](https://aka.ms/AzureSQLSurveyNov2021)

## <a name="what-are-sql-elastic-pools"></a>SQL 탄력적 풀이란?

SaaS 개발자는 여러 데이터베이스로 구성된 대규모 데이터 계층에 애플리케이션을 작성합니다. 일반적인 애플리케이션 패턴은 각 고객에 대해 단일 데이터베이스를 프로비전합니다. 하지만 다양한 고객에게는 종종 다양하고 예측할 수 없는 사용량 패턴이 있으며 각 데이터베이스 사용자의 리소스 요구 사항을 예측하기 어렵습니다. 일반적으로 두 가지 옵션이 있습니다.

- 최대 사용량에 따른 리소스 오버프로비저닝과 과다 지불 또는
- 최대 사용 기간 동안 성능 및 고객 만족은 저하되지만 비용을 절감하는 언더프로비저닝

탄력적 풀은 데이터베이스가 필요한 성능 리소스를 필요할 때 얻도록 하여 이 문제를 해결합니다. 예측 가능한 예산 내에서 간단한 리소스 할당 메커니즘을 제공합니다. 탄력적 풀을 사용한 SaaS 애플리케이션의 디자인 패턴에 대해 자세히 알아보려면 [Azure SQL Database를 사용한 다중 테넌트 SaaS 애플리케이션 디자인 패턴](saas-tenancy-app-design-patterns.md)을 참조하세요.
>
> [!IMPORTANT]
> 탄력적 풀에 대한 데이터베이스별 요금은 없습니다. 사용량에 관계없이 또는 풀이 한 시간 미만 동안 활성 상태였는지 여부에 관계없이 풀이 최고 eDTU 또는 vCore 수에 있었던 시간에 대해 시간당 요금이 청구됩니다.

탄력적 풀을 사용하면 개발자가 예측할 수 없는 개별 데이터베이스의 사용 기간을 수용하기 위해 여러 데이터베이스에서 공유되는 풀에 대한 리소스를 구매할 수 있습니다. [DTU 기반 구매 모델](service-tiers-dtu.md) 또는 [vCore 기반 구매 모델](service-tiers-vcore.md)에 따라 풀에 대한 리소스를 구성할 수 있습니다. 풀에 대한 리소스 요구 사항은 해당 데이터베이스의 집계 사용률에 의해 결정됩니다. 풀에 사용 가능한 리소스 양은 개발자 예산에 의해 제어됩니다. 개발자는 간단하게 풀에 데이터베이스를 추가하고, 데이터베이스에 대한 최소 및 최대 리소스를 설정한 다음(선택한 리소스 모델에 따라 최소 및 최대 DTU 또는 최소 또는 최대 vCore), 예산에 따라 풀의 리소스를 설정합니다. 개발자는 풀을 사용하여 처음 시작부터 계속 성장하는 성숙한 비즈니스까지 해당 서비스를 원활하게 증가시킬 수 있습니다.

풀 내에서 개별 데이터베이스는 집합 매개 변수 내에서 자동 크기 조정에 유연성이 제공됩니다. 부하가 높은 상태에서 데이터베이스는 더 많은 리소스를 사용하여 수요를 충족할 수 있습니다. 낮은 부하량에서 데이터베이스는 적게 사용하고 부하가 없는 데이터베이스는 리소스를 사용하지 않습니다. 단일 데이터베이스가 아닌 전체 풀에 대한 리소스 프로비저닝은 관리 작업을 간소화합니다. 또한 풀에 대한 예측 가능한 예산이 있습니다. 최소 가동 중지 시간으로 추가 리소스를 기존 풀에 추가할 수 있습니다. 마찬가지로, 추가 리소스가 더 이상 필요하지 않은 경우에는 언제든지 기존 풀에서 제거할 수 있습니다. 그리고 풀에서 데이터베이스를 추가하거나 제거할 수 있습니다. 데이터베이스가 예측 가능한 방식으로 리소스를 사용하는 경우 리소스를 이동합니다.

> [!NOTE]
> 데이터베이스를 탄력적 풀 안팎으로 이동하는 경우, 데이터베이스 연결이 끊길 때 작업이 종료되는 잠깐의 시간(초 단위)을 제외하고 가동 중지 시간이 발생하지 않습니다.

## <a name="when-should-you-consider-a-sql-database-elastic-pool"></a>SQL Database 탄력적 풀은 언제 고려해야 하나요?

풀은 특정 사용 패턴을 보이는 다수의 데이터베이스에 적합합니다. 주어진 데이터 베이스에 대해, 이 패턴은 상대적으로 사용률 급증이 드물고 평균 사용률이 낮음으로 규정됩니다. 반대로, 영구적이고 사용률이 중간이거나 높은 여러 데이터베이스는 동일한 탄력적 풀에 배치하면 안 됩니다.

풀에 더 많은 데이터베이스를 추가할수록 절감이 커집니다. 애플리케이션 사용률 패턴에 따라 S3 데이터베이스 두 개 만큼 절감이 가능합니다.

다음 섹션에서는 데이터베이스의 특정 컬렉션이 풀에 있는 것이 이득이 될 수 있는지 평가하는 방법을 이해할 수 있습니다. 예제에서는 표준 풀을 사용하지만 기본 및 프리미엄 풀에도 동일한 원칙이 적용됩니다.

### <a name="assessing-database-utilization-patterns"></a>데이터베이스 사용량 패턴 평가

다음 그림에는 대부분의 시간을 유휴 상태로 있지만 정기적으로 갑작스러운 활동이 증가하는 데이터베이스의 예가 나와있습니다. 다음은 풀에 적합한 사용률 패턴입니다.

   ![풀에 적합한 단일 데이터베이스](./media/elastic-pool-overview/one-database.png)

이 차트에서는 각 데이터 요소에 1분 세분성이 있는 12:00에서 1:00까지 1시간 동안의 DTU 사용량을 보여 줍니다. 12:10에 DB1이 90DTU까지 치솟았지만 전체 평균 사용량은 5DTU 미만입니다. 단일 데이터베이스에서 이 워크로드를 실행하려면 S3 컴퓨팅 크기가 필요하지만 활동이 적은 기간 동안 리소스의 대부분을 사용하지 않게 됩니다.

풀을 사용하면 이러한 사용하지 않는 DTU를 여러 데이터베이스에서 공유할 수 있으므로 필요한 DTU 및 전체 비용을 줄일 수 있습니다.

앞의 예제에 기초하여, DB1같은 유사한 사용률 패턴의 추가 데이터베이스가 있다고 가정합니다. 아래 두 그림에서 4개의 데이터베이스 사용률 및 20개의 데이터베이스는 동일한 그래프에 계층화되어 시간에 따라 DTU 기반 구매 모델을 사용하여 해당 사용률의 특성이 겹치지 않는다는 것을 설명합니다.

   ![풀에 적합한 사용 패턴을 가진 네 개의 데이터베이스](./media/elastic-pool-overview/four-databases.png)

   ![풀에 적합한 사용 패턴을 가진 20개의 데이터베이스](./media/elastic-pool-overview/twenty-databases.png)

전체 20개 데이터베이스의 총 DTU 사용률은 위 그림에서 검은색 선으로 표시되었습니다. 이는 DTU 사용률 집계가 100 DTU를 초과하지 않음을 보여 주며, 이 기간 동안 20개 데이터베이스가 100 eDTU를 공유할 수 있음을 나타냅니다. 이렇게 하면 각 데이터베이스를 단일 데이터베이스용 S3 컴퓨팅 크기에 배치할 때와 비교하여 DTU가 1/20, 가격이 1/13로 감소합니다.

이 예는 다음 이유로 이상적입니다.

- 최고사용률과 데이터베이스당 평균 사용률간에 큰 차이가 있습니다.
- 각 데이터베이스의 최고 사용률이 시간 내에 여러 지점에서 발생합니다.
- eDTU는 많은 데이터베이스 간에 공유됩니다.

DTU 구매 모델에서 풀의 가격은 풀 eDTU의 기능입니다. 풀의 eDTU 단가는 단일 데이터베이스에 대한 DTU 단가보다 1.5배지만, **많은 데이터베이스가 풀 eDTU를 공유할 수 있고 필요한 전체 eDTU가 적습니다**. 가격 책정 및 eDTU 공유에서의 이러한 차이가 풀이 제공할 수 있는 가격 절감 가능성의 기초가 됩니다.

VCore 구매 모델에서 탄력적 풀에 대한 vCore의 단가는 단일 데이터베이스에 대한 vCore 단가와 동일합니다.

## <a name="how-do-i-choose-the-correct-pool-size"></a>올바른 풀 크기를 선택하려면 어떻게 해야 하나요?

풀의 적절한 크기는 풀의 모든 데이터베이스에 필요한 집계 리소스에 따라 달라집니다. 이는 다음 결정을 포함합니다.

- 풀에 있는 데이터베이스는 최대량의 컴퓨팅 리소스를 활용합니다.  컴퓨팅 리소스는 선택한 구매 모델에 따라 eDTU 또는 vCore를 통해 인덱싱됩니다.
- 풀에 있는 모든 데이터베이스의 최대 스토리지된 바이트 수입니다.

각 구매 모델에서 사용할 수 있는 서비스 계층과 리소스 제한은 [DTU 기반 구매 모델](service-tiers-dtu.md) 또는 [vCore 기반 구매 모델](service-tiers-vcore.md)을 참조하세요.

다음 단계는 풀이 단일 데이터베이스보다 비용 효율적인지를 예측하는 데 유용합니다.

1. 다음 수식에 따라 풀에 필요한 eDTU 또는 vCore를 예측합니다.
   - DTU 기반 구매 모델의 경우:
     - MAX(<*총 DB 수* &times; *DB당 평균 DTU 사용률*>, <*동시 최고 DB의 수* &times; *DB당 최고 DTU 사용률*>)
   - vCore 기반 구매 모델의 경우:
     - MAX(<*총 DB 수* &times; *DB당 평균 vCore 사용률*>, <*동시 최고 DB의 수* &times; *DB당 최고 vCore 사용률*>)
2. 풀에 있는 모든 데이터베이스에 필요한 데이터 크기를 추가함으로써 풀에 필요한 스토리지 공간을 예측합니다. DTU 구매 모델의 경우 다음으로 이 스토리지 크기를 제공하는 eDTU 풀 크기를 확인합니다.
3. DTU 기반 구매 모델의 경우 1단계 및 2단계에서 eDTU 예상 중 큰 수를 사용합니다. VCore 기반 구매 모델의 경우 1단계의 vCore 예상을 사용합니다.
4. [SQL Database 가격 책정 페이지](https://azure.microsoft.com/pricing/details/sql-database/)를 확인하고 3단계의 예상보다 큰 경우 가장 작은 풀 크기를 찾습니다.
5. 4단계의 풀 가격을 적절한 단일 데이터베이스용 컴퓨팅 크기를 사용할 때의 가격과 비교합니다.

> [!IMPORTANT]
> 풀의 데이터베이스 수가 지원되는 최대치에 도달하는 경우에는 [고밀도 탄력적 풀에서 리소스 관리](elastic-pool-resource-management.md)를 고려해야 합니다.

### <a name="per-database-properties"></a>데이터베이스 속성별

선택적으로 "데이터베이스별" 속성을 설정하여 탄력적 풀의 리소스 사용 패턴을 수정할 수 있습니다. 자세한 내용은 [DTU](resource-limits-dtu-elastic-pools.md#database-properties-for-pooled-databases) 및 [vCore](resource-limits-vcore-elastic-pools.md#database-properties-for-pooled-databases) 탄력적 풀에 대한 리소스 제한 문서를 참조하세요.

## <a name="using-other-sql-database-features-with-elastic-pools"></a>탄력적 풀과 기타 SQL Database 기능 사용

### <a name="elastic-jobs-and-elastic-pools"></a>탄력적 작업 및 탄력적 풀

풀을 통해 **[탄력적 작업](elastic-jobs-overview.md)** 의 스크립트를 실행하여 관리 작업이 간소화됩니다. 탄력적 작업은 많은 수의 데이터베이스와 관련된 번거로움을 대부분 없애 줍니다.

여러 데이터베이스를 사용하기 위한 다른 데이터베이스 도구에 대한 자세한 내용은 [Azure SQL Database를 사용하여 확장](elastic-scale-introduction.md)을 참조합니다.

### <a name="business-continuity-options-for-databases-in-an-elastic-pool"></a>탄력적 풀의 데이터베이스에 대한 비즈니스 연속성 옵션

풀링된 데이터베이스는 일반적으로 단일 데이터베이스에서 사용할 수 있는 동일한 [비즈니스 연속성 기능](business-continuity-high-availability-disaster-recover-hadr-overview.md)을 지원합니다.

- **지정 시간 복원**

  지정 시간 복원은 자동 데이터베이스 백업을 사용하여 풀에 있는 데이터베이스를 지정 시간으로 복원합니다. [지정 시간 복원](recovery-using-backups.md#point-in-time-restore)

- **지역 복원**

  지역 복원은 데이터베이스가 호스팅되는 지역에 사고가 발생하여 데이터베이스를 사용할 수 없게 되었을 때를 위한 기본 복구 옵션을 제공합니다. [Azure SQL Database 복원 또는 보조 데이터베이스에 대한 장애 조치(failover)](disaster-recovery-guidance.md)

- **활성 지역 복제**

  지역 복원에서 제공하는 것보다 더 까다로운 복구 요구 사항이 있는 애플리케이션의 경우 [활성 지역 복제](active-geo-replication-overview.md) 또는 [자동 장애 조치(failover) 그룹](auto-failover-group-overview.md)을 구성합니다.

## <a name="creating-a-new-sql-database-elastic-pool-using-the-azure-portal"></a>Azure Portal을 사용하여 새 SQL Database 탄력적 풀 만들기

Azure Portal에서 두 가지 방법으로 탄력적 풀을 만들 수 있습니다.

1. [Azure Portal](https://portal.azure.com)로 이동하여 탄력적 풀을 만듭니다. **Azure SQL** 을 검색하여 선택합니다.
2. **+추가** 를 선택하여 **SQL 배포 옵션 선택** 페이지를 엽니다. **데이터베이스** 타일에서 **세부 정보 표시** 를 선택하여 탄력적 풀에 대한 추가 정보를 볼 수 있습니다.
3. **데이터베이스** 타일의 **리소스 종류** 드롭다운에서 **탄력적 풀** 을 선택하고 **만들기** 를 선택합니다.

   ![탄력적 풀 만들기](./media/elastic-pool-overview/create-elastic-pool.png)

4. 또는 기존 서버로 이동하고 **+ 새로운 풀 만들기** 를 클릭하여 해당 서버에 직접 풀을 만듦으로써 탄력적 풀을 만들 수 있습니다.

> [!NOTE]
> 서버에 풀을 여러 개 만들 수 있지만 다른 서버에 속하는 데이터베이스를 동일한 풀에 추가할 수 없습니다.

풀의 서비스 계층에 따라 풀에 있는 탄력적 데이터베이스에서 사용 가능한 기능과 각 데이터베이스에서 사용 가능한 최대 리소스 양이 결정됩니다. 자세한 내용은 [DTU 모델](resource-limits-dtu-elastic-pools.md#elastic-pool-storage-sizes-and-compute-sizes)에서 탄력적 풀에 대한 리소스 제한을 참조하세요. 탄력적 풀에 대한 vCore 기반 리소스 제한은 [vCore 기반 리소스 제한 - 탄력적 풀](resource-limits-vcore-elastic-pools.md)을 참조하세요.

리소스 및 풀의 가격 책정을 구성하려면 **풀 구성** 을 클릭합니다. 그런 다음, 서비스 계층을 선택하고, 풀에 데이터베이스를 추가하고, 풀과 해당 데이터베이스에 대한 리소스 제한을 구성합니다.

풀 구성을 완료하면 '적용'을 클릭하고, 풀의 이름을 지정하고, '확인'을 클릭하여 풀을 만들 수 있습니다.

## <a name="monitor-an-elastic-pool-and-its-databases"></a>탄력적 풀 및 해당 데이터베이스 모니터링

Azure Portal에서 탄력적 풀 및 해당 풀 내의 데이터베이스의 사용률을 모니터링할 수 있습니다. 탄력적 풀에 일련의 내용을 변경하는 동시에 모든 변경 내용을 전송할 수도 있습니다. 이러한 변경 내용에는 데이터베이스 추가 또는 제거, 탄력적 풀 설정 변경, 데이터베이스 설정 변경이 포함됩니다.

성능 평가와 결합된 [기본 제공 성능 모니터링](./performance-guidance.md) 및 [경고](./alerts-insights-configure-portal.md) 도구를 사용합니다.  또한 SQL Database는 쉬운 모니터링을 위해 [메트릭 및 리소스 로그를 내보낼](./metrics-diagnostic-telemetry-logging-streaming-export-configure.md?tabs=azure-portal) 수 있습니다.

## <a name="customer-case-studies"></a>고객 사례 연구

- [SnelStart](https://azure.microsoft.com/resources/videos/azure-sql-database-case-study-snelstart/)

  SnelStart는 탄력적 풀과 Azure SQL Database를 사용하여 매월 1,000개의 새 Azure SQL Database 규모로 비즈니스 서비스를 빠르게 확장했습니다.

- [Umbraco](https://azure.microsoft.com/resources/videos/azure-sql-database-case-study-umbraco/)

  Umbraco는 탄력적 풀과 Azure SQL Database를 사용하여 클라우드의 수천 개의 테넌트 분량의 서비스를 신속하게 프로비저닝하고 스케일링합니다.

- [Daxko/CSI](https://customers.microsoft.com/story/726277-csi-daxko-partner-professional-service-azure)

   Daxko/CSI는 탄력적 풀과 Azure SQL Database를 사용하여 개발 주기를 가속화하고 고객 서비스 및 성능을 개선했습니다.

## <a name="next-steps"></a>다음 단계

- 가격 책정 정보는 [탄력적 풀 가격 책정](https://azure.microsoft.com/pricing/details/sql-database/elastic)을 참조하세요.
- 탄력적 풀의 크기를 조정하려면 [탄력적 풀 크기 조정](elastic-pool-scale.md) 및 [탄력적 풀 크기 조정 - 샘플 코드](scripts/monitor-and-scale-pool-powershell.md)를 참조하세요.
- 탄력적 풀을 사용한 SaaS 애플리케이션의 디자인 패턴에 대해 자세히 알아보려면 [Azure SQL Database를 사용한 다중 테넌트 SaaS 애플리케이션 디자인 패턴](saas-tenancy-app-design-patterns.md)을 참조하세요.
- 탄력적 풀을 사용하는 SaaS 자습서의 경우 [Wingtip SaaS 애플리케이션 소개](saas-dbpertenant-wingtip-app-overview.md)를 참조하세요.
- 여러 데이터베이스가 포함된 탄력적 풀의 리소스 관리에 대해 알아보려면 [고밀도 탄력적 풀의 리소스 관리](elastic-pool-resource-management.md)를 참조하세요.
