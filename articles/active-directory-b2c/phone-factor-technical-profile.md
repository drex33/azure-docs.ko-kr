---
title: 사용자 지정 정책에서 전화 단계 기술 프로필 정의
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C의 사용자 지정 정책에서 전화 단계 기술 프로필을 정의합니다.
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 10/12/2020
ms.author: kengaderdus
ms.subservice: B2C
ms.openlocfilehash: 17e21c28961346cd10b436adaa5eab8c8160bcd4
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "131012741"
---
# <a name="define-a-phone-factor-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Azure Active Directory B2C의 사용자 지정 정책에서 전화 단계 기술 프로필 정의

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure AD B2C(Azure Active Directory B2C)는 전화 번호를 등록하고 확인하는 기능을 지원합니다. 이 기술 프로필은 다음과 같은 특징이 있습니다.

- 사용자와 상호 작용하여 전화 번호를 확인하거나 등록하는 사용자 인터페이스를 제공합니다.
- 전화 통화 및 문자 메시지를 통한 전화 번호 유효성 검사를 지원합니다.
- 여러 전화 번호를 지원합니다. 사용자는 전화 번호 중 하나를 선택하여 확인할 수 있습니다.  
- 사용자가 새 전화 번호를 지정했는지 여부를 나타내는 클레임을 반환합니다. 이 클레임을 사용하여 Azure AD B2C 사용자 프로필에 전화 번호를 보관할지 여부를 결정할 수 있습니다.  
- [콘텐츠 정의](contentdefinitions.md)를 사용하여 모양과 느낌을 제어합니다.

## <a name="protocol"></a>프로토콜

**Protocol** 요소의 **Name** 특성은 `Proprietary`로 설정해야 합니다. **handler** 특성에는 Azure AD B2C에서 전화 단계에 사용되는 프로토콜 처리기 어셈블리의 정규화된 이름이 포함되어야 합니다. `Web.TPEngine.Providers.PhoneFactorProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`

다음 예제에서는 등록 및 유효성 검사의 전화 단계 기술 프로필을 보여 줍니다.

```xml
<TechnicalProfile Id="PhoneFactor-InputOrVerify">
  <DisplayName>PhoneFactor</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.PhoneFactorProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
</TechnicalProfile>
```

## <a name="input-claims-transformations"></a>입력 클레임 변환

InputClaimsTransformations 요소는 입력 클레임을 수정하거나 새 클레임을 생성하는 데 사용되는 입력 클레임 변환 컬렉션을 포함할 수 있습니다. 다음 입력 클레임 변환은 나중에 입력 클레임 컬렉션에서 사용되는 `UserId` 클레임을 생성합니다.

```xml
<InputClaimsTransformations>
  <InputClaimsTransformation ReferenceId="CreateUserIdForMFA" />
</InputClaimsTransformations>
```

## <a name="input-claims"></a>입력 클레임

InputClaims 요소는 다음 클레임을 포함해야 합니다. 클레임 이름을 전화 단계 기술 프로필에서 정의된 이름에 매핑할 수도 있습니다. 

|  데이터 형식| 필수 | Description |
| --------- | -------- | ----------- | 
| 문자열| 예 | 사용자의 고유 식별자입니다. 클레임 이름 또는 PartnerClaimType을 `UserId`로 설정해야 합니다. 이 클레임은 개인 식별이 가능한 정보를 포함해서는 안 됩니다.|
| 문자열| 예 | 클레임 유형 목록입니다. 각 클레임에는 하나의 전화 번호가 포함됩니다. 입력 클레임에 전화 번호가 포함되어 있지 않으면 사용자에게 새 전화 번호를 등록하고 확인하라는 메시지가 표시됩니다. 유효성을 검사한 전화 번호가 출력 클레임으로 반환됩니다. 입력 클레임 중 하나에 전화 번호가 포함된 경우 사용자에게 확인하라는 메시지가 표시됩니다. 여러 입력 클레임에 전화 번호가 포함된 경우 사용자에게 전화 번호 중 하나를 선택하고 확인하라는 메시지가 표시됩니다. |

다음 예제에서는 여러 전화 번호를 사용하는 방법을 보여 줍니다. 자세한 내용은 [샘플 정책](https://github.com/azure-ad-b2c/samples/tree/master/policies/mfa-add-secondarymfa)을 참조하세요.

```xml
<InputClaims>
  <InputClaim ClaimTypeReferenceId="userIdForMFA" PartnerClaimType="UserId" />
  <InputClaim ClaimTypeReferenceId="strongAuthenticationPhoneNumber" />
  <InputClaim ClaimTypeReferenceId="secondaryStrongAuthenticationPhoneNumber" />
</InputClaims>
```

## <a name="output-claims"></a>출력 클레임

OutputClaims 요소는 전화 단계 기술 프로필에서 반환된 클레임 목록을 포함합니다.

|  데이터 형식| 필수 | 설명 |
|  -------- | ----------- |----------- |
| boolean | 예 | 사용자가 새 전화 번호를 입력했는지 여부를 나타냅니다. 클레임 이름 또는 PartnerClaimType을 `newPhoneNumberEntered`로 설정해야 합니다.|
| 문자열| 예 | 확인된 전화 번호입니다. 클레임 이름 또는 PartnerClaimType을 `Verified.OfficePhone`으로 설정해야 합니다.|

OutputClaimsTransformations 요소는 출력 클레임을 수정하거나 새 출력 클레임을 생성하는 데 사용되는 OutputClaimsTransformation 요소 컬렉션을 포함할 수 있습니다.

## <a name="cryptographic-keys"></a>암호화 키

**CryptographicKeys** 요소는 사용되지 않습니다.


## <a name="metadata"></a>메타데이터

| attribute | 필수 | Description |
| --------- | -------- | ----------- |
| ContentDefinitionReferenceId | 예 | 이 기술 프로필과 연결된 [콘텐츠 정의](contentdefinitions.md)의 식별자입니다. |
| ManualPhoneNumberEntryAllowed| 예 | 사용자가 전화 번호를 수동으로 입력할 수 있는지 여부를 지정합니다. 가능한 값은 `true` 또는 `false`(기본값)입니다.|
| setting.authenticationMode | 예 | 전화 번호의 유효성을 검사하는 메서드입니다. 가능한 값은 `sms`, `phone` 또는 `mixed`(기본값)입니다.|
| setting.autodial| 예| 기술 프로필에서 자동으로 전화를 걸지, 아니면 자동으로 SMS를 보낼지를 지정합니다. 가능한 값은 `true` 또는 `false`(기본값)입니다. 자동으로 전화를 걸려면 `setting.authenticationMode` 메타데이터를 `sms` 또는 `phone`으로 설정해야 합니다. 입력 클레임 컬렉션에는 전화 번호가 하나 있어야 합니다. |

### <a name="ui-elements"></a>UI 요소

전화 단계 인증 페이지 사용자 인터페이스 요소를 [지역화](localization-string-ids.md#phone-factor-authentication-page-user-interface-elements)할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [MFA를 사용하는 소셜 및 로컬 계정](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/SocialAndLocalAccountsWithMfa) 스타터 팩을 확인합니다.
