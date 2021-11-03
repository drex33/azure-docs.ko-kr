---
title: 매핑을 위해 Azure Active Directory에 특성 동기화
description: Azure Active Directory 및 SaaS 앱을 사용하여 사용자 프로비저닝을 구성하는 경우 디렉터리 확장 기능을 사용하여 기본값으로 동기화되지 않은 원본 특성을 추가합니다.
services: active-directory
author: kenwith
manager: karenh444
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: troubleshooting
ms.date: 08/24/2021
ms.author: kenwith
ms.reviewer: arvinh
ms.openlocfilehash: 207936bd4b8fe29cfa15cd284b8ebba1e6a12164
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131057536"
---
# <a name="syncing-extension-attributes-for-azure-active-directory-application-provisioning"></a>Azure Active Directory 애플리케이션 프로비저닝을 위한 확장 특성 동기화

Azure AD(Azure Active Directory)에서 사용자 계정을 [SaaS 앱](../saas-apps/tutorial-list.md)으로 프로비저닝할 때 사용자 프로필을 만드는 데 필요한 모든 데이터(특성)를 Azure AD에 포함해야 합니다. 사용자 프로비저닝을 위해 특성 매핑을 사용자 지정할 때 매핑하려는 특성이 **원본 특성** 목록에 나타나지 않을 수 있습니다. 이 문서에서는 누락된 특성을 추가하는 방법을 보여 줍니다.

Azure AD에만 사용되는 사용자의 경우 [PowerShell 또는 Microsoft Graph를 사용하여 스키마 확장을 생성](#create-an-extension-attribute-on-a-cloud-only-user)할 수 있습니다.

온-프레미스 Active Directory 사용자의 경우 사용자를 Azure AD에 동기화해야 합니다. [Azure AD Connect](../hybrid/whatis-azure-ad-connect.md)를 사용하여 사용자와 특성을 동기화할 수 있습니다. Azure AD Connect는 특정 특성을 Azure AD에 자동으로 동기화하지만 모든 특성을 자동으로 동기화하지는 않습니다. 또한 기본값으로 동기화되는 일부 특성(예: SAMAccountName)은 Azure AD Graph API를 사용하여 노출되지 않을 수 있습니다. 이러한 경우 [Azure AD Connect 디렉터리 확장 기능을 사용하여 특성을 Azure AD와 동기화](#create-an-extension-attribute-using-azure-ad-connect)할 수 있습니다. 이렇게 하면 특성이 Azure AD Graph API 및 Azure AD 프로비저닝 서비스에 표시됩니다.

## <a name="create-an-extension-attribute-on-a-cloud-only-user"></a>클라우드 전용 사용자의 확장 특성 만들기
Microsoft Graph 및 PowerShell을 사용하여 Azure AD의 사용자를 위한 사용자 스키마를 확장할 수 있습니다. 확장 특성은 대부분의 경우 자동으로 검색됩니다.

서비스 주체가 1000개를 초과하는 경우 원본 특성 목록에 누락된 확장이 있을 수 있습니다. 만들었던 특성이 자동으로 표시되지 않으면 해당 특성이 만들어졌는지 확인하고 스키마에 수동으로 추가합니다. Microsoft Graph 및 [Graph 탐색기](/graph/graph-explorer/graph-explorer-overview)를 사용하여 만들어졌는지 확인합니다. 스키마에 수동으로 추가하려면 [지원되는 특성 목록 편집](customize-application-attributes.md#editing-the-list-of-supported-attributes)을 참조하세요.

### <a name="create-an-extension-attribute-on-a-cloud-only-user-using-microsoft-graph"></a>Microsoft Graph를 사용하여 클라우드 전용 사용자의 확장 특성 만들기
[Microsoft Graph](/graph/overview)를 사용하여 Azure AD 사용자의 스키마를 확장할 수 있습니다. 

먼저, 작업 중인 앱의 ID를 가져올 수 있도록 테넌트의 앱을 나열합니다. 자세한 정보는 [extensionProperties 나열](/graph/api/application-list-extensionproperty?view=graph-rest-1.0&tabs=http&preserve-view=true)을 참조하세요.

```json
GET https://graph.microsoft.com/v1.0/applications
```

다음으로 확장 특성을 만듭니다. 아래 **ID** 속성을 이전 단계에서 검색된 **ID** 로 바꿉니다. “appId”가 아닌  **“ID”** 특성을 사용 해야 합니다. 자세한 내용은 [Create extensionProperty]/graph/api/application-post-extensionproperty.md?view=graph-rest-1.0&tabs=http&preserve-view=true)를 참조하세요.

```json
POST https://graph.microsoft.com/v1.0/applications/{id}/extensionProperties
Content-type: application/json

{
    "name": "extensionName",
    "dataType": "string",
    "targetObjects": [
        "User"
    ]
}
```

이전 요청은 `extension_appID_extensionName` 형식으로 확장 특성을 만들었습니다. 이제 이 확장 특성으로 사용자를 업데이트할 수 있습니다. 자세한 내용은 [사용자 업데이트](/graph/api/user-update?view=graph-rest-1.0&tabs=http&preserve-view=true)를 참조하세요.
```json
PATCH https://graph.microsoft.com/v1.0/users/{id}
Content-type: application/json

{
  "extension_inputAppId_extensionName": "extensionValue"
}
```
마지막으로 사용자의 특성을 확인합니다. 자세한 내용은 [사용자 가져오기](/graph/api/user-get?view=graph-rest-1.0&tabs=http#example-3-users-request-using-select&preserve-view=true)를 참조하세요.

```json
GET https://graph.microsoft.com/v1.0/users/{id}?$select=displayName,extension_inputAppId_extensionName
```


### <a name="create-an-extension-attribute-on-a-cloud-only-user-using-powershell"></a>PowerShell을 사용하여 클라우드 전용 사용자의 확장 특성 만들기
PowerShell을 사용하여 사용자 지정 확장을 만들고 사용자에 값을 할당합니다. 

```
#Connect to your Azure AD tenant   
Connect-AzureAD

#Create an application (you can instead use an existing application if you would like)
$App = New-AzureADApplication -DisplayName “test app name” -IdentifierUris https://testapp

#Create a service principal
New-AzureADServicePrincipal -AppId $App.AppId

#Create an extension property
New-AzureADApplicationExtensionProperty -ObjectId $App.ObjectId -Name “TestAttributeName” -DataType “String” -TargetObjects “User”

#List users in your tenant to determine the objectid for your user
Get-AzureADUser

#Set a value for the extension property on the user. Replace the objectid with the ID of the user and the extension name with the value from the previous step
Set-AzureADUserExtension -objectid 0ccf8df6-62f1-4175-9e55-73da9e742690 -ExtensionName “extension_6552753978624005a48638a778921fan3_TestAttributeName”

#Verify that the attribute was added correctly.
Get-AzureADUser -ObjectId 0ccf8df6-62f1-4175-9e55-73da9e742690 | Select -ExpandProperty ExtensionProperty

```

## <a name="create-an-extension-attribute-using-azure-ad-connect"></a>Azure AD Connect를 사용하여 확장 특성 만들기

1. Azure AD Connect 마법사를 열고 작업을 선택한 다음 **동기화 옵션 사용자 지정** 을 선택합니다.

   ![Azure Active Directory Connect 마법사 추가 작업 페이지](./media/user-provisioning-sync-attributes-for-mapping/active-directory-connect-customize.png)
 
2. Azure AD 전역 관리자 권한으로 로그인합니다. 

3. **선택적 기능** 페이지에서 **디렉터리 확장 특성 동기화** 를 선택합니다.
 
   ![Azure Active Directory Connect 마법사 선택적 기능 페이지](./media/user-provisioning-sync-attributes-for-mapping/active-directory-connect-directory-extension-attribute-sync.png)

4. Azure AD로 확장하려는 특성을 선택합니다.
   > [!NOTE]
   > **사용 가능한 특성** 에서 검색은 대/소문자를 구분합니다.

   ![“디렉터리 확장” 선택 페이지를 보여 주는 스크린샷](./media/user-provisioning-sync-attributes-for-mapping/active-directory-connect-directory-extensions.png)

5. Azure AD Connect 마법사를 종료하고 전체 동기화 주기를 실행하도록 허용합니다. 주기가 완료되면 스키마가 확장되고 새 값이 온-프레미스 AD와 Azure AD 간에 동기화됩니다.
 
6. Azure Portal에서 [사용자 특성 매핑을 편집](customize-application-attributes.md)하는 동안 **원본 특성** 목록에는 이제 추가된 특성이 `<attributename> (extension_<appID>_<attributename>)` 형식으로 포함됩니다. 특성을 선택하고 프로비저닝할 대상 애플리케이션에 매핑합니다.

   ![Azure Active Directory Connect 마법사 디렉터리 확장 선택 페이지](./media/user-provisioning-sync-attributes-for-mapping/attribute-mapping-extensions.png)

> [!NOTE]
> **managedby** 또는 **DN/DistinguishedName** 과 같은 온-프레미스 AD에서 참조 특성을 프로비저닝하는 기능은 현재 지원되지 않습니다. [사용자 의견](https://feedback.azure.com/d365community/forum/22920db1-ad25-ec11-b6e6-000d3a4f0789)에서 이 기능을 요청할 수 있습니다. 


## <a name="next-steps"></a>다음 단계

* [프로비저닝 범위에 있는 사용자 정의](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)
