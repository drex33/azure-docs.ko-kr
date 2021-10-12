---
title: 템플릿에서 Azure Key Vault 사용
description: Azure Key Vault를 사용하여 ARM 템플릿(Azure Resource Manager 템플릿) 배포 중에 보안 매개 변수 값을 전달하는 방법을 알아봅니다.
author: mumian
ms.date: 03/01/2021
ms.topic: tutorial
ms.author: jgao
ms.custom: seodec18, devx-track-azurepowershell
ms.openlocfilehash: a352015437437ef47b076b487710018e7ef8e2d9
ms.sourcegitcommit: 03e84c3112b03bf7a2bc14525ddbc4f5adc99b85
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/03/2021
ms.locfileid: "129402133"
---
# <a name="tutorial-integrate-azure-key-vault-in-your-arm-template-deployment"></a>자습서: ARM 템플릿 배포에 Azure Key Vault 통합

Azure Key Vault에서 비밀을 검색하여 ARM 템플릿(Azure Resource Manager 템플릿)을 배포할 때 비밀을 매개 변수로 전달하는 방법을 알아봅니다. 이 매개 변수 값은 해당 Key Vault ID만 참조하기 때문에 절대 노출되지 않습니다. 정적 ID 또는 동적 ID를 사용하여 키 자격 증명 모음 비밀을 참조할 수 있습니다. 이 자습서에서는 정적 ID를 사용합니다. 정적 ID 방식을 사용할 때는 템플릿 파일이 아닌 매개 변수 파일에서 키 자격 증명 모음을 참조합니다. 두 방법에 대한 자세한 내용은 [Azure Key Vault를 사용하여 배포 중에 보안 매개 변수 값 전달](./key-vault-parameter.md)을 참조하세요.

[리소스 배포 순서 설정](./template-tutorial-create-templates-with-dependent-resources.md) 자습서에서는 VM(가상 머신)을 만듭니다. VM 관리자 사용자 이름과 암호를 제공해야 합니다. 암호를 제공하는 대신, Azure Key Vault에 암호를 미리 저장한 다음, 배포 중에 키 자격 증명 모음에서 암호를 검색하도록 템플릿을 사용자 지정할 수 있습니다.

![키 자격 증명 모음과 Resource Manager 템플릿의 통합을 표시하는 다이어그램](./media/template-tutorial-use-key-vault/resource-manager-template-key-vault-diagram.png)

이 자습서에서 다루는 작업은 다음과 같습니다.

> [!div class="checklist"]
> * 키 자격 증명 모음 준비
> * 빠른 시작 템플릿 열기
> * 매개 변수 파일 편집
> * 템플릿 배포
> * 배포 유효성 검사
> * 리소스 정리

Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

키 자격 증명 모음의 보안 값을 사용하는 Microsoft Learn 모듈은 [고급 ARM 템플릿 기능을 사용하여 복잡한 클라우드 배포 관리](/learn/modules/manage-deployments-advanced-arm-template-features/)를 참조하세요.

## <a name="prerequisites"></a>필수 구성 요소

이 문서를 완료하려면 다음이 필요합니다.

* Resource Manager 도구 확장이 포함된 Visual Studio Code. [빠른 시작: Visual Studio Code를 사용하여 ARM 템플릿 만들기](quickstart-create-templates-use-visual-studio-code.md)를 참조하세요.
* 보안을 강화하려면 VM 관리자 계정용으로 생성된 암호를 사용하세요. [Azure Cloud Shell](../../cloud-shell/overview.md)을 사용하여 PowerShell 또는 Bash에서 다음 명령을 실행할 수 있습니다.

    ```shell
    openssl rand -base64 32
    ```

    자세히 알아보려면 `man openssl rand`을(를) 실행하여 수동 페이지를 엽니다.

    생성된 암호가 VM 암호 요구 사항을 충족하는지 확인합니다. Azure 서비스마다 특정한 암호 요구 사항이 있습니다. VM 암호 요구 사항은 [VM을 만들 때의 암호 요구 사항은 무엇인가요?](../../virtual-machines/windows/faq.yml#what-are-the-password-requirements-when-creating-a-vm-)를 참조하세요.

## <a name="prepare-a-key-vault"></a>키 자격 증명 모음 준비

이 섹션에서는 템플릿을 배포할 때 비밀을 검색할 수 있도록 키 자격 증명 모음을 만들고 비밀을 키 자격 증명 모음에 추가합니다. 키 자격 증명 모음을 만드는 여러 가지 방법이 있습니다. 이 자습서에서는 Azure PowerShell을 사용하여 [ARM 템플릿](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorials-use-key-vault/CreateKeyVault.json)을 배포합니다. 이 템플릿은 다음 두 가지 작업을 수행합니다.

* `enabledForTemplateDeployment` 속성이 활성화된 키 자격 증명 모음을 만듭니다. 이 속성이 *true* 여야 템플릿 배포 프로세스가 이 키 자격 증명 모음에 정의된 비밀에 액세스할 수 있습니다.
* 키 자격 증명 모음에 비밀을 추가합니다. 비밀에는 VM 관리자 암호가 저장됩니다.

> [!NOTE]
> 가상 머신 템플릿을 배포하는 사용자가 키 자격 증명 모음의 소유자나 기여자가 아닌 경우에는 소유자나 기여자가 키 자격 증명 모음의 `Microsoft.KeyVault/vaults/deploy/action`에 대한 액세스 권한을 해당 사용자에게 부여해야 합니다. 자세한 내용은 [Azure Key Vault를 사용하여 배포 중에 보안 매개 변수 값 전달](./key-vault-parameter.md)을 참조하세요.

다음 Azure PowerShell 스크립트를 실행하려면 **시도** 를 선택하여 Cloud Shell을 엽니다. 스크립트를 붙여넣으려면 마우스 오른쪽 단추로 셸 창을 클릭한 다음, **붙여넣기** 를 선택합니다.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$upn = Read-Host -Prompt "Enter your user principal name (email address) used to sign in to Azure"
$secretValue = Read-Host -Prompt "Enter the virtual machine administrator password" -AsSecureString

$resourceGroupName = "${projectName}rg"
$keyVaultName = $projectName
$adUserId = (Get-AzADUser -UserPrincipalName $upn).Id
$templateUri = "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorials-use-key-vault/CreateKeyVault.json"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -keyVaultName $keyVaultName -adUserId $adUserId -secretValue $secretValue

Write-Host "Press [ENTER] to continue ..."
```

> [!IMPORTANT]
> * 리소스 그룹 이름은 **rg** 가 추가된 프로젝트 이름입니다. [이 자습서에서 만든 리소스를 더 쉽게 정리](#clean-up-resources)하려면 [다음 템플릿을 배포](#deploy-the-template)할 때 동일한 프로젝트 이름과 리소스 그룹 이름을 사용하세요.
> * 비밀의 기본 이름은 **vmAdminPassword** 입니다. 이 이름은 템플릿에 하드 코드되어 있습니다.
> * 템플릿에서 비밀을 검색할 수 있도록 하려면 키 자격 증명 모음에 **템플릿 배포를 위해 Azure Resource Manager에 대한 액세스 사용** 이라는 액세스 정책을 사용하도록 설정해야 합니다. 이 정책은 템플릿에서 사용하도록 설정됩니다. 이 액세스 정책에 대한 자세한 내용은 [키 자격 증명 모음 및 비밀 배포](./key-vault-parameter.md#deploy-key-vaults-and-secrets)를 참조하세요.

템플릿에는 `keyVaultId`라는 하나의 출력 값이 있습니다. 이 ID를 비밀 이름과 함께 사용하여 자습서의 뒷부분에서 비밀 값을 검색합니다. 리소스 ID 형식은 다음과 같습니다.

```json
/subscriptions/<SubscriptionID>/resourceGroups/mykeyvaultdeploymentrg/providers/Microsoft.KeyVault/vaults/<KeyVaultName>
```

ID를 복사하여 붙여넣으면 ID가 여러 줄로 분할될 수 있습니다. 줄을 병합하고 여분의 공백을 제거하세요.

배포의 유효성을 검사하려면 동일한 셸 창에서 다음 PowerShell 명령을 실행하여 비밀을 일반 텍스트로 검색합니다. 이 명령은 이전 PowerShell 스크립트에 정의된 `$keyVaultName` 변수를 사용하므로 동일한 셸 세션에서만 작동합니다.

```azurepowershell
$secret = Get-AzKeyVaultSecret -VaultName $keyVaultName -Name "vmAdminPassword"
$ssPtr = [System.Runtime.InteropServices.Marshal]::SecureStringToBSTR($secret.SecretValue)
try {
   $secretValueText = [System.Runtime.InteropServices.Marshal]::PtrToStringBSTR($ssPtr)
} finally {
   [System.Runtime.InteropServices.Marshal]::ZeroFreeBSTR($ssPtr)
}
Write-Output $secretValueText
```

이제 키 자격 증명 모음 및 비밀이 준비되었습니다. 다음 섹션에서는 배포 중에 비밀을 검색하도록 기존 템플릿을 사용자 지정하는 방법을 보여줍니다.

## <a name="open-a-quickstart-template"></a>빠른 시작 템플릿 열기

Azure 빠른 시작 템플릿은 ARM 템플릿용 리포지토리입니다. 템플릿을 처음부터 새로 만드는 대신 샘플 템플릿을 찾아서 사용자 지정할 수 있습니다. 이 자습서에 사용되는 템플릿의 이름은 [Deploy a simple Windows VM](https://azure.microsoft.com/resources/templates/vm-simple-windows/)입니다.

1. Visual Studio Code에서 **파일** > **파일 열기** 를 차례로 선택합니다.

1. **파일 이름** 상자에 다음 URL을 붙여넣습니다.

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/quickstarts/microsoft.compute/vm-simple-windows/azuredeploy.json
    ```

1. **열기** 를 선택하여 파일을 엽니다. [자습서: 종속 리소스를 사용하여 ARM 템플릿 만들기](./template-tutorial-create-templates-with-dependent-resources.md).
   템플릿은 6개의 리소스를 정의합니다.

   * [**Microsoft.Storage/storageAccounts**](/azure/templates/Microsoft.Storage/storageAccounts).
   * [**Microsoft.Network/publicIPAddresses**](/azure/templates/microsoft.network/publicipaddresses).
   * [**Microsoft.Network/networkSecurityGroups**](/azure/templates/microsoft.network/networksecuritygroups).
   * [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks).
   * [**Microsoft.Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces).
   * [**Microsoft.Compute/virtualMachines**](/azure/templates/microsoft.compute/virtualmachines).

   템플릿을 사용자 지정하기 전에 템플릿의 몇 가지 기본적인 내용을 이해하면 유용합니다.

1. **파일** > **다른 이름으로 저장** 을 선택하여 파일 복사본을 로컬 컴퓨터에 *azuredeploy.json* 이라는 이름으로 저장합니다.

1. 1~3단계를 반복하여 다음 URL을 연 다음, 파일을 *azuredeploy.parameters.json* 으로 저장합니다.

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/quickstarts/microsoft.compute/vm-simple-windows/azuredeploy.parameters.json
    ```

## <a name="edit-the-parameters-file"></a>매개 변수 파일 편집

정적 ID 메서드를 사용하면 템플릿 파일을 변경할 필요가 없습니다. 비밀 값 검색은 템플릿 매개 변수 파일을 구성하여 수행됩니다.

1. 열려 있지 않은 경우 Visual Studio Code에서 *azuredeploy.parameters.json* 을 엽니다.
1. `adminPassword` 매개 변수를 다음과 같이 업데이트합니다.

    ```json
    "adminPassword": {
        "reference": {
            "keyVault": {
                "id": "/subscriptions/<SubscriptionID>/resourceGroups/mykeyvaultdeploymentrg/providers/Microsoft.KeyVault/vaults/<KeyVaultName>"
            },
            "secretName": "vmAdminPassword"
        }
    },
    ```

    > [!IMPORTANT]
    > `id`의 값을 이전 절차에서 만든 키 자격 증명 모음의 리소스 ID로 바꿉니다. `secretName`은 **vmAdminPassword** 로 하드 코딩됩니다.  [키 자격 증명 모음 준비](#prepare-a-key-vault)를 참조하세요.

    ![키 자격 증명 모음과 Resource Manager 템플릿 가상 머신 배포 매개 변수 파일 통합](./media/template-tutorial-use-key-vault/resource-manager-tutorial-create-vm-parameters-file.png)

1. 다음 값을 업데이트합니다.

    * `adminUsername`: 가상 머신 관리자 계정의 이름입니다.
    * `dnsLabelPrefix`: `dnsLabelPrefix` 값의 이름을 지정합니다.

    이름의 예는 이전의 이미지를 참조하세요.

1. 변경 내용을 저장합니다.

## <a name="deploy-the-template"></a>템플릿 배포

1. [Cloud Shell](https://shell.azure.com)에 로그인합니다.

1. 왼쪽 위 모서리에서 **PowerShell** 또는 **Bash**(CLI용)를 선택하여 기본 환경을 선택합니다.  전환하는 경우 셸을 다시 시작해야 합니다.

    ![Azure Portal Cloud Shell 업로드 파일](./media/template-tutorial-use-template-reference/azure-portal-cloud-shell-upload-file.png)

1. **파일 업로드/다운로드** 를 선택한 다음, **업로드** 를 선택합니다. *azuredeploy.json* 및 *azuredeploy.parameters.json* 을 Cloud Shell에 업로드합니다. 파일을 업로드한 후 `ls` 명령 및 `cat` 명령을 사용하여 파일이 성공적으로 업로드되었는지 확인할 수 있습니다.

1. 다음 PowerShell 스크립트를 실행하여 템플릿을 배포합니다.

    ```azurepowershell
    $projectName = Read-Host -Prompt "Enter the same project name that is used for creating the key vault"
    $location = Read-Host -Prompt "Enter the same location that is used for creating the key vault (i.e. centralus)"
    $resourceGroupName = "${projectName}rg"

    New-AzResourceGroupDeployment `
        -ResourceGroupName $resourceGroupName `
        -TemplateFile "$HOME/azuredeploy.json" `
        -TemplateParameterFile "$HOME/azuredeploy.parameters.json"

    Write-Host "Press [ENTER] to continue ..."
    ```

    템플릿을 배포할 때는 키 자격 증명 모음에서 사용한 것과 동일한 리소스 그룹을 사용합니다. 이 접근 방식을 사용하면 리소스 그룹 2개를 삭제하지 않고 1개만 삭제하면 되므로 리소스를 더 쉽게 정리할 수 있습니다.

## <a name="validate-the-deployment"></a>배포 유효성 검사

가상 머신 배포에 성공한 후에는 키 자격 증명 모음에 저장된 암호를 사용하여 로그인 자격 증명을 테스트합니다.

1. [Azure Portal](https://portal.azure.com)을 엽니다.

1. **리소스 그룹** >  **\<*YourResourceGroupName*>**  > **simpleWinVM** 을 선택합니다.
1. 위쪽에 있는 **연결** 을 선택합니다.
1. **RDP 파일 다운로드** 를 선택한 다음, 지침에 따라 키 자격 증명 모음에 저장된 암호를 사용하여 가상 머신에 로그인합니다.

## <a name="clean-up-resources"></a>리소스 정리

Azure 리소스가 더 이상 필요하지 않은 경우 리소스 그룹을 삭제하여 배포된 리소스를 정리합니다.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name that is used for creating the key vault"
$resourceGroupName = "${projectName}rg"

Remove-AzResourceGroup -Name $resourceGroupName

Write-Host "Press [ENTER] to continue ..."
```

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Azure Key Vault에서 비밀을 검색했습니다. 그런 다음, 템플릿 배포에 비밀을 사용했습니다. 가상 머신 확장을 사용하여 배포 후 작업을 수행하는 방법은 다음을 참조하세요.

> [!div class="nextstepaction"]
> [가상 머신 확장 배포](./template-tutorial-deploy-vm-extensions.md)
