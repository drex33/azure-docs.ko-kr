---
title: CLI 스크립트 - VNet에서 Azure Database for MySQL - 유연한 서버 만들기
description: 이 Azure CLI 샘플 스크립트는 VNet(프라이빗 액세스 연결 방법)에서 Azure Database for MySQL - 유연한 서버를 만들고, VNet 내의 VM에서 서버에 연결하는 방법을 보여 줍니다.
author: shreyaaithal
ms.author: shaithal
ms.service: mysql
ms.devlang: azurecli
ms.topic: sample
ms.custom: mvc, devx-track-azurecli
ms.date: 09/15/2021
ms.openlocfilehash: 4d7972293652d765cdb1686ff7759a41104f8229
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131844243"
---
# <a name="create-an-azure-database-for-mysql---flexible-server-in-a-vnet-using-azure-cli"></a>Azure CLI를 사용하여 VNet에서 Azure Database for MySQL - 유연한 서버 만들기

이 CLI 샘플 스크립트는 VNet([프라이빗 액세스 연결 방법](../concepts-networking-vnet.md))에서 Azure Database for MySQL - 유연한 서버를 만들고 VNet 내의 VM에서 서버에 연결합니다.

> [!NOTE] 
> 서버를 만든 후에는 연결 방법을 변경할 수 없습니다. 예를 들어 *프라이빗 액세스(VNet 통합)* 를 사용하여 서버를 만드는 경우 만든 후에 *퍼블릭 액세스(허용된 IP 주소)* 로 변경할 수 없습니다. 연결 방법에 대한 자세한 내용은 [네트워킹 개념](../concepts-networking.md)을 참조하세요.

[!INCLUDE [flexible-server-free-trial-note](../../includes/flexible-server-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment](../../../../includes/azure-cli-prepare-your-environment.md)]

- 이 문서에는 Azure CLI 버전 2.0 이상이 필요합니다. Azure Cloud Shell을 사용하는 경우 최신 버전이 이미 설치되어 있습니다. 

## <a name="sample-script"></a>샘플 스크립트

변수 값을 사용하여 스크립트에서 강조 표시된 줄을 편집합니다.

[!code-azurecli-interactive[main](../../../../cli_scripts/mysql/flexible-server/create-server-private-access/create-connect-server-in-vnet.sh?highlight=7,10 "Create and Connect to an Azure Database for MySQL - Flexible Server (General Purpose SKU) in VNet")]

## <a name="clean-up-deployment"></a>배포 정리

샘플 스크립트가 실행되었으면 다음 코드 조각을 사용하여 리소스를 정리할 수 있습니다.

[!code-azurecli-interactive[main](../../../../cli_scripts/mysql/flexible-server/create-server-private-access/clean-up-resources.sh "Clean up resources.")]

## <a name="script-explanation"></a>스크립트 설명

이 스크립트는 다음 명령을 사용합니다. 테이블에 있는 각 명령은 명령에 해당하는 문서에 연결됩니다.

| **명령** | **참고 사항** |
|---|---|
|[az group create](/cli/azure/group#az_group_create)|모든 리소스가 저장되는 리소스 그룹을 만듭니다.|
|[az mysql flexible-server create](/cli/azure/mysql/flexible-server#az_mysql_flexible_server_create)|데이터베이스를 호스트하는 유연한 서버를 만듭니다.|
|[az network vnet subnet create](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create)|VNet 내에서 서브넷을 만듭니다.|
|[az vm create](/cli/azure/vm#az_vm_create)|Azure Virtual Machine을 만듭니다.|
|[az vm open-port](/cli/azure/vm#az_vm_open_port)|지정된 포트에서 인바운드 트래픽에 대한 VM을 엽니다.|
|[az group delete](/cli/azure/group#az_group_delete) | 모든 중첩 리소스를 포함한 리소스 그룹을 삭제합니다.|

## <a name="next-steps"></a>다음 단계

- 추가 스크립트 시도: [Azure Database for MySQL - 유연한 서버용 Azure CLI 샘플](../sample-scripts-azure-cli.md)
- Azure CLI에 대한 자세한 내용은 [Azure CLI 설명서](/cli/azure)를 참조하세요.