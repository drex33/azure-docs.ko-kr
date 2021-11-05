---
title: ClaimsProviders  - Azure Active Directory B2C
description: Azure Active Directory B2C에서 사용자 지정 정책의 ClaimsProvider 요소를 지정하는 방법을 설명합니다.
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/08/2021
ms.author: kengaderdus
ms.subservice: B2C
ms.openlocfilehash: adac50058a34253bec40a765a47379b2fb52dcef
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "131007974"
---
# <a name="claimsproviders"></a>ClaimsProviders

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

클레임은 [기술 프로필](technicalprofiles.md)을 통해 서로 다른 유형의 당사자와 통신하는 인터페이스입니다. 모든 클레임 공급자에는 엔드포인트를 결정하는 하나 이상의 기술 프로필 및 클레임 공급자와 통신하는 데 필요한 프로토콜이 있어야 합니다. 하나의 클레임 공급자가 여러 개의 기술 프로필을 포함할 수 있습니다. 예를 들어, 클레임 공급자가 여러 프로토콜, 다른 기능을 가진 다양한 엔드포인트를 지원하거나 여러 보증 수준에서 다양한 클레임을 해제하므로 여러 기술 프로필을 정의할 수 있습니다. 다른 사용자 경험이 아닌 하나의 사용자 경험에서 중요한 클레임을 해제하기 위해 허용할 수도 있습니다.

사용자 경험은 오케스트레이션 단계를 통해 호출 기술 프로필을 결합하여 비즈니스 논리를 정의합니다. 

```xml
<ClaimsProviders>
  <ClaimsProvider>
    <Domain>Domain name</Domain>
    <DisplayName>Display name</DisplayName>
    <TechnicalProfiles>
      </TechnicalProfile>
        ...
      </TechnicalProfile>
        ...
    </TechnicalProfiles>
  </ClaimsProvider>
  ...
</ClaimsProviders>
```

**ClaimsProviders** 요소에는 다음과 같은 요소가 포함됩니다.

| 요소 | 발생 수 | 설명 |
| ------- | ----------- | ----------- |
| ClaimsProvider | 1:n | 다양한 사용자 journeys에서 활용할 수 있는 인증된 청구 공급자입니다. |

## <a name="claimsprovider"></a>ClaimsProvider

**ClaimsProvider** 요소에는 다음과 같은 하위 요소가 포함됩니다.

| 요소 | 발생 수 | 설명 |
| ------- | ---------- | ----------- |
| 도메인 | 0:1 | 클레임 공급자의 도메인 이름을 포함하는 문자열입니다. 예를 들어, 클레임 공급자에 Facebook 기술 프로필이 포함된 경우 도메인 이름은 Facebook.com입니다. 이 도메인 이름은 기술 프로필에 의해 대체되지 않는 한, 청구 제공자에 정의된 모든 기술 프로필에 사용됩니다. 도메인 이름은 **domain_hint** 에서 참조할 수도 있습니다. 자세한 내용은 [Azure Active Directory B2C를 사용하여 직접 로그인 설정](direct-signin.md) 섹션의 **소셜 공급자로 로그인 리디렉션** 섹션을 참조하세요. |
| DisplayName | 1:1 | 클레임 공급자의 이름을 포함하는 문자열입니다. |
| [TechnicalProfiles](technicalprofiles.md) | 0:1 | 클레임 공급자가 지원하는 기술 프로필 세트입니다. |

**ClaimsProvider** 는 기술적 프로필이 클레임 공급자와 관련되는 방식을 구성합니다. 다음 예에서는 Azure Active Directory 기술 프로필을 사용하여 Azure Active Directory 클레임 공급자를 보여 줍니다.

```xml
<ClaimsProvider>
  <DisplayName>Azure Active Directory</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="AAD-Common">
      ...
    </TechnicalProfile>
    <TechnicalProfile Id="AAD-UserWriteUsingAlternativeSecurityId">
      ...
    </TechnicalProfile>
    <TechnicalProfile Id="AAD-UserReadUsingAlternativeSecurityId">
      ...
    </TechnicalProfile>
    <TechnicalProfile Id="AAD-UserReadUsingAlternativeSecurityId-NoError">
      ...
    </TechnicalProfile>
    <TechnicalProfile Id="AAD-UserReadUsingEmailAddress">
      ...
    </TechnicalProfile>
      ...
    <TechnicalProfile Id="AAD-UserWritePasswordUsingObjectId">
      ...
    </TechnicalProfile>
    <TechnicalProfile Id="AAD-UserWriteProfileUsingObjectId">
      ...
    </TechnicalProfile>
    <TechnicalProfile Id="AAD-UserReadUsingObjectId">
      ...
    </TechnicalProfile>
    <TechnicalProfile Id="AAD-UserWritePhoneNumberUsingObjectId">
      ...
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

다음 예제는 **Facebook-OAUTH** 기술 프로필을 사용하여 Facebook 클레임 공급자를 보여 줍니다.

```xml
<ClaimsProvider>
  <Domain>facebook.com</Domain>
  <DisplayName>Facebook</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="Facebook-OAUTH">
      <DisplayName>Facebook</DisplayName>
      <Protocol Name="OAuth2" />
        ...
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```
