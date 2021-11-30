---
title: 중지/시작 - Azure CLI - Azure Database for PostgreSQL Flexible Server
description: 이 문서에서는 Azure CLI를 통해 Azure Database for PostgreSQL에서 작업을 중지/시작하는 방법을 설명합니다.
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.topic: how-to
ms.date: 11/30/2021
ms.openlocfilehash: 918e3b79175cf627be1fc12b72fdc586b60acedf
ms.sourcegitcommit: dcf3424d7149fceaea0340eb0657baa2c27882a5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/30/2021
ms.locfileid: "133265332"
---
# <a name="stopstart-azure-database-for-postgresql---flexible-server-using-azure-cli"></a>Azure CLI를 사용 하 Azure Database for PostgreSQL 유연한 서버 중지/시작



이 문서에서는 Azure CLI를 사용하여 유연한 서버를 다시 시작, 시작 및 중지하는 방법을 보여 줍니다.

## <a name="prerequisites"></a>사전 요구 사항

- Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.
- 최신 버전으로 Azure CLI를 설치하거나 업그레이드합니다. [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요.
-  [az login](/cli/azure/reference-index#az_login) 명령을 사용하여 Azure 계정에 로그인합니다. Azure 계정에 대한 **구독 ID** 를 참조하는 **id** 속성을 기록해 둡니다.

    ```azurecli-interactive
    az login
    ````

- 구독이 여러 개인 경우 ```az account set``` 명령을 사용하여 서버를 만들려는 적절한 구독을 선택합니다.
`
    ```azurecli
    az account set --subscription <subscription id>
    ```

- PostgreSQL Flexible Server를 아직 만들지 않은 경우 ```az postgres flexible-server create``` 명령을 사용하여 만듭니다.

    ```azurecli
    az postgres flexible-server create --resource-group myresourcegroup --name myservername
    ```

## <a name="stop-a-running-server"></a>실행 중인 서버 중지
서버를 중지하려면 ```az postgres flexible-server stop``` 명령을 실행합니다. [로컬 컨텍스트](/cli/azure/config/param-persist)를 사용하는 경우 인수를 제공할 필요가 없습니다.

**사용법:**
```azurecli
az postgres flexible-server stop  [--name]
                               [--resource-group]
                               [--subscription]
```

**로컬 컨텍스트가 없는 예:**
```azurecli
az postgres flexible-server stop  --resource-group --name myservername
```

**로컬 컨텍스트가 있는 예:**
```azurecli
az postgres flexible-server stop
```

## <a name="start-a-stopped-server"></a>중지된 서버 시작
서버를 시작하려면 ```az postgres flexible-server start``` 명령을 실행합니다. [로컬 컨텍스트](/cli/azure/config/param-persist)를 사용하는 경우 인수를 제공할 필요가 없습니다.

**사용법:**
```azurecli
az postgres flexible-server start [--name]
                               [--resource-group]
                               [--subscription]
```

**로컬 컨텍스트가 없는 예:**
```azurecli
az postgres flexible-server start  --resource-group --name myservername
```

**로컬 컨텍스트가 있는 예:**
```azurecli
az postgres flexible-server start
```

> [!IMPORTANT]
> 서버가 성공적으로 다시 시작되면 이제 유연한 서버에서 모든 관리 작업을 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계
- [Azure Database for PostgreSQL Flexible Server 다시 시작](./how-to-restart-server-cli.md)에 대해 자세히 알아보기


