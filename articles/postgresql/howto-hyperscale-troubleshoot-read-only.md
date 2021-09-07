---
title: 읽기 전용 액세스 문제 해결 – 하이퍼스케일(Citus) - Azure Database for PostgreSQL
description: 하이퍼스케일(Citus) 서버 그룹이 읽기 전용이 될 수 있는 이유 및 수행할 작업 알아보기
keywords: postgresql 연결, 읽기 전용
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 08/03/2021
ms.openlocfilehash: 48ea5fbeacf1769f73b9d505e6cc8fb55e8c3a44
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122528958"
---
# <a name="troubleshoot-read-only-access-to-azure-database-for-postgresql---hyperscale-citus"></a>Azure Database for PostgreSQL에 대한 읽기 전용 액세스 문제 해결 - 하이퍼스케일(Citus)

PostgreSQL은 사용 가능한 디스크 공간이 없는 머신에서 실행할 수 없습니다. PostgreSQL 서버에 대한 액세스를 유지하려면 디스크 공간이 부족해지지 않도록 해야 합니다.

하이퍼스케일(Citus)에서 디스크가 거의 가득 차면 노드가 RO(읽기 전용) 상태로 설정됩니다. 쓰기를 방지하면 디스크가 계속 채워지지 않도록 하고, 읽기에 노드를 사용할 수 있도록 합니다. 읽기 전용 상태 중에는 더 많은 디스크 공간을 확보하도록 조치를 취할 수 있습니다.

특히 하이퍼스케일(Citus) 노드는 남아 있는 사용 가능한 스토리지가 5GiB 미만인 경우 읽기 전용이 됩니다. 서버가 읽기 전용이 되면 모든 기존 세션은 연결이 끊어지고 커밋되지 않은 트랜잭션이 롤백됩니다. 모든 쓰기 작업 및 트랜잭션 커밋은 실패하지만 읽기 쿼리는 계속 작동합니다.

## <a name="ways-to-recover-write-access"></a>쓰기 액세스를 복구하는 방법

### <a name="on-the-coordinator-node"></a>코디네이터 노드에서

* 코디네이터 노드에서 [스토리지 크기를 늘립니다](howto-hyperscale-scale-grow.md#increase-storage-on-nodes). 또는
* 작업자 노드에 로컬 테이블을 배포하거나 데이터를 삭제합니다. 데이터베이스에 연결한 후 다른 명령을 실행하기 전에 `SET SESSION CHARACTERISTICS AS TRANSACTION READ WRITE`를 실행해야 합니다.

### <a name="on-a-worker-node"></a>작업자 노드에서

* 작업자 노드에서 [스토리지 크기를 늘립니다](howto-hyperscale-scale-grow.md#increase-storage-on-nodes). 또는
* 데이터를 다른 노드로 [리밸런스하거나](howto-hyperscale-scale-rebalance.md) 데이터를 삭제합니다.
    * 작업자 노드를 일시적으로 읽기/쓰기로 설정해야 합니다. 작업자 노드에 직접 연결하고 코디네이터 노드에 대해 위에서 설명한 대로 `SET SESSION CHARACTERISTICS`를 사용할 수 있습니다.

## <a name="prevention"></a>방지

서버 스토리지가 임계값에 근접할 때 알리기 위해 경고를 설정하는 것이 좋습니다. 이렇게 하면 읽기 전용 상태가 되지 않도록 조기에 조치를 취합니다. 자세한 내용은 [권장되는 경고](howto-hyperscale-alert-on-metric.md#suggested-alerts)에 대한 설명서를 참조하세요.

## <a name="next-steps"></a>다음 단계

* 읽기 전용 상태에 도달하기 전에 조치를 취할 수 있도록 사전 알림을 위해 [Azure 경고를 설정](howto-hyperscale-alert-on-metric.md#suggested-alerts)합니다.
* PostgreSQL 설명서에서 [디스크 사용량](https://www.postgresql.org/docs/current/diskusage.html)에 대해 알아봅니다.
* PostgreSQL 설명서에서 [세션 특징](https://www.postgresql.org/docs/13/sql-set-transaction.html)에 대해 알아봅니다.
