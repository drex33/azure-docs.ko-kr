---
title: 관리 ID를 통해 연결 인증
description: 관리 ID를 사용하여 Azure Logic Apps 자격 증명 또는 비밀 없이 Azure AD로 보호된 리소스에 대한 워크플로 연결을 인증합니다.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, azla
ms.topic: how-to
ms.date: 10/22/2021
ms.custom: devx-track-azurepowershell, subject-rbac-steps, ignite-fall-2021
ms.openlocfilehash: c6e814f0b0b36408210cac7657c947f16a0076c5
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131085368"
---
# <a name="authenticate-access-to-azure-resources-with-managed-identities-in-azure-logic-apps"></a>Azure Logic Apps 관리 ID를 사용하여 Azure 리소스에 대한 액세스 인증

논리 앱 워크플로의 일부 트리거 및 작업은 이전에 *MSI(관리 서비스 ID)로* 알려진 [관리 ID를](../active-directory/managed-identities-azure-resources/overview.md)사용하여 Azure AD(Azure Active Directory)로 보호되는 리소스에 대한 연결을 인증하는 것을 지원합니다. 논리 앱 리소스에서 관리 ID를 사용하도록 설정한 경우 자격 증명, 비밀 또는 Azure AD 토큰을 제공할 필요가 없습니다. Azure는 이 ID를 관리하며 이 중요한 정보를 관리할 필요가 없으므로 인증 정보를 안전하게 유지하는 데 도움이 됩니다.

Azure Logic Apps 하나의 논리 앱 리소스에서만 사용할 수 있는 [ *시스템 할당* 관리 ID](../active-directory/managed-identities-azure-resources/overview.md)및 논리 앱 워크플로가 실행되는 위치에 따라 논리 앱 리소스 그룹에서 공유할 수 있는 사용자 [ *할당* 관리 ID](../active-directory/managed-identities-azure-resources/overview.md)를 지원합니다.

| 논리 앱 리소스 종류 | 환경 | Description |
|-------------------------|-------------|-------------|
| Consumption | - 다중 테넌트 Azure Logic Apps <p><p>- ISE(통합 서비스 환경) | 논리 앱 리소스 수준 및 연결 수준에서 시스템 할당 ID 또는 *단일* 사용자 할당 ID를 사용하도록 설정하고 *사용할* 수 있습니다. |
| 표준 | - 단일 테넌트 Azure Logic Apps <p><p>- App Service Environment v3(ASEv3) <p><p>- Azure Arc 사용하도록 설정된 Logic Apps | 현재 자동으로 사용하도록 설정되는 시스템 할당 *ID만* 사용할 수 있습니다. 사용자 할당 ID는 현재 사용할 수 없습니다. |
|||

Azure Logic Apps 관리 ID 제한에 대해 알아보려면 [논리 앱의 관리 ID에 대한 제한을 검토하세요.](logic-apps-limits-and-config.md#managed-identity) 소비 및 표준 논리 앱 리소스 종류 및 환경에 대한 자세한 내용은 다음 설명서를 검토하세요.

* [Azure Logic Apps란?](logic-apps-overview.md#resource-environment-differences)
* [단일 테넌트 대 다중 테넌트 및 통합 서비스 환경](single-tenant-overview-compare.md)
* [Azure Arc 지원 Logic Apps](azure-arc-enabled-logic-apps-overview.md)

<a name="triggers-actions-managed-identity"></a>
<a name="managed-connectors-managed-identity"></a>

## <a name="where-you-can-use-a-managed-identity"></a>관리 ID를 사용할 수 있는 위치

Azure AD OAuth(Azure AD Open Authentication)를 지원하는 특정 기본 제공 및 관리 커넥터 작업만 인증에 관리 ID를 사용할 수 있습니다. 다음 표에서는 *샘플 선택* 만 제공합니다. 자세한 전체 목록은 [인증을 지원하는 트리거 및 작업에 대한 인증 유형 및](logic-apps-securing-a-logic-app.md#authentication-types-supported-triggers-actions) 관리 [ID를 사용하는 Azure AD 인증을 지원하는 Azure 서비스를 검토하세요.](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)

### <a name="consumption"></a>[Consumption](#tab/consumption)

다음 표에서는 **논리 앱(소비)** 리소스 종류에서 시스템 할당 관리 ID 또는 사용자 할당 관리 ID를 사용할 수 있는 작업을 나열합니다.

| 작업 유형 | 지원되는 작업 |
|----------------|----------------------|
| 기본 제공 | - Azure API Management <br>- Azure App Services <br>- Azure Functions <br>- HTTP <br>- HTTP + 웹후크 <p>**참고:** HTTP 작업은 시스템 할당 ID를 사용하여 Azure Firewall 뒤의 Azure Storage 계정에 대한 연결을 인증할 수 있습니다. 그러나 동일한 연결을 인증하기 위해 사용자 할당 관리 ID를 지원하지 않습니다. |
| 관리형 커넥터(**미리 보기**) | 단일 인증: <br>- Azure Automation <br>- Azure Event Grid <br>- Azure Key Vault <br>- Azure Resource Manager <br>- Azure AD를 사용하는 HTTP <p>다중 인증: <br> - Azure Blob Storage <br>- SQL Server |
|||

### <a name="standard"></a>[Standard](#tab/standard)

다음 표에서는 **논리 앱(표준)** 리소스 종류에서 시스템 할당 관리 ID를 사용할 수 있는 작업을 나열합니다.

| 작업 유형 | 지원되는 작업 |
|----------------|----------------------|
| 기본 제공 | - HTTP <br>- HTTP + 웹후크 <p>**참고:** HTTP 작업은 시스템 할당 ID를 사용하여 Azure Firewall 뒤의 Azure Storage 계정에 대한 연결을 인증할 수 있습니다. |
| 관리형 커넥터(**미리 보기**) | 단일 인증: <br>- Azure Automation <br>- Azure Event Grid <br>- Azure Key Vault <br>- Azure Resource Manager <br>- Azure AD를 사용하는 HTTP <p>다중 인증: <br> - Azure Blob Storage <br>- SQL Server |
|||

---

이 문서에서는 **논리 앱(소비) 또는 논리 앱(표준)** 리소스 종류를 사용하는지 여부에 따라 시스템 할당 ID  또는 사용자 할당 ID를 사용하도록 설정하고 설정하는 방법을 보여 줍니다. 수동으로 만들 필요가 없는 시스템 할당 ID와 달리 **논리 앱(소비)** 리소스 종류에 대한 사용자 할당 ID를 수동으로 만들어야 합니다.  이 문서에는 ARM 템플릿(Azure Portal 및 Azure Resource Manager 템플릿)을 사용하여 사용자 할당 ID를 만드는 단계가 포함되어 있습니다. Azure PowerShell, Azure CLI 및 Azure REST API 다음 설명서를 검토하세요.

| 도구 | 설명서 |
|------|---------------|
| Azure PowerShell | [사용자가 할당한 ID 만들기](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md) |
| Azure CLI | [사용자가 할당한 ID 만들기](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md) |
| Azure REST API | [사용자가 할당한 ID 만들기](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-rest.md) |
|||

## <a name="prerequisites"></a>사전 요구 사항

* Azure 계정 및 구독 구독이 없는 경우 [Azure 체험 계정에 등록](https://azure.microsoft.com/free/)합니다. 액세스 권한이 필요한 관리 ID 및 대상 Azure 리소스는 모두 동일한 Azure 구독을 사용해야 합니다.

* Azure 리소스에 대한 관리 ID 액세스 권한을 부여하려면 역할을 해당 ID의 대상 리소스에 추가해야 합니다. 역할을 추가하려면 이 역할을 해당 Azure AD 테넌트의 ID에 할당할 수 있는 [Azure AD 관리자 권한](../active-directory/roles/permissions-reference.md)이 필요합니다.

* 액세스하려는 대상 Azure 리소스. 이 리소스에서는 논리 앱 리소스 또는 연결이 대상 리소스에 대한 액세스를 인증하는 데 도움이 되는 관리 ID에 대한 역할을 추가합니다.

* 관리 ID 를 지원하는 트리거 또는 작업을 사용하려는 논리 앱 [리소스입니다.](logic-apps-securing-a-logic-app.md#authentication-types-supported-triggers-actions)

  | 논리 앱 리소스 종류 | 관리 ID 지원 |
  |-------------------------|--------------------------|
  | 소비 | 시스템 할당 또는 사용자 할당 ID |
  | 표준 | 시스템 할당 ID(자동으로 사용) |
  |||

<a name="system-assigned-azure-portal"></a>
<a name="azure-portal-system-logic-app"></a>

## <a name="enable-system-assigned-identity-in-azure-portal"></a>Azure Portal에서 시스템이 할당한 ID 사용

### <a name="consumption"></a>[Consumption](#tab/consumption)

1. [Azure Portal](https://portal.azure.com)논리 앱 리소스를 엽니다.

1. 논리 앱 메뉴의 **설정** 에서 **ID** 를 선택합니다.

1. **ID** 창의 **시스템 할당** 아래에서 저장 **시를**  >  선택합니다. Azure에서 확인하라는 메시지가 표시되면 **예** 를 선택합니다.

   ![소비 논리 앱의 "ID" 창과 "켜기" 및 "저장"이 선택된 "시스템 할당" 탭이 있는 Azure Portal 보여주는 스크린샷](./media/create-managed-service-identity/enable-system-assigned-identity-consumption.png)

   > [!NOTE]
   > 단일 관리 ID만 있을 수 있다는 오류가 발생하면 논리 앱 리소스가 이미 사용자 할당 ID와 연결되어 있습니다. 시스템 할당 ID를 추가하려면 먼저 논리 앱 리소스에서 사용자 할당 ID를 *제거해야* 합니다.

   이제 논리 앱 리소스는 Azure AD에 등록되고 개체 ID로 표시되는 시스템 할당 ID를 사용할 수 있습니다.

   ![시스템 할당 ID에 대한 개체 ID가 있는 소비 논리 앱의 "ID" 창을 보여주는 스크린샷](./media/create-managed-service-identity/object-id-system-assigned-identity.png)

   | 속성 | 값 | 설명 |
   |----------|-------|-------------|
   | **개체(보안 주체) ID** | <*identity-resource-ID*> | Azure AD 테넌트에서 논리 앱에 대한 시스템 할당 ID를 나타내는 GUID(Globally Unique Identifier)입니다. |
   ||||

1. 이제 이 항목의 뒷부분에 있는 [리소스에 대한 ID 액세스 권한을 부여하는 단계](#access-other-resources)를 수행합니다.

### <a name="standard"></a>[Standard](#tab/standard)

논리 **앱(표준)** 리소스에서 시스템 할당 ID가 자동으로 활성화됩니다.

1. [Azure Portal](https://portal.azure.com)디자이너에서 논리 앱 워크플로를 엽니다.

1. 논리 앱 메뉴의 **설정** 에서 **ID** 를 선택합니다.

1. **ID** 창의 **시스템 할당** 아래에서 저장 **시를**  >  선택합니다. Azure에서 확인하라는 메시지가 표시되면 **예** 를 선택합니다.

   ![표준 논리 앱의 "ID" 창과 "켜기" 및 "저장"이 선택된 "시스템 할당" 탭이 있는 Azure Portal 보여주는 스크린샷](./media/create-managed-service-identity/enable-system-assigned-identity-standard.png)

   이제 논리 앱 리소스는 Azure AD에 등록되고 개체 ID로 표시되는 시스템 할당 ID를 사용할 수 있습니다.

   ![시스템 할당 ID에 대한 개체 ID가 있는 표준 논리 앱의 "ID" 창을 보여주는 스크린샷](./media/create-managed-service-identity/object-id-system-assigned-identity.png)

   | 속성 | 값 | 설명 |
   |----------|-------|-------------|
   | **개체(보안 주체) ID** | <*identity-resource-ID*> | Azure AD 테넌트에서 논리 앱에 대한 시스템 할당 ID를 나타내는 GUID(Globally Unique Identifier)입니다. |
   ||||

1. 이제 이 항목의 뒷부분에 있는 [리소스에 대한 ID 액세스 권한을 부여하는 단계](#access-other-resources)를 수행합니다.

---

<a name="system-assigned-template"></a>
<a name="template-system-logic-app"></a>

## <a name="enable-system-assigned-identity-in-an-arm-template"></a>ARM 템플릿에서 시스템 할당 ID 사용

논리 앱과 같은 Azure 리소스의 만들기 및 배포를 자동화하려면 [ARM 템플릿](logic-apps-azure-resource-manager-templates-overview.md)을 사용할 수 있습니다. 템플릿에서 논리 앱 리소스에 대해 시스템 할당 관리 ID를 사용하도록 설정하려면 템플릿에서 `identity` 논리 앱의 리소스 정의에 개체 및 자식 속성을 추가합니다. `type` 예를 들면 다음과 같습니다.

### <a name="consumption"></a>[Consumption](#tab/consumption)

```json
{
   "apiVersion": "2016-06-01",
   "type": "Microsoft.logic/workflows",
   "name": "[variables('logicappName')]",
   "location": "[resourceGroup().location]",
   "identity": {
      "type": "SystemAssigned"
   },
   "properties": {},
   <...>
}
```

### <a name="standard"></a>[Standard](#tab/standard)

```json
{
   "apiVersion": "2021-01-15",
   "type": "Microsoft.Web/sites",
   "name": "[variables('sites_<logic-app-resource-name>_name')]",
   "location": "[resourceGroup().location]",
   "kind": "functionapp,workflowapp",
   "identity": {
      "type": "SystemAssigned"
   },
   "properties": {},
   <...>
}
```

---

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

<a name="azure-portal-user-identity"></a>
<a name="user-assigned-azure-portal"></a>

## <a name="create-user-assigned-identity-in-the-azure-portal-consumption-only"></a>Azure Portal 사용자 할당 ID 만들기(사용량에만 해당)

**논리 앱(소비)** 리소스에서 사용자 할당 ID를 사용하도록 설정하려면 먼저 해당 ID를 별도의 Azure 리소스로 만들어야 합니다.

1. [Azure Portal](https://portal.azure.com) 검색 상자에 를 `managed identities` 입력합니다. **관리 ID 를** 선택합니다.

   !["관리 ID"가 선택된 Azure Portal 보여주는 스크린샷](./media/create-managed-service-identity/find-select-managed-identities.png)

1. 관리 **ID** 창에서 **만들기를** 선택합니다.

   !["관리 ID" 창과 선택된 "만들기"를 보여주는 스크린샷.](./media/create-managed-service-identity/add-user-assigned-identity.png)

1. 관리 ID에 대한 정보를 제공한 다음, **검토 + 만들기** 를 선택합니다. 예를 들면 다음과 같습니다.

   ![관리 ID 세부 정보가 있는 "사용자 할당 관리 ID 만들기" 창을 보여주는 스크린샷](./media/create-managed-service-identity/create-user-assigned-identity.png)

   | 속성 | 필수 | 값 | 설명 |
   |----------|----------|-------|-------------|
   | **구독** | 예 | <*Azure-subscription-name*> | 사용할 Azure 구독의 이름입니다. |
   | **리소스 그룹** | 예 | <*Azure-resource-group-name*> | 사용할 Azure 리소스 그룹의 이름입니다. 새 그룹을 만들거나 기존 그룹을 선택합니다. 이 예제는 `fabrikam-managed-identities-RG`라는 새 리소스 그룹을 만듭니다. |
   | **지역** | 예 | <*Azure-region*> | 리소스에 대한 정보를 저장할 Azure 지역입니다. 이 예제에서는 **미국 서부** 를 사용합니다. |
   | **이름** | 예 | <*user-assigned-identity-name*> | 사용자가 할당한 ID를 제공하는 이름입니다. 이 예에서는 `Fabrikam-user-assigned-identity`를 사용합니다. |
   |||||

   정보의 유효성을 검사한 후 Azure는 관리 ID를 만듭니다. 이제 사용자 할당 ID를 하나만 가질 수 있는 논리 앱 리소스에 사용자 할당 ID를 추가할 수 있습니다.

1. Azure Portal 논리 앱 리소스를 엽니다.

1. 논리 앱 메뉴의 **설정** 에서 **ID** 를 선택합니다.

1. **ID** 창에서 **사용자 할당**  >  **추가를** 선택합니다.

   !["추가"가 선택된 "ID" 창을 보여주는 스크린샷.](./media/create-managed-service-identity/add-user-assigned-identity-logic-app.png)

1. 사용자 **할당 관리 ID 추가 창에서** 다음 단계를 수행합니다.

   1. **구독** 목록에서 Azure 구독을 선택합니다(아직 선택하지 않은 경우).

   1. 해당 구독의 *모든* 관리 ID가 있는 목록에서 원하는 사용자 할당 ID를 선택합니다. 목록을 필터링하려면 **사용자가 할당한 관리 ID** 검색 상자에서 ID 또는 리소스 그룹의 이름을 입력합니다.

      ![선택된 사용자 할당 ID를 보여주는 스크린샷.](./media/create-managed-service-identity/select-user-assigned-identity.png)

   1. 완료되면 **추가** 를 선택합니다.

      > [!NOTE]
      > 하나의 관리 ID만 있을 수 있다는 오류가 표시되면 논리 앱이 이미 시스템이 할당한 ID와 연결되어 있습니다. 사용자 할당 ID를 추가하려면 먼저 시스템 할당 ID를 사용하지 않도록 설정해야 합니다.

   이제 논리 앱이 사용자가 할당한 관리 ID와 연결됩니다.

   ![사용자 할당 ID와 논리 앱 리소스 간의 연결을 보여주는 스크린샷](./media/create-managed-service-identity/added-user-assigned-identity.png)

1. 이제 이 항목의 뒷부분에 있는 [리소스에 대한 ID 액세스 권한을 부여하는 단계](#access-other-resources)를 수행합니다.

<a name="template-user-identity"></a>

## <a name="create-user-assigned-identity-in-an-arm-template-consumption-only"></a>ARM 템플릿에서 사용자 할당 ID 만들기(사용량에만 해당)

논리 앱과 같은 Azure 리소스의 만들기 및 배포를 자동화하려면 인증을 위해 [사용자 할당 ID](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-arm.md)를 지원하는 [ARM 템플릿](logic-apps-azure-resource-manager-templates-overview.md)을 사용할 수 있습니다. 템플릿의 `resources` 섹션에서 논리 앱의 리소스 정의에는 다음 항목이 필요합니다.

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

인증에 논리 앱의 관리 ID를 사용하려면 ID를 사용하려는 Azure 리소스에서 Azure RBAC(Azure 역할 기반 액세스 제어)를 사용하여 ID에 대한 액세스 권한을 설정해야 합니다. 이 섹션의 단계에서는 [ARM 템플릿(Azure Portal](../role-based-access-control/role-assignments-template.md)및 [Azure Resource Manager](#azure-portal-assign-access) 템플릿)을 사용하여 Azure 리소스에서 해당 ID에 적절한 역할을 할당하는 방법을 다룹니다. Azure PowerShell, Azure CLI 및 Azure REST API 다음 설명서를 검토하세요.

| 도구 | 설명서 |
|------|---------------|
| Azure PowerShell | [역할 할당 추가](../active-directory/managed-identities-azure-resources/howto-assign-access-powershell.md) |
| Azure CLI | [역할 할당 추가](../active-directory/managed-identities-azure-resources/howto-assign-access-cli.md) |
| Azure REST API | [역할 할당 추가](../role-based-access-control/role-assignments-rest.md) |
|||

<a name="azure-portal-assign-access"></a>

### <a name="assign-managed-identity-role-based-access-in-the-azure-portal"></a>Azure Portal에서 관리 ID 역할 기반 액세스 할당

인증에 관리 ID를 사용하려는 Azure 리소스에서 해당 대상 리소스에 액세스할 수 있는 역할에 해당 ID를 할당해야 합니다. 이 작업에 대한 자세한 내용은 [Azure RBAC를 사용하여 다른 리소스에 관리 ID 액세스 할당](../active-directory/managed-identities-azure-resources/howto-assign-access-portal.md)을 검토하세요.

> [!NOTE]
> 관리 ID가 동일한 구독의 Azure 리소스에 액세스할 수 있는 경우 ID는 해당 리소스에만 액세스할 수 있습니다. 그러나 관리 ID를 지원하는 일부 트리거 및 작업에서는 먼저 대상 리소스가 포함된 Azure 리소스 그룹을 선택해야 합니다. ID가 리소스 그룹 수준에서 액세스할 수 없는 경우 대상 리소스에 대한 액세스 권한이 있더라도 해당 그룹의 리소스가 나열되지 않습니다.
>
> 이 동작을 처리하려면 리소스뿐만 아니라 리소스 그룹에 대한 ID 액세스 권한도 부여해야 합니다. 마찬가지로 대상 리소스를 선택하기 전에 구독을 선택해야 하는 경우 ID에 구독에 대한 액세스 권한을 부여해야 합니다.

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
   | **사용자** 할당(사용량만 해당) | 적용할 수 없음 | <*Azure-subscription-name*> | <*your-user-assigned-identity-name*> |
   |||||

   역할 할당에 대한 자세한 내용은 [Azure Portal 사용하여 역할 할당](../role-based-access-control/role-assignments-portal.md) 설명서를 검토하세요.

1. ID에 대한 액세스 설정을 완료한 후 ID를 사용하여 [관리 ID를 지원하는 트리거 및 동작에 대한 액세스를 인증](#authenticate-access-with-identity)할 수 있습니다.

<a name="authenticate-access-with-identity"></a>

## <a name="authenticate-access-with-managed-identity"></a>관리 ID를 사용하여 액세스 인증

논리 [앱 리소스에 대한 관리 ID를 사용하도록 설정하고](#azure-portal-system-logic-app) [해당 ID에 대상 리소스 또는 엔터티에 대한 액세스 권한을 부여한](#access-other-resources)후에는 [관리 ID를 지원하는 트리거 및 작업에서 해당 ID를](logic-apps-securing-a-logic-app.md#authentication-types-supported-triggers-actions)사용할 수 있습니다.

> [!IMPORTANT]
> 시스템 할당 ID를 사용하려는 Azure 함수가 있는 경우 먼저 [Azure Functions 인증을 사용하도록 설정합니다.](logic-apps-azure-functions.md#enable-authentication-for-functions)

다음 단계에서는 Azure Portal을 통해 트리거 또는 작업에서 관리 ID를 사용하는 방법을 보여 줍니다. 트리거 또는 작업의 기본 JSON 정의에서 관리 ID를 지정하려면 [관리 ID 인증을 검토합니다.](logic-apps-securing-a-logic-app.md#managed-identity-authentication)

### <a name="consumption"></a>[Consumption](#tab/consumption)

1. [Azure Portal](https://portal.azure.com)논리 앱 리소스를 엽니다.

1. 아직 수행하지 않은 경우 [관리 ID를 지원하는 트리거 또는 작업](logic-apps-securing-a-logic-app.md#authentication-types-supported-triggers-actions)을 추가합니다.

   > [!NOTE]
   > 일부 트리거 및 작업만 인증 유형을 추가할 수 있도록 지원합니다. 자세한 내용은 [인증을 지원하는 트리거 및 작업에 대한 인증 유형을](logic-apps-securing-a-logic-app.md#authentication-types-supported-triggers-actions)검토하세요.

1. 추가한 트리거 또는 작업에서 다음 단계를 수행합니다.

   * **관리 ID 인증을 지원하는 기본 제공 작업**

     1. 새 **매개 변수 추가** 목록에서 속성이 표시되지 않는 경우 **Authentication** 속성을 추가합니다.

        !["새 매개 변수 추가" 목록이 열려 있고 소비에서 "인증"이 선택된 기본 제공 작업 예제를 보여주는 스크린샷.](./media/create-managed-service-identity/built-in-authentication-consumption.png)

     1. 인증 **유형** 목록에서 **관리 ID** 를 선택합니다.

        ![사용량에서 "인증 유형" 목록이 열려 있고 "관리 ID"가 선택된 기본 제공 작업 예제를 보여주는 스크린샷.](./media/create-managed-service-identity/built-in-managed-identity-consumption.png)

     자세한 내용은 [예제: 관리 ID를 통해 기본 제공 트리거 또는 작업 인증을 검토하세요.](#authenticate-built-in-managed-identity)

   * **관리 ID 인증을 지원하는 관리 커넥터 작업(미리** 보기)

     1. 테넌트 선택 페이지에서 **관리 ID(미리 보기)가 있는 커넥트** 선택합니다. 예를 들면 다음과 같습니다.

        ![Azure Resource Manager 작업 및 소비에서 선택된 "관리 ID가 있는 커넥트"를 보여주는 스크린샷.](./media/create-managed-service-identity/select-connect-managed-identity-consumption.png)

     1. 다음 페이지에서 연결 이름 에 **대해 연결에** 사용할 이름을 제공합니다.

     1. 인증 유형의 경우 관리 커넥터에 따라 다음 옵션 중 하나를 선택합니다.

        * **단일 인증:** 이러한 커넥터는 하나의 인증 유형만 지원합니다. 관리 **ID** 목록에서 현재 사용하도록 설정된 관리 ID를 선택한 다음, 아직 선택하지 않은 경우 **만들기를** 선택합니다. 예를 들면 다음과 같습니다.

          ![사용량에서 선택한 연결 이름 페이지 및 단일 관리 ID를 보여주는 스크린샷.](./media/create-managed-service-identity/single-system-identity-consumption.png)

        * **다중 인증:** 이러한 커넥터는 두 개 이상의 인증 유형을 지원합니다. 인증 **유형** 목록에서 **Logic Apps 관리 ID**  >  **만들기를** 선택합니다. 예를 들면 다음과 같습니다.

          ![사용량에서 선택한 연결 이름 페이지 및 "Logic Apps 관리 ID"를 보여주는 스크린샷.](./media/create-managed-service-identity/multi-system-identity-consumption.png)

        자세한 내용은 [예제: 관리 ID를 통해 관리 커넥터 트리거 또는 작업 인증을 검토하세요.](#authenticate-managed-connector-managed-identity)

### <a name="standard"></a>[Standard](#tab/standard)

1. [Azure Portal](https://portal.azure.com)논리 앱 리소스를 엽니다.

1. 아직 수행하지 않은 경우 [관리 ID를 지원하는 트리거 또는 작업](logic-apps-securing-a-logic-app.md#authentication-types-supported-triggers-actions)을 추가합니다.

   > [!NOTE]
   > 일부 트리거 및 작업만 인증 유형을 추가할 수 있도록 지원합니다. 자세한 내용은 [인증을 지원하는 트리거 및 작업에 대한 인증 유형을](logic-apps-securing-a-logic-app.md#authentication-types-supported-triggers-actions)검토하세요.

1. 추가한 트리거 또는 작업에서 다음 단계를 수행합니다.

   * **관리 ID 인증을 지원하는 기본 제공 작업**

     1. 새 **매개 변수 추가** 목록에서 속성이 표시되지 않는 경우 **Authentication** 속성을 추가합니다.

        !["새 매개 변수 추가" 목록이 열려 있고 "인증"이 선택된 기본 제공 작업 예제를 보여주는 스크린샷 - 표준입니다.](./media/create-managed-service-identity/built-in-authentication-standard.png)

     1. 인증 **유형** 목록에서 **관리 ID** 를 선택합니다.

        ![표준에서 "인증 유형" 목록이 열려 있고 "관리 ID"가 선택된 기본 제공 작업 예제를 보여주는 스크린샷.](./media/create-managed-service-identity/built-in-managed-identity-standard.png)

     자세한 내용은 [예제: 관리 ID를 통해 기본 제공 트리거 또는 작업 인증을 검토하세요.](#authenticate-built-in-managed-identity)

   * **관리 ID 인증을 지원하는 관리 커넥터 작업(미리** 보기)

     1. 테넌트 선택 페이지에서 **관리 ID(미리 보기)가 있는 커넥트** 선택합니다. 예를 들면 다음과 같습니다.

        ![표준에서 선택한 Azure Resource Manager 작업 및 "관리 ID가 있는 커넥트"를 보여주는 스크린샷.](./media/create-managed-service-identity/select-connect-managed-identity-standard.png)

     1. 다음 페이지에서 연결 이름 에 **대해 연결에** 사용할 이름을 제공합니다.

     1. 인증 유형의 경우 관리 커넥터에 따라 다음 옵션 중 하나를 선택합니다.

        * **단일 인증:** 이러한 커넥터는 하나의 인증 유형만 지원합니다. 관리 **ID** 목록에서 현재 사용하도록 설정된 관리 ID를 선택한 다음, 아직 선택하지 않은 경우 **만들기를** 선택합니다. 예를 들면 다음과 같습니다.

          ![표준에서 선택한 연결 이름 페이지 및 단일 관리 ID를 보여 있는 스크린샷.](./media/create-managed-service-identity/single-system-identity-standard.png)

        * **다중 인증:** 이러한 커넥터는 두 개 이상의 인증 유형을 지원합니다. 인증 **유형** 목록에서 **Logic Apps 관리 ID**  >  **만들기를** 선택합니다. 예를 들면 다음과 같습니다.

          ![연결 이름 페이지와 표준에서 선택한 "Logic Apps 관리 ID"를 보여주는 스크린샷.](./media/create-managed-service-identity/multi-system-identity-standard.png)

        자세한 내용은 [예제: 관리 ID를 통해 관리 커넥터 트리거 또는 작업 인증을 검토하세요.](#authenticate-managed-connector-managed-identity)

---

<a name="authenticate-built-in-managed-identity"></a>

## <a name="example-authenticate-built-in-trigger-or-action-with-a-managed-identity"></a>예: 관리 ID로 기본 제공 트리거 또는 작업 인증

기본 제공 HTTP 트리거 또는 작업은 논리 앱 리소스에서 사용하도록 설정하는 시스템 할당 ID를 사용할 수 있습니다. 일반적으로 HTTP 트리거 또는 작업은 다음 속성을 사용하여 액세스하려는 리소스 또는 엔터티를 지정합니다.

| 속성 | 필수 | Description |
|----------|----------|-------------|
| **메서드** | 예 | 실행하려는 작업에서 사용하는 HTTP 메서드 |
| **URI** | 예 | 대상 Azure 리소스 또는 엔터티에 액세스하기 위한 엔드포인트 URL. URI 구문에는 일반적으로 Azure 리소스 또는 서비스에 대한 [리소스 ID](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)가 포함됩니다. |
| **헤더** | 예 | 나가는 요청에 필요하거나 포함시키려는 헤더 값(예: 콘텐츠 형식) |
| **쿼리** | 예 | 요청에 필요하거나 포함시키려는 쿼리 매개 변수(예: 특정 작업에 대한 매개 변수 또는 실행하려는 작업에 대한 API 버전) |
| **인증** | 예 | 대상 리소스 또는 엔터티에 대한 액세스를 인증하는 데 사용할 인증 유형 |
||||

특정한 예로, 이전에 ID에 대한 액세스를 설정한 Azure Storage 계정의 Blob에서 [Blob 스냅샷 작업](/rest/api/storageservices/snapshot-blob)을 실행하려고 한다고 가정합니다. 그러나 [Azure Blob Storage 커넥터](/connectors/azureblob/)는 현재 이 작업을 제공하지 않습니다. 대신 이 작업은 [HTTP 작업](logic-apps-workflow-actions-triggers.md#http-action) 또는 다른 [Blob Service REST API 작업](/rest/api/storageservices/operations-on-blobs)을 사용하여 실행할 수 있습니다.

> [!IMPORTANT]
> HTTP 요청 및 관리 ID를 사용하여 방화벽 내에서 Azure 스토리지 계정에 액세스하려면 [신뢰할 수 있는 Microsoft 서비스의 액세스를 허용하는 예외](../connectors/connectors-create-api-azureblobstorage.md#access-blob-storage-with-managed-identities)를 사용하는 스토리지 계정도 설정해야 합니다.

[Blob 스냅샷 작업](/rest/api/storageservices/snapshot-blob)을 실행하기 위해 HTTP 작업에서 지정하는 속성은 다음과 같습니다.

| 속성 | 필수 | 예제 값 | Description |
|----------|----------|---------------|-------------|
| **메서드** | 예 | `PUT`| Blob 스냅샷 작업에서 사용하는 HTTP 메서드 |
| **URI** | 예 | `https://<storage-account-name>/<folder-name>/{name}` | 이 구문을 사용하는 Azure 글로벌(퍼블릭) 환경의 Azure Blob Storage 파일에 대한 리소스 ID |
| **헤더** | Azure Storage용 | `x-ms-blob-type` = `BlockBlob` <p>`x-ms-version` = `2019-02-02` <p>`x-ms-date` = `@{formatDateTime(utcNow(),'r')}` | Azure Storage 작업에 필요한 `x-ms-blob-type`, `x-ms-version` 및 `x-ms-date` 헤더 값입니다. <p><p>**중요**: Azure Storage에 대한 나가는 HTTP 트리거 및 작업 요청에서 헤더에는 실행하려는 작업에 대한 `x-ms-version` 속성 및 API 버전이 필요합니다. `x-ms-date`는 현재 날짜여야 합니다. 그렇지 않으면 `403 FORBIDDEN` 워크플로가 오류로 실패합니다. 현재 날짜를 필수 형식으로 가져오려면 예제 값의 식을 사용할 수 있습니다. <p>자세한 내용은 다음 항목을 참조하세요. <p><p>- [요청 헤더 - Blob 스냅샷 ](/rest/api/storageservices/snapshot-blob#request) <br>- [Azure Storage 서비스에 대한 버전 관리](/rest/api/storageservices/versioning-for-the-azure-storage-services#specifying-service-versions-in-requests) |
| **쿼리** | Blob 스냅샷 작업에만 해당 | `comp` = `snapshot` | 작업에 대한 쿼리 매개 변수 이름 및 값입니다. |
|||||

### <a name="consumption"></a>[Consumption](#tab/consumption)

다음 예제에서는 Blob 스냅샷 작업에 사용할 이전에 설명한 모든 속성 값이 있는 샘플 HTTP 동작을 보여줍니다.

![리소스에 액세스하도록 설정된 소비 논리 앱 워크플로 및 HTTP 작업이 있는 Azure Portal 보여주는 스크린샷](./media/create-managed-service-identity/http-action-example.png)

1. HTTP 작업을 추가한 후에는 HTTP 동작에 **인증** 속성을 추가합니다. **새 매개 변수 추가** 목록에서 **인증** 을 선택합니다.

   ![HTTP 작업 및 "인증" 속성이 선택된 "새 매개 변수 추가" 목록이 열려 있는 소비 워크플로를 보여주는 스크린샷.](./media/create-managed-service-identity/add-authentication-property.png)

   > [!NOTE]
   > 일부 트리거 및 작업만 인증 유형을 추가할 수 있도록 지원합니다. 자세한 내용은 [인증을 지원하는 트리거 및 작업에 대한 인증 유형을](logic-apps-securing-a-logic-app.md#authentication-types-supported-triggers-actions)검토하세요.

1. 인증 **유형** 목록에서 **관리 ID** 를 선택합니다.

   ![HTTP 작업 및 "관리 ID" 값이 선택된 "인증" 속성이 있는 소비 워크플로를 보여주는 스크린샷.](./media/create-managed-service-identity/select-managed-identity.png)

1. 관리 ID 목록에서 시나리오에 따라 사용 가능한 옵션 중에서 선택합니다.

   * 시스템 할당 ID를 설정하는 경우 아직 선택하지 않은 경우 **시스템 할당 관리 ID를** 선택합니다.

     ![HTTP 작업 및 "시스템 할당 관리 ID" 값이 선택되어 있는 "관리 ID" 속성이 있는 소비 워크플로를 보여주는 스크린샷](./media/create-managed-service-identity/select-system-assigned-identity.png)

   * 사용자가 할당한 ID를 설정하는 경우 해당 ID를 선택합니다(아직 선택하지 않은 경우).

     ![HTTP 작업 및 사용자 할당 ID가 선택된 "관리 ID" 속성이 있는 소비 워크플로를 보여주는 스크린샷](./media/create-managed-service-identity/select-user-assigned-identity-action.png)

   이 예제에서는 **시스템 할당 관리 ID 를 계속합니다.**

1. 일부 트리거 및 작업에서는 대상 리소스 ID를 설정하기 위한 **대상 그룹** 속성도 표시됩니다. **대상 그룹** 속성을 [대상 리소스 또는 서비스에 대한 리소스 ID](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)로 설정합니다. 그렇지 않으면 **대상 그룹** 속성에서 기본적으로 Azure Resource Manager에 대한 리소스 ID인 `https://management.azure.com/` 리소스 ID를 사용합니다.
  
    예를 들어 [전역 Azure 클라우드의 Key Vault 리소스](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-key-vault)에 대한 액세스를 인증하려면 **Audience** 속성을 정확히 다음 리소스 ID로 설정해야 합니다. `https://vault.azure.net`. 이 특정 리소스 ID에는 후행 슬래시가 ‘없습니다.’ 실제로 후행 슬래시를 포함하면 `400 Bad Request` 오류 또는 `401 Unauthorized` 오류가 발생할 수 있습니다.

   > [!IMPORTANT]
   > 대상 리소스 ID는 필수 후행 슬래시를 포함하여 Azure AD(Active Directory)에 필요한 값과 *정확히 일치* 해야 합니다. 예를 들어 모든 Azure Blob Storage 계정에 대한 리소스 ID에는 후행 슬래시가 필요합니다. 그러나 특정 스토리지 계정에 대한 리소스 ID에는 후행 슬래시가 필요하지 않습니다. [Azure AD를 지원하는 Azure 서비스에 대한 리소스 ID](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)를 확인하세요.

   다음 예에서는 인증에 사용되는 액세스 토큰이 모든 스토리지 계정에 유효하도록 **대상 그룹** 속성을 `https://storage.azure.com/`으로 설정합니다. 그러나 특정 스토리지 계정에 대한 루트 서비스 URL(`https://<your-storage-account>.blob.core.windows.net`)도 지정할 수 있습니다.

   ![HTTP 작업 및 대상 리소스 ID로 설정된 "대상" 속성이 있는 소비 워크플로를 보여주는 스크린샷.](./media/create-managed-service-identity/specify-audience-url-target-resource.png)

   Azure Storage 위해 Azure AD를 통해 액세스 권한을 부여하는 자세한 내용은 다음 설명서를 검토하세요.

   * [Azure Active Directory를 사용하여 Azure Blob 및 큐에 대한 액세스 권한 부여](../storage/blobs/authorize-access-azure-active-directory.md)

   * [Azure Active Directory를 사용하여 Azure Storage에 대한 액세스 권한 부여](/rest/api/storageservices/authorize-with-azure-active-directory#use-oauth-access-tokens-for-authentication)

1. 워크플로를 원하는 방식으로 계속 빌드합니다.

### <a name="standard"></a>[Standard](#tab/standard)

다음 예제에서는 Blob 스냅샷 작업에 사용할 이전에 설명한 모든 속성 값이 있는 샘플 HTTP 동작을 보여줍니다.

![표준 논리 앱 워크플로 및 리소스에 액세스하도록 설정된 HTTP 작업이 있는 Azure Portal 보여주는 스크린샷](./media/create-managed-service-identity/http-action-example-standard.png)

1. HTTP 작업을 추가한 후에는 HTTP 동작에 **인증** 속성을 추가합니다. **새 매개 변수 추가** 목록에서 **인증** 을 선택합니다.

   ![HTTP 작업 및 "인증" 속성이 선택된 "새 매개 변수 추가" 목록이 열려 있는 표준 워크플로를 보여주는 스크린샷.](./media/create-managed-service-identity/add-authentication-property-standard.png)

   > [!NOTE]
   > 일부 트리거 및 작업만 인증 유형을 추가할 수 있도록 지원합니다. 자세한 내용은 [인증을 지원하는 트리거 및 작업에 대한 인증 유형을](logic-apps-securing-a-logic-app.md#authentication-types-supported-triggers-actions)검토하세요.

1. 인증 **유형** 목록에서 **관리 ID** 를 선택합니다.

   ![HTTP 작업 및 "관리 ID" 값이 선택된 "인증" 속성이 있는 표준 워크플로를 보여주는 스크린샷.](./media/create-managed-service-identity/select-managed-identity-standard.png)

1. 관리 ID 목록에서 시스템 **할당 관리 ID를** 선택합니다(아직 선택하지 않은 경우).

     ![HTTP 작업 및 "시스템 할당 관리 ID"가 선택된 "관리 ID" 목록이 열려 있는 표준 워크플로를 보여주는 스크린샷.](./media/create-managed-service-identity/select-system-assigned-identity-standard.png)

1. 일부 트리거 및 작업에서는 대상 리소스 ID를 설정하기 위한 **대상 그룹** 속성도 표시됩니다. **대상 그룹** 속성을 [대상 리소스 또는 서비스에 대한 리소스 ID](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)로 설정합니다. 그렇지 않으면 **대상 그룹** 속성에서 기본적으로 Azure Resource Manager에 대한 리소스 ID인 `https://management.azure.com/` 리소스 ID를 사용합니다.
  
    예를 들어 [전역 Azure 클라우드의 Key Vault 리소스](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-key-vault)에 대한 액세스를 인증하려면 **Audience** 속성을 정확히 다음 리소스 ID로 설정해야 합니다. `https://vault.azure.net`. 이 특정 리소스 ID에는 후행 슬래시가 ‘없습니다.’ 실제로 후행 슬래시를 포함하면 `400 Bad Request` 오류 또는 `401 Unauthorized` 오류가 발생할 수 있습니다.

   > [!IMPORTANT]
   > 대상 리소스 ID는 필수 후행 슬래시를 포함하여 Azure AD(Active Directory)에 필요한 값과 *정확히 일치* 해야 합니다. 예를 들어 모든 Azure Blob Storage 계정에 대한 리소스 ID에는 후행 슬래시가 필요합니다. 그러나 특정 스토리지 계정에 대한 리소스 ID에는 후행 슬래시가 필요하지 않습니다. [Azure AD를 지원하는 Azure 서비스에 대한 리소스 ID](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)를 확인하세요.

   다음 예에서는 인증에 사용되는 액세스 토큰이 모든 스토리지 계정에 유효하도록 **대상 그룹** 속성을 `https://storage.azure.com/`으로 설정합니다. 그러나 특정 스토리지 계정에 대한 루트 서비스 URL(`https://<your-storage-account>.blob.core.windows.net`)도 지정할 수 있습니다.

   !["대상 그룹" 속성을 대상 리소스 ID로 설정](./media/create-managed-service-identity/specify-audience-url-target-resource-standard.png)

   Azure Storage 위해 Azure AD를 통해 액세스 권한을 부여하는 자세한 내용은 다음 설명서를 검토하세요.

   * [Azure Active Directory를 사용하여 Azure Blob 및 큐에 대한 액세스 권한 부여](../storage/blobs/authorize-access-azure-active-directory.md)
   * [Azure Active Directory를 사용하여 Azure Storage에 대한 액세스 권한 부여](/rest/api/storageservices/authorize-with-azure-active-directory#use-oauth-access-tokens-for-authentication)

1. 워크플로를 원하는 방식으로 계속 빌드합니다.

---

<a name="authenticate-managed-connector-managed-identity"></a>

## <a name="example-authenticate-managed-connector-trigger-or-action-with-a-managed-identity"></a>예: 관리 ID로 관리형 커넥터 트리거 또는 작업 인증

Azure Resource Manager 관리 커넥터에는 논리 앱 **리소스에서** 사용하도록 설정한 관리 ID를 사용할 수 있는 리소스 읽기 작업이 있습니다. 이 예에서는 시스템이 할당한 관리 ID를 사용하는 방법을 보여 줍니다.

### <a name="consumption"></a>[Consumption](#tab/consumption)

1. 워크플로에 작업을 추가하고 Azure AD 테넌트 를 선택한 후 **관리 ID가 있는 커넥트(미리 보기)** 를 선택합니다.

   ![Azure Resource Manager 작업 및 "관리 ID가 있는 커넥트"이 선택된 스크린샷](./media/create-managed-service-identity/select-connect-managed-identity-consumption.png)

1. 연결 이름 페이지에서 연결의 이름을 입력하고 사용할 관리 ID를 선택합니다.

   Azure Resource Manager 작업은 단일 인증 작업이므로 연결 정보 창에는 논리 앱 리소스에서 현재 사용하도록 설정된 **관리 ID를** 자동으로 선택하는 관리 ID 목록이 표시됩니다. 시스템 할당 관리 ID를 사용하도록 설정한 경우 **관리 ID** 목록에서 시스템 할당 관리 **ID 를** 선택합니다. 대신 사용자 할당 관리 ID를 사용하도록 설정한 경우 목록에서 해당 ID를 대신 선택합니다.

   Azure Blob Storage 같은 다중 인증 트리거 또는 작업을 사용하는 경우 연결 정보 창에는 다른 인증 유형 간에 **Logic Apps 관리 ID** 옵션이 포함된 인증 **유형** 목록이 표시됩니다.

   이 예제에서는 **시스템 할당 관리 ID만** 선택할 수 있습니다.

   ![연결 이름이 입력되고 "시스템 할당 관리 ID"가 선택된 Azure Resource Manager 동작을 보여주는 스크린샷](./media/create-managed-service-identity/single-system-identity-consumption.png)

   > [!NOTE]
   > 연결을 만들거나, 연결을 변경하거나, 관리 ID 사용 연결이 여전히 존재하는 동안 관리 ID를 사용하도록 설정하지 않았거나, 제거된 경우 ID를 사용하도록 설정하고 대상 리소스에 대한 액세스 권한을 부여해야 한다는 오류가 표시됩니다.

1. 준비가 되면 **만들기** 를 선택합니다.

1. 성공적으로 연결한 후 디자이너는 관리 ID 인증을 사용하여 동적 값, 콘텐츠 또는 스키마를 가져올 수 있습니다.

1. 워크플로를 원하는 방식으로 계속 빌드합니다.

### <a name="standard"></a>[Standard](#tab/standard)

1. 워크플로에 작업을 추가한 후 작업의 연결 **만들기** 창에서 Azure AD 테넌트, **관리 ID가 있는 커넥트(미리 보기)** 를 선택합니다.

   ![Azure Resource Manager 작업 및 "관리 ID가 있는 커넥트"이 선택된 스크린샷](./media/create-managed-service-identity/select-connect-managed-identity-standard.png)

1. 연결 이름 페이지에서 연결의 이름을 제공합니다.

   Azure Resource Manager 작업은 단일 인증 작업이므로 연결 정보 창에는 논리 앱 리소스에서 현재 사용하도록 설정된 **관리 ID를** 자동으로 선택하는 관리 ID 목록이 표시됩니다. 시스템 할당 관리 ID를 사용하도록 설정한 경우 **관리 ID** 목록에서 시스템 할당 관리 **ID 를** 선택합니다. 대신 사용자 할당 관리 ID를 사용하도록 설정한 경우 목록에서 해당 ID를 대신 선택합니다.

   이 예제에서는 **시스템 할당 관리 ID만** 선택할 수 있습니다.

   ![연결 이름이 입력되고 "시스템 할당 관리 ID"가 선택된 Azure Resource Manager 동작을 보여주는 스크린샷](./media/create-managed-service-identity/single-system-identity-standard.png)

   Azure Blob Storage 같은 다중 인증 트리거 또는 작업을 사용하는 경우 연결 정보 창에는 다른 인증 유형 간에 **관리 ID** 옵션이 포함된 **인증 유형** 목록이 표시됩니다.

   > [!NOTE]
   > 연결을 만들거나, 연결을 변경하거나, 관리 ID 사용 연결이 여전히 존재하는 동안 관리 ID를 사용하도록 설정하지 않았거나, 제거된 경우 ID를 사용하도록 설정하고 대상 리소스에 대한 액세스 권한을 부여해야 한다는 오류가 표시됩니다.

1. 준비가 되면 **만들기** 를 선택합니다.

1. 성공적으로 연결한 후 디자이너는 관리 ID 인증을 사용하여 동적 값, 콘텐츠 또는 스키마를 가져올 수 있습니다.

1. 워크플로를 원하는 방식으로 계속 빌드합니다.

---

<a name="logic-app-resource-definition-connection-managed-identity"></a>

## <a name="logic-app-resource-definition-and-connections-that-use-a-managed-identity-consumption"></a>관리 ID를 사용하는 논리 앱 리소스 정의 및 연결(소비)

관리 ID를 사용하도록 설정하고 사용하는 연결은 관리 ID로만 작동하는 특수 연결 형식입니다. 런타임에 연결은 논리 앱 리소스에서 사용하도록 설정된 관리 ID를 사용합니다. 런타임에 Azure Logic Apps 서비스는 논리 앱 워크플로의 관리 커넥터 트리거 및 작업이 관리 ID를 사용하도록 설정되었는지 여부와 트리거 및 작업에 지정된 대상 리소스에 액세스하기 위해 관리 ID를 사용하도록 필요한 모든 권한이 설정되었는지 확인합니다. 성공하면 Azure Logic Apps 관리 ID와 연결된 Azure AD 토큰을 검색하고 해당 ID를 사용하여 대상 리소스에 대한 액세스를 인증하고 트리거 및 작업에서 구성된 작업을 수행합니다.

### <a name="consumption"></a>[Consumption](#tab/consumption)

논리 **앱(소비)** 리소스에서 연결 구성은 논리 앱 리소스 정의의 `parameters` 개체에 저장됩니다. 이 `$connections` 개체에는 사용자 할당 ID를 사용하는 경우 ID의 리소스 ID와 함께 연결의 리소스 ID에 대한 포인터를 포함하는 개체가 포함됩니다.

이 예제에서는 논리 앱에서 시스템이 할당한 관리 ID를 사용하도록 설정할 때의 구성을 보여 줍니다.

```json
"parameters": {
   "$connections": {
      "value": {
         "<action-name>": {
            "connectionId": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{resource-group-name}/providers/Microsoft.Web/connections/{connection-name}",
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
            "connectionId": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{resource-group-name}/providers/Microsoft.Web/connections/{connection-name}",
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

### <a name="standard"></a>[Standard](#tab/standard)

논리 **앱(표준)** 리소스에서 연결 구성은 `connections.json` `managedApiConnections` 워크플로에서 사용되는 관리되는 각 커넥터에 대한 연결 구성 정보를 포함하는 JSON 개체를 포함하는 논리 앱 리소스 또는 프로젝트의 파일에 저장됩니다. 예를 들어 이 연결 정보에는 사용자 할당 ID를 사용하는 경우 리소스 ID와 같은 관리 ID 속성과 함께 연결의 리소스 ID에 대한 포인터가 포함됩니다.

이 예제에서는 논리 앱이 사용자 할당 관리 ID를 사용하도록 설정할 때 구성이 어떻게 표시되는지 보여 줍니다.

```json
{
    "managedApiConnections": {
        "<connector-name>": {
            "api": {
                "id": "/subscriptions/{Azure-subscription-ID}/providers/Microsoft.Web/locations/{region}/managedApis/<connector-name>"
            },
            "connection": {
                "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{resource-group-name}/providers/Microsoft.Web/connections/<connection-name>"
            },
            "connectionRuntimeUrl": <connection-URL>,
            "authentication": { // Authentication with APIHub
                "type": "ManagedServiceIdentity"
            },
            "connectionProperties": {
                "authentication": { //Authentication with the target resource
                    "type": "ManagedServiceIdentity",
                    "identity": "<user-assigned-identity>", // Optional
                    "audience": "<resource-URL>"
                }
            }
        }
    }
}
```

---

<a name="arm-templates-connection-resource-managed-identity"></a>

## <a name="arm-template-for-managed-connections-and-managed-identities-consumption"></a>관리되는 연결 및 관리 ID용 ARM 템플릿(소비)

ARM 템플릿을 사용하여 배포를 자동화하고 논리 앱 워크플로에 관리되는 커넥터 트리거 또는 관리 ID를 사용하는 작업이 포함된 경우 기본 연결 리소스 정의에 속성 값으로 가 있는 속성이 포함되어 있는지 `parameterValueType` `Alternative` 확인합니다. 그렇지 않은 경우 ARM 배포는 인증을 위해 관리 ID를 사용하도록 연결을 설정하지 않으며 논리 앱의 워크플로에서 연결이 작동하지 않습니다. 이 요구 사항은 [ **관리 ID로 연결** 옵션](#authenticate-managed-connector-managed-identity)을 선택한 [특정 관리형 커넥터 트리거 및 작업](#triggers-actions-managed-identity)에만 적용됩니다.

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

인증에 관리 ID 사용을 중지하려면 먼저 [대상 리소스 에 대한 ID의 액세스를 제거합니다.](#disable-identity-target-resource) 다음으로 논리 앱 [리소스에서 시스템 할당 ID를 해제하거나 사용자 할당 ID 를 제거합니다.](#disable-identity-logic-app)

논리 앱 리소스에서 관리 ID를 사용하지 않도록 설정하면 해당 ID가 액세스 권한이 있는 Azure 리소스에 대한 액세스를 요청하는 기능을 제거합니다.

> [!NOTE]
> 시스템 할당 ID를 사용하지 않도록 설정하면 즉시 ID를 다시 사용하도록 설정하더라도 해당 논리 앱 워크플로의 워크플로에서 사용되는 모든 연결이 런타임에 작동하지 않습니다. 이 동작은 ID를 사용하지 않도록 하면 개체 ID가 삭제되므로 발생합니다. ID를 사용하도록 설정할 때마다 Azure는 서로 다른 고유한 개체 ID를 사용하여 ID를 생성합니다. 이 문제를 해결하려면 현재 시스템 할당 ID에 대한 현재 개체 ID를 사용하도록 연결을 다시 만들어야 합니다.
>
> 시스템 할당 ID를 최대한 사용하지 않도록 사용하지 않도록 하세요. Azure 리소스에 대한 ID의 액세스를 제거하려면 대상 리소스에서 ID의 역할 할당을 제거합니다. 논리 앱 리소스를 삭제하면 Azure는 Azure AD에서 관리 ID를 자동으로 제거합니다.

이 섹션의 단계에서는 [ARM](#azure-portal-disable) [템플릿(Azure Portal](#template-disable)및 Azure Resource Manager 템플릿) 사용을 다룹니다. Azure PowerShell, Azure CLI 및 Azure REST API 다음 설명서를 검토하세요.

| 도구 | 설명서 |
|------|---------------|
| Azure PowerShell | 1. 역할 할당 을 [제거합니다.](../role-based-access-control/role-assignments-powershell.md) <br>2. [사용자 할당 ID 를 삭제합니다.](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md) |
| Azure CLI | 1. 역할 할당 을 [제거합니다.](../role-based-access-control/role-assignments-cli.md) <br>2. [사용자 할당 ID 를 삭제합니다.](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md) |
| Azure REST API | 1. 역할 할당 을 [제거합니다.](../role-based-access-control/role-assignments-rest.md) <br>2. [사용자 할당 ID 를 삭제합니다.](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-rest.md) |
|||

<a name="azure-portal-disable"></a>

### <a name="disable-managed-identity-in-the-azure-portal"></a>Azure Portal에서 관리 ID 사용 안 함

관리 ID에 대한 액세스를 제거하려면 대상 리소스에서 ID의 역할 할당을 제거한 다음 관리 ID를 사용하지 않도록 설정합니다.

<a name="disable-identity-target-resource"></a>

#### <a name="remove-role-assignment"></a>역할 할당 제거

다음 단계에서는 관리 ID에서 대상 리소스에 대한 액세스를 제거합니다.

1. [Azure Portal](https://portal.azure.com)에서 관리 ID에 대한 액세스를 제거하려는 대상 Azure 리소스로 이동합니다.

1. 대상 리소스의 메뉴에서 **액세스 제어(IAM)** 를 선택합니다. 도구 모음 아래에서 **역할 할당** 을 선택합니다.

1. 역할 목록에서 제거하려는 관리 ID를 선택합니다. 도구 모음에서 **제거** 를 선택합니다.

   > [!TIP]
   > **제거** 옵션을 사용하지 않도록 설정되면 대부분 권한이 없을 가능성이 높습니다. 리소스에 대한 역할을 관리할 수 있는 권한에 대한 자세한 내용은 [Azure Active Directory 관리자 역할 권한을 검토하세요.](../active-directory/roles/permissions-reference.md)

<a name="disable-identity-logic-app"></a>

#### <a name="disable-managed-identity-on-logic-app-resource"></a>논리 앱 리소스에서 관리 ID 사용 안 함

1. [Azure Portal](https://portal.azure.com)에서 논리 앱 리소스를 엽니다.

1. 논리 앱 탐색 메뉴의 **설정** 아래에서 **id** 를 선택 하 고 id에 대 한 단계를 따릅니다.

   * **시스템 할당 항목** > **켜기** > **저장** 을 차례로 선택합니다. Azure에서 확인하라는 메시지가 표시되면 **예** 를 선택합니다.

   * **사용자 할당 항목** 및 관리 ID를 선택한 다음, **제거** 를 선택합니다. Azure에서 확인하라는 메시지가 표시되면 **예** 를 선택합니다.

<a name="template-disable"></a>

### <a name="disable-managed-identity-in-an-arm-template"></a>ARM 템플릿에서 관리 ID 사용 안 함

ARM 템플릿을 사용하여 논리 앱의 관리 ID를 만든 경우 `identity` 개체의 `type` 자식 속성을 `None`으로 설정합니다.

```json
"identity": {
   "type": "None"
}
```

## <a name="next-steps"></a>다음 단계

* [Azure Logic Apps에서 액세스 및 데이터 보안](logic-apps-securing-a-logic-app.md)
