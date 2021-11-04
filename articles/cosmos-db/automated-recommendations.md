---
title: Azure Cosmos DB에 대한 자동화된 성능, 비용, 보안 권장 사항
description: 워크로드 패턴에 따라 Azure Cosmos DB에 대한 사용자 지정 성능, 비용, 보안, 기타 권장 사항을 보는 방법을 알아봅니다.
author: ThomasWeiss
ms.author: thweiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/26/2021
ms.reviewer: sngun
ms.openlocfilehash: 73bad87352176e4272a68248bf7f052dccc0165a
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131446413"
---
# <a name="automated-recommendations-for-azure-cosmos-db"></a>Azure Cosmos DB에 대한 자동화된 권장 사항
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Cosmos DB를 포함한 모든 클라우드 서비스는 새로운 기능과 개선 사항으로 자주 업데이트됩니다. 애플리케이션에서 최신 성능 및 보안 업데이트를 유지하는 것이 중요합니다. Azure Portal은 애플리케이션 성능을 최대화할 수 있는 사용자 지정 권장 사항을 제공합니다. Azure Cosmos DB 자문 엔진은 Azure Cosmos DB 리소스의 사용 기록을 지속적으로 분석하고 워크로드 패턴에 따라 권장 사항을 제공합니다. 이 권장 사항은 분할, 인덱싱, 네트워크, 보안 등의 영역에 해당합니다. 사용자 지정 권장 사항은 애플리케이션 성능을 향상시키는 데 도움이 됩니다.

## <a name="view-recommendations"></a>권장 사항 보기

다음과 같은 방법으로 Azure Cosmos DB에 대한 권장 사항을 볼 수 있습니다.

- 권장 사항을 보는 한 가지 방법은 알림 탭에 있습니다. 새 권장 사항이 있으면 메시지 표시줄이 나타납니다. [Azure Portal](https://portal.azure.com)에 로그인한 다음, Azure Cosmos 계정으로 이동합니다. Azure Cosmos 계정 내에서 **알림** 창을 열고 **권장 사항** 탭을 선택합니다. 메시지를 선택하면 권장 사항을 볼 수 있습니다.  

   :::image type="content" source="./media/automated-recommendations/cosmos-db-pane-recommendations.png" alt-text="Azure Cosmos DB 창에서 권장 사항 보기":::

- [Azure Advisor](../advisor/advisor-overview.md)를 통해 비용, 보안, 안정성, 성능, 운영 효율성과 같은 다양한 버킷으로 분류된 권장 사항을 찾을 수도 있습니다. 특정 구독을 선택하고 리소스 종류(**Azure Cosmos DB 계정**)로 필터링할 수 있습니다.  특정 권장 사항을 선택하면 워크로드를 활용하기 위해 수행할 수 있는 작업이 표시됩니다.

   :::image type="content" source="./media/automated-recommendations/advisor-pane-recommendations.png" alt-text="Azure Advisor 창에서 권장 사항 보기":::

Azure Cosmos DB 창에 표시된 일부 권장 사항은 Azure Advisor에서 사용할 수 없으며, 그 반대의 경우도 마찬가지입니다. 권장 사항 유형에 따라 Azure Advisor 창, Azure Cosmos DB 창 또는 둘 다에 해당하기 때문입니다.

현재 Azure Cosmos DB는 다음 영역에 대한 권장 사항을 지원합니다. 각 권장 사항에는 관련 설명서 섹션의 링크가 포함되어 있으므로 다음 단계를 쉽게 수행할 수 있습니다.

## <a name="sdk-usage-recommendations"></a>SDK 사용 권장 사항

이 범주에서 Advisor는 이전 버전의 SDK 사용을 검색하고 최신 버그 수정과 성능 개선 사항을 활용하기 위해 최신 버전으로 업그레이드하도록 권장합니다. 현재 제공되는 SDK 관련 권장 사항은 다음과 같습니다.

|이름  |설명  |
|---------|---------|
| 이전 Spark 커넥터 | 이전 버전의 Spark 커넥터 사용을 검색하고 업그레이드하도록 권장합니다. |
| 이전 .NET SDK | 이전 버전의 .NET SDK 사용을 검색하고 업그레이드하도록 권장합니다. |
| 이전 Java SDK | 이전 버전의 Java 커넥터 사용을 검색하고 업그레이드하도록 권장합니다. |

## <a name="indexing-recommendations"></a>인덱싱 권장 사항

이 범주에서 Advisor는 인덱싱 모드, 인덱싱 정책, 인덱싱된 경로를 검색하고 현재 구성이 쿼리 성능에 영향을 주는 경우 변경하도록 권장합니다. 현재 제공되는 인덱싱 관련 권장 사항은 다음과 같습니다.

|이름  |설명  |
|---------|---------|
| 지연 인덱싱 | 지연 인덱싱 모드 사용을 검색하고 대신 일관된 인덱싱 모드를 사용하도록 권장합니다. Azure Cosmos DB의 지연 인덱싱 모드는 제한적인 목적으로 사용되며 경우에 따라 쿼리 결과 새로 고침에 영향을 줄 수 있으므로 일관된 인덱싱 모드가 권장됩니다. |
| 복합 인덱싱| 쿼리가 복합 인덱스를 활용할 수 있는 계정을 검색하고 복합 인덱스를 사용하도록 권장합니다. 복합 인덱스는 일부 쿼리의 성능과 처리량 사용을 크게 향상할 수 있습니다.|
| 여러 인덱싱된 경로를 사용하는 기본 인덱싱 정책 | 여러 인덱싱된 경로를 사용하는 기본 인덱싱에서 실행 중인 컨테이너를 검색하고 인덱싱 정책을 사용자 지정하도록 권장합니다.|
| RU/s 요금이 높은 ORDER BY 쿼리| RU/s 요금이 높은 ORDER BY 쿼리를 실행하는 컨테이너를 검색하고 복합 인덱스를 살펴보도록 권장합니다.|
| 인덱스가 없고 RU/s 사용이 높은 MongoDB 3.6 계정| RU/s 요금이 높은 쿼리를 실행하는 컨테이너 버전 3.6이 있는 MongoDB용 Azure Cosmos DB API를 검색하고 인덱스를 추가하도록 권장합니다.|

## <a name="cost-optimization-recommendations"></a>비용 최적화 권장 사항

이 범주에서 Advisor는 RU/s 사용을 검색하고 리소스를 변경하거나 다른 가격 책정 모델을 활용하여 가격을 최적화할 수 있음을 확인합니다. 현재 제공되는 비용 최적화 관련 권장 사항은 다음과 같습니다.

|이름  |설명  |
|---------|---------|
| 예약 용량 | RU/s 사용률을 검색하고 예약 인스턴스를 활용할 수 있는 사용자에게 예약 인스턴스를 권장합니다. |
| 비활성 컨테이너 | 30일 넘게 사용되지 않은 컨테이너를 검색하고 해당 컨테이너의 처리량을 줄이거나 삭제하도록 권장합니다.|
| 처리량이 높은 새 구독 | 하루에 비정상적으로 높은 RU/s를 지출하는 계정이 포함된 새 구독을 검색하고 알림을 제공합니다. 이 알림은 특히 Azure Cosmos DB가 사용량 기반 모델이 아니라 프로비저닝된 처리량 기반 모델로 작동한다는 것을 새 고객에게 알리기 위한 것입니다. |
| 자동 크기 조정 사용 | 현재 수동 처리량을 사용 하는 데이터베이스 및 컨테이너에서 자동 크기 조정을 사용 하 여 비용을 절감할 수 있는지 검색 합니다. |
| 자동 크기 조정 대신 수동 처리량 사용 | 현재 자동 크기 조정 처리량을 사용 하는 데이터베이스 및 컨테이너에서 수동 처리량으로 전환 하 여 비용을 절감할 수 있는지 검색 합니다. |

## <a name="migration-recommendations"></a>마이그레이션 권장 사항

이 범주에서 Advisor는 레거시 기능 사용을 검색하고 Azure Cosmos DB의 대규모 스케일링 수준과 기타 이점을 활용할 수 있도록 마이그레이션을 권장합니다. 현재 제공되는 마이그레이션 관련 권장 사항은 다음과 같습니다.

|이름  |설명  |
|---------|---------|
| 분할되지 않은 컨테이너 | 최대 스토리지 한도에 도달하는 고정 크기 컨테이너를 검색하고 분할된 컨테이너로 마이그레이션하도록 권장합니다.|

## <a name="query-usage-recommendations"></a>쿼리 사용 권장 사항

이 범주에서 Advisor는 쿼리 실행을 검색하고 몇 가지 변경으로 쿼리 성능을 튜닝할 수 있음을 식별합니다. 현재 제공되는 쿼리 사용 권장 사항은 다음과 같습니다.

|이름  |설명  |
|---------|---------|
| 고정 페이지 크기를 사용하는 쿼리 | 고정 페이지 크기로 실행된 쿼리를 검색하고 특정 값을 정의하는 대신 -1(페이지 크기 한도 없음)을 사용하도록 권장합니다. 이 옵션은 모든 결과를 검색하는 데 필요한 네트워크 왕복 횟수를 줄입니다. |

## <a name="next-steps"></a>다음 단계

* [Azure Cosmos DB에서 쿼리 성능 튜닝](sql-api-query-metrics.md)
* Azure Cosmos DB 사용 시 [쿼리 문제 해결](troubleshoot-query-performance.md)
* Azure Cosmos DB로 마이그레이션하기 위한 용량 계획을 수행하려고 하시나요? 용량 계획을 위해 기존 데이터베이스 클러스터에 대한 정보를 사용할 수 있습니다.
    * 기존 데이터베이스 클러스터의 vCore 및 서버 수만 알고 있는 경우 [vCore 또는 vCPU를 사용하여 요청 단위 예측](convert-vcore-to-request-unit.md)에 대해 읽어보세요. 
    * 현재 데이터베이스 워크로드에 대한 일반적인 요청 비율을 알고 있는 경우 [Azure Cosmos DB 용량 계획 도구를 사용하여 요청 단위 예측](estimate-ru-with-capacity-planner.md)에 대해 읽어보세요.
