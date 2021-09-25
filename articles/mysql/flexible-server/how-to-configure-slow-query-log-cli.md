---
title: Azure CLI를 사용하여 감사 로그 및 느린 쿼리 로그 구성 - Azure Database for MySQL - 유연한 서버
description: 이 문서에서는 Azure CLI에서 Azure Database for MySQL 유연한 서버의 느린 쿼리 로그를 구성 및 액세스하는 방법을 설명합니다.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 03/30/2021
ms.openlocfilehash: e13470bedf58d0012dfbdbb64f3ef12b085621e4
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "128634099"
---
# <a name="configure-slow-query-logs-for-azure-database-for-mysql---flexible-server-using-the-azure-cli"></a>Azure CLI를 사용하여 Azure Database for MySQL 유연한 서버의 느린 쿼리 로그 구성

[[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]

> [!IMPORTANT]
> Azure Database for MySQL - 유연한 서버는 현재 공개 미리 보기로 제공됩니다.

이 문서에서는 Azure CLI를 사용하여 MySQL 유연한 서버의 [느린 쿼리 로그](concepts-slow-query-logs.md)를 구성하는 방법을 보여 줍니다. 

## <a name="prerequisites"></a>필수 구성 요소

- 활성 구독이 있는 Azure 계정. 

    [!INCLUDE [flexible-server-free-trial-note](../includes/flexible-server-free-trial-note.md)]
- 최신 버전으로 Azure CLI를 설치하거나 업그레이드합니다. [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요.
- [az login](/cli/azure/reference-index#az_login) 명령을 사용하여 Azure 계정에 로그인합니다. Azure 계정에 대한 **구독 ID** 를 참조하는 **id** 속성을 기록해 둡니다.

    ```azurecli-interactive
    az login
    ````

- 구독이 여러 개인 경우 ```az account set``` 명령을 사용하여 서버를 만들려는 적절한 구독을 선택합니다.

    ```azurecli
    az account set --subscription <subscription id>
    ```

- MySQL Flexible Server를 아직 만들지 않은 경우 ```az mysql flexible-server create``` 명령을 사용하여 만듭니다.

    ```azurecli
    az mysql flexible-server create --resource-group myresourcegroup --name myservername
    ```

## <a name="configure-slow-query-logs"></a>느린 쿼리 로그 구성

> [!IMPORTANT]
> 서버 성능이 크게 영향을 받지 않도록 감사 목적에 필요한 이벤트 유형과 사용자만 로그하는 것이 좋습니다.

서버에 대한 느린 쿼리 로그를 사용하도록 설정하고 구성합니다.

```azurecli
# Turn on statement level log

az mysql flexible-server parameter set \
--name log_statement \
--resource-group myresourcegroup \
--server-name mydemoserver \
--value all


# Set log_min_duration_statement time to 10 sec

# This setting will log all queries executing for more than 10 sec. Please adjust this threshold based on your definition for slow queries

az mysql server configuration set \
--name log_min_duration_statement \
--resource-group myresourcegroup \
--server mydemoserver \
--value 10000

# Enable Slow query logs



az mysql flexible-server parameter set \
--name slow_query_log \
--resource-group myresourcegroup \
--server-name mydemoserver \
--value ON
```

## <a name="next-steps"></a>다음 단계

- [느린 쿼리 로그](concepts-slow-query-logs.md)에 대한 자세한 정보
