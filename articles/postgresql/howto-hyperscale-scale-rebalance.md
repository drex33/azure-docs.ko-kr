---
title: 분할된 데이터베이스 리밸런스 - 하이퍼스케일(Citus) - Azure Database for PostgreSQL
description: 성능 향상을 위해 여러 서버에 분할된 데이터베이스를 균등하게 분산
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 07/20/2021
ms.openlocfilehash: 19b44306185dcfcdd3675898b9066019f686dc75
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114463560"
---
# <a name="rebalance-shards-in-hyperscale-citus-server-group"></a>하이퍼스케일(Citus) 서버 그룹의 분할된 데이터베이스 리밸런스

새로 추가된 노드를 활용하려면 분산 테이블 [분할된 데이터베이스](concepts-hyperscale-distributed-data.md#shards)를 리밸런스해야 합니다. 이는 일부 분할된 데이터베이스를 기존 노드에서 새 노드로 이동하는 것을 의미합니다. 하이퍼스케일(Citus)은 가동 중지 시간이 없는 리밸런싱을 제공하므로 분할된 데이터베이스 리밸런싱 중에 쿼리를 중단 없이 실행할 수 있습니다.

## <a name="determine-if-the-server-group-needs-a-rebalance"></a>서버 그룹에 리밸런스가 필요한지 확인

Azure Portal은 데이터가 서버 그룹의 작업자 노드 간에 균등하게 배포되는지 여부를 보여 줄 수 있습니다. 이를 확인하려면 **서버 그룹 관리** 메뉴의 **분할된 데이터베이스 리밸런서** 페이지로 이동합니다. 작업자 간에 데이터가 기울어진 경우 각 노드의 크기 목록과 함께 **리밸런싱이 권장됨** 메시지가 표시됩니다.

데이터가 이미 균형을 이루고 있는 경우 **현재 리밸런싱이 권장되지 않음** 이라는 메시지가 표시됩니다.

## <a name="run-the-shard-rebalancer"></a>분할된 데이터베이스 리밸런서 실행

분할된 데이터베이스 리밸런서를 시작하려면 서버 그룹의 코디네이터 노드에 연결하고 분산 테이블에서 [rebalance_table_shards](reference-hyperscale-functions.md#rebalance_table_shards) SQL 함수를 실행해야 합니다. 함수는 인수에 명명된 테이블의 [공동 배치](concepts-hyperscale-colocation.md) 그룹에 있는 모든 테이블의 균형을 리밸런스합니다. 따라서 분산된 모든 테이블에 대해 함수를 호출할 필요는 없으며, 각 공동 배치 그룹의 대표 테이블에 대해 이 함수를 호출하기만 하면 됩니다.

```sql
SELECT rebalance_table_shards('distributed_table_name');
```

## <a name="monitor-rebalance-progress"></a>리밸런스 진행 상황 모니터링

리밸런서를 시작한 후 확인하려면 Azure Portal로 돌아갑니다. **서버 그룹 관리** 에서 **분할된 데이터베이스 리밸런서** 페이지를 엽니다. 그러면 두 개의 테이블과 함께 **리밸런싱 진행 중** 이라는 메시지가 표시됩니다.

첫 번째 표는 노드 안팎으로 이동하는 분할된 데이터베이스의 수를 보여 줍니다(예: "24개 중 6개 이동됨"). 두 번째 테이블은 이름, 영향을 받은 분할된 데이터베이스 수, 영향을 받은 데이터 크기, 리밸런싱 상태 등 데이터베이스 테이블당 진행 상황을 보여 줍니다.

페이지를 업데이트하려면 **새로 고침** 버튼을 선택합니다. 리밸런싱이 완료되면 다시 **현재 리밸런싱이 권장되지 않음** 이라고 표시됩니다.

## <a name="next-steps"></a>다음 단계

- 서버 그룹 [성능 옵션](concepts-hyperscale-configuration-options.md)에 대해 자세히 알아보기
- [서버 그룹 스케일](howto-hyperscale-scale-grow.md) 업 또는 아웃
- [Rebalance_table_shards](reference-hyperscale-functions.md#rebalance_table_shards) 참조
