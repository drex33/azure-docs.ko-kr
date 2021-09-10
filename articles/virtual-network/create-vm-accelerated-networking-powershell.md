---
title: 가속화된 네트워킹을 사용하는 VM 만들기 - Azure PowerShell
description: 가속화된 네트워킹을 사용하여 Windows VM(가상 머신)을 만들어 네트워킹 성능을 크게 향상시킵니다.
services: virtual-network
documentationcenter: ''
author: gsilva5
manager: gedegrac
editor: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 04/15/2020
ms.author: gsilva
ms.openlocfilehash: 44f061b124523a920a6fe02bb0c04233d533ab1c
ms.sourcegitcommit: 86ca8301fdd00ff300e87f04126b636bae62ca8a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/16/2021
ms.locfileid: "122538183"
---
# <a name="create-a-windows-vm-with-accelerated-networking-using-azure-powershell"></a>Azure PowerShell을 사용하여 가속 네트워킹을 사용하는 VM 만들기

이 자습서에서는 가속화된 네트워킹을 사용하여 Windows VM(가상 머신)을 만드는 방법에 대해 알아봅니다.

> [!NOTE]
> Linux 가상 머신에서 가속화된 네트워킹을 사용하려면 [가속화된 네트워킹을 사용하여 Linux VM 만들기](create-vm-accelerated-networking-cli.md)를 참조하세요.

가속화된 네트워킹을 사용하면 VM에 대한 단일 루트 I/O 가상화(SR-IOV)를 구현할 수 있어 네트워킹 성능이 크게 향상됩니다. 이 고성능 경로는 데이터 경로에서 호스트를 우회함으로써 지원되는 VM 유형에서 가장 까다로운 네트워크 워크로드에 대한 대기 시간, 지터 및 CPU 사용률을 줄입니다. 다음 다이어그램은 가속화된 네트워킹을 사용하거나 사용하지 않고 두 VM이 통신하는 방법을 보여 줍니다.

![가속화된 네트워킹을 사용하거나 사용하지 않는 Azure Virtual Machines 간 통신](./media/create-vm-accelerated-networking/accelerated-networking.png)

가속화된 네트워킹을 사용하지 않는 경우 VM에서 들어오고 나가는 모든 네트워킹 트래픽이 호스트와 가상 스위치를 통과해야 합니다. 가상 스위치는 네트워크 보안 그룹, 액세스 제어 목록, 격리 및 네트워크 트래픽에 대한 다른 네트워크 가상화 서비스 등, 모든 정책 적용을 제공합니다.

> [!NOTE]
> 가상 스위치에 대 한 자세한 참조 [Hyper-v 가상 스위치](/windows-server/virtualization/hyper-v-virtual-switch/hyper-v-virtual-switch)합니다.

가속화된 네트워킹을 사용하는 경우 네트워크 트래픽이 VM의 NIC(네트워크 인터페이스)에 도착한 다음 VM으로 전달됩니다. 이제 가상 스위치가 적용되는 모든 네트워크 정책은 오프로드되어 하드웨어에 적용됩니다. 정책은 하드웨어에서 적용되므로 NIC는 네트워크 트래픽을 VM에 직접 전달할 수 있습니다. NIC는 호스트와 가상 스위치를 바이패스하지만 호스트에 적용된 모든 정책을 관리합니다.

가속화된 네트워킹의 이점은 사용하도록 설정된 VM에만 적용된다는 것입니다. 최상의 결과를 얻으려면 동일한 Azure Virtual Network에 연결된 둘 이상의 VM에서 이 기능을 사용하도록 설정합니다. 가상 네트워크에서 통신하거나 온-프레미스에 연결할 때 이 기능을 사용하면 전체 대기 시간에 미치는 영향을 최소화할 수 있습니다.

## <a name="benefits"></a>이점

- **낮은 대기 시간/높은 pps(초당 패킷)** : 데이터 경로에서 가상 스위치를 제거하면 패킷이 정책 처리를 위해 호스트에서 소요하는 시간이 줄어듭니다. 또한 VM 내에서 처리할 수 있는 패킷 수를 늘립니다.

- **지터 감소**: 가상 스위치 처리는 적용해야 하는 정책의 양에 따라 다릅니다. 또한 처리를 수행하는 CPU의 워크로드에 따라 달라집니다. 정책 적용을 하드웨어로 오프로드하면 패킷을 VM에 직접 전달하여 이러한 가변성이 제거됩니다. 또한 오프로딩을 통해 호스트 간 통신, 모든 소프트웨어 인터럽트 및 모든 컨텍스트 전환이 제거됩니다.

- **CPU 사용률 감소:** 호스트의 가상 스위치를 무시하면 네트워크 트래픽 처리에 사용되는 CPU가 감소됩니다.

## <a name="supported-operating-systems"></a>지원되는 운영 체제

지원되는 Windows 버전은 다음과 같습니다.

- **Windows Server 2019 Standard/Datacenter**
- **Windows Server 2016 Standard/Datacenter** 
- **Windows Server 2012 R2 Standard/Datacenter**

## <a name="limitations-and-constraints"></a>제한 사항 및 제약 조건

### <a name="supported-vm-instances"></a>지원되는 VM 인스턴스

가속 네트워킹은 가장 일반적인 용도로 2개 이상의 vCPU가 포함된 계산 최적화 인스턴스 크기에서 지원됩니다. 하이퍼스레딩을 지원하는 인스턴스에서 가속 네트워킹은 4개 이상의 vCPU가 포함된 VM 인스턴스에서 지원됩니다. 

가속화된 네트워킹에 대한 지원은 개별 [가상 머신 크기](../virtual-machines/sizes.md) 설명서에서 확인할 수 있습니다. 

### <a name="custom-images"></a>사용자 지정 이미지

사용자 지정 이미지를 사용 중이고 이미지가 가속화된 네트워킹을 지원하는 경우 Azure에서 Mellanox ConnectX-3 및 ConnectX-4 Lx NIC와 작동하는 필수 드라이버가 있어야 합니다.

### <a name="regions"></a>영역

가속화된 네트워킹은 모든 글로벌 Azure 지역 및 Azure Government 클라우드에서 사용할 수 있습니다.

### <a name="enabling-accelerated-networking-on-a-running-vm"></a>실행 중인 VM에서 가속화된 네트워킹 사용

가속화된 네트워킹을 사용하지 않고 지원되는 VM 크기에서는 기능이 중지되고 할당이 취소된 경우에만 사용하도록 설정할 수 있습니다.

### <a name="deployment-through-azure-resource-manager"></a>Azure Resource Manager를 통한 배포

가속화된 네트워킹을 사용하여 가상 머신(클래식)을 배포할 수 없습니다.

## <a name="vm-creation-using-the-portal"></a>포털을 사용하여 VM 만들기

이 문서에서는 Azure PowerShell을 통해 가속화된 네트워킹을 사용하는 VM을 만드는 단계를 안내하지만, [Azure Portal을 통해 가속화된 네트워킹을 사용하는 가상 머신 만들기](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)도 가능합니다. 포털에서 VM을 만들 때 **가상 머신 만들기** 페이지에서 **네트워킹** 탭을 선택합니다. 이 탭에는 **가속화된 네트워킹** 옵션이 있습니다. [지원되는 운영 체제](#supported-operating-systems) 및 [VM 크기](#supported-vm-instances)를 선택한 경우 이 옵션은 자동으로 **On** 으로 설정됩니다. 그러지 않으면 옵션이 **Off** 로 설정되고 Azure에 사용할 수 없는 이유가 표시됩니다.

> [!NOTE]
> 지원되는 운영 체제만 포털을 통해 사용하도록 설정할 수 있습니다. 사용자 지정 이미지를 사용하고 이미지가 가속화된 네트워킹을 지원하는 경우 CLI 또는 PowerShell을 사용하여 VM을 만듭니다. 

VM을 만든 후 가속화된 네트워킹을 사용할 수 있는지 여부를 확인할 수 있습니다. 다음 지침을 따릅니다.

1. VM을 관리하기 위해 [Azure Portal](https://portal.azure.com)로 이동합니다. **가상 머신** 을 검색하여 선택합니다.

2. 가상 컴퓨터 목록에서 새 VM을 선택합니다.

3. VM 메뉴 모음에서 **네트워킹** 을 선택합니다.

포털에서 네트워크 인터페이스 정보의 **가속화된 네트워킹** 레이블 옆에 가속화된 네트워킹 상태가 **사용 안 함** 또는 **사용** 으로 표시됩니다.

## <a name="vm-creation-using-powershell"></a>PowerShell을 사용한 VM 만들기

계속하기 전에 [Azure PowerShell](/powershell/azure/install-az-ps) 버전 1.0.0 이상을 설치합니다. 현재 설치된 버전을 찾으려면 `Get-Module -ListAvailable Az`을 실행합니다. 설치 또는 업그레이드해야 할 경우 [PowerShell 갤러리](https://www.powershellgallery.com/packages/Az)에서 최신 버전의 Az 모듈을 설치합니다. PowerShell 세션에서 [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) 명령을 사용하여 Azure 계정에 로그인합니다.

다음 예제에서 매개 변수 이름을 고유한 값으로 바꿉니다. 예제 매개 변수 이름에는 *myResourceGroup*, *myNic*, *myVM* 이 포함됩니다.

### <a name="create-a-virtual-network"></a>가상 네트워크 만들기

1. [New-AzResourceGroup](/powershell/module/az.Resources/New-azResourceGroup)을 사용하여 리소스 그룹을 만듭니다. 다음 명령을 사용하면 *centralus* 위치에 *myResourceGroup* 이라는 리소스 그룹을 만듭니다.

    ```azurepowershell
    New-AzResourceGroup -Name "myResourceGroup" -Location "centralus"
    ```

2. [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.Network/New-azVirtualNetworkSubnetConfig)를 사용하여 서브넷 구성을 만듭니다. 다음 명령을 사용하면 *mySubnet* 이라는 서브넷을 만듭니다.

    ```azurepowershell
    $subnet = New-AzVirtualNetworkSubnetConfig `
        -Name "mySubnet" `
        -AddressPrefix "192.168.1.0/24"
    ```

3. *mySubnet* 서브넷이 있는 [New-AzVirtualNetwork](/powershell/module/az.Network/New-azVirtualNetwork)로 가상 네트워크를 만듭니다.

    ```azurepowershell
    $vnet = New-AzVirtualNetwork -ResourceGroupName "myResourceGroup" `
        -Location "centralus" `
        -Name "myVnet" `
        -AddressPrefix "192.168.0.0/16" `
        -Subnet $Subnet
    ```

### <a name="create-a-network-security-group"></a>네트워크 보안 그룹 만들기

1. [New-AzNetworkSecurityRuleConfig](/powershell/module/az.Network/New-azNetworkSecurityRuleConfig)를 사용하여 네트워크 보안 그룹 규칙을 만듭니다.

    ```azurepowershell
    $rdp = New-AzNetworkSecurityRuleConfig `
        -Name 'Allow-RDP-All' `
        -Description 'Allow RDP' `
        -Access Allow `
        -Protocol Tcp `
        -Direction Inbound `
        -Priority 100 `
        -SourceAddressPrefix * `
        -SourcePortRange * `
        -DestinationAddressPrefix * `
        -DestinationPortRange 3389
    ```

2. [New-AzNetworkSecurityGroup](/powershell/module/az.Network/New-azNetworkSecurityGroup)을 사용하여 네트워크 보안 그룹을 만들고 여기에 *Allow-RDP-All* 보안 규칙을 할당합니다. *Allow-RDP-All* 규칙 외에도 네트워크 보안 그룹에는 여러 기본 규칙이 포함되어 있습니다. 하나의 기본 규칙이 인터넷에서 모든 인바운드 액세스를 사용하지 않도록 설정합니다. 만든 후에는 *Allow-RDP-All* 규칙이 네트워크 보안 그룹에 할당되므로 VM에 원격으로 연결할 수 있습니다.

    ```azurepowershell
    $nsg = New-AzNetworkSecurityGroup `
        -ResourceGroupName myResourceGroup `
        -Location centralus `
        -Name "myNsg" `
        -SecurityRules $rdp
    ```

3. [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.Network/Set-azVirtualNetworkSubnetConfig)를 사용하여 네트워크 보안 그룹을 *mySubnet* 서브넷에 연결합니다. 네트워크 보안 그룹의 규칙은 서브넷에 배포된 모든 리소스에 효율적입니다.

    ```azurepowershell
    Set-AzVirtualNetworkSubnetConfig `
        -VirtualNetwork $vnet `
        -Name 'mySubnet' `
        -AddressPrefix "192.168.1.0/24" `
        -NetworkSecurityGroup $nsg
    ```

### <a name="create-a-network-interface-with-accelerated-networking"></a>가속 네트워킹을 사용하여 네트워크 인터페이스 만들기

1. [New-AzPublicIpAddress](/powershell/module/az.Network/New-azPublicIpAddress)를 사용하여 공용 IP 주소를 만듭니다. 인터넷에서는 VM에 액세스하지 않으려는 공용 IP 주소가 필요하지 않습니다. 그러나 이 문서의 단계를 완료해야 합니다.

    ```azurepowershell
    $publicIp = New-AzPublicIpAddress `
        -ResourceGroupName myResourceGroup `
        -Name 'myPublicIp' `
        -location centralus `
        -AllocationMethod Dynamic
    ```

2. [New-AzureRmNetworkInterface](/powershell/module/az.Network/New-azNetworkInterface)와 가속 네트워킹을 사용하여 네트워크 인터페이스를 만들고 네트워크 인터페이스에 공용 IP 주소를 할당합니다. 다음 예에서는 *myVnet* 가상 네트워크의 *mySubnet* 서브넷에서 *myNic* 라는 네트워크 인터페이스를 만들며 여기에 *myPublicIp* 공용 IP 주소를 할당합니다.

    ```azurepowershell
    $nic = New-AzNetworkInterface `
        -ResourceGroupName "myResourceGroup" `
        -Name "myNic" `
        -Location "centralus" `
        -SubnetId $vnet.Subnets[0].Id `
        -PublicIpAddressId $publicIp.Id `
        -EnableAcceleratedNetworking
    ```

### <a name="create-a-vm-and-attach-the-network-interface"></a>VM 만들기 및 네트워크 인터페이스 연결

1. 로그인하라는 메시지를 표시하는 [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential)을 사용하여 VM 자격 증명을 `$cred` 변수로 설정합니다.

    ```azurepowershell
    $cred = Get-Credential
    ```

2. [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig)를 사용하여 VM을 정의합니다. 다음 명령을 사용하면 가속화된 네트워킹(*Standard_DS4_v2*)을 지원하는 VM 크기를 사용하여 *myVM* 이라는 VM을 정의합니다.

    ```azurepowershell
    $vmConfig = New-AzVMConfig -VMName "myVm" -VMSize "Standard_DS4_v2"
    ```

    모든 VM 크기 및 특성 목록은 [Windows VM 크기](../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json)를 참조하세요.

3. [Set-AzVMOperatingSystem](/powershell/module/az.compute/set-azvmoperatingsystem) 및 [Set-AzVMSourceImage](/powershell/module/az.compute/set-azvmsourceimage)를 사용하여 나머지 VM 구성을 만듭니다. 다음 명령을 사용하면 Windows Server 2016 VM을 만듭니다.

    ```azurepowershell
    $vmConfig = Set-AzVMOperatingSystem -VM $vmConfig `
        -Windows `
        -ComputerName "myVM" `
        -Credential $cred `
        -ProvisionVMAgent `
        -EnableAutoUpdate
    $vmConfig = Set-AzVMSourceImage -VM $vmConfig `
        -PublisherName "MicrosoftWindowsServer" `
        -Offer "WindowsServer" `
        -Skus "2016-Datacenter" `
        -Version "latest"
    ```

4. [Add-AzureRmVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface)를 사용하여 이전에 만든 네트워크 인터페이스를 연결합니다.

    ```azurepowershell
    $vmConfig = Add-AzVMNetworkInterface -VM $vmConfig -Id $nic.Id
    ```

5. [New-AzVM](/powershell/module/az.compute/new-azvm)으로 VM을 만듭니다.

    ```azurepowershell
    New-AzVM -VM $vmConfig -ResourceGroupName "myResourceGroup" -Location "centralus"
    ```

### <a name="confirm-the-ethernet-controller-is-installed-in-the-windows-vm"></a>이더넷 컨트롤러가 Windows VM에 설치되었는지 확인합니다.

Azure에서 VM을 만들면 VM에 연결하고 Windows에서 이더넷 컨트롤러가 설치되어 있는지를 확인합니다.

1. VM을 관리하기 위해 [Azure Portal](https://portal.azure.com)로 이동합니다. **가상 머신** 을 검색하여 선택합니다.

2. 가상 컴퓨터 목록에서 새 VM을 선택합니다.

3. VM 개요 페이지에서 VM의 **상태** 가 **만들기** 로 나열되는 경우 Azure에서 VM 만들기를 완료할 때까지 기다립니다. VM 만들기가 완료되면 **상태** 가 **실행 중** 으로 변경됩니다.

4. VM 개요 도구 모음에서 **연결** > **REP** > **RDP 파일 다운로드** 를 선택합니다.

5. .rdp 파일을 열고 [VM 만들기 및 네트워크 인터페이스 연결](#create-a-vm-and-attach-the-network-interface) 섹션에 입력한 자격 증명을 사용하여 VM에 로그인합니다. Azure에서 Windows VM에 연결된 적이 없는 경우 [가상 머신에 연결](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#connect-to-virtual-machine)을 참조하세요.

6. VM에 대한 원격 데스크톱 세션이 나타나면 Windows 시작 단추를 마우스 오른쪽 단추로 클릭하고 **디바이스 관리자** 를 선택합니다.

7. **디바이스 관리자** 창에서 **네트워크 어댑터** 노드를 확장합니다.

8. 다음 이미지와 같이 **Mellanox ConnectX-3 Virtual Function Ethernet Adapter** 가 나타나는지 확인합니다.

    ![Mellanox ConnectX-3 가상 함수 이더넷 어댑터, 가속화된 네트워킹을 위한 새 네트워크 어댑터, 디바이스 관리자](./media/create-vm-accelerated-networking/device-manager.png)

이제 가속화된 네트워킹을 VM에 사용할 수 있습니다.

> [!NOTE]
> Mellanox 어댑터가 시작되지 않으면 원격 데스크톱 세션에서 관리자 프롬프트를 열고 다음 명령을 입력합니다.
>
> `netsh int tcp set global rss = enabled`

## <a name="enable-accelerated-networking-on-existing-vms"></a>기존 VM에서 가속화된 네트워킹 사용

가속화된 네트워킹을 사용하지 않고 VM을 만든 경우 기존 VM에서 이 기능을 사용하도록 설정할 수 있습니다. VM이 위에 설명된 다음 필수 조건을 충족하여 가속화된 네트워킹을 지원해야 합니다.

* VM은 가속화된 네트워킹에 대해 지원되는 크기여야 함
* VM은 지원되는 Azure Gallery 이미지(및 Linux용 커널 버전)여야 함
* 가용성 집합 또는 가상 머신 확장 집합의 모든 VM은 NIC에서 가속화된 네트워킹을 사용하도록 설정하기 전에 중지하거나 할당을 취소해야 합니다.

### <a name="individual-vms-and-vms-in-an-availability-set"></a>개별 VM 및 가용성 집합의 VM

1. VM을 중지 또는 할당 취소하거나, 가용성 집합인 경우 모든 VM을 중지 또는 할당 취소합니다.

    ```azurepowershell
    Stop-AzVM -ResourceGroup "myResourceGroup" -Name "myVM"
    ```

    > [!NOTE]
    > 가용성 집합 없이 VM을 개별적으로 만들 때 가속화된 네트워킹을 사용하려면 개별 VM을 중지하거나 할당 취소해야 합니다. VM이 가용성 집합을 사용하여 만들어진 경우에는 모든 NIC에서 가속 네트워킹을 사용하도록 설정하기 전에 가용성 집합에 포함된 모든 VM을 중지하거나 할당 취소해야 합니다. 이렇게 하면 VM이 가속화된 네트워킹을 지원하는 클러스터에서 종료됩니다. 가속화된 네트워킹을 지원하는 클러스터는 가속화된 네트워킹을 사용하지 않는 NIC에서도 잘 작동하기 때문에 가속화된 네트워킹을 사용하지 않도록 설정하면 중지 또는 할당 해제 요구 사항이 필요하지 않습니다.

2. VM의 NIC에서 가속 네트워킹을 사용하도록 설정합니다.

    ```azurepowershell
    $nic = Get-AzNetworkInterface -ResourceGroupName "myResourceGroup" `
        -Name "myNic"
    
    $nic.EnableAcceleratedNetworking = $true
    
    $nic | Set-AzNetworkInterface
    ```

3. VM을 다시 시작하거나, 가용성 집합인 경우 집합에 포함된 모든 VM을 다시 시작하고 가속 네트워킹이 사용되는지 확인합니다.

    ```azurepowershell
    Start-AzVM -ResourceGroup "myResourceGroup" `
        -Name "myVM"
    ```

### <a name="virtual-machine-scale-set"></a>가상 머신 크기 집합

가상 머신 확장 집합은 약간 다르지만 동일한 워크플로를 따릅니다.

1. VM을 중지합니다.

    ```azurepowershell
    Stop-AzVmss -ResourceGroupName "myResourceGroup" `
        -VMScaleSetName "myScaleSet"
    ```

2. 네트워크 인터페이스에서 가속화된 네트워킹 속성을 업데이트합니다.

    ```azurepowershell
    $vmss = Get-AzVmss -ResourceGroupName "myResourceGroup" `
        -VMScaleSetName "myScaleSet"
    
    $vmss.VirtualMachineProfile.NetworkProfile.NetworkInterfaceConfigurations[0].EnableAcceleratedNetworking = $true
    
    Update-AzVmss -ResourceGroupName "myResourceGroup" `
        -VMScaleSetName "myScaleSet" `
        -VirtualMachineScaleSet $vmss
    ```

3. 변경 내용이 즉시 적용되도록 적용된 업데이트를 자동으로 설정합니다.

    ```azurepowershell
    $vmss.UpgradePolicy.Mode = "Automatic"
    
    Update-AzVmss -ResourceGroupName "myResourceGroup" `
        -VMScaleSetName "myScaleSet" `
        -VirtualMachineScaleSet $vmss
    ```

    > [!NOTE]
    > 확장 집합에는 자동, 롤링, 수동의 세 가지 다른 설정으로 업데이트를 적용하는 VM 업그레이드가 있습니다. 이러한 지침에서 정책은 자동으로 설정되므로 확장 집합은 다시 시작된 후 즉시 변경 내용을 설정합니다.

4. 확장 집합을 다시 시작합니다.

    ```azurepowershell
    Start-AzVmss -ResourceGroupName "myResourceGroup" `
        -VMScaleSetName "myScaleSet"
    ```

다시 시작하면 업그레이드가 완료될 때까지 기다립니다. 업그레이드가 완료되면 VF(가상 함수)가 VM 내부에 표시됩니다. 지원되는 OS 및 VM 크기를 사용하고 있는지 확인합니다.

### <a name="resizing-existing-vms-with-accelerated-networking"></a>가속화된 네트워킹을 사용하여 기존 VM 크기 조정

VM에서 가속화된 네트워킹을 사용하는 경우 가속화된 네트워킹을 지원하는 VM으로만 크기를 조정할 수 있습니다.  

가속 네트워킹을 사용하는 VM은 크기 조정 작업을 사용하여 가속 네트워킹을 지원하지 않는 VM 인스턴스로 크기를 조정할 수 없습니다. 이러한 VM 중 하나의 크기를 조정하려면 다음을 수행합니다.

1. VM을 중지하고 할당을 취소합니다. 가용성 집합 또는 확장 집합의 경우 가용성 집합 또는 확장 집합의 모든 VM을 중지하거나 할당을 취소합니다.

2. VM의 NIC에서 가속화된 네트워킹을 사용하지 않도록 설정합니다. 가용성 집합 또는 확장 집합의 경우 가용성 집합 또는 확장 집합에 있는 모든 VM의 NIC에서 가속화된 네트워킹을 사용하지 않도록 설정합니다.

3. 가속화된 네트워킹을 사용하지 않도록 설정한 후에는 VM, 가용성 집합 또는 확장 집합을 가속화된 네트워킹을 지원하지 않는 새 크기로 이동한 후 다시 시작합니다.