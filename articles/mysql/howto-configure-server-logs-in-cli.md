---
title: 느린 쿼리 로그 액세스 - Azure CLI - Azure Database for MySQL
description: 이 문서에서는 Azure CLI를 사용하여 Azure Database for MySQL에서 느린 쿼리 로그에 액세스하는 방법을 설명합니다.
author: savjani
ms.author: pariks
ms.service: mysql
ms.devlang: azurecli
ms.topic: how-to
ms.date: 4/13/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: f1ab084667f073f7cd43dd986ff4cb8b3f039c75
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/30/2021
ms.locfileid: "122642150"
---
# <a name="configure-and-access-slow-query-logs-by-using-azure-cli"></a>Azure CLI를 사용하여 느린 쿼리 로그 구성 및 액세스

[!INCLUDE[applies-to-mysql-single-server](includes/applies-to-mysql-single-server.md)]
Azure 명령줄 유틸리티인 Azure CLI를 사용하여 Azure Database for MySQL 느린 쿼리 로그를 다운로드할 수 있습니다.

## <a name="prerequisites"></a>필수 구성 요소
이 방법 가이드를 단계별로 실행하려면 다음이 필요합니다.
- [Azure Database for MySQL 서버](quickstart-create-mysql-server-database-using-azure-cli.md)
- [Azure CLI](/cli/azure/install-azure-cli) 또는 브라우저의 Azure Cloud Shell

## <a name="configure-logging"></a>로깅 구성
다음 단계를 수행하여 MySQL 느린 쿼리 로그에 액세스하도록 서버를 구성할 수 있습니다.
1. **slow\_query\_log** 매개 변수를 켜기로 설정하여 느린 쿼리 로깅을 켭니다.
2. **log\_output** 을 사용하여 로그를 출력할 위치를 선택합니다. 로컬 스토리지 및 Azure Monitor 진단 로그 모두에 로그를 전송하려면 **파일** 을 선택합니다. 로그를 Azure Monitor 로그에만 전송하려면 **없음** 을 선택합니다.
3. **long\_query\_time** 및 **log\_slow\_admin\_statements** 와 같은 다른 매개 변수를 조정합니다.

Azure CLI를 통해 이러한 매개 변수 값을 설정하는 방법을 알아보려면 [서버 매개 변수를 구성하는 방법](howto-configure-server-parameters-using-cli.md)을 참조하세요.

예를 들어 다음 CLI 명령은 느린 쿼리 로그를 켜기로 설정하고, 긴 쿼리 시간을 10초로 설정한 다음, 느린 관리자 명령문의 로깅을 해제합니다. 마지막으로 검토할 구성 옵션을 나열합니다.
```azurecli-interactive
az mysql server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver --value ON
az mysql server configuration set --name log_output --resource-group myresourcegroup --server mydemoserver --value FILE
az mysql server configuration set --name long_query_time --resource-group myresourcegroup --server mydemoserver --value 10
az mysql server configuration set --name log_slow_admin_statements --resource-group myresourcegroup --server mydemoserver --value OFF
az mysql server configuration list --resource-group myresourcegroup --server mydemoserver
```

## <a name="list-logs-for-azure-database-for-mysql-server"></a>Azure Database for MySQL 서버에 대한 로그 나열
**log_output** 이 "File"로 구성된 경우 서버의 로컬 스토리지에서 로그에 직접 액세스할 수 있습니다. 서버에 대한 사용 가능한 느린 쿼리 로그 파일을 나열하려면 [az mysql server-logs list](/cli/azure/mysql/server-logs#az_mysql_server_logs_list) 명령을 실행합니다.

**myresourcegroup** 리소스 그룹에서 **mydemoserver.mysql.database.azure.com** 서버에 대한 로그 파일을 나열할 수 있습니다. 그런 다음, **log\_files\_list.txt** 라는 텍스트 파일에 로그 파일 목록을 전송합니다.
```azurecli-interactive
az mysql server-logs list --resource-group myresourcegroup --server mydemoserver > log_files_list.txt
```
## <a name="download-logs-from-the-server"></a>서버에서 로그 다운로드
**log_output** 이 "File"로 구성된 경우 [az mysql server-logs download](/cli/azure/mysql/server-logs#az_mysql_server_logs_download) 명령을 사용하여 서버에서 개별 로그 파일을 다운로드할 수 있습니다.

다음 예제를 사용하여 **myresourcegroup** 리소스 그룹에 있는 **mydemoserver.mysql.database.azure.com** 서버에 대한 특정 로그 파일을 로컬 환경에 다운로드합니다.
```azurecli-interactive
az mysql server-logs download --name 20170414-mydemoserver-mysql.log --resource-group myresourcegroup --server mydemoserver
```

## <a name="next-steps"></a>다음 단계
- Azure Database for MySQL의 [느린 쿼리 로그](concepts-server-logs.md)에 대해 알아보세요.
