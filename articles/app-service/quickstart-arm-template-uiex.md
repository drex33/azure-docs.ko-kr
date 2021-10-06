---
title: Azure Resource Manager 템플릿을 사용하여 App Service 앱 만들기
description: App Service에 배포하는 여러 가지 방법 중 하나인 ARM 템플릿(Azure Resource Manager 템플릿)을 사용하여 몇 초 내에 Azure App Service에 첫 번째 앱을 만듭니다.
author: msangapu-msft
ms.author: msangapu
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.topic: quickstart
ms.date: 10/16/2020
ms.custom: subject-armqs
zone_pivot_groups: app-service-platform-windows-linux
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 7d6111a68be8bc195b7268a778a77facdd944772
ms.sourcegitcommit: 10029520c69258ad4be29146ffc139ae62ccddc7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/27/2021
ms.locfileid: "129084135"
---
# <a name="quickstart-create-app-service-app-using-an-arm-template"></a>빠른 시작: ARM 템플릿을 사용하여 App Service 앱 만들기

클라우드에 앱을 배포하여 [Azure App Service](overview.md) 시작 - 사용: <abbr title="하나 이상의 Azure 리소스와 배포된 리소스 간의 종속성을 선언적으로 정의하는 JSON 파일입니다. 템플릿은 리소스를 일관되고 반복적으로 배포하는 데 사용할 수 있습니다.">ARM 템플릿</abbr> 및 Cloud Shell의 [Azure CLI](/cli/azure/get-started-with-azure-cli) 무료 App Service 계층을 사용하므로 이 빠른 시작을 완료하는 데 비용이 들지 않습니다. <abbr title="선언적 구문에서는 배포를 만들기 위한 프로그래밍 명령의 시퀀스를 작성하지 않고 의도하는 배포를 설명합니다.">이 템플릿은 선언적 구문을 사용합니다.</abbr>

 환경이 필수 구성 요소를 충족하고 [ARM 템플릿](../azure-resource-manager/templates/overview.md) 사용에 익숙한 경우 **Azure에 배포** 단추를 선택합니다. 그러면 Azure Portal에서 템플릿이 열립니다.

::: zone pivot="platform-windows"
[![Azure에 배포](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.web%2Fapp-service-docs-windows%2Fazuredeploy.json)
::: zone-end

::: zone pivot="platform-linux"
[![Azure에 배포](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.web%2Fapp-service-docs-linux%2Fazuredeploy.json)
::: zone-end

<hr/>

## <a name="1-prepare-your-environment"></a>1. 환경 준비

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

<hr/>

## <a name="2-review-the-template"></a>2. 템플릿 검토

::: zone pivot="platform-windows"
이 빠른 시작에서 사용되는 템플릿은 [Azure 빠른 시작 템플릿](https://azure.microsoft.com/resources/templates/app-service-docs-windows)에서 나온 것입니다. Windows에 App Service 요금제 및 App Service 앱을 배포합니다.

:::code language="json" source="~/quickstart-templates/quickstarts/microsoft.web/app-service-docs-windows/azuredeploy.json":::

<details>
<summary>어떤 리소스와 매개 변수가 템플릿에 정의되어 있나요?</summary>

템플릿에는 두 개의 Azure 리소스가 정의되어 있습니다.

* [**Microsoft.Web/serverfarms**](/azure/templates/microsoft.web/serverfarms): App Service 요금제를 만듭니다.
* [**Microsoft.Web/sites**](/azure/templates/microsoft.web/sites): App Service 앱을 만듭니다.

다음 표에는 기본 매개 변수 및 그에 대한 설명이 자세히 제공됩니다.

| 매개 변수 | Type    | 기본값                | 설명: |
|------------|---------|------------------------------|-------------|
| webAppName | 문자열  | "webApp- **[`<uniqueString>`](../azure-resource-manager/templates/template-functions-string.md#uniquestring)** " | 앱 이름 |
| 위치   | 문자열  | "[[resourceGroup().location](../azure-resource-manager/templates/template-functions-resource.md#resourcegroup)]" | 앱 지역 |
| sku        | 문자열  | "F1"                         | 인스턴스 크기(F1 = 무료 계층) |
| 언어   | 문자열  | ".net"                       | 프로그래밍 언어 스택(.net, php, node, html) |
| helloWorld | boolean | False                        | True = "Hello World" 앱 배포 |
| repoUrl    | 문자열  | " "                          | 외부 Git 리포지토리(선택 사항) |

---

</details>
::: zone-end
::: zone pivot="platform-linux"
이 빠른 시작에서 사용되는 템플릿은 [Azure 빠른 시작 템플릿](https://azure.microsoft.com/resources/templates/app-service-docs-linux)에서 나온 것입니다. Windows에 App Service 요금제 및 App Service 앱을 배포합니다.

:::code language="json" source="~/quickstart-templates/quickstarts/microsoft.web/app-service-docs-linux/azuredeploy.json":::

이 템플릿에는 편의를 위해 정의된 Azure 리소스 및 매개 변수가 포함되어 있습니다.

<details>
<summary>어떤 리소스와 매개 변수가 템플릿에 정의되어 있나요?</summary>

템플릿에는 두 개의 Azure 리소스가 정의되어 있습니다.

* [**Microsoft.Web/serverfarms**](/azure/templates/microsoft.web/serverfarms): App Service 요금제를 만듭니다.
* [**Microsoft.Web/sites**](/azure/templates/microsoft.web/sites): App Service 앱을 만듭니다.

다음 표에는 기본 매개 변수 및 그에 대한 설명이 자세히 제공됩니다.

| 매개 변수 | Type    | 기본값                | 설명: |
|------------|---------|------------------------------|-------------|
| webAppName | 문자열  | "webApp- **[`<uniqueString>`](../azure-resource-manager/templates/template-functions-string.md#uniquestring)** " | 앱 이름 |
| 위치   | 문자열  | "[[resourceGroup().location](../azure-resource-manager/templates/template-functions-resource.md#resourcegroup)]" | 앱 지역 |
| sku        | 문자열  | "F1"                         | 인스턴스 크기(F1 = 무료 계층) |
| linuxFxVersion   | 문자열  | "DOTNETCORE&#124;3.0        | "프로그래밍 언어 스택 &#124; 버전" |
| repoUrl    | 문자열  | " "                          | 외부 Git 리포지토리(선택 사항) |

---

</details>

::: zone-end

<hr/>

## <a name="3-deploy-the-template"></a>3. 템플릿 배포

::: zone pivot="platform-windows"
Azure CLI를 사용하여 아래 코드를 실행하고 Windows에 .NET Framework 앱을 배포합니다. 

바꾸기 <abbr title="유효한 문자는 `a-z`, `0-9`, `-`입니다."> \<app-name> </abbr> 전역적으로 고유한 앱 이름으로 기타 정보 알아보기 <abbr title="Azure Portal, Azure PowerShell 및 REST API를 사용할 수도 있습니다.">배포 방법</abbr>, [템플릿 배포](../azure-resource-manager/templates/deploy-powershell.md)를 참조하세요. 자세한 내용은 [Azure App Service 템플릿 샘플](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Sites)에서 확인할 수 있습니다.

```azurecli-interactive
az group create --name myResourceGroup --location "southcentralus" &&
az deployment group create --resource-group myResourceGroup \
--parameters language=".net" helloWorld="true" webAppName="<app-name>" \
--template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/quickstarts/microsoft.web/app-service-docs-windows/azuredeploy.json"
```
::: zone-end
::: zone pivot="platform-linux"
아래 코드를 실행하여 Linux에서 Python 앱을 만듭니다. 

\<app-name\>을 전역적으로 고유한 앱 이름으로 바꿉니다. 유효한 문자는 `a-z`, `0-9`, `-`입니다.

```azurecli-interactive
az group create --name myResourceGroup --location "southcentralus" &&
az deployment group create --resource-group myResourceGroup --parameters webAppName="<app-name>" linuxFxVersion="PYTHON|3.7" \
--template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/quickstarts/microsoft.web/app-service-docs-linux/azuredeploy.json"
```
::: zone-end

<details>
<summary>코드는 무엇을 수행하나요?</summary>
<p>명령은 다음 작업을 수행합니다.</p>
<ul>
<li>기본 항목 만들기 <abbr title="한 단위로 관리할 수 있는 관련 Azure 리소스에 대한 논리적 컨테이너입니다.">리소스 그룹</abbr>.</li>
<li>기본 항목 만들기 <abbr title="앱을 호스팅하는 웹 서버 팜의 위치, 크기 및 기능을 지정하는 계획.">App Service 계획</abbr>.</li>
<li><a href="/cli/azure/webapp#az_webapp_create">만들기 <abbr title="앱 코드, DNS 호스트 이름, 인증서 및 관련 리소스를 포함하는 웹앱의 표현.">App Service 앱</abbr>지정한 이름의 </a>.</li>
</ul>
</details>

::: zone pivot="platform-windows"
<details>
<summary>다른 언어 스택은 어떻게 배포하나요?</summary>
다른 언어 스택을 배포하려면 - 업데이트: <abbr title="이 템플릿은 .NET Core, .NET Framework, PHP, Node.js 및 정적 HTML 앱과 호환됩니다.">언어 매개 변수</abbr> 적절한 값으로. Java의 경우 <a href="/azure/app-service/quickstart-java-uiex">Java 앱 만들기</a>를 참조하세요.

| 매개 변수 | Type    | 기본값                | 설명 |
|------------|---------|------------------------------|-------------|
| 언어   | 문자열  | ".net"                       | 프로그래밍 언어 스택(.net, php, node, html) |

---

</details>
::: zone-end
::: zone pivot="platform-linux"
<details>
<summary>다른 언어 스택은 어떻게 배포하나요?</summary>
 
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

</details>
::: zone-end

<hr/>

## <a name="4-validate-the-deployment"></a>4. 배포 유효성 검사

`http://<app_name>.azurewebsites.net/`으로 이동하여 생성되었는지 확인합니다.

<hr/>

## <a name="5-clean-up-resources"></a>5. 리소스 정리

더 이상 필요하지 않은 경우 [리소스 그룹을 삭제](../azure-resource-manager/management/delete-resource-group.md?tabs=azure-portal#delete-resource-group)합니다.

<hr/>

## <a name="next-steps"></a>다음 단계

- 로컬 Git에서 배포
- [SQL Database를 사용한 ASP.NET Core](tutorial-dotnetcore-sqldb-app.md)
- Postgres를 사용하는 Python
- [MySQL을 사용하는 PHP](tutorial-php-mysql-app.md)
- [Java를 사용하여 Azure SQL 데이터베이스에 연결](../azure-sql/database/connect-query-java.md?toc=%2fazure%2fjava%2ftoc.json)
- [사용자 지정 도메인 매핑](app-service-web-tutorial-custom-domain-uiex.md)
