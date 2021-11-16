---
title: Azure Active Directory B2C를 사용한 TypingDNA
titleSuffix: Azure AD B2C
description: Azure AD B2C 인증을 TypingDNA와 통합하여 사용자 입력 패턴을 기반으로 ID 확인 및 교정을 지원하고 다단계 인증을 강제하는 ID 확인 솔루션을 제공하며 PSD2(Payment Services Directive 2)에 대한 SCA 요구 사항을 준수하는 방법에 대해 알아봅니다.
author: gargi-sinha
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 06/25/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 7c580974206a5eb4186180c997a33a5be2d02090
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130035793"
---
# <a name="tutorial-for-configuring-typingdna-with-azure-active-directory-b2c"></a>Azure Active Directory B2C를 사용하여 TypingDNA를 구성하기 위한 자습서

이 연습에서는 Azure Active Directory B2C의 샘플 온라인 결제 앱을 TypingDNA 앱과 통합하는 방법을 알아봅니다. TypingDNA 앱을 사용하면 Azure AD B2C 고객은 동적 키 입력 및 강력한 고객 인증을 통해 PSD2[(Payment Services Directive 2)](https://www.typingdna.com/use-cases/sca-strong-customer-authentication) 트랜잭션 요구 사항을 준수할 수 있습니다. TypingDNA에 대한 자세한 내용은 [여기](https://www.typingdna.com/)를 참조하십시오.

 Azure AD B2C는 TypingDNA의 기술을 사용하여 사용자 입력 특성을 캡처하고 각 인증에 대한 숙련도를 기록하고 분석합니다. 이것은 인증의 위험성과 관련된 보호 레이어를 추가하고 위험 수준을 평가합니다. Azure AD B2C는 다른 메커니즘을 호출하여 Azure AD MFA를 호출하고 이메일 확인을 강제하거나 시나리오에 대한 기타 사용자 지정 논리를 통해 사용자가 자신이 누구라고 주장하는지에 대해 더욱 신뢰를 제공할 수 있습니다.

>[!NOTE]
> 해당 샘플 정책은 [SocialAndLocalAccountsWithMfa](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/SocialAndLocalAccountsWithMfa) 스타터 팩을 기반으로 합니다.

## <a name="scenario-description"></a>시나리오 설명

![TypingDNA 아키텍처 다이어그램 스크린샷](./media/partner-typingdna/typingdna-architecture-diagram.png)

### <a name="sign-up"></a>등록

1. Azure AD B2C 페이지는 TypingDNA의 JavaScript 라이브러리를 사용하여 사용자의 입력 패턴을 기록합니다. 예를 들어 사용자 이름 및 암호는 초기 등록을 위해 가입 시 기록되고 이후 확인을 위해 로그인할 때마다 기록됩니다.

2. 사용자가 페이지를 제출하면 TypingDNA 라이브러리가 사용자의 입력 특성을 계산합니다. 그런 다음 Azure AD B2C가 렌더링된 숨겨진 텍스트 필드에 정보를 삽입합니다. 해당 필드는 CSS를 사용하여 숨겨집니다.  

    [샘플에는 JavaScript 및 CSS 수정 사항이 있는 HTML 파일](https://github.com/azure-ad-b2c/partner-integrations/blob/master/samples/TypingDNA/source-code/selfAssertedSignUp.cshtml)이 포함되어 있으며 `api.selfasserted.tdnasignin` 및 `api.selfasserted.tdnasignup` 콘텐츠 정의에서 참조됩니다. HTML 파일을 호스팅하려면 [페이지 콘텐츠 호스팅](./customize-ui-with-html.md#hosting-the-page-content)을 참조하세요.

3. 이제 Azure AD B2C에는 사용자가 자격 증명을 제출할 때 클레임 모음 내에 입력 패턴이 있습니다. 이 데이터를 TypingDNA REST API 엔드포인트로 전달하려면 (사용자의)API를 호출해야 합니다. 이 API는 [샘플(typingDNA-API-Interface)](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/TypingDNA/source-code/TypingDNA-API-Interface)에 포함되어 있습니다.
4. 그러면 중간 계층 API는 입력 패턴 데이터를 TypingDNA REST API로 전달합니다. 가입 시 사용자가 존재하지 않았음을 확인하기 위해 [사용자 엔드포인트 확인](https://api.typingdna.com/index.html#api-API_Services-GetUser)이 호출되고 사용자의 첫 번째 입력 패턴을 저장하기 위해 [패턴 저장](https://api.typingdna.com/index.html#api-API_Services-saveUserPattern) 엔드포인트가 호출됩니다.

> [!NOTE]
> TypingDNA REST API 엔드포인트에 대한 모든 호출은 UserId를 보냅니다. 해시 값이어야 합니다. Azure AD B2C는 `HashObjectIdWithEmail` 클레임 변환을 사용하여 임의의 솔트 및 암호로 이메일을 해시합니다.  

REST API 호출은 `LocalAccountSignUpWithLogonEmail-TDNA` 내에서 `validationTechnicalProfiles`로 모델링됩니다:

```xml

<ValidationTechnicalProfiles>

    <ValidationTechnicalProfile ReferenceId="AAD-UserWriteUsingLogonEmail-TDNA" />
    <ValidationTechnicalProfile ReferenceId="REST-TDNA-CheckUser" ContinueOnError="true"/>

    <ValidationTechnicalProfile ReferenceId="REST-TDNA-SaveUser"/>

</ValidationTechnicalProfiles>

```

### <a name="sign-in"></a>로그인

후속 로그인 시 사용자의 입력 패턴은 [사용자 지정 HTML](https://github.com/azure-ad-b2c/partner-integrations/blob/master/samples/TypingDNA/source-code/selfAssertedSignIn.cshtml)을 사용하여 가입 시와 동일한 방식으로 계산됩니다. 입력 프로필이 Azure AD B2C 클레임 모음 내에 있으면 Azure AD B2C는 API를 호출하여 TypingDNA REST API 엔드포인트를 호출합니다. [사용자 확인](https://api.typingdna.com/index.html#api-API_Services-GetUser) 엔드포인트가 호출되어 사용자가 있는지 확인합니다. 다음으로 [패턴 확인](https://api.typingdna.com/index.html#api-API_Services-verifyTypingPattern) 엔드포인트가 호출되어 `net_score`을 반환합니다. 이 `net_score`은 가입시 입력 패턴이 원본과 얼마나 근접했는지를 나타냅니다.

이 입력 패턴은 `SelfAsserted-LocalAccountSignin-Email-TDNA` 내에서 `validationTechnicalProfiles`로 모델링됩니다:

```xml

<ValidationTechnicalProfiles>

    <ValidationTechnicalProfile ReferenceId="login-NonInteractive"/>

    <ValidationTechnicalProfile ReferenceId="REST-TDNA-CheckUser" ContinueOnError="false"/>

    <ValidationTechnicalProfile ReferenceId="REST-TDNA-VerifyUser"/>

    <ValidationTechnicalProfile ReferenceId="REST-TDNA-SaveUser">

        <Preconditions>

            <Precondition Type="ClaimEquals" ExecuteActionsIf="true">

            <Value>saveTypingPattern</Value>

            <Value>False</Value>

            <Action>SkipThisValidationTechnicalProfile</Action>

            </Precondition>

        </Preconditions>

    </ValidationTechnicalProfile>

</ValidationTechnicalProfiles>

```

 사용자가 `net_score`이 높은 입력 패턴을 가져오는 경우 TypingDNA [입력 패턴 저장](https://api.typingdna.com/index.html#api-API_Services-saveUserPattern) 엔드포인트를 사용하여 해당 패턴을 저장할 수 있습니다.  

(API를 통해)Azure AD B2C에서 TypingDNA 저장 입력 패턴 엔드포인트를 호출하려면 API에서 클레임 `saveTypingPattern`을 반환해야 합니다.

리포지토리의 예제에는 다음 속성으로 구성된 API(TypingDNA-API-Interface)가 포함되어 있습니다.

- 학습 모드 - 사용자에게 저장된 패턴이 2개 미만인 경우 항상 MFA를 묻는 메시지를 표시합니다.

- 사용자에게 2-5개의 패턴이 저장되어 있고 `net_score`이 50보다 작은 경우 MFA를 묻는 메시지를 표시합니다.

- 사용자에게 5개 이상의 패턴이 저장되어 있고 `net_score`이 65보다 작은 경우 MFA를 묻는 메시지를 표시합니다.

이러한 임계값은 사용 사례에 따라 조정해야 합니다.

- API가 `net_score`을 평가한 후 B2C - `promptMFA`에 부울 클레임을 반환해야 합니다.

- `promptMFA` 클레임은 Azure AD MFA를 조건부로 실행하기 위한 전제 조건 내에서 사용됩니다.

```xml

<OrchestrationStep Order="3" Type="ClaimsExchange">

    <Preconditions>

        <Precondition Type="ClaimsExist" ExecuteActionsIf="true">

            <Value>isActiveMFASession</Value>

            <Action>SkipThisOrchestrationStep</Action>

        </Precondition>

        <Precondition Type="ClaimEquals" ExecuteActionsIf="true">

            <Value>promptMFA</Value>

            <Value>False</Value>

            <Action>SkipThisOrchestrationStep</Action>

        </Precondition>

    </Preconditions>

    <ClaimsExchanges>

        <ClaimsExchange Id="PhoneFactor-Verify" TechnicalProfileReferenceId="PhoneFactor-InputOrVerify" />

    </ClaimsExchanges>

</OrchestrationStep>

```

## <a name="onboard-with-typingdna"></a>TypingDNA로 온보딩

1. [여기](https://www.typingdna.com/)에서 TypingDNA에 가입
2. TypingDNA 대시보드에 로그인하여 **API 키** 및 **API 암호** 를 가져옵니다. 나중에 API 인터페이스 설정에 필요합니다.

## <a name="integrate-typingdna-with-azure-ad-b2c"></a>TypingDNA를 Azure AD B2C와 통합

1. 선택한 호스팅 공급자에서 [TypingDNA-API-Interface](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/TypingDNA/source-code/TypingDNA-API-Interface)를 호스팅
2. [TypingDNA-API-Interface](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/TypingDNA/source-code/TypingDNA-API-Interface) 솔루션에서 `apiKey` 및 `apiSecret`의 모든 인스턴스를 TypingDNA 대시보드의 자격 증명으로 변경
3. [여기](./customize-ui-with-html.md#3-configure-cors)에서 CORS 요구 사항에 따라 선택한 공급자에서 HTML 파일을 호스팅
4. `TrustFrameworkExtensions.xml` 파일의 `api.selfasserted.tdnasignup` 및 `api.selfasserted.tdnasignin` 콘텐츠 정의에 대한 LoadURI 요소를 각각 호스팅된 HTML 파일의 URI로 바꿉니다.
5. **Azure Portal** 의 Azure AD 블레이드에 있는 ID 경험 프레임워크에서 B2C 정책 키를 만듭니다. `Generate` 옵션을 사용하고 이 키의 이름을 `tdnaHashedId`로 합니다.
6. 정책 파일에서 TenantId를 변경
7. 모든 TypingDNA REST API 기술 프로필(REST-TDNA-VerifyUser, REST-TDNA-SaveUser, REST-TDNA-CheckUser)의 ServiceURL을 [TypingDNA-API-Interface API](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/TypingDNA/source-code/TypingDNA-API-Interface)의 엔드포인트로 바꿉니다.
8. 테넌트에 [정책 파일](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/TypingDNA/policy)을 업로드합니다.

## <a name="test-the-user-flow"></a>사용자 흐름 테스트

1. B2C 테넌트를 열고 ID 경험 프레임워크를 선택합니다.
2. 이전에 만든 **사용자 흐름** 을 선택합니다.
3. 사용자 흐름 **실행** 선택

    a. **애플리케이션** - 등록된 앱 선택(JWT는 샘플)

    b. **회신 URL** - 리디렉션 URL 선택

    다. **사용자 흐름 실행** 을 선택합니다.
  
4. 등록 흐름을 따라 계정 만들기
5. 로그아웃
6. 로그인 흐름 진행
7. JWT 결과가 TypingDNA 결과를 표시

## <a name="live-version"></a>라이브 버전

• 이 테스트 버전에서는 MFA가 사용하지 않도록 설정되었지만 인증 후 클레임 `promptMFA`에 의해 MFA에 메시지가 표시되는지 여부에 대한 결과를 볼 수 있습니다.

• [여기](https://b2cprod.b2clogin.com/b2cprod.onmicrosoft.com/oauth2/v2.0/authorize?p=B2C_1A_SU_TDNA&client_id=51d907f8-db14-4460-a1fd-27eaeb2a74da&nonce=defaultNonce&redirect_uri=https://jwt.ms/&scope=openid&response_type=id_token&prompt=login)에서 등록하고 [여기](https://b2cprod.b2clogin.com/b2cprod.onmicrosoft.com/oauth2/v2.0/authorize?p=B2C_1A_SI_TDNA&client_id=51d907f8-db14-4460-a1fd-27eaeb2a74da&nonce=defaultNonce&redirect_uri=https://jwt.ms/&scope=openid&response_type=id_token&prompt=login)에서 로그인하세요.

## <a name="next-steps"></a>다음 단계

자세한 내용은 다음 문서를 참조하세요:

- [AAD B2C의 사용자 지정 정책](./custom-policy-overview.md)

- [AAD B2C에서 사용자 지정 정책 시작](tutorial-create-user-flows.md?pivots=b2c-custom-policy)