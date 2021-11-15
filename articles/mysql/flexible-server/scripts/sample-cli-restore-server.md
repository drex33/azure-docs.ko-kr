---
title: CLI 스크립트 - Azure Database for MySQL - 유연한 서버 복원
description: 이 Azure CLI 샘플 스크립트에서는 단일 Azure Database for MySQL - 유연한 서버를 이전 시점으로 복원하는 방법을 보여줍니다.
author: shreyaaithal
ms.author: shaithal
ms.service: mysql
ms.devlang: azurecli
ms.topic: sample
ms.custom: mvc, devx-track-azurecli
ms.date: 09/15/2021
ms.openlocfilehash: f109da4924f04cd97fbe9cd816c2da45ce5c0e2d
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131844159"
---
# <a name="restore-an-azure-database-for-mysql---flexible-server-using-azure-cli"></a>Azure CLI를 사용하여 Azure Database for MySQL - 유연한 서버 복원

Azure Database for MySQL - 유연한 서버는 자동으로 서버 백업을 만들고 지역 내 로컬 중복 스토리지에 안전하게 저장합니다.

이 샘플 CLI 스크립트는 [특정 시점 복원](../concepts-backup-restore.md)을 수행하고 유연한 서버 백업에서 새 서버를 만듭니다. 

새 유연한 서버는 원래 서버 구성으로 생성되고 원본 서버에서 가상 네트워크 및 방화벽과 같은 태그와 설정도 상속합니다. 복원이 완료된 후 복원된 서버의 컴퓨팅 및 스토리지 계층, 구성 및 보안 설정을 변경할 수 있습니다.

[!INCLUDE [flexible-server-free-trial-note](../../includes/flexible-server-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment](../../../../includes/azure-cli-prepare-your-environment.md)]

- 이 문서에는 Azure CLI 버전 2.0 이상이 필요합니다. Azure Cloud Shell을 사용하는 경우 최신 버전이 이미 설치되어 있습니다. 

## <a name="sample-script"></a>샘플 스크립트

변수 값을 사용하여 스크립트에서 강조 표시된 줄을 편집합니다.

[!code-azurecli-interactive[main](../../../../cli_scripts/mysql/flexible-server/backup-restore/restore-server.sh?highlight=7,10-12 "Perform point-in-time-restore of a source server to a new server.")]

## <a name="clean-up-deployment"></a>배포 정리

샘플 스크립트가 실행되었으면 다음 코드 조각을 사용하여 리소스를 정리할 수 있습니다.

[!code-azurecli-interactive[main](../../../../cli_scripts/mysql/flexible-server/backup-restore/clean-up-resources.sh?highlight=4-5 "Clean up resources.")]

## <a name="script-explanation"></a>스크립트 설명

이 스크립트는 다음 명령을 사용합니다. 테이블에 있는 각 명령은 명령에 해당하는 문서에 연결됩니다.

| **명령** | **참고 사항** |
|---|---|
|[az group create](/cli/azure/group#az_group_create)|모든 리소스가 저장되는 리소스 그룹을 만듭니다.|
|[az mysql flexible-server create](/cli/azure/mysql/flexible-server#az_mysql_flexible_server_create)|데이터베이스를 호스트하는 유연한 서버를 만듭니다.|
|[az mysql flexible-server restore](/cli/azure/mysql/flexible-server#az_mysql_flexible_server_restore)|백업에서 유연한 서버를 복원합니다.|
|[az mysql flexible-server show](/cli/azure/mysql/flexible-server#az_mysql_flexible_server_show)|유연한 서버의 세부 정보를 가져옵니다.|
|[az mysql flexible-server delete](/cli/azure/mysql/flexible-server#az_mysql_flexible_server_delete)|유연한 서버를 삭제합니다.|
|[az group delete](/cli/azure/group#az_group_delete) | 모든 중첩 리소스를 포함한 리소스 그룹을 삭제합니다.|

## <a name="next-steps"></a>다음 단계

- 추가 스크립트 시도: [Azure Database for MySQL - 유연한 서버용 Azure CLI 샘플](../sample-scripts-azure-cli.md)
- Azure CLI에 대한 자세한 내용은 [Azure CLI 설명서](/cli/azure)를 참조하세요.