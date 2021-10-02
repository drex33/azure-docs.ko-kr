---
title: Azure 가상 머신의 여러 IP 주소 - PowerShell | Microsoft Docs
description: PowerShell을 사용하여 가상 머신에 여러 IP 주소를 할당하는 방법을 알아봅니다. | Resource Manager
services: virtual-network
documentationcenter: na
author: asudbring
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/24/2017
ms.author: allensu
ms.openlocfilehash: 58f3a8e2a7da4589d7306b3350e72c50ed505e2f
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/01/2021
ms.locfileid: "129372886"
---
# <a name="assign-multiple-ip-addresses-to-virtual-machines-using-powershell"></a>PowerShell을 사용하여 가상 머신에 여러 IP 주소 할당

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [virtual-network-multiple-ip-addresses-intro.md](../../../includes/virtual-network-multiple-ip-addresses-intro.md)]

이 문서에서는 PowerShell을 사용하여 Azure Resource Manager 배포 모델을 통해 VM(가상 컴퓨터)을 만드는 방법을 설명합니다. 클래식 VM에는 각각 고유한 IP 주소를 가진 여러 NIC가 있을 수 있지만 클래식 배포 모델을 통해 만든 단일 NIC에는 여러 IP 주소를 할당할 수 없습니다. Azure 배포 모델에 대해 자세히 알아보려면 [배포 모델 이해](../../azure-resource-manager/management/deployment-models.md) 문서를 참조하세요.

[!INCLUDE [virtual-network-multiple-ip-addresses-scenario.md](../../../includes/virtual-network-multiple-ip-addresses-scenario.md)]

## <a name="create-a-vm-with-multiple-ip-addresses"></a><a name = "create"></a>여러 IP 주소를 사용하여 VM 만들기

다음 단계는 시나리오에 설명된 대로 여러 IP 주소를 가진 예시 VM을 만드는 방법을 설명합니다. 변수 값을 구현에 필요한 대로 변경합니다.

1. PowerShell 명령 프롬프트를 열고 단일 PowerShell 세션 내에서 이 섹션의 나머지 단계를 완료합니다. Azure PowerShell을 아직 설치 및 구성하지 않은 경우 [Azure PowerShell 설치 및 구성 방법](/powershell/azure/) 문서의 단계를 완료합니다.
2. `Connect-AzAccount` 명령을 사용하여 계정에 로그인합니다.
3. *myResourceGroup* 및 *westus* 를 선택한 이름과 위치로 바꿉니다. 리소스 그룹을 생성합니다. 리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다.

   ```powershell
   $RgName   = "MyResourceGroup"
   $Location = "westus"

   New-AzResourceGroup `
   -Name $RgName `
   -Location $Location
   ```

4. 리소스 그룹과 같은 위치에 가상 네트워크(VNet) 및 서브넷을 만듭니다.

   ```powershell

   # Create a subnet configuration
   $SubnetConfig = New-AzVirtualNetworkSubnetConfig `
   -Name MySubnet `
   -AddressPrefix 10.0.0.0/24

   # Create a virtual network
   $VNet = New-AzVirtualNetwork `
   -ResourceGroupName $RgName `
   -Location $Location `
   -Name MyVNet `
   -AddressPrefix 10.0.0.0/16 `
   -Subnet $subnetConfig

   # Get the subnet object
   $Subnet = Get-AzVirtualNetworkSubnetConfig -Name $SubnetConfig.Name -VirtualNetwork $VNet
   ```

5. NSG(네트워크 보안 그룹) 및 규칙을 만듭니다. NSG는 인바운드 및 아웃바운드 규칙을 사용하여 VM을 보호합니다. 이 경우 포트 3389에 대해 들어오는 원격 데스크톱 연결을 허용하는 인바운드 규칙이 만들어집니다.

    ```powershell
    
    # Create an inbound network security group rule for port 3389

    $NSGRule = New-AzNetworkSecurityRuleConfig `
    -Name MyNsgRuleRDP `
    -Protocol Tcp `
    -Direction Inbound `
    -Priority 1000 `
    -SourceAddressPrefix * `
    -SourcePortRange * `
    -DestinationAddressPrefix * `
    -DestinationPortRange 3389 -Access Allow
    
    # Create a network security group
    $NSG = New-AzNetworkSecurityGroup `
    -ResourceGroupName $RgName `
    -Location $Location `
    -Name MyNetworkSecurityGroup `
    -SecurityRules $NSGRule
    ```

6. NIC에 대한 기본 IP 구성을 정의합니다. 이전에 정의된 값을 사용하지 않는 경우 10.0.0.4를 만든 서브넷의 올바른 주소로 변경합니다. 고정 IP 주소를 할당하기 전에 먼저 해당 주소를 이미 사용하고 있지 않은지 확인하는 것이 좋습니다. `Test-AzPrivateIPAddressAvailability -IPAddress 10.0.0.4 -VirtualNetwork $VNet` 명령을 입력합니다. 주소를 사용할 수 있는 경우 출력은 *True* 를 반환합니다. 주소를 사용할 수 없는 경우 출력은 *False* 와 사용할 수 있는 주소 목록을 반환합니다. 

    다음 명령에서 **\<replace-with-your-unique-name>을 사용할 고유 DNS 이름으로 바꿉니다.** 이름은 Azure 지역 내의 모든 공용 IP 주소에서 고유해야 합니다. 선택적 매개 변수입니다. 공용 IP 주소를 사용하여 VM에 연결하려는 경우에만 제거할 수 있습니다.

    ```powershell
    
    # Create a public IP address
    $PublicIP1 = New-AzPublicIpAddress `
    -Name "MyPublicIP1" `
    -ResourceGroupName $RgName `
    -Location $Location `
    -DomainNameLabel <replace-with-your-unique-name> `
    -AllocationMethod Static
        
    #Create an IP configuration with a static private IP address and assign the public IP address to it
    $IpConfigName1 = "IPConfig-1"
    $IpConfig1     = New-AzNetworkInterfaceIpConfig `
    -Name $IpConfigName1 `
    -Subnet $Subnet `
    -PrivateIpAddress 10.0.0.4 `
    -PublicIpAddress $PublicIP1 `
    -Primary
    ```

    NIC에 여러 IP 구성을 할당하는 경우 하나의 구성이 *기본* 으로 할당되어야 합니다.

    > [!NOTE]
    > 공용 IP 주소에는 명목 요금이 부과됩니다. IP 주소 가격에 대한 자세한 내용은 [IP 주소 가격](https://azure.microsoft.com/pricing/details/ip-addresses) 페이지를 참조하세요. 구독 내에서 사용할 수 있는 공용 IP 주소의 수는 제한되어 있습니다. 이러한 한에 대한 자세한 내용은 [Azure 제한](../../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits) 문서를 참조하세요.

7. NIC에 대한 보조 IP 구성을 정의합니다. 필요에 따라 구성을 추가 또는 제거할 수 있습니다. 각 IP 구성에 개인 IP 주소가 할당되어야 합니다. 경우에 따라 각 구성에 공용 IP 주소가 할당될 수 있습니다.

    ```powershell
    
    # Create a public IP address
    $PublicIP2 = New-AzPublicIpAddress `
    -Name "MyPublicIP2" `
    -ResourceGroupName $RgName `
    -Location $Location `
    -AllocationMethod Static
        
    #Create an IP configuration with a static private IP address and assign the public IP address to it
    $IpConfigName2 = "IPConfig-2"
    $IpConfig2     = New-AzNetworkInterfaceIpConfig `
    -Name $IpConfigName2 `
    -Subnet $Subnet `
    -PrivateIpAddress 10.0.0.5 `
    -PublicIpAddress $PublicIP2
        
    $IpConfigName3 = "IpConfig-3"
    $IpConfig3 = New-AzNetworkInterfaceIpConfig `
    -Name $IPConfigName3 `
    -Subnet $Subnet `
    -PrivateIpAddress 10.0.0.6
    ```

8. NIC를 만들고 세 가지 IP 구성을 연결합니다.

   ```powershell
   $NIC = New-AzNetworkInterface `
   -Name MyNIC `
   -ResourceGroupName $RgName `
   -Location $Location `
   -NetworkSecurityGroupId $NSG.Id `
   -IpConfiguration $IpConfig1,$IpConfig2,$IpConfig3
   ```

   >[!NOTE]
   >이 문서에서는 모든 구성이 한 NIC에 할당되어 있지만 VM에 연결된 모든 NIC에 여러 IP 구성을 할당할 수 있습니다. 여러 NIC로 VM을 만드는 방법에 대해 자세히 알아보려면 [여러 NIC를 사용하여 VM 만들기](../../virtual-machines/windows/multiple-nics.md) 문서를 참조하세요.

9. 다음 명령을 입력하여 VM을 만듭니다.

    ```powershell
    
    # Define a credential object. When you run these commands, you're prompted to enter a username and password for the VM you're creating.
    $cred = Get-Credential
    
    # Create a virtual machine configuration
    $VmConfig = New-AzVMConfig `
    -VMName MyVM `
    -VMSize Standard_DS1_v2 | `
    Set-AzVMOperatingSystem -Windows `
    -ComputerName MyVM `
    -Credential $cred | `
    Set-AzVMSourceImage `
    -PublisherName MicrosoftWindowsServer `
    -Offer WindowsServer `
    -Skus 2016-Datacenter `
    -Version latest | `
    Add-AzVMNetworkInterface `
    -Id $NIC.Id
    
    # Create the VM
    New-AzVM `
    -ResourceGroupName $RgName `
    -Location $Location `
    -VM $VmConfig
    ```

10. 이 문서의 [VM 운영 체제에 IP 주소 추가](#os-config) 섹션에 나오는 사용자 운영 체제별 단계를 완료하여 개인 IP 주소를 VM 운영 체제에 추가합니다. 운영 체제에 공용 IP 주소를 추가하지 마십시오.

## <a name="add-ip-addresses-to-a-vm"></a><a name="add"></a>VM에 IP 주소 추가

다음 단계를 완료하여 개인 및 공용 IP 주소를 Azure 네트워크 인터페이스에 추가할 수 있습니다. 다음 섹션의 예제는 이 문서의 [시나리오](#scenario)에서 설명한 3개의 IP로 구성된 VM이 이미 있다는 가정 하에 진행하되 필수 사항은 아닙니다.

1. PowerShell 명령 프롬프트를 열고 단일 PowerShell 세션 내에서 이 섹션의 나머지 단계를 완료합니다. Azure PowerShell을 아직 설치 및 구성하지 않은 경우 [Azure PowerShell 설치 및 구성 방법](/powershell/azure/) 문서의 단계를 완료합니다.
2. 다음 $Variables의 "값"을 IP 주소를 추가하려는 NIC의 이름과 NIC가 있는 리소스 그룹 및 위치로 변경합니다.

   ```powershell
   $NicName  = "MyNIC"
   $RgName   = "MyResourceGroup"
   $Location = "westus"
   ```

   변경하려는 NIC의 이름을 잘 모르는 경우 다음 명령을 입력한 다음 이전 변수의 값을 변경합니다.

   ```powershell
   Get-AzNetworkInterface | Format-Table Name, ResourceGroupName, Location
   ```

3. 다음 명령을 입력하여 변수를 만들고 기존 NIC로 설정합니다.

   ```powershell
   $MyNIC = Get-AzNetworkInterface -Name $NicName -ResourceGroupName $RgName
   ```

4. 다음 명령에서 *MyVNet* 및 *MySubnet* 을 NIC가 연결된 VNet 및 서브넷의 이름으로 변경합니다. NIC가 연결된 VNet 및 서브넷 개체를 검색하는 명령을 입력합니다.

   ```powershell
   $MyVNet = Get-AzVirtualnetwork -Name MyVNet -ResourceGroupName $RgName
   $Subnet = $MyVnet.Subnets | Where-Object { $_.Name -eq "MySubnet" }
   ```

   NIC가 연결된 VNet 또는 서브넷 이름을 모르는 경우 다음 명령을 입력합니다.

   ```powershell
   $MyNIC.IpConfigurations
   ```

   출력에서 다음과 유사한 예제 출력을 찾습니다.

   ```
   "Id": "/subscriptions/[Id]/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/MyVNet/subnets/MySubnet"
   ```

    이 출력의 경우 *MyVnet* 은 VNet, *MySubnet* 은 NIC가 연결된 서브넷입니다.

5. 요구 사항에 따라 다음 섹션 중 하나의 단계를 완료합니다.

   **개인 IP 주소 추가**

   NIC에 개인 IP 주소를 추가하려면 IP 구성을 만들어야 합니다. 다음 명령은 10.0.0.7의 고정 IP 주소로 구성을 만듭니다. 고정 IP 주소를 지정하는 경우 서브넷에 사용되지 않는 주소이어야 합니다. `Test-AzPrivateIPAddressAvailability -IPAddress 10.0.0.7 -VirtualNetwork $myVnet` 명령을 입력하여 먼저 주소를 사용할 수 있는지 테스트 하는 것이 좋습니다. IP 주소를 사용할 수 있는 경우 출력은 *True* 를 반환합니다. IP 주소를 사용할 수 없는 경우 출력은 *False* 와 사용할 수 있는 주소 목록을 반환합니다.

   ```powershell
   Add-AzNetworkInterfaceIpConfig -Name IPConfig-4 -NetworkInterface `
   $MyNIC -Subnet $Subnet -PrivateIpAddress 10.0.0.7
   ```

   고유한 구성 이름과 개인 IP 주소를 사용하여 필요한 만큼 구성을 만듭니다(정적 IP 주소를 가진 구성의 경우).

   이 문서의 [VM 운영 체제에 IP 주소 추가](#os-config) 섹션에 나오는 사용자 운영 체제별 단계를 완료하여 개인 IP 주소를 VM 운영 체제에 추가합니다.

   **공용 IP 주소 추가**

   공용 IP 주소 리소스를 새 IP 구성 또는 기존 IP 구성에 연결하면 공용 IP 주소가 추가됩니다. 필요에 따라 이후 섹션 중 하나에 나와 있는 단계를 완료합니다.

   > [!NOTE]
   > 공용 IP 주소에는 명목 요금이 부과됩니다. IP 주소 가격에 대한 자세한 내용은 [IP 주소 가격](https://azure.microsoft.com/pricing/details/ip-addresses) 페이지를 참조하세요. 구독 내에서 사용할 수 있는 공용 IP 주소의 수는 제한되어 있습니다. 이러한 한에 대한 자세한 내용은 [Azure 제한](../../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits) 문서를 참조하세요.
   >

   **새 IP 구성에 공용 IP 주소 리소스 연결**

   새 IP 구성의 공용 IP 주소를 추가할 때마다 모든 IP 구성 시 개인 IP 주소가 있어야 하기 때문에 개인 IP 주소도 추가해야 합니다. 기존 공용 IP 주소 리소스를 추가하거나 새로 만들 수 있습니다. 새 파일을 만들려면 다음 명령을 입력합니다.

   ```powershell
   $myPublicIp3 = New-AzPublicIpAddress `
   -Name "myPublicIp3" `
   -ResourceGroupName $RgName `
   -Location $Location `
   -AllocationMethod Static
   ```

   고정 개인 IP 주소 및 여기에 연결된 *myPublicIp3* 공용 IP 주소 리소스가 있는 새 IP 구성을 만들려면 다음 명령을 입력합니다.

   ```powershell
   Add-AzNetworkInterfaceIpConfig `
   -Name IPConfig-4 `
   -NetworkInterface $myNIC `
   -Subnet $Subnet `
   -PrivateIpAddress 10.0.0.7 `
   -PublicIpAddress $myPublicIp3
   ```

   **기존 IP 구성에 공용 IP 주소 리소스 연결**

   공용 IP 주소 리소스는 아직 연결한 리소스가 없는 IP 구성에만 연결될 수 있습니다. 다음 명령을 입력하면 IP 구성에 연결된 공용 IP 주소가 있는지 여부를 확인할 수 있습니다.

   ```powershell
   $MyNIC.IpConfigurations | Format-Table Name, PrivateIPAddress, PublicIPAddress, Primary
   ```

   그러면 다음과 같은 출력이 표시됩니다.

   ```
   Name       PrivateIpAddress PublicIpAddress                                           Primary

   IPConfig-1 10.0.0.4         Microsoft.Azure.Commands.Network.Models.PSPublicIpAddress    True
   IPConfig-2 10.0.0.5         Microsoft.Azure.Commands.Network.Models.PSPublicIpAddress   False
   IpConfig-3 10.0.0.6                                                                     False
   ```

   *IpConfig-3* 에 대한 **PublicIpAddress** 열이 비어 있기 때문에 현재 공용 IP 주소 리소스가 여기에 연결되어 있지 않습니다. IpConfig-3에 기존 공용 IP 주소 리소스를 추가하거나 다음 명령을 입력하여 새로 만들 수 있습니다.

   ```powershell
   $MyPublicIp3 = New-AzPublicIpAddress `
   -Name "MyPublicIp3" `
   -ResourceGroupName $RgName `
   -Location $Location -AllocationMethod Static
   ```

   *IpConfig-3* 이라는 기존 IP 구성에 공용 IP 주소 리소스를 연결하려면 다음 명령을 입력합니다.

   ```powershell
   Set-AzNetworkInterfaceIpConfig `
   -Name IpConfig-3 `
   -NetworkInterface $mynic `
   -Subnet $Subnet `
   -PublicIpAddress $myPublicIp3
   ```

6. 새로운 IP 구성으로 NIC를 설정하려면 다음 명령을 입력합니다.

   ```powershell
   Set-AzNetworkInterface -NetworkInterface $MyNIC
   ```

7. 다음 명령을 입력하여 NIC에 할당된 개인 IP 주소 및 공용 IP 주소 리소스를 봅니다.

   ```powershell
   $MyNIC.IpConfigurations | Format-Table Name, PrivateIPAddress, PublicIPAddress, Primary
   ```

8. 이 문서의 [VM 운영 체제에 IP 주소 추가](#os-config) 섹션에 나오는 사용자 운영 체제별 단계를 완료하여 개인 IP 주소를 VM 운영 체제에 추가합니다. 운영 체제에 공용 IP 주소를 추가하지 마세요.

[!INCLUDE [virtual-network-multiple-ip-addresses-os-config.md](../../../includes/virtual-network-multiple-ip-addresses-os-config.md)]
