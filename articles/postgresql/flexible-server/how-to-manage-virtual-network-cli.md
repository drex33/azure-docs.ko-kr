---
title: 가상 네트워크 관리 - Azure CLI - Azure Database for PostgreSQL - 유연한 서버
description: Azure CLI를 사용하여 Azure Database for PostgreSQL - 유연한 서버에 대한 가상 네트워크 만들기 및 관리
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: how-to
ms.date: 11/30/2021
ms.openlocfilehash: 1b82a06626a56464736a62b9b47d5f5be23b0ae3
ms.sourcegitcommit: dcf3424d7149fceaea0340eb0657baa2c27882a5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/30/2021
ms.locfileid: "133271216"
---
# <a name="create-and-manage-virtual-networks-for-azure-database-for-postgresql---flexible-server-using-the-azure-cli"></a>Azure CLI를 사용하여 Azure Database for PostgreSQL - 유연한 서버에 대한 가상 네트워크 만들기 및 관리



Azure Database for PostgreSQL - 유연한 서버는 유연한 서버에 연결하는 두 가지 유형의 상호 배타적인 네트워크 연결 방법을 지원합니다. 다음은 두 가지 옵션입니다.

* 퍼블릭 액세스(허용된 IP 주소)
* 프라이빗 액세스(VNet 통합)

이 문서에서는 Azure CLI를 사용하여 **개인 액세스(VNet 통합)** 권한으로 PostgreSQL 서버를 만드는 방법에 중점을 둡니다. *개인 액세스(VNet 통합)* 를 사용하면 고유한 [Azure Virtual Network](../../virtual-network/virtual-networks-overview.md)에 유연한 서버를 배포할 수 있습니다. Azure 가상 네트워크는 프라이빗하고 안전한 네트워크 통신을 제공합니다. 개인 액세스에서 PostgreSQL 서버에 대한 연결은 가상 네트워크 내에서만 사용할 수 있습니다. 이에 대해 자세히 알아보려면 [개인 액세스(VNet 통합)](./concepts-networking.md#private-access-vnet-integration)를 참조하세요.

Azure Database for PostgreSQL - 유연한 서버에서 서버를 만드는 동안 서버를 가상 네트워크 및 서브넷에 배포할 수 있습니다. 유연한 서버를 가상 네트워크 및 서브넷에 배포한 후에는 다른 가상 네트워크, 서브넷 또는 *공용 액세스(허용된 IP 주소)* 로 이동할 수 없습니다.

## <a name="launch-azure-cloud-shell"></a>Azure Cloud Shell 시작

[Azure Cloud Shell](../../cloud-shell/overview.md)은 이 문서의 단계를 실행하는 데 무료로 사용할 수 있는 대화형 셸입니다. 공용 Azure 도구가 사전 설치되어 계정에서 사용하도록 구성되어 있습니다.

Cloud Shell을 열려면 코드 블록의 오른쪽 위 모서리에 있는 **사용해 보세요** 를 선택하기만 하면 됩니다. 또한 [https://shell.azure.com/bash](https://shell.azure.com/bash)로 이동하여 별도의 브라우저 탭에서 Cloud Shell을 열 수도 있습니다. **복사** 를 선택하여 코드 블록을 복사하여 Cloud Shell에 붙여넣고, **Enter** 를 선택하여 실행합니다.

CLI를 로컬로 설치하고 사용하려면 이 빠른 시작에서 Azure CLI 버전 2.0 이상이 필요합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요.

## <a name="prerequisites"></a>필수 구성 요소

[az login](/cli/azure/reference-index#az_login) 명령을 사용하여 계정에 로그인해야 합니다. Azure 계정에 대한 **구독 ID** 를 참조하는 **ID** 속성을 기록해 둡니다.

```azurecli-interactive
az login
```

[az account set](/cli/azure/account#az_account_set) 명령을 사용하여 계정에 속한 특정 구독을 선택합니다. 명령에서 **subscription** 인수 값으로 사용할 **az login** 출력의 **ID** 값을 적어 둡니다. 구독이 여러 개인 경우 리소스가 과금되어야 할 적절한 구독을 선택합니다. 모든 구독을 가져오려면 [az account list](/cli/azure/account#az_account_list)를 사용합니다.

```azurecli
az account set --subscription <subscription id>
```

## <a name="create-azure-database-for-postgresql---flexible-server-using-cli"></a>CLI를 사용하여 Azure Database for PostgreSQL - 유연한 서버 만들기
`az postgres flexible-server` 명령을 사용하여 *개인 액세스(VNet 통합)* 권한으로 유연한 서버를 만들 수 있습니다. 이 명령은 기본 연결 방법으로 개인 액세스(VNet 통합)를 사용합니다. 아무 것도 제공되지 않으면 가상 네트워크와 서브넷이 만들어집니다. 서브넷 ID를 사용하여 기존 가상 네트워크 및 서브넷을 제공할 수도 있습니다. <!-- You can provide the **vnet**,**subnet**,**vnet-address-prefix** or**subnet-address-prefix** to customize the virtual network and subnet.--> 아래 예와 같이 CLI를 사용하여 유연한 서버를 만드는 다양한 옵션이 있습니다.

>[!Important]
> 이 명령을 사용하면 서브넷이 **Microsoft.DBforPostgreSQL/flexibleServers** 에 위임됩니다. 이렇게 위임하면 Azure Database for PostgreSQL 유연한 서버에서만 해당 서브넷을 사용할 수 있습니다. 다른 Azure 리소스 유형은 위임된 서브넷에 있을 수 없습니다.
>
Azure CLI 참조 문서를 참조하세요. <!--FIXME --> 구성 가능한 CLI 매개 변수의 전체 목록을 참조하세요. 예를 들어 아래 명령에서 선택적으로 리소스 그룹을 지정할 수 있습니다.

- 기본 가상 네트워크, 기본 주소 접두사가 포함된 서브넷을 사용하여 유연한 서버 만들기
    ```azurecli-interactive
    az postgres flexible-server create
    ```
- 기존 가상 네트워크와 서브넷을 사용하여 유연한 서버를 만듭니다. 제공된 가상 네트워크 및 서브넷이 없는 경우 가상 네트워크 및 기본 주소 접두사가 있는 서브넷이 생성됩니다.
    ```azurecli-interactive
    az postgres flexible-server create --vnet myVnet --subnet mySubnet
    ```
- 이미 있는 기존 가상 네트워크, 서브넷 및 서브넷 ID를 사용하여 유연한 서버를 만듭니다. 제공된 서브넷에는 다른 리소스가 배포되어서는 안 되며 이 서브넷은 아직 위임되지 않은 경우  **Microsoft.DBforPostgreSQL/flexibleServers** 에 위임됩니다.
    ```azurecli-interactive
    az postgres flexible-server create --subnet /subscriptions/{SubID}/resourceGroups/{ResourceGroup}/providers/Microsoft.Network/virtualNetworks/{VNetName}/subnets/{SubnetName}
    ```
    > [!Note]
    > 가상 네트워크 및 서브넷은 유연한 서버와 동일한 지역 및 구독에 있어야 합니다.

    > [!IMPORTANT]
    > `AzureFirewallSubnet`, `AzureFirewallManagementSubnet`, `AzureBastionSubnet` 및 `GatewaySubnet`을 포함한 이름은 Azure 내에서 예약된 이름입니다. 해당 이름을 서브넷 이름으로 사용하지 마세요.

- 새 가상 네트워크, 기본 주소가 아닌 접두사가 있는 서브넷을 사용하여 유연한 서버 만들기
    ```azurecli-interactive
    az postgres flexible-server create --vnet myVnet --address-prefixes 10.0.0.0/24 --subnet mySubnet --subnet-prefixes 10.0.0.0/24
    ```
구성 가능한 CLI 매개 변수의 전체 목록은 Azure CLI [참조 설명서](/cli/azure/postgres/flexible-server)를 참조하세요.

>[!Important]
> `The parameter PrivateDnsZoneArguments is required, and must be provided by customer` 오류가 발생하면 이전 버전의 Azure CLI를 실행 중일 수 있습니다. [Azure CLI를 업그레이드](/cli/azure/update-azure-cli)하고 작업을 다시 시도하세요.

## <a name="next-steps"></a>다음 단계
- [Azure Database for PostgreSQL - 유연한 서버의 네트워킹](./concepts-networking.md)에 대해 자세히 알아봅니다.
- [Azure Portal을 사용하여 Azure Database for PostgreSQL - 유연한 서버 가상 네트워크 만들기 및 관리](./how-to-manage-virtual-network-portal.md)를 수행합니다.
- [Azure Database for PostgreSQL - 유연한 서버 가상 네트워크](./concepts-networking.md#private-access-vnet-integration)에 대해 자세히 알아봅니다.