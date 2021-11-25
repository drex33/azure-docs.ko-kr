---
title: '빠른 시작: Azure CLI를 사용하여 Synapse SQL 풀 만들기'
description: Azure CLI를 사용하여 서버 수준 방화벽 규칙으로 Synapse SQL 풀을 신속하게 만듭니다.
services: synapse-analytics
author: julieMSFT
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: sql-dw
ms.date: 11/20/2020
ms.author: jrasnick
ms.custom: azure-synapse, devx-track-azurecli, mode-api
ms.openlocfilehash: 81944fde56d641c9d09d853ed709c8615f546d69
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133070823"
---
# <a name="quickstart-create-a-synapse-sql-pool-with-azure-cli"></a>빠른 시작: Azure CLI를 사용하여 Synapse SQL 풀 만들기

Azure CLI를 사용하여 Azure Synapse Analytics에서 Synapse SQL 풀(데이터 웨어하우스)을 만듭니다.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

## <a name="getting-started"></a>시작

이러한 명령을 사용하여 Azure에 로그인하고 리소스 그룹을 설정합니다.

1. 로컬 설치를 사용하는 경우 [az login](/cli/azure/reference-index#az_login) 명령을 실행하여 Azure에 로그인합니다.

   ```azurecli
   az login
   ```

1. 필요한 경우 [az account set](/cli/azure/account#az_account_set) 명령을 사용하여 구독을 선택합니다.

   ```azurecli
   az account set --subscription 00000000-0000-0000-0000-000000000000
   ```

1. [az group create](/cli/azure/group#az_group_create)를 실행하여 리소스 그룹을 만듭니다.

   ```azurecli
   az group create --name myResourceGroup --location WestEurope
   ```

1. [az sql server create](/cli/azure/sql/server#az_sql_server_create) 명령을 사용하여 [논리 SQL 서버](../../azure-sql/database/logical-servers.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)를 만듭니다.

   ```azurecli
   az sql server create --resource-group myResourceGroup --name mysqlserver \
      --admin-user ServerAdmin --admin-password ChangeYourAdminPassword1
   ```

   서버는 그룹으로 관리되는 데이터베이스 그룹을 포함합니다.

## <a name="configure-a-server-level-firewall-rule"></a>서버 수준 방화벽 규칙 구성

[서버 수준 방화벽 규칙](../../azure-sql/database/firewall-configure.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)을 만듭니다. 서버 수준 방화벽 규칙을 사용하면 외부 애플리케이션(예제: SQL Server Management Studio 또는 SQLCMD 유틸리티)이 SQL 풀 서비스 방화벽을 통해 SQL 풀에 연결할 수 있습니다.

[az sql server firewall-rule create](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_create) 명령을 실행하여 방화벽 규칙을 만듭니다.

```azurecli
az sql server firewall-rule create --resource-group myResourceGroup --name AllowSome \
   --server mysqlserver --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

이 예제에서 방화벽은 다른 Azure 리소스에 대해서만 열립니다. 외부 연결을 사용하려면 IP 주소를 사용자 환경에 적절한 주소로 변경합니다. 모든 IP 주소를 열려면 시작 IP 주소로 0.0.0.0을, 끝나는 IP 주소로 255.255.255.255를 사용합니다.

> [!NOTE]
> SQL 엔드포인트는 1433 포트를 통해 통신합니다. 회사 네트워크 내에서 연결을 시도하는 경우 포트 1433을 통한 아웃바운드 트래픽이 네트워크 방화벽에서 허용되지 않을 수 있습니다. 이 경우 IT 부서에서 1433 포트를 열지 않으면 서버에 연결할 수 없습니다.
>

## <a name="create-and-manage-your-sql-pool"></a>SQL 풀 만들기 및 관리

SQL 풀을 만듭니다. 이 예제에서는 DW100c를 SQL 풀의 저렴한 시작점인 서비스 목표로 사용합니다.

> [!NOTE]
> 이전에 만든 작업 영역이 필요합니다. 자세한 내용은 [빠른 시작: Azure CLI를 사용하여 Azure Synapse 작업 영역 만들기](../quickstart-create-workspace-cli.md)를 참조하세요.

[az synapse sql pool create](/cli/azure/synapse/sql/pool#az_synapse_sql_pool_create) 명령을 사용하여 SQL 풀을 만듭니다.

```azurecli
az synapse sql pool create --resource-group myResourceGroup --name mySampleDataWarehouse \
   --performance-level "DW1000c" --workspace-name testsynapseworkspace
```

매개 변수 옵션에 대한 자세한 내용은 [az synapse sql pool](/cli/azure/synapse/sql/pool)을 참조하세요.

[az synapse sql pool list](/cli/azure/synapse/sql/pool#az_synapse_sql_pool_list) 명령을 사용하여 SQL 풀을 볼 수 있습니다.

```azurecli
az synapse sql pool list --resource-group myResourceGroup --workspace-name testsynapseworkspace
```

[az synapse sql pool update](/cli/azure/synapse/sql/pool#az_synapse_sql_pool_update) 명령을 사용하여 기존 풀을 업데이트합니다.

```azurecli
az synapse sql pool update --resource-group myResourceGroup --name mySampleDataWarehouse \
   --workspace-name testsynapseworkspace
```

[az synapse sql pool pause](/cli/azure/synapse/sql/pool#az_synapse_sql_pool_pause) 명령을 사용하여 풀을 일시 중지합니다.

```azurecli
az synapse sql pool pause --resource-group myResourceGroup --name mySampleDataWarehouse \
   --workspace-name testsynapseworkspace
```

[az synapse sql pool resume](/cli/azure/synapse/sql/pool#az_synapse_sql_pool_resume) 명령을 사용하여 일시 중지된 풀을 시작합니다.

```azurecli
az synapse sql pool resume --resource-group myResourceGroup --name mySampleDataWarehouse \
   --workspace-name testsynapseworkspace
```

기존 SQL 풀을 제거하려면 [az synapse SQL pool delete](/cli/azure/synapse/sql/pool#az_synapse_sql_pool_delete) 명령을 사용합니다.

```azurecli
az synapse sql pool delete --resource-group myResourceGroup --name mySampleDataWarehouse \
   --workspace-name testsynapseworkspace
```

## <a name="clean-up-resources"></a>리소스 정리

이 컬렉션의 다른 빠른 시작 자습서는 이 빠른 시작을 기반으로 하여 작성됩니다.

> [!TIP]
> 후속 빠른 시작 자습서를 계속 사용하려면 이 빠른 시작에서 만든 리소스를 정리하지 마세요. 계속하지 않으려는 경우 [az group delete](/cli/azure/group#az_group_delete) 명령을 사용하여 이 빠른 시작에서 만든 모든 리소스를 삭제합니다.
>

```azurecli
az group delete --ResourceGroupName MyResourceGroup
```

## <a name="next-steps"></a>다음 단계

지금까지 SQL 풀을 만들고, 방화벽 규칙을 만들고, SQL 풀에 연결했습니다. 자세히 알아보려면 [SQL 풀에 데이터 로드](./load-data-from-azure-blob-storage-using-copy.md) 문서를 계속 진행하세요.
