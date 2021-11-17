---
title: F5를 통한 안전한 하이브리드 액세스
titleSuffix: Azure AD
description: 보안 하이브리드 액세스를 위한 F5 BIG-IP Access Policy Manager 및 Azure Active Directory 통합
author: davidmu1
manager: martinco
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.date: 11/12/2020
ms.author: davidmu
ms.collection: M365-identity-device-management
ms.reviewer: miccohen
ms.openlocfilehash: 0faa0d97c1e1bb73e2a97aa3715efee58a589cd8
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131432627"
---
# <a name="integrate-f5-big-ip-with-azure-active-directory"></a>Azure Active Directory와 F5 BIG-IP 통합

모바일의 확산과 진화하는 위협 환경에 따라, 리소스 액세스 및 거버넌스에 대한 면밀한 조사가 요구되기 때문에 모든 최신 프로그램에 대한 [제로 트러스트](https://www.microsoft.com/security/blog/2020/04/02/announcing-microsoft-zero-trust-assessment-tool/)가 무엇보다 중요합니다.
Microsoft와 F5는 이러한 디지털 변환이 어느 기업에서나 장기간에 걸친 과정이기 때문에 현대화가 완료될 때까지 중요한 리소스가 노출될 가능성이 있음을 확인하게 되었습니다. F5 BIG-IP 및 Azure Active Directory SHA(Secure Hybrid Access)는 근본적으로 온-프레미스 애플리케이션에 대한 원격 액세스 향상뿐 아니라, 취약한 서비스의 전반적인 보안 상태를 강화하는 것을 목표로 합니다.

이와 관련하여 연구에 따르면 온-프레미스 애플리케이션의 60-80%가 본질적으로 레거시로 추정됩니다. 즉, Azure AD(Active Directory)와 직접적으로 통합될 수 없습니다. 이 연구에서는 이러한 시스템의 상당 비율이 SAP, Oracle, SAGE 및 기타 핵심 서비스를 제공하는 유명 워크로드의 하위 버전에서 실행되고 있다고도 밝히고 있습니다.

SHA는 이 간과하기 쉬운 부분을 파악하여, 조직이 지속적으로 F5 투자를 뛰어난 네트워크와 애플리케이션 배달에 사용할 수 있도록 합니다. Azure AD와 함께 사용하면 이종 애플리케이션 환경을 최신 ID 컨트롤 플레인에 연계할 수 있습니다.

BIG-IP 게시 서비스에 대한 Azure AD 사전 인증 액세스에는 다음과 같은 여러 이점이 있습니다.

- [Windows Hello](/windows/security/identity-protection/hello-for-business/hello-overview), [MS Authenticator](https://support.microsoft.com/account-billing/download-and-install-the-microsoft-authenticator-app-351498fc-850a-45da-b7b6-27e523b8702a), [FIDO(Fast Identity Online) 키](../authentication/howto-authentication-passwordless-security-key.md) 및 [인증서 기반 인증](../authentication/active-directory-certificate-based-authentication-get-started.md)을 통한 암호 없는 인증

- 선점형 [조건부 액세스](../conditional-access/overview.md) 및 [Azure AD MFA(다단계 인증)](../authentication/concept-mfa-howitworks.md)

- [ID 보호](../identity-protection/overview-identity-protection.md) - 사용자 및 세션 위험 프로파일링을 통한 적응형 제어

- [누출 자격 증명 검색](../identity-protection/concept-identity-protection-risks.md)

- [SSPR(셀프 서비스 암호 재설정)](../authentication/tutorial-enable-sspr.md)

- [파트너 협업](../governance/entitlement-management-external-users.md) - 관리되는 게스트 액세스를 위한 권한 관리

- [CASB(Cloud App Security)](/cloud-app-security/what-is-cloud-app-security) -전체 앱 검색 및 제어

- 위협 모니터링 - 고급 위협 분석을 위한 [Azure Sentinel](https://azure.microsoft.com/services/azure-sentinel/)

- [Azure AD 포털](https://azure.microsoft.com/features/azure-portal/) - ID 및 액세스 관리를 위한 단일 컨트롤 플레인

## <a name="scenario-description"></a>시나리오 설명

ADC(애플리케이션 배달 컨트롤러) 및 SSL-VPN으로서 BIG-IP 시스템은 다음을 포함한 모든 서비스 유형에 대해 로컬 및 원격 액세스를 제공합니다.

- 최신 및 레거시 웹 애플리케이션

- 웹 기반이 아닌 애플리케이션

- REST 및 SOAP 웹 API 서비스

LTM(Local Traffic Manager)는 역방향 프록시 기능을 통해 서비스의 보안 게시를 허용합니다. 동시에, APM(고급 액세스 정책 관리자)은 더 다양한 기능 집합으로 BIG-IP를 확장하여 페더레이션과 SSO(Single Sign On)를 구현합니다.

이 통합은 [SSL-VPN 시나리오](f5-aad-password-less-vpn.md)를 포함하는 대부분의 SHA 사용 사례에 공통인, APM과 Azure AD 간의 표준 페더레이션 트러스트를 기반으로 합니다. SAML(Security Assertion Markup Language), OAuth 및 OIDC(Open ID Connect) 리소스도 원격 액세스에 대해 보호될 수 있으므로 예외가 아닙니다. BIG-IP가, SaaS 앱을 포함하여 모든 서비스에 대한 제로 트러스트 액세스를 위한 요충지가 되는 시나리오도 가능합니다.

BIG-IP의 Azure AD 통합 기능은 [암호 없는 인증](https://www.microsoft.com/security/business/identity/passwordless) 및 [조건부 액세스](../conditional-access/overview.md) 등 최신 제어를 통해 레거시 또는 Azure AD 이외의 통합 서비스를 보호하는 데 필요한 프로토콜 전환을 사용할 수 있게 하는 것입니다. 이 시나리오에서 BIG-IP는 지속적으로 역방향 프록시 역할을 이행하고, 서비스 기준에 따라 인증과 권한 부여는 Azure AD에게 맡깁니다.

다이어그램의 1-4단계는 서비스 공급자가 시작한 흐름에서 사용자, BIG-IP 및 Azure AD 간의 프런트 엔드 사전 인증 교환을 설명하고 있습니다. 5-6단계는 개별 백 엔드 서비스에 대한 후속 APM 세션 보강 및 SSO를 나타냅니다.

![상위 수준 아키텍처를 보여주는 이미지](./media/f5-aad-integration/integration-flow-diagram.png)

| 단계 | Description |
|:------|:-----------|
| 1. | 사용자가 포털에서 애플리케이션 아이콘을 선택하여 SAML SP(BIG-IP)에 대해 URL을 확인합니다. |
| 2. | BIG-IP가 사전 인증을 위해 사용자를 SAML IDP(Azure AD)로 리디렉션합니다.|
| 3. | Azure AD가 권한 부여를 위해 조건부 액세스 정책 및 [세션 컨트롤](../conditional-access/concept-conditional-access-session.md)을 처리합니다.|
| 4. | 사용자가 Azure AD에서 발급한 SAML 클레임을 제시하여 다시 BIG-IP로 리디렉션됩니다. |
| 5. | BIG-IP가 게시된 서비스에 대해 [SSO](../hybrid/how-to-connect-sso.md) 및 [RBAC(역할 기반 액세스 제어)](../../role-based-access-control/overview.md)에 포함할 추가 세션 정보를 요청합니다. |
| 6. | BIG-IP가 백 엔드 서비스에 클라이언트 요청을 전달합니다.

## <a name="user-experience"></a>사용자 환경

소속 직원, 계열사 또는 소비자 등 대부분의 사용자는 이미 Office 365 로그인 환경을 잘 알고 있기 때문에 SHA를 통한 BIG-IP 서비스 액세스가 매우 익숙합니다.

이제 사용자는 디바이스 유형이나 위치에 관계없이 [MyApps](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510) 또는 [O365 실행 패드](https://o365pp.blob.core.windows.net/media/Resources/Microsoft%20365%20Business/Launchpad%20Overview_for%20Partners_10292019.pdf)에서 셀프 서비스 기능과 함께 더 광범위한 서비스 집합에 통합된 BIG-IP 게시 서비스를 확인할 수 있습니다. 사용자는 원하는 경우 BIG-IP 소유 Webtop 포털을 통해 직접 게시된 서비스에 계속 액세스할 수도 있습니다. 로그오프하면 SHA가 사용자의 세션이 BIG-IP와 Azure AD 양쪽에서 모두 확실히 종료되게 하여 서비스를 무단 액세스로부터 완전히 보호합니다.  

제공되는 스크린샷은 Azure AD 앱 포털에서 사용자가 BIG-IP 게시 서비스를 찾고 자신의 계정 속성을 관리하기 위해 안전하게 액세스하는 화면입니다.  

![woodgrove myapps 갤러리를 표시하는 스크린샷](media/f5-aad-integration/woodgrove-app-gallery.png)

![woodgrove myaccounts 셀프 서비스 페이지를 표시하는 스크린샷](media/f5-aad-integration/woodgrove-myaccount.png)

## <a name="insights-and-analytics"></a>인사이트 및 분석

어느 비즈니스에서나 BIG-IP의 역할이 중요하므로, SHA 수준과 운영 모두에서 게시된 서비스가 높은 가용성 수준으로 제공되도록 배포된 BIG-IP 인스턴스를 모니터링해야 합니다.

SIEM(보안 정보 및 이벤트 관리) 솔루션을 통해 이벤트를 로컬 또는 원격으로 로그하는 데는 몇 가지 옵션이 있으므로 원격 분석의 외부 저장 및 처리를 사용할 수 있습니다. Azure AD 및 SHA 관련 활동을 모니터링하는 매우 효과적인 솔루션은 [Azure Monitor](../../azure-monitor/overview.md) 및 [Azure Sentinel](../../sentinel/overview.md)을 다음 제품과 함께 사용하는 것입니다.

- BIG-IP 인프라를 비롯하여 여러 클라우드 및 온-프레미스 위치에 있을 수 있는 조직의 상세 개요

- 복잡하고 다양한 도구를 사용할 필요 없이, 모든 신호의 결합된 보기를 제공하는 단일 컨트롤 플레인

![모니터링 흐름을 표시하는 이미지](media/f5-aad-integration/azure-sentinel.png)

## <a name="prerequisites"></a>사전 요구 사항

SHA를 위해 F5 BIG-IP를 Azure AD에 통합하기 위한 사전 요구 사항은 다음과 같습니다.

- 다음 플랫폼 중 하나에서 실행되는 F5 BIG-IP 인스턴스:

  - 물리적 어플라이언스

  - Microsoft Hyper-V, VMware ESXi, Linux KVM 및 Citrix Hypervisor 등과 같은 하이퍼바이저 가상 버전

  - Azure, VMware, KVM, Community Xen, MS Hyper-V, AWS, OpenStack 및 Google Cloud와 같은 클라우드 가상 버전

    BIG-IP 인스턴스의 실제 위치는 온-프레미스이거나, 인터넷에 연결 가능하고 리소스가 게시되었으며 Active Directory와 같은 다른 필수 서비스가 있다면 Azure 등과 같이 지원되는 모든 클라우드 플랫폼이 될 수 있습니다.  

- 다음 옵션 중 하나를 통해 활성화된 F5 BIG-IP APM 라이선스:

  - F5 BIG-IP® Best 번들 (또는)

    - F5 BIG-IP APM(Access Policy Manager™) 독립 실행형 라이선스

    - 기본 BIG-IP F5 BIG-IP® LTM(Local Traffic Manager)™에 대한 F5 BIG-IP APM(Access Policy Manager™) 추가 기능 라이선스

    - 90일 BIG-IP APM(Access Policy Manager™) [평가판 라이선스](https://www.f5.com/trial/big-ip-trial.php)

- 다음 옵션 중 하나를 통해 Azure AD 라이선스 부여:

  - Azure AD [무료 구독](/windows/client-management/mdm/register-your-free-azure-active-directory-subscription#:~:text=%20Register%20your%20free%20Azure%20Active%20Directory%20subscription,will%20take%20you%20to%20the%20Azure...%20More%20)은 암호 없는 인증으로 SHA를 구현하기 위한 최소한의 핵심 요구 사항을 제공합니다.

  - [프리미엄 구독](https://www.microsoft.com/security/business/identity-access-management/azure-ad-pricing)은 [조건부 액세스](../conditional-access/overview.md), [MFA](../authentication/concept-mfa-howitworks.md) 및 [ID 보호](../identity-protection/overview-identity-protection.md)를 포함하여 앞에서 설명한 모든 추가적인 값을 더 제공합니다.

SHA 구현에 이전의 경험이나 F5 BIG-IP 지식이 필요한 것은 아니지만 F5 BIG-IP 용어에는 익숙해지는 것이 좋습니다. F5의 [기술 자료](https://www.f5.com/services/resources/glossary)도 BIG-IP 지식을 쌓기 위한 좋은 출발점이 됩니다.

## <a name="deployment-scenarios"></a>배포 시나리오

다음 자습서에서는 BIG-IP 및 Azure AD SHA의 더 일반적인 패턴 몇 가지를 구현하기 위한 자세한 지침을 제공합니다.

- [Azure 배포 연습의 F5 BIG-IP](f5-bigip-deployment-guide.md)

- [Kerberos 애플리케이션에 대한 F5 BIG-IP APM 및 Azure AD SSO](../saas-apps/kerbf5-tutorial.md#configure-f5-single-sign-on-for-kerberos-application)

- [헤더 기반 애플리케이션에 대한 F5 BIG-IP APM 및 Azure AD SSO](../saas-apps/headerf5-tutorial.md#configure-f5-single-sign-on-for-header-based-application)

- [Azure AD SHA를 통한 F5 BIG-IP SSL-VPN 보호](f5-aad-password-less-vpn.md)

- [F5 BIG-IP로 Azure AD B2C 구성](../../active-directory-b2c/partner-f5.md)

- [양식 기반 인증 SSO를 위해 Azure Active Directory와 F5 BIG-IP 통합](f5-big-ip-forms-advanced.md)

## <a name="additional-resources"></a>추가 리소스

- [암호는 잊고 암호 없는 환경으로 전환](https://www.microsoft.com/security/business/identity/passwordless)

- [Azure Active Directory 보안 하이브리드 액세스](https://azure.microsoft.com//services/active-directory/sso/secure-hybrid-access/)

- [Microsoft 제로 트러스트 프레임워크를 사용하여 원격 인력 지원](https://www.microsoft.com/security/blog/2020/04/02/announcing-microsoft-zero-trust-assessment-tool/)

- [Azure Sentinel 시작하기](https://azure.microsoft.com/services/azure-sentinel/?&OCID=AID2100131_SEM_XfknpgAAAHoVMTvh:20200922160358:s&msclkid=5e0e022409fc1c94dab85d4e6f4710e3&ef_id=XfknpgAAAHoVMTvh:20200922160358:s&dclid=CJnX6vHU_esCFUq-ZAod1iQF6A)

## <a name="next-steps"></a>다음 단계

기존 BIG-IP 인프라를 사용하거나 평가판 인스턴스를 배포하여 SHA POC(개념 증명)를 실행하는 것이 좋습니다. [Azure에 BIG-IP VE(가상 버전) VM을 배포](f5-bigip-deployment-guide.md)하는 데는 약 30분이 소요되고, 배포되면 다음을 보유하게 됩니다.

- SHA 개념 증명을 모델링하는 완전 보안 플랫폼

- 새 BIG-IP 시스템 업데이트 및 핫픽스를 테스트하는 데 사용할 사전 프로덕션 인스턴스, 완전 보안 플랫폼

동시에, BIG-IP를 통해 게시하고 SHA로 보호할 대상이 될 수 있는 하나 또는 두 개의 애플리케이션을 식별해야 합니다.  

프로덕션 서비스의 잠재적 중단을 방지할 수 있게 아직 BIG-IP를 통해 게시되지 않은 애플리케이션으로 시작하는 것이 좋습니다. 이 문서에서 설명한 지침은 다양한 BIG-IP 구성 개체를 만들고 SHA를 설정하기 위한 일반적인 절차를 이해하는 데 도움이 됩니다. 마친 후에는 다른 새 서비스에서 같은 작업을 수행할 수 있고, 최소한의 작업으로 기존 BIG-IP 게시 서비스를 SHA로 변환하기에 충분한 지식을 확보하게 됩니다.

아래 대화형 가이드는 SHA를 구현하고 최종 사용자 환경을 확인하기 위한 개략적인 절차를 안내합니다.

[![대화형 가이드 커버를 보여주는 이미지](media/f5-aad-integration/interactive-guide.png)](https://aka.ms/Secure-Hybrid-Access-F5-Interactive-Guide)
