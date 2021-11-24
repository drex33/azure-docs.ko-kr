---
title: '빠른 시작: Python을 사용하여 - Azure Database for PostgreSQL - 단일 서버에 연결'
description: 이 빠른 시작에서는 Azure Database for PostgreSQL - 단일 서버의 데이터를 연결하고 쿼리하는 데 사용할 수 있는 Python 코드 샘플을 제공합니다.
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.custom: mvc, devcenter, devx-track-python, mode-other
ms.devlang: python
ms.topic: quickstart
ms.date: 10/28/2020
ms.openlocfilehash: 836ec96bc1eebec3814b78a5282cde8fa0dd13a3
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133066305"
---
# <a name="quickstart-use-python-to-connect-and-query-data-in-azure-database-for-postgresql---single-server"></a>빠른 시작: Python을 사용하여 Azure Database for PostgreSQL - 단일 서버의 데이터 연결 및 쿼리

이 빠른 시작에서는 Azure Database for PostgreSQL 단일 서버에서 데이터베이스에 연결하고 SQL 문을 실행하여 macOS, Ubuntu Linux 또는 Windows에서 Python을 사용하여 쿼리하는 방법에 대해 알아봅니다.

> [!TIP]
> PostgreSQL을 사용하여 Django 애플리케이션을 빌드하려는 경우 자습서를 체크 아웃하고 [PostgreSQL 자습서를 사용하여 Django 웹앱을 배포](../app-service/tutorial-python-postgresql-app.md)합니다.


## <a name="prerequisites"></a>사전 요구 사항
이 빠른 시작에는 다음이 필요합니다.

- 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free).
- [Azure Portal](./quickstart-create-server-database-portal.md)을 사용하여 Azure Database for PostgreSQL 단일 서버 만들기 <br/> 또는 [Azure CLI](./quickstart-create-server-database-azure-cli.md)를 만듭니다(없는 경우).
- 퍼블릭 또는 프라이빗 액세스를 사용하는지 여부에 따라 아래 작업 중 **하나** 를 완료하여 연결을 활성화합니다.

  |작업| 연결 방법|방법 가이드|
  |:--------- |:--------- |:--------- |
  | **방화벽 규칙 구성** | 공용 | [포털](./howto-manage-firewall-using-portal.md) <br/> [CLI](./howto-manage-firewall-using-cli.md)|
  | **서비스 엔드포인트 구성** | 공용 | [포털](./howto-manage-vnet-using-portal.md) <br/> [CLI](./howto-manage-vnet-using-cli.md)|
  | **프라이빗 링크 구성** | Private | [포털](./howto-configure-privatelink-portal.md) <br/> [CLI](./howto-configure-privatelink-cli.md) |

- [Python](https://www.python.org/downloads/) 2.7 또는 3.6 이상

- 최신 [pip](https://pip.pypa.io/en/stable/installing/) 패키지 설치 관리자.
- 터미널 또는 명령 프롬프트 창에서 `pip install psycopg2-binary`를 사용하여 [psycopg2](https://pypi.python.org/pypi/psycopg2-binary/)를 설치합니다. 자세한 내용은 [설치 방법 `psycopg2`](https://www.psycopg.org/docs/install.html)을 참조하세요.

## <a name="get-database-connection-information"></a>데이터베이스 연결 정보 가져오기
Azure Database for PostgreSQL 데이터베이스에 연결하려면 정규화된 서버 이름 및 로그인 자격 증명이 필요합니다. Azure Portal에서 이 정보를 가져올 수 있습니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인하고 Azure Database for PostgreSQL 서버 이름을 선택합니다.
1. 서버의 **개요** 페이지에서 정규화된 **서버 이름** 및 **관리자 사용자 이름** 을 복사합니다. 정규화된 **서버 이름** 은 항상 *\<my-server-name>my-server-name>.postgres.database.azure.com* 형식을 가지며, **관리 사용자 이름** 은 항상 *\<my-admin-username>@\<my-server-name>* 형식을 갖습니다.

   관리자 암호도 필요합니다. 이 암호를 잊어버린 경우 이 페이지에서 다시 설정할 수 있습니다.

   :::image type="content" source="./media/connect-python/1-connection-string.png" alt-text="Azure Database for PostgreSQL 서버 이름":::

> [!IMPORTANT]
>  다음 값을 바꿉니다.
>   - `<server-name>` 및 `<admin-username>`을 Azure Portal에서 복사한 값으로 바꿉니다.
>   - `<admin-password>`를 서버 암호로 바꿉니다.
>   - `<database-name>` *postgres* 라는 기본 데이터베이스는 서버를 만들 때 자동으로 생성되었습니다. SQL 명령을 사용하여 해당 데이터베이스의 이름을 바꾸거나 [새 데이터베이스를 생성](https://www.postgresql.org/docs/current/sql-createdatabase.html)할 수 있습니다.

## <a name="step-1-connect-and-insert-data"></a>1단계: 데이터 연결 및 삽입
다음 코드 예제에서는
-  [psycopg2.connect](https://www.psycopg.org/docs/connection.html) 함수를 사용하여 Azure Database for PostgreSQL 데이터베이스에 연결하고 SQL **INSERT** 문을 사용하여 데이터를 로드합니다.
- [cursor.execute](https://www.psycopg.org/docs/cursor.html#execute) 함수는 데이터베이스에 대해 SQL 쿼리를 실행합니다.

```Python
import psycopg2

# Update connection string information
host = "<server-name>"
dbname = "<database-name>"
user = "<admin-username>"
password = "<admin-password>"
sslmode = "require"

# Construct connection string
conn_string = "host={0} user={1} dbname={2} password={3} sslmode={4}".format(host, user, dbname, password, sslmode)
conn = psycopg2.connect(conn_string)
print("Connection established")

cursor = conn.cursor()

# Drop previous table of same name if one exists
cursor.execute("DROP TABLE IF EXISTS inventory;")
print("Finished dropping table (if existed)")

# Create a table
cursor.execute("CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);")
print("Finished creating table")

# Insert some data into the table
cursor.execute("INSERT INTO inventory (name, quantity) VALUES (%s, %s);", ("banana", 150))
cursor.execute("INSERT INTO inventory (name, quantity) VALUES (%s, %s);", ("orange", 154))
cursor.execute("INSERT INTO inventory (name, quantity) VALUES (%s, %s);", ("apple", 100))
print("Inserted 3 rows of data")

# Clean up
conn.commit()
cursor.close()
conn.close()
```

코드가 성공적으로 실행되면 다음과 같은 출력이 생성됩니다.

:::image type="content" source="media/connect-python/2-example-python-output.png" alt-text="명령줄 출력":::


[문제가 있나요? 알려주세요.](https://aka.ms/postgres-doc-feedback)

## <a name="step-2-read-data"></a>2단계: 데이터 읽기
다음 코드 예제에서는 Azure Database for PostgreSQL 데이터베이스에 연결하고
- SQL **SELECT** 에서 [cursor.execute](https://www.psycopg.org/docs/cursor.html#execute)를 사용하여 데이터를 읽습니다.
- [cursor.fetchall()](https://www.psycopg.org/docs/cursor.html#cursor.fetchall)은 쿼리를 수락하고 다음을 사용하여 반복할 결과 집합을 반환합니다.

```Python

# Fetch all rows from table
cursor.execute("SELECT * FROM inventory;")
rows = cursor.fetchall()

# Print all rows
for row in rows:
    print("Data row = (%s, %s, %s)" %(str(row[0]), str(row[1]), str(row[2])))


```
[문제가 있나요? 알려주세요.](https://aka.ms/postgres-doc-feedback)

## <a name="step-3-update-data"></a>3단계: 데이터 업데이트
다음 코드 예제에서는 SQL **UPDATE** 문에서 [cursor.execute](https://www.psycopg.org/docs/cursor.html#execute)를 사용하여 데이터를 업데이트합니다.

```Python

# Update a data row in the table
cursor.execute("UPDATE inventory SET quantity = %s WHERE name = %s;", (200, "banana"))
print("Updated 1 row of data")

```
[문제가 있나요? 알려주세요.](https://aka.ms/postgres-doc-feedback)

## <a name="step-5-delete-data"></a>5단계: 데이터 삭제
다음 코드 예제에서는 SQL **DELETE** 에서 [cursor.execute](https://www.psycopg.org/docs/cursor.html#execute)를 실행하여 이전에 삽입된 인벤토리 항목을 삭제합니다.

```Python

# Delete data row from table
cursor.execute("DELETE FROM inventory WHERE name = %s;", ("orange",))
print("Deleted 1 row of data")

```

[문제가 있나요? 알려주세요.](https://aka.ms/postgres-doc-feedback)

## <a name="clean-up-resources"></a>리소스 정리

이 빠른 시작에서 사용된 모든 리소스를 정리하려면 다음 명령을 사용하여 리소스 그룹을 삭제합니다.

```azurecli
az group delete \
    --name $AZ_RESOURCE_GROUP \
    --yes
```

## <a name="next-steps"></a>다음 단계
> [!div class="nextstepaction"]
> [포털을 사용하여 Azure Database for MySQL 서버 관리](./howto-create-manage-server-portal.md)<br/>

> [!div class="nextstepaction"]
> [CLI를 사용하여 Azure Database for MySQL 서버 관리](./how-to-manage-server-cli.md)<br/>

[원하는 항목을 찾을 수 없나요? 알려주세요.](https://aka.ms/postgres-doc-feedback)
