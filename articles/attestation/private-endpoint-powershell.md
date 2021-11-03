---
title: Azure PowerShell을 사용하여 프라이빗 엔드포인트 만들기
description: Azure PowerShell을 사용하여 Azure Attestation에 대한 프라이빗 엔드포인트 만들기
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 03/26/2021
ms.author: mbaldwin
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: a799d161aef0047524d37e02b64b13debdacf5a4
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131020809"
---
# <a name="quickstart-create-a-private-endpoint-using-azure-powershell"></a>빠른 시작: Azure PowerShell을 사용하여 프라이빗 엔드포인트 만들기

프라이빗 엔드포인트를 통해 Azure Attestation에 안전하게 연결하여 Azure Private Link를 시작합니다.

이 빠른 시작에서는 Azure Attestation에 대한 프라이빗 엔드포인트를 만들고, 가상 머신을 배포하여 프라이빗 연결을 테스트합니다.  

> [!NOTE]
> 현재 구현에는 자동 승인 옵션만 포함됩니다. 프라이빗 엔드포인트 만들기를 계속하려면 구독을 허용 목록에 추가해야 합니다. 아래 단계를 진행하기 전에 서비스 팀에 연락하거나 [Azure 지원 페이지](https://azure.microsoft.com/support/options/)에서 Azure 지원 요청을 제출하세요.

## <a name="prerequisites"></a>필수 구성 요소

* [Azure Private Link](../private-link/private-link-overview.md)에 대해 알아보기
* [Azure PowerShell을 사용하여 Azure Attestation 설정](./quickstart-powershell.md)

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

Azure 리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다.

[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)을 사용하여 다음 리소스 그룹을 만듭니다.

```azurepowershell-interactive
## Create to your Azure account subscription and create a resource group in a desired location. ##
Connect-AzAccount
Set-AzSubscription "mySubscription"
$rg = "CreateAttestationPrivateLinkTutorial-rg"
$loc= "eastus"
New-AzResourceGroup -Name $rg -Location $loc
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
$vnet = New-AzVirtualNetwork -Name "myAttestationTutorialVNet" -ResourceGroupName $rg -Location $loc -AddressPrefix "10.0.0.0/16" -Subnet $subnetConfig, $bastsubnetConfig

## Create public IP address for bastion host. ##
$publicip = New-AzPublicIpAddress -Name "myBastionIP" -ResourceGroupName $rg -Location $loc -Sku "Standard" -AllocationMethod "Static"

## Create bastion host ##
New-AzBastion -ResourceGroupName $rg -Name "myBastion" -PublicIpAddress $publicip -VirtualNetwork $vnet
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

## Command to create network interface for VM ##
$nicVM = New-AzNetworkInterface -Name "myNicVM" -ResourceGroupName $rg -Location $loc -Subnet $vnet.Subnets[0] 

## Create a virtual machine configuration.##
$vmConfig = New-AzVMConfig -VMName "myVM" -VMSize "Standard_DS1_v2" | Set-AzVMOperatingSystem -Windows -ComputerName "myVM" -Credential $cred | Set-AzVMSourceImage -PublisherName "MicrosoftWindowsServer" -Offer "WindowsServer" -Skus "2019-Datacenter" -Version "latest" | Add-AzVMNetworkInterface -Id $nicVM.Id 

## Create the virtual machine ##
New-AzVM -ResourceGroupName $rg -Location $loc -VM $vmConfig
```

## <a name="create-an-attestation-provider"></a>증명 공급자 만들기

```azurepowershell-interactive
## Create an attestation provider ##
$attestationProviderName = "myattestationprovider"
$attestationProvider = New-AzAttestation -Name $attestationProviderName -ResourceGroupName $rg -Location $loc
$attestationProviderId = $attestationProvider.Id
```
## <a name="access-the-attestation-provider-from-local-machine"></a>로컬 컴퓨터에서 증명 공급자 액세스 ##
`nslookup <provider-name>.attest.azure.net`를 입력합니다. **\<provider-name>** 을 이전 단계에서 만든 증명 공급자 인스턴스의 이름으로 바꿉니다. 
```azurepowershell-interactive
## Access the attestation provider from local machine ##
nslookup myattestationprovider.eus.attest.azure.net

<# You'll receive a message similar to what is displayed below:

Server:  cdns01.comcast.net
Address:  2001:558:feed::1

Non-authoritative answer:
Name:    eus.service.attest.azure.net
Address:  20.62.219.160
Aliases:  myattestationprovider.eus.attest.azure.net
    attesteusatm.trafficmanager.net

#>
```

## <a name="create-private-endpoint"></a>프라이빗 엔드포인트 만들기

이 섹션에서는 다음을 사용하여 프라이빗 엔드포인트 및 연결을 만듭니다.

* [New-AzPrivateLinkServiceConnection](/powershell/module/az.network/New-AzPrivateLinkServiceConnection)
* [New-AzPrivateEndpoint](/powershell/module/az.network/new-azprivateendpoint)

```azurepowershell-interactive
## Create private endpoint connection. ##
$privateEndpointConnection = New-AzPrivateLinkServiceConnection -Name "myConnection" -PrivateLinkServiceId $attestationProviderId -GroupID "Standard"

## Disable private endpoint network policy ##
$vnet.Subnets[0].PrivateEndpointNetworkPolicies = "Disabled" 
$vnet | Set-AzVirtualNetwork

## Create private endpoint
New-AzPrivateEndpoint  -ResourceGroupName $rg -Name "myPrivateEndpoint" -Location $loc -Subnet $vnet.Subnets[0] -PrivateLinkServiceConnection $privateEndpointConnection
```
## <a name="configure-the-private-dns-zone"></a>프라이빗 DNS 영역 구성

이 섹션에서는 다음을 사용하여 프라이빗 DNS 영역을 만들어 구성합니다.

* [New-AzPrivateDnsZone](/powershell/module/az.privatedns/new-azprivatednszone)
* [New-AzPrivateDnsVirtualNetworkLink](/powershell/module/az.privatedns/new-azprivatednsvirtualnetworklink)
* [New-AzPrivateDnsZoneConfig](/powershell/module/az.network/new-azprivatednszoneconfig)
* [New-AzPrivateDnsZoneGroup](/powershell/module/az.network/new-azprivatednszonegroup)

```azurepowershell-interactive
## Create private dns zone. ##
$zone = New-AzPrivateDnsZone -ResourceGroupName $rg -Name "privatelink.attest.azure.net"

## Create dns network link. ##
$link = New-AzPrivateDnsVirtualNetworkLink -ResourceGroupName $rg -ZoneName "privatelink.attest.azure.net" -Name "myLink" -VirtualNetworkId $vnet.Id

## Create DNS configuration ##
$config = New-AzPrivateDnsZoneConfig -Name "privatelink.attest.azure.net" -PrivateDnsZoneId $zone.ResourceId

## Create DNS zone group. ##
New-AzPrivateDnsZoneGroup -ResourceGroupName $rg -PrivateEndpointName "myPrivateEndpoint" -Name "myZoneGroup" -PrivateDnsZoneConfig $config
```

## <a name="test-connectivity-to-private-endpoint"></a>프라이빗 엔드포인트에 연결 테스트

이 섹션에서는 이전 단계에서 만든 가상 머신을 사용하여 프라이빗 엔드포인트에서 SQL 서버에 연결합니다.

1. [Azure 포털](https://portal.azure.com) 
 
2. 왼쪽 탐색 창에서 **리소스 그룹** 을 선택합니다.

3. **CreateAttestationPrivateLinkTutorial-rg** 를 선택합니다.

4. **myVM** 을 선택합니다.

5. **myVM** 에 대한 개요 페이지에서 **연결** 을 선택한 다음, **Bastion** 을 선택합니다.

6. 파란색 **Bastion 사용** 단추를 선택합니다.

7. 가상 머신 만들기에서 입력한 사용자 이름과 암호를 입력합니다.

8. 연결한 후 서버에서 Windows PowerShell을 엽니다.

9. `nslookup <provider-name>.attest.azure.net`를 입력합니다. **\<provider-name>** 을 이전 단계에서 만든 증명 공급자 인스턴스의 이름으로 바꿉니다.

    ```azurepowershell-interactive
    ## Access the attestation provider from local machine ##
    nslookup myattestationprovider.eus.attest.azure.net
    
    <# You'll receive a message similar to what is displayed below:
    
    Server:  cdns01.comcast.net
    Address:  2001:558:feed::1
        cdns01.comcast.net can't find myattestationprovider.eus.attest.azure.net: Non-existent domain

    #>

    ## Access the attestation provider from the VM created in the same virtual network as the private endpoint.   ##
    nslookup myattestationprovider.eus.attest.azure.net
    
    <# You'll receive a message similar to what is displayed below:
    
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    myattestationprovider.eastus.test.attest.azure.net
    
    #>
    ```
