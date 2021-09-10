---
title: 구성 가능한 토큰 수명
titleSuffix: Microsoft identity platform
description: Microsoft ID 플랫폼에서 발급한 액세스, SAML 및 ID 토큰의 수명을 설정하는 방법에 대해 알아봅니다.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 06/01/2021
ms.author: ryanwi
ms.custom: aaddev, identityplatformtop40, content-perf, FY21Q1, contperf-fy21q1
ms.reviewer: hirsin, jlu, annaba
ms.openlocfilehash: f67e02d0da3d7f7c1083cdb96436c8b82cae2cef
ms.sourcegitcommit: ee8ce2c752d45968a822acc0866ff8111d0d4c7f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/14/2021
ms.locfileid: "113728069"
---
# <a name="configurable-token-lifetimes-in-the-microsoft-identity-platform-preview"></a>Microsoft ID 플랫폼의 구성 가능한 토큰 수명(미리 보기)

Microsoft ID 플랫폼에서 발급한 액세스, ID 또는 SAML 토큰의 수명을 지정할 수 있습니다. 조직의 모든 앱, 다중 테넌트(다중 조직) 애플리케이션 또는 조직의 특정 서비스 주체에 대해 토큰 수명을 구성할 수 있습니다. 그러나 현재 [관리 ID 서비스 주체](../managed-identities-azure-resources/overview.md)에 대한 토큰 수명 구성은 지원되지 않습니다.

Azure AD에서 정책 개체는 개별 애플리케이션 또는 조직의 모든 애플리케이션에 적용되는 규칙 집합을 나타냅니다. 각 정책 유형에는 할당된 개체에 적용되는 속성 집합이 포함된 고유의 구조가 있습니다.

조직의 기본 정책이 될 정책을 지정할 수 있습니다. 해당 정책은 더 높은 우선 순위의 정책으로 재정의되지 않는 한 조직 내의 모든 애플리케이션에 적용됩니다. 특정 애플리케이션에 정책을 할당할 수도 있습니다. 우선 순위는 정책 유형에 따라 다릅니다.

예제는 [토큰 수명을 구성하는 방법에 대한 예제](configure-token-lifetimes.md)를 참조하세요.

> [!NOTE]
> 구성 가능한 토큰 수명 정책은 SharePoint Online 및 OneDrive for Business 리소스에 액세스하는 모바일 및 데스크톱 클라이언트에만 적용되며 웹 브라우저 세션에는 적용되지 않습니다.
> SharePoint Online 및 OneDrive for Business의 웹 브라우저 세션 수명을 관리하려면 [조건부 액세스 세션 수명](../conditional-access/howto-conditional-access-session-lifetime.md) 기능을 사용합니다. 유휴 세션 제한 시간을 구성하는 방법에 대한 자세한 내용은 [SharePoint Online 블로그](https://techcommunity.microsoft.com/t5/SharePoint-Blog/Introducing-Idle-Session-Timeout-in-SharePoint-and-OneDrive/ba-p/119208)를 참조하세요.

## <a name="license-requirements"></a>라이선스 요구 사항

이 기능을 사용하려면 Azure AD Premium P1 라이선스가 필요합니다. 요구 사항에 적합한 라이선스를 찾으려면 [체험판 및 프리미엄 버전의 일반적으로 사용할 수 있는 기능 비교](https://www.microsoft.com/security/business/identity-access-management/azure-ad-pricing)를 참조하세요.

[Microsoft 365 Business 라이선스](/office365/servicedescriptions/microsoft-365-service-descriptions/microsoft-365-business-service-description)가 있는 고객은 조건부 액세스 기능에도 액세스할 수 있습니다.

## <a name="token-lifetime-policies-for-access-saml-and-id-tokens"></a>액세스, SAML 및 ID 토큰에 대한 토큰 수명 정책

액세스 토큰, SAML 토큰 및 ID 토큰에 대한 토큰 수명 정책을 설정할 수 있습니다.

### <a name="access-tokens"></a>액세스 토큰

클라이언트는 액세스 토큰을 사용하여 보호된 리소스에 액세스합니다. 액세스 토큰은 사용자, 클라이언트 및 리소스의 특정 조합에만 사용할 수 있습니다. 액세스 토큰은 해지될 수 없으며 만료될 때까지 유효합니다. 액세스 토큰을 획득한 악의적인 행위자는 수명 범위 동안 이를 사용할 수 있습니다. 액세스 토큰의 수명을 조정하는 것은 시스템 성능을 개선하는 것과 사용자 계정이 비활성화된 후 클라이언트에서 액세스를 유지하는 기간을 늘리는 것 간의 절충 작업입니다. 시스템 성능을 개선하려면 클라이언트에서 새 액세스 토큰을 획득해야 하는 횟수를 줄이면 됩니다.  기본값은 토큰을 요청하는 클라이언트 응용 프로그램에 따라 달라집니다. 예를 들어 CAE 인식 세션을 협상하는 CAE(지속적인 액세스 권한 평가) 지원 클라이언트는 오래 지속되는 토큰 수명(최대 28시간)을 볼 수 있습니다. 토큰이 만료된 후 클라이언트는 새로 고침 토큰을 사용하여(보통 자동으로) 새로운 새로 고침 토큰을 획득하고 토큰에 액세스해야 합니다.

### <a name="saml-tokens"></a>SAML 토큰

SAML 토큰은 많은 웹 기반 SaaS 애플리케이션에서 사용되며, Azure Active Directory의 SAML2 프로토콜 엔드포인트를 사용하여 가져옵니다. 또한 WS-Federation을 사용하는 애플리케이션에서도 사용됩니다. 토큰의 기본 수명은 1시간입니다. 애플리케이션의 관점에서 토큰의 유효 기간은 토큰의 `<conditions …>` 요소에 대한 NotOnOrAfter 값으로 지정됩니다. 토큰의 유효 기간이 끝난 후 클라이언트는 새 인증 요청을 시작해야 합니다. 이는 대화형 로그인 없이 SSO(Single Sign On) 세션 토큰의 결과로 충족되는 경우가 많습니다.

NotOnOrAfter의 값은 `TokenLifetimePolicy`에 `AccessTokenLifetime` 매개 변수를 사용하여 변경할 수 있습니다. 정책에 구성된 수명(있는 경우)에 5분의 클록 오차 인수를 더해 설정됩니다.

`<SubjectConfirmationData>` 요소에 지정된 주체 확인 NotOnOrAfter는 토큰 수명 구성의 영향을 받지 않습니다. 

### <a name="id-tokens"></a>ID 토큰

ID 토큰은 웹 사이트 및 기본 클라이언트에 전달됩니다. ID 토큰은 사용자에 대한 프로필 정보를 포함합니다. ID 토큰은 사용자와 클라이언트의 특정 조합에 바인딩되며, ID 토큰은 만료될 때까지 유효한 것으로 간주됩니다. 일반적으로 웹 애플리케이션은 애플리케이션의 사용자 세션 수명을 해당 사용자에 대해 발급된 ID 토큰의 수명과 일치시킵니다. ID 토큰의 수명을 조정하여 웹 애플리케이션이 애플리케이션 세션을 만료시키는 빈도 및 사용자에게 Microsoft ID 플랫폼에 다시 인증하도록(자동으로 또는 대화형으로) 요구하는 빈도를 제어할 수 있습니다.

## <a name="token-lifetime-policies-for-refresh-tokens-and-session-tokens"></a>새로 고침 토큰 및 세션 토큰에 대한 토큰 수명 정책

새로 고침 토큰 및 세션 토큰에 대한 토큰 수명 정책을 설정할 수 없습니다. 새로 고침 토큰에 대한 수명, 시간 제한 및 해지 정보는 [새로 고침 토큰](refresh-tokens.md)을 참조하세요.

> [!IMPORTANT]
> 2021년 1월 30일부터 새로 고침 및 세션 토큰 수명을 구성할 수 없습니다. Azure Active Directory는 기존 정책의 새로 고침 및 세션 토큰 구성을 더 이상 적용하지 않습니다.  기존 토큰이 만료된 후 발급된 새 토큰은 이제 [기본 구성](#configurable-token-lifetime-properties)으로 설정됩니다. 새로 고침 및 세션 토큰 구성의 사용이 중지된 후에도 액세스, SAML 및 ID 토큰 수명을 구성할 수 있습니다.
>
> 기존 토큰의 수명은 변경되지 않습니다. 만료되면 기본값에 따라 새 토큰이 발급됩니다.
>
> 사용자에게 다시 로그인하라는 메시지가 표시되기 전까지의 기간을 계속 정의해야 하는 경우 조건부 액세스에서 로그인 빈도를 구성합니다. 조건부 액세스에 대해 자세히 알아보려면 [조건부 액세스를 사용하여 인증 세션 관리 구성](../conditional-access/howto-conditional-access-session-lifetime.md)을 참조하세요.

## <a name="configurable-token-lifetime-properties"></a>구성 가능한 토큰 수명 속성
토큰 수명 정책은 토큰 수명 규칙을 포함하는 정책 개체의 형식입니다. 이 정책은 이 리소스에 대한 액세스, SAML 및 ID 토큰이 유효한 것으로 간주되는 기간을 제어합니다. 새로 고침 및 세션 토큰에 대해 토큰 수명 정책을 설정할 수 없습니다. 설정된 정책이 없는 경우 시스템에서 기본 수명값을 적용합니다.

### <a name="access-id-and-saml2-token-lifetime-policy-properties"></a>액세스, ID 및 SAML2 토큰 수명 정책 속성

액세스 토큰 수명 속성을 줄이면 악의적인 행위자가 액세스 토큰 또는 ID 토큰을 장시간 사용할 위험을 완화할 수 있습니다. (이러한 토큰은 해지할 수 없습니다.) 그 대신 토큰을 좀 더 자주 교체해야 하기 때문에 성능에 악영향을 줍니다.

예를 들어 [웹 로그인에 대한 정책 만들기](configure-token-lifetimes.md#create-a-policy-for-web-sign-in)를 참조하세요.

액세스, ID 및 SAML2 토큰 구성은 다음 속성과 각각 설정된 값의 영향을 받습니다.

- **속성**: 액세스 토큰 수명
- **정책 속성 문자열**: AccessTokenLifetime
- **영향**: 액세스 토큰, ID 토큰, SAML2 토큰
- **기본값**:
    - 액세스 토큰: 토큰을 요청하는 클라이언트 응용 프로그램에 따라 달라집니다. 예를 들어 CAE 인식 세션을 협상하는 CAE(지속적인 액세스 권한 평가) 지원 클라이언트는 오래 지속되는 토큰 수명(최대 28시간)을 볼 수 있습니다.
    - ID 토큰, SAML2 토큰: 1시간
- **최소**: 10분
- **최대**: 1일

### <a name="refresh-and-session-token-lifetime-policy-properties"></a>새로 고침 및 세션 토큰 수명 정책 속성

새로 고침 및 세션 토큰 구성은 다음 속성과 각각 설정된 값의 영향을 받습니다. 2021년 1월 30일에 새로 고침 및 세션 토큰 구성의 사용이 중지된 후 Azure AD는 아래에 설명된 기본값만 적용합니다. [조건부 액세스](../conditional-access/howto-conditional-access-session-lifetime.md)를 사용하여 로그인 빈도를 관리하지 않기로 결정한 경우 새로 고침 및 세션 토큰이 해당 날짜의 기본 구성으로 설정되며 더 이상 수명을 변경할 수 없습니다.  

|속성   |정책 속성 문자열    |영향 |기본값 |
|----------|-----------|------------|------------|
|새로 고침 토큰 최대 비활성 시간 |MaxInactiveTime  |새로 고침 토큰 |90일  |
|단일 단계 새로 고침 토큰 최대 기간  |MaxAgeSingleFactor  |새로 고침 토큰(모든 사용자)  |Until-revoked  |
|다단계 새로 고침 토큰 최대 기간  |MaxAgeMultiFactor  |새로 고침 토큰(모든 사용자) |Until-revoked  |
|단일 단계 세션 토큰 최대 기간  |MaxAgeSessionSingleFactor |세션 토큰(영구 및 비영구)  |Until-revoked |
|다단계 세션 토큰 최대 기간  |MaxAgeSessionMultiFactor  |세션 토큰(영구 및 비영구)  |Until-revoked |

PowerShell을 사용하여 사용 중지의 영향을 받는 정책을 찾을 수 있습니다.  조직에서 만든 모든 정책을 확인하거나 특정 정책에 연결된 앱 및 서비스 주체를 찾으려면 [PowerShell cmdlet](configure-token-lifetimes.md#get-started)을 사용합니다.

## <a name="policy-evaluation-and-prioritization"></a>정책 평가 및 우선 순위 지정
토큰 수명 정책을 만들어서 특정 애플리케이션, 조직 및 서비스 주체에 할당할 수 있습니다. 특정 애플리케이션에 여러 정책이 적용될 수 있습니다. 적용되는 토큰 수명 정책은 다음 규칙을 따릅니다.

* 정책이 서비스 주체에 명시적으로 할당된 경우 해당 정책이 적용됩니다.
* 서비스 주체에 명시적으로 할당된 정책이 없는 경우 서비스 주체의 상위 조직에 명시적으로 할당된 정책이 적용됩니다.
* 서비스 주체 또는 조직에 명시적으로 할당된 정책이 없는 경우 애플리케이션에 할당된 정책이 적용됩니다.
* 서비스 주체, 조직 또는 애플리케이션 개체에 할당된 정책이 없는 경우 기본값이 적용됩니다. [구성 가능한 토큰 수명 속성](#configurable-token-lifetime-properties)의 테이블을 참조하세요.

애플리케이션 개체와 서비스 주체 간의 관계에 대한 자세한 내용은 [Azure Active Directory의 애플리케이션 및 서비스 주체 개체](app-objects-and-service-principals.md)를 참조하세요.

토큰의 유효성은 토큰이 사용되는 시점에 평가됩니다. 액세스하는 애플리케이션에 대한 우선 순위가 가장 높은 정책이 적용됩니다.

여기에 사용되는 시간 범위는 C# [TimeSpan](/dotnet/api/system.timespan) 개체 - D.HH:MM:SS에 따라 서식이 지정됩니다.  따라서 80일 및 30분은 `80.00:30:00`입니다.  앞에 오는 D는 0인 경우 삭제할 수 있으므로 90분은 `00:90:00`입니다.  

## <a name="cmdlet-reference"></a>Cmdlet 참조

이는 [Azure Active Directory PowerShell For Graph 미리 보기 모듈](/powershell/module/azuread/?view=azureadps-2.0-preview&preserve-view=true#service-principals)의 cmdlet입니다.

### <a name="manage-policies"></a>정책 관리

다음 cmdlet을 사용하여 정책을 관리할 수 있습니다.

| Cmdlet | Description | 
| --- | --- |
| [New-AzureADPolicy](/powershell/module/azuread/new-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) | 새 정책을 만듭니다. |
| [Get-AzureADPolicy](/powershell/module/azuread/get-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) | 모든 Azure AD 정책 또는 지정된 정책을 가져옵니다. |
| [Get-AzureADPolicyAppliedObject](/powershell/module/azuread/get-azureadpolicyappliedobject?view=azureadps-2.0-preview&preserve-view=true) | 정책에 연결된 모든 앱 및 서비스 주체를 가져옵니다. |
| [Set-AzureADPolicy](/powershell/module/azuread/set-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) | 기존 정책을 업데이트합니다. |
| [Remove-AzureADPolicy](/powershell/module/azuread/remove-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) | 지정된 정책을 삭제합니다. |

### <a name="application-policies"></a>애플리케이션 정책
애플리케이션 정책에 다음 cmdlet을 사용할 수 있습니다.</br></br>

| Cmdlet | Description | 
| --- | --- |
| [Add-AzureADApplicationPolicy](/powershell/module/azuread/add-azureadapplicationpolicy?view=azureadps-2.0-preview&preserve-view=true) | 지정된 정책을 애플리케이션에 연결합니다. |
| [Get-AzureADApplicationPolicy](/powershell/module/azuread/get-azureadapplicationpolicy?view=azureadps-2.0-preview&preserve-view=true) | 애플리케이션에 할당된 정책을 가져옵니다. |
| [Remove-AzureADApplicationPolicy](/powershell/module/azuread/remove-azureadapplicationpolicy?view=azureadps-2.0-preview&preserve-view=true) | 애플리케이션에서 정책을 제거합니다. |

### <a name="service-principal-policies"></a>서비스 사용자 정책
서비스 주체 정책에 다음 cmdlet을 사용할 수 있습니다.

| Cmdlet | Description | 
| --- | --- |
| [Add-AzureADServicePrincipalPolicy](/powershell/module/azuread/add-azureadserviceprincipalpolicy?view=azureadps-2.0-preview&preserve-view=true) | 지정된 정책을 서비스 주체에 연결합니다. |
| [Get-AzureADServicePrincipalPolicy](/powershell/module/azuread/get-azureadserviceprincipalpolicy?view=azureadps-2.0-preview&preserve-view=true) | 지정된 서비스 주체에 연결된 모든 정책을 가져옵니다.|
| [Remove-AzureADServicePrincipalPolicy](/powershell/module/azuread/remove-azureadserviceprincipalpolicy?view=azureadps-2.0-preview&preserve-view=true) | 지정된 서비스 주체에서 정책을 제거합니다.|

## <a name="next-steps"></a>다음 단계

자세한 정보는 [토큰 수명을 구성하는 방법에 대한 예제](configure-token-lifetimes.md)를 참조하세요.
