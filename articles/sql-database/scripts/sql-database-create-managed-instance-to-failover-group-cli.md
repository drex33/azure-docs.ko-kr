---
title: 'Azure CLI: 장애 조치(failover) 그룹에 관리형 인스턴스 추가'
description: 두 개의 관리되는 인스턴스를 만들고 이를 장애 조치(failover) 그룹에 추가한 다음, 장애 조치(failover)를 테스트하는 방법을 알아봅니다.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: devx-track-azurecli
ms.devlang: azurecli
ms.topic: sample
author: rothja
ms.author: jroth
ms.reviewer: mathoma
ms.date: 07/16/2019
ms.openlocfilehash: 34d64e9afc9e744ceb44751eb01dafb59d32cd6b
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131013691"
---
# <a name="use-cli-to-create-an-azure-sql-managed-instance-to-a-failover-group"></a>CLI를 사용하여 Azure SQL Managed Instance를 장애 조치(failover) 그룹에 만들기

이 Azure CLI 스크립트 예제는 두 개의 관리형 인스턴스를 만들어서 장애 조치(failover) 그룹에 추가한 다음, 기본 관리형 인스턴스에서 보조 관리형 인스턴스로 장애 조치(failover)를 테스트합니다.

CLI를 로컬로 설치하여 사용하도록 선택하는 경우 이 문서에서 Azure CLI 버전 2.0 이상을 실행해야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드가 필요한 경우, [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요.

## <a name="sample-scripts"></a>샘플 스크립트

### <a name="sign-in-to-azure"></a>Azure에 로그인

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

### <a name="run-the-script"></a>스크립트 실행

[!code-azurecli-interactive[main](../../../cli_scripts/sql-database/failover-groups/add-managed-instance-to-failover-group-az-cli.sh "Add managed instance to a failover group")]

### <a name="clean-up-deployment"></a>배포 정리

다음 명령을 사용하여 리소스 그룹 및 모든 관련 리소스를 제거합니다. 리소스 그룹을 두 번 제거해야 합니다. 리소스 그룹을 처음으로 제거하면 관리형 인스턴스와 가상 클러스터가 제거되지만, `az group delete : Long running operation failed with status 'Conflict'.` 오류 메시지와 함께 실패하게 됩니다. az group delete 명령을 한 번 더 실행하여 리소스 그룹뿐 아니라 나머지 리소스를 모두 제거합니다.

```azurecli-interactive
az group delete --name $resource
```

## <a name="sample-reference"></a>샘플 참조

이 스크립트는 다음 명령을 사용합니다. 테이블에 있는 각 명령은 명령에 해당하는 문서에 연결됩니다.

| 명령 | 설명 |
|---|---|
| [az network vnet](/cli/azure/network/vnet) | 가상 네트워크 명령입니다.  |
| [az network vnet subnet](/cli/azure/network/vnet/subnet) | Virtual Network 서브넷 명령입니다. |
| [az network nsg](/cli/azure/network/nsg) | 네트워크 보안 그룹 명령입니다. |
| [az network nsg rule](/cli/azure/network/nsg/rule)| 네트워크 보안 규칙 명령입니다. |
| [az network route-table](/cli/azure/network/route-table) | 경로 테이블 명령입니다. |
| [az sql mi](/cli/azure/sql/mi) | SQL Managed Instance 명령입니다. |
| [az network public-ip](/cli/azure/network/public-ip) | 네트워크 공용 IP 주소 명령입니다. |
| [az network vnet-gateway](/cli/azure/network/vnet-gateway) | Virtual Network Gateway 명령 |
| [az sql instance-failover-group](/cli/azure/sql/instance-failover-group) | SQL Managed Instance 장애 조치(failover) 그룹 명령입니다. |

## <a name="next-steps"></a>다음 단계

Azure CLI에 대한 자세한 내용은 [Azure CLI 설명서](/cli/azure)를 참조하세요.

추가 SQL Database CLI 스크립트 샘플은 [Azure SQL Database 설명서](../../azure-sql/database/az-cli-script-samples-content-guide.md)에서 찾을 수 있습니다.
