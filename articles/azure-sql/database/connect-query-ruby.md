---
title: Ruby를 사용하여 쿼리
description: 이 항목에서는 Ruby를 사용하여 데이터베이스에 연결하고 Transact-SQL 문을 사용하여 쿼리하는 프로그램을 만드는 방법을 보여줍니다.
titleSuffix: Azure SQL Database & SQL Managed Instance
services: sql-database
ms.service: sql-database
ms.subservice: connect
ms.custom: sqldbrb=2 , mode-other
ms.devlang: ruby
ms.topic: quickstart
author: dzsquared
ms.author: drskwier
ms.reviewer: mathoma
ms.date: 05/29/2020
ms.openlocfilehash: 7d0562c683eb5879d0475a16e9dd7460029a35ea
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133060367"
---
# <a name="quickstart-use-ruby-to-query-a-database-in-azure-sql-database-or-azure-sql-managed-instance"></a>빠른 시작: Ruby를 사용하여 Azure SQL Database 또는 Azure SQL Managed Instance의 데이터베이스 쿼리
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

이 빠른 시작에서는 [Ruby](https://www.ruby-lang.org)를 사용하여 데이터베이스에 연결하고 Transact-SQL 문을 사용하여 데이터를 쿼리하는 방법을 보여줍니다.

## <a name="prerequisites"></a>필수 구성 요소

이 빠른 시작을 완료하려면 다음 필수 구성 요소가 필요합니다.

- 데이터베이스입니다. 다음 빠른 시작 중 하나를 사용하여 데이터베이스를 만들고 구성할 수 있습니다.

  | 작업 | SQL Database | SQL Managed Instance | Azure VM의 SQL Server |
  |:--- |:--- |:---|:---|
  | 생성| [포털](single-database-create-quickstart.md) | [포털](../managed-instance/instance-create-quickstart.md) | [포털](../virtual-machines/windows/sql-vm-create-portal-quickstart.md)
  || [CLI](scripts/create-and-configure-database-cli.md) | [CLI](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) |
  || [PowerShell](scripts/create-and-configure-database-powershell.md) | [PowerShell](../managed-instance/scripts/create-configure-managed-instance-powershell.md) | [PowerShell](../virtual-machines/windows/sql-vm-create-powershell-quickstart.md)
  | 구성 | [서버 수준 IP 방화벽 규칙](firewall-create-server-level-portal-quickstart.md)| [VM에서 연결](../managed-instance/connect-vm-instance-configure.md)|
  |||[온-프레미스에서 연결](../managed-instance/point-to-site-p2s-configure.md) | [SQL Server 인스턴스에 연결](../virtual-machines/windows/sql-vm-create-portal-quickstart.md)
  |데이터 로드|Adventure Works(빠른 시작마다 로드됨)|[Wide World Importers 복원](../managed-instance/restore-sample-database-quickstart.md) | [Wide World Importers 복원](../managed-instance/restore-sample-database-quickstart.md) |
  |||[GitHub](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works)의 [BACPAC](database-import.md) 파일에서 Adventure Works 복원 또는 가져오기| [GitHub](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works)의 [BACPAC](database-import.md) 파일에서 Adventure Works 복원 또는 가져오기|
  |||

  > [!IMPORTANT]
  > 이 문서의 스크립트는 Adventure Works 데이터베이스를 사용하도록 작성되었습니다. SQL Managed Instance의 경우 Adventure Works 데이터베이스를 인스턴스 데이터베이스로 가져오거나 이 문서의 스크립트를 수정하여 Wide World Importers 데이터베이스를 사용해야 합니다.
  
- 운영 체제용 Ruby 및 관련 소프트웨어:
  
  - **macOS**: Homebrew, rbenv and ruby-build, Ruby, FreeTDS 및 TinyTDS를 설치합니다. [macOS에서 SQL Server를 사용하여 Ruby 앱 만들기](https://www.microsoft.com/sql-server/developer-get-started/ruby/mac/)에서 1.2, 1.3, 1.4, 1.5 및 2.1 단계를 참조하세요.
  
  - **Ubuntu**: Ruby, rbenv and ruby-build, Ruby, FreeTDS 및 TinyTDS에 대한 필수 구성 요소를 설치합니다. [Ubuntu에서 SQL Server를 사용하여 Ruby 앱 만들기](https://www.microsoft.com/sql-server/developer-get-started/ruby/ubuntu/)에서 1.2, 1.3, 1.4, 1.5 및 2.1 단계를 참조하세요.
  
  - **Windows**: Ruby, Ruby Devkit 및 TinyTDS를 설치합니다. [Ruby 개발을 위한 개발 환경 구성](/sql/connect/ruby/step-1-configure-development-environment-for-ruby-development)을 참조하세요.

## <a name="get-server-connection-information"></a>서버 연결 정보 가져오기

Azure SQL Database의 데이터베이스에 연결하는 데 필요한 연결 정보를 가져옵니다. 다음 절차를 수행하려면 정규화된 서버 이름이나 호스트 이름, 데이터베이스 이름 및 로그인 정보가 필요합니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.

2. **SQL 데이터베이스** 또는 **SQL Managed Instances** 페이지로 이동합니다.

3. **개요** 페이지에서 Azure SQL Database의 데이터베이스에 대한 **서버 이름** 옆에 있는 정규화된 서버 이름 또는 Azure VM의 Azure SQL Managed Instance 또는 SQL Server에 대한 **호스트** 옆에 있는 정규화된 서버 이름(또는 IP 주소)을 검토합니다. 서버 이름이나 호스트 이름을 복사하려면 마우스로 해당 이름 위를 가리키고 **복사** 아이콘을 선택합니다.

> [!NOTE]
> Azure VM의 SQL Server에 대한 연결 정보는 [SQL Server 인스턴스에 연결](../virtual-machines/windows/sql-vm-create-portal-quickstart.md#connect-to-sql-server)을 참조하세요.

## <a name="create-code-to-query-your-database-in-azure-sql-database"></a>Azure SQL Database의 데이터베이스를 쿼리할 코드 만들기

1. 텍스트 또는 코드 편집기에서 *sqltest.rb* 라는 새 파일을 만듭니다.

1. 다음 코드를 추가합니다. Azure SQL Database의 데이터베이스 값을 `<server>`, `<database>`, `<username>` 및 `<password>`로 대체합니다.

   >[!IMPORTANT]
   >이 예제의 코드에서는 데이터베이스를 만들 때 원본으로 선택할 수 있는 샘플 AdventureWorksLT 데이터를 사용합니다. 사용자 데이터베이스에 다른 데이터가 있는 경우 SELECT 쿼리에 해당 데이터베이스의 테이블을 사용합니다. 

   ```ruby
   require 'tiny_tds'
   server = '<server>.database.windows.net'
   database = '<database>'
   username = '<username>'
   password = '<password>'
   client = TinyTds::Client.new username: username, password: password, 
       host: server, port: 1433, database: database, azure: true
   
   puts "Reading data from table"
   tsql = "SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName
           FROM [SalesLT].[ProductCategory] pc
           JOIN [SalesLT].[Product] p
           ON pc.productcategoryid = p.productcategoryid"
   result = client.execute(tsql)
   result.each do |row|
       puts row
   end
   ```

## <a name="run-the-code"></a>코드 실행

1. 명령 프롬프트에서 다음 명령을 실행합니다.

   ```bash
   ruby sqltest.rb
   ```
   
1. 상위 20개의 Category/Product 행이 데이터베이스에서 반환되는지 확인합니다. 

## <a name="next-steps"></a>다음 단계
- [Azure SQL Database에서 첫 번째 데이터베이스 디자인](design-first-database-tutorial.md)
- [TinyTDS에 대한 GitHub 리포지토리](https://github.com/rails-sqlserver/tiny_tds)
- [TinyTDS에 관한 문제 보고 또는 질문](https://github.com/rails-sqlserver/tiny_tds/issues)
- [SQL Server용 Ruby 드라이버](/sql/connect/ruby/ruby-driver-for-sql-server/)
