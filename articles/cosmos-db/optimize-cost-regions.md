---
title: Azure Cosmos DB에서 다중 지역 배포에 대해 비용 최적화
description: 이 문서에서는 Azure Cosmos DB에서 다중 지역 배포 비용을 관리하는 방법을 설명합니다.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/26/2021
ms.openlocfilehash: 0cd4174cf937fa8e2cda96237a09e8cb4f5270e4
ms.sourcegitcommit: 03f0db2e8d91219cf88852c1e500ae86552d8249
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123029147"
---
# <a name="optimize-multi-region-cost-in-azure-cosmos-db"></a>Azure Cosmos DB에서 여러 지역 비용 최적화
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

언제든지 Azure Cosmos 계정에서 지역을 추가 및 제거할 수 있습니다. 다양한 Azure Cosmos 데이터베이스 및 컨테이너에 대해 구성하는 처리량은 계정과 연결된 각 지역에 예약됩니다. 시간당 프로비전된 처리량, 즉 Azure Cosmos 계정의 모든 데이터베이스 및 컨테이너에 구성된 RU/s의 합계가 `T`이고 데이터베이스 계정과 연결된 Azure 지역 수가 `N`이면 주어진 총 시간 동안 Cosmos 계정에 프로비전된 처리량은 `T x N RU/s`와 같습니다.

단일 쓰기 지역으로 프로비전된 처리량 비용은 100RU당 $0.008/시간이며, 여러 쓰기 가능 지역으로 프로비전된 처리량 비용은 100RU당 $0.016/시간입니다. 자세한 내용은 Azure Cosmos DB [가격 책정 페이지](https://azure.microsoft.com/pricing/details/cosmos-db/)를 참조하세요.

## <a name="costs-for-multiple-write-regions"></a>여러 쓰기 지역에 대한 비용

다중 지역 쓰기 시스템에서 쓰기 작업에 대해 사용 가능한 순 RU는 `N`배만큼 증가합니다. 여기서 `N`은 쓰기 지역 수입니다. 단일 지역 쓰기와 달리, 모든 지역은 이제 쓰기 가능하며 충돌 해결을 지원합니다. 비용 계획 관점에서 전 세계적으로 `M` RU/s에 상당하는 쓰기를 수행하려면 컨테이너 또는 데이터베이스 수준에서 M `RUs`를 프로비전해야 합니다. 원하는 수의 지역을 추가하고 쓰기에 사용하여 전 세계적으로 `M` RU에 상당하는 쓰기를 수행할 수 있습니다.

### <a name="example"></a>예제

미국 서부에 단일 지역 쓰기용으로 구성된 컨테이너가 있고 10K RU/s 처리량으로 프로비전되어 이번 달에 0.5TB의 데이터를 저장한다고 가정해 보겠습니다. 스토리지와 처리량이 동일한 미국 동부 지역을 추가하고 앱에서 두 지역의 컨테이너에 쓰기 기능을 원한다고 가정해봅시다. 새로운 총 월간 청구 금액은 1개월을 730시간으로 가정했을 때 다음과 같습니다.

|**Item**|**사용량(월간)**|**비용**|**월간 비용**|
|----|----|----|----|
|미국 서부의 컨테이너에 대한 처리량 청구(단일 쓰기 지역) |10K RU/s * 730시간 |100RU당 $0.008/시간 |$584 |
|두 지역(미국 서부 및 미국 동부)의 컨테이너에 대한 처리량 청구서(다중 쓰기 지역) |2 * 10K RU/s * 730시간 |100RU당 $0.016/시간 |$2,336 |
|미국 서부의 컨테이너에 대한 스토리지 청구 |0.5TB(또는 512GB) |$0.25/GB |$128 |
|2 개 지역(미국 서부 및 미국 동부)의 컨테이너 스토리지 청구 |2 * 0.5TB(또는 1,024GB) |$0.25/GB |$256 |

## <a name="improve-throughput-utilization-on-a-per-region-basis"></a>지역별 처리량 사용률 개선

사용률이 비효율적인 경우(예: 하나 이상의 사용률이 낮은 읽기 지역) 읽기 지역의 변경 피드를 사용하여 읽기 지역에서 RU를 최대한 활용하거나 초과하는 경우 다른 보조 영역으로 이동하는 단계를 수행할 수 있습니다. 먼저 쓰기 지역에서 프로비전된 처리량(RUs)을 최적화해야 합니다. 큰 쿼리가 아니면 쓰기 비용이 읽기보다 더 비싸므로 사용률을 균일하게 유지하는 것이 어려울 수 있습니다. 전반적으로 지역에서 소비된 처리량을 모니터링하고, 요청 시 지역을 추가하거나 제거하여 읽기 및 쓰기 처리량을 확장하여 동일한 지역에 배포된 모든 앱의 대기 시간에 미치는 영향을 확실히 파악합니다.

## <a name="next-steps"></a>다음 단계

이제 다음 문서를 사용하여 Azure Cosmos DB의 비용 최적화에 대해 좀 더 자세히 알아볼 수 있습니다.

* [개발 및 테스트용으로 최적화](optimize-dev-test.md)에 대한 자세한 정보
* [Azure Cosmos DB 요금 청구 이해](understand-your-bill.md)에 대한 자세한 정보
* [처리량 비용 최적화](optimize-cost-throughput.md)에 대한 자세한 정보
* [스토리지 비용 최적화](optimize-cost-storage.md)에 대한 자세한 정보
* [읽기 및 쓰기 비용 최적화](optimize-cost-reads-writes.md)에 대한 자세한 정보
* [쿼리 비용 최적화](./optimize-cost-reads-writes.md)에 대한 자세한 정보
* Azure Cosmos DB로 마이그레이션하기 위한 용량 계획을 수행하려고 하시나요? 용량 계획을 위해 기존 데이터베이스 클러스터에 대한 정보를 사용할 수 있습니다.
    * 기존 데이터베이스 클러스터의 vCore 및 서버 수만 알고 있는 경우 [vCore 또는 vCPU를 사용하여 요청 단위 예측](convert-vcore-to-request-unit.md)에 대해 읽어보세요. 
    * 현재 데이터베이스 워크로드에 대한 일반적인 요청 비율을 알고 있는 경우 [Azure Cosmos DB 용량 계획 도구를 사용하여 요청 단위 예측](estimate-ru-with-capacity-planner.md)에 대해 읽어보세요.
