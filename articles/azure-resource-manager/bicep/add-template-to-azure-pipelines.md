---
title: Azure Pipelines 및 Bicep 파일을 사용한 CI/CD
description: Bicep 파일을 사용하여 Azure Pipelines에서 연속 통합을 구성하는 방법을 설명합니다. Azure CLI 작업을 사용하여 Bicep 파일을 배포하는 방법을 보여 줍니다.
author: mumian
ms.topic: conceptual
ms.author: jgao
ms.date: 06/23/2021
ms.openlocfilehash: 1c8817fcc6a0cf24a0983f2df60f02cc42b79200
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124764377"
---
# <a name="integrate-bicep-with-azure-pipelines"></a>Azure Pipelines와 Bicep 통합

CI/CD(연속 통합 및 지속적인 배포)를 위해 Azure Pipelines와 Bicep 파일을 통합할 수 있습니다. 이 문서에서는 Azure CLI 파이프라인 작업을 사용하여 Bicep 파일을 배포하는 방법을 알아봅니다.

## <a name="prepare-your-project"></a>프로젝트 준비

이 문서에서는 Bicep 파일 및 Azure DevOps 조직이 파이프라인을 만들 준비가 되었다고 가정합니다. 다음 단계에서는 준비되었는지 확인하는 방법을 보여 줍니다.

* Azure DevOps 조직이 있습니다. 계정이 없는 경우 [체험 계정을 만듭니다](/azure/devops/pipelines/get-started/pipelines-sign-up). 팀에 이미 Azure DevOps 조직이 있는 경우 사용할 Azure DevOps 프로젝트의 관리자인지 확인합니다.

* Azure 구독에 대한 [서비스 연결](/azure/devops/pipelines/library/connect-to-azure)을 구성했습니다. 파이프라인의 작업은 서비스 주체의 ID로 실행됩니다. 연결을 만드는 단계는 [DevOps 프로젝트 만들기](../templates/deployment-tutorial-pipeline.md#create-a-devops-project)를 참조하세요.

* 프로젝트의 인프라를 정의하는 [Bicep 파일](./quickstart-create-bicep-use-visual-studio-code.md)이 있습니다.

## <a name="create-pipeline"></a>파이프라인 만들기

1. 이전에 파이프라인을 추가하지 않은 경우 새 파이프라인을 만들어야 합니다. Azure DevOps 조직에서 **파이프라인** 및 **새 파이프라인** 을 선택합니다.

   ![새 파이프라인 추가](./media/add-template-to-azure-pipelines/new-pipeline.png)

1. 코드를 저장할 위치를 지정합니다. 다음 그림에서는 **Azure Repos Git** 선택을 보여 줍니다.

   ![코드 원본 선택](./media/add-template-to-azure-pipelines/select-source.png)

1. 해당 원본에서 프로젝트에 대한 코드를 포함하는 리포지토리를 선택합니다.

   ![리포지토리 선택](./media/add-template-to-azure-pipelines/select-repo.png)

1. 만들 파이프라인의 형식을 선택합니다. **시작 파이프라인** 을 선택할 수 있습니다.

   ![파이프라인 선택](./media/add-template-to-azure-pipelines/select-pipeline.png)

Azure PowerShell 작업 또는 복사 파일을 추가하고 작업을 배포할 준비가 되었습니다.

## <a name="azure-cli-task"></a>Azure CLI 작업

다음 YAML 파일은 [Azure CLI 작업](/azure/devops/pipelines/tasks/deploy/azure-cli)을 사용하여 리소스 그룹을 만들고 Bicep 파일을 배포합니다.

```yml
trigger:
- master

name: Deploy Bicep files

variables:
  vmImageName: 'ubuntu-latest'

  azureServiceConnection: '<your-connection-name>'
  resourceGroupName: '<your-resource-group-name>'
  location: '<your-resource-group-location>'
  templateFile: './azuredeploy.bicep'
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

* `azureSubscription`, 만든 서비스 연결의 이름을 제공합니다.  [프로젝트 준비](#prepare-your-project)를 참조하세요.
* `scriptType`, **bash** 를 사용합니다.
* `scriptLocation`, **inlineScript** 또는 **scriptPath** 를 사용합니다. **scriptPath** 를 지정하는 경우 `scriptPath` 매개 변수도 지정해야 합니다.
* `inlineScript`, 스크립트 줄을 지정합니다.  이 샘플에 제공된 스크립트는 *azuredeploy.bicep* 이라는 bicep 파일을 빌드하고 리포지토리의 루트에 존재합니다.

## <a name="next-steps"></a>다음 단계

* Azure Pipelines와 함께 Bicep를 사용 하는 방법에 대 한 자세한 내용 및 실습 지침은 **Microsoft Learn** 에서 [Azure Pipelines를 사용 하 여 첫 번째 Bicep 배포 파이프라인 빌드](/learn/modules/build-first-bicep-deployment-pipeline-using-azure-pipelines/) 를 참조 하세요.
* 파이프라인에서 가상 작업을 사용하려면 [파이프라인에서 가상을 사용하여 ARM 템플릿 테스트](https://4bes.nl/2021/03/06/test-arm-templates-with-what-if/)를 참조하세요.
* GitHub Actions와 함께 Bicep 파일을 사용하는 방법에 대한 자세한 내용은 [GitHub Actions를 사용하여 Bicep 파일 배포](./deploy-github-actions.md)를 참조하세요.