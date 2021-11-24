---
title: Azure Resource Manager 템플릿을 사용하여 App Service 앱 만들기
description: App Service에 배포하는 여러 가지 방법 중 하나인 ARM 템플릿(Azure Resource Manager 템플릿)을 사용하여 몇 초 내에 Azure App Service에 첫 번째 앱을 만듭니다.
author: msangapu-msft
ms.author: msangapu
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.topic: quickstart
ms.date: 10/16/2020
ms.custom: subject-armqs, mode-arm
zone_pivot_groups: app-service-platform-windows-linux
adobe-target: true
adobe-target-activity: DocsExp–386541–A/B–Enhanced-Readability-Quickstarts–2.19.2021
adobe-target-experience: Experience B
adobe-target-content: ./quickstart-arm-template-uiex
ms.openlocfilehash: 1bf96005abe84edf269b89de64611d0afa702970
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133042876"
---
# <a name="quickstart-create-app-service-app-using-an-arm-template"></a>빠른 시작: ARM 템플릿을 사용하여 App Service 앱 만들기

ARM 템플릿(Azure Resource Manager 템플릿) 및 Cloud Shell의 [Azure CLI](/cli/azure/get-started-with-azure-cli)를 사용하여 클라우드에 앱을 배포하여 [Azure App Service](overview.md)를 시작하세요. 무료 App Service 계층을 사용하므로 이 빠른 시작을 완료하는 데 비용이 들지 않습니다.

 [!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

환경이 필수 구성 요소를 충족하고 ARM 템플릿 사용에 익숙한 경우 **Azure에 배포** 단추를 선택합니다. 그러면 Azure Portal에서 템플릿이 열립니다.

다음 단추를 사용하여 **Linux** 에 배포합니다.

[![Azure에 배포](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.web%2Fapp-service-docs-linux%2Fazuredeploy.json)

다음 단추를 사용하여 **Windows** 에 배포합니다.

[![Azure에 배포](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.web%2Fapp-service-docs-windows%2Fazuredeploy.json)

## <a name="prerequisites"></a>필수 구성 요소

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="review-the-template"></a>템플릿 검토

::: zone pivot="platform-windows"
이 빠른 시작에서 사용되는 템플릿은 [Azure 빠른 시작 템플릿](https://azure.microsoft.com/resources/templates/app-service-docs-windows)에서 나온 것입니다. Windows에 App Service 요금제 및 App Service 앱을 배포합니다. .NET Core, .NET Framework, PHP, Node.js 및 정적 HTML 앱과 호환됩니다. Java의 경우 [Java 앱 만들기](./quickstart-java.md)를 참조하세요.

:::code language="json" source="~/quickstart-templates/quickstarts/microsoft.web/app-service-docs-windows/azuredeploy.json":::

템플릿에는 두 개의 Azure 리소스가 정의되어 있습니다.

* [**Microsoft.Web/serverfarms**](/azure/templates/microsoft.web/serverfarms): App Service 요금제를 만듭니다.
* [**Microsoft.Web/sites**](/azure/templates/microsoft.web/sites): App Service 앱을 만듭니다.

이 템플릿에는 편의를 위해 미리 정의된 몇 가지 매개 변수가 포함되어 있습니다. 매개 변수 기본값 및 설명은 아래 표를 참조하세요.

| 매개 변수 | Type    | 기본값                | 설명: |
|------------|---------|------------------------------|-------------|
| webAppName | 문자열  | "webApp- **[`<uniqueString>`](../azure-resource-manager/templates/template-functions-string.md#uniquestring)** " | 앱 이름 |
| 위치   | 문자열  | "[[resourceGroup().location](../azure-resource-manager/templates/template-functions-resource.md#resourcegroup)]" | 앱 지역 |
| sku        | 문자열  | "F1"                         | 인스턴스 크기(F1 = 무료 계층) |
| 언어   | 문자열  | ".net"                       | 프로그래밍 언어 스택(.net, php, node, html) |
| helloWorld | boolean | False                        | True = "Hello World" 앱 배포 |
| repoUrl    | 문자열  | " "                          | 외부 Git 리포지토리(선택 사항) |
::: zone-end
::: zone pivot="platform-linux"
이 빠른 시작에서 사용되는 템플릿은 [Azure 빠른 시작 템플릿](https://azure.microsoft.com/resources/templates/app-service-docs-linux)에서 나온 것입니다. Linux에 App Service 요금제 및 App Service 앱을 배포합니다. App Service에서 지원되는 모든 프로그래밍 언어와 호환됩니다.

:::code language="json" source="~/quickstart-templates/quickstarts/microsoft.web/app-service-docs-linux/azuredeploy.json":::

템플릿에는 두 개의 Azure 리소스가 정의되어 있습니다.

* [**Microsoft.Web/serverfarms**](/azure/templates/microsoft.web/serverfarms): App Service 요금제를 만듭니다.
* [**Microsoft.Web/sites**](/azure/templates/microsoft.web/sites): App Service 앱을 만듭니다.

이 템플릿에는 편의를 위해 미리 정의된 몇 가지 매개 변수가 포함되어 있습니다. 매개 변수 기본값 및 설명은 아래 표를 참조하세요.

| 매개 변수 | Type    | 기본값                | 설명: |
|------------|---------|------------------------------|-------------|
| webAppName | 문자열  | "webApp- **[`<uniqueString>`](../azure-resource-manager/templates/template-functions-string.md#uniquestring)** " | 앱 이름 |
| 위치   | 문자열  | "[[resourceGroup().location](../azure-resource-manager/templates/template-functions-resource.md#resourcegroup)]" | 앱 지역 |
| sku        | 문자열  | "F1"                         | 인스턴스 크기(F1 = 무료 계층) |
| linuxFxVersion   | 문자열  | "DOTNETCORE&#124;3.0        | "프로그래밍 언어 스택 &#124; 버전" |
| repoUrl    | 문자열  | " "                          | 외부 Git 리포지토리(선택 사항) |

---
::: zone-end

## <a name="deploy-the-template"></a>템플릿 배포

Azure CLI는 템플릿을 배포하는 데 사용됩니다. Azure Portal, Azure PowerShell 및 REST API를 사용할 수도 있습니다. 다른 배포 방법을 알아보려면 [템플릿 배포](../azure-resource-manager/templates/deploy-powershell.md)를 참조하세요.

다음 코드는 리소스 그룹, App Service 요금제 및 웹앱을 만듭니다. 기본 리소스 그룹, App Service 요금제 및 위치가 설정되었습니다. `<app-name>`을 전역적으로 고유한 앱 이름(유효한 문자: `a-z`, `0-9`, `-`)으로 바꿉니다.

::: zone pivot="platform-windows"
아래 코드를 실행하여 Windows에 .NET Framework 앱을 배포합니다.

```azurecli-interactive
az group create --name myResourceGroup --location "southcentralus" &&
az deployment group create --resource-group myResourceGroup \
--parameters language=".net" helloWorld="true" webAppName="<app-name>" \
--template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/quickstarts/microsoft.web/app-service-docs-windows/azuredeploy.json"
::: zone-end
::: zone pivot="platform-linux"
Run the code below to create a Python app on Linux.

```azurecli-interactive
az group create --name myResourceGroup --location "southcentralus" &&
az deployment group create --resource-group myResourceGroup --parameters webAppName="<app-name>" linuxFxVersion="PYTHON|3.7" \
--template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/quickstarts/microsoft.web/app-service-docs-linux/azuredeploy.json"
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
::: zone-end

> [!NOTE]
> 자세한 내용은 [Azure App Service 템플릿 샘플](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Sites)에서 확인할 수 있습니다.

## <a name="validate-the-deployment"></a>배포 유효성 검사

`http://<app_name>.azurewebsites.net/`으로 이동하여 생성되었는지 확인합니다.

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우 [리소스 그룹을 삭제](../azure-resource-manager/management/delete-resource-group.md?tabs=azure-portal#delete-resource-group)합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> 로컬 Git에서 배포

> [!div class="nextstepaction"]
> [SQL Database를 사용한 ASP.NET Core](tutorial-dotnetcore-sqldb-app.md)

> [!div class="nextstepaction"]
> Postgres를 사용하는 Python

> [!div class="nextstepaction"]
> [MySQL을 사용하는 PHP](tutorial-php-mysql-app.md)

> [!div class="nextstepaction"]
> [Java를 사용하여 Azure SQL 데이터베이스에 연결](../azure-sql/database/connect-query-java.md?toc=%2fazure%2fjava%2ftoc.json)

> [!div class="nextstepaction"]
> [사용자 지정 도메인 매핑](app-service-web-tutorial-custom-domain.md)
