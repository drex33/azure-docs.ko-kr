---
title: Azure의 템플릿에서 Windows VM 만들기 | Microsoft Docs
description: Resource Manager 템플릿 및 PowerShell을 사용하여 새 Windows VM을 쉽게 만들 수 있습니다.
author: cynthn
ms.service: virtual-machines
ms.topic: how-to
ms.date: 03/22/2019
ms.author: cynthn
ms.custom: H1Hack27Feb2017, devx-track-azurepowershell
ms.openlocfilehash: 1be03bee5005cbcc4b2eec3d469631d504d3c504
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122690156"
---
# <a name="create-a-windows-virtual-machine-from-a-resource-manager-template"></a>Resource Manager 템플릿을 사용하여 Windows 가상 머신 만들기

**적용 대상:** :heavy_check_mark: Windows VM 

Azure Cloud shell의 Azure Resource Manager 템플릿과 Azure PowerShell을 사용하여 Windows 가상 머신을 만드는 방법에 대해 알아봅니다. 이 문서에 사용된 템플릿은 단일 서브넷을 사용하는 새 가상 네트워크에서 Windows Server를 실행하는 단일 가상 머신을 배포합니다. Linux 가상 머신을 만드는 방법에 대한 자세한 내용은 [Azure Resource Manager 템플릿을 사용하여 Linux 가상 머신을 만드는 방법](../linux/create-ssh-secured-vm-from-template.md)을 참조하세요.

하나의 대안은 Azure Portal에서 템플릿을 배포하는 것입니다. 포털에서 템플릿을 열려면, **Azure에 배포** 단추를 선택합니다.

[![Azure에 배포](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.compute%2Fvm-simple-windows%2Fazuredeploy.json)

## <a name="create-a-virtual-machine"></a>가상 머신 만들기

Azure 가상 컴퓨터를 만드는 데는 일반적으로 다음 두 단계가 포함됩니다.

- 리소스 그룹을 생성합니다. Azure 리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다. 가상 머신보다 먼저 리소스 그룹을 만들어야 합니다.
- 가상 머신을 만듭니다.

다음 예제에서는 [Azure 빠른 시작 템플릿](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/quickstarts/microsoft.compute/vm-simple-windows/azuredeploy.json)에서 VM을 만드는 것을 보여 줍니다. 다음은 템플릿의 복사본입니다.

[!code-json[create-windows-vm](~/quickstart-templates/quickstarts/microsoft.compute/vm-simple-windows/azuredeploy.json)]

PowerShell 스크립트를 실행하려면 **사용해 보세요** 를 선택하여 Azure Cloud Shell을 엽니다. 스크립트를 붙여넣으려면 셸을 마우스 오른쪽 단추로 클릭하고 **붙여넣기** 를 선택합니다.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$adminUsername = Read-Host -Prompt "Enter the administrator username"
$adminPassword = Read-Host -Prompt "Enter the administrator password" -AsSecureString
$dnsLabelPrefix = Read-Host -Prompt "Enter an unique DNS name for the public IP"

New-AzResourceGroup -Name $resourceGroupName -Location "$location"
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/quickstarts/microsoft.compute/vm-simple-windows/azuredeploy.json" `
    -adminUsername $adminUsername `
    -adminPassword $adminPassword `
    -dnsLabelPrefix $dnsLabelPrefix

 (Get-AzVm -ResourceGroupName $resourceGroupName).name

```

Azure Cloud Shell 대신에 PowerShell을 로컬에 설치하고 사용하도록 선택하는 경우, 이 자습서에는 Azure PowerShell 모듈이 필요합니다. `Get-Module -ListAvailable Az`을 실행하여 버전을 찾습니다. 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-az-ps)를 참조하세요. 또한 PowerShell을 로컬로 실행하는 경우 `Connect-AzAccount`를 실행하여 Azure와 연결해야 합니다.

이전 예제에서는 GitHub에 저장된 템플릿을 지정했습니다. 또한 템플릿을 다운로드하거나 만들고 `--template-file` 매개 변수로 로컬 경로를 지정할 수도 있습니다.

다음은 몇 가지 추가 리소스입니다.

- Resource Manager 템플릿을 개발하는 방법을 알아보려면 [Azure Resource Manager 설명서](../../azure-resource-manager/index.yml)를 참조하세요.
- Azure 가상 컴퓨터 스키마를 보려면 [Azure 템플릿 참조](/azure/templates/microsoft.compute/allversions)를 참조하세요.
- 더 많은 가상 머신 템플릿 샘플을 보려면, [Azure 빠른 시작 템플릿](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Compute&pageNumber=1&sort=Popular)을 참조하세요.

## <a name="connect-to-the-virtual-machine"></a>가상 머신에 연결

이전 스크립트의 마지막 PowerShell 명령은 가상 머신 이름을 표시합니다. 가상 머신에 연결하려면 [Windows를 실행하는 Azure 가상 머신에 연결하고 로그온하는 방법](./connect-logon.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

- 배포에 문제가 있는 경우 [Azure Resource Manager를 사용한 일반적인 Azure 배포 오류 해결](../../azure-resource-manager/templates/common-deployment-errors.md)을 살펴봅니다.
- [Azure PowerShell 모듈을 사용하여 Windows VM 만들기 및 관리](tutorial-manage-vm.md)에서 가상 머신을 만들고 관리하는 방법을 알아봅니다.

템플릿 만들기에 대해 자세히 알아보려면 배포한 리소스 종류의 JSON 구문 및 속성을 확인하세요.

- [Microsoft.Network/publicIPAddresses](/azure/templates/microsoft.network/publicipaddresses)
- [Microsoft.Network/virtualNetworks](/azure/templates/microsoft.network/virtualnetworks)
- [Microsoft.Network/networkInterfaces](/azure/templates/microsoft.network/networkinterfaces)
- [Microsoft.Compute/virtualMachines](/azure/templates/microsoft.compute/virtualmachines)
