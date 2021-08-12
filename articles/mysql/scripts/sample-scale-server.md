---
title: CLI 스크립트 - 크기 조정 서버 - Azure Database for MySQL
description: 이 샘플 CLI 스크립트는 메트릭을 쿼리한 후에 MySQL 서버용 Azure Database를 다양한 성능 수준으로 확장합니다.
author: savjani
ms.author: pariks
ms.service: mysql
ms.devlang: azurecli
ms.topic: sample
ms.custom: mvc, devx-track-azurecli
ms.date: 12/02/2019
ms.openlocfilehash: a0150128328eb94dd1ddbdfde6bbaf7e24c90627
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/30/2021
ms.locfileid: "113084620"
---
# <a name="monitor-and-scale-an-azure-database-for-mysql-server-using-azure-cli"></a>Azure CLI를 사용하여 MySQL용 Azure Database 모니터링 및 확장

[[!INCLUDE[applies-to-mysql-single-flexible-server](../includes/applies-to-mysql-single-flexible-server.md)]

이 샘플 CLI 스크립트는 메트릭을 쿼리한 후에 단일 Azure Database for MySQL 서버에 대한 컴퓨팅 및 스토리지를 확장합니다. 컴퓨팅을 확장하거나 축소할 수 있습니다. 스토리지는 확장만 가능합니다.

[!INCLUDE[azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- 이 문서에는 Azure CLI 버전 2.0 이상이 필요합니다. Azure Cloud Shell을 사용하는 경우 최신 버전이 이미 설치되어 있습니다. 

## <a name="sample-script"></a>샘플 스크립트

구독 ID로 스크립트를 업데이트합니다.
[!code-azurecli-interactive[main](../../../cli_scripts/mysql/scale-mysql-server/scale-mysql-server.sh "Create and scale Azure Database for MySQL.")]

## <a name="clean-up-deployment"></a>배포 정리

스크립트가 실행 된 후 다음 명령을 사용하여 리소스 그룹 및 관련된 모든 리소스를 제거합니다. 
[!code-azurecli-interactive[main](../../../cli_scripts/mysql/scale-mysql-server/delete-mysql.sh  "Delete the resource group.")]

## <a name="script-explanation"></a>스크립트 설명

이 스크립트에는 다음 표에 설명된 명령이 사용됩니다.

| **명령** | **참고** |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | 모든 리소스가 저장되는 리소스 그룹을 만듭니다. |
| [az mysql server create](/cli/azure/mysql/server#az_mysql_server_create) | 데이터베이스를 호스팅하는 MySQL 서버를 만듭니다. |
| [az mysql server update](/cli/azure/mysql/server#az_mysql_server_update) | MySQL 서버의 속성을 업데이트합니다. |
| [az monitor metrics list](/cli/azure/monitor/metrics#az_monitor_metrics_list) | 리소스에 대한 메트릭 값을 나열합니다. |
| [az group delete](/cli/azure/group#az_group_delete) | 모든 중첩 리소스를 포함한 리소스 그룹을 삭제합니다. |

## <a name="next-steps"></a>다음 단계

- [Azure Database for MySQL 컴퓨팅 및 스토리지](../concepts-pricing-tiers.md)에 대해 자세히 알아보기
- 추가 스크립트 시도: [MySQL용 Azure Database에 대한 Azure CLI 샘플](../sample-scripts-azure-cli.md)
- [Azure CLI](/cli/azure)에 대한 자세한 정보
