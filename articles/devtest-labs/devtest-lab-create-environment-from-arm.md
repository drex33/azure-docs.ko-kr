---
title: 템플릿을 사용하여 다중 VM 환경 및 PaaS 리소스 생성
description: Azure Resource Manager 템플릿에서 Azure DevTest Labs에 다중 VM 환경 및 PaaS 리소스를 만드는 방법에 대해 알아봅니다.
ms.topic: how-to
ms.date: 08/12/2020
ms.openlocfilehash: c1a83a5e8f471b68d1c512443371a9a592cfbc88
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128587994"
---
# <a name="create-multi-vm-environments-and-paas-resources-with-azure-resource-manager-templates"></a>Azure Resource Manager 템플릿으로 다중 VM 환경 및 PaaS 리소스 만들기

Azure DevTest Labs 환경을 통해 사용자는 복잡한 인프라를 랩의 범위 내에서 일관된 방식으로 손쉽게 배포할 수 있습니다. [Azure Resource Manager 템플릿](../azure-resource-manager/templates/syntax.md)을 사용하여 DevTest Labs의 리소스 집합으로 환경을 생성할 수 있습니다. 이러한 환경에는 리소스 관리자 템플릿이 생성할 수 있는 모든 Azure 리소스가 포함될 수 있습니다.

[Azure Portal](https://portal.azure.com)을 사용하여 랩에 [가상 머신(VM)을 한 번에 하나씩](devtest-lab-add-vm.md) 쉽게 추가할 수 있습니다. 그러나 다중 계층 웹앱 애플리케이션이 또는 쉐어포인트 팜과 같은 시나리오에서는 여러 VM을 한 번에 생성할 수 있는 메커니즘이 필요합니다. Azure Resource Manager 템플릿을 사용하면 Azure 솔루션의 인프라 및 구성을 정의하고 여러 VM을 일관된 상태로 반복적으로 배포할 수 있습니다.

또한 Azure Resource Manager 템플릿은 다음과 같은 이점을 제공합니다.

- Azure Resource Manager 템플릿은 GitHub 또는 Azure 리포지토리 소스 제어 저장소에서 직접 로드됩니다.
- 사용자는 다른 유형의 [VM 베이스](devtest-lab-comparing-vm-base-image-types.md)에서와 마찬가지로 Azure Portal에서 구성된 Azure Resource Manager 템플릿을 선택하여 환경을 생성할 수 있습니다.
- Azure Resource Manager 템플릿에서 Azure PaaS 리소스 및 IaaS VM을 환경에서 프로비전할 수 있습니다.
- 다른 유형의 베이스에서 생성된 개별 VM뿐만 아니라 랩 내 환경 비용도 추적할 수 있습니다. PaaS 리소스가 생성되고 비용 추적에 표시됩니다. 하지만 VM 자동 종료는 PaaS 리소스에 적용되지 않습니다.

Resource Manager 템플릿을 사용하여 단일 작업에서 많은 랩 리소스를 배포, 업데이트 또는 삭제하는 이점에 대한 자세한 내용은 [Resource Manager 템플릿 사용의 이점](../azure-resource-manager/management/overview.md#the-benefits-of-using-resource-manager)을 참조하세요.

> [!NOTE]
> 리소스 관리자 템플릿을 기반으로 사용하여 랩 VM을 생성할 때 여러 VM 생성과 단일 VM 생성에는 몇 가지 차이점이 있습니다. 자세한 내용은 [가상 머신의 Azure Resource Manager 템플릿 사용](devtest-lab-use-resource-manager-template.md)을 참조하세요.
>

## <a name="use-devtest-labs-public-environments"></a>DevTest Labs 공용 환경 사용
Azure DevTest Labs의 [Azure Resource Manager 템플릿의 공용 리포지토리](https://github.com/Azure/azure-devtestlab/tree/master/Environments)를 사용하면 외부 GitHub 소스에 직접 연결하지 않고도 환경을 구축할 수 있습니다. 이 공용 리포지토리는 생성되는 모든 랩의 Azure Portal에서 사용할 수 있는 아티팩트의 공용 리포지토리와 유사합니다. 환경 리포지토리를 사용하면 입력 매개 변수가 거의 없는 사전 인증 환경 템플릿을 빠르게 시작할 수 있습니다. 이러한 템플릿을 사용하면 랩 내의 PaaS 리소스를 원활하게 시작할 수 있습니다.

공공 리포지토리에서 DevTest Labs 팀과 다른 팀들은 Azure Web Apps, Service Fabric 클러스터 및 개발 쉐어포인트 팜 환경과 같이 자주 사용되는 템플릿을 생성하고 공유했습니다. 이러한 템플릿은 직접 사용하거나 필요에 따라 사용자 지정할 수 있습니다. 자세한 내용은 [DevTest Labs에서 공용 환경 구성 및 사용](devtest-lab-configure-use-public-environments.md)을 참조하세요. 사용자 고유의 템플릿을 생성한 후에는 이 리포지토리에 저장하여 다른 사용자와 공유하거나 자신의 Git 리포지토리를 설정할 수 있습니다.

<a name="configure-your-own-template-repositories"></a>
## <a name="create-your-own-template-repositories"></a>사용자 고유의 템플릿 리포지토리 생성

코드 기반 인프라 및 코드 기반 구성을 사용하는 모범 사례 중 하나로, 소스 제어에서 환경 템플릿을 관리해야 합니다. Azure DevTest Labs는 이 사례를 수행하고 GitHub 또는 Azure 리포지토리에서 직접 모든 Azure Resource Manager 템플릿을 로드합니다. 따라서 테스트 환경에서 프로덕션 환경에 이르기까지 전체 릴리스 주기에서 Resource Manager 템플릿을 사용할 수 있습니다.

Azure Resource Manager 템플릿을 리포지토리에 구성하려면 다음과 같은 몇 가지 규칙을 따라야 합니다.

- 마스터 템플릿 파일 이름을 *azuredeploy.json* 으로 지정해야 합니다.

- 매개 변수 파일에 정의된 매개 변수 값을 사용하려면 매개 변수 파일의 이름을 *azuredeploy.parameters.json* 로 지정해야 합니다.

  DevTest Labs에서 중첩된 템플릿을 자동으로 관리할 수 있도록 매개 변수 `_artifactsLocation` 및 `_artifactsLocationSasToken`을 사용하여 parametersLink URI 값을 생성합니다. 자세한 내용은 [테스트 환경을 위한 중첩 Azure Resource Manager 템플릿 배포](deploy-nested-template-environments.md)를 참조하십시오.

- 메타데이터를 정의하여 *metadata.json* 파일에서 템플릿 표시 이름 및 설명을 다음과 같이 지정할 수 있습니다.

  ```json
  {
    "itemDisplayName": "<your template name>",
    "description": "<description of the template>"
  }
  ```

![주요 Azure Resource Manager 템플릿 파일](./media/devtest-lab-create-environment-from-arm/master-template.png)

## <a name="add-template-repositories-to-the-lab"></a>랩에 템플릿 리포지토리 추가

리포지토리를 생성하고 구성한 후 Azure Portal을 사용하여 해당 리포지토리를 랩에 추가할 수 있습니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. **모든 서비스** 를 선택한 다음, 목록에서 **DevTest Labs** 를 선택합니다.
1. 랩 목록에서 원하는 랩을 선택합니다.
1. 랩의 **개요** 창에서 **구성 및 정책** 을 선택하세요.

   ![구성 및 정책](./media/devtest-lab-create-environment-from-arm/configuration-and-policies-menu.png)

1. **구성 및 정책** 설정 목록에서 **리포지토리** 를 선택하세요. **공용 아티팩트 리포지토리** 는 모든 랩에 자동으로 생성되고 [DevTest Labs 공용 GitHub 리포지토리](https://github.com/Azure/azure-devtestlab)에 연결됩니다.

1. Azure Resource Manager 템플릿 리포지토리를 추가하려면 **추가** 를 선택하세요.

   ![공용 리포지토리](./media/devtest-lab-create-environment-from-arm/public-repo.png)

1. **리포지토리** 창에서 다음 정보를 입력하세요.

   - **이름**: 랩에서 사용할 리포지토리 이름을 입력하세요.
   - **Git 복제 URL**: GitHub 또는 Azure Repos에서 Git HTTPS 복제 URL을 입력합니다.
   - **분기**(선택 사항): Azure Resource Manager 템플릿 정의에 액세스하기 위한 분기 이름을 입력하세요.
   - **개인 액세스 토큰**: 리포지토리에 안전하게 액세스하는 데 사용되는 개인 액세스 토큰을 입력하세요.
     - Azure Repos에서 토큰을 가져오려면 사용자 프로필에서 **사용자 설정** > **보안** > **개인 액세스 토큰** 을 선택하세요.
     - GitHub에서 토큰을 가져오려면 사용자 프로필에서 **설정** >  **개발자 설정** > **개인 액세스 토큰** 을 선택하세요.
   - **폴더 경로**: 아티팩트 정의 또는 Azure Resource Manager 템플릿 정의에 대한 Git 복제 URI와 관련된 폴더 경로를 입력하세요.

1. **저장** 을 선택합니다.

   ![새 리포지토리 추가](./media/devtest-lab-create-environment-from-arm/repo-values.png)

Azure Resource Manager 템플릿을 랩에 추가하면 해당 템플릿을 사용하여 환경을 구축할 수 있습니다.

## <a name="configure-access-rights-for-lab-users"></a>랩 사용자에 대한 액세스 권한 구성

랩 사용자는 기본적으로 **Reader** 역할을 갖고 있으므로 환경 리소스 그룹의 리소스를 변경할 수 없습니다. 예를 들어, 해당 리소스를 중지하거나 시작할 수 없습니다.

랩 사용자가 환경 리소스를 편집할 수 있도록 **Contributor** 역할을 부여하려면 다음 단계를 수행하세요.

1. [Azure Portal](https://portal.azure.com)의 랩 **개요** 창에서 **구성 및 정책** 을 선택한 다음 **랩 설정** 을 선택하세요.

1. **랩 설정** 창에서 **contributor** 를 선택한 다음 **저장** 을 선택하여 랩 사용자에게 쓰기 권한을 부여하세요.

   ![랩 사용자 액세스 권한 구성](./media/devtest-lab-create-environment-from-arm/config-access-rights.png)

다음 섹션에서는 Azure Resource Manager 템플릿에서 환경을 생성하는 과정에 대해 설명합니다.

## <a name="create-environments-from-templates-in-the-azure-portal"></a>Azure Portal 템플릿에서 환경 생성

Azure Resource Manager 템플릿을 Lab에 추가하면 다음 단계를 수행하여 Azure Portal 환경을 생성할 수 있습니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. **모든 서비스** 를 선택한 다음, 목록에서 **DevTest Labs** 를 선택합니다.

1. 랩 목록에서 원하는 랩을 선택하세요.

1. 랩 페이지에서 **추가** 를 선택하세요.

1. **기본 베이스** 창에 사용할 수 있는 기본 이미지가 표시되고 Azure Resource Manager 템플릿이 먼저 나열됩니다. 원하는 Azure Resource Manager 템플릿을 선택하세요.

   ![기본 선택](./media/devtest-lab-create-environment-from-arm/choose-a-base.png)

1. **추가** 창에서 환경 사용자에게 표시할 **환경 이름** 값을 입력하세요.

   Azure Resource Manager 템플릿은 나머지 입력 필드를 정의합니다. *azuredeploy.parameter.json* 템플릿에서 기본값이 정의되는 경우 입력 필드에 해당 값이 표시됩니다.

   *보안 문자열* 형식의 매개 변수의 경우 Azure Key Valut의 암호를 사용할 수 있습니다. 키 자격 증명 모음에 암호를 저장하고 랩 리소스를 생성 시 사용하는 방법에 대한 자세한 내용은 [Azure Key Vault의 암호 저장](devtest-lab-store-secrets-in-key-vault.md)을 참조하세요.  

   ![창 추가](./media/devtest-lab-create-environment-from-arm/add.png)

   > [!NOTE]
   > 다음 매개 변수 값은 템플릿이 지정하는 경우에도 입력 필드에 표시되지 않습니다. 대신, 랩 사용자가 환경을 생성할 때 값을 입력해야 하는 빈 입력 필드가 양식에 표시됩니다.
   >
   > - GEN-UNIQUE
   > - GEN-UNIQUE-[N]
   > - GEN-SSH-PUB-KEY
   > - GEN-PASSWORD

1. **추가** 를 선택하여 환경을 만듭니다.

   환경에서 프로비전을 시작하고 즉시 **내 가상 머신** 목록에 상태가 표시됩니다. 랩에서 자동으로 새 리소스 그룹을 생성하여 Azure Resource Manager 템플릿에 정의된 모든 리소스를 프로비전합니다.

1. 환경이 생성되면 **내 가상 머신** 목록에서 환경을 선택하여 리소스 그룹 창을 열고 환경에 프로비전된 모든 리소스를 찾아보세요.

   ![환경 리소스](./media/devtest-lab-create-environment-from-arm/all-environment-resources.png)

   또한 환경을 확장하여 환경에서 프로비전된 VM 목록만 볼 수 있습니다.

   ![내 가상 머신 목록](./media/devtest-lab-create-environment-from-arm/my-vm-list.png)

1. 환경을 선택하여 아티팩트 적용, 데이터 디스크 연결, 자동 종료 시간 변경 등 사용 가능한 작업을 확인합니다.

   ![환경 작업](./media/devtest-lab-create-environment-from-arm/environment-actions.png)

<a name="automate-deployment-of-environments"></a>
## <a name="automate-environment-creation-with-powershell"></a>PowerShell을 통해 환경 생성 자동화

Azure Portal을 사용하여 랩에 단일 환경을 추가할 수 있지만, 개발 또는 테스트 시나리오에서 여러 환경을 생성해야 하는 경우 자동 배포가 더 나은 환경을 제공합니다.

계속하기 전에 생성할 리소스를 정의하는 Azure Resource Manager 템플릿이 있는지 확인하세요. [Git 리포지토리](#configure-your-own-template-repositories)에 템플릿을 추가 및 구성하고 [리포지토리를 랩에 추가하세요](#add-template-repositories-to-the-lab).

다음 샘플 스크립트는 랩에서 환경을 생성합니다. 주석을 사용하면 스크립트를 더 잘 이해할 수 있습니다.

1. 다음 샘플 PowerShell 스크립트를 하드 드라이브에 *deployEnv.ps1* 로 저장합니다.

   [!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

   ```powershell
   #Requires -Module Az.Resources

   [CmdletBinding()]

   param (
   # ID of the Azure Subscription for the lab
   [string] [Parameter(Mandatory=$true)] $SubscriptionId,

   # Name of the existing lab in which to create the environment
   [string] [Parameter(Mandatory=$true)] $LabName,

   # Name of the connected repository in the lab
   [string] [Parameter(Mandatory=$true)] $RepositoryName,

   # Name of the template (folder name in the Git repository)
   [string] [Parameter(Mandatory=$true)] $TemplateName,

   # Name of the environment to be created in the lab
   [string] [Parameter(Mandatory=$true)] $EnvironmentName,

   # The parameters to be passed to the template. Each parameter is prefixed with "-param_".
   # For example, if the template has a parameter named "TestVMName" with a value of "MyVMName",
   # the string in $Params will have the form: -param_TestVMName MyVMName.
   # This convention allows the script to dynamically handle different templates.
   [Parameter(ValueFromRemainingArguments=$true)]
       $Params
   )

   # Sign in to Azure.
   # Comment out the following statement to completely automate the environment creation.
   Connect-AzAccount

   # Select the subscription that has the lab.  
   Set-AzContext -SubscriptionId $SubscriptionId | Out-Null

   # Get information about the user, specifically the user ID, which is used later in the script.  
   $UserId = $((Get-AzADUser -UserPrincipalName ((Get-AzContext).Account).Id).Id)

   # Get information about the lab, such as lab location.
   $lab = Get-AzResource -ResourceType "Microsoft.DevTestLab/labs" -Name $LabName
   if ($lab -eq $null) { throw "Unable to find lab $LabName in subscription $SubscriptionId." }

   # Get information about the repository in the lab.
   $repository = Get-AzResource -ResourceGroupName $lab.ResourceGroupName `
       -ResourceType 'Microsoft.DevTestLab/labs/artifactsources' `
       -ResourceName $LabName `
       -ApiVersion 2016-05-15 `
       | Where-Object { $RepositoryName -in ($_.Name, $_.Properties.displayName) } `
       | Select-Object -First 1
   if ($repository -eq $null) { throw "Unable to find repository $RepositoryName in lab $LabName." }

   # Get information about the Resource Manager template base for the environment.
   $template = Get-AzResource -ResourceGroupName $lab.ResourceGroupName `
       -ResourceType "Microsoft.DevTestLab/labs/artifactSources/armTemplates" `
       -ResourceName "$LabName/$($repository.Name)" `
       -ApiVersion 2016-05-15 `
       | Where-Object { $TemplateName -in ($_.Name, $_.Properties.displayName) } `
       | Select-Object -First 1
   if ($template -eq $null) { throw "Unable to find template $TemplateName in lab $LabName." }

   # Build the template parameters with parameter name and values.  
   $parameters = Get-Member -InputObject $template.Properties.contents.parameters -MemberType NoteProperty | Select-Object -ExpandProperty Name
   $templateParameters = @()

   # Extract the custom parameters from $Params and format as name/value pairs.
   $Params | ForEach-Object {
       if ($_ -match '^-param_(.*)' -and $Matches[1] -in $parameters) {
           $name = $Matches[1]                
       } elseif ( $name ) {
           $templateParameters += @{ "name" = "$name"; "value" = "$_" }
           $name = $null #reset name variable
       }
   }

   # Once name/value pairs are isolated, create an object to hold the necessary template properties.
   $templateProperties = @{ "deploymentProperties" = @{ "armTemplateId" = "$($template.ResourceId)"; "parameters" = $templateParameters }; }

   # Now, create or deploy the environment in the lab by using the New-AzResource command.
   New-AzResource -Location $Lab.Location `
       -ResourceGroupName $lab.ResourceGroupName `
       -Properties $templateProperties `
       -ResourceType 'Microsoft.DevTestLab/labs/users/environments' `
       -ResourceName "$LabName/$UserId/$EnvironmentName" `
       -ApiVersion '2016-05-15' -Force

   Write-Output "Environment $EnvironmentName completed."
   ```

1. SubscriptionId, LabName, ResourceGroupName, RepositoryName, TemplateName(Git 리포지토리의 폴더) 및 EnvironmentName에 대한 특정 값을 사용하여 스크립트를 다음과 같이 실행하세요.

   ```powershell
   ./deployenv.ps1 -SubscriptionId "000000000-0000-0000-0000-0000000000000" -LabName "mydevtestlab" -ResourceGroupName "mydevtestlabRG000000" -RepositoryName "myRepository" -TemplateName "My Environment template name" -EnvironmentName "myGroupEnv"
   ```

또한 Azure CLI를 사용하여 Resource Manager 템플릿을 통해 리소스를 배포할 수도 있습니다. 자세한 내용은 [Resource Manager 템플릿과 Azure CLI로 리소스 배포](../azure-resource-manager/templates/deploy-cli.md)를 참조하세요.

> [!NOTE]
> 랩 소유자 권한을 가진 사용자만 Azure PowerShell을 사용하여 Resource Manager 템플릿에서 VM을 만들 수 있습니다. Resource Manager 템플릿을 사용하여 VM 생성을 자동화하고 사용자 권한만 있는 경우 CLI 명령 [azlab vm create](/cli/azure/lab/vm#az_lab_vm_create)를 사용할 수 있습니다.

## <a name="resource-manager-template-limitations-in-devtest-labs"></a>DevTest Labs의 Resource Manager 템플릿 제한

DevTest Labs에서 Resource Manager 템플릿을 사용하는 경우 다음과 같은 제한 사항을 고려하세요.

- Resource Manager 템플릿에서 생성된 랩 VM에서는 수식 또는 사용자 지정 이미지를 생성할 수 없습니다.

- 대부분의 정책은 Resource Manager 템플릿 배포 시 평가되지 않습니다.

예를 들어 사용자가 5개의 VM만 생성할 수 있는 랩 정책이 있을 수 있습니다. 그러나 사용자는 수십 개의 VM을 만드는 Resource Manager 템플릿을 배포할 수 있습니다. 평가되지 않는 정책은 다음과 같습니다.

  - 사용자당 VM 수

  - 랩 사용자당 프리미엄 VM 수

  - 랩 사용자당 프리미엄 디스크 수

## <a name="next-steps"></a>다음 단계
- VM을 생성한 후에는 VM의 관리 창에서 **연결** 를 선택하여 VM에 연결할 수 있습니다.
- 랩의 **내 가상 머신** 목록에서 환경을 선택하여 환경에서 리소스를 보고 관리합니다.
- [Azure 빠른 시작 템플릿 갤러리의 Azure Resource Manager 템플릿](https://github.com/Azure/azure-quickstart-templates)을 탐색하세요.