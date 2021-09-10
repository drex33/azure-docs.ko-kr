---
title: 'MySQL 온-프레미스에서 Azure Database for MySQL로 마이그레이션: 테스트 계획'
description: WWI는 일련의 IT 및 비즈니스 작업을 포함하는 테스트 계획을 만들었습니다. 성공적인 마이그레이션을 위해서는 모든 테스트를 실행해야 합니다.
ms.service: mysql
ms.subservice: migration-guide
ms.topic: how-to
author: arunkumarthiags
ms.author: arthiaga
ms.reviewer: maghan
ms.custom: ''
ms.date: 06/21/2021
ms.openlocfilehash: d13a6b63b297aff9504b00449f660f3ba55b6591
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/30/2021
ms.locfileid: "113084987"
---
# <a name="migrate-mysql-on-premises-to-azure-database-for-mysql-test-plans"></a>MySQL 온-프레미스에서 Azure Database for MySQL로 마이그레이션: 테스트 계획

[!INCLUDE[applies-to-mysql-single-flexible-server](../../includes/applies-to-mysql-single-flexible-server.md)]

## <a name="prerequisites"></a>사전 요구 사항

[마이그레이션 방법](05-migration-methods.md)

## <a name="overview"></a>개요

WWI는 일련의 IT 및 비즈니스 작업을 포함하는 테스트 계획을 만들었습니다. 성공적인 마이그레이션을 위해서는 모든 테스트를 실행해야 합니다.

테스트:

  - 마이그레이션된 데이터베이스가 온-프레미스 테이블과 일치하는지 확인합니다(레코드 수 및 쿼리 결과).

  - 성능이 적절한지 확인합니다. 즉, 온-프레미스에서 실행하는 것과 동일한 성능을 나타내야 합니다.

  - 대상 쿼리의 성능이 명시된 요구 사항을 충족하는지 확인합니다.

  - 온-프레미스 네트워크와 Azure 네트워크 간의 네트워크 연결이 적절한지 확인합니다.

  - 식별된 모든 애플리케이션 및 사용자가 마이그레이션된 데이터 인스턴스에 연결할 수 있는지 확인합니다.

WWI는 태평양 표준시로 오후 10시에 시작되고 오전 2시에 종료되는 마이그레이션 주말 및 기간을 확인했습니다. 마이그레이션이 모든 테스트를 통과한 상태로 오전 2시 목표(4시간 가동 시간 목표) 전에 완료되지 않은 경우 롤백 절차가 시작되었습니다. 이러한 이슈는 후속 마이그레이션 시도를 위해 문서화되었습니다. 모든 마이그레이션 기간은 적절한 비즈니스 타임라인에 따라 앞으로 당겨지고 다시 예약되었습니다.

## <a name="sample-queries"></a>샘플 쿼리

데이터베이스 마이그레이션 성공 여부를 확인하기 위해 원본 및 대상에 대해 일련의 쿼리를 실행했습니다. 다음 쿼리 및 스크립트는 마이그레이션 작업을 통해 필요한 모든 데이터베이스 개체가 원본에서 대상으로 이동되었는지를 확인하는 데 도움이 됩니다.

### <a name="table-rows"></a>테이블 행

이 쿼리를 사용하여 모든 테이블 및 예상 행 수를 알 수 있습니다.

```
SELECT SUM(TABLE_ROWS)
FROM INFORMATION_SCHEMA.TABLES
WHERE TABLE_SCHEMA = '{SchemaName}';
```

> [!NOTE]
> `INFORMATION_SCHEMA` 테이블은 테이블 전체의 예상되는 값 세트를 제공합니다. 테이블 크기와 같은 메트릭에 대한 보다 정확한 뷰를 얻으려면 `innodb_stats_transient_sample_pages` 서버 매개 변수를 사용하여 페이지 샘플 크기를 늘립니다. 이 서버 값을 늘리면 더 많은 페이지가 분석되므로 보다 정확한 결과를 얻을 수 있습니다.

모든 테이블에 대해 `count(*)` SQL 문을 실행하여 정확한 수의 행을 가져옵니다. 이 명령은 대용량 테이블에서 실행할 때 시간이 많이 걸릴 수 있습니다. 다음 스크립트는 정확한 개수를 얻기 위해 실행할 수 있는 SQL 문 집합을 생성합니다.

```
SELECT CONCAT( 
    'SELECT "', 
    table_name, 
    '" AS table_name, COUNT(*) AS exact_row_count FROM `', 
    table_schema, 
    '`.`', 
    table_name, 
    '` UNION ' 
)  
FROM INFORMATION_SCHEMA.TABLES 
WHERE table_schema = '**my_schema**';
```

### <a name="table-fragmentation"></a>테이블 조각화

애플리케이션을 계속 사용하게 되면서 데이터 테이블이 훨씬 더 커질 수 있습니다. 일부 경우에는 데이터가 증가하지 않을 수 있지만 삭제 및 업데이트를 통해 지속적으로 변경됩니다. 이러한 경우 데이터베이스 파일에 많은 조각화가 발생할 수 있습니다. MySQL OPTIMIZE TABLE 문은 실제 스토리지 공간 요구를 줄이고 I/O 효율성을 향상시킬 수 있습니다.

다음을 실행하여 [MySQL 테이블을 최적화](https://dev.mysql.com/doc/refman/8.0/en/optimize-table.html)할 수 있습니다.

`optimize table TABLE_NAME`

### <a name="database-objects"></a>데이터베이스 개체

다음 쿼리를 사용하여 다른 모든 데이터베이스 개체가 고려되는지 확인합니다. 이러한 쿼리는 테이블 행 수를 계산할 수 있지만 특정 데이터베이스 개체의 버전을 고려하지 않을 수 있습니다. 예를 들어, 저장 프로시저가 있어도 마이그레이션의 시작과 종료 중간에 변경될 수 있습니다. 마이그레이션 중에 데이터베이스 개체가 변경되지 않도록 고정하는 것이 좋습니다.

**사용자**

```sql
SELECT DISTINCT 
        USER 
FROM 
        mysql.user 
WHERE 
        user <> '' order by user
```

**인덱스**

```sql
SELECT DISTINCT 
        INDEX_NAME 
FROM 
        information_schema.STATISTICS 
WHERE 
        TABLE_SCHEMA = '{SchemaName}'
```

**제약 조건**

```sql
SELECT DISTINCT 
        CONSTRAINT_NAME 
FROM 
        information_schema.TABLE_CONSTRAINTS 
WHERE 
        CONSTRAINT_SCHEMA = '{SchemaName}'
```

**Views**

```sql
SELECT 
        TABLE_NAME 
FROM 
        information_schema.VIEWS 
WHERE 
        TABLE_SCHEMA = '{SchemaName}'
```

**저장 프로시저**

```sql
SELECT 
        ROUTINE_NAME 
FROM 
        information_schema.ROUTINES 
WHERE 
        ROUTINE_TYPE = 'FUNCTION' and 
        ROUTINE_SCHEMA = '{SchemaName}'
```

**함수**

```sql
SELECT 
        ROUTINE_NAME 
FROM 
        information_schema.ROUTINES 
WHERE 
        ROUTINE_TYPE = 'PROCEDURE' and 
        ROUTINE_SCHEMA = '{SchemaName}'
```

**이벤트**

```sql
SELECT 
        EVENT_NAME 
FROM 
        INFORMATION_SCHEMA.EVENTS 
WHERE 
        EVENT_SCHEMA = '{SchemaName}'
```

## <a name="rollback-strategies"></a>롤백 전략

위의 쿼리는 롤백 결정에 사용되는 개체 이름 및 개수 목록을 제공합니다. 마이그레이션 사용자는 원본 및 대상 개체 개수를 확인하여 첫 번째 개체 확인 단계를 수행할 수 있습니다. 개체 수가 달라진다고 해서 반드시 롤백이 필요한 것은 아닙니다. 심층 평가를 수행하면 이러한 차이가 경미하여 쉽게 복구할 수 있는지 알 수 있습니다. 일부 실패한 개체를 수동으로 마이그레이션하여 문제를 해결할 수도 있습니다. 예를 들어, 모든 테이블 및 데이터 행이 마이그레이션되었으며 일부 함수만 누락된 경우 실패한 개체를 수정하여 마이그레이션을 완료합니다. 데이터베이스가 비교적 작은 경우 Azure Database for MySQL 인스턴스를 지우고 마이그레이션을 다시 시작할 수 있습니다. 대량 데이터베이스의 경우 누락된 개체를 확인하기 위해 마이그레이션 기간 동안 사용할 수 있는 것보다 더 많은 시간이 필요할 수 있습니다. 마이그레이션을 중지하고 롤백해야 합니다.

마이그레이션 기간 중에 누락된 데이터베이스 개체를 신속하게 확인해야 합니다. 분마다 계산됩니다. 한 가지 옵션은 환경 개체 이름을 파일로 내보내고 데이터 비교 도구를 사용하여 누락된 개체를 신속하게 식별하는 것입니다. 또 다른 옵션은 원본 데이터베이스 개체 이름을 내보내고 대상 데이터베이스 환경 임시 테이블로 데이터를 가져오는 것입니다. **스크립팅된** SQL 문과 **테스트된** SQL 문을 사용하여 데이터를 비교합니다. 데이터 확인 속도와 정확도는 마이그레이션 프로세스에 매우 중요합니다. 마이그레이션 기간 동안 데이터베이스 개체의 긴 목록을 수동으로 읽고 확인하려고 하지 않도록 합니다. 사용자가 실수할 수 있는 가능성이 너무 높습니다. 도구를 사용하여 예외별로 관리하는 것이 가장 좋습니다.

## <a name="wwi-scenario"></a>WWI 시나리오

WWI CIO는 모든 데이터베이스 개체가 온-프레미스 데이터베이스에서 Azure Database for MySQL 인스턴스로 마이그레이션되었다는 확인 보고서를 받았습니다. 데이터베이스 팀은 마이그레이션을 시작하기 전에 데이터베이스에 대해 위의 쿼리를 실행하고 모든 결과를 스프레드시트에 저장했습니다.

원본 데이터베이스 스키마 정보는 대상 마이그레이션 개체 정확도를 확인하는 데 사용되었습니다.

## <a name="test-plans-checklist"></a>테스트 계획 검사 목록

  - 테스트 쿼리를 스크립팅 및 테스트했으며 실행할 준비가 되었습니다.

  - 테스트 쿼리가 실행되는 데 소요되는 시간을 확인하고 마이그레이션 타임라인의 일부로 함께 문서화합니다.

  - 잠재적 결과에 따라 완화 및 롤백 전략을 준비합니다.

  - 마이그레이션의 이벤트 타임라인이 잘 정의되어 있어야 합니다.  


## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [성능 기준](./07-performance-baselines.md)