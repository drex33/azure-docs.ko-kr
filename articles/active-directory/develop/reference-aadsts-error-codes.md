---
title: Azure AD 인증 및 권한 부여 오류 코드
description: Azure AD STS(보안 토큰 서비스)에서 반환된 AADSTS 오류 코드에 대해 알아봅니다.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: reference
ms.date: 07/28/2021
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: 3b1d7d8b658e0a0ac01789ca8a13ce0a2f779767
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122528632"
---
# <a name="azure-ad-authentication-and-authorization-error-codes"></a>Azure AD 인증 및 권한 부여 오류 코드

Azure AD STS(보안 토큰 서비스)에서 반환된 AADSTS 오류 코드에 대한 정보를 찾고 있나요? 이 문서에서 AADSTS 오류 설명, 수정 및 일부 제안된 해결 방법을 확인해 보세요.

> [!NOTE]
> 이 정보는 임시로 제공되며 변경될 수 있습니다. 질문이 있거나 원하는 항목을 찾을 수 없나요? GitHub 문제를 만들거나 [개발자를 위한 지원 및 도움말 옵션](./developer-support-help-options.md)을 확인하여 도움말 및 지원을 얻을 수 있는 다른 방법을 알아보세요.
>
> 이 설명서는 개발자 및 관리자 참고 자료 목적으로 제공되지만 클라이언트 자체에서 사용해서는 안 됩니다. 오류 코드는 개발자가 애플리케이션을 빌드하는 동안 도움이 되도록 보다 세부적인 오류 메시지를 제공하기 위해 언제든지 변경될 수 있습니다. 텍스트 또는 오류 코드 번호에 종속되는 앱은 시간이 지나면서 중단됩니다.

## <a name="handling-error-codes-in-your-application"></a>애플리케이션의 오류 코드 처리

[OAuth2.0 사양](https://tools.ietf.org/html/rfc6749#section-5.2)은 오류 응답의 `error` 부분을 사용하여 인증 중에 오류를 처리하는 방법에 대한 지침을 제공합니다. 

다음은 샘플 오류 응답입니다.

```json
{
  "error": "invalid_scope",
  "error_description": "AADSTS70011: The provided value for the input parameter 'scope' is not valid. The scope https://example.contoso.com/activity.read is not valid.\r\nTrace ID: 255d1aef-8c98-452f-ac51-23d051240864\r\nCorrelation ID: fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7\r\nTimestamp: 2016-01-09 02:02:12Z",
  "error_codes": [
    70011
  ],
  "timestamp": "2016-01-09 02:02:12Z",
  "trace_id": "255d1aef-8c98-452f-ac51-23d051240864",
  "correlation_id": "fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7", 
  "error_uri":"https://login.microsoftonline.com/error?code=70011"
}
```

| 매개 변수         | 설명    |
|-------------------|----------------|
| `error`       | 발생하는 오류 유형을 분류하는 데 사용할 수 있고 오류에 대응하는 데 사용할 수 있는 오류 코드 문자열입니다. |
| `error_description` | 개발자가 인증 오류의 근본 원인을 식별하도록 도울 수 있는 특정 오류 메시지입니다. 절대 이 필드를 사용하여 코드 오류에 대응하지 마십시오. |
| `error_codes` | 진단에 도움이 될 수 있는 STS 특정 오류 코드의 목록입니다.  |
| `timestamp`   | 오류가 발생한 시간입니다. |
| `trace_id`    | 진단에 도움이 될 수 있는 요청에 대한 고유 식별자입니다. |
| `correlation_id` | 여러 구성 요소에서 진단에 도움이 될 수 있는 요청에 대한 고유 식별자입니다. |
| `error_uri` |  오류에 대한 추가 정보를 포함하는 오류 조회 페이지에 대한 링크입니다.  개발자 전용이므로 사용자에게 제시하지 마세요.  오류 조회 시스템에 오류에 대한 추가 정보가 있는 경우에만 표시됩니다. 모든 오류에 추가 정보가 제공되지는 않습니다.|

`error` 필드에는 몇 가지 가능한 값이 있습니다. 프로토콜 설명서 링크 및 OAuth 2.0 사양을 검토하여 특정 오류(예: [디바이스 코드 흐름](v2-oauth2-device-code.md) 내부의 `authorization_pending`) 및 이에 대응하는 방법에 대해 자세히 알아보세요.  몇 가지 일반적인 항목이 여기에 나열되어 있습니다.

| 오류 코드         | Description        | 클라이언트 작업    |
|--------------------|--------------------|------------------|
| `invalid_request`  | 프로토콜 오류(예: 필수 매개 변수 누락). | 요청을 수정하여 다시 제출하십시오.|
| `invalid_grant`    | 일부 인증 자료(인증 코드, 새로 고침 토큰, 액세스 토큰, PKCE 챌린지)가 잘못되었거나, 구문 분석할 수 없거나, 누락되었거나 사용할 수 없는 경우 | 새 인증 코드를 가져오려면 `/authorize` 엔드포인트에 새 요청을 보냅니다.  앱의 프로토콜의 사용을 검토하고 유효성을 검사하는 게 좋습니다. |
| `unauthorized_client` | 인증된 클라이언트가 이 권한 부여 유형을 사용할 권한이 없습니다. | 이 오류는 일반적으로 클라이언트 애플리케이션이 Azure AD에 등록되지 않았거나 사용자의 Azure AD 테넌트에 추가되지 않은 경우 발생합니다. 애플리케이션이 사용자에게 애플리케이션을 설치하고 Azure AD에 추가하기 위한 지침이 포함된 메시지를 표시할 수 있습니다. |
| `invalid_client` | 클라이언트 인증에 실패했습니다.  | 클라이언트 자격 증명이 잘못되었습니다. 해결하려면 애플리케이션 관리자가 자격 증명을 업데이트합니다.   |
| `unsupported_grant_type` | 권한 부여 서버가 해당 권한 부여 유형을 지원하지 않습니다. | 요청에서 권한 부여 유형을 변경하십시오. 이 유형의 오류는 개발 중에만 발생하며 초기 테스트 중에 검색됩니다. |
| `invalid_resource` | 대상 리소스가 존재하지 않거나 Azure AD에서 해당 리소스를 찾을 수 없거나 올바르게 구성되지 않았기 때문에 잘못되었습니다. | 리소스가 존재하는 경우 테넌트에 구성되지 않았음을 나타냅니다. 애플리케이션이 사용자에게 애플리케이션을 설치하고 Azure AD에 추가하기 위한 지침이 포함된 메시지를 표시할 수 있습니다.  개발 중에 이는 일반적으로 잘못 설정된 테스트 테넌트 또는 요청된 범위 이름의 오타를 나타냅니다. |
| `interaction_required` | 요청을 위해 사용자 상호 작용이 필요합니다. 예를 들어 추가 인증 단계가 필요합니다. | 사용자가 필요한 모든 문제를 해결할 수 있도록 대화형으로 동일한 리소스를 사용해 다시 요청합니다.  |
| `temporarily_unavailable` | 서버가 일시적으로 사용량이 많아 요청을 처리할 수 없습니다. | 요청을 다시 시도하십시오. 클라이언트 애플리케이션이 일시적 상태 때문에 응답이 지연되었음을 사용자에게 설명할 수 있습니다. |

## <a name="lookup-current-error-code-information"></a>현재 오류 코드 정보 조회
오류 코드와 메시지는 변경될 수 있습니다.  최신 정보를 보려면 [https://login.microsoftonline.com/error](https://login.microsoftonline.com/error) 페이지에서 AADSTS 오류 설명, 픽스 및 제안된 몇 가지 해결 방법을 살펴보세요.  

예를 들어 “AADSTS50058” 오류 코드를 받은 경우 “50058”에 대한 [https://login.microsoftonline.com/error](https://login.microsoftonline.com/error)에서 검색을 합니다.  URL에 [https://login.microsoftonline.com/error?code=50058](https://login.microsoftonline.com/error?code=50058)와 같이 오류 코드 번호를 추가하여 특정 오류에 직접 연결할 수도 있습니다.

## <a name="aadsts-error-codes"></a>AADSTS 오류 코드

| Error | 설명 |
|---|---|
| AADSTS16000 | SelectUserAccount - 이것은 Azure AD에 의해 throw된 인터럽트로, 사용자가 여러 개의 유효한 SSO 세션 중에서 선택할 수 있는 UI가 나타납니다. 이 오류는 매우 일반적이며 `prompt=none`이 지정된 경우 애플리케이션에 반환될 수 있습니다. |
| AADSTS16001 | UserAccountSelectionInvalid - 사용자가 세션 선택 논리가 거부한 타일을 클릭하면 이 오류가 표시됩니다. 이 오류가 트리거되면 사용자는 업데이트된 타일/세션 목록에서 선택하거나 다른 계정을 선택하여 복구할 수 있습니다. 이 오류는 코드 결함 또는 경합 상태로 인해 발생할 수 있습니다. |
| AADSTS16002 | AppSessionSelectionInvalid - 앱 지정 SID 요구 사항이 충족되지 않았습니다.  |
| AADSTS16003 | SsoUserAccountNotFoundInResourceTenant - 사용자가 테넌트에 명시적으로 추가되지 않았음을 나타냅니다. |
| AADSTS17003 | CredentialKeyProvisioningFailed - Azure AD에서 사용자 키를 프로비전할 수 없습니다. |
| AADSTS20001 | WsFedSignInResponseError - 페더레이션 ID 공급자에 문제가 있습니다. 이 문제를 해결하려면 IDP에 문의하세요. |
| AADSTS20012 | WsFedMessageInvalid - 페더레이션 ID 공급자에 문제가 있습니다. 이 문제를 해결하려면 IDP에 문의하세요. |
| AADSTS20033 | FedMetadataInvalidTenantName - 페더레이션 ID 공급자에 문제가 있습니다. 이 문제를 해결하려면 IDP에 문의하세요. |
| AADSTS40008 | OAuth2IdPUnretryableServerError - 페더레이션 ID 공급자에 문제가 있습니다. 이 문제를 해결하려면 IDP에 문의하세요. |
| AADSTS40009 | OAuth2IdPRefreshTokenRedemptionUserError - 페더레이션 ID 공급자에 문제가 있습니다. 이 문제를 해결하려면 IDP에 문의하세요. |
| AADSTS40010 | OAuth2IdPRetryableServerError - 페더레이션 ID 공급자에 문제가 있습니다. 이 문제를 해결하려면 IDP에 문의하세요. |
| AADSTS40015 | OAuth2IdPAuthCodeRedemptionUserError - 페더레이션 ID 공급자에 문제가 있습니다. 이 문제를 해결하려면 IDP에 문의하세요. |
| AADSTS50000 | TokenIssuanceError - 로그인 서비스에 문제가 있습니다. 이 문제를 해결하려면 [지원 티켓을 여세요](../fundamentals/active-directory-troubleshooting-support-howto.md). |
| AADSTS50001 | InvalidResource - 리소스를 사용할 수 없거나 존재하지 않습니다. 앱 코드를 확인하여 액세스하려는 리소스의 정확한 리소스 URL을 지정했는지 확인하세요.  |
| AADSTS50002 | NotAllowedTenant - 테넌트의 제한된 프록시 액세스로 인해 로그인하지 못했습니다. 사용자 고유의 테넌트 정책인 경우 제한된 테넌트 설정을 변경하여 문제를 해결할 수 있습니다. |
| AADSTS500021 | ‘{Tenant}’ 테넌트에 대한 액세스가 거부되었습니다. AADSTS500021는 테넌트 제한 기능이 구성되었고 사용자가 `Restrict-Access-To-Tenant` 헤더에 지정된 허용되는 테넌트 목록에 없는 테넌트에 액세스하려고 시도하고 있음을 나타냅니다. 더 많은 정보는 [테넌트 제한을 사용하여 SaaS 클라우드 애플리케이션에 대한 액세스 관리](../manage-apps/tenant-restrictions.md)를 참조하세요.|
| AADSTS50003 | MissingSigningKey - 서명 키 또는 인증서의 누락으로 인해 로그인하지 못했습니다. 이 오류는 앱에 구성된 서명 키가 없기 때문일 수 있습니다. 자세히 알아보려면 오류 [AADSTS50003](/troubleshoot/azure/active-directory/error-code-aadsts50003-cert-or-key-not-configured)에 대한 문제 해결 문서를 참조하세요. 그래도 문제가 계속되면 앱 소유자 또는 앱 관리자에게 문의하세요. |
| AADSTS50005 | DevicePolicyError - 사용자가 현재 조건부 액세스 정책을 통해 지원되지 않는 플랫폼에서 디바이스에 로그인하려고 했습니다. |
| AADSTS50006 | InvalidSignature - 잘못된 서명으로 인해 서명을 확인하지 못했습니다. |
| AADSTS50007 | PartnerEncryptionCertificateMissing - 이 앱에 대한 파트너 암호화 인증서를 찾을 수 없습니다. 이 문제를 해결하려면 Microsoft에서 [지원 티켓을 여세요](../fundamentals/active-directory-troubleshooting-support-howto.md). |
| AADSTS50008 | InvalidSamlToken - 토큰에서 SAML 어설션이 누락되었거나 잘못 구성되었습니다. 페더레이션 공급자에게 문의하세요. |
| AADSTS50010 | AudienceUriValidationFailed - 토큰 대상 그룹이 구성되지 않았으므로 앱에 대한 대상 그룹 URI 유효성 검사가 실패했습니다. |
| AADSTS50011 | InvalidReplyTo - 회신 주소가 누락되었거나, 잘못 구성되었거나, 앱에 대해 구성된 회신 주소와 일치하지 않습니다.  해결 방법으로, 누락된 이 회신 주소를 Azure Active Directory 애플리케이션에 추가하거나 Active Directory에서 애플리케이션을 관리할 권한이 있는 사람이 이 작업을 수행하도록 합니다. 자세히 알아보려면 오류 [AADSTS50011](/troubleshoot/azure/active-directory/error-code-aadsts50011-reply-url-mismatch)에 대한 문제 해결 문서를 참조하세요.|
| AADSTS50012 | AuthenticationFailed - 다음 이유 중 하나로 인해 인증에 실패했습니다.<ul><li>서명 인증서의 주체 이름이 인증되지 않았습니다.</li><li>인증된 주체 이름에 대해 일치하는 신뢰할 수 있는 인증 기관 정책을 찾을 수 없습니다.</li><li>인증서 체인이 유효하지 않습니다.</li><li>서명 인증서가 유효하지 않습니다.</li><li>테넌트에서 정책이 구성되지 않았습니다.</li><li>서명 인증서의 지문이 인증되지 않았습니다.</li><li>클라이언트 어설션에 잘못된 서명이 들어 있습니다.</li></ul> |
| AADSTS50013 | InvalidAssertion - 어설션이 여러 가지 이유로 유효하지 않습니다. 즉 토큰 발급자가 유효한 시간 범위 내의 API 버전과 일치하지 않거나, 만료되었거나, 잘못된 형식으로 되어 있습니다. 또는 어설션의 새로 고침 토큰이 주 새로 고침 토큰이 아닙니다. |
| AADSTS50014 | GuestUserInPendingState - 사용자의 사용이 보류 상태입니다. 게스트 사용자 계정이 아직 완전히 생성되지 않았습니다. |
| AADSTS50015 | ViralUserLegalAgeConsentRequiredState - 사용자에게 법적 연령 그룹 동의가 필요합니다. |
| AADSTS50017 | CertificateValidationFailed - 다음과 같은 이유로 인증 유효성 검사에 실패했습니다.<ul><li>신뢰할 수 있는 인증서 목록에서 발급한 인증서를 찾을 수 없습니다.</li><li>필요한 CrlSegment를 찾을 수 없습니다.</li><li>신뢰할 수 있는 인증서 목록에서 발급한 인증서를 찾을 수 없습니다.</li><li>델타 CRL 배포 지점이 해당 CRL 배포 지점 없이 구성되어 있습니다.</li><li>시간 제한 문제로 인해 유효한 CRL 세그먼트를 검색할 수 없습니다.</li><li>CRL을 다운로드할 수 없습니다.</li></ul>테넌트 관리자에게 문의하세요. |
| AADSTS50020 | UserUnauthorized - 사용자가 이 엔드포인트를 호출할 권한이 없습니다. |
| AADSTS50027 | InvalidJwtToken - 다음과 같은 이유로 JWT 토큰이 잘못되었습니다.<ul><li>nonce 클레임 또는 sub 클레임이 포함되지 않았습니다.</li><li>주체 식별자가 일치하지 않습니다.</li><li>idToken 클레임에 중복된 클레임이 있습니다.</li><li>예기치 않은 발급자입니다.</li><li>예기치 않은 대상 그룹입니다.</li><li>유효한 시간 범위 내에 있지 않습니다. </li><li>토큰 형식이 잘못되었습니다.</li><li>발급자의 외부 ID 토큰에서 서명을 확인하지 못했습니다.</li></ul> |
| AADSTS50029 | URI가 잘못되었습니다. 도메인 이름에 잘못된 문자가 있습니다. 테넌트 관리자에게 문의하세요. |
| AADSTS50032 | WeakRsaKey - 잘못된 사용자가 약한 RSA 키를 사용하려고 했음을 나타냅니다. |
| AADSTS50033 | RetryableError - 데이터베이스 작업과 관련이 없는 일시적인 오류를 나타냅니다. |
| AADSTS50034 | UserAccountNotFound - 이 애플리케이션에 로그인하려면 계정을 해당 디렉터리에 추가해야 합니다. |
| AADSTS50042 | UnableToGeneratePairwiseIdentifierWithMissingSalt - 쌍으로 된 식별자를 생성하는 데 필요한 솔트가 원칙적으로 없습니다. 테넌트 관리자에게 문의하세요. |
| AADSTS50043 | UnableToGeneratePairwiseIdentifierWithMultipleSalts |
| AADSTS50048 | SubjectMismatchesIssuer - 주체가 클라이언트 어설션의 발급자 클레임과 일치하지 않습니다. 테넌트 관리자에게 문의하세요. |
| AADSTS50049 | NoSuchInstanceForDiscovery - 알 수 없거나 잘못된 인스턴스입니다. |
| AADSTS50050 | MalformedDiscoveryRequest - 요청 형식이 잘못되었습니다. |
| AADSTS50053 | 이 오류는 두 가지 다른 이유로 인해 발생할 수 있습니다. <br><ul><li>IdsLocked - 잘못된 사용자 ID 또는 암호로 로그인을 너무 많이 시도하여 계정이 잠겨 있습니다. 반복적인 로그인 시도로 인해 사용자가 차단되었습니다. [위험 재구성 및 사용자 차단 해제](../identity-protection/howto-identity-protection-remediate-unblock.md)를 참조하세요.</li><li>또는 악의적인 활동이 있는 IP 주소에서 제공되었으므로 로그인이 차단되었습니다.</li></ul> <br>이 오류를 발생시킨 실패 이유를 확인하려면 [Azure Portal](https://portal.azure.com)에 로그인합니다.  Azure AD 테넌트로 이동한 다음 **모니터링** -> **로그인** 으로 이동합니다. **로그인 오류 코드** 50053으로 실패한 사용자 로그인을 찾아 **실패 이유** 를 확인합니다.|
| AADSTS50055 | InvalidPasswordExpiredPassword - 암호가 만료되었습니다. 사용자의 암호가 만료되어 로그인 또는 세션이 종료되었습니다. 사용자가 다시 설정하거나 [Azure Active Directory를 사용하여 사용자의 암호 다시 설정](../fundamentals/active-directory-users-reset-password-azure-portal.md)을 통해 관리자에게 다시 설정하도록 요청할 수 있습니다. |
| AADSTS50056 | 잘못된 암호 또는 Null 암호: 이 사용자에 대한 디렉터리에 암호가 없습니다. 사용자에게 암호를 다시 입력하라는 메시지가 표시됩니다. |
| AADSTS50057 | UserDisabled - 사용자 계정이 비활성화됩니다. 이 계정을 지원하는 Active Directory의 사용자 개체가 사용하지 않도록 설정되었습니다. 관리자는 [Powershell을 통해](/powershell/module/activedirectory/enable-adaccount) 이 계정을 다시 사용하도록 설정할 수 있습니다. |
| AADSTS50058 | UserInformationNotProvided - Single-Sign-on의 세션 정보가 부족합니다. 이는 사용자가 로그인하지 않았음을 의미합니다. 이는 사용자가 인증되지 않았고 아직 로그인하지 않은 경우에 발생하는 일반적인 오류입니다.</br>사용자가 이전에 로그인한 SSO 컨텍스트에서 이 오류가 발생하는 경우 SSO 세션을 찾을 수 없거나 SSO 세션이 잘못되었음을 의미합니다.</br>이 오류는 prompt=none이 지정된 경우 애플리케이션에 반환될 수 있습니다. |
| AADSTS50059 | MissingTenantRealmAndNoUserInformationProvided - 테넌트를 식별하는 정보가 요청에 없거나 제공된 자격 증명으로 암시되지 않습니다. 사용자는 문제를 해결하기 위해 테넌트 관리자에게 문의할 수 있습니다. |
| AADSTS50061 | SignoutInvalidRequest - 로그아웃을 완료할 수 없습니다. 요청이 잘못되었습니다. |
| AADSTS50064 | CredentialAuthenticationError - 사용자 이름 또는 암호에 대한 자격 증명 유효성 검사에 실패했습니다. |
| AADSTS50068 | SignoutInitiatorNotParticipant - 로그아웃에 실패했습니다. 로그아웃을 시작한 앱은 현재 세션의 참가자가 아닙니다. |
| AADSTS50070 | SignoutUnknownSessionIdentifier - 로그아웃에 실패했습니다. 로그아웃 요청에서 지정된 이름의 식별자가 기존 세션과 일치하지 않습니다. |
| AADSTS50071 | SignoutMessageExpired - 로그아웃 요청이 만료되었습니다. |
| AADSTS50072 | UserStrongAuthEnrollmentRequiredInterrupt - 사용자는 2단계 인증(대화형)에 등록해야 합니다. |
| AADSTS50074 | UserStrongAuthClientAuthNRequiredInterrupt - 강력한 인증이 필요하며 사용자가 MFA 챌린지를 통과하지 못했습니다. |
| AADSTS50076 | UserStrongAuthClientAuthNRequired - 관리자가 구성을 변경했거나 사용자가 새 위치로 이동했기 때문에 사용자가 리소스에 액세스하기 위해 다단계 인증을 사용해야 합니다. 리소스에 대한 새 권한 부여 요청을 사용하여 다시 시도하세요. |
| AADSTS50079 | UserStrongAuthEnrollmentRequired - 관리자가 구성을 변경했거나 사용자가 새 위치로 이동했기 때문에 사용자는 다단계 인증을 사용해야 합니다. |
| AADSTS50085 | 새로 고침 토큰에 소셜 IDP 로그인이 필요합니다. 사용자가 사용자 이름-암호를 사용하여 다시 로그인해 보세요. |
| AADSTS50086 | SasNonRetryableError |
| AADSTS50087 | SasRetryableError - 강력한 인증 중에 일시적인 오류가 발생했습니다. 다시 시도하세요. |
| AADSTS50088 | 통신 MFA 호출 제한에 도달했습니다. 몇 분 후에 다시 시도하세요. |
| AADSTS50089 | 흐름 토큰이 만료되어 인증에 실패했습니다. Expected - 인증 코드, 새로 고침 토큰 및 세션은 시간이 지남에 따라 만료되거나 사용자 또는 관리자에 의해 해제됩니다. 앱은 사용자에게 새 로그인을 요청합니다. |
| AADSTS50097 | DeviceAuthenticationRequired - 디바이스 인증이 필요합니다. |
| AADSTS50099 | PKeyAuthInvalidJwtUnauthorized - JWT 서명이 잘못되었습니다. |
| AADSTS50105 | EntitlementGrantsNotFound - 로그인한 사용자가 로그인한 앱의 역할에 할당되지 않았습니다. 앱에 사용자를 할당하세요. 자세히 알아보려면 오류 [AADSTS50105](/troubleshoot/azure/active-directory/error-code-aadsts50105-user-not-assigned-role)에 대한 문제 해결 문서를 참조하세요. |
| AADSTS50107 | InvalidRealmUri - 요청된 페더레이션 영역 개체가 존재하지 않습니다. 테넌트 관리자에게 문의하세요. |
| AADSTS50120 | ThresholdJwtInvalidJwtFormat - JWT 헤더에 문제가 있습니다. 테넌트 관리자에게 문의하세요. |
| AADSTS50124 | ClaimsTransformationInvalidInputParameter - 클레임 변환에 잘못된 입력 매개 변수가 있습니다. 테넌트 관리자에게 문의하여 정책을 업데이트하세요. |
| AADSTS501241 | 변환 ID '{transformId}'에 필수 입력 '{paramName}'이(가) 없습니다. 이 오류는 Azure AD가 애플리케이션에 대한 SAML 응답을 빌드하려고 시도하는 동안 반환됩니다. NameID 클레임 또는 NameIdentifier는 SAML 응답에서 필수이며 Azure AD가 NameID 클레임에 대한 원본 특성을 가져오지 못한 경우 이 오류를 반환합니다. 해결 방법으로 Azure Portal > Azure Active Directory > 엔터프라이즈 애플리케이션 > 애플리케이션 선택 > Single Sign-On > 사용자 특성 및 클레임 > 고유한 사용자 ID(이름 ID)에서 클레임 규칙을 추가합니다.  |
| AADSTS50125 | PasswordResetRegistrationRequiredInterrupt - 암호 재설정 또는 암호 등록 항목으로 인해 로그인이 중단되었습니다. |
| AADSTS50126 | InvalidUserNameOrPassword - 잘못된 사용자 이름 또는 암호로 인해 자격 증명 유효성 검사 오류가 발생했습니다. |
| AADSTS50127 | BrokerAppNotInstalled - 사용자는 이 콘텐츠에 액세스하기 위해 broker 앱을 설치해야 합니다. |
| AADSTS50128 | 도메인 이름이 잘못되었습니다. 테넌트를 식별하는 정보가 요청에 없거나 제공된 자격 증명으로 암시되지 않습니다. |
| AADSTS50129 | DeviceIsNotWorkplaceJoined - 작업 공간 연결에는 디바이스를 등록해야 합니다. |
| AADSTS50131 | ConditionalAccessFailed - Windows 장치 상태 불량, 의심스러운 활동으로 인해 차단된 요청, 액세스 정책 또는 보안 정책 결정과 같은 다양한 조건부 액세스 오류를 나타냅니다. |
| AADSTS50132 | SsoArtifactInvalidOrExpired - 암호 만료 또는 최근 암호 변경으로 인해 세션이 유효하지 않습니다. |
| AADSTS50133 | SsoArtifactRevoked - 암호 만료 또는 최근 암호 변경으로 인해 세션이 유효하지 않습니다. |
| AADSTS50134 | DeviceFlowAuthorizeWrongDatacenter - 데이터 센터가 잘못되었습니다. OAuth 2.0 디바이스 흐름에서 앱이 시작한 요청을 승인하려면 권한 부여 파티가 원래 요청이 있는 동일한 데이터 센터에 있어야 합니다. |
| AADSTS50135 | PasswordChangeCompromisedPassword - 계정이 위험하므로 암호를 변경해야 합니다. |
| AADSTS50136 | RedirectMsaSessionToApp - 단일 MSA 세션이 검색되었습니다. |
| AADSTS50139 | SessionMissingMsaOAuth2RefreshToken - 외부 새로 고침 토큰이 누락되어 세션이 잘못되었습니다. |
| AADSTS50140 | KmsiInterrupt - 사용자가 로그인할 때 "로그인 유지" 인터럽트로 인해 이 오류가 발생했습니다. 이는 예상되는 로그인 흐름의 일부로, 사용자에게 더 쉽게 로그인할 수 있도록 현재 브라우저에 로그인 상태를 유지할 것인지 묻는 메시지가 표시됩니다. 자세한 내용은 [새로운 Azure AD 로그인 및 “로그인 유지” 환경 출시!](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/the-new-azure-ad-sign-in-and-keep-me-signed-in-experiences/m-p/128267)를 참조하세요. 상관 ID, 요청 ID 및 오류 코드가 있는 [지원 티켓을 열면](../fundamentals/active-directory-troubleshooting-support-howto.md) 자세한 내용을 확인할 수 있습니다.|
| AADSTS50143 | 세션이 일치하지 않습니다. 사용자 테넌트가 다른 리소스로 인해 도메인 힌트와 일치하지 않기 때문에 세션이 유효하지 않습니다. 자세한 내용을 확인하려면 상관 ID, 요청 ID 및 오류 코드가 있는 [지원 티켓을 여세요](../fundamentals/active-directory-troubleshooting-support-howto.md). |
| AADSTS50144 | InvalidPasswordExpiredOnPremPassword - 사용자의 Active Directory 암호가 만료되었습니다. 사용자에 대한 새 암호를 생성하거나 사용자가 셀프 서비스 재설정 도구를 사용하여 암호를 다시 설정하도록 합니다. |
| AADSTS50146 | MissingCustomSigningKey - 이 앱은 앱별 서명 키로 구성해야 합니다. 응용 프로그램이 키로 구성되지 않았거나, 키가 만료되었거나 아직 유효하지 않습니다. |
| AADSTS50147 | MissingCodeChallenge - 코드 챌린지 매개 변수의 크기가 잘못되었습니다. |
| AADSTS50155 | DeviceAuthenticationFailed - 이 사용자에 대한 디바이스 인증에 실패했습니다. |
| AADSTS50158 | ExternalSecurityChallenge - 외부 보안 챌린지가 충족되지 않았습니다. |
| AADSTS50161 | InvalidExternalSecurityChallengeConfiguration - 외부 공급자가 보낸 클레임이 충분하지 않거나 외부 공급자에게 요청된 클레임이 누락되었습니다. |
| AADSTS50166 | ExternalClaimsProviderThrottled - 클레임 공급자에게 요청을 보내지 못했습니다. |
| AADSTS50168 | ChromeBrowserSsoInterruptRequired - 클라이언트는 Windows 10 계정 확장을 통해 SSO 토큰을 얻을 수 있지만 토큰을 요청에서 찾지 못했거나 제공된 토큰이 만료되었습니다. |
| AADSTS50169 | InvalidRequestBadRealm - 영역이 현재 서비스 네임스페이스의 구성된 영역이 아닙니다. |
| AADSTS50170 | MissingExternalClaimsProviderMapping - 외부 제어 매핑이 누락되었습니다. |
| AADSTS50173 | FreshTokenNeeded - 제공된 권한 부여가 철회되어 만료되었으며 새로운 인증 토큰이 필요합니다. 관리자 또는 사용자가 이 사용자의 토큰을 철회하여 후속 토큰 새로 고침이 실패했고 재인증이 필요합니다. 사용자가 다시 로그인을 해야 합니다. |
| AADSTS50177 | ExternalChallengeNotSupportedForPassthroughUsers - 통과 사용자에 대한 외부 챌린지는 지원하지 않습니다. |
| AADSTS50178 | SessionControlNotSupportedForPassthroughUsers - 통과 사용자에 대해서는 세션 컨트롤이 지원되지 않습니다. |
| AADSTS50180 | WindowsIntegratedAuthMissing - 통합된 Windows 인증이 필요합니다. Seamless SSO에 대한 테넌트를 사용하도록 설정합니다. |
| AADSTS50187 | DeviceInformationNotProvided - 서비스에서 디바이스 인증을 수행하지 못했습니다. |
| AADSTS50196 | LoopDetected - 클라이언트 루프가 검색되었습니다. 앱의 논리를 확인해 토큰 캐싱이 구현되었고 오류 조건이 올바르게 처리되었는지 확인합니다.  앱이 너무 짧은 기간 내에 너무 많은 요청을 수행하여 잘못된 상태가 됐거나 토큰 요청이 남용됐음을 나타냅니다. |
| AADSTS50197 | ConflictingIdentities - 사용자를 찾을 수 없습니다. 다시 로그인해 보세요. |
| AADSTS50199 | CmsiInterrupt - 보안상의 이유로 이 요청에는 사용자 확인이 필요합니다.  이 오류는 “interaction_required” 오류이기 때문에 클라이언트는 대화형 인증을 수행해야 합니다. 이는 시스템 웹 보기를 사용해 네이티브 애플리케이션에 대한 토큰을 요청했기 때문에 발생하며, 사용자에게 실제로 로그인하려는 앱인지 여부를 묻는 메시지를 표시해야 합니다. 이 메시지를 표시하지 않으려면 리디렉션 URI는 다음의 안전 목록에 포함되어야 합니다. <br />http://<br />https://<br />msauth://(iOS에만 해당)<br />msauthv2://(iOS에만 해당)<br />chrome-extension://(데스크톱 Chrome 브라우저만 해당) |
| AADSTS51000 | RequiredFeatureNotEnabled - 기능을 사용할 수 없습니다. |
| AADSTS51001 | DomainHintMustbePresent - 도메인 힌트에 온-프레미스 보안 식별자와 온-프레미스 UPN이 있어야 합니다. |
| AADSTS51004 | UserAccountNotInDirectory - 사용자 계정이 디렉터리에 존재하지 않습니다. |
| AADSTS51005 | TemporaryRedirect - 요청된 정보가 위치 헤더에 지정된 URI에 있음을 나타내는 HTTP 상태 307과 동일합니다. 이 상태를 수신하면 응답과 관련된 위치 헤더 뒤에 나옵니다. 원래의 요청 메서드가 POST이면 리디렉션된 요청도 POST 메서드를 사용합니다. |
| AADSTS51006 | ForceReauthDueToInsufficientAuth - 통합 Windows 인증이 필요합니다. 사용자가 통합 Windows 인증 클레임이 누락된 세션 토큰을 사용하여 로그인했습니다. 사용자가 다시 로그인하도록 요청합니다. |
| AADSTS52004 | DelegationDoesNotExistForLinkedIn - 사용자가 LinkedIn 리소스에 대한 액세스 동의를 제공하지 않았습니다. |
| AADSTS53000 | DeviceNotCompliant - 조건부 액세스 정책에는 이를 준수한 디바이스가 필요하며, 해당 디바이스는 정책을 준수하지 않았습니다. 사용자가 Intune과 같이 승인된 MDM 공급자에 자신의 디바이스를 등록해야 합니다. |
| AADSTS53001 | DeviceNotDomainJoined - 조건부 액세스 정책에는 도메인 가입 디바이스가 필요하며, 해당 디바이스는 도메인에 가입되어 있지 않습니다. 사용자가 도메인 가입 디바이스를 사용하도록 합니다. |
| AADSTS53002 | ApplicationUsedIsNotAnApprovedApp - 사용된 앱이 조건부 액세스에 승인된 앱이 아닙니다. 사용자가 액세스 권한을 부여받으려면 사용하도록 승인된 앱 목록에 있는 앱 중 하나를 사용해야 합니다. |
| AADSTS53003 | BlockedByConditionalAccess - 조건부 액세스 정책에 의해 액세스가 차단되었습니다. 액세스 정책에 따라 토큰 발급을 허용하지 않습니다. |
| AADSTS53004 | ProofUpBlockedDueToRisk - 사용자는 이 콘텐츠에 액세스하기 전에 다단계 인증 등록 프로세스를 완료해야 합니다. 사용자가 다단계 인증을 등록해야 합니다. |
| AADSTS53011 | 사용자가 홈 테넌트의 위험으로 인해 차단되었습니다. |
| AADSTS54000 | MinorUserBlockedLegalAgeGroupRule |
| AADSTS65001 | DelegationDoesNotExist - 사용자 또는 관리자가 X ID로 애플리케이션을 사용하는 데 동의하지 않았습니다. 이 사용자 및 리소스에 대한 대화형 권한 부여 요청을 보내세요. |
| AADSTS65004 | UserDeclinedConsent - 사용자가 앱에 액세스하는 데 동의하지 않았습니다. 사용자가 로그인을 다시 시도하고 응용 프로그램에 동의해야 합니다.|
| AADSTS65005 | MisconfiguredApplication - 앱에 필요한 리소스 액세스 목록에 리소스에서 검색 가능한 앱이 없거나, 클라이언트 앱에서 필요한 리소스 액세스 목록에 지정되지 않은 리소스에 대한 액세스를 요청했거나, Graph 서비스에서 잘못된 요청 또는 찾을 수 없는 리소스를 반환했습니다. 앱에서 SAML을 지원하는 경우 잘못된 식별자(엔터티)로 앱을 구성했을 수 있습니다. 자세히 알아보려면 오류 [AADSTS650056](/troubleshoot/azure/active-directory/error-code-aadsts650056-misconfigured-app)에 대한 문제 해결 문서를 참조하세요. |
| AADSTS650052 | 조직 `\"{organization}\"`에서 구독하지 않거나 사용하도록 설정하지 않은 서비스 `(\"{name}\")`에 대한 액세스가 앱에 필요합니다. IT 관리자에게 문의하여 서비스 구독 구성을 검토하세요. |
| AADSTS650054 |  애플리케이션에서 제거되었거나 더 이상 사용할 수 없는 리소스에 액세스할 수 있는 권한을 요청했습니다. 앱이 호출하는 모든 리소스가 작업 중인 테넌트에 있는지 확인합니다. |
| AADSTS67003 | ActorNotValidServiceIdentity |
| AADSTS70000 | InvalidGrant - 인증에 실패했습니다. 새로 고침 토큰이 잘못되었습니다. 다음과 같은 이유로 오류가 발생했을 수 있습니다.<ul><li>토큰 바인딩 헤더가 비어 있습니다.</li><li>토큰 바인딩 해시가 일치하지 않습니다.</li></ul> |
| AADSTS70001 | UnauthorizedClient - 애플리케이션을 사용할 수 없습니다. 자세히 알아보려면 오류 [AADSTS70001](/troubleshoot/azure/active-directory/error-code-aadsts70001-app-not-found-in-directory)에 대한 문제 해결 문서를 참조하세요. |
| AADSTS70002 | InvalidClient - 자격 증명 유효성을 검사하는 중 오류가 발생했습니다. 지정된 client_secret이 이 클라이언트에 대해 필요한 값과 일치하지 않습니다. client_secret을 수정한 후 다시 시도하세요. 자세한 내용은 [인증 코드를 사용하여 액세스 토큰 요청](v2-oauth2-auth-code-flow.md#redeem-a-code-for-an-access-token)을 참조하세요. |
| AADSTS70003 | UnsupportedGrantType - 앱에서 지원되지 않는 권한 부여 유형을 반환했습니다. |
| AADSTS70004 | InvalidRedirectUri - 앱에서 잘못된 리디렉션 URI를 반환했습니다. 클라이언트에서 지정한 리디렉션 주소가 구성된 주소 또는 OIDC 승인 목록의 주소와 일치하지 않습니다. |
| AADSTS70005 | UnsupportedResponseType - 다음과 같은 이유로 앱이 지원되지 않는 응답 형식을 반환했습니다.<ul><li>'token' 응답 형식을 앱에 대해 사용할 수 없습니다.</li><li>'id_token' 응답 형식에 'OpenID' 범위가 필요합니다. 인코딩된 wctx에 지원되지 않는 OAuth 매개 변수 값이 있습니다.</li></ul> |
| AADSTS700054 | Response_type 'id_token'을 애플리케이션에 대해 사용할 수 없습니다.  애플리케이션이 권한 부여 엔드포인트에서 ID 토큰을 요청했지만 ID 토큰 암시적 허용이 사용하도록 설정되지 않았습니다.  Azure Portal > Azure Active Directory > 앱 등록 > 애플리케이션 선택 > 인증 > '암시적 허용 및 하이브리드 흐름'에서 'ID 토큰'이 선택되어 있는지 확인합니다.|
| AADSTS70007 | UnsupportedResponseMode - 앱에서 토큰을 요청할 때 지원되지 않는 `response_mode` 값을 반환했습니다.  |
| AADSTS70008 | ExpiredOrRevokedGrant - 비활성 상태로 인해 새로 고침 토큰이 만료되었습니다. 토큰이 XXX에서 발행되었으며 일정 기간 동안 비활성 상태였습니다. |
| AADSTS70011 | InvalidScope - 앱에서 요청한 범위가 잘못되었습니다. |
| AADSTS70012 | MsaServerError - MSA(소비자) 사용자를 인증하는 동안 서버 오류가 발생했습니다. 다시 시도하세요. 오류가 계속되면 [지원 티켓을 여세요](../fundamentals/active-directory-troubleshooting-support-howto.md). |
| AADSTS70016 | AuthorizationPending - OAuth 2.0 디바이스 흐름 오류입니다. 권한 부여 보류 중입니다. 디바이스가 요청 폴링을 다시 시도합니다. |
| AADSTS70018 | BadVerificationCode - 사용자가 디바이스 코드 흐름에 대해 잘못된 사용자 코드를 입력했기 때문에 확인 코드가 잘못되었습니다. 권한 부여가 승인되지 않았습니다. |
| AADSTS70019 | CodeExpired - 확인 코드가 만료되었습니다. 사용자가 로그인을 다시 시도하도록 합니다. |
| AADSTS75001 | BindingSerializationError - SAML 메시지를 바인딩하는 동안 오류가 발생했습니다. |
| AADSTS75003 | UnsupportedBindingError - 앱에서 지원되지 않는 바인딩 관련 오류를 반환했습니다. SAML 프로토콜 응답은 HTTP POST 이외의 바인딩을 통해 보낼 수 없습니다. |
| AADSTS75005 | Saml2MessageInvalid - Azure AD에서는 SSO의 앱에서 보낸 SAML 요청을 지원하지 않습니다. 자세히 알아보려면 오류 [AADSTS75005](/troubleshoot/azure/active-directory/error-code-aadsts75005-not-a-valid-saml-request)에 대한 문제 해결 문서를 참조하세요. |
| AADSTS7500514 | 지원되는 유형의 SAML 응답을 찾을 수 없습니다. 지원되는 응답 형식은 ‘Response’(XML 네임스페이스 ‘urn:oasis:names:tc:SAML:2.0:protocol’) 또는 ‘Assertion’(XML 네임스페이스 ‘urn:oasis:names:tc:SAML:2.0:assertion’)입니다. 애플리케이션 오류 - 개발자가 이 오류를 처리할 것입니다.|
| AADSTS750054 | SAML 리디렉션 바인딩에 대한 HTTP 요청의 쿼리 문자열 매개 변수로 SAMLRequest 또는 SAMLResponse가 있어야 합니다. 자세히 알아보려면 오류 [AADSTS750054](/troubleshoot/azure/active-directory/error-code-aadsts750054-saml-request-not-present)에 대한 문제 해결 문서를 참조하세요. |
| AADSTS75008 | RequestDeniedError - SAML 요청에 예기치 않은 대상이 있기 때문에 앱의 요청이 거부되었습니다. |
| AADSTS75011 | NoMatchedAuthnContextInOutputClaims - 사용자가 서비스를 통해 인증하는 인증 방법이 요청한 인증 방법과 일치하지 않습니다. 자세히 알아보려면 오류 [AADSTS75011](/troubleshoot/azure/active-directory/error-code-aadsts75011-auth-method-mismatch)에 대한 문제 해결 문서를 참조하세요. |
| AADSTS75016 | Saml2AuthenticationRequestInvalidNameIDPolicy - SAML2 인증 요청에 잘못된 NameIdPolicy가 있습니다. |
| AADSTS80001 | OnPremiseStoreIsNotAvailable - 인증 에이전트가 Active Directory에 연결할 수 없습니다. 에이전트 서버가 자신의 암호에 대한 유효성이 검사되어야 하는 사용자와 동일한 AD 포리스트의 멤버이고 Active Directory에 연결할 수 있는지 확인합니다. |
| AADSTS80002 | OnPremisePasswordValidatorRequestTimedout - 암호 유효성 검사 요청 시간이 초과되었습니다. Active Directory가 사용 가능하고 에이전트의 요청에 응답하는지 확인합니다. |
| AADSTS80005 | OnPremisePasswordValidatorUnpredictableWebException - 인증 에이전트에서 응답을 처리하는 동안 알 수 없는 오류가 발생했습니다. 요청을 다시 시도하십시오. 계속 실패하는 경우 오류에 대한 자세한 내용을 확인하려면 [지원 티켓을 여세요](../fundamentals/active-directory-troubleshooting-support-howto.md). |
| AADSTS80007 | OnPremisePasswordValidatorErrorOccurredOnPrem - 인증 에이전트에서 사용자 암호의 유효성을 검사할 수 없습니다. 에이전트 로그에서 자세한 정보를 확인하고 Active Directory가 예상대로 작동하는지 확인합니다. |
| AADSTS80010 | OnPremisePasswordValidationEncryptionException - 인증 에이전트가 암호를 해독할 수 없습니다. |
| AADSTS80012 | OnPremisePasswordValidationAccountLogonInvalidHours - 사용자가 허용되는 시간(AD에서 지정됨)이 아닌 시간에 로그온하려고 했습니다. |
| AADSTS80013 | OnPremisePasswordValidationTimeSkew - 인증 에이전트를 실행하는 머신과 AD 사이의 시간차로 인해 인증 시도를 완료할 수 없습니다. 시간 동기화 문제를 해결하세요. |
| AADSTS81004 | DesktopSsoIdentityInTicketIsNotAuthenticated - Kerberos 인증 시도가 실패했습니다. |
| AADSTS81005 | DesktopSsoAuthenticationPackageNotSupported - 인증 패키지가 지원되지 않습니다. |
| AADSTS81006 | DesktopSsoNoAuthorizationHeader - 권한 부여 헤더가 없습니다. |
| AADSTS81007 | DesktopSsoTenantIsNotOptIn - Seamless SSO에 대한 테넌트를 사용할 수 없습니다. |
| AADSTS81009 | DesktopSsoAuthorizationHeaderValueWithBadFormat - 사용자의 Kerberos 티켓에 대한 유효성을 검사할 수 없습니다. |
| AADSTS81010 | DesktopSsoAuthTokenInvalid - 사용자의 Kerberos 티켓이 만료되었거나 잘못되었으므로 Seamless SSO가 실패했습니다. |
| AADSTS81011 | DesktopSsoLookupUserBySidFailed - 사용자의 Kerberos 티켓 정보에 기반하여 사용자 개체를 찾을 수 없습니다. |
| AADSTS81012 | DesktopSsoMismatchBetweenTokenUpnAndChosenUpn - Azure AD에 로그인하려는 사용자가 디바이스에 로그인한 사용자와 다릅니다. |
| AADSTS90002 | InvalidTenantName - 데이터 저장소에서 테넌트 이름을 찾을 수 없습니다. 올바른 테넌트 ID가 있는지 확인하세요. |
| AADSTS90004 | InvalidRequestFormat - 요청 형식이 잘못되었습니다. |
| AADSTS90005 | InvalidRequestWithMultipleRequirements - 요청을 완료할 수 없습니다. 식별자와 로그인 힌트를 함께 사용할 수 없으므로 요청이 잘못되었습니다.  |
| AADSTS90006 | ExternalServerRetryableError - 서비스를 일시적으로 사용할 수 없습니다.|
| AADSTS90007 | InvalidSessionId - 잘못된 요청입니다. 전달된 세션 ID를 구문 분석할 수 없습니다. |
| AADSTS90008 | TokenForItselfRequiresGraphPermission - 사용자 또는 관리자가 애플리케이션을 사용하도록 동의하지 않았습니다. 최소한 로그인 및 사용자 프로필 읽기 권한을 지정하여 애플리케이션이 Azure AD에 액세스할 수 있어야 합니다. |
| AADSTS90009 | TokenForItselfMissingIdenticalAppIdentifier - 애플리케이션이 자신의 토큰을 요청하고 있습니다. 이 시나리오는 지정된 리소스가 GUID 기반 애플리케이션 ID를 사용하는 경우에만 지원됩니다. |
| AADSTS90010 | NotSupported - 알고리즘을 만들 수 없습니다. |
| AADSTS90012 | RequestTimeout - 요청 시간이 초과되었습니다. |
| AADSTS90013 | InvalidUserInput - 사용자 입력이 잘못되었습니다. |
| AADSTS90014 | MissingRequiredField - 예상된 필드가 자격 증명에 없는 다양한 경우에 이 코드가 나타날 수 있습니다. |
| AADSTS90015 | QueryStringTooLong - 쿼리 문자열이 너무 깁니다. |
| AADSTS90016 | MissingRequiredClaim - 액세스 토큰이 잘못되었습니다. 필수 클레임이 누락되었습니다. |
| AADSTS90019 | MissingTenantRealm - Azure AD가 요청에서 테넌트 식별자를 확인할 수 없습니다. |
| AADSTS90022 | AuthenticatedInvalidPrincipalNameFormat - 주체 이름 형식이 유효하지 않거나 예상된 `name[/host][@realm]` 형식에 맞지 않습니다. 주체 이름이 필요하고 호스트 및 영역은 선택 사항이며 null로 설정할 수 있습니다. |
| AADSTS90023 | InvalidRequest - 인증 서비스 요청이 잘못되었습니다. |
| AADSTS9002313 | InvalidRequest - 요청의 형식이 잘못되었거나 무효인 요청입니다. - 특정 엔드포인트에 대한 요청에 잘못된 문제가 있었기 때문입니다. 해당 문제에 대해서는 발생한 오류의 피들러 추적을 가져와 요청이 실제로 올바른 형식인지 여부를 확인하는 것을 제안합니다. |
| AADSTS90024 | RequestBudgetExceededError - 일시적인 오류가 발생했습니다. 다시 시도하세요. |
| AADSTS90033 | MsodsServiceUnavailable - MSODS(Microsoft Online Directory Service)를 사용할 수 없습니다. |
| AADSTS90036 | MsodsServiceUnretryableFailure - MSODS가 호스트하는 WCF 서비스에서 다시 시도할 수 없는 예기치 않은 오류가 발생했습니다. 오류에 대한 자세한 내용을 확인하려면 [지원 티켓을 여세요](../fundamentals/active-directory-troubleshooting-support-howto.md). |
| AADSTS90038 | NationalCloudTenantRedirection - 지정된 테넌트 'Y'가 국가별 클라우드'X'에 속합니다. 현재 클라우드 인스턴스 'Z'가 X와 페더레이션되지 않습니다. 클라우드 리디렉션 오류가 반환됩니다. |
| AADSTS90043 | NationalCloudAuthCodeRedirection - 기능을 사용할 수 없습니다. |
| AADSTS90051 | InvalidNationalCloudId - 국가별 클라우드 식별자에 잘못된 클라우드 식별자가 포함되어 있습니다. |
| AADSTS90055 | TenantThrottlingError - 들어오는 요청이 너무 많습니다. 차단된 테넌트에 대해 이 예외가 throw됩니다. |
| AADSTS90056 | BadResourceRequest - 액세스 토큰의 코드를 사용하려면 앱에서 `/token` 엔드포인트로 POST 요청을 보내야 합니다. 또한 그 이전에, 인증 코드를 제공하고 해당 코드를 POST 요청으로 `/token` 엔드포인트에 보내야 합니다. OAuth 2.0 인증 코드 흐름에 대한 개요는 다음 문서를 참조하세요. [../azuread-dev/v1-protocols-oauth-code.md](../azuread-dev/v1-protocols-oauth-code.md). 사용자를 `/authorize` 엔드포인트로 리디렉션합니다. 그러면 authorization_code가 반환됩니다. 사용자는 요청을 `/token` 엔드포인트에 게시하여 액세스 토큰을 가져옵니다. Azure Portal에 로그인하고 **앱 등록 > 엔드포인트** 를 확인하여 두 엔드포인트가 올바르게 구성되었는지 확인합니다. |
| AADSTS90072 | PassThroughUserMfaError - 사용자가 로그인에 사용한 외부 계정이 로그인한 테넌트에 존재하지 않으므로 사용자가 테넌트에 대한 MFA 요구 사항을 충족할 수 없습니다. 사용자가 동기화되었지만 Active Directory와 Azure AD 간에 ImmutableID(sourceAnchor) 특성이 일치하지 않는 경우에도 이 오류가 발생할 수 있습니다. 먼저 계정을 테넌트의 외부 사용자로 추가해야 합니다. 로그아웃하고 다른 Azure AD 사용자 계정으로 로그인하세요. |
| AADSTS90081 | OrgIdWsFederationMessageInvalid - 서비스가 WS-Federation 메시지를 처리하려고 할 때 오류가 발생했습니다. 메시지가 잘못되었습니다. |
| AADSTS90082 | OrgIdWsFederationNotSupported - 요청에 대해 선택한 인증 정책이 현재 지원되지 않습니다. |
| AADSTS90084 | OrgIdWsFederationGuestNotAllowed - 이 사이트에서는 게스트 계정이 허용되지 않습니다. |
| AADSTS90085 | OrgIdWsFederationSltRedemptionFailed - 회사 개체가 아직 프로비전되지 않았으므로 서비스에서 토큰을 발급할 수 없습니다. |
| AADSTS90086 | OrgIdWsTrustDaTokenExpired - 사용자 DA 토큰이 만료되었습니다. |
| AADSTS90087 | OrgIdWsFederationMessageCreationFromUriFailed - URI에서 WS-Federation 메시지를 만드는 동안 오류가 발생했습니다. |
| AADSTS90090 | GraphRetryableError - 서비스를 일시적으로 사용할 수 없습니다. |
| AADSTS90091 | GraphServiceUnreachable |
| AADSTS90092 | GraphNonRetryableError |
| AADSTS90093 | GraphUserUnauthorized - 요청에 대해 사용할 수 없는 오류 코드와 함께 반환된 그래프입니다. |
| AADSTS90094 | AdminConsentRequired - 관리자 동의가 필요합니다. |
| AADSTS900382 | 기밀 클라이언트는 클라우드 간 요청에서 지원되지 않습니다. |
| AADSTS90099 | ‘{appId}’ ({appName}) 애플리케이션이 ‘{tenant}’ 테넌트에서 권한을 부여받지 않았습니다. 파트너로 위임된 관리자가 애플리케이션을 사용하려면 먼저 애플리케이션에 고객 테넌트에 액세스할 수 있는 권한이 있어야 합니다. 사전 동의를 제공하거나 적절한 파트너 센터 API를 실행하여 애플리케이션에 권한을 부여합니다. |
| AADSTS900971| 회신 주소가 제공되지 않았습니다.|
| AADSTS90100 | InvalidRequestParameter - 매개 변수가 비어 있거나 잘못되었습니다. |
| AADSTS901002 | AADSTS901002: ‘resource’ 요청 매개 변수는 지원되지 않습니다. |
| AADSTS90101 | InvalidEmailAddress - 제공한 데이터가 유효한 메일 주소가 아닙니다. 메일 주소는 `someone@example.com` 형식이어야 합니다. |
| AADSTS90102 | InvalidUriParameter - 값이 유효한 절대 URI여야 합니다. |
| AADSTS90107 | InvalidXml - 요청이 잘못되었습니다. 데이터에 잘못된 문자가 없는지 확인합니다.|
| AADSTS90114 | InvalidExpiryDate - 대량 토큰 만료 타임스탬프로 인해 만료된 토큰이 발급됩니다. |
| AADSTS90117 | InvalidRequestInput |
| AADSTS90119 | InvalidUserCode - 사용자 코드가 Null이거나 비어 있습니다.|
| AADSTS90120 | InvalidDeviceFlowRequest - 요청이 이미 승인되었거나 거부되었습니다. |
| AADSTS90121 | InvalidEmptyRequest - 잘못된 비어 있는 요청입니다.|
| AADSTS90123 | IdentityProviderAccessDenied - ID 또는 클레임 발급 공급자가 요청을 거부했으므로 토큰을 발급할 수 없습니다. |
| AADSTS90124 | V1ResourceV2GlobalEndpointNotSupported - 리소스가 `/common` 또는 `/consumers` 엔드포인트에서 지원되지 않습니다. 대신, `/organizations` 또는 테넌트 특정 엔드포인트를 사용합니다. |
| AADSTS90125 | DebugModeEnrollTenantNotFound - 사용자가 시스템에 없습니다. 사용자 이름을 올바르게 입력했는지 확인합니다. |
| AADSTS90126 | DebugModeEnrollTenantNotInferred - 사용자 유형이 이 엔드포인트에서 지원되지 않습니다. 시스템이 사용자 이름에서 사용자 테넌트를 유추할 수 없습니다. |
| AADSTS90130 | NonConvergedAppV2GlobalEndpointNotSupported - 애플리케이션이 `/common` 또는 `/consumers` 엔드포인트에서 지원되지 않습니다. 대신, `/organizations` 또는 테넌트 특정 엔드포인트를 사용합니다. |
| AADSTS120000 | PasswordChangeIncorrectCurrentPassword |
| AADSTS120002 | PasswordChangeInvalidNewPasswordWeak |
| AADSTS120003 | PasswordChangeInvalidNewPasswordContainsMemberName |
| AADSTS120004 | PasswordChangeOnPremComplexity |
| AADSTS120005 | PasswordChangeOnPremSuccessCloudFail |
| AADSTS120008 | PasswordChangeAsyncJobStateTerminated - 다시 시도할 수 없는 오류가 발생했습니다.|
| AADSTS120011 | PasswordChangeAsyncUpnInferenceFailed |
| AADSTS120012 | PasswordChangeNeedsToHappenOnPrem |
| AADSTS120013 | PasswordChangeOnPremisesConnectivityFailure |
| AADSTS120014 | PasswordChangeOnPremUserAccountLockedOutOrDisabled |
| AADSTS120015 | PasswordChangeADAdminActionRequired |
| AADSTS120016 | PasswordChangeUserNotFoundBySspr |
| AADSTS120018 | PasswordChangePasswordDoesnotComplyFuzzyPolicy |
| AADSTS120020 | PasswordChangeFailure |
| AADSTS120021 | PartnerServiceSsprInternalServiceError |
| AADSTS130004 | NgcKeyNotFound - 사용자 계정에 NGC ID 키가 구성되어 있지 않습니다. |
| AADSTS130005 | NgcInvalidSignature - NGC 키 서명을 확인하지 못했습니다.|
| AADSTS130006 | NgcTransportKeyNotFound - NGC 전송 키가 디바이스에 구성되어 있지 않습니다. |
| AADSTS130007 | NgcDeviceIsDisabled - 디바이스를 사용할 수 없습니다. |
| AADSTS130008 | NgcDeviceIsNotFound - NGC 키가 참조하는 디바이스를 찾을 수 없습니다. |
| AADSTS135010 | KeyNotFound |
| AADSTS140000 | InvalidRequestNonce - 요청 nonce를 제공하지 않았습니다. |
| AADSTS140001 | InvalidSessionKey - 세션 키가 잘못되었습니다.|
| AADSTS165900 | InvalidApiRequest - 잘못된 요청입니다. |
| AADSTS220450 | UnsupportedAndroidWebViewVersion - Chrome WebView 버전은 지원되지 않습니다. |
| AADSTS220501 | InvalidCrlDownload |
| AADSTS221000 | DeviceOnlyTokensNotSupportedByResource - 디바이스 전용 토큰을 수락하도록 리소스가 구성되어 있지 않습니다. |
| AADSTS240001 | BulkAADJTokenUnauthorized - 사용자에게 Azure AD에 디바이스를 등록할 수 있는 권한이 없습니다. |
| AADSTS240002 | RequiredClaimIsMissing - id_token을 `urn:ietf:params:oauth:grant-type:jwt-bearer` 권한 부여로 사용할 수 없습니다.|
| AADSTS530032 | BlockedByConditionalAccessOnSecurityPolicy - 테넌트 관리자가 이 요청을 차단하는 보안 정책을 구성했습니다. 테넌트 수준에 정의된 보안 정책을 확인하여 요청이 정책 요구 사항을 충족하는지 확인합니다. |
| AADSTS700016 | UnauthorizedClient_DoesNotMatchRequest - 디렉터리/테넌트에서 애플리케이션을 찾지 못했습니다. 이 오류는 테넌트의 관리자가 애플리케이션을 설치하지 않았거나 테넌트의 사용자가 동의하지 않은 경우에 발생할 수 있습니다. 애플리케이션에 대한 식별자 값을 잘못 구성했거나 잘못된 테넌트에 인증 요청을 보낸 것일 수 있습니다. |
| AADSTS700020 | InteractionRequired - 액세스 권한 부여에는 상호 작용이 필요합니다. |
| AADSTS700022 | InvalidMultipleResourcesScope - 입력 매개 변수 범위에 대해 제공한 값이 둘 이상의 리소스를 포함하므로 잘못되었습니다. |
| AADSTS700023 | InvalidResourcelessScope - 액세스 토큰을 요청할 때 입력 매개 변수 범위에 대해 제공한 값이 잘못되었습니다. |
| AADSTS7000215 | 잘못된 클라이언트 비밀을 제공합니다. 개발자 오류 - 앱에서 필수 또는 올바른 인증 매개 변수를 사용하지 않고 로그인하려고 합니다.|
| AADSTS7000222 | InvalidClientSecretExpiredKeysProvided - 제공된 클라이언트 암호 키가 만료되었습니다. Azure Portal을 방문하여 앱에 쓸 새 키를 만들거나, 추가 보안을 위해 [https://aka.ms/certCreds](./active-directory-certificate-credentials.md)와 같이 인증서 자격 증명을 사용하는 것이 좋습니다. |
| AADSTS700005 | InvalidGrantRedeemAgainstWrongTenant - 제공된 권한 부여 코드는 다른 테넌트에 사용되는 코드이므로 거부되었습니다. OAuth2 인증 코드는 획득한 테넌트와 동일한 테넌트에 (/common 또는 /{tenant-ID}가 적절함) 사용해야 합니다 |
| AADSTS1000000 | UserNotBoundError - 바인딩 API를 사용하려면 Azure AD 사용자가 외부 IDP에도 인증해야 하는데, 아직 인증하지 않았습니다. |
| AADSTS1000002 | BindCompleteInterruptError - 바인딩이 성공적으로 완료되었지만 사용자에게 알려야 합니다. |
| AADSTS7000112 | UnauthorizedClientApplicationDisabled - 애플리케이션을 사용할 수 없습니다. |
| AADSTS7000114| 애플리케이션 ‘appIdentifier’는 애플리케이션을 대신 호출할 수 없습니다.|
| AADSTS7500529 | ‘SAMLId-Guid’ 값은 유효한 SAML ID가 아닙니다. Azure AD는 이 특성을 사용하여 반환된 응답의 InResponseTo 특성을 채웁니다. ID는 숫자로 시작할 수 없으므로 GUID의 문자열 표현에 "id"와 같은 문자열을 앞에 추가합니다. 예를 들어 id6c1c178c166d486687be4aaf5e482730은 잘못된 ID입니다. |

## <a name="next-steps"></a>다음 단계

* 질문이 있거나 원하는 항목을 찾을 수 없나요? GitHub 문제를 만들거나 [개발자를 위한 지원 및 도움말 옵션](./developer-support-help-options.md)을 확인하여 도움말 및 지원을 얻을 수 있는 다른 방법을 알아보세요.