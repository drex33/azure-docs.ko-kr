---
title: Azure CLI를 사용하여 Azure Database for MySQL 유연한 서버에서 읽기 복제본을 관리합니다.
description: Azure CLI를 사용하여 Azure Database for MySQL 유연한 서버에서 읽기 복제본을 설정하고 관리하는 방법을 알아봅니다.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 06/17/2021
ms.custom: devx-track-azurecli
ms.openlocfilehash: adffb4edf7f689002cab7eae86388ff18ac04027
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/30/2021
ms.locfileid: "122642188"
---
# <a name="how-to-create-and-manage-read-replicas-in-azure-database-for-mysql-flexible-server-using-the-azure-cli"></a>Azure CLI를 사용하여 Azure Database for MySQL 유연한 서버에서 읽기 복제본을 만들고 관리하는 방법

[[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]

> [!IMPORTANT]
> Azure Database for MySQL의 읽기 복제본 - 유연한 서버는 미리 보기 상태입니다.

이 문서에서는 Azure CLI를 사용하여 Azure Database for MySQL 유연한 서버에서 읽기 복제본을 만들고 관리하는 방법을 알아봅니다. 읽기 복제본에 대한 자세한 내용은 [개요](concepts-read-replicas.md)를 참조하세요.

> [!Note]
>
> * 고가용성 지원 서버에서는 복제본이 지원되지 않습니다. 
>
> * 주 서버에서 GTID를 사용하는 경우(`gtid_mode` = ON) 새로 만든 복제본도 GTID를 사용하도록 설정하고 GTID 기반 복제를 사용합니다. 자세한 내용은 [GTID(글로벌 트랜잭션 식별자)](concepts-read-replicas.md#global-transaction-identifier-gtid)를 참조하세요.

## <a name="azure-cli"></a>Azure CLI

Azure CLI를 사용하여 읽기 복제본을 생성하고 관리할 수 있습니다.

### <a name="prerequisites"></a>필수 구성 요소

- [Azure CLI 2.0 설치](/cli/azure/install-azure-cli)
- [Azure Database for MySQL 유연한 서버](quickstart-create-server-cli.md)를 원본 서버로 사용합니다.

### <a name="create-a-read-replica"></a>읽기 복제본 만들기

> [!IMPORTANT]
>기존 복제본이 없는 원본에 대한 복제본을 만드는 경우 원본이 먼저 다시 시작하여 자체적으로 복제할 준비를 합니다. 이를 고려하고 사용량이 적은 기간 동안 이러한 작업을 수행합니다.

다음 명령을 사용하여 읽기 복제본 서버를 만들 수 있습니다.

```azurecli-interactive
az mysql flexible-server replica create --replica-name mydemoreplicaserver --source-server mydemoserver --resource-group myresourcegroup
``` 

> [!NOTE]
> 읽기 복제본은 원본과 동일한 서버 구성으로 만들어집니다. 복제본이 생성된 후에 복제본 서버 구성을 변경할 수 있습니다. 복제본 서버는 항상 원본 서버와 동일한 리소스 그룹, 동일한 위치 및 동일한 구독에 생성됩니다. 다른 리소스 그룹 또는 다른 구독에 복제본 서버를 만들려면 복제본 서버를 만든 후에 [이동](../../azure-resource-manager/management/move-resource-group-and-subscription.md)할 수 있습니다. 복제본이 원본을 따라갈 수 있도록 복제본 서버의 구성을 원본과 동등하거나 더 큰 값으로 설정하기를 권합니다.


### <a name="list-replicas-for-a-source-server"></a>원본 서버에 대한 복제본 나열

지정된 원본 서버에 대한 모든 복제본을 보려면 다음 명령을 실행합니다. 

```azurecli-interactive
az mysql flexible-server replica list --server-name mydemoserver --resource-group myresourcegroup
```

### <a name="stop-replication-to-a-replica-server"></a>복제본 서버로의 복제 중지

> [!IMPORTANT]
>서버로의 복제는 중지하고 나면 취소할 수 없습니다. 원본과 복제본 사이의 복제가 중단된 경우, 중단을 취소할 수 없습니다. 복제를 중지하고 나면 복제본 서버는 독립 실행형 서버가 되어 읽기와 쓰기를 모두 지원합니다. 이 서버를 다시 복제본으로 설정할 수는 없습니다.

다음 명령을 사용하여 읽기 복제본 서버에 대한 복제를 중지할 수 있습니다.

```azurecli-interactive
az mysql flexible-server replica stop-replication --replica-name mydemoreplicaserver --resource-group myresourcegroup
```

### <a name="delete-a-replica-server"></a>복제본 서버 삭제

읽기 복제본 서버 삭제는 **[az mysql server delete](/cli/azure/mysql/server)** 명령을 실행하여 수행할 수 있습니다.

```azurecli-interactive
az mysql flexible-server delete --resource-group myresourcegroup --name mydemoreplicaserver
```

### <a name="delete-a-source-server"></a>원본 서버 삭제

> [!IMPORTANT]
>원본 서버를 삭제하면 모든 복제본 서버에 대한 복제가 중지되며 원본 서버 자체도 삭제됩니다. 그러면 복제본 서버는 읽기와 쓰기를 모두 지원하는 독립 실행형 서버로 설정됩니다.

원본 서버를 삭제하려면 **[az mysql flexible-server delete](/cli/azure/mysql/flexible-server)** 명령을 실행합니다.

```azurecli-interactive
az mysql flexible-server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="next-steps"></a>다음 단계

- [읽기 복제본](concepts-read-replicas.md)에 대해 자세히 알아보기