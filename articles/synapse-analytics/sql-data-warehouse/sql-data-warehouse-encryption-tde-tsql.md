---
title: 투명한 데이터 암호화(T-SQL)
description: Azure Synapse Analytics(T-SQL)의 TDE(투명한 데이터 암호화)
services: synapse-analytics
author: nanditavalsan
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/30/2019
ms.author: nanditav
ms.reviewer: rortloff
ms.custom: seo-lt-2019
ms.openlocfilehash: 5f44d775006ed199d7c13b4131809b3aa16ddd4b
ms.sourcegitcommit: 991268c548dd47e5f7487cd025c7501b9315e477
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/30/2021
ms.locfileid: "133288452"
---
# <a name="get-started-with-transparent-data-encryption-tde"></a>투명한 데이터 암호화(TDE) 시작

> [!div class="op_single_selector"]
>
> * [보안 개요](sql-data-warehouse-overview-manage-security.md)
> * [인증](sql-data-warehouse-authentication.md)
> * [암호화(포털)](sql-data-warehouse-encryption-tde.md)
> * [암호화(T-SQL)](sql-data-warehouse-encryption-tde-tsql.md)

## <a name="required-permissions"></a>필요한 권한

TDE(투명한 데이터 암호화)를 사용하려면 관리자 또는 dbmanager 역할의 멤버여야 합니다.

## <a name="enabling-encryption"></a>암호화 설정

다음 단계에 따라 TDE를 사용하도록 설정합니다.

1. 마스터 데이터베이스에서 *dbmanager* 역할의 관리자 또는 멤버인 로그인을 사용하여 데이터베이스를 호스팅하는 서버의 **마스터** 데이터베이스에 연결
2. 다음 문을 실행하여 데이터베이스를 암호화합니다.

```sql
ALTER DATABASE [AdventureWorks] SET ENCRYPTION ON;
```

## <a name="disabling-encryption"></a>암호화 비활성화

다음 단계를 수행하여 TDE를 사용하지 않도록 설정합니다.

1. 마스터 데이터베이스에서 *dbmanager* 역할의 관리자 또는 멤버인 로그인을 사용하여 **마스터** 데이터베이스에 연결
2. 다음 문을 실행하여 데이터베이스를 암호화합니다.

```sql
ALTER DATABASE [AdventureWorks] SET ENCRYPTION OFF;
```

> [!NOTE]
> TDE 설정을 변경하기 전에 일시 중지된 전용 SQL 풀을 다시 시작해야 합니다.

## <a name="verifying-encryption"></a>암호화 확인

암호화 상태를 확인하려면 다음 단계를 수행합니다.

1. 마스터 데이터베이스에서 *dbmanager* 역할의 관리자 또는 멤버인 로그인을 사용하여 **마스터** 또는 인스턴스 데이터베이스에 연결
2. 다음 문을 실행하여 데이터베이스를 암호화합니다.

```sql
SELECT
    [name],
    [is_encrypted]
FROM
    sys.databases;
```

```1```의 결과는 암호화된 데이터베이스를 나타내고 ```0```은(는) 암호화되지 않은 데이터베이스를 나타냅니다.

## <a name="encryption-dmvs"></a>암호화 DMV

* [sys.databases](/sql/relational-databases/system-catalog-views/sys-databases-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
* [sys.dm_pdw_nodes_database_encryption_keys](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-nodes-database-encryption-keys-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
