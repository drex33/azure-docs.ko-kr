---
title: 함수에 대 한 Azure SQL 입력 바인딩
description: Azure Functions에서 Azure SQL 입력 바인딩을 사용 하는 방법을 알아봅니다.
author: dzsquared
ms.topic: reference
ms.date: 11/9/2021
ms.author: drskwier
ms.openlocfilehash: 45afc0cc6ccf42711b878d3fccd7912d2bdc750d
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132352701"
---
# <a name="azure-sql-input-binding-for-azure-functions"></a>Azure Functions에 대 한 Azure SQL 입력 바인딩

Azure SQL 입력 바인딩은 데이터베이스에서 데이터를 검색 하 여 함수의 입력 매개 변수에 전달 합니다.

설정 및 구성 세부 정보에 관한 내용은 [개요](./functions-bindings-azure-sql.md)를 참조하세요.

<a id="example" name="example"></a>

# <a name="c"></a>[C#](#tab/csharp)

이 섹션에는 다음 예제가 포함되어 있습니다.

* [HTTP 트리거, 쿼리 문자열에서 ID 조회](#http-trigger-look-up-id-from-query-string-c)
* [HTTP 트리거, 경로 데이터에서 여러 문서 가져오기](#http-trigger-get-multiple-items-from-route-data-c)

이 예에서는 `ToDoItem` 형식 및 해당 데이터베이스 테이블을 참조 합니다.

```cs
namespace AzureSQLSamples
{
    public class ToDoItem
    {
        public string Id { get; set; }
        public int Priority { get; set; }
        public string Description { get; set; }
    }
}
```

```sql
CREATE TABLE dbo.ToDo (
    [Id] int primary key,
    [Priority] int null,
    [Description] nvarchar(200) not null
)
```

<a id="http-trigger-look-up-id-from-query-string-c"></a>

### <a name="http-trigger-look-up-id-from-query-string"></a>HTTP 트리거, 쿼리 문자열에서 ID 조회

다음 예제에서는 단일 레코드를 검색 하는 [c # 함수](functions-dotnet-class-library.md) 를 보여 줍니다. 함수는 쿼리 문자열을 사용 하 여 ID를 지정 하는 HTTP 요청에 의해 트리거됩니다. 이 ID는 지정 된 쿼리를 사용 하 여 레코드를 검색 하는 데 사용 됩니다 `ToDoItem` .

> [!NOTE]
> HTTP 쿼리 문자열 매개 변수는 대/소문자를 구분합니다.
>

```cs
using System.Collections.Generic;
using System.Linq;
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;

namespace AzureSQLSamples
{
    public static class GetToDoItem
    {
        [FunctionName("GetToDoItem")]
        public static IActionResult Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", Route = "gettodoitem")]
            HttpRequest req,
            [Sql("select * from dbo.ToDo where Id = @Id",
                CommandType = System.Data.CommandType.Text,
                Parameters = "@Id={Query.id}",
                ConnectionStringSetting = "SqlConnectionString")]
            IEnumerable<ToDoItem> toDoItem)
        {
            return new OkObjectResult(toDoItem.FirstOrDefault());
        }
    }
}
```

<a id="http-trigger-get-multiple-items-from-route-data-c"></a>

### <a name="http-trigger-get-multiple-items-from-route-data"></a>HTTP 트리거, 경로 데이터에서 여러 항목 가져오기

다음 예제에서는 쿼리에서 반환 된 문서를 검색 하는 [c # 함수](functions-dotnet-class-library.md) 를 보여 줍니다. 함수는 경로 데이터를 사용 하 여 쿼리 매개 변수의 값을 지정 하는 HTTP 요청에 의해 트리거됩니다. 이 매개 변수는 지정 된 쿼리의 레코드를 필터링 하는 데 사용 됩니다 `ToDoItem` .

```cs
using System.Collections.Generic;
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;

namespace AzureSQLSamples
{
    public static class GetToDoItems
    {
        [FunctionName("GetToDoItems")]
        public static IActionResult Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", Route = "gettodoitems/{priority}")]
            HttpRequest req,
            [Sql("select * from dbo.ToDo where [Priority] > @Priority",
                CommandType = System.Data.CommandType.Text,
                Parameters = "@Priority={priority}",
                ConnectionStringSetting = "SqlConnectionString")]
            IEnumerable<ToDoItem> toDoItems)
        {
            return new OkObjectResult(toDoItems);
        }
    }
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Azure Functions에 대 한 Azure SQL 바인딩은 현재 JavaScript를 지원 하지 않습니다.

# <a name="python"></a>[Python](#tab/python)

Azure Functions에 대 한 Azure SQL 바인딩은 현재 Python을 지원 하지 않습니다.

 ---

## <a name="attributes-and-annotations"></a>특성 및 주석

# <a name="c"></a>[C#](#tab/csharp)

[C # 클래스 라이브러리](functions-dotnet-class-library.md)에서 [Sql](https://github.com/Azure/azure-functions-sql-extension/blob/main/src/SqlAttribute.cs) 특성을 사용 합니다.

특성의 생성자는 SQL 명령 텍스트, 명령 유형, 매개 변수 및 연결 문자열 설정 이름을 사용 합니다. 명령은 명령 유형 `System.Data.CommandType.Text` 또는 저장 프로시저 이름이 명령 유형과 함께 사용 되는 transact-sql (SQL SQL) 쿼리 일 수 있습니다 `System.Data.CommandType.StoredProcedure` . 연결 문자열 설정 이름은 `local.settings.json` Azure SQL 또는 SQL Server 인스턴스에 대 한 [연결 문자열](/dotnet/api/microsoft.data.sqlclient.sqlconnection.connectionstring?view=sqlclient-dotnet-core-3.0&preserve-view=true#Microsoft_Data_SqlClient_SqlConnection_ConnectionString) 을 포함 하는 응용 프로그램 설정 (로컬 개발의 경우)에 해당 합니다.

다음은 메서드 서명의 `Sql` 특성 예제입니다.

```csharp
    [FunctionName("GetToDoItems")]
    public static IActionResult Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", Route = "gettodoitems/{priority}")]
            HttpRequest req,
            [Sql("select * from dbo.ToDo where [Priority] > @Priority",
                CommandType = System.Data.CommandType.Text,
                Parameters = "@Priority={priority}",
                ConnectionStringSetting = "SqlConnectionString")]
            IEnumerable<ToDoItem> toDoItems)
    {
        ...
    }
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Azure Functions에 대 한 Azure SQL 바인딩은 현재 JavaScript를 지원 하지 않습니다.

# <a name="python"></a>[Python](#tab/python)

Azure Functions에 대 한 Azure SQL 바인딩은 현재 Python을 지원 하지 않습니다.


---


[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]



## <a name="next-steps"></a>다음 단계

- [데이터베이스에 데이터 저장 (출력 바인딩)](./functions-bindings-azure-sql-output.md)
