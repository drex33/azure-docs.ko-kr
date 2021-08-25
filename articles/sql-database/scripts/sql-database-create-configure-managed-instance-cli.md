---
title: 'Azure CLI: 관리되는 인스턴스 만들기'
description: Azure SQL Managed Instance에서 관리형 인스턴스를 만드는 Azure CLI 예제 스크립트
services: sql-managed-instance
ms.service: sql-managed-instance
ms.subservice: deployment-configuration
ms.custom: devx-track-azurecli
ms.devlang: azurecli
ms.topic: sample
author: urosmil
ms.author: urmilano
ms.reviewer: mathoma
ms.date: 03/25/2019
ms.openlocfilehash: 479eb8f343011748aa3f89b6416c9f4b8cfe9b38
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "121747108"
---
# <a name="use-cli-to-create-an-azure-sql-managed-instance"></a>CLI를 사용하여 Azure SQL Managed Instance 만들기

이 Azure CLI 스크립트 예제는 새 가상 네트워크 내에서 전용 서브넷에 Azure SQL Managed Instance를 만듭니다. 또한 가상 네트워크에 대한 네트워크 보안 그룹과 경로 테이블을 구성합니다. 스크립트가 성공적으로 실행되면 가상 네트워크나 온-프레미스 환경 내에서 관리형 인스턴스에 액세스할 수 있습니다. [Azure SQL Managed Instance에 연결하도록 Azure VM 구성]../../azure-sql/managed-instance/connect-vm-instance-configure.md) 및 [온-프레미스에서 Azure SQL Managed Instance로의 지점 및 사이트 간 연결 구성](../../azure-sql/managed-instance/point-to-site-p2s-configure.md)을 참조하세요.

> [!IMPORTANT]
> 제한 사항은 [지원되는 지역](../../azure-sql/managed-instance/resource-limits.md#supported-regions)과 [지원되는 구독 유형](../../azure-sql/managed-instance/resource-limits.md#supported-subscription-types)을 참조하세요.

CLI를 로컬로 설치하여 사용하도록 선택하는 경우 이 문서에서 Azure CLI 버전 2.0 이상을 실행해야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드가 필요한 경우, [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요.

## <a name="sample-script"></a>샘플 스크립트

### <a name="sign-in-to-azure"></a>Azure에 로그인

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

### <a name="run-the-script"></a>스크립트 실행

[!code-azurecli-interactive[main](../../../cli_scripts/sql-database/managed-instance/create-managed-instance.sh "Create managed instance")]

### <a name="clean-up-deployment"></a>배포 정리

다음 명령을 사용하여 리소스 그룹 및 모든 관련 리소스를 제거합니다.

```azurecli-interactive
az group delete --name $resource
```

## <a name="sample-reference"></a>샘플 참조

이 스크립트는 다음 명령을 사용합니다. 테이블에 있는 각 명령은 명령에 해당하는 문서에 연결됩니다.

| 명령 | 설명 |
|---|---|
| [az network vnet](/cli/azure/network/vnet) | 가상 네트워크 명령입니다. |
| [az network vnet subnet](/cli/azure/network/vnet/subnet) | Virtual Network 서브넷 명령입니다. |
| [az network route-table](/cli/azure/network/route-table) | 네트워크 경로 테이블 명령입니다. |
| [az sql mi](/cli/azure/sql/mi) | SQL Managed Instance 명령입니다. |

## <a name="next-steps"></a>다음 단계

Azure CLI에 대한 자세한 내용은 [Azure CLI 설명서](/cli/azure)를 참조하세요.

추가 SQL Database CLI 스크립트 샘플은 [Azure SQL Database 설명서](../../azure-sql/database/az-cli-script-samples-content-guide.md)에서 찾을 수 있습니다.
