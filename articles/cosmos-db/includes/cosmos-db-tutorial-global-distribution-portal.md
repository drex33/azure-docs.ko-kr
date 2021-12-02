---
title: Azure Cosmos DB 전역 배포
description: Azure Portal에서 Azure Cosmos DB를 사용하여 데이터를 전역으로 복제하는 방법을 알아봅니다.
services: cosmos-db
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.topic: include
ms.date: 12/26/2018
ms.custom: include file
ms.openlocfilehash: e8b7aaaca3536831ce976a8c60333c1eb0476f92
ms.sourcegitcommit: 93c7420c00141af83ed3294923b4826dd4dc6ff2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/02/2021
ms.locfileid: "133445064"
---
## <a name="add-global-database-regions-using-the-azure-portal"></a><a id="addregion"></a>Azure Portal을 사용하여 글로벌 데이터베이스 지역 추가
Azure Cosmos DB는 전 세계의 모든 [Azure 지역][azureregions]에서 사용할 수 있습니다. 데이터베이스 계정에서 기본 일관성 수준을 선택한 후에는 (선택한 기본 일관성 수준 및 글로벌 배포 수요에 따라) 하나 이상의 지역을 연결합니다.

1. [Azure Portal](https://portal.azure.com/)의 왼쪽 막대에서 **Azure Cosmos DB** 를 클릭합니다.
2. **Azure Cosmos DB** 페이지에서 수정할 데이터베이스 계정을 선택합니다.
3. 계정 페이지의 메뉴에서 **전역적으로 데이터 복제** 를 클릭합니다.
4. **전역으로 데이터 복제** 페이지에서 맵의 지역을 클릭하여 추가 또는 제거할 지역을 선택하고 **저장** 을 클릭합니다. 지역을 추가하는 비용에 대한 자세한 내용은 [가격 책정 페이지](https://azure.microsoft.com/pricing/details/cosmos-db/) 또는 [Azure Cosmos DB를 사용하여 전역적으로 데이터 배포](../distribute-data-globally.md) 문서를 참조하세요.
   
    ![지도에서 지역을 클릭하여 추가/삭제][1]
    
두 번째 하위 지역을 추가하면 포털의 **전역으로 데이터 복제** 페이지에서 **수동 장애 조치** 옵션 사용이 설정됩니다. 장애 조치 프로세스를 테스트하거나 기본 쓰기 지역을 변경하려면 이 옵션을 사용할 수 있습니다. 세 번째 하위 지역을 추가하면 동일한 페이지에서 **장애 조치 우선 순위** 옵션 사용이 설정되므로 읽기의 장애 조치 순서를 변경할 수 있습니다.  

### <a name="selecting-global-database-regions"></a>글로벌 데이터베이스 지역 선택
둘 이상의 지역을 구성하기 위한 두 가지 일반적인 시나리오는 다음과 같습니다.

1. 전세계 어느 위치에 있든 관계 없이 최종 사용자에게 낮은 대기 시간으로 데이터 액세스 제공
2. BCDR(무중단 업무 방식 및 재해 복구)를 위한 지역 복원 기능 추가

최종 사용자에게 낮은 대기 시간을 제공하기 위해서는 애플리케이션 사용자가 있는 위치와 동일한 지역에 애플리케이션과 Azure Cosmos DB를 모두 배포하는 것이 좋습니다.

BCDR의 경우 [Azure의 지역 간 복제: 비즈니스 연속성 및 재해 복구](../../availability-zones/cross-region-replication-azure.md) 문서에서 설명한 지역 쌍을 기반으로 지역을 추가 하는 것이 좋습니다.

<!--

## <a id="selectwriteregion"></a>Select the write region

While all regions associated with your Cosmos DB database account can serve reads (both, single item as well as multi-item paginated reads) and queries, only one region can actively receive the write (insert, upsert, replace, delete) requests. To set the active write region, do the following  


1. In the **Azure Cosmos DB** blade, select the database account to modify.
2. In the account blade, click **Replicate data globally** from the menu.
3. In the **Replicate data globally** blade, click **Manual Failover** from the top bar.
    ![Change the write region under Azure Cosmos DB Account > Replicate data globally > Manual Failover][2]
4. Select a read region to become the new write region, click the checkbox to confirm triggering a failover, and click OK
    ![Change the write region by selecting a new region in list under Azure Cosmos DB Account > Replicate data globally > Manual Failover][3]

--->

<!--Image references-->
[1]: ./media/cosmos-db-tutorial-global-distribution-portal/azure-cosmos-db-add-region.png
[2]: ./media/cosmos-db-tutorial-global-distribution-portal/azure-cosmos-db-manual-failover-1.png
[3]: ./media/cosmos-db-tutorial-global-distribution-portal/azure-cosmos-db-manual-failover-2.png

<!--Reference style links - using these makes the source content way more readable than using inline links-->
[consistency]: ../consistency-levels.md
[azureregions]: https://azure.microsoft.com/regions/#services
[offers]: https://azure.microsoft.com/pricing/details/cosmos-db/