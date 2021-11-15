---
title: CLI 스크립트 - Azure Database for MySQL - 유연한 서버에서 감사 로그 구성
description: 이 Azure CLI 샘플 스크립트에서는 Azure Database for MySQL - 유연한 서버에서 감사 로그를 구성하는 방법을 보여줍니다.
author: shreyaaithal
ms.author: shaithal
ms.service: mysql
ms.devlang: azurecli
ms.topic: sample
ms.custom: mvc, devx-track-azurecli
ms.date: 09/15/2021
ms.openlocfilehash: 359d505f8d04090c14736be0d448558847e05c80
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131844338"
---
# <a name="configure-audit-logs-on-an-azure-database-for-mysql---flexible-server-using-azure-cli"></a>Azure CLI를 사용하여 Azure Database for MySQL - 유연한 서버에서 감사 로그 구성

이 샘플 CLI 스크립트는 Azure Database for MySQL - 유연한 서버에서 [감사 로그](../concepts-audit-logs.md)를 사용합니다. 


[!INCLUDE [flexible-server-free-trial-note](../../includes/flexible-server-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment](../../../../includes/azure-cli-prepare-your-environment.md)]


- 이 문서에는 Azure CLI 버전 2.0 이상이 필요합니다. Azure Cloud Shell을 사용하는 경우 최신 버전이 이미 설치되어 있습니다. 

## <a name="sample-script"></a>샘플 스크립트

변수 값을 사용하여 스크립트에서 강조 표시된 줄을 편집합니다.

[!code-azurecli-interactive[main](../../../../cli_scripts/mysql/flexible-server/configure-logs/configure-audit-logs.sh?highlight=7,10-11 "Configure audit logs on Azure Database for MySQL - Flexible Server.")]


## <a name="clean-up-deployment"></a>배포 정리

샘플 스크립트가 실행되었으면 다음 코드 조각을 사용하여 리소스를 정리할 수 있습니다.

[!code-azurecli-interactive[main](../../../../cli_scripts/mysql/flexible-server/configure-logs/clean-up-resources.sh?highlight=4 "Clean up resources.")]


## <a name="script-explanation"></a>스크립트 설명

이 스크립트는 다음 명령을 사용합니다. 테이블에 있는 각 명령은 명령에 해당하는 문서에 연결됩니다.

| **명령** | **참고 사항** |
|---|---|
|[az group create](/cli/azure/group#az_group_create)|모든 리소스가 저장되는 리소스 그룹을 만듭니다.|
|[az mysql flexible-server create](/cli/azure/mysql/flexible-server#az_mysql_flexible_server_create)|데이터베이스를 호스트하는 유연한 서버를 만듭니다.|
|[az mysql flexible-server parameter set](/cli/azure/mysql/flexible-server/parameter#az_mysql_flexible_server_parameter_set)|유연한 서버의 매개 변수를 업데이트합니다.|
|[az mysql flexible-server delete](/cli/azure/mysql/flexible-server#az_mysql_flexible_server_delete)|유연한 서버를 삭제합니다.|
|[az group delete](/cli/azure/group#az_group_delete) | 모든 중첩 리소스를 포함한 리소스 그룹을 삭제합니다.|

## <a name="next-steps"></a>다음 단계

- 추가 스크립트 시도: [Azure Database for MySQL - 유연한 서버용 Azure CLI 샘플](../sample-scripts-azure-cli.md)
- Azure CLI에 대한 자세한 내용은 [Azure CLI 설명서](/cli/azure)를 참조하세요.