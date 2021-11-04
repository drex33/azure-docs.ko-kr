---
title: Azure Storage 방화벽 및 가상 네트워크 구성 | Microsoft Docs
description: Azure Storage 방화벽과 Azure Virtual Network를 사용해 스토리지 계정에 계층화된 네트워크 보안을 구성합니다.
services: storage
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 06/09/2021
ms.author: normesta
ms.reviewer: santoshc
ms.subservice: common
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 8ffa380ec55dfcf96368782a3345ce3d13fe5f4f
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131474840"
---
# <a name="configure-azure-storage-firewalls-and-virtual-networks"></a>Azure Storage 방화벽 및 가상 네트워크 구성

Azure Storage는 계층화된 보안 모델을 제공합니다. 이 모델을 사용하면 사용되는 네트워크의 유형 및 하위 집합에 따라 애플리케이션 및 기업 환경에 필요한 스토리지 계정에 대한 액세스 수준을 보호하고 제어할 수 있습니다. 네트워크 규칙이 구성되면 지정된 네트워크 집합 및 지정된 Azure 리소스 집합을 통해 데이터를 요청하는 애플리케이션만 스토리지 계정에 액세스할 수 있습니다. 스토리지 계정에 대한 액세스를 지정된 IP 주소, IP 범위, Azure VNet(Virtual Network )의 서브넷 또는 일부 Azure 서비스의 리소스 인스턴스에서 오는 요청만으로 제한할 수 있습니다.

스토리지 계정에는 인터넷을 통해 액세스할 수 있는 퍼블릭 엔드포인트가 있습니다. VNet에서 스토리지 계정으로 프라이빗 IP 주소를 할당하고 프라이빗 링크를 통과하는 VNet 및 스토리지 계정 간의 모든 트래픽을 보호하는 [스토리지 계정에 대한 프라이빗 엔드포인트](storage-private-endpoints.md)를 만들 수도 있습니다. Azure 스토리지 방화벽은 스토리지 계정의 퍼블릭 엔드포인트에 액세스 제어 기능을 제공합니다. 또한 프라이빗 엔드포인트를 사용할 때 방화벽을 사용하여 퍼블릭 엔드포인트를 통한 모든 액세스를 차단할 수도 있습니다. 스토리지 방화벽 구성을 사용하여 선택한 신뢰할 수 있는 Azure 플랫폼 서비스에서 스토리지 계정에 안전하게 액세스할 수 있도록 합니다.

네트워크 규칙이 적용될 때 스토리지 계정에 액세스하는 애플리케이션에는 요청에 대한 적절한 권한 부여가 필요합니다. 권한 부여는 Azure AD(Azure Active Directory) 자격 증명(Blob 및 큐용), 유효한 계정 액세스 키 또는 SAS 토큰을 통해 지원됩니다.

> [!IMPORTANT]
> 스토리지 계정에 대해 방화벽 규칙을 켜면 기본적으로 Azure VNet(Virtual Network) 내에서 작동하는 서비스 또는 허용되는 퍼블릭 IP 주소에서 요청하지 않는 한, 들어오는 데이터 요청이 차단됩니다. 차단되는 요청에는 다른 Azure 서비스, Azure Portal, 로깅 및 메트릭 서비스 등이 포함됩니다.
>
> 서비스 인스턴스를 호스트하는 서브넷의 트래픽을 허용하여 VNet 내에서 작동하는 Azure 서비스에 대한 액세스 권한을 부여할 수 있습니다. 아래에 설명된 예외 메커니즘을 통해 제한된 수의 시나리오를 사용하도록 설정할 수도 있습니다. Azure Portal을 통해 스토리지 계정의 데이터에 액세스하려면 설정한 신뢰할 수 있는 경계(IP 또는 VNet) 내의 머신에 있어야 합니다.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="scenarios"></a>시나리오

스토리지 계정을 보호하려면 먼저 퍼블릭 엔드포인트에서 모든 네트워크(인터넷 트래픽 포함)의 트래픽에 대한 액세스를 거부하도록 규칙을 구성해야 합니다. 그런 다음, 특정 VNet 트래픽에 대한 액세스를 허가하는 규칙을 구성해야 합니다. 또한 퍼블릭 인터넷 IP 주소 범위의 트래픽에 대한 액세스를 허가하도록 규칙을 구성하여 특정 인터넷 또는 온-프레미스 클라이언트의 연결을 사용하도록 설정할 수도 있습니다. 이 구성을 사용하면 애플리케이션에 대한 보안 네트워크 경계를 구축할 수 있습니다.

특정 가상 네트워크의 액세스를 허용하는 방화벽 규칙과 동일한 스토리지 계정의 퍼블릭 IP 주소 범위에서의 액세스를 허용하는 방화벽 규칙을 결합할 수 있습니다. 스토리지 방화벽 규칙을 기존 스토리지 계정에 적용하거나 새 스토리지 계정을 만들 때 적용할 수 있습니다.

스토리지 방화벽 규칙은 스토리지 계정의 퍼블릭 엔드포인트에 적용됩니다. 스토리지 계정의 프라이빗 엔드포인트에 대한 트래픽을 허용하는 방화벽 액세스 규칙은 필요하지 않습니다. 프라이빗 엔드포인트의 생성을 승인하면 프라이빗 엔드포인트를 호스트하는 서브넷의 트래픽에 대해 암시적 액세스 권한이 부여됩니다.

네트워크 규칙은 REST 및 SMB를 포함해서 Azure 스토리지에 대한 모든 네트워크 프로토콜에 적용됩니다. Azure Portal, 스토리지 탐색기 및 AZCopy와 같은 도구를 사용하여 데이터에 액세스하려면 명시적 네트워크 규칙을 구성해야 합니다.

네트워크 규칙이 적용되면 모든 요청에 적용됩니다. 특정 IP 주소에 대한 액세스 권한을 부여하는 SAS 토큰은 토큰 소유자의 액세스를 제한하지만, 구성된 네트워크 규칙 이외의 새 액세스 권한은 부여하지 않습니다.

가상 머신 디스크 트래픽(탑재 및 분리 작업 및 디스크 IO 포함)은 네트워크 규칙의 영향을 받지 않습니다. 페이지 Blob에 대한 REST 액세스는 네트워크 규칙에 의해 보호됩니다.

클래식 스토리지 계정은 방화벽 및 가상 네트워크를 지원하지 않습니다.

예외를 만들어서 VM 백업 및 복원에 적용되는 네트워크 규칙이 있는 스토리지 계정에서 관리되지 않는 디스크를 사용할 수 있습니다. 이 프로세스는 이 문서의 [예외](#manage-exceptions) 섹션에서 설명하고 있습니다. 방화벽 예외는 Azure에서 이미 관리되고 있으므로 관리 디스크에는 적용되지 않습니다.

## <a name="change-the-default-network-access-rule"></a>기본 네트워크 액세스 규칙 변경

기본적으로 스토리지 계정은 네트워크에 있는 모든 클라이언트로부터의 연결을 허용합니다. 선택한 네트워크에 대한 액세스를 제한하려면 먼저 기본 동작을 변경해야 합니다.

> [!WARNING]
> 네트워크 규칙을 변경하면 Azure Storage에 연결하는 애플리케이션의 기능에 영향을 미칠 수 있습니다. 기본 네트워크 규칙을 **거부** 로 설정하면 액세스를 **허용** 하는 특정 네트워크 규칙이 적용되지 않는 한 데이터에 대한 모든 액세스가 차단됩니다. 액세스를 거부하도록 기본 규칙을 변경하기 전에 네트워크 규칙을 사용하여 허용된 모든 네트워크에 대한 액세스를 허가해야 합니다.

### <a name="managing-default-network-access-rules"></a>기본 네트워크 액세스 규칙 관리

스토리지 계정에 대한 기본 네트워크 액세스 규칙은 Azure Portal, PowerShell 또는 CLIv2를 통해 관리할 수 있습니다.

#### <a name="portal"></a>[포털](#tab/azure-portal)

1. 보호하려는 스토리지 계정으로 이동합니다.

2. **네트워킹** 이라는 설정 메뉴에서 선택합니다.

3. 기본적으로 액세스를 거부하려면 **선택한 네트워크** 에서 액세스를 허용하도록 선택합니다. 모든 네트워크의 트래픽을 허용하려면 **모든 네트워크** 에서 액세스를 허용하도록 선택합니다.

4. **저장** 을 선택하여 변경 내용을 적용합니다.

<a id="powershell"></a>

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. [Azure PowerShell](/powershell/azure/install-Az-ps)을 설치하고 [로그인](/powershell/azure/authenticate-azureps)합니다.

2. 스토리지 계정에 대한 기본 규칙의 상태를 표시합니다.

    ```powershell
    (Get-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").DefaultAction
    ```

3. 기본적으로 네트워크 액세스를 거부하도록 기본 규칙을 설정합니다.

    ```powershell
    Update-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -DefaultAction Deny
    ```

4. 기본적으로 네트워크 액세스를 허용하도록 기본 규칙을 설정합니다.

    ```powershell
    Update-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -DefaultAction Allow
    ```

#### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. [Azure CLI](/cli/azure/install-azure-cli)를 설치하고 [로그인](/cli/azure/authenticate-azure-cli)합니다.

2. 스토리지 계정에 대한 기본 규칙의 상태를 표시합니다.

    ```azurecli
    az storage account show --resource-group "myresourcegroup" --name "mystorageaccount" --query networkRuleSet.defaultAction
    ```

3. 기본적으로 네트워크 액세스를 거부하도록 기본 규칙을 설정합니다.

    ```azurecli
    az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --default-action Deny
    ```

4. 기본적으로 네트워크 액세스를 허용하도록 기본 규칙을 설정합니다.

    ```azurecli
    az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --default-action Allow
    ```

---

## <a name="grant-access-from-a-virtual-network"></a>가상 네트워크의 액세스 허가

특정 서브넷에서만 액세스를 허용하도록 스토리지 계정을 구성할 수 있습니다. 허용되는 서브넷은 동일한 구독의 VNet 또는 다른 Azure Active Directory 테넌트에 속한 구독을 포함하여 다른 구독의 VNet에 속할 수 있습니다.

VNet 내의 Azure Storage에 대해 [서비스 엔드포인트](../../virtual-network/virtual-network-service-endpoints-overview.md)를 사용하도록 설정합니다. 서비스 엔드포인트는 VNet의 트래픽을 Azure Storage 서비스에 대한 최적의 경로를 통해 라우팅합니다. 서브넷 및 가상 네트워크의 ID 또한 각 요청과 함께 전송됩니다. 그러면 관리자가 VNet의 특정 서브넷에서 요청을 받을 수 있도록 스토리지 계정에 대한 네트워크 규칙을 구성할 수 있습니다. 이러한 네트워크 규칙을 통해 액세스가 허용되는 클라이언트에서 데이터에 액세스하려면 스토리지 계정의 권한 부여 요구 사항을 계속 충족해야 합니다.

각 스토리지 계정은 [IP 네트워크 규칙](#grant-access-from-an-internet-ip-range)과 결합될 수 있는 최대 200개의 가상 네트워크 규칙을 지원합니다.

> [!IMPORTANT]
> 네트워크 규칙에 포함된 서브넷을 삭제하면 스토리지 계정에 대한 네트워크 규칙에서 제거됩니다. 동일한 이름으로 새 서브넷을 만들면 스토리지 계정에 액세스할 수 없습니다. 액세스를 허용하려면 스토리지 계정에 대한 네트워크 규칙에서 새 서브넷에 명시적으로 권한을 부여해야 합니다.

### <a name="available-virtual-network-regions"></a>사용 가능한 가상 네트워크 지역

일반적으로 서비스 엔드포인트는 동일한 Azure 지역의 가상 네트워크와 서비스 인스턴스 간에 작동합니다. Azure Storage에서 서비스 엔드포인트를 사용하는 경우 이 범위에는 [쌍을 이루는 지역](../../best-practices-availability-paired-regions.md)이 포함됩니다. 서비스 엔드포인트를 사용하면 지역별 장애 조치 및 읽기 전용 RA-GRS(지역 중복 스토리지) 인스턴스에 대한 액세스 중에 연속성을 유지할 수 있습니다. 가상 네트워크에서 스토리지 계정으로의 액세스를 허가하는 네트워크 규칙은 모든 RA-GRS 인스턴스에 대한 액세스도 허가합니다.

지역 가동 중단 시 재해 복구를 계획하는 경우 쌍을 이루는 지역에 VNet을 미리 만들어야 합니다. 이러한 대체 가상 네트워크에서 액세스할 수 있도록 허용하는 네트워크 규칙을 사용하여 Azure Storage에 대한 서비스 엔드포인트를 사용하도록 설정합니다. 그런 다음, 이러한 규칙을 지역 중복 스토리지 계정에 적용합니다.

> [!NOTE]
> 서비스 엔드포인트는 가상 네트워크 지역 및 지정된 지역 쌍 외부의 트래픽에는 적용되지 않습니다. 가상 네트워크에서 액세스할 수 있도록 허용하는 네트워크 규칙만 스토리지 계정의 주 지역 또는 쌍을 이루는 지정된 지역의 스토리지 계정에 적용할 수 있습니다.

### <a name="required-permissions"></a>필요한 사용 권한

가상 네트워크 규칙을 스토리지 계정에 적용하려면 추가되는 서브넷에 대한 적절한 권한이 사용자에게 있어야 합니다. 규칙을 적용하는 작업은 `Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action` 사용자 지정 Azure 역할을 통해 [Azure 리소스 공급자 작업](../../role-based-access-control/resource-provider-operations.md#microsoftnetwork)의 사용 권한을 부여받은 [스토리지 계정 기여자](../../role-based-access-control/built-in-roles.md#storage-account-contributor) 혹은 또는 사용자가 수행할 수 있습니다.

스토리지 계정 및 가상 네트워크에 허용된 액세스 권한은 다른 구독(다른 Azure AD 테넌트의 일부인 구독 포함)에 있을 수 있습니다.

> [!NOTE]
> 다른 Azure Active Directory 테넌트에 속한 가상 네트워크의 서브넷에 대해 액세스 권한을 부여하는 규칙의 구성은 현재 Powershell, CLI 및 REST API를 통해서만 지원됩니다. 이러한 규칙은 Azure Portal에서 볼 수 있지만 구성할 수는 없습니다.

### <a name="managing-virtual-network-rules"></a>가상 네트워크 규칙 관리

Azure Portal, PowerShell 또는 CLIv2를 통해 스토리지 계정에 대한 가상 네트워크 규칙을 관리할 수 있습니다.

#### <a name="portal"></a>[포털](#tab/azure-portal)

1. 보호하려는 스토리지 계정으로 이동합니다.

2. **네트워킹** 이라는 설정 메뉴에서 선택합니다.

3. **선택한 네트워크** 에서 액세스를 허용하도록 선택했는지 확인합니다.

4. 새 네트워크 규칙을 사용해 가상 네트워크에 액세스 권한을 부여하려면 **가상 네트워크** 에서 **기존 가상 네트워크 추가** 를 선택하고 **가상 네트워크** 및 **서브넷** 옵션을 선택한 다음 **추가** 를 선택합니다. 새 가상 네트워크를 만들고 거기에 액세스 권한을 부여하려면 **새 가상 네트워크 추가** 를 선택합니다. 새 가상 네트워크를 만드는 데 필요한 정보를 입력하고 **만들기** 를 선택합니다.

    > [!NOTE]
    > 이전에 Azure Storage에 대한 서비스 엔드포인트가 선택한 가상 네트워크 및 서브넷에 구성되지 않은 경우 이 작업의 일환으로 구성할 수 있습니다.
    >
    > 현재, 규칙을 만드는 동안 동일한 Azure Active Directory 테넌트에 속한 가상 네트워크만 선택할 수 있도록 표시됩니다. 다른 테넌트에 속한 가상 네트워크의 서브넷에 대한 액세스 권한을 부여하려면 Powershell, CLI 또는 REST API를 사용하세요.

5. 가상 네트워크 또는 서브넷 규칙을 제거하려면 **...** 을 선택해 가상 네트워크 또는 서브넷 바로 가기 메뉴를 열고 **제거** 를 선택합니다.

6. **저장** 을 선택하여 변경 내용을 적용합니다.

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. [Azure PowerShell](/powershell/azure/install-Az-ps)을 설치하고 [로그인](/powershell/azure/authenticate-azureps)합니다.

2. 가상 네트워크 규칙을 나열합니다.

    ```powershell
    (Get-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").VirtualNetworkRules
    ```

3. 기존 가상 네트워크 및 서브넷에서 Azure Storage에 대한 서비스 엔드포인트를 사용하도록 설정합니다.

    ```powershell
    Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Set-AzVirtualNetworkSubnetConfig -Name "mysubnet" -AddressPrefix "10.0.0.0/24" -ServiceEndpoint "Microsoft.Storage" | Set-AzVirtualNetwork
    ```

4. 가상 네트워크 및 서브넷에 대한 네트워크 규칙을 추가합니다.

    ```powershell
    $subnet = Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Get-AzVirtualNetworkSubnetConfig -Name "mysubnet"
    Add-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -VirtualNetworkResourceId $subnet.Id
    ```

    > [!TIP]
    > 다른 Azure AD 테넌트에 속한 VNet의 서브넷에 대한 네트워크 규칙을 추가하려면 "/subscriptions/subscription-ID/resourceGroups/resourceGroup-Name/providers/Microsoft.Network/virtualNetworks/vNet-name/subnets/subnet-name" 형식의 정규화된 **VirtualNetworkResourceId** 매개 변수를 사용합니다.

5. 가상 네트워크 및 서브넷에 대한 네트워크 규칙을 제거합니다.

    ```powershell
    $subnet = Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Get-AzVirtualNetworkSubnetConfig -Name "mysubnet"
    Remove-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -VirtualNetworkResourceId $subnet.Id
    ```

> [!IMPORTANT]
> 반드시 [기본 규칙](#change-the-default-network-access-rule)을 **거부** 로 설정해야 합니다. 그렇지 않으면 네트워크 규칙이 적용되지 않습니다.

#### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. [Azure CLI](/cli/azure/install-azure-cli)를 설치하고 [로그인](/cli/azure/authenticate-azure-cli)합니다.

2. 가상 네트워크 규칙을 나열합니다.

    ```azurecli
    az storage account network-rule list --resource-group "myresourcegroup" --account-name "mystorageaccount" --query virtualNetworkRules
    ```

3. 기존 가상 네트워크 및 서브넷에서 Azure Storage에 대한 서비스 엔드포인트를 사용하도록 설정합니다.

    ```azurecli
    az network vnet subnet update --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --service-endpoints "Microsoft.Storage"
    ```

4. 가상 네트워크 및 서브넷에 대한 네트워크 규칙을 추가합니다.

    ```azurecli
    subnetid=$(az network vnet subnet show --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --query id --output tsv)
    az storage account network-rule add --resource-group "myresourcegroup" --account-name "mystorageaccount" --subnet $subnetid
    ```

    > [!TIP]
    > 다른 Azure AD 테넌트에 속한 VNet의 서브넷에 규칙을 추가하려면 “/subscriptions/\<subscription-ID\>/resourceGroups/\<resourceGroup-Name\>/providers/Microsoft.Network/virtualNetworks/\<vNet-name\>/subnets/\<subnet-name\>” 형식의 정규화된 서브넷 ID를 사용합니다.
    >
    > **subscription** 매개 변수를 사용하여 다른 Azure AD 테넌트에 속한 VNet의 서브넷 ID를 검색할 수 있습니다.

5. 가상 네트워크 및 서브넷에 대한 네트워크 규칙을 제거합니다.

    ```azurecli
    subnetid=$(az network vnet subnet show --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --query id --output tsv)
    az storage account network-rule remove --resource-group "myresourcegroup" --account-name "mystorageaccount" --subnet $subnetid
    ```

> [!IMPORTANT]
> 반드시 [기본 규칙](#change-the-default-network-access-rule)을 **거부** 로 설정해야 합니다. 그렇지 않으면 네트워크 규칙이 적용되지 않습니다.

---

## <a name="grant-access-from-an-internet-ip-range"></a>인터넷 IP 범위의 액세스 허가

IP 네트워크 규칙을 이용해 IP 네트워크 규칙을 만들어서 특정 퍼블릭 인터넷 IP 주소 범위에서만 액세스를 허용할 수 있습니다. 각 스토리지 계정은 최대 200개의 규칙을 지원합니다. 이러한 규칙은 특정 인터넷 기반 서비스 및 온-프레미스 네트워크의 액세스 권한을 부여하고 일반 인터넷 트래픽을 차단합니다.

IP 주소 범위에는 다음과 같은 제한 사항이 적용됩니다.

- IP 네트워크 규칙은 **퍼블릭 인터넷** IP 주소에만 허용됩니다.

  사설망에 예약된 IP 주소 범위([RFC 1918](https://tools.ietf.org/html/rfc1918#section-3)에 정의된 대로)는 IP 규칙에서 허용되지 않습니다. 개인 네트워크에는 *10. * *, *172.16** 으로 시작 하는 주소가 포함 됩니다. -* 172.31. * * 및 * 192.168. * *.

- *16.17.18.0/24* 형식의 [CIDR 표기법](https://tools.ietf.org/html/rfc4632)을 사용해 허용되는 인터넷 주소 범위를 제공하거나 *16.17.18.19* 같은 개별 IP 주소를 제공해야 합니다.

- "/31" 또는 "/32" 접두사 크기를 사용하는 작은 주소 범위는 지원되지 않습니다. 이러한 범위는 개별 IP 주소 규칙을 사용하여 구성해야 합니다.

- 스토리지 방화벽 규칙의 구성에는 IPV4 주소만 지원됩니다.

IP 네트워크 규칙은 다음과 같은 경우에는 사용할 수 없습니다.

- 스토리지 계정과 동일한 Azure 지역에 있는 클라이언트에 대한 액세스를 제한할 때.

  IP 네트워크 규칙은 스토리지 계정과 동일한 Azure 지역에서 시작된 요청에 영향을 주지 않습니다. 동일한 지역 요청을 허용하려면 [가상 네트워크 규칙](#grant-access-from-a-virtual-network)을 사용하세요.

- 서비스 엔드포인트가 있는 VNet에 있는 [쌍을 이루는 지역](../../best-practices-availability-paired-regions.md)에 있는 클라이언트에 대한 액세스를 제한할 때.

- 스토리지 계정과 동일한 지역에 배포된 Azure 서비스에 대한 액세스를 제한할 때.

  스토리지 계정과 동일한 지역에 배포된 서비스는 통신을 위해 프라이빗 Azure IP 주소를 사용합니다. 따라서 퍼블릭 아웃바운드 IP 주소 범위에 따라 특정 Azure 서비스에 대한 액세스를 제한할 수 없습니다.

### <a name="configuring-access-from-on-premises-networks"></a>온-프레미스 네트워크에서의 액세스 구성

IP 네트워크 규칙을 사용하여 온-프레미스 네트워크에서 스토리지 계정으로의 액세스 권한을 부여하려면 네트워크에서 사용되는 인터넷 연결 IP 주소를 식별해야 합니다. 네트워크 관리자에게 도움을 요청합니다.

공용 피어링 또는 Microsoft 피어링을 위해 온-프레미스에서 [ExpressRoute](../../expressroute/expressroute-introduction.md)를 사용하는 경우 사용되는 NAT IP 주소를 식별해야 합니다. 공용 피어링의 경우 기본적으로 각 ExpressRoute 회로는 트래픽이 Microsoft Azure 네트워크 백본으로 들어갈 때 Azure 서비스 트래픽에 적용되는 두 개의 NAT IP 주소를 사용합니다. Microsoft 피어링의 경우 사용되는 NAT IP 주소는 고객이 제공하거나 서비스 공급자가 제공합니다. 서비스 리소스에 대한 액세스를 허용하려면 리소스 IP 방화벽 설정에서 이러한 공용 IP 주소를 허용해야 합니다. ExpressRoute 회로 IP 주소를 찾으려면 Azure Portal을 통해 [ExpressRoute에서 지원 티켓을 엽니다](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview). [ExpressRoute 공용 및 Microsoft 피어링을 위한 NAT](../../expressroute/expressroute-nat.md#nat-requirements-for-azure-public-peering)에 대해 자세히 알아보세요.

### <a name="managing-ip-network-rules"></a>IP 네트워크 규칙 관리

Azure Portal, PowerShell 또는 CLIv2를 통해 스토리지 계정에 대한 IP 네트워크 규칙을 관리할 수 있습니다.

#### <a name="portal"></a>[포털](#tab/azure-portal)

1. 보호하려는 스토리지 계정으로 이동합니다.

2. **네트워킹** 이라는 설정 메뉴에서 선택합니다.

3. **선택한 네트워크** 에서 액세스를 허용하도록 선택했는지 확인합니다.

4. 인터넷 IP 범위에 대한 액세스 권한을 부여하려면 **방화벽** > **주소 범위** 아래에서 IP 주소 또는 주소 범위(CIDR 형식)를 입력합니다.

5. IP 네트워크 규칙을 제거하려면 주소 범위 옆에 있는 휴지통 아이콘을 선택합니다.

6. **저장** 을 선택하여 변경 내용을 적용합니다.

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. [Azure PowerShell](/powershell/azure/install-Az-ps)을 설치하고 [로그인](/powershell/azure/authenticate-azureps)합니다.

2. IP 네트워크 규칙을 나열합니다.

    ```powershell
    (Get-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").IPRules
    ```

3. 개별 IP 주소에 대한 네트워크 규칙을 추가합니다.

    ```powershell
    Add-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.19"
    ```

4. IP 주소 범위에 대한 네트워크 규칙을 추가합니다.

    ```powershell
    Add-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.0/24"
    ```

5. 개별 IP 주소에 대한 네트워크 규칙을 제거합니다.

    ```powershell
    Remove-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.19"
    ```

6. IP 주소 범위에 대한 네트워크 규칙을 제거합니다.

    ```powershell
    Remove-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.0/24"
    ```

> [!IMPORTANT]
> 반드시 [기본 규칙](#change-the-default-network-access-rule)을 **거부** 로 설정해야 합니다. 그렇지 않으면 네트워크 규칙이 적용되지 않습니다.

#### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. [Azure CLI](/cli/azure/install-azure-cli)를 설치하고 [로그인](/cli/azure/authenticate-azure-cli)합니다.

1. IP 네트워크 규칙을 나열합니다.

    ```azurecli
    az storage account network-rule list --resource-group "myresourcegroup" --account-name "mystorageaccount" --query ipRules
    ```

2. 개별 IP 주소에 대한 네트워크 규칙을 추가합니다.

    ```azurecli
    az storage account network-rule add --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.19"
    ```

3. IP 주소 범위에 대한 네트워크 규칙을 추가합니다.

    ```azurecli
    az storage account network-rule add --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.0/24"
    ```

4. 개별 IP 주소에 대한 네트워크 규칙을 제거합니다.

    ```azurecli
    az storage account network-rule remove --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.19"
    ```

5. IP 주소 범위에 대한 네트워크 규칙을 제거합니다.

    ```azurecli
    az storage account network-rule remove --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.0/24"
    ```

> [!IMPORTANT]
> 반드시 [기본 규칙](#change-the-default-network-access-rule)을 **거부** 로 설정해야 합니다. 그렇지 않으면 네트워크 규칙이 적용되지 않습니다.

---

<a id="grant-access-specific-instances"></a>

## <a name="grant-access-from-azure-resource-instances-preview"></a>Azure 리소스 인스턴스에서 액세스 권한 부여(미리 보기)

경우에 따라 애플리케이션은 가상 네트워크 또는 IP 주소 규칙을 통해 격리할 수 없는 Azure 리소스에 종속될 수 있습니다. 그러나 계속 애플리케이션의 Azure 리소스에 대해서만 스토리지 계정 액세스를 보호하고 제한하고 싶을 수도 있습니다. 리소스 인스턴스 규칙을 만들어 일부 Azure 서비스의 특정 리소스 인스턴스에 대한 액세스를 허용하도록 스토리지 계정을 구성할 수 있습니다.

리소스 인스턴스가 스토리지 계정 데이터에서 수행할 수 있는 작업의 유형은 리소스 인스턴스의 Azure 역할 할당 에 의해 결정됩니다. 리소스 인스턴스는 스토리지 계정과 동일한 테넌트에 있어야 하지만 테넌트의 모든 구독에 포함될 수 있습니다.

> [!NOTE]
> 해당 기능은 공개 미리 보기로 제공되며 모든 퍼블릭 클라우드 지역에서 사용할 수 있습니다.

### <a name="portal"></a>[포털](#tab/azure-portal)

Azure Portal에서 리소스 네트워크 규칙을 추가하거나 제거할 수 있습니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인하여 시작합니다.

2. 스토리지 계정을 찾아 계정 개요를 표시합니다.

3. **네트워킹** 을 선택해 네트워킹의 구성 페이지를 표시합니다.

4. **리소스 종류** 드롭다운 목록에서 리소스 인스턴스의 리소스 종류를 선택합니다.

5. **인스턴스 이름** 드롭다운 목록에서 리소스 인스턴스를 선택합니다. 활성 테넌트, 구독 또는 리소스 그룹에 모든 리소스 인스턴스를 포함하도록 선택할 수도 있습니다.

6. **저장** 을 선택하여 변경 내용을 적용합니다. 리소스 인스턴스는 네트워크 설정 페이지의 **리소스 인스턴스** 섹션에 표시됩니다.

리소스 인스턴스를 제거하려면 리소스 인스턴스 옆에 있는 삭제 아이콘(:::image type="icon" source="media/storage-network-security/delete-icon.png":::)을 선택합니다.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell 명령을 사용해 리소스 네트워크 규칙을 추가하거나 제거할 수 있습니다.

> [!IMPORTANT]
> 반드시 [기본 규칙](#change-the-default-network-access-rule)을 **거부** 로 설정해야 합니다. 그렇지 않으면 네트워크 규칙이 적용되지 않습니다.

#### <a name="install-the-preview-module"></a>미리 보기 모듈 설치

PowershellGet 모듈의 최신 버전을 설치합니다. 그런 다음 PowerShell 콘솔을 닫았다가 다시 엽니다.

```powershell
install-Module PowerShellGet –Repository PSGallery –Force  
```

**Az. Storage** 미리 보기 모듈을 설치합니다.

```powershell
Install-Module Az.Storage -Repository PsGallery -RequiredVersion 3.0.1-preview -AllowClobber -AllowPrerelease -Force 
```

PowerShell 모듈 설치 방법에 대한 자세한 내용은 [Azure PowerShell 모듈 설치](/powershell/azure/install-az-ps)를 참조하세요.

#### <a name="grant-access"></a>액세스 권한 부여

리소스 인스턴스에서 액세스 권한을 부여하는 네트워크 규칙을 추가합니다.

```powershell
$resourceId = "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.DataFactory/factories/myDataFactory"
$tenantId = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
$resourceGroupName = "myResourceGroup"
$accountName = "mystorageaccount"

Add-AzStorageAccountNetworkRule -ResourceGroupName $resourceGroupName -Name $accountName -TenantId $tenantId -ResourceId $resourceId

```

네트워크 규칙 집합을 수정하여 한 번에 여러 리소스 인스턴스를 지정합니다.

```powershell
$resourceId1 = "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.DataFactory/factories/myDataFactory"
$resourceId2 = "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.Sql/servers/mySQLServer"
$tenantId = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
$resourceGroupName = "myResourceGroup"
$accountName = "mystorageaccount"

Update-AzStorageAccountNetworkRuleSet -ResourceGroupName $resourceGroupName -Name $accountName -ResourceAccessRule (@{ResourceId=$resourceId1;TenantId=$tenantId},@{ResourceId=$resourceId2;TenantId=$tenantId}) 
```

#### <a name="remove-access"></a>액세스 권한 제거

리소스 인스턴스에서 액세스 권한을 부여하는 네트워크 규칙을 제거합니다.

```powershell
$resourceId = "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.DataFactory/factories/myDataFactory"
$tenantId = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
$resourceGroupName = "myResourceGroup"
$accountName = "mystorageaccount"

Remove-AzStorageAccountNetworkRule -ResourceGroupName $resourceGroupName -Name $accountName -TenantId $tenantId -ResourceId $resourceId  
```

리소스 인스턴스에서 액세스 권한을 부여하는 모든 네트워크 규칙을 제거합니다.

```powershell
$resourceGroupName = "myResourceGroup"
$accountName = "mystorageaccount"

Update-AzStorageAccountNetworkRuleSet -ResourceGroupName $resourceGroupName -Name $accountName -ResourceAccessRule @()  
```

#### <a name="view-a-list-of-allowed-resource-instances"></a>허용되는 리소스 인스턴스 목록 보기

스토리지 계정에 액세스 권한이 부여된 리소스 인스턴스의 전체 목록을 표시합니다.

```powershell
$resourceGroupName = "myResourceGroup"
$accountName = "mystorageaccount"

$rule = Get-AzStorageAccountNetworkRuleSet -ResourceGroupName $resourceGroupName -Name $accountName
$rule.ResourceAccessRules 
```

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI 명령을 사용해 리소스 네트워크 규칙을 추가하거나 제거할 수 있습니다.

#### <a name="install-the-preview-extension"></a>미리 보기 확장 기능 설치

1. [Azure Cloud Shell](../../cloud-shell/overview.md)을 열거나 Azure CLI를 로컬로 [설치](/cli/azure/install-azure-cli)한 경우 Windows PowerShell과 같은 명령 콘솔 애플리케이션을 엽니다.

2. 다음 명령을 사용하여 설치된 Azure CLI 버전이 `2.13.0` 이상인지 확인합니다.

   ```azurecli
   az --version
   ```

   Azure CLI 버전이 `2.13.0`보다 낮은 경우 이후 버전을 설치합니다. [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요.

3. 다음 명령을 실행하여 미리 보기 확장 기능을 설치합니다.

   ```azurecli
   az extension add -n storage-preview
   ```

#### <a name="grant-access"></a>액세스 권한 부여

리소스 인스턴스에서 액세스 권한을 부여하는 네트워크 규칙을 추가합니다.

```azurecli
az storage account network-rule add \
    --resource-id /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.Synapse/workspaces/testworkspace \
    --tenant-id xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx \
    -g myResourceGroup \
    --account-name mystorageaccount
```

#### <a name="remove-access"></a>액세스 권한 제거

리소스 인스턴스에서 액세스 권한을 부여하는 네트워크 규칙을 제거합니다.

```azurecli
az storage account network-rule remove \
    --resource-id /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.Synapse/workspaces/testworkspace \
    --tenant-id xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx \
    -g myResourceGroup \
    --account-name mystorageaccount
```

#### <a name="view-a-list-of-allowed-resource-instances"></a>허용되는 리소스 인스턴스 목록 보기

스토리지 계정에 액세스 권한이 부여된 리소스 인스턴스의 전체 목록을 표시합니다.

```azurecli
az storage account network-rule list \
    -g myResourceGroup \
    --account-name mystorageaccount
```

---

<a id="exceptions"></a>
<a id="trusted-microsoft-services"></a>

## <a name="grant-access-to-trusted-azure-services"></a>신뢰할 수 있는 Azure 서비스에 액세스 권한 부여

일부 Azure 서비스는 네트워크 규칙에 포함될 수 없는 네트워크에서 작동합니다. 다른 앱에 적용되는 네트워크 규칙을 그대로 유지하면서 이러한 신뢰할 수 있는 Azure 서비스의 하위 집합에 스토리지 계정의 액세스 권한을 부여할 수 있습니다. 해당 신뢰할 수 있는 서비스는 강력한 인증으로 스토리지 계정에 안전하게 연결합니다.

네트워크 규칙 예외를 만들어 신뢰할 수 있는 Azure 서비스에 액세스 권한을 부여할 수 있습니다. 단계별 지침은 이 문서의 [예외 관리](#manage-exceptions) 섹션을 참조하세요.

신뢰할 수 있는 Azure 서비스에 액세스 권한을 부여하는 경우 다음 유형의 액세스를 부여합니다.

- 구독에 등록된 리소스에 진행할 작업 선택에 대한 신뢰할 수 있는 액세스.
- 시스템 할당 관리 ID 기반 리소스에 대한 신뢰할 수 있는 액세스.

<a id="trusted-access-resources-in-subscription"></a>

### <a name="trusted-access-for-resources-registered-in-your-subscription"></a>구독에 등록된 리소스에 대한 신뢰할 수 있는 액세스

**구독에 등록된 경우** 일부 서비스의 리소스는 로그 또는 백업과 같은 선택 작업에 대해 **동일한 구독** 에 있는 스토리지 계정에 액세스할 수 있습니다.  다음 표에서는 각 서비스와 허용되는 작업에 대해 설명합니다.

| 서비스                  | 리소스 공급자 이름     | 허용되는 연산                 |
|:------------------------ |:-------------------------- |:---------------------------------- |
| Azure Backup             | Microsoft.RecoveryServices | IAAS 가상 머신에서 관리되지 않는 디스크의 백업 및 복원을 실행합니다. (관리되는 디스크에 필요 없음). [자세히 알아보기](../../backup/backup-overview.md). |
| Azure Data Box           | Microsoft.DataBox          | Data Box를 사용하여 Azure로 데이터를 가져올 수 있습니다. [자세히 알아보기](../../databox/data-box-overview.md). |
| Azure DevTest Labs       | Microsoft.DevTestLab       | 사용자 지정 이미지 만들기 및 아티팩트 설치. [자세히 알아보기](../../devtest-labs/devtest-lab-overview.md). |
| Azure Event Grid         | Microsoft.EventGrid        | Blob Storage 이벤트 게시를 사용하도록 설정하고 Event Grid가 스토리지 큐에 게시하도록 허용합니다. [Blob Storage 이벤트](../../event-grid/overview.md#event-sources)와 [큐에 게시](../../event-grid/event-handlers.md)에 대해 알아봅니다. |
| Azure Event Hubs         | Microsoft.EventHub         | Event Hubs 캡처로 데이터를 보관합니다. [자세한 정보](../../event-hubs/event-hubs-capture-overview.md). |
| Azure 파일 동기화          | Microsoft.StorageSync      | Azure 파일 공유를 위해 온-프레미스 파일 서버를 캐시로 변환할 수 있습니다. 다중 사이트 동기화, 빠른 재해 복구 및 클라우드 쪽 백업을 허용합니다. [자세히 알아보기](../file-sync/file-sync-planning.md) |
| Azure HDInsight          | Microsoft.HDInsight        | 새 HDInsight 클러스터에 대한 기본 파일 시스템의 초기 콘텐츠를 프로비저닝합니다. [자세히 알아보기](../../hdinsight/hdinsight-hadoop-use-blob-storage.md). |
| Azure Import/Export      | Microsoft.ImportExport     | Azure Storage 가져오기/내보내기 서비스를 사용하여 Azure로 데이터를 가져오고 Azure에서 데이터를 내보낼 수 있습니다. [자세히 알아보기](../../import-export/storage-import-export-service.md).  |
| Azure Monitor            | Microsoft.Insights         | 리소스 로그 Azure Active Directory 로그인 및 감사 로그, Microsoft Intune 로그를 포함하는 모니터링 데이터를 보안 스토리지 계정에 쓸 수 있습니다. [자세히 알아보기](../../azure-monitor/roles-permissions-security.md). |
| Azure 네트워킹         | Microsoft.Network          | Network Watcher와 트래픽 분석 서비스를 포함한 네트워크 트래픽 로그를 저장하고 분석합니다. [자세히 알아보기](../../network-watcher/network-watcher-nsg-flow-logging-overview.md). |
| Azure Site Recovery      | Microsoft.SiteRecovery     | 방화벽 지원 캐시, 원본 또는 대상 스토리지 계정을 사용하는 경우 Azure IaaS 가상 머신의 재해 복구를 위해 복제를 사용하도록 설정합니다.  [자세히 알아보기](../../site-recovery/azure-to-azure-tutorial-enable-replication.md). |

<a id="trusted-access-system-assigned-managed-identity"></a>

### <a name="trusted-access-based-on-system-assigned-managed-identity"></a>시스템이 할당한 관리 ID를 기반으로 하는 신뢰할 수 있는 액세스

다음 표에서는 해당 서비스의 리소스 인스턴스에 적절한 권한을 부여하는 경우 스토리지 계정 데이터에 액세스할 수 있는 서비스를 나열합니다.

계정에 계층 구조 네임스페이스 기능을 사용하도록 설정하지 않은 경우 각 리소스 인스턴스의 시스템 할당 관리 ID에 명시적으로 [Azure 역할을 할당](../../active-directory/managed-identities-azure-resources/overview.md)하여 권한을 부여할 수 있습니다. 이 경우 인스턴스에 대한 액세스 범위는 관리 ID에 할당된 Azure 역할에 해당합니다.

계층 구조 네임스페이스 기능이 사용하도록 설정된 계정에 대해 동일한 방법을 사용할 수 있습니다. 그러나 스토리지 계정에 포함된 디렉터리 또는 Blob의 ACL(액세스 제어 목록)에 시스템 할당 관리 ID를 추가하는 경우에는 Azure 역할을 할당할 필요가 없습니다. 이 경우 인스턴스의 액세스 범위는 시스템 할당 관리 ID에 액세스 권한이 부여된 디렉터리나 파일에 해당합니다. Azure 역할 및 ACL을 함께 결합할 수도 있습니다. 액세스 권한을 부여하기 위해 결합하는 방법에 대한 자세한 내용은 [Azure Data Lake Storage Gen2의 액세스 제어 모델](../blobs/data-lake-storage-access-control-model.md)을 참조하세요.

> [!TIP]
> 특정 리소스에 액세스 권한을 부여할 때는 방법은 리소스 인스턴스 규칙을 사용하기를 권합니다. 특정 리소스 인스턴스에 액세스 권한을 부여하려면 이 문서의 [Azure 리소스 인스턴스에 대한 액세스 권한 부여(미리 보기)](#grant-access-specific-instances) 섹션을 참조하세요.

| 서비스                        | 리소스 공급자 이름                 | 목적            |
| :----------------------------- | :------------------------------------- | :----------------- |
| Azure API Management           | Microsoft.ApiManagement/service        | 정책을 사용하여 방화벽 뒤에 있는 스토리지 계정에 대한 API Management 서비스 액세스를 사용하는 것으로 설정합니다. [자세히 알아보기](../../api-management/api-management-authentication-policies.md#use-managed-identity-in-send-request-policy). |
| Azure Cognitive Search         | Microsoft.Search/searchServices        | Cognitive Search 서비스를 사용하여 인덱싱, 처리 및 쿼리를 위해 스토리지 계정에 액세스할 수 있습니다. |
| Azure Cognitive Services       | Microsoft.CognitiveService/accounts    | Cognitive Services를 사용하는 것으로 설정하여 스토리지 계정에 액세스할 수 있습니다. [자세한 정보](../..//cognitive-services/cognitive-services-virtual-networks.md).|
| Azure Container Registry 작업 | Microsoft.ContainerRegistry/registries | ACR 태스크는 컨테이너 이미지를 빌드할 때 스토리지 계정에 액세스할 수 있습니다. |
| Azure 데이터 팩터리             | Microsoft.DataFactory/factories        | ADF 런타임을 통해 스토리지 계정에 액세스할 수 있도록 합니다. |
| Azure Data Share               | Microsoft.DataShare/accounts           | Data Share를 통해 스토리지 계정에 액세스할 수 있도록 합니다. |
| Azure DevTest Labs             | Microsoft.DevTestLab/labs              | DevTest Labs를 통해 스토리지 계정에 액세스를 허용합니다. |
| Azure IoT Hub                  | Microsoft.Devices/IotHubs              | IoT Hub의 데이터를 Blob 스토리지에 쓸 수 있습니다. [자세히 알아보기](../../iot-hub/virtual-network-support.md#egress-connectivity-from-iot-hub-to-other-azure-resources) |
| Azure Logic Apps               | Microsoft.Logic/workflows              | 논리 앱을 사용하여 스토리지 계정에 액세스할 수 있습니다. [자세히 알아보기](../../logic-apps/create-managed-service-identity.md#authenticate-access-with-managed-identity). |
| Azure Machine Learning 서비스 | Microsoft.MachineLearningServices      | 권한 있는 Azure Machine Learning 작업 영역은 실험 출력, 모델 및 로그를 Blob 스토리지에 쓰고 데이터를 읽습니다. [자세히 알아보기](../../machine-learning/how-to-network-security-overview.md#secure-the-workspace-and-associated-resources). |
| Azure Media Services           | Microsoft.Media/mediaservices          | Media Services를 통해 스토리지 계정에 액세스를 허용합니다. |
| Azure Migrate                  | Microsoft.Migrate/migrateprojects      | Azure Migrate를 통해 스토리지 계정에 액세스를 허용합니다. |
| Azure Purview                  | Microsoft.Purview/accounts             | Purview의 스토리지 계정 액세스를 허용합니다. |
| Azure Remote Rendering         | Microsoft.MixedReality/remoteRenderingAccounts | Remote Rendering을 통해 스토리지 계정에 액세스를 허용합니다. |
| Azure Site Recovery            | Microsoft.RecoveryServices/vaults      | Site Recovery를 통해 스토리지 계정에 액세스를 허용합니다. |
| Azure SQL Database             | Microsoft.Sql                          | 방화벽 뒤에 있는 스토리지 계정에 감사 데이터를 [쓸](../../azure-sql/database/audit-write-storage-account-behind-vnet-firewall.md) 수 있습니다. |
| Azure Synapse Analytics        | Microsoft.Sql                          | 전용 풀의 COPY 문이나 PolyBase 또는 서버리스 풀의 `openrowset` 함수와 외부 테이블을 사용해 특정 SQL 데이터베이스에서 데이터를 가져오거나 내보낼 수 있습니다. [자세히 알아보기](../../azure-sql/database/vnet-service-endpoint-rule-overview.md). |
| Azure Stream Analytics         | Microsoft.StreamAnalytics              | 스트리밍 작업의 데이터를 Blob 스토리지에 쓸 수 있습니다. [자세히 알아보기](../../stream-analytics/blob-output-managed-identity.md). |
| Azure Synapse Analytics        | Microsoft.Synapse/workspaces           | Azure Synapse Analytics에서 Azure Storage의 데이터에 대한 액세스를 사용하는 것으로 설정합니다. |

## <a name="grant-access-to-storage-analytics"></a>스토리지 분석에 대한 액세스 권한 부여

경우에 따라 네트워크 경계 밖에서 리소스 로그 및 메트릭을 읽을 수 있는 권한이 필요합니다. 스토리지 계정에 대한 신뢰할 수 있는 서비스 액세스를 구성하는 경우, 네트워크 규칙 예외를 만들어서 로그 파일, 메트릭 테이블 또는 양쪽 다에 대하여 읽기 액세스를 허용할 수 있습니다. 단계별 지침은 아래의 **예외 관리** 섹션을 참조하세요. 스토리지 분석 작업에 대한 자세한 내용은 [Azure Storage 분석을 사용하여 로그 및 메트릭 데이터 수집](./storage-analytics.md)을 참조하세요.

<a id="manage-exceptions"></a>

## <a name="manage-exceptions"></a>예외 관리

Azure Portal, PowerShell 또는 Azure CLI v2를 통해 네트워크 규칙 예외를 관리할 수 있습니다.

#### <a name="portal"></a>[포털](#tab/azure-portal)

1. 보호하려는 스토리지 계정으로 이동합니다.

2. **네트워킹** 이라는 설정 메뉴에서 선택합니다.

3. **선택한 네트워크** 에서 액세스를 허용하도록 선택했는지 확인합니다.

4. **예외** 아래에서 허용하려는 예외를 선택합니다.

5. **저장** 을 선택하여 변경 내용을 적용합니다.

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. [Azure PowerShell](/powershell/azure/install-Az-ps)을 설치하고 [로그인](/powershell/azure/authenticate-azureps)합니다.

2. 스토리지 계정 네트워크 규칙에 대한 예외를 표시합니다.

    ```powershell
    (Get-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount").Bypass
    ```

3. 스토리지 계정 네트워크 규칙에 대한 예외를 구성합니다.

    ```powershell
    Update-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -Bypass AzureServices,Metrics,Logging
    ```

4. 스토리지 계정 네트워크 규칙에 대한 예외를 제거합니다.

    ```powershell
    Update-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -Bypass None
    ```

> [!IMPORTANT]
> 반드시 [기본 규칙](#change-the-default-network-access-rule)을 **거부** 로 설정해야 합니다. 그렇지 않으면 예외를 제거해도 효과가 없습니다.

#### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. [Azure CLI](/cli/azure/install-azure-cli)를 설치하고 [로그인](/cli/azure/authenticate-azure-cli)합니다.

2. 스토리지 계정 네트워크 규칙에 대한 예외를 표시합니다.

    ```azurecli
    az storage account show --resource-group "myresourcegroup" --name "mystorageaccount" --query networkRuleSet.bypass
    ```

3. 스토리지 계정 네트워크 규칙에 대한 예외를 구성합니다.

    ```azurecli
    az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --bypass Logging Metrics AzureServices
    ```

4. 스토리지 계정 네트워크 규칙에 대한 예외를 제거합니다.

    ```azurecli
    az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --bypass None
    ```

> [!IMPORTANT]
> 반드시 [기본 규칙](#change-the-default-network-access-rule)을 **거부** 로 설정해야 합니다. 그렇지 않으면 예외를 제거해도 효과가 없습니다.

---

## <a name="next-steps"></a>다음 단계

[서비스 엔드포인트](../../virtual-network/virtual-network-service-endpoints-overview.md)에서 Azure 네트워크 서비스 엔드포인트에 대해 자세히 알아봅니다.

[Azure Storage 보안 가이드](../blobs/security-recommendations.md)에서 Azure Storage 보안에 대해 자세히 살펴봅니다.
