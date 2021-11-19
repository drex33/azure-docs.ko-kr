---
title: Azure Cosmos DB용 Cassandra 서버리스 계정, 키스페이스 및 테이블 만들기
description: Azure Cosmos DB용 Cassandra 서버리스 계정, 키스페이스 및 테이블 만들기
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: sample
ms.date: 11/15/2021
ms.openlocfilehash: b0cb3dcffc17779f0befb4eb2cb8617628a8191d
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2021
ms.locfileid: "132730502"
---
# <a name="create-an-azure-cosmos-cassandra-api-serverless-account-keyspace-and-table-using-azure-cli"></a>Azure CLI를 사용하여 Azure Cosmos Cassandra API 서버리스 계정, 키스페이스 및 테이블 만들기

[!INCLUDE[appliesto-cassandra-api](../../../includes/appliesto-cassandra-api.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../../../includes/azure-cli-prepare-your-environment.md)]

## <a name="sample-script"></a>샘플 스크립트

[!code-azurecli-interactive[main](../../../../../cli_scripts/cosmosdb/cassandra/serverless.sh "Create an Azure Cosmos DB Cassandra API serverless account, keyspace, and table.")]

## <a name="clean-up-deployment"></a>배포 정리

스크립트 샘플을 실행한 후에 다음 명령을 사용하여 리소스 그룹 및 관련된 모든 리소스를 제거할 수 있습니다.

```azurecli-interactive
az group delete --name $resourceGroupName
```

## <a name="script-explanation"></a>스크립트 설명

이 스크립트는 다음 명령을 사용합니다. 테이블에 있는 각 명령은 명령에 해당하는 문서에 연결됩니다.

| 명령 | 메모 |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | 모든 리소스가 저장되는 리소스 그룹을 만듭니다. |
| [az cosmosdb create](/cli/azure/cosmosdb#az_cosmosdb_create) | Azure Cosmos DB 계정을 만듭니다. |
| [az cosmosdb cassandra keyspace create](/cli/azure/cosmosdb/cassandra/keyspace#az_cosmosdb_cassandra_keyspace_create) | Azure Cosmos Cassandra 키스페이스를 만듭니다. |
| [az cosmosdb cassandra table create](/cli/azure/cosmosdb/cassandra/table#az_cosmosdb_cassandra_table_create) | Azure Cosmos Cassandra 테이블을 만듭니다. |
| [az group delete](/cli/azure/resource#az_resource_delete) | 모든 중첩 리소스를 포함한 리소스 그룹을 삭제합니다. |

## <a name="next-steps"></a>다음 단계

Azure Cosmos DB CLI에 대한 자세한 내용은 [Azure Cosmos DB CLI 설명서](/cli/azure/cosmosdb)를 참조하세요.

모든 Azure Cosmos DB CLI 스크립트 샘플은 [Azure Cosmos DB CLI GitHub 리포지토리](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb)에서 확인할 수 있습니다.
