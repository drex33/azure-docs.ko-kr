---
title: CLI 예제 지역 백업 복원 - Azure SQL Database
description: 지역 중복 백업에서 Azure SQL Managed Instance Database를 복원하는 Azure CLI 예제 스크립트입니다.
services: sql-database
ms.service: sql-database
ms.subservice: backup-restore
ms.custom: ''
ms.devlang: azurecli
ms.topic: sample
author: rothja
ms.author: jroth
ms.reviewer: mathoma
ms.date: 07/03/2019
ms.openlocfilehash: 014ce4fdc45ba219cc7f7c68aa6b4aa16d192a2f
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114470041"
---
# <a name="use-cli-to-restore-a-managed-instance-database-to-another-geo-region"></a>CLI를 사용하여 다른 지역으로 Managed Instance 데이터베이스 복원

이 Azure CLI 스크립트 예제는 원격 지역(지역 복원)에서 Azure SQL Managed Instance 데이터베이스를 복원합니다.  

CLI를 로컬로 설치하여 사용하도록 선택하는 경우 이 문서에서 Azure CLI 버전 2.0 이상을 실행해야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드가 필요한 경우, [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요.

## <a name="sample-script"></a>샘플 스크립트

### <a name="prerequisites"></a>필수 구성 요소

관리형 인스턴스의 기존 쌍은 [Azure CLI를 사용하여 Azure SQL Managed Instance 인스턴스 만들기](sql-database-create-configure-managed-instance-cli.md)를 참조하세요.

### <a name="sign-in-to-azure"></a>Azure에 로그인

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

### <a name="run-the-script"></a>스크립트 실행

```azurepowershell-interactive
#!/bin/bash
$instance = "<instanceId>" # add instance here
$targetInstance = "<targetInstanceId>" # add target instance here
$resource = "<resourceId>" # add resource here

$randomIdentifier = $(Get-Random)
$managedDatabase = "managedDatabase-$randomIdentifier"

echo "Creating $($managedDatabase) on $($instance)..."
az sql midb create -g $resource --mi $instance -n $managedDatabase

echo "Restoring $($managedDatabase) to $($targetInstance)..."
az sql midb restore -g $resource --mi $instance -n $managedDatabase --dest-name $targetInstance --time "2018-05-20T05:34:22"
```

## <a name="sample-reference"></a>샘플 참조

이 스크립트는 다음 명령을 사용합니다. 테이블에 있는 각 명령은 명령에 해당하는 문서에 연결됩니다.

| 스크립트 | Description |
|---|---|
| [az sql midb](/cli/azure/sql/midb) | Managed Instance Database 명령입니다. |

## <a name="next-steps"></a>다음 단계

Azure CLI에 대한 자세한 내용은 [Azure CLI 설명서](/cli/azure)를 참조하세요.

추가 SQL Database CLI 스크립트 샘플은 [Azure SQL Database 설명서](../../azure-sql/database/az-cli-script-samples-content-guide.md)에서 찾을 수 있습니다.
