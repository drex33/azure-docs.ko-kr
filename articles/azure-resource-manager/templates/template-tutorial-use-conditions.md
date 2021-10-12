---
title: 템플릿에서 조건 사용
description: 조건에 따라 Azure 리소스를 배포하는 방법을 알아봅니다. 새 리소스를 배포하거나 기존 리소스를 사용하는 방법을 보여줍니다.
author: mumian
ms.date: 04/23/2020
ms.topic: tutorial
ms.author: jgao
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 4f6255c0a59a57677c4ff4119611630a4169133f
ms.sourcegitcommit: 03e84c3112b03bf7a2bc14525ddbc4f5adc99b85
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/03/2021
ms.locfileid: "129400746"
---
# <a name="tutorial-use-condition-in-arm-templates"></a>자습서: ARM 템플릿에서 조건 사용

ARM 템플릿(Azure Resource Manager 템플릿)의 조건에 따라 Azure 리소스를 배포하는 방법에 대해 알아봅니다.

[리소스 배포 순서 설정](./template-tutorial-create-templates-with-dependent-resources.md) 자습서에서는 가상 머신, 가상 네트워크 및 스토리지 계정을 포함한 기타 종속 리소스를 만듭니다. 매번 새 스토리지 계정을 만드는 대신, 사용자가 새 스토리지 계정을 만들지, 기존 스토리지 계정을 사용할지 선택하도록 할 수 있습니다. 이 목표를 달성하기 위해 추가 매개 변수를 정의합니다. 매개 변수의 값이 **new** 인 경우 새 스토리지 계정이 만들어집니다. 그렇지 않을 경우 지정된 이름의 기존 스토리지 계정이 사용됩니다.

![Resource Manager 템플릿 사용 조건 다이어그램](./media/template-tutorial-use-conditions/resource-manager-template-use-condition-diagram.png)

이 자습서에서 다루는 작업은 다음과 같습니다.

> [!div class="checklist"]
> * 빠른 시작 템플릿 열기
> * 템플릿 수정
> * 템플릿 배포
> * 리소스 정리

이 자습서에서는 기본적인 조건 사용 시나리오만 다룹니다. 자세한 내용은 다음을 참조하세요.

* [템플릿 파일 구조: 조건](conditional-resource-deployment.md).
* [조건에 따라 ARM 템플릿에 리소스를 배포합니다](/azure/architecture/guide/azure-resource-manager/advanced-templates/conditional-deploy).
* [템플릿 함수: If](./template-functions-logical.md#if).
* [ARM 템플릿의 비교 함수](./template-functions-comparison.md)

조건을 다루는 Microsoft Learn 모듈은 [고급 ARM 템플릿 기능을 사용하여 복잡한 클라우드 배포 관리](/learn/modules/manage-deployments-advanced-arm-template-features/)를 참조하세요.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

## <a name="prerequisites"></a>사전 요구 사항

이 문서를 완료하려면 다음이 필요합니다.

* Resource Manager 도구 확장이 포함된 Visual Studio Code. [빠른 시작: Visual Studio Code를 사용하여 ARM 템플릿 만들기](quickstart-create-templates-use-visual-studio-code.md)를 참조하세요.
* 보안을 강화하려면 가상 머신 관리자 계정에 생성된 암호를 사용합니다. [Azure Cloud Shell](../../cloud-shell/overview.md)을 사용하여 PowerShell 또는 Bash에서 다음 명령을 실행할 수 있습니다.

    ```shell
    openssl rand -base64 32
    ```

    자세히 알아보려면 `man openssl rand`를 실행하여 수동 페이지를 엽니다.

    Azure Key Vault는 암호화 키 및 기타 비밀을 보호하기 위한 것입니다. 자세한 내용은 [자습서: ARM 템플릿 배포에 Azure Key Vault 통합](./template-tutorial-use-key-vault.md)을 참조하세요. 또한 3개월 마다 암호를 업데이트하는 것도 좋습니다.

## <a name="open-a-quickstart-template"></a>빠른 시작 템플릿 열기

Azure 빠른 시작 템플릿은 ARM 템플릿용 리포지토리입니다. 템플릿을 처음부터 새로 만드는 대신 샘플 템플릿을 찾아서 사용자 지정할 수 있습니다. 이 자습서에 사용되는 템플릿의 이름은 [Deploy a simple Windows VM](https://azure.microsoft.com/resources/templates/vm-simple-windows/)입니다.

1. Visual Studio Code에서 **파일** > **파일 열기** 를 차례로 선택합니다.
1. **파일 이름** 에서 다음 URL을 붙여넣습니다.

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/quickstarts/microsoft.compute/vm-simple-windows/azuredeploy.json
    ```

1. **열기** 를 선택하여 파일을 엽니다.
1. 템플릿에 6개의 리소스가 정의되어 있습니다.

   * [**Microsoft.Storage/storageAccounts**](/azure/templates/Microsoft.Storage/storageAccounts).
   * [**Microsoft.Network/publicIPAddresses**](/azure/templates/microsoft.network/publicipaddresses).
   * [**Microsoft.Network/networkSecurityGroups**](/azure/templates/microsoft.network/networksecuritygroups).
   * [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks).
   * [**Microsoft.Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces).
   * [**Microsoft.Compute/virtualMachines**](/azure/templates/microsoft.compute/virtualmachines).

    템플릿을 사용자 지정하기 전에 템플릿 참조를 검토하는 것이 좋습니다.

1. **파일** > **다른 이름으로 저장** 을 선택하여 파일 복사본을 로컬 컴퓨터에 _azuredeploy.json_ 이라는 이름으로 저장합니다.

## <a name="modify-the-template"></a>템플릿 수정

기존 템플릿에서 두 가지를 변경합니다.

* 스토리지 계정 이름 매개 변수를 추가합니다. 사용자는 새 스토리지 계정 이름 또는 기존 스토리지 계정 이름을 지정할 수 있습니다.
* `newOrExisting`이라는 새 매개 변수를 추가합니다. 배포에서는 이 매개 변수를 사용하여 새 스토리지 계정을 만들지 또는 기존 스토리지 계정을 사용할지 여부를 결정합니다.

변경 절차는 다음과 같습니다.

1. Visual Studio Code에서 _azuredeploy.json_ 을 엽니다.
1. 템플릿 전체에서 3개의 `variables('storageAccountName')` 를 `parameters('storageAccountName')` 로 바꿉니다.
1. 다음 변수 정의를 제거합니다.

    ![제거해야 하는 변수 정의를 강조 표시하는 스크린샷.](./media/template-tutorial-use-conditions/resource-manager-tutorial-use-condition-template-remove-storageaccountname.png)

1. 매개 변수 섹션의 시작 부분에 다음 두 개의 매개 변수를 추가합니다.

    ```json
    "storageAccountName": {
      "type": "string"
    },
    "newOrExisting": {
      "type": "string",
      "allowedValues": [
        "new",
        "existing"
      ]
    },
    ```

    Alt+Shift+F를 눌러 Visual Studio Code에서 템플릿의 서식을 지정합니다.

    업데이트된 매개 변수 정의는 다음과 같습니다.

    ![Resource Manager 사용 조건](./media/template-tutorial-use-conditions/resource-manager-tutorial-use-condition-template-parameters.png)

1. 스토리지 계정 정의의 시작 부분에 다음 줄을 추가합니다.

    ```json
    "condition": "[equals(parameters('newOrExisting'),'new')]",
    ```

    조건은 `newOrExisting` 매개 변수의 값을 확인합니다. 매개 변수 값이 **new** 이면 배포에서 스토리지 계정을 만듭니다.

    업데이트된 스토리지 계정 정의는 다음과 같습니다.

    ![업데이트된 스토리지 계정 정의를 보여주는 스크린샷.](./media/template-tutorial-use-conditions/resource-manager-tutorial-use-condition-template.png)
1. 가상 머신 리소스 정의의 `storageUri` 속성을 다음 값으로 업데이트합니다.

    ```json
    "storageUri": "[concat('https://', parameters('storageAccountName'), '.blob.core.windows.net')]"
    ```

    다른 리소스 그룹에서 기존 스토리지 계정을 사용하는 경우 이 변경은 필요합니다.

1. 변경 내용을 저장합니다.

## <a name="deploy-the-template"></a>템플릿 배포

1. [Cloud Shell](https://shell.azure.com)에 로그인합니다.

1. 왼쪽 위 모서리에서 **PowerShell** 또는 **Bash**(CLI용)를 선택하여 기본 환경을 선택합니다. 전환하는 경우 셸을 다시 시작해야 합니다.

    ![Azure Portal Cloud Shell 업로드 파일](./media/template-tutorial-use-template-reference/azure-portal-cloud-shell-upload-file.png)

1. **파일 업로드/다운로드** 를 선택한 다음, **업로드** 를 선택합니다. 이전 스크린샷을 참조하세요. 이전 섹션에서 저장한 파일을 선택합니다. 파일을 업로드한 후 `ls` 명령 및 `cat` 명령을 사용하여 파일이 성공적으로 업로드되었는지 확인할 수 있습니다.

1. 다음 PowerShell 스크립트를 실행하여 템플릿을 배포합니다.

    > [!IMPORTANT]
    > 스토리지 계정 이름은 Azure 내에서 고유해야 합니다. 이름에는 소문자 또는 숫자만 사용할 수 있습니다. 24자 이하여야 합니다. 스토리지 계정 이름은 **store** 가 추가된 프로젝트 이름입니다. 프로젝트 이름과 생성된 스토리지 계정 이름이 스토리지 계정 이름 요구 사항을 충족하는지 확인합니다.

    ```azurepowershell
    $projectName = Read-Host -Prompt "Enter a project name that is used to generate resource group name and resource names"
    $newOrExisting = Read-Host -Prompt "Create new or use existing (Enter new or existing)"
    $location = Read-Host -Prompt "Enter the Azure location (i.e. centralus)"
    $vmAdmin = Read-Host -Prompt "Enter the admin username"
    $vmPassword = Read-Host -Prompt "Enter the admin password" -AsSecureString
    $dnsLabelPrefix = Read-Host -Prompt "Enter the DNS Label prefix"

    $resourceGroupName = "${projectName}rg"
    $storageAccountName = "${projectName}store"

    New-AzResourceGroup -Name $resourceGroupName -Location $location
    New-AzResourceGroupDeployment `
        -ResourceGroupName $resourceGroupName `
        -adminUsername $vmAdmin `
        -adminPassword $vmPassword `
        -dnsLabelPrefix $dnsLabelPrefix `
        -storageAccountName $storageAccountName `
        -newOrExisting $newOrExisting `
        -TemplateFile "$HOME/azuredeploy.json"

    Write-Host "Press [ENTER] to continue ..."
    ```

    > [!NOTE]
    > `newOrExisting`이 **new** 이지만 지정된 스토리지 계정 이름의 스토리지 계정이 이미 있는 경우 배포가 실패합니다.

`newOrExisting`이 **existing** 으로 설정된 다른 배포를 시도하고 기존 스토리지 계정을 지정해 보세요. 스토리지 계정을 미리 들려면 [스토리지 계정 만들기](../../storage/common/storage-account-create.md)를 참조하세요.

## <a name="clean-up-resources"></a>리소스 정리

Azure 리소스가 더 이상 필요하지 않은 경우 리소스 그룹을 삭제하여 배포한 리소스를 정리합니다. 리소스 그룹을 삭제하려면 **사용해 보기** 를 선택하여 Cloud Shell을 엽니다. PowerShell 스크립트를 붙여넣으려면 셸 창을 마우스 오른쪽 단추로 클릭한 후 **붙여넣기** 를 선택합니다.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name you used in the last procedure"
$resourceGroupName = "${projectName}rg"

Remove-AzResourceGroup -Name $resourceGroupName

Write-Host "Press [ENTER] to continue ..."
```

## <a name="next-steps"></a>다음 단계

이 자습서에서는 새로운 스토리지 계정을 만들지, 기존 스토리지 계정을 사용할지 사용자가 선택할 수 있는 템플릿을 개발했습니다. Azure Key Vault에서 비밀을 검색하고 해당 비밀을 템플릿 배포에 암호로 사용하는 방법을 알아보려면 다음을 참조하세요.

> [!div class="nextstepaction"]
> [템플릿 배포에서 Key Vault 통합](./template-tutorial-use-key-vault.md)
