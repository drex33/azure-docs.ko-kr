---
title: 자습서 - Azure에서 Windows VM에 애플리케이션 설치
description: 이 자습서에서는 사용자 지정 스크립트 확장을 사용하여 스크립트를 실행하고 Azure에서 Windows 가상 머신에 애플리케이션을 배포하는 방법을 배웁니다.
author: cynthn
ms.service: virtual-machines
ms.collection: windows
ms.topic: tutorial
ms.workload: infrastructure
ms.date: 11/29/2018
ms.author: cynthn
ms.custom: mvc, devx-track-azurepowershell
ms.openlocfilehash: 1ed110618b81c5aa074b5003a2fbbac67073a4dc
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122692307"
---
# <a name="tutorial---deploy-applications-to-a-windows-virtual-machine-in-azure-with-the-custom-script-extension"></a>자습서 - 사용자 지정 스크립트 확장을 사용하여 Azure에서 Windows 가상 머신에 애플리케이션 배포
**적용 대상:** :heavy_check_mark: 창 :heavy_check_mark: 유연한 확장 집합 :heavy_check_mark: 균일 확장 집합

신속하고 일관된 방식으로 VM(가상 머신)을 구성하려면 [Windows용 사용자 지정 스크립트 확장](../extensions/custom-script-windows.md)을 사용합니다. 이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 사용자 지정 스크립트 확장을 사용하여 IIS 설치
> * 사용자 지정 스크립트 확장을 사용하는 VM 만들기
> * 확장이 적용된 후 실행 중인 IIS 사이트 보기

## <a name="launch-azure-cloud-shell"></a>Azure Cloud Shell 시작

Azure Cloud Shell은 이 항목의 단계를 실행하는 데 무료로 사용할 수 있는 대화형 셸입니다. 공용 Azure 도구가 사전 설치되어 계정에서 사용하도록 구성되어 있습니다. 

Cloud Shell을 열려면 코드 블록의 오른쪽 위 모서리에 있는 **사용해 보세요** 를 선택하기만 하면 됩니다. 또한 [https://shell.azure.com/powershell](https://shell.azure.com/powershell)로 이동하여 별도의 브라우저 탭에서 Cloud Shell을 시작할 수도 있습니다. **복사** 를 선택하여 코드 블록을 복사하여 Cloud Shell에 붙여넣고, Enter 키를 눌러 실행합니다.

## <a name="custom-script-extension-overview"></a>사용자 지정 스크립트 확장 개요
사용자 지정 스크립트 확장은 Azure VM에서 스크립트를 다운로드하고 실행합니다. 이 확장은 배포 후 구성, 소프트웨어 설치 또는 기타 구성/관리 작업에 유용합니다. 스크립트를 Azure Storage 또는 GitHub에서 다운로드하거나 확장 런타임에서 Azure Portal에 제공할 수 있습니다.

사용자 지정 스크립트 확장은 Azure Resource Manager 템플릿과 통합되고, Azure CLI, PowerShell, Azure Portal 또는 Azure Virtual Machine REST API를 사용하여 실행할 수도 있습니다.

Windows VM 및 Linux VM 둘 다에 사용자 지정 스크립트 확장을 사용할 수 있습니다.


## <a name="create-virtual-machine"></a>가상 머신 만들기
[Get-Credential](/powershell/module/microsoft.powershell.security/get-credential)을 사용하여 VM의 관리자 사용자 이름과 암호를 설정합니다.

```azurepowershell-interactive
$cred = Get-Credential
```

이제 [New-AzVM](/powershell/module/az.compute/new-azvm)을 사용하여 VM을 만들 수 있습니다. 다음 예제에서는 *EastUS* 위치에 *myVM* 이라는 VM을 만듭니다. 아직 없는 경우 *myResourceGroupAutomate* 리소스 그룹 및 지원 네트워크 리소스가 만들어집니다. 웹 트래픽을 허용하기 위해 cmdlet에서 *80* 포트도 엽니다.

```azurepowershell-interactive
New-AzVm `
    -ResourceGroupName "myResourceGroupAutomate" `
    -Name "myVM" `
    -Location "East US" `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress" `
    -OpenPorts 80 `
    -Credential $cred
```

리소스 및 VM을 만드는 데 몇 분 정도 걸립니다.


## <a name="automate-iis-install"></a>IIS 설치 자동화
[Set-AzVMExtension](/powershell/module/az.compute/set-azvmextension)을 사용하여 사용자 지정 스크립트 확장을 설치합니다. 확장은 `powershell Add-WindowsFeature Web-Server`를 실행하여 IIS 웹 서버를 설치한 다음 *Default.htm* 페이지를 업데이트하여 VM의 호스트 이름을 표시합니다.

```azurepowershell-interactive
Set-AzVMExtension -ResourceGroupName "myResourceGroupAutomate" `
    -ExtensionName "IIS" `
    -VMName "myVM" `
    -Location "EastUS" `
    -Publisher Microsoft.Compute `
    -ExtensionType CustomScriptExtension `
    -TypeHandlerVersion 1.8 `
    -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}'
```


## <a name="test-web-site"></a>웹 사이트 테스트
[Get-AzPublicIPAddress](/powershell/module/az.network/get-azpublicipaddress)를 사용하여 부하 분산 장치의 공용 IP 주소를 가져옵니다. 다음 예제에서는 앞서 만든 *myPublicIPAddress* 의 IP 주소를 가져옵니다.

```azurepowershell-interactive
Get-AzPublicIPAddress `
    -ResourceGroupName "myResourceGroupAutomate" `
    -Name "myPublicIPAddress" | select IpAddress
```

그런 다음 웹 브라우저에 공용 IP 주소를 입력할 수 있습니다. 다음 예제와 같이 부하 분산 장치가 트래픽을 분산한 VM의 호스트 이름을 포함하여 웹 사이트가 표시됩니다.

![실행 중인 IIS 웹 사이트](./media/tutorial-automate-vm-deployment/running-iis-website.png)


## <a name="next-steps"></a>다음 단계

이 자습서에서는 VM에서 IIS 설치를 자동화합니다. 구체적으로 다음 작업 방법을 알아보았습니다.

> [!div class="checklist"]
> * 사용자 지정 스크립트 확장을 사용하여 IIS 설치
> * 사용자 지정 스크립트 확장을 사용하는 VM 만들기
> * 확장이 적용된 후 실행 중인 IIS 사이트 보기

사용자 지정 VM 이미지를 만드는 방법에 대해 알아보려면 다음 자습서로 이동합니다.

> [!div class="nextstepaction"]
> [사용자 지정 VM 이미지 만들기](./tutorial-custom-images.md)
