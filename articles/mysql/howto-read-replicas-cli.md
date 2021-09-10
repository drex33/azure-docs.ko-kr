---
title: 읽기 복제본 관리 - Azure CLI, REST API - Azure Database for MySQL
description: Azure CLI 또는 REST API를 사용하여 Azure Database for MySQL에서 읽기 복제본을 설정하고 관리하는 방법을 알아봅니다.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 06/17/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: c7f33156394b3dfde100014ace6d8b7f1cbc8caf
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122528551"
---
# <a name="how-to-create-and-manage-read-replicas-in-azure-database-for-mysql-using-the-azure-cli-and-rest-api"></a>Azure CLI 및 REST API를 사용하여 Azure Database for MySQL에서 읽기 복제본을 만들고 관리하는 방법

[!INCLUDE[applies-to-mysql-single-server](includes/applies-to-mysql-single-server.md)]

이 문서에서는 Azure CLI 및 REST API를 사용하여 Azure Database for MySQL 서비스에서 읽기 복제본을 만들고 관리하는 방법을 알아봅니다. 읽기 복제본에 대한 자세한 내용은 [개요](concepts-read-replicas.md)를 참조하세요.

## <a name="azure-cli"></a>Azure CLI
Azure CLI를 사용하여 읽기 복제본을 생성하고 관리할 수 있습니다.

### <a name="prerequisites"></a>필수 조건

- [Azure CLI 2.0 설치](/cli/azure/install-azure-cli)
- 원본 서버로 사용할 [Azure Database for MySQL 서버](quickstart-create-mysql-server-database-using-azure-portal.md) 

> [!IMPORTANT]
> 읽기 복제본 기능은 범용 또는 메모리 최적화 가격 책정 계층의 Azure Database for MySQL 서버에서만 사용 가능합니다. 원본 서버가 이러한 가격 책정 계층 중 하나에 포함되어 있는지 확인합니다.

### <a name="create-a-read-replica"></a>읽기 복제본 만들기

> [!IMPORTANT]
> 기존 복제본이 없는 원본에 대한 복제본을 만드는 경우 원본이 먼저 다시 시작하여 자체적으로 복제할 준비를 합니다. 이를 고려하고 사용량이 적은 기간 동안 이러한 작업을 수행합니다.
>
>주 서버에서 GTID를 사용하는 경우(`gtid_mode` = ON) 새로 만든 복제본도 GTID를 지원하고 GTID 기반 복제를 사용합니다. 자세한 내용은 [GTID(글로벌 트랜잭션 식별자)](concepts-read-replicas.md#global-transaction-identifier-gtid) 참조

다음 명령을 사용하여 읽기 복제본 서버를 만들 수 있습니다.

```azurecli-interactive
az mysql server replica create --name mydemoreplicaserver --source-server mydemoserver --resource-group myresourcegroup
```

`az mysql server replica create` 명령에는 다음과 같은 매개 변수가 필요합니다.

| 설정 | 예제 값 | Description  |
| --- | --- | --- |
| resource-group |  myresourcegroup |  복제본 서버가 만들어지는 리소스 그룹입니다.  |
| name | mydemoreplicaserver | 만들어지는 새 복제본 서버의 이름입니다. |
| source-server | mydemoserver | 복제할 기존 원본 서버의 이름 또는 ID입니다. |

영역 간 읽기 복제본을 만들려면 `--location` 매개 변수를 사용합니다. 아래 CLI 예제에서는 미국 서부에 복제본을 만듭니다.

```azurecli-interactive
az mysql server replica create --name mydemoreplicaserver --source-server mydemoserver --resource-group myresourcegroup --location westus
```

> [!NOTE]
> 복제본을 만들 수 있는 지역에 대해 자세히 알아보려면 [읽기 복제본 개념 문서](concepts-read-replicas.md)를 참조하세요. 

> [!NOTE]
> * `az mysql server replica create`명령에는 Azure CLI를 사용하여 복제본을 만드는 동안 sku(`{pricing_tier}_{compute generation}_{vCores}`)를 지정할 수 있는 `--sku-name` 인수가 있습니다. </br>
> * 주 서버와 읽기 복제본은 동일한 가격 책정 계층(범용 또는 메모리 최적화)에 있어야 합니다. </br>
> * 복제본 서버 구성을 먼저 만든 후 변경할 수도 있습니다. 복제본이 마스터를 유지할 수 있도록 복제본 서버 구성을 원본과 같거나 더 큰 값으로 유지하는 것이 좋습니다.


### <a name="list-replicas-for-a-source-server"></a>원본 서버에 대한 복제본 나열

지정된 원본 서버에 대한 모든 복제본을 보려면 다음 명령을 실행합니다. 

```azurecli-interactive
az mysql server replica list --server-name mydemoserver --resource-group myresourcegroup
```

`az mysql server replica list` 명령에는 다음과 같은 매개 변수가 필요합니다.

| 설정 | 예제 값 | Description  |
| --- | --- | --- |
| resource-group |  myresourcegroup |  복제본 서버가 만들어지는 리소스 그룹입니다.  |
| 서버 이름 | mydemoserver | 원본 서버의 이름 또는 ID입니다. |

### <a name="stop-replication-to-a-replica-server"></a>복제본 서버로의 복제 중지

> [!IMPORTANT]
> 서버로의 복제는 중지하고 나면 취소할 수 없습니다. 원본과 복제본 사이의 복제가 중단된 경우, 중단을 취소할 수 없습니다. 복제를 중지하고 나면 복제본 서버는 독립 실행형 서버가 되어 읽기와 쓰기를 모두 지원합니다. 이 서버를 다시 복제본으로 설정할 수는 없습니다.

다음 명령을 사용하여 읽기 복제본 서버에 대한 복제를 중지할 수 있습니다.

```azurecli-interactive
az mysql server replica stop --name mydemoreplicaserver --resource-group myresourcegroup
```

`az mysql server replica stop` 명령에는 다음과 같은 매개 변수가 필요합니다.

| 설정 | 예제 값 | Description  |
| --- | --- | --- |
| resource-group |  myresourcegroup |  복제본 서버가 있는 리소스 그룹입니다.  |
| name | mydemoreplicaserver | 복제를 중지할 복제본 서버의 이름입니다. |

### <a name="delete-a-replica-server"></a>복제본 서버 삭제

읽기 복제본 서버 삭제는 **[az mysql server delete](/cli/azure/mysql/server)** 명령을 실행하여 수행할 수 있습니다.

```azurecli-interactive
az mysql server delete --resource-group myresourcegroup --name mydemoreplicaserver
```

### <a name="delete-a-source-server"></a>원본 서버 삭제

> [!IMPORTANT]
> 원본 서버를 삭제하면 모든 복제본 서버에 대한 복제가 중지되며 원본 서버 자체도 삭제됩니다. 그러면 복제본 서버는 읽기와 쓰기를 모두 지원하는 독립 실행형 서버로 설정됩니다.

원본 서버를 삭제하려면 **[az mysql server delete](/cli/azure/mysql/server)** 명령을 실행합니다.

```azurecli-interactive
az mysql server delete --resource-group myresourcegroup --name mydemoserver
```


## <a name="rest-api"></a>REST API
[Azure REST API](/rest/api/azure/)를 사용하여 읽기 복제본을 생성하고 관리할 수 있습니다.

### <a name="create-a-read-replica"></a>읽기 복제본 만들기
[API 만들기](/rest/api/mysql/flexibleserver(preview)/servers/create)를 사용하여 읽기 복제본을 만들 수 있습니다.

```http
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforMySQL/servers/{replicaName}?api-version=2017-12-01
```

```json
{
  "location": "southeastasia",
  "properties": {
    "createMode": "Replica",
    "sourceServerId": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforMySQL/servers/{masterServerName}"
  }
}
```

> [!NOTE]
> 복제본을 만들 수 있는 지역에 대해 자세히 알아보려면 [읽기 복제본 개념 문서](concepts-read-replicas.md)를 참조하세요. 

범용 또는 메모리 최적화 원본 서버에서 `azure.replication_support` 매개 변수를 **REPLICA** 로 설정하지 않은 경우 서버를 다시 시작하면 오류가 발생합니다. 복제본을 만들기 전에 이러한 두 단계를 완료합니다.

복제본은 마스터와 동일한 컴퓨팅 및 스토리지 설정을 사용하여 생성됩니다. 복제본을 만든 후에는 컴퓨팅 세대, vCores, 스토리지 및 백업 보존 기간 등 여러 설정을 원본 서버와 독립적으로 변경할 수 있습니다. 가격 책정도 기본 계층에서 다른 계층으로 또는 다른 계층에서 기본 계층으로 변경하는 경우를 제외하고 독립적으로 변경할 수 있습니다.


> [!IMPORTANT]
> 원본 서버 설정을 새 값으로 업데이트하기 전에 복제본 설정을 같거나 더 큰 값으로 업데이트합니다. 이렇게 하면 복제본은 마스터에 생긴 변경 내용을 유지할 수 있습니다.

### <a name="list-replicas"></a>복제본 목록
[복제본 목록 API](/rest/api/mysql/flexibleserver(preview)/replicas/listbyserver)를 사용하여 원본 서버의 복제본 목록을 볼 수 있습니다.

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforMySQL/servers/{masterServerName}/Replicas?api-version=2017-12-01
```

### <a name="stop-replication-to-a-replica-server"></a>복제본 서버로의 복제 중지
[업데이트 API](/rest/api/mysql/flexibleserver(preview)/servers/update)를 사용하여 원본 서버와 읽기 복제본 간의 복제를 중지할 수 있습니다.

원본 서버와 읽기 복제본에 대한 복제를 중지하면 실행 취소할 수 없습니다. 읽기 복제본은 읽기 및 쓰기를 둘 다 지원하는 독립 실행형 서버가 됩니다. 독립 실행형 서버를 다시 복제본으로 만들 수 없습니다.

```http
PATCH https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforMySQL/servers/{masterServerName}?api-version=2017-12-01
```

```json
{
  "properties": {
    "replicationRole":"None"  
   }
}
```

### <a name="delete-a-source-or-replica-server"></a>원본 또는 복제본 서버 삭제
원본 또는 복제본 서버를 삭제하려면 [API 삭제](/rest/api/mysql/flexibleserver(preview)/servers/delete)를 사용합니다.

원본 서버를 삭제하면 모든 읽기 복제본에 대한 복제가 중지됩니다. 그러면 읽기 복제본은 읽기와 쓰기를 모두 지원하는 독립 실행형 서버가 됩니다.

```http
DELETE https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforMySQL/servers/{serverName}?api-version=2017-12-01
```

### <a name="known-issue"></a>알려진 문제

범용 및 메모리 최적화 계층의 서버가 사용하는 스토리지에는 범용 스토리지 v1(최대 4TB까지 지원) 및 범용 스토리지 v2(최대 16TB 스토리지까지 지원)의 두 세대가 있습니다.
원본 서버와 복제본 서버의 스토리지 유형은 동일해야 합니다. 일부 지역에서는 [범용 스토리지 v2](./concepts-pricing-tiers.md#general-purpose-storage-v2-supports-up-to-16-tb-storage)를 사용할 수 없으므로, 읽기 복제본 만들기를 위해 CLI 또는 REST API를 통해 위치를 사용하는 동안 올바른 복제본 지역을 선택해야 합니다. 원본 서버의 스토리지 유형을 식별하는 방법은 [내 서버가 실행 중인 스토리지 유형을 확인하는 방법](./concepts-pricing-tiers.md#how-can-i-determine-which-storage-type-my-server-is-running-on) 링크를 참조하세요. 

사용자의 원본 서버에 대한 읽기 복제본을 만들 수 없는 지역을 선택하는 경우, 아래 그림과 같이 배포가 계속 실행되고 *“리소스 프로비전 작업이 허용된 시간 제한 기간 내에 완료되지 않았습니다”* 라는 오류와 함께 시간 제한이 초과됩니다.

[ :::image type="content" source="media/howto-read-replicas-cli/replcia-cli-known-issue.png" alt-text="읽기 복제본 cli 오류.":::](media/howto-read-replicas-cli/replcia-cli-known-issue.png#lightbox)

## <a name="next-steps"></a>다음 단계

- [읽기 복제본](concepts-read-replicas.md)에 대해 자세히 알아보기
