---
title: 개발자 포털 - 질문과 대답
titleSuffix: Azure API Management
description: API Management의 개발자 포털에 대한 질문과 대답입니다. 개발자 포털은 API 소비자가 API를 탐색할 수 있는 사용자 지정 가능한 웹 사이트입니다.
services: api-management
documentationcenter: API Management
author: mikebudzynski
ms.service: api-management
ms.topic: troubleshooting
ms.date: 07/30/2021
ms.author: apimpm
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: b7b7efda7ca28382ca1dfbdead64db9976d67bd4
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122567219"
---
# <a name="api-management-developer-portal---frequently-asked-questions"></a>API Management 개발자 포털 - 질문과 대답

## <a name="what-if-i-need-functionality-that-isnt-supported-in-the-portal"></a>포털에서 지원되지 않는 기능이 필요한 경우 어떻게 하나요?

[GitHub 리포지토리](https://github.com/Azure/api-management-developer-portal)에서 기능 요청을 열거나 [누락된 기능을 직접 구현](developer-portal-implement-widgets.md)할 수 있습니다. 개발자 포털 [확장성](api-management-howto-developer-portal.md#managed-vs-self-hosted)에 대해 자세히 알아보세요.


## <a name="can-i-have-multiple-developer-portals-in-one-api-management-service"></a>하나의 API Management 서비스에서 여러 개발자 포털을 사용할 수 있나요?

하나의 관리되는 포털 및 여러 개의 자체 호스팅 포털이 있을 수 있습니다. 모든 포털의 콘텐츠는 동일한 API Management 서비스에 저장되므로 동일합니다. 포털의 외관과 기능을 구분하려면 런타임 시 페이지를 동적으로 사용자 지정하는(예: URL을 기반으로) 고유한 사용자 지정 위젯을 사용하여 자체 호스팅할 수 있습니다.

## <a name="does-the-portal-support-azure-resource-manager-templates-andor-is-it-compatible-with-api-management-devops-resource-kit"></a>포털이 Azure Resource Manager 템플릿을 지원하나요? 아니면 API Management DevOps Resource Kit와 호환되나요?

아니요.

## <a name="is-the-portals-content-saved-with-the-backuprestore-functionality-in-api-management"></a>포털 콘텐츠가 API Management의 백업/복원 기능으로 저장되나요?

아니요.

## <a name="do-i-need-to-enable-additional-vnet-connectivity-for-the-managed-portal-dependencies"></a>관리되는 포털 종속성에 대한 추가 VNet 연결을 사용하도록 설정해야 하나요?

대부분의 경우 아닙니다.

API Management 서비스가 내부 VNet에 있는 경우 개발자 포털은 네트워크 내에서만 액세스할 수 있습니다. 관리 엔드포인트의 호스트 이름은 포털의 관리 인터페이스에 액세스하는 데 사용하는 컴퓨터에서 서비스의 내부 VIP로 확인되어야 합니다. 관리 엔드포인트가 DNS에 등록되어 있는지 확인합니다. 잘못된 구성의 경우 `Unable to start the portal. See if settings are specified correctly in the configuration (...)` 오류가 표시됩니다.

API Management 서비스가 내부 VNet에 있고 인터넷에서 Application Gateway를 통해 액세스하는 경우 개발자 포털 및 API Management의 관리 엔드포인트에 대한 연결을 사용하도록 설정해야 합니다. Web Application Firewall 규칙을 사용하지 않도록 설정해야 할 수 있습니다. 자세한 내용은 [이 설명서 문서](api-management-howto-integrate-internal-vnet-appgateway.md)를 참조하세요.

## <a name="i-assigned-a-custom-api-management-domain-and-the-published-portal-doesnt-work"></a>사용자 지정 API Management 도메인을 할당하고 게시된 포털이 작동하지 않음

도메인을 업데이트한 후 변경 사항을 적용하려면 [포털을 다시 게시](api-management-howto-developer-portal-customize.md#publish)해야 합니다.

## <a name="i-added-an-identity-provider-and-i-cant-see-it-in-the-portal"></a>ID 공급자를 추가하고 포털에서 볼 수 없음

ID 공급자(예: Azure AD, Azure AD B2C)를 추가한 후 변경 사항을 적용하려면 [포털을 다시 게시](api-management-howto-developer-portal-customize.md#publish)해야 합니다. 개발자 포털 페이지에 OAuth 단추 위젯이 포함되어 있는지 확인합니다.

## <a name="i-set-up-delegation-and-the-portal-doesnt-use-it"></a>위임을 설정하고 포털에서 이를 사용하지 않음

위임을 설정한 후 변경 사항을 적용하려면 [포털을 다시 게시](api-management-howto-developer-portal-customize.md#publish)해야 합니다.

## <a name="my-other-api-management-configuration-changes-havent-been-propagated-in-the-developer-portal"></a>다른 API Management 구성 변경 사항이 개발자 포털에 전파되지 않았음

대부분의 구성 변경(예: VNet, 로그인, 제품 약관) 시 [포털을 다시 게시](api-management-howto-developer-portal-customize.md#publish)해야 합니다.

## <a name="im-getting-a-cors-error-when-using-the-interactive-console"></a><a name="cors"></a> 대화형 콘솔을 사용할 때 CORS 오류가 발생함

대화형 콘솔은 브라우저에서 클라이언트 측 API 요청을 만듭니다. API에 [CORS 정책](api-management-cross-domain-policies.md#CORS)을 추가하여 CORS 문제를 해결합니다.

Azure Portal에서 API Management 서비스의 **포털 개요** 섹션에서 CORS 정책의 상태를 확인할 수 있습니다. 경고 상자에는 존재하지 않거나 잘못 구성된 정책이 표시됩니다.

> [!NOTE]
> 
> CORS 정책은 하나만 실행됩니다. 여러 CORS 정책을 지정하는 경우(예: API 수준 및 모든 API 수준에서) 대화형 콘솔이 예상대로 작동하지 않을 수 있습니다.

![CDRS 정책의 상태를 확인할 수 있는 위치를 보여주는 스크린샷.](media/developer-portal-faq/cors-azure-portal.png)

**CORS 사용** 단추를 클릭하여 CORS 정책을 자동으로 적용합니다.

CORS를 수동으로 사용 설정할 수도 있습니다.

1. **전역 수준에서 수동으로 적용** 링크를 클릭하여 생성된 정책 코드를 확인합니다.
2. Azure Portal의 API Management 서비스에서 **API** 섹션의 **모든 API** 로 이동합니다.
3. **인바운드 처리** 섹션에서 **</>** 아이콘을 선택합니다.
4. XML 파일의 **<inbound>** 섹션에 정책을 삽입합니다. **<origin>** 값이 개발자 포털의 도메인과 일치해야 합니다.

> [!NOTE]
> 
> API 범위 대신 제품 범위에 CORS를 정책하고, API에서 헤더를 통해 구독 키 인증을 사용하는 경우 콘솔이 작동하지 않습니다.
>
> 브라우저는 구독 키가 있는 헤더를 포함하지 않는 `OPTIONS` HTTP 요청을 자동으로 실행합니다. 구독 키 누락으로 인해 API Management는 `OPTIONS` 호출과 제품을 연결할 수 없어 CORS 정책을 적용할 수 없습니다.
>
> 해결 방법으로, 쿼리 매개 변수에서 구독 키를 전달할 수 있습니다.

## <a name="what-is-the-cors-proxy-feature-and-when-should-i-use-it"></a>CORS 프록시 기능은 무엇이며 언제 사용해야 합니까?

API 작업 세부 정보 위젯의 **CORS 프록시 사용** 옵션을 선택하여 API Management 서비스에 있는 포털의 백엔드를 통해 대화형 콘솔의 API 호출을 라우팅합니다. 이 구성에서는 더 이상 API에 대한 CORS 정책을 적용할 필요가 없으며, 로컬 컴퓨터에서 게이트웨이 엔드포인트에 대한 연결이 필요하지 않습니다. API가 자체 호스팅 게이트웨이를 통해 노출되거나 서비스가 가상 네트워크에 있는 경우 API Management의 백엔드 서비스에서 게이트웨이로의 연결이 필요합니다. 자체 호스팅 포털을 사용하는 경우 구성 파일의 `backendUrl` 옵션을 사용하여 포털의 백엔드 엔드포인트를 지정합니다. 그렇지 않은 경우 자체 호스팅 포털이 백엔드 서비스의 위치를 인식하지 못합니다.

## <a name="what-permissions-do-i-need-to-edit-the-developer-portal"></a>개발자 포털을 편집하는 데 필요한 권한은 무엇인가요?

관리 모드에서 포털을 열었을 때 `Oops. Something went wrong. Please try again later.` 오류가 표시되는 경우 필요한 권한(Azure RBAC)이 부족한 것일 수 있습니다.

사용자 관리자가 포털에 액세스하도록 허용하기 위해 레거시 포털에서는 서비스 범위(`/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ApiManagement/service/<apim-service-name>`)에서 `Microsoft.ApiManagement/service/getssotoken/action` 권한이 필요했습니다. 새 포털에서는 `/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ApiManagement/service/<apim-service-name>/users/1` 범위에서 `Microsoft.ApiManagement/service/users/token/action` 권한이 필요합니다.

다음 PowerShell 스크립트를 사용하여 필요한 권한이 있는 역할을 만들 수 있습니다. `<subscription-id>` 매개 변수를 변경해야 합니다. 

```powershell
#New Portals Admin Role 
Import-Module Az 
Connect-AzAccount 
$contributorRole = Get-AzRoleDefinition "API Management Service Contributor" 
$customRole = $contributorRole 
$customRole.Id = $null
$customRole.Name = "APIM New Portal Admin" 
$customRole.Description = "This role gives the user ability to log in to the new Developer portal as administrator" 
$customRole.Actions = "Microsoft.ApiManagement/service/users/token/action" 
$customRole.IsCustom = $true 
$customRole.AssignableScopes.Clear() 
$customRole.AssignableScopes.Add('/subscriptions/<subscription-id>') 
New-AzRoleDefinition -Role $customRole 
```
 
역할을 만든 후에는 Azure Portal에서 **액세스 제어(IAM)** 섹션의 모든 사용자에게 역할을 부여할 수 있습니다. 사용자에게 이 역할을 할당하면 서비스 범위에서 권한이 할당됩니다. 사용자는 서비스의 모든 사용자를 대신하여 SAS 토큰을 생성할 수 있습니다. 최소한 이 역할은 서비스 관리자에게 할당되어야 합니다. 다음 PowerShell 명령은 `user1` 사용자에게 불필요한 권한이 부여되지 않도록 가장 낮은 범위에서 역할을 할당하는 방법을 보여줍니다. 

```powershell
New-AzRoleAssignment -SignInName "user1@contoso.com" -RoleDefinitionName "APIM New Portal Admin" -Scope "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ApiManagement/service/<apim-service-name>/users/1" 
```

사용자에게 권한이 부여된 후에는 사용자가 로그아웃하고 Azure Portal에 다시 로그인해야 새 권한이 적용됩니다.

## <a name="im-seeing-the-unable-to-start-the-portal-see-if-settings-are-specified-correctly--error"></a>`Unable to start the portal. See if settings are specified correctly (...)` 오류가 표시됩니다.

`https://<management-endpoint-hostname>/subscriptions/xxx/resourceGroups/xxx/providers/Microsoft.ApiManagement/service/xxx/contentTypes/document/contentItems/configuration?api-version=2018-06-01-preview`에 대한 `GET` 호출이 실패하면 이 오류가 표시됩니다. 호출은 포털의 관리 인터페이스에 의해 브라우저에서 실행됩니다.

API Management 서비스가 VNet에 있는 경우 [VNet 연결 질문](#do-i-need-to-enable-additional-vnet-connectivity-for-the-managed-portal-dependencies)을 참조하세요.

사용자 지정 도메인에 할당되고 브라우저에서 신뢰하지 않는 TLS/SSL 인증서로 인해 호출 실패가 발생할 수도 있습니다. 완화를 위해 관리 엔드포인트 사용자 지정 도메인 API Management 제거할 수 있으며, 신뢰할 수 있는 인증서가 있는 기본 엔드포인트로 대체됩니다.

## <a name="whats-the-browser-support-for-the-portal"></a>포털에 대해 어떤 브라우저가 지원되나요?

| 브라우저                     | 지원 여부       |
|-----------------------------|-----------------|
| Apple Safari                | 예<sup>1</sup> |
| Google Chrome               | 예<sup>1</sup> |
| Microsoft Edge              | 예<sup>1</sup> |
| Microsoft Internet Explorer | 아니요              |
| Mozilla Firefox             | 예<sup>1</sup> |

 <small><sup>1</sup> 2개의 최신 프로덕션 버전에서 지원됩니다.</small>

## <a name="local-development-of-my-self-hosted-portal-is-no-longer-working"></a>자체 호스팅 포털의 로컬 개발이 더 이상 작동하지 않습니다.

개발자 포털의 로컬 버전에서 스토리지 계정 또는 API Management 인스턴스에 정보를 저장하거나 검색할 수 없는 경우 SAS 토큰이 만료되었을 수 있습니다. 새 토큰을 생성하여 해결할 수 있습니다. [개발자 포털 자체 호스트](developer-portal-self-host.md#step-2-configure-json-files-static-website-and-cors-settings) 자습서를 참조하세요.

## <a name="how-do-i-disable-sign-up-in-the-developer-portal"></a>개발자 포털에서 등록을 사용하지 않도록 설정하려면 어떻게 하나요?

개발자 포털에서 기본적으로 등록 기능을 사용하도록 설정할 필요가 없으면 다음 단계를 사용하여 사용하지 않도록 설정할 수 있습니다.

1. Azure Portal에서 API Management 인스턴스로 이동합니다.
1. 메뉴의 **개발자 포털** 에서 **ID** 를 선택합니다.
1. 목록에 표시되는 각 ID 공급자를 삭제합니다. 각 공급자를 선택하고 상황에 맞는 메뉴( **...** )를 선택한 후 **삭제** 를 선택합니다.
 
   :::image type="content" source="media/developer-portal-faq/delete-identity-providers.png" alt-text="ID 공급자 삭제":::
 
1. 개발자 포털 관리 인터페이스로 이동합니다.
1. 포털 콘텐츠에서 **등록** 링크 및 탐색 항목을 제거합니다. 포털 콘텐츠 사용자 지정에 대한 자세한 내용은 [자습서: 개발자 포털 액세스 및 사용자 지정](api-management-howto-developer-portal-customize.md)을 참조하세요.
 
   :::image type="content" source="media/developer-portal-faq/delete-navigation-item.png" alt-text="탐색 항목 삭제":::
 
1. 사용자가 직접 탐색하는 경우 **등록** 페이지 콘텐츠를 수정하여 ID 데이터를 입력하는 데 사용되는 필드를 제거합니다.
   
   선택적으로 **등록** 페이지를 삭제합니다. 현재 [contentItem](/rest/api/apimanagement/2021-01-01-preview/content-item) REST API를 사용하여 이 페이지를 나열하고 삭제합니다.
 
1. 변경 내용을 저장하고 [포털을 다시 게시](api-management-howto-developer-portal-customize.md#publish)합니다.

## <a name="how-can-i-remove-the-developer-portal-content-provisioned-to-my-api-management-service"></a>API Management 서비스에 프로비전된 개발자 포털 콘텐츠를 어떻게 제거할 수 있나요?

개발자 포털 [GitHub 리포지토리](https://github.com/Azure/api-management-developer-portal)의 `scripts.v3/cleanup.bat` 스크립트에 필요한 매개 변수를 제공하고 스크립트 실행

```sh
cd scripts.v3
.\cleanup.bat
cd ..
```

## <a name="how-do-i-enable-single-sign-on-sso-authentication-to-self-hosted-developer-portal"></a>자체 호스트 개발자 포털에 대한 SSO(Single Sign-On) 인증을 어떻게 사용 설정하나요?

다른 인증 방법 중에서 개발자 포털은 SSO(Single Sign-On)를 지원합니다. 이 방법을 사용하여 인증하려면 쿼리 매개 변수의 토큰을 사용하여 `/signin-sso`를 호출해야 합니다.

```html
https://contoso.com/signin-sso?token=[user-specific token]
```
### <a name="generate-user-tokens"></a>사용자 토큰 생성
[API Management REST API](/rest/api/apimanagement/apimanagementrest/api-management-rest)의 [공유 액세스 토큰 가져오기](/rest/api/apimanagement/2020-12-01/user/get-shared-access-token) 작업을 사용하여 사용자별 토큰(관리자 토큰 포함)을 생성할 수 있습니다.

> [!NOTE]
> 토큰은 URL로 인코딩해야 합니다.


## <a name="next-steps"></a>다음 단계

새 개발자 포털에 대해 자세히 알아봅니다.

- [관리형 개발자 포털 액세스 및 사용자 지정](api-management-howto-developer-portal-customize.md)
- [포털의 자체 호스트 버전 설정](developer-portal-self-host.md)
- [고유한 위젯 구현](developer-portal-implement-widgets.md)

다른 리소스 찾아보기:

- [소스 코드를 포함한 GitHub 리포지토리](https://github.com/Azure/api-management-developer-portal)

