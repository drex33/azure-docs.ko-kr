---
title: Azure Pipelines 및 Bicep 파일을 사용한 CI/CD
description: 이 빠른 시작에서는 Bicep 파일을 사용하여 Azure Pipelines에서 연속 통합을 구성하는 방법에 대해 알아봅니다. Azure CLI 작업을 사용하여 Bicep 파일을 배포하는 방법을 보여 줍니다.
author: mumian
ms.topic: quickstart
ms.author: jgao
ms.date: 11/16/2021
ms.custom: mode-other
ms.openlocfilehash: 9de29d13e4ac487e1c833d135883913e10329355
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133054335"
---
# <a name="quickstart-integrate-bicep-with-azure-pipelines"></a>빠른 시작: Azure Pipelines와 Bicep 통합

이 빠른 시작에서는 CI/CD(연속 통합 및 지속적인 배포)를 위해 Azure Pipelines와 Bicep 파일을 통합하는 방법을 보여줍니다.

Bicep 파일을 배포하는 데 필요한 파이프라인 작업에 대한 간략한 소개를 제공합니다. 파이프라인 및 프로젝트 설정에 대한 자세한 단계는 **Microsoft Learn** 에서 [Bicep 및 Azure Pipelines를 사용하여 Azure 리소스 배포](/learn/paths/bicep-azure-pipelines/)를 참조하세요.

## <a name="prerequisites"></a>필수 구성 요소

Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

Azure DevOps 조직이 필요합니다. 계정이 없는 경우 [체험 계정을 만듭니다](/azure/devops/pipelines/get-started/pipelines-sign-up). 팀에 이미 Azure DevOps 조직이 있는 경우 사용할 Azure DevOps 프로젝트의 관리자인지 확인합니다.

Azure 구독에 대한 [서비스 연결](/azure/devops/pipelines/library/connect-to-azure)을 구성해야 합니다. 파이프라인의 작업은 서비스 주체의 ID로 실행됩니다. 연결을 만드는 단계는 [DevOps 프로젝트 만들기](../templates/deployment-tutorial-pipeline.md#create-a-devops-project)를 참조하세요.

프로젝트의 인프라를 정의하는 [Bicep 파일](./quickstart-create-bicep-use-visual-studio-code.md)이 필요합니다. 이 파일은 리포지토리에 있습니다.

## <a name="create-pipeline"></a>파이프라인 만들기

1. Azure DevOps 조직에서 **파이프라인** 및 **새 파이프라인** 을 선택합니다.

   ![새 파이프라인 추가](./media/add-template-to-azure-pipelines/new-pipeline.png)

1. 코드를 저장할 위치를 지정합니다.

   ![코드 원본 선택](./media/add-template-to-azure-pipelines/select-source.png)

1. 프로젝트에 대한 코드를 포함하는 리포지토리를 선택합니다.

   ![리포지토리 선택](./media/add-template-to-azure-pipelines/select-repo.png)

1. 만들 파이프라인 유형에 **시작 파이프라인** 을 선택합니다.

   ![파이프라인 선택](./media/add-template-to-azure-pipelines/select-pipeline.png)

## <a name="azure-cli-task"></a>Azure CLI 작업

시작 파이프라인을 다음 YAML로 바꿉니다. [Azure CLI 작업](/azure/devops/pipelines/tasks/deploy/azure-cli)을 사용하여 리소스 그룹을 만들고 Bicep 파일을 배포합니다.

```yml
trigger:
- master

name: Deploy Bicep files

variables:
  vmImageName: 'ubuntu-latest'

  azureServiceConnection: '<your-connection-name>'
  resourceGroupName: 'exampleRG'
  location: '<your-resource-group-location>'
  templateFile: './main.bicep'
pool:
  vmImage: $(vmImageName)

steps:
- task: AzureCLI@2
  inputs:
    azureSubscription: $(azureServiceConnection)
    scriptType: bash
    scriptLocation: inlineScript
    inlineScript: |
      az --version
      az group create --name $(resourceGroupName) --location $(location)
      az deployment group create --resource-group $(resourceGroupName) --template-file $(templateFile)
```

Azure CLI 작업은 다음 입력을 사용합니다.

* `azureSubscription`, 만든 서비스 연결의 이름을 제공합니다.  [필수 조건](#prerequisites)을 참조하세요.
* `scriptType`, **bash** 를 사용합니다.
* `scriptLocation`, **inlineScript** 또는 **scriptPath** 를 사용합니다. **scriptPath** 를 지정하는 경우 `scriptPath` 매개 변수도 지정해야 합니다.
* `inlineScript`, 스크립트 줄을 지정합니다.  샘플에 제공된 스크립트는 *main.bicep* 이라는 Bicep 파일을 배포합니다.

**저장** 을 선택합니다. 빌드 파이프라인이 자동으로 실행됩니다. 빌드 파이프라인의 요약으로 돌아가서 상태를 확인합니다.

## <a name="clean-up-resources"></a>리소스 정리

Azure 리소스가 더 이상 필요 없으면 Azure CLI 또는 Azure PowerShell을 사용하여 빠른 시작 리소스 그룹을 삭제합니다.

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli
az group delete --name exampleRG
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell
Remove-AzResourceGroup -Name exampleRG
```

---

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [GitHub Actions를 사용하여 Bicep 파일 배포](deploy-github-actions.md)
