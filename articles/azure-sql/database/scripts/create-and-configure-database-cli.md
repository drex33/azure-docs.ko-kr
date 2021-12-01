---
title: 'Azure CLI: 단일 데이터베이스 만들기'
description: 이 Azure CLI 예제 스크립트를 사용하여 단일 데이터베이스를 만듭니다.
services: sql-database
ms.service: sql-database
ms.subservice: deployment-configuration
ms.custom: sqldbrb=1, devx-track-azurecli
ms.devlang: azurecli
ms.topic: sample
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.reviewer: ''
ms.date: 06/25/2019
ms.openlocfilehash: cd91e1c06f56b9ffa13e0d7bd56eab0bc1125183
ms.sourcegitcommit: 66b6e640e2a294a7fbbdb3309b4829df526d863d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/01/2021
ms.locfileid: "133359611"
---
# <a name="use-the-azure-cli-to-create-a-single-database-and-configure-a-firewall-rule"></a>Azure CLI를 사용하여 단일 데이터베이스 만들고 및 방화벽 규칙을 구성합니다.

[!INCLUDE[appliesto-sqldb](../../includes/appliesto-sqldb.md)]

이 Azure CLI 스크립트 예제는 Azure SQL Database의 단일 데이터베이스를 만들고 서버 수준 방화벽 규칙을 구성합니다. 스크립트가 성공적으로 실행되면 모든 Azure 서비스 및 구성된 IP 주소에서 데이터베이스에 액세스할 수 있습니다.

CLI를 로컬로 설치하여 사용하도록 선택한 경우 이 항목에서 Azure CLI 버전 2.0 이상을 실행해야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드가 필요한 경우, [Azure CLI 설치]( /cli/azure/install-azure-cli)를 참조하세요.

## <a name="sample-script"></a>샘플 스크립트

### <a name="sign-in-to-azure"></a>Azure에 로그인

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

### <a name="run-the-script"></a>스크립트 실행

[!code-azurecli-interactive[main](../../../../cli_scripts/sql-database/create-and-configure-database/create-and-configure-database.sh "Create SQL Database")]

### <a name="clean-up-deployment"></a>배포 정리

다음 명령을 사용하여 리소스 그룹 및 모든 관련 리소스를 제거합니다.

```azurecli-interactive
az group delete --name $resource
```

## <a name="sample-reference"></a>샘플 참조

이 스크립트는 다음 명령을 사용합니다. 표에 있는 각 명령은 명령에 해당하는 문서에 연결됩니다.

| 명령 | Description |
|---|---|
| [az sql server](/cli/azure/sql/server#az_sql_server_create) | 서버 명령 |
| [az sql server firewall](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_create) | 서버 방화벽 명령입니다. |
| [az sql db](/cli/azure/sql/db#az_sql_db_create) | 데이터베이스 명령입니다. |

## <a name="next-steps"></a>다음 단계

Azure CLI에 대한 자세한 내용은 [Azure CLI 설명서](/cli/azure)를 참조하세요.

추가 SQL Database CLI 스크립트 샘플은 [Azure SQL Database 설명서](../az-cli-script-samples-content-guide.md)에서 찾을 수 있습니다.
