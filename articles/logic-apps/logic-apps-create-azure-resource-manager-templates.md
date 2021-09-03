---
title: 논리 앱 배포 템플릿 만들기
description: Azure Logic Apps에서 배포를 자동화하기 위해 Azure Resource Manager 템플릿을 만드는 방법을 알아봅니다.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, azla
ms.topic: how-to
ms.date: 07/20/2021
ms.openlocfilehash: 3378c04fd6a43d50b670767d1d3fc4f59043e1e9
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114456606"
---
# <a name="create-azure-resource-manager-templates-to-automate-deployment-for-azure-logic-apps"></a>Azure Logic Apps에 대한 배포를 자동화하는 Azure Resource Manager 템플릿 만들기

논리 앱 만들기 및 배포를 자동화하는 데 도움이 되도록 이 문서에서는 논리 앱용 [Azure Resource Manager 템플릿](../azure-resource-manager/management/overview.md)을 만들 수 있는 방법을 설명합니다. 워크플로 정의 및 배포에 필요한 기타 리소스를 포함하는 템플릿의 구조 및 구문에 대한 개요는 [개요: Azure Resource Manager 템플릿을 사용하여 논리 앱 배포 자동화](logic-apps-azure-resource-manager-templates-overview.md)를 참조하세요.

Azure Logic Apps는 논리 앱을 만들고 배포에 사용할 리소스와 매개 변수를 정의하기 위해 재사용할 수 있는 [미리 작성된 논리 앱 Azure Resource Manager 템플릿](https://github.com/Azure/azure-quickstart-templates/blob/master/quickstarts/microsoft.logic/logic-app-create/azuredeploy.json)을 제공합니다. 이 템플릿을 자체 비즈니스 시나리오에 사용하거나 요구 사항에 맞게 사용자 지정할 수 있습니다.

> [!IMPORTANT]
> 템플릿의 연결이 논리 앱과 동일한 Azure 리소스 그룹 및 위치를 사용하는지 확인합니다.

Azure Resource Manager 템플릿에 대한 자세한 내용은 다음 항목을 참조하세요.

* [Azure Resource Manager 템플릿 구조 및 구문](../azure-resource-manager/templates/syntax.md)
* [Azure 리소스 관리자 템플릿 작성](../azure-resource-manager/templates/syntax.md)
* [클라우드 일관성을 위한 Azure Resource Manager 템플릿 개발](../azure-resource-manager/templates/template-cloud-consistency.md)

<a name="visual-studio"></a>

## <a name="create-templates-with-visual-studio"></a>Visual Studio를 사용하여 템플릿 만들기

대부분 배포 준비가 된 유효한 매개 변수가 있는 논리 앱 템플릿을 만드는 가장 쉬운 방법은 Visual Studio(무료 Community Edition 이상) 및 Visual Studio용 Azure Logic Apps 도구를 사용하는 것입니다. 그런 다음, [Visual studio에서 논리 앱을 만들거나](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md) [ Azure Portal에서 기존 논리 앱을 찾아 Visual Studio로 다운로드](../logic-apps/manage-logic-apps-with-visual-studio.md)할 수 있습니다.

논리 앱을 다운로드하면 논리 앱의 정의와 연결 등의 기타 리소스를 포함하는 템플릿을 사용할 수 있습니다. 또한 템플릿은 논리 앱과 기타 리소스를 배포하는 데 사용되는 값을 ‘매개 변수화’하거나 해당 매개 변수를 정의합니다. 별도의 매개 변수 파일에서 매개 변수의 값을 제공할 수 있습니다. 이렇게 하면 배포 요구 사항에 따라 값을 보다 쉽게 변경할 수 있습니다. 자세한 내용은 다음 항목을 참조하세요.

* [Visual Studio로 논리 앱 만들기](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)
* [Visual Studio에서 논리 앱 관리](../logic-apps/manage-logic-apps-with-visual-studio.md)

<a name="azure-powershell"></a>

## <a name="create-templates-with-azure-powershell"></a>Azure PowerShell을 사용하여 템플릿 만들기

[LogicAppTemplate 모듈](https://github.com/jeffhollan/LogicAppTemplateCreator)에서 Azure PowerShell을 사용하여 논리 앱 템플릿을 만들 수 있습니다. 이 오픈 소스 모듈은 논리 앱 및 논리 앱이 사용하는 모든 연결을 먼저 평가합니다. 그런 다음, 배포에 필요한 매개 변수를 사용하여 템플릿 리소스를 생성합니다.

예를 들어 Azure Service Bus 큐에서 메시지를 받고 Azure SQL Database에 데이터를 업로드하는 논리 앱이 있다고 가정합니다. 모듈은 모든 오케스트레이션 논리를 보존하고 SQL 및 Service Bus 연결 문자열을 매개 변수화하여 배포 요구 사항에 따라 해당 값을 제공하고 변경할 수 있도록 합니다.

다음 샘플에서는 Azure Resource Manager 템플릿, Azure DevOps의 Azure Pipelines, Azure PowerShell을 사용하여 논리 앱을 만들고 배포하는 방법을 보여 줍니다.

* [샘플: Azure Logic Apps를 사용하여 Azure Pipelines 오케스트레이션](https://github.com/Azure-Samples/azure-logic-apps-pipeline-orchestration)
* [샘플: Azure Logic Apps에서 Azure Storage 계정에 연결 및 Azure DevOps에서 Azure Pipelines를 사용하여 배포](https://github.com/Azure-Samples/azure-logic-apps-deployment-samples/tree/master/storage-account-connections)
* [샘플: Azure Logic Apps에서 Azure Service Bus 큐에 연결 및 Azure DevOps에서 Azure Pipelines를 사용하여 배포](https://github.com/Azure-Samples/azure-logic-apps-deployment-samples/tree/master/service-bus-connections)
* [샘플: Azure Logic Apps에 대한 Azure Functions 작업 설정 및 Azure DevOps에서 Azure Pipelines를 사용하여 배포](https://github.com/Azure-Samples/azure-logic-apps-deployment-samples/tree/master/function-app-actions)
* [샘플: Azure Logic Apps에서 통합 계정에 연결 및 Azure DevOps에서 Azure Pipelines를 사용하여 배포](https://github.com/Azure-Samples/azure-logic-apps-deployment-samples/tree/master/integration-account-connections)

### <a name="install-powershell-modules"></a>PowerShell 모듈 설치

1. 아직 설치하지 않은 경우 [Azure PowerShell](/powershell/azure/install-az-ps)을 설치합니다.

1. [PowerShell 갤러리](https://www.powershellgallery.com/packages/LogicAppTemplate)에서 LogicAppTemplate 모듈을 설치하는 가장 쉬운 방법인 다음 명령을 실행합니다.

   ```powershell
   Install-Module -Name LogicAppTemplate
   ```

   최신 버전으로 업데이트하려면 다음 명령을 실행합니다.

   ```powershell
   Update-Module -Name LogicAppTemplate
   ```

또는 수동으로 설치하려면 GitHub에서 [논리 앱 템플릿 작성자](https://github.com/jeffhollan/LogicAppTemplateCreator)를 위한 단계를 수행합니다.

### <a name="install-azure-resource-manager-client"></a>Azure Resource Manager 클라이언트 설치

LogicAppTemplate 모듈에서 Azure 테넌트 및 구독 액세스 토큰을 사용하려면 Azure Resource Manager API를 호출하는 간단한 명령줄 도구인 [Azure Resource Manager 클라이언트 도구](https://github.com/projectkudu/ARMClient)를 설치합니다.

이 도구를 사용하여 `Get-LogicAppTemplate` 명령을 실행하는 경우 명령은 먼저 ARMClient 도구를 통해 액세스 토큰을 가져오고 토큰을 PowerShell 스크립트로 파이프한 다음, 템플릿을 JSON 파일로 만듭니다. 도구에 대한 자세한 내용은 [Azure Resource Manager 클라이언트 도구에 대한 문서](https://blog.davidebbo.com/2015/01/azure-resource-manager-client.html)를 참조하세요.

### <a name="generate-template-with-powershell"></a>PowerShell을 사용하여 템플릿 생성

LogicAppTemplate 모듈 및 [Azure CLI](/cli/azure/)를 설치한 후에 템플릿을 생성하려면 다음 PowerShell 명령을 실행합니다.

```powershell
$parameters = @{
    Token = (az account get-access-token | ConvertFrom-Json).accessToken
    LogicApp = '<logic-app-name>'
    ResourceGroup = '<Azure-resource-group-name>'
    SubscriptionId = $SubscriptionId
    Verbose = $true
}

Get-LogicAppTemplate @parameters | Out-File C:\template.json
```

[Azure Resource Manager 클라이언트 도구](https://github.com/projectkudu/ARMClient)에서 토큰을 파이프하는 방법에 대한 권장 사항을 따르려면 다음 명령을 대신 실행합니다. 여기서 `$SubscriptionId`는 사용자의 Azure 구독 ID입니다.

```powershell
$parameters = @{
    LogicApp = '<logic-app-name>'
    ResourceGroup = '<Azure-resource-group-name>'
    SubscriptionId = $SubscriptionId
    Verbose = $true
}

armclient token $SubscriptionId | Get-LogicAppTemplate @parameters | Out-File C:\template.json
```

추출 후에는 다음 명령을 실행하여 템플릿에서 매개 변수 파일을 만들 수 있습니다.

```powershell
Get-ParameterTemplate -TemplateFile $filename | Out-File '<parameters-file-name>.json'
```

Azure Key Vault 참조(정적만 해당)를 사용하여 추출하려면 다음 명령을 실행합니다.

```powershell
Get-ParameterTemplate -TemplateFile $filename -KeyVault Static | Out-File $fileNameParameter
```

| 매개 변수 | 필수 | 설명 |
|------------|----------|-------------|
| TemplateFile | Yes | 템플릿 파일의 파일 경로입니다. |
| KeyVault | 아니요 | 가능한 키 자격 증명 모음 값을 처리하는 방법을 설명하는 열거형입니다. 기본값은 `None`입니다. |
||||

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [논리 앱 템플릿 배포](../logic-apps/logic-apps-deploy-azure-resource-manager-templates.md)