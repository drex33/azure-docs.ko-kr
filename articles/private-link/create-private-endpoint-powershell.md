---
title: 빠른 시작 - Azure PowerShell을 사용하여 Azure 프라이빗 엔드포인트 만들기
description: 이 빠른 시작을 통해 Azure PowerShell을 사용하여 프라이빗 엔드포인트를 만드는 방법을 알아봅니다.
services: private-link
author: asudbring
ms.service: private-link
ms.topic: quickstart
ms.date: 11/02/2020
ms.author: allensu
ms.custom: devx-track-azurepowershell, mode-other
ms.openlocfilehash: 1fb9b9449c956e567ed238a76f3be22b32891799
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133055640"
---
# <a name="quickstart-create-an-azure-private-endpoint-using-azure-powershell"></a>빠른 시작: Azure PowerShell을 사용하여 Azure Private Endpoint 만들기

Private Endpoint를 통해 Azure 웹앱에 안전하게 연결하여 Azure Private Link를 시작합니다.

이 빠른 시작에서는 Azure 웹앱에 대한 프라이빗 엔드포인트를 만들고, 가상 머신을 배포하여 프라이빗 연결을 테스트합니다.  

Azure SQL 및 Azure Storage와 같은 다양한 종류의 Azure 서비스에 대한 프라이빗 엔드포인트를 만들 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

* 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* **PremiumV2 계층** 이상의 App Service 요금제가 Azure 구독에 배포된 Azure 웹앱  
    * 자세한 내용 및 예제는 [빠른 시작: Azure에서 ASP.NET Core 웹앱 만들기](../app-service/quickstart-dotnetcore.md)를 참조하세요. 
    * 웹앱 및 엔드포인트를 만드는 방법에 대한 자세한 자습서는 [자습서: Azure Private Endpoint를 사용하여 웹앱에 연결](tutorial-private-endpoint-webapp-portal.md)을 참조하세요.

PowerShell을 로컬로 설치하고 사용하도록 선택하는 경우 이 문서에는 Azure PowerShell 모듈 버전 5.4.1 이상이 필요합니다. 설치되어 있는 버전을 확인하려면 `Get-Module -ListAvailable Az`을 실행합니다. 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-Az-ps)를 참조하세요. 또한 PowerShell을 로컬로 실행하는 경우 `Connect-AzAccount`를 실행하여 Azure와 연결해야 합니다.

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

Azure 리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다.

[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)을 사용하여 다음 리소스 그룹을 만듭니다.

```azurepowershell-interactive
New-AzResourceGroup -Name 'CreatePrivateEndpointQS-rg' -Location 'eastus'
```

## <a name="create-a-virtual-network-and-bastion-host"></a>가상 네트워크 및 베스천 호스트 만들기

이 섹션에서는 가상 네트워크, 서브넷 및 베스천 호스트를 만듭니다. 

베스천 호스트는 가상 머신에 안전하게 연결하여 프라이빗 엔드포인트를 테스트하는 데 사용됩니다.

가상 네트워크 및 베스천 호스트 만들기

* [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork)
* [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress)
* [New-AzBastion](/powershell/module/az.network/new-azbastion)

```azurepowershell-interactive
## Create backend subnet config. ##
$subnetConfig = New-AzVirtualNetworkSubnetConfig -Name myBackendSubnet -AddressPrefix 10.0.0.0/24

## Create Azure Bastion subnet. ##
$bastsubnetConfig = New-AzVirtualNetworkSubnetConfig -Name AzureBastionSubnet -AddressPrefix 10.0.1.0/24

## Create the virtual network. ##
$parameters1 = @{
    Name = 'MyVNet'
    ResourceGroupName = 'CreatePrivateEndpointQS-rg'
    Location = 'eastus'
    AddressPrefix = '10.0.0.0/16'
    Subnet = $subnetConfig, $bastsubnetConfig
}
$vnet = New-AzVirtualNetwork @parameters1

## Create public IP address for bastion host. ##
$parameters2 = @{
    Name = 'myBastionIP'
    ResourceGroupName = 'CreatePrivateEndpointQS-rg'
    Location = 'eastus'
    Sku = 'Standard'
    AllocationMethod = 'Static'
}
$publicip = New-AzPublicIpAddress @parameters2

## Create bastion host ##
$parameters3 = @{
    ResourceGroupName = 'CreatePrivateEndpointQS-rg'
    Name = 'myBastion'
    PublicIpAddress = $publicip
    VirtualNetwork = $vnet
}
New-AzBastion @parameters3
```

Azure Bastion 호스트를 배포하는 데 몇 분 정도 걸릴 수 있습니다.

## <a name="create-test-virtual-machine"></a>테스트 가상 머신 만들기

이 섹션에서는 프라이빗 엔드포인트를 테스트하는 데 사용할 가상 머신을 만듭니다.

다음을 사용하여 가상 머신을 만듭니다.

  * [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential)
  * [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) 
  * [New-AzVM](/powershell/module/az.compute/new-azvm)
  * [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig)
  * [Set-AzVMOperatingSystem](/powershell/module/az.compute/set-azvmoperatingsystem)
  * [Set-AzVMSourceImage](/powershell/module/az.compute/set-azvmsourceimage)
  * [Add-AzVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface)


```azurepowershell-interactive
## Set credentials for server admin and password. ##
$cred = Get-Credential

## Command to get virtual network configuration. ##
$vnet = Get-AzVirtualNetwork -Name myVNet -ResourceGroupName CreatePrivateEndpointQS-rg

## Command to create network interface for VM ##
$parameters1 = @{
    Name = 'myNicVM'
    ResourceGroupName = 'CreatePrivateEndpointQS-rg'
    Location = 'eastus'
    Subnet = $vnet.Subnets[0]
}
$nicVM = New-AzNetworkInterface @parameters1

## Create a virtual machine configuration.##
$parameters2 = @{
    VMName = 'myVM'
    VMSize = 'Standard_DS1_v2'
}
$parameters3 = @{
    ComputerName = 'myVM'
    Credential = $cred
}
$parameters4 = @{
    PublisherName = 'MicrosoftWindowsServer'
    Offer = 'WindowsServer'
    Skus = '2019-Datacenter'
    Version = 'latest'
}
$vmConfig = 
New-AzVMConfig @parameters2 | Set-AzVMOperatingSystem -Windows @parameters3 | Set-AzVMSourceImage @parameters4 | Add-AzVMNetworkInterface -Id $nicVM.Id

## Create the virtual machine ##
New-AzVM -ResourceGroupName 'CreatePrivateEndpointQS-rg' -Location 'eastus' -VM $vmConfig
```

[!INCLUDE [ephemeral-ip-note.md](../../includes/ephemeral-ip-note.md)]

## <a name="create-private-endpoint"></a>프라이빗 엔드포인트 만들기

이 섹션에서는 다음을 사용하여 프라이빗 엔드포인트 및 연결을 만듭니다.

* [New-AzPrivateLinkServiceConnection](/powershell/module/az.network/New-AzPrivateLinkServiceConnection)
* [New-AzPrivateEndpoint](/powershell/module/az.network/new-azprivateendpoint)

```azurepowershell-interactive
## Place web app into variable. Replace <webapp-resource-group-name> with the resource group of your webapp. ##
## Replace <your-webapp-name> with your webapp name ##
$webapp = Get-AzWebApp -ResourceGroupName <webapp-resource-group-name> -Name <your-webapp-name>

## Create private endpoint connection. ##
$parameters1 = @{
    Name = 'myConnection'
    PrivateLinkServiceId = $webapp.ID
    GroupID = 'sites'
}
$privateEndpointConnection = New-AzPrivateLinkServiceConnection @parameters1

## Place virtual network into variable. ##
$vnet = Get-AzVirtualNetwork -ResourceGroupName 'CreatePrivateEndpointQS-rg' -Name 'myVNet'

## Disable private endpoint network policy ##
$vnet.Subnets[0].PrivateEndpointNetworkPolicies = "Disabled"
$vnet | Set-AzVirtualNetwork

## Create private endpoint
$parameters2 = @{
    ResourceGroupName = 'CreatePrivateEndpointQS-rg'
    Name = 'myPrivateEndpoint'
    Location = 'eastus'
    Subnet = $vnet.Subnets[0]
    PrivateLinkServiceConnection = $privateEndpointConnection
}
New-AzPrivateEndpoint @parameters2
```
## <a name="configure-the-private-dns-zone"></a>프라이빗 DNS 영역 구성

이 섹션에서는 다음을 사용하여 프라이빗 DNS 영역을 만들어 구성합니다.

* [New-AzPrivateDnsZone](/powershell/module/az.privatedns/new-azprivatednszone)
* [New-AzPrivateDnsVirtualNetworkLink](/powershell/module/az.privatedns/new-azprivatednsvirtualnetworklink)
* [New-AzPrivateDnsZoneConfig](/powershell/module/az.network/new-azprivatednszoneconfig)
* [New-AzPrivateDnsZoneGroup](/powershell/module/az.network/new-azprivatednszonegroup)

```azurepowershell-interactive
## Place virtual network into variable. ##
$vnet = Get-AzVirtualNetwork -ResourceGroupName 'CreatePrivateEndpointQS-rg' -Name 'myVNet'

## Create private dns zone. ##
$parameters1 = @{
    ResourceGroupName = 'CreatePrivateEndpointQS-rg'
    Name = 'privatelink.azurewebsites.net'
}
$zone = New-AzPrivateDnsZone @parameters1

## Create dns network link. ##
$parameters2 = @{
    ResourceGroupName = 'CreatePrivateEndpointQS-rg'
    ZoneName = 'privatelink.azurewebsites.net'
    Name = 'myLink'
    VirtualNetworkId = $vnet.Id
}
$link = New-AzPrivateDnsVirtualNetworkLink @parameters2

## Create DNS configuration ##
$parameters3 = @{
    Name = 'privatelink.azurewebsites.net'
    PrivateDnsZoneId = $zone.ResourceId
}
$config = New-AzPrivateDnsZoneConfig @parameters3

## Create DNS zone group. ##
$parameters4 = @{
    ResourceGroupName = 'CreatePrivateEndpointQS-rg'
    PrivateEndpointName = 'myPrivateEndpoint'
    Name = 'myZoneGroup'
    PrivateDnsZoneConfig = $config
}
New-AzPrivateDnsZoneGroup @parameters4
```

## <a name="test-connectivity-to-private-endpoint"></a>프라이빗 엔드포인트에 연결 테스트

이 섹션에서는 이전 단계에서 만든 가상 머신을 사용하여 프라이빗 엔드포인트에서 SQL 서버에 연결합니다.

1. [Azure 포털](https://portal.azure.com) 
 
2. 왼쪽 탐색 창에서 **리소스 그룹** 을 선택합니다.

3. **CreatePrivateEndpointQS-rg** 를 선택합니다.

4. **myVM** 을 선택합니다.

5. **myVM** 에 대한 개요 페이지에서 **연결** 을 선택한 다음, **Bastion** 을 선택합니다.

6. 파란색 **Bastion 사용** 단추를 선택합니다.

7. 가상 머신 만들기에서 입력한 사용자 이름과 암호를 입력합니다.

8. 연결한 후 서버에서 Windows PowerShell을 엽니다.

9. `nslookup <your-webapp-name>.azurewebsites.net`를 입력합니다. **\<your-webapp-name>** 을 이전 단계에서 만든 웹앱의 이름으로 바꿉니다.  아래 표시된 것과 유사한 메시지가 표시됩니다.

    ```powershell
    Server:  UnKnown
    Address:  168.63.129.16

    Non-authoritative answer:
    Name:    mywebapp8675.privatelink.azurewebsites.net
    Address:  10.0.0.5
    Aliases:  mywebapp8675.azurewebsites.net
    ```

    웹앱 이름에 대해 **10.0.0.5** 의 개인 IP 주소가 반환됩니다.  이 주소는 이전에 만든 가상 네트워크의 서브넷에 있습니다.

10. **myVM** 에 대한 베스천 연결에서 Internet Explorer를 엽니다.

11. 웹앱의 URL(**https://\<your-webapp-name>.azurewebsites.net**)을 입력합니다.

12. 애플리케이션이 배포되지 않은 경우 기본 웹앱 페이지가 표시됩니다.

    :::image type="content" source="./media/create-private-endpoint-portal/web-app-default-page.png" alt-text="기본 웹앱 페이지" border="true":::

13. **myVM** 에 대한 연결을 닫습니다.

## <a name="clean-up-resources"></a>리소스 정리 
프라이빗 엔드포인트 및 VM 사용을 마쳤으면 [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup)을 사용하여 리소스 그룹과 리소스 그룹에 포함된 모든 리소스를 제거합니다.

```azurepowershell-interactive
Remove-AzResourceGroup -Name CreatePrivateEndpointQS-rg -Force
```

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 다음을 만들었습니다.

* 가상 네트워크 및 베스천 호스트
* 가상 머신
* Azure Web App에 대한 프라이빗 엔드포인트

가상 머신을 사용하여 프라이빗 엔드포인트에서 웹앱에 대한 연결을 안전하게 테스트했습니다.

프라이빗 엔드포인트를 지원하는 서비스에 대한 자세한 내용은 다음을 참조하세요.
> [!div class="nextstepaction"]
> [Private Link 가용성](private-link-overview.md#availability)
