---
title: 서버 수준 방화벽 규칙 만들기
description: 서버 수준 방화벽 규칙 만들기
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: sqldbrb=1, mode-portal
ms.devlang: ''
ms.topic: quickstart
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: mathoma, vanto
ms.date: 02/11/2019
ms.openlocfilehash: 16e163d1bad1d5fb45349bcb9e3d26012d16a06e
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133048771"
---
# <a name="quickstart-create-a-server-level-firewall-rule-using-the-azure-portal"></a>빠른 시작: Azure Portal을 사용하여 서버 수준 방화벽 규칙 만들기
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

이 빠른 시작에서는 [논리 SQL 서버](logical-servers.md), 단일 데이터베이스, 탄력적 풀 및 해당 데이터베이스에 연결할 수 있도록 Azure Portal을 사용하여 Azure SQL Database에서 [서버 수준 방화벽 규칙](firewall-configure.md)을 만드는 방법을 설명합니다. 방화벽 규칙은 다른 Azure 리소스 및 온-프레미스 리소스에서 연결하는 데 필요합니다. 서버 수준 방화벽 규칙은 Azure SQL Managed Instance에 적용되지 않습니다.

## <a name="prerequisites"></a>필수 구성 요소

이 빠른 시작에서는 [Azure Portal에서 단일 데이터베이스 만들기](single-database-create-quickstart.md)에서 만든 리소스를 시작점으로 사용합니다.

## <a name="sign-in-to-the-azure-portal"></a>Azure Portal에 로그인

[Azure Portal](https://portal.azure.com/)에 로그인합니다.

## <a name="create-a-server-level-ip-firewall-rule"></a>서버 수준 IP 방화벽 규칙 만들기

 SQL Database는 단일 및 풀링된 데이터베이스에 대한 서버 수준에서 방화벽을 만듭니다. 방화벽을 여는 IP 방화벽 규칙을 만들지 않는 한 이 방화벽은 클라이언트 애플리케이션에서 서버 또는 데이터베이스에 연결하지 못하도록 차단합니다. Azure 외부의 IP 주소에서 연결하려면 연결하려는 특정 IP 주소 또는 주소 범위에 대한 방화벽 규칙을 만듭니다. 서버 수준 및 데이터베이스 수준 IP 방화벽 규칙에 대한 자세한 내용은 [서버 수준 및 데이터베이스 수준 IP 방화벽 규칙](firewall-configure.md)을 참조하세요.

> [!NOTE]
> Azure SQL Database는 포트 1433을 통해 통신합니다. 회사 네트워크 내에서 연결하려는 경우 1433 포트를 통한 아웃바운드 트래픽이 네트워크 방화벽에서 허용되지 않을 수 있습니다. 이 경우 IT 부서에서 1433 포트를 열지 않으면 서버에 연결할 수 없습니다.
> [!IMPORTANT]
> 0\.0.0.0의 방화벽 규칙은 모든 Azure 서비스가 서버 수준 방화벽 규칙을 통과한 후 서버를 통해 데이터베이스에 연결을 시도할 수 있게 해줍니다.

다음 단계에 따라 클라이언트의 IP 주소에 대한 서버 수준 IP 방화벽 규칙을 만들고, IP 주소에 대해서만 Azure SQL Database 방화벽을 통해 외부 연결을 사용하도록 설정합니다.

1. [데이터베이스](#prerequisites) 배포가 완료되면 왼쪽 메뉴에서 **SQL 데이터베이스** 를 선택한 다음, **SQL 데이터베이스** 페이지에서 **mySampleDatabase** 를 선택합니다. 데이터베이스에 대한 개요 페이지가 열려 정규화된 서버 이름(예: **mynewserver-20170824.database.windows.net**)을 표시하고 추가 구성을 위한 옵션을 제공합니다.

2. 다른 빠른 시작에서 서버 및 해당 데이터베이스에 연결하는 데 사용하기 위해 이 정규화된 서버 이름을 복사합니다.

   ![서버 이름](./media/firewall-create-server-level-portal-quickstart/server-name.png)

3. 도구 모음에서 **서버 방화벽 설정** 을 선택합니다. 서버에 대한 **방화벽 설정** 페이지가 열립니다.

   ![서버 수준 IP 방화벽 규칙](./media/firewall-create-server-level-portal-quickstart/server-firewall-rule.png)

4. 도구 모음에서 **클라이언트 IP 추가** 를 선택하여 현재 IP 주소를 새 서버 수준 IP 방화벽 규칙에 추가합니다. 서버 수준 IP 방화벽 규칙은 단일 IP 주소 또는 IP 주소의 범위에 1433 포트를 열 수 있습니다.

   > [!IMPORTANT]
   > 기본적으로 Azure SQL Database 방화벽을 통한 액세스는 모든 Azure 서비스에 대해 비활성됩니다. 모든 Azure 서비스에 대한 액세스를 사용하도록 설정하려면 이 페이지에서 **켜기** 를 선택합니다.
   >

5. **저장** 을 선택합니다. 서버의 1433 포트를 여는 현재 IP 주소에 서버 수준 IP 방화벽 규칙이 생성됩니다.

6. **방화벽 설정** 페이지를 닫습니다.

이제 SQL Server Management Studio 또는 원하는 다른 도구를 사용하여 이전에 만든 서버 관리자 계정을 통해 이 IP 주소에서 서버 및 해당 데이터베이스에 연결할 수 있습니다.

## <a name="clean-up-resources"></a>리소스 정리

[다음 단계](#next-steps)로 이동하여 다양한 방법으로 데이터베이스에 연결하고 쿼리하는 방법을 알아보려면 이러한 리소스를 저장합니다. 그러나 이 빠른 시작에서 만든 리소스를 삭제하려면 다음 단계를 사용합니다.

1. Azure Portal의 왼쪽 메뉴에서 **리소스 그룹** 을 선택한 다음, **myResourceGroup** 을 선택합니다.
2. 리소스 그룹 페이지에서 **삭제** 를 선택하고, 텍스트 상자에서 **myResourceGroup** 을 입력한 다음, **삭제** 를 선택합니다.

## <a name="next-steps"></a>다음 단계

- 이제 데이터베이스가 생겼으니, 다음을 포함하여 자주 사용하는 도구 또는 언어 중 하나를 사용하여 [연결하고 쿼리](connect-query-content-reference-guide.md)할 수 있습니다.
  - [SQL Server Management Studio를 사용하여 연결 및 쿼리](connect-query-ssms.md)
  - [Azure Data Studio를 사용하여 연결 및 쿼리](/sql/azure-data-studio/quickstart-sql-database?toc=/azure/sql-database/toc.json)
- 첫 번째 데이터베이스를 디자인하고, 테이블을 만들고, 데이터를 삽입하는 방법을 알아보려면 다음 자습서 중 하나를 참조하세요.
  - [SSMS를 사용하여 Azure SQL Database에서 첫 번째 단일 데이터베이스 디자인](design-first-database-tutorial.md)
  - [Azure SQL Database에서 단일 데이터베이스를 설계하고 C# 및 ADO.NET으로 연결](design-first-database-csharp-tutorial.md)
