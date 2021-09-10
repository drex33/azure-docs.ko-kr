---
title: Azure AD 조건부 액세스 인증 컨텍스트에 대한 개발자 지침
description: Azure AD 조건부 액세스 인증 컨텍스트에 대한 개발자 지침 및 시나리오
services: active-directory
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.date: 05/18/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: CelesteDG
ms.reviewer: kkrishna
ms.workload: identity
ms.custom: aaddev
ms.openlocfilehash: 0a310df6793d2caecda095f0c89244ffee5e46eb
ms.sourcegitcommit: ee8ce2c752d45968a822acc0866ff8111d0d4c7f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/14/2021
ms.locfileid: "113728033"
---
# <a name="developers-guide-to-conditional-access-authentication-context"></a>조건부 액세스 인증 컨텍스트에 대한 개발자 가이드

[조건부 액세스](../conditional-access/overview.md)는 모든 앱(이전 또는 신규, 프라이빗 또는 퍼블릭, 온-프레미스 또는 다중 클라우드)에 대한 액세스 정책을 대상으로 지정할 수 있는 제로 트러스트 컨트롤 플레인입니다. [조건부 액세스 인증 컨텍스트](../conditional-access/concept-conditional-access-cloud-apps.md#authentication-context-preview)를 사용하여 해당 앱 내에 다른 정책을 적용할 수 있습니다. 

조건부 액세스 인증 컨텍스트(인증 컨텍스트)를 사용하면 앱 수준뿐만 아니라 중요한 데이터 및 작업에 세분화된 정책을 적용할 수 있습니다. 사용자 마찰을 최소화하고 사용자의 생산성과 리소스를 더욱 안전하게 유지하면서 최소 권한의 액세스에 대해 제로 트러스트 정책을 구체화할 수 있습니다. 오늘날 회사에서 개발한 인증을 위한 [OpenId Connect](https://openid.net/specs/openid-connect-core-1_0.html)를 사용하는 애플리케이션에서 높은 가치의 트랜잭션과 같은 중요한 리소스를 보호하거나 직원의 개인 데이터를 보는 데 사용할 수 있습니다.

Azure AD 조건부 액세스 엔진의 새 인증 컨텍스트 기능을 사용하여 애플리케이션 및 서비스 내에서 단계별 인증 요청을 트리거합니다. 개발자는 애플리케이션 내에서 최종 사용자의 MFA와 같이 선택적으로 더 강력한 고급 인증을 요구하는 기능을 사용할 수 있습니다. 이 기능은 개발자가 애플리케이션의 대부분에 대해 더 원활한 사용자 환경을 빌드하는 동시에 더 강력한 인증 제어를 바탕으로 더 안전한 운영 및 데이터에 액세스할 수 있도록 지원합니다.

## <a name="problem-statement"></a>문제 설명

IT 관리자와 규제자는 사용자에게 인증에 대한 추가 요소를 너무 자주 요구하는 것과 중요한 데이터와 운영이 포함된 애플리케이션 및 서비스에 대해 적절한 보안 및 정책 준수를 달성하는 것 사이에서 종종 어려움을 겪습니다. 대부분의 데이터 및 작업에 액세스할 때 사용자의 생산성에 영향을 미치는 강력한 정책을 선택하거나, 아니면 중요한 리소스에 충분히 강력하지 않은 정책을 선택할 수 있습니다. 

그런데 대부분의 사용자 및 운영에 대해서는 상대적으로 보안 수준이 낮고 프롬프트 횟수가 적으면서도, 사용자가 더 민감한 부분에 액세스할 때는 조건부로 보안 요구 사항을 강화할 수 있도록 앱이 두 가지를 모두 결합할 수 있다면 어떨까요? 

## <a name="common-scenarios"></a>일반적인 시나리오

예를 들어, 사용자가 다단계 인증을 사용하여 SharePoint에 로그인할 수 있는 한편, 중요한 문서가 포함된 SharePoint의 사이트 컬렉션에 액세스하기 위해서는 정책 준수 디바이스가 필요하며 신뢰할 수 있는 IP 범위에서만 액세스할 수 있습니다. 

## <a name="steps"></a>단계

다음은 조건부 액세스 인증 컨텍스트를 사용하려는 경우의 필수 구성 요소 및 단계입니다. 

### <a name="prerequisites"></a>사전 요구 사항

**먼저** 인증 및 권한 부여를 위해 [OpenID Connect](v2-protocols-oidc.md)/ [OAuth 2.0](v2-oauth2-auth-code-flow.md) 프로토콜을 사용하여 앱을 Microsoft ID 플랫폼과 통합해야 합니다. [Microsoft ID 플랫폼 인증 라이브러리](reference-v2-libraries.md)를 사용하여 Azure Active Directory로 애플리케이션을 통합하고 보호하는 것이 좋습니다. [Microsoft ID 플랫폼 설명서](index.yml)는 앱을 Microsoft ID 플랫폼과 통합하는 방법에 대한 학습을 시작하는 데 적합합니다. 조건부 액세스 인증 컨텍스트 기능 지원은 업계 표준 [OpenID Connect](v2-protocols-oidc.md) 프로토콜에서 제공하는 프로토콜 확장을 기반으로 합니다. 개발자는 [클레임 요청](claims-challenge.md) 매개 변수와 함께 [조건부 액세스 인증 컨텍스트 참조](/graph/api/resources/authenticationcontextclassreference) **값** 을 사용하여 앱에 정책을 트리거하고 충족하는 방법을 제공합니다.

**두 번째로** [조건부 액세스](../conditional-access/overview.md)에는 Azure AD Premium P1 라이선스가 필요합니다. 라이선스에 대한 자세한 내용은 [Azure AD 가격 책정 페이지](https://www.microsoft.com/security/business/identity-access-management/azure-ad-pricing)에서 확인할 수 있습니다.

**세 번째로**, 현재 사용자가 로그인하는 애플리케이션에서만 사용할 수 있습니다. 자체로 인증하는 애플리케이션은 지원되지 않습니다. [인증 흐름 및 애플리케이션 시나리오 가이드](authentication-flows-app-scenarios.md)를 사용하여 Microsoft ID 플랫폼에서 지원되는 인증 앱 유형 및 흐름에 대해 알아보세요.

### <a name="integration-steps"></a>통합 단계

지원되는 인증 프로토콜을 사용하여 애플리케이션을 통합하고 조건부 액세스 기능을 사용할 수 있는 Azure AD 테넌트에 등록한 후에는 사용자가 로그인하는 애플리케이션에서 이 기능을 통합하는 프로세스를 시작할 수 있습니다.

> [!NOTE]
> 이 기능에 대한 자세한 연습은 [앱에서 단계별\-인증을 위한 조건부 액세스 인증 컨텍스트 사용](https://www.youtube.com/watch?v=_iO7CfoktTY)의 녹화 세션에서 확인할 수 있습니다.

**먼저** 테넌트에서 사용 가능한 인증 컨텍스트를 선언하고 만듭니다. 자세한 내용은 [인증 컨텍스트 구성](../conditional-access/concept-conditional-access-cloud-apps.md#configure-authentication-contexts)을 참조하세요.

값 **C1-C25** 는 테넌트에서 **인증 컨텍스트 ID** 로 사용할 수 있습니다. 인증 컨텍스트의 예제는 다음과 같습니다.

- **C1** - 강력한 인증 필요
- **C2** - 정책 준수 디바이스 필요
- **C3** – 신뢰할 수 있는 위치 필요

조건부 액세스 인증 컨텍스트를 사용하도록 조건부 액세스 정책을 만들거나 수정합니다. 정책 예제는 다음과 같습니다.

- 이 웹 애플리케이션에 로그인하는 모든 사용자는 인증 컨텍스트 ID **C1** 에 대해 2FA를 성공적으로 완료해야 합니다.
- 이 웹 애플리케이션에 로그인하는 모든 사용자는 인증 컨텍스트 ID **C3** 에 대해 2FA를 성공적으로 완료하고 특정 IP 주소 범위에서 웹앱에 액세스해야 합니다.

> [!NOTE]
> 조건부 액세스 인증 컨텍스트 값은 애플리케이션에서 별도로 선언 및 유지 관리됩니다. 애플리케이션이 인증 컨텍스트 ID에 대해 하드 종속성을 사용하지 않는 것이 좋습니다. 조건부 액세스 정책은 정책 적용에 사용할 수 있는 리소스를 더 잘 이해할 수 있도록 일반적으로 IT 관리자가 작성합니다. 예를 들어, Azure AD 테넌트의 경우 IT 관리자는 MFA에 대해 2FA를 사용하는 테넌트의 사용자 수에 대한 정보를 보유하고 있으므로 2FA가 요구되는 조건부 액세스 정책의 범위를 이러한 장착된 사용자로 제한할 수 있습니다. 마찬가지로 애플리케이션이 여러 테넌트에서 사용되는 경우 사용 중인 인증 컨텍스트 ID가 다를 수 있으며 경우에 따라 전혀 사용할 수 없습니다.

**두 번째로** 조건부 액세스 인증 컨텍스트를 사용하려는 애플리케이션 개발자는 먼저 애플리케이션 관리자 또는 IT 관리자에게 잠재적으로 중요한 작업을 인증 컨텍스트 ID에 매핑할 수 있는 수단을 제공하는 것이 좋습니다. 대략적인 단계는 다음과 같습니다.

1. 인증 컨텍스트 ID에 대해 매핑할 수 있는 코드의 작업을 파악합니다.
1. IT 관리자가 사용 가능한 인증 컨텍스트 ID에 중요한 작업을 매핑하는 데 사용할 수 있는 앱(또는 이와 동등한 기능)의 관리 포털에 화면을 빌드합니다.
1. 작업을 수행하는 방법에 대한 예제를 보려면 코드 샘플에서 [조건부 액세스 인증 컨텍스트를 사용한 단계별 인증 수행](https://github.com/Azure-Samples/ms-identity-ca-auth-context/blob/main/README.md)을 참조하세요.

이러한 단계는 코드 베이스에서 수행해야 하는 변경 내용입니다. 단계는 대체로 다음과 같습니다.

- [사용 가능한 모든 인증 컨텍스트를 나열](/graph/api/conditionalaccessroot-list-authenticationcontextclassreferences)하는 MS Graph를 쿼리합니다.
- IT 관리자가 중요한/높은 권한을 가진 작업을 선택하고 CA 정책을 사용하여 사용 가능한 인증 컨텍스트에 할당할 수 있도록 허용합니다. 
- 이 매핑 정보를 데이터베이스/로컬 저장소에 저장합니다.

:::image type="content" source="media/developer-guide-conditional-access-authentication-context/configure-conditional-access-authentication-context.png" alt-text="인증 컨텍스트를 만들기 위한 설정 흐름":::

**세 번째로** 이 예제에서는 웹 API라고 가정하는 사용자의 애플리케이션은 저장된 매핑에 대한 호출을 평가하고, 그에 따라 해당 클라이언트 앱에 대한 클레임 문제를 발생시켜야 합니다. 이 작업을 준비하려면 다음 단계를 수행합니다.

1. 인증 컨텍스트 작업에 의해 중요시되고 보호되는/이전에 저장된 인증 컨텍스트 ID 매핑에 대해 **acrs** 클레임의 값을 평가하고 아래 코드 조각에 제공된 대로 [클레임 문제](claims-challenge.md)를 발생시킵니다. 

1. 다음 다이어그램은 사용자, 클라이언트 앱 및 웹 API 간의 상호 작용을 보여 줍니다.

   :::image type="content" source="media/developer-guide-conditional-access-authentication-context/authentication-context-application-flow.png" alt-text="사용자, 웹앱, API 및 Azure AD의 상호 작용을 보여 주는 다이어그램":::

   다음 코드 조각은 코드 샘플 [조건부 액세스 인증 컨텍스트를 사용한 단계별 인증 수행](https://github.com/Azure-Samples/ms-identity-ca-auth-context/blob/main/README.md)에서 가져온 것입니다. API의 첫 번째 메서드 `CheckForRequiredAuthContext()` 

      - 호출 중인 애플리케이션의 작업에 단계별 인증이 필요한지 확인합니다. 해당 데이터베이스에서 이 메서드에 대한 저장된 매핑을 확인하여 수행합니다.
      - 이 작업에 실제로 상승된 인증 컨텍스트가 필요한 경우 일치하는 기존 인증 컨텍스트 ID에 대한 **acrs** 클레임을 확인합니다.
      - 일치하는 인증 컨텍스트 ID가 없다면 [클레임 문제](claims-challenge.md#claims-challenge-header-format)를 발생시킵니다.

      ```
      public void CheckForRequiredAuthContext(string method)
      {
          string authType = _commonDBContext.AuthContext.FirstOrDefault(x => x.Operation == method 
                      && x.TenantId == _configuration["AzureAD:TenantId"])?.AuthContextId;

          if (!string.IsNullOrEmpty(authType))
          {
              HttpContext context = this.HttpContext;
              string authenticationContextClassReferencesClaim = "acrs";

              if (context == null || context.User == null || context.User.Claims == null 
                  || !context.User.Claims.Any())
              {
                  throw new ArgumentNullException("No Usercontext is available to pick claims from");
              }

              Claim acrsClaim = context.User.FindAll(authenticationContextClassReferencesClaim).FirstOrDefault(x 
                  => x.Value == authType);

              if (acrsClaim == null || acrsClaim.Value != authType)
              {
                  if (IsClientCapableofClaimsChallenge(context))
                  {
                      string clientId = _configuration.GetSection("AzureAd").GetSection("ClientId").Value;
                      var base64str = Convert.ToBase64String(Encoding.UTF8.GetBytes("{\"access_token\":{\"acrs\":{\"essential\":true,\"value\":\"" + authType + "\"}}}"));

                      context.Response.Headers.Append("WWW-Authenticate", $"Bearer realm=\"\", authorization_uri=\"https://login.microsoftonline.com/common/oauth2/authorize\", client_id=\"" + clientId + "\", error=\"insufficient_claims\", claims=\"" + base64str + "\", cc_type=\"authcontext\"");
                      context.Response.StatusCode = (int)HttpStatusCode.Unauthorized;
                      string message = string.Format(CultureInfo.InvariantCulture, "The presented access tokens had insufficient claims. Please request for claims requested in the WWW-Authentication header and try again.");
                      context.Response.WriteAsync(message);
                      context.Response.CompleteAsync();
                      throw new UnauthorizedAccessException(message);
                  }
                  else
                  {
                      throw new UnauthorizedAccessException("The caller does not meet the authentication  bar to carry our this operation. The service cannot allow this operation");
                  }
              }
          }
      }
      ```

   > [!NOTE]
   > 클레임 문제의 형식은 [Microsoft ID 플랫폼의 클레임 문제](claims-challenge.md) 문서에 설명되어 있습니다. 

1. 클라이언트 애플리케이션에서 클레임 문제를 가로채고 추가 정책 평가를 위해 사용자를 다시 Azure AD로 리디렉션합니다. 다음 코드 조각은 코드 샘플 [조건부 액세스 인증 컨텍스트를 사용한 단계별 인증 수행](https://github.com/Azure-Samples/ms-identity-ca-auth-context/blob/main/README.md)에서 가져온 것입니다.

   ```
   internal static string ExtractHeaderValues(WebApiMsalUiRequiredException response)
   {
       if (response.StatusCode == System.Net.HttpStatusCode.Unauthorized && response.Headers.WwwAuthenticate.Any())
       {
           AuthenticationHeaderValue bearer = response.Headers.WwwAuthenticate.First(v => v.Scheme == "Bearer");
           IEnumerable<string> parameters = bearer.Parameter.Split(',').Select(v => v.Trim()).ToList();
           var errorValue = GetParameterValue(parameters, "error");

           try
           {
               // read the header and checks if it conatins error with insufficient_claims value.
               if (null != errorValue && "insufficient_claims" == errorValue)
               {
                   var claimChallengeParameter = GetParameterValue(parameters, "claims");
                   if (null != claimChallengeParameter)
                   {
                       var claimChallenge = ConvertBase64String(claimChallengeParameter);

                       return claimChallenge;
                   }
               }
           }
           catch (Exception ex)
           {
               throw ex;
           }
       }
       return null;
   }
   ```

   웹 API에 대한 호출에서 예외를 처리합니다. 클레임 문제가 표시되는 경우 추가 처리를 위해 사용자를 Azure AD로 다시 리디렉션합니다.

   ```
   try
   {
       // Call the API 
       await _todoListService.AddAsync(todo);
   }
   catch (WebApiMsalUiRequiredException hex)
   {
       // Challenges the user if exception is thrown from Web API.
       try
       {
           var claimChallenge =ExtractHeaderValues(hex);
           _consentHandler.ChallengeUser(new string[] { "user.read" }, claimChallenge);

           return new EmptyResult();
       }
       catch (Exception ex)
       {
           _consentHandler.HandleException(ex);
       }

       Console.WriteLine(hex.Message);
   }
   return RedirectToAction("Index");
   ```
   
1. (선택 사항) 클라이언트 기능을 선언합니다. 클라이언트 기능을 통해 웹 API와 같은 RP(리소스 공급자)는 호출하는 클라이언트 애플리케이션이 클레임 문제를 이해하고 그에 따라 응답을 사용자 지정할 수 있는지 여부를 감지합니다. 이 기능은 일부 API 클라이언트가 클레임 문제를 처리할 수 없고 일부 오래된 API가 여전히 다른 응답을 기대하는 경우에 유용할 수 있습니다. 자세한 내용은 [클라이언트 기능](claims-challenge.md#client-capabilities) 섹션을 참조하세요.

## <a name="caveats-and-recommendations"></a>제한 사항 및 권장 사항

앱에서 인증 컨텍스트 값을 하드 코딩하지 마십시오. 앱은 [MS Graph 호출을 사용하여](/graph/api/resources/authenticationcontextclassreference) 인증 컨텍스트를 읽고 적용해야 합니다. 이 방법은 [다중 테넌트 애플리케이션](howto-convert-app-to-be-multi-tenant.md)에 매우 중요합니다. Azure AD 무료 버전에서는 Azure AD 테넌트 간에 인증 컨텍스트 값이 달라질 수 있습니다. 앱이 코드에서 인증 컨텍스트를 쿼리, 설정 및 사용하는 방법에 대한 자세한 내용은 코드 샘플 [조건부 액세스 인증 컨텍스트를 사용한 단계별 인증 수행](https://github.com/Azure-Samples/ms-identity-ca-auth-context/blob/main/README.md)에서 앱이 코드에서 인증 컨텍스트를 쿼리, 설정 및 사용하는 방법을 참조하세요. 

앱 자체가 조건부 액세스 정책의 대상이 되는 인증 컨텍스트를 사용하지 마세요. 이 기능은 애플리케이션의 일부에서 사용자가 더 높은 수준의 인증을 충족해야 하는 경우에 가장 잘 작동합니다.

## <a name="next-steps"></a>다음 단계

- [중요한 데이터 및 작업에 대한 세분화된 조건부 액세스(블로그)](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/granular-conditional-access-for-sensitive-data-and-actions/ba-p/1751775)
- [Microsoft ID 플랫폼을 사용하는 제로 트러스트](/security/zero-trust/identity-developer)
- [Microsoft ID 플랫폼을 사용하여 제로 트러스트 지원 앱 빌드](/security/zero-trust/identity-developer)
- [조건부 액세스 인증 컨텍스트를 사용하여 웹앱의 높은 권한 작업을 위한 버전 업그레이드 인증 수행](https://github.com/Azure-Samples/ms-identity-dotnetcore-ca-auth-context-app/blob/main/README.md)
- [웹 API의 높은 권한 작업을 위한 조건부 액세스 인증 컨텍스트를 사용한 단계별 인증 수행](https://github.com/Azure-Samples/ms-identity-ca-auth-context/blob/main/README.md)
- [조건부 액세스 인증 컨텍스트](../conditional-access/concept-conditional-access-cloud-apps.md#authentication-context-preview)
- [authenticationContextClassReference 리소스 유형 - MS Graph](/graph/api/conditionalaccessroot-list-authenticationcontextclassreferences)
- [Microsoft ID 플랫폼의 클레임 문제, 클레임 요청 및 클라이언트 기능](claims-challenge.md)
- [Microsoft Information Protection 및 SharePoint에서 인증 컨텍스트 사용](/microsoft-365/compliance/sensitivity-labels-teams-groups-sites#more-information-about-the-dependencies-for-the-authentication-context-option)
- [인증 흐름 및 애플리케이션 시나리오](authentication-flows-app-scenarios.md)
- [애플리케이션에서 지속적인 액세스 권한 평가를 사용하도록 설정된 API를 사용하는 방법](app-resilience-continuous-access-evaluation.md)
