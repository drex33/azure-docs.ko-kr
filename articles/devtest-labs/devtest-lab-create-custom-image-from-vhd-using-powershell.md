---
title: Azure PowerShell을 사용하여 VHD 파일에서 사용자 지정 이미지 만들기
description: PowerShell을 사용하여 VHD 파일에서 Azure DevTest Labs에 사용자 지정 이미지 만들기 자동화
ms.topic: how-to
ms.date: 06/26/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 812a0062caab01131b84465cc67c625b8253469e
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130235173"
---
# <a name="create-a-custom-image-from-a-vhd-file-using-powershell"></a>PowerShell을 사용하여 VHD 파일에서 사용자 지정 이미지 만들기

[!INCLUDE [devtest-lab-create-custom-image-from-vhd-selector](../../includes/devtest-lab-create-custom-image-from-vhd-selector.md)]

[!INCLUDE [devtest-lab-custom-image-definition](../../includes/devtest-lab-custom-image-definition.md)]

[!INCLUDE [devtest-lab-upload-vhd-options](../../includes/devtest-lab-upload-vhd-options.md)]

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="step-by-step-instructions"></a>단계별 지침

다음 단계는 PowerShell을 사용하여 VHD 파일에서 사용자 지정 이미지를 만드는 과정을 안내합니다.

1. PowerShell 프롬프트에서, **Connect-AzAccount** cmdlet에 대한 다음 호출로 Azure 계정에 로그인합니다.

    ```powershell
    Connect-AzAccount
    ```

1.  **Select-AzSubscription** cmdlet을 호출하여 원하는 Azure 구독을 선택합니다. **$subscriptionId** 변수에 대한 다음 자리 표시자를 유효한 Azure 구독 ID로 바꿉니다.

    ```powershell
    $subscriptionId = '<Specify your subscription ID here>'
    Select-AzSubscription -SubscriptionId $subscriptionId
    ```

1.  **Get-AzResource** cmdlet을 호출하여 랩 객체를 가져옵니다. **$labRg** 및 **$labName** 변수에 대한 다음 자리 표시자를 환경에 적합한 값으로 바꿉니다.

    ```powershell
    $labRg = '<Specify your lab resource group name here>'
    $labName = '<Specify your lab name here>'
    $lab = Get-AzResource -ResourceId ('/subscriptions/' + $subscriptionId + '/resourceGroups/' + $labRg + '/providers/Microsoft.DevTestLab/labs/' + $labName)
    ```

1.  **$vhdUri** 변수에 대한 다음 자리 표시자를 업로드한 VHD 파일에 대한 URI로 바꿉니다. Azure Portal의 스토리지 계정의 Blob 블레이드에서 VHD 파일의 URl를 가져올 수 있습니다.

    ```powershell
    $vhdUri = '<Specify the VHD URI here>'
    ```

1.  **New-AzResourceGroupDeployment** cmdlet로 사용자 지정 이미지를 만듭니다. **$customImageName** 및 **$customImageDescription** 변수에 대한 다음 자리 표시자를 환경에 의미 있는 이름으로 바꿉니다.

    ```powershell
    $customImageName = '<Specify the custom image name>'
    $customImageDescription = '<Specify the custom image description>'

    $parameters = @{existingLabName="$($lab.Name)"; existingVhdUri=$vhdUri; imageOsType='windows'; isVhdSysPrepped=$false; imageName=$customImageName; imageDescription=$customImageDescription}

    New-AzResourceGroupDeployment -ResourceGroupName $lab.ResourceGroupName -Name CreateCustomImage -TemplateUri 'https://raw.githubusercontent.com/Azure/azure-devtestlab/master/samples/DevTestLabs/QuickStartTemplates/201-dtl-create-customimage-from-vhd/azuredeploy.json' -TemplateParameterObject $parameters
    ```

## <a name="powershell-script-to-create-a-custom-image-from-a-vhd-file"></a>VHD 파일에서 사용자 지정 이미지를 만들 PowerShell 스크립트

다음 PowerShell 스크립트를 사용하여 VHD 파일에서 사용자 지정 이미지를 만들 수 있습니다. 자리 표시자(각괄호)를 필요한 적합한 값으로 바꿉니다.

```powershell
# Log in to your Azure account.
Connect-AzAccount

# Select the desired Azure subscription.
$subscriptionId = '<Specify your subscription ID here>'
Select-AzSubscription -SubscriptionId $subscriptionId

# Get the lab object.
$labRg = '<Specify your lab resource group name here>'
$labName = '<Specify your lab name here>'
$lab = Get-AzResource -ResourceId ('/subscriptions/' + $subscriptionId + '/resourceGroups/' + $labRg + '/providers/Microsoft.DevTestLab/labs/' + $labName)

# Set the URI of the VHD file.
$vhdUri = '<Specify the VHD URI here>'

# Set the custom image name and description values.
$customImageName = '<Specify the custom image name>'
$customImageDescription = '<Specify the custom image description>'

# Set up the parameters object.
$parameters = @{existingLabName="$($lab.Name)"; existingVhdUri=$vhdUri; imageOsType='windows'; isVhdSysPrepped=$false; imageName=$customImageName; imageDescription=$customImageDescription}

# Create the custom image.
New-AzResourceGroupDeployment -ResourceGroupName $lab.ResourceGroupName -Name CreateCustomImage -TemplateUri 'https://raw.githubusercontent.com/Azure/azure-devtestlab/master/samples/DevTestLabs/QuickStartTemplates/201-dtl-create-customimage-from-vhd/azuredeploy.json' -TemplateParameterObject $parameters
```

## <a name="related-blog-posts"></a>관련 블로그 게시물

- [사용자 지정 이미지 또는 수식?](./devtest-lab-faq.yml#blog-post)
- [Azure DevTest Labs 간의 사용자 지정 이미지 복사](https://www.visualstudiogeeks.com/blog/DevOps/How-To-Move-CustomImages-VHD-Between-AzureDevTestLabs#copying-custom-images-between-azure-devtest-labs)

## <a name="next-steps"></a>다음 단계

- [랩에 VM 추가](devtest-lab-add-vm.md)