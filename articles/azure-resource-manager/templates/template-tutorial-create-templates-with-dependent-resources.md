---
title: 종속 리소스가 포함된 템플릿
description: Azure Portal을 사용하여 여러 리소스가 포함된 ARM 템플릿(Azure Resource Manager 템플릿)을 만들고 배포하는 방법을 알아봅니다.
author: mumian
ms.date: 04/23/2020
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: b7e4fa06db063538c801b01225818c4c9311e3d6
ms.sourcegitcommit: 03e84c3112b03bf7a2bc14525ddbc4f5adc99b85
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/03/2021
ms.locfileid: "129400784"
---
# <a name="tutorial-create-arm-templates-with-dependent-resources"></a>자습서: 종속 리소스를 사용하여 ARM 템플릿 만들기

ARM 템플릿(Azure Resource Manager 템플릿)을 만들어서 여러 리소스를 배포하고 배포 순서를 구성하는 방법을 알아봅니다. 템플릿을 만든 후에는 Azure Portal에서 Azure Cloud Shell을 사용하여 템플릿을 배포합니다.

이 자습서에서는 스토리지 계정, 가상 머신, 가상 네트워크 및 몇 가지 다른 종속 리소스를 만듭니다. 일부 리소스는 다른 리소스가 존재하기 전에는 배포할 수 없습니다. 예를 들어 스토리지 계정 및 네트워크 인터페이스가 없으면 가상 머신을 만들 수 없습니다. 한 리소스를 다른 리소스의 종속 리소스로 만들어서 이 관계를 정의할 수 있습니다. Resource Manager는 리소스 간의 종속성을 평가한 후 종속된 순서에 따라 리소스를 배포합니다. 리소스가 서로 종속되어 있지 않은 경우 Resource Manager는 이를 병렬로 배포합니다. 자세한 내용은 [ARM 템플릿에서 리소스를 배포하는 순서 정의](./resource-dependency.md)를 참조하세요.

![Resource Manager 템플릿 종속 리소스 배포 순서 다이어그램](./media/template-tutorial-create-templates-with-dependent-resources/resource-manager-template-dependent-resources-diagram.png)

이 자습서에서 다루는 작업은 다음과 같습니다.

> [!div class="checklist"]
> * 빠른 시작 템플릿 열기
> * 템플릿 탐색
> * 템플릿 배포

Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

리소스 종속성을 다루는 Microsoft Learn 모듈은 [고급 ARM 템플릿 기능을 사용하여 복잡한 클라우드 배포 관리](/learn/modules/manage-deployments-advanced-arm-template-features/)를 참조하세요.

## <a name="prerequisites"></a>필수 구성 요소

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
2. **파일 이름** 에서 다음 URL을 붙여넣습니다.

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/quickstarts/microsoft.compute/vm-simple-windows/azuredeploy.json
    ```

3. **열기** 를 선택하여 파일을 엽니다.
4. **파일** > **다른 이름으로 저장** 을 선택하여 파일 복사본을 로컬 컴퓨터에 _azuredeploy.json_ 이라는 이름으로 저장합니다.

## <a name="explore-the-template"></a>템플릿 탐색

이 섹션의 템플릿을 탐색하는 경우 다음 질문에 대답해 보세요.

* 이 템플릿에 정의되는 Azure 리소스는 얼마나 되나요?
* 리소스 중 하나는 Azure Storage 계정입니다. 마지막 자습서에서 사용한 것과 비슷한 같은 정의인가요?
* 이 템플릿에 정의된 리소스에 대한 템플릿 참조를 찾을 수 있나요?
* 리소스의 종속성을 찾을 수 있나요?

1. Visual Studio Code에서 `resources` 내부에 첫 번째 수준 요소와 두 번째 수준 요소만 표시될 때까지 요소를 축소합니다.

    ![Visual Studio Code ARM 템플릿](./media/template-tutorial-create-templates-with-dependent-resources/resource-manager-template-visual-studio-code.png)

    템플릿에 6개의 리소스가 정의되어 있습니다.

   * [**Microsoft.Storage/storageAccounts**](/azure/templates/Microsoft.Storage/storageAccounts).
   * [**Microsoft.Network/publicIPAddresses**](/azure/templates/microsoft.network/publicipaddresses).
   * [**Microsoft.Network/networkSecurityGroups**](/azure/templates/microsoft.network/networksecuritygroups).
   * [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks).
   * [**Microsoft.Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces).
   * [**Microsoft.Compute/virtualMachines**](/azure/templates/microsoft.compute/virtualmachines).

     템플릿을 사용자 지정하기 전에 템플릿 참조를 검토하는 것이 좋습니다.

1. 첫 번째 리소스를 확장합니다. 스토리지 계정입니다. 리소스 정의를 [템플릿 참조](/azure/templates/Microsoft.Storage/storageAccounts)와 비교합니다.

    ![Visual Studio Code ARM 템플릿 스토리지 계정 정의](./media/template-tutorial-create-templates-with-dependent-resources/resource-manager-template-storage-account-definition.png)

1. 두 번째 리소스를 확장합니다. 리소스 종류는 `Microsoft.Network/publicIPAddresses`입니다. 리소스 정의를 [템플릿 참조](/azure/templates/microsoft.network/publicipaddresses)와 비교합니다.

    ![Visual Studio Code ARM 템플릿 공용 IP 주소 정의](./media/template-tutorial-create-templates-with-dependent-resources/resource-manager-template-public-ip-address-definition.png)

1. 세 번째 리소스를 확장합니다. 리소스 종류는 `Microsoft.Network/networkSecurityGroups`입니다. 리소스 정의를 [템플릿 참조](/azure/templates/microsoft.network/networksecuritygroups)와 비교합니다.

    ![Visual Studio Code ARM 템플릿 네트워크 보안 그룹 정의](./media/template-tutorial-create-templates-with-dependent-resources/resource-manager-template-network-security-group-definition.png)

1. 네 번째 리소스를 확장합니다. 리소스 종류는 `Microsoft.Network/virtualNetworks`입니다.

    ![Visual Studio Code ARM 템플릿 가상 네트워크 dependsOn](./media/template-tutorial-create-templates-with-dependent-resources/resource-manager-template-virtual-network-definition.png)

    `dependsOn` 요소를 사용하면 한 리소스를 하나 이상의 리소스에 종속된 것으로 정의할 수 있습니다. 이 리소스는 다음과 같은 다른 리소스에 종속됩니다.

    * `Microsoft.Network/networkSecurityGroups`

1. 다섯 번째 리소스를 확장합니다. 리소스 종류는 `Microsoft.Network/networkInterfaces`입니다. 이 리소스는 다음과 같은 다른 두 리소스에 종속됩니다.

    * `Microsoft.Network/publicIPAddresses`
    * `Microsoft.Network/virtualNetworks`

1. 여섯 번째 리소스를 확장합니다. 이 리소스는 가상 머신입니다. 이 리소스는 다음과 같은 두 리소스에 종속됩니다.

    * `Microsoft.Storage/storageAccounts`
    * `Microsoft.Network/networkInterfaces`

다음 다이어그램은 이 템플릿의 리소스 및 종속성 정보를 보여줍니다.

![Visual Studio Code ARM 템플릿 종속성 다이어그램](./media/template-tutorial-create-templates-with-dependent-resources/resource-manager-template-visual-studio-code-dependency-diagram.png)

종속성을 지정하면 Resource Manager가 솔루션을 효율적으로 배포합니다. 종속성이 없기 때문에 스토리지 계정, 공용 IP 주소 및 가상 네트워크가 병렬로 배포됩니다. 공용 IP 주소 및 가상 네트워크가 배포된 후에는 네트워크 인터페이스가 생성됩니다. 다른 모든 리소스가 배포되면 Resource Manager는 가상 머신을 배포합니다.

## <a name="deploy-the-template"></a>템플릿 배포

1. [Cloud Shell](https://shell.azure.com)에 로그인합니다.

1. 왼쪽 위 모서리에서 **PowerShell** 또는 **Bash**(CLI용)를 선택하여 기본 환경을 선택합니다.  전환하는 경우 셸을 다시 시작해야 합니다.

    ![Azure Portal Cloud Shell 업로드 파일](./media/template-tutorial-use-template-reference/azure-portal-cloud-shell-upload-file.png)

1. **파일 업로드/다운로드** 를 선택한 다음, **업로드** 를 선택합니다. 이전 스크린샷을 참조하세요. 이전에 저장한 파일을 선택합니다. 파일을 업로드한 후 `ls` 명령 및 `cat` 명령을 사용하여 파일이 성공적으로 업로드되었는지 확인할 수 있습니다.

1. 다음 PowerShell 스크립트를 실행하여 템플릿을 배포합니다.

    # <a name="cli"></a>[CLI](#tab/CLI)

    ```azurecli
    echo "Enter a project name that is used to generate resource group name:" &&
    read projectName &&
    echo "Enter the location (i.e. centralus):" &&
    read location &&
    echo "Enter the virtual machine admin username:" &&
    read adminUsername &&
    echo "Enter the DNS label prefix:" &&
    read dnsLabelPrefix &&
    resourceGroupName="${projectName}rg" &&
    az group create --name $resourceGroupName --location $location &&
    az deployment group create --resource-group $resourceGroupName --template-file "$HOME/azuredeploy.json" --parameters adminUsername=$adminUsername dnsLabelPrefix=$dnsLabelPrefix
    ```

    # <a name="powershell"></a>[PowerShell](#tab/PowerShell)

    ```azurepowershell
    $projectName = Read-Host -Prompt "Enter a project name that is used to generate resource group name"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $adminUsername = Read-Host -Prompt "Enter the virtual machine admin username"
    $adminPassword = Read-Host -Prompt "Enter the admin password" -AsSecureString
    $dnsLabelPrefix = Read-Host -Prompt "Enter the DNS label prefix"
    $resourceGroupName = "${projectName}rg"

    New-AzResourceGroup -Name $resourceGroupName -Location "$location"
    New-AzResourceGroupDeployment `
        -ResourceGroupName $resourceGroupName `
        -adminUsername $adminUsername `
        -adminPassword $adminPassword `
        -dnsLabelPrefix $dnsLabelPrefix `
        -TemplateFile "$HOME/azuredeploy.json"

    Write-Host "Press [ENTER] to continue ..."
    ```

    ---

1. 가상 머신에 RDP를 수행하여 가상 머신이 성공적으로 생성되었는지 확인합니다.

## <a name="clean-up-resources"></a>리소스 정리

Azure 리소스가 더 이상 필요하지 않은 경우 리소스 그룹을 삭제하여 배포한 리소스를 정리합니다.

1. Azure Portal의 왼쪽 메뉴에서 **리소스 그룹** 을 선택합니다.
2. **이름으로 필터링** 필드에서 리소스 그룹 이름을 입력합니다.
3. 해당 리소스 그룹 이름을 선택합니다. 리소스 그룹에 총 6개의 리소스가 표시됩니다.
4. 위쪽 메뉴에서 **리소스 그룹 삭제** 를 선택합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 가상 머신, 가상 네트워크 및 종속 리소스를 만드는 템플릿을 개발하고 배포했습니다. 배포 스크립트를 사용하여 배포 전/후 작업을 수행하는 방법에 대한 자세한 내용은

> [!div class="nextstepaction"]
> [배포 스크립트 사용](./template-tutorial-deployment-script.md)을 참조하세요.
