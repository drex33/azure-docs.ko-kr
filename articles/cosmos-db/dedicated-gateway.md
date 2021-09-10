---
title: Azure Cosmos DB 전용 게이트웨이
description: 전용 게이트웨이는 Azure Cosmos DB 계정의 프런트 엔드가 되는 계산입니다. 전용 게이트웨이에 연결하면 요청을 라우팅하고 데이터를 캐시합니다.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 08/30/2021
ms.author: tisande
ms.openlocfilehash: 8c253abdfc8ded8ab5507d482640d78e6d4469c8
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/30/2021
ms.locfileid: "123226610"
---
# <a name="azure-cosmos-db-dedicated-gateway---overview-preview"></a>Azure Cosmos DB 전용 게이트웨이 - 개요(미리 보기)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

전용 게이트웨이는 서버 쪽 컴퓨팅으로, Azure Cosmos DB 계정의 프런트 엔드입니다. 전용 게이트웨이에 연결하면 요청을 라우팅하고 데이터를 캐시합니다. 프로비전된 처리량과 마찬가지로 전용 게이트웨이는 매시간 청구됩니다.

## <a name="overview"></a>개요

규모에 맞게 성능을 향상시키기 위해 전용 게이트웨이를 프로비전할 수 있습니다. 전용 게이트웨이를 프로비전하려는 가장 일반적인 이유는 캐싱입니다. 전용 게이트웨이를 프로비전할 때 전용 게이트웨이 내에서 [통합 캐시](integrated-cache.md)가 자동으로 구성됩니다. 통합 캐시에 도달한 지점 읽기 및 쿼리는 RU를 사용하지 않습니다. 통합 캐시를 사용하여 전용 게이트웨이를 프로비전하면 읽기 작업이 많은 워크로드에서 Azure Cosmos DB 비용을 줄일 수 있습니다.

전용 게이트웨이는 Azure Cosmos DB에 기본 제공됩니다. [전용 게이트웨이를 프로비전](how-to-configure-integrated-cache.md)할 경우 완전 관리형 노드로 요청을 백 엔드 파티션에 라우팅합니다. 전용 게이트웨이를 사용하여 Azure Cosmos DB에 연결하면 표준 게이트웨이로 Azure Cosmos DB에 연결하는 것보다 대기 시간이 더 빠르고 예측 가능해집니다. 전용 게이트웨이와 표준 게이트웨이를 비교하면 캐시 누락도 대기 시간을 향상시킬 수 있습니다.

애플리케이션에서 전용 게이트웨이를 사용하는 데 필요한 최소한의 코드 변경만 있습니다. 신규 및 기존 Azure Cosmos DB 계정 모두 읽기 성능을 향상시키기 위해 전용 게이트웨이를 프로비전할 수 있습니다.

> [!NOTE]
> 전용 게이트웨이에 관한 피드백이 있나요? 많은 의견 부탁드립니다. 언제든지 자유롭게 Azure Cosmos DB 엔지니어링 팀(cosmoscachefeedback@microsoft.com)과 피드백을 직접 공유해 주세요.

## <a name="connection-modes"></a>연결 모드

Azure Cosmos DB 계정에 연결하는 방법에는 다음 세 가지가 있습니다.

- [직접 모드](#connect-to-azure-cosmos-db-using-direct-mode)
- [표준 게이트웨이를 사용하는 게이트웨이 모드](#connect-to-azure-cosmos-db-using-gateway-mode)
- [전용 게이트웨이를 사용하는 게이트웨이 모드](#connect-to-azure-cosmos-db-using-the-dedicated-gateway)(SQL API 계정에만 사용 가능)

### <a name="connect-to-azure-cosmos-db-using-direct-mode"></a>직접 모드를 사용하여 Azure Cosmos DB에 연결

직접 모드를 사용하여 Azure Cosmos DB에 연결하면 애플리케이션이 Azure Cosmos DB 백 엔드에 직접 연결됩니다. 실제 파티션이 많더라도 요청 라우팅은 전적으로 클라이언트 쪽에서 처리됩니다. 직접 모드는 애플리케이션이 Azure Cosmos DB 백 엔드와 직접 통신할 수 있고 중간 네트워크 홉이 필요하지 않기 때문에 짧은 대기 시간을 제공합니다.

직접 모드 연결의 그래픽 표현:

:::image type="content" source="./media/dedicated-gateway/direct-mode.png" alt-text="Cosmos DB 직접 모드의 작동 방식을 보여 주는 이미지" border="false":::

### <a name="connect-to-azure-cosmos-db-using-gateway-mode"></a>게이트웨이 모드를 사용하여 Azure Cosmos DB에 연결

게이트웨이 모드를 사용하여 Azure Cosmos DB를 연결하는 경우 애플리케이션은 먼저 프런트 엔드 노드에 연결합니다. 이 노드는 적절한 백 엔드 노드에 대한 요청 라우팅을 처리합니다. 게이트웨이 모드에는 추가 네트워크 홉이 포함되므로 직접 모드에 비해 대기 시간이 약간 더 높을 수 있습니다. 

게이트웨이 모드를 사용하여 Azure Cosmos DB를 연결할 때는 다음 옵션 중 하나를 사용하여 연결할 수 있습니다.

* **표준 게이트웨이** - 프로비전된 처리량 및 스토리지를 포함하는 백 엔드에는 컨테이너당 전용 용량이 있지만 표준 게이트웨이는 여러 Azure Cosmos 계정 간에 공유됩니다. 많은 고객이 표준 게이트웨이를 공유하면 각 개별 고객이 소비하는 컴퓨팅 리소스가 적어지므로 실용적입니다.
* **전용 게이트웨이** - 이 게이트웨이에서는 백 엔드와 게이트웨이 모두가 전용 용량을 갖습니다. 통합 캐시는 Azure Cosmos DB 계정으로 한정된 상당한 CPU 및 메모리가 필요하기 때문에 전용 게이트웨이 노드가 필요합니다.

### <a name="connect-to-azure-cosmos-db-using-the-dedicated-gateway"></a>전용 게이트웨이를 사용하여 Azure Cosmos DB 연결

통합 캐시를 사용하려면 전용 게이트웨이를 사용하여 Azure Cosmos DB에 연결해야 합니다. 전용 게이트웨이에는 Azure Cosmos DB 계정과 함께 제공되며 표준 엔드포인트와는 다른 엔드포인트가 있습니다. 전용 게이트웨이 엔드포인트에 연결하면 애플리케이션에서 전용 게이트웨이로 요청을 보낸 다음, 요청을 여러 백 엔드 노드로 라우팅합니다. 가능한 경우 통합 캐시가 결과를 제공합니다.

전용 게이트웨이를 사용하는 게이트웨이 모드 연결 다이어그램:

:::image type="content" source="./media/dedicated-gateway/dedicated-gateway-mode.png" alt-text="Cosmos DB 전용 게이트웨이의 작동 방식을 보여 주는 이미지" border="false":::
 
## <a name="provisioning-the-dedicated-gateway"></a>전용 게이트웨이 프로비전

전용 게이트웨이 클러스터는 코어(SQL) API 계정으로 프로비전할 수 있습니다. 전용 게이트웨이 클러스터는 최대 5개의 노드를 포함할 수 있으며, 언제든지 노드를 추가하거나 제거할 수 있습니다. 계정 내의 모든 전용 게이트웨이 노드는 [동일한 연결 문자열을 공유](how-to-configure-integrated-cache.md#configuring-the-integrated-cache)합니다.

전용 게이트웨이 노드는 서로 독립적입니다. 여러 개의 전용 게이트웨이 노드를 프로비전할 경우 모든 단일 노드에서 지정된 요청을 라우팅할 수 있습니다. 또한 각 노드에는 서로 다른 별도의 통합 캐시가 있습니다. 각 노드 내에 캐시된 데이터는 특정 노드를 통해 최근에 [쓰거나 읽은](integrated-cache.md#item-cache) 데이터에 따라 달라집니다. 즉, 항목이나 쿼리가 하나의 노드에 캐시되는 경우 다른 노드에 캐시될 필요는 없습니다.

개발을 위해서는 하나의 노드로 시작하는 것이 좋지만, 프로덕션 환경에서는 고가용성을 위해 세 개 이상의 노드를 프로비전해야 합니다. [통합 캐시를 사용하여 전용 게이트웨이 클러스터를 프로비전하는 방법을 알아봅니다](how-to-configure-integrated-cache.md). 여러 전용 게이트웨이 노드를 프로비전하면 전용 게이트웨이 노드 중 하나를 사용할 수 없는 경우에도 전용 게이트웨이 클러스터가 요청을 계속 라우팅하고 캐시된 데이터를 제공할 수 있습니다.

공개 미리 보기 상태이므로 전용 게이트웨이에는 가용성 SLA가 없습니다. 그러나 일반적으로 Azure Cosmos DB 계정의 나머지 부분에 대한 가용성을 향상시킬 수 있습니다.

전용 게이트웨이는 다음과 같은 크기로 제공됩니다.

| **SKU 이름** | **vCPU** | **메모리**  |
| ------------ | -------- | ----------- |
| **D4s**      | **4**    | **16GB** |
| **D8s**      | **8**    | **32GB** |
| **D16s**     | **16**   | **64GB** |

> [!NOTE]
> 만든 후에는 전용 게이트웨이 노드의 크기를 수정할 수 없습니다. 그러나 노드를 추가하거나 제거할 수는 있습니다.

## <a name="dedicated-gateway-in-multi-region-accounts"></a>다중 지역 계정의 전용 게이트웨이

다중 지역 계정에서 전용 게이트웨이 클러스터를 프로비전하면 동일한 전용 게이트웨이 클러스터가 각 지역에 프로비전됩니다. 미국 동부 및 북유럽의 Azure Cosmos DB 계정을 예로 들 수 있습니다. 이 계정에 두 개의 D8 노드가 있는 전용 게이트웨이 클러스터를 프로비전하는 경우 총 4개의 D8 노드(미국 동부에 2개, 북유럽에 2개)가 있습니다. 각 지역에서 전용 게이트웨이를 명시적으로 구성할 필요가 없으며 연결 문자열은 동일하게 유지됩니다. 장애 조치(failover) 수행에 대한 모범 사례도 변경되지 않습니다.

> [!NOTE]
> 가용성 영역이 사용 설정된 계정에서는 전용 게이트웨이 클러스터를 프로비전할 수 없습니다.

클러스터 내의 노드와 마찬가지로 지역 간 전용 게이트웨이 노드는 독립적입니다. 해당 지역에 대한 최근 읽기 또는 쓰기에 따라 각 지역의 캐시된 데이터가 다를 수 있습니다.

## <a name="limitations"></a>제한 사항

공개 미리 보기 중에는 전용 게이트웨이에 다음과 같은 제한 사항이 있습니다.

- 전용 게이트웨이는 SQL API 계정에서만 지원됩니다.
- [IP 방화벽](how-to-configure-firewall.md) 또는 [프라이빗 링크](how-to-configure-private-endpoints.md)가 구성된 Azure Cosmos DB 계정에서 전용 게이트웨이를 프로비전할 수 없습니다.
- [가용성 영역](high-availability.md#availability-zone-support)이 사용 설정된 Azure Cosmos DB 계정에서 전용 게이트웨이를 프로비전할 수 없습니다.
- [RBAC(역할 기반 액세스 제어)](how-to-setup-rbac.md)를 사용하여 전용 게이트웨이를 통해 라우팅되는 데이터 평면 요청을 인증할 수는 없습니다.

## <a name="supported-regions"></a>지원되는 지역

전용 게이트웨이는 공개 미리 보기 상태이며, 아직 모든 Azure 지역에서 지원되지는 않습니다. 공개 미리 보기 기간 동안 새로운 용량이 추가되고 있습니다. 전용 게이트웨이가 일반 공급될 때는 지역 제한이 없을 것입니다.

현재 지원되는 Azure 지역의 목록:

| **아메리카** | **유럽 및 아프리카**  | **아시아 태평양**  |
| ------------ | -------- | ----------- | 
| 브라질 남부      | 프랑스 중부    | 오스트레일리아 중부 |
| 캐나다 중부  | 프랑스 남부    | 오스트레일리아 중부 2 |
| 캐나다 동부     | 독일 북부   | 오스트레일리아 남동부 |
| 미국 중부     | 독일 중서부   | 인도 중부 |
| 미국 동부     | 북유럽   | 동아시아 |
| 미국 동부 2     | 스위스 북부   | 일본 서부 |
| 미국 중북부     | 영국 남부   | 한국 중부 |
| 미국 중남부     | 영국 서부   | 한국 남부 |
| 미국 중서부     | 서유럽   | 동남 아시아 |
| 미국 서부     |   | 아랍에미리트 중부 |
| 미국 서부 2     |    | 인도 서부 |


## <a name="next-steps"></a>다음 단계

다음 문서에서 전용 게이트웨이 사용에 대해 자세히 읽어보세요.

- [통합 캐시](integrated-cache.md)
- [통합 캐시 구성](how-to-configure-integrated-cache.md)
- [통합 캐시 FAQ](integrated-cache-faq.md)
- Azure Cosmos DB로 마이그레이션하기 위한 용량 계획을 수행하려고 하시나요? 용량 계획을 위해 기존 데이터베이스 클러스터에 대한 정보를 사용할 수 있습니다.
    - 기존 데이터베이스 클러스터의 vCore 및 서버 수만 알고 있는 경우 [vCore 또는 vCPU를 사용하여 요청 단위 예측](convert-vcore-to-request-unit.md)에 대해 읽어보세요. 
    - 현재 데이터베이스 워크로드에 대한 일반적인 요청 비율을 알고 있는 경우 [Azure Cosmos DB 용량 플래너를 사용하여 요청 단위 예측](estimate-ru-with-capacity-planner.md)에 대해 읽어보세요.