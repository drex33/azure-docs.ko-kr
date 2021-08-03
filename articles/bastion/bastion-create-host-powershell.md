---
title: Azure PowerShell을 사용하여 베스천 호스트 만들기 | Microsoft Docs
description: PowerShell을 사용하여 Azure Bastion 호스트를 만드는 방법을 알아봅니다.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: how-to
ms.date: 10/14/2020
ms.author: cherylmc
ms.openlocfilehash: f08ffdafdbca4e406e0bf7d6e23fa3218ccccfe4
ms.sourcegitcommit: 9ad20581c9fe2c35339acc34d74d0d9cb38eb9aa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2021
ms.locfileid: "110538033"
---
# <a name="create-an-azure-bastion-host-using-azure-powershell"></a>Azure PowerShell을 사용하여 Azure Bastion 호스트 만들기

이 문서에서는 PowerShell을 사용하여 Azure Bastion 호스트를 만드는 방법을 보여 줍니다. 가상 네트워크에서 Azure Bastion 서비스를 프로비전한 후에는 같은 가상 네트워크의 모든 VM에서 원활한 RDP/SSH 환경을 사용할 수 있습니다. Azure Bastion 배포는 구독/계정 또는 가상 머신이 아닌 가상 네트워크별로 수행됩니다.

필요에 따라 [Azure Portal](./tutorial-create-host-portal.md)을 사용하여 Azure Bastion 호스트를 만들 수 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

Azure 구독이 있는지 확인합니다. Azure 구독이 아직 없는 경우 [MSDN 구독자 혜택](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details)을 활성화하거나 [무료 계정](https://azure.microsoft.com/pricing/free-trial)에 등록할 수 있습니다.

[!INCLUDE [PowerShell](../../includes/vpn-gateway-cloud-shell-powershell-about.md)]

 >[!NOTE]
 >Azure 프라이빗 DNS 영역에서 Azure Bastion을 사용하는 것은 현재 지원되지 않습니다. 시작하기 전에 Bastion 리소스를 배포하려는 가상 네트워크가 프라이빗 DNS 영역에 연결되어 있지 않은지 확인하세요.
 >

## <a name="create-a-bastion-host"></a><a name="createhost"></a>Bastion 호스트 만들기

이 섹션은 Azure PowerShell을 사용하여 새 Azure Bastion 리소스를 만드는 데 도움이 됩니다.

1. 가상 네트워크 및 Azure Bastion 서브넷을 만듭니다. 이름 값으로 **AzureBastionSubnet** 을 사용하여 Azure Bastion 서브넷을 만들어야 합니다. 이 값을 통해 Azure에서 Bastion 리소스를 배포할 서브넷을 인식할 수 있습니다. 이는 게이트웨이 서브넷과는 다릅니다. 최소 /27 이상(/27, /26 등)의 서브넷을 사용해야 합니다. 경로 테이블 또는 위임 없이 **AzureBastionSubnet** 을 만듭니다. **AzureBastionSubnet** 의 네트워크 보안 그룹을 사용하는 경우 [NSG 사용](bastion-nsg.md) 문서를 참조하세요.

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

## <a name="next-steps"></a>다음 단계

* 추가 정보는 [Bastion FAQ](bastion-faq.md)를 참조하세요.
* Azure Bastion 서브넷에서 네트워크 보안 그룹을 사용하려면 [NSG 사용](bastion-nsg.md)을 참조하세요.
