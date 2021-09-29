---
title: '빠른 시작: Python을 사용하여 연결 - Azure Database for MySQL - 유연한 서버'
description: 이 빠른 시작에서는 Azure Database for MySQL - 유연한 서버에서 데이터를 연결하고 쿼리하는 데 사용할 수 있는 몇 가지 Python 코드 샘플을 제공합니다.
author: savjani
ms.author: pariks
ms.service: mysql
ms.custom: mvc
ms.devlang: python
ms.topic: quickstart
ms.date: 9/21/2020
ms.openlocfilehash: 8430fc401e8cbb6c526f5bdca9a0b6841447793b
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "128588773"
---
# <a name="quickstart-use-python-to-connect-and-query-data-in-azure-database-for-mysql---flexible-server"></a>빠른 시작: Python을 사용하여 Azure Database for MySQL - 유연한 서버에서 데이터 연결 및 쿼리

[[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]

> [!IMPORTANT]
> Azure Database for MySQL - 유연한 서버는 현재 공개 미리 보기로 제공됩니다.

이 빠른 시작에서는 Python을 사용하여 Azure Database for MySQL 유연한 서버에 연결합니다. 그런 다음, SQL 문을 사용하여 Mac, Ubuntu Linux 및 Windows 플랫폼에서 데이터베이스의 데이터를 쿼리, 삽입, 업데이트 및 삭제합니다. 

이 문서에서는 사용자가 Python을 사용하여 개발하는 데 익숙하지만 Azure Database for MySQL 유연한 서버를 처음 사용한다고 가정합니다.

## <a name="prerequisites"></a>사전 요구 사항

* 활성 구독이 있는 Azure 계정. 

    [!INCLUDE [flexible-server-free-trial-note](../includes/flexible-server-free-trial-note.md)]
* Azure Database for MySQL 유연한 계정. 유연한 서버를 만들려면 [Azure Portal을 사용하여 Azure Database for MySQL 유연한 서버 만들기](./quickstart-create-server-portal.md) 또는 [Azure CLI를 사용하여 Azure Database for MySQL 유연한 서버 만들기](./quickstart-create-server-cli.md)를 참조하세요.

## <a name="preparing-your-client-workstation"></a>클라이언트 워크스테이션 준비
- *프라이빗 액세스(VNet 통합)* 를 사용하여 유연한 서버를 만든 경우 서버와 동일한 VNet 내의 리소스에서 서버에 연결해야 합니다. 가상 머신을 만들고 유연한 서버를 사용하여 만든 VNet에 추가할 수 있습니다. [Azure CLI를 사용하여 Azure Database for MySQL 유연한 서버 가상 네트워크 만들기 및 관리](./how-to-manage-virtual-network-cli.md)를 참조하세요.
- *퍼블릭 액세스(허용되는 IP 주소)* 를 사용하여 유연한 서버를 만든 경우 서버의 방화벽 규칙 목록에 로컬 IP 주소를 추가할 수 있습니다. [Azure CLI를 사용하여 Azure Database for MySQL 유연한 서버 방화벽 규칙 만들기 및 관리](./how-to-manage-firewall-cli.md)를 참조하세요.

## <a name="install-python-and-the-mysql-connector"></a>Python 및 MySQL 커넥터 설치

다음 단계를 사용하여 컴퓨터에 Python과 Python용 MySQL 커넥터를 설치합니다. 

> [!NOTE]
> 이 빠른 시작은 MySQL에 연결하는 원시 SQL 쿼리 접근 방식을 사용합니다. 웹 프레임워크를 사용하는 경우 프레임워크에 대한 권장되는 커넥터를 사용합니다(예: Django용 [mysqlclient](https://pypi.org/project/mysqlclient/)).

1. OS에 대한 [Python 3.7 이상](https://www.python.org/downloads/)을 다운로드하여 설치합니다. MySQL 커넥터를 사용하려면 `PATH`에 Python을 추가해야 합니다.
   
2. 명령 프롬프트 또는 `bash` 셸을 열고 대문자 V 스위치를 사용하여 `python -V`를 실행하여 Python 버전을 확인합니다.
   
3. `pip` 패키지 설치 관리자는 최신 버전의 Python에 포함되어 있습니다. `pip install -U pip`를 실행하여 최신 버전으로 `pip`를 업데이트합니다. 
   
   `pip`가 설치되지 않은 경우 `get-pip.py`를 사용하여 다운로드하고 설치할 수 있습니다. 자세한 내용은 [설치](https://pip.pypa.io/en/stable/installing/)를 참조하세요. 
   
4. `pip`를 사용하여 Python용 MySQL 커넥터 및 해당 종속성을 설치합니다.
   
   ```bash
   pip install mysql-connector-python
   ```
   
   [mysql.com](https://dev.mysql.com/downloads/connector/python/)에서 MySQL용 Python 커넥터를 설치할 수도 있습니다. Python용 MySQL 커넥터에 대한 자세한 내용은 [MySQL 커넥터/Python 개발자 가이드](https://dev.mysql.com/doc/connector-python/en/)를 참조하세요. 

## <a name="get-connection-information"></a>연결 정보 가져오기

Azure Portal에서 Azure Database for MySQL 유연한 서버에 연결하는 데 필요한 연결 정보를 가져옵니다. 서버 이름, 데이터베이스 이름 및 로그인 자격 증명이 필요합니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
   
2. 포털 검색 창에서 **mydemoserver** 와 같이 만든 Azure Database for MySQL 유연한 서버를 검색하여 선택합니다.
   
   <!---:::image type="content" source="./media/connect-python/1_server-overview-name-login.png" alt-text="Azure Database for MySQL Flexible Server name":::-->
   
3. 서버의 **개요** 패널에 있는 **서버 이름** 과 **서버 관리자 로그인 이름** 을 기록해 둡니다. 암호를 잊어버리면 이 페이지에서 암호를 재설정할 수 있습니다.
   
   <!---:::image type="content" source="./media/connect-python/azure-database-for-mysql-server-overview-name-login.png" alt-text="Azure Database for MySQL Flexible Server name":::-->

## <a name="code-samples"></a>코드 샘플

### <a name="run-below-mentioned-python-code-samples"></a>아래에 언급된 Python 코드 샘플을 실행합니다.

이 문서의 각 코드 예제는 다음과 같습니다.

1. 텍스트 편집기에서 새 파일을 만듭니다.
2. 파일에 코드 예제를 추가합니다. 코드에서 `<mydemoserver>`, `<myadmin>`, `<mypassword>` 및 `<mydatabase>` 자리 표시자를 MySQL 서버 및 데이터베이스의 값으로 바꿉니다.
3. 파일을 *C:\pythonmysql\createtable.py* 또는 */home/username/pythonmysql/createtable.py* 와 같이 *.py* 파일 확장명이 포함된 프로젝트 폴더에 저장합니다.
4. 코드를 실행하려면 명령 프롬프트 또는 `bash` 셸을 시작한 후 디렉터리를 사용자의 프로젝트 폴더로 변경합니다(예: `cd pythonmysql`). `python` 명령 다음에 파일 이름을 입력하고(예: `python createtable.py`) Enter 키를 누릅니다. 
   
   > [!NOTE]
   > Windows에서 *python.exe* 를 찾을 수 없으면 PATH 환경 변수에 Python 경로를 추가하거나 *python.exe* 에 대한 전체 경로(예: `C:\python27\python.exe createtable.py`)를 제공해야 할 수 있습니다.

### <a name="create-a-table-and-insert-data"></a>테이블 만들기 및 데이터 삽입

다음 코드를 사용하여 서버 및 데이터베이스에 연결하고, 테이블을 만들고, **INSERT** SQL 문을 사용하여 데이터를 로드합니다. 

코드는 mysql.connector 라이브러리를 가져오고, [connect()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysql-connector-connect.html) 함수를 사용하여 구성 컬렉션의 [인수](https://dev.mysql.com/doc/connector-python/en/connector-python-connectargs.html)를 통해 유연한 서버에 연결합니다. 코드는 연결 시 커서를 사용하고, [cursor.execute()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-execute.html) 메서드는 MySQL 데이터베이스에 대해 SQL 쿼리를 실행합니다.

```python
import mysql.connector
from mysql.connector import errorcode

# Obtain connection string information from the portal

config = {
  'host':'<mydemoserver>.mysql.database.azure.com',
  'user':'<myadmin>',
  'password':'<mypassword>',
  'database':'<mydatabase>'
}

# Construct connection string

try:
   conn = mysql.connector.connect(**config)
   print("Connection established")
except mysql.connector.Error as err:
  if err.errno == errorcode.ER_ACCESS_DENIED_ERROR:
    print("Something is wrong with the user name or password")
  elif err.errno == errorcode.ER_BAD_DB_ERROR:
    print("Database does not exist")
  else:
    print(err)
else:
  cursor = conn.cursor()

# Drop previous table of same name if one exists
cursor.execute("DROP TABLE IF EXISTS inventory;")
print("Finished dropping table (if existed).")

# Create table
cursor.execute("CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);")
print("Finished creating table.")

# Insert some data into table
cursor.execute("INSERT INTO inventory (name, quantity) VALUES (%s, %s);", ("banana", 150))
print("Inserted",cursor.rowcount,"row(s) of data.")
cursor.execute("INSERT INTO inventory (name, quantity) VALUES (%s, %s);", ("orange", 154))
print("Inserted",cursor.rowcount,"row(s) of data.")
cursor.execute("INSERT INTO inventory (name, quantity) VALUES (%s, %s);", ("apple", 100))
print("Inserted",cursor.rowcount,"row(s) of data.")

# Cleanup
conn.commit()
cursor.close()
conn.close()
print("Done.")
```

### <a name="read-data"></a>데이터 읽기

**SELECT** SQL 문을 사용하여 데이터를 연결하고 읽으려면 다음 코드를 사용하세요. 

코드는 mysql.connector 라이브러리를 가져오고, [connect()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysql-connector-connect.html) 함수를 사용하여 구성 컬렉션의 [인수](https://dev.mysql.com/doc/connector-python/en/connector-python-connectargs.html)를 통해 유연한 서버에 연결합니다. 코드는 연결 시 커서를 사용하고, [cursor.execute()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-execute.html) 메서드는 MySQL 데이터베이스에 대해 SQL 쿼리를 실행합니다. 

이 코드는 [fetchall()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-fetchall.html) 메서드를 사용하여 데이터 행을 읽고, 결과 세트를 컬렉션 행에 보관하고, `for` 반복기를 사용하여 행을 반복합니다.

```python
import mysql.connector
from mysql.connector import errorcode

# Obtain connection string information from the portal

config = {
  'host':'<mydemoserver>.mysql.database.azure.com',
  'user':'<myadmin>',
  'password':'<mypassword>',
  'database':'<mydatabase>'
}

# Construct connection string

try:
   conn = mysql.connector.connect(**config)
   print("Connection established")
except mysql.connector.Error as err:
  if err.errno == errorcode.ER_ACCESS_DENIED_ERROR:
    print("Something is wrong with the user name or password")
  elif err.errno == errorcode.ER_BAD_DB_ERROR:
    print("Database does not exist")
  else:
    print(err)
else:
  cursor = conn.cursor()

  # Read data
  cursor.execute("SELECT * FROM inventory;")
  rows = cursor.fetchall()
  print("Read",cursor.rowcount,"row(s) of data.")

  # Print all rows
  for row in rows:
    print("Data row = (%s, %s, %s)" %(str(row[0]), str(row[1]), str(row[2])))

  # Cleanup
  conn.commit()
  cursor.close()
  conn.close()
  print("Done.")
```

### <a name="update-data"></a>데이터 업데이트

**UPDATE** SQL 문을 사용하여 데이터를 연결하고 업데이트하려면 다음 코드를 사용하세요. 

코드는 mysql.connector 라이브러리를 가져오고, [connect()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysql-connector-connect.html) 함수를 사용하여 구성 컬렉션의 [인수](https://dev.mysql.com/doc/connector-python/en/connector-python-connectargs.html)를 통해 유연한 서버에 연결합니다. 코드는 연결 시 커서를 사용하고, [cursor.execute()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-execute.html) 메서드는 MySQL 데이터베이스에 대해 SQL 쿼리를 실행합니다. 

```python
import mysql.connector
from mysql.connector import errorcode

# Obtain connection string information from the portal

config = {
  'host':'<mydemoserver>.mysql.database.azure.com',
  'user':'<myadmin>',
  'password':'<mypassword>',
  'database':'<mydatabase>'
}

# Construct connection string

try:
   conn = mysql.connector.connect(**config)
   print("Connection established")
except mysql.connector.Error as err:
  if err.errno == errorcode.ER_ACCESS_DENIED_ERROR:
    print("Something is wrong with the user name or password")
  elif err.errno == errorcode.ER_BAD_DB_ERROR:
    print("Database does not exist")
  else:
    print(err)
else:
  cursor = conn.cursor()

  # Update a data row in the table
  cursor.execute("UPDATE inventory SET quantity = %s WHERE name = %s;", (200, "banana"))
  print("Updated",cursor.rowcount,"row(s) of data.")

  # Cleanup
  conn.commit()
  cursor.close()
  conn.close()
  print("Done.")
```

### <a name="delete-data"></a>데이터 삭제

다음 코드를 사용하여 데이터를 연결하고 **DELETE** SQL 문을 통해 데이터를 제거합니다. 

코드는 mysql.connector 라이브러리를 가져오고, [connect()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysql-connector-connect.html) 함수를 사용하여 구성 컬렉션의 [인수](https://dev.mysql.com/doc/connector-python/en/connector-python-connectargs.html)를 통해 유연한 서버에 연결합니다. 코드는 연결 시 커서를 사용하고, [cursor.execute()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-execute.html) 메서드는 MySQL 데이터베이스에 대해 SQL 쿼리를 실행합니다. 

```python
import mysql.connector
from mysql.connector import errorcode

# Obtain connection string information from the portal

config = {
  'host':'<mydemoserver>.mysql.database.azure.com',
  'user':'<myadmin>',
  'password':'<mypassword>',
  'database':'<mydatabase>'
}

# Construct connection string

try:
   conn = mysql.connector.connect(**config)
   print("Connection established.")
except mysql.connector.Error as err:
  if err.errno == errorcode.ER_ACCESS_DENIED_ERROR:
    print("Something is wrong with the user name or password.")
  elif err.errno == errorcode.ER_BAD_DB_ERROR:
    print("Database does not exist.")
  else:
    print(err)
else:
  cursor = conn.cursor()

  # Delete a data row in the table
  cursor.execute("DELETE FROM inventory WHERE name=%(param1)s;", {'param1':"orange"})
  print("Deleted",cursor.rowcount,"row(s) of data.")

  # Cleanup
  conn.commit()
  cursor.close()
  conn.close()
  print("Done.")
```

## <a name="next-steps"></a>다음 단계
- [Azure Database for MySQL -유연한 서버에서 TLS(Transport Layer Security) 1.2를 사용하는 암호화된 연결](./how-to-connect-tls-ssl.md).
- [Azure Database for MySQL 유연한 서버의 네트워킹](./concepts-networking.md)에 대해 자세히 알아봅니다.
- [Azure Portal을 사용하여 Azure Database for MySQL 유연한 서버 방화벽 규칙 만들기 및 관리](./how-to-manage-firewall-portal.md).
- [Azure Portal을 사용하여 Azure Database for MySQL 유연한 서버 가상 네트워크 만들기 및 관리](./how-to-manage-virtual-network-portal.md).
