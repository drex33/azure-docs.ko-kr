---
title: Azure Bicep을 사용하여 App Service 앱 만들기
description: App Service 배포하는 여러 방법 중 하나인 Azure Bicep을 사용하여 몇 초 안에 Azure App Service 첫 번째 앱을 만듭니다.
author: seligj95
ms.author: msangapu
ms.topic: article
ms.date: 8/26/2021
ms.openlocfilehash: 764a44a79ce6f892f0715dde6d657755a3048acf
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130257198"
---
# <a name="create-app-service-app-using-bicep"></a>Bicep을 사용하여 App Service 앱 만들기

[Bicep](../azure-resource-manager/bicep/index.yml) 파일을 사용하여 클라우드에 앱을 배포하고 [Cloud Shell](overview.md) [Azure CLI Azure App Service](/cli/azure/get-started-with-azure-cli) 시작합니다. 무료 App Service 계층을 사용하므로 이 빠른 시작을 완료하는 데 비용이 들지 않습니다.

Bicep은 선언적 구문을 사용하여 Azure 리소스를 배포하는 DSL(도메인 특정 언어)입니다. 간결한 구문, 신뢰할 수 있는 형식 안전성 및 코드 다시 사용에 대한 지원을 제공합니다. JSON 대신 Bicep을 사용하여 Azure Resource Manager[템플릿(ARM 템플릿)을](../azure-resource-manager/templates/overview.md)개발할 수 있습니다. ARM 템플릿을 만드는 JSON 구문은 자세한 정보를 사용할 수 있으며 복잡한 식이 필요할 수 있습니다. Bicep 구문은 이러한 복잡성을 줄이고 개발 환경을 향상시킵니다. Bicep은 ARM 템플릿 JSON에 대한 투명한 추상화이며 JSON 템플릿 기능이 손실되지 않습니다. 배포하는 동안 Bicep CLI는 Bicep 파일을 ARM 템플릿 JSON으로 변환 컴파일됩니다.

## <a name="prerequisites"></a>사전 요구 사항

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

Bicep을 사용하여 리소스를 효과적으로 만들려면 Bicep [개발 환경을](../azure-resource-manager/bicep/install.md)설정해야 합니다. [Visual Studio Code](https://code.visualstudio.com/) Bicep 확장은 언어 지원 및 리소스 자동 완성을 제공합니다. 확장은 Bicep 파일을 만들고 유효성을 검사하는 데 도움이 되며, 이 빠른 시작 완료 후 Bicep을 사용하여 리소스를 만드는 개발자에게 권장됩니다.

## <a name="review-the-template"></a>템플릿 검토

이 빠른 시작에 사용된 템플릿은 다음과 같습니다. Linux에 App Service 계획 및 App Service 앱과 [Azure 샘플](https://github.com/Azure-Samples) 리포지션의 샘플 Node.js "Hello World" 앱을 배포합니다.

```bicep
param webAppName string = uniqueString(resourceGroup().id) // Generate unique String for web app name
param sku string = 'F1' // The SKU of App Service Plan
param linuxFxVersion string = 'node|14-lts' // The runtime stack of web app
param location string = resourceGroup().location // Location for all resources
param repositoryUrl string = 'https://github.com/Azure-Samples/nodejs-docs-hello-world'
param branch string = 'master'
var appServicePlanName = toLower('AppServicePlan-${webAppName}')
var webSiteName = toLower('wapp-${webAppName}')
resource appServicePlan 'Microsoft.Web/serverfarms@2020-06-01' = {
  name: appServicePlanName
  location: location
  properties: {
    reserved: true
  }
  sku: {
    name: sku
  }
  kind: 'linux'
}
resource appService 'Microsoft.Web/sites@2020-06-01' = {
  name: webSiteName
  location: location
  properties: {
    serverFarmId: appServicePlan.id
    siteConfig: {
      linuxFxVersion: linuxFxVersion
    }
  }
}
resource srcControls 'Microsoft.Web/sites/sourcecontrols@2021-01-01' = {
  name: '${appService.name}/web'
  properties: {
    repoUrl: repositoryUrl
    branch: branch
    isManualIntegration: true
  }
}
```

템플릿에 정의된 세 가지 Azure 리소스는 다음과 같습니다.

* [**Microsoft.Web/serverfarms**](/azure/templates/microsoft.web/serverfarms): App Service 요금제를 만듭니다.
* [**Microsoft.Web/sites**](/azure/templates/microsoft.web/sites): App Service 앱을 만듭니다.
* [**Microsoft.Web/sites/sourcecontrols:**](/azure/templates/microsoft.web/sites/sourcecontrols)외부 git 배포 구성을 만듭니다.

이 템플릿에는 편의를 위해 미리 정의된 몇 가지 매개 변수가 포함되어 있습니다. 매개 변수 기본값 및 설명은 아래 표를 참조하세요.

| 매개 변수 | Type    | 기본값                | 설명: |
|------------|---------|------------------------------|-------------|
| webAppName | 문자열  | "webApp- **[`<uniqueString>`](../azure-resource-manager/templates/template-functions-string.md#uniquestring)** " | 앱 이름 |
| 위치   | 문자열  | "[[resourceGroup().location](../azure-resource-manager/templates/template-functions-resource.md#resourcegroup)]" | 앱 지역 |
| sku        | 문자열  | "F1"                         | 인스턴스 크기  |
| linuxFxVersion   | 문자열  | "NODE&#124;14-LTS"       | "프로그래밍 언어 스택 &#124; 버전" |
| repositoryUrl    | 문자열  | "https://github.com/Azure-Samples/nodejs-docs-hello-world"    | 외부 Git 리포지토리(선택 사항) |
| 분기    | 문자열  | "master"    | 코드 샘플의 기본 분기 |

---

## <a name="deploy-the-template"></a>템플릿 배포

템플릿을 복사하여 원하는 편집기/IDE에 붙여넣고 파일을 로컬 작업 디렉터리에 저장합니다.

Azure CLI는 템플릿을 배포하는 데 사용됩니다. Azure Portal, Azure PowerShell 및 REST API를 사용할 수도 있습니다. 다른 배포 방법을 알아보려면 [Bicep 배포 명령을 참조하세요.](../azure-resource-manager/bicep/deploy-cli.md)

다음 코드는 리소스 그룹, App Service 요금제 및 웹앱을 만듭니다. 기본 리소스 그룹, App Service 요금제 및 위치가 설정되었습니다. `<app-name>`을 전역적으로 고유한 앱 이름(유효한 문자: `a-z`, `0-9`, `-`)으로 바꿉니다.

Azure CLI 설치된 터미널을 열고 아래 코드를 실행하여 Linux에서 Node.js 앱을 만듭니다.

```azurecli-interactive
az group create --name myResourceGroup --location "southcentralus" &&
az deployment group create --resource-group myResourceGroup --template-file <path-to-template>
```

다른 언어 스택을 배포하려면 `linuxFxVersion`을 적절한 값으로 업데이트합니다. 샘플은 다음과 같습니다. 최신 버전을 표시하려면 Cloud Shell에서 다음 명령어를 실행합니다. `az webapp config show --resource-group myResourceGroup --name <app-name> --query linuxFxVersion`

| 언어    | 예제:                                               |
|-------------|------------------------------------------------------|
| **.NET**    | linuxFxVersion="DOTNETCORE&#124;3.0"                 |
| **PHP**     | linuxFxVersion="PHP&#124;7.4"                        |
| **Node.JS** | linuxFxVersion="NODE&#124;10.15"                     |
| **Java**    | linuxFxVersion="JAVA&#124;1.8 &#124;TOMCAT&#124;9.0" |
| **Python**  | linuxFxVersion="PYTHON&#124;3.7"                     |
| **Ruby**    | linuxFxVersion="RUBY&#124;2.6"                       |

---

## <a name="validate-the-deployment"></a>배포 유효성 검사

`http://<app_name>.azurewebsites.net/`으로 이동하여 생성되었는지 확인합니다.

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우 [리소스 그룹을 삭제](../azure-resource-manager/management/delete-resource-group.md?tabs=azure-portal#delete-resource-group)합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Bicep 설명서](../azure-resource-manager/bicep/index.yml)
> [!div class="nextstepaction"]
> [Azure App Service 대한 Bicep 샘플](./samples-bicep.md)