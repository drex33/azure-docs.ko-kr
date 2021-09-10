---
title: 앱 역할 추가 및 토큰에서 가져오기 | Azure
titleSuffix: Microsoft identity platform
description: Azure Active Directory에 등록된 애플리케이션에서 앱 역할을 추가하고 이 역할에 사용자와 그룹을 할당하여 토큰의 ‘역할’ 클레임으로 받는 방법을 알아봅니다.
services: active-directory
author: kalyankrishna1
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: how-to
ms.date: 05/06/2021
ms.author: kkrishna
ms.reviewer: marsma, kkrishna, jmprieur
ms.custom: aaddev
ms.openlocfilehash: 5f213c6b41a0a34850b35fb24dd4288c00693511
ms.sourcegitcommit: 03f0db2e8d91219cf88852c1e500ae86552d8249
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123039339"
---
# <a name="add-app-roles-to-your-application-and-receive-them-in-the-token"></a>애플리케이션에서 앱 역할을 추가하고 토큰에서 수신

RBAC(역할 기반 액세스 제어)는 애플리케이션에서 권한 부여를 적용하는 데 널리 사용되는 메커니즘입니다. RBAC를 사용하는 경우, 관리자는 개별 사용자나 그룹이 아닌 역할에 사용 권한을 부여합니다. 그런 다음 관리자는 다른 사용자 및 그룹에 역할을 할당하여 누가 어떤 컨텐츠와 기능에 액세스 권한을 갖는지를 제어할 수 있습니다.

애플리케이션 역할 및 역할 클레임과 함께 RBAC를 사용하면 개발자는 적은 노력으로 자신의 앱에서 권한 부여를 안전하게 적용할 수 있습니다.

또 다른 접근 방식은 GitHub의 [active-directory-aspnetcore-webapp-openidconnect-v2](https://aka.ms/groupssample) 코드 샘플에 표시된 것처럼 Azure AD 그룹 및 그룹 클레임을 사용하는 것입니다. Azure AD 그룹 및 애플리케이션 역할은 상호 배타적이지 않습니다. 보다 세분화된 액세스 제어를 제공하기 위해 동시에 사용할 수 있습니다.

## <a name="declare-roles-for-an-application"></a>애플리케이션에 대한 역할 선언

[Azure Portal](https://portal.azure.com)을 사용하여 앱 역할을 정의합니다. 앱 역할은 일반적으로 서비스, 앱 또는 API를 나타내는 애플리케이션 등록에 정의됩니다. 사용자가 애플리케이션에 로그인하면 Azure AD는 사용자 또는 서비스 주체가 사용자에게 개별적으로 부여한 역할 및 자신의 그룹 멤버 자격에서 부여한 각각의 역할에 대해 `roles` 클레임을 발행합니다. 이를 통해 클레임 기반 권한 부여를 구현할 수 있습니다. 앱 역할을 [사용자 또는 사용자 그룹](../manage-apps/add-application-portal-assign-users.md#assign-users-to-an-app)에 할당할 수 있습니다. 또한 앱 역할을 다른 애플리케이션의 서비스 주체 또는 [관리 ID의 서비스 주체](../managed-identities-azure-resources/how-to-assign-app-role-managed-identity-powershell.md)에 할당할 수도 있습니다.

> [!IMPORTANT]
> 현재까지 그룹에 서비스 주체를 추가한 후 이 그룹에 앱 역할을 할당하는 경우 Azure AD는 발행하는 토큰에 `roles` 클레임을 추가하지 않습니다.

Azure Portal을 사용하여 앱 역할을 선언하는 방법에는 두 가지가 있습니다.

- [앱 역할 UI](#app-roles-ui)
- [앱 매니페스트 편집기](#app-manifest-editor)

추가하는 역할 수는 Azure Active Directory에 적용된 애플리케이션 매니페스트 제한에 따라 계산됩니다. 이러한 제한에 대한 자세한 내용은 [Azure Active Directory 앱 매니페스트 참조](reference-app-manifest.md)의 [매니페스트 제한](./reference-app-manifest.md#manifest-limits) 섹션을 참조하세요.

### <a name="app-roles-ui"></a>앱 역할 UI

Azure Portal의 사용자 인터페이스를 사용하여 앱 역할을 만들려면 다음을 수행합니다.

1. <a href="https://portal.azure.com/" target="_blank">Azure Portal</a>에 로그인합니다.
1. 상단 메뉴에서 **디렉터리 + 구독** 필터를 선택한 후 앱 역할을 추가하려는 앱 등록이 포함된 Azure Active Directory 테넌트를 선택합니다.
1. **Azure Active Directory** 를 검색하고 선택합니다.
1. **관리** 에서 **앱 등록** 을 선택한 후 앱 역할을 정의하려는 애플리케이션을 선택합니다.
1. **앱 역할** 을 선택한 다음, **앱 역할 만들기** 를 선택합니다.

   :::image type="content" source="media/howto-add-app-roles-in-azure-ad-apps/app-roles-overview-pane.png" alt-text="Azure Portal의 앱 등록의 앱 역할 창":::

1. **앱 역할 만들기** 창에서 역할에 대한 설정을 입력합니다. 이미지 다음 표는 각 설정 및 해당 매개 변수를 설명합니다.

   :::image type="content" source="media/howto-add-app-roles-in-azure-ad-apps/app-roles-create-context-pane.png" alt-text="Azure Portal의 앱 등록의 앱 역할 만들기 컨텍스트 창":::

   | 필드                                    | 설명                                                                                                                                                                                                                                                                                                       | 예제                       |
   | ---------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------- |
   | **표시 이름**                         | 관리자 승인 및 앱 할당 환경에 표시되는 앱 역할의 표시 이름입니다. 이 값에는 공백이 포함될 수 있습니다.                                                                                                                                                                                    | `Survey Writer`               |
   | **허용된 멤버 유형**                 | 이 앱 역할을 사용자, 애플리케이션 또는 둘 다에 할당할 수 있는지 여부를 지정합니다.<br/><br/>`applications`에 제공되는 경우 앱 등록의 **관리** 섹션 > **API 권한 > 권한 추가 > 내 API > API 선택 > 애플리케이션 권한** 에서 앱 역할이 애플리케이션 권한으로 표시됩니다. | `Users/Groups`                |
   | **값**                                | 애플리케이션이 토큰에서 예상하는 역할 클레임의 값을 지정합니다. 값은 애플리케이션 코드에 참조된 문자열과 정확하게 일치해야 합니다. 값은 공백을 포함할 수 없습니다.                                                                                                          | `Survey.Create`               |
   | **설명**                          | 관리 앱 할당 및 동의 환경 중에 표시되는 앱 역할에 대한 자세한 설명입니다.                                                                                                                                                                                                        | `Writers can create surveys.` |
   | **이 앱 역할을 사용하도록 설정하시겠습니까?** | 앱 역할 사용 여부를 지정합니다. 앱 역할을 삭제하려면 삭제 작업을 시도하기 전 이 확인란을 선택 취소하고 변경 사항을 적용합니다.                                                                                                                                                             | _선택_                     |

1. **적용** 을 선택하여 변경 내용을 저장합니다.

### <a name="app-manifest-editor"></a>앱 매니페스트 편집기

매니페스트를 직접 편집하여 역할을 추가하려면 다음을 수행하세요.

1. <a href="https://portal.azure.com/" target="_blank">Azure Portal</a>에 로그인합니다.
1. 상단 메뉴에서 **디렉터리 + 구독** 필터를 선택한 후 앱 역할을 추가하려는 앱 등록이 포함된 Azure Active Directory 테넌트를 선택합니다.
1. **Azure Active Directory** 를 검색하고 선택합니다.
1. **관리** 에서 **앱 등록** 을 선택한 후 앱 역할을 정의하려는 애플리케이션을 선택합니다.
1. **관리** 에서 **매니페스트** 를 선택합니다.
1. `appRoles` 설정을 찾고 모든 애플리케이션 역할을 추가하여 앱 매니페스트를 편집합니다. `users`, `applications` 또는 둘 다를 대상으로 하는 앱 역할을 정의할 수 있습니다. 다음 JSON 코드 조각은 두 예제를 모두 보여줍니다.
1. 매니페스트를 저장합니다.

매니페스트의 각 앱 역할 정의에는 해당 `id` 값으로 고유한 GUID가 포함되어야 합니다.

각 앱 역할 정의의 `value` 속성은 애플리케이션의 코드에 사용되는 문자열과 정확히 일치해야 합니다. `value` 속성은 공백을 포함할 수 없습니다. 이 경우 매니페스트를 저장하면 오류가 표시됩니다.

#### <a name="example-user-app-role"></a>예제: 사용자 앱 역할

이 예제에서는 `User`에 할당할 수 있는 `Writer`라는 앱 역할을 정의합니다.

```json
"appId": "8763f1c4-0000-0000-0000-158e9ef97d6a",
"appRoles": [
    {
      "allowedMemberTypes": [
        "User"
      ],
      "displayName": "Writer",
      "id": "d1c2ade8-0000-0000-0000-6d06b947c66f",
      "isEnabled": true,
      "description": "Writers Have the ability to create tasks.",
      "value": "Writer"
    }
  ],
"availableToOtherTenants": false,
```

#### <a name="example-application-app-role"></a>예제: 애플리케이션 앱 역할

`applications`에 제공되는 경우 앱 등록의 **관리** 섹션 > **API 권한 > 권한 추가 > 내 API > API 선택 > 애플리케이션 권한** 에서 앱 역할이 애플리케이션 권한으로 표시됩니다.

다음 예제는 `Application`이 대상으로 지정된 앱 역할을 보여줍니다.

```json
"appId": "8763f1c4-0000-0000-0000-158e9ef97d6a",
"appRoles": [
    {
      "allowedMemberTypes": [
        "Application"
      ],
      "displayName": "ConsumerApps",
      "id": "47fbb575-0000-0000-0000-0f7a6c30beac",
      "isEnabled": true,
      "description": "Consumer apps have access to the consumer data.",
      "value": "Consumer"
    }
  ],
"availableToOtherTenants": false,
```

## <a name="assign-users-and-groups-to-roles"></a>역할에 사용자 및 그룹 할당

애플리케이션에서 앱 역할을 추가하고 나면 이 역할에 사용자 및 그룹을 할당할 수 있습니다. 역할에 사용자 및 그룹을 할당하는 작업은 포털의 UI를 통해 수행하거나 [Microsoft Graph](/graph/api/user-post-approleassignments)를 사용하여 프로그래밍 방식으로 수행할 수 있습니다. 다양한 앱 역할에 할당된 사용자가 애플리케이션에 로그인하면 해당 토큰은 `roles` 클레임에 할당된 역할을 갖게 됩니다.

Azure Portal을 사용하여 사용자 및 그룹을 역할에 할당하려면 다음을 수행합니다.

1. <a href="https://portal.azure.com/" target="_blank">Azure Portal</a>에 로그인합니다.
1. **Azure Active Directory** 의 왼쪽 탐색 메뉴에서 **엔터프라이즈 애플리케이션** 을 선택합니다.
1. **모든 애플리케이션** 을 선택하여 모든 애플리케이션 목록을 봅니다. 애플리케이션이 목록에 표시되지 않으면 **모든 애플리케이션** 목록 상단의 필터를 사용하여 목록을 제한하거나 목록을 아래로 스크롤하여 애플리케이션을 찾습니다.
1. 역할에 사용자 또는 보안 그룹을 할당할 애플리케이션을 선택합니다.
1. **관리** 에서 **사용자 및 그룹** 을 선택합니다.
1. **사용자 추가** 를 선택하여 **할당 추가** 창을 엽니다.
1. **할당 추가** 창에서 **사용자 및 그룹** 선택기를 선택합니다. 사용자와 보안 그룹 목록이 표시됩니다. 특정 사용자 또는 그룹을 검색하고 목록에 표시되는 여러 사용자 및 그룹을 선택할 수 있습니다.
1. 사용자 및 그룹을 선택했으면 **선택** 단추를 선택하여 계속합니다.
1. **할당 추가** 창에서 **역할 선택** 을 선택합니다. 애플리케이션에 정의한 모든 역할이 표시됩니다.
1. 역할을 선택하고 **선택** 단추를 선택합니다.
1. **할당** 단추를 선택하여 앱에 대한 사용자 및 그룹 할당을 완료합니다.

추가한 사용자와 그룹이 **사용자 및 그룹** 목록에 보이는지 확인합니다.

## <a name="assign-app-roles-to-applications"></a>애플리케이션에 앱 역할 할당

애플리케이션에 앱 역할을 추가했으면 Azure Portal을 사용하여 또는 [Microsoft Graph](/graph/api/user-post-approleassignments)를 사용하여 프로그래밍 방식으로 클라이언트 앱에 앱 역할을 할당할 수 있습니다.

애플리케이션에 앱 역할을 할당할 때 _애플리케이션 권한_ 을 만듭니다. 애플리케이션 권한은 일반적으로 인증을 수행하고 사용자의 상호 작용 없이 자체적으로 권한 부여된 API 호출을 수행해야 하는 데몬 앱 또는 백엔드 서비스에서 사용됩니다.

Azure Portal을 사용하여 애플리케이션에 앱 역할을 할당하려면 다음을 수행합니다.

1. <a href="https://portal.azure.com/" target="_blank">Azure Portal</a>에 로그인합니다.
1. **Azure Active Directory** 의 왼쪽 탐색 메뉴에서 **앱 등록** 을 선택합니다.
1. **모든 애플리케이션** 을 선택하여 모든 애플리케이션 목록을 봅니다. 애플리케이션이 목록에 표시되지 않으면 **모든 애플리케이션** 목록 상단의 필터를 사용하여 목록을 제한하거나 목록을 아래로 스크롤하여 애플리케이션을 찾습니다.
1. 앱 역할을 할당하려는 애플리케이션을 선택합니다.
1. **API 권한** > **권한 추가** 를 선택합니다.
1. **내 API** 탭을 선택한 후 앱 역할을 정의한 대상 앱을 선택합니다.
1. **애플리케이션 권한** 을 선택합니다.
1. 할당하려는 역할을 선택합니다.
1. **권한 추가** 단추를 선택하여 역할 추가 작업을 완료합니다.

새로 추가된 역할은 앱 등록의 **API 권한** 창에 표시됩니다.

#### <a name="grant-admin-consent"></a>관리자 동의 허용

이 권한은 위임된 권한이 아니라 _애플리케이션 권한_ 이기 때문에 관리자가 애플리케이션에 할당된 앱 역할을 사용하도록 동의해야 합니다.

1. 앱 등록의 **API 권한** 창에서 **\<tenant name\>에 대한 관리자 동의 허용** 을 선택합니다.
1. 요청된 권한에 대해 동의를 허용하라는 메시지가 표시되면 **예** 를 선택합니다.

**상태** 열에는 동의가 **\<tenant name\>에 대해 허용** 되었는지 표시됩니다.

## <a name="use-app-roles-in-your-web-api"></a>웹 API에서 앱 역할 사용

앱 역할을 정의하고 이를 사용자, 그룹 또는 애플리케이션에 할당했으면 이제 API가 호출될 때 이러한 역할을 확인하는 코드를 웹 API에 추가해야 합니다. 즉, 권한 부여가 필요하도록 결정된 API 작업을 클라이언트 앱이 요청할 때, API 코드는 클라이언트의 앱 호출에 있는 액세스 토큰이 범위에 포함되는지 확인해야 합니다.

웹 API에 권한 부여를 추가하는 방법은 [보호된 웹 API: 범위 및 앱 역할 확인](scenario-protected-web-api-verification-scope-app-roles.md)을 참조하세요.

## <a name="app-roles-vs-groups"></a>앱 역할 및 그룹

권한 부여를 위해 앱 역할 또는 그룹을 사용할 수 있지만, 시나리오에 사용할 항목을 결정할 때 둘 사이의 주요 차이점을 고려해야 합니다.

| 앱 역할                                                                                                    | 그룹                                                      |
| ------------------------------------------------------------------------------------------------------------ | ----------------------------------------------------------- |
| 애플리케이션에 따라 다르며, 앱 등록에 정의됩니다. 애플리케이션과 함께 이동합니다. | 앱을 따르지 않지만, Azure AD 테넌트에 따라 다릅니다. |
| 앱 역할은 앱 등록이 제거될 때 제거됩니다.                                                | 그룹은 앱이 제거되어도 그대로 유지됩니다.            |
| `roles` 클레임에 제공됩니다.                                                                               | `groups` 클레임에 제공됩니다.                                 |

개발자는 앱 역할을 사용하여 사용자가 앱에 로그인할 수 있는지 또는 앱이 웹 API에 대해 액세스 토큰을 가져올 수 있는지 제어할 수 있습니다. 이 보안 제어를 그룹을 확장하기 위해 개발자 및 관리자는 보안 그룹을 앱 역할에 할당할 수도 있습니다.

앱 역할은 개발자가 자신의 앱 자체에서 권한 부여 매개 변수를 기술하고 제어하길 원하는 경우에 선호됩니다. 예를 들어 권한 부여를 위해 그룹을 사용하는 앱은 해당 그룹 ID와 이름이 다를 수 있기 때문에 다음 테넌트에서 중단됩니다. 앱 역할을 사용하는 앱은 안전하게 유지됩니다. 실제로 앱 역할에 그룹을 할당하는 것은 동일한 이유로 SaaS 앱에서 자주 사용됩니다.

## <a name="next-steps"></a>다음 단계

앱 역할에 대해 자세히 알아보려면 다음 리소스를 참조하세요.

- GitHub의 코드 샘플
  - [그룹 및 그룹 클레임을 사용하여 ASP.NET Core 웹 앱에 권한 부여 추가](https://aka.ms/groupssample)
  - [.NET Core 웹 API를 호출하고 앱 역할 및 보안 그룹을 사용하는 Angular SPA(단일 페이지 애플리케이션)](https://github.com/Azure-Samples/ms-identity-javascript-angular-tutorial/tree/main/5-AccessControl)
  - [Node.js 웹 API를 호출하고 앱 역할 및 보안 그룹을 사용하는 React SPA(단일 페이지 애플리케이션)](https://github.com/Azure-Samples/ms-identity-javascript-react-tutorial/tree/main/5-AccessControl)
- 참조 설명서
  - [Azure AD 앱 매니페스트](./reference-app-manifest.md)
  - [Azure AD 액세스 토큰](access-tokens.md)
  - [Azure AD ID 토큰](id-tokens.md)
  - [앱에 선택적인 클레임 제공](active-directory-optional-claims.md)
- 동영상: [Microsoft ID 플랫폼을 사용하여 애플리케이션에서 권한 부여 구현](https://www.youtube.com/watch?v=LRoc-na27l0)(1:01:15)
