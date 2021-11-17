---
title: 게시자 확인 문제 해결 | Azure
titleSuffix: Microsoft identity platform
description: Microsoft Graph API를 호출하여 Microsoft ID 플랫폼에 대한 게시자 확인 문제를 해결하는 방법을 설명합니다.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: troubleshooting
ms.workload: identity
ms.date: 10/21/2021
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: jesakowi
ms.openlocfilehash: cfbc4c469ca25247c6fb74246133a8dc972a68a9
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130262777"
---
# <a name="troubleshoot-publisher-verification"></a>게시자 확인 문제 해결
[게시자 확인](publisher-verification-overview.md)을 사용하여 프로세스를 완료할 수 없거나 예기치 않은 동작이 발생하는 경우 오류를 수신하거나 예기치 않은 동작이 표시되면 먼저 다음을 수행해야 합니다. 

1. [요구 사항](publisher-verification-overview.md#requirements)을 검토하고 모두 충족되었는지 확인합니다.

1. 지침을 검토하여 [앱을 게시자 확인으로 표시](mark-app-as-publisher-verified.md)하고 모든 단계가 성공적으로 수행되었는지 확인합니다.

1. [일반적인 문제](#common-issues)의 목록을 검토합니다.

1. [Graph Explorer](#making-microsoft-graph-api-calls)를 통해 요청을 재현하여 추가 정보를 수집하고 UI에서 문제를 제외합니다.

## <a name="common-issues"></a>일반적인 문제
프로세스 중에 발생할 수 있는 몇 가지 일반적인 문제는 다음과 같습니다. 

- **내 MPN ID(Microsoft 파트너 네트워크 ID)를 모르거나, 계정에 대한 기본 연락처를 알 수 없습니다.** 
    1. [MPN 등록 페이지](https://partner.microsoft.com/dashboard/account/v3/enrollment/joinnow/basicpartnernetwork/new)로 이동합니다.
    1. 조직의 기본 Azure AD 테넌트에서 사용자 계정으로 로그인합니다. 
    1. MPN 계정이 이미 있으면 이 계정이 인식되어 계정에 추가됩니다. 
    1. MPN ID 및 기본 계정 연락처가 나열되는 [파트너 프로필 페이지](https://partner.microsoft.com/pcv/accountsettings/connectedpartnerprofile)로 이동합니다.

- **Azure AD 전역 관리자(회사 관리자 또는 테넌트 관리자라고도 함)가 누구인지 알 수 없는 경우, 어떻게 찾을 수 있나요? 애플리케이션 관리자 또는 클라우드 애플리케이션 관리자는 어떻게 되나요?**
    1. 조직의 기본 테넌트에서 사용자 계정을 사용하여 [Azure AD 포털](https://aad.portal.azure.com)에 로그인합니다.
    1. [역할 관리](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RolesAndAdministrators)로 이동합니다.
    1. 원하는 관리자 역할을 클릭합니다.
    1. 해당 역할에 할당된 사용자의 목록이 표시됩니다.

- **내 MPN 계정에 대한 관리자가 누구인지 알 수 없습니다.** [MPN 사용자 관리 페이지](https://partner.microsoft.com/pcv/users)로 이동하고, 사용자 목록을 필터링하여 다양한 관리 역할에 속한 사용자를 확인합니다.

- **MPN ID가 잘못되었거나 액세스할 수 없다는 오류가 발생합니다.**
    1. [파트너 프로필](https://partner.microsoft.com/pcv/accountsettings/connectedpartnerprofile)로 이동하여 다음을 확인합니다. 
        - MPN ID가 올바릅니다. 
        - 오류 또는 "보류 중인 작업"이 표시되지 않으며, 법적 비즈니스 프로필 및 파트너 정보 아래에서 확인 상태가 모두 "권한 있음" 또는 "성공"으로 표시됩니다.
    1. [MPN 테넌트 관리 페이지](https://partner.microsoft.com/dashboard/account/v3/tenantmanagement)로 이동하여 앱이 등록되어 있고 사용자 계정으로 서명하는 테넌트가 연결된 테넌트 목록에 있는지 확인합니다. 추가 테넌트를 추가하려면 [여기](/partner-center/multi-tenant-account)의 지침을 따르세요. 추가하는 테넌트의 모든 전역 관리자에게는 파트너 센터 계정에 대한 전역 관리자 권한이 부여됩니다.
    1. [MPN 사용자 관리 페이지](https://partner.microsoft.com/pcv/users)로 이동하여 로그인하는 사용자가 전역 관리자, MPN 관리자 또는 계정 관리자인지 확인합니다. 파트너 센터에서 사용자를 역할에 추가하려면, [여기](/partner-center/create-user-accounts-and-set-permissions)에 있는 지침을 따르세요.

- **Azure AD 포털에 로그인하면 등록된 앱이 표시되지 않습니다. 그 이유는 무엇일까요?** 
    앱 등록이 다른 사용자 계정을 사용하여 만들어졌거나 다른 테넌트에 만들어졌을 수 있습니다. 앱 등록을 만든 테넌트에서 올바른 계정으로 로그인했는지 확인합니다.

- **다단계 인증과 관련된 오류가 발생합니다. 제가 뭘 해야 하나요?** 
    함께 로그인하는 사용자에 대해 그리고 이 시나리오에 대해 [다단계 인증](../fundamentals/concept-fundamentals-mfa-get-started.md)이 사용으로 설정되고 이 인증이 **요구되는지** 를 확인하세요. 예를 들어, MFA는 다음과 같을 수 있습니다.
    - 함께 로그인하는 사용자에 대해 항상 필요
    - [Azure 관리에 필요](../conditional-access/howto-conditional-access-policy-azure-management.md)
    - 함께 로그인하는 [관리자 유형에 필요](../conditional-access/howto-conditional-access-policy-admin-mfa.md)합니다.

## <a name="making-microsoft-graph-api-calls"></a>Microsoft Graph API 호출 

문제가 있지만 UI에 표시되는 내용에 따라 이유를 이해할 수 없는 경우 Microsoft Graph 호출을 통해 앱 등록 포털에서 수행할 수 있는 것과 동일한 작업을 수행하여 문제 해결을 추가로 수행하는 것이 유용할 수 있습니다.

이러한 요청을 수행하는 가장 쉬운 방법은 [Graph Explorer](https://developer.microsoft.com/graph/graph-explorer)를 사용하는 것입니다. [Postman](https://www.postman.com/) 또는 PowerShell을 사용하여 [웹 요청을 호출](/powershell/module/microsoft.powershell.utility/invoke-webrequest)하는 것과 같은 다른 옵션을 고려할 수도 있습니다.  

Microsoft Graph를 사용하여 앱의 확인된 게시자를 설정하거나 해제하고 이러한 작업 중 하나를 수행한 후 결과를 확인할 수 있습니다. 결과는 앱 등록에 해당하는 [애플리케이션](/graph/api/resources/application) 개체와 해당 앱에서 인스턴스화된 [서비스 주체](/graph/api/resources/serviceprincipal) 모두에서 볼 수 있습니다. 이러한 개체 간의 관계에 대한 자세한 내용은 [Azure Active Directory의 애플리케이션 및 서비스 주체 개체](app-objects-and-service-principals.md)를 참조하세요.  

몇 가지 유용한 요청의 예제는 다음과 같습니다.  

### <a name="set-verified-publisher"></a>확인된 게시자 설정 

요청

```
POST /applications/0cd04273-0d11-4e62-9eb3-5c3971a7cbec/setVerifiedPublisher 

{ 

    "verifiedPublisherId": "12345678" 

} 
```
 
응답 
```
204 No Content 
```
> [!NOTE]
> *verifiedPublisherID* 는 MPN ID입니다. 

### <a name="unset-verified-publisher"></a>확인된 게시자 설정 해제 

요청:  
```
POST /applications/0cd04273-0d11-4e62-9eb3-5c3971a7cbec/unsetVerifiedPublisher 
```
 
응답 
```
204 No Content 
```
### <a name="get-verified-publisher-info-from-application"></a>애플리케이션에서 확인된 게시자 정보 가져오기 
 
```
GET https://graph.microsoft.com/v1.0/applications/0cd04273-0d11-4e62-9eb3-5c3971a7cbec 

HTTP/1.1 200 OK 

{ 
    "id": "0cd04273-0d11-4e62-9eb3-5c3971a7cbec", 

    ... 

    "verifiedPublisher" : { 
        "displayName": "myexamplePublisher", 
        "verifiedPublisherId": "12345678", 
        "addedDateTime": "2019-12-10T00:00:00" 
    } 
} 
```

### <a name="get-verified-publisher-info-from-service-principal"></a>서비스 주체에서 확인된 게시자 정보 가져오기 
```
GET https://graph.microsoft.com/v1.0/servicePrincipals/010422a7-4d77-4f40-9335-b81ef5c23dd4 

HTTP/1.1 200 OK 

{ 
    "id": "010422a7-4d77-4f40-9335-b81ef5c22dd4", 

    ... 

    "verifiedPublisher" : { 
        "displayName": "myexamplePublisher", 
        "verifiedPublisherId": "12345678", 
        "addedDateTime": "2019-12-10T00:00:00" 
    } 
} 
```

## <a name="error-reference"></a>오류 참조 

Microsoft Graph를 사용하여 문제를 해결하거나 앱 등록 포털에서 프로세스를 진행하는 경우 받을 수 있는 잠재적인 오류 코드의 목록은 다음과 같습니다.

### <a name="mpnaccountnotfoundornoaccess"></a>MPNAccountNotFoundOrNoAccess

제공한 MPN ID(`MPNID`)가 없거나 이에 액세스할 수 없습니다. 유효한 MPN ID를 제공하고 다시 시도하세요.
    
로그인한 사용자가 파트너 센터에서 MPN 계정에 대한 적절한 역할의 멤버가 아닌 경우 가장 일반적으로 발생하는 항목입니다. 자세한 내용은 적합한 역할 목록의 [요구 사항](publisher-verification-overview.md#requirements) 및 [일반적인 문제](#common-issues)를 참조하세요. 앱이 등록된 테넌트가 MPN 계정에 추가되지 않았거나 잘못된 MPN ID로 인해 발생할 수도 있습니다.

### <a name="mpnglobalaccountnotfound"></a>MPNGlobalAccountNotFound

제공한 MPN ID(`MPNID`)가 잘못되었습니다. 유효한 MPN ID를 제공하고 다시 시도하세요.
    
가장 일반적으로는 PLA(파트너 위치 계정)에 해당하는 MPN ID가 제공될 때 발생합니다. 파트너 전역 계정만 지원됩니다. 자세한 내용은 [파트너 센터 계정 구조체](/partner-center/account-structure)를 참조하세요.

### <a name="mpnaccountinvalid"></a>MPNAccountInvalid

제공한 MPN ID(`MPNID`)가 잘못되었습니다. 유효한 MPN ID를 제공하고 다시 시도하세요.
    
잘못된 MPN ID가 제공되는 경우 가장 일반적으로 발생합니다.

### <a name="mpnaccountnotvetted"></a>MPNAccountNotVetted

제공한 MPN ID(`MPNID`)에서 심사 프로세스를 완료하지 않았습니다. 파트너 센터에서 이 프로세스를 완료하고 다시 시도하세요. 
    
MPN 계정이 [확인](/partner-center/verification-responses) 프로세스를 완료하지 않은 경우에 가장 일반적으로 발생합니다.

### <a name="nopublisheridonassociatedmpnaccount"></a>NoPublisherIdOnAssociatedMPNAccount

제공한 MPN ID(`MPNID`)가 잘못되었습니다. 유효한 MPN ID를 제공하고 다시 시도하세요. 
   
잘못된 MPN ID가 제공되는 경우 가장 일반적으로 발생합니다.

### <a name="mpniddoesnotmatchassociatedmpnaccount"></a>MPNIdDoesNotMatchAssociatedMPNAccount

제공한 MPN ID(`MPNID`)가 잘못되었습니다. 유효한 MPN ID를 제공하고 다시 시도하세요.
    
잘못된 MPN ID가 제공되는 경우 가장 일반적으로 발생합니다.

### <a name="applicationnotfound"></a>ApplicationNotFound

대상 애플리케이션(`AppId`)을 찾을 수 없습니다. 유효한 애플리케이션 ID를 제공하고 다시 시도하세요.
    
가장 일반적으로는 Graph API을 통해 확인을 수행하고, 제공된 애플리케이션의 ID가 올바르지 않은 경우에 발생합니다. 참고 - AppId/ClientId가 아니라 애플리케이션의 ID가 제공되어야 합니다.

### <a name="b2ctenantnotallowed"></a>B2CTenantNotAllowed

이 기능은 Azure AD B2C 테넌트에서 지원되지 않습니다.

### <a name="emailverifiedtenantnotallowed"></a>EmailVerifiedTenantNotAllowed

이 기능은 이메일로 확인된 테넌트에서 지원되지 않습니다.

### <a name="nopublisherdomainonapplication"></a>NoPublisherDomainOnApplication

게시자 도메인이 대상 애플리케이션(`AppId`)에 설정되어 있어야 합니다. 게시자 도메인을 설정하고 다시 시도하세요.

앱에서 [게시자 도메인](howto-configure-publisher-domain.md)이 구성되지 않은 경우에 발생합니다.

### <a name="publisherdomainmismatch"></a>PublisherDomainMismatch

애플리케이션의 게시자 도메인(`publisherDomain`)이 파트너 센터에서 이메일 확인을 수행하는 데 사용되는 도메인(`pcDomain`)과 일치하지 않습니다. 이러한 도메인이 일치하는지 확인하고 다시 시도하세요. 
    
앱의 [게시자 도메인](howto-configure-publisher-domain.md) 또는 Azure AD 테넌트에 추가된 [사용자 지정 도메인](../fundamentals/add-custom-domain.md) 중 하나가 파트너 센터에서 전자 메일 확인을 수행하는 데 사용되는 도메인과 일치하지 않는 경우에 발생합니다.

### <a name="notauthorizedtoverifypublisher"></a>NotAuthorizedToVerifyPublisher

애플리케이션(<`AppId`)에서 확인된 게시자 속성을 설정할 수 있는 권한이 없습니다. 
  
로그인한 사용자가 Azure AD에서 MPN 계정에 대한 적절한 역할의 멤버가 아닌 경우 가장 일반적으로 발생하는 항목입니다. 자세한 내용은 적합한 역할 목록의 [요구 사항](publisher-verification-overview.md#requirements) 및 [일반적인 문제](#common-issues)를 참조하세요.

### <a name="mpnidwasnotprovided"></a>MPNIdWasNotProvided

MPN ID가 요청 본문에 제공되지 않았거나 요청 콘텐츠 형식이 "application/json"이 아닙니다.

### <a name="msanotsupported"></a>MSANotSupported 

이 기능은 Microsoft 소비자 계정에서 지원되지 않습니다. Azure AD 사용자가 Azure AD에 등록한 애플리케이션만 지원됩니다.

### <a name="interactionrequired"></a>InteractionRequired

앱에 확인된 게시자를 추가하기 전에 다단계 인증을 수행하지 않은 경우에 발생합니다. 자세한 내용은 [일반적인 문제](#common-issues)를 참조하세요. 참고: MFA는 확인된 게시자를 추가하려고 할 때 동일한 세션에서 수행해야 합니다. MFA를 사용하도록 설정 했지만 세션에서 수행할 필요가 없는 경우, 요청이 실패합니다. 

표시되는 오류 메시지: “관리자가 구성을 변경했거나 사용자가 새 위치로 이동했기 때문에, 계속 진행하려면 다단계 인증을 사용해야 합니다.”

### <a name="unabletoaddpublisher"></a>UnableToAddPublisher

다음 오류 메시지 중 하나가 표시됩니다. "확인된 게시자를 이 애플리케이션에 추가할 수 없습니다. 도움이 필요하면 관리자에게 문의하세요." 또는 "이 애플리케이션에 확인된 게시자를 추가할 수 없습니다. 도움이 필요하면 관리자에게 문의하세요."

먼저 [게시자 확인 요구 사항](publisher-verification-overview.md#requirements)을 충족 했는지 확인합니다.

확인된 게시자를 추가하라는 요청이 생성되면 보안 위험 평가를 위해 많은 신호가 사용됩니다. 요청이 위험한 것으로 확인되면 오류가 반환됩니다. 보안상의 이유로 Microsoft는 요청이 위험한지 여부를 판단하는 데 사용되는 특정 기준을 공개하지 않습니다. 이 오류가 수신되고 "위험" 평가가 잘못되었다고 생각되면 기다렸다가 확인 요청을 다시 제출해 보세요. 일부 고객은 여러 번의 시도 후에 성공을 보고했습니다.

## <a name="next-steps"></a>다음 단계

이전 정보를 모두 검토했지만 여전히 Microsoft Graph에서 오류를 수신하는 경우 실패한 요청과 관련하여 다음 정보를 최대한 많이 수집하고 [Microsoft 지원에 문의](developer-support-help-options.md#create-an-azure-support-request)하세요.

- 타임스탬프 
- CorrelationId 
- 로그인한 사용자의 ObjectID 또는 UserPrincipalName 
- 대상 애플리케이션의 ObjectId
- 대상 애플리케이션의 AppId
- 앱이 등록된 TenantId
- MPN ID
- 만들고 있는 REST 요청 
- 반환되는 오류 코드 및 메시지
