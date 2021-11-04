---
title: 신뢰할 수 있는 시작 VM 배포
description: 신뢰할 수 있는 시작을 사용하는 VM을 배포합니다.
author: khyewei
ms.author: khwei
ms.reviewer: cynthn
ms.service: virtual-machines
ms.subservice: trusted-launch
ms.topic: how-to
ms.date: 10/25/2021
ms.custom: template-how-to
ms.openlocfilehash: 1bf17761ed7ddba74ea62f5545f44f1c4b57c8d3
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131456531"
---
# <a name="deploy-a-vm-with-trusted-launch-enabled"></a>신뢰할 수 있는 시작이 사용하도록 설정된 VM 배포

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 유연한 확장 집합

[신뢰할 수 있는 시작](trusted-launch.md)은 [2세대](generation-2.md) VM의 보안을 개선하는 방법입니다. 신뢰할 수 있는 시작은 vTPM 및 보안 부팅 등의 인프라 기술을 결합하여 고급 및 지속적인 공격 기법을 방지합니다.

## <a name="prerequisites"></a>사전 요구 사항 

- 구독이 아직 등록되지 않은 경우 Azure Security Center 등록해야 [합니다.](https://azure.microsoft.com/services/security-center/?&ef_id=CjwKCAjwwsmLBhACEiwANq-tXHeKhV--teH6kIijnBTmP-PgktfvGr5zW9TAx00SR7xsGUc3sTj5sBoCkEoQAvD_BwE:G:s&OCID=AID2200277_SEM_CjwKCAjwwsmLBhACEiwANq-tXHeKhV--teH6kIijnBTmP-PgktfvGr5zW9TAx00SR7xsGUc3sTj5sBoCkEoQAvD_BwE:G:s&gclid=CjwKCAjwwsmLBhACEiwANq-tXHeKhV--teH6kIijnBTmP-PgktfvGr5zW9TAx00SR7xsGUc3sTj5sBoCkEoQAvD_BwE#overview) ASC(Azure Security Center)에는 다양한 Azure 및 하이브리드 리소스에 매우 유용한 인사이트를 제공하는 무료 계층이 있습니다. 신뢰할 수 있는 시작은 ASC를 활용하여 VM 상태와 관련된 여러 권장 사항을 제시합니다. 

- 구독에 Azure 정책 이니셔티브를 할당합니다. 이러한 정책 이니셔티브는 구독당 한 번만 할당해야 합니다. 그러면 지원되는 모든 VM에 필요한 모든 확장이 자동으로 설치됩니다. 
    - 신뢰할 수 있는 시작이 설정된 VM에서 게스트 증명을 사용하도록 필수 구성 조건 구성 

    - 가상 머신에 Azure Monitor 및 Azure Security Agent를 자동으로 설치하도록 머신 구성 

 
## <a name="deploy-a-trusted-vm"></a>신뢰할 수 있는 VM 배포
신뢰할 수 있는 시작이 사용하도록 설정된 가상 머신을 만듭니다. 아래 옵션을 선택합니다.

### <a name="portal"></a>[포털](#tab/portal)

1. Azure [Portal](https://portal.azure.com)에 로그인합니다.
2. **Virtual Machines** 를 검색합니다.
3. **서비스** 에서 **가상 머신** 을 선택합니다.
4. **가상 머신** 페이지에서 **추가** 를 선택한 다음, **가상 머신** 을 선택합니다.
5. **프로젝트 세부 정보** 에서 올바른 구독이 선택되어 있는지 확인합니다.
6. **리소스 그룹** 에서 **새로 만들기** 를 선택하고 리소스 그룹의 이름을 입력하거나 드롭다운 목록에서 기존 리소스 그룹을 선택합니다.
7. **인스턴스 세부 정보** 에서 가상 머신 이름에 대한 이름을 입력하고 [신뢰할 수 있는 시작](trusted-launch.md#limitations)을 지원하는 지역을 선택합니다.
1. **보안 유형에서** **신뢰할 수 있는 가상 머신 시작 을** 선택합니다. 이렇게 하면 **보안 부팅** 및 **vTPM이라는** 두 가지 옵션이 더 표시됩니다. 배포에 적합한 옵션을 선택합니다.
    :::image type="content" source="media/trusted-launch/security.png" alt-text="신뢰할 수 있는 시작에 대한 옵션을 보여주는 스크린샷":::
3. **이미지** 아래에서 신뢰할 수 있는 **시작 과 호환되는 권장 Gen 2 이미지에서 이미지를** 선택합니다. 목록은 [신뢰할 수 있는 시작 을 지원하는 이미지를](trusted-launch.md#limitations)참조하세요. 
   > [!TIP]
   > 드롭다운에서 원하는 이미지의 Gen 2 버전이 표시되지 않으면 모든 **이미지 보기를** 선택한 **다음, 보안 유형** 필터를 신뢰할 수 있는 **시작으로** 변경합니다.
13. 신뢰할 수 있는 시작을 지원하는 VM 크기를 선택합니다. [지원되는 크기](trusted-launch.md#limitations) 목록을 참조하세요.
14. **관리자 계정** 정보를 입력한 다음, **인바운드 포트 규칙** 을 입력합니다.
15. 페이지의 아래쪽에서 **검토 + 생성** 을 선택합니다.
16. **가상 머신 만들기** 페이지에서 배포하려는 VM의 세부 정보를 볼 수 있습니다. 유효성 검사가 통과된 것으로 표시되면 **만들기를** 선택합니다.

    :::image type="content" source="media/trusted-launch/validation.png" alt-text="신뢰할 수 있는 시작 옵션이 포함되어 있는 유효성 검사 페이지의 스크린샷":::


VM 배포에는 몇 분 정도 걸립니다.

### <a name="cli"></a>[CLI](#tab/cli)

최신 버전의 Azure CLI 실행 중인지 확인합니다. 

`az login`를 사용하여 Azure에 로그인합니다.  

```azurecli-interactive
az login 
```

신뢰할 수 있는 시작으로 가상 머신을 만듭니다. 

```azurecli-interactive
az group create -n myresourceGroup -l eastus 
az vm create \ 
   --resource-group myResourceGroup \ 
   --name myVM \ 
   --image UbuntuLTS \ 
   --admin-username azureuser \ 
   --generate-ssh-keys \ 
   --SecurityType trustedLaunch \ 
   --EnableSecureBoot true \  
   --EnableVtpm true \
```
 
기존 VM의 경우 보안 부팅 및 vTPM 설정을 사용하거나 사용하지 않도록 설정할 수 있습니다. 보안 부팅 및 vTPM 설정을 사용하여 가상 머신을 업데이트하면 자동 다시 부팅이 트리거됩니다.

```azurecli-interactive
az vm update \  
   --resource-group myResourceGroup \ 
   --name myVM \ 
   --EnableSecureBoot \  
   --EnableVtpm 
```  

### <a name="powershell"></a>[PowerShell](#tab/powershell)


신뢰할 수 있는 시작으로 VM을 프로비전하려면 먼저 `TrustedLaunch` cmdlet을 사용하여 를 사용하도록 설정해야 `Set-AzVmSecurityType` 합니다. 그런 다음, Set-AzVmUefi cmdlet을 사용하여 vTPM 및 SecureBoot 구성을 설정할 수 있습니다. 아래 조각은 빠른 시작으로 사용합니다. 이 예제의 값을 사용자 고유의 값으로 바꾸어야 합니다. 

```azurepowershell-interactive
$resourceGroup = "myResourceGroup"
$location = "West US"
$vmName = "myTrustedVM"
$vmSize = Standard_B2s
$publisher = "MicrosoftWindowsServer"
$offer = "WindowsServer"
$sku = "2019-Datacenter"
$version = latest
$cred = Get-Credential `
   -Message "Enter a username and password for the virtual machine."

$vm = New-AzVMConfig -VMName $vmName -VMSize $vmSize 

$vm = Set-AzVMOperatingSystem `
   -VM $vm -Windows `
   -ComputerName $vmName `
   -Credential $cred `
   -ProvisionVMAgent `
   -EnableAutoUpdate 

$vm = Add-AzVMNetworkInterface -VM $vm `
   -Id $NIC.Id 

$vm = Set-AzVMSourceImage -VM $vm `
   -PublisherName $publisher `
   -Offer $offer `
   -Skus $sku `
   -Version $version 

$vm = Set-AzVMOSDisk -VM $vm `
   -StorageAccountType "StandardSSD_LRS" `
   -CreateOption "FromImage" 

$vm = Set-AzVmSecurityType -VM $vm `
   -SecurityType "TrustedLaunch" 

$vm = Set-AzVmUefi -VM $vm `
   -EnableVtpm $true `
   -EnableSecureBoot $true 

New-AzVM -ResourceGroupName $resourceGroup -Location $location -VM $vm 
```
 


### <a name="template"></a>[템플릿](#tab/template)

빠른 시작 템플릿을 사용하여 신뢰할 수 있는 시작 VM을 배포할 수 있습니다.

**Linux**

[![Azure에 배포](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.svg?sanitize=true)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.compute%2Fvm-trustedlaunch-linux%2Fazuredeploy.json/createUIDefinitionUri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.compute%2Fvm-trustedlaunch-linux%2FcreateUiDefinition.json)

**Windows**

[![Azure에 배포](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.svg?sanitize=true)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.compute%2Fvm-trustedlaunch-windows%2Fazuredeploy.json/createUIDefinitionUri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.compute%2Fvm-trustedlaunch-windows%2FcreateUiDefinition.json)

---




## <a name="verify-or-update-your-settings"></a>설정 확인 또는 업데이트

포털에서 VM에 대한 **개요** 페이지를 방문하여 기존 VM에 대한 신뢰할 수 있는 시작 구성을 볼 수   있습니다. **속성** 탭에는 신뢰할 수 있는 시작 기능의 상태가 표시됩니다.

:::image type="content" source="media/trusted-launch/overview-properties.png" alt-text="VM의 신뢰할 수 있는 시작 속성 스크린샷.":::

신뢰할 수 있는 시작 구성을 변경하려면 **** 왼쪽 메뉴에서    **설정** 섹션 아래의 구성을   선택합니다. 신뢰할 수 있는 시작보안 유형 섹션에서 보안 부팅 및 vTPM을 사용하거나 사용하지 않도록 설정할 수 있습니다. 완료되면 페이지 맨 위에서 저장을 선택합니다. 

:::image type="content" source="media/trusted-launch/update.png" alt-text="신뢰할 수 있는 시작 설정을 변경하는 확인란을 보여주는 스크린샷":::

VM이 실행 중인 경우 VM이 다시 시작된다는 메시지가 표시됩니다.  **예를** 선택한   다음, VM이 다시 시작될 때까지 기다렸다가 변경 내용이 적용됩니다. 


## <a name="next-steps"></a>다음 단계

[신뢰할 수 있는 시작](trusted-launch.md) 및 [2세대](generation-2.md) VM에 대해 자세히 알아보세요.
