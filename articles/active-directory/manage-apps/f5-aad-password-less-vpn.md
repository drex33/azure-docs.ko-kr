---
title: F5 VPN을 통한 안전한 하이브리드 액세스
titleSuffix: Azure AD
description: 암호 없는 VPN용 F5 BIG-IP와 Azure Active Directory SSO(Single Sign-On) 통합에 대한 자습서
services: active-directory
author: davidmu1
manager: martinco
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.date: 10/12/2020
ms.author: davidmu
ms.collection: M365-identity-device-management
ms.reviewer: miccohen
ms.openlocfilehash: 0af9ea42dbe47ab9c0e62ad13e055164c3ec2464
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131058198"
---
# <a name="integrate-f5-big-ip-for-password-less-vpn-with-azure-active-directory"></a>Azure Active Directory와 암호 없는 VPN용 F5 BIG-IP 통합

이 자습서에서는 F5의 BIG-IP 기반 SSL-VPN(Secure Socket Layer 가상 사설망) 솔루션을 SHA(Secure Hybrid Access)용 AD(Azure Active Directory)와 통합하는 방법에 대해 알아봅니다.

BIG-IP SSL-VPN을 Azure AD와 통합하면 다음을 비롯한 [많은 주요 혜택](f5-aad-integration.md)이 제공됩니다.

- [Azure AD 사전 인증 및 권한 부여](../../app-service/overview-authentication-authorization.md)를 통해 제로 트러스트 거버넌스 개선

- [암호 없는 VPN 서비스 인증](https://www.microsoft.com/security/business/identity/passwordless)

- 단일 컨트롤 플레인에서 ID 및 액세스 관리 - [Azure Portal](https://portal.azure.com/#home)

이러한 큰 장점에도 불구하고, 클래식 VPN은 내부에서는 신뢰할 수 있고 외부에서는 신뢰할 수 없는 네트워크 경계의 개념에 여전히 기반을 두고 있습니다. 회사 자산은 더 이상 엔터프라이즈 데이터 센터 자체로 제한되지 않고 고정된 경계가 없는 다중 클라우드 환경에 걸쳐 있기 때문에, 이 모델은 진정한 제로 트러스트 태세를 달성하는 데 더 이상 효과적이지 않습니다. 이러한 이유로 [애플리케이션별로 액세스](../fundamentals/five-steps-to-full-application-integration-with-azure-ad.md)를 관리할 때 ID 기반 접근 방식으로의 전환을 고려할 것을 고객에게 권장합니다.

## <a name="scenario-description"></a>시나리오 설명

이 시나리오에서 SSL-VPN 서비스의 BIG-IP APM 인스턴스는 SAML SP(서비스 공급자)로 구성되고 Azure AD는 신뢰할 수 있는 SAML IDP가 되어 사전 인증을 제공합니다. Azure AD의 SSO(Single Sign-On)는 클레임 기반 인증을 통해 BIG-IP APM에 제공되어 원활한 VPN 액세스 환경을 제공합니다.

![ssl-vpn 아키텍처를 표시하는 이미지](media/f5-sso-vpn/ssl-vpn-architecture.png)

>[!NOTE]
>이 가이드 전체에서 참조되는 모든 예시 문자열 또는 값은 실제 환경의 문자열로 대체해야 합니다.

## <a name="prerequisites"></a>사전 요구 사항

F5 BIG-IP에 대한 이전 경험이나 지식은 필요하지 않지만 다음 사항이 필요합니다.

- Azure AD [무료 구독](https://azure.microsoft.com/trial/get-started-active-directory/) 이상

- 사용자 ID는 [온-프레미스 디렉터리](../hybrid/how-to-connect-sync-whatis.md)에서 Azure AD로 동기화되어야 합니다.

- Azure AD 애플리케이션 관리자 [사용 권한](../roles/permissions-reference.md#application-administrator)이 있는 계정

- BIG-IP에 대한 클라이언트 트래픽을 라우팅을 주고받는 기존 BIG-IP 인프라 또는 [Azure에 BIG-IP 가상 버전을 배포](f5-bigip-deployment-guide.md)합니다.

- 테스트하는 동안 BIG-IP 게시 VPN 서비스에 대한 레코드는 공용 DNS 또는 테스트 클라이언트의 localhost 파일에 있어야 합니다.

- HTTPS를 통해 서비스를 게시하는 데 필요한 SSL 인증서를 사용하여 BIG-IP를 프로비전해야 합니다.

[F5 BIG-IP 용어](https://www.f5.com/services/resources/glossary)에 익숙해진다면 이 자습서에서 참조되는 다양한 구성 요소를 이해하는 데도 도움이 됩니다.

>[!NOTE]
>Azure는 지속적으로 진화하고 있습니다. 이 가이드의 지침과 Azure Portal에 표시되는 내용 사이에 미묘한 차이가 있어도 걱정할 필요가 없습니다. 스크린샷은 BIG-IP v15에서 가져온 것이지만 v13.1와 상대적으로 유사합니다.

## <a name="add-f5-big-ip-from-the-azure-ad-gallery"></a>Azure AD 갤러리에서 F5 BIG-IP 추가

BIG-IP 간에 SAML 페더레이션 트러스트를 설정하면 게시된 VPN 서비스에 대한 액세스 권한을 부여하기 전에 Azure AD BIG-IP에서 Azure AD에 대한 사전 인증 및 [조건부 액세스](../conditional-access/overview.md)를 전달할 수 있습니다.

1. 애플리케이션 관리자 권한이 있는 계정을 사용하여 Azure AD 포털에 로그인합니다.

2. 왼쪽 탐색 창에서 **Azure Active Directory 서비스** 를 선택합니다.

3. **엔터프라이즈 애플리케이션** 으로 이동하고 위쪽 리본에서 **새 애플리케이션** 을 선택합니다.

4. 갤러리에서 F5를 검색하고 **F5 BIP-IP APM Azure AD 통합** 을 선택합니다.

5. 애플리케이션의 이름을 입력한 다음 **추가/만들기** 를 통해 사용자의 테넌트에 추가합니다. 사용자는 Azure 및 Office 365 애플리케이션 포털에서 이름을 아이콘으로 볼 수 있습니다. 이 이름은 VPN과 같은 해당 특정 서비스를 반영해야 합니다.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

1. 새 F5 애플리케이션 속성 보기에서 **Single sign-on** > **관리** 로 이동합니다.

2. **Single Sign-On 방법 선택** 페이지에서 **SAML** 을 선택합니다. Single Sign-On 설정을 저장하라는 메시지가 표시되면 **아니요, 나중에 저장하겠습니다** 를 선택하여 건너뜁니다.

3. **SAML로 Single Sign-On 설정** 메뉴에서 **기본 SAML 구성** 에 대한 펜 아이콘을 선택하여 다음 세부 정보를 제공합니다.

   - 미리 정의된 **식별자 URL** 을 BIG-IP 게시 서비스의 URL로 바꿉니다. 예를 들어 `https://ssl-vpn.contoso.com`

   - SAML 엔드포인트 경로를 포함하여 **회신 URL** 텍스트 상자에 동일한 작업을 수행합니다. 예를 들어 `https://ssl-vpn.contoso.com/saml/sp/profile/post/acs`

   - 이 구성에서만 애플리케이션은 IDP 시작 모드에서 작동합니다. 여기에서 Azure AD는 BIG-IP SAML 서비스로 리디렉션하기 전에 사용자에게 SAML 어설션을 발행합니다. IDP 시작 모드를 지원하지 않는 앱의 경우 BIG-IP SAML 서비스에 대한 **로그온 URL** 을 지정합니다. 예들 들어 `https://ssl-vpn.contoso.com`입니다.

   - 로그아웃 URL의 경우 게시 중인 서비스의 호스트 헤더가 앞에 붙는 BIG-IP APM SLO(단일 로그아웃) 엔드포인트를 입력합니다. 예를 들어 `https://ssl-vpn.contoso.com/saml/sp/profile/redirect/slr`

   SLO URL을 제공하면 사용자가 로그인한 후 양쪽 끝인 BIG-IP 및 Azure AD에서 사용자 세션이 종료됩니다. BIG-IP APM은 특정 애플리케이션 URL을 호출할 때 모든 세션을 종료하는 [옵션](https://support.f5.com/csp/article/K12056) 또한 제공합니다.

![기본 saml 구성을 표시하는 이미지](media/f5-sso-vpn/basic-saml-configuration.png).

>[!NOTE]
>TMOS v16에서 SAML SLO 엔드포인트가 /saml/sp/profile/redirect/slo로 변경되었습니다.

4. SAML 구성 메뉴를 종료하기 전에 **저장** 을 선택하고 SSO 테스트 프롬프트를 건너뜁니다.

Azure AD에서 BIG-IP APM 인증을 위해 사용자에게 발급한 **사용자 특성 및 클레임** 섹션의 속성을 관찰합니다.

![사용자 특성 클레임을 표시하는 이미지](media/f5-sso-vpn/user-attributes-claims.png)

BIG-IP 게시 서비스에서 예상할 수 있는 다른 특정 클레임을 자유롭게 추가할 수 있으며 기본 집합 외에 정의된 모든 클레임은 채워진 특성으로 Azure AD에 있는 경우에만 발급된다는 점에 유의하십시오. 동일한 방식으로 디렉터리 [역할 또는 그룹](../hybrid/how-to-connect-fed-group-claims.md) 멤버 자격은 클레임으로 발급되기 전에 Azure AD의 사용자 개체에 대해 정의해야 합니다.

![페더레이션 메타데이터 다운로드 링크를 표시하는 이미지](media/f5-sso-vpn/saml-signing-certificate.png)

Azure AD에서 만든 SAML 서명 인증서의 수명은 3년이므로 Azure AD 게시 참고 자료를 사용하여 관리해야 합니다.

### <a name="azure-ad-authorization"></a>Azure AD 권한 부여

기본값으로 Azure AD는 서비스에 대한 액세스 권한이 부여된 사용자에게만 토큰을 발급합니다.

1. 애플리케이션의 구성 보기에서 **사용자 및 그룹** 을 선택합니다.

2. **+ 사용자 추가** 를 선택하고 할당 추가 메뉴에서 **사용자 및 그룹** 을 선택합니다.

3. **사용자 및 그룹** 대화 상자에서 VPN에 액세스 권한이 있는 사용자 그룹을 추가한 다음 **선택** > **할당** 을 선택합니다.

![사용자 링크 추가를 표시하는 이미지 ](media/f5-sso-vpn/add-user-link.png)

4. 이는 SAML 페더레이션 트러스트의 Azure AD 부분을 완료합니다. BIG-IP APM은 SAML 사전 인증을 위해 이제 SSL-VPN 서비스를 게시하도록 설정하고 해당 속성 집합으로 구성할 수 있어 트러스트를 완료할 수 있습니다.

## <a name="big-ip-apm-configuration"></a>BIG-IP APM 구성

### <a name="saml-federation"></a>SAML 페더레이션

다음 섹션에서는 Azure AD와 VPN 서비스 페더레이션을 완료하는 데 필요한 BIG-IP SAML 서비스 공급자 및 해당 SAML IDP 개체를 만듭니다.

1. **액세스** > **페더레이션** > **SAML 서비스 공급자** > **로컬 SP 서비스** 로 이동하고 **만들기** 를 선택합니다.

![BIG-IP SAML 구성을 표시하는 이미지](media/f5-sso-vpn/bigip-saml-configuration.png)

2. **이름** 과 이전에 Azure AD에서 정의한 것과 동일한 **엔터티 ID** 값을 입력하고 애플리케이션에 연결하는 데 사용될 Host FQDN을 입력합니다.

![새 SAML SP 서비스 만들기를 표시하는 이미지](media/f5-sso-vpn/create-new-saml-sp.png)

   SP **이름** 설정은 엔터티 ID가 게시된 URL의 호스트 이름 부분과 정확히 일치하지 않거나 일반 호스트 이름 기반 URL 형식이 아닌 경우에만 필요합니다. 엔터티 ID가 `urn:ssl-vpn:contosoonline`인 경우 게시되는 애플리케이션의 외부 스키마 및 호스트 이름을 제공합니다.

3. 아래로 스크롤하여 새 **SAML SP 개체** 를 선택하고 **IDP 커넥터 바인딩/바인딩 해제** 를 선택 합니다.

![로컬 SP 서비스를 통해 페더레이션 만들기를 표시하는 이미지](media/f5-sso-vpn/federation-local-sp-service.png)

4. **새 IDP Connector 만들기** 를 선택하고 드롭다운 메뉴에서 **메타데이터로부터** 를 선택합니다.

![새 IDP 커넥터 만들기를 표시하는 이미지](media/f5-sso-vpn/create-new-idp-connector.png)

5. 이전에 다운로드한 페더레이션 메타데이터 XML 파일을 찾아 외부 SAML IDP를 나타내는 APM 개체의 **ID 공급자 이름** 을 제공 합니다.

6. **새 행 추가** 를 선택하여 새 Azure AD 외부 IDP 커넥터를 선택합니다.

![외부 IDP 커넥터를 표시하는 이미지](media/f5-sso-vpn/external-idp-connector.png)

7. **업데이트** 를 선택하여 SAML SP 개체를 SAML IDP 개체에 바인딩한 다음, **확인** 을 선택합니다.

![SP를 사용하는 SAML IDP를 표시하는 이미지](media/f5-sso-vpn/saml-idp-using-sp.png)

### <a name="webtop-configuration"></a>웹톱 구성

다음 단계를 통해 BIG-IP의 재산적 가치를 가진 웹 포털을 통해 사용자에게 SSL-VPN을 제공하도록 사용할 수 있습니다.

1. **액세스** > **웹톱** > **웹톱 목록** 으로 이동하여 **만들기** 를 선택합니다.

2. 포털에 이름을 지정하고 형식을 **전체** 로 설정합니다. 예들 들어 `Contoso_webtop`입니다.

3. 나머지 기본 설정을 조정한 후 **완료** 를 선택합니다.

![웹톱 구성을 표시하는 이미지](media/f5-sso-vpn/webtop-configuration.png)

### <a name="vpn-configuration"></a>VPN 구성

VPN 기능은 여러 요소로 구성되며, 각 요소는 전체 서비스의 서로 다른 측면을 제어합니다.

1. **액세스** > **연결/VPN** > **네트워크 액세스(VPN)**  > **IPV4 임대 풀** 로 이동하여 **만들기** 를 선택합니다.

2. VPN 클라이언트에 할당되는 IP 주소 풀의 이름을 제공합니다. For example, Contoso_vpn_pool

3. 형식을 **IP 주소 범위** 로 설정하고 시작 및 끝 IP를 제공한 후, **추가** 및 **완료** 를 클릭합니다.

![vpn 구성을 표시하는 이미지](media/f5-sso-vpn/vpn-configuration.png)

네트워크 액세스 목록은 VPN 풀의 IP 및 DNS 설정, 사용자 라우팅 사용 권한으로 서비스를 프로비전하고 필요한 경우 애플리케이션을 시작할 수도 있습니다.

1. **액세스** > **연결/VPN: 네트워크 액세스(VPN)**  > **네트워크 액세스 목록** 으로 이동하여 **만들기** 를 선택합니다.

2. VPN 액세스 목록 및 캡션의 이름(예: Contoso-VPN)을 제공하고 **완료** 를 클릭합니다.

![네트워크 액세스 목록에서 vpn 구성을 표시하는 이미지](media/f5-sso-vpn/vpn-configuration-network-access-list.png)

3. 위쪽 리본에서 **네트워크 설정** 을 선택하고 아래 설정을 추가합니다.

   • **지원되는 IP 버전**: IPV4

   • **IPV4 임대 풀**: 이전에 만든 VPN 풀(예: Contoso_vpn_pool)을 선택합니다

![contoso vpn 풀을 표시하는 이미지](media/f5-sso-vpn/contoso-vpn-pool.png)

   클라이언트 설정 옵션을 사용하여 VPN을 설정할 때 클라이언트 트래픽이 라우팅되는 방법에 대한 제한을 적용할 수 있습니다.

4. **완료** 를 선택하고 DNS/호스트 탭으로 이동하여 설정을 추가 합니다.

   • **IPV4 기본 이름 서버**: 사용자 환경의 DNS 서버의 IP입니다.

   • **DNS 기본 도메인 접미사**:이 특정 VPN 연결에 대한 도메인 접미사입니다. 예를 들면 contoso.com입니다.

![기본 도메인 접미사를 표시하는 이미지](media/f5-sso-vpn/domain-suffix.png)

[F5 문서](https://techdocs.f5.com/kb/en-us/products/big-ip_apm/manuals/product/apm-network-access-11-5-0/2.html) 는 기본 설정에 따라 나머지 설정을 조정하는 방법에 대한 세부 정보를 제공합니다.

이제 VPN 서비스가 지원해야 하는 각 VPN 클라이언트 형식에 대해 설정을 구성하려면 BIG-IP 연결 프로필이 필요합니다. 예를 들어 Windows, OSX 및 Android 등입니다.

1. **액세스** > **연결/VPN** > **연결** > **프로필** 로 이동하여 **추가** 를 선택합니다.

2. 프로필 이름을 입력하고 상위 프로필을 **/Common/connectivity** 로 설정합니다(예: Contoso_VPN_Profile).

![새 연결 프로필 만들기를 표시하는 이미지](media/f5-sso-vpn/create-connectivity-profile.png)

F5 [설명서](https://techdocs.f5.com/kb/en-us/bigip-edge-apps.html)에서는 클라이언트 지원에 대한 세부 정보를 제공 합니다.

## <a name="access-profile-configuration"></a>액세스 프로필 구성

VPN 개체가 구성된 상태에서 SAML 인증에 서비스를 사용 하도록 설정하려면 액세스 정책이 필요합니다.

1. **액세스** > **프로필/정책** > **액세스 프로필(세션별 정책)** 로 이동하고 **만들기** 를 선택합니다.

2. 프로필 이름을 입력하고 프로필 유형에 대해 **모두** 를 선택 합니다(예: Contoso_network_access).

3. 아래로 스크롤하여 **허용된 언어** 목록에 언어를 하나 이상 추가하고 **완료** 를 선택합니다.

![일반 속성을 표시하는 이미지](media/f5-sso-vpn/general-properties.png)

4. 새 액세스 프로필의 세션별 정책 필드에서 **편집** 을 선택하여 별도의 브라우저 탭에서 시각적 정책 편집기를 시작합니다.

![세션별 정책을 표시하는 이미지](media/f5-sso-vpn/per-session-policy.png)

5. **+** 기호를 선택하고 팝업에서 **인증** > **SAML 인증** > **항목 추가** 를 선택합니다.

6. SAML 인증 SP 구성에서 이전에 만든 VPN SAML SP 개체와 **저장** 을 선택합니다.

![SAML 인증을 표시하는 이미지](media/f5-sso-vpn/saml-authentication.png)

7. SAML 인증의 성공 분기에 대해 **+** 를 선택합니다.

8. 할당 탭에서 **고급 리소스 할당** 과 **항목 추가** 를 뒤이어 선택합니다.

![고급 리소스 할당을 표시하는 이미지](media/f5-sso-vpn/advance-resource-assign.png)

9. 팝업에서 **새 항목** 을 선택한 다음, **추가/삭제** 를 선택합니다.

10. 하위 창에서 **네트워크 액세스** 를 선택한 다음, 이전에 만든 네트워크 액세스 프로필을 선택합니다.

![새 네트워크 액세스 항목 추가를 표시하는 이미지](media/f5-sso-vpn/add-new-entry.png)

11. **웹톱** 탭으로 전환하고 이전에 만든 웹톱 개체를 추가합니다.

![웹톱 개체 추가를 표시하는 이미지](media/f5-sso-vpn/add-webtop-object.png)

12. **업데이트** 를 선택하고 뒤이어 **저장** 을 선택합니다.

13. 위쪽 거부 상자에서 링크를 선택하여 성공 분기를 **허용** 으로 변경한 다음, **저장** 으로 변경합니다.

![새 시각적 정책 편집기를 표시하는 이미지](media/f5-sso-vpn/vizual-policy-editor.png)

14. **액세스 정책 적용** 을 선택하여 해당 설정을 커밋하고 시각적 정책 편집기 탭을 닫습니다.

![새 액세스 정책 관리자를 표시하는 이미지](media/f5-sso-vpn/access-policy-manager.png)

## <a name="publish-the-vpn-service"></a>VPN 서비스 게시

모든 설정이 준비되면 이제 APM은 VPN에 연결하는 클라이언트를 수신 대기할 프런트 엔드 가상 서버가 필요합니다.

1. **로컬 트래픽** > **가상 서버** > **가상 서버 목록** 을 선택하고 **만들기** 를 선택합니다.

2. VPN 가상 서버의 **이름(예:**VPN_Listener**)** 을 제공합니다.

3. 클라이언트 트래픽을 수신하기 위한 라우팅이 있는 사용되지 않는 **IP 대상 주소** 를 가상 서버에 제공합니다.

4. 서비스 포트를 **443 HTTPS** 로 설정하고 상태가 **사용** 으로 표시되는지 확인합니다.

![새 가상 서버를 표시하는 이미지](media/f5-sso-vpn/new-virtual-server.png)

5. **HTTP 프로필** 을 http로 설정하고 전제 조건의 일부로 프로비전한 공용 SSL 인증서에 대 한 SSL 프로필(클라이언트)을 추가합니다.

![SSL 프로필을 표시하는 이미지](media/f5-sso-vpn/ssl-profile.png)

6. 액세스 정책에서 **액세스 프로필** 및 **연결 프로필** 을 만들어진 VPN 개체를 사용하도록 설정합니다.

![액세스 정책을 표시하는 이미지](media/f5-sso-vpn/access-policy.png)

7. 완료되면 **완료** 를 선택합니다.

8.  SSL-VPN 서비스는 이제 SHA를 통해 게시되고 액세스할 수 있습니다. 해당 URL을 직접 통하거나 Microsoft의 애플리케이션 포털을 통합니다.

## <a name="additional-resources"></a>추가 리소스

- [암호 끝, 암호 없이 이동](https://www.microsoft.com/security/business/identity/passwordless)

- [조건부 액세스란?](../conditional-access/overview.md)

- [Microsoft 제로 트러스트 프레임워크를 사용하여 원격 작업 지원](https://www.microsoft.com/security/blog/2020/04/02/announcing-microsoft-zero-trust-assessment-tool/)

- [Azure AD와 전체 애플리케이션 통합에 대한 5단계](../fundamentals/five-steps-to-full-application-integration-with-azure-ad.md)

## <a name="next-steps"></a>다음 단계

원격 Windows 클라이언트에서 브라우저를 열고  **BIG-IP VPN 서비스** 의 URL를 찾아봅니다. Azure AD에 인증한 후에는 BIG-IP 웹톱 포털 및 VPN 시작 관리자가 표시됩니다.

![Vpn 시작 관리자를 표시하는 이미지](media/f5-sso-vpn/vpn-launcher.png)

VPN 타일을 선택하면 BIG-IP Edge 클라이언트를 설치하고 SHA에 대해 구성된 VPN 연결을 설정합니다.
또한 F5 VPN 애플리케이션은 Azure AD 조건부 액세스에서 대상 리소스로 표시되어야 합니다. 조건부 액세스 정책을 빌드하고 사용자가 Azure AD [암호 없는 인증](https://www.microsoft.com/security/business/identity/passwordless)을 사용하도록 설정하는 방법은 [참고 자료](../conditional-access/concept-conditional-access-policies.md)를 확인하세요.
