---
title: Azure Active Directory B2C의 사용자 프로필 특성
description: Azure AD B2C 디렉터리 사용자 프로필에서 지원하는 사용자 리소스 유형 특성에 대해 알아봅니다. 기본 제공되는 특성 및 확장과 Microsoft Graph에 특성이 매핑되는 방식을 알아봅니다.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 06/16/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 76a7fa3cfeafa20dd81f043d67388bdfb281b6d4
ms.sourcegitcommit: f3b930eeacdaebe5a5f25471bc10014a36e52e5e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/16/2021
ms.locfileid: "112232524"
---
# <a name="user-profile-attributes"></a>사용자 프로필 특성

Azure AD(Azure Active Directory) B2C 디렉터리 사용자 프로필에는 이름, 성, 도시, 우편 번호, 전화 번호와 같은 기본 제공되는 특성 세트가 함께 제공됩니다. 외부 데이터 저장소 없이 자체 애플리케이션 데이터로 사용자 프로필을 확장할 수 있습니다.

Azure AD B2C 사용자 프로필에서 사용할 수 있는 대부분의 특성은 Microsoft Graph에서 지원됩니다. 이 문서에서는 지원되는 Azure AD B2C 사용자 프로필 특성에 대해 설명합니다. 또한, Microsoft Graph에서 지원하지 않는 특성과 Azure AD B2C에서 사용할 수 없는 Microsoft Graph 특성도 안내합니다.

> [!IMPORTANT]
> 기본 제공 특성이나 확장 특성은 계정 자격 증명, 정부 발행 신분증 번호, 카드 소유자 데이터, 금융 계정 데이터, 건강 정보, 중요한 배경 정보와 같은 중요한 개인 정보를 저장하는 데 사용해서는 안 됩니다.

외부 시스템과도 통합해야 합니다. 예를 들어 인증에 Azure AD B2C를 사용하되 외부 CRM(고객 관계 관리) 또는 고객 충성도 데이터베이스를 고객 데이터의 정보 원본으로 위임할 수 있습니다. 자세한 내용은 [원격 프로필](https://github.com/azure-ad-b2c/samples/tree/master/policies/remote-profile) 솔루션을 참조하세요.

## <a name="azure-ad-user-resource-type"></a>Azure AD 사용자 리소스 종류

아래 표에는 Azure AD B2C 디렉터리 사용자 프로필에서 지원하는 [사용자 리소스 유형](/graph/api/resources/user) 특성이 나와 있습니다. 표에서는 각 특성에 대해 다음 정보를 제공합니다.

- Azure AD B2C에서 사용하는 특성 이름(Microsoft Graph 이름이 다른 경우 그 뒤에 Microsoft Graph 이름)
- 특성 데이터 형식
- 특성 설명
- 특성을 Azure Portal에서 사용할 수 있는지 여부
- 사용자 흐름에서 특성을 사용할 수 있는지 여부
- 사용자 지정 정책 [Azure AD 기술 프로필](active-directory-technical-profile.md)에서 특성을 사용할 수 있는지 여부와 사용할 수 있는 섹션(&lt;InputClaims&gt;, &lt;OutputClaims&gt; 또는 &lt;PersistedClaims&gt;)

|속성     |유형     |설명|Azure portal|사용자 흐름|사용자 지정 정책|
|---------|---------|----------|------------|----------|-------------|
|accountEnabled  |부울|사용자 계정이 사용하도록 설정되었는지 여부: **true** - 계정이 사용하도록 설정됨, **false** - 계정이 사용하도록 설정되지 않음.|예|예|지속형, 출력|
|ageGroup        |String|사용자의 나이 그룹입니다. 가능한 값: Null, Undefined, Minor, Adult, NotAdult.|예|예|지속형, 출력|
|alternativeSecurityId([Identities](#identities-attribute))|String|외부 ID 공급자에서 가져온 단일 사용자 ID.|예|예|입력, 지속형, 출력|
|alternativeSecurityIds([Identities](#identities-attribute))|대체 securityId 컬렉션|외부 ID 공급자에서 가져온 사용자 ID 컬렉션.|예|예|지속형, 출력|
|city            |String|사용자가 위치한 구/군입니다. 최대 길이는 128입니다.|예|예|지속형, 출력|
|consentProvidedForMinor|String|미성년에 대한 동의가 제공되었는지 여부입니다. Null, granted, denied 또는 notRequired가 허용되는 값입니다.|예|예|지속형, 출력|
|country         |String|사용자가 위치한 국가/지역입니다. 예제: “US” 또는 “UK”. 최대 길이는 128입니다.|예|예|지속형, 출력|
|createdDateTime|DateTime|사용자 개체가 만들어진 날짜입니다. 읽기 전용입니다.|예|예|지속형, 출력|
|creationType    |String|사용자 계정이 Azure Active Directory B2C 테넌트의 로컬 계정으로 만들어진 경우, 값은 LocalAccount 또는 nameCoexistence입니다. 읽기 전용입니다.|예|예|지속형, 출력|
|dateOfBirth     |Date|생년월일입니다.|예|예|지속형, 출력|
|department      |String|사용자가 근무하는 부서의 이름입니다. 최대 길이는 64입니다.|예|예|지속형, 출력|
|displayName     |String|사용자의 표시 이름입니다. 최대 길이는 256입니다.|예|예|지속형, 출력|
|facsimileTelephoneNumber<sup>1</sup>|String|사용자의 회사 팩스 전화 번호입니다.|예|예|지속형, 출력|
|givenName       |String|사용자의 이름(성 제외)입니다. 최대 길이는 64입니다.|예|예|지속형, 출력|
|jobTitle        |String|사용자의 직위입니다. 최대 길이는 128입니다.|예|예|지속형, 출력|
|immutableId     |String|일반적으로 온-프레미스 Active Directory에서 마이그레이션된 사용자에게 사용되는 식별자입니다.|아니요|예|지속형, 출력|
|legalAgeGroupClassification|String|법적 연령 그룹 분류입니다. ageGroup 및 consentProvidedForMinor 속성을 기준으로 계산되는 읽기 전용 값입니다. 허용되는 값: Null, minorWithOutParentalConsent, minorWithParentalConsent, minorNoParentalConsentRequired, notAdult, adult.|예|예|지속형, 출력|
|legalCountry<sup>1</sup>  |String|법적 목적을 위한 국가/지역입니다.|예|예|지속형, 출력|
|mailNickName    |String|사용자의 메일 별칭입니다. 최대 길이는 64입니다.|예|예|지속형, 출력|
|모바일(mobilePhone) |String|사용자의 기본 휴대폰 번호입니다. 최대 길이는 64입니다.|예|예|지속형, 출력|
|netId           |String|Net ID입니다.|예|예|지속형, 출력|
|objectId        |String|사용자의 고유 식별자인 GUID(Globally Unique Identifier)입니다. 예제: 12345678-9abc-def0-1234-56789abcde. 읽기 전용, 변경 불가.|읽기 전용|예|입력, 지속형, 출력|
|otherMails      |문자열 컬렉션|사용자의 추가 메일 주소 목록입니다. 예: [“bob@contoso.com”, “Robert@fabrikam.com”]. 참고: 악센트 문자는 허용되지 않습니다.|예(대체 메일)|예|지속형, 출력|
|password        |String|사용자자 생성 중 만든 로컬 계정의 암호입니다.|예|예|지속됨|
|passwordPolicies     |String|암호의 정책입니다. 쉼표로 구분된 여러 정책 이름으로 구성된 문자열입니다. 예를 들면 “DisablePasswordExpiration, DisableStrongPassword”입니다.|아니요|예|지속형, 출력|
|physicalDeliveryOfficeName(officeLocation)|String|사용자 사업장의 사무실 위치입니다. 최대 길이는 128입니다.|예|예|지속형, 출력|
|postalCode      |String|사용자의 우편 주소에 대한 우편 번호입니다. 우편 번호는 사용자의 해당 국가/지역에 따라 다릅니다. 미국의 경우 이 특성은 우편 번호를 포함합니다. 최대 길이는 40입니다.|예|예|지속형, 출력|
|preferredLanguage    |String|사용자의 기본 설정 언어입니다. 기본 언어 형식은 RFC 4646을 기반으로 합니다. 이름은 언어와 관련된 ISO 639 두 문자의 소문자 문화권 코드와 국가 또는 지역에 연결된 ISO 3166 두 문자의 대문자 하위 문화권 코드의 조합입니다. 예: “en-US” 또는 “es-ES”.|아니요|예|지속형, 출력|
|refreshTokensValidFromDateTime(signInSessionsValidFromDateTime)|DateTime|이 시간 이전에 발급된 모든 새로 고침 토큰은 유효하지 않으며, 유효하지 않은 새로 고침 토큰을 사용하여 새 액세스 토큰을 가져오면 애플리케이션에서 오류가 발생합니다. 이 경우 애플리케이션이 권한 부여 엔드포인트에 요청을 제출하여 새 새로 고침 토큰을 가져와야 합니다. 읽기 전용입니다.|예|예|출력|
|signInNames([Identities](#identities-attribute)) |String|디렉터리에 있는 임의 유형의 로컬 계정 사용자의 고유한 로그인 이름입니다. 이 특성을 사용하여 로컬 계정 유형을 지정하지 않고 로그인 값으로 사용자를 가져올 수 있습니다.|아니요|예|입력|
|signInNames.userName([Identities](#identities-attribute)) |String|디렉터리에 있는 로컬 계정 사용자의 고유한 사용자 이름입니다. 이 특성을 사용하여 특정 로그인 사용자 이름을 갖는 사용자를 만들거나 가져올 수 있습니다. 패치 작업 중에 이 특성을 PersistedClaims에 단독으로 지정하면 다른 유형의 signInNames가 제거됩니다. 새로운 유형의 signInNames를 추가하려면 기존 signInNames를 지속 처리해야 합니다. 참고: 사용자 이름에는 악센트 문자가 허용되지 않습니다.|아니요|예|입력, 지속형, 출력|
|signInNames.phoneNumber([Identities](#identities-attribute)) |String|디렉터리에 있는 로컬 계정 사용자의 고유한 전화 번호입니다. 이 특성을 사용하여 특정 로그인 전화 번호를 갖는 사용자를 만들거나 가져올 수 있습니다. 패치 작업 중에 이 특성을 PersistedClaims에 단독으로 지정하면 다른 유형의 signInNames가 제거됩니다. 새로운 유형의 signInNames를 추가하려면 기존 signInNames를 지속 처리해야 합니다.|예|예|입력, 지속형, 출력|
|signInNames.emailAddress([Identities](#identities-attribute))|String|디렉터리에 있는 로컬 계정 사용자의 고유한 메일 주소입니다. 이 특성을 사용하여 특정 로그인 메일 주소를 갖는 사용자를 만들거나 가져올 수 있습니다. 패치 작업 중에 이 특성을 PersistedClaims에 단독으로 지정하면 다른 유형의 signInNames가 제거됩니다. 새로운 유형의 signInNames를 추가하려면 기존 signInNames를 지속 처리해야 합니다.|예|예|입력, 지속형, 출력|
|state           |String|사용자 주소의 시/도입니다. 최대 길이는 128입니다.|예|예|지속형, 출력|
|streetAddress   |String|사용자 사업장의 주소입니다. 최대 길이는 1024입니다.|예|예|지속형, 출력|
|strongAuthentication AlternativePhoneNumber<sup>1</sup>|String|사용자의 보조 전화 번호로, 다단계 인증에 사용됩니다.|예|예|지속형, 출력|
|strongAuthenticationEmailAddress<sup>1</sup>|String|사용자의 SMTP 주소입니다. 예: “bob@contoso.com” 이 특성은 사용자 이름 정책에서 사용자 주소 메일을 저장하는 데 로그인에 사용됩니다. 메일 주소는 암호 재설정 흐름에서도 사용됩니다. 이 특성에는 악센트 문자가 허용되지 않습니다.|예|예|지속형, 출력|
|strongAuthenticationPhoneNumber<sup>2</sup>|String|사용자의 기본 전화 번호로, 다단계 인증에 사용됩니다.|예|예|지속형, 출력|
|surname         |String|사용자의 성입니다(이름 제외). 최대 길이는 64입니다.|예|예|지속형, 출력|
|telephoneNumber(businessPhones의 첫 번째 항목)|String|사용자 사업장의 기본 전화 번호입니다.|예|예|지속형, 출력|
|userPrincipalName    |String|사용자의 UPN(사용자 계정 이름)입니다. UPN은 인터넷 표준 RFC 822를 기반으로 하는 사용자의 인터넷 스타일 로그인 이름입니다. 도메인은 테넌트의 확인된 도메인 컬렉션에 있어야 합니다. 이 속성은 계정을 만들 때 필요합니다. 변경 불가|예|예|입력, 지속형, 출력|
|usageLocation   |String|국가/지역의 서비스 가용성을 확인해야 하는 법적 요구 사항으로 인해 라이선스를 할당받을 사용자에게 필요합니다. Null을 허용하지 않습니다. 두 문자로 된 국가/지역 코드(ISO 표준 3166)입니다. 예제: “US”, “JP”, “GB”.|예|예|지속형, 출력|
|userType        |String|등 디렉터리의 사용자 유형을 분류하는 데 사용할 수 있는 문자열 값입니다. 값은 멤버여야 합니다. 읽기 전용입니다.|읽기 전용|예|지속형, 출력|
|userState(externalUserState)<sup>3</sup>|String|Azure AD B2B 계정에서만, 초대가 PendingAcceptance 또는 Accepted인지 나타냅니다.|예|예|지속형, 출력|
|userStateChangedOn(externalUserStateChangeDateTime)<sup>2</sup>|DateTime|UserState 속성의 최신 변경에 대한 타임스탬프를 표시합니다.|예|예|지속형, 출력|

<sup>1 </sup>Microsoft Graph에서 지원하지 않음<br><sup>2 </sup>자세한 내용은 [MFA 전화 번호 특성](#mfa-phone-number-attribute)을 참조하세요.<br><sup>3 </sup>Azure AD B2C와 함께 사용해서는 안 됩니다.

## <a name="required-attributes"></a>필수 특성

Azure AD B2C 디렉터리에 사용자 계정을 생성하려면 다음 필수 특성을 입력합니다. 

- [표시 이름](#display-name-attribute)

- [ID](#display-name-attribute) - 하나 이상의 엔터티(로컬 또는 페더레이션 계정) 포함.

- [암호 프로필](#password-policy-attribute)- 로컬 계정을 생성하는 경우, 암호 프로필을 입력합니다.

## <a name="display-name-attribute"></a>이름 특성 표시

`displayName`은 사용자에 대한 Azure Portal 사용자 관리에서 표시하는 이름이며, 액세스 토큰에서 Azure AD B2C가 애플리케이션에 반환합니다. 이 속성은 필수입니다.

## <a name="identities-attribute"></a>ID 특성

소비자, 파트너 또는 시민일 수 있는 고객 계정은 다음과 같은 ID 형식에 연결할 수 있습니다.

- **로컬** ID - 사용자 이름과 암호는 Azure AD B2C 디렉터리에 로컬로 저장됩니다. 이러한 ID는 "로컬 계정"으로 참조하는 경우가 많습니다.
- **페더레이션** ID - ‘소셜’ 계정 또는 ‘엔터프라이즈’ 계정이라고도 하며, 사용자의 ID는 Facebook, Microsoft, ADFS 또는 Salesforce와 같은 페더레이션 ID 공급자를 통해 관리됩니다. 

고객 계정을 가진 사용자는 여러 ID를 사용하여 로그인할 수 있습니다. 예를 들면 사용자 이름, 메일, 직원 ID, 정부 ID, 기타입니다. 단일 계정에는 동일한 암호를 가진 로컬 및 소셜 모두의 여러 ID가 있을 수 있습니다.

Microsoft Graph API에서 로컬 및 페더레이션 ID는 모두 [objectIdentity](/graph/api/resources/objectidentity) 형식의 사용자 `identities` 특성에 저장됩니다. `identities` 컬렉션은 사용자 계정에 로그인하는 데 사용되는 ID 세트를 나타냅니다. 사용자는 이 컬렉션을 사용하여 연결된 모든 ID로 사용자 계정에 로그인할 수 있습니다. ID 특성에는 최대 10개의 [objectIdentity](/graph/api/resources/objectidentity) 개체가 포함될 수 있습니다. 각 개체에는 다음 속성이 포함되어 있습니다.

| 이름   | 유형 |설명|
|:---------------|:--------|:----------|
|signInType|문자열| 디렉터리에서 사용자 로그인 유형을 지정합니다. 로컬 계정: `emailAddress`, `emailAddress1`, `emailAddress2`, `emailAddress3`, `userName` 또는 기타 원하는 다른 모든 형식. 소셜 계정 `federated`로 설정되어야 합니다.|
|발급자|문자열|ID의 발급자를 지정합니다. 로컬 계정(**signInType** 이 `federated`가 아닌 경우)의 경우, 이 속성은 로컬 B2C 테넌트 기본 도메인 이름입니다(예: `contoso.onmicrosoft.com`). 소셜 ID(**signInType** 이 `federated`인 경우)의 경우, 값은 발급자의 이름입니다(예: `facebook.com`).|
|issuerAssignedId|문자열|발급자가 사용자에게 할당한 고유 식별자를 지정합니다. **발급자** 와 **issuerAssignedId** 의 조합은 테넌트 내에서 고유해야 합니다. 로컬 계정의 경우, **signInType** 이 `emailAddress` 또는 `userName`으로 설정되면, 이는 사용자의 로그인 이름을 나타냅니다.<br>**SignInType** 으로 설정된 경우: <ul><li>`emailAddress`(또는 `emailAddress1`과 같이 `emailAddress`로 시작하는 경우) **issuerAssignedId** 는 유효한 메일 주소여야 합니다.</li><li>`userName`(또는 다른 값), **issuerAssignedId** 는 [메일 주소의 유효한 로컬 파트](https://tools.ietf.org/html/rfc3696#section-3)여야 합니다.</li><li>`federated`, **issuerAssignedId** 는 페더레이션 계정 고유 식별자를 나타냅니다.</li></ul>|

다음 **ID** 특성은 로그인 이름이 있는 로컬 계정 ID, 로그인 시 메일 주소, 소셜 ID입니다.

```json
"identities": [
  {
    "signInType": "userName",
    "issuer": "contoso.onmicrosoft.com",
    "issuerAssignedId": "johnsmith"
  },
  {
    "signInType": "emailAddress",
    "issuer": "contoso.onmicrosoft.com",
    "issuerAssignedId": "jsmith@yahoo.com"
  },
  {
    "signInType": "federated",
    "issuer": "facebook.com",
    "issuerAssignedId": "5eecb0cd"
  }
]
```

페더레이션 ID의 경우 ID 공급자에 따라 **issuerAssignedId** 는 애플리케이션 또는 개발 계정당 지정된 사용자에 대한 고유한 값입니다. 이전에 소셜 공급자나 동일한 개발 계정 내의 다른 애플리케이션에서 할당한 것과 동일한 애플리케이션 ID를 사용하여 Azure AD B2C 정책을 구성합니다.

## <a name="password-profile-property"></a>암호 프로필 속성

로컬 ID의 경우 **Passwordprofile** 특성이 필요하며, 사용자의 암호를 포함합니다. `forceChangePasswordNextSignIn` 특성은 사용자가 다음 로그인 시 암호를 다시 설정해야 하는지 여부를 나타냅니다. 강제 암호 재설정을 처리하려면 [강제 암호 재설정 흐름을 설정](force-password-reset.md)합니다.

페더레이션(소셜) ID의 경우 **Passwordprofile** 특성이 필요하지 않습니다.

```json
"passwordProfile" : {
    "password": "password-value",
    "forceChangePasswordNextSignIn": false
  }
```

## <a name="password-policy-attribute"></a>암호 정책 특성

로컬 계정에 대한 Azure AD B2C 암호 정책은 Azure Active Directory [강력한 암호 강도](../active-directory/authentication/concept-sspr-policy.md) 정책을 기반으로 합니다. Azure AD B2C 등록 또는 로그인과 암호 재설정 정책에는 강력한 암호 강도가 필요하며 암호는 만료되지 않습니다.

사용자 마이그레이션 시나리오에서 마이그레이션하려는 계정이 Azure AD B2C에 의해 적용되는 [강력한 암호 강도](../active-directory/authentication/concept-sspr-policy.md)보다 약한 암호 강도를 사용하고 있다면 강력한 암호 강도를 사용하지 않도록 설정할 수 있습니다. 기본 암호 정책을 변경하려면 `passwordPolicies` 특성을 `DisableStrongPassword`로 설정합니다. 예를 들어 다음과 같이 사용자 만들기 요청을 수정할 수 있습니다.

```json
"passwordPolicies": "DisablePasswordExpiration, DisableStrongPassword"
```

## <a name="mfa-phone-number-attribute"></a>MFA 전화 번호 특성

MFA(다단계 인증)에 전화를 사용하는 경우 사용자 ID를 확인하기 위해 휴대폰을 사용합니다. 새 전화 번호를 프로그래밍 방식으로 [추가](/graph/api/authentication-post-phonemethods)하거나 전화 번호를 [업데이트](/graph/api/b2cauthenticationmethodspolicy-update), [가져오기](/graph/api/b2cauthenticationmethodspolicy-get) 또는 [삭제](/graph/api/phoneauthenticationmethod-delete)하려면 MS Graph API [전화 인증 방법을](/graph/api/resources/phoneauthenticationmethod) 사용합니다.

Azure AD B2C의 [사용자 지정 정책](custom-policy-overview.md)에서 전화 번호는 `strongAuthenticationPhoneNumber` 클레임 유형을 통해 사용할 수 있습니다.

## <a name="extension-attributes"></a>확장 특성

모든 고객 관련 애플리케이션은 수집해야 하는 정보에 관한 특정 요구 사항이 있습니다. Azure AD B2C 테넌트에는 이름, 성, 우편 번호 등 속성에 저장된 기본 정보 세트가 포함되어 있습니다. Azure AD B2C를 사용하면 각 고객 계정에 저장된 속성 세트를 확장할 수 있습니다. 자세한 내용은 [Azure Active Directory B2C에서 사용자 특성 추가 및 사용자 입력 사용자 지정](configure-user-input.md)을 참조하세요.

확장 특성은 디렉터리에서 사용자 개체의 [스키마를 확장](/graph/extensibility-overview#schema-extensions)합니다. 확장 특성은 사용자에 대한 데이터를 포함할 수 있더라도 애플리케이션 개체에만 등록할 수 있습니다. 확장 특성은 `b2c-extensions-app`이라는 애플리케이션에 연결됩니다. 이 애플리케이션은 사용자 데이터 저장을 위해 Azure AD B2C에서 사용하므로 수정하지 않습니다. 이 애플리케이션은 Azure Active Directory App 등록 아래에서 확인할 수 있습니다.

> [!NOTE]
> - 사용자 계정에 최대 100개의 확장 특성을 쓸 수 있습니다.
> - b2c-extensions-app 애플리케이션을 삭제할 경우, 확장 특성은 포함된 데이터와 함께 모든 사용자에게서 제거됩니다.
> - 확장 특성이 애플리케이션에 의해 삭제될 경우, 모든 사용자 계정에서 제거되고 값이 삭제됩니다.

Graph API의 확장 특성은 `extension_ApplicationClientID_AttributeName` 규칙을 사용하여 이름을 지정하며, 여기서 `ApplicationClientID`는 `b2c-extensions-app` 애플리케이션의 **애플리케이션(클라이언트) ID** 입니다(Azure Portal의 **앱 등록** > **모든 애플리케이션** 에서 찾을 수 있음). 확장 특성 이름에 표시되는 **애플리케이션(클라이언트) ID** 에는 하이픈이 포함되지 않습니다. 예를 들면 다음과 같습니다.

```json
"extension_831374b3bd5041bfaa54263ec9e050fc_loyaltyNumber": "212342"
```

스키마 확장에서 특성을 정의할 때 다음과 같은 데이터 형식이 지원됩니다.

|형식 |설명  |
|--------------|---------|
|부울    | 가능한 값: **true** 또는 **false**. |
|DateTime   | ISO 8601 형식으로 지정해야 합니다. UTC로 저장됩니다.   |
|정수    | 32비트 값.               |
|String     | 최대 256자.     |

## <a name="next-steps"></a>다음 단계

확장 특성에 대한 자세한 정보:

- [스키마 확장](/graph/extensibility-overview#schema-extensions)
- [사용자 지정 특성 정의](user-flow-custom-attributes.md)
