---
title: 애플리케이션 성능을 개선하기 위해 일괄 처리를 사용하는 방법
description: 이 토픽에서는 데이터베이스 작업을 일괄 처리하면 Azure SQL Database 및 Azure SQL Managed Instance 애플리케이션의 속도와 스케일링 성능이 대폭 향상된다는 증거를 제공합니다. 해당 일괄 처리 기법은 SQL 데이터베이스에 적용되지만, 이 문서는 Azure에 중점을 두었습니다.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: how-to
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.reviewer: mathoma
ms.date: 06/22/2021
ms.openlocfilehash: dc246908497d8f13bddbc06498900842850397ae
ms.sourcegitcommit: 05dd6452632e00645ec0716a5943c7ac6c9bec7c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/17/2021
ms.locfileid: "122530414"
---
# <a name="how-to-use-batching-to-improve-azure-sql-database-and-azure-sql-managed-instance-application-performance"></a>일괄 처리를 사용하여 Azure SQL Database 및 Azure SQL Managed Instance 애플리케이션 성능을 개선하는 방법
[!INCLUDE[appliesto-sqldb-sqlmi](includes/appliesto-sqldb-sqlmi.md)]

Azure SQL Database 및 Azure SQL Managed Instance에 관해 일괄 처리 작업을 수행하면 애플리케이션의 성능 및 스케일링 성능이 상당히 향상됩니다. 장점을 이해할 수 있도록, 이 문서의 첫 번째 부분에서는 Azure SQL Managed Instance 또는 Azure SQL Database의 데이터베이스에 대한 순차적인 요청과 일괄 처리된 요청을 비교하는 샘플 테스트 결과를 설명합니다. 문서의 나머지 부분은 Azure 애플리케이션에서 일괄 처리를 성공적으로 사용하는데 도움이 되는 기법, 시나리오, 고려 사항을 보여줍니다.

## <a name="why-is-batching-important-for-azure-sql-database-and-azure-sql-managed-instance"></a>Azure SQL Database 및 Azure SQL Managed Instance에 일괄 처리가 중요한 이유는 무엇인가요?

원격 서비스에 대한 호출 일괄 처리는 성능 및 확장성 향상을 위해 잘 알려진 전략입니다. 직렬화, 네트워크 전송, 역직렬화 같은 원격 서비스와의 모든 트랜잭션에는 고정 처리 비용이 있습니다. 다수의 분리된 트랜잭션을 하나의 배치로 패키징하면 이러한 비용이 최소화됩니다.

이 문서에서는 몇 가지 일괄 처리 전략 및 시나리오를 살펴보고자 합니다. 해당 전략은 SQL Server를 사용하는 온-프레미스 애플리케이션에도 중요하지만, Azure SQL Database 및 Azure SQL Managed Instance에 관한 일괄 처리 사용을 강조하는 이유가 몇 가지 있습니다.

* 같은 Microsoft Azure 데이터 센터 외부에서 Azure SQL Database 또는 Azure SQL Managed Instance에 액세스하는 경우 특히, Azure SQL Database 및 Azure SQL Managed Instance에 액세스할 때 네트워크 대기 시간이 더 길어질 수 있습니다.
* Azure SQL Database 및 Azure SQL Managed Instance의 다중 테넌트 특징은 데이터 액세스 계층의 효율이 데이터베이스의 전반적인 스케일링 성능과 상관 관계가 있다는 것을 의미합니다. 미리 정의된 할당량을 초과하는 사용량에 대해 Azure SQL Database 및 Azure SQL Managed Instance는 처리량을 낮추거나 제한 예외로 응답할 수 있습니다. 일괄 처리와 같은 효율성을 통해 해당 한도에 도달하기 전에 더 많은 작업을 수행할 수 있습니다.
* 일괄 처리는 다수의 데이터베이스(분할)를 사용하는 아키텍처에 대해서도 효과적입니다. 각 데이터베이스 단위와의 상호 작용 효율은 전반적인 확장성에 있어 여전히 주요 요인입니다.

Azure SQL Database 및 Azure SQL Managed Instance를 사용하는 장점 중 하나는 데이터베이스를 호스트하는 서버를 관리하지 않아도 된다는 것입니다. 하지만 관리되는 인프라는 사용자가 데이터베이스 최적화에 대해 달리 생각해봐야 한다는 것을 의미하기도 합니다. 더 이상은 데이터베이스 하드웨어 또는 네트워크 인프라 개선에만 기대를 걸 수 없습니다. Microsoft Azure는 이러한 환경을 제어합니다. 사용자가 제어할 수 있는 주요 영역은 애플리케이션이 Azure SQL Database 및 Azure SQL Managed Instance와 상호 작용하는 방식입니다. 일괄 처리는 이러한 최적화 중 하나입니다.

이 문서의 첫 번째 부분에서는 Azure SQL Database 또는 Azure SQL Managed Instance를 사용하는 .NET 애플리케이션의 다양한 일괄 처리 기술을 살펴봅니다. 마지막 두 세션은 일괄 처리 지침 및 시나리오를 포함합니다.

## <a name="batching-strategies"></a>일괄 처리 전략

### <a name="note-about-timing-results-in-this-article"></a>이 문서의 타이밍 결과에 대한 정보

> [!NOTE]
> 결과가 기준은 아니며 **상대적인 성능** 을 표시하기 위한 것입니다. 타이밍은 평균적으로 최소 10회의 테스트 실행을 기반으로 합니다. 작업은 빈 테이블로의 삽입니다. 이러한 테스트는 V12 이전 버전에서 측정되었으며, 새로운 [DTU 서비스 계층](database/service-tiers-dtu.md) 또는 [vCore 서비스 계층](database/service-tiers-vcore.md)을 사용하는 V12 데이터베이스에서 경험하는 처리량과 일치하지 않을 수 있습니다. 일괄 처리 기법의 상대적인 장점은 유사합니다.

### <a name="transactions"></a>트랜잭션

일괄 작업에 대한 검토를 트랜잭션에 대한 얘기로 시작하는 것이 생소해 보일 수 있습니다. 하지만 클라이언트 쪽 트랜잭션 사용은 서버 쪽 일괄 처리에 성능을 향상시키는 미묘한 영향을 미칩니다. 트랜잭션은 단지 몇 줄의 코드만으로 추가될 수 있으며, 순차적인 작업의 성능을 향상시키는 빠른 방법을 제공합니다.

다음 C# 코드는 간단한 테이블에 삽입 및 업데이트 작업 시퀀스를 포함합니다.

```csharp
List<string> dbOperations = new List<string>();
dbOperations.Add("update MyTable set mytext = 'updated text' where id = 1");
dbOperations.Add("update MyTable set mytext = 'updated text' where id = 2");
dbOperations.Add("update MyTable set mytext = 'updated text' where id = 3");
dbOperations.Add("insert MyTable values ('new value',1)");
dbOperations.Add("insert MyTable values ('new value',2)");
dbOperations.Add("insert MyTable values ('new value',3)");
```

다음 ADO.NET 코드는 이러한 작업을 순차적으로 수행합니다.

```csharp
using (SqlConnection connection = new SqlConnection(CloudConfigurationManager.GetSetting("Sql.ConnectionString")))
{
    conn.Open();

    foreach(string commandString in dbOperations)
    {
        SqlCommand cmd = new SqlCommand(commandString, conn);
        cmd.ExecuteNonQuery();
    }
}
```

이 코드를 최적화하는 최고의 방법은 이러한 호출의 클라이언트 쪽 일괄 처리 형식을 구현하는 것입니다. 하지만 호출 시퀀스를 하나의 트랜잭션에 래핑하는 것만으로 이 코드의 성능을 높이는 간단한 방법이 있습니다. 다음은 트랜잭션을 사용하는 동일한 코드입니다.

```csharp
using (SqlConnection connection = new SqlConnection(CloudConfigurationManager.GetSetting("Sql.ConnectionString")))
{
    conn.Open();
    SqlTransaction transaction = conn.BeginTransaction();

    foreach (string commandString in dbOperations)
    {
        SqlCommand cmd = new SqlCommand(commandString, conn, transaction);
        cmd.ExecuteNonQuery();
    }

    transaction.Commit();
}
```

트랜잭션이 양쪽 예제에 실제로 사용되고 있습니다. 첫 번째 예제에서 각각의 개별 호출은 암시적 트랜잭션입니다. 두 번째 예제에서 명시적 트랜잭션이 모든 호출을 래핑합니다. [미리 쓰기 트랜잭션 로그](/sql/relational-databases/sql-server-transaction-log-architecture-and-management-guide?view=sql-server-ver15&preserve-view=true#WAL) 설명에 따라 로그 기록은 트랜잭션이 커밋될 때 디스크에 플러시됩니다. 따라서 트랜잭션에 더 많은 호출을 포함시켜서, 트랜잭션 로그에 대한 쓰기를 트랜잭션이 커밋될 때까지 지연시킬 수 있습니다. 사실상, 서버의 트랜잭션 로그에 쓰기에 대해 일괄 처리를 사용하는 것입니다.

다음 테이블은 임시 테스팅 결과를 보여 줍니다. 테스트는 동일한 순차적 삽입을 트랜잭션을 포함한 상태와 그렇지 않은 상태로 수행하였습니다. 보다 다양한 견해를 위해, 첫 번째 테스트는 랩톱에서 Microsoft Azure의 데이터베이스에 대해 원격으로 실행했습니다. 두 번째 테스트는 동일한 Microsoft Azure 데이터 센터(미국 서부) 내에 상주하는 클라우드 서비스 및 데이터베이스에서 실행했습니다. 다음 테이블은 트랜잭션 유 무 상태에서 순차적인 삽입의 소요 시간(밀리초)를 보여줍니다.

**온-프레미스에서 Azure**:

| 작업 | 트랜잭션 없음(밀리초) | 트랜잭션(밀리초) |
| --- | --- | --- |
| 1 |130 |402 |
| 10 |1208 |1226 |
| 100 |12662 |10395 |
| 1000 |128852 |102917 |

**Azure에서Azure(동일한 데이터 센터)**:

| 작업 | 트랜잭션 없음(밀리초) | 트랜잭션(밀리초) |
| --- | --- | --- |
| 1 |21 |26 |
| 10 |220 |56 |
| 100 |2145 |341 |
| 1000 |21479 |2756 |

> [!NOTE]
> 결과가 기준은 아닙니다. [이 문서의 타이밍 결과에 대한 정보](#note-about-timing-results-in-this-article)를 참조하세요.

이전 테스트 결과에 따르면, 단일 작업을 트랜잭션에 래핑하면 성능이 실제로 감소합니다. 하지만 단일 트랜잭션에 포함하는 작업의 수를 증가시키면, 성능 향상이 더 두드러집니다. 모든 작업이 Microsoft Azure 데이터 센터 내에서 발생하는 경우에는 성능 차이가 더 현저하게 나타납니다. Microsoft Azure 데이터 센터 외부에서 Azure SQL Database 및 Azure SQL Managed Instance를 사용하여 증가하는 대기 시간은 트랜잭션 사용으로 인한 성능 향상을 무색하게 만듭니다.

트랜잭션 사용을 통해 성능을 향상시킬 수 있지만 계속해서 [트랜잭션 및 연결에 대한 모범 사례](/previous-versions/sql/sql-server-2008-r2/ms187484(v=sql.105))를 살펴보겠습니다. 트랜잭션을 최대한 짧게 유지하고 작업이 완료된 후에는 데이터베이스 연결을 닫습니다. 이전 예제의 using 문은 후속 코드 블록이 완료되면 연결이 닫히도록 합니다.

이전 예제는 로컬 트랜잭션을 모든 ADO.NET 코드에 두 줄로 추가할 수 있다는 것을 보여줍니다. 트랜잭션은 순차적인 삽입, 업데이트, 삭제 작업을 생성하는 코드의 성능을 향상시키는 신속한 방법을 제공합니다. 하지만 가장 빠른 성능의 경우에는, 클라이언트 쪽 일괄 처리의 장점(예: 테이블 반환 매개 변수)을 활용하기 위한 코드 변경을 고려합니다.

ADO.NET의 트랜잭션에 대한 자세한 내용은 [ADO.NET의 로컬 트랜잭션](/dotnet/framework/data/adonet/local-transactions)을 참조하세요.

### <a name="table-valued-parameters"></a>테이블 반환 매개 변수

테이블 반환 매개 변수는 Transact-SQL 문, 저장 프로시저, 함수의 매개 변수로 사용자 정의 테이블 형식을 지원합니다. 클라이언트 쪽 일괄 처리 기법을 사용하면 여러 행의 데이터를 테이블 반환 변수 내에서 전송할 수 있습니다. 테이블 반환 매개 변수를 사용하려면 우선 테이블 형식을 정의합니다. 다음 Transact-SQL 문은 **MyTableType** 이라는 이름의 테이블 형식을 만듭니다.

```sql
    CREATE TYPE MyTableType AS TABLE
    ( mytext TEXT,
      num INT );
```

코드에서 테이블 형식과 이름과 형식이 정확이 같은 **DataTable** 을 만듭니다. **DataTable** 을 저장 프로시저 호출 또는 텍스트 쿼리의 매개 변수로 전달합니다. 다음 예제는 이러한 기법을 보여줍니다.

```csharp
using (SqlConnection connection = new SqlConnection(CloudConfigurationManager.GetSetting("Sql.ConnectionString")))
{
    connection.Open();

    DataTable table = new DataTable();
    // Add columns and rows. The following is a simple example.
    table.Columns.Add("mytext", typeof(string));
    table.Columns.Add("num", typeof(int));
    for (var i = 0; i < 10; i++)
    {
        table.Rows.Add(DateTime.Now.ToString(), DateTime.Now.Millisecond);
    }

    SqlCommand cmd = new SqlCommand(
        "INSERT INTO MyTable(mytext, num) SELECT mytext, num FROM @TestTvp",
        connection);

    cmd.Parameters.Add(
        new SqlParameter()
        {
            ParameterName = "@TestTvp",
            SqlDbType = SqlDbType.Structured,
            TypeName = "MyTableType",
            Value = table,
        });

    cmd.ExecuteNonQuery();
}
```

이전 예제에서 **SqlCommand** 개체는 테이블 반환 매개 변수 **\@TestTvp** 의 행을 삽입합니다. 이전에 만든 **DataTable** 개체는 **SqlCommand.Parameters.Add** 메서드로 이 매개 변수에 할당됩니다. 삽입을 하나의 호출로 일괄 처리하면 순차적인 삽입의 성능을 상당히 향상시킵니다.

이전 예제를 더욱 향상시키려면 텍스트 기반 명령 대신 저장 프로시저를 사용합니다. 다음 Transact-SQL 명령은 **SimpleTestTableType** 테이블 반환 매개 변수를 받아들이는 저장 프로시저를 만듭니다.

```sql
CREATE PROCEDURE [dbo].[sp_InsertRows]
@TestTvp as MyTableType READONLY
AS
BEGIN
INSERT INTO MyTable(mytext, num)
SELECT mytext, num FROM @TestTvp
END
GO
```

그 후 이전 코드 예제의 **SqlCommand** 개체 선언을 다음과 같이 변경합니다.

```csharp
SqlCommand cmd = new SqlCommand("sp_InsertRows", connection);
cmd.CommandType = CommandType.StoredProcedure;
```

대부분의 경우 테이블 반환 매개 변수는 다른 일괄 처리 기법과 동등하거나 그 보다 뛰어난 성능을 갖습니다. 테이블 반환 매개 변수는 다른 옵션에 비해 융통성이 많기 때문에 더 좋을 수 있습니다. 예를 들어 SQL 대량 복사와 같은 다른 기법은 새 행의 삽입만을 허용합니다. 하지만 테이블 반환 매개 변수를 사용하면 저장 프로시저의 논리를 사용하여 업데이트되는 행과 삽입되는 행을 결정할 수 있습니다. 지정된 행이 삽입될지, 업데이트될지 또는 삭제될지를 나타내는 “작업” 열을 포함하도록 테이블 형식이 수정될 수도 있습니다.

다음 테이블은 테이블 반환 매개 변수 사용에 대한 임시 테스트 결과를 밀리초 단위로 보여 줍니다.

| 작업 | 온-프레미스에서 Azure(밀리초) | Azure 동일한 데이터 센터(밀리초) |
| --- | --- | --- |
| 1 |124 |32 |
| 10 |131 |25 |
| 100 |338 |51 |
| 1000 |2615 |382 |
| 10000 |23830 |3586 |

> [!NOTE]
> 결과가 기준은 아닙니다. [이 문서의 타이밍 결과에 대한 정보](#note-about-timing-results-in-this-article)를 참조하세요.

일괄 처리를 통한 성능 향상은 바로 식별이 가능합니다. 이전의 순차 테스트에서 1000개 작업이 데이터센터 외부에서는 129초가 소요되었고 데이터센터 내부에서는 21초가 소요되었습니다. 하지만 테이블 반환 변수를 사용하면 1000개 작업이 데이터센터 외부에서는 2.6초, 데이터센터 내부에서는 0.4초밖에 걸리지 않습니다.

테이블 반환 매개 변수에 대한 자세한 내용은 [테이블 반환 매개 변수](/sql/relational-databases/tables/use-table-valued-parameters-database-engine)를 참조하세요.

### <a name="sql-bulk-copy"></a>SQL 대량 복사

SQL 대량 복사는 대량의 데이터를 대상 데이터베이스에 삽입하는 또 다른 방법입니다. NET 애플리케이션은 **SqlBulkCopy** 클래스를 사용하여 대량 삽입 작업을 수행할 수 있습니다. **SqlBulkCopy** 는 명령줄 도구 **Bcp.exe** 또는 Transact-SQL 문 **BULK INSERT** 와 기능면에서 유사합니다. 다음 코드 예제는 원본 **DataTable** 테이블의 행을 MyTable이라는 대상 테이블로 대량 복사하는 방법을 보여 줍니다.

```csharp
using (SqlConnection connection = new SqlConnection(CloudConfigurationManager.GetSetting("Sql.ConnectionString")))
{
    connection.Open();

    using (SqlBulkCopy bulkCopy = new SqlBulkCopy(connection))
    {
        bulkCopy.DestinationTableName = "MyTable";
        bulkCopy.ColumnMappings.Add("mytext", "mytext");
        bulkCopy.ColumnMappings.Add("num", "num");
        bulkCopy.WriteToServer(table);
    }
}
```

테이블 반환 매개 변수보다 대량 복사를 선호하는 경우도 있습니다. [테이블 반환 매개 변수](/sql/relational-databases/tables/use-table-valued-parameters-database-engine) 문서에서 테이블 반환 매개 변수와 BULK INSERT 작업의 비교 테이블을 참고하세요.

다음 임시 테스트 결과는 **SqlBulkCopy** 를 통한 일괄 처리 성능을 밀리초 단위로 보여 줍니다.

| 작업 | 온-프레미스에서 Azure(밀리초) | Azure 동일한 데이터 센터(밀리초) |
| --- | --- | --- |
| 1 |433 |57 |
| 10 |441 |32 |
| 100 |636 |53 |
| 1000 |2535 |341 |
| 10000 |21605 |2737 |

> [!NOTE]
> 결과가 기준은 아닙니다. [이 문서의 타이밍 결과에 대한 정보](#note-about-timing-results-in-this-article)를 참조하세요.

소규모 배치에서는, 테이블 반환 매개 변수가 **SqlBulkCopy** 클래스보다 성능이 뛰어납니다. 하지만 1,000개 및 10,000개 행에 대한 테스트의 경우 **SqlBulkCopy** 가 테이블 반환 매개 변수보다 12-31% 더 빠르게 수행됩니다. 테이블 반환 매개 변수처럼 **SqlBulkCopy** 역시 일괄 처리된 삽입의 좋은 옵션이며, 비일괄 처리 작업의 성능과 비교하면 특히 그렇습니다.

ADO.NET에서 대량 복사에 대한 자세한 내용은 [대량 복사 작업](/dotnet/framework/data/adonet/sql/bulk-copy-operations-in-sql-server)을 참조하세요.

### <a name="multiple-row-parameterized-insert-statements"></a>여러 행의 매개 변수가 있는 INSERT 문

소규모 배치에 대한 한 가지 대안은 여러 행을 삽입하는 매개 변수가 있는 대량 INSERT 문을 생성하는 것입니다. 다음 코드 예제는 이러한 기법을 보여줍니다.

```csharp
using (SqlConnection connection = new SqlConnection(CloudConfigurationManager.GetSetting("Sql.ConnectionString")))
{
    connection.Open();

    string insertCommand = "INSERT INTO [MyTable] ( mytext, num ) " +
        "VALUES (@p1, @p2), (@p3, @p4), (@p5, @p6), (@p7, @p8), (@p9, @p10)";

    SqlCommand cmd = new SqlCommand(insertCommand, connection);

    for (int i = 1; i <= 10; i += 2)
    {
        cmd.Parameters.Add(new SqlParameter("@p" + i.ToString(), "test"));
        cmd.Parameters.Add(new SqlParameter("@p" + (i+1).ToString(), i));
    }

    cmd.ExecuteNonQuery();
}
```

이 예제는 기본적인 개념을 보여주기 위한 것입니다. 보다 현실적인 시나리오는 필요한 엔터티를 이어서 쿼리 문자열과 명령 매개 변수를 동시에 구성합니다. 쿼리 매개 변수는 총 2100개로 제한되기 때문에, 이러한 방식으로 처리되는 행의 총 수가 제한됩니다.

다음 임시 테스트 결과는 이런 형식으로 된 Insert 문의 성능을 밀리초 단위로 보여 줍니다.

| 작업 | 테이블 반환 매개 변수(밀리초) | 단일 문 INSERT(밀리초) |
| --- | --- | --- |
| 1 |32 |20 |
| 10 |30 |25 |
| 100 |33 |51 |

> [!NOTE]
> 결과가 기준은 아닙니다. [이 문서의 타이밍 결과에 대한 정보](#note-about-timing-results-in-this-article)를 참조하세요.

이 방법은 행이 100개 미만인 배치에 대해 약간 더 빠를 수 있습니다. 이 기법은 향상 폭은 작지만 사용자의 특정 애플리케이션 시나리오에서 잘 작동할만한 또 다른 옵션입니다.

### <a name="dataadapter"></a>DataAdapter

**DataAdapter** 클래스를 사용하면 **DataSet** 개체를 수정한 후 INSERT, UPDATE, DELETE 작업으로 제출할 수 있습니다. **DataAdapter** 를 이런 방식으로 사용하는 경우, 각각의 고유한 작업에 대해 개별 호출이 생성된다는 점에 유의해야 합니다. 성능을 향상시키려면 한 번에 일괄 처리되어야 하는 작업의 수에 대해 **UpdateBatchSize** 속성을 사용합니다. 자세한 내용은 [DataAdapter를 사용하여 일괄 처리 작업 수행](/dotnet/framework/data/adonet/performing-batch-operations-using-dataadapters)을 참조하세요.

### <a name="entity-framework"></a>Entity Framework

[Entity Framework Core](/ef/efcore-and-ef6/#saving-data)에서 일괄 처리를 지원합니다.

### <a name="xml"></a>XML

완벽함을 기하기 위해 일괄 처리 전략의 하나로 XML에 대해 얘기하는 것이 중요하다고 생각합니다. 하지만 XML 사용이 다른 메서드에 비해 이점이 없고 몇 가지 불편한 점이 있습니다. 접근 방법은 테이블 반환 매개 변수와 유사하지만 사용자 정의된 테이블 대신 XML 파일 또는 문자열이 저장 프로시저로 전달됩니다. 저장 프로시저는 저장 프로시저로 명령을 구문 분석합니다.

이러한 접근 방법에는 몇 가지 불편한 점이 있습니다.

* XML 작업은 번거롭고 오류 가능성이 높습니다.
* 데이터베이스에서 XML 구문 분석은 CPU를 많이 사용할 수 있습니다.
* 대부분의 경우 이 방법은 테이블 반환 매개 변수보다 느립니다.

이런 이유로 인해서 배치 쿼리에 XML을 사용하지 않는 것이 좋습니다.

## <a name="batching-considerations"></a>일괄 처리 고려 사항

다음 섹션은 Azure SQL Database 및 Azure SQL Managed Instance 애플리케이션에서 일괄 처리를 사용하는 것에 대해 더 많은 참고 자료를 제공합니다.

### <a name="tradeoffs"></a>균형 유지

아키텍처에 따라서 일괄 처리는 성능과 복원력 사이에서 균형을 유지해야 하는 경우가 있습니다. 사용자의 역할이 예상치 않게 중단되는 시나리오를 예로 들어보겠습니다. 데이터 행을 하나만 손실하는 것이, 제출하지 않은 일괄 처리 행을 대량으로 손실하는 것보다 충격이 적습니다. 행을 데이터베이스에 보내기 전에 일정 시간 동안 버퍼에 보류하면 큰 위험이 따릅니다.

이렇게 균형 유지가 필요하기 때문에 일괄 처리하는 작업의 유형을 평가해야 합니다. 덜 중요한 데이터는 보다 적극적으로(배치 규모는 더 크게 기간은 더 길게) 일괄 처리합니다.

### <a name="batch-size"></a>Batch 크기

테스트에 따르면 대량의 배치를 작은 청크로 나누는 장점은 대체적으로 거의 없었습니다. 실제로 이러한 세분화가 큰 배치 하나를 제출하는 것보다 성능을 느리게 하는 결과를 초래하기도 했습니다. 예를 들어, 행 1000개를 삽입하는 시나리오를 생각해 보겠습니다. 다음 테이블은 테이블 반환 매개 변수를 사용하여 행 1000개를 소규모 배치로 나누어  삽입하는데 소요되는 시간을 보여줍니다.

| Batch 크기 | 반복 횟수 | 테이블 반환 매개 변수(밀리초) |
| --- | --- | --- |
| 1000 |1 |347 |
| 500 |2 |355 |
| 100 |10 |465 |
| 50 |20 |630 |

> [!NOTE]
> 결과가 기준은 아닙니다. [이 문서의 타이밍 결과에 대한 정보](#note-about-timing-results-in-this-article)를 참조하세요.

1000개 행에 대한 최고의 성능은 모두를 한꺼번에 제출하는 것이라는 사실을 볼 수 있습니다. 다른 테스트(여기에 표시되지 않은)에서는 10,000개 행의 배치 하나를 5,000개 행의 배치 2개로 나눈 경우에 약간의 성능 향상이 있었습니다. 하지만 이 테스트에 대한 테이블 스키마가 상대적으로 간단하기 때문에, 이러한 결론을 검증하기 위해서는 사용자의 데이터 및 배치 크기에 대한 테스트를 수행해야 합니다.

또 다른 요인 고려 사항은 전체 배치가 너무 커지면 Azure SQL Database 및 Azure SQL Managed Instance가 흐름을 제한하고 배치 커밋을 거부할 수 있다는 점입니다. 최고의 결과를 위해서는 사용자의 특정한 시나리오를 테스트하여 이상적인 배치 규모가 있는가를 판단합니다. 런타임에 배치 규모를 구성할 수 있도록 하여 성능 또는 오류를 기반으로 신속한 조정이 가능하도록 합니다.

마지막으로 배치의 규모를 일괄 처리와 관련된 위험과 비교 평가합니다. 일시적인 오류 또는 역할 실패가 발생하는 경우에는 작업을 재시도하거나 배치의 데이터가 손실되어 발생하는 결과를 고려합니다.

### <a name="parallel-processing"></a>병렬 처리

배치의 규모는 줄이면서 다수의 스레드를 사용하여 작업을 실행하는 방법을 취하면 어떨까요? 앞서 언급했지만, 테스트에 따르면 여러 개의 소형 다중 스레드 배치는 일반적으로 하나의 대형 배치보다 성능이 낮았습니다. 다음 테스트는 1000개의 행을 하나 이상의 병렬 배치에 삽입하려고 합니다. 이 테스트는 동시에 실행되는 배치가 많아질수록 실제로 성능이 어떻게 감소되는가를 보여줍니다.

| Batch 크기[반복 횟수] | 스레드 2개(밀리초) | 스레드 4개(밀리초) | 스레드 6개(밀리초) |
| --- | --- | --- | --- |
| 1000 [1] |277 |315 |266 |
| 500 [2] |548 |278 |256 |
| 250 [4] |405 |329 |265 |
| 100 [10] |488 |439 |391 |

> [!NOTE]
> 결과가 기준은 아닙니다. [이 문서의 타이밍 결과에 대한 정보](#note-about-timing-results-in-this-article)를 참조하세요.

병렬 처리로 인한 성능의 저하에는 몇 가지 잠재적인 이유가 있습니다.

* 동시에 실행되는 네트워크 호출이 하나가 아니라 여러 개입니다.
* 단일 테이블에 대해 여러 개의 작업이 수행되면 경합과 차단이 발생할 수 있습니다.
* 멀티 스레드와 관련된 오버헤드가 있습니다.
* 다수의 연결을 여는 비용이 병렬 처리의 이점을 능가합니다.

다른 테이블이나 데이터베이스를 대상으로 하는 경우에는 이러한 전략을 통해 얼마간 성능 향상을 볼 수도 있습니다. 데이터베이스 분할 또는 페더레이션은 이런 방법에 대한 시나리오가 될 수 있습니다. 분할은 여러 개의 데이터베이스를 사용하며 각각의 데이터베이스에 다른 데이터를 보냅니다. 각각의 소형 배치가 다른 데이터베이스로 가는 경우에는 병렬로 작업을 수행하는 것이 더 효율적일 수 있습니다. 하지만 솔루션에 데이터베이스 분할을 사용하자는 결정을 내리는 근거로 사용할 정도로 성능 향상이 현저하지는 않습니다.

일부 디자인의 경우, 소형 배치의 병렬 실행으로 인해 부하가 걸린 시스템 내에서 요청 처리량이 향상되기도 합니다. 이런 경우, 하나의 대형 배치를 처리하는 것이 더 빠르더라도 다수의 배치를 병렬로 처리하는 것이 더 효율적일 수 있습니다.

병렬 실행을 사용하는 경우에는 최대 작업자 스레드 수에 대한 제어를 고려합니다. 숫자가 작을수록 경합이 줄어들고 실행 시간은 빨라집니다. 또한 이를 통해 연결 및 트랜잭션이 대상 데이터베이스에 부과하는 추가적인 부하를 고려합니다.

### <a name="related-performance-factors"></a>관련된 성능 요인

데이터 베이스 성능에 대한 전형적인 지침은 일괄 처리에도 영향을 미칩니다. 예를 들어, 기본 키가 크거나 비클러스터형 인덱스가 많은 테이블에 대한 삽입 성능은 감소됩니다.

테이블 반환 매개 변수가 저장 프로시저를 사용하는 경우에는 프로시저의 시작에 **SET NOCOUNT ON** 명령을 사용할 수 있습니다. 이 명령문은 프로시저에서 영향을 받은 행의 수에 대한 반환을 억제합니다. 하지만 테스트에서는 **SET NOCOUNT ON** 의 사용이 효과가 없거나 성능을 감소시켰습니다. 테스트 저장 프로시저는 간단하게 테이블 반환 매개 변수의 **INSERT** 명령 하나만 포함했습니다. 이 명령문은 더 복잡한 저장 프로시저에 유용할 수 있습니다. 하지만 저장 프로시저에 **SET NOCOUNT ON** 을 추가한다고 해서 자동으로 성능이 향상될 것이라고 가정하지 마세요. 효과를 이해하려면 **SET NOCOUNT ON** 문을 포함한 상태와 그렇지 않은 상태로 사용자의 저장 프로시저를 테스트해야 합니다.

## <a name="batching-scenarios"></a>일괄 처리 시나리오

다음 섹션은 세 개의 애플리케이션 시나리오에서 테이블 반환 매개 변수를 사용하는 방법을 설명합니다. 첫 번째 시나리오는 버퍼링과 일괄 처리가 함께 작업할 수 있는 방법을 보여줍니다. 두 번째 시나리오는 하나의 저장 프로시저 호출로 마스터-세부 정보 작업 수행하여 성능을 향상시킵니다. 마지막 시나리오는 “UPSERT” 작업에서 테이블 반환 매개 변수를 사용하는 방법을 보여 줍니다.

### <a name="buffering"></a>버퍼링

일괄 처리가 확실히 적합할 만한 시나리오가 있기는 하지만 지연 처리를 통한 일괄 처리를 활용할 수 있는 시나리오는 많이 있습니다. 하지만 지연 처리는 예기치 않은 오류가 발생하면 데이터가 손실되는 등의 큰 위험이 수반됩니다. 이러한 위험을 이해하고 그에 따른 결과를 고려하는 것이 중요합니다.

예를 들어, 각 사용자의 탐색 내역을 추적하는 애플리케이션을 생각해 보겠습니다. 각 페이지 요청에 대해, 애플리케이션은 사용자의 페이지 보기를 기록하기 위한 데이터베이스 호출을 만들 수 있습니다. 하지만 사용자의 검색 작업을 버퍼링한 후 이 데이터를 데이터베이스에 일괄 처리해서 보내면 더 높은 성능과 스케일링 성능을 달성할 수 있습니다. 경과 시간 및/또는 버퍼 크기에 따라서 데이터베이스 업데이트를 트리거할 수 있습니다. 예를 들어, 20초 후에 배치가 처리되도록 하거나 버퍼의 항목이 1000개에 도달하면 배치가 처리되도록 규칙을 지정할 수 있습니다.

다음 코드 예제는 모니터링 클래스에 의해 발생한 버퍼 이벤트를 처리하기 위해 [Reactive Extensions - Rx](/previous-versions/dotnet/reactive-extensions/hh242985(v=vs.103)) 를 사용합니다. 버퍼가 차거나 제한 시간에 도달하면, 사용자 데이터 배치는 테이블 반환 매개 변수와 함께 데이터베이스로 전송됩니다.

다음 NavHistoryData 클래스는 사용자 탐색 세부 정보를 모델링합니다. 사용자 ID, 액세스한 URL, 액세스 시간을 비롯한 기본 정보를 포함합니다.

```csharp
public class NavHistoryData
{
    public NavHistoryData(int userId, string url, DateTime accessTime)
    { UserId = userId; URL = url; AccessTime = accessTime; }
    public int UserId { get; set; }
    public string URL { get; set; }
    public DateTime AccessTime { get; set; }
}
```

NavHistoryDataMonitor 클래스는 사용자 탐색 데이터를 데이터베이스로 버퍼링하는 것을 담당합니다. **OnAdded** 이벤트가 발생하면 응답하는RecordUserNavigationEntry라는 메서드를 포함합니다. 다음 코드는 Rx를 사용하여 이벤트를 기반으로 관측 가능한 컬렉션을 만드는 생성자 논리를 보여줍니다. 그 후 Buffer 메서드와 함께 관측 가능한 컬렉션을 구독합니다. 오버로드는 20초마다 또는 항목이 1000개가 될 때마다 버퍼가 전송되어야 한다는 것을 지정합니다.

```csharp
public NavHistoryDataMonitor()
{
    var observableData =
        Observable.FromEventPattern<NavHistoryDataEventArgs>(this, "OnAdded");

    observableData.Buffer(TimeSpan.FromSeconds(20), 1000).Subscribe(Handler);
}
```

처리기는 버퍼링된 모든 항목을 테이블 반환 형식으로 변환한 후 이 형식을 배치를 처리하는 저장 프로시저로 전달합니다. 다음 코드는 NavHistoryDataEventArgs 및 NavHistoryDataMonitor 클래스에 대한 전체 정의를 보여줍니다.

```csharp
public class NavHistoryDataEventArgs : System.EventArgs
{
    public NavHistoryDataEventArgs(NavHistoryData data) { Data = data; }
    public NavHistoryData Data { get; set; }
}

public class NavHistoryDataMonitor
{
    public event EventHandler<NavHistoryDataEventArgs> OnAdded;

    public NavHistoryDataMonitor()
    {
        var observableData =
            Observable.FromEventPattern<NavHistoryDataEventArgs>(this, "OnAdded");

        observableData.Buffer(TimeSpan.FromSeconds(20), 1000).Subscribe(Handler);
    }
```

처리기는 버퍼링된 모든 항목을 테이블 반환 형식으로 변환한 후 이 형식을 배치를 처리하는 저장 프로시저로 전달합니다. 다음 코드는 NavHistoryDataEventArgs 및 NavHistoryDataMonitor 클래스에 대한 전체 정의를 보여줍니다.

```csharp
    public class NavHistoryDataEventArgs : System.EventArgs
    {
        if (OnAdded != null)
            OnAdded(this, new NavHistoryDataEventArgs(data));
    }

    protected void Handler(IList<EventPattern<NavHistoryDataEventArgs>> items)
    {
        DataTable navHistoryBatch = new DataTable("NavigationHistoryBatch");
        navHistoryBatch.Columns.Add("UserId", typeof(int));
        navHistoryBatch.Columns.Add("URL", typeof(string));
        navHistoryBatch.Columns.Add("AccessTime", typeof(DateTime));
        foreach (EventPattern<NavHistoryDataEventArgs> item in items)
        {
            NavHistoryData data = item.EventArgs.Data;
            navHistoryBatch.Rows.Add(data.UserId, data.URL, data.AccessTime);
        }

        using (SqlConnection connection = new SqlConnection(CloudConfigurationManager.GetSetting("Sql.ConnectionString")))
        {
            connection.Open();

            SqlCommand cmd = new SqlCommand("sp_RecordUserNavigation", connection);
            cmd.CommandType = CommandType.StoredProcedure;

            cmd.Parameters.Add(
                new SqlParameter()
                {
                    ParameterName = "@NavHistoryBatch",
                    SqlDbType = SqlDbType.Structured,
                    TypeName = "NavigationHistoryTableType",
                    Value = navHistoryBatch,
                });

            cmd.ExecuteNonQuery();
        }
    }
}
```

이 버퍼링 클래스를 사용하기 위해서 애플리케이션은 정적 NavHistoryDataMonitor 개체를 생성합니다. 사용자가 페이지에 액세스할 때마다 애플리케이션은 NavHistoryDataMonitor.RecordUserNavigationEntry 메서드를 호출합니다. 버퍼링 논리는 이러한 항목의 데이터베이스에 대한 일괄 전송을 처리하도록 진행됩니다.

### <a name="master-detail"></a>마스터-세부 정보

테이블 반환 매개 변수는 간단한 INSERT 시나리오에 유용합니다. 하지만 두 개 이상의 테이블이 연관되는 일괄 처리 삽입은 더 어려울 수 있습니다. “마스터/세부 정보” 시나리오가 좋은 예입니다. 마스터 테이블은 기본 엔터티를 식별합니다. 하나 이상의 세부 정보 테이블은 엔터티에 대한 데이터를 더 많이 저장합니다. 이 시나리오에서 외래 키 관계는 고유 마스터 엔터티에 세부 정보의 관계를 적용합니다. PurchaseOrder 테이블의 간소화된 버전 및 그와 연결된 OrderDetail 테이블을 생각해 보겠습니다. 다음 Transact-SQL은 4개의 열 즉 OrderID, OrderDate, CustomerID, Status를 포함하는 PurchaseOrder 테이블을 생성합니다.

```sql
CREATE TABLE [dbo].[PurchaseOrder](
[OrderID] [int] IDENTITY(1,1) NOT NULL,
[OrderDate] [datetime] NOT NULL,
[CustomerID] [int] NOT NULL,
[Status] [nvarchar](50) NOT NULL,
CONSTRAINT [PrimaryKey_PurchaseOrder]
PRIMARY KEY CLUSTERED ( [OrderID] ASC ))
```

각각의 주문은 하나 이상의 제품 구매를 포함합니다. 이 정보는 PurchaseOrderDetail 테이블에 캡처됩니다. 다음 Transact-SQL은 5개의 열 즉, OrderID, OrderDetailID, ProductID, UnitPrice, OrderQty를 포함하는 PurchaseOrderDetail 테이블을 생성합니다.

```sql
CREATE TABLE [dbo].[PurchaseOrderDetail](
[OrderID] [int] NOT NULL,
[OrderDetailID] [int] IDENTITY(1,1) NOT NULL,
[ProductID] [int] NOT NULL,
[UnitPrice] [money] NULL,
[OrderQty] [smallint] NULL,
CONSTRAINT [PrimaryKey_PurchaseOrderDetail] PRIMARY KEY CLUSTERED
( [OrderID] ASC, [OrderDetailID] ASC ))
```

PurchaseOrderDetail 테이블의 OrderID 열은 PurchaseOrder 테이블에서 주문을 참조해야 합니다. 외래 키에 대한 다음 정의가 이 제약 조건에 적용됩니다.

```sql
ALTER TABLE [dbo].[PurchaseOrderDetail]  WITH CHECK ADD
CONSTRAINT [FK_OrderID_PurchaseOrder] FOREIGN KEY([OrderID])
REFERENCES [dbo].[PurchaseOrder] ([OrderID])
```

테이블 반환 매개 변수를 사용하려면 각 대상 테이블에 대해 하나의 사용자 정의 테이블 형식이 있어야 합니다.

```sql
CREATE TYPE PurchaseOrderTableType AS TABLE
( OrderID INT,
    OrderDate DATETIME,
    CustomerID INT,
    Status NVARCHAR(50) );
GO

CREATE TYPE PurchaseOrderDetailTableType AS TABLE
( OrderID INT,
    ProductID INT,
    UnitPrice MONEY,
    OrderQty SMALLINT );
GO
```

그 후 이런 형식의 테이블을 허용하는 저장 프로시저를 정의합니다. 이 프로시저는 애플리케이션이 단일 호출로 주문 집합 및 주문 세부 정보를 로컬에서 일괄 처리하도록 합니다. 다음 Transact-SQL은 이 구매 주문 예제에 대한 저장 프로시저 선언 전체를 제공합니다.

```sql
CREATE PROCEDURE sp_InsertOrdersBatch (
@orders as PurchaseOrderTableType READONLY,
@details as PurchaseOrderDetailTableType READONLY )
AS
SET NOCOUNT ON;

-- Table that connects the order identifiers in the @orders
-- table with the actual order identifiers in the PurchaseOrder table
DECLARE @IdentityLink AS TABLE (
SubmittedKey int,
ActualKey int,
RowNumber int identity(1,1)
);

-- Add new orders to the PurchaseOrder table, storing the actual
-- order identifiers in the @IdentityLink table
INSERT INTO PurchaseOrder ([OrderDate], [CustomerID], [Status])
OUTPUT inserted.OrderID INTO @IdentityLink (ActualKey)
SELECT [OrderDate], [CustomerID], [Status] FROM @orders ORDER BY OrderID;

-- Match the passed-in order identifiers with the actual identifiers
-- and complete the @IdentityLink table for use with inserting the details
WITH OrderedRows As (
SELECT OrderID, ROW_NUMBER () OVER (ORDER BY OrderID) As RowNumber
FROM @orders
)
UPDATE @IdentityLink SET SubmittedKey = M.OrderID
FROM @IdentityLink L JOIN OrderedRows M ON L.RowNumber = M.RowNumber;

-- Insert the order details into the PurchaseOrderDetail table,
-- using the actual order identifiers of the master table, PurchaseOrder
INSERT INTO PurchaseOrderDetail (
[OrderID],
[ProductID],
[UnitPrice],
[OrderQty] )
SELECT L.ActualKey, D.ProductID, D.UnitPrice, D.OrderQty
FROM @details D
JOIN @IdentityLink L ON L.SubmittedKey = D.OrderID;
GO
```

이 예제에서 로컬에 정의된 @IdentityLink 테이블은 새로 삽입된 행의 실제 OrderID 값을 저장합니다. 이 주문 ID는 @orders 및 @details 테이블 반환 매개 변수의 임시 OrderID 값과 다릅니다. 이런 이유 때문에 @IdentityLink 테이블은 @orders 매개 변수의 OrderID 값을 PurchaseOrder 테이블의 새로운 행에 대한 실제 OrderID 값에 연결합니다. 이 단계에서 @IdentityLink 테이블은 외래 키 제약 조건을 충족하는 실제 OrderID로 주문 세부 정보를 삽입하는데 도움이 될 수 있습니다.

저장된 프로시저는 코드 또는 기타 Transact-SQL 호출에서 사용할 수 있습니다. 코드 예제는 이 문서의 테이블 반환 매개 변수 섹션을 참조하세요. 다음 Transact-SQL은 sp_InsertOrdersBatch를 호출하는 방법을 보여줍니다.

```sql
declare @orders as PurchaseOrderTableType
declare @details as PurchaseOrderDetailTableType

INSERT @orders
([OrderID], [OrderDate], [CustomerID], [Status])
VALUES(1, '1/1/2013', 1125, 'Complete'),
(2, '1/13/2013', 348, 'Processing'),
(3, '1/12/2013', 2504, 'Shipped')

INSERT @details
([OrderID], [ProductID], [UnitPrice], [OrderQty])
VALUES(1, 10, $11.50, 1),
(1, 12, $1.58, 1),
(2, 23, $2.57, 2),
(3, 4, $10.00, 1)

exec sp_InsertOrdersBatch @orders, @details
```

이 솔루션은 각 배치가 1로 시작하는 OrderID 값 집합을 사용하도록 합니다. 임시 OrderID 값은 이 배치의 관계를 설명하지만 실제 OrderID 값은 삽입 작업 시에 결정됩니다. 이전 예제와 같은 명령문을 반복해서 실행하고 데이터베이스에 고유한 주문을 생성할 수 있습니다. 이런 이유 때문에 일괄 처리 기법을 사용할 때는 중복 주문을 방지하는 코드 또는 데이터베이스 논리를 더 추가하는 것을 고려하는 좋습니다.

이 예제는 훨씬 더 복잡한 데이터베이스 작업(예: 마스터-세부 정보 작업)도 테이블 반환 매개 변수를 사용하여 일괄 처리가 가능하다는 것을 보여줍니다.

### <a name="upsert"></a>UPSERT

다른 일괄 작업 시나리오는 동시에 기존 행을 업데이트하고 새 행을 삽입하는 작업을 포함합니다. 이 작업은 경우에 따라 “UPSERT”(업데이트 + 삽입) 작업이라고 합니다. INSERT 및 UPDATE에 대한 호출을 따로 생성하는 대신 MERGE 문을 사용하는 것이 적합합니다. MERGE 문은 삽입과 업데이트 작업을 단일 호출로 수행할 수 있습니다. MERGE 문 잠금 메커니즘은 개별 INSERT 및 UPDATE 문과 다르게 작동합니다. 프로덕션에 배포하기 전에 특정 워크로드를 테스트합니다.

테이블 반환 매개 변수가 MERGE 문과 함께 사용되어 업데이트와 삽입을 수행할 수 있습니다. 예를 들어 EmployeeID, FirstName, LastName, SocialSecurityNumber 열을 포함하는 간소화된 Employee 테이블이 있습니다.

```sql
CREATE TABLE [dbo].[Employee](
[EmployeeID] [int] IDENTITY(1,1) NOT NULL,
[FirstName] [nvarchar](50) NOT NULL,
[LastName] [nvarchar](50) NOT NULL,
[SocialSecurityNumber] [nvarchar](50) NOT NULL,
CONSTRAINT [PrimaryKey_Employee] PRIMARY KEY CLUSTERED
([EmployeeID] ASC ))
```

이 예제에서 여러 직원의 MERGE를 수행하기 위해 SocialSecurityNumber 가 고유하다는 사실을 이용할 수 있습니다. 우선, 사용자 정의 테이블 형식을 만듭니다.

```sql
CREATE TYPE EmployeeTableType AS TABLE
( Employee_ID INT,
    FirstName NVARCHAR(50),
    LastName NVARCHAR(50),
    SocialSecurityNumber NVARCHAR(50) );
GO
```

다음으로, 업데이트 및 삽입을 수행하기 위해 MERGE 문을 사용하는 코드를 작성하거나 저장 프로시저를 만듭니다. 다음 예제는 EmployeeTableType 형식의 @employees 테이블 반환 매개 변수에 MERGE 문을 사용합니다. @employees 테이블의 내용은 여기에 표시되어 있습니다.

```sql
MERGE Employee AS target
USING (SELECT [FirstName], [LastName], [SocialSecurityNumber] FROM @employees)
AS source ([FirstName], [LastName], [SocialSecurityNumber])
ON (target.[SocialSecurityNumber] = source.[SocialSecurityNumber])
WHEN MATCHED THEN
UPDATE SET
target.FirstName = source.FirstName,
target.LastName = source.LastName
WHEN NOT MATCHED THEN
    INSERT ([FirstName], [LastName], [SocialSecurityNumber])
    VALUES (source.[FirstName], source.[LastName], source.[SocialSecurityNumber]);
```

자세한 내용은 MERGE 문에 대한 설명서 또는 예제를 참조하세요. 동일한 작업이 별도의 INSERT 및 UPDATE 작업을 포함하는 여러 단계의 저장 프로시저로 수행될 수 있지만 MERGE 문이 더 효율적입니다. 데이터베이스 코드는 INSERT 및 UPDATE에 대한 두 개의 데이터베이스 호출 없이도 MERGE 문을 바로 사용하는 Transact-SQL 호출을 생성할 수 있습니다.

## <a name="recommendation-summary"></a>권장 사항 요약

다음 목록은 이 문서에 논의된 일괄 작업 권장 사항에 대한 요약을 제공합니다.

* Azure SQL Database 및 Azure SQL Managed Instance 애플리케이션의 성능과 스케일링 성능을 높이려면 버퍼링 및 일괄 처리를 사용합니다.
* 일괄 처리/버퍼링과 복원력 사이의 균형 유지(상쇄)를 이해합니다. 역할에 오류가 발생하면, 중요한 비즈니스 데이터를 처리하지 않은 배치 파일을 손실할 위험이 일괄 처리 성능의 이점을 능가합니다.
* 대기 시간을 줄이기 위해 단일 데이터 센터 내에 데이터베이스에 대한 모든 호출을 유지하도록 시도합니다.
* 단일 일괄 처리 기법을 선택하는 경우, 테이블 반환 매개 변수가 최고의 성능 및 유연성을 제공합니다.
* 최고의 삽입 성능을 위해 아래의 일반적인 지침을 따르되 사용자의 시나리오를 테스트합니다.
  * 행이 100개 미만이면 단일 매개 변수가 있는 INSERT 명령을 사용합니다.
  * 행이 1000개 미만이면 테이블 반환 매개 변수를 사용합니다.
  * 행이 1000개 이상이면 SqlBulkCopy를 사용합니다.
* 업데이트 및 삭제 작업의 경우 테이블 매개 변수의 각 행에 대해 올바른 작업을 결정하는 저장 프로시저 논리와 함께 테이블 반환 매개 변수를 사용합니다.
* Batch 크기 지침:
  * 사용자의 애플리케이션 및 비즈니스 요구 사항에 합당한 최대 배치 크기를 사용합니다.
  * 대형 배치의 성능 향상과 임시 오류 또는 치명적인 오류의 위험 사이에서 균형을 유지합니다. 재시도 또는 배치 파일에 포함된 데이터 손실의 결과(대가)는 무엇인가요?
  * Azure SQL Database 또는 Azure SQL Managed Instance가 일괄 처리를 거부하지 않는지 확인하기 위해 큰 규모의 일괄 처리를 테스트합니다.
  * 배치 크기 또는 버퍼링 시간대와 같이 일괄 처리를 제어하는 구성 설정을 만듭니다. 이러한 설정은 유연성을 제공합니다. 클라우드 서비스를 다시 배포하지 않고도 프로덕션에서 일괄 처리 동작을 변경할 수 있습니다.
* 단일 데이터베이스의 단일 테이블에서 작동하는 배치를 병렬로 실행하지 않도록 합니다. 그렇게 하는 경우에는 여러 작업자 스레드의 단일 배치를 나누고 테스트를 실행하여 이상적인 스레드의 개수를 판단합니다. 스레드가 지정되지 않으면 더 많은 스레드가 성능을 높이기 보다는 감소시킵니다.
* 보다 많은 시나리오에 일괄 처리를 구현하는 방법으로 크기 및 시간에 따른 버퍼링을 고려합니다.

## <a name="next-steps"></a>다음 단계

이 문서는 일괄 처리와 관련된 데이터베이스 디자인과 코딩 기법이 애플리케이션 성능과 확장성을 향상시킬 수 있는 방법에 중점을 두고 있습니다. 하지만 이것은 사용자의 전반적인 전략 중 한 가지 요소에 불과합니다. 성능과 스케일링 성능을 개선하는 방법을 더 보려면 [데이터베이스 성능 참고 자료](database/performance-guidance.md) 및 [탄력적 풀의 가격 및 성능 고려 사항](database/elastic-pool-overview.md)을 참조하세요.