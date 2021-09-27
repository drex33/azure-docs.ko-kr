---
title: Azure PowerShell을 사용하여 베스천 호스트 만들기 | Microsoft Docs
description: PowerShell을 사용하여 Azure Bastion 호스트를 만드는 방법을 알아봅니다.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: how-to
ms.date: 09/22/2021
ms.author: cherylmc
ms.openlocfilehash: 5f58c9c5c58b2706d0861aeef4a2dac2af64274f
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128605167"
---
# <a name="create-an-azure-bastion-host-using-azure-powershell"></a>Azure PowerShell을 사용하여 Azure Bastion 호스트 만들기

이 문서에서는 PowerShell을 사용하여 Azure Bastion 호스트를 만드는 방법을 보여 줍니다. 가상 네트워크에서 Azure Bastion 서비스를 프로비전한 후에는 같은 가상 네트워크의 모든 VM에서 원활한 RDP/SSH 환경을 사용할 수 있습니다. Azure Bastion 배포는 구독/계정 또는 가상 머신이 아닌 가상 네트워크별로 수행됩니다.

필요에 따라 다음 메서드를 사용하여 Azure Bastion 호스트를 만들 수 있습니다.
* [Azure Portal](./tutorial-create-host-portal.md)
* [Azure CLI](create-host-cli.md)

[!INCLUDE [Note about SKU limitations for preview.](../../includes/bastion-preview-sku-note.md)]

## <a name="prerequisites"></a>필수 구성 요소

Azure 구독이 있는지 확인합니다. Azure 구독이 아직 없는 경우 [MSDN 구독자 혜택](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details)을 활성화하거나 [무료 계정](https://azure.microsoft.com/pricing/free-trial)에 등록할 수 있습니다.

[!INCLUDE [PowerShell](../../includes/vpn-gateway-cloud-shell-powershell-about.md)]

 > [!NOTE]
 > Azure 프라이빗 DNS 영역에서 Azure Bastion을 사용하는 것은 현재 지원되지 않습니다. 시작하기 전에 Bastion 리소스를 배포하려는 가상 네트워크가 프라이빗 DNS 영역에 연결되어 있지 않은지 확인하세요.
 >

## <a name="create-a-bastion-host"></a><a name="createhost"></a>Bastion 호스트 만들기

이 섹션은 Azure PowerShell을 사용하여 새 Azure Bastion 리소스를 만드는 데 도움이 됩니다.

1. 가상 네트워크 및 Azure Bastion 서브넷을 만듭니다. 이름 값으로 **AzureBastionSubnet** 을 사용하여 Azure Bastion 서브넷을 만들어야 합니다. 이 값을 통해 Azure에서 Bastion 리소스를 배포할 서브넷을 인식할 수 있습니다. 이는 VPN 게이트웨이 서브넷과 다릅니다.

   [!INCLUDE [Note about BastionSubnet size.](../../includes/bastion-subnet-size.md)]

   ```azurepowershell-interactive
   $subnetName = "AzureBastionSubnet"
   $subnet = New-AzVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24
   $vnet = New-AzVirtualNetwork -Name "myVnet" -ResourceGroupName "myBastionRG" -Location "westeurope" -AddressPrefix 10.0.0.0/16 -Subnet $subnet
   ```

2. Azure Bastion의 공용 IP 주소를 만듭니다. 공용 IP는 포트 443을 통해 RDP/SSH에 액세스할 Bastion 리소스의 공용 IP 주소입니다. 공용 IP 주소는 만들려는 Bastion 리소스와 동일한 지역에 있어야 합니다.

   ```azurepowershell-interactive
   $publicip = New-AzPublicIpAddress -ResourceGroupName "myBastionRG" -name "myPublicIP" -location "westeurope" -AllocationMethod Static -Sku Standard
   ```

3. 가상 네트워크의 AzureBastionSubnet에서 새 Azure Bastion 리소스를 만듭니다. Bastion 리소스가 생성되고 배포될 때까지 약 5분 정도 걸립니다.

   ```azurepowershell-interactive
   $bastion = New-AzBastion -ResourceGroupName "myBastionRG" -Name "myBastion" -PublicIpAddress $publicip -VirtualNetwork $vnet
   ```
## <a name="disassociate-the-vm-public-ip-address"></a>VM 공용 IP 주소 분리

Azure Bastion 공용 IP 주소를 사용하여 클라이언트 VM에 연결하지 않습니다. VM에 대한 공용 IP 주소가 필요하지 않은 경우 Azure VM에서 공용 IP 주소 분리 문서의 단계를 사용하여 [공용 IP 주소를](../virtual-network/remove-public-ip-address-vm.md)분리할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* 추가 정보는 [Bastion FAQ](bastion-faq.md)를 참조하세요.
* Azure Bastion 서브넷에서 네트워크 보안 그룹을 사용하려면 [NSG 사용](bastion-nsg.md)을 참조하세요.
