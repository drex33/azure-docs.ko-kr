---
title: '빠른 시작: 워크로드 격리 구성 - T-SQL'
description: T-SQL을 사용하여 워크로드 격리를 구성합니다.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: sql-dw
ms.date: 04/27/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse, mode-other
ms.openlocfilehash: 987e8138900f1735992b5fc60c91072e588bcb87
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133076921"
---
# <a name="quickstart-configure-workload-isolation-in-a-dedicated-sql-pool-using-t-sql"></a>빠른 시작: T-SQL을 사용하여 전용 SQL 풀에서 워크로드 격리 구성

이 빠른 시작에서는 데이터 로드를 위해 리소스를 예약하기 위한 워크로드 및 분류자를 빠르게 만들어 봅니다. 워크로드 그룹은 시스템 리소스의 20%를 데이터 로드에 할당합니다.  워크로드 분류자는 데이터 로드 워크로드 그룹에 요청을 할당합니다.  데이터 로드에 대해 20% 격리를 사용하는 경우 리소스는 SLA를 준수할 수 있습니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

> [!NOTE]
> Azure Synapse Analytics에서 Synapse SQL 인스턴스를 만들면 새로운 청구 가능 서비스가 생성될 수 있습니다.  자세한 내용은 [Azure Synapse Analytics 가격 책정](https://azure.microsoft.com/pricing/details/sql-data-warehouse/)을 참조하세요.

## <a name="prerequisites"></a>필수 구성 요소

이 빠른 시작에서는 Azure Synapse에 Synapse SQL 인스턴스가 이미 있고 CONTROL DATABASE 권한이 있다고 가정합니다. 만들어야 하는 경우 [만들기 및 연결 - 포털](create-data-warehouse-portal.md)을 사용하여 **mySampleDataWarehouse** 라는 전용 SQL 풀을 만듭니다.

## <a name="create-login-for-dataloads"></a>DataLoads에 대한 로그인 만들기

'ELTLogin'에 [CREATE LOGIN](/sql/t-sql/statements/create-login-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)을 사용하여 `master` 데이터베이스에 SQL Server 인증 로그인을 만듭니다.

```sql
IF NOT EXISTS (SELECT * FROM sys.sql_logins WHERE name = 'ELTLogin')
BEGIN
CREATE LOGIN [ELTLogin] WITH PASSWORD='<strongpassword>'
END
;
```

## <a name="create-user"></a>사용자 만들기

mySampleDataWarehouse에 "ELTLogin"이라는 [사용자 만들기](/sql/t-sql/statements/create-user-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)

```sql
IF NOT EXISTS (SELECT * FROM sys.database_principals WHERE name = 'ELTLogin')
BEGIN
CREATE USER [ELTLogin] FOR LOGIN [ELTLogin]
END
;
```

## <a name="create-a-workload-group"></a>워크로드 그룹 만들기

20% 격리를 사용하는 DataLoads에 대해 [워크로드 그룹](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)을 만듭니다.

```sql
CREATE WORKLOAD GROUP DataLoads
WITH ( MIN_PERCENTAGE_RESOURCE = 20
      ,CAP_PERCENTAGE_RESOURCE = 100
      ,REQUEST_MIN_RESOURCE_GRANT_PERCENT = 5)
;
```

## <a name="create-a-workload-classifier"></a>워크로드 분류자 만들기

[워크로드 분류자](/sql/t-sql/statements/create-workload-classifier-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)를 만들어 ELTLogin을 DataLoads 워크로드 그룹에 매핑합니다.

```sql
CREATE WORKLOAD CLASSIFIER [wgcELTLogin]
WITH (WORKLOAD_GROUP = 'DataLoads'
      ,MEMBERNAME = 'ELTLogin')
;
```

## <a name="view-existing-workload-groups-and-classifiers-and-run-time-values"></a>기존 워크로드 그룹, 분류자 및 런타임 값 보기

```sql
--Workload groups
SELECT * FROM
sys.workload_management_workload_groups

--Workload classifiers
SELECT * FROM
sys.workload_management_workload_classifiers

--Run-time values
SELECT * FROM
sys.dm_workload_management_workload_groups_stats
```

## <a name="clean-up-resources"></a>리소스 정리

```sql
DROP WORKLOAD CLASSIFIER [wgcELTLogin]
DROP WORKLOAD GROUP [DataLoads]
DROP USER [ELTLogin]
;
```

전용 SQL 풀에 저장된 데이터 웨어하우스 단위 및 데이터에 대해 요금이 청구됩니다. 이러한 컴퓨팅 및 스토리지 리소스에 대한 요금이 별도로 청구됩니다.

- 데이터를 스토리지에 보관하려는 경우 전용 SQL 풀을 사용하지 않을 때 컴퓨팅을 일시 중지할 수 있습니다. 컴퓨팅을 일시 중지하면 데이터 스토리지 비용만 부과됩니다. 데이터로 작업할 준비가 되면 컴퓨팅을 다시 시작합니다.
- 앞으로 요금이 부과되지 않게 하려면 전용 SQL 풀을 삭제하면 됩니다.

## <a name="next-steps"></a>다음 단계

- 이제 워크로드 분류자를 만들었습니다. ELTLogin으로 몇 가지 쿼리를 실행하여 수행하는 방법을 확인합니다. 쿼리 및 할당된 워크로드 그룹을 보려면 [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql/?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)를 참조하세요.
- Synapse SQL 워크로드 관리에 대한 자세한 내용은 [워크로드 관리](sql-data-warehouse-workload-management.md)와 [워크로드 격리](sql-data-warehouse-workload-isolation.md)를 참조하세요.
