---
title: XTP 메모리 내 스토리지 모니터링
description: XTP 메모리 내 스토리지 사용, 용량을 예측 및 모니터링합니다. 41823 용량 오류를 해결합니다.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: how-to
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.reviewer: mathoma
ms.date: 01/25/2019
ms.openlocfilehash: c36b07e68081690b4fb326cd9520a418989b8411
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122529457"
---
# <a name="monitor-in-memory-oltp-storage-in-azure-sql-database-and-azure-sql-managed-instance"></a>Azure SQL Database 및 Azure SQL Managed Instance에서 메모리 내 OLTP 스토리지 모니터링
[!INCLUDE[appliesto-sqldb-sqlmi](includes/appliesto-sqldb-sqlmi.md)]

[메모리 내 OLTP](in-memory-oltp-overview.md)를 사용하는 경우 메모리 최적화 테이블 및 테이블 변수에 있는 데이터는 메모리 내 OLTP 스토리지에 상주합니다.

## <a name="determine-whether-data-fits-within-the-in-memory-oltp-storage-cap"></a>데이터가 메모리 내 OLTP 스토리지 용량에 맞는지 여부 결정

다른 서비스 계층의 스토리지 용량을 결정합니다. 각 프리미엄 및 중요 비즈니스용 서비스 계층에는 최대 메모리 내 OLTP 스토리지 크기가 포함됩니다.

- [DTU 기반 리소스 제한 - 단일 데이터베이스](database/resource-limits-dtu-single-databases.md)
- [DTU 기반 리소스 제한 - 탄력적 풀](database/resource-limits-dtu-elastic-pools.md)
- [vCore 기반 리소스 제한 - 단일 데이터베이스](database/resource-limits-vcore-single-databases.md)
- [vCore 기반 리소스 제한 - 탄력적 풀](database/resource-limits-vcore-elastic-pools.md)
- [vCore 기반 리소스 제한 - 관리되는 인스턴스](managed-instance/resource-limits.md)

메모리 최적화 테이블의 메모리 요구 사항 예측은 Azure SQL Database 및 Azure SQL Managed Instance에서와 동일한 방식으로 SQL Server에서 작동합니다. 잠시 시간을 내어 [예상 메모리 요구 사항](/sql/relational-databases/in-memory-oltp/estimate-memory-requirements-for-memory-optimized-tables)을 검토합니다.

테이블 및 테이블 변수 행뿐만 아니라 인덱스가 최대 사용자 데이터 크기를 계산합니다. 또한 ALTER TABLE은 전체 테이블 및 인덱스의 새 버전을 만들기 위해 충분한 공간이 필요합니다.

이 한도를 초과하면 삽입 및 업데이트 작업이 Azure SQL Database의 단일 데이터베이스 및 Azure SQL Managed Instance의 데이터베이스에 대한 오류 41823 및 Azure SQL Database의 탄력적 풀에 대한 오류 41840으로 실패할 수 있습니다. 해당 시점에서 데이터를 삭제하여 메모리를 회수하거나 데이터베이스의 성능 계층 또는 컴퓨팅 크기를 업그레이드해야 합니다.

## <a name="monitoring-and-alerting"></a>모니터링 및 경고

메모리 내 스토리지 사용량을 [Azure Portal](https://portal.azure.com/)에서 컴퓨팅 크기에 대한 스토리지 용량 비율로 모니터링할 수 있습니다.

1. 데이터베이스 블레이드에서 리소스 사용률 상자를 찾고 편집을 클릭합니다.
2. 메트릭 `In-Memory OLTP Storage percentage`을 참조하세요.
3. 경고를 추가하려면 리소스 사용률 상자 클릭하여 메트릭 블레이드를 연 다음 경고 추가를 클릭합니다.

또는 다음 쿼리를 사용하여 메모리 내 스토리지 사용률을 표시합니다.

```sql
    SELECT xtp_storage_percent FROM sys.dm_db_resource_stats
```

## <a name="correct-out-of-in-memory-oltp-storage-situations---errors-41823-and-41840"></a>메모리 내 OLTP 스토리지 부족 상황 수정 - 오류 41823 및 41840

데이터베이스에서 메모리 내 OLTP 스토리지 용량에 도달하면 단일 데이터베이스의 경우 오류 메시지 41823 또는 탄력적 풀의 경우 오류 41840과 함께 INSERT, UPDATE, ALTER 및 CREATE 작업이 실패할 수 있습니다. 두 오류 모두 활성 트랜잭션이 중단됩니다.

오류 메시지 41823 및 41840은 데이터베이스 또는 풀에서 메모리에 최적화된 테이블 및 테이블 변수가 최대 메모리 내 OLTP 스토리지 크기에 도달했음을 나타냅니다.

이 오류를 해결하려면

- 잠재적으로 데이터를 기존의 디스크 기반 테이블에 오프로드딩하여 메모리 최적화 테이블에서 데이터를 삭제합니다. 또는
- 메모리 최적화 테이블에서 유지하는 데 필요한 데이터에 대한 충분한 메모리 내 스토리지가 있는 서비스 계층을 업그레이드합니다.

> [!NOTE]
> 드문 경우지만 오류 41823 및 41840은 일시적일 수 있습니다. 즉, 메모리 내 OLTP 스토리지 용량이 충분하고 작업을 다시 시도하면 성공할 수 있습니다. 따라서 사용 가능한 전체 메모리 내 OLTP 스토리지를 모니터링하고 처음 41823 또는 41840 오류가 발생하는 경우에는 다시 시도하는 것이 좋습니다. 다시 시도 논리에 대한 자세한 내용은 [메모리 내 OLTP를 통해 충돌 검색 및 다시 시도 논리](/sql/relational-databases/In-memory-oltp/transactions-with-memory-optimized-tables#conflict-detection-and-retry-logic)를 참조하세요.

## <a name="next-steps"></a>다음 단계

모니터링 지침은 [동적 관리 뷰를 사용하여 모니터링](database/monitoring-with-dmvs.md)을 참조하세요.