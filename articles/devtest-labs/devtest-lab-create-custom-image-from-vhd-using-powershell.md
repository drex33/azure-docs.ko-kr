---
title: Azure PowerShell 사용하여 VHD 파일에서 사용자 지정 이미지 만들기
description: PowerShell을 사용하여 VHD 파일에서 Azure DevTest Labs 사용자 지정 이미지 만들기를 자동화합니다.
ms.topic: how-to
ms.date: 10/24/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 2185309194b04d1b76ca84daea19e92e7c017463
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131469056"
---
# <a name="create-a-custom-image-from-a-vhd-file-with-powershell"></a>PowerShell을 사용하여 VHD 파일에서 사용자 지정 이미지 만들기

[!INCLUDE [devtest-lab-create-custom-image-from-vhd-selector](../../includes/devtest-lab-create-custom-image-from-vhd-selector.md)]

[!INCLUDE [devtest-lab-custom-image-definition](../../includes/devtest-lab-custom-image-definition.md)]

[!INCLUDE [devtest-lab-upload-vhd-options](../../includes/devtest-lab-upload-vhd-options.md)]

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="powershell-steps"></a>PowerShell 단계

다음 단계에서는 Azure PowerShell 사용하여 VHD 파일에서 사용자 지정 이미지를 만드는 단계를 안내합니다.

1. PowerShell 명령 프롬프트에서 **커넥트-AzAccount** cmdlet을 사용하여 Azure 계정에 로그인합니다.

   ```powershell
   Connect-AzAccount
   ```

1. **Select-AzSubscription** cmdlet을 통해 Azure 구독을 선택합니다. \<subscription ID>를 구독 ID GUID로 대체합니다.

   ```powershell
   $subscriptionId = '<subscription ID>'
   Select-AzSubscription -SubscriptionId $subscriptionId
   ```

1. **Get-AzResource** cmdlet을 호출하여 랩 객체를 가져옵니다. 및 \<lab resource group name> \<lab name> 자리 표시자를 고유한 리소스 그룹 및 랩 이름으로 대체합니다.

   ```powershell
   $labRg = '<lab resource group name>'
   $labName = '<lab name>'
   $lab = Get-AzResource -ResourceId ('/subscriptions/' + $subscriptionId + '/resourceGroups/' + $labRg + '/providers/Microsoft.DevTestLab/labs/' + $labName)
   ```

1. **$vhdUri** 변수의 자리 표시자를 업로드된 VHD 파일의 URI로 바꿉다. Azure Portal 랩의 스토리지 계정에 있는 해당 Blob 페이지에서 VHD 파일의 URI를 얻을 수 있습니다. 예를 들어 VHD URI는 `https://acontosolab1234.blob.core.windows.net/uploads/myvhd.vhd` 입니다.

   ```powershell
   $vhdUri = '<VHD URI>'
   ```

1. **New-AzResourceGroupDeployment** cmdlet을 사용하여 사용자 지정 이미지를 만듭니다. 및 \<custom image name> \<custom image description> 자리 표시자를 원하는 이름 및 설명으로 대체합니다.

   ```powershell
   $customImageName = '<custom image name>'
   $customImageDescription = '<custom image description>'

   $parameters = @{existingLabName="$($lab.Name)"; existingVhdUri=$vhdUri; imageOsType='windows'; isVhdSysPrepped=$false; imageName=$customImageName; imageDescription=$customImageDescription}

   New-AzResourceGroupDeployment -ResourceGroupName $lab.ResourceGroupName -Name CreateCustomImage -TemplateUri 'https://raw.githubusercontent.com/Azure/azure-devtestlab/master/samples/DevTestLabs/QuickStartTemplates/201-dtl-create-customimage-from-vhd/azuredeploy.json' -TemplateParameterObject $parameters
   ```

## <a name="complete-powershell-script"></a>전체 PowerShell 스크립트

위의 단계를 결합하면 VHD 파일에서 사용자 지정 이미지를 만드는 다음 PowerShell 스크립트가 생성됩니다. 스크립트를 사용하려면 다음 자리 표시자를 사용자 고유의 값으로 대체합니다.

- \<subscription ID>
- \<lab resource group name>
- \<lab name>
- \<VHD URI>
- \<custom image name>
- \<custom image description>

```powershell
# Log in to your Azure account.
Connect-AzAccount

# Select the desired Azure subscription.
$subscriptionId = '<subscription ID>'
Select-AzSubscription -SubscriptionId $subscriptionId

# Get the lab object.
$labRg = '<lab resource group name>'
$labName = '<lab name>'
$lab = Get-AzResource -ResourceId ('/subscriptions/' + $subscriptionId + '/resourceGroups/' + $labRg + '/providers/Microsoft.DevTestLab/labs/' + $labName)

# Set the URI of the VHD file.
$vhdUri = '<VHD URI>'

# Set the custom image name and description values.
$customImageName = '<custom image name>'
$customImageDescription = '<custom image description>'

# Set up the parameters object.
$parameters = @{existingLabName="$($lab.Name)"; existingVhdUri=$vhdUri; imageOsType='windows'; isVhdSysPrepped=$false; imageName=$customImageName; imageDescription=$customImageDescription}

# Create the custom image.
New-AzResourceGroupDeployment -ResourceGroupName $lab.ResourceGroupName -Name CreateCustomImage -TemplateUri 'https://raw.githubusercontent.com/Azure/azure-devtestlab/master/samples/DevTestLabs/QuickStartTemplates/201-dtl-create-customimage-from-vhd/azuredeploy.json' -TemplateParameterObject $parameters
```

## <a name="next-steps"></a>다음 단계

- [DevTest Lab에서 사용자 지정 이미지와 수식 비교](devtest-lab-comparing-vm-base-image-types.md)
- [Azure DevTest Labs 간의 사용자 지정 이미지 복사](https://www.visualstudiogeeks.com/blog/DevOps/How-To-Move-CustomImages-VHD-Between-AzureDevTestLabs#copying-custom-images-between-azure-devtest-labs)
