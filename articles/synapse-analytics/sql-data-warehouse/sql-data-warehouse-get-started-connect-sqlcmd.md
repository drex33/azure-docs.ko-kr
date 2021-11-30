---
title: Sqlcmd를 사용하여 연결
description: Sqlcmd 명령줄 유틸리티를 사용하여 Azure Synapse Analytics에서 전용 SQL 풀에 연결하고 쿼리합니다.
services: synapse-analytics
author: joannapea
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/17/2018
ms.author: joanpo
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 449107980d700ebb8c36d1892a0a31c722b0d9d0
ms.sourcegitcommit: 991268c548dd47e5f7487cd025c7501b9315e477
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/30/2021
ms.locfileid: "133288469"
---
# <a name="connect-to-a-dedicated-sql-pool-in-azure-synapse-analytics-with-sqlcmd"></a>sqlcmd를 사용하여 Azure Synapse Analytics에서 전용 SQL 풀에 연결합니다

> [!div class="op_single_selector"]
>
> * [Power BI](/power-bi/connect-data/service-azure-sql-data-warehouse-with-direct-connect)
> * [Azure Machine Learning](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
> * [Visual Studio](sql-data-warehouse-query-visual-studio.md)
> * [sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md)
> * [SSMS](sql-data-warehouse-query-ssms.md)

sqlcmd 명령줄 유틸리티를 사용하여 전용 SQL 풀에 연결하고 쿼리합니다.  

## <a name="1-connect"></a>1. 연결

[sqlcmd][sqlcmd]를 시작하려면 명령 프롬프트를 열고 전용 SQL 풀에 대한 연결 문자열 뒤에 **sqlcmd** 를 입력합니다. 연결 문자열에는 다음 매개 변수가 필요합니다.

* **서버(-S):** `<`서버 이름`>`.database.windows.net 형식의 서버
* **데이터베이스 (-d):** 전용 SQL 풀 이름입니다.
* **따옴표 붙은 식별자 사용(-I):** 전용 SQL 풀 인스턴스에 연결하려면 따옴표 붙은 식별자를 사용하도록 설정해야 합니다.

SQL Server 인증을 사용하려면 사용자 이름/암호 매개 변수를 추가해야 합니다.

* **사용자(-U):** `<`사용자`>` 형태의 서버 사용자
* **암호(-P):** 사용자와 연결된 암호

예를 들어 연결 문자열은 다음과 같습니다.

```sql
C:\>sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I
```

Azure Active Directory 통합 인증을 사용하려면 Azure Active Directory 매개 변수를 추가해야 합니다.

* **Azure Active Directory 인증(-G):** 인증을 위해 Azure Active Directory를 사용합니다.

예를 들어 연결 문자열은 다음과 같습니다.

```sql
C:\>sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -G -I
```

> [!NOTE]
> Active Directory를 사용하여 인증하려면 [Azure Active Directory 인증을 사용하도록 설정](sql-data-warehouse-authentication.md) 해야 합니다.

## <a name="2-query"></a>2. 쿼리

연결 후, 인스턴스에 대해 지원되는 모든 TRANSACT-SQL 문을 실행할 수 있습니다.  이 예에서 쿼리는 대화형 모드로 전송됩니다.

```sql
C:\>sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I
1> SELECT name FROM sys.tables;
2> GO
3> QUIT
```

다음 예제에서는 -Q 옵션을 사용하거나 sqlcmd에 SQL을 파이핑하여 배치 모드에서 쿼리를 실행하는 방법을 보여 줍니다.

```sql
sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I -Q "SELECT name FROM sys.tables;"
```

```sql
"SELECT name FROM sys.tables;" | sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I > .\tables.out
```

## <a name="next-steps"></a>다음 단계

sqlcmd에서 사용할 수 있는 옵션에 대한 세부 정보는 [sqlcmd 설명서](/sql/tools/sqlcmd-utility?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)를 참조하세요.