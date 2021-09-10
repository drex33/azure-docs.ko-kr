---
title: PowerShell을 사용해 허브에 교차 테넌트 VNet 연결
titleSuffix: Azure Virtual WAN
description: 이 문서는 PowerShell을 사용하여 교차 테넌트 VNet을 가상 허브에 연결하는 데 유용합니다.
services: virtual-wan
author: wtnlee
ms.service: virtual-wan
ms.topic: how-to
ms.date: 09/28/2020
ms.author: wellee
ms.openlocfilehash: 8c9ab37e46f23d533550ffc535633575a85937a6
ms.sourcegitcommit: 28cd7097390c43a73b8e45a8b4f0f540f9123a6a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/24/2021
ms.locfileid: "122778902"
---
# <a name="connect-cross-tenant-vnets-to-a-virtual-wan-hub"></a>가상 Wan 허브에 교차 테넌트 VNet 연결

이 문서는 Virtual WAN을 사용하여 다른 테넌트의 가상 허브에 VNet을 연결하는 데 유용합니다. 이 아키텍처는 같은 네트워크에 연결되어야 하지만 다른 테넌트에 연결되어 있는 클라이언트 워크로드가 있는 경우에 유용합니다. 예를 들어 다음 다이어그램에 표시된 것처럼 비 Contoso VNet(원격 테넌트)을 Contoso 가상 허브(부모 테넌트)에 연결할 수 있습니다.

:::image type="content" source="./media/cross-tenant-vnet/connectivity.png" alt-text="라우팅 구성 설정" :::

이 문서에서는 다음 방법을 설명합니다.

* 다른 테넌트를 Azure 구독에 참가자로 추가합니다.
* 가상 허브에 교차 테넌트 VNet을 연결합니다.

이 구성의 단계는 Azure Portal과 PowerShell의 조합으로 수행합니다. 그러나 기능 자체는 PowerShell과 CLI에서만 사용할 수 있습니다.

## <a name="before-you-begin"></a>시작하기 전에

### <a name="prerequisites"></a>필수 구성 요소

이 문서의 단계를 수행하려면 환경에 다음 구성이 이미 갖춰져 있어야 합니다.

* 부모 구독의 가상 WAN 및 가상 허브.
* 다른 원격 테넌트의 구독에 구성된 가상 네트워크입니다.
* 원격 테넌트의 VNet 주소 공간이 이미 부모 가상 허브에 연결된 다른 VNet 내의 다른 주소 공간과 겹치지 않는지 확인합니다.

### <a name="working-with-azure-powershell"></a>Azure PowerShell 작업

[!INCLUDE [PowerShell](../../includes/vpn-gateway-cloud-shell-powershell.md)]

## <a name="assign-permissions"></a><a name="rights"></a>권한 할당

가상 허브를 사용하여 부모 구독을 관리하는 사용자가 원격 테넌트의 가상 네트워크를 수정하고 액세스할 수 있게 하려면, 원격 테넌트 구독에서 해당 사용자에 대한 **contributor** 권한을 할당해야 합니다. **contributor** 권한을 해당 사용자에 할당하는 작업은 원격 테넌트의 VNET 구독에서 이루어집니다.

1. 관리자(가상 WAN 허브를 관리)에 **contributor** 역할 할당을 추가합니다. PowerShell 또는 Azure Portal을 사용해 이 역할을 할당할 수 있습니다. 수행 단계는 다음 **역할 할당 추가 또는 제거** 문서를 참조하세요.

   * [PowerShell](../role-based-access-control/role-assignments-powershell.md)
   * [포털](../role-based-access-control/role-assignments-portal.md)

1. 그런 다음 PowerShell의 현재 세션에 원격 테넌트 구독과 부모 테넌트 구독을 추가합니다. 다음 명령을 실행합니다. 부모에 로그인한 경우에는 원격 테넌트에 대해서만 명령을 실행하면 됩니다.

   ```azurepowershell-interactive
   Connect-AzAccount -SubscriptionId "[subscription ID]" -TenantId "[tenant ID]"
   ```

1. 부모 자격 증명을 사용하여 Azure PowerShell에 로깅하고 다음 명령을 실행하여 역할 할당에 성공했는지 확인합니다.

   ```azurepowershell-interactive
   Get-AzSubscription
   ```

1. 권한이 부모에 성공적으로 전파되고 세션에 추가된 경우, 부모 **및** 원격 테넌트가 소유한 구독이 모두 명령 출력에 표시됩니다.

## <a name="connect-vnet-to-hub"></a><a name="connect"></a>허브에 VNet 연결

다음 단계에서는 가상 네트워크를 가상 허브에 연결할 때 두 구독의 컨텍스트를 전환합니다. 예시 값을 변경하여 고유한 환경을 바꿉니다.

1. 다음 명령을 실행하여 원격 계정의 컨텍스트에 사용자가 있는지 확인합니다.

   ```azurepowershell-interactive
   Select-AzSubscription -SubscriptionId "[remote ID]"
   ```

1. 허브에 연결하려는 가상 네트워크의 메타데이터를 저장하는 지역 변수를 만듭니다.

   ```azurepowershell-interactive
   $remote = Get-AzVirtualNetwork -Name "[vnet name]" -ResourceGroupName "[resource group name]"
   ```

1. 부모 계정으로 다시 전환합니다.

   ```azurepowershell-interactive
   Select-AzSubscription -SubscriptionId "[parent ID]"
   ```

1. 허브에 VNet을 연결합니다.

   ```azurepowershell-interactive
   New-AzVirtualHubVnetConnection -ResourceGroupName "[parent resource group name]" -VirtualHubName "[virtual hub name]" -Name "[name of connection]" -RemoteVirtualNetwork $remote
   ```

1. PowerShell 또는 Azure Portal에서 새 연결을 볼 수 있습니다.

   * **PowerShell:** 연결을 성공적으로 구성했으면 새로 만든 연결의 메타데이터가 PowerShell 콘솔에 표시됩니다.
   * **Azure Portal:** 가상 허브에서, **연결 -> Virtual Network 연결** 로 이동합니다. 연결에 대한 포인터를 볼 수 있습니다. 실제 리소스를 보려면 적절한 권한이 필요합니다.
   
## <a name="troubleshooting"></a><a name="troubleshoot"></a>문제 해결

* 앞 [섹션](#connect)의 $remote 메타데이터가 Azure Portal의 정보와 일치하는지 확인합니다.
* 원격 테넌트 리소스 그룹의 IAM 설정이나 Azure PowerShell 명령(Get-AzSubscription)을 사용해 권한을 확인할 수 있습니다.
* 리소스 그룹의 이름 또는 다른 환경 관련 변수의 이름을 따옴표로 묶어야 합니다(예: “VirtualHub1” 또는 “VirtualNetwork1”).

## <a name="next-steps"></a>다음 단계

Virtual WAN에 대한 자세한 내용은 다음을 참조하세요.

* Virtual WAN [FAQ](virtual-wan-faq.md)
