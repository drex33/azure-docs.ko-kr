---
title: CLI 스크립트 - Azure Database for MySQL - 유연한 서버(미리 보기) 다시 시작/중지/시작
description: 이 Azure CLI 샘플 스크립트에서는 Azure Database for MySQL - 유연한 서버를 다시 시작/중지/시작하는 방법을 보여줍니다.
author: shreyaaithal
ms.author: shaithal
ms.service: mysql
ms.devlang: azurecli
ms.topic: sample
ms.custom: mvc, devx-track-azurecli
ms.date: 09/15/2021
ms.openlocfilehash: 2c4d17b0bcbba1c231b06f6112ef8b43e9b4786a
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128604700"
---
# <a name="restartstopstart-an-azure-database-for-mysql---flexible-server-preview-using-azure-cli"></a>Azure CLI를 사용하여 Azure Database for MySQL - 유연한 서버(미리 보기) 다시 시작/중지/시작

이 샘플 CLI 스크립트는 Azure Database for MySQL - 유연한 서버에서 다시 시작, 시작 및 중지 작업을 수행합니다. 

[!INCLUDE [flexible-server-free-trial-note](../../includes/flexible-server-free-trial-note.md)]


> [!IMPORTANT]
> 서버를 **중지** 하면 다음 7일 동안 계속 해당 상태가 유지됩니다. 이 시간 동안 수동으로 **시작** 하지 않으면 서버는 7일이 끝나는 시점에 자동으로 시작됩니다. 서버를 사용하지 않는 경우 다시 **중지** 하도록 선택할 수 있습니다.

서버가 중지된 동안에는 서버에서 관리 작업을 수행할 수 없습니다. 서버에서 구성 설정을 변경하려면 서버를 시작해야 합니다. 

또한 중지/시작 작업을 수행하기 전에 [중지/시작 제한 사항](../concepts-limitations.md#stopstart-operation)을 참조하세요.


[!INCLUDE [azure-cli-prepare-your-environment](../../../../includes/azure-cli-prepare-your-environment.md)]

- 이 문서에는 Azure CLI 버전 2.0 이상이 필요합니다. Azure Cloud Shell을 사용하는 경우 최신 버전이 이미 설치되어 있습니다. 

## <a name="sample-script"></a>샘플 스크립트

변수 값을 사용하여 스크립트에서 강조 표시된 줄을 편집합니다.

[!code-azurecli-interactive[main](../../../../cli_scripts/mysql/flexible-server/manage-server/restart-start-stop.sh?highlight=7,10-11 "Create a server, perform restart / start / stop operations.")]

## <a name="clean-up-deployment"></a>배포 정리

샘플 스크립트가 실행되었으면 다음 코드 조각을 사용하여 리소스를 정리할 수 있습니다.

[!code-azurecli-interactive[main](../../../../cli_scripts/mysql/flexible-server/manage-server/clean-up-resources.sh?highlight=4 "Clean up resources.")]

## <a name="script-explanation"></a>스크립트 설명

이 스크립트는 다음 명령을 사용합니다. 테이블에 있는 각 명령은 명령에 해당하는 문서에 연결됩니다.

| **명령** | **참고** |
|---|---|
|[az group create](/cli/azure/group#az_group_create)|모든 리소스가 저장되는 리소스 그룹을 만듭니다.|
|[az mysql flexible-server create](/cli/azure/mysql/flexible-server#az_mysql_flexible_server_create)|데이터베이스를 호스트하는 유연한 서버를 만듭니다.|
|[az mysql flexible-server stop](/cli/azure/mysql/flexible-server#az_mysql_flexible_server_stop)|유연한 서버를 중지합니다.|
|[az mysql flexible-server start](/cli/azure/mysql/flexible-server#az_mysql_flexible_server_start)|유연한 서버를 시작합니다.|
|[az mysql flexible-server restart](/cli/azure/mysql/flexible-server#az_mysql_flexible_server_restart)|유연한 서버를 다시 시작합니다.|
|[az mysql flexible-server delete](/cli/azure/mysql/flexible-server#az_mysql_flexible_server_delete)|유연한 서버를 삭제합니다.|
|[az group delete](/cli/azure/group#az_group_delete) | 모든 중첩 리소스를 포함한 리소스 그룹을 삭제합니다.|

## <a name="next-steps"></a>다음 단계

- 추가 스크립트 시도: [Azure Database for MySQL - 유연한 서버(미리 보기)용 Azure CLI 샘플](../sample-scripts-azure-cli.md)
- Azure CLI에 대한 자세한 내용은 [Azure CLI 설명서](/cli/azure)를 참조하세요.