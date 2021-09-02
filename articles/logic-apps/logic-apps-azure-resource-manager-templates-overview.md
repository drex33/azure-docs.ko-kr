---
title: 개요 - Azure Logic Apps의 배포 자동화
description: Azure Logic Apps에 대한 배포를 자동화하는 Azure Resource Manager 템플릿 자세히 알아보기
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, azla
ms.topic: article
ms.date: 11/06/2020
ms.openlocfilehash: 538ee3d5dec64e2558297005a600cfd1662a322a
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/30/2021
ms.locfileid: "123223892"
---
# <a name="overview-automate-deployment-for-azure-logic-apps-by-using-azure-resource-manager-templates"></a>개요: Azure Resource Manager 템플릿을 사용하여 Azure Logic Apps에 대한 배포 자동화

논리 앱 만들기 및 배포를 자동화할 준비가 되면 논리 앱의 기본 워크플로 정의를 [Azure Resource Manager 템플릿](../azure-resource-manager/management/overview.md)으로 확장할 수 있습니다. 이 템플릿은 논리 앱을 프로비전하고 배포하기 위한 인프라, 리소스, 매개 변수 및 기타 정보를 정의합니다. 배포 시 다양한 값에 대한 매개 변수를 정의하여(*매개 변수화* 라고도 함) 다양한 배포 요구 사항에 따라 논리 앱을 반복적이고 일관되게 배포할 수 있습니다.

예를 들어 개발, 테스트 및 프로덕션 환경에 배포하는 경우 각 환경에 대해 서로 다른 연결 문자열을 사용할 수 있습니다. 서로 다른 연결 문자열을 허용하는 템플릿 매개 변수를 선언한 후 이러한 문자열을 별도의 [매개 변수 파일](../azure-resource-manager/templates/parameter-files.md)에 저장할 수 있습니다. 그러면 템플릿을 업데이트하고 다시 배포하지 않고도 이러한 값을 변경할 수 있습니다. 암호 및 비밀과 같이 중요하거나 보안을 유지해야 하는 매개 변수 값이 있는 시나리오의 경우 해당 값을 [Azure Key Vault](../azure-resource-manager/templates/key-vault-parameter.md)에 저장하고 매개 변수 파일에서 해당 값을 검색할 수 있습니다. 그러나 이 시나리오에서는 다시 배포하여 현재 값을 검색합니다.

이 개요에서는 논리 앱 워크플로 정의를 포함하는 Resource Manager 템플릿의 특성에 대해 설명합니다. 템플릿과 워크플로 정의 모두 JSON 구문을 사용하지만 워크플로 정의도 [워크플로 정의 언어 스키마](../logic-apps/logic-apps-workflow-definition-language.md)를 따르기 때문에 몇 가지 차이점이 있습니다. 예를 들어 템플릿 식과 워크플로 정의 식은 [매개 변수를 참조](#parameter-references)하는 방식과 허용할 수 있는 값이 다릅니다.

> [!TIP]
> 대부분 배포 준비가 된 유효한 매개 변수가 있는 논리 앱 템플릿을 가져오는 가장 쉬운 방법은 Visual Studio(무료 Community Edition 이상) 및 Visual Studio용 Azure Logic Apps 도구를 사용하는 것입니다. 그런 다음, [Visual studio에서 논리 앱을 만들거나](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md) [Azure에서 기존 논리 앱을 찾아 Visual Studio로 다운로드](../logic-apps/manage-logic-apps-with-visual-studio.md)할 수 있습니다.
>
> 또는 [LogicAppTemplate 모듈과 함께 Azure PowerShell](../logic-apps/logic-apps-create-azure-resource-manager-templates.md#azure-powershell)을 사용하여 논리 앱 템플릿을 만들 수 있습니다.

이 항목의 예제 논리 앱은 새 메일이 도착할 때 실행되는 [Office 365 Outlook 트리거](/connectors/office365/)와 이메일 본문에 대한 Blob을 만들고 해당 Blob을 Azure Storage 컨테이너에 업로드하는 [Azure Blob Storage 작업](/connectors/azureblob/)을 사용합니다. 또한 이 예제에서는 배포에 따라 달라지는 값을 매개 변수화하는 방법을 보여 줍니다.

Resource Manager 템플릿에 대한 자세한 내용은 다음 항목을 참조하세요.

* [Azure Resource Manager 템플릿 구조 및 구문](../azure-resource-manager/templates/syntax.md)
* [Azure Resource Manager 템플릿 모범 사례](../azure-resource-manager/templates/best-practices.md)
* [클라우드 일관성을 위한 Azure Resource Manager 템플릿 개발](../azure-resource-manager/templates/template-cloud-consistency.md)

논리 앱, 통합 계정, 통합 계정 아티팩트 및 통합 서비스 환경과 관련된 템플릿 리소스 정보는 [Microsoft.Logic 리소스 종류](/azure/templates/microsoft.logic/allversions)를 참조하세요.

샘플 논리 앱 템플릿은 다음 예제를 참조하세요.

* 이 항목의 예제에 사용되는 [전체 템플릿](#full-example-template)
* GitHub의 [샘플 빠른 시작 논리 앱 템플릿](https://github.com/Azure/azure-quickstart-templates/blob/master/quickstarts/microsoft.logic/logic-app-create/azuredeploy.json)

Logic Apps REST API의 경우 [Azure Logic Apps REST API 개요](/rest/api/logic)부터 시작합니다.

<a name="template-structure"></a>

## <a name="template-structure"></a>템플릿 구조

최상위 수준에서 Resource Manager 템플릿은 [Azure Resource Manager 템플릿 구조 및 구문](../azure-resource-manager/templates/syntax.md) 항목에 자세히 설명된 이 구조를 따릅니다.

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {},
   "variables": {},
   "functions": [],
   "resources": [],
   "outputs": {}
}
```

논리 앱 템플릿의 경우 주로 다음과 같은 템플릿 개체를 사용합니다.

| attribute | 설명 |
|-----------|-------------|
| `parameters` | Azure에서 배포할 리소스를 만들고 사용자 지정할 때 사용할 값을 허용하기 위한 [템플릿 매개 변수](../azure-resource-manager/templates/syntax.md#parameters)를 선언합니다. 예를 들어 이러한 매개 변수는 논리 앱의 이름과 위치, 연결 및 배포에 필요한 기타 리소스에 대한 값을 허용합니다. 이러한 매개 변수 값은 이 항목의 뒷부분에서 설명하는 [매개 변수 파일](#template-parameter-files)에 저장할 수 있습니다. 일반적인 세부 정보는 [매개 변수 - Resource Manager 템플릿 구조 및 구문](../azure-resource-manager/templates/syntax.md#parameters)을 참조하세요. |
| `resources` | 논리 앱, 연결, Azure Storage 계정 등과 같은 Azure 리소스 그룹을 만들거나 업데이트하고 배포할 [리소스](../azure-resource-manager/templates/syntax.md#resources)를 정의합니다. 일반적인 세부 정보는 [리소스 - Resource Manager 템플릿 구조 및 구문](../azure-resource-manager/templates/syntax.md#resources)을 참조하세요. |
|||

논리 앱 템플릿은 다음 파일 이름 형식을 사용합니다.

**<*logic-app-name*>.json**

> [!IMPORTANT]
> 템플릿 구문은 대/소문자를 구분하므로 일관된 대/소문자를 사용해야 합니다. 

<a name="template-parameters"></a>

## <a name="template-parameters"></a>템플릿 매개 변수

논리 앱 템플릿에는 서로 다른 수준에 존재하는 여러 `parameters` 개체가 있으며 다른 기능을 수행합니다. 예를 들어 최상위 수준에서 Azure에서 리소스를 만들고 배포하는 경우 배포 시 사용할 값에 대한 [템플릿 매개 변수](../azure-resource-manager/templates/syntax.md#parameters)를 선언할 수 있습니다. 예를 들면 다음과 같습니다.

* 논리 앱
* 논리가 [관리되는 커넥터](../connectors/apis-list.md)를 통해 다른 서비스 및 시스템에 액세스하는 데 사용하는 연결
* 논리 앱이 배포에 필요한 기타 리소스

  예를 들어 논리 앱이 B2B(Business-to-Business) 시나리오에 [통합 계정](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)을 사용하는 경우 템플릿의 최상위 수준 `parameters` 개체는 해당 통합 계정의 리소스 ID를 허용하는 매개 변수를 선언합니다.

다음은 [매개 변수 - Resource Manager 템플릿 구조 및 구문 ](../azure-resource-manager/templates/syntax.md#parameters)에서 자세히 설명하는 매개 변수 정의의 일반 구조 및 구문입니다.

```json
"<parameter-name>": {
   "type": "<parameter-type>",
   "defaultValue": <default-parameter-value>,
   <other-parameter-attributes>,
   "metadata": {
      "description": "<parameter-description>"
   }
},
```

이 예제에서는 Azure에서 이러한 리소스를 만들고 배포하는 데 사용되는 값에 대한 템플릿 매개 변수만 보여 줍니다.

* 논리 앱의 이름 및 위치
* 논리 앱에 연결된 통합 계정에 사용할 ID

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   // Template parameters
   "parameters": {
      "LogicAppName": {
         "type": "string",
         "minLength": 1,
         "maxLength": 80,
         "defaultValue": "MyLogicApp",
         "metadata": {
            "description": "The resource name for the logic app"
         }
      },
      "LogicAppLocation": {
         "type": "string",
         "minLength": 1,
         "defaultValue": "[resourceGroup().location]",
         "metadata": {
            "description": "The resource location for the logic app"
         }
      },
      "LogicAppIntegrationAccount": {
         "type":"string",
         "minLength": 1,
         "defaultValue": "/subscriptions/<Azure-subscription-ID>/resourceGroups/fabrikam-integration-account-rg/providers/Microsoft.Logic/integrationAccounts/fabrikam-integration-account",
         "metadata": {
            "description": "The ID to use for the integration account"
         }
      }
   },
   "variables": {},
   "functions": [],
   "resources": [],
   "outputs": {}
}
```

사용자 이름, 암호 및 비밀과 같이 중요하거나 보안을 유지해야 하는 값을 처리하는 매개 변수를 제외하고, 이러한 모든 매개 변수에는 `defaultValue` 특성이 포함됩니다. 하지만 일부 경우 기본값은 비어 있습니다. 이러한 템플릿 매개 변수에 사용할 배포 값은 이 항목의 뒷부분에 설명된 샘플 [매개 변수 파일](#template-parameter-files)에서 제공됩니다.

템플릿 매개 변수를 보호하는 방법에 대한 자세한 내용은 다음 항목을 참조하세요.

* [템플릿 매개 변수에 대한 보안 권장 사항](../azure-resource-manager/templates/best-practices.md#parameters)
* [템플릿 매개 변수에 대한 보안 향상](../logic-apps/logic-apps-securing-a-logic-app.md#secure-parameters-deployment-template)
* [Azure Key Vault를 사용하여 안전한 매개 변수 값 전달](../azure-resource-manager/templates/key-vault-parameter.md)

다른 템플릿 개체는 템플릿 매개 변수를 참조하는 경우가 많으므로 템플릿 매개 변수를 통해 전달하는 값을 사용할 수 있습니다. 예를 들면 다음과 같습니다.

* 이 항목의 뒷부분에서 설명하는 [템플릿의 리소스 개체](#template-resources)는 [논리 앱의 리소스 정의](#logic-app-resource-definition)와 같이 만들고 배포할 Azure의 각 리소스를 정의합니다. 이러한 리소스는 종종 논리 앱의 이름, 위치 및 연결 정보 등의 템플릿 매개 변수 값을 사용합니다.

* 논리 앱의 리소스 정의에서 더 심층적인 수준의 [워크플로 정의의 매개 변수 개체](#workflow-definition-parameters)는 논리 앱의 런타임에 사용할 값에 대한 매개 변수를 선언합니다. 예를 들어 HTTP 트리거가 인증에 사용하는 사용자 이름 및 암호에 대한 워크플로 정의 매개 변수를 선언할 수 있습니다. 워크플로 정의 매개 변수의 값을 지정하려면 워크 플로 정의 *외부* 이지만 논리 앱의 리소스 정의 *내부* 에 있는 `parameters` 개체를 사용합니다. 이 외부 `parameters` 개체에서는 이전에 선언된 템플릿 매개 변수를 참조할 수 있습니다. 이 매개 변수는 배치 시 매개 변수 파일에서 값을 허용할 수 있습니다.

매개 변수를 참조할 때 템플릿 식과 함수는 다른 구문을 사용하며 워크플로 정의 식 및 함수와는 다르게 동작합니다. 이러한 차이점에 대한 자세한 내용은 이 항목의 뒷부분에 나오는 [매개 변수에 대한 참조](#parameter-references)를 참조하세요.

<a name="best-practices-template-parameters"></a>

## <a name="best-practices---template-parameters"></a>모범 사례 - 템플릿 매개 변수

매개 변수를 정의하는 몇 가지 모범 사례는 다음과 같습니다.

* 배포 요구 사항에 따라 달라지는 값에 대해서만 매개 변수를 선언합니다. 여러 배포 요구 사항에서 동일하게 유지되는 값에는 매개 변수를 선언하지 마세요.

* `defaultValue`중요하거나 보안을 유지해야 하는 값을 제외한 모든 매개 변수에 대해 빈 값을 지정할 수 있는 특성을 포함합니다. 사용자 이름, 암호 및 비밀에는 항상 보안 매개 변수를 사용합니다. 중요한 매개 변수 값을 숨기거나 보호하려면 다음 항목의 지침을 따르세요.

  * [템플릿 매개 변수에 대한 보안 권장 사항](../azure-resource-manager/templates/best-practices.md#parameters)

  * [템플릿 매개 변수에 대한 보안 향상](../logic-apps/logic-apps-securing-a-logic-app.md#secure-parameters-deployment-template)

  * [Azure Key Vault를 사용하여 안전한 매개 변수 값 전달](../azure-resource-manager/templates/key-vault-parameter.md)

* 템플릿 매개 변수 이름을 워크플로 정의 매개 변수 이름과 구분하기 위해 다음과 같이 설명이 포함된 템플릿 매개 변수 이름을 사용할 수 있습니다. `TemplateFabrikamPassword`

자세한 템플릿 모범 사례는 [템플릿 매개 변수에 대한 모범 사례](../azure-resource-manager/templates/best-practices.md#parameters)를 참조하세요.

<a name="template-parameter-files"></a>

## <a name="template-parameters-file"></a>템플릿 매개 변수 파일

템플릿 매개 변수에 대한 값을 제공하려면 해당 값을 [매개 변수 파일](../azure-resource-manager/templates/parameter-files.md)에 저장합니다. 이렇게 하면 배포 요구 사항에 따라 다양한 매개 변수 파일을 사용할 수 있습니다. 사용할 파일 이름 형식은 다음과 같습니다.

* 논리 앱 템플릿 파일 이름: **<*logic-app-name*>.json**
* 매개 변수 파일 이름: **<*logic-app-name*>.parameters.json**

다음은 [Azure Key Vault를 사용하여 보안 매개 변수 값을 전달](../azure-resource-manager/templates/key-vault-parameter.md)하기 위한 주요 자격 증명 모음 참조가 포함된 매개 변수 파일 내 구조입니다.

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
   "contentVersion": "1.0.0.0",
   // Template parameter values
   "parameters": {
      "<parameter-name-1>": {
         "value": "<parameter-value>"
      },
      "<parameter-name-2>": {
         "value": "<parameter-value>"
      },
      "<secured-parameter-name>": {
         "reference": {
            "keyVault": {
               "id": "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/Microsoft.KeyVault/vaults/<key-vault-name>"
            },
            "secretName: "<secret-name>"
         }
      },
      <other-parameter-values>
   }
}
```

이 예제 매개 변수 파일은 이 항목의 앞부분에 선언된 템플릿 매개 변수의 값을 지정합니다.

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
   "contentVersion": "1.0.0.0",
   // Template parameter values
   "parameters": {
      "LogicAppName": {
         "value": "Email-Processor-Logic-App"
      },
      "LogicAppLocation": {
         "value": "westeurope"
      }
   }
}
```

<a name="template-resources"></a>

## <a name="template-resources"></a>템플릿 리소스

템플릿에는 [논리 앱의 리소스 정의](#logic-app-resource-definition), [연결 리소스 정의](#connection-resource-definitions) 및 배포에 논리 앱이 필요한 기타 리소스와 같이 Azure에서 만들고 배포할 각 리소스에 대한 정의가 포함된 배열인 `resources` 개체가 있습니다.

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {<template-parameters>},
   "variables": {},
   "functions": [],
   "resources": [
      {
         <logic-app-resource-definition>
      },
      // Start connection resource definitions
      {
         <connection-resource-definition-1>
      },
      {
         <connection-resource-definition-2>
      }
   ],
   "outputs": {}
}
```

> [!NOTE]
> 템플릿에는 여러 논리 앱에 대한 리소스 정의가 포함될 수 있으므로 모든 논리 앱 리소스가 동일한 Azure 리소스 그룹을 지정해야 합니다. Visual Studio를 사용하여 Azure 리소스 그룹에 템플릿을 배포하는 경우 열려는 논리 앱을 묻는 메시지가 표시됩니다. 또한 Azure 리소스 그룹 프로젝트에는 둘 이상의 템플릿이 포함될 수 있으므로 메시지가 표시되면 올바른 매개 변수 파일을 선택해야 합니다.

<a name="view-resource-definitions"></a>

### <a name="view-resource-definitions"></a>리소스 정의 보기

Azure 리소스 그룹의 모든 리소스에 대한 리소스 정의를 검토하려면 [Azure에서 Visual Studio로 논리 앱을 다운로드](../logic-apps/manage-logic-apps-with-visual-studio.md)하세요. 이는 거의 배포 준비가 완료된 유효한 매개 변수화된 논리 앱 템플릿을 만드는 가장 쉬운 방법입니다.

템플릿 리소스 및 해당 특성에 대한 일반적인 정보는 다음 항목을 참조하세요.

* [리소스 - Resource Manager 템플릿 구조 및 구문](../azure-resource-manager/templates/syntax.md#resources)
* [템플릿 리소스에 대한 모범 사례](../azure-resource-manager/templates/best-practices.md#resources)

<a name="logic-app-resource-definition"></a>

### <a name="logic-app-resource-definition"></a>논리 앱 리소스 정의

논리 앱의 [템플릿에서 워크플로 리소스 정의](/azure/templates/microsoft.logic/workflows)는 다음 정보를 포함하는 `properties` 개체로 시작합니다.

* 배포 시 논리 앱의 상태
* 논리 앱에서 사용 하는 모든 통합 계정의 ID
* 논리 앱의 워크플로 정의
* 런타임에 사용할 값을 설정하는 `parameters` 개체
* 논리 앱에 대한 기타 리소스 정보(예: 이름, 유형, 위치, 런타임 구성 설정 등)

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {<template-parameters>},
   "variables": {},
   "functions": [],
   "resources": [
      {
         // Start logic app resource definition
         "properties": {
            "state": "<Enabled or Disabled>",
            "integrationAccount": {
               "id": "[parameters('LogicAppIntegrationAccount')]" // Template parameter reference
            },
            "definition": {<workflow-definition>},
            "parameters": {<workflow-definition-parameter-values>},
            "accessControl": {},
            "runtimeConfiguration": {}
         },
         "name": "[parameters('LogicAppName')]", // Template parameter reference
         "type": "Microsoft.Logic/workflows",
         "location": "[parameters('LogicAppLocation')]", // Template parameter reference
         "tags": {
           "displayName": "LogicApp"
         },
         "apiVersion": "2016-06-01",
         "dependsOn": [
         ]
      }
      // End logic app resource definition
   ],
   "outputs": {}
}
```

논리 앱 리소스 정의와 관련된 특성은 다음과 같습니다.

| attribute | 필수 | Type | 설명 |
|-----------|----------|------|-------------|
| `state` | 예 | String | 배포 시 논리 앱의 상태입니다. 여기서 `Enabled`는 논리 앱이 활성 상태이며 `Disabled`는 논리 앱이 비활성 상태임을 의미합니다. 예를 들어 논리 앱을 활성 상태로 전환할 준비가 되지 않았지만 초안 버전을 배포하려는 경우 `Disabled` 옵션을 사용할 수 있습니다. |
| `integrationAccount` | No | 개체 | 논리 앱이 B2B(Business-to-Business) 시나리오에 대한 아티팩트를 저장하는 통합 계정을 사용하는 경우 이 개체에는 통합 계정의 ID를 지정하는 `id` 속성이 포함됩니다. |
| `definition` | 예 | Object | 논리 앱의 기본 워크플로 정의는 코드 보기에 표시되는 동일한 개체이며 [워크플로 정의 언어에 대한 스키마 참조](../logic-apps/logic-apps-workflow-definition-language.md) 항목에 자세히 설명되어 있습니다. 이 워크플로 정의에서 `parameters` 개체는 논리 앱 런타임에 사용할 값에 대한 매개 변수를 선언합니다. 자세한 내용은 [워크플로 정의 및 매개 변수](#workflow-definition-parameters)를 참조하세요. <p><p>논리 앱의 워크플로 정의에서 특성을 보려면 Azure Portal 또는 Visual Studio에서 "디자인 뷰"를 "코드 보기"로 전환하거나 [Azure Resource Explorer](https://resources.azure.com)와 같은 도구를 사용하여 전환합니다. |
| `parameters` | No | 개체 | 논리 앱 런타임에 사용할 [워크플로 정의 매개 변수 값](#workflow-definition-parameters)입니다. 이러한 값에 대한 매개 변수 정의는 [워크플로 정의의 매개 변수 개체](#workflow-definition-parameters) 내에 표시됩니다. 또한 논리 앱이 [관리되는 커넥터](../connectors/apis-list.md)를 사용하여 다른 서비스 및 시스템에 액세스하는 경우 이 개체에는 런타임에 사용할 연결 값을 설정하는 `$connections` 개체가 포함됩니다. |
| `accessControl` | No | 개체 | 요청 트리거 또는 실행 기록 입력 및 출력에 대한 IP 액세스 제한과 같은 논리 앱의 보안 속성을 지정합니다. 자세한 내용은 [논리 앱에 대한 액세스 보호](../logic-apps/logic-apps-securing-a-logic-app.md)를 참조하세요. |
| `runtimeConfiguration` | No | 개체 | 런타임에 논리 앱이 작동하는 방식을 제어하는 `operationOptions` 속성을 지정합니다. 예를 들어 [높은 처리량 모드](../logic-apps/logic-apps-limits-and-config.md#run-high-throughput-mode)에서 논리 앱을 실행할 수 있습니다. |
|||||

이러한 논리 앱 개체의 리소스 정의에 대한 자세한 내용은 [Microsoft.Logic 리소스 종류](/azure/templates/microsoft.logic/allversions)를 참조하세요.

* [워크플로 리소스 정의](/azure/templates/microsoft.logic/workflows)
* [통합 서비스 환경 리소스 정의](/azure/templates/microsoft.logic/integrationserviceenvironments)
* [통합 서비스 환경 관리형 API 리소스 정의](/azure/templates/microsoft.logic/integrationserviceenvironments/managedapis)

* [통합 계정 리소스 정의](/azure/templates/microsoft.logic/integrationaccounts)

* 통합 계정 아티팩트:

  * [규약 리소스 정의](/azure/templates/microsoft.logic/integrationaccounts/agreements)

  * [어셈블리 리소스 정의](/azure/templates/microsoft.logic/integrationaccounts/assemblies)

  * [일괄 처리 구성 리소스 정의](/azure/templates/microsoft.logic/integrationaccounts/batchconfigurations)

  * [인증서 리소스 정의](/azure/templates/microsoft.logic/integrationaccounts/certificates)

  * [맵 리소스 정의](/azure/templates/microsoft.logic/integrationaccounts/maps)

  * [파트너 리소스 정의](/azure/templates/microsoft.logic/integrationaccounts/partners)

  * [스키마 리소스 정의](/azure/templates/microsoft.logic/integrationaccounts/schemas)

  * [세션 리소스 정의](/azure/templates/microsoft.logic/integrationaccounts/sessions)

<a name="workflow-definition-parameters"></a>

## <a name="workflow-definition-and-parameters"></a>워크플로 정의 및 매개 변수

논리 앱의 워크플로 정의가 `definition` 개체에 표시됩니다. 이 개체는 논리 앱의 리소스 정의 내에 있는 `properties` 개체에 표시됩니다. 이 `definition` 개체는 코드 보기에 표시되는 것과 동일한 개체이며 [워크플로 정의 언어에 대한 스키마 참조](../logic-apps/logic-apps-workflow-definition-language.md) 항목에 자세히 설명되어 있습니다. 워크플로 정의에는 런타임에 워크플로 정의에서 사용하는 값에 대해 새 매개 변수를 정의하거나 기존 매개 변수를 편집할 수 있는 내부 `parameters` 선언 개체가 포함됩니다. 그런 다음 워크플로의 트리거 또는 작업 내에서 이러한 매개 변수를 참조할 수 있습니다. 기본적으로 논리 앱이 [관리되는 커넥터](../connectors/apis-list.md)를 통해 다른 서비스 및 시스템에 대한 연결을 생성하지 않는 한 이 `parameters` 개체는 비어 있습니다.

워크플로 정의 매개 변수에 대한 값을 설정하려면 워크플로 정의 *외부* 이지만 논리 앱의 리소스 정의 *내부* 에 있는 `parameters` 개체를 사용합니다. 그러면 이 외부 `parameters` 개체에서 이전에 선언된 템플릿 매개 변수를 참조할 수 있으며 매개 변수 파일에서 배포 시 값을 사용할 수 있습니다.

> [!TIP]
>
> 모범 사례로, 워크플로 정의 내에서 배포 시 평가되는 템플릿 매개 변수를 직접 참조하지 마세요. 대신 워크플로 정의 매개 변수를 선언합니다. 그러면 워크플로 정의 *외부* 이지만 논리 앱의 리소스 정의 *내부* 에 있는 `parameters` 개체에 설정할 수 있습니다. 자세한 내용은 [매개 변수에 대한 참조](#parameter-references)를 참조하세요.

이 구문은 템플릿 및 워크플로 정의 매개 변수를 참조하여 해당 매개 변수 값을 설정할 수 있는 위치와 함께 템플릿 및 워크플로 정의 수준 모두에서 매개 변수를 선언할 수 있는 위치를 보여줍니다.

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   // Template parameters
   "parameters": {
      "<template-parameter-name>": {
         "type": "<parameter-type>",
         "defaultValue": "<parameter-default-value>",
         "metadata": {
            "description": "<parameter-description>"
         }
      }
   },
   "variables": {},
   "functions": [],
   "resources": [
      {
         // Start logic app resource definition
         "properties": {
            <other-logic-app-resource-properties>,
            "definition": {
               "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
               "actions": {<action-definitions>},
               // Workflow definition parameters
               "parameters": {
                  "<workflow-definition-parameter-name>": {
                     "type": "<parameter-type>",
                     "defaultValue": "<parameter-default-value>",
                     "metadata": {
                        "description": "<parameter-description>"
                     }
                  }
               },
               "triggers": {
                  "<trigger-name>": {
                     "type": "<trigger-type>",
                     "inputs": {
                         // Workflow definition parameter reference
                         "<attribute-name>": "@parameters('<workflow-definition-parameter-name')"
                     }
                  }
               },
               <...>
            },
            // Workflow definition parameter value
            "parameters": {
               "<workflow-definition-parameter-name>": { 
                  "value": "[parameters('<template-parameter-name>')]"
               }
            },
            "accessControl": {}
         },
         <other-logic-app-resource-definition-attributes>
      }
      // End logic app resource definition
   ],
   "outputs": {}
}
```

<a name="secure-workflow-definition-parameters"></a>

### <a name="secure-workflow-definition-parameters"></a>안전한 워크플로 정의 매개 변수

런타임 시 중요한 정보, 암호, 액세스 키 또는 비밀을 처리하는 워크플로 정의 매개 변수의 경우 `securestring` 또는 `secureobject` 매개 변수 유형을 사용하도록 매개 변수를 선언하거나 편집합니다. 워크플로 정의 내에서 이 매개 변수를 참조할 수 있습니다. 템플릿의 최상위 수준에서 동일한 형식의 매개 변수를 선언하여 배포 시 이 정보를 처리합니다.

워크플로 정의 매개 변수의 값을 설정하려면 워크플로 정의 *외부* 이지만 논리 앱 리소스 정의 *내부* 에 있는 `parameters` 개체를 사용하여 템플릿 매개 변수를 참조합니다. 마지막으로 배포 시 템플릿 매개 변수에 값을 전달하려면 해당 값을 [Azure Key Vault](../azure-resource-manager/templates/key-vault-parameter.md)에 저장하고 배포 시 템플릿에서 사용하는 [매개 변수 파일](#template-parameter-files)에서 해당 Key Vault를 참조합니다.

이 예제 템플릿에서는 필요 시 Azure Key Vault에 값을 저장할 수 있도록 보안 매개 변수를 정의하여 이러한 작업을 완료하는 방법을 보여 줍니다.

* 액세스를 인증하는 데 사용되는 값에 대한 안전한 매개 변수를 선언합니다.
* 템플릿과 워크플로 정의 수준 모두에서 이러한 값을 사용합니다.
* 매개 변수 파일을 사용하여 이러한 값을 제공합니다.

**템플릿**

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {
      <previously-defined-template-parameters>,
      // Additional template parameters for passing values to use in workflow definition
      "TemplateAuthenticationType": {
         "type": "string",
         "defaultValue": "",
         "metadata": {
            "description": "The type of authentication used for the Fabrikam portal"
         }
      },
      "TemplateFabrikamPassword": {
         "type": "securestring",
         "metadata": {
            "description": "The password for the Fabrikam portal"
         }
      },
      "TemplateFabrikamUserName": {
         "type": "securestring",
         "metadata": {
            "description": "The username for the Fabrikam portal"
         }
      }
   },
   "variables": {},
   "functions": [],
   "resources": [
      {
         // Start logic app resource definition
         "properties": {
            <other-logic-app-resource-properties>,
            // Start workflow definition
            "definition": {
               "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
               "actions": {<action-definitions>},
               // Workflow definition parameters
               "parameters": {
                  "authenticationType": {
                     "type": "string",
                     "defaultValue": "",
                     "metadata": {
                        "description": "The type of authentication used for the Fabrikam portal"
                     }
                  },
                  "fabrikamPassword": {
                     "type": "securestring",
                     "metadata": {
                        "description": "The password for the Fabrikam portal"
                     }
                  },
                  "fabrikamUserName": {
                     "type": "securestring",
                     "metadata": {
                        "description": "The username for the Fabrikam portal"
                     }
                  }
               },
               "triggers": {
                  "HTTP": {
                     "inputs": {
                        "authentication": {
                           // Reference workflow definition parameters
                           "password": "@parameters('fabrikamPassword')",
                           "type": "@parameters('authenticationType')",
                           "username": "@parameters('fabrikamUserName')"
                        }
                     },
                     "recurrence": {<...>},
                     "type": "Http"
                  }
               },
               <...>
            },
            // End workflow definition
            // Start workflow definition parameter values
            "parameters": {
               "authenticationType": {
                  "value": "[parameters('TemplateAuthenticationType')]" // Template parameter reference
               },
               "fabrikamPassword": {                  
                  "value": "[parameters('TemplateFabrikamPassword')]" // Template parameter reference
               },
               "fabrikamUserName": {
                  "value": "[parameters('TemplateFabrikamUserName')]" // Template parameter reference
               }
            },
            "accessControl": {}
         },
         <other-logic-app-resource-attributes>
      }
      // End logic app resource definition
   ],
   "outputs": {}
}
```

**매개 변수 파일**

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
   "contentVersion": "1.0.0.0",
   // Template parameter values
   "parameters": {
      <previously-defined-template-parameter-values>,
     "TemplateAuthenticationType": {
        "value": "Basic"
     },
     "TemplateFabrikamPassword": {
        "reference": {
           "keyVault": {
              "id": "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/Microsoft.KeyVault/vaults/fabrikam-key-vault"
           },
           "secretName": "FabrikamPassword"
        }
     },
     "TemplateFabrikamUserName": {
        "reference": {
           "keyVault": {
              "id": "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/Microsoft.KeyVault/vaults/fabrikam-key-vault"
           },
           "secretName": "FabrikamUserName"
        }
     }
   }
}
```

<a name="best-practices-workflow-definition-parameters"></a>

## <a name="best-practices---workflow-definition-parameters"></a>모범 사례 - 워크플로 정의 매개 변수

Logic App 디자이너가 워크플로 정의 매개 변수를 올바르게 표시할 수 있는지 확인하려면 다음 모범 사례를 따르세요.

* `defaultValue`중요하거나 보안을 유지해야 하는 값을 제외한 모든 매개 변수에 대해 빈 값을 지정할 수 있는 특성을 포함합니다.

* 사용자 이름, 암호 및 비밀에는 항상 보안 매개 변수를 사용합니다. 중요한 매개 변수 값을 숨기거나 보호하려면 다음 항목의 지침을 따르세요.

  * [작업 매개 변수에 대한 보안 권장 사항](../logic-apps/logic-apps-securing-a-logic-app.md#secure-action-parameters)

  * [워크플로 정의의 매개 변수에 대한 보안 권장 사항](../logic-apps/logic-apps-securing-a-logic-app.md#secure-parameters-workflow)

  * [Azure Key Vault를 사용하여 보안 매개 변수 값 전달](../azure-resource-manager/templates/key-vault-parameter.md)

워크플로 정의 매개 변수에 대한 자세한 내용은 [매개 변수 - 워크플로 정의 언어](../logic-apps/logic-apps-workflow-definition-language.md#parameters)를 참조하세요.

<a name="connection-resource-definitions"></a>

## <a name="connection-resource-definitions"></a>연결 리소스 정의

논리 앱이 [관리되는 커넥터](../connectors/apis-list.md)를 사용하여 다른 서비스와 시스템에 대한 연결을 만들고 사용하는 경우 템플릿의 `resources` 개체에는 해당 연결에 대한 리소스 정의가 포함됩니다. 논리 앱 내에서 연결을 만들더라도 연결은 자체 리소스 정의가 있는 별도의 Azure 리소스입니다. 이러한 연결 리소스 정의를 검토하려면 [Azure에서 Visual Studio로 논리 앱을 다운로드](../logic-apps/manage-logic-apps-with-visual-studio.md)합니다. 이는 일반적으로 배포할 준비가 된 유효한 매개 변수가 있는 논리 앱 템플릿을 만드는 가장 쉬운 방법입니다.

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {<template-parameters>},
   "variables": {},
   "functions": [],
   "resources": [
      {
         <logic-app-resource-definition>
      },
      // Start connection resource definitions
      {
         <connection-resource-definition-1>
      },
      {
         <connection-resource-definition-2>
      }
   ],
   "outputs": {}
}
```

연결 리소스 정의는 매개 변수 파일을 사용하여 배포 시 이러한 값을 제공할 수 있도록 해당 값에 대한 템플릿의 최상위 매개 변수를 참조합니다. 연결이 논리 앱과 동일한 Azure 리소스 그룹 및 위치를 사용하는지 확인합니다.

Office 365 Outlook 연결 및 해당 템플릿 매개 변수에 대한 예제 리소스 정의는 다음과 같습니다.

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   // Template parameters
   "parameters": {
      "LogicAppName": {<parameter-definition>},
      "LogicAppLocation": {<parameter-definition>},
      "office365_1_Connection_Name": {
         "type": "string",
         "defaultValue": "office365",
         "metadata": {
            "description": "The resource name for the Office 365 Outlook connection"
         }
      },
      "office365_1_Connection_DisplayName": {
         "type": "string",
         "defaultValue": "",
         "metadata": {
            "description": "The display name for the Office 365 Outlook connection"
         }
      }
   },
   "variables": {},
   "functions": [],
   "resources": [
      {
         <logic-app-resource-definition>
      },
      // Office 365 Outlook API connection resource definition
      {
         "type": "MICROSOFT.WEB/CONNECTIONS",
         "apiVersion": "2016-06-01",
         // Template parameter reference for connection name
         "name": "[parameters('office365_1_Connection_Name')]",
         // Template parameter reference for connection resource location. Must match logic app location.
         "location": "[parameters('LogicAppLocation')]",
         "properties": {
            "api": {
               // Connector ID
               "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', parameters('LogicAppLocation'), '/managedApis/', 'office365')]"
            },
            // Template parameter reference for connection display name
            "displayName": "[parameters('office365_1_Connection_DisplayName')]"
         }
      }
   ],
   "outputs": {}
}
```

논리 앱의 리소스 정의는 다음과 같은 방법으로 연결 리소스 정의에도 적용됩니다.

* 워크플로 정의 내에서 `parameters` 개체는 논리 앱 런타임에서 사용할 연결 값에 대한 `$connections` 매개 변수를 선언합니다. 또한 연결을 작성하는 트리거 또는 조치는 이 `$connections` 매개 변수를 통해 전달되는 해당 값을 사용합니다.

* 워크플로 정의 *외부* 이지만 논리 앱의 리소스 정의 *내부* 에서 다른 `parameters` 개체는 해당 템플릿 매개 변수를 참조하여 `$connections` 매개 변수에 대해 런타임에 사용할 값을 설정합니다. 이러한 값은 템플릿 식을 사용하여 논리 앱의 연결에 대한 메타데이터를 안전하게 저장하는 리소스를 참조합니다.

  예를 들어 메타데이터에는 [Azure Key Vault](../azure-resource-manager/templates/key-vault-parameter.md)에 저장할 수 있는 연결 문자열 및 액세스 토큰이 포함될 수 있습니다. 이러한 값을 템플릿 매개 변수에 전달하려면 배포 시 템플릿에서 사용하는 [매개 변수 파일](#template-parameter-files)에서 해당 Key Vault를 참조합니다. 참조 매개 변수의 차이점에 대한 자세한 내용은 이 항목 뒷부분의 [매개 변수에 대한 참조](#parameter-references)를 참조하세요.

  Azure Portal 또는 Visual Studio를 통해 코드 보기에서 논리 앱의 워크플로 정의를 열면 `$connections` 개체가 워크플로 정의 외부에 있지만 동일한 수준에서 나타납니다. 코드 보기에서 이 순서를 지정하면 워크플로 정의를 수동으로 업데이트할 때 해당 매개 변수를 더 쉽게 참조할 수 있습니다.

  ```json
  {
     "$connections": {<workflow-definition-parameter-connection-values-runtime},
     "definition": {<workflow-definition>}
  }
  ```

* 논리 앱의 리소스 정의에는 논리 앱에서 사용하는 연결에 대한 종속성을 지정하는 `dependsOn` 개체가 있습니다.

만든 각 연결에는 Azure에서 고유한 이름이 있습니다. 동일한 서비스 또는 시스템에 대한 여러 연결을 만들면 각 연결 이름에 숫자가 추가되며, 이는 새로 만들 때마다 증가합니다(예 : `office365`, `office365-1` 등).

이 예제에서는 논리 앱의 리소스 정의와 Office 365 Outlook에 대한 연결 리소스 정의 간의 상호 작용을 보여 줍니다.

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   // Template parameters
   "parameters": {
      "LogicAppName": {<parameter-definition>},
      "LogicAppLocation": {<parameter-definition>},
      "office365_1_Connection_Name": {<parameter-definition>},
      "office365_1_Connection_DisplayName": {<parameter-definition>}
   },
   "variables": {},
   "functions": [],
   "resources": [
      {
         // Start logic app resource definition
         "properties": {
            <...>,
            "definition": {
               <...>,
               "parameters": {
                  // Workflow definition "$connections" parameter
                  "$connections": {
                     "defaultValue": {},
                     "type": "Object"
                  }
               },
               <...>
            },
            "parameters": {
               // Workflow definition "$connections" parameter values to use at runtime
               "$connections": {
                  "value": {
                     "office365": {
                        // Template parameter references
                        "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', parameters('LogicAppLocation'), '/managedApis/', 'office365')]",
                        "connectionId": "[resourceId('Microsoft.Web/connections', parameters('office365_1_Connection_Name'))]",
                        "connectionName": "[parameters('office365_1_Connection_Name')]"
                     }
                  }
               }
            }
         },
         <other-logic-app-resource-information>,
         "dependsOn": [
            "[resourceId('Microsoft.Web/connections', parameters('office365_1_Connection_Name'))]"
         ]
         // End logic app resource definition
      },
      // Office 365 Outlook API connection resource definition
      {
         "type": "MICROSOFT.WEB/CONNECTIONS",
         "apiVersion": "2016-06-01",
         // Template parameter reference for connection name
         "name": "[parameters('office365_1_Connection_Name')]",
         // Template parameter reference for connection resource location. Must match logic app location.
         "location": "[parameters('LogicAppLocation')]",
         "properties": {
            "api": {
               // Connector ID
               "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', parameters('LogicAppLocation'), '/managedApis/', 'office365')]"
            },
            // Template parameter reference for connection display name
            "displayName": "[parameters('office365_1_Connection_DisplayName')]"
         }
      }
   ],
   "outputs": {}
}
```

<a name="secure-connection-parameters"></a>

### <a name="secure-connection-parameters"></a>안전한 연결 매개 변수

중요한 정보, 암호, 액세스 키 또는 비밀을 처리하는 연결 매개 변수의 경우 연결의 리소스 정의에는 이러한 값을 이름-값 쌍 형식으로 지정하는 `parameterValues` 개체가 포함됩니다. 이 정보를 숨기려면 `securestring` 또는 `secureobject` 매개 변수 유형을 사용하여 해당 값에 대한 템플릿 매개 변수를 선언하거나 편집할 수 있습니다. 그런 다음 해당 정보를 [Azure Key Vault](../azure-resource-manager/templates/key-vault-parameter.md)에 저장할 수 있습니다. 이러한 값을 템플릿 매개 변수에 전달하려면 배포 시 템플릿에서 사용하는 [매개 변수 파일](#template-parameter-files)에서 해당 Key Vault를 참조합니다.

Azure Blob Storage 연결에 대한 계정 이름 및 액세스 키를 제공하는 예제는 다음과 같습니다.

**매개 변수 파일**

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
   "contentVersion": "1.0.0.0",
   // Template parameter values
   "parameters": {
      "LogicAppName": {
         "value": "Email-Processor-Logic-App"
      },
      "LogicAppLocation": {
         "value": "westeurope"
      },
      "azureblob_1_Connection_Name": {
         "value": "Fabrikam-Azure-Blob-Storage-Connection"
      },
      "azureblob_1_Connection_DisplayName": {
         "value": "Fabrikam-Storage"
      },
      "azureblob_1_accountName": {
         "value": "Fabrikam-Storage-Account"
      },
      "azureblob_1_accessKey": {
         "reference": {
            "keyVault": {
               "id": "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/Microsoft.KeyVault/vaults/fabrikam-key-vault"
            },
            "secretName": "FabrikamStorageKey"
         }
      }
   }
}
```

**템플릿**

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   // Template parameters
   "parameters": {
      "LogicAppName": {<parameter-definition>},
      "LogicAppLocation": {<parameter-definition>},
      "azureblob_1_Connection_Name": {<parameter-definition>},
      "azureblob_1_Connection_DisplayName": {<parameter-definition>},
      "azureblob_1_accountName": {
         "type": "string",
         "defaultValue": "",
         "metadata": {
            "description": "Name of the storage account the connector should use."
         }
      },
      "azureblob_1_accessKey": {
         "type": "secureobject",
         "metadata": {
            "description": "Specify a valid primary/secondary storage account access key."
         }
      }
   },
   "variables": {},
   "functions": [],
   "resources": [
      {
         "properties": {
            "state": "Disabled",
            "definition": {
               "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
               "actions": {
                  // Azure Blob Storage action
                  "Create_blob": {
                     "type": "ApiConnection",
                     "inputs": {
                        "host": {
                           "connection": {
                              // Workflow definition parameter reference for values to use at runtime
                              "name": "@parameters('$connections')['azureblob']['connectionId']"
                           }
                        },
                     },
                     "method": "post",
                     "body": "@triggerBody()?['Body']",
                     "path": "/datasets/default/files",
                     "queries": {
                        "folderPath": "/emails",
                        "name": "@triggerBody()?['Subject']",
                        "queryParametersSingleEncoded": true
                     },
                     "runAfter": {},
                     "runtimeConfiguration": {
                        "contentTransfer": {
                           "transferMode": "Chunked"
                        }
                     }
                  }
               },
               "parameters": {
                  // Workflow definition parameter for values to use at runtime
                  "$connections": {
                     "defaultValue": {},
                     "type": "Object"
                  }
               },
               "triggers": {<trigger-definition>},
               "contentVersion": "1.0.0.0",
               "outputs": {}
            },
            "parameters": {
               "$connections": {
                  "value": {
                     // Template parameter references for values to use at runtime
                     "azureblob": {
                        "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', parameters('LogicAppLocation'), '/managedApis/', 'azureblob')]",
                        "connectionId": "[resourceId('Microsoft.Web/connections', parameters('azureblob_1_Connection_Name'))]",
                        "connectionName": "[parameters('azureblob_1_Connection_Name')]"
                    }
                  }
               }
            },
            "name": "[parameters('LogicAppName')]",
            "type": "Microsoft.Logic/workflows",
            "location": "[parameters('LogicAppLocation')]",
            "tags": {
               "displayName": "LogicApp"
            },
            "apiVersion": "2016-06-01",
            // Template parameter reference for value to use at deployment
            "dependsOn": [
               "[resourceId('Microsoft.Web/connections', parameters('azureblob_1_Connection_Name'))]"
            ]
         }
      },
      // Azure Blob Storage API connection resource definition
      {
         "type": "MICROSOFT.WEB/CONNECTIONS",
         "apiVersion": "2016-06-01",
         "name": "[parameters('azureblob_1_Connection_Name')]",
         "location": "[parameters('LogicAppLocation')]",
         "properties": {
            "api": {
               "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', parameters('LogicAppLocation'), '/managedApis/', 'azureblob')]"
            },
            "displayName": "[parameters('azureblob_1_Connection_DisplayName')]",
            // Template parameter reference for values to use at deployment
            "parameterValues": {
               "accountName": "[parameters('azureblob_1_accountName')]",
               "accessKey": "[parameters('azureblob_1_accessKey')]"
            }
         }
      }
   ],
   "outputs": {}
}
```

<a name="authenticate-connections"></a>

### <a name="authenticate-connections"></a>연결 인증

배포 후 논리 앱은 유효한 매개 변수와 함께 엔드투엔드에 적용됩니다. 그러나 [자격 증명을 인증](../active-directory/develop/authentication-vs-authorization.md)하는 데 유효한 액세스 토큰을 생성하려면 여전히 OAuth 연결에 권한을 부여해야 합니다. 자세한 내용은 [OAuth 연결 권한 부여](../logic-apps/logic-apps-deploy-azure-resource-manager-templates.md#authorize-oauth-connections)를 참조하세요.

일부 연결에서는 Azure AD(Azure Active Directory) [서비스 주체](../active-directory/develop/app-objects-and-service-principals.md)를 사용하여 [Azure AD에 등록](../active-directory/develop/quickstart-register-app.md)된 논리 앱에 대한 연결 권한을 부여합니다. 예를 들어 이 Azure Data Lake 연결 리소스 정의는 서비스 주체의 정보를 처리하는 템플릿 매개 변수를 참조하는 방법과 템플릿이 이러한 매개 변수를 선언하는 방법을 보여 줍니다.

**연결 리소스 정의**

```json
{
   <other-template-objects>
   "type": "MICROSOFT.WEB/CONNECTIONS",
   "apiVersion": "2016-06-01",
   "name": "[parameters('azuredatalake_1_Connection_Name')]",
   "location": "[parameters('LogicAppLocation')]",
   "properties": {
      "api": {
         "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', 'resourceGroup().location', '/managedApis/', 'azuredatalake')]"
      },
      "displayName": "[parameters('azuredatalake_1_Connection_DisplayName')]",
      "parameterValues": {
         "token:clientId": "[parameters('azuredatalake_1_token:clientId')]",
         "token:clientSecret": "[parameters('azuredatalake_1_token:clientSecret')]",
         "token:TenantId": "[parameters('azuredatalake_1_token:TenantId')]",
         "token:grantType": "[parameters('azuredatalake_1_token:grantType')]"
      }
   }
}
```

| attribute | 설명 |
|-----------|-------------|
| `token:clientId` | 서비스 주체와 연결된 애플리케이션 또는 클라이언트 ID |
| `token:clientSecret` | 서비스 주체와 연결된 키 값 |
| `token:TenantId` | Azure AD 테넌트의 디렉터리 ID |
| `token:grantType` | 요청된 권한 부여 형식으로, `client_credentials`이어야 합니다. 자세한 내용은 [Microsoft ID 플랫폼 및 OAuth 2.0 클라이언트 자격 증명 흐름](../active-directory/develop/v2-oauth2-client-creds-grant-flow.md)을 참조하세요. |
|||

**템플릿 매개 변수 정의**

템플릿의 최상위 `parameters` 개체는 예제 연결에 대해 다음 매개 변수를 선언합니다.

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {
      "azuredatalake_1_Connection_Name": {
        "type": "string",
        "defaultValue": "azuredatalake"
      },
      "azuredatalake_1_Connection_DisplayName": {
        "type": "string",
        "defaultValue": "<connection-name>"
      },
      "azuredatalake_1_token:clientId": {
        "type": "securestring",
        "metadata": {
          "description": "Client (or Application) ID of the Azure Active Directory application."
        }
      },
      "azuredatalake_1_token:clientSecret": {
        "type": "securestring",
        "metadata": {
          "description": "Client secret of the Azure Active Directory application."
        }
      },
      "azuredatalake_1_token:TenantId": {
        "type": "securestring",
        "metadata": {
          "description": "The tenant ID of for the Azure Active Directory application."
        }
      },
      "azuredatalake_1_token:resourceUri": {
        "type": "string",
        "metadata": {
          "description": "The resource you are requesting authorization to use."
        }
      },
      "azuredatalake_1_token:grantType": {
        "type": "string",
        "metadata": {
          "description": "Grant type"
        },
        "defaultValue": "client_credentials",
        "allowedValues": [
          "client_credentials"
        ]
      },
      // Other template parameters
   }
   // Other template objects
}
```

서비스 주체를 사용하는 방법에 대한 자세한 내용은 다음 항목을 참조하세요.

* [Azure Portal을 사용하여 서비스 주체 만들기](../active-directory/develop/howto-create-service-principal-portal.md)
* [Azure PowerShell을 사용하여 Azure 서비스 주체 만들기](/powershell/azure/create-azure-service-principal-azureps)
* [Azure PowerShell을 사용하여 인증서로 서비스 주체 만들기](../active-directory/develop/howto-authenticate-service-principal-powershell.md)

<a name="parameter-references"></a>

## <a name="references-to-parameters"></a>매개 변수에 대한 참조

템플릿 매개 변수를 참조하려면 배포 시 평가되는 [템플릿 함수](../azure-resource-manager/templates/template-functions.md)에 템플릿 식을 사용하면 됩니다. 템플릿 식은 대괄호( **[]** )를 사용합니다.

`"<attribute-name>": "[parameters('<template-parameter-name>')]"`

워크플로 정의 매개 변수를 참조하려면 런타임에 평가되는 [워크플로 정의 언어 식과 함수](../logic-apps/workflow-definition-language-functions-reference.md)를 사용합니다. 일부 템플릿 함수와 워크플로 정의 함수의 이름은 동일합니다. 워크플로 정의 식은 "at" 기호( **@** )로 시작합니다.

`"<attribute-name>": "@parameters('<workflow-definition-parameter-name>')"`

런타임에 사용할 논리 앱에 대한 워크플로 정의에 템플릿 매개 변수 값을 전달할 수 있습니다. 그러나 Logic App 디자이너는 템플릿 요소를 지원하지 않으므로 워크플로 정의에서 템플릿 매개 변수, 식 및 구문을 사용하지 마세요. 또한 템플릿 구문과 식은 식이 평가되는 시기의 차이로 인해 코드를 복잡하게 만들 수 있습니다

대신, 다음 일반 단계에 따라 런타임에 사용할 워크플로 정의 매개 변수를 선언 및 참조하고, 배포 시 사용할 템플릿 매개 변수를 선언 및 참조하고, 매개 변수 파일을 사용하여 배포 시 전달할 값을 지정합니다. 자세한 내용은 이 항목의 앞부분에 나오는 [워크플로 정의 및 매개 변수](#workflow-definition-parameters) 섹션을 참조하세요.

1. 템플릿을 만들고, 배포 시 수락하고 사용할 값에 대한 템플릿 매개 변수를 선언합니다.

1. 워크플로 정의에서 런타임에 수락하고 사용할 값에 대한 매개 변수를 선언합니다. 그러면 워크플로 정의 전체에서 이러한 값을 참조할 수 있습니다.

1. 워크플로 정의 *외부* 이지만 논리 앱의 리소스 정의 *내부* 인 `parameters` 개체에서 해당 템플릿 매개 변수를 참조하여 워크플로 정의 매개 변수 값을 설정합니다. 이렇게 하면 템플릿 매개 변수 값을 워크플로 정의 매개 변수에 전달할 수 있습니다.

1. 매개 변수 파일에서 배포 시 사용할 템플릿의 값을 지정합니다.

<a name="full-example-template"></a>

## <a name="full-example-template"></a>전체 예제 템플릿

이 항목의 예제에서 사용하는 매개 변수가 있는 샘플 템플릿은 다음과 같습니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
   "parameters": {
      "LogicAppName": {
         "type": "string",
         "minLength": 1,
         "maxLength": 80,
         "defaultValue": "MyLogicApp",
         "metadata": {
            "description": "The resource name to use for the logic app"
         }
      },
      "LogicAppLocation": {
         "type": "string",
         "minLength": 1,
         "defaultValue": "[resourceGroup().location]",
         "metadata": {
            "description": "The resource location to use for the logic app"
         }
      },
      "office365_1_Connection_Name": {
         "type": "string",
         "defaultValue": "office365",
         "metadata": {
            "description": "The resource name to use for the Office 365 Outlook connection"
         }
      },
      "office365_1_Connection_DisplayName": {
         "type": "string",
         "defaultValue": "",
         "metadata": {
            "description": "The display name to use for the Office 365 Outlook connection"
         }
      },
      "azureblob_1_Connection_Name": {
         "type": "string",
         "defaultValue": "azureblob",
         "metadata": {
            "description": "The resource name to use for the Azure Blob storage account connection"
         }
      },
      "azureblob_1_Connection_DisplayName": {
         "type": "string",
         "defaultValue": "",
         "metadata": {
            "description": "Name of the storage account the connector should use."
         }

      },
      "azureblob_1_accountName": {
         "type": "string",
         "defaultValue": "",
         "metadata": {
            "description": "Name of the storage account the connector should use."
         }
      },
      "azureblob_1_accessKey": {
         "type": "securestring",
         "metadata": {
            "description": "Specify a valid primary/secondary storage account access key."
         }
      },
      "LogicAppIntegrationAccount": {
         "type":"string",
         "minLength": 1,
         "defaultValue": "/subscriptions/<Azure-subscription-ID>/resourceGroups/fabrikam-integration-account-rg/providers/Microsoft.Logic/integrationAccounts/fabrikam-integration-account",
         "metadata": {
            "description": "The ID to use for the integration account"
         }
      }
   },
   "variables": {},
   "resources": [
      {
         "properties": {
            "state": "Disabled",
            "integrationAccount": {
              "id": "[parameters('LogicAppIntegrationAccount')]"
            },
            "definition": {
               "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
               "actions": {
                  "Create_blob": {
                     "type": "ApiConnection",
                     "inputs": {
                        "host": {
                           "connection": {
                              "name": "@parameters('$connections')['azureblob']['connectionId']"
                           }
                        }
                     },
                     "method": "post",
                     "body": "@triggerBody()?['Body']",
                     "path": "/datasets/default/files",
                     "queries": {
                        "folderPath": "/emails",
                        "name": "@triggerBody()?['Subject']",
                        "queryParametersSingleEncoded": true
                     },
                     "runAfter": {},
                     "runtimeConfiguration": {
                        "contentTransfer": {
                           "transferMode": "Chunked"
                        }
                     }
                  }
               },
               "parameters": {
                  "$connections": {
                     "defaultValue": {},
                     "type": "Object"
                  }
               },
               "triggers": {
                  "When_a_new_email_arrives": {
                     "type": "ApiConnection",
                     "inputs": {
                        "host": {
                           "connection": {
                              "name": "@parameters('$connections')['office365']['connectionId']"
                           }
                        },
                        "method": "get",
                        "path": "/Mail/OnNewEmail",
                        "queries": {
                           "folderPath": "Inbox",
                           "importance": "Any",
                           "fetchOnlyWithAttachment": false,
                           "includeAttachments": false
                        }
                     },
                     "recurrence": {
                        "frequency": "Day",
                        "interval": 1
                     },
                     "splitOn": "@triggerBody()?['value']"
                  }
               },
               "contentVersion": "1.0.0.0",
               "outputs": {}
            },
            "parameters": {
               "$connections": {
                  "value": {
                     "azureblob": {
                        "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', parameters('LogicAppLocation'), '/managedApis/', 'azureblob')]",
                        "connectionId": "[resourceId('Microsoft.Web/connections', parameters('azureblob_1_Connection_Name'))]",
                        "connectionName": "[parameters('azureblob_1_Connection_Name')]"
                     },
                     "office365": {
                        "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', parameters('LogicAppLocation'), '/managedApis/', 'office365')]",
                        "connectionId": "[resourceId('Microsoft.Web/connections', parameters('office365_1_Connection_Name'))]",
                        "connectionName": "[parameters('office365_1_Connection_Name')]"
                     }
                  }
               }
            },
            "accessControl": {}
         },
         "name": "[parameters('LogicAppName')]",
         "type": "Microsoft.Logic/workflows",
         "location": "[parameters('LogicAppLocation')]",
         "tags": {
            "displayName": "LogicApp"
         },
         "apiVersion": "2016-06-01",
         "dependsOn": [
            "[resourceId('Microsoft.Web/connections', parameters('azureblob_1_Connection_Name'))]",
            "[resourceId('Microsoft.Web/connections', parameters('office365_1_Connection_Name'))]"
         ]
      },
      {
         "type": "MICROSOFT.WEB/CONNECTIONS",
         "apiVersion": "2016-06-01",
         "name": "[parameters('office365_1_Connection_Name')]",
         "location": "[parameters('LogicAppLocation')]",
         "properties": {
            "api": {
                "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', parameters('LogicAppLocation'), '/managedApis/', 'office365')]"
            },
            "displayName": "[parameters('office365_1_Connection_DisplayName')]"
         }
      },
      {
         "type": "MICROSOFT.WEB/CONNECTIONS",
         "apiVersion": "2016-06-01",
         "name": "[parameters('azureblob_1_Connection_Name')]",
         "location": "[parameters('LogicAppLocation')]",
         "properties": {
            "api": {
               "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', parameters('LogicAppLocation'), '/managedApis/', 'azureblob')]"
            },
            "displayName": "[parameters('azureblob_1_Connection_DisplayName')]",
            "parameterValues": {
               "accountName": "[parameters('azureblob_1_accountName')]",
               "accessKey": "[parameters('azureblob_1_accessKey')]"
            }
         }
      }
   ],
   "outputs": {}
}
```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [논리 앱 템플릿 만들기](../logic-apps/logic-apps-create-azure-resource-manager-templates.md)
