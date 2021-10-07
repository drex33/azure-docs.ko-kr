---
title: 메트릭을 보는 방법 - 하이퍼스케일(Citus) - Azure Database for PostgreSQL
description: Azure Database for PostgreSQL 데이터베이스 메트릭에 액세스하는 방법 - 하이퍼스케일(Citus)
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 10/05/2021
ms.openlocfilehash: b698a69631ba2ad8aba58f6de17d8cbee1cbafaf
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/06/2021
ms.locfileid: "129622400"
---
# <a name="how-to-view-metrics-in-azure-database-for-postgresql---hyperscale-citus"></a>Azure Database for PostgreSQL 메트릭을 보는 방법 - 하이퍼스케일(Citus)

리소스 메트릭은 하이퍼스케일(Citus) 서버 그룹의 모든 노드 및 노드 전체에서 집계할 수 있습니다.

## <a name="view-metrics"></a>메트릭 보기

하이퍼스케일(Citus) 서버 그룹의 메트릭에 액세스하려면 Azure Portal **모니터링에서** **메트릭을** 엽니다.

:::image type="content" source="media/howto-hyperscale-monitoring/metrics.png" alt-text="메트릭 화면":::

차원 및 집계(예: CPU **백분율** 및 **최대)를** 선택하여 모든 노드에서 집계된 메트릭을 봅니다. 각 메트릭에 대한 설명은 [여기를 참조하세요.](concepts-hyperscale-monitoring.md#list-of-metrics)

:::image type="content" source="media/howto-hyperscale-monitoring/dimensions.png" alt-text="차원 선택":::

### <a name="view-metrics-per-node"></a>노드당 메트릭 보기

동일한 그래프에서 각 노드의 메트릭을 개별적으로 보는 것을 "분할"이라고 합니다.
이를 사용하도록 설정하려면 **분할 적용을 선택합니다.**

:::image type="content" source="media/howto-hyperscale-monitoring/splitting.png" alt-text="분할 적용 단추":::

분할할 값을 선택합니다. 하이퍼스케일(Citus) 노드의 경우 서버 **이름** 을 선택합니다.

:::image type="content" source="media/howto-hyperscale-monitoring/split-value.png" alt-text="분할 값 선택":::

이제 메트릭이 노드당 하나의 색으로 구분된 줄에 그려지게 됩니다.

:::image type="content" source="media/howto-hyperscale-monitoring/split-chart.png" alt-text="여러 노드 차트 선":::

## <a name="next-steps"></a>다음 단계

* 하이퍼스케일(Citus) [모니터링 개념](concepts-hyperscale-monitoring.md) 검토
