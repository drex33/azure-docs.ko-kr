---
title: CLI 예제 - 활성 지역 복제 - 단일 Azure SQL Database
description: Azure SQL Database에서 단일 데이터베이스에 대한 활성 지역 복제를 설정하여 장애 조치(failover)를 수행하는 Azure CLI 예제 스크립트입니다.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: azurecli
ms.topic: sample
author: rothja
ms.author: jroth
ms.reviewer: mathoma
ms.date: 03/12/2019
ms.openlocfilehash: 899417024a10cc1286dc619ecd9e2c0edf7fcae5
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131083444"
---
# <a name="use-cli-to-configure-active-geo-replication-for-a-single-database-in-azure-sql-database"></a>CLI를 사용하여 Azure SQL Database의 단일 데이터베이스에 대한 활성 지역 복제 구성

이 Azure CLI 스크립트 예제는 단일 데이터베이스에 대해 활성 지역 복제를 구성하고 데이터베이스의 보조 복제본으로 장애 조치(failover)를 수행합니다.

CLI를 로컬로 설치하여 사용하도록 선택하는 경우 이 문서에서 Azure CLI 버전 2.0 이상을 실행해야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드가 필요한 경우, [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요.

## <a name="sample-script"></a>샘플 스크립트

### <a name="sign-in-to-azure"></a>Azure에 로그인

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

```azurecli-interactive
$subscription = "<subscriptionId>" # add subscription here

az account set -s $subscription # ...or use 'az login'
```

### <a name="run-the-script"></a>스크립트 실행

[!code-azurecli-interactive[main](../../../cli_scripts/sql-database/setup-geodr-and-failover/setup-geodr-and-failover-single-database.sh "Set up active geo-replication for single database")]

### <a name="clean-up-deployment"></a>배포 정리

다음 명령을 사용하여 리소스 그룹 및 모든 관련 리소스를 제거합니다.

```azurecli-interactive
az group delete --name $resource
```

## <a name="sample-reference"></a>샘플 참조

이 스크립트는 다음 명령을 사용합니다. 테이블에 있는 각 명령은 명령에 해당하는 문서에 연결됩니다.

| 명령 | 설명 |
|---|---|
| [az sql db replica](/cli/azure/sql/db/replica) | 데이터베이스 복제본 명령입니다. |

## <a name="next-steps"></a>다음 단계

Azure CLI에 대한 자세한 내용은 [Azure CLI 설명서](/cli/azure)를 참조하세요.

추가 SQL Database CLI 스크립트 샘플은 [Azure SQL Database 설명서](../../azure-sql/database/az-cli-script-samples-content-guide.md)에서 찾을 수 있습니다.