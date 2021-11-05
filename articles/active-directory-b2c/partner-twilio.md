---
title: Azure Active Directory B2C와 Twilio Verify 앱 통합
titleSuffix: Azure AD B2C
description: Azure AD B2C의 샘플 온라인 결제 앱을 Twilio Verify API와 통합하는 방법을 알아봅니다. 동적 연결 및 강력한 고객 인증을 통해 PSD2(Payment Services Directive 2) 트랜잭션 요구 사항을 준수합니다.
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/20/2021
ms.author: kengaderdus
ms.subservice: B2C
ms.openlocfilehash: 49ca54ef90e4fb9c2f6263d10a36f511e64946a8
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131044497"
---
# <a name="integrating-twilio-verify-app-with-azure-active-directory-b2c"></a>Azure Active Directory B2C와 Twilio Verify 앱 통합

이 연습에서는 Azure AD B2C(Azure Active Directory B2C)의 샘플 온라인 결제 앱을 Twilio Verify API와 통합하는 방법을 알아봅니다. Twilio Verify 앱을 사용하면 Azure AD B2C 고객은 동적 연결 및 강력한 고객 인증을 통해 PSD2(Payment Services Directive 2) 트랜잭션 요구 사항을 준수할 수 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

시작하려면 다음이 필요합니다.

* Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* Azure 구독에 연결된 [Azure AD B2C 테넌트](tutorial-create-tenant.md)
* Twilio의 [체험 계정](https://www.twilio.com/try-twilio)

## <a name="scenario-description"></a>시나리오 설명

Twilio 솔루션의 구성 요소는 다음과 같습니다.

- .NET [PSD2 데모 웹앱](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Twilio-VerifyAPI/source-code/PSD2%20Demo%20App) - 로그인 또는 가입 기능을 제공하고 위험 수준이 높은 더미 트랜잭션을 수행합니다.
- Azure AD B2C 결합된 [로그인 및 가입 정책](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Twilio-VerifyAPI/policy).
- `id_token_hint`를 사용하여 Twilio Verify API와 통합된 Azure AD B2C 정책.
- .NET 웹앱 - `id_token_hint`의 유효성 검사를 허용하는 `.well-known` OpenIdConnect 엔드포인트를 호스트합니다.


    ![twilio 흐름](media/partner-twilio/twilio-flow.png)

| 단계 | 설명 |
|------|------|
| 1     | 사용자가 PSD2 데모 앱에 로그인 또는 가입을 시작합니다. 사용자는 Azure AD B2C 결합된 로그인 및 가입 정책을 통해 인증됩니다. 애플리케이션에 토큰이 반환됩니다. 가입 시 사용자의 전화 번호가 SMS/전화를 사용하여 확인되고 Azure AD B2C 계정에 기록됩니다.     |
| 2     | 사용자가 $50.00 송금과 같이 위험 수준이 높은 트랜잭션을 시작합니다. PolicyId에 대해 사용자의 현재 액세스 토큰을 평가하여 사용자가 버전 업그레이드 사용자 지정 정책을 통해 이미 인증되었는지 여부를 확인합니다.     |
| 3     | 애플리케이션에서 트랜잭션 값과 수취인, $50.00 및 John Doe를 기록하고 서명된 토큰을 생성합니다. 이 토큰은 `id_token_hint`라고 하며 클레임 `amount:$500, payee:john doe`를 포함합니다. `id_token_hint`는 Twilio와 통합된 Azure AD B2C 사용자 지정 정책에 대한 요청과 함께 전송됩니다.     |
| 4     | Azure AD B2C는 애플리케이션 `/.well-known` OpenId Connect 엔드포인트를 확인하여 id_token_hint의 서명을 확인합니다. 확인 후 이 토큰에서 클레임, 특히 `amount` 및 `payee`를 추출합니다. SMS 메시지를 통해 휴대폰 번호를 확인할 수 있는 페이지가 사용자에게 표시됩니다.     |
| 5     | 사용자가 SMS 메시지를 통해 전화번호를 확인하도록 요청하면 Azure AD B2C는 Twilio의 Verify API 엔드포인트에 대해 REST API 요청을 만듭니다. 또한 PSD2 프로세스의 일부로 `amount` 및 `payee` 트랜잭션을 전송하여 OTP(일회용 암호)를 생성합니다. Twilio는 사용자의 등록된 전화 번호로 SMS 메시지를 보냅니다.     |
| 6     |  사용자가 SMS 메시지를 통해 받은 OTP를 입력하고 Azure AD B2C에 제출합니다. Azure AD B2C는 이 OTP로 Twilio의 Verify API에 대한 API 요청을 만들어 OTP가 올바른지 확인합니다. 마지막으로 사용자가 인증을 업그레이드했음을 나타내는 새 PolicyId를 사용하는 토큰이 애플리케이션에 발급됩니다.    |
|      |      |

## <a name="onboard-with-twilio"></a>Twilio에 등록

1. Twilio에서 [체험 계정](https://www.twilio.com/try-twilio)을 얻습니다.

1. [이 문서](https://support.twilio.com/hc/articles/223135247-How-to-Search-for-and-Buy-a-Twilio-Phone-Number-from-Console)에 설명된 대로 Twilio에서 전화 번호를 구매합니다.

1. Twilio 콘솔에서 [Verify API](https://www.twilio.com/console/verify/services)로 이동하고 [지침](https://www.twilio.com/docs/verify/verifying-transactions-psd2)에 따라 서비스를 만들고 PSD2 옵션을 사용하도록 설정합니다.  

## <a name="configure-the-psd2-demo-app"></a>PSD2 데모 앱 구성

1. B2C-WebAPI-DotNet 솔루션을 열고 다음 값을 web.config의 고유한 테넌트 관련 값으로 바꿉니다.

    ```xml
    <add key="ida:Tenant" value="yourtenant.onmicrosoft.com" />
    <add key="ida:TenantId" value="d6f33888-0000-4c1f-9b50-1590f171fc70" />
    <add key="ida:ClientId" value="6bd98cc8-0000-446a-a05e-b5716ef2651b" />
    <add key="ida:ClientSecret" value="secret" />
    <add key="ida:AadInstance" value="https://yourtenant.b2clogin.com/tfp/{0}/{1}" />
    <add key="ida:RedirectUri" value="https://your hosted psd2 demo app url/" />
    ```

1. [웹앱](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Twilio-VerifyAPI/source-code/PSD2%20Demo%20App)은 ID 토큰 힌트 생성기 및 메타데이터 엔드포인트도 호스트합니다.

   - 이 [샘플 설명](https://github.com/azure-ad-b2c/samples/tree/master/policies/invite#creating-a-signing-certificate)에 설명된 대로 서명 인증서를 만듭니다.

   - web.config에서 인증서에 따라 다음 줄을 업데이트합니다.
   
     ```xml
     <add key="ida:SigningCertThumbprint" value="4F39D6014818082CBB763E5BA5F230E545212E89" />
     <add key="ida:SigningCertAlgorithm" value="RS256" />
     ```

1. 선택한 호스팅 공급자에 데모 애플리케이션을 업로드합니다. 인증서 업로드 지침을 포함하여 Azure App Service에 대한 지침이  [이 샘플 설명](https://github.com/azure-ad-b2c/samples/tree/master/policies/invite#hosting-the-application-in-azure-app-service)에서 제공됩니다.

1. 애플리케이션이 호스트되는 URL과 동일한 회신 URL을 추가하여 Azure AD B2C 애플리케이션 등록을 업데이트합니다.

1. [정책 파일](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Twilio-VerifyAPI/policy)을 열고  `contoso`의 모든 인스턴스를 테넌트 이름으로 바꿉니다.

1. Twilio REST API 기술 프로필  **Custom-SMS-Enroll** 을 찾습니다.  `ServiceURL` 을  Twilio AccountSID로 업데이트하고 보낸 사람 번호를 구매한 전화 번호로 업데이트합니다.

1. Twilio REST API 기술 프로필  **TwilioRestAPI-Verify-Step1**  및  **TwilioRestAPI-Verify-Step2** 를 찾고  `ServiceURL` 을 Twilio AccountSID로 업데이트합니다.

## <a name="integrate-with-azure-ad-b2c"></a>Azure AD B2C와 통합

Azure AD B2C에 정책 파일을 추가합니다.

1. Azure AD B2C 테넌트의 전역 관리자로 [Azure Portal](https://portal.azure.com/)에 로그인합니다.
1. Azure AD B2C 테넌트가 포함된 디렉터리를 사용하고 있는지 확인합니다. 포털 도구 모음에서 **디렉터리 + 구독** 아이콘을 선택합니다.
1. **포털 설정 | 디렉터리 + 구독** 페이지의 **디렉터리 이름** 목록에서 Azure AD B2C 디렉터리를 찾은 다음, **전환** 을 선택합니다.
1. Azure Portal의 왼쪽 상단 모서리에서 **모든 서비스** 를 선택하고 **Azure AD B2C** 를 검색하여 선택합니다.
1. **Azure AD B2C** > **Identity Experience Framework** > **정책 키** 로 이동합니다.
1. 이름이 **B2cRestTwilioClientId** 인 새 키를 추가합니다.  **수동** 을 선택하고 Twilio AccountSID의 값을 제공합니다.
1. 이름이 **B2cRestTwilioClientSecret** 인 새 키를 추가합니다.  **수동** 을 선택하고 Twilio 인증 토큰의 값을 제공합니다.
1. 테넌트에 모든 정책 파일을 업로드합니다.
1. 가입 SMS 문자에서 GenerateOTPMessageEnrol 클레임 변환의 문자열을 사용자 지정합니다.

## <a name="test-the-solution"></a>솔루션 테스트

* 애플리케이션으로 이동하여 로그인, 가입 및 송금 작업을 테스트합니다.

## <a name="next-steps"></a>다음 단계

자세한 내용은 다음 문서를 참조하세요.

- GitHub에서 [Twilio 통합 코드 샘플](https://github.com/azure-ad-b2c/samples/tree/master/policies/twilio-mfa-psd2)을 참조하세요.  

- [AAD B2C의 사용자 지정 정책](custom-policy-overview.md)

- [AAD B2C에서 사용자 지정 정책 시작](tutorial-create-user-flows.md?pivots=b2c-custom-policy)
