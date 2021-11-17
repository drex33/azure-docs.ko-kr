---
title: SendGrid를 사용한 사용자 지정 이메일 확인
titleSuffix: Azure AD B2C
description: 고객이 Azure AD B2C 지원 애플리케이션을 사용하기 위해 등록할 때 고객에게 전송되는 확인 이메일을 사용자 지정하기 위해 SendGrid와 통합하는 방법을 알아봅니다.
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/15/2021
ms.author: kengaderdus
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 5bb6c3aef0476e3da440eb8523d0ccc09491e074
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131501403"
---
# <a name="custom-email-verification-with-sendgrid"></a>SendGrid를 사용한 사용자 지정 이메일 확인

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

Azure Active Directory B2C(Azure AD B2C)에서 사용자 지정 메일을 사용하여 애플리케이션을 사용하기 위해 등록하는 사용자에게 사용자 지정 메일을 보냅니다. 타사 이메일 공급자 SendGrid를 사용하여 사용자 고유의 이메일 템플릿과 *보낸 사람:* 주소 및 제목을 사용하고 현지화 및 사용자 지정 OTP(일회용 암호) 설정을 지원할 수 있습니다.

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

사용자 지정 이메일 확인을 위해서는 [SendGrid](https://sendgrid.com), [Mailjet](https://Mailjet.com) 또는 [SparkPost](https://sparkpost.com)와 같은 타사 이메일 공급자, 사용자 지정 REST API 또는 HTTP 기반 이메일 공급자(자체 공급자 포함)를 사용해야 합니다. 이 문서에서는 SendGrid를 사용하는 솔루션 설정 방법을 설명합니다.

## <a name="create-a-sendgrid-account"></a>SendGrid 계정 만들기

아직 없는 경우 먼저 SendGrid 계정을 설정합니다(Azure 고객은 매달 25,000통의 무료 메일을 잠금 해제할 수 있음). 설정 지침은 [Azure에서 SendGrid를 사용하여 이메일을 보내는 방법](https://docs.sendgrid.com/for-developers/partners/microsoft-azure-2021#create-a-twilio-sendgrid-accountcreate-a-twilio-sendgrid-account)의 [SendGrid 계정 만들기](https://docs.sendgrid.com/for-developers/partners/microsoft-azure-2021#create-a-sendgrid-account) 섹션을 참조하세요.

[SendGrid API 키 만들기](https://docs.sendgrid.com/for-developers/partners/microsoft-azure-2021#to-find-your-sendgrid-api-key) 섹션을 완료해야 합니다. 이후 단계에서 사용할 API 키를 기록합니다.

> [!IMPORTANT]
> SendGrid는 고객에게 공유 IP 및 [전용 IP 주소](https://sendgrid.com/docs/ui/account-and-settings/dedicated-ip-addresses/)에서 이메일을 보낼 수 있는 기능을 제공합니다. 전용 IP 주소를 사용하는 경우 IP 주소 워밍업으로 자신의 평판을 제대로 구축해야 합니다. 자세한 내용은 [IP 주소 워밍업](https://sendgrid.com/docs/ui/sending-email/warming-up-an-ip-address/)을 참조하세요.

## <a name="create-azure-ad-b2c-policy-key"></a>Azure AD B2C 정책 키 만들기

다음으로, 정책이 참조할 SendGrid API 키를 Azure AD B2C 정책 키에 저장합니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
1. Azure AD B2C 테넌트가 포함된 디렉터리를 사용하고 있는지 확인합니다. 포털 도구 모음에서 **디렉터리 + 구독** 아이콘을 선택합니다.
1. **포털 설정 | 디렉터리 + 구독** 페이지의 **디렉터리 이름** 목록에서 Azure AD B2C 디렉터리를 찾은 다음, **전환** 을 선택합니다.
1. Azure Portal의 왼쪽 상단 모서리에서 **모든 서비스** 를 선택하고 **Azure AD B2C** 를 검색하여 선택합니다.
1. 개요 페이지에서 **ID 경험 프레임워크** 를 선택합니다.
1. **정책 키**, **추가** 를 차례로 선택합니다.
1. **옵션** 에서 **수동** 을 선택합니다.
1. 정책 키의 **이름** 을 입력합니다. 예들 들어 `SendGridSecret`입니다. `B2C_1A_` 접두사가 키의 이름에 자동으로 추가됩니다.
1. **비밀** 에 이전에 기록한 SendGrid API 키를 입력합니다.
1. **키 사용** 으로는 **서명** 을 선택합니다.
1. **만들기** 를 선택합니다.

## <a name="create-sendgrid-template"></a>SendGrid 템플릿 만들기

SendGrid 계정을 만들고 Azure AD B2C 정책 키에 SendGrid API 키를 저장했으면 SendGrid [동적 트랜잭션 템플릿](https://sendgrid.com/docs/ui/sending-email/how-to-send-an-email-with-dynamic-transactional-templates/)을 만듭니다.

1. SendGrid 사이트에서 [트랜잭션 템플릿](https://sendgrid.com/dynamic_templates) 페이지를 열고 **동적 템플릿 만들기** 를 선택합니다.
1. `Verification email`과 같은 고유한 템플릿 이름을 입력한 다음, **만들기** 를 선택합니다.
1. 새 템플릿 편집을 시작하려면 템플릿(예: `Verification email`)을 선택한 다음, **버전 추가** 를 선택합니다.
1. **빈 템플릿** 을 선택한 다음, **코드 편집기** 를 선택합니다.
1. HTML 편집기에서 다음 HTML 템플릿을 붙여넣거나 사용자 고유의 템플릿을 사용합니다. `{{otp}}` 매개 변수와 `{{email}}` 매개 변수는 일회성 암호 값 및 사용자 메일 주소로 동적으로 교체됩니다.

    ```HTML
    <!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">

    <html xmlns="http://www.w3.org/1999/xhtml" dir="ltr" lang="en"><head id="Head1">
    <meta http-equiv="Content-Type" content="text/html; charset=utf-8"><title>Contoso demo account email verification code</title><meta name="ROBOTS" content="NOINDEX, NOFOLLOW">
       <style>
           table td {border-collapse:collapse;margin:0;padding:0;}
       </style>
    </head>
    <body dir="ltr" lang="en">
       <table width="100%" cellpadding="0" cellspacing="0" border="0" dir="ltr" lang="en">
            <tr>
               <td valign="top" width="50%"></td>
               <td valign="top">
                  <!-- Email Header -->
                  <table width="640" cellpadding="0" cellspacing="0" border="0" dir="ltr" lang="en" style="border-left:1px solid #e3e3e3;border-right: 1px solid #e3e3e3;">
                   <tr style="background-color: #0072C6;">
                       <td width="1" style="background:#0072C6; border-top:1px solid #e3e3e3;"></td>
                       <td width="24" style="border-top:1px solid #e3e3e3;border-bottom:1px solid #e3e3e3;">&nbsp;</td>
                       <td width="310" valign="middle" style="border-top:1px solid #e3e3e3; border-bottom:1px solid #e3e3e3;padding:12px 0;">
                           <h1 style="line-height:20pt;font-family:Segoe UI Light; font-size:18pt; color:#ffffff; font-weight:normal;">
                            <span id="HeaderPlaceholder_UserVerificationEmailHeader"><font color="#FFFFFF">Verify your email address</font></span>
                           </h1>
                       </td>
                       <td width="24" style="border-top: 1px solid #e3e3e3;border-bottom: 1px solid #e3e3e3;">&nbsp;</td>
                   </tr>
                  </table>
                  <!-- Email Content -->
                  <table width="640" cellpadding="0" cellspacing="0" border="0" dir="ltr" lang="en">
                   <tr>
                       <td width="1" style="background:#e3e3e3;"></td>
                       <td width="24">&nbsp;</td>
                       <td id="PageBody" width="640" valign="top" colspan="2" style="border-bottom:1px solid #e3e3e3;padding:10px 0 20px;border-bottom-style:hidden;">
                           <table cellpadding="0" cellspacing="0" border="0">
                               <tr>
                                   <td width="630" style="font-size:10pt; line-height:13pt; color:#000;">
                                       <table cellpadding="0" cellspacing="0" border="0" width="100%" style="" dir="ltr" lang="en">
                                           <tr>
                                               <td>

       <div style="font-family:'Segoe UI', Tahoma, sans-serif; font-size:14px; color:#333;">
           <span id="BodyPlaceholder_UserVerificationEmailBodySentence1">Thanks for verifying your {{email}} account!</span>
       </div>
       <br>
       <div style="font-family:'Segoe UI', Tahoma, sans-serif; font-size:14px; color:#333; font-weight: bold">
           <span id="BodyPlaceholder_UserVerificationEmailBodySentence2">Your code is: {{otp}}</span>
       </div>
       <br>
       <br>

                                                   <div style="font-family:'Segoe UI', Tahoma, sans-serif; font-size:14px; color:#333;">
                                                   Sincerely,
                                                   </div>
                                                   <div style="font-family:'Segoe UI', Tahoma, sans-serif; font-size:14px; font-style:italic; color:#333;">
                                                       Contoso
                                                   </div>
                                               </td>
                                           </tr>
                                       </table>
                                   </td>
                               </tr>
                           </table>

                       </td>

                       <td width="1">&nbsp;</td>
                       <td width="1"></td>
                       <td width="1">&nbsp;</td>
                       <td width="1" valign="top"></td>
                       <td width="29">&nbsp;</td>
                       <td width="1" style="background:#e3e3e3;"></td>
                   </tr>
                   <tr>
                       <td width="1" style="background:#e3e3e3; border-bottom:1px solid #e3e3e3;"></td>
                       <td width="24" style="border-bottom:1px solid #e3e3e3;">&nbsp;</td>
                       <td id="PageFooterContainer" width="585" valign="top" colspan="6" style="border-bottom:1px solid #e3e3e3;padding:0px;">

                       </td>

                       <td width="29" style="border-bottom:1px solid #e3e3e3;">&nbsp;</td>
                       <td width="1" style="background:#e3e3e3; border-bottom:1px solid #e3e3e3;"></td>
                   </tr>
                  </table>

               </td>
               <td valign="top" width="50%"></td>
           </tr>
       </table>
    <img src="https://mucp.api.account.microsoft.com/m/v2/v?d=AIAACWEPFYXYIUTJIJVV4ST7XLBHVI5MLLYBKJAVXHBDTBHUM5VBSVVPTTVRWDFIXJ5JQTHYOH5TUYIPO4ZAFRFK52UAMIS3UNIPPI7ZJNDZPRXD5VEJBN4H6RO3SPTBS6AJEEAJOUYL4APQX5RJUJOWGPKUABY&amp;i=AIAACL23GD2PFRFEY5YVM2XQLM5YYWMHFDZOCDXUI2B4LM7ETZQO473CVF22PT6WPGR5IIE6TCS6VGEKO5OZIONJWCDMRKWQQVNP5VBYAINF3S7STKYOVDJ4JF2XEW4QQVNHMAPQNHFV3KMR3V3BA4I36B6BO7L4VQUHQOI64EOWPLMG5RB3SIMEDEHPILXTF73ZYD3JT6MYOLAZJG7PJJCAXCZCQOEFVH5VCW2KBQOKRYISWQLRWAT7IINZ3EFGQI2CY2EMK3FQOXM7UI3R7CZ6D73IKDI" width="1" height="1"></body>
    </html>
    ```

1. 왼쪽에서 **설정** 을 확장하고 **버전 이름** 에 템플릿 버전을 입력합니다. 
1. **제목** 에 대해 `{{subject}}`를 입력합니다.
1. 페이지 맨 위에서 **저장** 을 선택합니다.
1. 뒤로 화살표를 선택하여 **트랜잭션 템플릿** 페이지로 돌아갑니다.
1. 이후 단계에서 사용할 수 있도록 만든 템플릿의 **ID** 를 기록합니다. 예들 들어 `d-989077fbba9746e89f3f6411f596fb96`입니다. [클레임 변환을 추가](#add-the-claims-transformation)할 때 해당 ID를 지정합니다.

## <a name="add-azure-ad-b2c-claim-types"></a>Azure AD B2C 클레임 형식 추가

정책에서 다음 클레임 형식을 `<BuildingBlocks>` 내의 `<ClaimsSchema>` 요소에 추가합니다.

해당 클레임 형식은 OTP(일회성 암호) 코드를 사용하여 이메일 주소를 생성 및 확인하는 데 필요합니다.

```xml
<!-- 
<BuildingBlocks>
  <ClaimsSchema> -->
    <ClaimType Id="Otp">
      <DisplayName>Secondary One-time password</DisplayName>
      <DataType>string</DataType>
    </ClaimType>
    <ClaimType Id="emailRequestBody">
      <DisplayName>SendGrid request body</DisplayName>
      <DataType>string</DataType>
    </ClaimType>
    <ClaimType Id="VerificationCode">
      <DisplayName>Secondary Verification Code</DisplayName>
      <DataType>string</DataType>
      <UserHelpText>Enter your email verification code</UserHelpText>
      <UserInputType>TextBox</UserInputType>
    </ClaimType>
  <!-- 
  </ClaimsSchema>
</BuildingBlocks> -->
```

## <a name="add-the-claims-transformation"></a>클레임 변환 추가

다음으로, SendGrid로 전송된 요청의 본문이 될 JSON 문자열 클레임을 출력하는 클레임 변환이 필요합니다.

JSON 개체의 구조는 InputParameters의 점 표기법의 ID와 InputClaims의 TransformationClaimTypes로 정의됩니다. 점 표기법의 숫자는 배열을 의미합니다. 값은 InputClaims의 값과 InputParameters의 “Value” 속성에서 가져옵니다. JSON 클레임 변환에 대한 자세한 내용은 [JSON 클레임 변환](json-transformations.md)을 참조하세요.

`<BuildingBlocks>` 내에서 `<ClaimsTransformations>` 요소에 다음 클레임 변환을 추가합니다. 클레임 변환 XML에 다음 사항을 업데이트합니다.

* `template_id`InputParameter 값을 앞서 [SendGrid 템플릿 만들기](#create-sendgrid-template)에서 만든 SendGrid 트랜잭션 템플릿 ID로 업데이트합니다.
* `from.email` 주소 값을 업데이트합니다. 확인 이메일이 스팸으로 표시되지 않도록 유효한 이메일 주소를 사용합니다.
   > [!NOTE]
   > 이 이메일 주소는 도메인 인증 또는 단일 보낸 사람 인증을 사용하여 보낸 사람 인증 아래의 SendGrid에서 확인되어야 합니다.
* `personalizations.0.dynamic_template_data.subject` 제목 줄 입력 매개 변수의 값을 조직에 적절한 제목 줄로 업데이트합니다.

```xml
<!-- 
<BuildingBlocks>
  <ClaimsTransformations> -->
    <ClaimsTransformation Id="GenerateEmailRequestBody" TransformationMethod="GenerateJson">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="personalizations.0.to.0.email" />
        <InputClaim ClaimTypeReferenceId="otp" TransformationClaimType="personalizations.0.dynamic_template_data.otp" />
        <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="personalizations.0.dynamic_template_data.email" />
      </InputClaims>
      <InputParameters>
        <!-- Update the template_id value with the ID of your SendGrid template. -->
        <InputParameter Id="template_id" DataType="string" Value="d-989077fbba9746e89f3f6411f596fb96"/>
        <InputParameter Id="from.email" DataType="string" Value="my_email@mydomain.com"/>
        <!-- Update with a subject line appropriate for your organization. -->
        <InputParameter Id="personalizations.0.dynamic_template_data.subject" DataType="string" Value="Contoso account email verification code"/>
      </InputParameters>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="emailRequestBody" TransformationClaimType="outputClaim"/>
      </OutputClaims>
    </ClaimsTransformation>
  <!--
  </ClaimsTransformations>
</BuildingBlocks> -->
```

## <a name="add-datauri-content-definition"></a>DataUri 콘텐츠 정의 추가

`<BuildingBlocks>` 내의 클레임 변환 아래에서 다음 [ContentDefinition](contentdefinitions.md)을 추가하여 버전 2.1.2 데이터 URI를 참조합니다.

```xml
<!--
<BuildingBlocks> -->
  <ContentDefinitions>
   <ContentDefinition Id="api.localaccountsignup">
      <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:2.1.2</DataUri>
    </ContentDefinition>
    <ContentDefinition Id="api.localaccountpasswordreset">
      <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:2.1.2</DataUri>
    </ContentDefinition>
  </ContentDefinitions>
<!--
</BuildingBlocks> -->
```

## <a name="create-a-displaycontrol"></a>DisplayControl 만들기

확인 표시 컨트롤은 사용자에게 전송되는 확인 코드로 이메일 주소를 확인하는 데 사용됩니다.

이 예제 표시 컨트롤은 다음과 같이 구성됩니다.

1. 사용자로부터 `email` 주소 클레임 형식을 수집합니다.
1. 사용자에게 보낸 코드를 사용하여 사용자가 `verificationCode` 클레임 형식을 제공할 때까지 기다립니다.
1. 해당 표시 컨트롤에 대한 참조를 포함하는 자체 어설션된 기술 프로필에 `email`을 다시 반환합니다.
1. `SendCode` 작업을 사용하여 OTP 코드를 생성하고 OTP 코드가 포함된 이메일을 사용자에게 보냅니다.

![확인 코드 이메일 보내기 작업](media/custom-email-sendgrid/display-control-verification-email-action-01.png)

콘텐츠 정의에서 여전히 `<BuildingBlocks>` 내에 있는 [VerificationControl](display-control-verification.md) 형식의 다음 [DisplayControl](display-controls.md)을 정책에 추가합니다.

```xml
<!--
<BuildingBlocks> -->
  <DisplayControls>
    <DisplayControl Id="emailVerificationControl" UserInterfaceControlType="VerificationControl">
      <DisplayClaims>
        <DisplayClaim ClaimTypeReferenceId="email" Required="true" />
        <DisplayClaim ClaimTypeReferenceId="verificationCode" ControlClaimType="VerificationCode" Required="true" />
      </DisplayClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="email" />
      </OutputClaims>
      <Actions>
        <Action Id="SendCode">
          <ValidationClaimsExchange>
            <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="GenerateOtp" />
            <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="SendOtp" />
          </ValidationClaimsExchange>
        </Action>
        <Action Id="VerifyCode">
          <ValidationClaimsExchange>
            <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="VerifyOtp" />
          </ValidationClaimsExchange>
        </Action>
      </Actions>
    </DisplayControl>
  </DisplayControls>
<!--
</BuildingBlocks> -->
```

## <a name="add-otp-technical-profiles"></a>OTP 기술 프로필 추가

`GenerateOtp` 기술 프로필은 이메일 주소의 코드를 생성합니다. `VerifyOtp` 기술 프로필은 이메일 주소와 연결된 코드를 확인합니다. 일회성 암호의 형식 구성과 만료를 변경할 수 있습니다. OTP 기술 프로필에 대한 자세한 내용은 [일회성 암호 기술 프로필 정의](one-time-password-technical-profile.md)를 참조하세요.

> [!NOTE]
> Web.TPEngine.Providers.OneTimePasswordProtocolProvider 프로토콜에 의해 생성된 OTP 코드는 브라우저 세션에 연결됩니다. 즉, 사용자는 각각 해당 세션에 유효한 서로 다른 브라우저 세션에서 고유한 OTP 코드를 생성할 수 있습니다. 반면, 기본 제공 이메일 공급자가 생성한 OTP 코드는 브라우저 세션과 독립적이므로 사용자가 새 브라우저 세션에서 새 OTP 코드를 생성하는 경우 이전 OTP 코드를 대체합니다.

다음 기술 프로필을 `<ClaimsProviders>` 요소에 추가합니다.

```xml
<!--
<ClaimsProviders> -->
  <ClaimsProvider>
    <DisplayName>One time password technical profiles</DisplayName>
    <TechnicalProfiles>
      <TechnicalProfile Id="GenerateOtp">
        <DisplayName>Generate one time password</DisplayName>
        <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.OneTimePasswordProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
        <Metadata>
          <Item Key="Operation">GenerateCode</Item>
          <Item Key="CodeExpirationInSeconds">1200</Item>
          <Item Key="CodeLength">6</Item>
          <Item Key="CharacterSet">0-9</Item>
          <Item Key="ReuseSameCode">true</Item>
          <Item Key="NumRetryAttempts">5</Item>
        </Metadata>
        <InputClaims>
          <InputClaim ClaimTypeReferenceId="email" PartnerClaimType="identifier" />
        </InputClaims>
        <OutputClaims>
          <OutputClaim ClaimTypeReferenceId="otp" PartnerClaimType="otpGenerated" />
        </OutputClaims>
      </TechnicalProfile>

      <TechnicalProfile Id="VerifyOtp">
        <DisplayName>Verify one time password</DisplayName>
        <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.OneTimePasswordProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
        <Metadata>
          <Item Key="Operation">VerifyCode</Item>
        </Metadata>
        <InputClaims>
          <InputClaim ClaimTypeReferenceId="email" PartnerClaimType="identifier" />
          <InputClaim ClaimTypeReferenceId="verificationCode" PartnerClaimType="otpToVerify" />
        </InputClaims>
      </TechnicalProfile>
     </TechnicalProfiles>
  </ClaimsProvider>
<!--
</ClaimsProviders> -->
```

## <a name="add-a-rest-api-technical-profile"></a>REST API 기술 프로필 추가

해당 REST API 기술 프로필은 SendGrid 형식을 사용하여 메일 콘텐츠를 생성합니다. RESTful 기술 프로필에 대한 자세한 내용은 [RESTful 기술 프로필 정의](restful-technical-profile.md)를 참조하세요.

OTP 기술 프로필과 마찬가지로 다음 기술 프로필을 `<ClaimsProviders>` 요소에 추가합니다.

```xml
<ClaimsProvider>
  <DisplayName>RestfulProvider</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="SendOtp">
      <DisplayName>Use SendGrid's email API to send the code the the user</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://api.sendgrid.com/v3/mail/send</Item>
        <Item Key="AuthenticationType">Bearer</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <Item Key="ClaimUsedForRequestPayload">emailRequestBody</Item>
      </Metadata>
      <CryptographicKeys>
        <Key Id="BearerAuthenticationToken" StorageReferenceId="B2C_1A_SendGridSecret" />
      </CryptographicKeys>
      <InputClaimsTransformations>
        <InputClaimsTransformation ReferenceId="GenerateEmailRequestBody" />
      </InputClaimsTransformations>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="emailRequestBody" />
      </InputClaims>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="make-a-reference-to-the-displaycontrol"></a>DisplayControl에 대한 참조 만들기

마지막 단계로, 앞서 만든 DisplayControl에 대한 참조를 추가합니다. 기존의 자체 어설션된 `LocalAccountSignUpWithLogonEmail` 및 `LocalAccountDiscoveryUsingEmailAddress` 기술 프로필을 다음으로 바꿉니다. 이전 버전의 Azure AD B2C 정책을 사용한 경우, 해당 기술 프로필은 DisplayControl에 대한 참조와 함께 `DisplayClaims`를 사용합니다.

자세한 내용은 [자체 어설션된 기술 프로필](restful-technical-profile.md) 및 [DisplayControl](display-controls.md)을 참조하세요.

```xml
<ClaimsProvider>
  <DisplayName>Local Account</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
      <DisplayClaims>
        <DisplayClaim DisplayControlReferenceId="emailVerificationControl" />
        <DisplayClaim ClaimTypeReferenceId="displayName" Required="true" />
        <DisplayClaim ClaimTypeReferenceId="givenName" Required="true" />
        <DisplayClaim ClaimTypeReferenceId="surName" Required="true" />
        <DisplayClaim ClaimTypeReferenceId="newPassword" Required="true" />
        <DisplayClaim ClaimTypeReferenceId="reenterPassword" Required="true" />
      </DisplayClaims>
    </TechnicalProfile>
    <TechnicalProfile Id="LocalAccountDiscoveryUsingEmailAddress">
      <DisplayClaims>
        <DisplayClaim DisplayControlReferenceId="emailVerificationControl" />
      </DisplayClaims>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="optional-localize-your-email"></a>[선택 사항] 이메일 지역화

이메일을 지역화하려면 SendGrid 또는 이메일 공급자에 지역화된 문자열을 보내야 합니다. 예를 들어 메일의 제목, 본문, 코드 메시지 또는 서명을 지역화할 수 있습니다. 이렇게 하려면 [GetLocalizedStringsTransformation](string-transformations.md) 클레임 변환을 사용하여 지역화된 문자열을 클레임 형식으로 복사하면 됩니다. JSON 페이로드를 생성하는 `GenerateEmailRequestBody` 클레임 변환은 지역화된 문자열을 포함하는 입력 클레임을 사용합니다.

1. 정책에서 subject, message, codeIntro, signature와 같은 문자열 클레임을 정의합니다.
1. [GetLocalizedStringsTransformation](string-transformations.md) 클레임 변환을 정의하여 지역화된 문자열 값을 1단계의 클레임으로 대체합니다.
1. 다음 XML 코드 조각과 함께 입력 클레임을 사용하도록 `GenerateEmailRequestBody` 클레임 변환을 변경합니다.
1. Azure AD B2C에서 지역화할 모든 문자열 대신 동적 매개 변수를 사용하도록 SendGrid 템플릿을 업데이트합니다.

    ```xml
    <ClaimsTransformation Id="GetLocalizedStringsForEmail" TransformationMethod="GetLocalizedStringsTransformation">
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="subject" TransformationClaimType="email_subject" />
        <OutputClaim ClaimTypeReferenceId="message" TransformationClaimType="email_message" />
        <OutputClaim ClaimTypeReferenceId="codeIntro" TransformationClaimType="email_code" />
        <OutputClaim ClaimTypeReferenceId="signature" TransformationClaimType="email_signature" />
      </OutputClaims>
    </ClaimsTransformation>
    <ClaimsTransformation Id="GenerateEmailRequestBody" TransformationMethod="GenerateJson">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="personalizations.0.to.0.email" />
        <InputClaim ClaimTypeReferenceId="subject" TransformationClaimType="personalizations.0.dynamic_template_data.subject" />
        <InputClaim ClaimTypeReferenceId="otp" TransformationClaimType="personalizations.0.dynamic_template_data.otp" />
        <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="personalizations.0.dynamic_template_data.email" />
        <InputClaim ClaimTypeReferenceId="message" TransformationClaimType="personalizations.0.dynamic_template_data.message" />
        <InputClaim ClaimTypeReferenceId="codeIntro" TransformationClaimType="personalizations.0.dynamic_template_data.codeIntro" />
        <InputClaim ClaimTypeReferenceId="signature" TransformationClaimType="personalizations.0.dynamic_template_data.signature" />
      </InputClaims>
      <InputParameters>
        <InputParameter Id="template_id" DataType="string" Value="d-1234567890" />
        <InputParameter Id="from.email" DataType="string" Value="my_email@mydomain.com" />
      </InputParameters>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="emailRequestBody" TransformationClaimType="outputClaim" />
      </OutputClaims>
    </ClaimsTransformation>
    ```

1. 다음 [지역화](localization.md) 요소를 추가합니다.

    ```xml
    <!--
    <BuildingBlocks> -->
      <Localization Enabled="true">
        <SupportedLanguages DefaultLanguage="en" MergeBehavior="Append">
          <SupportedLanguage>en</SupportedLanguage>
          <SupportedLanguage>es</SupportedLanguage>
        </SupportedLanguages>
        <LocalizedResources Id="api.custom-email.en">
          <LocalizedStrings>
            <!--Email template parameters-->
            <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_subject">Contoso account email verification code</LocalizedString>
            <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_message">Thanks for validating the account</LocalizedString>
            <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_code">Your code is</LocalizedString>
            <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_signature">Sincerely</LocalizedString>
          </LocalizedStrings>
        </LocalizedResources>
        <LocalizedResources Id="api.custom-email.es">
          <LocalizedStrings>
            <!--Email template parameters-->
            <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_subject">Código de verificación del correo electrónico de la cuenta de Contoso</LocalizedString>
            <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_message">Gracias por comprobar la cuenta de </LocalizedString>
            <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_code">Su código es</LocalizedString>
            <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_signature">Sinceramente</LocalizedString>
          </LocalizedStrings>
        </LocalizedResources>
      </Localization>
    <!--
    </BuildingBlocks> -->
    ```

1. [ContentDefinitions](contentdefinitions.md) 요소를 업데이트하여 LocalizedResources 요소에 대한 참조를 추가합니다.

    ```xml
    <!--
    <BuildingBlocks> -->
      <ContentDefinitions>
        <ContentDefinition Id="api.localaccountsignup">
          <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:2.1.2</DataUri>
          <LocalizedResourcesReferences MergeBehavior="Prepend">
            <LocalizedResourcesReference Language="en" LocalizedResourcesReferenceId="api.custom-email.en" />
            <LocalizedResourcesReference Language="es" LocalizedResourcesReferenceId="api.custom-email.es" />
          </LocalizedResourcesReferences>
        </ContentDefinition>
        <ContentDefinition Id="api.localaccountpasswordreset">
          <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:2.1.2</DataUri>
          <LocalizedResourcesReferences MergeBehavior="Prepend">
            <LocalizedResourcesReference Language="en" LocalizedResourcesReferenceId="api.custom-email.en" />
            <LocalizedResourcesReference Language="es" LocalizedResourcesReferenceId="api.custom-email.es" />
          </LocalizedResourcesReferences>
        </ContentDefinition>
      </ContentDefinitions>
    <!--
    </BuildingBlocks> -->
    ```

1. 마지막으로 다음 입력 클레임 변환을 `LocalAccountSignUpWithLogonEmail` 및 `LocalAccountDiscoveryUsingEmailAddress` 기술 프로필에 추가합니다.

    ```xml
    <InputClaimsTransformations>
      <InputClaimsTransformation ReferenceId="GetLocalizedStringsForEmail" />
    </InputClaimsTransformations>
    ```

## <a name="optional-localize-the-ui"></a>[선택 사항] UI 지역화

Localization 요소를 사용하면 사용자 경험용 정책에서 여러 로캘이나 언어를 지원할 수 있습니다. 정책의 지역화 지원을 통해 [확인 표시 컨트롤 사용자 인터페이스 요소](localization-string-ids.md#verification-display-control-user-interface-elements)와 [일회성 암호 오류 메시지](localization-string-ids.md#one-time-password-error-messages)에 대해 언어별 문자열을 제공할 수 있습니다. 다음 LocalizedString을 LocalizedResources에 추가합니다.

```xml
<LocalizedResources Id="api.custom-email.en">
  <LocalizedStrings>
    ...
    <!-- Display control UI elements-->
    <LocalizedString ElementType="DisplayControl" ElementId="emailVerificationControl" StringId="intro_msg">Verification is necessary. Please click Send button.</LocalizedString>
    <LocalizedString ElementType="DisplayControl" ElementId="emailVerificationControl" StringId="success_send_code_msg">Verification code has been sent to your inbox. Please copy it to the input box below.</LocalizedString>
    <LocalizedString ElementType="DisplayControl" ElementId="emailVerificationControl" StringId="failure_send_code_msg">We are having trouble verifying your email address. Please enter a valid email address and try again.</LocalizedString>
    <LocalizedString ElementType="DisplayControl" ElementId="emailVerificationControl" StringId="success_verify_code_msg">E-mail address verified. You can now continue.</LocalizedString>
    <LocalizedString ElementType="DisplayControl" ElementId="emailVerificationControl" StringId="failure_verify_code_msg">We are having trouble verifying your email address. Please try again.</LocalizedString>
    <LocalizedString ElementType="DisplayControl" ElementId="emailVerificationControl" StringId="but_send_code">Send verification code</LocalizedString>
    <LocalizedString ElementType="DisplayControl" ElementId="emailVerificationControl" StringId="but_verify_code">Verify code</LocalizedString>
    <LocalizedString ElementType="DisplayControl" ElementId="emailVerificationControl" StringId="but_send_new_code">Send new code</LocalizedString>
    <LocalizedString ElementType="DisplayControl" ElementId="emailVerificationControl" StringId="but_change_claims">Change e-mail</LocalizedString>
    <!-- Claims-->
    <LocalizedString ElementType="ClaimType" ElementId="emailVerificationCode" StringId="DisplayName">Verification Code</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="emailVerificationCode" StringId="UserHelpText">Verification code received in the email.</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="emailVerificationCode" StringId="AdminHelpText">Verification code received in the email.</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="email" StringId="DisplayName">Email</LocalizedString>
    <!-- Email validation error messages-->
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfSessionDoesNotExist">You have exceeded the maximum time allowed.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfMaxRetryAttempted">You have exceeded the number of retries allowed.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfMaxNumberOfCodeGenerated">You have exceeded the number of code generation attempts allowed.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfInvalidCode">You have entered the wrong code.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfSessionConflict">Cannot verify the code, please try again later.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfVerificationFailedRetryAllowed">The verification has failed, please try again.</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>
```


## <a name="next-steps"></a>다음 단계

GitHub에서 사용자 지정 이메일 확인 정책의 예제를 확인할 수 있습니다.

- [사용자 지정 이메일 확인 - DisplayControls](https://github.com/azure-ad-b2c/samples/tree/master/policies/custom-email-verifcation-displaycontrol)
- 사용자 지정 REST API 또는 HTTP 기반 SMTP 이메일 공급자를 사용하는 방법에 대한 자세한 내용은 [Azure AD B2C 사용자 지정 정책의 RESTful 기술 프로필 정의](restful-technical-profile.md)를 참조하세요.

::: zone-end
