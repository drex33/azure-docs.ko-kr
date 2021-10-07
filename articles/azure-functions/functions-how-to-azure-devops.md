---
title: Azure DevOps를 사용하여 지속적으로 함수 앱 코드 업데이트
description: Azure Functions를 대상으로 하는 Azure DevOps 파이프라인 설정 방법에 대해 알아보세요.
author: craigshoemaker
ms.topic: conceptual
ms.date: 04/18/2019
ms.author: cshoe
ms.custom: devx-track-csharp, devx-track-python
ms.openlocfilehash: 6e35b814b8b6fce5f223200f1774b7da2fee7a8c
ms.sourcegitcommit: e82ce0be68dabf98aa33052afb12f205a203d12d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/07/2021
ms.locfileid: "129660973"
---
# <a name="continuous-delivery-by-using-azure-devops"></a>Azure DevOps를 사용한 지속적인 업데이트

[Azure Pipeline](/azure/devops/pipelines/)을 사용하여 Azure Functions 앱에 자동으로 함수를 배포할 수 있습니다.

파이프라인을 정의하는 두 가지 옵션이 있습니다.

- **YAML 파일** : YAML 파일은 파이프라인을 설명합니다. 파일은 빌드 단계 섹션과 릴리스 섹션이 포함할 수 있습니다. YAML 파일은 앱과 동일한 리포지토리에 있어야 합니다.
- **템플릿**: 템플릿은 앱을 빌드하거나 배포하는 사전 제작 작업입니다.

## <a name="yaml-based-pipeline"></a>YAML 기반 파이프라인

YAML 기반 파이프라인을 생성하려면 먼저 앱을 빌드한 다음 배포하세요.

### <a name="build-your-app"></a>앱 빌드

Azure Pipeline에서 앱을 빌드하는 방법은 앱의 프로그래밍 언어에 따라 달라집니다. 각 언어에는 배포 아티팩트를 생성하는 특정 빌드 단계가 있습니다. 배포 아티팩트는 Azure에서 함수 앱을 배포하는 데 사용됩니다.

# <a name="c"></a>[C\#](#tab/csharp)

다음 샘플을 사용하여 NET 앱을 빌드하는 YAML 파일을 생성할 수 있습니다:

```yaml
pool:
      vmImage: 'VS2017-Win2016'
steps:
- script: |
    dotnet restore
    dotnet build --configuration Release
- task: DotNetCoreCLI@2
  inputs:
    command: publish
    arguments: '--configuration Release --output publish_output'
    projects: '*.csproj'
    publishWebProjects: false
    modifyOutputPath: false
    zipAfterPublish: false
- task: ArchiveFiles@2
  displayName: "Archive files"
  inputs:
    rootFolderOrFile: "$(System.DefaultWorkingDirectory)/publish_output"
    includeRootFolder: false
    archiveFile: "$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip"
- task: PublishBuildArtifacts@1
  inputs:
    PathtoPublish: '$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip'
    artifactName: 'drop'
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

다음 샘플을 사용하여 JavaScript 앱을 빌드하는 YAML 파일을 생성할 수 있습니다:

```yaml
pool:
      vmImage: ubuntu-16.04 # Use 'VS2017-Win2016' if you have Windows native +Node modules
steps:
- bash: |
    if [ -f extensions.csproj ]
    then
        dotnet build extensions.csproj --output ./bin
    fi
    npm install 
    npm run build --if-present
    npm prune --production
- task: ArchiveFiles@2
  displayName: "Archive files"
  inputs:
    rootFolderOrFile: "$(System.DefaultWorkingDirectory)"
    includeRootFolder: false
    archiveFile: "$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip"
- task: PublishBuildArtifacts@1
  inputs:
    PathtoPublish: '$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip'
    artifactName: 'drop'
```

# <a name="python"></a>[Python](#tab/python)

다음 샘플의 사용하여 특정한 Python 버전용 앱을 빌드하는 YAML 파일을 생성할 수 있습니다. Python은 Linux에서 실행되는 함수 앱에만 지원됩니다.

**버전 3.7**

```yaml
pool:
  vmImage: ubuntu-16.04
steps:
- task: UsePythonVersion@0
  displayName: "Setting python version to 3.7 as required by functions"
  inputs:
    versionSpec: '3.7'
    architecture: 'x64'
- bash: |
    if [ -f extensions.csproj ]
    then
        dotnet build extensions.csproj --output ./bin
    fi
    pip install --target="./.python_packages/lib/site-packages" -r ./requirements.txt
- task: ArchiveFiles@2
  displayName: "Archive files"
  inputs:
    rootFolderOrFile: "$(System.DefaultWorkingDirectory)"
    includeRootFolder: false
    archiveFile: "$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip"
- task: PublishBuildArtifacts@1
  inputs:
    PathtoPublish: '$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip'
    artifactName: 'drop'
```

**버전 3.6**

```yaml
pool:
  vmImage: ubuntu-16.04
steps:
- task: UsePythonVersion@0
  displayName: "Setting python version to 3.6 as required by functions"
  inputs:
    versionSpec: '3.6'
    architecture: 'x64'
- bash: |
    if [ -f extensions.csproj ]
    then
        dotnet build extensions.csproj --output ./bin
    fi
    pip install --target="./.python_packages/lib/python3.6/site-packages" -r ./requirements.txt
- task: ArchiveFiles@2
  displayName: "Archive files"
  inputs:
    rootFolderOrFile: "$(System.DefaultWorkingDirectory)"
    includeRootFolder: false
    archiveFile: "$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip"
- task: PublishBuildArtifacts@1
  inputs:
    PathtoPublish: '$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip'
    artifactName: 'drop'
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

다음 샘플을 사용하여 PowerShell 앱을 패키징하는 YAML 파일을 생성하여 할 수 있습니다. PowerShell은 Windows Azure Functions에만 지원됩니다.

```yaml
pool:
      vmImage: 'VS2017-Win2016'
steps:
- task: ArchiveFiles@2
  displayName: "Archive files"
  inputs:
    rootFolderOrFile: "$(System.DefaultWorkingDirectory)"
    includeRootFolder: false
    archiveFile: "$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip"
- task: PublishBuildArtifacts@1
  inputs:
    PathtoPublish: '$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip'
    artifactName: 'drop'
```

---

### <a name="deploy-your-app"></a>앱 배포

호스팅 OS에 따라 YAML 파일에 다음 YAML 샘플 중 하나를 포함해야 합니다.

# <a name="windows"></a>[Windows](#tab/windows)

다음 코드 조각을 사용하여 Windows 함수 앱을 배포할 수 있습니다.

```yaml
steps:
- task: AzureFunctionApp@1
  inputs:
    azureSubscription: '<Azure service connection>'
    appType: functionApp
    appName: '<Name of function app>'
    #Uncomment the next lines to deploy to a deployment slot
    #deployToSlotOrASE: true
    #resourceGroupName: '<Resource Group Name>'
    #slotName: '<Slot name>'
```

# <a name="linux"></a>[Linux](#tab/linux)

다음 코드 조각을 사용하여 Linux 힘수 앱을 배포할 수 있습니다.

```yaml
steps:
- task: AzureFunctionApp@1
  inputs:
    azureSubscription: '<Azure service connection>'
    appType: functionAppLinux
    appName: '<Name of function app>'
    #Uncomment the next lines to deploy to a deployment slot
    #Note that deployment slots is not supported for Linux Dynamic SKU
    #deployToSlotOrASE: true
    #resourceGroupName: '<Resource Group Name>'
    #slotName: '<Slot name>'
```
---

## <a name="template-based-pipeline"></a>템플릿 기반 파이프라인

Azure DevOps의 템플릿은 앱을 빌드하거나 배포하는 사전 정의 작업 그룹입니다.

### <a name="build-your-app"></a>앱 빌드

Azure Pipeline에서 앱을 빌드하는 방법은 앱의 프로그래밍 언어에 따라 달라집니다. 각 언어에는 배포 아티팩트를 생성하는 특정 빌드 단계가 있습니다. 배포 아티팩트는 Azure에서 함수 앱을 업데이트하는 데 사용됩니다.

기본 제공 빌드 템플릿을 사용하려면 새 빌드 파이프라인을 생성할 때 **클래식 편집기 사용** 을 선택하여 디자이너 템플릿을 사용해 파이프라인을 생성합니다.

![Azure Pipeline 클래식 편집기 선택](media/functions-how-to-azure-devops/classic-editor.png)

코드 소스를 구성한 후 Azure Functions 빌드 템플릿을 검색합니다. 앱 언어와 일치하는 템플릿을 선택합니다.

![Azure Functions 빌드 템플릿 선택](media/functions-how-to-azure-devops/build-templates.png)

경우에 따라 빌드 아티팩트에 특정 폴더 구조체가 있습니다. **보관 경로에 루트 폴더 이름 접두사 추가** 확인란을 선택해야 할 수 있습니다.

![루트 폴더 이름 접두사 추가 옵션](media/functions-how-to-azure-devops/prepend-root-folder.png)

#### <a name="javascript-apps"></a>JavaScript 앱

JavaScript 앱이 Windows 네이티브 모듈에 대한 종속성이 있는 경우 에이전트 풀 버전을 **호스팅 VS2017** 로 업데이트해야 합니다.

![에이전트 풀 버전 업데이트](media/functions-how-to-azure-devops/change-agent.png)

### <a name="deploy-your-app"></a>앱 배포

새 릴리스 파이프라인을 생성할 때 Azure Functions 릴리스 템플릿을 검색합니다.

![Azure Functions 릴리스 템플릿 검색](media/functions-how-to-azure-devops/release-template.png)

## <a name="create-a-build-pipeline-by-using-the-azure-cli"></a>Azure CLI를 사용하여 빌드 파이프라인 생성

Azure에서 빌드 파이프라인을 생성하려면 `az functionapp devops-pipeline create` [명령](/cli/azure/functionapp/devops-pipeline#az_functionapp_devops_pipeline_create)을 사용하세요. 빌드 파이프라인은 리포지토리에서 만들어진 모든 코드 변경을 빌드하고 릴리스하기 위해 생성됩니다. 명령어는 빌드 및 릴리스 파이프라인을 정의하는 새 YAML 파일을 생성한 다음 이를 사용자의 리포지토리에 커밋합니다. 이 명령의 사전 요구 사항은 코드의 위치에 따라 달라집니다.

- 코드가 GitHub에 있는 경우:

    - 구독에 대한 **쓰기** 사용 권한이 있어야 합니다.

    - Azure DevOps의 프로젝트 관리자여야 합니다.

    - 충분한 권한이 있는 GitHub 개인용 액세스 토큰(PAT)를 생성할 수 있는 사용 권한이 있어야 합니다. 자세한 내용은 [GitHub PAT 사용 권한 요구 사항](/azure/devops/pipelines/repos/github#repository-permissions-for-personal-access-token-pat-authentication)을 참조하세요.

    - 자동 생성된 YAML 파일을 커밋하려면 GitHub 리포지토리의 기본 분기에 커밋할 수 있는 권한이 있어야 합니다.

- 코드가 Azure Repos에 있는 경우:

    - 구독에 대한 **쓰기** 사용 권한이 있어야 합니다.

    - Azure DevOps의 프로젝트 관리자여야 합니다.

## <a name="next-steps"></a>다음 단계

- [Azure Functions 개요](functions-overview.md)를 검토하세요.
- [Azure DevOps 개요](/azure/devops/pipelines/)를 검토하세요.
