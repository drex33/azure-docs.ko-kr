---
title: Azure CLI를 사용하여 Azure Database for MySQL - 유연한 서버 복원
description: 이 문서에서는 Azure CLI를 통해 Azure Database for MySQL에서 복원 작업을 수행하는 방법을 설명합니다.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 04/01/2021
ms.openlocfilehash: ac6a6964c738cfb970b7bd65a6d7e3b12f796b2a
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "128705601"
---
# <a name="point-in-time-restore-of-a-azure-database-for-mysql---flexible-server-with-azure-cli"></a>Azure CLI를 사용하여 Azure Database for MySQL - 유연한 서버의 특정 시점 복원

[[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]

> [!IMPORTANT]
> Azure Database for MySQL - 유연한 서버는 현재 공개 미리 보기로 제공됩니다.

이 문서에서는 백업을 사용하여 유연한 서버에서 지정 시간 복구를 수행하는 단계별 절차를 제공합니다.

## <a name="prerequisites"></a>사전 요구 사항

- 활성 구독이 있는 Azure 계정. 

    [!INCLUDE [flexible-server-free-trial-note](../includes/flexible-server-free-trial-note.md)]
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

- MySQL Flexible Server를 아직 만들지 않은 경우 ```az mysql flexible-server create``` 명령을 사용하여 만듭니다.

    ```azurecli
    az mysql flexible-server create --resource-group myresourcegroup --name myservername
    ```

## <a name="restore-a-server-from-backup-to-a-new-server"></a>백업에서 새 서버로 서버 복원

다음 명령을 실행하여 서버를 가장 빠른 기존 백업으로 복원할 수 있습니다.

**사용 현황**
```azurecli
az mysql flexible-server restore --restore-time
                                 --source-server
                                 [--ids]
                                 [--location]
                                 [--name]
                                 [--no-wait]
                                 [--resource-group]
                                 [--subscription]
```

**예:** 이 ```2021-03-03T13:10:00Z``` 백업 스냅샷에서 서버를 복원합니다.

```azurecli
az mysql server restore \
--name mydemoserver-restored \
--resource-group myresourcegroup \
--restore-point-in-time "2021-03-03T13:10:00Z" \
--source-server mydemoserver
```
복원하는 데 걸리는 시간은 서버에 저장된 데이터의 크기에 따라 달라집니다.

## <a name="perform-post-restore-tasks"></a>복원 후 작업 수행
복원이 완료된 후 다음 작업을 수행하여 사용자와 애플리케이션을 다시 실행되도록 해야 합니다.

- 새 서버가 원래 서버를 교체하기 위한 것이라면 클라이언트와 클라이언트 애플리케이션을 새 서버로 리디렉션합니다.
- 사용자가 연결할 수 있도록 적합한 VNet 규칙이 설정되었는지 확인합니다. 이러한 규칙은 원래 서버에서 복사되지 않습니다.
- 적절한 로그인 및 데이터베이스 수준 권한이 있는지 확인합니다.
- 새로운 복원 서버에 대해 적절한 경고를 구성합니다.

## <a name="next-steps"></a>다음 단계
[비즈니스 연속성](concepts-business-continuity.md) 알아보기

