---
title: Ping ID를 사용하여 Azure Active Directory B2C를 구성하는 자습서
titleSuffix: Azure AD B2C
description: Ping ID를 사용하여 Azure AD B2C 인증을 통합하는 방법 알아보기.
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 01/20/2021
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 5c7938e9c80cbce1128981516ccac2adcbcb8949
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131044535"
---
# <a name="tutorial-configure-ping-identity-with-azure-active-directory-b2c-for-secure-hybrid-access"></a>자습서: 보안 하이브리드 액세스를 위해 Azure Active Directory B2C를 사용하여 Ping ID 구성하기

이 샘플 자습서에서는 보안 하이브리드 액세스를 사용하도록 설정하는 [PingAccess](https://www.pingidentity.com/en/software/pingaccess.html#:~:text=%20Modern%20Access%20Managementfor%20the%20Digital%20Enterprise%20,consistent%20enforcement%20of%20security%20policies%20by...%20More) 및 [PingFederate](https://www.pingidentity.com/en/software/pingfederate.html)를 사용하여 Azure Active Directory(AD) B2C 를 확장하는 방법에 대해 알아봅니다.

인터넷에 노출되는 전자 상거래 사이트 및 웹 애플리케이션과 같은 많은 기존 웹 속성은 프록시 시스템 뒤에 배포되며, 때로는 역방향 프록시 시스템이라고도 합니다. 프록시 시스템은 사전 인증, 정책 적용 및 트래픽 라우팅을 비롯한 다양한 기능을 제공합니다. 사용 사례에는 인바운드 웹 트래픽으로부터 웹 애플리케이션을 보호하고 분산 서버 배포에서 일관된 세션 관리를 제공하는 작업이 포함됩니다.

이 구성에는 대부분 웹 애플리케이션으로부터 인증을 표면화하는 인증 이 번역 레이어가 포함됩니다. 역방향 프록시는 인증된 사용자의 컨텍스트를 일반 형식 또는 다이제스트 형식의 헤더 값과 같은 간단한 형식으로 웹 애플리케이션에 제공합니다. 해당 구성에서 애플리케이션은 Security Assertion Markup Language(SAML), OAuth 또는 Open ID Connect(OIDC)와 같은 산업 표준 토큰을 사용하지 않고 프록시에 따라 인증 컨텍스트를 제공하고 브라우저 또는 네이티브 애플리케이션과 같은 최종 사용자 에이전트로 세션을 유지합니다. ‘중간자(man-in-the-middle)’에서 실행되는 서비스의 경우 프록시는 최종 세션 제어를 제공할 수 있습니다. 이는 프록시 서비스가 매우 효율적이고 확장 가능해야 한다는 것을 의미합니다. 이는 프록시 서비스 뒤에 있는 애플리케이션에 대해 단일 실패 지점이 되거나 병목 상태가 되지 않아야 한다는 것입니다. 다이어그램은 일반적인 역방향 프록시 구현 및 통신 흐름을 설명합니다.

![이미지에서 역방향 프록시 구현을 확인할 수 있습니다.](./media/partner-ping/reverse-proxy.png)

해당 구성에서 ID 플랫폼을 최신화하려는 경우 다음 사항을 고려해야 합니다.

- 애플리케이션 최신화 활동을 ID 플랫폼의 최신화와 분리하는 방법

- 최신화된 ID 서비스 공급자로부터 구매한 최신 및 레거시 인증을 통한 공용 환경 구축 방법

  a. 최종 사용자 환경 일관성을 유지하는 방법

  b. 공용 애플리케이션에서 단일 로그인 환경을 제공하는 방법

이 문제를 Azure AD B2C과 [PingAccess](https://www.pingidentity.com/en/software/pingaccess.html#:~:text=%20Modern%20Access%20Managementfor%20the%20Digital%20Enterprise%20,consistent%20enforcement%20of%20security%20policies%20by...%20More) 및 [PingFederate](https://www.pingidentity.com/en/software/pingfederate.html)를 통합하여 해결하는 방법에 대해 설명합니다.

## <a name="coexistence-environment"></a>공용 환경

기술적으로 가능하고 간단하면서 비용 효율적인 솔루션은 인증을 위임하여 최신화된 ID 시스템을 사용하는 역방향 프록시 시스템을 구성하는 것입니다.  
이 경우 프록시는 최신 인증 프로토콜을 지원하고 사용자를 새 ID 공급자(IdP)에게 보내는 리디렉션 기반(수동) 인증을 사용합니다.

### <a name="azure-ad-b2c-as-an-identity-provider"></a>ID 공급자로 Azure AD B2C

Azure AD B2C에는 서버 엔드에서 오케스트레이션된 **사용자 경험** 이라고도 불리는 다른 사용자 환경 및 동작을 구동하는 **정책** 을 정의할 수 있는 기능이 있습니다. 각 정책은 IdP처럼 인증을 수행할 수 있는 프로토콜 엔드포인트를 노출합니다. 특정 정책에 대한 애플리케이션 쪽에는 특별한 처리가 필요하지 않습니다. 애플리케이션은 단지 관련 정책에 의해 노출되는 프로토콜별 인증 엔드포인트에 대한 업계 표준 인증 요청을 만듭니다.  
Azure AD B2C는 각 정책에 대한 여러 정책 또는 고유한 발급자에서 동일한 발급자를 공유하도록 구성할 수 있습니다. 각 애플리케이션은 프로토콜 네이티브 인증 요청을 만들어 해당 정책 중 하나 이상을 가리킬 수 있고 로그인, 등록 및 프로필 편집과 같은 원하는 사용자 동작을 구동할 수 있습니다. 다이어그램에서 OIDC와 SAML 애플리케이션 워크플로를 확인할 수 있습니다.

![OIDC와 SAML 구현을 보여 주는 이미지](./media/partner-ping/azure-ad-identity-provider.png)

언급된 시나리오는 최신화된 애플리케이션에 적합하지만, 애플리케이션에 대한 액세스 요청에 사용자 환경에 대한 컨텍스트가 포함되지 않을 수 있으므로 레거시 애플리케이션에서 사용자를 적절하게 리디렉션하는 것이 어려울 수 있습니다. 대부분의 경우 프록시 계층 또는 웹 애플리케이션의 통합 에이전트가 액세스 요청을 가로챕니다.

### <a name="pingaccess-as-a-reverse-proxy"></a>역방향 프록시로 PingAccess

여러 고객들이 이 문서의 서두에서 설명한 대로 하나 이상의 역할을 하는 역방향 프록시로 PingAccess를 배포했습니다. PingAccess는 중간자(man-in-the-middle) 또는 웹 애플리케이션 서버에서 실행되는 에이전트에서 제공되는 리디렉션으로 직접 요청을 가로챌 수 있습니다.

PingAccess는 OIDC, OAuth2 또는 SAML로 구성하여 업스트림 인증 공급자에 대한 인증을 수행할 수 있습니다. PingAccess 서버에서 이 목적을 위해 단일 업스트림 IdP을 구성할 수 있습니다. 다음 다이어그램에서는 이 구성을 보여 줍니다.

![OIDC 구현에 대한 PingAccess를 보여 주는 이미지](./media/partner-ping/authorization-flow.png)

다수의 정책이 다수의 **IdP** 를 노출하는 일반적인 Azure AD B2C 배포에서는 어려움이 있을 수 있습니다. PingAccess는 단일 업스트림 IdP로만 구성될 수 있기 때문입니다.  

### <a name="pingfederate-as-a-federation-proxy"></a>페더레이션 프록시로 PingFederate

PingFederate는 인증 공급자로 완전히 구성하거나 필요한 경우 다른 여러 업스트림 IdP에 대한 프록시로 완전히 구성할 수 있는 엔터프라이즈 ID 브리지입니다. 다음 다이어그램에서 해당 기능을 확인할 수 있습니다.

![PingFederate 구현을 보여 주는 이미지](./media/partner-ping/pingfederate.png)

이 기능은 컨텍스트/동적 또는 선언적으로 인바운드 요청을 특정 Azure AD B2C 정책으로 전환하는 데 사용할 수 있습니다. 다음은 이 구성에 대한 프로토콜 시퀀스 흐름을 묘사한 것입니다.

![PingAccess 및 PingFederate 워크플로를 보여 주는 이미지](./media/partner-ping/pingaccess-pingfederate-workflow.png)

## <a name="prerequisites"></a>사전 요구 사항

시작하려면 다음이 필요합니다.

- Azure 구독 계정이 없으면 [체험 계정](https://azure.microsoft.com/free/)을 얻습니다.

- [Azure AD B2C 테넌트](./tutorial-create-tenant.md)를 Azure 구독에 연결

- Docker 컨테이너 또는 Azure VM에서 직접 배포된 PingAccess 및 PingFederate

## <a name="connectivity"></a>연결

다음이 연결되어 있는지 확인합니다.

- **PingAccess 서버** – Azure AD B2C 서비스 및 PingFederate 서버에 게시된 잘 알려진 PingFederate 서버, 클라이언트 브라우저, OIDC, OAuth 및 키 검색과 통신할 수 있습니다.

- **PingFederate서버** - Azure AD B2C 서비스에 게시된 잘 알려진 PingAccess 서버, 클라이언트 브라우저, OIDC, OAuth 및 키 검색과 통신할 수 있습니다.

- **레거시 또는 헤더 기반 AuthN 애플리케이션** - PingAccess 서버 간 통신할 수 있습니다.

- **SAML 신뢰 당사자 애플리케이션** – 클라이언트에서 브라우저 트래픽에 도달할 수 있습니다. Azure AD B2C 서비스에서 게시한 SAML 페더레이션 메타데이터에 액세스할 수 있습니다.

- **최신 애플리케이션** – 클라이언트에서 브라우저 트래픽에 도달할 수 있습니다. Azure AD B2C 서비스에 게시된 잘 알려진 OIDC, OAuth 및 키 검색에 액세스할 수 있습니다.

- **REST API** – 네이티브 또는 웹 클라이언트에서 트래픽에 도달할 수 있습니다. Azure AD B2C 서비스에 게시된 잘 알려진 OIDC, OAuth 및 키 검색에 액세스할 수 있습니다.

## <a name="configure-azure-ad-b2c"></a>Azure AD B2C 구성하기

이 목적을 위해 기본 사용자 흐름 또는 고급 Identity enterprise framework(IEF) 정책을 사용할 수 있습니다. PingAccess는 [WebFinger](https://tools.ietf.org/html/rfc7033) 기반 검색 규칙을 사용하여 **발급자** 값을 기반으로 메타데이터 엔드포인트를 생성합니다.
이 규칙을 따르려면 사용자 흐름의 정책 속성을 사용하여 Azure AD B2C 발급자 업데이트를 실행합니다.

![토큰 설정을 보여 주는 이미지](./media/partner-ping/token-setting.png)

고급 정책에서는 [JWT 토큰 발급자 기술 프로필](./jwt-issuer-technical-profile.md)의 **IssuanceClaimPattern** 메타데이터 요소를 **AuthorityWithTfp** 값으로 사용하여 이를 구성할 수 있습니다.

## <a name="configure-pingaccesspingfederate"></a>PingAccess/PingFederate 구성

다음 섹션에서는 필수 구성에 대해 설명합니다.
이 다이어그램에서 통합에 대한 전반적인 사용자 흐름을 볼 수 있습니다.

![PingAccess 및 PingFederate 통합을 보여 주는 이미지](./media/partner-ping/pingaccess.png)

### <a name="configure-pingfederate-as-the-token-provider"></a>토큰 공급자로서 PingFederate 구성하기

PingAccess에 대해 토큰 공급자로 PingFederate를 구성하려면 PingFederate에서 PingAccess로 연결되고 PingAccess에서 PingFederate로 연결이 설정되었는지 확인합니다.  
구성 단계는 [이 문서](https://docs.pingidentity.com/bundle/pingaccess-61/page/zgh1581446287067.html)를 참조하세요.

### <a name="configure-a-pingaccess-application-for-header-based-authentication"></a>헤더 기반 인증에 대한 PingAccess 애플리케이션 구성

헤더 기반 인증을 위해 대상 웹 애플리케이션에 대한 PingAccess 애플리케이션을 만들어야 합니다. 다음 단계를 수행합니다.

#### <a name="step-1--create-a-virtual-host"></a>1단계 - 가상 호스트 만들기

>[!IMPORTANT]
>이 솔루션을 구성하려면 모든 애플리케이션에 대해 가상 호스트를 만들어야 합니다. 구성 고려 사항 및 해당 영향에 대한 자세한 내용은 [주요 고려 사항](https://docs.pingidentity.com/bundle/pingaccess-43/page/reference/pa_c_KeyConsiderations.html)을 참조하세요.

다음 단계에 따라 가상 호스트를 만듭니다.

1. **설정** > **액세스** > **가상 호스트** 로 이동합니다.

2. **가상 호스트 추가** 선택하기

3. 호스트 필드에 애플리케이션 URL의 FQDN 부분을 입력합니다.

4. 포트 필드에 **443** 을 입력합니다.

5. **저장** 을 선택합니다.

#### <a name="step-2--create-a-web-session"></a>2단계 - 웹 세션 만들기

다음 단계에 따라 웹 세션을 만듭니다.

1. **설정** > **액세스** > **웹 세션** 으로 이동합니다.

2. **웹 세션 추가** 를 선택합니다.

3. 웹 세션의 **이름을** 제공합니다.

4. **쿠키 형식**, **서명된 JWT** 또는 **암호화된 JWT** 를 선택합니다.

5. **대상** 에 고유 값을 제공합니다.

6. **클라이언트 ID** 필드에 **Azure AD 애플리케이션 ID** 를 입력합니다.

7. Azure AD에서 애플리케이션에 대해 생성한 **키** 를 **클라이언트 암호** 필드에 입력합니다.

8. 선택 사항 - Microsoft Graph API를 사용하여 사용자 지정 클레임을 만들고 사용할 수 있습니다. 그렇게 하도록 선택하는 경우 **고급** 을 선택하고 **요청 프로필** 및 **사용자 특성 새로 고침** 옵션을 선택 취소합니다. 사용자 지정 클레임을 사용하는 방법에 대한 자세한 내용은 [사용자 지정 클레임 사용](../active-directory/app-proxy/application-proxy-configure-single-sign-on-with-headers.md)을 참조하세요.

9. **저장** 을 선택합니다.

#### <a name="step-3--create-identity-mapping"></a>3단계 - ID 매핑 만들기

>[!NOTE]
>둘 이상의 애플리케이션이 헤더에 동일한 데이터를 필요로 하는 경우 두 개 이상의 애플리케이션으로 ID 매핑을 사용할 수 있습니다.

ID 매핑을 만들려면 다음 단계를 수행합니다.

1. **설정** > **액세스** > **ID 매핑** 으로 이동하기

2. **ID 매핑 추가** 선택하기

3. **이름** 지정하기

4. ID 매핑을 **헤더 ID 매핑 형식** 으로 선택하기

5. **특성 매핑** 테이블에서 필요한 매핑 지정하기. 예를 들면 다음과 같습니다.

   특성 이름 | 헤더 이름 |
   |---|---|
   | 'upn' | x-userprincipalname |
   | 'email' | x-email |
   | 'oid' | x-oid |
   | 'scp' | x-scope |
   | 'amr' | x-amr |

6. **저장** 을 선택합니다.

#### <a name="step-4--create-a-site"></a>4단계 - 사이트 만들기

>[!NOTE]
>일부 구성에서는 사이트에 둘 이상의 애플리케이션이 포함될 수 있습니다. 사이트는 둘 이상의 애플리케이션과 함께 사용할 수 있습니다(해당하는 경우).

사이트를 만들려면 다음 단계를 따릅니다.

1. **기본** > **사이트** 로 이동하기

2. **사이트 추가** 선택하기

3. 사이트의 **이름** 지정하기

4. 사이트 **대상** 입력하기. 대상은 애플리케이션을 호스트하는 서버에 대한 hostname:port 쌍입니다. 이 필드에 애플리케이션의 경로를 입력하지 마세요. 예를 들어 https://mysite:9999/AppName 에서 애플리케이션은 대상 값 mysite:9999를 갖습니다.

5. 대상에 보안 연결이 필요한지 여부를 나타냅니다.

6. 대상에 보안 연결이 필요하면 신뢰할 수 있는 인증서 그룹을 **Trust Any** 로 설정합니다.

7. **저장** 을 선택합니다.

#### <a name="step-5--create-an-application"></a>5단계 - 애플리케이션 만들기

보호하려는 Azure의 각 애플리케이션에 대해 PingAccess에서 애플리케이션을 만들려면 다음 단계를 수행합니다.

1. **기본** > **애플리케이션** 으로 이동하기

2. **애플리케이션 추가** 선택하기

3. 애플리케이션의 **이름** 지정하기

4. 필요에 따라 애플리케이션에 대한 **설명** 입력하기

5. 애플리케이션에 대한 **컨텍스트 루트** 지정하기. 예를 들어 https://mysite:9999/AppName 의 애플리케이션은 /AppName.을 컨텍스트 루트로 가집니다. 컨텍스트 루트는 슬래시(/)로 시작해야 하고, 슬래시(/)로 끝나지 않아야 하며, 계층이 둘 이상일 수 있습니다(예: /Apps/MyApp).

6. 생성된 **가상 호스트** 선택하기

   >[!NOTE]
   >가상 호스트와 컨텍스트 루트의 조합은 PingAccess에서 고유해야 합니다.

7. 생성된 **웹 세션** 선택하기

8. 애플리케이션을 포함한 **사이트** 선택하기

9. 생성된 **ID 매핑** 선택하기

10. 저장 시 사이트를 사용하도록 설정하기 위해 **사용** 선택하기

11. **저장** 을 선택합니다.

### <a name="configure-the-pingfederate-authentication-policy"></a>PingFederate 인증 정책 구성하기

PingFederate 인증 정책을 구성하여 Azure AD B2C 테넌트에서 제공하는 여러 IdP에 페더레이션합니다.

1. IdP와 SP 간에 특성을 연결하는 계약을 만듭니다. 자세한 내용은 [페더레이션 허브 및 인증 정책 계약](https://docs.pingidentity.com/bundle/pingfederate-101/page/ope1564002971971.html)을 참조하세요. SP가 각 IdP와 다른 특성 집합을 요구하지 않는 한 계약은 하나만 필요할 가능성이 높습니다.

2. 각 IdP에 대해 IdP와 페더레이션 허브(SP) PingFederate 간에 IdP 연결을 만듭니다.

3. **대상 세션 매핑** 창에서 적용 가능한 인증 정책 계약을 IdP 연결에 추가합니다.

4. **선택기** 창에서 인증 선택기를 구성합니다. 예를 들어 각 IdP을 인증 정책의 해당 IdP 연결에 매핑하려면 **Identifier First Adapter** 의 인스턴스를 참조하세요.

5. 페더레이션 허브(IdP) PingFederate와 SP 간에 SP 연결을 만듭니다.

6. **인증 원본 매핑** 창의 SP 연결에 해당하는 인증 정책 계약을 추가합니다.

7. 각 IdP를 사용하여 페더레이션 허브(SP) PingFederate에 연결합니다.

8. SP를 사용하여 페더레이션 허브(IdP) PingFederate에 연결합니다.

## <a name="next-steps"></a>다음 단계

자세한 내용은 다음 문서를 참조하세요.

- [Azure AD B2C의 사용자 지정 정책](./custom-policy-overview.md)

- [Azure AD B2C의 사용자 지정 정책 시작하기](tutorial-create-user-flows.md?pivots=b2c-custom-policy)
