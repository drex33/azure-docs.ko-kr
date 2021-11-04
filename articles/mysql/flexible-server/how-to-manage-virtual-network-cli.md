---
title: 가상 네트워크 관리 - Azure CLI - Azure Database for MySQL - 유연한 서버
description: Azure CLI를 사용하여 Azure Database for MySQL - 유연한 서버 가상 네트워크 만들기 및 관리
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 9/21/2020
ms.openlocfilehash: 5767eacad78b775514483d7c2e90de7d74eee7fe
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131468046"
---
# <a name="create-and-manage-virtual-networks-for-azure-database-for-mysql-flexible-server-using-the-azure-cli"></a>Azure CLI를 사용 하 Azure Database for MySQL 유연한 서버를 위한 가상 네트워크 만들기 및 관리


Azure Database for MySQL 유동 서버는 유동 서버에 연결하는 두 가지 유형의 상호 배타적인 네트워크 연결 방법을 지원합니다. 다음은 두 가지 옵션입니다.

- 퍼블릭 액세스(허용된 IP 주소)
- 프라이빗 액세스(VNet 통합)

이 문서에서는 Azure CLI를 사용하여 **개인 액세스(VNet 통합)** 권한으로 MySQL 서버를 만드는 방법에 중점을 둡니다. *개인 액세스(VNet 통합)* 를 사용하여 고유한 [Azure Virtual Network](../../virtual-network/virtual-networks-overview.md)에 유연한 서버를 배포할 수 있습니다. Azure Virtual Network는 안전한 비공개 네트워크 통신을 제공합니다. 개인 액세스에서 MySQL 서버에 대한 연결은 가상 네트워크 이내로만 제한됩니다. 이에 대해 자세히 알아보려면 [개인 액세스(VNet 통합)](./concepts-networking-vnet.md)를 참조하세요.

Azure Database for MySQL 유연한 서버에서 서버를 만드는 동안에만 서버를 가상 네트워크 및 서브넷에 배포할 수 있습니다. 유연한 서버를 가상 네트워크 및 서브넷에 배포한 후에는 다른 가상 네트워크, 서브넷 또는 *공용 액세스(허용된 IP 주소)* 로 이동할 수 없습니다.

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

## <a name="create-azure-database-for-mysql-flexible-server-using-cli"></a>CLI를 사용하여 Azure Database for MySQL 유연한 서버 만들기
`az mysql flexible-server` 명령을 사용하여 *개인 액세스(VNet 통합)* 권한으로 유연한 서버를 만들 수 있습니다. 이 명령은 기본 연결 방법으로 개인 액세스(VNet 통합)를 사용합니다. 가상 네트워크 및 서브넷이 제공되지 않은 경우 만들어집니다. 서브넷 ID를 사용하여 기존 가상 네트워크 및 서브넷을 제공할 수도 있습니다. <!-- You can provide the **vnet**,**subnet**,**vnet-address-prefix** or**subnet-address-prefix** to customize the virtual network and subnet.--> 아래 예제와 같이 CLI를 사용하여 유연한 서버를 만들 수 있는 옵션은 여러 가지가 있습니다.

>[!Important]
> 이 명령을 사용하면 서브넷이 **Microsoft.DBforMySQL/flexibleServers** 에 위임됩니다. 이렇게 위임하면 Azure Database for MySQL 유동 서버에서만 해당 서브넷을 사용할 수 있습니다. 다른 Azure 리소스 유형은 위임된 서브넷에 있을 수 없습니다.
>

구성 가능한 CLI 매개 변수의 전체 목록은 Azure CLI [참조 설명서](/cli/azure/mysql/flexible-server)를 참조하세요. 예를 들면, 아래 명령에서 필요에 따라 리소스 그룹을 지정할 수 있습니다.

- 기본 가상 네트워크, 기본 주소 접두사가 포함된 서브넷을 사용하여 유연한 서버 만들기
    ```azurecli-interactive
    az mysql flexible-server create
    ```
- 기존 가상 네트워크와 서브넷을 사용하여 유연한 서버를 만듭니다. 제공된 가상 네트워크 및 서브넷이 없는 경우 가상 네트워크 및 기본 주소 접두사가 있는 서브넷이 생성됩니다.
    ```azurecli-interactive
    az mysql flexible-server create --vnet myVnet --subnet mySubnet
    ```

- 기존 가상 네트워크, 서브넷 및 서브넷 ID를 사용하여 유연한 서버를 만듭니다. 제공된 서브넷은 다른 리소스를 배포하지 않아야 하고, 이 서브넷은 **Microsoft.DBforMySQL/flexibleServers** 에 위임됩니다(아직 위임되지 않은 경우).
    ```azurecli-interactive
    az mysql flexible-server create --subnet /subscriptions/{SubID}/resourceGroups/{ResourceGroup}/providers/Microsoft.Network/virtualNetworks/{VNetName}/subnets/{SubnetName}
    ```
    > [!Note]
    > 가상 네트워크 및 서브넷은 유연한 서버와 동일한 지역 및 구독에 있어야 합니다.
<
- 새 가상 네트워크, 기본 주소가 아닌 접두사가 포함된 서브넷을 사용하여 유연한 서버를 만듭니다.
    ```azurecli-interactive
    az mysql flexible-server create --vnet myVnet --address-prefixes 10.0.0.0/24 --subnet mySubnet --subnet-prefixes 10.0.0.0/24
    ```
구성 가능한 CLI 매개 변수의 전체 목록은 Azure CLI [참조 설명서](/cli/azure/mysql/flexible-server)를 참조하세요.


## <a name="next-steps"></a>다음 단계
- [Azure Database for MySQL 유연한 서버의 네트워킹](./concepts-networking.md)에 대해 자세히 알아봅니다.
- [Azure Portal을 사용하여 Azure Database for MySQL 유연한 서버 가상 네트워크 만들기 및 관리](./how-to-manage-virtual-network-portal.md).
- [Azure Database for MySQL 유연한 서버 가상 네트워크](./concepts-networking-vnet.md#private-access-vnet-integration)에 대해 자세히 알아봅니다.