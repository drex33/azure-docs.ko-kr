---
title: '빠른 시작: C#을 사용하여 연결 - Azure Database for MySQL 유연한 서버'
description: 이 빠른 시작에서는 Azure Database for MySQL 유연한 서버에서 데이터를 연결하고 쿼리하는 데 사용할 수 있는 C#(.NET) 코드 샘플을 제공합니다.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.custom: mvc, devx-track-csharp
ms.devlang: csharp
ms.topic: quickstart
ms.date: 01/16/2021
ms.openlocfilehash: f092a1c2347157561768464c196a3d044b550b43
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131445676"
---
# <a name="quickstart-use-net-c-to-connect-and-query-data-in-azure-database-for-mysql---flexible-server"></a>빠른 시작: .NET(C#)을 사용하여 Azure Database for MySQL - 유연한 서버에서 데이터 연결 및 쿼리

[[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]

이 빠른 시작에서는 C# 애플리케이션을 사용하여 MySQL용 Azure Database에 연결하는 방법을 보여 줍니다. SQL 문을 사용하여 데이터베이스의 데이터를 쿼리, 삽입, 업데이트 및 삭제하는 방법을 보여 줍니다.

## <a name="prerequisites"></a>사전 요구 사항

이 빠른 시작에는 다음이 필요합니다.

- 활성 구독이 있는 Azure 계정. 

    [!INCLUDE [flexible-server-free-trial-note](../includes/flexible-server-free-trial-note.md)]
- [Azure Portal](./quickstart-create-server-portal.md)을 사용하여 Azure Database for MySQL 유연한 서버 만들기 <br/> 또는 [Azure CLI](./quickstart-create-server-cli.md)를 만듭니다(없는 경우).
- 퍼블릭 또는 프라이빗 액세스를 사용하는지 여부에 따라 아래 작업 중 **하나** 를 완료하여 연결을 활성화합니다.
- [데이터베이스 및 비관리 사용자 만들기](../howto-create-users.md)
- 플랫폼에 [플랫폼용 .NET SDK](https://dotnet.microsoft.com/download)(Windows, Ubuntu Linux 또는 macOS)를 설치합니다.

[문제가 있나요? 알려주세요.](https://github.com/MicrosoftDocs/azure-docs/issues)

## <a name="create-a-c-project"></a>C# 프로젝트 만들기
명령 프롬프트에서 다음을 실행합니다.

```
mkdir AzureMySqlExample
cd AzureMySqlExample
dotnet new console
dotnet add package MySqlConnector
```

## <a name="get-connection-information"></a>연결 정보 가져오기
MySQL용 Azure Database에 연결하는 데 필요한 연결 정보를 가져옵니다. 정규화된 서버 이름 및 로그인 자격 증명이 필요합니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
2. Azure Portal의 왼쪽 메뉴에서 **모든 리소스** 를 선택한 다음, 생성한 서버를 검색합니다(예: **mydemoserver**).
3. 서버 이름을 선택합니다.
4. 서버의 **개요** 패널에 있는 **서버 이름** 과 **서버 관리자 로그인 이름** 을 기록해 둡니다. 암호를 잊어버리면 이 패널에서 암호를 재설정할 수 있습니다.
 :::image type="content" source="./media/connect-csharp/server-overview-name-login.png" alt-text="MySQL용 Azure Database 서버 이름":::

## <a name="step-1-connect-and-insert-data"></a>1단계: 데이터 연결 및 삽입
`CREATE TABLE` 및 `INSERT INTO` SQL 문을 사용하여 데이터를 연결하고 읽으려면 다음 코드를 사용하세요. 이 코드는 `MySqlConnection` 클래스의 메서드를 사용합니다.
- [OpenAsync()](/dotnet/api/system.data.common.dbconnection.openasync#System_Data_Common_DbConnection_OpenAsync): MySQL에 대한 연결을 설정합니다.
- [CreateCommand()](/dotnet/api/system.data.common.dbconnection.createcommand), CommandText 속성 설정
- [ExecuteNonQueryAsync()](/dotnet/api/system.data.common.dbcommand.executenonqueryasync): 데이터베이스 명령을 실행합니다.

`Server`, `Database`, `UserID` 및 `Password` 매개 변수는 서버 및 데이터베이스를 만들 때 지정한 값으로 바꿉니다.

```csharp
using System;
using System.Threading.Tasks;
using MySqlConnector;

namespace AzureMySqlExample
{
    class MySqlCreate
    {
        static async Task Main(string[] args)
        {
            var builder = new MySqlConnectionStringBuilder
            {
                Server = "YOUR-SERVER.mysql.database.azure.com",
                Database = "YOUR-DATABASE",
                UserID = "USER",
                Password = "PASSWORD",
                SslMode = MySqlSslMode.Required,
            };

            using (var conn = new MySqlConnection(builder.ConnectionString))
            {
                Console.WriteLine("Opening connection");
                await conn.OpenAsync();

                using (var command = conn.CreateCommand())
                {
                    command.CommandText = "DROP TABLE IF EXISTS inventory;";
                    await command.ExecuteNonQueryAsync();
                    Console.WriteLine("Finished dropping table (if existed)");

                    command.CommandText = "CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);";
                    await command.ExecuteNonQueryAsync();
                    Console.WriteLine("Finished creating table");

                    command.CommandText = @"INSERT INTO inventory (name, quantity) VALUES (@name1, @quantity1),
                        (@name2, @quantity2), (@name3, @quantity3);";
                    command.Parameters.AddWithValue("@name1", "banana");
                    command.Parameters.AddWithValue("@quantity1", 150);
                    command.Parameters.AddWithValue("@name2", "orange");
                    command.Parameters.AddWithValue("@quantity2", 154);
                    command.Parameters.AddWithValue("@name3", "apple");
                    command.Parameters.AddWithValue("@quantity3", 100);

                    int rowCount = await command.ExecuteNonQueryAsync();
                    Console.WriteLine(String.Format("Number of rows inserted={0}", rowCount));
                }

                // connection will be closed by the 'using' block
                Console.WriteLine("Closing connection");
            }

            Console.WriteLine("Press RETURN to exit");
            Console.ReadLine();
        }
    }
}
```

[문제가 있나요? 알려주세요.](https://github.com/MicrosoftDocs/azure-docs/issues)

## <a name="step-2-read-data"></a>2단계: 데이터 읽기

`SELECT` SQL 문을 사용하여 데이터를 연결하고 읽으려면 다음 코드를 사용하세요. 이 코드는 메서드와 함께 `MySqlConnection` 클래스를 사용합니다.
- [OpenAsync()](/dotnet/api/system.data.common.dbconnection.openasync#System_Data_Common_DbConnection_OpenAsync): MySQL에 대한 연결을 설정합니다.
- [CreateCommand()](/dotnet/api/system.data.common.dbconnection.createcommand): CommandText 속성을 설정합니다.
- [ExecuteReaderAsync()](/dotnet/api/system.data.common.dbcommand.executereaderasync): 데이터베이스 명령을 실행합니다.
- [ReadAsync()](/dotnet/api/system.data.common.dbdatareader.readasync#System_Data_Common_DbDataReader_ReadAsync): 결과의 레코드로 이동합니다. 그런 다음 GetInt32 및 GetString을 사용하여 레코드의 값을 구문 분석합니다.


`Server`, `Database`, `UserID` 및 `Password` 매개 변수는 서버 및 데이터베이스를 만들 때 지정한 값으로 바꿉니다.

```csharp
using System;
using System.Threading.Tasks;
using MySqlConnector;

namespace AzureMySqlExample
{
    class MySqlRead
    {
        static async Task Main(string[] args)
        {
            var builder = new MySqlConnectionStringBuilder
            {
                Server = "YOUR-SERVER.mysql.database.azure.com",
                Database = "YOUR-DATABASE",
                UserID = "USER@YOUR-SERVER",
                Password = "PASSWORD",
                SslMode = MySqlSslMode.Required,
            };

            using (var conn = new MySqlConnection(builder.ConnectionString))
            {
                Console.WriteLine("Opening connection");
                await conn.OpenAsync();

                using (var command = conn.CreateCommand())
                {
                    command.CommandText = "SELECT * FROM inventory;";

                    using (var reader = await command.ExecuteReaderAsync())
                    {
                        while (await reader.ReadAsync())
                        {
                            Console.WriteLine(string.Format(
                                "Reading from table=({0}, {1}, {2})",
                                reader.GetInt32(0),
                                reader.GetString(1),
                                reader.GetInt32(2)));
                        }
                    }
                }

                Console.WriteLine("Closing connection");
            }

            Console.WriteLine("Press RETURN to exit");
            Console.ReadLine();
        }
    }
}
```

[문제가 있나요? 알려주세요.](https://github.com/MicrosoftDocs/azure-docs/issues)

## <a name="step-3-update-data"></a>3단계: 데이터 업데이트
`UPDATE` SQL 문을 사용하여 데이터를 연결하고 읽으려면 다음 코드를 사용하세요. 이 코드는 메서드와 함께 `MySqlConnection` 클래스를 사용합니다.
- [OpenAsync()](/dotnet/api/system.data.common.dbconnection.openasync#System_Data_Common_DbConnection_OpenAsync): MySQL에 대한 연결을 설정합니다.
- [CreateCommand()](/dotnet/api/system.data.common.dbconnection.createcommand): CommandText 속성을 설정합니다.
- [ExecuteNonQueryAsync()](/dotnet/api/system.data.common.dbcommand.executenonqueryasync): 데이터베이스 명령을 실행합니다.


`Server`, `Database`, `UserID` 및 `Password` 매개 변수는 서버 및 데이터베이스를 만들 때 지정한 값으로 바꿉니다.

```csharp
using System;
using System.Threading.Tasks;
using MySqlConnector;

namespace AzureMySqlExample
{
    class MySqlUpdate
    {
        static async Task Main(string[] args)
        {
            var builder = new MySqlConnectionStringBuilder
            {
                Server = "YOUR-SERVER.mysql.database.azure.com",
                Database = "YOUR-DATABASE",
                UserID = "USER",
                Password = "PASSWORD",
                SslMode = MySqlSslMode.Required,
            };

            using (var conn = new MySqlConnection(builder.ConnectionString))
            {
                Console.WriteLine("Opening connection");
                await conn.OpenAsync();

                using (var command = conn.CreateCommand())
                {
                    command.CommandText = "UPDATE inventory SET quantity = @quantity WHERE name = @name;";
                    command.Parameters.AddWithValue("@quantity", 200);
                    command.Parameters.AddWithValue("@name", "banana");

                    int rowCount = await command.ExecuteNonQueryAsync();
                    Console.WriteLine(String.Format("Number of rows updated={0}", rowCount));
                }

                Console.WriteLine("Closing connection");
            }

            Console.WriteLine("Press RETURN to exit");
            Console.ReadLine();
        }
    }
}
```


## <a name="step-4-delete-data"></a>4단계: 데이터 삭제
`DELETE` SQL 문을 사용하여 데이터를 연결하고 삭제하려면 다음 코드를 사용하세요.

이 코드는 메서드와 함께 `MySqlConnection` 클래스를 사용합니다.
- [OpenAsync()](/dotnet/api/system.data.common.dbconnection.openasync#System_Data_Common_DbConnection_OpenAsync): MySQL에 대한 연결을 설정합니다.
- [CreateCommand()](/dotnet/api/system.data.common.dbconnection.createcommand): CommandText 속성을 설정합니다.
- [ExecuteNonQueryAsync()](/dotnet/api/system.data.common.dbcommand.executenonqueryasync): 데이터베이스 명령을 실행합니다.


`Server`, `Database`, `UserID` 및 `Password` 매개 변수는 서버 및 데이터베이스를 만들 때 지정한 값으로 바꿉니다.

```csharp
using System;
using System.Threading.Tasks;
using MySqlConnector;

namespace AzureMySqlExample
{
    class MySqlDelete
    {
        static async Task Main(string[] args)
        {
            var builder = new MySqlConnectionStringBuilder
            {
                Server = "YOUR-SERVER.mysql.database.azure.com",
                Database = "YOUR-DATABASE",
                UserID = "USER",
                Password = "PASSWORD",
                SslMode = MySqlSslMode.Required,
            };

            using (var conn = new MySqlConnection(builder.ConnectionString))
            {
                Console.WriteLine("Opening connection");
                await conn.OpenAsync();

                using (var command = conn.CreateCommand())
                {
                    command.CommandText = "DELETE FROM inventory WHERE name = @name;";
                    command.Parameters.AddWithValue("@name", "orange");

                    int rowCount = await command.ExecuteNonQueryAsync();
                    Console.WriteLine(String.Format("Number of rows deleted={0}", rowCount));
                }

                Console.WriteLine("Closing connection");
            }

            Console.WriteLine("Press RETURN to exit");
            Console.ReadLine();
        }
    }
}
```

## <a name="clean-up-resources"></a>리소스 정리

이 빠른 시작에서 사용된 모든 리소스를 정리하려면 다음 명령을 사용하여 리소스 그룹을 삭제합니다.

```azurecli
az group delete \
    --name $AZ_RESOURCE_GROUP \
    --yes
```

## <a name="next-steps"></a>다음 단계
> [!div class="nextstepaction"]
> [포털을 사용하여 Azure Database for MySQL 서버 관리](./how-to-manage-server-portal.md)<br/>

> [!div class="nextstepaction"]
> [CLI를 사용하여 Azure Database for MySQL 서버 관리](./how-to-manage-server-cli.md)


