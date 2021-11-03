---
title: CLI 예제 - 장애 조치(failover) 그룹 - Azure SQL Database 탄력적 풀
description: Azure SQL Database 탄력적 풀을 만들어서 장애 조치(failover) 그룹에 추가하고 장애 조치(failover)를 테스트하는 Azure CLI 예제 스크립트입니다.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: azurecli
ms.topic: sample
author: rothja
ms.author: jroth
ms.reviewer: mathoma
ms.date: 07/16/2019
ms.openlocfilehash: 04389f3cc5604e0412776e2c02b4e3e4fcc8738b
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131013729"
---
# <a name="use-cli-to-add-an-azure-sql-database-elastic-pool-to-a-failover-group"></a>CLI를 사용하여 Azure SQL Database 탄력적 풀을 장애 조치(failover) 그룹에 추가

이 Azure CLI 스크립트 예제에서는 단일 데이터베이스를 만들어 탄력적 풀에 추가하고, 장애 조치(failover) 그룹을 만들고, 장애 조치(failover)를 테스트합니다.

CLI를 로컬로 설치하여 사용하도록 선택하는 경우 이 문서에서 Azure CLI 버전 2.0 이상을 실행해야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드가 필요한 경우, [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요.

## <a name="sample-script"></a>샘플 스크립트

### <a name="sign-in-to-azure"></a>Azure에 로그인

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

```azurecli-interactive
$subscription = "<subscriptionId>" # add subscription here

az account set -s $subscription # ...or use 'az login'
```

### <a name="run-the-script"></a>스크립트 실행

[!code-azurecli-interactive[main](../../../cli_scripts/sql-database/failover-groups/add-elastic-pool-to-failover-group-az-cli.sh "Add elastic pool to a failover group")]

### <a name="clean-up-deployment"></a>배포 정리

다음 명령을 사용하여 리소스 그룹 및 모든 관련 리소스를 제거합니다.

```azurecli-interactive
az group delete --name $resource
```

## <a name="sample-reference"></a>샘플 참조

이 스크립트는 다음 명령을 사용합니다. 테이블에 있는 각 명령은 명령에 해당하는 문서에 연결됩니다.

| 명령 | 설명 |
|---|---|
| [az sql elastic-pool](/cli/azure/sql/elastic-pool) | 탄력적 풀 명령입니다. |
| [az sql failover-group](/cli/azure/sql/failover-group) | 장애 조치(failover) 그룹 명령입니다. |

## <a name="next-steps"></a>다음 단계

Azure CLI에 대한 자세한 내용은 [Azure CLI 설명서](/cli/azure/overview)를 참조하세요.

추가 SQL Database Azure CLI 스크립트 샘플은 [Azure SQL Database Azure CLI 스크립트](../../azure-sql/database/az-cli-script-samples-content-guide.md)에서 찾을 수 있습니다.
