---
title: 관리 ID로 워크플로 인증
description: 자격 증명 또는 암호 없이 Azure AD로 보호된 리소스에 대한 트리거 및 동작을 인증하기 위해 관리 ID 사용
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, azla
ms.topic: article
ms.date: 06/25/2021
ms.custom: devx-track-azurepowershell, subject-rbac-steps
ms.openlocfilehash: 76edcac6b77b70928cb2d6cd378b421b68b3d3ef
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122535875"
---
# <a name="authenticate-access-to-azure-resources-using-managed-identities-in-azure-logic-apps"></a>Azure Logic Apps에서 관리 ID를 사용하여 Azure 리소스에 대한 액세스 인증

논리 앱 워크플로의 일부 트리거 및 동작은 Azure AD(Azure Active Directory)로 보호되는 리소스에 연결할 때 인증을 위해 이전에 ‘MSI(관리 서비스 ID)’로 알려진 [관리 ID](../active-directory/managed-identities-azure-resources/overview.md)를 사용하도록 지원합니다. 논리 앱 리소스에서 관리 ID를 사용하도록 설정하고 논리 앱 리소스를 설정한 경우 사용자 고유의 자격 증명, 비밀 또는 Azure AD 토큰을 사용할 필요가 없습니다. 비밀이나 토큰을 관리할 필요가 없기 때문에 Azure는 이 ID를 관리하고 자격 증명을 보호하는 데 도움이 됩니다.

이 문서에서는 논리 앱에 대해 두 가지 종류의 관리 ID를 설정하는 방법을 보여 줍니다. 자세한 내용은 다음 설명서를 검토하세요.

* [관리 ID를 지원하는 트리거 및 작업](../logic-apps/logic-apps-securing-a-logic-app.md#authentication-types-supported-triggers-actions)
* [논리 앱 관리 ID 한도](../logic-apps/logic-apps-limits-and-config.md#managed-identity)
* [관리 ID를 사용하여 Azure AD 인증을 지원하는 Azure 서비스](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)

<a name="triggers-actions-managed-identity"></a>

## <a name="where-to-use-managed-identities"></a>관리 ID를 사용하는 위치

Azure Logic Apps는 [*시스템 할당* 관리 ID](../active-directory/managed-identities-azure-resources/overview.md)와 [*사용자 할당* 관리 ID](../active-directory/managed-identities-azure-resources/overview.md)를 모두 지원합니다. 이러한 ID는 논리 앱 워크플로가 실행되는 위치에 따라 논리 앱 그룹에서 공유할 수 있습니다.

* 다중 테넌트(소비 플랜) 기반 논리 앱은 시스템 할당 ID와 단일 사용자 할당 ID를 모두 지원합니다. 그러나 논리 앱 수준 또는 연결 수준에서는 이러한 할당 ID 유형을 동시에 사용하도록 설정할 수 없으므로 한 가지 관리 ID 유형만 사용할 수 있습니다.

  단일 테넌트(표준 플랜) 기반 논리 앱은 현재 시스템 할당 ID만 지원합니다.

  다중 테넌트(소비 플랜) 및 단일 테넌트(표준 플랜)에 대한 자세한 내용은 [단일 테넌트 대 다중 테넌트 및 통합 서비스 환경](single-tenant-overview-compare.md) 설명서를 참조하세요.

<a name="built-in-managed-identity"></a>
<a name="managed-connectors-managed-identity"></a>

* Azure AD Open 인증을 지원하는 특정 기본 제공 및 관리형 커넥터 작업만 인증을 위해 관리 ID를 사용할 수 있습니다. 다음 표에서는 *샘플 선택* 만 제공합니다. 전체 목록을 보려면 [인증을 지원하는 트리거 및 동작에 대한 인증 유형](../logic-apps/logic-apps-securing-a-logic-app.md#authentication-types-supported-triggers-actions)을 참조하세요.

  | 작업 유형 | 지원되는 작업 |
  |----------------|----------------------|
  | 기본 제공 | - Azure API Management <br>- Azure App Services <br>- Azure Functions <br>- HTTP <br>- HTTP + 웹후크 <p><p> **참고**: HTTP 작업은 시스템 할당 ID로 Azure 방화벽 뒤에 있는 Azure Storage 계정 연결을 인증할 수는 있지만 사용자 할당 ID로 동일한 연결을 인증할 수는 없습니다. |
  | 관리형 커넥터(**미리 보기**) | - Azure Automation <br>- Azure Event Grid <br>- Azure Key Vault <br>- Azure Resource Manager <br>- Azure AD를 사용하는 HTTP |
  |||

## <a name="prerequisites"></a>사전 요구 사항

* Azure 계정 및 구독 구독이 없는 경우 [Azure 체험 계정에 등록](https://azure.microsoft.com/free/)합니다. 액세스 권한이 필요한 관리 ID 및 대상 Azure 리소스는 모두 동일한 Azure 구독을 사용해야 합니다.

* Azure 리소스에 대한 관리 ID 액세스 권한을 부여하려면 역할을 해당 ID의 대상 리소스에 추가해야 합니다. 역할을 추가하려면 이 역할을 해당 Azure AD 테넌트의 ID에 할당할 수 있는 [Azure AD 관리자 권한](../active-directory/roles/permissions-reference.md)이 필요합니다.

* 액세스하려는 대상 Azure 리소스. 이 리소스에서는 논리 앱에서 대상 리소스에 대한 액세스를 인증하는 데 도움이 되는 관리 ID에 대한 역할을 추가합니다.

* [관리 ID를 지원하는 트리거 또는 작업](../logic-apps/logic-apps-securing-a-logic-app.md#authentication-types-supported-triggers-actions)을 사용하려는 논리 앱입니다.

## <a name="enable-managed-identity"></a>관리 ID 사용

사용하려는 관리 ID를 설정하려면 해당 ID에 대한 링크를 따릅니다.

* [시스템이 할당한 ID](#system-assigned)
* [사용자가 할당한 ID](#user-assigned)

<a name="system-assigned"></a>

### <a name="enable-system-assigned-identity"></a>시스템이 할당한 ID 사용

사용자가 할당한 ID와 달리 시스템이 할당한 ID는 수동으로 만들 필요가 없습니다. 논리 앱에 대해 시스템이 할당한 ID를 설정하려면 다음 옵션을 사용할 수 있습니다.

* [Azure Portal](#azure-portal-system-logic-app)
* [ARM 템플릿(Azure Resource Manager 템플릿)](#template-system-logic-app)

<a name="azure-portal-system-logic-app"></a>

#### <a name="enable-system-assigned-identity-in-azure-portal"></a>Azure Portal에서 시스템이 할당한 ID 사용

1. [Azure Portal](https://portal.azure.com)의 디자이너 보기에서 논리 앱을 엽니다.

1. 논리 앱 메뉴의 **설정** 에서 **ID** 를 선택합니다. **시스템 할당 항목** > **켜기** > **저장** 을 차례로 선택합니다. Azure에서 확인하라는 메시지가 표시되면 **예** 를 선택합니다.

   ![시스템이 할당한 ID 사용](./media/create-managed-service-identity/enable-system-assigned-identity.png)

   > [!NOTE]
   > 하나의 관리 ID만 있을 수 있다는 오류가 표시되면 논리 앱이 이미 사용자가 할당한 ID와 연결되어 있습니다. 시스템이 할당한 ID를 추가하려면 먼저 논리 앱에서 사용자가 할당한 ID를 *제거* 해야 합니다.

   이제 논리 앱에서 시스템이 할당한 ID를 사용할 수 있으며, Azure Active Directory에 등록되고 개체 ID로 표시됩니다.

   ![시스템이 할당한 ID에 대한 개체 ID](./media/create-managed-service-identity/object-id-system-assigned-identity.png)

   | 속성 | 값 | Description |
   |----------|-------|-------------|
   | **개체 ID** | <*identity-resource-ID*> | Azure AD 테넌트의 논리 앱에 대한 시스템이 할당한 ID를 나타내는 GUID(Globally Unique Identifier) |
   ||||

1. 이제 이 항목의 뒷부분에 있는 [리소스에 대한 ID 액세스 권한을 부여하는 단계](#access-other-resources)를 수행합니다.

<a name="template-system-logic-app"></a>

#### <a name="enable-system-assigned-identity-in-an-arm-template"></a>ARM 템플릿에서 시스템 할당 ID 사용

논리 앱과 같은 Azure 리소스의 만들기 및 배포를 자동화하려면 [ARM 템플릿](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)을 사용할 수 있습니다. 템플릿에서 논리 앱에 대해 시스템이 할당한 관리 ID를 사용하도록 설정하려면 `identity` 개체 및 `type` 자식 속성을 템플릿의 논리 앱 리소스 정의에 추가합니다. 예를 들어 다음과 같습니다.

```json
{
   "apiVersion": "2016-06-01",
   "type": "Microsoft.logic/workflows",
   "name": "[variables('logicappName')]",
   "location": "[resourceGroup().location]",
   "identity": {
      "type": "SystemAssigned"
   },
   "properties": {
      "definition": {
         "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
         "actions": {},
         "parameters": {},
         "triggers": {},
         "contentVersion": "1.0.0.0",
         "outputs": {}
   },
   "parameters": {},
   "dependsOn": []
}
```

Azure에서 논리 앱 리소스 정의를 만들면 `identity` 개체에서 다음과 같은 기타 속성을 가져옵니다.

```json
"identity": {
   "type": "SystemAssigned",
   "principalId": "<principal-ID>",
   "tenantId": "<Azure-AD-tenant-ID>"
}
```

| 속성(JSON) | 값 | Description |
|-----------------|-------|-------------|
| `principalId` | <*principal-ID*> | Azure AD 테넌트에서 논리 앱을 나타내는 관리 ID에 대한 서비스 주체 개체의 GUID(Globally Unique Identifier)입니다. 이 GUID는 "개체 ID" 또는 `objectID`로 나타나는 경우도 있습니다. |
| `tenantId` | <*Azure-AD-tenant-ID*> | 논리 앱이 현재 멤버로 속해 있는 Azure AD 테넌트를 나타내는 GUID(Globally Unique Identifier)입니다. Azure AD 테넌트 내부에서 서비스 주체는 논리 앱 인스턴스와 이름이 같습니다. |
||||

<a name="user-assigned"></a>

### <a name="enable-user-assigned-identity"></a>사용자가 할당한 ID 사용

논리 앱에 대해 사용자가 할당한 관리 ID를 설정하려면 먼저 해당 ID를 별도의 독립 실행형 Azure 리소스로 만들어야 합니다. 사용할 수 있는 옵션은 다음과 같습니다.

* [Azure Portal](#azure-portal-user-identity)
* [ARM 템플릿](#template-user-identity)
* Azure PowerShell
  * [사용자가 할당한 ID 만들기](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md)
  * [역할 할당 추가](../active-directory/managed-identities-azure-resources/howto-assign-access-powershell.md)
* Azure CLI
  * [사용자가 할당한 ID 만들기](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md)
  * [역할 할당 추가](../active-directory/managed-identities-azure-resources/howto-assign-access-cli.md)
* Azure REST API
  * [사용자가 할당한 ID 만들기](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-rest.md)
  * [역할 할당 추가](../role-based-access-control/role-assignments-rest.md)

<a name="azure-portal-user-identity"></a>

#### <a name="create-user-assigned-identity-in-the-azure-portal"></a>Azure Portal에서 사용자가 할당한 ID 만들기

1. [Azure Portal](https://portal.azure.com)의 모든 페이지에 있는 검색 상자에서 `managed identities`를 입력하고 **관리 ID** 를 선택합니다.

   !["관리 ID"가 선택된 포털을 보여 주는 스크린샷](./media/create-managed-service-identity/find-select-managed-identities.png)

1. **관리 ID** 아래에서 **추가** 를 선택합니다.

   ![새 관리 ID 추가](./media/create-managed-service-identity/add-user-assigned-identity.png)

1. 관리 ID에 대한 정보를 제공한 다음, **검토 + 만들기** 를 선택합니다. 예를 들면 다음과 같습니다.

   ![사용자가 할당한 관리 ID 만들기](./media/create-managed-service-identity/create-user-assigned-identity.png)

   | 속성 | 필수 | 값 | 설명 |
   |----------|----------|-------|-------------|
   | **구독** | 예 | <*Azure-subscription-name*> | 사용할 Azure 구독의 이름입니다. |
   | **리소스 그룹** | 예 | <*Azure-resource-group-name*> | 사용할 리소스 그룹의 이름입니다. 새 그룹을 만들거나 기존 그룹을 선택합니다. 이 예제는 `fabrikam-managed-identities-RG`라는 새 리소스 그룹을 만듭니다. |
   | **지역** | 예 | <*Azure-region*> | 리소스에 대한 정보를 저장할 Azure 지역입니다. 이 예제에서는 “미국 서부”를 사용합니다. |
   | **이름** | 예 | <*user-assigned-identity-name*> | 사용자가 할당한 ID를 제공하는 이름입니다. 이 예에서는 `Fabrikam-user-assigned-identity`를 사용합니다. |
   |||||

   세부 정보의 유효성을 검사한 후 Azure에서 관리 ID를 만듭니다. 이제 사용자가 할당한 ID를 논리 앱에 추가할 수 있습니다. 사용자가 할당한 ID를 둘 이상 논리 앱에 추가할 수 없습니다.

1. Azure Portal의 디자이너 보기에서 논리 앱을 엽니다.

1. 논리 앱 메뉴의 **설정** 아래에서 **ID** 를 선택한 다음, **사용자 할당 항목** > **추가** 를 차례로 선택합니다.

   ![사용자가 할당한 관리 ID 추가](./media/create-managed-service-identity/add-user-assigned-identity-logic-app.png)

1. 아직 선택하지 않은 경우 **사용자가 할당한 관리 ID 추가** 창의 **구독** 목록에서 Azure 구독을 선택합니다. 해당 구독의 모든 관리 ID를 표시하는 목록에서 원하는 사용자 할당 ID를 선택합니다. 목록을 필터링하려면 **사용자가 할당한 관리 ID** 검색 상자에서 ID 또는 리소스 그룹의 이름을 입력합니다. 완료되면 **추가** 를 선택합니다.

   ![사용할 사용자가 할당한 ID 선택](./media/create-managed-service-identity/select-user-assigned-identity.png)

   > [!NOTE]
   > 하나의 관리 ID만 있을 수 있다는 오류가 표시되면 논리 앱이 이미 시스템이 할당한 ID와 연결되어 있습니다. 사용자 할당 ID를 추가하려면 먼저 논리 앱에서 시스템이 할당한 ID를 사용하지 않도록 설정해야 합니다.

   이제 논리 앱이 사용자가 할당한 관리 ID와 연결됩니다.

   ![사용자가 할당한 ID와의 연결](./media/create-managed-service-identity/added-user-assigned-identity.png)

1. 이제 이 항목의 뒷부분에 있는 [리소스에 대한 ID 액세스 권한을 부여하는 단계](#access-other-resources)를 수행합니다.

<a name="template-user-identity"></a>

#### <a name="create-user-assigned-identity-in-an-arm-template"></a>ARM 템플릿에서 사용자 할당 ID 만들기

논리 앱과 같은 Azure 리소스의 만들기 및 배포를 자동화하려면 인증을 위해 [사용자 할당 ID](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-arm.md)를 지원하는 [ARM 템플릿](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)을 사용할 수 있습니다. 템플릿의 `resources` 섹션에서 논리 앱의 리소스 정의에는 다음 항목이 필요합니다.

* `type` 속성이 `UserAssigned`로 설정된 `identity` 개체

* 사용자 할당 리소스 및 이름을 지정하는 자식 `userAssignedIdentities` 개체입니다.

다음 예제에서는 HTTP PUT 요청에 대한 논리 앱 리소스 정의가 표시되며 매개 변수가 없는 `identity` 개체가 포함됩니다. PUT 요청 및 후속 GET 작업에 대한 응답에도 이 `identity` 개체가 있습니다.

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {<template-parameters>},
   "resources": [
      {
         "apiVersion": "2016-06-01",
         "type": "Microsoft.logic/workflows",
         "name": "[variables('logicappName')]",
         "location": "[resourceGroup().location]",
         "identity": {
            "type": "UserAssigned",
            "userAssignedIdentities": {
               "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<user-assigned-identity-name>": {}
            }
         },
         "properties": {
            "definition": {<logic-app-workflow-definition>}
         },
         "parameters": {},
         "dependsOn": []
      },
   ],
   "outputs": {}
}
```

관리 ID의 리소스 정의도 템플릿에 포함되면 `identity` 개체를 매개 변수화할 수 있습니다. 다음 예제에서는 템플릿의 `variables` 섹션에서 정의한 자식 `userAssignedIdentities` 개체에서 `userAssignedIdentity` 변수를 참조하는 방법을 보여 줍니다. 이 변수는 사용자가 할당한 ID에 대한 리소스 ID를 참조합니다.

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {
      "Template_LogicAppName": {
         "type": "string"
      },
      "Template_UserAssignedIdentityName": {
         "type": "securestring"
      }
   },
   "variables": {
      "logicAppName": "[parameters(`Template_LogicAppName')]",
      "userAssignedIdentityName": "[parameters('Template_UserAssignedIdentityName')]"
   },
   "resources": [
      {
         "apiVersion": "2016-06-01",
         "type": "Microsoft.logic/workflows",
         "name": "[variables('logicAppName')]",
         "location": "[resourceGroup().location]",
         "identity": {
            "type": "UserAssigned",
            "userAssignedIdentities": {
               "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities/', variables('userAssignedIdentityName'))]": {}
            }
         },
         "properties": {
            "definition": {<logic-app-workflow-definition>}
         },
         "parameters": {},
         "dependsOn": [
            "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities/', variables('userAssignedIdentityName'))]"
         ]
      },
      {
         "apiVersion": "2018-11-30",
         "type": "Microsoft.ManagedIdentity/userAssignedIdentities",
         "name": "[parameters('Template_UserAssignedIdentityName')]",
         "location": "[resourceGroup().location]",
         "properties": {}
      }
  ]
}
```

<a name="access-other-resources"></a>

## <a name="give-identity-access-to-resources"></a>리소스에 대한 ID 액세스 권한 부여

인증에 논리 앱의 관리 ID를 사용하려면 ID를 사용하려는 Azure 리소스에서 Azure RBAC(Azure 역할 기반 액세스 제어)를 사용하여 ID에 대한 액세스 권한을 설정해야 합니다.

이 작업을 완료하려면 다음 옵션 중 하나로 Azure 리소스에서 해당 ID에 적절한 역할을 할당합니다.

* [Azure Portal](#azure-portal-assign-access)
* [ARM 템플릿](../role-based-access-control/role-assignments-template.md)
* [Azure PowerShell](../role-based-access-control/role-assignments-powershell.md)
* [Azure CLI](../role-based-access-control/role-assignments-cli.md)
* [Azure REST API](../role-based-access-control/role-assignments-rest.md)

<a name="azure-portal-assign-access"></a>

### <a name="assign-managed-identity-role-based-access-in-the-azure-portal"></a>Azure Portal에서 관리 ID 역할 기반 액세스 할당

관리 ID를 사용하려는 Azure 리소스에서 대상 리소스에 액세스할 수 있는 역할에 ID를 할당해야 합니다. 이 작업에 대한 자세한 내용은 [Azure RBAC를 사용하여 다른 리소스에 관리 ID 액세스 할당](../active-directory/managed-identities-azure-resources/howto-assign-access-portal.md)을 검토하세요.

1. [Azure Portal](https://portal.azure.com)에서 ID를 사용하려는 리소스를 엽니다.

1. 리소스의 메뉴에서 **액세스 제어(IAM)**  > **추가** > **역할 할당 추가** 를 차례로 선택합니다.

   > [!NOTE]
   > **역할 할당 추가** 옵션이 사용되지 않도록 설정되면 역할을 할당할 수 있는 권한이 없는 것입니다. 자세한 내용은 [Azure AD 기본 제공 역할](../active-directory/roles/permissions-reference.md)을 검토하세요.

1. 이제 관리 ID에 필요한 역할을 할당합니다. **역할** 탭에서 ID에 현재 리소스에 대해 필요한 액세스 권한을 제공하는 역할을 할당합니다.

   이 예제에서는 Azure Storage 컨테이너의 Blob에 대한 쓰기 액세스 권한을 포함하는 **Storage Blob 데이터 기여자** 라는 역할을 할당합니다. 특정 스토리지 컨테이너 역할에 대한 자세한 내용은 [Azure Storage 컨테이너의 Blob에 액세스할 수 있는 역할](../storage/blobs/authorize-access-azure-active-directory.md#assign-azure-roles-for-access-rights)을 검토하세요.

1. 다음으로, 역할을 할당할 관리 ID를 선택합니다. **다음에 대한 액세스 할당** 에서 **관리 ID** > **구성원 추가** 를 선택합니다.

1. 관리 ID의 유형에 따라 다음 값을 선택하거나 제공합니다.

   | 형식 | Azure 서비스 인스턴스 | 구독 | 멤버 |
   |------|------------------------|--------------|--------|
   | **시스템 할당** | **논리 앱** | <*Azure-subscription-name*> | <*your-logic-app-name*> |
   | **사용자 할당** | 적용할 수 없음 | <*Azure-subscription-name*> | <*your-user-assigned-identity-name*> |
   |||||

   역할 할당에 대한 자세한 내용은 [Azure Portal 사용하여 역할 할당](../role-based-access-control/role-assignments-portal.md) 설명서를 검토하세요.

1. ID에 대한 액세스 설정을 완료한 후 ID를 사용하여 [관리 ID를 지원하는 트리거 및 동작에 대한 액세스를 인증](#authenticate-access-with-identity)할 수 있습니다.

<a name="authenticate-access-with-identity"></a>

## <a name="authenticate-access-with-managed-identity"></a>관리 ID를 사용하여 액세스 인증

[관리 ID를 논리 앱에 사용하도록 설정](#azure-portal-system-logic-app)하고 [대상 리소스 또는 엔터티에 대한 해당 ID 액세스 권한을 부여](#access-other-resources)하면 [관리 ID를 지원하는 트리거 및 작업](logic-apps-securing-a-logic-app.md#authentication-types-supported-triggers-actions)에서 해당 ID를 사용할 수 있습니다.

> [!IMPORTANT]
> 시스템이 할당한 ID를 사용하도록 하려는 Azure 함수가 있는 경우 먼저 [Azure 함수에 대한 인증을 사용하도록 설정](../logic-apps/logic-apps-azure-functions.md#enable-authentication-for-functions)합니다.

다음 단계에서는 Azure Portal을 통해 트리거 또는 작업에서 관리 ID를 사용하는 방법을 보여 줍니다. 트리거 또는 작업의 기본 JSON 정의에서 관리 ID를 지정하려면 [관리 ID 인증](../logic-apps/logic-apps-securing-a-logic-app.md#managed-identity-authentication)을 참조하세요.

1. [Azure Portal](https://portal.azure.com)의 디자이너 보기에서 논리 앱을 엽니다.

1. 아직 수행하지 않은 경우 [관리 ID를 지원하는 트리거 또는 작업](logic-apps-securing-a-logic-app.md#authentication-types-supported-triggers-actions)을 추가합니다.

   > [!NOTE]
   > 일부 트리거 및 작업만 인증 유형을 추가할 수 있도록 지원합니다. 자세한 내용은 [트리거 인증 형식과 인증 지원 동작](../logic-apps/logic-apps-securing-a-logic-app.md#authentication-types-supported-triggers-actions)을 참조하세요.

1. 추가한 트리거 또는 작업에서 다음 단계를 수행합니다.

   * **관리 ID 사용을 지원하는 기본 제공 트리거 및 작업**

     1. 속성이 아직 표시되지 않은 경우 **인증** 속성을 추가합니다.

     1. **인증 유형** 에서 **관리 ID** 를 선택합니다.

     자세한 내용은 [예: 관리 ID로 기본 제공 트리거 또는 작업 인증](#authenticate-built-in-managed-identity)을 참조하세요.
 
   * **관리 ID 사용을 지원하는 관리형 커넥터 트리거 및 작업**

     1. 테넌트 선택 페이지에서 **관리 ID로 연결** 을 선택합니다.

     1. 다음 페이지에서 연결 이름을 입력합니다.

        기본값으로 논리 앱은 한 번에 하나의 관리 ID를 사용하도록 지원하므로 현재 사용하도록 설정된 관리 ID만 표시됩니다. 예를 들면 다음과 같습니다.

        ![연결 이름 페이지 및 선택한 관리 ID를 보여 주는 스크린샷](./media/create-managed-service-identity/system-assigned-managed-identity.png)

     자세한 내용은 [예: 관리 ID로 관리형 커넥터 트리거 또는 작업 인증](#authenticate-managed-connector-managed-identity)을 참조하세요.

<a name="authenticate-built-in-managed-identity"></a>

#### <a name="example-authenticate-built-in-trigger-or-action-with-a-managed-identity"></a>예: 관리 ID로 기본 제공 트리거 또는 작업 인증

HTTP 트리거 또는 작업은 논리 앱에서 사용하도록 설정한 시스템이 할당한 ID를 사용할 수 있습니다. 일반적으로 HTTP 트리거 또는 작업에서 다음 속성을 사용하여 액세스하려는 리소스 또는 엔터티를 지정합니다.

| 속성 | 필수 | Description |
|----------|----------|-------------|
| **메서드** | 예 | 실행하려는 작업에서 사용하는 HTTP 메서드 |
| **URI** | 예 | 대상 Azure 리소스 또는 엔터티에 액세스하기 위한 엔드포인트 URL. URI 구문에는 일반적으로 Azure 리소스 또는 서비스에 대한 [리소스 ID](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)가 포함됩니다. |
| **헤더** | 예 | 나가는 요청에 필요하거나 포함시키려는 헤더 값(예: 콘텐츠 형식) |
| **쿼리** | 예 | 요청에 필요하거나 포함시키려는 쿼리 매개 변수(예: 특정 작업에 대한 매개 변수 또는 실행하려는 작업에 대한 API 버전) |
| **인증** | 예 | 대상 리소스 또는 엔터티에 대한 액세스를 인증하는 데 사용할 인증 유형 |
||||

특정한 예로, 이전에 ID에 대한 액세스를 설정한 Azure Storage 계정의 Blob에서 [Blob 스냅샷 작업](/rest/api/storageservices/snapshot-blob)을 실행하려고 한다고 가정합니다. 그러나 [Azure Blob Storage 커넥터](/connectors/azureblob/)는 현재 이 작업을 제공하지 않습니다. 대신 이 작업은 [HTTP 작업](../logic-apps/logic-apps-workflow-actions-triggers.md#http-action) 또는 다른 [Blob Service REST API 작업](/rest/api/storageservices/operations-on-blobs)을 사용하여 실행할 수 있습니다.

> [!IMPORTANT]
> HTTP 요청 및 관리 ID를 사용하여 방화벽 내에서 Azure 스토리지 계정에 액세스하려면 [신뢰할 수 있는 Microsoft 서비스의 액세스를 허용하는 예외](../connectors/connectors-create-api-azureblobstorage.md#access-blob-storage-with-managed-identities)를 사용하는 스토리지 계정도 설정해야 합니다.

[Blob 스냅샷 작업](/rest/api/storageservices/snapshot-blob)을 실행하기 위해 HTTP 작업에서 지정하는 속성은 다음과 같습니다.

| 속성 | 필수 | 예제 값 | Description |
|----------|----------|---------------|-------------|
| **메서드** | 예 | `PUT`| Blob 스냅샷 작업에서 사용하는 HTTP 메서드 |
| **URI** | 예 | `https://{storage-account-name}.blob.core.windows.net/{blob-container-name}/{folder-name-if-any}/{blob-file-name-with-extension}` | 이 구문을 사용하는 Azure 글로벌(퍼블릭) 환경의 Azure Blob Storage 파일에 대한 리소스 ID |
| **헤더** | Azure Storage용 | `x-ms-blob-type` = `BlockBlob` <p>`x-ms-version` = `2019-02-02` <p>`x-ms-date` = `@{formatDateTime(utcNow(),'r')}` | Azure Storage 작업에 필요한 `x-ms-blob-type`, `x-ms-version` 및 `x-ms-date` 헤더 값입니다. <p><p>**중요**: Azure Storage에 대한 나가는 HTTP 트리거 및 작업 요청에서 헤더에는 실행하려는 작업에 대한 `x-ms-version` 속성 및 API 버전이 필요합니다. `x-ms-date`는 현재 날짜여야 합니다. 그렇지 않으면 논리 앱은 `403 FORBIDDEN` 오류와 함께 실패합니다. 현재 날짜를 필수 형식으로 가져오려면 예제 값의 식을 사용할 수 있습니다. <p>자세한 내용은 다음 항목을 참조하세요. <p><p>- [요청 헤더 - Blob 스냅샷 ](/rest/api/storageservices/snapshot-blob#request) <br>- [Azure Storage 서비스에 대한 버전 관리](/rest/api/storageservices/versioning-for-the-azure-storage-services#specifying-service-versions-in-requests) |
| **쿼리** | Blob 스냅샷 작업에만 해당 | `comp` = `snapshot` | 작업에 대한 쿼리 매개 변수 이름 및 값입니다. |
|||||

다음은 이러한 속성 값을 모두 보여 주는 HTTP 작업 예입니다.

![Azure 리소스에 액세스하기 위한 HTTP 작업 추가](./media/create-managed-service-identity/http-action-example.png)

1. HTTP 작업을 추가한 후에는 HTTP 동작에 **인증** 속성을 추가합니다. **새 매개 변수 추가** 목록에서 **인증** 을 선택합니다.

   ![HTTP 작업에 "인증" 속성 추가](./media/create-managed-service-identity/add-authentication-property.png)

   > [!NOTE]
   > 일부 트리거 및 작업만 인증 유형을 추가할 수 있도록 지원합니다. 자세한 내용은 [트리거 인증 형식과 인증 지원 동작](../logic-apps/logic-apps-securing-a-logic-app.md#authentication-types-supported-triggers-actions)을 참조하세요.

1. **인증 유형** 목록에서 **관리 ID** 를 선택합니다.

   !["인증"에 대해 "관리 ID" 선택](./media/create-managed-service-identity/select-managed-identity.png)

1. 관리 ID 목록에서 시나리오에 따라 사용 가능한 옵션 중에서 선택합니다.

   * 시스템이 할당한 ID를 설정하는 경우 **시스템이 할당한 관리 ID** 를 선택합니다(아직 선택하지 않은 경우).

     !["시스템이 할당한 관리 ID" 선택](./media/create-managed-service-identity/select-system-assigned-identity-for-action.png)

   * 사용자가 할당한 ID를 설정하는 경우 해당 ID를 선택합니다(아직 선택하지 않은 경우).

     ![사용자가 할당한 ID 선택](./media/create-managed-service-identity/select-user-assigned-identity-for-action.png)

   이 예에서는 **시스템이 할당한 관리 ID** 를 사용하여 계속 진행합니다.

1. 일부 트리거 및 작업에서는 대상 리소스 ID를 설정하기 위한 **대상 그룹** 속성도 표시됩니다. **대상 그룹** 속성을 [대상 리소스 또는 서비스에 대한 리소스 ID](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)로 설정합니다. 그렇지 않으면 **대상 그룹** 속성에서 기본적으로 Azure Resource Manager에 대한 리소스 ID인 `https://management.azure.com/` 리소스 ID를 사용합니다.
  
    예를 들어 [전역 Azure 클라우드의 Key Vault 리소스](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-key-vault)에 대한 액세스를 인증하려면 **Audience** 속성을 정확히 다음 리소스 ID로 설정해야 합니다. `https://vault.azure.net`. 이 특정 리소스 ID에는 후행 슬래시가 ‘없습니다.’ 실제로 후행 슬래시를 포함하면 `400 Bad Request` 오류 또는 `401 Unauthorized` 오류가 발생할 수 있습니다.

   > [!IMPORTANT]
   > 대상 리소스 ID는 필수 후행 슬래시를 포함하여 Azure AD(Active Directory)에 필요한 값과 *정확히 일치* 해야 합니다. 예를 들어 모든 Azure Blob Storage 계정에 대한 리소스 ID에는 후행 슬래시가 필요합니다. 그러나 특정 스토리지 계정에 대한 리소스 ID에는 후행 슬래시가 필요하지 않습니다. [Azure AD를 지원하는 Azure 서비스에 대한 리소스 ID](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)를 확인하세요.

   다음 예에서는 인증에 사용되는 액세스 토큰이 모든 스토리지 계정에 유효하도록 **대상 그룹** 속성을 `https://storage.azure.com/`으로 설정합니다. 그러나 특정 스토리지 계정에 대한 루트 서비스 URL(`https://fabrikamstorageaccount.blob.core.windows.net`)도 지정할 수 있습니다.

   !["대상 그룹" 속성을 대상 리소스 ID로 설정](./media/create-managed-service-identity/specify-audience-url-target-resource.png)

   Azure Storage용 Azure AD를 사용하여 액세스 권한을 부여하는 방법에 대한 자세한 내용은 다음 문서를 참조하세요.

   * [Azure Active Directory를 사용하여 Azure Blob 및 큐에 대한 액세스 권한 부여](../storage/blobs/authorize-access-azure-active-directory.md)
   * [Azure Active Directory를 사용하여 Azure Storage에 대한 액세스 권한 부여](/rest/api/storageservices/authorize-with-azure-active-directory#use-oauth-access-tokens-for-authentication)

1. 논리 앱을 원하는 방식으로 계속 빌드합니다.

<a name="authenticate-managed-connector-managed-identity"></a>

#### <a name="example-authenticate-managed-connector-trigger-or-action-with-a-managed-identity"></a>예: 관리 ID로 관리형 커넥터 트리거 또는 작업 인증

**리소스 읽기** Azure Resource Manager 작업은 논리 앱에서 사용하도록 설정된 관리 ID를 사용할 수 있습니다. 이 예에서는 시스템이 할당한 관리 ID를 사용하는 방법을 보여 줍니다.

1. 워크플로에 작업을 추가한 후 테넌트 선택 페이지에서 **관리 ID로 연결** 을 선택합니다.

   ![Azure Resource Manager 작업 및 ‘관리 ID로 연결’ 선택을 보여 주는 스크린샷](./media/create-managed-service-identity/select-connect-managed-identity.png)

   이제 작업은 논리 앱에서 현재 사용하도록 설정된 관리 ID 유형을 포함하는 관리 ID 목록으로 연결 이름 페이지를 표시합니다.

1. 연결 이름 페이지에서 연결의 이름을 제공합니다. 관리 ID 목록에서 이 예의 **시스템이 할당한 관리 ID** 인 관리 ID를 선택하고 **만들기** 를 선택합니다. 사용자가 할당한 관리 ID를 사용하도록 설정한 경우 대신 해당 ID를 선택합니다.

   ![연결 이름이 입력된 Azure Resource Manager와 선택된 ‘시스템이 할당한 관리 ID’를 보여 주는 스크린샷](./media/create-managed-service-identity/system-assigned-managed-identity.png)

   관리 ID를 사용하도록 설정하지 않으면 연결할 때 다음과 같은 오류가 나타납니다.

   *논리 앱에 관리 ID를 사용하도록 설정한 다음 대상 리소스에서 ID에 필요한 액세스 권한을 부여해야 합니다.*

   ![관리 ID를 사용하도록 설정하지 않아 오류가 발생한 Azure Resource Manager 작업을 보여 주는 스크린샷](./media/create-managed-service-identity/system-assigned-managed-identity-disabled.png)

1. 성공적으로 연결한 후 디자이너는 관리 ID 인증을 사용하여 동적 값, 콘텐츠 또는 스키마를 가져올 수 있습니다.

1. 논리 앱을 원하는 방식으로 계속 빌드합니다.

<a name="logic-app-resource-definition-connection-managed-identity"></a>

### <a name="logic-app-resource-definition-and-connections-that-use-a-managed-identity"></a>관리 ID를 사용하는 논리 앱 리소스 정의 및 연결

관리 ID를 사용하도록 설정하고 사용하는 연결은 관리 ID로만 작동하는 특수 연결 형식입니다. 런타임에 연결은 논리 앱에서 사용하도록 설정된 관리 ID를 사용합니다. 이 구성은 논리 앱 리소스 정의의 `parameters` 개체에 저장되며, 이것은 사용자 할당 ID를 사용하는 경우 ID의 리소스 ID와 함께 연결의 리소스 ID에 대한 포인터를 포함하는 `$connections` 개체를 포함합니다.

이 예제에서는 논리 앱에서 시스템이 할당한 관리 ID를 사용하도록 설정할 때의 구성을 보여 줍니다.

```json
"parameters": {
   "$connections": {
      "value": {
         "<action-name>": {
            "connectionId": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{resourceGroupName}/providers/Microsoft.Web/connections/{connection-name}",
            "connectionName": "{connection-name}",
            "connectionProperties": {
               "authentication": {
                  "type": "ManagedServiceIdentity"
               }
            },
            "id": "/subscriptions/{Azure-subscription-ID}/providers/Microsoft.Web/locations/{Azure-region}/managedApis/{managed-connector-type}"
         }
      }
   }
}
```

이 예제에서는 논리 앱에서 사용자가 할당한 관리 ID를 사용하도록 설정할 때의 구성을 보여 줍니다.

```json
"parameters": {
   "$connections": {
      "value": {
         "<action-name>": {
            "connectionId": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{resourceGroupName}/providers/Microsoft.Web/connections/{connection-name}",
            "connectionName": "{connection-name}",
            "connectionProperties": {
               "authentication": {
                  "identity": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{resourceGroupName}/providers/microsoft.managedidentity/userassignedidentities/{managed-identity-name}",
                  "type": "ManagedServiceIdentity"
               }
            },
            "id": "/subscriptions/{Azure-subscription-ID}/providers/Microsoft.Web/locations/{Azure-region}/managedApis/{managed-connector-type}"
         }
      }
   }
}
```

런타임 중에 Logic Apps 서비스는 논리 앱의 관리형 커넥터 트리거와 작업이 관리 ID를 사용하도록 설정되어 있는지, 모든 필요한 권한이 관리 ID를 사용하여 트리거 및 작업에 지정된 대상 리소스에 액세스하도록 설정되었는지를 확인합니다. 성공하면 Logic Apps 서비스에서 관리 ID와 연결된 Azure AD 토큰을 검색하고, 이 ID로 대상 리소스에 대한 액세스를 인증하여 트리거 및 작업에서 구성된 작업을 수행합니다.

<a name="arm-templates-connection-resource-managed-identity"></a>

## <a name="arm-template-for-managed-connections-and-managed-identities"></a>관리형 연결 및 관리 ID를 위한 ARM 템플릿

ARM 템플릿을 사용하여 배포를 자동화하고 논리 앱이 관리 ID를 사용하는 관리형 커넥터 트리거 또는 작업을 포함하면, 기본 연결 리소스 정의가 `parameterValueType` 속성을 포함하고 속성 값이 `Alternative`인지 확인합니다. 그렇지 않은 경우 ARM 배포는 인증을 위해 관리 ID를 사용하도록 연결을 설정하지 않으며 논리 앱의 워크플로에서 연결이 작동하지 않습니다. 이 요구 사항은 [ **관리 ID로 연결** 옵션](#authenticate-managed-connector-managed-identity)을 선택한 [특정 관리형 커넥터 트리거 및 작업](#managed-connectors-managed-identity)에만 적용됩니다.

예를 들어, `parameterValueType` 속성을 정의에 포함하는 관리 ID를 사용하는 Azure Automation 작업용 기본 연결 리소스 정의는 다음과 같으며, 속성 값은 `Alternative`로 설정되어 있습니다.

```json
{
    "type": "Microsoft.Web/connections",
    "name": "[variables('automationAccountApiConnectionName')]",
    "apiVersion": "2016-06-01",
    "location": "[parameters('location')]",
    "kind": "V1",
    "properties": {
        "api": {
            "id": "[subscriptionResourceId('Microsoft.Web/locations/managedApis', parameters('location'), 'azureautomation')]"
        },
        "customParameterValues": {},
        "displayName": "[variables('automationAccountApiConnectionName')]",
        "parameterValueType": "Alternative"
    }
},
```

<a name="remove-identity"></a>

## <a name="disable-managed-identity"></a>관리 ID 사용 안 함

관리 ID를 논리 앱에 사용하는 것을 중지하려면 다음 옵션을 사용할 수 있습니다.

* [Azure Portal](#azure-portal-disable)
* [ARM 템플릿](#template-disable)
* Azure PowerShell
  * [역할 할당 제거](../role-based-access-control/role-assignments-powershell.md)
  * [사용자가 할당한 ID 삭제](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md)
* Azure CLI
  * [역할 할당 제거](../role-based-access-control/role-assignments-cli.md)
  * [사용자가 할당한 ID 삭제](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md)
* Azure REST API
  * [역할 할당 제거](../role-based-access-control/role-assignments-rest.md)
  * [사용자가 할당한 ID 삭제](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-rest.md)

논리 앱을 삭제하면 Azure에서 Azure AD의 관리 ID를 자동으로 제거합니다.

<a name="azure-portal-disable"></a>

### <a name="disable-managed-identity-in-the-azure-portal"></a>Azure Portal에서 관리 ID 사용 안 함

Azure Portal에서 먼저 [대상 리소스](#disable-identity-target-resource)에 대한 ID의 액세스 권한을 제거합니다. 다음으로, 시스템이 할당한 ID를 해제하거나 [논리 앱](#disable-identity-logic-app)에서 사용자가 할당한 ID를 제거합니다.

<a name="disable-identity-target-resource"></a>

#### <a name="remove-identity-access-from-resources"></a>리소스에서 ID 액세스 제거

1. [Azure Portal](https://portal.azure.com)에서 관리 ID에 대한 액세스를 제거하려는 대상 Azure 리소스로 이동합니다.

1. 대상 리소스의 메뉴에서 **액세스 제어(IAM)** 를 선택합니다. 도구 모음 아래에서 **역할 할당** 을 선택합니다.

1. 역할 목록에서 제거하려는 관리 ID를 선택합니다. 도구 모음에서 **제거** 를 선택합니다.

   > [!TIP]
   > **제거** 옵션을 사용하지 않도록 설정되면 대부분 권한이 없을 가능성이 높습니다. 리소스에 대한 역할을 관리할 수 있는 권한에 대한 자세한 내용은 [Azure Active Directory의 관리자 역할 권한](../active-directory/roles/permissions-reference.md)을 참조하세요.

이제 관리 ID가 제거되었고 대상 리소스에 더 이상 액세스할 수 없습니다.

<a name="disable-identity-logic-app"></a>

#### <a name="disable-managed-identity-on-logic-app"></a>논리 앱에서 관리 ID 사용 안 함

1. [Azure Portal](https://portal.azure.com)의 디자이너 보기에서 논리 앱을 엽니다.

1. 논리 앱 메뉴의 **설정** 아래에서 **ID** 를 선택한 다음, ID에 대한 단계를 수행합니다.

   * **시스템 할당 항목** > **켜기** > **저장** 을 차례로 선택합니다. Azure에서 확인하라는 메시지가 표시되면 **예** 를 선택합니다.

     ![시스템이 할당한 ID 사용 안 함](./media/create-managed-service-identity/disable-system-assigned-identity.png)

   * **사용자 할당 항목** 및 관리 ID를 선택한 다음, **제거** 를 선택합니다. Azure에서 확인하라는 메시지가 표시되면 **예** 를 선택합니다.

     ![사용자가 할당한 ID 제거](./media/create-managed-service-identity/remove-user-assigned-identity.png)

이제 논리 앱에서 관리 ID를 사용하지 않도록 설정되었습니다.

<a name="template-disable"></a>

### <a name="disable-managed-identity-in-an-arm-template"></a>ARM 템플릿에서 관리 ID 사용 안 함

ARM 템플릿을 사용하여 논리 앱의 관리 ID를 만든 경우 `identity` 개체의 `type` 자식 속성을 `None`으로 설정합니다.

```json
"identity": {
   "type": "None"
}
```

## <a name="next-steps"></a>다음 단계

* [Azure Logic Apps에서 액세스 및 데이터 보안](../logic-apps/logic-apps-securing-a-logic-app.md)