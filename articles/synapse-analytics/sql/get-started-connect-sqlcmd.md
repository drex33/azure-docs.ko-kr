---
title: sqlcmd를 사용하여 Synapse SQL에 연결
description: sqlcmd 명령줄 유틸리티를 사용하여 서버리스 SQL 풀 및 전용 SQL 풀에 연결하고 쿼리합니다.
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 04/15/2020
ms.author: stefanazaric
ms.reviewer: jrasnick
ms.openlocfilehash: 3972b82c3477e6ac75574ce9110ad90435bbf8a3
ms.sourcegitcommit: 5fabdc2ee2eb0bd5b588411f922ec58bc0d45962
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/23/2021
ms.locfileid: "112538884"
---
# <a name="connect-to-synapse-sql-with-sqlcmd"></a>sqlcmd를 사용하여 Synapse SQL에 연결

> [!div class="op_single_selector"]
> * [Azure Data Studio)](get-started-azure-data-studio.md)
> * [Power BI](get-started-power-bi-professional.md)
> * [Visual Studio](../sql/get-started-visual-studio.md)
> * [sqlcmd](../sql/get-started-connect-sqlcmd.md)
> * [SSMS](get-started-ssms.md)

[sqlcmd](/sql/tools/sqlcmd-utility?view=azure-sqldw-latest&preserve-view=true) 명령줄 유틸리티를 사용하여 Synapse SQL 내에서 서버리스 SQL 풀 및 전용 SQL 풀에 연결하고 쿼리할 수 있습니다.  

## <a name="1-connect"></a>1. 연결
[sqlcmd](/sql/tools/sqlcmd-utility?view=azure-sqldw-latest&preserve-view=true)를 시작하려면 명령 프롬프트를 열고 Synapse SQL 데이터베이스에 대한 연결 문자열 뒤에 **sqlcmd** 를 입력합니다. 연결 문자열에는 다음 매개 변수가 필요합니다.

* **서버(-S):** `<`서버 이름`>`.database.windows.net 형식의 서버
* **데이터베이스(-D):** 데이터베이스 이름
* **따옴표 붙은 식별자 사용(-I):** Synapse SQL 인스턴스에 연결하려면 따옴표 붙은 식별자를 사용하도록 설정해야 합니다.

SQL Server 인증을 사용하려면 사용자 이름 및 암호 매개 변수를 추가해야 합니다.

* **사용자(-U):** `<`사용자`>` 형태의 서버 사용자
* **암호(-P):** 사용자와 연결된 암호

연결 문자열은 다음 예제와 비슷합니다.

**서버리스 SQL 풀**

```sql
C:\>sqlcmd -S partyeunrt.database.windows.net -d demo -U Enter_Your_Username_Here -P Enter_Your_Password_Here -I
```

**전용 SQL 풀**

```
C:\>sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I
```

Azure Active Directory 통합 인증을 사용하려면 Azure Active Directory 매개 변수를 추가해야 합니다.

* **Azure Active Directory 인증(-G):** 인증을 위해 Azure Active Directory를 사용합니다.

연결 문자열은 다음 예제와 유사할 수 있습니다.

**서버리스 SQL 풀**

```
C:\>sqlcmd -S partyeunrt.database.windows.net -d demo -G -I
```

**전용 SQL 풀**

```sql
C:\>sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -G -I
```

> [!NOTE]
> Active Directory를 사용하여 인증하려면 [Azure Active Directory 인증을 사용하도록 설정](../sql/active-directory-authentication.md) 해야 합니다.

## <a name="2-query"></a>2. 쿼리

### <a name="use-dedicated-sql-pool"></a>전용 SQL 풀 사용

연결 후 인스턴스에 대해 지원되는 모든 [Transact-SQL](/sql/t-sql/language-reference?view=azure-sqldw-latest&preserve-view=true)(T-SQL) 문을 실행할 수 있습니다. 이 예제에서 쿼리는 대화형 모드로 전송됩니다.

```sql
C:\>sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I
1> SELECT name FROM sys.tables;
2> GO
3> QUIT
```

전용 SQL 풀의 경우 다음 예제에서는 -Q 옵션을 사용하거나 SQL을 sqlcmd로 파이핑하여 배치 모드에서 쿼리를 실행하는 방법을 보여줍니다.

```sql
sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I -Q "SELECT name FROM sys.tables;"
```

```sql
"SELECT name FROM sys.tables;" | sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I > .\tables.out
```

### <a name="use-serverless-sql-pool"></a>서버리스 SQL 풀 사용

연결 후 인스턴스에 대해 지원되는 [Transact-SQL](/sql/t-sql/language-reference?view=azure-sqldw-latest&preserve-view=true)(T-SQL) 문을 실행할 수 있습니다.  다음 예제에서 쿼리는 대화형 모드로 전송됩니다.

```sql
C:\>sqlcmd -S partyeunrt.database.windows.net -d demo -U Enter_Your_Username_Here -P Enter_Your_Password_Here -I
1> SELECT COUNT(*) FROM  OPENROWSET(BULK 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer/release/us_population_county/year=20*/*.parquet', FORMAT='PARQUET')
2> GO
3> QUIT
```

서버리스 SQL 풀의 경우 다음 예제에서는 -Q 옵션을 사용하거나 SQL을 sqlcmd로 파이핑하여 배치 모드에서 쿼리를 실행하는 방법을 보여줍니다.

```sql
sqlcmd -S partyeunrt.database.windows.net -d demo -U Enter_Your_Username_Here -P 'Enter_Your_Password_Here' -I -Q "SELECT COUNT(*) FROM  OPENROWSET(BULK 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer/release/us_population_county/year=20*/*.parquet', FORMAT='PARQUET')"
```

```sql
"SELECT COUNT(*) FROM  OPENROWSET(BULK 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer/release/us_population_county/year=20*/*.parquet', FORMAT='PARQUET')" | sqlcmd -S partyeunrt.database.windows.net -d demo -U Enter_Your_Username_Here -P 'Enter_Your_Password_Here' -I > ./tables.out
```

## <a name="next-steps"></a>다음 단계

sqlcmd 옵션에 대한 자세한 내용은 [sqlcmd 설명서](/sql/tools/sqlcmd-utility?view=azure-sqldw-latest&preserve-view=true)를 참조하세요.
