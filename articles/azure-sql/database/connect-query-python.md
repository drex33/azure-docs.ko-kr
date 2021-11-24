---
title: Python을 사용하여 데이터베이스 쿼리
description: 이 항목에서는 Python을 사용하여 Azure SQL Database의 데이터베이스에 연결하고 Transact-SQL 문을 사용하여 쿼리하는 프로그램을 만드는 방법을 보여줍니다.
titleSuffix: Azure SQL Database & SQL Managed Instance
services: sql-database
ms.service: sql-database
ms.subservice: connect
ms.custom: seo-python-october2019, sqldbrb=2, devx-track-python, mode-other
ms.devlang: python
ms.topic: quickstart
author: dzsquared
ms.author: drskwier
ms.reviewer: mathoma
ms.date: 12/19/2020
ms.openlocfilehash: 2f78068bfb3bf44a06f97a1f87e1a86bf1aae87d
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133068608"
---
# <a name="quickstart-use-python-to-query-a-database"></a>빠른 시작: Python을 사용하여 데이터베이스 쿼리

[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi-asa.md)]

이 빠른 시작에서는 Python을 사용하여 Azure SQL Database, Azure SQL Managed Instance 또는 Synapse SQL 데이터베이스에 연결하고 T-SQL 문을 사용하여 데이터를 쿼리합니다.

## <a name="prerequisites"></a>필수 구성 요소

이 빠른 시작을 완료하려면 다음이 필요합니다.

- 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

- 쿼리를 실행할 데이터베이스입니다.

  [!INCLUDE[create-configure-database](../includes/create-configure-database.md)]

- [Python](https://python.org/downloads) 3 및 관련 소프트웨어
    

    |**동작**|**macOS**|**Ubuntu**|**Windows**|
    |----------|-----------|------------|---------|
    |ODBC 드라이버, SQLCMD 및 SQL Server용 Python 드라이버 설치|[macOS에서 SQL Server를 사용하여 Python 앱 만들기](https://www.microsoft.com/sql-server/developer-get-started/python/mac/)에서 **1.2**, **1.3** 및 **2.1** 단계를 사용합니다. 그러면 Homebrew와 Python도 설치됩니다.       |[pyodbc Python 개발을 위한 환경 구성](/sql/connect/python/pyodbc/step-1-configure-development-environment-for-pyodbc-python-development#linux)|[pyodbc Python 개발을 위한 환경을 구성](/sql/connect/python/pyodbc/step-1-configure-development-environment-for-pyodbc-python-development#windows)합니다.|
    |Python 및 기타 필수 패키지 설치|    |`sudo apt-get install python python-pip gcc g++ build-essential`을 사용합니다.|    |
    |추가 정보|[macOS의 Microsoft ODBC 드라이버](/sql/connect/odbc/linux-mac/installing-the-microsoft-odbc-driver-for-sql-server)  |[Linux의 Microsoft ODBC 드라이버](/sql/connect/odbc/linux-mac/installing-the-microsoft-odbc-driver-for-sql-server)|[Linux의 Microsoft ODBC 드라이버](/sql/connect/odbc/linux-mac/installing-the-microsoft-odbc-driver-for-sql-server)|



Python 및 Azure SQL Database의 데이터베이스를 자세히 살펴보려면 [Python용 Azure SQL Database 라이브러리](/python/api/overview/azure/sql), [pyodbc 리포지토리](https://github.com/mkleehammer/pyodbc/wiki/) 및 [pyodbc 샘플](https://github.com/mkleehammer/pyodbc/wiki/Getting-started)을 참조하세요.

## <a name="create-code-to-query-your-database"></a>데이터베이스를 쿼리할 코드 만들기 

1. 텍스트 편집기에서 *sqltest.py* 라는 새 파일을 만듭니다.  
   
1. 다음 코드를 추가합니다. 필수 구성 요소 섹션에서 연결 정보를 가져오고 \<server>, \<database>, \<username> 및 \<password>에 대한 사용자 고유의 값을 대체합니다.
   
   ```python
   import pyodbc
   server = '<server>.database.windows.net'
   database = '<database>'
   username = '<username>'
   password = '{<password>}'   
   driver= '{ODBC Driver 17 for SQL Server}'
   
   with pyodbc.connect('DRIVER='+driver+';SERVER=tcp:'+server+';PORT=1433;DATABASE='+database+';UID='+username+';PWD='+ password) as conn:
       with conn.cursor() as cursor:
           cursor.execute("SELECT TOP 3 name, collation_name FROM sys.databases")
           row = cursor.fetchone()
           while row:
               print (str(row[0]) + " " + str(row[1]))
               row = cursor.fetchone()
   ```
   

## <a name="run-the-code"></a>코드 실행

1. 명령 프롬프트에서 다음 명령을 실행합니다.

   ```cmd
   python sqltest.py
   ```

1. 데이터베이스 및 해당 데이터 정렬이 반환되었는지 확인한 다음, 명령 창을 닫습니다.

## <a name="next-steps"></a>다음 단계

- [Azure SQL Database에서 첫 번째 데이터베이스 디자인](design-first-database-tutorial.md)
- [SQL Server용 Microsoft Python 드라이버](/sql/connect/python/python-driver-for-sql-server/)
- [Python 개발자 센터](https://azure.microsoft.com/develop/python/?v=17.23h)
