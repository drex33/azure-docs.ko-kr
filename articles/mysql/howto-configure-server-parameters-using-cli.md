---
title: 서버 매개 변수 구성 - Azure CLI - Azure Database for MySQL
description: 이 문서에서는 Azure CLI 명령줄 유틸리티를 사용하여 Azure Database for MySQL에서 서비스 매개 변수를 구성하는 방법을 설명합니다.
author: savjani
ms.author: pariks
ms.service: mysql
ms.devlang: azurecli
ms.topic: how-to
ms.date: 10/1/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 08a8a26cd0420f5c856eebf2063b0ae4f14beb7d
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/30/2021
ms.locfileid: "122642171"
---
# <a name="configure-server-parameters-in-azure-database-for-mysql-using-the-azure-cli"></a>Azure CLI를 사용하여 Azure Database for MySQL에서 서버 매개 변수 구성

[!INCLUDE[applies-to-mysql-single-server](includes/applies-to-mysql-single-server.md)]
Azure 명령줄 유틸리티인 Azure CLI를 사용하여 Azure Database for MySQL 서버의 구성 매개 변수를 나열하고, 표시하며, 업데이트할 수 있습니다. 엔진 구성의 하위 집합은 서버 수준에서 노출되고 수정할 수 있습니다. 

>[!Note]
> 서버 매개 변수는 서버 수준에서 전역적으로 업데이트될 수 있으며 [Azure CLI](./howto-configure-server-parameters-using-cli.md), [PowerShell](./howto-configure-server-parameters-using-powershell.md) 또는 [Azure Portal](./howto-server-parameters.md)을 사용합니다.

## <a name="prerequisites"></a>필수 구성 요소
이 방법 가이드를 단계별로 실행하려면 다음이 필요합니다.
- [Azure Database for MySQL 서버](quickstart-create-mysql-server-database-using-azure-cli.md)
- [Azure CLI](/cli/azure/install-azure-cli) 명령줄 유틸리티 또는 브라우저의 Azure Cloud Shell

## <a name="list-server-configuration-parameters-for-azure-database-for-mysql-server"></a>Azure Database for MySQL에 대한 서버 구성 매개 변수 나열
서버의 수정 가능한 모든 매개 변수와 해당 값을 나열하려면 [az mysql server configuration list](/cli/azure/mysql/server/configuration#az_mysql_server_configuration_list) 명령을 실행합니다.

**myresourcegroup** 리소스 그룹에 있는 **mydemoserver.mysql.database.azure.com** 서버에 대한 서버 구성 매개 변수를 나열할 수 있습니다.
```azurecli-interactive
az mysql server configuration list --resource-group myresourcegroup --server mydemoserver
```
나열된 각 매개 변수의 정의를 보려면 [서버 시스템 변수](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html)에서 MySQL 참조 섹션을 참조하세요.

## <a name="show-server-configuration-parameter-details"></a>서버 구성 매개 변수 세부 정보 표시
서버에 대한 특정 구성 매개 변수의 세부 정보를 표시하려면 [az mysql server configuration show](/cli/azure/mysql/server/configuration#az_mysql_server_configuration_show) 명령을 실행합니다.

이 예제에서는 **myresourcegroup** 리소스 그룹에 있는 **mydemoserver.mysql.database.azure.com** 서버에 대한 **slow\_query\_log** 서버 구성 매개 변수의 세부 정보를 보여 줍니다.
```azurecli-interactive
az mysql server configuration show --name slow_query_log --resource-group myresourcegroup --server mydemoserver
```
## <a name="modify-a-server-configuration-parameter-value"></a>서버 구성 매개 변수 값 수정
특정 서버 구성 매개 변수의 값을 수정할 수 있습니다. 그러면 MySQL 서버 엔진에 대한 기본 구성 값이 업데이트됩니다. 구성 값을 업데이트하려면 [az mysql server configuration set](/cli/azure/mysql/server/configuration#az_mysql_server_configuration_set) 명령을 사용합니다. 

**myresourcegroup** 리소스 그룹에 있는 **mydemoserver.mysql.database.azure.com** 서버에 대한 **slow\_query\_log** 서버 구성 매개 변수를 업데이트하려면
```azurecli-interactive
az mysql server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver --value ON
```
구성 매개 변수 값을 다시 설정하려는 경우 선택 사항인 `--value` 매개 변수를 생략합니다. 그러면 서비스에서 기본값을 적용합니다. 위의 예제에서는 다음과 같이 표시됩니다.
```azurecli-interactive
az mysql server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver
```
이 코드는 **slow\_query\_log** 구성을 기본값인 **OFF** 로 다시 설정합니다. 

## <a name="setting-parameters-not-listed"></a>나열되지 않은 매개 변수 설정
업데이트하려는 서버 매개 변수가 Azure Portal에 나열되지 않는 경우 필요에 따라 `init_connect`를 사용하여 연결 수준에서 매개 변수를 설정할 수 있습니다. 이는 서버에 연결하는 각 클라이언트에 대한 서버 매개 변수를 설정합니다. 

리소스 그룹 **myresourcegroup** 에 있는 **mydemoserver.mysql.database.azure.com** 서버의 **init\_connect** 서버 구성 매개 변수를 업데이트하여 문자 집합 등의 값으로 설정합니다.
```azurecli-interactive
az mysql server configuration set --name init_connect --resource-group myresourcegroup --server mydemoserver --value "SET character_set_client=utf8;SET character_set_database=utf8mb4;SET character_set_connection=latin1;SET character_set_results=latin1;"
```

## <a name="working-with-the-time-zone-parameter"></a>표준 시간대 매개 변수 작업

### <a name="populating-the-time-zone-tables"></a>표준 시간대 테이블 채우기

MySQL 명령줄 또는 MySQL Workbench와 같은 도구에서 `mysql.az_load_timezone` 저장 프로시저를 호출하면 서버의 표준 시간대 테이블을 채울 수 있습니다.

> [!NOTE]
> MySQL Workbench에서 `mysql.az_load_timezone` 명령을 실행하는 경우, 먼저 `SET SQL_SAFE_UPDATES=0;`을 사용하여 안전한 업데이트 모드를 꺼야 할 수 있습니다.

```sql
CALL mysql.az_load_timezone();
```

> [!IMPORTANT]
> 표준 시간대 테이블이 제대로 채워지도록 하려면 서버를 다시 시작해야 합니다. 서버를 다시 시작하려면 [Azure Portal](howto-restart-server-portal.md) 또는 [CLI](howto-restart-server-cli.md)를 사용합니다.

사용 가능한 표준 시간대 값을 보려면 다음 명령을 실행합니다.

```sql
SELECT name FROM mysql.time_zone_name;
```

### <a name="setting-the-global-level-time-zone"></a>전역 수준 표준 시간대 설정

전역 수준 표준 시간대는 [az mysql server configuration set](/cli/azure/mysql/server/configuration#az_mysql_server_configuration_set) 명령을 사용하여 설정할 수 있습니다.

다음 명령은 리소스 그룹 **myresourcegroup** 아래에 있는 **mydemoserver.mysql.database.azure.com** 서버의 **time\_zone** 서버 구성 매개 변수를 **US/Pacific** 으로 업데이트합니다.

```azurecli-interactive
az mysql server configuration set --name time_zone --resource-group myresourcegroup --server mydemoserver --value "US/Pacific"
```

### <a name="setting-the-session-level-time-zone"></a>세션 수준 표준 시간대 설정

세션 수준 표준 시간대는 MySQL 명령줄 또는 MySQL Workbench와 같은 도구에서 `SET time_zone` 명령을 실행하여 설정할 수 있습니다. 아래 예제에서는 표준 시간대를 **US/Pacific** 표준 시간대로 설정합니다.  

```sql
SET time_zone = 'US/Pacific';
```

[날짜 및 시간 함수](https://dev.mysql.com/doc/refman/5.7/en/date-and-time-functions.html#function_convert-tz)에 대한 MySQL 문서를 참조하세요.


## <a name="next-steps"></a>다음 단계

- [Azure Portal에서 서버 매개 변수](howto-server-parameters.md)를 구성하는 방법
