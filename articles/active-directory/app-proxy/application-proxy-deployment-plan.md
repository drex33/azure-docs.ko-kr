---
title: Azure Active Directory 애플리케이션 프록시 배포 계획
description: 조직 내 애플리케이션 프록시 배포를 계획하기 위한 엔드투엔드 가이드
services: active-directory
author: kenwith
manager: karenh444
ms.service: active-directory
ms.subservice: app-proxy
ms.workload: identity
ms.topic: conceptual
ms.date: 04/27/2021
ms.author: kenwith
ms.openlocfilehash: 0779297a60d3dc6561e5df461e63b432afd219bf
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2021
ms.locfileid: "129989554"
---
# <a name="plan-an-azure-ad-application-proxy-deployment"></a>Azure AD 애플리케이션 프록시 배포 계획

Azure AD(Azure Active Directory) 애플리케이션 프록시는 온-프레미스 애플리케이션의 비용 효율적인 보안 원격 액세스 솔루션입니다. “클라우드 우선” 조직이 아직 최신 프로토콜을 사용할 수 없는 기존 온-프레미스 애플리케이션에 대한 액세스를 관리할 수 있는 즉각적인 전환 경로를 제공합니다. 추가 소개 정보는 [애플리케이션 프록시 정의](./application-proxy.md)를 참조하세요.

애플리케이션 프록시는 원격 사용자에게 내부 리소스에 대한 액세스를 제공하기 위해 권장됩니다. 애플리케이션 프록시는 해당 원격 액세스 사용 사례에 필요한 VPN 또는 역방향 프록시를 대체합니다. 회사 네트워크에 있는 사용자를 위한 것은 아닙니다. 인트라넷 액세스를 위해 애플리케이션 프록시를 사용하는 이러한 사용자에게 원하지 않는 성능 문제가 발생할 수 있습니다.

이 문서에는 Azure AD 애플리케이션 프록시를 계획, 운영 및 관리하는 데 필요한 리소스가 포함되어 있습니다.

## <a name="plan-your-implementation"></a>구현 계획

다음 섹션에서는 효율적인 배포 환경을 설정하는 주요 계획 요소에 대한 광범위한 보기를 제공합니다.

### <a name="prerequisites"></a>사전 요구 사항

구현을 시작하기 전에 다음 필수 구성 요소를 충족해야 합니다. 이 필수 구성 요소를 포함하여 환경을 설정하는 방법에 관한 자세한 내용은 이 [자습서](application-proxy-add-on-premises-application.md)에서 확인할 수 있습니다.

* **커넥터**: 커넥터는 배포할 수 있는 경량 에이전트입니다.
   * 온-프레미스 물리적 하드웨어
   * 하이퍼바이저 솔루션에서 호스트하는 VM
   * 애플리케이션 프록시 서비스에 대한 아웃바운드 연결을 사용하기 위해 Azure에서 호스트하는 VM입니다.

* 자세한 개요는 [Azure AD 앱 프록시 커넥터 이해](application-proxy-connectors.md)를 참조하세요.

     * 커넥터를 설치하기 전에 커넥터 컴퓨터에서 [TLS 1.2를 사용하도록 설정](application-proxy-add-on-premises-application.md)해야 합니다.

     * 가능하면 백 엔드 웹 애플리케이션 서버와 [같은 네트워크](application-proxy-network-topology.md) 및 세그먼트에 커넥터를 배포합니다. 애플리케이션 검색을 완료한 다음 커넥터를 배포하는 것이 가장 좋습니다.
     * 각 커넥터 그룹에는 고가용성 및 스케일을 제공하기 위해 두 개 이상의 커넥터가 있는 것이 좋습니다. 언제든 컴퓨터를 수리해야 할 경우에 대비해 세 개의 커넥터를 사용하는 것이 가장 좋습니다. [커넥터 용량 표](./application-proxy-connectors.md#capacity-planning)를 검토하면 커넥터를 설치할 컴퓨터 형식을 결정하는 데 유용합니다. 컴퓨터 크기가 클수록 버퍼가 증가하고 커넥터의 성능이 향상됩니다.

* **네트워크 액세스 설정**: Azure AD 애플리케이션 프록시 커넥터는 [HTTPS(TCP 포트 443) 및 HTTP(TCP 포트 80)를 통해 Azure에 연결](application-proxy-add-on-premises-application.md)합니다.

   * 커넥터 TLS 트래픽을 종료하는 것은 지원되지 않으며 커넥터는 각각의 Azure App Proxy 엔드포인트를 사용하여 보안 채널을 설정할 수 없습니다.

   * 커넥터와 Azure 간의 아웃바운드 TLS 통신에 대한 모든 형태의 인라인 검사를 방지합니다. 커넥터와 백 엔드 애플리케이션 간의 내부 검사는 가능하지만, 사용자 환경이 저하될 수 있으므로 권장하지 않습니다.

   * 커넥터 자체의 부하 분산도 지원되지 않거나 필요하지도 않습니다.

### <a name="important-considerations-before-configuring-azure-ad-application-proxy"></a>Azure AD 애플리케이션 프록시를 구성하기 전에 중요한 고려 사항

Azure AD 애플리케이션 프록시를 구성하고 구현하려면 다음 핵심 요구 사항을 충족해야 합니다.

*  **Azure 온보딩**: 애플리케이션 프록시를 배포하기 전에 사용자 ID는 온-프레미스 디렉터리에서 동기화되거나 Azure AD 테넌트 내에서 직접 생성되어야 합니다. ID 동기화를 통해 Azure AD가 사용자에게 App Proxy 게시 애플리케이션에 대한 액세스 권한을 부여하기 전에 미리 인증하고 SSO(Single Sign-On)를 수행하는 데 필요한 사용자 식별자 정보를 가질 수 있습니다.

* **조건부 액세스 요구 사항**: 사용자에게 영향을 미치는 대기 시간이 추가되므로 인트라넷 액세스에는 애플리케이션 프록시를 사용하지 않는 것이 좋습니다. 인터넷에서 원격 액세스를 위해 사전 인증 및 조건부 액세스 정책과 함께 애플리케이션 프록시를 사용하는 것이 좋습니다.  인트라넷 사용을 위한 조건부 액세스를 제공하는 접근 방식은 AAD로 직접 인증할 수 있도록 애플리케이션을 현대화하는 것입니다. 자세한 내용은 [애플리케이션을 AAD로 마이그레이션하기 위한 리소스](../manage-apps/migration-resources.md)를 참조하세요.

* **서비스 한도**: 개별 테넌트가 리소스를 과도하게 사용하지 않도록 보호하기 위해 애플리케이션과 테넌트당 설정된 조절 한도가 있습니다. 해당 한도를 확인하려면 [Azure AD 서비스 한도 및 제한 사항](../enterprise-users/directory-service-limits-restrictions.md)을 참조하세요. 이 조절 한도는 일반적인 사용량을 훨씬 초과하는 벤치마크를 기반으로 하며 대부분의 배포에 충분한 버퍼를 제공합니다.

* **퍼블릭 인증서**: 사용자 지정 도메인 이름을 사용하는 경우 TLS/SSL 인증서를 사용해야 합니다. 조직의 요구 사항에 따라 인증서를 받는 데 다소 시간이 걸릴 수 있으며 최대한 빨리 프로세스를 시작하는 것이 좋습니다. Azure 애플리케이션 프록시는 표준, [와일드카드](application-proxy-wildcard.md) 또는 SAN 기반 인증서를 지원합니다. 자세한 내용은 [Azure AD 애플리케이션 프록시를 사용하여 사용자 지정 도메인 구성](application-proxy-configure-custom-domain.md)을 참조하세요.

* **도메인 요구 사항**: KCD(Kerberos 제한된 위임)를 사용하여 게시된 애플리케이션에 Single Sign-On하려면 커넥터를 실행하는 서버와 앱을 실행하는 서버가 도메인에 조인되어 있고 같은 도메인 또는 신뢰할 수 있는 도메인의 일부여야 합니다.
토픽에 관한 자세한 내용은 애플리케이션 프록시를 사용하는 [Single Sign-On용 KCD](application-proxy-configure-single-sign-on-with-kcd.md)를 참조하세요. 커넥터 서비스는 로컬 시스템의 컨텍스트에서 실행되며 사용자 지정 ID를 사용하도록 구성되지 않아야 합니다.

* **URL의 DNS 레코드**

   * 애플리케이션 프록시에서 사용자 지정 도메인을 사용하기 전에 클라이언트가 사용자 지정 외부 URL을 미리 정의된 애플리케이션 프록시 주소로 확인할 수 있도록 퍼블릭 DNS에 CNAME 레코드를 만들어야 합니다. 사용자 지정 도메인을 사용하는 애플리케이션의 CNAME 레코드를 만들지 못하면 원격 사용자가 애플리케이션에 연결할 수 없습니다. CNAME 레코드를 추가하는 데 필요한 단계는 DNS 공급자마다 다를 수 있으므로, [Azure Portal을 사용하여 DNS 레코드 및 레코드 세트 관리](../../dns/dns-operations-recordsets-portal.md) 방법을 알아봅니다.

   * 마찬가지로 커넥터 호스트는 게시되는 애플리케이션의 내부 URL을 확인할 수 있어야 합니다.

* **관리 권한 및 역할**

   * **커넥터를 설치** 하려면 설치되는 Windows 서버에 대한 로컬 관리자 권한이 있어야 합니다. 또한 커넥터 인스턴스를 인증하고 Azure AD 테넌트에 등록하려면 최소한 *애플리케이션 관리자* 역할이 필요합니다.

   * **애플리케이션 게시 및 관리** 에는 *애플리케이션 관리자* 역할이 필요합니다. 애플리케이션 관리자는 등록, SSO 설정, 사용자/그룹 할당 및 라이선스, 애플리케이션 프록시 설정 및 동의를 포함하여 디렉터리의 모든 애플리케이션을 관리할 수 있습니다. 조건부 액세스를 관리하는 기능은 부여하지 않습니다. *클라우드 애플리케이션 관리자* 역할에는 애플리케이션 프록시 설정 관리를 허용하지 않는다는 점을 제외하고 애플리케이션 관리자의 모든 기능이 있습니다.

* **라이선스**: 애플리케이션 프록시는 Azure AD Premium 구독을 통해 사용할 수 있습니다. 라이선스 옵션 및 기능의 전체 목록은 [Azure Active Directory 가격 책정 페이지](https://www.microsoft.com/security/business/identity-access-management/azure-ad-pricing)를 참조하세요.

### <a name="application-discovery"></a>애플리케이션 검색

다음 정보를 수집하여 애플리케이션 프록시를 통해 게시되는 모든 범위 내 애플리케이션의 인벤토리를 컴파일합니다.

| Information Type| 수집할 정보 |
|---|---|
| 서비스 종류| 예: SharePoint, SAP, CRM, 사용자 지정 웹 애플리케이션, API |
| 애플리케이션 플랫폼 | 예: Windows IIS, Linux용 Apache, Tomcat, NGINX |
| 도메인 멤버 자격| 웹 서버의 FQDN(정규화된 도메인 이름) |
| 애플리케이션 위치 | 인프라에서 웹 서버 또는 팜이 있는 위치 |
| 내부 액세스 | 내부적으로 애플리케이션에 액세스할 때 사용되는 정확한 URL입니다. <br> 팜에서 사용 중인 부하 분산 형식은 무엇인가요? <br> 애플리케이션이 다른 원본에서 콘텐츠를 가져오는지 여부입니다.<br> 애플리케이션이 WebSocket을 통해 작동하는지 확인합니다. |
| 외부 액세스 | 애플리케이션이 이미 외부에 노출된 공급업체 솔루션입니다. <br> 외부 액세스에 사용할 URL입니다. SharePoint인 경우 [이 참고 자료](/SharePoint/administration/configure-alternate-access-mappings)에 따라 대체 액세스 매핑을 구성해야 합니다. 그렇지 않으면 외부 URL을 정의해야 합니다. |
| 공용 인증서 | 사용자 지정 도메인을 사용하는 경우 해당 주체 이름으로 인증서를 확보합니다. 인증서가 있으면 일련 번호와 인증서를 얻을 수 있는 위치를 기록해 둡니다. |
| 인증 유형| 기본, Windows 통합 인증, 양식 기반, 헤더 기반 및 클레임과 같은 애플리케이션 지원에서 지원하는 인증 형식입니다. <br>애플리케이션이 특정 도메인 계정에서 실행되도록 구성된 경우 서비스 계정의 FQDN(정규화된 도메인 이름)을 기록해 둡니다.<br> SAML 기반인 경우 식별자와 회신 URL입니다. <br> 헤더 기반인 경우 인증 형식을 처리하기 위한 공급업체 솔루션 및 특정 요구 사항입니다. |
| 커넥터 그룹 이름 | 이 백 엔드 애플리케이션에 통로와 SSO를 제공하도록 지정될 커넥터 그룹의 논리적 이름입니다. |
| 사용자/그룹 액세스 | 애플리케이션에 대한 외부 액세스 권한이 부여될 사용자 또는 사용자 그룹입니다. |
| 추가 요구 사항 | 애플리케이션을 게시할 때 고려해야 하는 추가 원격 액세스 또는 보안 요구 사항을 확인합니다. |

이 [애플리케이션 인벤토리 스프레드시트](https://aka.ms/appdiscovery)를 다운로드하여 앱을 인벤토리에 추가할 수 있습니다.

### <a name="define-organizational-requirements"></a>조직 요구 사항 정의

다음은 조직의 비즈니스 요구 사항에 대해 정의해야 하는 영역입니다. 각 영역에는 요구 사항의 예가 포함되어 있습니다.

 **Access**

* 도메인에 조인되었거나 Azure AD에 조인된 디바이스를 사용하는 원격 사용자는 원활한 SSO(Single Sign-On)를 통해 게시된 애플리케이션에 안전하게 액세스할 수 있습니다.

* 승인된 개인 디바이스가 있는 원격 사용자는 MFA에 등록되어 있고 인증 방법으로 휴대폰에 Microsoft Authenticator 앱을 등록한 경우 게시된 애플리케이션에 안전하게 액세스할 수 있습니다.

**거버넌스**

* 관리자는 애플리케이션 프록시를 통해 게시된 애플리케이션에 대한 사용자 할당의 수명 주기를 정의하고 모니터링할 수 있습니다.

**보안**

* 그룹 멤버 자격을 통하거나 개별적으로 애플리케이션에 할당된 사용자만 해당 애플리케이션에 액세스할 수 있습니다.

**성능**

* 내부 네트워크에서 애플리케이션에 액세스하는 것과 비교하여 애플리케이션 성능이 저하되지 않습니다.

**사용자 환경**

* 사용자는 모든 디바이스 플랫폼에서 익숙한 회사 URL을 사용하여 애플리케이션에 액세스하는 방법을 알고 있습니다.

**감사**
* 관리자는 사용자 액세스 작업을 감사할 수 있습니다.


### <a name="best-practices-for-a-pilot"></a>파일럿에 대한 모범 사례

SSO(Single Sign-On)를 사용하여 원격 액세스를 위해 단일 애플리케이션을 완전히 작동시키는 데 필요한 시간과 활동을 결정합니다. 초기 검색, 게시 및 일반 테스트를 고려하는 파일럿을 실행하여 이 작업을 수행합니다. IWA(Windows 통합 인증)용으로 미리 구성된 간단한 IIS 기반 웹 애플리케이션을 사용하면 원격 액세스 및 SSO를 성공적으로 파일럿하는 데 최소한의 활동이 필요하므로 기준을 설정하는 데 도움이 됩니다.

다음 디자인 요소는 프로덕션 테넌트에서 직접 파일럿 구현의 성공을 높여야 합니다.

**커넥터 관리**:

* 커넥터는 애플리케이션에 온-프레미스 통로를 제공하는 데 중요한 역할을 합니다. **기본** 커넥터 그룹을 사용하는 것은 프로덕션으로 이동하기 전에 게시된 애플리케이션을 초기 파일럿 테스트하는 데 적합합니다. 테스트를 완료한 후 프로덕션 커넥터 그룹으로 애플리케이션을 이동할 수 있습니다.

**애플리케이션 관리**:

* 직원은 외부 URL이 익숙하고 적절하다는 것을 기억할 가능성이 높습니다. 미리 정의된 msappproxy.net 또는 onmicrosoft.com 접미사를 사용하여 애플리케이션을 게시하지 마세요. 대신 *intranet.<customers_domain>.com* 과 같은 논리적 호스트 이름이 접두사로 붙은 익숙한 최상위 수준의 확인된 도메인을 제공합니다.

* Azure MyApps 포털에서 시작 아이콘을 숨겨 파일럿 애플리케이션 아이콘 표시를 파일럿 그룹으로 제한합니다. 프로덕션 준비가 되면 같은 사전 프로덕션 테넌트에서 또는 프로덕션 테넌트에서 애플리케이션을 게시하여 해당 대상으로 앱 범위를 지정할 수 있습니다.

**Single Sign-On 설정**: 일부 SSO 설정에는 설정하는 데 시간이 걸릴 수 있는 특정 종속성이 있으므로 종속성을 미리 해결하여 변경 제어 연기를 방지합니다. 여기에는 KCD(Kerberos 제한된 위임)를 사용하여 SSO를 수행하고 시간이 많이 걸리는 기타 작업을 처리하기 위한 도메인 조인 커넥터 호스트가 포함됩니다. 예를 들어 헤더 기반 SSO가 필요한 경우 PING 액세스 인스턴스를 설정합니다.

**커넥터 호스트와 대상 애플리케이션 간 TLS**: 보안이 가장 중요하므로 커넥터 호스트와 대상 애플리케이션 간에 TLS를 항상 사용해야 합니다. 특히 웹 애플리케이션이 FBA(양식 기반 인증)용으로 구성된 경우 사용자 자격 증명이 일반 텍스트로 효과적으로 전송됩니다.

**점진적으로 구현하고 각 단계를 테스트** 합니다.
애플리케이션을 게시한 후 기본 기능 테스트를 수행하여 아래 지침에 따라 모든 사용자와 비즈니스 요구 사항이 충족되는지 확인합니다.

1. 사전 인증을 사용하지 않게 설정한 상태에서 웹 애플리케이션에 대한 일반 액세스를 테스트하고 유효성을 검사합니다.
2. 성공하면 사전 인증을 사용으로 설정하고 사용자와 그룹을 할당합니다. 액세스를 테스트하고 유효성을 검사합니다.
3. 그런 다음 애플리케이션의 SSO 메서드를 추가하고 다시 테스트하여 액세스의 유효성을 검사합니다.
4. 필요에 따라 조건부 액세스 및 MFA 정책을 적용합니다. 액세스를 테스트하고 유효성을 검사합니다.

**문제 해결 도구**: 문제를 해결할 때 항상 커넥터 호스트의 브라우저에서 게시된 애플리케이션에 대한 액세스의 유효성을 검사하여 시작하고 애플리케이션이 예상대로 작동하는지 확인합니다. 설정이 간단할수록 근본 원인을 파악하기가 쉬워지므로, 단일 커넥터만 사용하고 SSO를 사용하지 않는 등 최소한의 구성으로 문제를 재현해 보세요. 경우에 따라 Telerik의 Fiddler와 같은 웹 디버깅 도구는 프록시를 통해 액세스되는 애플리케이션의 액세스 또는 콘텐츠 문제를 해결하는 데 필수일 수 있습니다. Fiddler는 iOS 및 Android와 같은 모바일 플랫폼 및 프록시를 통해 라우팅하도록 구성할 수 있는 거의 모든 것에 대한 트래픽을 추적하고 디버그하는 데 도움이 되는 프록시로 작동할 수도 있습니다. 자세한 내용은 [문제 해결 가이드](application-proxy-troubleshoot.md)를 참조하세요.

## <a name="implement-your-solution"></a>솔루션 구현

### <a name="deploy-application-proxy"></a>애플리케이션 프록시 배포

애플리케이션 프록시를 배포하는 단계는 이 [원격 액세스용 온-프레미스 애플리케이션 추가 자습서](application-proxy-add-on-premises-application.md)에서 다룹니다. 설치에 실패하면 포털에서 **애플리케이션 프록시 문제 해결** 을 선택하거나 [애플리케이션 프록시 에이전트 커넥터 설치 문제](application-proxy-connector-installation-problem.md)에 관한 문제 해결 가이드를 사용합니다.

### <a name="publish-applications-via-application-proxy"></a>애플리케이션 프록시를 통해 애플리케이션 게시

애플리케이션 게시는 모든 필수 구성 요소를 충족하고 애플리케이션 프록시 페이지에 등록 및 활성화된 것으로 표시되는 커넥터가 여러 개 있다고 가정합니다.

[PowerShell](/powershell/module/azuread/?view=azureadps-2.0-preview&preserve-view=true)을 사용하여 애플리케이션을 게시할 수도 있습니다.

애플리케이션을 게시할 때 따라야 할 몇 가지 모범 사례는 다음과 같습니다.

* **커넥터 그룹 사용**: 각 애플리케이션을 게시하기 위해 지정된 커넥터 그룹을 할당합니다. 각 커넥터 그룹에는 고가용성 및 스케일을 제공하기 위해 두 개 이상의 커넥터가 있는 것이 좋습니다. 언제든 컴퓨터를 수리해야 할 경우에 대비해 세 개의 커넥터를 사용하는 것이 가장 좋습니다. 또한 커넥터 그룹을 사용하여 네트워크 또는 위치별로 커넥터를 분류하는 방법을 알아보려면 [커넥터 그룹을 사용하여 별도의 네트워크 및 위치에 애플리케이션 게시](application-proxy-connector-groups.md)를 참조하세요.

* **백 엔드 애플리케이션 시간 제한 설정**: 이 설정은 애플리케이션이 클라이언트 트랜잭션을 처리하는 데 75초가 넘게 필요할 수 있는 시나리오에서 유용합니다. 예를 들어 클라이언트가 데이터베이스에 대한 프런트 엔드 역할을 하는 웹 애플리케이션에 쿼리를 보내는 경우입니다. 프런트 엔드는 이 쿼리를 백 엔드 데이터베이스 서버로 보내고 응답에 대기하지만, 응답을 받을 때 대화의 클라이언트 측 시간이 초과됩니다. 시간 제한을 Long으로 설정하면 더 긴 트랜잭션이 완료되도록 180초를 제공합니다.

* **적절한 쿠키 형식 사용**

   * **HTTP 전용 쿠키**: 애플리케이션 프록시의 set-cookie HTTP 응답 헤더에 HTTPOnly 플래그를 포함하여 추가 보안을 제공합니다. 이 설정을 사용하면 XSS(교차 사이트 스크립팅)와 같은 익스플로잇을 완화하는 데 도움이 됩니다. 세션 쿠키에 액세스해야 하는 클라이언트/사용자 에이전트의 경우 이 설정을 그대로 아니요로 둡니다. 예를 들어 RDP/MTSC 클라이언트는 앱 프록시를 통해 게시된 원격 데스크톱 게이트웨이에 연결합니다.

   * **보안 쿠키**: 쿠키가 보안 특성으로 설정되면 사용자 에이전트(클라이언트 측 앱)는 요청이 TLS 보안 채널을 통해 전송되는 경우에만 HTTP 요청에 쿠키를 포함합니다. 따라서 일반 텍스트 채널을 통해 쿠키가 손상될 위험을 줄이는 데 도움이 되므로 사용으로 설정해야 합니다.

   * **영구 쿠키**: 애플리케이션 프록시 세션 쿠키가 만료되거나 삭제될 때까지 유효한 상태를 유지하여 브라우저가 종료될 때까지 지속되도록 합니다. Office와 같은 다양한 애플리케이션이 사용자에게 인증을 요청하지 않고 게시된 웹 애플리케이션 내의 문서에 액세스하는 시나리오에 사용됩니다. 그러나 영구 쿠키는 다른 보정 컨트롤과 함께 사용하지 않으면 궁극적으로 서비스가 무단 액세스의 위험에 처할 수 있으므로 주의하여 사용해야 합니다. 이 설정은 프로세스 간에 쿠키를 공유할 수 없는 이전 애플리케이션에만 사용해야 합니다. 이 설정을 사용하는 대신 프로세스 간 공유 쿠키를 처리하도록 애플리케이션을 업데이트하는 것이 좋습니다.

* **헤더의 URL 변환**: 조직의 퍼블릭 네임스페이스(분할 DNS라고도 함)와 일치하도록 내부 DNS를 구성할 수 없는 시나리오에 사용합니다. 애플리케이션에서 클라이언트 요청에 원본 호스트 헤더를 요구하지 않는 경우 이 값을 예로 설정합니다. 대안은 커넥터가 실제 트래픽의 라우팅을 위해 내부 URL의 FQDN을 사용하고 외부 URL의 FQDN을 호스트 헤더로 사용하는 것입니다. 대부분의 경우 이 대안을 사용하면 원격으로 액세스할 때 애플리케이션이 정상적으로 작동할 수 있지만, 내부 및 외부 URL이 일치하는 이점을 잃을 수 있습니다.

* **애플리케이션 본문의 URL 변환**: 해당 앱의 링크를 클라이언트에 대한 응답으로 변환하려는 경우 앱의 애플리케이션 본문 링크 변환을 설정합니다. 사용으로 설정하면 앱 프록시가 클라이언트에 반환되는 HTML 및 CSS 응답에서 찾은 모든 내부 링크를 변환하는 데 최적인 활동을 제공합니다. 콘텐츠에 하드 코드된 절대 또는 NetBIOS 단축 이름 링크가 포함된 앱이나 다른 온-프레미스 애플리케이션에 연결되는 콘텐츠가 있는 앱을 게시할 때 유용합니다.

게시된 앱이 게시된 다른 앱에 연결되는 시나리오의 경우 앱별 수준에서 사용자 환경을 제어할 수 있도록 애플리케이션마다 링크 변환을 사용으로 설정합니다.

예를 들어, 모두 서로 연결되는 애플리케이션 프록시를 통해 게시된 세 개의 애플리케이션(Benefits, Expenses 및 Travel,)과 애플리케이션 프록시를 통해 게시되지 않은 네 번째 앱인 Feedback이 있다고 가정합니다.

![그림 1](media/App-proxy-deployment-plan/link-translation.png)

Benefits 앱의 링크 변환을 사용으로 설정하면 Expenses 및 Travel의 링크가 해당 앱의 외부 URL로 리디렉션되므로 회사 네트워크 외부에서 애플리케이션에 액세스하는 사용자가 액세스할 수 있습니다. Expenses 및 Travel에서 Benefits로 다시 링크하는 것은 작동하지 않습니다. 이 두 앱에는 링크 변환이 설정되지 않았기 때문입니다. 피드백 링크는 외부 URL이 없기 때문에 리디렉션되지 않기 때문에 Benefits 앱을 사용하는 사용자는 회사 네트워크 외부에서 피드백 앱에 액세스할 수 없습니다. [링크 변환 및 기타 리디렉션 옵션](application-proxy-configure-hard-coded-link-translation.md)에 관한 자세한 내용을 참조하세요.

### <a name="access-your-application"></a>애플리케이션 액세스

앱 프록시 게시 리소스에 대한 액세스를 관리하는 데 사용하는 옵션이 여러 개 있으므로 지정된 시나리오 및 스케일링 성능 요구 사항에 가장 적합한 옵션을 선택합니다. 일반적인 접근 방식에는 Azure AD Connect를 통해 동기화되는 온-프레미스 그룹 사용, 사용자 특성을 기반으로 Azure AD에서 동적 그룹 만들기, 리소스 소유자가 관리하는 셀프 서비스 그룹 사용 또는 이 모든 방식의 조합이 포함됩니다. 각각의 이점은 연결된 리소스를 참조하세요.

애플리케이션에 대한 사용자 액세스 권한을 할당하는 가장 간단한 방법은 게시된 애플리케이션의 왼쪽 창에서 **사용자 및 그룹** 옵션으로 이동하여 그룹 또는 개인을 직접 할당하는 것입니다.

![그림 24](media/App-proxy-deployment-plan/add-user.png)

사용자가 현재 구성원이 아닌 그룹을 할당하고 셀프 서비스 옵션을 구성하여 애플리케이션에 대한 셀프 서비스 액세스를 허용할 수도 있습니다.

![그림 25](media/App-proxy-deployment-plan/allow-access.png)

사용으로 설정된 경우 사용자는 MyApps 포털에 로그인하여 액세스를 요청할 수 있으며, 자동 승인되어 이미 허용된 셀프 서비스 그룹에 추가되거나 지정된 승인자의 승인이 필요합니다.

게스트 사용자를 [Azure AD B2B를 사용하여 애플리케이션 프록시를 통해 게시된 내부 애플리케이션에 액세스하도록 초대](../external-identities/add-users-information-worker.md)할 수도 있습니다.

일반적으로 익명으로 액세스할 수 있고 인증이 필요하지 않은 온-프레미스 애플리케이션의 경우 애플리케이션의 **속성** 에 있는 옵션을 사용하지 않게 설정하는 것이 좋습니다.

![그림 26](media/App-proxy-deployment-plan/assignment-required.png)


이 옵션을 아니요로 설정된 상태로 두면 사용자가 권한 없이 Azure AD 앱 프록시를 통해 온-프레미스 애플리케이션에 액세스할 수 있으므로 주의해서 사용해야 합니다.

애플리케이션이 게시되면 브라우저에 외부 URL을 입력하거나 [https://myapps.microsoft.com](https://myapps.microsoft.com/)에 있는 아이콘으로 액세스할 수 있습니다.

### <a name="enable-pre-authentication"></a>사전 인증 사용

외부 URL을 통해 액세스하는 애플리케이션 프록시를 통해 애플리케이션에 액세스할 수 있는지 확인합니다.

1. **Azure Active Directory** > **Enterprise 애플리케이션** > **모든 애플리케이션** 으로 이동하고 관리하려는 앱을 선택합니다.

2. **애플리케이션 프록시** 를 선택합니다.

3. **사전 인증** 필드에서 드롭다운 목록을 사용하여 **Azure Active Directory** 를 선택하고 **저장** 을 선택합니다.

사전 인증을 사용하도록 설정하면 Azure AD는 먼저 인증을 위해 사용자에게 질문을 하고 Single Sign-On이 구성된 경우 백 엔드 애플리케이션에서는 애플리케이션에 대한 액세스 권한을 부여하기 전에 사용자도 확인합니다. 사전 인증 모드를 통과에서 Azure AD로 변경하면 외부 URL도 HTTPS로 구성되므로 처음에 HTTP용으로 구성된 모든 애플리케이션은 이제 HTTPS로 보호됩니다.

### <a name="enable-single-sign-on"></a>Single Sign-On 사용

SSO는 사용자가 Azure AD에 액세스할 때 한 번만 로그인하면 되므로 최상의 사용자 환경과 보안을 제공합니다. 사용자가 사전 인증을 받으면 온-프레미스 애플리케이션에 인증하는 애플리케이션 프록시 커넥터가 사용자를 대신하여 SSO를 수행합니다. 백 엔드 애플리케이션은 사용자 자신인 것처럼 로그인을 처리합니다.

**통과** 옵션을 선택하면 사용자가 Azure AD에 인증하지 않고도 게시된 애플리케이션에 액세스할 수 있습니다.

SSO를 수행하는 것은 Azure AD가 리소스에 대한 액세스를 요청하는 사용자를 식별할 수 있는 경우에만 가능하므로 SSO가 작동하려면 액세스할 때 Azure AD를 사용하여 사용자를 사전 인증하도록 애플리케이션을 구성해야 합니다. 그렇지 않으면 SSO 옵션이 사용하지 않게 설정됩니다.

애플리케이션을 구성할 때 가장 적합한 SSO 방법을 선택하는 데 도움이 되도록 [Azure AD에서 애플리케이션에 대한 Single Sign-on](../manage-apps/what-is-single-sign-on.md)을 확인합니다.

###  <a name="working-with-other-types-of-applications"></a>다른 형식의 애플리케이션 작업

Azure AD 애플리케이션 프록시는 [MSAL(Microsoft Authentication Library)](../develop/v2-overview.md)을 사용하도록 개발된 애플리케이션도 지원할 수 있습니다. 사용자를 대신하여 사전 인증을 수행하기 위해 클라이언트 요청의 헤더 정보에 수신된 Azure AD 발급 토큰을 사용하여 원시 클라이언트 앱을 지원합니다.

[원시 및 모바일 클라이언트 앱 게시](./application-proxy-configure-native-client-application.md) 및 [클레임 기반 애플리케이션](./application-proxy-configure-for-claims-aware-applications.md)을 읽고 사용 가능한 애플리케이션 프록시 구성에 관해 알아보세요.

### <a name="use-conditional-access-to-strengthen-security"></a>조건부 액세스를 사용하여 보안 강화

애플리케이션 보안을 위해서는 온-프레미스 및 클라우드에서 복잡한 위협으로부터 보호하고 이에 대응할 수 있는 고급 보안 기능 세트가 필요합니다. 공격자는 대부분 취약하거나 기본이거나 도난당한 사용자 자격 증명을 통해 회사 네트워크의 액세스 권한을 얻습니다.  Microsoft ID 기반 보안은 권한 있는 ID와 권한 없는 ID를 모두 관리하고 보호하여 도난된 자격 증명의 사용을 줄입니다.

Azure AD 애플리케이션 프록시를 지원하기 위해 다음 기능을 사용할 수 있습니다.

* 사용자 및 위치 기반 조건부 액세스: [위치 기반 조건부 액세스 정책](../conditional-access/location-condition.md)을 사용하여 지리적 위치 또는 IP 주소를 기반으로 사용자 액세스를 제한하여 중요한 데이터를 보호합니다.

* 디바이스 기반 조건부 액세스: 등록, 승인 및 규격 디바이스만 [디바이스 기반 조건부 액세스](../conditional-access/require-managed-devices.md)를 통해 회사 데이터에 액세스할 수 있게 합니다.

* 애플리케이션 기반 조건부 액세스: 사용자가 회사 네트워크에 있지 않을 때 작업을 중지할 필요가 없습니다. [회사 클라우드 및 온-프레미스 앱에 안전하게 액세스](../conditional-access/app-based-conditional-access.md)하고 조건부 액세스를 통해 제어를 유지 관리합니다.

* 위험 기반 조건부 액세스: 온-프레미스 또는 클라우드와 관계없이 모든 앱과 모든 사용자에게 적용할 수 있는 [위험 기반 조건부 액세스 정책](https://www.microsoft.com/cloud-platform/conditional-access)을 통해 악의적인 해커로부터 데이터를 보호합니다.

* Azure AD My Apps: 애플리케이션 프록시 서비스가 배포되고 애플리케이션이 안전하게 게시되면 사용자에게 모든 애플리케이션을 검색하고 액세스할 수 있는 간단한 허브를 제공합니다. [내 앱](https://aka.ms/AccessPanelDPDownload)을 통해 새 앱 및 그룹에 대한 액세스를 요청하거나 다른 사용자를 대신하여 해당 리소스에 대한 액세스를 관리하는 기능과 같은 셀프 서비스 기능으로 생산성을 높입니다.

## <a name="manage-your-implementation"></a>구현 관리

### <a name="required-roles"></a>필요한 역할

Microsoft는 Azure AD로 필요한 작업을 수행할 수 있는 최소한의 권한을 부여하는 원칙을 지지합니다. [사용 가능한 다양한 Azure 역할을 검토](../roles/permissions-reference.md)하고 각 가상 사용자의 요구 사항을 해결하는 데 적합한 역할을 선택합니다. 일부 역할은 배포를 완료한 후 임시로 적용해야 할 수도 있습니다.

| 비즈니스 역할| 비즈니스 작업| Azure AD 역할 |
|---|---|---|
| 지원 센터 관리자 | 일반적으로 최종 사용자가 보고한 문제를 검증하고 사용자 암호 변경, 새로 고침 토큰 무효화 및 서비스 상태 모니터링과 같은 제한된 작업을 수행하는 것으로 제한됩니다. | 기술 지원팀 관리자 |
| ID 관리| Azure AD 로그인 보고서 및 감사 로그를 읽어 앱 프록시 관련 문제를 디버그합니다.| 보안 판독기 |
| 애플리케이션 소유자| 엔터프라이즈 애플리케이션, 애플리케이션 등록 및 애플리케이션 프록시 설정의 모든 측면을 만들고 관리합니다.| 애플리케이션 관리자 |
| 인프라 관리자 | 인증서 롤오버 소유자 | 애플리케이션 관리자 |

보안 정보 또는 리소스에 액세스할 수 있는 사용자의 수를 최소화하면 악의적인 작업자가 무단 액세스를 얻거나 권한이 있는 사용자가 실수로 중요한 리소스에 영향을 미칠 가능성을 줄이는 데 도움이 됩니다.

그러나 사용자는 여전히 권한이 있는 일상적인 작업을 수행해야 하므로 Azure 리소스 및 Azure AD에 대한 주문형 권한 있는 액세스를 제공하도록 JIT(Just-In-Time) 기반 [Privileged Identity Management](../privileged-identity-management/pim-configure.md) 정책을 시행하는 것이 관리 액세스 및 감사를 효과적으로 관리하기 위해 권장되는 접근 방식입니다.

### <a name="reporting-and-monitoring"></a>보고 및 모니터링

Azure AD는 [감사 로그 및 보고서](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context)를 통해 조직의 애플리케이션 사용 및 운영 상태에 관한 추가 인사이트를 제공합니다. 애플리케이션 프록시를 사용하면 Azure AD 포털 및 Windows 이벤트 로그에서 커넥터를 매우 쉽게 모니터링할 수 있습니다.

#### <a name="application-audit-logs"></a>애플리케이션 감사 로그

이 로그는 애플리케이션 프록시로 구성된 애플리케이션에 대한 로그인과 애플리케이션에 액세스하는 사용자 및 디바이스에 관한 자세한 정보를 제공합니다. [감사 로그](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context)는 내보내기를 위해 Azure Portal 및 [감사 API](/graph/api/resources/directoryaudit)에 있습니다. 또한 애플리케이션에 관한 [사용 및 인사이트 보고서](../reports-monitoring/concept-usage-insights-report.md?context=azure/active-directory/manage-apps/context/manage-apps-context)도 사용할 수 있습니다.

#### <a name="application-proxy-connector-monitoring"></a>애플리케이션 프록시 커넥터 모니터링

커넥터와 서비스는 모든 고가용성 작업을 처리합니다. Azure AD 포털의 애플리케이션 프록시 페이지에서 커넥터 상태를 모니터링할 수 있습니다. 커넥터 유지 관리에 관한 자세한 내용은 [Azure AD 애플리케이션 프록시 커넥터 이해](./application-proxy-connectors.md#maintenance)를 참조하세요.

![예: Azure AD 애플리케이션 프록시 커넥터](./media/application-proxy-connectors/app-proxy-connectors.png)

#### <a name="windows-event-logs-and-performance-counters"></a>Windows 이벤트 로그 및 성능 카운터

커넥터에는 관리 및 세션 로그가 모두 포함됩니다. 관리 로그에는 주요 이벤트와 해당 오류가 포함됩니다. 세션 로그에는 모든 트랜잭션 및 처리 세부 정보가 포함됩니다. 로그와 카운터는 Windows 이벤트 로그에 있습니다. 자세한 내용은 [Azure AD 애플리케이션 프록시 커넥터 이해](./application-proxy-connectors.md#under-the-hood)를 참조하세요. [Azure Monitor에서 이벤트 로그 데이터 원본을 구성하려면 이 자습서](../../azure-monitor/agents/data-sources-windows-events.md)에 따라 수행합니다.

### <a name="troubleshooting-guide-and-steps"></a>문제 해결 가이드 및 단계

오류 메시지 [문제 해결](application-proxy-troubleshoot.md) 가이드를 통해 일반적인 문제와 해결 방법에 관해 자세히 알아보세요.

다음 문서에서는 지원 조직에 대한 문제 해결 가이드를 만드는데도 사용할 수 있는 일반적인 시나리오를 다룹니다.

* [앱 페이지를 표시할 때 문제](application-proxy-page-appearance-broken-problem.md)
* [너무 긴 애플리케이션 로드](application-proxy-page-load-speed-problem.md)
* [애플리케이션 페이지에서 링크가 작동하지 않음](application-proxy-page-links-broken-problem.md)
* [내 앱에 대해 열 포트](application-proxy-add-on-premises-application.md)
* [내 앱에 대한 커넥터 그룹에서 작동하지 않는 커넥터](application-proxy-connectivity-no-working-connector.md)
* [관리자 포털에서 구성](application-proxy-config-how-to.md)
* [내 앱에 Single Sign-On 구성](application-proxy-config-sso-how-to.md)
* [관리 포털에서 앱을 만드는 문제](application-proxy-config-problem.md)
* [Kerberos 제한된 위임 구성](application-proxy-back-end-kerberos-constrained-delegation-how-to.md)
* [PingAccess를 사용하여 구성](application-proxy-ping-access-publishing-guide.md)
* [회사 애플리케이션에 액세스할 수 없음 오류](application-proxy-sign-in-bad-gateway-timeout-error.md)
* [애플리케이션 프록시 에이전트 커넥터를 설치할 때 문제 발생](application-proxy-connector-installation-problem.md)
* [로그인 문제](application-sign-in-problem-on-premises-application-proxy.md)
