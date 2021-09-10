---
title: 서버 그룹 스케일링 - 하이퍼스케일(Citus) - Azure Database for PostgreSQL
description: 서버 그룹 메모리, 디스크 및 CPU 리소스를 조정하여 증가된 부하 처리
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 08/03/2021
ms.openlocfilehash: 12c4cd7848b0d58fd6b91e27e254ccc613ff5676
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122567405"
---
# <a name="scale-a-hyperscale-citus-server-group"></a>하이퍼스케일(Citus) 서버 그룹 스케일링

Azure Database for PostgreSQL - 하이퍼스케일(Citus)은 늘어난 부하를 처리하기 위한 셀프 서비스 스케일링 기능을 제공합니다. Azure Portal을 사용하면 쉽게 새 작업자 노드를 추가하고 기존 노드의 vCore를 늘릴 수 있습니다. 노드를 추가해도 가동 중지 시간이 발생하지 않으며 쿼리를 중단하지 않고 분할된 데이터베이스가 새 노드로 이동됩니다([분할된 데이터베이스 리밸런싱](howto-hyperscale-scale-rebalance.md)이라고 함).

## <a name="add-worker-nodes"></a>작업자 노드 추가

노드를 추가하려면 하이퍼스케일(Citus) 서버 그룹의 **컴퓨팅 + 스토리지** 탭으로 이동합니다.  **작업자 노드 수** 에 대한 슬라이더를 끌어 값을 변경합니다.

> [!NOTE]
>
> [기본 계층](concepts-hyperscale-tiers.md)을 사용하여 만든 하이퍼스케일(Citus) 서버 그룹에는 작업자가 없습니다. 작업자 수를 늘리면 서버 그룹이 표준 계층으로 자동 변경됩니다.  서버 그룹을 표준 계층으로 업그레이드한 후에는 기본 계층으로 다시 다운그레이드할 수 없습니다.

:::image type="content" source="./media/howto-hyperscale-scaling/01-sliders-workers.png" alt-text="리소스 슬라이더":::

**저장** 단추를 클릭하여 변경된 값을 적용합니다.

> [!NOTE]
> 작업자 노드 수를 늘리고 저장한 다음에는 슬라이더를 사용하여 줄일 수 없습니다.

> [!NOTE]
> 새로 추가된 노드를 활용하려면 [분산 테이블 분할된 데이터베이스를 리밸런스](howto-hyperscale-scale-rebalance.md)해야 합니다. 이는 일부 [분할된 데이터베이스](concepts-hyperscale-distributed-data.md#shards)를 기존 노드에서 새 노드로 이동하는 것을 의미합니다. 리밸런싱은 백그라운드에서 작동할 수 있으며 가동 중지 시간이 필요하지 않습니다.

## <a name="increase-or-decrease-vcores-on-nodes"></a>노드에서 vCore 늘리기 또는 줄이기

새 노드를 추가할 뿐만 아니라 기존 노드의 기능을 향상할 수 있습니다. 컴퓨팅 용량을 늘리거나 줄이면 트래픽 수요의 단기 또는 장기 변경뿐과 성능 실험에 유용할 수 있습니다.

모든 작업자 노드에 대한 vCore를 변경하려면 **구성(작업자 노드당)** 에서 **vCore** 슬라이더를 조정합니다. 코디네이터 노드의 vCore는 독립적으로 조정할 수 있습니다. **구성(코디네이터 노드)** 에서 **vCore** 슬라이더를 조정합니다.

## <a name="increase-storage-on-nodes"></a>노드의 저장소 늘리기

새 노드를 추가할 뿐만 아니라 기존 노드의 디스크 공간을 늘릴 수 있습니다. 디스크 공간을 늘리면 작업자 노드를 더 추가하기 전에 기존 작업자 노드로 더 많은 작업을 수행할 수 있습니다.

모든 작업자 노드에 대한 저장소를 변경하려면 **구성(작업자 노드당)** 에서 **저장소** 슬라이더를 조정합니다. 코디네이터 노드의 저장소는 독립적으로 조정할 수 있습니다. **구성(코디네이터 노드)** 에서 **저장소** 슬라이더를 조정합니다.

> [!NOTE]
> 늘리고 저장한 다음에는 슬라이더를 사용하여 노드당 스토리지를 줄일 수 없습니다.

## <a name="next-steps"></a>다음 단계

- 서버 그룹 [성능 옵션](concepts-hyperscale-configuration-options.md)에 대해 자세히 알아보기
- 모든 작업자 노드가 병렬 쿼리에 참여할 수 있도록 [분산 테이블 분할된 데이터베이스 리밸런스](howto-hyperscale-scale-rebalance.md)
