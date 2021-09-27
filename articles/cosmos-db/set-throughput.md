---
title: Azure Cosmos 컨테이너 및 데이터베이스에 대한 처리량 프로비전
description: Azure Cosmos 컨테이너 및 데이터베이스에 프로비전되는 처리량을 설정하는 방법을 알아봅니다.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/16/2021
ms.openlocfilehash: aa10c93b63fd621397318c701deef8c3912b05da
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128601725"
---
# <a name="introduction-to-provisioned-throughput-in-azure-cosmos-db"></a>Azure Cosmos DB의 프로비전된 처리량 소개
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Cosmos DB에서는 데이터베이스 및 컨테이너에 프로비전된 처리량을 설정할 수 있습니다. 프로비전된 처리량에는 표준(수동) 또는 자동 크기 조정이라는 두 가지 유형이 있습니다. 이 문서는 프로비저닝된 처리량의 작동 방식에 대한 개요를 제공합니다. 

Azure Cosmos 데이터베이스는 컨테이너 세트의 관리 단위입니다. 데이터베이스는 스키마 제약 없는 컨테이너의 집합으로 구성됩니다. Azure Cosmos 컨테이너는 처리량과 스토리지 모두에 대한 확장성 단위입니다. 컨테이너는 Azure 지역 내에 있는 머신 세트에 수평적으로 분할되고 Azure Cosmos 계정과 연결된 모든 Azure 지역에 분산됩니다.

Azure Cosmos DB를 사용하면 두 가지 단위로 처리량을 프로비전할 수 있습니다.
 
- Azure Cosmos 컨테이너
- Azure Cosmos 데이터베이스

## <a name="set-throughput-on-a-container"></a>컨테이너의 처리량 설정  

Azure Cosmos 컨테이너에 프로비전된 처리량은 해당 컨테이너 전용으로 예약됩니다. 컨테이너는 항상 프로비전된 처리량을 받습니다. 컨테이너에 프로비전된 처리량은 재정적으로 SLA의 지원을 받습니다. 컨테이너에서 표준(수동) 처리량을 구성하는 방법에 대한 자세한 내용은 [Azure Cosmos 컨테이너에 처리량 프로비전](how-to-provision-container-throughput.md)을 참조하세요. 컨테이너에서 자동 크기 조정 처리량을 구성하는 방법을 알아보려면 [자동 크기 조정 처리량 프로비전](how-to-provision-autoscale-throughput.md)을 참조하세요.

컨테이너에 프로비전된 처리량을 설정하는 방법이 가장 자주 사용되는 옵션입니다. [RU(요청 단위)](request-units.md)를 사용해서 처리량을 원하는 대로 프로비전하여 컨테이너의 처리량 크기를 탄력적으로 조정할 수 있습니다. 

컨테이너에 프로비전된 처리량은 실제 파티션 간에 균등하게 분산되며, 논리 파티션을 실제 파티션 간에 균등하게 분산하는 좋은 파티션 키를 가정하여 처리량이 컨테이너의 모든 논리 파티션에도 균등하게 분산됩니다. 그러나 논리 파티션의 처리량을 선택적으로 지정할 수는 없습니다. 컨테이너에 있는 하나 이상의 논리 파티션은 실제 파티션에서 호스트되므로, 실제 파티션은 컨테이너에 배타적으로 포함되고 컨테이너에서 프로비전된 처리량을 지원합니다. 

논리 파티션에서 실행되는 워크로드가 기본 물리적 파티션에 할당된 처리량보다 더 많이 소비하는 경우 작업 속도가 제한될 수 있습니다. _핫 파티션_ 이라고 하는 것은 하나의 논리 파티션에 다른 파티션 키 값보다 많은 요청이 있을 때 발생합니다.

속도 제한이 발생하는 경우 전체 컨테이너의 프로비전된 처리량을 높이거나 작업을 다시 시도할 수 있습니다. 또한 스토리지와 요청 볼륨을 고르게 분배하는 파티션 키를 선택해야 합니다. 분할에 대한 자세한 내용은 [Azure Cosmos DB의 분할 및 수평 크기 조정](partitioning-overview.md)을 참조하세요.

컨테이너에 대해 예측 가능한 성능을 원하는 경우 컨테이너 단위로 처리량을 구성하는 것이 좋습니다.

다음 이미지는 실제 파티션이 컨테이너의 논리 파티션을 하나 이상 호스트하는 방법을 보여줍니다.

:::image type="content" source="./media/set-throughput/resource-partition.png" alt-text="컨테이너의 하나 이상의 논리 파티션을 호스트하는 물리적 파티션" border="false":::

## <a name="set-throughput-on-a-database"></a>데이터베이스의 처리량 설정

Azure Cosmos 데이터베이스의 처리량을 프로비전하는 경우 데이터베이스의 모든 컨테이너(공유 데이터베이스 컨테이너라고 함) 간에 처리량이 공유됩니다. 단, 데이터베이스의 특정 컨테이너에 프로비전된 처리량을 지정한 경우는 예외입니다. 컨테이너 간에 데이터베이스 수준 프로비전된 처리량을 공유하는 것은 머신 클러스터에 데이터베이스를 호스트하는 것과 비슷합니다. 데이터베이스 내의 모든 컨테이너가 머신에 제공되는 리소스를 공유하므로 당연히 특정 컨테이너에 대한 예상 성능이 제공되지 않습니다. 데이터베이스에서 프로비전된 처리량을 구성하는 방법을 알아보려면 [Azure Cosmos 데이터베이스에 프로비전된 처리량 구성](how-to-provision-database-throughput.md)을 참조하세요. 데이터베이스에서 자동 크기 조정 처리량을 구성하는 방법을 알아보려면 [자동 크기 조정 처리량 프로비전](how-to-provision-autoscale-throughput.md)을 참조하세요.

데이터베이스 내 모든 컨테이너가 프로비전된 처리량을 공유하므로, Azure Cosmos DB는 해당 데이터베이스의 특정 컨테이너에 대해 예측 가능한 처리량을 보장하지 않습니다. 특정 컨테이너가 받을 수 있는 처리량은 다음 조건에 따라 다릅니다.

* 컨테이너 수
* 다양한 컨테이너에 대해 선택한 파티션 키
* 컨테이너의 다양한 논리 파티션에 분산되는 워크로드 

특정 컨테이너의 전용 처리량으로 지정하지 않고 여러 컨테이너 간에 처리량을 공유하려는 경우 데이터베이스의 처리량을 구성하는 것이 좋습니다. 

다음 예제에서는 데이터베이스 수준에서 처리량을 프로비전하는 것이 좋은 경우를 보여 줍니다.

* 데이터베이스에 프로비전된 처리량을 컨테이너 집합 간에 공유하는 방법은 다중 테넌트 애플리케이션에 유용합니다. 각 사용자를 고유한 Azure Cosmos 컨테이너로 나타낼 수 있습니다.

* 데이터베이스에 프로비전된 처리량을 컨테이너 세트 간에 공유하는 방법은 VM 클러스터에 호스트된 MongoDB, Cassandra 등의 NoSQL 데이터베이스를 마이그레이션하거나 온-프레미스 물리적 서버에서 Azure Cosmos DB로 마이그레이션하는 경우에 유용합니다. Azure Cosmos 데이터베이스에 구성된 프로비전된 처리량은 MongoDB 또는 Cassandra 클러스터의 컴퓨팅 용량과 논리적으로 상응하지만 더 비용 효과적이고 탄력적인 것으로 생각하면 됩니다.  

프로비전된 처리량을 가진 데이터베이스 내에서 생성된 모든 컨테이너는 [파티션 키](partitioning-overview.md)를 사용하여 만들어야 합니다. 데이터베이스 내 컨테이너에 할당된 처리량은 지정된 시점에 해당 컨테이너의 모든 논리 파티션 간에 분산됩니다. 데이터베이스에 구성된 프로비전된 처리량을 공유하는 컨테이너가 있는 경우 처리량을 특정 컨테이너 또는 논리 파티션에 선택적으로 적용할 수 없습니다. 

논리 파티션의 워크로드가 특정 논리 파티션에 할당된 처리량보다 많은 양을 사용하는 경우 작업 속도가 제한됩니다. 속도 제한이 발생하는 경우 전체 데이터베이스의 처리량을 높이거나 작업을 다시 시도할 수 있습니다. 분할에 대한 자세한 내용은 [논리 파티션](partitioning-overview.md)을 참조하세요.

공유 처리량 데이터베이스의 컨테이너는 해당 데이터베이스에 할당된 처리량(RU/s)을 공유합니다. 표준(수동) 프로비저닝된 처리량을 사용하면 데이터베이스에서 최소 400RU/s의 컨테이너를 최대 25개까지 가질 수 있습니다. 자동 크기 조정 프로비저닝된 처리량을 사용하면 자동 크기 조정 최대 4,000RU/s(400~4,000RU/s 범위에서 크기 조정)를 사용하여 데이터베이스에 최대 25개 컨테이너를 포함할 수 있습니다.

> [!NOTE]
> 2020년 2월에 공유 처리량 데이터베이스에 최대 25개의 컨테이너를 포함하여 보다 원활히 컨테이너 전체에서 처리량을 공유할 수 있도록 하는 변경 내용이 도입되었습니다. 처음 25개 컨테이너 이후에는 데이터베이스의 공유 처리량과는 별개인 [전용 처리량을 사용하여 프로비전](#set-throughput-on-a-database-and-a-container)된 경우에만 데이터베이스에 더 많은 컨테이너를 추가할 수 있습니다.<br>
Azure Cosmos DB 계정에 컨테이너가 25개 이상인 공유 처리량 데이터베이스가 이미 포함되어 있는 경우 동일한 Azure 구독의 해당 계정 및 다른 모든 계정은 이 변경에서 제외됩니다. 사용자 의견 또는 문의 사항이 있는 경우 [제품 고객 지원팀에 문의](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)하세요. 

워크로드에 데이터베이스의 모든 컬렉션을 삭제하고 다시 만드는 작업이 포함된 경우에는 컬렉션을 만들기 전에 빈 데이터베이스를 삭제하고 새 데이터베이스를 다시 만드는 것이 좋습니다. 다음 이미지는 데이터베이스 내 여러 컨테이너에 속하는 하나 이상의 논리 파티션을 실제 파티션에 호스트할 수 있다는 것을 보여줍니다.

:::image type="content" source="./media/set-throughput/resource-partition2.png" alt-text="서로 다른 컨테이너에 속한 하나 이상의 논리 파티션을 호스트하는 물리적 파티션" border="false":::

## <a name="set-throughput-on-a-database-and-a-container"></a>데이터베이스 및 컨테이너의 처리량 설정

두 모델을 결합할 수 있습니다. 데이터베이스와 컨테이너의 처리량을 둘 다 프로비전할 수 있습니다. 다음 예제에서는 Azure Cosmos 데이터베이스 및 컨테이너에 표준(수동) 프로비전된 처리량을 프로비전하는 방법을 보여 줍니다.

* 이름이 *Z* 이고 프로비전된 처리량이 *"K"* RU인 Azure Cosmos 데이터베이스를 만들 수 있습니다. 
* 다음으로, 데이터베이스 내에서 5개 컨테이너 *A*, *B*, *C*, *D*, *E* 를 만듭니다. 컨테이너 B를 만들 때 **이 컨테이너에 전용 처리량 프로비전** 옵션을 사용하도록 설정하고 이 컨테이너에서 프로비전된 처리량 *"P"* RU를 명시적으로 구성해야 합니다. 데이터베이스 및 컨테이너를 만들 때만 공유 및 전용 처리량을 구성할 수 있습니다. 

   :::image type="content" source="./media/set-throughput/coll-level-throughput.png" alt-text="컨테이너 수준에서 처리량 설정":::

* *"K"* RU 처리량은 4개의 컨테이너 *A*, *C*, *D*, *E* 간에 공유됩니다. *A*, *C*, *D* 또는 *E* 에 사용 가능한 정확한 처리량은 변동합니다. 각 개별 컨테이너의 처리량에 대한 SLA는 없습니다.
* 컨테이너 *B* 는 항상 *"P"* RU 처리량을 보장받을 수 있으며 SLA가 지원됩니다.

> [!NOTE]
> 프로비전된 처리량이 할당된 컨테이너는 공유 데이터베이스 컨테이너로 변환할 수 없습니다. 반대로 공유 데이터베이스 컨테이너는 전용 처리량을 사용하도록 변환할 수 없습니다.

## <a name="update-throughput-on-a-database-or-a-container"></a>데이터베이스 또는 컨테이너에서 처리량 업데이트

Azure Cosmos 컨테이너 또는 데이터베이스를 만든 후 프로비전된 처리량을 업데이트할 수 있습니다. 데이터베이스 또는 컨테이너에 구성할 수 있는 최대 프로비전된 처리량에는 제한이 없습니다.

### <a name="current-provisioned-throughput"></a><a id="current-provisioned-throughput"></a> 현재 프로비저닝된 처리량

Azure Portal에서 또는 SDK를 사용하여 컨테이너 또는 데이터베이스의 프로비저닝된 처리량을 검색할 수 있습니다.

* .NET SDK의 [Container.ReadThroughputAsync](/dotnet/api/microsoft.azure.cosmos.container.readthroughputasync).
* Java SDK의 [CosmosContainer.readThroughput](/java/api/com.azure.cosmos.cosmosasynccontainer.readthroughput).

이러한 메서드의 응답에는 컨테이너 또는 데이터베이스에 대한 [프로비저닝된 최소 처리량](concepts-limits.md#storage-and-database-operations)도 포함됩니다.

* .NET SDK의 [ThroughputResponse.MinThroughput](/dotnet/api/microsoft.azure.cosmos.throughputresponse.minthroughput).
* Java SDK의 [ThroughputResponse.getMinThroughput()](/java/api/com.azure.cosmos.models.throughputresponse.getminthroughput).

실제 최소 RU/s는 계정 구성에 따라 다를 수 있습니다. 그러나 일반적으로 다음 값의 최댓값입니다.

* 400RU/s 
* 현재 스토리지(GB * 10RU/s)(이 제약 조건은 경우에 따라 완화될 수 있음, [높은 스토리지/낮은 처리량 프로그램](#high-storage-low-throughput-program) 참조)
* 데이터베이스 또는 컨테이너/100에서 프로 비전 된 최고/초

### <a name="changing-the-provisioned-throughput"></a>프로비저닝된 처리량 변경

Azure Portal 또는 다음 SDK를 사용하여 컨테이너 또는 데이터베이스의 프로비저닝된 처리량을 조정할 수 있습니다.

* .NET SDK의 [Container.ReplaceThroughputAsync](/dotnet/api/microsoft.azure.cosmos.container.replacethroughputasync).
* Java SDK의 [CosmosContainer.replaceThroughput](/java/api/com.azure.cosmos.cosmosasynccontainer.replacethroughput).

**프로비저닝된 처리량을 줄이는** 경우 [최소](#current-provisioned-throughput)까지 줄일 수 있습니다.

**프로비저닝된 처리량을 늘리는** 경우 대부분의 경우에는 작업이 즉시 수행됩니다. 그러나 필요한 리소스를 프로비저닝하는 시스템 작업으로 인해 작업이 더 오래 걸릴 수 있는 경우가 있습니다. 이 경우 이 작업이 진행 중인 동안 프로비저닝된 처리량을 수정하려고 하면 다른 크기 조정 작업이 진행 중임을 설명하는 오류 메시지와 함께 HTTP 423 응답이 생성됩니다.

[프로 비전 된 처리량 확장에 대 한 모범 사례](scaling-provisioned-throughput-best-practices.md) 문서에서 자세히 알아보세요.

> [!NOTE]
> 프로비저닝된 처리량을 크게 증가시켜야 하는 매우 큰 수집 워크로드를 계획하는 경우 크기 조정 작업에는 SLA가 없으며 이전 단락에서 언급했듯이 증가량이 클 경우 시간이 오래 걸릴 수 있음을 명심합니다. 미리 계획하고 워크로드가 시작되기 전에 크기 조정을 시작하고 아래 방법을 사용하여 진행률을 확인하는 것이 좋습니다.

[현재 프로비저닝된 처리량](#current-provisioned-throughput)을 읽고 다음을 사용하여 프로그래밍 방식으로 크기 조정 진행률을 확인할 수 있습니다.

* .NET SDK의 [ThroughputResponse.IsReplacePending](/dotnet/api/microsoft.azure.cosmos.throughputresponse.isreplacepending).
* Java SDK의 [ThroughputResponse.isReplacePending()](/java/api/com.azure.cosmos.models.throughputresponse.isreplacepending).

[Azure Monitor 메트릭](monitor-cosmos-db.md#view-operation-level-metrics-for-azure-cosmos-db)을 사용하여 리소스에서 프로비저닝된 처리량(RU/s) 및 스토리지의 기록을 볼 수 있습니다.

## <a name="high-storage--low-throughput-program"></a><a id="high-storage-low-throughput-program"></a> 높은 스토리지/낮은 처리량 프로그램

위의 [현재 프로비저닝된 처리량](#current-provisioned-throughput) 섹션에 설명된 대로 컨테이너 또는 데이터베이스에 프로비저닝할 수 있는 최소 처리량은 여러 요인에 따라 다릅니다. Azure Cosmos DB는 스토리지 GB당 10RU/s의 최소 처리량을 적용하므로 그중 하나는 현재 저장된 데이터의 양입니다.

이는 많은 양의 데이터를 저장해야 하지만 상대적으로 낮은 처리량 요구 사항이 있는 상황에서 문제가 될 수 있습니다. 이러한 시나리오를 더 잘 수용하기 위해 Azure Cosmos DB는 적격 계정에 대한 GB당 RU/s 제약 조건을 줄이는 **"높은 스토리지/낮은 처리량" 프로그램** 을 도입했습니다.

이 프로그램에 참여하고 모든 자격을 평가하려면 [이 설문 조사](https://customervoice.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRzBPrdEMjvxPuDm8fCLUtXpUREdDU0pCR0lVVFY5T1lRVEhWNUZITUJGMC4u)를 작성하기만 하면 됩니다. 그러면 Azure Cosmos DB 팀에서 후속 조치를 취하고 온보딩을 진행합니다.

## <a name="comparison-of-models"></a>모델 비교
다음 표에서는 데이터베이스 및 컨테이너에 대한 표준(수동) 처리량 프로비전을 비교합니다. 

|**매개 변수**  |**데이터베이스 표준(수동) 처리량**  |**컨테이너 표준(수동) 처리량**|**데이터베이스 자동 크기 조정 처리량** | **컨테이너 크기 자동 조정 처리량**|
|---------|---------|---------|---------|---------|
|진입점(최소 RU/s) |400RU/s. 컨테이너당 최소 RU/s 없이 최대 25개의 컨테이너 사용 가능</li> |400| 400~4,000RU/s 범위에서 자동 크기 조정. 컨테이너당 최소 RU/s 없이 최대 25개의 컨테이너 사용 가능</li> | 400~4,000RU/s 범위에서 자동 크기 조정.|
|컨테이너당 최소 RU/s|--|400|--|400~4,000RU/s 범위에서 자동 크기 조정|
|최대 RU|데이터베이스에서 무제한|컨테이너에서 무제한|데이터베이스에서 무제한|컨테이너에서 무제한
|특정 컨테이너에 할당 또는 제공되는 RU|보장되지 않습니다. 지정된 컨테이너에 할당되는 RU는 속성에 따라 다릅니다. 속성은 처리량, 워크로드 분산 및 컨테이너 수를 공유하는 컨테이너 파티션 키의 선택 항목일 수 있습니다. |컨테이너에 구성된 모든 RU는 컨테이너에만 배타적으로 예약됩니다.|보장되지 않습니다. 지정된 컨테이너에 할당되는 RU는 속성에 따라 다릅니다. 속성은 처리량, 워크로드 분산 및 컨테이너 수를 공유하는 컨테이너 파티션 키의 선택 항목일 수 있습니다. |컨테이너에 구성된 모든 RU는 컨테이너에만 배타적으로 예약됩니다.|
|컨테이너의 최대 스토리지|무제한.|제한 없음|제한 없음|제한 없음|
|컨테이너의 논리 파티션당 최대 처리량|10,000RU/s|10,000RU/s|10,000RU/s|10,000RU/s|
|컨테이너의 논리 파티션당 최대 스토리지(데이터 + 인덱스)|20GB|20GB|20GB|20GB|

## <a name="next-steps"></a>다음 단계

* [논리 파티션](partitioning-overview.md)에 대해 자세히 알아봅니다.
* [Azure Cosmos 컨테이너에서 표준(수동) 처리량을 프로비전](how-to-provision-container-throughput.md)하는 방법을 알아봅니다.
* [Azure Cosmos 데이터베이스에서 표준(수동) 처리량을 프로비전](how-to-provision-database-throughput.md)하는 방법을 알아봅니다.
* [Azure Cosmos 데이터베이스 또는 컨테이너에서 자동 크기 조정 처리량을 프로비전](how-to-provision-autoscale-throughput.md)하는 방법을 알아봅니다.
* Azure Cosmos DB로 마이그레이션하기 위한 용량 계획을 수행하려고 하시나요? 용량 계획을 위해 기존 데이터베이스 클러스터에 대한 정보를 사용할 수 있습니다.
    * 기존 데이터베이스 클러스터의 vCore 및 서버 수만 알고 있는 경우 [vCore 또는 vCPU를 사용하여 요청 단위 예측](convert-vcore-to-request-unit.md)에 대해 읽어보세요. 
    * 현재 데이터베이스 워크로드에 대한 일반적인 요청 비율을 알고 있는 경우 [Azure Cosmos DB 용량 플래너를 사용하여 요청 단위 예측](estimate-ru-with-capacity-planner.md)에 대해 읽어보세요.
