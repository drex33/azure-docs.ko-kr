---
title: F5 BIG-IP를 사용하여 Azure Active Directory B2C를 구성하는 자습서
titleSuffix: Azure AD B2C
description: 안전한 하이브리드 액세스를 위해 Azure AD B2C 인증을 F5 BIG-IP와 통합하는 방법 알아보기
author: gargi-sinha
ms.author: gasinh
manager: martinco
ms.service: active-directory
ms.subservice: B2C
ms.workload: identity
ms.topic: how-to
ms.date: 10/15/2021
ms.openlocfilehash: ca0d912c837a4c3fb218d1bb3fb8b07b43100119
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131044630"
---
# <a name="tutorial-extend-azure-active-directory-b2c-to-protect-on-premises-applications-using-f5-big-ip"></a>자습서: F5 BIG-IP를 사용하여 온-프레미스 애플리케이션을 보호하도록 Azure Active Directory B2C 확장

이 샘플 자습서에서는 Azure AD(Azure Active Directory) B2C를 [F5 BIG-IP APM(Access Policy Manager)](https://www.f5.com/services/resources/white-papers/easily-configure-secure-access-to-all-your-applications-via-azure-active-directory)과 통합하는 방법을 알아봅니다. 이 자습서에서는 Azure AD B2C 사전 인증, CA(조건부 액세스) 및 SSO(Single Sign-On)와 결합된 BIG-IP 보안을 통해 레거시 애플리케이션을 인터넷에 안전하게 노출할 수 있는 방법을 보여 줍니다.

F5 Inc.는 컴퓨팅, 스토리지 및 네트워크 리소스의 가용성을 포함하여 연결된 서비스의 제공, 보안, 성능 및 가용성에 중점을 둡니다. 하드웨어, 모듈화된 소프트웨어 및 클라우드 지원 가상 어플라이언스 솔루션을 제공합니다.

F5의 BIG-IP ADC(Application Delivery Controller)는 종종 프라이빗 네트워크와 인터넷 사이의 보안 게이트웨이로 배포됩니다.
애플리케이션 수준 검사 및 완전히 사용자 지정 가능한 액세스 제어를 포함하여 풍부한 기능을 제공합니다. 역방향 프록시로 배포되는 경우 BIG-IP는 F5의 APM이 관리하는 연합 ID 액세스 레이어를 사용하여 프런트 엔드 서비스를 통해 중요한 비즈니스 애플리케이션에 대한 보안 하이브리드 액세스를 가능하게 하는 데도 사용할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

시작하려면 다음이 필요합니다.

- Azure 구독에 연결된 [Azure AD B2C 테넌트](tutorial-create-tenant.md)

- 기존 BIG-IP 또는 평가판 배포 [Azure에서 BIG-IP VE(가상 환경)](../active-directory/manage-apps/f5-bigip-deployment-guide.md)

- 다음 F5 BIG-IP 라이선스 SKU 중 하나

  - F5 BIG-IP® Best 번들

  - F5 BIG-IP APM(Access Policy Manager™) 독립 실행형 라이선스

  - 기존 BIG-IP F5 BIG-IP LTM(Local Traffic Manager)에 대한 F5 BIG-IP Access Policy Manager 애드온 라이선스

  - BIG-IP 전체 기능 90일 [평가판 라이선스](https://www.f5.com/trial/big-ip-trial.php)

- 기존 헤더 기반 웹 애플리케이션 또는 테스트를 위한 [IIS 앱 설정](/previous-versions/iis/6.0-sdk/ms525396(v=vs.90))

- HTTPS를 통해 서비스를 게시하기 위한 [SSL 인증서](../active-directory/manage-apps/f5-bigip-deployment-guide.md#ssl-profile) 또는 테스트하는 동안 기본값을 사용합니다.

## <a name="scenario-description"></a>시나리오 설명

이 시나리오에서 Microsoft는 접근이 레거시 브로커 시스템으로부터 HTTP 권한 부여 헤더를 수신하는 것에 의존하는 내부 애플리케이션이 있어서 판매 에이전트가 각각의 콘텐츠 영역으로 이동할 수 있도록 합니다.

서비스를 더 넓은 소비자 기반으로 확장해야 하므로 애플리케이션을 업그레이드하여 소비자 인증 옵션을 선택하거나 더 적합한 솔루션으로 완전히 교체해야 합니다.

이상적인 환경에서 애플리케이션은 최신 컨트롤 플레인을 통해 직접 관리되고 통제되는 것을 지원하도록 업그레이드됩니다. 그러나 최신 상호 운용성이 없기 때문에 현대화하는 데 상당한 노력과 시간이 소요되며 불가피한 비용과 잠재적 가동 중지 시간 위험이 발생합니다.

대신 공용 인터넷과 애플리케이션이 연결된 내부 Azure VNet 사이에 배포된 BIG-IP VE(가상 버전)는 광범위한 로그인 및 등록 기능 선택을 위해 Azure AD B2C로 액세스를 제어하는 데 사용됩니다.

애플리케이션 앞에 BIG-IP가 있으면 Azure AD B2C 사전 인증 및 헤더 기반 SSO로 서비스를 오버레이할 수 있어 애플리케이션의 전반적인 보안 태세를 크게 개선하여 비즈니스가 중단 없이 속도로 계속 성장할 수 있습니다. .

이 시나리오에 대한 보안 하이브리드 액세스 솔루션은 다음 구성 요소로 구성됩니다.

- **애플리케이션** - Azure AD B2C 및 BIG-IP 보안 하이브리드 액세스로 보호되는 백 엔드 서비스

- **Azure AD B2C** - 사용자 자격 증명, MFA(다단계 인증) 및 BIG-IP APM에 대한 SSO 확인을 담당하는 IdP 및 OIDC(Open ID Connect) 권한 부여 서버입니다.

- **BIG-IP** - 애플리케이션의 역방향 프록시인 BIG-IP APM은 백 엔드 서비스에 대한 헤더 기반 SSO를 수행하기 전에 OIDC 권한 부여 서버에 권한 부여를 위임하는 OIDC 클라이언트도 됩니다.

다음 다이어그램은 이 시나리오에 대한 SP(서비스 공급자) 시작 흐름을 보여 줍니다.

![이 시나리오에 대한 SP 시작 흐름을 보여 주는 스크린샷](./media/partner-f5/flow-diagram.png)

|단계| 설명|
|:----|:-------|
| 1. | 사용자는 BIG-IP가 서비스 공급자인 애플리케이션 엔드포인트에 연결합니다. |
| 2. | OIDC 클라이언트인 BIG-IP APM은 사용자를 Azure AD B2C 테넌트 엔드포인트인 OIDC 권한 부여 서버로 리디렉션합니다. |
| 3. | Azure AD B2C 테넌트는 사용자를 사전 인증하고 적용된 조건부 액세스 정책을 적용합니다. |
|4. | Azure AD B2C는 인증 코드를 사용하여 사용자를 SP로 다시 리디렉션합니다. |
| 5. | OIDC 클라이언트는 권한 부여 서버에 인증 코드를 ID 토큰으로 교환하도록 요청합니다. |
| 6. | BIG-IP APM은 사용자 액세스 권한을 부여하고 애플리케이션에 전달된 클라이언트 요청에 HTTP 헤더를 삽입합니다. |

보안 강화를 위해 이 패턴을 사용하는 조직은 애플리케이션에 대한 모든 직접 액세스를 차단하여 BIG-IP를 통한 엄격한 경로를 적용할 수도 있습니다.

## <a name="azure-ad-b2c-configuration"></a>Azure AD B2C 구성 업데이트

Azure AD B2C 인증으로 BIG-IP를 사용하도록 설정하려면 적절한 사용자 흐름 또는 사용자 지정 정책이 있는 Azure AD B2C 테넌트가 필요합니다. [Azure AD B2C 사용자 흐름을 설정합니다](tutorial-create-user-flows.md).

### <a name="create-custom-attributes"></a>사용자 지정 특성 만들기

사용자 지정 특성은 기존 Azure AD B2C 사용자 개체에서 직접 가져오거나, 페더레이션된 IdP, API 커넥터에서 요청하거나, 사용자 등록 과정에서 수집하는 등 다양한 원본에서 가져올 수 있습니다. 필요한 경우 애플리케이션에 전송되는 토큰에 포함될 수 있습니다.

레거시 애플리케이션은 특정 특성을 예상하므로 사용자 흐름에 이러한 특성을 포함합니다. 그러나 애플리케이션에 필요한 특성으로 자유롭게 바꾸세요. 또는 필수 조건의 지침을 사용하여 테스트 앱을 설정하는 경우 헤더가 모두 표시되는 대로 작동합니다.

1. Azure AD B2C 테넌트 포털에 로그인합니다.

2. 왼쪽 창에서 **사용자 특성** 을 선택한 다음 **추가** 를 선택하여 두 개의 사용자 지정 특성을 만듭니다.

   - 에이전트 ID: 문자열 **데이터 형식**

   - 에이전트 지역: 문자열 **데이터 형식**

### <a name="add-attributes-to-user-flow"></a>사용자 흐름에 특성 추가

1. 왼쪽 창에서 **정책** > **사용자 흐름** 으로 이동합니다.

2. 정책을 선택합니다(예: **B2C_1_SignupSignin**).

3. **사용자 특성** 을 선택하고 두 사용자 지정 특성과 **표시 이름** 특성을 모두 추가합니다. 사용자 등록 중에 수집되는 특성입니다.

4. **애플리케이션 클레임** 을 선택하고 사용자 지정 특성과 **표시 이름** 을 모두 추가합니다. 이는 BIG-IP로 전송되는 특성입니다.

왼쪽 탐색 모음의 사용자 흐름 메뉴에서 [사용자 흐름 실행](tutorial-create-user-flows.md) 기능을 사용하여 정의된 모든 특성에 대한 프롬프트를 확인할 수 있습니다.

### <a name="azure-ad-b2c-federation"></a>Azure AD B2C 페더레이션

BIG-IP와 Azure AD B2C가 서로를 신뢰하려면 페더레이션이 필요하므로 BIG-IP는 Azure AD B2C 테넌트에 OIDC 애플리케이션으로 등록되어야 합니다.

1. 여전히 Azure AD B2C 포털에서 **앱 등록** >  **새 등록** 을 선택합니다.

2. 애플리케이션 이름을 제공합니다. 예: **HeaderApp1**

3. **지원되는 계정 유형** 아래에서 **모든 ID 공급자 또는 조직 디렉터리의 계정(사용자 흐름에서 사용자를 인증하는 용도)** 을 선택합니다.

4. **리디렉션 URI** 에서 **웹** 을 선택하고 경로와 함께 보호되는 서비스의 공용 FQDN을 입력합니다.

5. 나머지는 그대로 두고 **등록** 을 선택합니다.

6. **인증서 및 비밀** >  **+ 새 클라이언트 암호** 로 이동합니다.

7. BIG-IP에서 사용할 비밀에 대한 설명이 포함된 이름과 TTL을 제공합니다.

8. 클라이언트 암호를 기록해 두세요. 나중에 BIG-IP를 구성할 때 필요합니다.

리디렉션 URI는 권한 부여 후 권한 부여 서버(Azure AD B2C)에서 사용자를 다시 보내는 BIG-IP 엔드포인트입니다. Azure AD B2C용 [애플리케이션을 등록](tutorial-register-applications.md)합니다.

## <a name="big-ip-configuration"></a>BIG-IP 구성

BIG-IP는 마법사 기반 단계별 구성을 포함하여 Azure AD 보안 하이브리드 액세스를 구성하기 위한 여러 가지 방법을 제공하여 몇 가지 일반적인 시나리오를 구현하기 위한 시간과 노력을 최소화합니다. 워크플로 기반 프레임워크는 특정 액세스 토폴로지에 맞게 조정된 직관적인 경험을 제공하며 게시를 위해 최소한의 구성이 필요한 웹 서비스의 신속한 게시에 사용됩니다.

### <a name="version-check"></a>버전 확인

이 자습서는 단계별 구성 v.7/8을 기반으로 하지만 이전 버전에도 적용될 수 있습니다. 버전을 확인하려면 관리자 계정으로 BIG-IP 웹 구성에 로그인하고 **액세스** > **단계별 구성** 으로 이동합니다. 버전은 오른쪽 상단에 표시되어야 합니다. BIG-IP의 단계별 구성을 업그레이드하려면 [이 지침](https://support.f5.com/csp/article/K85454683)을 따르세요.

### <a name="ssl-profiles"></a>SSL 프로필

클라이언트 SSL 프로필로 BIG-IP를 구성하면 TLS를 통해 클라이언트 쪽 트래픽을 보호할 수 있습니다. 이렇게 하려면 애플리케이션의 공개 URL에서 사용하는 도메인 이름과 일치하는 인증서를 가져와야 합니다. 가능한 경우 공개 인증 기관을 사용하는 것이 좋지만 테스트하는 동안 내장된 BIG-IP 자체 서명 인증서를 사용할 수도 있습니다.
BIG-IP VE에서 [인증서를 추가 및 관리](https://techdocs.f5.com/kb/en-us/products/big-ip_ltm/manuals/product/bigip-ssl-administration-13-0-0.html)합니다.

## <a name="guided-configuration"></a>단계별 구성

1. 웹 구성에서 **액세스** > **단계별 구성** 으로 이동하여 배포 마법사를 시작합니다.

2. **페더레이션** > **OAuth 클라이언트 및 리소스 서버로서의 F5** 를 선택합니다.

3. 이 시나리오의 흐름 요약을 관찰하고 **다음** 을 선택하여 마법사를 시작합니다.

### <a name="oauth-properties"></a>OAuth 속성

이 섹션에서는 BIG-IP APM과 Azure AD B2C 테넌트인 OAuth 권한 부여 서버 간의 페더레이션을 사용하도록 설정하는 속성을 정의합니다. OAuth는 BIG-IP 구성 전체에서 참조되지만 솔루션은 실제로 OIDC 클라이언트가 사용자의 ID를 확인하고 다른 프로필 정보를 얻을 수 있도록 하는 OAuth 2.0 프로토콜 위에 있는 간단한 ID 계층인 OIDC를 사용합니다.

실수가 인증 및 액세스에 영향을 미치므로 세부 사항에 세심한 주의를 기울이세요.

#### <a name="configuration-name"></a>구성 이름

구성에 대한 표시 이름을 제공하면 안내 구성에 결국 존재할 수 있는 많은 배포 구성을 구별하는 데 도움이 됩니다. 설정한 후에는 이름을 변경할 수 없으며 단계별 구성 보기에서만 볼 수 있습니다.

#### <a name="mode"></a>모드

BIG-IP APM은 OIDC 클라이언트로 작동하므로 클라이언트 옵션만 선택합니다.

#### <a name="dns-resolver"></a>DNS 확인자

지정된 대상은 Azure AD B2C 엔드포인트의 공용 IP 주소를 확인할 수 있어야 합니다. 기존 공용 DNS 확인자를 선택하거나 새로 만듭니다.

#### <a name="provider-settings"></a>공급자 설정

여기서는 Azure AD B2C를 OAuth2 IdP로 구성합니다. 단계별 구성 v8은 Azure AD B2C 템플릿을 제공하지만 몇 가지 범위가 없기 때문에 지금은 사용자 지정 유형을 잘 사용합니다. F5는 향후 단계별 구성 업데이트에 누락된 범위를 포함하려고 합니다. 새 공급자를 추가하고 다음과 같이 구성합니다.

- **OAuth 일반 속성**

  | 속성 | 설명 |
  |:-------|:---------|
  |OAuth 공급자 유형 | 사용자 지정 |
  | OAuth 공급자 선택 | 새로 만들기(또는 기존 OAuth 공급자가 있는 경우 사용) |
  | 속성 | B2C IdP의 고유한 표시 이름입니다. 이 이름은 로그인에 대한 공급자 옵션으로 사용자에게 표시됩니다.|
  | 토큰 형식 | JSON Web Token |

- **OAuth 정책 설정**

  | 속성 | 설명 |
  |:-----------|:----------------|
  | 범위 | 비워두면 사용자 로그인을 위한 OpenID 범위가 자동으로 추가됩니다. |
  | 권한 부여 유형 | 인증 코드 |
  | OpenID Connect 사용 | APM OAuth 클라이언트를 OIDC 모드로 설정하려면 확인합니다. |
  | 흐름 형식 | 인증 코드 |

- **OAuth 공급자 설정**

  아래 OpenID URI는 서명 인증서 롤오버와 같은 중요한 IdP 정보를 자동 검색하기 위해 OIDC 클라이언트에서 사용하는 메타데이터 엔드포인트를 나타냅니다. **앱 등록** > **엔드포인트** 로 이동하고 Azure AD B2C OpenID Connect 메타데이터 문서 URI를 복사하여 Azure AD B2C 테넌트의 메타데이터 엔드포인트를 찾습니다. 예들 들어 `https://wacketywackb2c .b2clogin.com/<tenantname>.onmicrosoft.com/<policyname>/v2.0/.well-known/openid-configuration`입니다.

  그런 다음 고유한 속성 `https://<tenantname>.b2clogin.com/WacketywackB2C.onmicrosoft.com/B2C_1_SignUpIn/v2.0/.well-known/openid-configuration`으로 URI를 업데이트합니다.

  Azure AD B2C 테넌트에 대한 OIDC 메타데이터를 보려면 이 URI를 브라우저에 붙여넣습니다.

  | 속성 | 설명 |
  |:----------|:----------|
  | 사용자 | Azure AD B2C 테넌트에서 BIG-IP를 나타내는 애플리케이션의 클라이언트 ID |
  | 인증 URI | B2C OIDC 메타데이터의 권한 부여 엔드포인트 |
  | 토큰 URI | Azure AD B2C 메타데이터의 토큰 엔드포인트 |
  | 사용자 정보 요청 URI | 비워 둡니다. Azure AD B2C는 현재 이 기능을 지원하지 않습니다. |
  |OpenID URI | 위에서 만든 OpenID URI 메타데이터 엔드포인트 |
  | 만료된 인증서 유효성 검사 무시 | 확인되지 않은 상태로 둡니다. |
  | 자체 서명된 JWK 구성 인증서 허용 | 확인 |
  | 신뢰할 수 있는 CA 번들 | 기본 F5 신뢰할 수 있는 기관을 사용하려면 ca-bundle.crt를 선택합니다. |
  | 검색 간격 | BIG-IP가 업데이트를 위해 Azure AD B2C 테넌트를 쿼리하도록 적절한 간격을 제공합니다. AGC 버전 16.1 0.0.19 final에서 제공하는 최소 간격 시간은 5분입니다.|

- **OAuth 서버 설정**

  이 섹션은 Azure AD B2C 테넌트인 OIDC 권한 부여 서버를 나타냅니다.

  |속성 | 설명|
  |:---------|:---------|
  | 클라이언트 ID | Azure AD B2C 테넌트에서 BIG-IP를 나타내는 애플리케이션의 클라이언트 ID입니다. |
  | 클라이언트 암호 | 애플리케이션에 해당하는 클라이언트 암호입니다. |
  |클라이언트-서버 SSL 프로필 | SSL 프로필을 설정하면 APM이 TLS를 통해 Azure AD B2C IdP와 통신합니다. 기본 `serverssl` 옵션을 선택합니다. |

- **OAuth 요청 설정**

  흥미롭게도 BIG-IP에는 사전 구성된 요청 세트에 필요한 모든 Azure AD B2C 요청이 있습니다. 그러나 Microsoft가 구현한 빌드의 경우 이러한 요청의 형식이 잘못되었으며 중요한 매개 변수가 누락된 것으로 관찰되었습니다. 그래서 Microsoft는 수동으로 만들기로 결정했습니다.

- **토큰 요청 - 사용**

  | 속성 | 설명 |
  |:-----------|:------------|
  | OAuth 요청 선택 | 새로 만들기 |
  | HTTP 메서드 | POST |
  | 헤더 사용| 선택 취소됨 |
  | 매개 변수 사용 | 선택됨 |

  | 매개 변수 형식 | 매개 변수 이름 | 매개 변수 값|
  |:---------|:---------------|:----------------|
  | client-id | client-id | |
  | nonce | nonce| |
  | redirect-uri | redirect-uri | |
  | scope | scope | |
  | response-type | response-type | |
  | client-secret | client-secret | |
  | custom | grant_type | authorization_code |

- **인증 리디렉션 요청 - 사용**

  | 속성 | 설명 |
  |:-----------|:------------|
  | OAuth 요청 선택 | 새로 만들기 |
  | HTTP 메서드 | GET |
  | 프롬프트 형식 | 없음 |
  | 헤더 사용 | 선택 취소됨 |
  | 매개 변수 사용 | 선택됨 |

  | 매개 변수 형식 | 매개 변수 이름 | 매개 변수 값|
  |:---------|:---------------|:----------------|
  | client-id | client-id | |
  | redirect-uri | redirect-uri | |
  | response-type |response-type | |
  | scope | scope | |
  | nonce | nonce | |

- **토큰 새로 고침 요청** - **사용 안 함** - 필요한 경우 사용하도록 설정 및 구성할 수 있습니다.

- **OpenID UserInfo 요청** - **사용 안 함** - 현재 전역 Azure AD B2C 테넌트에서 지원되지 않습니다.

- **가상 서버 속성**

  보안 하이브리드 액세스를 통해 보호되는 백 엔드 서비스에 대한 외부 클라이언트 요청을 가로채려면 BIG-IP 가상 서버를 만들어야 합니다. 가상 서버에는 애플리케이션을 나타내는 BIG-IP 서비스 엔드포인트에 대한 공용 DNS 레코드에 매핑되는 IP가 할당되어야 합니다. 가능한 경우 기존 가상 서버를 사용하고, 그렇지 않으면 다음을 제공합니다.

  | 속성 | 설명 |
  |:-----------|:------------|
  | 대상 주소 | 백 엔드 애플리케이션의 BIG-IP 서비스 엔드포인트가 될 프라이빗 또는 공용 IP |
  | 서비스 포트 | HTTPS |
  | 리디렉션 포트 사용 | 사용자가 http에서 https로 자동 리디렉션되도록 하려면 확인합니다. |
  | 리디렉션 포트 | HTTP |
  | 클라이언트 SSL 프로필 | 사전 정의된 `clientssl` 프로필을 SSL 인증서가 포함된 프로필로 바꿉니다. 기본 프로필로 테스트해도 괜찮지만 브라우저 경고가 발생할 수 있습니다. |

- **풀 속성**

  백 엔드 서비스는 가상 서버가 인바운드 트래픽을 보내는 하나 이상의 애플리케이션 서버를 포함하는 풀로 BIG-IP에 표시됩니다. 기존 풀을 선택하고 그렇지 않으면 새 풀을 만듭니다.

  | 속성 | 설명 |
  |:-----------|:------------|
  | 부하 분산 메서드 | 라운드 로빈으로 나가기 |
  |풀 서버 | 백 엔드 애플리케이션의 내부 IP |
  | 포트 | 백 엔드 애플리케이션의 서비스 포트 |
  
>[!NOTE]
>BIG-IP는 지정된 풀 서버 주소에 대한 가시선이 있어야 합니다.

- **Single Sign-On 설정**

  BIG-IP는 많은 SSO 옵션을 지원하지만 OAuth 클라이언트 모드에서 Guided Config는 Kerberos 또는 HTTP 헤더로 제한됩니다. SSO를 사용하도록 설정하고 다음 정보를 사용하여 APM이 이전에 정의한 인바운드 특성을 아웃바운드 헤더에 매핑하도록 합니다.

  | 속성 | 설명 |
  |:-----------|:------------|
  | 헤더 작업 |`Insert`|
  | 헤더 이름 | '이름' |
  | 헤더 값 | `%{session.oauth.client.last.id_token.name}`|
  | 헤더 작업 | `Insert`|
  |헤더 이름| `agentid`|
  |헤더 값 | `%{session.oauth.client.last.id_token.extension_AgentGeo}`|
 
  >[!Note]
  > 중괄호 안에 정의된 APM 세션 변수는 대/소문자를 구분합니다. 따라서 Azure AD B2C 특성 이름이 AgentID로 전송될 때 agentid를 입력하면 특성 매핑 실패가 발생합니다. 필요한 경우가 아니면 모든 특성을 소문자로 정의하는 것이 좋습니다. Azure AD B2C의 경우 사용자 흐름은 포털에 표시되는 특성 이름을 사용하여 추가 특성을 묻는 메시지를 표시하므로 소문자 대신 일반 문장을 사용하는 것이 좋습니다.

  ![스크린샷은 사용자 Single Sign-On 설정을 보여 줍니다.](./media/partner-f5/single-sign-on.png)

- **사용자 지정 속성**

  이러한 설정을 사용하면 사용자가 APM 액세스 정책 흐름과 상호 작용할 때 보게 되는 화면의 언어와 모양과 느낌을 사용자 지정할 수 있습니다. 화면 메시지 및 프롬프트를 개인화하고, 화면 레이아웃, 색상, 이미지를 변경하고, 일반적으로 액세스 정책 항목에서 사용자 지정할 수 있는 캡션, 설명 및 메시지를 현지화할 수 있습니다.

  양식 머리글 텍스트 필드의 "F5 Networks 문자열"을 자신의 조직 이름으로 바꿉니다. 예: "Wacketywack Inc. 보안 하이브리드 액세스.

- **세션 관리 속성**

  BIG-IP 세션 관리 설정은 사용자 세션이 종료되거나 계속되도록 허용되는 조건, 사용자 및 IP 주소에 대한 제한, 오류 페이지를 정의하는 데 사용됩니다. 이는 선택 사항이지만 SLO(단일 로그아웃) 기능을 구현하는 것이 좋습니다. 이 기능은 세션이 더 이상 필요하지 않을 때 안전하게 종료되어 누군가가 실수로 게시된 애플리케이션에 무단으로 액세스할 수 있는 위험을 줄입니다.

## <a name="related-information"></a>관련 정보

마지막 단계에서는 구성에 대한 개요를 제공합니다. 배포를 누르면 애플리케이션에 대한 보안 하이브리드 액세스를 사용하도록 설정하기 위해 설정을 커밋하고 필요한 모든 BIG-IP 및 APM 개체를 만듭니다.
애플리케이션은 CA에서 대상 리소스로도 표시되어야 합니다. [Azure AD B2C용 CA 정책 구축 지침](conditional-access-identity-protection-overview.md)을 참조하세요.
보안 강화를 위해 이 패턴을 사용하는 조직은 애플리케이션에 대한 모든 직접 액세스를 차단하여 BIG-IP를 통한 엄격한 경로를 강제할 수도 있습니다.

## <a name="next-steps"></a>다음 단계

사용자로서 브라우저를 실행하고 애플리케이션 외부 URL에 연결합니다. BIG-IP OAuth 클라이언트 로그온 페이지는 인증 코드 부여를 사용하여 로그온하라는 메시지를 표시합니다. 이 단계를 제거하기 위한 지침은 추가 구성 섹션에서 제공됩니다.

그런 다음 Azure AD B2C 테넌트에 등록하고 인증하도록 리디렉션됩니다.

![스크린샷은 사용자 로그인을 보여 줍니다.](./media/partner-f5/sign-in-message.png)

![스크린샷은 로그인 후 환영 메시지를 보여 줍니다.](./media/partner-f5/welcome-page.png)

### <a name="supplemental-configurations"></a>추가 구성

**SLO(단일 로그아웃)**

Azure AD B2C는 다양한 [메커니즘](session-behavior.md?pivots=b2c-custom-policy#single-sign-out)을 통해 IdP 및 애플리케이션 로그아웃을 완벽하게 지원합니다.
애플리케이션 로그아웃 함수가 Azure AD B2C 로그아웃 엔드포인트를 호출하도록 하는 것은 SLO를 달성하는 한 가지 방법입니다. 그렇게 하면 Azure AD B2C가 BIG-IP에 대한 최종 리디렉션을 실행하여 사용자와 애플리케이션 간의 APM 세션도 종료되었는지 확인할 수 있습니다.
또 다른 대안은 애플리케이션 로그아웃 단추를 선택할 때 BIG-IP가 요청을 수신하도록 하고 요청을 탐지하면 Azure AD B2C 로그오프 엔드포인트를 동시에 호출하는 것입니다. 이 접근 방식을 사용하면 애플리케이션 자체를 변경하지 않아도 SLO를 달성할 수 있습니다. 이를 구현하기 위해 BIG-IP iRules를 사용하는 방법에 대한 자세한 내용은 [사용 가능](https://support.f5.com/csp/article/K42052145)합니다.
두 경우 모두 Azure AD B2C 테넌트가 APM 로그아웃 엔드포인트를 알아야 합니다. 

1. Azure AD B2C 포털에서 **관리** > **매니페스트** 로 이동하여 logoutUrl 속성을 찾습니다. null로 읽어야 합니다.

2. APM 포스트 로그아웃 URI(`https://<mysite.com>/my.logout.php3`)를 추가합니다. 여기서 `<mysite.com>`는 자체 헤더 기반 애플리케이션에 대한 BIG-IP FQDN입니다.

**최적화된 로그인 흐름**

사용자 로그인 환경을 개선하기 위한 한 가지 선택적 단계는 Azure AD 사전 인증 전에 사용자에게 표시되는 OAuth 로그온 프롬프트를 표시하지 않는 것입니다. 

1. **액세스** > **단계별 구성** 으로 이동하여 헤더 기반 애플리케이션 행의 맨 오른쪽에 있는 작은 자물쇠 아이콘을 선택하여 엄격한 구성을 잠금 해제합니다.

   ![스크린샷은 최적화된 로그인 흐름을 보여 줍니다.](./media/partner-f5/optimized-login-flow.png)

   엄격한 구성의 잠금을 해제하면 마법사 UI를 통한 추가 변경이 방지되어 애플리케이션의 게시된 인스턴스와 연결된 모든 BIG-IP 개체가 직접 관리를 위해 열려 있습니다.

2. **액세스** > **프로필/정책** > **액세스 프로필(세션당 정책)** 로 이동하여 애플리케이션의 정책 개체에 대한 **세션별 정책** 편집 링크를 선택합니다.

   ![스크린샷은 액세스 프로필을 보여 줍니다.](./media/partner-f5/access-profile.png)

3. 작은 십자가를 선택하여 OAuth 로그온 페이지 정책 개체를 삭제하고 메시지가 표시되면 이전 노드에 연결하도록 선택합니다.

   ![스크린샷은 OAuth 로그온 페이지를 보여 줍니다.](./media/partner-f5/oauth-logon.png)

4. 왼쪽 상단에서 **액세스 정책 적용** 을 선택하고 시각적 편집기 탭을 닫습니다. 다음에 애플리케이션에 연결하려고 하면 Azure AD B2C 로그인 페이지로 바로 이동해야 합니다.

>[!Note]
>strict 모드를 다시 사용하도록 설정하고 구성을 배포하면 단계별 구성 UI 외부에서 수행된 모든 설정을 덮어쓰므로 프로덕션 서비스에 대해 모든 구성 개체를 수동으로 만들어 이 시나리오를 구현하는 것이 좋습니다.

### <a name="troubleshooting"></a>문제 해결

보호된 애플리케이션에 액세스하지 못하면 잘못된 구성을 포함하여 여러 가지 잠재적 요인이 발생할 수 있습니다.

- BIG-IP 로그는 모든 인증 및 SSO 문제를 분리하기 위한 훌륭한 정보 원본입니다. 문제를 해결하려면 로그 상세 수준을 높여야 합니다.

  1. **액세스 정책** > **개요** > **이벤트 로그** > **설정** 으로 이동합니다.

  2. 게시된 애플리케이션의 행을 선택한 다음 **편집** > **시스템 로그 액세스** 를 선택합니다.

  3. SSO 목록에서 **디버그** 를 선택한 다음 **확인** 을 선택합니다. 이제 로그를 보기 전에 문제를 재현할 수 있지만 완료되면 다시 전환해야 합니다.

- Azure AD B2C 인증에 성공한 직후에 BIG-IP 브랜드 오류가 표시되면 Azure AD에서 BIG-IP로의 SSO와 관련된 문제일 수 있습니다.

  1. **액세스** > **개요** > **액세스 보고서** 로 이동합니다.

  2. 지난 1시간 동안 보고서를 실행하여 로그에서 단서를 제공하는지 확인합니다. 세션에 대한 세션 변수 보기 링크는 APM이 Azure AD에서 예상되는 클레임을 수신하는지 이해하는 데 도움이 됩니다.

- BIG-IP 오류 페이지가 표시되지 않으면 문제가 BIG-IP에서 애플리케이션으로의 백 엔드 요청 또는 SSO와 더 관련이 있을 수 있습니다.

  1. **액세스 정책** > **개요** > **활성 세션** 으로 이동합니다.

  2. 활성 세션에 대한 링크를 선택합니다.

- 이 위치의 변수 보기 링크는 특히 BIG-IP APM이 올바른 세션 특성을 얻지 못하는 경우 근본 원인을 파악하는 데 도움이 될 수 있습니다.
그러면 애플리케이션 로그가 해당 특성을 헤더로 수신했는지 여부를 이해하는 데 도움이 됩니다.

- 단계별 구성 v8을 사용하는 경우 Azure AD B2C 인증에 성공한 후 다음 BIG-IP 오류를 생성하는 알려진 문제를 알고 있어야 합니다.  

  ![스크린샷은 오류 메시지를 보여 줍니다.](./media/partner-f5/error-message.png)

  이는 BIG-IP가 Azure AD B2C에서 발급한 토큰의 서명을 확인할 수 없기 때문에 발생하는 정책 위반입니다. 동일한 액세스 로그가 문제에 대한 자세한 정보를 제공할 수 있어야 합니다.

  ![스크린샷은 액세스 로그를 보여 줍니다.](./media/partner-f5/access-log.png)

  정확한 근본 원인은 여전히 F5 엔지니어링에서 조사 중이지만 배포 중에 AGC가 자동 JWT 설정을 사용하도록 설정하지 않아 APM이 현재 토큰 서명 키를 얻지 못하는 것과 관련된 문제가 나타납니다.

  해결될 때까지 이 문제를 해결하는 한 가지 방법은 이 설정을 수동으로 사용하도록 설정하는 것입니다. 

  1. **액세스** > **단계별 구성** 으로 이동하여 헤더 기반 애플리케이션 행의 맨 오른쪽에 있는 작은 자물쇠 아이콘을 선택합니다.

  2. 관리 구성이 잠금 해제된 상태에서 **액세스** > **제휴** > **OAuth 클라이언트/리소스 서버** > **공급자** 로 이동합니다.

  3. Azure AD B2C 구성에 대한 공급자를 선택합니다.

  4. **자동 JWT 사용** 확인란을 선택한 다음 **검색** 을 선택한 다음 **저장** 을 선택합니다.

    이제 OpenID URI 메타데이터를 통해 제공된 토큰 서명 인증서의 KID(키 ID)로 채워진 키(JWT) 필드가 표시되어야 합니다.
  
  5. 마지막으로 F5 로고 옆에 있는 왼쪽 상단의 노란색 **액세스 정책 적용** 옵션을 선택합니다. 이러한 설정을 적용하고 **적용** 을 다시 선택하여 액세스 프로필 목록을 새로 고칩니다.

[OAuth 클라이언트 및 리소스 서버 문제 해결 팁](https://techdocs.f5.com/en-us/bigip-14-1-0/big-ip-access-policy-manager-oauth-configuration-14-1-0/apm-oauth-client-and-resource-server.html#GUID-774384BC-CF63-469D-A589-1595D0DDFBA2)에 대한 자세한 내용은 F5 지침을 참조하세요.
