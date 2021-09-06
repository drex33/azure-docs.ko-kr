---
title: 단일 테넌트 Azure Logic Apps의 DevOps 설정
description: 단일 테넌트 Azure Logic Apps 워크플로에 대한 DevOps 배포를 설정하는 방법입니다.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, azla
ms.topic: how-to
ms.date: 08/11/2021
ms.openlocfilehash: 010fbfc3b6a2df9c8cdca1221fb4f25a5d288d70
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122536448"
---
# <a name="set-up-devops-deployment-for-single-tenant-azure-logic-apps"></a>단일 테넌트 Azure Logic Apps의 DevOps 배포 설정

이 문서에서는 DevOps 도구 및 프로세스를 사용하여 Visual Studio Code에서 인프라에 단일 테넌트 기반 논리 앱 프로젝트를 배포하는 방법을 보여줍니다. 배포 시 GitHub과 Azure DevOps 중 어떤 것을 선호하는지 여부에 따라 시나리오에 가장 적합한 경로와 도구를 선택합니다. 예제 논리 앱 프로젝트와 GitHub 또는 Azure DevOps를 사용하는 Azure 배포 예제가 포함된 샘플을 사용할 수 있습니다. 단일 테넌트용 DevOps에 대한 자세한 내용은 [단일 테넌트 Azure Logic Apps의 DevOps 배포 개요](devops-deployment-single-tenant-azure-logic-apps.md)를 참조하세요.

## <a name="prerequisites"></a>필수 구성 요소

- 활성 구독이 있는 Azure 계정. Azure 구독이 아직 없는 경우 [무료 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

- [Visual Studio Code 및 Azure Logic Apps(표준) 확장](create-single-tenant-workflows-visual-studio-code.md#prerequisites)을 사용하여 만든 단일 테넌트 기반 논리 앱 프로젝트입니다.

  논리 앱 프로젝트 또는 인프라를 아직 설정하지 않은 경우 원본 및 배포 옵션에 따라 포함된 샘플 프로젝트를 사용하여 예제 앱 및 인프라를 배포할 수 있습니다. 이러한 샘플 프로젝트와 예제 논리 앱을 실행하기 위해 포함된 리소스에 대한 자세한 내용은 [인프라 배포](#deploy-infrastructure)를 참조하세요.

- Azure에 배포하려는 경우 Azure에서 만든 기존 **논리 앱(표준)** 리소스가 필요합니다. 빈 논리 앱 리소스를 빠르게 만들려면 [단일 테넌트 기반 논리 앱 워크플로 만들기 - 포털](create-single-tenant-workflows-azure-portal.md)을 검토합니다.

<a name="deploy-infrastructure"></a>

## <a name="deploy-infrastructure-resources"></a>인프라 리소스 배포

논리 앱 프로젝트 또는 인프라를 아직 설정하지 않은 경우 사용할 원본 및 배포 옵션에 따라 다음 샘플 프로젝트를 사용하여 예제 앱 및 인프라를 배포할 수 있습니다.

- [단일 테넌트 Azure Logic Apps의 GitHub 샘플](https://github.com/Azure/logicapps/tree/master/github-sample)

  이 샘플에는 단일 테넌트 Azure Logic Apps에 대한 예제 논리 앱 프로젝트와 Azure 배포 및 GitHub Actions에 대한 예제가 포함되어 있습니다.

- [단일 테넌트 Azure Logic Apps의 Azure DevOps 샘플](https://github.com/Azure/logicapps/tree/master/azure-devops-sample)

  이 샘플에는 단일 테넌트 Azure Logic Apps에 대한 예제 논리 앱 프로젝트와 Azure 배포 및 Azure Pipelines에 대한 예제가 포함되어 있습니다.
  
두 샘플 모두 논리 앱이 실행하는 데 사용하는 다음 리소스를 포함합니다.

| 리소스 이름 | 필수 | 설명 |
|---------------|----------|-------------|
| 논리 앱(표준) | 예 | 이 Azure 리소스에는 단일 테넌트 Azure Logic Apps에서 실행하는 워크플로가 포함되어 있습니다. |
| Functions 프리미엄 또는 App Service 호스팅 계획 | 예 | 이 Azure 리소스는 컴퓨팅, 처리, 스토리지, 네트워킹 등 논리 앱을 실행하는 데 사용할 호스팅 리소스를 지정합니다. <p><p>**중요**: 현재 환경에서 **논리 앱(표준)** 리소스에는 Functions 프리미엄 호스팅 계획을 기반으로 하는 [**워크플로 표준** 호스팅 계획](logic-apps-pricing.md#standard-pricing)이 필요합니다. |
| Azure Storage 계정 | 예, 상태 저장 및 상태 비저장 워크플로 모두에 대해 | 이 Azure 리소스는 메타데이터, 액세스 제어용 키, 상태, 입력, 출력, 실행 기록 및 워크플로에 대한 기타 정보를 저장합니다. |
| Application Insights | 선택 사항 | 이 Azure 리소스는 워크플로에 대한 모니터링 기능을 제공합니다. |
| API 연결 | 선택 사항(없는 경우) | 이러한 Azure 리소스는 워크플로가 Office 365, SharePoint 등과 같은 관리 커넥터 작업을 실행하는 데 사용하는 모든 관리되는 API 연결을 정의합니다. <p><p>**중요**: 논리 앱 프로젝트에서 **connections.js** 파일은 관리되는 API 연결 및 워크플로에서 사용하는 Azure Functions에 대한 메타데이터, 엔드포인트 및 키를 포함합니다. 각 환경에 대해 서로 다른 연결과 함수를 사용하려면 이 **connections.json** 파일을 매개 변수화하고 엔드포인트를 업데이트해야 합니다. <p><p>자세한 내용은 [API 연결 리소스 및 액세스 정책](#api-connection-resources)을 검토하세요. |
| ARM(Azure Resource Manager) 템플릿 | 선택 사항 | 이 Azure 리소스는 재사용하거나 [내보낼](../azure-resource-manager/templates/template-tutorial-export-template.md) 수 있는 기준 인프라 배포를 정의합니다. 템플릿에는 관리되는 API 연결을 사용하는 데 필요한 액세스 정책도 포함되어 있습니다. <p><p>**중요**: ARM 템플릿 내보내기에는 워크플로에서 사용하는 API 연결 리소스에 대한 모든 관련 매개 변수가 포함되지는 않습니다. 자세한 내용은 [API 연결 매개 변수 찾기](#find-api-connection-parameters)를 검토하세요. |
||||

<a name="api-connection-resources"></a>

## <a name="api-connection-resources-and-access-policies"></a>API 연결 리소스 및 액세스 정책

단일 테넌트 Azure Logic Apps에서 워크플로의 모든 관리 또는 API 연결 리소스에는 연결된 액세스 정책이 필요합니다. 이 정책에는 관리되는 커넥터 인프라에 액세스하기 위한 올바른 권한을 제공하기 위해 논리 앱의 ID가 필요합니다. 포함된 샘플 프로젝트에는 이러한 액세스 정책을 비롯하여 필요한 모든 인프라 리소스를 포함하는 ARM 템플릿이 포함되어 있습니다.

다음 다이어그램에서는 논리 앱 프로젝트와 인프라 리소스 간의 종속성을 보여줍니다.

![단일 테넌트 Azure Logic Apps 모델의 논리 앱 프로젝트에 대한 인프라 종속성을 보여주는 개념 다이어그램](./media/set-up-devops-deployment-single-tenant-azure-logic-apps/infrastructure-dependencies.png)

<a name="find-api-connection-parameters"></a>

### <a name="find-api-connection-parameters"></a>API 연결 매개 변수 찾기

워크플로에서 관리되는 API 연결을 사용하는 경우 내보내기 템플릿 기능을 사용할 때 모든 관련 매개 변수가 포함되지는 않습니다. ARM 템플릿에서 모든 [API 연결 리소스 정의](logic-apps-azure-resource-manager-templates-overview.md#connection-resource-definitions)의 일반적인 형식은 다음과 같습니다.

```json
{
   "type": "Microsoft.Web/connections",
   "apiVersion": "2016–06–01",
   "location": "[parameters('location')]",
   "name": "[parameters('connectionName')]",
   "properties": {}
}
```

연결 리소스 정의를 완료하기 위해 `properties` 개체에서 사용해야 하는 값을 찾으려면 특정 커넥터에 대해 다음 API를 사용할 수 있습니다.

`GET https://management.azure.com/subscriptions/{subscription-ID}/providers/Microsoft.Web/locations/{location}/managedApis/{connector-name}?api-version=2016-06-01`

응답에서 특정 커넥터에 대한 리소스 정의를 완료하는 데 필요한 모든 정보를 포함하는 `connectionParameters` 개체를 찾습니다. 다음 예제에서는 SQL 관리 연결에 대한 예제 리소스 정의를 보여줍니다.

```json
{
   "type": "Microsoft.Web/connections",
   "apiVersion": "2016–06–01",
   "location": "[parameters('location')]",
   "name": "[parameters('connectionName')]",
   "properties": {
      "displayName": "sqltestconnector",
      "api": {
         "id": "/subscriptions/{subscription-ID}/providers/Microsoft.Web/locations/{location}/managedApis/sql"
      },
      "parameterValues": {
         "authType": "windows", 
         "database": "TestDB",
         "password": "TestPassword",
         "server": "TestServer",
         "username": "TestUserName"
      }
   }
}
```

또는 Logic Apps 디자이너에서 연결을 만들 때의 네트워크 추적을 검토할 수 있습니다. 앞에서 설명한 대로 커넥터의 관리되는 API에 대한 `PUT` 호출을 찾고 필요한 모든 정보에 대한 요청 본문을 검토합니다.

## <a name="deploy-logic-app-resources-zip-deploy"></a>논리 앱 리소스 배포(zip 배포)

논리 앱 프로젝트를 원본 리포지토리로 푸시한 후 Azure 내부 또는 외부의 인프라에 논리 앱을 배포하는 빌드 및 릴리스 파이프라인을 설정할 수 있습니다.

### <a name="build-your-project"></a>프로젝트 빌드

논리 앱 프로젝트 형식에 따라 빌드 파이프라인을 설정하려면 다음 표에 나열된 해당 작업을 완료합니다.

| 프로젝트 형식 | 설명 및 단계 |
|--------------|-----------------------|
| Nuget 기반 | NuGet 기반 프로젝트 구조는 .NET Framework를 기반으로 합니다. 이러한 프로젝트를 빌드하려면 .NET Standard 빌드 단계를 수행해야 합니다. 자세한 내용은 [MSBuild를 사용하여 NuGet 패키지 만들기](/nuget/create-packages/creating-a-package-msbuild) 설명서를 검토하세요. |
| 번들 기반 | 확장 번들 기반 프로젝트는 언어별 프로젝트가 아니며 언어별 빌드 단계가 필요하지 않습니다. 모든 메서드를 사용하여 프로젝트 파일을 압축할 수 있습니다. <p><p>**중요**: .zip 파일에 모든 워크플로 폴더, 구성 파일(예: host.json, connections.json) 및 기타 관련 파일을 포함한 실제 빌드 아티팩트가 포함되어 있는지 확인합니다. |
|||

### <a name="release-to-azure"></a>Azure에 릴리스

Azure에 배포되는 릴리스 파이프라인을 설정하려면 GitHub, Azure DevOps 또는 Azure CLI에 대한 관련 옵션을 선택합니다.

> [!NOTE]
> Azure Logic Apps는 현재 Azure 배포 슬롯을 지원하지 않습니다.

#### <a name="github"></a>[GitHub](#tab/github)

GitHub 배포의 경우 [GitHub Actions](https://docs.github.com/actions)(예: Azure Functions의 GitHub 작업)를 사용하여 논리 앱을 배포할 수 있습니다. 이 작업을 수행하려면 다음 정보를 전달해야 합니다.

- 배포에 사용할 논리 앱 이름
- 모든 워크플로 폴더, 구성 파일(예: host.json, connections.json) 및 기타 관련 파일을 포함한 실제 빌드 아티팩트가 포함된 zip 파일입니다.
- 인증에 사용되는 [게시 프로필](../azure-functions/functions-how-to-github-actions.md#generate-deployment-credentials)

```yaml
- name: 'Run Azure Functions Action'
  uses: Azure/functions-action@v1
  id: fa
  with:
   app-name: 'MyLogicAppName'
   package: 'MyBuildArtifact.zip'
   publish-profile: 'MyLogicAppPublishProfile'
```

자세한 내용은 [GitHub 작업을 사용하여 지속적으로 업데이트](../azure-functions/functions-how-to-github-actions.md) 설명서를 검토하세요.

#### <a name="azure-devops"></a>[Azure DevOps](#tab/azure-devops)

Azure DevOps 배포의 경우 Azure Pipelines에서 [Azure 함수 앱 배포 작업](/azure/devops/pipelines/tasks/deploy/azure-function-app?view=azure-devops&preserve-view=true)을 사용하여 논리 앱을 배포할 수 있습니다. 이 작업을 수행하려면 다음 정보를 전달해야 합니다.

- 배포에 사용할 논리 앱 이름
- 모든 워크플로 폴더, 구성 파일(예: host.json, connections.json) 및 기타 관련 파일을 포함한 실제 빌드 아티팩트가 포함된 zip 파일입니다.
- 인증에 사용되는 [게시 프로필](../azure-functions/functions-how-to-github-actions.md#generate-deployment-credentials)

```yaml
- task: AzureFunctionApp@1
  displayName: 'Deploy logic app workflows'
  inputs:
     azureSubscription: 'MyServiceConnection'
     appType: 'workflowapp'
     appName: 'MyLogicAppName'
     package: 'MyBuildArtifact.zip'
     deploymentMethod: 'zipDeploy'
```

자세한 내용은 [Azure Pipelines를 사용하여 Azure 함수 배포](/azure/devops/pipelines/targets/azure-functions-windows) 설명서를 검토하세요.

#### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

다른 배포 도구를 사용하는 경우 Azure CLI를 사용하여 단일 테넌트 기반 논리 앱을 배포할 수 있습니다. 시작하기 전에 다음 항목이 있어야 합니다.

- 로컬 컴퓨터에 설치된 최신 Azure CLI 확장입니다.

  - 이 확장이 없으면 [운영 체제 또는 플랫폼에 대한 설치 가이드](/cli/azure/install-azure-cli)를 검토하세요.

  - 최신 버전이 있는지 확실하지 않은 경우 [환경 및 CLI 버전을 확인하는 단계](#check-environment-cli-version)를 수행합니다.

- Azure CLI용 *미리 보기* 단일 테넌트 Azure Logic Apps(표준) 확장입니다.

  이 확장이 없으면 [확장을 설치하는 단계](#install-logic-apps-cli-extension)를 수행합니다. 단일 테넌트 Azure Logic Apps가 일반 공급되지만 Azure CLI용 단일 테넌트 Azure Logic Apps 확장은 아직 미리 보기로 제공됩니다.

- 논리 앱을 배포하는 데 사용할 Azure 리소스 그룹입니다.

  이 리소스 그룹이 없는 경우 [리소스 그룹을 만드는 단계](#create-resource-group)를 수행합니다.

- 논리 앱에서 데이터 및 실행 기록 보존에 사용할 Azure 스토리지 계정입니다.

  이 스토리지 계정이 없으면 [스토리지 계정을 만드는 단계](/cli/azure/storage/account#az_storage_account_create)를 수행합니다.

<a name="check-environment-cli-version"></a>

##### <a name="check-environment-and-cli-version"></a>환경 및 CLI 버전 확인

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. 터미널 또는 명령 창에서 [`az login`](/cli/azure/authenticate-azure-cli) 명령을 실행하여 구독이 활성화되어 있는지 확인합니다.

   ```azurecli-interactive
   az login
   ```

1. 터미널 또는 명령 창에서 다음 필수 매개 변수를 통해 `az` 명령을 실행하여 Azure CLI 버전을 확인합니다.

   ```azurecli-interactive
   az --version
   ```

1. 최신 Azure CLI 버전이 없는 경우 [사용 중인 운영 체제 또는 플랫폼의 설치 가이드](/cli/azure/install-azure-cli)에 따라 설치를 업데이트합니다.

   최신 버전에 대한 자세한 내용은 [최신 릴리스 정보](/cli/azure/release-notes-azure-cli?tabs=azure-cli)를 검토하세요.

<a name="install-logic-apps-cli-extension"></a>

##### <a name="install-azure-logic-apps-standard-extension-for-azure-cli"></a>Azure CLI용 Azure Logic Apps(표준) 확장 설치

현재 이 확장의 *미리 보기* 버전만 사용할 수 있습니다. 이전에 이 확장을 설치하지 않은 경우 다음 필수 매개 변수를 사용하여 `az extension add` 명령을 실행합니다.

```azurecli-interactive
az extension add --yes --source "https://aka.ms/logicapp-latest-py2.py3-none-any.whl"
```

최신 확장(버전 0.1.1)을 가져오려면 다음 명령을 실행하여 기존 확장을 제거한 다음, 원본에서 최신 버전을 설치합니다.

```azurecli-interactive
az extension remove --name logicapp
az extension add --yes --source "https://aka.ms/logicapp-latest-py2.py3-none-any.whl"
```

<a name="create-resource-group"></a>

#### <a name="create-resource-group"></a>리소스 그룹 만들기

논리 앱에 대한 리소스 그룹을 아직 설정하지 않은 경우 `az group create` 명령을 실행하여 그룹을 만듭니다. Azure 계정에 대한 기본 구독을 아직 설정하지 않은 경우 `--subscription` 매개 변수를 구독 이름 또는 식별자와 함께 사용해야 합니다. 그렇지 않으면 `--subscription` 매개 변수를 사용할 필요가 없습니다.

> [!TIP]
> 기본 구독을 설정하려면 다음 명령을 실행하고 `MySubscription`을 구독 이름 또는 식별자로 대체합니다.
>
> `az account set --subscription MySubscription`

예를 들어 다음 명령은 위치 `eastus`에서 `MySubscription`이라는 Azure 구독을 사용하여 `MyResourceGroupName`이라는 리소스 그룹을 만듭니다.

```azurecli
az group create --name MyResourceGroupName 
   --subscription MySubscription 
   --location eastus
```

리소스 그룹이 성공적으로 만들어지면 출력에서 `provisioningState`가 `Succeeded`로 표시됩니다.

```output
<...>
   "name": "testResourceGroup",
   "properties": {
      "provisioningState": "Succeeded"
    },
<...>
```

<a name="deploy-logic-app"></a>

##### <a name="deploy-logic-app"></a>논리 앱 배포

압축된 아티팩트를 Azure 리소스 그룹에 배포하려면 다음 필수 매개 변수를 통해 `az logicapp deployment` 명령을 실행합니다.

> [!IMPORTANT]
> zip 파일에 루트 수준의 프로젝트 아티팩트가 포함되어 있는지 확인합니다. 이러한 아티팩트에는 모든 워크플로 폴더, host.js, connections.json과 같은 구성 파일 및 기타 관련 파일이 포함됩니다. 추가 폴더를 추가하거나 프로젝트 구조에 아직 없는 폴더에 아티팩트를 배치하지 마세요. 예를 들어 이 목록은 MyBuildArtifacts.zip 파일 구조의 예제를 보여줍니다.
>
> ```output
> MyStatefulWorkflow1-Folder
> MyStatefulWorkflow2-Folder
> connections.json
> host.json
> ```

```azurecli-interactive
az logicapp deployment source config-zip --name MyLogicAppName 
   --resource-group MyResourceGroupName --subscription MySubscription 
   --src MyBuildArtifact.zip
```

---

### <a name="release-to-containers"></a>컨테이너에 릴리스

논리 앱을 컨테이너화한 경우 배포는 배포하고 관리하는 다른 컨테이너와 거의 동일하게 작동합니다.

엔드투엔드 컨테이너 빌드 및 배포 파이프라인을 구현하는 방법을 보여주는 예제는 [컨테이너에 대한 CI/CD](https://azure.microsoft.com/solutions/architecture/cicd-for-containers/)를 참조하세요.

## <a name="next-steps"></a>다음 단계

- [단일 테넌트 Azure Logic Apps용 DevOps 배포](devops-deployment-single-tenant-azure-logic-apps.md)

단일 테넌트 Azure Logic Apps 사용 경험에 대한 여러분의 의견을 듣고 싶습니다.

- 버그 또는 문제가 발견되는 경우 [GitHub에서 문제를 알려주세요](https://github.com/Azure/logicapps/issues).
- [이 사용자 의견 양식을 사용](https://aka.ms/logicappsdevops)하여 질문, 요청, 의견 및 기타 피드백을 보내주세요.
