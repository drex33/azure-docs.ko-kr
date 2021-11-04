---
title: Azure CLI를 사용하여 Bastion 호스트 만들기 | Azure Bastion
description: Azure CLI를 사용하여 Bastion 호스트를 만들고 삭제하는 방법을 알아봅니다.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: how-to
ms.date: 09/22/2021
ms.author: cherylmc
ms.custom: ignite-fall-2021
ms.openlocfilehash: d855327b67d9918b62ab451259dd2c7025c885b9
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131087156"
---
# <a name="create-an-azure-bastion-host-using-azure-cli"></a>Azure CLI를 사용하여 Azure Bastion 호스트 만들기

이 문서에서는 Azure CLI를 사용하여 Azure Bastion 호스트를 만드는 방법을 보여 줍니다. 가상 네트워크에서 Azure Bastion 서비스를 프로비전한 후에는 같은 가상 네트워크의 모든 VM에서 원활한 RDP/SSH 환경을 사용할 수 있습니다. Azure Bastion 배포는 구독/계정 또는 가상 머신이 아닌 가상 네트워크별로 수행됩니다.

필요에 따라 다음 메서드를 사용하여 Azure Bastion 호스트를 만들 수 있습니다.
* [Azure Portal](./tutorial-create-host-portal.md)
* [Azure PowerShell](bastion-create-host-powershell.md)

[!INCLUDE [About skus](../../includes/bastion-sku-note.md)]

## <a name="prerequisites"></a>필수 구성 요소

Azure 구독이 있는지 확인합니다. Azure 구독이 아직 없는 경우 [MSDN 구독자 혜택](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details)을 활성화하거나 [무료 계정](https://azure.microsoft.com/pricing/free-trial)에 등록할 수 있습니다.

[!INCLUDE [Cloud Shell CLI](../../includes/vpn-gateway-cloud-shell-cli.md)]

 > [!NOTE]
 > Azure 프라이빗 DNS 영역에서 Azure Bastion을 사용하는 것은 현재 지원되지 않습니다. 시작하기 전에 Bastion 리소스를 배포하려는 가상 네트워크가 프라이빗 DNS 영역에 연결되어 있지 않은지 확인하세요.
 >

## <a name="create-a-bastion-host"></a><a name="createhost"></a>Bastion 호스트 만들기

이 섹션은 Azure CLI를 사용하여 새 Azure Bastion 리소스를 만드는 데 도움이 됩니다.

> [!NOTE]
> 예제에서 볼 수 있듯이, 모든 명령에서 `--resource-group`과 함께 `--location` 매개 변수를 사용하여 리소스가 함께 배포되도록 합니다.

1. 가상 네트워크 및 Azure Bastion 서브넷을 만듭니다. 이름 값으로 **AzureBastionSubnet** 을 사용하여 Azure Bastion 서브넷을 만들어야 합니다. 이 값을 통해 Azure에서 Bastion 리소스를 배포할 서브넷을 인식할 수 있습니다. 이는 VPN 게이트웨이 서브넷과 다릅니다.

   [!INCLUDE [Note about BastionSubnet size.](../../includes/bastion-subnet-size.md)]

   ```azurecli-interactive
   az network vnet create --resource-group MyResourceGroup --name MyVnet --address-prefix 10.0.0.0/16 --subnet-name AzureBastionSubnet --subnet-prefix 10.0.0.0/24 --location northeurope
   ```

2. Azure Bastion의 공용 IP 주소를 만듭니다. 공용 IP는 포트 443을 통해 RDP/SSH에 액세스할 Bastion 리소스의 공용 IP 주소입니다. 공용 IP 주소는 만들려는 Bastion 리소스와 동일한 지역에 있어야 합니다.

   ```azurecli-interactive
   az network public-ip create --resource-group MyResourceGroup --name MyIp --sku Standard --location northeurope
   ```

3. 가상 네트워크의 AzureBastionSubnet에서 새 Azure Bastion 리소스를 만듭니다. Bastion 리소스가 생성되고 배포될 때까지 약 5분 정도 걸립니다.

   ```azurecli-interactive
   az network bastion create --name MyBastion --public-ip-address MyIp --resource-group MyResourceGroup --vnet-name MyVnet --location northeurope
   ```
## <a name="disassociate-the-vm-public-ip-address"></a>VM 공용 IP 주소를 분리 합니다.

Azure 방호는 클라이언트 VM에 연결 하기 위해 공용 IP 주소를 사용 하지 않습니다. VM에 대 한 공용 IP 주소가 필요 하지 않은 경우 [AZURE vm에서 공용 ip 주소 분리](../virtual-network/ip-services/remove-public-ip-address-vm.md)문서의 단계를 사용 하 여 공용 ip 주소를 분리할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* 추가 정보는 [Bastion FAQ](bastion-faq.md)를 참조하세요.
* Azure Bastion 서브넷에서 네트워크 보안 그룹을 사용하려면 [NSG 사용](bastion-nsg.md)을 참조하세요.
