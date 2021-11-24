---
title: .NET과 C#에서 Visual Studio를 사용하여 쿼리
description: Visual Studio를 사용하여 Azure SQL Database 또는 Azure SQL Managed Instance의 데이터베이스에 연결하고 쿼리를 실행하는 C# 앱을 만듭니다.
titleSuffix: Azure SQL Database & SQL Managed Instance
services: sql-database
ms.service: sql-database
ms.subservice: connect
ms.custom: devx-track-csharp, sqldbrb=2, mode-other
ms.devlang: dotnet
ms.topic: quickstart
author: dzsquared
ms.author: drskwier
ms.reviewer: mathoma
ms.date: 08/10/2020
ms.openlocfilehash: 410562ca1050f0d75ebc40a50ad46974629bb38a
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133048794"
---
# <a name="quickstart-use-net-and-c-in-visual-studio-to-connect-to-and-query-a-database"></a>빠른 시작: Visual Studio에서 .NET과 C#을 사용하여 데이터베이스 연결 및 쿼리
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi-asa.md)]

이 빠른 시작에서는 Visual Studio에서 [.NET Framework](https://dotnet.microsoft.com) 및 C# 코드를 사용하여 Transact-SQL 문으로 Azure SQL 또는 Synapse SQL의 데이터베이스를 쿼리하는 방법을 보여줍니다.

## <a name="prerequisites"></a>필수 구성 요소

이 빠른 시작을 완료하려면 다음이 필요합니다.

- 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- [Visual Studio 2019](https://www.visualstudio.com/downloads/) Community, Professional 또는 Enterprise 버전
- 쿼리를 실행할 수 있는 데이터베이스입니다.

  [!INCLUDE[create-configure-database](../includes/create-configure-database.md)]

## <a name="create-code-to-query-the-database-in-azure-sql-database"></a>Azure SQL Database의 데이터베이스를 쿼리할 코드 만들기

1. Visual Studio에서 새 프로젝트를 만듭니다. 
   
1. **새 프로젝트** 대화 상자에서 **Visual C#** , **콘솔 앱(.NET Framework)** 을 선택합니다.
   
1. 프로젝트 이름으로 *sqltest* 를 입력한 다음, **확인** 을 선택합니다. 새 프로젝트가 만들어집니다. 
   
1. **프로젝트** > **NuGet 패키지 관리** 를 선택합니다. 
   
1. **NuGet 패키지 관리자** 에서 **찾아보기** 탭을 선택한 다음, **Microsoft.Data.SqlClient** 를 검색하여 선택합니다.
   
1. **Microsoft.Data.SqlClient** 페이지에서 **설치** 를 선택합니다. 
   - 메시지가 표시되면 **확인** 을 선택하여 설치를 계속 진행합니다. 
   - **라이선스 승인** 창이 표시되면 **동의** 를 선택합니다.
   
1. 설치가 완료되면 **NuGet 패키지 관리자** 를 닫을 수 있습니다. 
   
1. 코드 편집기에서 **Program.cs** 내용을 다음 코드로 바꿉니다. `<your_server>`, `<your_username>`, `<your_password>` 및 `<your_database>`의 값을 대체합니다.
   
   ```csharp
   using System;
   using Microsoft.Data.SqlClient;
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
                   builder.DataSource = "<your_server>.database.windows.net"; 
                   builder.UserID = "<your_username>";            
                   builder.Password = "<your_password>";     
                   builder.InitialCatalog = "<your_database>";
   
                   using (SqlConnection connection = new SqlConnection(builder.ConnectionString))
                   {
                       Console.WriteLine("\nQuery data example:");
                       Console.WriteLine("=========================================\n");
                       
                       String sql = "SELECT name, collation_name FROM sys.databases";
   
                       using (SqlCommand command = new SqlCommand(sql, connection))
                       {
                           connection.Open();
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
               Console.ReadLine();
           }
       }
   }
   ```

## <a name="run-the-code"></a>코드 실행

1. 앱을 실행하려면 **디버그** > **디버그 시작** 을 선택하거나 도구 모음에서 **시작** 을 선택하거나 **F5** 키를 누릅니다.
1. 데이터베이스 이름 및 데이터 정렬이 반환되었는지 확인한 다음, 앱 창을 닫습니다.

## <a name="next-steps"></a>다음 단계

- Windows/Linux/macOS에서 [.NET Core를 사용하여 Azure SQL Database의 데이터베이스를 연결 및 쿼리](connect-query-dotnet-core.md)하는 방법을 알아봅니다.  
- [명령줄을 사용하여 Windows/Linux/macOS에서 .NET Core를 시작하는 방법](/dotnet/core/tutorials/using-with-xplat-cli)을 알아봅니다.
- [SSMS를 사용하여 첫 번째 Azure SQL Database의 데이터베이스 설계](design-first-database-tutorial.md) 또는 [.NET을 사용하여 첫 번째 Azure SQL Database의 데이터베이스 설계](design-first-database-csharp-tutorial.md) 방법을 알아봅니다.
- .NET에 대한 자세한 내용은 [.NET 설명서](/dotnet/)를 참조하세요.
- 재시도 로직 예제: [ADO.NET을 사용하여 Azure SQL에 탄력적으로 연결][step-4-connect-resiliently-to-sql-with-ado-net-a78n]합니다.


<!-- Link references. -->

[step-4-connect-resiliently-to-sql-with-ado-net-a78n]: /sql/connect/ado-net/step-4-connect-resiliently-sql-ado-net
