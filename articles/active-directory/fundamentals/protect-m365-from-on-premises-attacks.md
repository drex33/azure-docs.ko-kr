---
title: 온-프레미스 공격으로부터 Microsoft 365 보호
description: 온-프레미스 공격이 Microsoft 365에 영향을 주지 않도록 하는 방법에 대한 지침입니다.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 12/22/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 73bd6ed2c94ca696eefd4e7a3dfcf5b78cc6dd99
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130251189"
---
# <a name="protecting-microsoft-365-from-on-premises-attacks"></a>온-프레미스 공격으로부터 Microsoft 365 보호

많은 고객이 프라이빗 회사 네트워크를 Microsoft 365에 연결하여 사용자, 디바이스, 애플리케이션에 이점을 제공합니다. 그러나 프라이빗 네트워크는 잘 문서화된 방식으로 손상될 수 있습니다. Microsoft 365는 많은 조직에서 일종의 신경계 역할을 하므로 손상된 온-프레미스 인프라로부터 이를 보호하는 것이 중요합니다.

이 문서에서는 온-프레미스 손상으로부터 Microsoft 365 클라우드 환경을 보호하도록 시스템을 구성하는 방법을 알아봅니다. 주로 다음에 초점을 둡니다. 

- Azure AD(Azure Active Directory) 테넌트 구성 설정.
- 온-프레미스 시스템에 Azure AD 테넌트를 안전하게 연결하는 방법.
- 온-프레미스 클라우드 시스템이 손상되지 않도록 보호하는 방식으로 시스템을 운영하는 데 필요한 절충안.

Microsoft 365 클라우드 환경을 보호하려면 이 지침을 구현하는 것이 좋습니다.
> [!NOTE]
> 이 문서는 블로그 게시물로 처음 게시되었으며, 수명 및 유지 관리를 위해 현재 위치로 이동되었습니다.
>
> 이 문서의 오프라인 버전을 만들려면 브라우저의 PDF 인쇄 기능을 사용합니다. 자주 이 페이지에서 업데이트된 사항을 확인하세요.

## <a name="primary-threat-vectors-from-compromised-on-premises-environments"></a>손상된 온-프레미스 환경의 주요 위협 벡터


Microsoft 365 클라우드 환경은 광범위한 모니터링 및 보안 인프라를 활용합니다. Microsoft 365는 기계 학습 및 휴먼 인텔리전스를 사용하여 전 세계 트래픽을 확인합니다. 공격을 신속하게 감지하고 거의 실시간으로 다시 구성할 수 있도록 해 줍니다. 

온-프레미스 인프라를 Microsoft 365에 연결하는 하이브리드 배포에서 많은 조직은 중요한 인증 및 디렉터리 개체 상태 관리 결정을 위해 온-프레미스 구성 요소에 신뢰를 위임합니다.
아쉽게도 온-프레미스 환경이 손상되면 신뢰 관계는 공격자가 Microsoft 365 환경을 손상할 수 있는 기회가 됩니다.

두 가지 주요 위협 벡터는 ‘페더레이션 트러스트 관계’와 ‘계정 동기화’입니다.  두 벡터 모두 공격자에게 클라우드에 대한 관리 액세스 권한을 부여할 수 있습니다.

* SAML 인증과 같은 **페더레이션 트러스트 관계** 를 사용하여 온-프레미스 ID 인프라를 통해 Microsoft 365를 인증합니다. SAML 토큰 서명 인증서가 손상되면 페더레이션을 통해 해당 인증서를 가진 모든 사용자가 클라우드의 모든 사용자를 가장할 수 있습니다. ‘가능한 경우 Microsoft 365 인증을 위해 페더레이션 트러스트 관계를 사용하지 않는 것이 좋습니다.’

* **계정 동기화** 를 사용하여 권한이 있는 사용자(자격 증명 포함) 또는 Microsoft 365에서 관리 권한이 있는 그룹을 수정할 수 있습니다. ‘동기화된 개체는 직접 또는 신뢰할 수 있는 역할이나 그룹에 포함하여 Microsoft 365의 사용자 이상의 권한을 보유하지 않는 것이 좋습니다.’ 해당 개체에 신뢰할 수 있는 클라우드 역할 또는 그룹의 직접 또는 중첩된 할당이 없는지 확인하세요.

## <a name="protecting-microsoft-365-from-on-premises-compromise"></a>온-프레미스 손상으로부터 Microsoft 365 보호


앞에서 설명한 위협 벡터를 해결하려면 다음 다이어그램에 설명된 원칙을 따르는 것이 좋습니다.

![Microsoft 365를 보호하기 위한 참조 아키텍처.](media/protect-m365/protect-m365-principles.png)

1. **Microsoft 365 관리자 계정을 완전히 격리합니다.** 이는 다음과 같아야 합니다.

    * Azure AD에서 마스터됨.

     * 다단계 인증을 사용하여 인증됨.

     *  Azure AD 조건부 액세스를 통해 보안됨.

     *  Azure 관리형 워크스테이션만을 사용하여 액세스됨.

    해당 관리자 계정은 사용이 제한된 계정입니다. ‘온-프레미스 계정에는 Microsoft 365에 대한 관리자 권한이 없어야 합니다.’ 

    자세한 내용은 [Microsoft 365 관리자 역할 개요](/microsoft-365/admin/add-users/about-admin-roles)를 참조하세요. [Azure AD에서의 Microsoft 365 역할](../roles/m365-workload-docs.md)도 참조하세요.

1. **Microsoft 365에서 디바이스를 관리합니다.** Azure AD 조인 및 클라우드 기반 MDM(모바일 장치 관리)을 사용하여 온-프레미스 디바이스 관리 인프라에 대한 종속성을 제거합니다. 종속성은 디바이스 및 보안 제어를 손상할 수 있습니다.

1. **Microsoft 365에 대해 상승된 권한이 있는 온-프레미스 계정이 없는지 확인합니다.**
    일부 계정은 NTLM, LDAP 또는 Kerberos 인증이 필요한 온-프레미스 애플리케이션에 액세스합니다. 해당 계정은 조직의 온-프레미스 ID 인프라에 있어야 합니다. 서비스 계정을 비롯한 해당 계정이 권한 있는 클라우드 역할 또는 그룹에 포함되어 있지 않은지 확인합니다. 해당 계정에 대한 변경 내용이 클라우드 환경의 무결성에 영향을 주지 않도록 해야 합니다. 권한이 있는 온-프레미스 소프트웨어는 Microsoft 365 권한이 있는 계정 또는 역할에 영향을 미치지 않습니다.

1. **Azure AD 클라우드 인증을 사용하여** 온-프레미스 자격 증명에 대한 종속성을 제거합니다. Windows Hello, FIDO, Microsoft Authenticator 또는 Azure AD 다단계 인증과 같은 강력한 인증을 항상 사용합니다.

## <a name="specific-security-recommendations"></a>특정 보안 권장 사항


다음 섹션에서는 앞에서 설명한 원칙을 구현하는 방법에 대한 구체적인 지침을 살펴봅니다.

### <a name="isolate-privileged-identities"></a>권한이 있는 ID 분리


Azure AD에서는 관리자와 같은 권한이 있는 역할을 가진 사용자가 나머지 환경을 빌드하고 관리하는 데 신뢰의 핵심입니다. 손상의 영향을 최소화하기 위해 다음 사례를 구현합니다.

* Azure AD 및 Microsoft 365 권한이 있는 역할에 대해 클라우드 전용 계정을 사용합니다.

* [권한이 있는 액세스 디바이스](/security/compass/privileged-access-devices#device-roles-and-profiles)를 배포하여 Microsoft 365 및 Azure AD를 관리합니다.

*  권한이 있는 역할이 있는 모든 사용자 계정에 JIT(just-in-time) 액세스하기 위해 [PIM](../privileged-identity-management/pim-configure.md)(Azure AD Privileged Identity Management)을 배포합니다. 역할을 활성화하려면 강력한 인증이 필요합니다.

* [필요한 작업을 수행하는 데 필요한 최소 권한을](../roles/delegate-by-task.md) 허용하는 관리자 역할을 부여합니다.

* 위임 및 여러 역할을 동시에 포함하는 효과적인 역할 할당 환경을 사용하려면 Azure AD 보안 그룹 또는 Microsoft 365 그룹을 사용하는 것이 좋습니다. 해당 그룹을 ‘클라우드 그룹’이라고 통칭합니다. 또한 [역할 기반 액세스 제어를 사용](../roles/groups-assign-role.md)합니다. [관리 단위](../roles/administrative-units.md)를 사용하여 역할의 범위를 조직 일부분으로 제한할 수 있습니다.

* [응급 액세스 계정을](../roles/security-emergency-access.md)배포합니다. 자격 증명을 저장하는 데 온-프레미스 암호 자격 증명 모음을 ‘사용하지 마세요’.

자세한 내용은 [권한 있는 액세스 보안](/security/compass/overview)을 참조하세요. 또한 [Azure AD에서의 관리자 보안 액세스 사례](../roles/security-planning.md)를 참조하세요.

### <a name="use-cloud-authentication"></a>클라우드 인증 사용 

자격 증명은 주요 공격 벡터입니다. 자격 증명을 더욱 안전하게 만들려면 다음 방법을 구현합니다.

* [암호 없는 인증을 배포](../authentication/howto-authentication-passwordless-deployment.md)합니다. 암호 없는 자격 증명을 배포하여 암호 사용을 최대한 줄입니다. 자격 증명은 클라우드에서 기본적으로 관리되고 유효성을 검사합니다. 다음 인증 방법 중에서 선택합니다.

   * [비즈니스용 Windows Hello](/windows/security/identity-protection/hello-for-business/passwordless-strategy)

   * [Microsoft Authenticator 앱](../authentication/howto-authentication-passwordless-phone.md)

   * [FIDO2 보안 키](../authentication/howto-authentication-passwordless-security-key-windows.md)

* [다단계 인증을 배포](../authentication/howto-mfa-getstarted.md)합니다. [Azure AD 다단계 인증을 사용하여 강력한 자격 증명을 여러 개](../fundamentals/resilience-in-credentials.md) 프로비저닝합니다. 이렇게 하면 클라우드 리소스에 액세스할 때 조작 가능한 온-프레미스 암호 외에도 Azure AD에서 관리되는 자격 증명이 필요합니다. 자세한 내용은 [Azure AD를 사용하여 복원력 있는 액세스 제어 관리 전략 만들기](./resilience-overview.md)를 참조하세요.

### <a name="limitations-and-tradeoffs"></a>제한 사항 및 절충안

* 하이브리드 계정 암호 관리에는 암호 보호 에이전트 및 비밀번호 쓰기 저장 에이전트와 같은 하이브리드 구성 요소가 필요합니다. 온-프레미스 인프라가 손상되면 공격자는 해당 에이전트가 있는 머신을 제어할 수 있습니다. 이 취약성은 클라우드 인프라를 손상하지는 않습니다. 그러나 클라우드 계정은 온-프레미스 손상으로부터 해당 구성 요소를 보호하지 않습니다.

*  Active Directory에서 동기화된 온-프레미스 계정은 Azure AD에서 만료되지 않은 것으로 표시됩니다. 이 설정은 일반적으로 온-프레미스 Active Directory 암호 설정에 의해 완화됩니다. 그러나 Active Directory의 온-프레미스 인스턴스가 손상되어 동기화를 사용하지 않는 경우 암호 변경을 적용하도록 [EnforceCloudPasswordPolicyForPasswordSyncedUsers](../hybrid/how-to-connect-password-hash-synchronization.md) 옵션을 설정해야 합니다.

## <a name="provision-user-access-from-the-cloud"></a>클라우드에서 사용자 액세스 프로비저닝

‘프로비저닝’은 애플리케이션 또는 ID 공급자에서 사용자 계정 및 그룹 만들기를 의미합니다.

![프로비저닝 아키텍처의 다이어그램입니다.](media/protect-m365/protect-m365-provision.png)

다음의 프로비저닝 방법을 권장합니다.

* **클라우드 HR 앱에서 Azure AD로 프로비저닝**: 이 프로비저닝은 클라우드 HR 앱에서 Azure AD로의 joiner-mover-leaver 주기를 방해하지 않고 온-프레미스 손상을 분리할 수 있습니다.

* **클라우드 애플리케이션**: 가능한 경우 온-프레미스 프로비저닝 솔루션과 달리 [Azure AD 앱 프로비저닝](../app-provisioning/user-provisioning.md)을 배포합니다. 이 방법은 SaaS(software as a service) 앱 중 일부를 온-프레미스 위반 시 악성 해커 프로필의 영향을 받지 않도록 보호합니다. 

* **외부 ID**: [Azure AD B2B Collaboration](../external-identities/what-is-b2b.md)을 사용합니다.
    이 방법은 파트너, 고객, 공급자와의 외부 협업을 위해 온-프레미스 계정에 대한 종속성을 줄입니다. 다른 ID 공급자와의 직접 페더레이션을 신중하게 평가합니다. B2B 게스트 계정은 다음과 같은 방법으로 제한하는 것이 좋습니다.

   *  디렉터리의 검색 그룹 및 기타 속성에 대한 게스트 액세스를 제한합니다. 외부 협업 설정을 사용하여 게스트의 구성원이 아닌 그룹 읽기 기능을 제한할 수 있습니다. 

    *   Azure Portal에 대한 액세스를 차단합니다. 드물게 필요한 예외를 만들 수 있습니다.  모든 게스트 및 외부 사용자를 포함하는 조건부 액세스 정책을 만듭니다. 그런 다음 [액세스를 차단하는 정책을 구현](../../role-based-access-control/conditional-access-azure-management.md)합니다. 

* **연결되지 않은 포리스트**: [Azure AD 클라우드 프로비저닝](../cloud-sync/what-is-cloud-sync.md)을 사용합니다. 이 방법을 사용하면 연결되지 않은 포리스트에 연결하여 온-프레미스 위반의 영향을 넓히는 포리스트 간 연결 또는 트러스트를 설정할 필요가 없습니다. 
 
### <a name="limitations-and-tradeoffs"></a>제한 사항 및 절충안

하이브리드 계정을 프로비저닝하는 데 사용하는 경우 Azure-AD-클라우드-HR 시스템은 온-프레미스 동기화를 통해 Active Directory에서 Azure AD로 데이터 흐름을 완료합니다. 동기화가 중단되면 Azure AD에서 새 직원 레코드를 사용할 수 없습니다.

## <a name="use-cloud-groups-for-collaboration-and-access"></a>협업 및 액세스에 클라우드 그룹 사용

클라우드 그룹을 사용하여 온-프레미스 인프라에서 협업과 액세스를 분리할 수 있습니다.

* **협업**: 최신 협업을 위해 Microsoft 365 Groups와 Microsoft Teams를 사용합니다. 온-프레미스 배포 목록 서비스를 해제하고 [Outlook에서 배포 목록을 Microsoft 365 그룹으로 업그레이드](/office365/admin/manage/upgrade-distribution-lists)합니다.

* **액세스**: Azure AD 보안 그룹 또는 Microsoft 365 그룹을 사용하여 Azure AD에서 애플리케이션에 대한 액세스 권한을 부여합니다.
* **Office 365 라이선스**: 그룹 기반 라이선스로 클라우드 전용 그룹을 사용하여 Office 365에 프로비저닝합니다. 이 메서드는 온-프레미스 인프라에서 그룹 멤버 자격 제어를 분리합니다.

액세스에 사용되는 그룹 소유자는 온-프레미스 손상의 멤버 자격 인수를 방지하기 위해 권한 있는 ID로 간주해야 합니다.
인수에는 온-프레미스 그룹 멤버 자격 조작 또는 Microsoft 365의 동적 그룹 멤버 자격에 영향을 줄 수 있는 온-프레미스 특성 조작이 포함됩니다.

## <a name="manage-devices-from-the-cloud"></a>클라우드에서 장치 관리


Azure AD 기능을 사용하여 디바이스를 안전하게 관리합니다.

-   **Windows 10 워크스테이션 사용**: MDM 정책을 사용하여 [Azure AD 조인 디바이스를 배포](../devices/azureadjoin-plan.md)합니다. 완전히 자동화된 프로비저닝 환경을 위해 [Windows Autopilot](/mem/autopilot/windows-autopilot)을 사용합니다.

    -   Windows 8.1 및 이전 버전을 실행하는 머신 사용을 중단합니다.

    -   서버 OS 머신을 워크스테이션으로 배포하지 않습니다.

    -   모든 디바이스 관리 워크로드에 대한 권한의 원본으로 [Microsoft Intune](https://www.microsoft.com/microsoft-365/enterprise-mobility-security/microsoft-intune) 을 사용합니다.

-   [**권한 있는 액세스 디바이스 배포**](/security/compass/privileged-access-devices#device-roles-and-profiles): 권한 있는 액세스를 사용하여 [권한 있는 액세스 보호](/security/compass/overview)에 대한 완전한 접근 방식의 일부로 Microsoft 365와 Azure AD를 관리합니다.

## <a name="workloads-applications-and-resources"></a>워크로드, 애플리케이션, 리소스 

-   **온-프레미스 SSO(single sign-on) 시스템** 

    모든 온-프레미스 페더레이션 및 웹 액세스 관리 인프라를 사용 중단합니다. Azure AD를 사용하도록 애플리케이션을 구성합니다.  

-   **최신 인증 프로토콜을 지원하는 SaaS 및 LOB(line-of-business) 애플리케이션** 

    [SSO에 Azure AD를 사용합니다](../manage-apps/what-is-single-sign-on.md). 인증을 위해 Azure AD를 사용하도록 구성하는 앱이 많을수록 온-프레미스 손상의 위험이 줄어듭니다.


* **레거시 애플리케이션** 

   * 최신 인증을 지원하지 않는 레거시 애플리케이션에 대한 인증, 권한 부여, 원격 액세스를 사용할 수 있습니다. [Azure AD 애플리케이션 프록시](../app-proxy/application-proxy.md)를 사용합니다. [보안 하이브리드 액세스 파트너 통합](../manage-apps/secure-hybrid-access.md)을 사용하여 네트워크 또는 애플리케이션 제공 컨트롤러 솔루션을 통해 사용할 수도 있습니다.   

   * 최신 인증을 지원하는 VPN 공급업체를 선택합니다. 인증을 Azure AD와 통합합니다. 온-프레미스 손상에서는 Azure AD를 사용하여 VPN을 사용하지 않음으로써 액세스를 사용하지 않거나 차단할 수 있습니다.

*  **애플리케이션 및 워크로드 서버**

   * 필요한 서버를 Azure IaaS(infrastructure as a service)로 마이그레이션할 수 있는 애플리케이션 또는 리소스입니다. [Azure AD Domain Services](../../active-directory-domain-services/overview.md)(Azure AD DS)를 사용하여 Active Directory의 온-프레미스 인스턴스에 대한 트러스트 및 종속성을 분리합니다. 분리를 위해 Azure AD DS에 사용되는 가상 네트워크가 회사 네트워크에 연결되어 있지 않은지 확인합니다.

   * [자격 증명 계층화](/security/compass/privileged-access-access-model#ADATM_BM)에 대한 지침을 따르세요. 애플리케이션 서버는 일반적으로 계층 1 자산으로 간주합니다.

## <a name="conditional-access-policies"></a>조건부 액세스 정책

Azure AD 조건부 액세스를 사용하여 신호를 해석하고 이를 통해 인증 결정을 내립니다. 자세한 내용은 [조건부 액세스 배포 계획](../conditional-access/plan-conditional-access.md)을 참조하세요.

* 가능한 경우 조건부 액세스를 사용하여 [레거시 인증 프로토콜을 차단](../conditional-access/howto-conditional-access-policy-block-legacy.md)합니다. 또한 애플리케이션 관련 구성을 사용하여 애플리케이션 수준에서 레거시 인증 프로토콜을 사용하지 않습니다.

   자세한 내용은 [레거시 인증 프로토콜](../fundamentals/auth-sync-overview.md)을 참조하세요. 또는 [Exchange online](/exchange/clients-and-mobile-in-exchange-online/disable-basic-authentication-in-exchange-online#how-basic-authentication-works-in-exchange-online) 및 [SharePoint online](/powershell/module/sharepoint-online/set-spotenant)에 관한 자세한 내용을 확인하세요.

* 권장 [ID 및 디바이스 액세스 구성을](/microsoft-365/security/office-365-security/identity-access-policies) 구현합니다.

* 조건부 액세스를 포함하지 않는 Azure AD의 버전을 사용하는 경우 [Azure AD 보안 기본값](../fundamentals/concept-fundamentals-security-defaults.md)을 사용하고 있는지 확인합니다.

   Azure AD 기능 라이선스에 대한 자세한 내용은 [Azure AD 가격 책정 가이드](https://www.microsoft.com/security/business/identity-access-management/azure-ad-pricing)를 참조하세요.

## <a name="monitor"></a>모니터 

온-프레미스 손상으로부터 Microsoft 365를 보호하도록 환경을 구성한 후 환경을 [사전 모니터링](../reports-monitoring/overview-monitoring.md)합니다.
### <a name="scenarios-to-monitor"></a>모니터링할 시나리오

조직과 관련된 시나리오 외에도 다음과 같은 주요 시나리오를 모니터링합니다. 예를 들어 중요 비즈니스용 애플리케이션 및 리소스에 대한 액세스를 사전에 모니터링해야 합니다.

* **의심되는 활동** 

    의심스러운 활동에 대한 모든 [ Azure AD 위험 이벤트](../identity-protection/overview-identity-protection.md#risk-detection-and-remediation)를 모니터링합니다. [Azure AD ID 보호](../identity-protection/overview-identity-protection.md)는 기본적으로 Azure Security Center와 통합됩니다.

    위치 기반 신호의 소음 검색을 방지하기 위해 네트워크 [명명된 위치](../conditional-access/location-condition.md)를 정의합니다. 
*  **UEBA(사용자 및 엔터티 동작 분석) 경고** 

    UEBA를 사용하여 변칙 검색에 대한 인사이트를 얻습니다.
    * MCAS(Microsoft Cloud App Security)는 [클라우드에 UEBA](/cloud-app-security/tutorial-ueba)를 제공합니다.

    * [Azure ATP(Advanced Threat Protection)에서 온-프레미스 UEBA를 통합](/defender-for-identity/install-step2)할 수 있습니다. MCAS는 Azure AD ID 보호의 신호를 읽습니다. 

* **응급 액세스 계정 작업** 

    [응급 액세스 계정을](../roles/security-emergency-access.md) 사용하는 모든 액세스를 모니터링합니다. 조사에 대한 경고를 만듭니다. 이 모니터링에는 다음이 포함되어야 합니다. 

   * 로그인. 

   * 자격 증명 관리. 

   * 그룹 멤버 자격에 대한 모든 업데이트. 

   * 애플리케이션 할당. 
* **권한 있는 역할 작업**

    [Azure AD PIM(Privileged Identity Management)에서 생성된 보안 경고](../privileged-identity-management/pim-how-to-configure-security-alerts.md?tabs=new#security-alerts)를 구성하고 검토합니다.
    사용자가 직접 할당될 때마다 경고를 생성하여 PIM 외부의 권한 있는 역할에 대한 직접 할당을 모니터링합니다.

* **Azure AD 테넌트 전체 구성**

    테넌트 전체 구성에 변경 내용이 발생하면 시스템에서 경고를 생성해야 합니다. 해당 변경사항에 포함되는 사항은 다음과 같으며 이에 국한되지 않습니다.

  *  사용자 지정 도메인이 업데이트됨.  

  * Azure AD B2B가 허용 목록 및 차단 목록으로 변경됩니다.

  * Azure AD B2B는 허용되는 ID 공급자(직접 페더레이션 또는 소셜 로그인을 통한 SAML ID 공급자)로 변경됩니다.  

  * 조건부 액세스 또는 위험 정책이 변경됩니다. 

* **애플리케이션 및 서비스 주체 개체**
    
   * 새 애플리케이션 또는 서비스 주체는 조건부 액세스 정책을 필요로 할 수 있습니다. 

   * 서비스 주체에 자격 증명이 추가되었습니다.
   * 애플리케이션 동의 활동입니다. 

* **사용자 지정 역할**
   * 사용자 지정 역할 정의를 업데이트합니다. 

   * 새로 만든 사용자 지정 역할입니다. 

### <a name="log-management"></a>로그 관리

안정적인 도구 세트를 지원하기 위해 로그 스토리지 및 보존 전략, 디자인 및 구현을 정의합니다. 예를 들어 Azure Sentinel, 일반적인 쿼리, 조사 및 법적 고 플레이북과 같은 SIEM(보안 정보 및 이벤트 관리) 시스템을 고려할 수 있습니다.

* **Azure AD 로그**: 진단, 로그 보존, SIEM 수집 등 설정 관련 모범 사례를 일관되게 준수하여 생성된 로그 및 신호를 수집합니다. 

    로그 전략에는 다음 Azure AD 로그가 포함되어야 합니다.
   * 로그인 작업 

   * 감사 로그 

   * 위험 이벤트 

    Azure AD는 로그인 활동 로그 및 감사 로그에 대한 [Azure Monitor 통합](../reports-monitoring/concept-activity-logs-azure-monitor.md)을 제공합니다. 위험 이벤트는 [Microsoft Graph API](/graph/api/resources/identityprotection-root)를 통해 수집할 수 있습니다. [Azure AD 로그를 Azure Monitor 로그로 스트리밍](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)할 수 있습니다.

* **하이브리드 인프라 OS 보안 로그**: 모든 하이브리드 ID 인프라 OS 로그는 노출 영역 영향으로 인해 계층 0 시스템으로 보관하고 신중하게 모니터링해야 합니다. 다음 요소를 포함합니다. 

   *  Azure AD Connect. ID 동기화를 모니터링하려면 [Azure AD Connect Health](../hybrid/whatis-azure-ad-connect.md)를 배포해야 합니다.

   *  애플리케이션 프록시 에이전트 


   * 비밀번호 쓰기 저장 에이전트 

   * 암호 보호 게이트웨이 머신  

   * Azure AD 다단계 인증 RADIUS 확장이 있는 NPSs(네트워크 정책 서버) 

## <a name="next-steps"></a>다음 단계
* [Azure AD를 사용하여 ID 및 액세스 관리에 대한 복원력 빌드](resilience-overview.md)

* [리소스에 대한 외부 액세스 보호](secure-external-access-resources.md) 
* [모든 앱을 Azure AD와 통합](five-steps-to-full-application-integration-with-azure-ad.md)
