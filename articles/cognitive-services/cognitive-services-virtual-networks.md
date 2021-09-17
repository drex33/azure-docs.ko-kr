---
title: 가상 네트워크
titleSuffix: Azure Cognitive Services
description: Cognitive Services 리소스에 대한 계층화된 네트워크 보안을 구성합니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 02/09/2021
ms.author: aahi
ms.openlocfilehash: f8c20e9da69c9b4a6f1a301e11d73e39853662e1
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/04/2021
ms.locfileid: "123467811"
---
# <a name="configure-azure-cognitive-services-virtual-networks"></a>Azure Cognitive Services 가상 네트워크 구성

Azure Cognitive Services는 계층화된 보안 모델을 제공합니다. 이 모델을 사용하여 Cognitive Services 계정을 특정 네트워크 하위 집합으로 보호할 수 있습니다. 네트워크 규칙이 구성되면 지정된 네트워크 세트를 통해 데이터를 요청하는 애플리케이션만 계정에 액세스할 수 있습니다. 요청 필터링을 사용하여 리소스에 대한 액세스를 제한할 수 있습니다. 지정된 IP 주소, IP 범위 또는 [Azure Virtual Networks](../virtual-network/virtual-networks-overview.md)의 서브넷 목록에서 시작되는 요청만 허용됩니다.

네트워크 규칙이 적용될 때 Cognitive Services에 액세스하는 애플리케이션에는 권한 부여가 필요합니다. 권한 부여는 Azure AD([Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md)) 자격 증명 또는 유효한 API 키를 사용하여 지원됩니다.

> [!IMPORTANT]
> Cognitive Services 계정에 대한 방화벽 규칙을 설정하면 기본적으로 데이터에 대해 들어오는 요청이 차단됩니다. 요청을 허용하려면 다음 조건 중 하나를 충족해야 합니다.

> * 요청은 대상 Cognitive Services 계정의 허용된 서브넷 목록에 있는 Azure VNet(Virtual Network) 내에서 작동하는 서비스로부터 시작되어야 합니다. VNet에서 시작된 요청의 엔드포인트는 Cognitive Services 계정의 [사용자 지정 하위 도메인](cognitive-services-custom-subdomains.md)으로 설정되어야 합니다.
> * 또는 요청이 허용되는 IP 주소 목록에서 시작되어야 합니다.
>
> 차단되는 요청에는 다른 Azure 서비스, Azure Portal, 로깅 및 메트릭 서비스 등이 포함됩니다.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="scenarios"></a>시나리오

Cognitive Services 리소스를 보호하려면 먼저 모든 네트워크(인터넷 트래픽 포함)의 트래픽에 대한 액세스를 거부하도록 규칙을 구성해야 합니다. 그런 다음, 특정 VNet 트래픽에 대한 액세스를 허가하는 규칙을 구성해야 합니다. 이 구성을 사용하면 애플리케이션에 대한 보안 네트워크 경계를 구축할 수 있습니다. 또한 퍼블릭 인터넷 IP 주소 범위의 트래픽에 대한 액세스를 허가하도록 규칙을 구성하여 특정 인터넷 또는 온-프레미스 클라이언트의 연결을 사용하도록 설정할 수도 있습니다.

네트워크 규칙은 REST 및 WebSocket을 포함하여 Azure Cognitive Services에 대한 모든 네트워크 프로토콜에 적용됩니다. Azure 테스트 콘솔 등의 도구를 사용하여 데이터에 액세스하려면 명시적 네트워크 규칙을 구성해야 합니다. 네트워크 규칙을 기존 Cognitive Services 리소스에 적용하거나 새 Cognitive Services 리소스를 만들 때 적용할 수 있습니다. 네트워크 규칙이 적용되면 모든 요청에 적용됩니다.

## <a name="supported-regions-and-service-offerings"></a>지원되는 지역 및 서비스 제공 사항

VNET(가상 네트워크)는 [Cognitive Services를 사용할 수 있는 지역](https://azure.microsoft.com/global-infrastructure/services/)에서 지원됩니다. 현재 다중 서비스 리소스는 VNET을 지원하지 않습니다. Cognitive Services는 네트워크 규칙 구성에 대한 서비스 태그를 지원합니다. 아래 나열된 서비스는 **CognitiveServicesManagement** 서비스 태그에 포함되어 있습니다.

> [!div class="checklist"]
> * Anomaly Detector
> * Computer Vision
> * Content Moderator
> * Custom Vision
> * Face
> * 언어 이해(LUIS)
> * Personalizer
> * Speech Services
> * 텍스트 분석
> * QnA Maker
> * Translator Text


> [!NOTE]
> LUIS 또는 Speech Services를 사용하는 경우 **CognitiveServicesManagement** 태그를 통해서만 SDK 또는 REST API를 사용하는 서비스를 사용할 수 있습니다. 가상 네트워크에서 LUIS 포털 및/또는 Speech Studio에 액세스하고 이를 사용하려면 다음 태그를 사용해야 합니다.  
> * **AzureActiveDirectory**
> * **AzureFrontDoor.Frontend**
> * **AzureResourceManager** 
> * **CognitiveServicesManagement**



## <a name="change-the-default-network-access-rule"></a>기본 네트워크 액세스 규칙 변경

기본적으로 Cognitive Services 리소스는 네트워크에 있는 클라이언트로부터의 연결을 허용합니다. 선택한 네트워크에 대한 액세스를 제한하려면 먼저 기본 동작을 변경해야 합니다.

> [!WARNING]
> 네트워크 규칙을 변경하면 Azure Cognitive Services에 연결하는 애플리케이션의 기능에 영향을 미칠 수 있습니다. 기본 네트워크 규칙을 **거부** 로 설정하면 액세스를 **허용** 하는 특정 네트워크 규칙이 적용되지 않는 한 데이터에 대한 모든 액세스가 차단됩니다. 액세스를 거부하도록 기본 규칙을 변경하기 전에 네트워크 규칙을 사용하여 허용된 모든 네트워크에 대한 액세스를 허가해야 합니다. 온-프레미스 네트워크에 대한 IP 주소를 나열하도록 허용하는 경우 온-프레미스 네트워크에서 사용 가능한 모든 나가는 공용 IP 주소를 추가해야 합니다.

### <a name="managing-default-network-access-rules"></a>기본 네트워크 액세스 규칙 관리

Azure Portal, PowerShell 또는 Azure CLI를 통해 Cognitive Services 리소스에 대한 기본 네트워크 액세스 규칙을 관리할 수 있습니다.

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

1. 보안을 유지하려는 Cognitive Services 리소스로 이동합니다.

1. **가상 네트워크** 라는 **리소스 관리** 메뉴를 선택합니다.

   ![가상 네트워크 옵션](media/vnet/virtual-network-blade.png)

1. 기본적으로 액세스를 거부하려면 **선택한 네트워크** 에서 액세스를 허용하도록 선택합니다. 구성된 **가상 네트워크** 또는 **주소 범위** 를 함께 사용하지 않고 **선택한 네트워크** 설정만 단독으로 사용하면 모든 액세스가 사실상 거부됩니다. 모든 액세스가 거부되면 Cognitive Services 리소스를 사용하려는 요청이 허용되지 않습니다. 계속 Azure Portal, Azure PowerShell 또는 Azure CLI를 사용하여 Cognitive Services 리소스를 구성할 수 있습니다.
1. 모든 네트워크의 트래픽을 허용하려면 **모든 네트워크** 에서 액세스를 허용하도록 선택합니다.

   ![가상 네트워크 거부](media/vnet/virtual-network-deny.png)

1. **저장** 을 선택하여 변경 내용을 적용합니다.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

1. [Azure PowerShell](/powershell/azure/install-az-ps)을 설치하고 [로그인](/powershell/azure/authenticate-azureps)하거나 **사용해 보세요** 를 선택합니다.

1. Cognitive Services 리소스에 대한 기본 규칙의 상태를 표시합니다.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
    }
    (Get-AzCognitiveServicesAccountNetworkRuleSet @parameters).DefaultAction
    ```

1. 기본적으로 네트워크 액세스를 거부하도록 기본 규칙을 설정합니다.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -DefaultAction Deny
    }
    Update-AzCognitiveServicesAccountNetworkRuleSet @parameters
    ```

1. 기본적으로 네트워크 액세스를 허용하도록 기본 규칙을 설정합니다.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -DefaultAction Allow
    }
    Update-AzCognitiveServicesAccountNetworkRuleSet @parameters
    ```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. [Azure CLI](/cli/azure/install-azure-cli)를 설치하고 [로그인](/cli/azure/authenticate-azure-cli)하거나 **사용해 보세요** 를 선택합니다.

1. Cognitive Services 리소스에 대한 기본 규칙의 상태를 표시합니다.

    ```azurecli-interactive
    az cognitiveservices account show \
        -g "myresourcegroup" -n "myaccount" \
        --query networkRuleSet.defaultAction
    ```

1. 기본적으로 네트워크 액세스를 거부하도록 기본 규칙을 설정합니다.

    ```azurecli-interactive
    az cognitiveservices account update \
        -g "myresourcegroup" -n "myaccount" \
        --default-action Deny
    ```

1. 기본적으로 네트워크 액세스를 허용하도록 기본 규칙을 설정합니다.

    ```azurecli-interactive
    az cognitiveservices account update \
        -g "myresourcegroup" -n "myaccount" \
        --default-action Allow
    ```

***

## <a name="grant-access-from-a-virtual-network"></a>가상 네트워크의 액세스 허가

특정 서브넷에서만 액세스를 허용하도록 Cognitive Services 리소스를 구성할 수 있습니다. 허용되는 서브넷은 동일한 구독의 VNet 또는 다른 Azure Active Directory 테넌트에 속한 구독을 포함하여 다른 구독의 VNet에 속할 수 있습니다.

VNet 내의 Azure Cognitive Services에 대해 [서비스 엔드포인트](../virtual-network/virtual-network-service-endpoints-overview.md)를 사용하도록 설정합니다. 서비스 엔드포인트는 VNet의 트래픽을 Azure Cognitive Services 서비스에 대한 최적의 경로를 통해 라우팅합니다. 서브넷 및 가상 네트워크의 ID 또한 각 요청과 함께 전송됩니다. 그러면 관리자가 VNet의 특정 서브넷에서 요청을 받을 수 있도록 Cognitive Services 리소스에 대한 네트워크 규칙을 구성할 수 있습니다. 이러한 네트워크 규칙을 통해 액세스가 허가된 클라이언트는 데이터에 액세스하기 위해 Cognitive Services 리소스의 인증 요구 사항을 계속 충족해야 합니다.

각 Cognitive Services 리소스는 [IP 네트워크 규칙](#grant-access-from-an-internet-ip-range)과 결합될 수 있는 최대 100개의 가상 네트워크 규칙을 지원합니다.

### <a name="required-permissions"></a>필요한 사용 권한

가상 네트워크 규칙을 Cognitive Services 리소스에 적용하려면 추가되는 서브넷에 대한 적절한 권한이 사용자에게 있어야 합니다. 필요한 권한은 기본 *기여자* 역할 또는 *Cognitive Services 기여자* 역할입니다. 필요한 사용 권한을 사용자 지정 역할 정의에 추가할 수도 있습니다.

Cognitive Services 리소스 및 가상 네트워크에 허용된 액세스 권한은 다른 Azure AD 테넌트에 속하는 구독을 포함한 다른 구독에 있을 수 있습니다.

> [!NOTE]
> 다른 Azure Active Directory 테넌트에 속한 가상 네트워크의 서브넷에 대해 액세스 권한을 부여하는 규칙의 구성은 현재 Powershell, CLI 및 REST API를 통해서만 지원됩니다. 이러한 규칙은 Azure Portal에서 볼 수 있지만 구성할 수는 없습니다.

### <a name="managing-virtual-network-rules"></a>가상 네트워크 규칙 관리

Azure Portal, PowerShell 또는 Azure CLI를 통해 Cognitive Services 리소스에 대한 가상 네트워크 규칙을 관리할 수 있습니다.

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

1. 보안을 유지하려는 Cognitive Services 리소스로 이동합니다.

1. **가상 네트워크** 라는 **리소스 관리** 메뉴를 선택합니다.

1. **선택한 네트워크** 에서 액세스를 허용하도록 선택했는지 확인합니다.

1. 기존 네트워크 규칙을 사용하여 가상 네트워크에 대한 액세스를 허용하려면 **가상 네트워크** 에서 **기존 가상 네트워크 추가** 를 선택합니다.

   ![기존 vNet 추가](media/vnet/virtual-network-add-existing.png)

1. **가상 네트워크** 및 **서브넷** 옵션을 선택한 다음, **사용** 을 선택합니다.

   ![기존 vNet 세부 정보 추가](media/vnet/virtual-network-add-existing-details.png)

1. 새 가상 네트워크를 만들고 거기에 액세스 권한을 부여하려면 **새 가상 네트워크 추가** 를 선택합니다.

   ![새 vNet 추가](media/vnet/virtual-network-add-new.png)

1. 새 가상 네트워크를 만드는 데 필요한 정보를 입력하고 **만들기** 를 선택합니다.

   ![vNet 만들기](media/vnet/virtual-network-create.png)

    > [!NOTE]
    > 이전에 Azure Cognitive Services에 대한 서비스 엔드포인트가 선택한 가상 네트워크 및 서브넷에 대해 구성되지 않은 경우 이 작업의 일환으로 구성할 수 있습니다.
    >
    > 현재, 규칙을 만드는 동안 동일한 Azure Active Directory 테넌트에 속한 가상 네트워크만 선택할 수 있도록 표시됩니다. 다른 테넌트에 속한 가상 네트워크의 서브넷에 대한 액세스 권한을 부여하려면 Powershell, CLI 또는 REST API를 사용하세요.

1. 가상 네트워크 또는 서브넷 규칙을 제거하려면 **...** 을 선택해 가상 네트워크 또는 서브넷의 상황에 맞는 메뉴를 열고 **제거** 를 선택합니다.

   ![vNet 제거](media/vnet/virtual-network-remove.png)

1. **저장** 을 선택하여 변경 내용을 적용합니다.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

1. [Azure PowerShell](/powershell/azure/install-az-ps)을 설치하고 [로그인](/powershell/azure/authenticate-azureps)하거나 **사용해 보세요** 를 선택합니다.

1. 가상 네트워크 규칙을 나열합니다.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
    }
    (Get-AzCognitiveServicesAccountNetworkRuleSet @parameters).VirtualNetworkRules
    ```

1. 기존 가상 네트워크 및 서브넷에서 Azure Cognitive Services에 대한 서비스 엔드포인트를 사용하도록 설정합니다.

    ```azurepowershell-interactive
    Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" `
        -Name "myvnet" | Set-AzVirtualNetworkSubnetConfig -Name "mysubnet" `
        -AddressPrefix "10.0.0.0/24" `
        -ServiceEndpoint "Microsoft.CognitiveServices" | Set-AzVirtualNetwork
    ```

1. 가상 네트워크 및 서브넷에 대한 네트워크 규칙을 추가합니다.

    ```azurepowershell-interactive
    $subParameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myvnet"
    }
    $subnet = Get-AzVirtualNetwork @subParameters | Get-AzVirtualNetworkSubnetConfig -Name "mysubnet"

    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -VirtualNetworkResourceId $subnet.Id
    }
    Add-AzCognitiveServicesAccountNetworkRule @parameters
    ```

    > [!TIP]
    > 다른 Azure AD 테넌트에 속한 VNet의 서브넷에 대한 네트워크 규칙을 추가하려면 "/subscriptions/subscription-ID/resourceGroups/resourceGroup-Name/providers/Microsoft.Network/virtualNetworks/vNet-name/subnets/subnet-name" 형식의 정규화된 **VirtualNetworkResourceId** 매개 변수를 사용합니다.

1. 가상 네트워크 및 서브넷에 대한 네트워크 규칙을 제거합니다.

    ```azurepowershell-interactive
    $subParameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myvnet"
    }
    $subnet = Get-AzVirtualNetwork @subParameters | Get-AzVirtualNetworkSubnetConfig -Name "mysubnet"

    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -VirtualNetworkResourceId $subnet.Id
    }
    Remove-AzCognitiveServicesAccountNetworkRule @parameters
    ```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. [Azure CLI](/cli/azure/install-azure-cli)를 설치하고 [로그인](/cli/azure/authenticate-azure-cli)하거나 **사용해 보세요** 를 선택합니다.

1. 가상 네트워크 규칙을 나열합니다.

    ```azurecli-interactive
    az cognitiveservices account network-rule list \
        -g "myresourcegroup" -n "myaccount" \
        --query virtualNetworkRules
    ```

1. 기존 가상 네트워크 및 서브넷에서 Azure Cognitive Services에 대한 서비스 엔드포인트를 사용하도록 설정합니다.

    ```azurecli-interactive
    az network vnet subnet update -g "myresourcegroup" -n "mysubnet" \
    --vnet-name "myvnet" --service-endpoints "Microsoft.CognitiveServices"
    ```

1. 가상 네트워크 및 서브넷에 대한 네트워크 규칙을 추가합니다.

    ```azurecli-interactive
    $subnetid=(az network vnet subnet show \
        -g "myresourcegroup" -n "mysubnet" --vnet-name "myvnet" \
        --query id --output tsv)

    # Use the captured subnet identifier as an argument to the network rule addition
    az cognitiveservices account network-rule add \
        -g "myresourcegroup" -n "myaccount" \
        --subnet $subnetid
    ```

    > [!TIP]
    > 다른 Azure AD 테넌트에 속한 VNet의 서브넷에 대한 규칙을 추가하려면 "/subscriptions/subscription-ID/resourceGroups/resourceGroup-Name/providers/Microsoft.Network/virtualNetworks/vNet-name/subnets/subnet-name" 형식의 정규화된 서브넷 ID를 사용합니다.
    > 
    > **subscription** 매개 변수를 사용하여 다른 Azure AD 테넌트에 속한 VNet의 서브넷 ID를 검색할 수 있습니다.

1. 가상 네트워크 및 서브넷에 대한 네트워크 규칙을 제거합니다.

    ```azurecli-interactive
    $subnetid=(az network vnet subnet show \
        -g "myresourcegroup" -n "mysubnet" --vnet-name "myvnet" \
        --query id --output tsv)

    # Use the captured subnet identifier as an argument to the network rule removal
    az cognitiveservices account network-rule remove \
        -g "myresourcegroup" -n "myaccount" \
        --subnet $subnetid
    ```

***

> [!IMPORTANT]
> 반드시 [기본 규칙](#change-the-default-network-access-rule)을 **거부** 로 설정해야 합니다. 그렇지 않으면 네트워크 규칙이 적용되지 않습니다.

## <a name="grant-access-from-an-internet-ip-range"></a>인터넷 IP 범위의 액세스 허가

특정 공용 인터넷 IP 주소 범위에서 액세스할 수 있도록 Cognitive Services 리소스를 구성할 수 있습니다. 이 구성은 특정 서비스와 온-프레미스 네트워크에 대한 액세스 권한을 부여하고 일반 인터넷 트래픽을 효과적으로 차단합니다.

`16.17.18.0/24` 형식의 [CIDR 표기법](https://tools.ietf.org/html/rfc4632)을 사용하거나 개별 IP 주소(예: `16.17.18.19`)를 사용하여 허용된 인터넷 주소 범위를 제공합니다.

   > [!Tip]
   > "/31" 또는 "/32" 접두사 크기를 사용하는 작은 주소 범위는 지원되지 않습니다. 이러한 범위는 개별 IP 주소 규칙을 사용하여 구성해야 합니다.

IP 네트워크 규칙은 **공용 인터넷** IP 주소에 대해서만 허용됩니다. 사설망에 예약된 IP 주소 범위([RFC 1918](https://tools.ietf.org/html/rfc1918#section-3)에 정의된 대로)는 IP 규칙에서 허용되지 않습니다. 사설망에는 `10.*`, `172.16.*` - `172.31.*` 및 `192.168.*`로 시작하는 주소가 포함됩니다.

현재 IPv4 주소만 지원됩니다. 각 Cognitive Services 리소스는 [가상 네트워크 규칙](#grant-access-from-a-virtual-network)과 결합될 수 있는 최대 100개의 IP 네트워크 규칙을 지원합니다.

### <a name="configuring-access-from-on-premises-networks"></a>온-프레미스 네트워크에서의 액세스 구성

IP 네트워크 규칙을 사용하여 온-프레미스 네트워크에서 Cognitive Services 리소스로의 액세스를 허가하려면 네트워크에서 사용되는 인터넷 연결 IP 주소를 식별해야 합니다. 네트워크 관리자에게 도움을 요청합니다.

공용 피어링 또는 Microsoft 피어링을 위해 [ExpressRoute](../expressroute/expressroute-introduction.md) 온-프레미스를 사용하는 경우 NAT IP 주소를 식별해야 합니다. 공용 피어링의 경우 기본적으로 각 ExpressRoute 회로에서 두 개의 NAT IP 주소를 사용합니다. 각각은 트래픽이 Microsoft Azure 네트워크 백본으로 들어갈 때 Azure 서비스 트래픽에 적용됩니다. Microsoft 피어링의 경우 사용되는 NAT IP 주소는 고객이 제공하거나 서비스 공급자가 제공합니다. 서비스 리소스에 대한 액세스를 허용하려면 리소스 IP 방화벽 설정에서 이러한 공용 IP 주소를 허용해야 합니다. ExpressRoute 회로 IP 주소를 찾으려면 Azure Portal을 통해 [ExpressRoute에서 지원 티켓을 엽니다](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview). [ExpressRoute 공용 및 Microsoft 피어링을 위한 NAT](../expressroute/expressroute-nat.md#nat-requirements-for-azure-public-peering)에 대해 자세히 알아보세요.

### <a name="managing-ip-network-rules"></a>IP 네트워크 규칙 관리

Azure Portal, PowerShell 또는 Azure CLI를 통해 Cognitive Services 리소스에 대한 IP 네트워크 규칙을 관리할 수 있습니다.

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

1. 보안을 유지하려는 Cognitive Services 리소스로 이동합니다.

1. **가상 네트워크** 라는 **리소스 관리** 메뉴를 선택합니다.

1. **선택한 네트워크** 에서 액세스를 허용하도록 선택했는지 확인합니다.

1. 인터넷 IP 범위에 대한 액세스 권한을 부여하려면 **방화벽** > **주소 범위** 아래에서 IP 주소 또는 주소 범위([CIDR 형식](https://tools.ietf.org/html/rfc4632))를 입력합니다. 유효한 공용 IP(예약되지 않음) 주소만 허용됩니다.

   ![IP 범위 추가](media/vnet/virtual-network-add-ip-range.png)

1. IP 네트워크 규칙을 제거하려면 주소 범위 옆에 있는 휴지통 <span class="docon docon-delete x-hidden-focus"></span> 아이콘을 선택합니다.

   ![IP 범위 삭제](media/vnet/virtual-network-delete-ip-range.png)

1. **저장** 을 선택하여 변경 내용을 적용합니다.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

1. [Azure PowerShell](/powershell/azure/install-az-ps)을 설치하고 [로그인](/powershell/azure/authenticate-azureps)하거나 **사용해 보세요** 를 선택합니다.

1. IP 네트워크 규칙을 나열합니다.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
    }
    (Get-AzCognitiveServicesAccountNetworkRuleSet @parameters).IPRules
    ```

1. 개별 IP 주소에 대한 네트워크 규칙을 추가합니다.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -IPAddressOrRange "16.17.18.19"
    }
    Add-AzCognitiveServicesAccountNetworkRule @parameters
    ```

1. IP 주소 범위에 대한 네트워크 규칙을 추가합니다.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -IPAddressOrRange "16.17.18.0/24"
    }
    Add-AzCognitiveServicesAccountNetworkRule @parameters
    ```

1. 개별 IP 주소에 대한 네트워크 규칙을 제거합니다.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -IPAddressOrRange "16.17.18.19"
    }
    Remove-AzCognitiveServicesAccountNetworkRule @parameters
    ```

1. IP 주소 범위에 대한 네트워크 규칙을 제거합니다.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -IPAddressOrRange "16.17.18.0/24"
    }
    Remove-AzCognitiveServicesAccountNetworkRule @parameters
    ```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. [Azure CLI](/cli/azure/install-azure-cli)를 설치하고 [로그인](/cli/azure/authenticate-azure-cli)하거나 **사용해 보세요** 를 선택합니다.

1. IP 네트워크 규칙을 나열합니다.

    ```azurecli-interactive
    az cognitiveservices account network-rule list \
        -g "myresourcegroup" -n "myaccount" --query ipRules
    ```

1. 개별 IP 주소에 대한 네트워크 규칙을 추가합니다.

    ```azurecli-interactive
    az cognitiveservices account network-rule add \
        -g "myresourcegroup" -n "myaccount" \
        --ip-address "16.17.18.19"
    ```

1. IP 주소 범위에 대한 네트워크 규칙을 추가합니다.

    ```azurecli-interactive
    az cognitiveservices account network-rule add \
        -g "myresourcegroup" -n "myaccount" \
        --ip-address "16.17.18.0/24"
    ```

1. 개별 IP 주소에 대한 네트워크 규칙을 제거합니다.

    ```azurecli-interactive
    az cognitiveservices account network-rule remove \
        -g "myresourcegroup" -n "myaccount" \
        --ip-address "16.17.18.19"
    ```

1. IP 주소 범위에 대한 네트워크 규칙을 제거합니다.

    ```azurecli-interactive
    az cognitiveservices account network-rule remove \
        -g "myresourcegroup" -n "myaccount" \
        --ip-address "16.17.18.0/24"
    ```

***

> [!IMPORTANT]
> 반드시 [기본 규칙](#change-the-default-network-access-rule)을 **거부** 로 설정해야 합니다. 그렇지 않으면 네트워크 규칙이 적용되지 않습니다.

## <a name="use-private-endpoints"></a>프라이빗 엔드포인트 사용

Cognitive Services 리소스에서 [프라이빗 엔드포인트](../private-link/private-endpoint-overview.md)를 사용하면 VNet(가상 네트워크)의 클라이언트가 [Private Link](../private-link/private-link-overview.md)를 통해 안전하게 데이터에 액세스하도록 할 수 있습니다. 프라이빗 엔드포인트는 Cognitive Services 리소스용 VNet 주소 공간의 IP 주소를 사용합니다. VNet과 리소스에 있는 클라이언트 사이의 네트워크 트래픽은 VNet과 Microsoft 백본 네트워크의 프라이빗 링크를 가로지르며 공용 인터넷 노출을 방지합니다.

Cognitive Services 리소스의 프라이빗 엔드포인트를 사용하면 다음과 같은 작업을 할 수 있습니다.

* Cognitive Services 서비스에 대한 퍼블릭 엔드포인트의 모든 연결을 차단하도록 방화벽을 구성하여 Cognitive Services 리소스를 보호합니다.
* VNet에서의 데이터 반출을 차단하도록 하여 VNet 보안을 강화합니다.
* 프라이빗 피어링을 통해 [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) 또는 [ExpressRoutes](../expressroute/expressroute-locations.md)를 사용하여 VNet에 연결하는 온-프레미스 네트워크에서 Cognitive Services 리소스에 안전하게 연결합니다.

### <a name="conceptual-overview"></a>개념적 개요

프라이빗 엔드포인트는 [VNet](../virtual-network/virtual-networks-overview.md)의 Azure 리소스에 대한 특수한 네트워크 인터페이스입니다. Cognitive Services 리소스에 대한 프라이빗 엔드포인트를 만들면 VNet 및 리소스의 클라이언트 간에 보안 연결을 제공합니다. 프라이빗 엔드포인트에는 VNet의 IP 주소 범위에서 IP 주소가 할당됩니다. 프라이빗 엔드포인트와 Cognitive Services 서비스 간의 연결은 안전한 프라이빗 링크를 사용합니다.

VNet의 애플리케이션은 다른 방법으로 사용하는 것과 동일한 연결 문자열 및 권한 부여 메커니즘을 사용하여 프라이빗 엔드포인트를 통해 서비스에 원활하게 연결할 수 있습니다. 별도의 엔드포인트가 필요한 Speech Services는 예외입니다. [Speech Services를 사용하는 프라이빗 엔드포인트](#private-endpoints-with-the-speech-services)에 대한 섹션을 참조하세요. 프라이빗 엔드포인트는 REST를 포함하여 Cognitive Services 리소스에서 지원하는 모든 프로토콜과 함께 사용할 수 있습니다.

[서비스 엔드포인트](../virtual-network/virtual-network-service-endpoints-overview.md)를 사용하는 서브넷에서 프라이빗 엔드포인트를 만들 수 있습니다. 서브넷의 클라이언트는 서비스 엔드포인트를 사용해 다른 Cognitive Services 리소스에 액세스하는 동안 프라이빗 엔드포인트를 사용하여 하나의 Cognitive Services 리소스에 연결할 수 있습니다.

VNet에서 Cognitive Services 리소스에 대한 프라이빗 엔드포인트를 만드는 경우 Cognitive Services 리소스 소유자에게 승인을 위한 동의 요청이 전송됩니다. 프라이빗 엔드포인트 만들기를 요청한 사용자가 리소스의 소유자인 경우 이 동의 요청이 자동으로 승인됩니다.

Cognitive Services 리소스 소유자는 [Azure Portal](https://portal.azure.com)의 Cognitive Services 리소스에 대해 '*프라이빗 엔드포인트*' 탭을 통해 동의 요청 및 프라이빗 엔드포인트를 관리할 수 있습니다.

### <a name="private-endpoints"></a>프라이빗 엔드포인트

프라이빗 엔드포인트를 만들 때 연결할 Cognitive Services 리소스를 지정해야 합니다. 프라이빗 엔드포인트를 만드는 방법에 대한 자세한 내용은 다음을 참조하세요.

* [Azure Portal에서 Private Link 센터를 사용하여 프라이빗 엔드포인트 만들기](../private-link/create-private-endpoint-portal.md)
* [Azure CLI를 사용하여 Azure 프라이빗 엔드포인트 만들기](../private-link/create-private-endpoint-cli.md)
* [Azure PowerShell을 사용하여 프라이빗 엔드포인트 만들기](../private-link/create-private-endpoint-powershell.md)

### <a name="connecting-to-private-endpoints"></a>프라이빗 엔드포인트에 연결

프라이빗 엔드포인트를 사용하는 VNet의 클라이언트는 퍼블릭 엔드포인트에 연결하는 클라이언트와 동일한 Cognitive Services 리소스 연결 문자열을 사용해야 합니다. 별도의 엔드포인트가 필요한 Speech Services는 예외입니다. [Speech Services를 사용하는 프라이빗 엔드포인트](#private-endpoints-with-the-speech-services)에 대한 섹션을 참조하세요. DNS 확인을 사용하여 프라이빗 링크를 통해 VNet에서 Cognitive Services 리소스로 연결을 자동으로 라우팅합니다. 

기본적으로 프라이빗 엔드포인트에 대한 필수 업데이트를 사용하여 VNet에 연결된 [프라이빗 DNS 영역](../dns/private-dns-overview.md)을 만듭니다. 그러나 사용자가 자체 DNS 서버를 사용하는 경우 DNS 구성을 추가로 변경해야 할 수 있습니다. 아래 [DNS 변경 내용](#dns-changes-for-private-endpoints) 섹션에서는 프라이빗 엔드포인트에 필요한 업데이트에 대해 설명합니다.

### <a name="private-endpoints-with-the-speech-services"></a>Speech Services를 사용하는 프라이빗 엔드포인트

[Azure Private Link에서 제공하는 프라이빗 엔드포인트에서 Speech Services 사용](Speech-Service/speech-services-private-link.md)을 참조하세요.

### <a name="dns-changes-for-private-endpoints"></a>프라이빗 엔드포인트에 대한 DNS 변경 내용

프라이빗 엔드포인트를 만들 때 Cognitive Services 리소스에 대한 DNS CNAME 리소스 레코드는 '*privatelink*' 접두사가 있는 하위 도메인의 별칭으로 업데이트됩니다. 또한 기본적으로 프라이빗 엔드포인트에 대한 DNS A 리소스 레코드를 사용하여 '*privatelink*' 하위 도메인에 해당하는 [프라이빗 DNS 영역](../dns/private-dns-overview.md)을 만듭니다.

프라이빗 엔드포인트를 사용하여 VNet 외부에서 엔드포인트 URL을 확인하면 Cognitive Services 리소스의 퍼블릭 엔드포인트로 확인됩니다. 프라이빗 엔드포인트를 호스트하는 VNet에서 확인되면 엔드포인트 URL은 프라이빗 엔드포인트의 IP 주소로 확인됩니다.

이 접근 방식을 사용하면 프라이빗 엔드포인트를 호스트하는 VNet의 클라이언트와 VNet 외부의 클라이언트에 동일한 연결 문자열을 사용하여 Cognitive Services 리소스에 액세스할 수 있습니다.

네트워크에서 사용자 지정 DNS 서버를 사용하는 경우 클라이언트는 프라이빗 엔드포인트 IP 주소에 대한 Cognitive Services 리소스 엔드포인트의 FQDN(정규화된 도메인 이름)을 확인할 수 있어야 합니다. 프라이빗 링크 하위 도메인을 VNet의 프라이빗 DNS 영역에 위임하도록 DNS 서버를 구성합니다.

> [!TIP]
> 사용자 지정 또는 온-프레미스 DNS 서버를 사용하는 경우 'privatelink' 하위 도메인의 Cognitive Services 리소스 이름을 프라이빗 엔드포인트 IP 주소로 확인하도록 DNS 서버를 구성해야 합니다. 'privatelink' 하위 도메인을 VNet의 프라이빗 DNS 영역에 위임하거나 DNS 서버에서 DNS 영역을 구성하고 DNS A 레코드를 추가하면 그렇게 할 수 있습니다.

프라이빗 엔드포인트를 지원하기 위해 자체 DNS 서버를 구성하는 방법에 대한 자세한 내용은 다음 문서를 참조하세요.

* [Azure 가상 네트워크의 리소스 이름 확인](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)
* [프라이빗 엔드포인트에 대한 DNS 구성](../private-link/private-endpoint-overview.md#dns-configuration)

### <a name="pricing"></a>가격 책정

가격 책정에 대한 자세한 내용은 [Azure Private Link 가격 책정](https://azure.microsoft.com/pricing/details/private-link)을 참조하세요.

## <a name="next-steps"></a>다음 단계

* 다양한 [Azure Cognitive Services](./what-are-cognitive-services.md) 살펴보기
* [Azure Virtual Network 서비스 엔드포인트](../virtual-network/virtual-network-service-endpoints-overview.md)에 대해 자세히 알아보기
