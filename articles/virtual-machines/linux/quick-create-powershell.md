---
title: 빠른 시작 - Azure PowerShell을 사용하여 Linux VM 만들기
description: 이 빠른 시작에서는 Azure PowerShell을 사용하여 Linux 가상 머신을 만드는 방법을 배웁니다.
author: cynthn
ms.service: virtual-machines
ms.collection: linux
ms.topic: quickstart
ms.workload: infrastructure
ms.date: 07/31/2020
ms.author: cynthn
ms.custom: mvc, devx-track-azurepowershell
ms.openlocfilehash: f195a741ed11fc8f7ded421593417bf41a3b9d84
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122692822"
---
# <a name="quickstart-create-a-linux-virtual-machine-in-azure-with-powershell"></a>빠른 시작: PowerShell을 사용하여 Azure에서 Linux 가상 머신 만들기

**적용 대상:** :heavy_check_mark: Linux VM 

PowerShell 명령줄 또는 스크립트에서 Azure 리소스를 만들고 관리하는 데 Azure PowerShell 모듈이 사용됩니다. 이 빠른 시작에서는 Azure PowerShell 모듈을 사용하여 Azure에서 Linux VM(가상 머신)을 배포하는 방법을 보여줍니다. 이 빠른 시작에서는 Canonical의 Ubuntu 18.04 LTS 마켓플레이스 이미지를 사용합니다. VM 작동을 확인하기 위해 VM에 대해 SSH를 수행하고 NGINX 웹 서버도 설치합니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="launch-azure-cloud-shell"></a>Azure Cloud Shell 시작

Azure Cloud Shell은 이 항목의 단계를 실행하는 데 무료로 사용할 수 있는 대화형 셸입니다. 공용 Azure 도구가 사전 설치되어 계정에서 사용하도록 구성되어 있습니다. 

Cloud Shell을 열려면 코드 블록의 오른쪽 위 모서리에 있는 **사용해 보세요** 를 선택하기만 하면 됩니다. **복사** 를 선택하여 코드 블록을 복사하여 Cloud Shell에 붙여넣고, Enter 키를 눌러 실행합니다.

## <a name="create-ssh-key-pair"></a>SSH 키 쌍 만들기

[ssh-keygen](https://www.ssh.com/ssh/keygen/)을 사용하여 SSH 키 쌍을 만듭니다. SSH 키 쌍이 이미 있는 경우 이 단계를 건너뜁니다.


```azurepowershell-interactive
ssh-keygen -t rsa -b 4096
```

키 쌍의 파일 이름을 입력하라는 메시지가 표시됩니다. 또는 **Enter** 키를 눌러 기본 위치 `/home/<username>/.ssh/id_rsa`를 사용할 수 있습니다. 원하는 경우 키의 암호를 만들 수도 있습니다.

SSH 키 쌍을 만드는 방법에 대한 자세한 내용은 [Windows에 SSH 키를 사용하는 방법](ssh-from-windows.md)을 참조하세요.

Cloud Shell을 사용하여 SSH 키 쌍을 만드는 경우 [Cloud Shell에서 자동으로 만들어지는 스토리지 계정](../../cloud-shell/persisting-shell-storage.md)에 저장됩니다. 키를 검색할 때까지 스토리지 계정을 삭제하거나 스토리지 계정의 파일 공유를 삭제하지 마세요. VM에 대한 액세스를 잃게 됩니다. 

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)을 사용하여 Azure 리소스 그룹을 만듭니다. 리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다.

```azurepowershell-interactive
New-AzResourceGroup -Name "myResourceGroup" -Location "EastUS"
```

## <a name="create-virtual-network-resources"></a>가상 네트워크 리소스 만들기

가상 네트워크, 서브넷 및 공용 IP 주소를 만듭니다. 이러한 리소스는 VM에 네트워크 연결을 제공하고 인터넷에 연결하는 데 사용됩니다.

```azurepowershell-interactive
# Create a subnet configuration
$subnetConfig = New-AzVirtualNetworkSubnetConfig `
  -Name "mySubnet" `
  -AddressPrefix 192.168.1.0/24

# Create a virtual network
$vnet = New-AzVirtualNetwork `
  -ResourceGroupName "myResourceGroup" `
  -Location "EastUS" `
  -Name "myVNET" `
  -AddressPrefix 192.168.0.0/16 `
  -Subnet $subnetConfig

# Create a public IP address and specify a DNS name
$pip = New-AzPublicIpAddress `
  -ResourceGroupName "myResourceGroup" `
  -Location "EastUS" `
  -AllocationMethod Static `
  -IdleTimeoutInMinutes 4 `
  -Name "mypublicdns$(Get-Random)"
```

Azure 네트워크 보안 그룹 및 트래픽 규칙을 만듭니다. 네트워크 보안 그룹은 인바운드 및 아웃바운드 규칙을 사용하여 VM을 보호합니다. 다음 예제에서는 SSH 연결을 허용하는 TCP 포트 22에 대한 인바운드 규칙이 만들어집니다. 들어오는 웹 트래픽을 허용하기 위해 TCP 포트 80에 대한 인바운드 규칙도 생성됩니다.

```azurepowershell-interactive
# Create an inbound network security group rule for port 22
$nsgRuleSSH = New-AzNetworkSecurityRuleConfig `
  -Name "myNetworkSecurityGroupRuleSSH"  `
  -Protocol "Tcp" `
  -Direction "Inbound" `
  -Priority 1000 `
  -SourceAddressPrefix * `
  -SourcePortRange * `
  -DestinationAddressPrefix * `
  -DestinationPortRange 22 `
  -Access "Allow"

# Create an inbound network security group rule for port 80
$nsgRuleWeb = New-AzNetworkSecurityRuleConfig `
  -Name "myNetworkSecurityGroupRuleWWW"  `
  -Protocol "Tcp" `
  -Direction "Inbound" `
  -Priority 1001 `
  -SourceAddressPrefix * `
  -SourcePortRange * `
  -DestinationAddressPrefix * `
  -DestinationPortRange 80 `
  -Access "Allow"

# Create a network security group
$nsg = New-AzNetworkSecurityGroup `
  -ResourceGroupName "myResourceGroup" `
  -Location "EastUS" `
  -Name "myNetworkSecurityGroup" `
  -SecurityRules $nsgRuleSSH,$nsgRuleWeb
```

[New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface)를 사용하여 가상 NIC(네트워크 인터페이스 카드)를 만듭니다. 가상 NIC는 서브넷, 네트워크 보안 그룹 및 공용 IP 주소에 VM을 연결합니다.

```azurepowershell-interactive
# Create a virtual network card and associate with public IP address and NSG
$nic = New-AzNetworkInterface `
  -Name "myNic" `
  -ResourceGroupName "myResourceGroup" `
  -Location "EastUS" `
  -SubnetId $vnet.Subnets[0].Id `
  -PublicIpAddressId $pip.Id `
  -NetworkSecurityGroupId $nsg.Id
```

## <a name="create-a-virtual-machine"></a>가상 머신 만들기

PowerShell에서 VM을 만들려면 사용할 이미지, 크기 및 인증 옵션 등의 설정이 있는 구성을 만듭니다. 그런 다음, 이 구성을 사용하여 VM을 빌드합니다.

SSH 자격 증명, OS 정보 및 VM 크기를 정의합니다. 이 예제에서는 SSH 키가 `~/.ssh/id_rsa.pub`에 저장됩니다. 

```azurepowershell-interactive
# Define a credential object
$securePassword = ConvertTo-SecureString ' ' -AsPlainText -Force
$cred = New-Object System.Management.Automation.PSCredential ("azureuser", $securePassword)

# Create a virtual machine configuration
$vmConfig = New-AzVMConfig `
  -VMName "myVM" `
  -VMSize "Standard_D1" | `
Set-AzVMOperatingSystem `
  -Linux `
  -ComputerName "myVM" `
  -Credential $cred `
  -DisablePasswordAuthentication | `
Set-AzVMSourceImage `
  -PublisherName "Canonical" `
  -Offer "UbuntuServer" `
  -Skus "18.04-LTS" `
  -Version "latest" | `
Add-AzVMNetworkInterface `
  -Id $nic.Id

# Configure the SSH key
$sshPublicKey = cat ~/.ssh/id_rsa.pub
Add-AzVMSshPublicKey `
  -VM $vmconfig `
  -KeyData $sshPublicKey `
  -Path "/home/azureuser/.ssh/authorized_keys"
```

이제 [New-AzVM](/powershell/module/az.compute/new-azvm)을 사용하여 만들도록 이전 구성 정의를 결합합니다.

```azurepowershell-interactive
New-AzVM `
  -ResourceGroupName "myResourceGroup" `
  -Location eastus -VM $vmConfig
```

VM 배포에는 몇 분 정도 걸립니다. 배포가 완료되면 다음 섹션으로 이동합니다.

[!INCLUDE [ephemeral-ip-note.md](../../../includes/ephemeral-ip-note.md)]

## <a name="connect-to-the-vm"></a>VM에 연결

공용 IP 주소를 사용하여 VM과 SSH 연결을 만듭니다. VM의 공용 IP 주소를 보려면 [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) cmdlet을 사용합니다.

```azurepowershell-interactive
Get-AzPublicIpAddress -ResourceGroupName "myResourceGroup" | Select "IpAddress"
```

SSH 키 쌍을 만드는 데 사용한 것과 동일한 셸을 사용하여 다음 명령을 셸에 붙여넣어 SSH 세션을 만듭니다. *10.111.12.123* 을 VM의 IP 주소로 바꿉니다.

```bash
ssh azureuser@10.111.12.123
```

로그인 사용자 이름을 묻는 메시지가 표시되면 *azureuser* 를 입력합니다. SSH 키로 암호가 사용되는 경우 메시지가 표시되면 입력해야 합니다.


## <a name="install-nginx"></a>NGINX 설치

실제로 작동 중인 VM을 보려면 NGINX 웹 서버를 설치합니다. SSH 세션에서 패키지 소스를 업데이트한 다음, 최신 NGINX 패키지를 설치합니다.

```bash
sudo apt-get -y update
sudo apt-get -y install nginx
```

완료되면 `exit`를 입력하여 SSH 세션을 종료합니다.


## <a name="view-the-web-server-in-action"></a>실제로 작동 중인 웹 서버 보기

원하는 웹 브라우저를 사용하여 기본 NGINX 시작 페이지를 봅니다. VM의 공용 IP 주소를 웹 주소로 입력합니다. 공용 IP 주소는 VM 개요 페이지나, 앞서 사용한 SSH 구성 문자열 부분에 있습니다.

![NGINX 기본 시작 페이지](./media/quick-create-cli/nginix-welcome-page.png)

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우 [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) cmdlet을 사용하여 리소스 그룹, VM 및 모든 관련 리소스를 제거할 수 있습니다.

```azurepowershell-interactive
Remove-AzResourceGroup -Name "myResourceGroup"
```

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 간단한 가상 머신을 배포하고, 네트워크 보안 그룹 및 규칙을 만들고 기본 웹 서버를 설치했습니다. Azure 가상 머신에 대한 자세한 내용을 알아보려면 Linux VM의 자습서를 계속 진행합니다.

> [!div class="nextstepaction"]
> [Azure Linux 가상 머신 자습서](./tutorial-manage-vm.md)
