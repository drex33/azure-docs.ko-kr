---
title: .NET Core를 사용하여 데이터베이스 연결 및 쿼리
description: 이 토픽은 .NET Core를 사용하여 Azure SQL Database 또는 Azure SQL Managed Instance의 데이터베이스에 연결하고 Transact-SQL 문을 사용하여 쿼리하는 프로그램을 만드는 방법을 보여줍니다.
titleSuffix: Azure SQL Database & SQL Managed Instance
services: sql-database
ms.service: sql-database
ms.subservice: connect
ms.custom: sqldbrb=2, devx-track-csharp, mode-other
ms.devlang: dotnet
ms.topic: quickstart
author: dzsquared
ms.author: drskwier
ms.reviewer: mathoma
ms.date: 05/29/2020
ms.openlocfilehash: 46cf4f24c9358c8ead51c424d06c0cf70aabf792
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133068720"
---
# <a name="quickstart-use-net-core-c-to-query-a-database"></a>빠른 시작: .NET Core(C#)를 사용하여 데이터베이스 쿼리
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi-asa.md)]

이 빠른 시작에서는 [.NET Core](https://dotnet.microsoft.com) 및 C# 코드를 사용하여 데이터베이스에 연결합니다. 그런 다음, Transact-SQL 문을 실행하여 데이터를 쿼리합니다.

> [!TIP]
> 다음 Microsoft Learn 모듈을 사용하면 [Azure SQL Database의 데이터베이스를 쿼리하는 ASP.NET 애플리케이션을 개발하고 구성](/learn/modules/develop-app-that-queries-azure-sql/)하는 방법을 무료로 배울 수 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

이 빠른 시작을 완료하려면 다음이 필요합니다.

- 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- [운영 체제의 .NET Core SDK](https://dotnet.microsoft.com/download)가 설치되었습니다.
- 쿼리를 실행할 수 있는 데이터베이스입니다. 

  [!INCLUDE[create-configure-database](../includes/create-configure-database.md)]
  
## <a name="create-a-new-net-core-project"></a>새로운 .NET Core 프로젝트 만들기

1. 명령 프롬프트를 열고 **sqltest** 라는 폴더를 만듭니다. 이 폴더로 이동하여 이 명령을 실행합니다.

    ```cmd
    dotnet new console
    ```

    이 명령은 초기 C# 코드 파일(**Program.cs**), XML 구성 파일(**sqltest.csproj**) 및 필요한 이진 파일을 포함하여 새로운 앱 프로젝트 파일을 만듭니다.

2. 텍스트 편집기에서 **sqltest.csproj** 를 열고 `<Project>` 태그 사이에 다음 XML을 붙여넣습니다. 이 XML은 `System.Data.SqlClient`를 종속성으로 추가합니다.

    ```xml
    <ItemGroup>
        <PackageReference Include="System.Data.SqlClient" Version="4.6.0" />
    </ItemGroup>
    ```

## <a name="insert-code-to-query-the-database-in-azure-sql-database"></a>Azure SQL Database의 데이터베이스를 쿼리할 코드 삽입하기

1. 텍스트 편집기에서 **Program.cs** 를 엽니다.

2. 내용을 다음 코드로 바꾸고 서버, 데이터베이스, 사용자 이름 및 암호에 대해 적절한 값을 추가합니다.

> [!NOTE]
> ADO.NET 연결 문자열을 사용하려면 서버, 데이터베이스, 사용자 이름 및 암호를 설정하는 코드의 4줄을 아래 줄로 바꿉니다. 문자열에서 사용자 이름과 암호를 설정합니다.
>
>    `builder.ConnectionString="<your_ado_net_connection_string>";`

```csharp
using System;
using System.Data.SqlClient;
using System.Text;

namespace sqltest
{
    class Program
    {
        static void Main(string[] args)
        {
            try 
            { 
                SqlConnectionStringBuilder builder = new SqlConnectionStringBuilder();

                builder.DataSource = "<your_server.database.windows.net>"; 
                builder.UserID = "<your_username>";            
                builder.Password = "<your_password>";     
                builder.InitialCatalog = "<your_database>";
         
                using (SqlConnection connection = new SqlConnection(builder.ConnectionString))
                {
                    Console.WriteLine("\nQuery data example:");
                    Console.WriteLine("=========================================\n");
                    
                    connection.Open();       

                    String sql = "SELECT name, collation_name FROM sys.databases";

                    using (SqlCommand command = new SqlCommand(sql, connection))
                    {
                        using (SqlDataReader reader = command.ExecuteReader())
                        {
                            while (reader.Read())
                            {
                                Console.WriteLine("{0} {1}", reader.GetString(0), reader.GetString(1));
                            }
                        }
                    }                    
                }
            }
            catch (SqlException e)
            {
                Console.WriteLine(e.ToString());
            }
            Console.WriteLine("\nDone. Press enter.");
            Console.ReadLine(); 
        }
    }
}
```

## <a name="run-the-code"></a>코드 실행

1. 프롬프트에서 다음 명령을 실행합니다.

   ```cmd
   dotnet restore
   dotnet run
   ```

2. 행이 반환되는지 확인합니다.

   ```text
   Query data example:
   =========================================

   master   SQL_Latin1_General_CP1_CI_AS
   tempdb   SQL_Latin1_General_CP1_CI_AS
   WideWorldImporters   Latin1_General_100_CI_AS

   Done. Press enter.
   ```

3. **Enter** 를 선택하여 애플리케이션 창을 닫습니다.

## <a name="next-steps"></a>다음 단계

- [명령줄을 사용하여 Windows/Linux/macOS에서 .NET Core를 시작하는 방법](/dotnet/core/tutorials/using-with-xplat-cli)을 알아봅니다.
- [.NET Framework 및 Visual Studio를 사용하여 Azure SQL Database 또는 Azure SQL Managed Instance를 연결 및 쿼리하는 방법](connect-query-dotnet-visual-studio.md)을 알아봅니다.  
- [SSMS를 사용하여 첫 번째 데이터베이스를 디자인](design-first-database-tutorial.md)하거나 [데이터베이스를 디자인하고 C# 및 ADO.NET에 연결](design-first-database-csharp-tutorial.md)하는 방법을 알아봅니다.
- .NET에 대한 자세한 내용은 [.NET 설명서](/dotnet/)를 참조하세요.
