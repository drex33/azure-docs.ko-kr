---
title: '빠른 시작: Azure CLI를 사용하여 연결 - Azure Database for PostgreSQL - 유연한 서버'
description: 이 빠른 시작에서는 Azure Database for PostgreSQL - 유연한 서버를 사용하여 Azure CLI에 연결하는 여러 방법을 제공합니다.
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.custom: mvc, devx-track-azurecli, mode-other
ms.topic: quickstart
ms.date: 03/06/2021
ms.openlocfilehash: a070878362d60d052ddc57047e5d7c69a40d7d2b
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133038616"
---
# <a name="quickstart-connect-and-query-with-azure-cli--with-azure-database-for-postgresql---flexible-server"></a>빠른 시작: Azure Database for PostgreSQL - 유연한 서버를 사용하여 Azure CLI로 연결 및 쿼리

> [!IMPORTANT]
> Azure Database for PostgreSQL 유연한 서버는 현재 공개 미리 보기로 제공됩니다.

이 빠른 시작에서는 ```az postgres flexible-server connect```에서 Azure CLI를 사용하여 Azure Database for PostgreSQL 유연한 서버에 연결하고 ```az postgres flexible-server execute``` 명령을 통해 단일 쿼리 또는 sql 파일을 실행하는 방법을 보여줍니다. 이 명령을 사용하면 데이터베이스 서버에 대한 연결을 테스트하고 쿼리를 실행할 수 있습니다. 대화형 모드를 사용하여 여러 쿼리를 실행할 수도 있습니다. 


## <a name="prerequisites"></a>필수 구성 요소
- Azure 계정. 계정이 없으면 [체험 계정을 얻습니다](https://azure.microsoft.com/free/).
- [Azure CLI](/cli/azure/install-azure-cli) 최신 버전(2.20.0 이상) 설치
- ```az login``` 명령을 통해 Azure CLI를 사용하여 로그인 
- ```az config param-persist on```을 사용하여 매개 변수 지속성을 설정합니다. 매개 변수 지속성은 리소스 그룹이나 위치와 같은 많은 인수를 반복하지 않고도 로컬 컨텍스트를 사용할 수 있도록 도와줍니다.

## <a name="create-an-postgresql-flexible-server"></a>PostgreSQL 유연한 서버 만들기

먼저 관리형 PostgreSQL 서버를 만듭니다. [Azure Cloud Shell](https://shell.azure.com/)에서 다음 스크립트를 실행하고 이 명령에서 생성된 **서버 이름**, **사용자 이름** 및 **암호** 를 기록해 둡니다.

```azurecli
az postgres flexible-server create --public-access <your-ip-address>
```
이 명령에 대한 추가 인수를 제공하여 사용자 지정할 수 있습니다. [az postgres flexible-server create](/cli/azure/postgres/flexible-server#az_postgres_flexible_server_create)에 대한 모든 인수를 참조하세요.

## <a name="view-all-the-arguments"></a>모든 인수 보기
```--help``` 인수를 사용하여 이 명령에 대한 모든 인수를 볼 수 있습니다. 

```azurecli
az postgresql flexible-server connect --help
```

## <a name="test-database-server-connection"></a>데이터베이스 서버 연결 테스트
명령을 사용하여 다음 개발 환경에서 데이터베이스에 대한 연결을 테스트하고 유효성을 검사할 수 있습니다.

```azurecli
az postgres flexible-server connect -n <servername> -u <username> -p "<password>" -d <databasename>
```
**예제:** 
```azurecli
az postgres flexible-server connect -n postgresdemoserver -u dbuser -p "dbpassword" -d postgres
```
연결이 성공하면 출력이 표시됩니다.
```output
Command group 'postgres flexible-server' is in preview and under development. Reference and support levels: https://aka.ms/CLI_refstatus
Successfully connected to postgresdemoserver.
Local context is turned on. Its information is saved in working directory C:\mydir. You can run `az local-context off` to turn it off.
Your preference of  are now saved to local context. To learn more, type in `az local-context --help`
```

연결에 실패하면 다음 솔루션을 시도해 보세요.
- 클라이언트 컴퓨터에서 포트 5432가 열려 있는지 확인합니다.
- 서버 관리자의 사용자 이름 및 암호가 올바른 경우
- 클라이언트 컴퓨터에 대한 방화벽 규칙을 구성한 경우
- 가상 네트워킹에서 프라이빗 액세스로 서버를 구성한 경우 클라이언트 컴퓨터가 동일한 가상 네트워크에 있는지 확인합니다.

## <a name="run-multiple-queries-using-interactive-mode"></a>대화형 모드를 사용하여 여러 쿼리 실행
**대화형** 모드를 사용하여 여러 쿼리를 실행할 수 있습니다. 대화형 모드를 활성화하려면 다음 명령을 실행합니다.

```azurecli
az postgres flexible-server connect -n <servername> -u <username> -p "<password>" -d <databasename>
```

**예제:**

```azurecli
az postgres flexible-server connect -n postgresdemoserver -u dbuser -p "dbpassword" -d flexibleserverdb --interactive
```

아래와 같이 **psql** 셸 환경이 표시됩니다.

```bash
Command group 'postgres flexible-server' is in preview and under development. Reference and support levels: https://aka.ms/CLI_refstatus
Password for earthyTurtle7:
Server: PostgreSQL 12.5
Version: 3.0.0
Chat: https://gitter.im/dbcli/pgcli
Home: http://pgcli.com
postgres> create database pollsdb;
CREATE DATABASE
Time: 0.308s
postgres> exit
Goodbye!
Local context is turned on. Its information is saved in working directory C:\sunitha. You can run `az local-context off` to turn it off.
Your preference of  are now saved to local context. To learn more, type in `az local-context --help`
```

## <a name="run-single-query"></a>단일 쿼리 실행
```--querytext``` 인수, ```-q```를 사용하여 명령을 통해 단일 쿼리를 실행할 수 있습니다

```azurecli
az postgres flexible-server execute -n <server-name> -u <username> -p "<password>" -d <database-name> -q "<query-text>"
```

**예제:** 
```azurecli
az postgres flexible-server execute -n postgresdemoserver -u dbuser -p "dbpassword" -d flexibleserverdb -q "select * from table1;" --output table
```

아래와 같이 출력이 표시됩니다.

```output
Command group 'postgres flexible-server' is in preview and under development. Reference and support levels: https://aka.ms/CLI_refstatus
Successfully connected to postgresdemoserver.
Ran Database Query: 'select * from table1;'
Retrieving first 30 rows of query output, if applicable.
Closed the connection postgresdemoserver.
Local context is turned on. Its information is saved in working directory C:\mydir. You can run `az local-context off` to turn it off.
Your preference of  are now saved to local context. To learn more, type in `az local-context --help`
Txt    Val
-----  -----
test   200
test   200
test   200
test   200
test   200
test   200
test   200
```

## <a name="run-sql-file"></a>SQL 파일 실행
```--file-path``` 인수, ```-f```를 사용하여 명령을 통해 sql 파일을 실행할 수 있습니다.

```azurecli
az postgres flexible-server execute -n <server-name> -u <username> -p "<password>" -d <database-name> --file-path "<file-path>"
```

**예제:** 
```azurecli
az postgres flexible-server execute -n postgresdemoserver -u dbuser -p "dbpassword" -d flexibleserverdb -f "./test.sql"
```

아래와 같이 출력이 표시됩니다.

```output
Command group 'postgres flexible-server' is in preview and under development. Reference and support levels: https://aka.ms/CLI_refstatus
Running sql file '.\test.sql'...
Successfully executed the file.
Closed the connection to postgresdemoserver.
```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [서버 관리](./how-to-manage-server-cli.md)
