---
title: 보안 하이브리드 액세스
description: 이 문서에서는 레거시 온-프레미스, 퍼블릭 클라우드 또는 프라이빗 클라우드 애플리케이션을 Azure AD와 통합하기 위한 파트너 솔루션에 대해 설명합니다.
titleSuffix: Azure AD
services: active-directory
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.date: 8/17/2021
ms.author: gasinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9403600e65bfc563c66f31e6d2ed8905702a3943
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/06/2021
ms.locfileid: "129615679"
---
# <a name="secure-hybrid-access-secure-legacy-apps-with-azure-active-directory"></a>보안 하이브리드 액세스: Azure Active Directory를 사용한 보안 레거시 앱

이제 온-프레미스 및 클라우드 레거시 인증 애플리케이션을 다음과 함께 Azure AD(Active Directory)에 연결하여 보호할 수 있습니다.

- [Azure AD 애플리케이션 프록시](#secure-hybrid-access-through-azure-ad-application-proxy)

- [보안 하이브리드 액세스 파트너](#secure-hybrid-access-through-azure-ad-partner-integrations)

[Azure AD 조건부 액세스](../conditional-access/overview.md) 및 [Azure AD ID 보호](../identity-protection/overview-identity-protection.md) 같은 Azure AD 기능을 사용하여 모든 애플리케이션에서 간극을 연결하고, 보안 태세를 강화할 수 있습니다. IDP(ID 공급자)로 Azure AD를 사용하여 SSO([Single Sign-On](what-is-single-sign-on.md))와 MFA([다단계 인증](../authentication/concept-mfa-howitworks.md))와 같은 최신 인증 및 권한 부여 방법을 통해 온-프레미스 레거시 애플리케이션을 보호할 수 있습니다.

## <a name="secure-hybrid-access-through-azure-ad-application-proxy"></a>Azure AD 애플리케이션 프록시를 통한 보안 하이브리드 액세스
  
[애플리케이션 프록시](../app-proxy/what-is-application-proxy.md)를 통해 온-프레미스 웹 애플리케이션에 대한 [보안 원격 액세스](../app-proxy/application-proxy-add-on-premises-application.md)를 제공할 수 있습니다. 사용자가 VPN을 사용할 필요가 없습니다. 사용자는 [SSO](../app-proxy/application-proxy-config-sso-how-to.md#how-to-configure-single-sign-on) 후에 모든 디바이스에서 애플리케이션에 쉽게 연결하여 이점을 누릴 수 있습니다. 애플리케이션 프록시는 원격 액세스를 서비스로 제공하므로 [온-프레미스 애플리케이션](../app-proxy/application-proxy-add-on-premises-application.md)을 회사 네트워크 외부의 사용자에게 쉽게 게시할 수 있습니다. 온-프레미스 애플리케이션을 수정할 필요 없이 클라우드 액세스 관리를 확장하는 데 도움이 됩니다. 다음 단계로 [Azure AD 애플리케이션 프록시 배포를 계획](../app-proxy/application-proxy-deployment-plan.md)합니다.

## <a name="secure-hybrid-access-through-azure-ad-partner-integrations"></a>Azure AD 파트너 통합을 통한 보안 하이브리드 액세스  

[Azure AD 애플리케이션 프록시](../app-proxy/what-is-application-proxy.md) 외에도 Microsoft는 타사 공급자와 협력하여 레거시 인증을 사용하는 애플리케이션 및 온-프레미스 애플리케이션에 대한 보안 액세스를 지원합니다.

![앱 프록시와 파트너를 통한 보안 하이브리드 액세스를 보여 주는 이미지](./media/secure-hybrid-access/secure-hybrid-access.png)

다음 파트너는 **애플리케이션당 조건부 액세스 정책** 을 지원하고 Azure AD와 통합하기 위한 자세한 지침을 제공하는 사전 구축된 솔루션을 제공합니다. 

- [Akamai 엔터프라이즈 애플리케이션 액세스](../saas-apps/akamai-tutorial.md)

- [Citrix ADC(Application Delivery Controller)](../saas-apps/citrix-netscaler-tutorial.md)  

- [Datawiza Access Broker](../manage-apps/datawiza-with-azure-ad.md)

- [F5 Big-IP APM ADC](../manage-apps/f5-aad-integration.md)

- [F5 Big-IP APM VPN](../manage-apps/f5-aad-password-less-vpn.md)

- [Kemp](../saas-apps/kemp-tutorial.md)

- [Perimeter 81](../saas-apps/perimeter-81-tutorial.md)

- [Silverfort 인증 플랫폼](../manage-apps/silverfort-azure-ad-integration.md)

- [Strata](../saas-apps/maverics-identity-orchestrator-saml-connector-tutorial.md)

다음 파트너는 Azure AD와 통합하기 위한 미리 빌드한 솔루션과 자세한 지침을 제공합니다.

- [Cisco AnyConnect](../saas-apps/cisco-anyconnect.md)

- [Fortinet](../saas-apps/fortigate-ssl-vpn-tutorial.md)

- [Palo Alto Networks Global Protect](../saas-apps/paloaltoadmin-tutorial.md)

- [Pulse Secure PCS(Pulse Connect Secure)](../saas-apps/pulse-secure-pcs-tutorial.md)

- [Pulse Secure VTM(Virtual Traffic Manager)](../saas-apps/pulse-secure-virtual-traffic-manager-tutorial.md)

- [ZPA(Zscaler Private Access)](../saas-apps/zscalerprivateaccess-tutorial.md)
