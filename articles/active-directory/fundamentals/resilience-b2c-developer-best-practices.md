---
title: Azure AD B2C를 사용하는 개발자 모범 사례를 통한 복원력 | Microsoft Docs
description: Azure AD B2C를 사용하는 고객 ID 및 액세스 관리에 대한 개발자 모범 사례를 통한 복원력
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: how-to
author: gargi-sinha
ms.author: gasinh
manager: martinco
ms.reviewer: ''
ms.date: 11/30/2020
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 87149785d670e85f991d0fbc851b642688526630
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131059341"
---
# <a name="resilience-through-developer-best-practices"></a>개발자 모범 사례를 통한 복원력

이 문서에서는 대기업 고객과 작업한 경험을 바탕으로 한 몇 가지 학습 내용을 공유합니다. 서비스 설계 및 구현 시 이러한 권장 사항을 고려할 수 있습니다.

![개발자 환경 구성 요소를 보여 주는 이미지](media/resilience-b2c-developer-best-practices/developer-best-practices-architecture.png)

## <a name="use-the-microsoft-authentication-library-msal"></a>MSAL(Microsoft 인증 라이브러리) 사용

[MSAL(Microsoft 인증 라이브러리)](../develop/msal-overview.md) 및 [ASP.NET용 Microsoft ID 웹 인증 라이브러리](../develop/reference-v2-libraries.md)는 애플리케이션에 필요한 토큰을 획득하고, 관리하고, 캐싱하고 새로 고치는 작업을 간소화합니다. 해당 라이브러리는 애플리케이션 복원력을 향상하는 기능을 포함하여 Microsoft ID를 지원하는 데 특히 최적화되어 있습니다.

개발자는 MSAL의 최신 릴리스를 채택하고 최신 상태를 유지해야 합니다. 애플리케이션에서 [인증 및 권한 부여 복원력을 향상하는 방법](resilience-app-development-overview.md)을 참조하세요. 가급적 자체 인증 스택을 구현하지 말고 제대로 구축된 라이브러리를 사용하세요.

## <a name="optimize-directory-reads-and-writes"></a>디렉터리 읽기 및 쓰기 최적화

Microsoft Azure AD B2C 디렉터리 서비스는 하루에 수십억 건의 인증을 지원합니다. 해당 서비스는 높은 초당 읽기 속도를 발휘하도록 설계되었습니다. 쓰기를 최적화하여 의존도를 최소화하고 복원력을 높입니다.

### <a name="how-to-optimize-directory-reads-and-writes"></a>디렉터리 읽기 및 쓰기를 최적화하는 방법

- **로그인 시 디렉터리에 함수 쓰기 방지**: 사용자 지정 정책에서 사전 조건(if 절)이 없으면 로그인 시 쓰기를 실행하지 않습니다. 로그인 시 쓰기가 필요한 한 가지 사용 사례는 [사용자 암호의 Just-In-Time 마이그레이션](https://github.com/azure-ad-b2c/user-migration/tree/master/seamless-account-migration)입니다. 로그인할 때마다 쓰기가 필요한 모든 시나리오를 지양하세요.

  - 사용자 경험의 [사전 조건](../../active-directory-b2c/userjourneys.md)은 다음과 비슷합니다.

  ```xml
  <Precondition Type="ClaimEquals" ExecuteActionsIf="true"> 
  <Value>requiresMigration</Value>
  ...
  <Precondition/>
  ```

  - [CAPTCHA 시스템과 통합하여 봇 기반 등록](https://github.com/azure-ad-b2c/samples/tree/master/policies/captcha-integration)에 대한 저항을 구축합니다.

  - [부하 테스트 샘플](../../active-directory-b2c/best-practices.md#testing)을 사용하여 등록과 로그인을 시뮬레이션합니다. 

- **제한 이해**: 디렉터리에서 애플리케이션 및 테넌트 수준 제한 규칙을 모두 구현합니다. Read/GET, Write/POST, Update/PUT, Delete/DELETE 작업에 대한 추가 속도 제한이 있으며 작업마다 제한이 다릅니다.

  - 로그인 시 쓰기는 새 사용자에 대한 POST 또는 기존 사용자에 대한 PUT에 해당합니다.

  - 로그인할 때마다 사용자를 만들거나 업데이트하는 사용자 지정 정책이 애플리케이션 수준 PUT 또는 POST 속도 제한에 도달할 수도 있습니다. Azure AD 또는 Microsoft Graph를 통해 디렉터리 개체를 업데이트하는 경우에도 동일한 제한이 적용됩니다. 마찬가지로, 읽기를 검토하여 로그인할 때마다 읽기 수를 최소로 유지합니다.

  - 최대 부하를 예상하여 디렉터리 쓰기 속도를 예측하고 제한이 적용되지 않도록 합니다. 최고 트래픽 예상치에는 등록, 로그인, MFA(다단계 인증)와 같은 작업에 대한 예상치가 포함됩니다. Azure AD B2C 시스템과 애플리케이션 모두의 최대 트래픽을 테스트해야 합니다. 다운스트림 애플리케이션이나 서비스가 처리할 수 없는 경우 Azure AD B2C가 제한 없이 부하를 처리할 수 있습니다.

  - 마이그레이션 타임라인을 이해하고 계획합니다. Microsoft Graph를 사용하여 Azure AD B2C 사용자를 마이그레이션하도록 계획할 때 애플리케이션 제한과 테넌트 제한을 고려하여 사용자 마이그레이션을 완료하는 데 필요한 시간을 계산합니다. 두 개의 애플리케이션을 사용하여 사용자 만들기 작업 또는 스크립트를 분할하는 경우 애플리케이션별 제한을 사용할 수 있습니다. 이는 테넌트별 임계값 미만으로 유지되어야 합니다.

  - 다른 애플리케이션에 대한 마이그레이션 작업의 영향을 이해합니다. 사용하는 다른 애플리케이션에서 제공하는 라이브 트래픽이 테넌트 수준에서의 제한과 라이브 애플리케이션의 리소스 부족을 발생시키지 않도록 합니다. 자세한 내용은 [Microsoft Graph 제한 지침](/graph/throttling)을 참조하세요.
  
## <a name="extend-token-lifetimes"></a>토큰 수명 연장

드물게 Azure AD B2C 인증 서비스에서 새 등록 및 로그인을 완료할 수 없는 경우에도 로그인한 사용자에 대한 완화책을 제공할 수 있습니다. [구성](../../active-directory-b2c/configure-tokens.md)에 따라, 이미 로그인한 사용자가 애플리케이션에서 로그아웃하거나 비활성으로 인해 [세션](../../active-directory-b2c/session-behavior.md) 시간이 초과될 때까지 계속해서 애플리케이션을 사용하도록 허용할 수 있습니다.

비즈니스 요구 사항과 원하는 최종 사용자 환경에 따라 웹 및 SPA(단일 페이지 애플리케이션)에 대한 토큰 새로 고침 빈도가 달라집니다.

### <a name="how-to-extend-token-lifetimes"></a>토큰 수명을 연장하는 방법

- **웹 애플리케이션**: 로그인을 시작할 때 인증 토큰의 유효성이 검사되는 웹 애플리케이션의 경우 해당 애플리케이션에서 세션 쿠키를 사용하여 세션 유효성을 계속 연장할 수 있습니다.

  - 사용자 활동에 따라 세션을 계속 갱신하는 롤링 세션 시간을 구현하여 사용자가 로그인 상태를 유지할 수 있도록 합니다. 장기적으로 토큰 발급이 중단되는 경우 애플리케이션에 대한 일회성 구성으로 해당 세션 시간을 더 늘릴 수 있습니다. 세션의 수명을 허용되는 최댓값으로 유지합니다.

- **SPA**: SPA는 API에 대한 호출을 수행하는 액세스 토큰에 따라 달라질 수 있습니다. 일반적으로 SPA는 토큰을 새로 고치지 않는 암시적 흐름을 사용합니다. 브라우저에 여전히 Azure AD B2C를 사용한 활성 세션이 있는 경우, SPA는 숨겨진 iframe을 사용하여 권한 부여 엔드포인트에 대한 새 토큰 요청을 수행할 수 있습니다. SPA는 사용자가 애플리케이션을 계속 사용할 수 있는 몇 가지 옵션을 제공합니다.

  - 비즈니스 요구 사항에 맞게 액세스 토큰의 유효 기간을 연장합니다.

  - API 게이트웨이를 인증 프록시로 사용하도록 애플리케이션을 빌드합니다. 이 구성에서 SPA는 인증 없이 로드하며, API 게이트웨이에 대한 API 호출이 이루어집니다. API 게이트웨이는 정책에 따라 [권한 부여 코드 부여](https://oauth.net/2/grant-types/authorization-code/)를 사용하여 로그인 프로세스를 통해 사용자를 보내고 사용자를 인증합니다. 이후에 API 게이트웨이와 클라이언트 간의 인증 세션은 인증 쿠키를 사용하여 유지됩니다. API는 API 게이트웨이에서 가져온 토큰이나 인증서, 클라이언트 자격 증명 또는 API 키와 같은 다른 직접 인증 방법을 사용하여 API 게이트웨이에서 제공됩니다.

  - PKCE(코드 교환용 증명 키) 및 CORS(원본 간 리소스 공유) 지원을 통해 [SPA를 암시적 권한 부여](https://developer.microsoft.com/identity/blogs/msal-js-2-0-supports-authorization-code-flow-is-now-generally-available/)에서 [권한 부여 코드 부여 흐름으로 마이그레이션](../../active-directory-b2c/implicit-flow-single-page-application.md)합니다. 애플리케이션을 MSAL.js 1.x에서 MSAL.js 2.x로 마이그레이션하여 웹 애플리케이션의 복원력을 실현할 수 있습니다.

  - 모바일 애플리케이션의 경우 새로 고침 및 액세스 토큰 수명을 모두 연장하는 것이 좋습니다.

- **백 엔드 또는 마이크로 서비스 애플리케이션**: 백 엔드(디먼) 애플리케이션은 비대화형이며 사용자 컨텍스트에 있지 않으므로 토큰 도난 가능성이 매우 낮습니다. 보안과 수명 간에 균형을 맞추고 토큰 수명을 길게 설정하는 것이 좋습니다.

## <a name="configure-single-sign-on"></a>Single Sign-On 구성

[SSO(Single Sign-On)](../manage-apps/what-is-single-sign-on.md)를 통해 사용자는 단일 계정으로 한 번 로그인하여 여러 애플리케이션에 액세스할 수 있습니다. 애플리케이션은 플랫폼 또는 도메인 이름과 관계없이 웹, 모바일 또는 SPA(단일 페이지 애플리케이션)일 수 있습니다. 사용자가 애플리케이션에 처음으로 로그인하면 Azure AD B2C는 [쿠키 기반 세션](../../active-directory-b2c/session-behavior.md)을 유지합니다.

후속 인증 요청 시 Azure AD B2C는 쿠키 기반 세션을 읽고 유효성을 검사한 후, 사용자에게 다시 로그인하라는 메시지를 표시하지 않고 액세스 토큰을 발급합니다. 정책이나 애플리케이션에서 제한된 범위로 SSO를 구성하는 경우 나중에 다른 정책과 애플리케이션에 액세스하려면 새 인증을 수행해야 합니다.

### <a name="how-to-configure-sso"></a>SSO를 구성하는 방법

여러 애플리케이션과 테넌트의 사용자 흐름이 동일한 사용자 세션을 공유할 수 있도록 테넌트 전체(기본값)로 [SSO를 구성](../hybrid/how-to-connect-sso-quick-start.md)합니다. 테넌트 전체 구성은 새 인증에 대해 가장 높은 복원력을 제공합니다.  

## <a name="safe-deployment-practices"></a>안전한 배포 사례

서비스를 방해하는 가장 일반적인 요소는 코드 변경과 구성 변경입니다. CICD(연속 통합 및 지속적인 업데이트) 프로세스와 도구를 채택하면 대규모로 신속하게 배포할 수 있고, 테스트 및 배포 중에 사용자의 오류를 줄일 수 있습니다. 오류 감소, 효율성, 일관성을 위해 CICD를 채택합니다. [Azure Pipelines](/azure/devops/pipelines/apps/cd/azure/cicd-data-overview)가 CICD의 한 가지 예입니다.

## <a name="web-application-firewall"></a>웹 애플리케이션 방화벽

[OWASP Top 10](https://owasp.org/www-project-top-ten/)에 설명된 대로 DDoS(분산형 서비스 거부) 공격, SQL 삽입, 교차 사이트 스크립팅, 원격 코드 실행 등 여러 알려진 취약성으로부터 애플리케이션을 보호할 수 있습니다. WAF(웹 애플리케이션 방화벽) 배포를 통해 일반적인 악용과 취약성으로부터 방어할 수 있습니다.

- 공격으로부터 중앙 집중화된 보호 기능을 제공하는 Azure [WAF](../../web-application-firewall/overview.md)를 사용합니다.

- Azure AD B2C를 사용하는 경우 Azure AD [ID 보호 및 조건부 액세스와 함께 WAF를 사용하면 다중 계층 보호 기능을 제공](../../active-directory-b2c/conditional-access-identity-protection-overview.md)할 수 있습니다.  

## <a name="secrets-rotation"></a>비밀 회전

Azure AD B2C는 애플리케이션, API, 정책, 암호화에 비밀을 사용합니다. 비밀은 인증, 외부 상호 작용, 스토리지를 보호합니다. NIST(미국 국립표준기술원)는 합법적인 엔터티에 의해 특정 키를 사용하도록 승인된 기간을 암호 사용 기간이라고 합니다. 비즈니스 요구 사항에 맞게 적절한 [암호 사용 기간](https://csrc.nist.gov/publications/detail/sp/800-57-part-1/rev-5/final)을 선택합니다. 개발자는 만료 전에 수동으로 만료를 설정하고 비밀도 순환해야 합니다.

### <a name="how-to-implement-secret-rotation"></a>비밀 순환을 구현하는 방법

- 지원되는 리소스에 [관리 ID](../managed-identities-azure-resources/overview.md)를 사용하여 Azure AD 인증을 지원하는 모든 서비스를 인증할 수 있습니다. 관리 ID를 사용하는 경우 자격 증명 순환을 포함하여 리소스를 자동으로 관리할 수 있습니다.

- Azure AD B2C에서 [구성된 모든 키 및 인증서](../../active-directory-b2c/policy-keys-overview.md)의 인벤토리를 작성합니다. 이 목록에는 사용자 지정 정책, [API](../../active-directory-b2c/secure-rest-api.md), 서명 ID 토큰, SAML용 인증서에 사용되는 키가 포함될 수 있습니다.

- CICD를 사용하여 사용량이 가장 많을 것으로 예상되는 시점으로부터 2개월 이내에 만료될 비밀을 순환합니다. 인증서에 연결된 프라이빗 키의 권장 암호 사용 기간은 최대 1년입니다.

- 암호, 인증서와 같은 API 액세스 자격 증명을 사전에 모니터링하고 순환합니다.

## <a name="test-rest-apis"></a>REST API 테스트

복원력의 컨텍스트에서 REST API 테스트에는 HTTP 코드, 응답 페이로드, 헤더, 성능에 대한 검증이 포함되어야 합니다. 테스트에 정상 경로에 대한 테스트만 포함해서는 안 되며 API가 문제 시나리오를 원활하게 처리하는지도 확인해야 합니다.

### <a name="how-to-test-apis"></a>API를 테스트하는 방법

테스트 계획에 [포괄적인 API 테스트](../../active-directory-b2c/best-practices.md#testing)를 포함하는 것이 좋습니다. 프로모션 또는 휴일 트래픽으로 인해 예상되는 급증에 대비하는 경우 새로운 추정치로 부하 테스트를 수정해야 합니다. 프로덕션 환경이 아닌 개발자 환경에서 API와 CDN(Content Delivery Network)의 부하 테스트를 수행합니다.

## <a name="next-steps"></a>다음 단계

- [Azure AD B2C 개발자를 위한 복원력 리소스](resilience-b2c.md)
  - [복원력 있는 최종 사용자 환경](resilient-end-user-experience.md)
  - [외부 프로세스를 사용하는 복원력 있는 인터페이스](resilient-external-processes.md)
  - [모니터링 및 분석을 통한 복원력](resilience-with-monitoring-alerting.md)
- [인증 인프라의 빌드 복원력 구축](resilience-in-infrastructure.md)
- [애플리케이션에서 인증 및 권한 부여 복원력 향상](resilience-app-development-overview.md)
