---
title: 'Azure CLI: Azure SQL Database에서 데이터베이스 백업'
description: Azure SQL 단일 데이터베이스를 Azure 스토리지 컨테이너에 백업하는 Azure CLI 예제 스크립트
services: sql-database
ms.service: sql-database
ms.subservice: backup-restore
ms.custom: devx-track-azurecli
ms.devlang: azurecli
ms.topic: sample
author: SQLSourabh
ms.author: sourabha
ms.reviewer: mathoma
ms.date: 03/27/2019
ms.openlocfilehash: e9ced8d027455e11a402968afda632369dac3b82
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "121737493"
---
# <a name="use-cli-to-backup-an-azure-sql-single-database-to-an-azure-storage-container"></a>CLI를 사용하여 Azure SQL 단일 데이터베이스를 Azure 스토리지 컨테이너에 백업

이 Azure CLI 예제는 SQL Database의 데이터베이스를 Azure 스토리지 컨테이너에 백업합니다.  

CLI를 로컬로 설치하여 사용하도록 선택하는 경우 이 문서에서 Azure CLI 버전 2.0 이상을 실행해야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드가 필요한 경우, [Azure CLI 설치]( /cli/azure/install-azure-cli)를 참조하세요.

## <a name="sample-script"></a>샘플 스크립트

### <a name="sign-in-to-azure"></a>Azure에 로그인

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

```azurecli-interactive
$subscription = "<subscriptionId>" # add subscription here

az account set -s $subscription # ...or use 'az login'
```

### <a name="run-the-script"></a>스크립트 실행

[!code-azurecli-interactive[main](../../../cli_scripts/sql-database/backup-database/backup-database.sh "Restore SQL Database")]

### <a name="clean-up-deployment"></a>배포 정리

다음 명령을 사용하여 리소스 그룹 및 모든 관련 리소스를 제거합니다.

```azurecli-interactive
az group delete --name $resource
```

## <a name="sample-reference"></a>샘플 참조

이 스크립트는 다음 명령을 사용합니다. 테이블에 있는 각 명령은 명령에 해당하는 문서에 연결됩니다.

| 명령 | 메모 |
|---|---|
| [az sql server](/cli/azure/sql/server) | 서버 명령입니다. |
| [az sql db](/cli/azure/sql/db) | 데이터베이스 명령입니다. |

## <a name="next-steps"></a>다음 단계

Azure CLI에 대한 자세한 내용은 [Azure CLI 설명서](/cli/azure)를 참조하세요.

추가 SQL Database CLI 스크립트 샘플은 [Azure SQL Database 설명서](../../azure-sql/database/az-cli-script-samples-content-guide.md)에서 찾을 수 있습니다.
