---
title: 'Azure CLI: 장애 조치(failover) 그룹에 데이터베이스 추가'
description: Azure CLI 예제 스크립트를 사용하여 Azure SQL Database의 데이터베이스를 만들어서 자동 장애 조치(failover) 그룹에 추가하고 장애 조치(failover)를 테스트합니다.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: sqldbrb=1, devx-track-azurecli
ms.devlang: azurecli
ms.topic: sample
author: emlisa
ms.author: emlisa
ms.reviewer: mathoma
ms.date: 07/16/2019
ms.openlocfilehash: 6865095f3c50b0fc2e28ac293a7409bcd60304a0
ms.sourcegitcommit: 01dcf169b71589228d615e3cb49ae284e3e058cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/19/2021
ms.locfileid: "130162628"
---
# <a name="use-the-azure-cli-to-add-a-database-to-a-failover-group"></a>Azure CLI를 사용하여 장애 조치(failover) 그룹에 데이터베이스 추가

[!INCLUDE[appliesto-sqldb](../../includes/appliesto-sqldb.md)]

이 Azure CLI 스크립트 예제에서는 Azure SQL Database의 데이터베이스를 만들고, 장애 조치(failover) 그룹을 만들고, 데이터베이스를 이 그룹에 추가하고, 장애 조치(failover)를 테스트합니다.

CLI를 로컬로 설치하여 사용하도록 선택한 경우 이 항목에서 Azure CLI 버전 2.0 이상을 실행해야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드가 필요한 경우, [Azure CLI 설치]( /cli/azure/install-azure-cli)를 참조하세요.

## <a name="sample-script"></a>샘플 스크립트

### <a name="sign-in-to-azure"></a>Azure에 로그인

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

```azurecli-interactive
$subscription = "<subscriptionId>" # add subscription here

az account set -s $subscription # ...or use 'az login'
```

### <a name="run-the-script"></a>스크립트 실행

[!code-azurecli-interactive[main](../../../../cli_scripts/sql-database/failover-groups/add-single-db-to-failover-group-az-cli.sh "Add Azure SQL Database to failover group")]

### <a name="clean-up-deployment"></a>배포 정리

다음 명령을 사용하여 리소스 그룹 및 모든 관련 리소스를 제거합니다.

```azurecli-interactive
az group delete --name $resource
```

## <a name="sample-reference"></a>샘플 참조

이 스크립트는 다음 명령을 사용합니다. 표에 있는 각 명령은 명령에 해당하는 문서에 연결됩니다.

| 명령 | Description |
|---|---|
| [az sql db](/cli/azure/sql/db) | 데이터베이스 명령입니다. |
| [az sql failover-group](/cli/azure/sql/failover-group) | 장애 조치(failover) 그룹 명령입니다. |

## <a name="next-steps"></a>다음 단계

Azure CLI에 대한 자세한 내용은 [Azure CLI 설명서](/cli/azure)를 참조하세요.

추가 SQL Database CLI 스크립트 샘플은 [Azure SQL Database 설명서](../az-cli-script-samples-content-guide.md)에서 찾을 수 있습니다.
