---
title: 연결 문자열
description: Synapse SQL 풀에 대한 연결 문자열
services: synapse-analytics
author: joannapea
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/17/2018
ms.author: joanpo
ms.reviewer: igorstan
ms.custom: azure-synapse, seo-lt-2019, devx-track-csharp
ms.openlocfilehash: 886a37e5f5802e75c39bbddbd1da08ea2d273a9c
ms.sourcegitcommit: 991268c548dd47e5f7487cd025c7501b9315e477
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/30/2021
ms.locfileid: "133287521"
---
# <a name="connection-strings-for-sql-pools-in-azure-synapse"></a>Azure Synapse의 SQL 풀에 대한 연결 문자열

[ADO.NET](/dotnet/framework/data/adonet?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json), [ODBC](/sql/connect/odbc/windows/microsoft-odbc-driver-for-sql-server-on-windows?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), [PHP](/sql/connect/php/overview-of-the-php-sql-driver?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) 및 [JDBC](/sql/connect/jdbc/microsoft-jdbc-driver-for-sql-server?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)와 같은 여러 가지 다양한 애플리케이션 프로토콜을 사용하여 Azure Synapse의 SQL 풀에 연결할 수 있습니다. 다음은 각 프로토콜에 대한 연결 문제열의 몇 가지 예입니다.  또한 Azure 포털을 사용하여 연결 문자열을 빌드할 수도 있습니다.  

Azure Portal을 사용하여 연결 문자열을 빌드하려면 SQL 풀 블레이드로 이동한 후 *Essentials* 링크에서 ‘데이터베이스 연결 문자열 표시’를 클릭합니다.

## <a name="sample-adonet-connection-string"></a>샘플 ADO.NET 연결 문자열

```csharp
Server=tcp:{your_server}.database.windows.net,1433;Database={your_database};User ID={your_user_name};Password={your_password_here};Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
```

## <a name="sample-odbc-connection-string"></a>샘플 ODBC 연결 문자열

```csharp
Driver={SQL Server Native Client 11.0};Server=tcp:{your_server}.database.windows.net,1433;Database={your_database};Uid={your_user_name};Pwd={your_password_here};Encrypt=yes;TrustServerCertificate=no;Connection Timeout=30;
```

## <a name="sample-php-connection-string"></a>샘플 PHP 연결 문자열

```PHP
Server: {your_server}.database.windows.net,1433 \r\nSQL Database: {your_database}\r\nUser Name: {your_user_name}\r\n\r\nPHP Data Objects(PDO) Sample Code:\r\n\r\ntry {\r\n   $conn = new PDO ( \"sqlsrv:server = tcp:{your_server}.database.windows.net,1433; Database = {your_database}\", \"{your_user_name}\", \"{your_password_here}\");\r\n    $conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );\r\n}\r\ncatch ( PDOException $e ) {\r\n   print( \"Error connecting to SQL Server.\" );\r\n   die(print_r($e));\r\n}\r\n\rSQL Server Extension Sample Code:\r\n\r\n$connectionInfo = array(\"UID\" => \"{your_user_name}\", \"pwd\" => \"{your_password_here}\", \"Database\" => \"{your_database}\", \"LoginTimeout\" => 30, \"Encrypt\" => 1, \"TrustServerCertificate\" => 0);\r\n$serverName = \"tcp:{your_server}.database.windows.net,1433\";\r\n$conn = sqlsrv_connect($serverName, $connectionInfo);
```

## <a name="sample-jdbc-connection-string"></a>샘플 JDBC 연결 문자열

```Java
jdbc:sqlserver://yourserver.database.windows.net:1433;database=yourdatabase;user={your_user_name};password={your_password_here};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;
```

> [!NOTE]
> 짧은 시간 동안 사용 불가 이후에 연결이 계속 유지될 수 있도록 하려면 연결 제한 시간을 300초로 설정하는 것이 좋습니다.

## <a name="next-steps"></a>다음 단계

Visual Studio 및 기타 애플리케이션으로 SQL 풀을 쿼리하려면 [Visual Studio를 사용하여 쿼리](sql-data-warehouse-query-visual-studio.md)를 참조하세요.
