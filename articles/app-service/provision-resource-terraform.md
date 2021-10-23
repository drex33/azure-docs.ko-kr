---
title: Terraform 템플릿을 사용하여 App Service 앱 만들기
description: App Service 배포하는 여러 방법 중 하나인 Terraform 템플릿을 사용하여 몇 초 안에 Azure App Service 첫 번째 앱을 만듭니다.
author: seligj95
ms.author: msangapu
ms.topic: article
ms.date: 8/26/2021
ms.custom: subject-terraform
ms.openlocfilehash: d41b61252abd3e914239a28e78564128a4fbf32d
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130227153"
---
# <a name="create-app-service-app-using-a-terraform-template"></a>Terraform 템플릿을 사용하여 App Service 앱 만들기

[Terraform을](/azure/developer/terraform/)사용하여 클라우드에 앱을 배포하여 [Azure App Service](overview.md) 시작합니다. 무료 App Service 계층을 사용하므로 이 빠른 시작을 완료하는 데 비용이 들지 않습니다.

Terraform을 사용하면 Azure에서 완전한 인프라를 정의하고 만들 수 있습니다. 일관되고 재현 가능한 방식으로 Azure 리소스를 만들고 구성하는 Terraform 템플릿을 이해하기 쉬운 형태로 빌드할 수 있습니다. 이 문서에서는 Terraform을 사용하여 Windows 앱을 만드는 방법을 보여줍니다.

## <a name="prerequisites"></a>필수 구성 요소

Azure 구독: Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)을 만듭니다.

Terraform 구성: 아직 구성하지 않은 경우 다음 옵션 중 하나를 사용하여 Terraform을 구성합니다.

* [Bash를 사용하여 Azure Cloud Shell에서 Terraform 구성](/azure/developer/terraform/get-started-cloud-shell-bash?tabs=bash)
* [PowerShell을 사용하여 Azure Cloud Shell에서 Terraform 구성](/azure/developer/terraform/get-started-cloud-shell-powershell?tabs=bash)
* [Bash를 사용하여 Windows에서 Terraform 구성](/azure/developer/terraform/get-started-windows-bash?tabs=bash)
* [PowerShell을 사용하여 Windows에서 Terraform 구성](/azure/developer/terraform/get-started-windows-powershell?tabs=bash)

Azure Terraform Visual Studio Code 확장을 사용하면 편집기에서 Terraform으로 작업할 수 있습니다. 이 확장을 통해 Terraform 구성을 작성, 테스트 및 실행할 수 있습니다. 이 확장은 리소스 그래프 시각화도 지원합니다. Azure Terraform Visual Studio Code 확장을 구성하기 위한 [이 가이드를](/azure/developer/terraform/configure-vs-code-extension-for-terraform) 참조하세요.

## <a name="review-the-template"></a>템플릿 검토

이 빠른 시작에 사용된 템플릿은 다음과 같습니다. Windows App Service 계획 및 App Service 앱과 [Azure 샘플](https://github.com/Azure-Samples) 리포지션의 샘플 Node.js "Hello World" 앱을 배포합니다.

```hcl
# Configure the Azure provider
terraform {
  required_providers {
    azurerm = {
      source  = "hashicorp/azurerm"
      version = "~> 2.65"
    }
  }
  required_version = ">= 0.14.9"
}
provider "azurerm" {
  features {}
}
# Generate a random integer to create a globally unique name
resource "random_integer" "ri" {
  min = 10000
  max = 99999
}
# Create the resource group
resource "azurerm_resource_group" "rg" {
  name     = "myResourceGroup-${random_integer.ri.result}"
  location = "eastus"
}
# Create the Linux App Service Plan
resource "azurerm_app_service_plan" "appserviceplan" {
  name                = "webapp-asp-${random_integer.ri.result}"
  location            = azurerm_resource_group.rg.location
  resource_group_name = azurerm_resource_group.rg.name
  sku {
    tier = "Free"
    size = "F1"
  }
}
# Create the web app, pass in the App Service Plan ID, and deploy code from a public GitHub repo
resource "azurerm_app_service" "webapp" {
  name                = "webapp-${random_integer.ri.result}"
  location            = azurerm_resource_group.rg.location
  resource_group_name = azurerm_resource_group.rg.name
  app_service_plan_id = azurerm_app_service_plan.appserviceplan.id
  source_control {
    repo_url           = "https://github.com/Azure-Samples/nodejs-docs-hello-world"
    branch             = "master"
    manual_integration = true
    use_mercurial      = false
  }
}
```

Azure 리소스 3개와 하위 리소스 1개는 템플릿에 정의되어 있습니다. [Azure Provider Terraform Registry에](https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs) 대한 링크는 자세한 내용 및 사용량 정보를 위해 아래에 제공됩니다.

* [**Microsoft.Resources/resourcegroups:**](/azure/templates/microsoft.resources/resourcegroups?tabs=json)리소스 그룹이 없는 경우 리소스 그룹을 만듭니다.
  * [azurerm_resource_group](https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs/resources/resource_group) 
* [**Microsoft.Web/serverfarms**](/azure/templates/microsoft.web/serverfarms): App Service 요금제를 만듭니다.
  * [azurerm_app_service_plan](https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs/resources/app_service_plan)
* [**Microsoft.Web/sites**](/azure/templates/microsoft.web/sites): App Service 앱을 만듭니다.
  * [azurerm_app_service](https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs/resources/app_service)
* [**Microsoft.Web/sites/sourcecontrols:**](/azure/templates/microsoft.web/sites/sourcecontrols)외부 git 배포 구성을 만듭니다.
  * [source_control](https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs/resources/app_service#source_control)

Terraform 템플릿을 구성하는 방법에 대한 자세한 내용은 [Terraform Learn 설명서를 참조하세요.](https://learn.hashicorp.com/collections/terraform/azure-get-started?utm_source=WEBSITE&utm_medium=WEB_IO&utm_offer=ARTICLE_PAGE&utm_content=DOCS)

## <a name="implement-the-terraform-code"></a>Terraform 코드 구현

Terraform은 인프라 관리, 빌드, 배포 및 업데이트에 대한 많은 기능을 제공합니다. 아래 단계에서는 리소스를 배포하고 삭제하는 방법만 안내합니다. [Terraform Learn 설명서](https://learn.hashicorp.com/collections/terraform/azure-get-started?utm_source=WEBSITE&utm_medium=WEB_IO&utm_offer=ARTICLE_PAGE&utm_content=DOCS) 및 [Azure 설명서의 Terraform은](/azure/developer/terraform/) 더 자세히 설명하며 Terraform이 Azure 인프라 전략의 일부인지 검토해야 합니다.

1. 샘플 Terraform 코드를 테스트 및 실행할 디렉터리를 만들고 현재 디렉터리로 만듭니다.

    ```bash
    mkdir appservice_tf_quickstart
    cd appservice_tf_quickstart
    ```

1. 라는 파일을 만들고 `main.tf` 위의 코드를 삽입합니다.

    ```bash
    code main.tf
    ```

1. Terraform을 초기화합니다.

    ```bash
    terraform init
    ```

1. Terraform 계획을 만듭니다.

    ```bash
    terraform plan
    ```

1. 구성 파일에 정의된 리소스를 `main.tf` 프로비전합니다(프롬프트에 를 입력하여 작업을 `yes` 확인).

    ```bash
    terraform apply
    ```

## <a name="validate-the-deployment"></a>배포 유효성 검사

1. Azure Portal 주 메뉴에서 **리소스 그룹을** 선택하고 위의 템플릿을 사용하여 만든 리소스 그룹으로 이동합니다. 이름이 "myResourceGroup-"이고 그 뒤에 임의의 정수 문자열이 묶입니다.

1. 이제 Terraform에서 만든 모든 리소스(App Service 및 App Service 계획)가 표시됩니다.

1. **App Service** 선택하고 URL로 이동하여 사이트가 제대로 생성되었는지 확인합니다. 대신 `http://<app_name>.azurewebsites.net/` 앱 이름이 "webapp-"인 위치로 이동한 다음 리소스 그룹에서 동일한 임의의 정수 문자열을 찾아볼 수 있습니다.

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우 [리소스 그룹을 삭제하거나](../azure-resource-manager/management/delete-resource-group.md?tabs=azure-portal#delete-resource-group) 터미널/명령줄로 돌아가서 `terraform destroy` 를 실행하여 이 빠른 시작과 연결된 모든 리소스를 삭제합니다.

> [!NOTE]
> 자세한 Azure App Service Terraform 샘플은 여기에서 찾을 수 [있습니다.](./samples-terraform.md) 여기에서 모든 Azure 서비스에서 더 많은 Terraform 샘플을 찾을 수 [있습니다.](https://github.com/hashicorp/terraform-provider-azurerm/tree/main/examples)
## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"] 
> [Azure에서 Terraform을 사용하는 방법에 대해 자세히 알아보기](/azure/terraform)
> [!div class="nextstepaction"] 
> [Azure App Service에 대한 Terraform 샘플](./samples-terraform.md)