---
title: 사용자 지정 정책에서 Azure AD 기술 프로필 정의
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C의 사용자 지정 정책에서 Azure Active Directory 기술 프로필을 정의합니다.
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 12/11/2020
ms.author: kengaderdus
ms.subservice: B2C
ms.openlocfilehash: 08c2def39cf3fc673d83f1f1e371544eb679c45e
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "131008335"
---
# <a name="define-an-azure-active-directory-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Azure Active Directory B2C 사용자 지정 정책에서 Azure Active Directory 기술 프로필 정의

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure AD B2C(Azure Active Directory B2C)는 Azure Active Directory 사용자 관리를 지원합니다. 이 문서에서는 이 표준 프로토콜을 지원하는 클레임 공급자와 상호 작용하기 위한 기술 프로필에 대해 구체적으로 설명합니다.

## <a name="protocol"></a>프로토콜

**Protocol** 요소의 **Name** 특성은 `Proprietary`로 설정해야 합니다. **handler** 특성은 프로토콜 처리기 어셈블리의 정규화된 이름 `Web.TPEngine.Providers.AzureActiveDirectoryProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`을 포함해야 합니다.

[사용자 지정 정책 시작 팩](tutorial-create-user-flows.md?pivots=b2c-custom-policy#custom-policy-starter-pack) Azure AD 기술 프로필에는 **AAD-Common** 기술 프로필이 포함됩니다. 프로토콜이 **AAD-Common** 기술 프로필에 구성되어 있으므로 Azure AD 기술 프로필은 프로토콜을 지정하지 않습니다.
 
- **AAD-UserReadUsingAlternativeSecurityId** 및 **AAD-UserReadUsingAlternativeSecurityId-NoError** - 디렉터리에서 소셜 계정을 조회합니다.
- **AAD-UserWriteUsingAlternativeSecurityId** - 새 소셜 계정을 만듭니다.
- **AAD-UserReadUsingEmailAddress** - 디렉터리에서 로컬 계정을 조회합니다.
- **AAD-UserWriteUsingLogonEmail** - 새 로컬 계정을 만듭니다.
- **AAD-UserWritePasswordUsingObjectId** - 로컬 계정의 암호를 업데이트합니다.
- **AAD-UserWriteProfileUsingObjectId** - 로컬 또는 소셜 계정의 사용자 프로필을 업데이트합니다.
- **AAD-UserReadUsingObjectId** - 로컬 또는 소셜 계정의 사용자 프로필을 읽습니다.
- **AAD-UserWritePhoneNumberUsingObjectId** - 로컬 또는 소셜 계정의 MFA 전화 번호를 씁니다.

다음 예제는 **AAD-Common** 기술 프로필을 보여 줍니다.

```xml
<TechnicalProfile Id="AAD-Common">
  <DisplayName>Azure Active Directory</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AzureActiveDirectoryProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />

  <CryptographicKeys>
    <Key Id="issuer_secret" StorageReferenceId="B2C_1A_TokenSigningKeyContainer" />
  </CryptographicKeys>

  <!-- We need this here to suppress the SelfAsserted provider from invoking SSO on validation profiles. -->
  <IncludeInSso>false</IncludeInSso>
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
</TechnicalProfile>
```

## <a name="inputclaims"></a>InputClaims

InputClaims 요소는 디렉터리에서 계정을 조회하거나 새로 만드는 데 사용되는 클레임을 포함합니다. 모든 Azure AD 기술 프로필의 입력 클레임 컬렉션에 정확히 하나의 InputClaim 요소가 있어야 합니다. 정책에 정의된 클레임 이름을 Azure Active Directory에서 정의된 이름에 매핑해야 할 수도 있습니다.

기존 사용자 계정을 읽거나, 업데이트하거나, 삭제하기 위해 입력 클레임은 Azure AD 디렉터리에서 계정을 고유하게 식별하는 키입니다. 예를 들어, **objectId**, **userPrincipalName**, **signInNames.emailAddress**, **signInNames.userName** 또는 **alternativeSecurityId** 입니다. 

새 사용자 계정을 만들기 위해 입력 클레임은 로컬 또는 페더레이션된 계정을 고유하게 식별하는 키입니다. 예를 들어, 로컬 계정은 **signInNames.emailAddress** 또는 **signInNames.userName** 입니다. 페더레이션된 계정은 **alternativeSecurityId** 입니다.

[InputClaimsTransformations](technicalprofiles.md#input-claims-transformations) 요소는 입력 클레임을 수정하거나 새 클레임을 생성하는 데 사용되는 입력 클레임 변환 요소의 컬렉션을 포함할 수 있습니다.

## <a name="outputclaims"></a>OutputClaims

**OutputClaims** 요소는 Azure AD 기술 프로필에서 반환된 클레임 목록을 포함합니다. 정책에 정의된 클레임 이름을 Azure Active Directory에서 정의된 이름에 매핑해야 할 수도 있습니다. `DefaultValue` 특성만 설정하면, Azure Active Directory에서 반환되지 않은 클레임을 포함할 수도 있습니다.

[OutputClaimsTransformations](technicalprofiles.md#output-claims-transformations) 요소는 출력 클레임을 수정하거나 새 출력 클레임을 생성하는 데 사용되는 **OutputClaimsTransformation** 요소 컬렉션을 포함할 수 있습니다.

예를 들어 **AAD-UserWriteUsingLogonEmail** 기술 프로필은 로컬 계정을 만들고 다음 클레임을 반환합니다.

- **objectId** - 새 계정의 식별자입니다.
- **newUser** - 새 사용자인지 여부를 나타냅니다.
- **authenticationSource** - 인증을 `localAccountAuthentication`으로 설정합니다.
- **userPrincipalName** - 새 계정의 사용자 계정 이름입니다.
- **signInNames.emailAddress** - **email** 입력 클레임과 유사한 계정 로그인 이름입니다.

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="objectId" />
  <OutputClaim ClaimTypeReferenceId="newUser" PartnerClaimType="newClaimsPrincipalCreated" />
  <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="localAccountAuthentication" />
  <OutputClaim ClaimTypeReferenceId="userPrincipalName" />
  <OutputClaim ClaimTypeReferenceId="signInNames.emailAddress" />
</OutputClaims>
```

## <a name="persistedclaims"></a>PersistedClaims

**PersistedClaims** 요소는 정책의 [ClaimsSchema](claimsschema.md) 섹션에 이미 정의된 클레임 유형과 Azure AD 특성 이름 간에 가능한 매핑 정보를 사용하여 Azure AD에서 저장해야 하는 모든 값을 포함합니다.

새 로컬 계정을 만드는 **AAD-UserWriteUsingLogonEmail** 기술 프로필은 다음 클레임을 저장합니다.

```xml
  <PersistedClaims>
    <!-- Required claims -->
    <PersistedClaim ClaimTypeReferenceId="email" PartnerClaimType="signInNames.emailAddress" />
    <PersistedClaim ClaimTypeReferenceId="newPassword" PartnerClaimType="password"/>
    <PersistedClaim ClaimTypeReferenceId="displayName" DefaultValue="unknown" />
    <PersistedClaim ClaimTypeReferenceId="passwordPolicies" DefaultValue="DisablePasswordExpiration" />

    <!-- Optional claims. -->
    <PersistedClaim ClaimTypeReferenceId="givenName" />
    <PersistedClaim ClaimTypeReferenceId="surname" />
  </PersistedClaims>
```

Azure AD 특성 이름을 포함하는 **PartnerClaimType** 특성이 지정되지 않은 경우 클레임 이름은 Azure AD 특성 이름입니다.

## <a name="requirements-of-an-operation"></a>작업 요구사항

- 모든 Azure AD 기술 프로필의 클레임 모음에 정확히 하나의 **InputClaim** 요소가 있어야 합니다.
- [사용자 프로필 특성 문서](user-profile-attributes.md)에서는 입력 클레임, 출력 클레임 및 지속형 클레임에서 사용할 수 있는 지원되는 Azure AD B2C 사용자 프로필 특성을 설명합니다. 
- 작업이 `Write` 또는 `DeleteClaims`이면 **PersistedClaims** 요소에도 표시되어야 합니다.
- **userPrincipalName** 클레임의 값은 `user@tenant.onmicrosoft.com` 형식이어야 합니다.
- **displayName** 클레임은 필수이며 빈 문자열일 수 없습니다.

## <a name="azure-ad-technical-provider-operations"></a>Azure AD 기술 공급자 작업

### <a name="read"></a>읽기

**Read** 작업은 단일 사용자 계정에 대한 데이터를 읽습니다. 다음 기술 프로필은 사용자의 objectId를 사용하여 사용자 계정에 대한 데이터를 읽습니다.

```xml
<TechnicalProfile Id="AAD-UserReadUsingObjectId">
  <Metadata>
    <Item Key="Operation">Read</Item>
    <Item Key="RaiseErrorIfClaimsPrincipalDoesNotExist">true</Item>
  </Metadata>
  <IncludeInSso>false</IncludeInSso>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="objectId" Required="true" />
  </InputClaims>
  <OutputClaims>

    <!-- Required claims -->
    <OutputClaim ClaimTypeReferenceId="strongAuthenticationPhoneNumber" />

    <!-- Optional claims -->
    <OutputClaim ClaimTypeReferenceId="signInNames.emailAddress" />
    <OutputClaim ClaimTypeReferenceId="displayName" />
    <OutputClaim ClaimTypeReferenceId="otherMails" />
    <OutputClaim ClaimTypeReferenceId="givenName" />
    <OutputClaim ClaimTypeReferenceId="surname" />
  </OutputClaims>
  <IncludeTechnicalProfile ReferenceId="AAD-Common" />
</TechnicalProfile>
```

### <a name="write"></a>쓰기

**Write** 작업은 단일 사용자 계정을 만들거나 업데이트합니다. 다음 기술 프로필은 새 소셜 계정을 만듭니다.

```xml
<TechnicalProfile Id="AAD-UserWriteUsingAlternativeSecurityId">
  <Metadata>
    <Item Key="Operation">Write</Item>
    <Item Key="RaiseErrorIfClaimsPrincipalAlreadyExists">true</Item>
    <Item Key="UserMessageIfClaimsPrincipalAlreadyExists">You are already registered, please press the back button and sign in instead.</Item>
  </Metadata>
  <IncludeInSso>false</IncludeInSso>
  <InputClaimsTransformations>
    <InputClaimsTransformation ReferenceId="CreateOtherMailsFromEmail" />
  </InputClaimsTransformations>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="AlternativeSecurityId" PartnerClaimType="alternativeSecurityId" Required="true" />
  </InputClaims>
  <PersistedClaims>
    <!-- Required claims -->
    <PersistedClaim ClaimTypeReferenceId="alternativeSecurityId" />
    <PersistedClaim ClaimTypeReferenceId="userPrincipalName" />
    <PersistedClaim ClaimTypeReferenceId="mailNickName" DefaultValue="unknown" />
    <PersistedClaim ClaimTypeReferenceId="displayName" DefaultValue="unknown" />

    <!-- Optional claims -->
    <PersistedClaim ClaimTypeReferenceId="otherMails" />
    <PersistedClaim ClaimTypeReferenceId="givenName" />
    <PersistedClaim ClaimTypeReferenceId="surname" />
  </PersistedClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="objectId" />
    <OutputClaim ClaimTypeReferenceId="newUser" PartnerClaimType="newClaimsPrincipalCreated" />
    <OutputClaim ClaimTypeReferenceId="otherMails" />
  </OutputClaims>
  <IncludeTechnicalProfile ReferenceId="AAD-Common" />
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-AAD" />
</TechnicalProfile>
```

### <a name="deleteclaims"></a>DeleteClaims

**DeleteClaims** 작업은 제공된 클레임 목록에서 정보를 지웁니다. 다음 기술 프로필은 클레임을 삭제합니다.

```xml
<TechnicalProfile Id="AAD-DeleteClaimsUsingObjectId">
  <Metadata>
    <Item Key="Operation">DeleteClaims</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="objectId" Required="true" />
  </InputClaims>
  <PersistedClaims>
    <PersistedClaim ClaimTypeReferenceId="objectId" />
    <PersistedClaim ClaimTypeReferenceId="Verified.strongAuthenticationPhoneNumber" PartnerClaimType="strongAuthenticationPhoneNumber" />
  </PersistedClaims>
  <OutputClaims />
  <IncludeTechnicalProfile ReferenceId="AAD-Common" />
</TechnicalProfile>
```

### <a name="deleteclaimsprincipal"></a>DeleteClaimsPrincipal

**DeleteClaimsPrincipal** 작업은 디렉터리에서 단일 사용자 계정을 삭제합니다. 다음 기술 프로필은 사용자 계정 이름을 사용하여 디렉터리에서 사용자 계정을 삭제합니다.

```xml
<TechnicalProfile Id="AAD-DeleteUserUsingObjectId">
  <Metadata>
    <Item Key="Operation">DeleteClaimsPrincipal</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="objectId" Required="true" />
  </InputClaims>
  <OutputClaims/>
  <IncludeTechnicalProfile ReferenceId="AAD-Common" />
</TechnicalProfile>
```

다음 기술 프로필은 **alternativeSecurityId** 를 사용하여 소셜 사용자 계정을 삭제합니다.

```xml
<TechnicalProfile Id="AAD-DeleteUserUsingAlternativeSecurityId">
  <Metadata>
    <Item Key="Operation">DeleteClaimsPrincipal</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="alternativeSecurityId" Required="true" />
  </InputClaims>
  <OutputClaims/>
  <IncludeTechnicalProfile ReferenceId="AAD-Common" />
</TechnicalProfile>
```
## <a name="metadata"></a>메타데이터

| attribute | 필수 | 설명 |
| --------- | -------- | ----------- |
| 작업 | 예 | 수행할 작업입니다. 가능한 값은 `Read`, `Write`, `DeleteClaims` 또는 `DeleteClaimsPrincipal`입니다. |
| RaiseErrorIfClaimsPrincipalDoesNotExist | 예 | 사용자 개체가 디렉터리에 없는 경우 오류가 발생합니다. 가능한 값은 `true` 또는 `false`입니다. |
| RaiseErrorIfClaimsPrincipalAlreadyExists | 예 | 사용자 개체가 이미 있는 경우 오류가 발생합니다. 가능한 값은 `true` 또는 `false`입니다.|
| ApplicationObjectId | 예 | 확장 특성에 대한 애플리케이션 개체 ID입니다. 값은 애플리케이션의 ObjectId입니다. 자세한 내용은 [사용자 지정 특성 사용](user-flow-custom-attributes.md?pivots=b2c-custom-policy)을 참조하세요. |
| clientid | 예 | 테넌트에 제3자로 액세스하기 위한 클라이언트 식별자입니다. 자세한 내용은 [사용자 지정 프로필 편집 정책에서 사용자 지정 특성 사용](user-flow-custom-attributes.md?pivots=b2c-custom-policy)을 참조하세요. |
| IncludeClaimResolvingInClaimsHandling  | 예 | 입력 및 출력 클레임의 경우 기술 프로필에 [클레임 해결](claim-resolver-overview.md)이 포함되는지 여부를 지정합니다. 가능한 값은 `true` 또는 `false`(기본값)입니다. 기술 프로필에서 클레임 확인 프로그램을 사용하려는 경우 이 값을 `true`로 설정합니다. |

### <a name="ui-elements"></a>UI 요소
 
다음 설정을 사용하여 오류 발생 시 표시되는 오류 메시지를 구성할 수 있습니다. 메타데이터는 [자체 어설션](self-asserted-technical-profile.md) 기술 프로필에서 구성해야 합니다. 오류 메시지는 [지역화](localization.md)될 수 있습니다.

| attribute | 필수 | 설명 |
| --------- | -------- | ----------- |
| UserMessageIfClaimsPrincipalAlreadyExists | 예 | 오류가 발생해야 한다면(RaiseErrorIfClaimsPrincipalAlreadyExists 특성 설명 참조), 사용자 개체가 이미 있는 경우 사용자에게 표시할 메시지를 지정합니다. |
| UserMessageIfClaimsPrincipalDoesNotExist | 예 | 오류가 발생해야 한다면(RaiseErrorIfClaimsPrincipalDoesNotExist 특성 설명 참조), 사용자 개체가 없는 경우 사용자에게 표시할 메시지를 지정합니다. |


## <a name="next-steps"></a>다음 단계

Azure AD 기술 프로필을 사용하는 예제는 다음 문서를 참조하세요.

- [Azure Active Directory B2C에서 클레임 추가 및 사용자 지정 정책을 사용하여 사용자 입력 사용자 지정](configure-user-input.md)














