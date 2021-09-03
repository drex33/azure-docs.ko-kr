---
title: Azure Virtual Desktop에 대한 Azure 보안 기준
description: Azure Virtual Desktop 보안 기준은 Azure Security Benchmark에 지정된 보안 권장 사항을 구현하기 위한 절차 지침과 리소스를 제공합니다.
author: msmbaldwin
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 01/25/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: b9a0d2b785f98555d723c81c6beecaa85313881e
ms.sourcegitcommit: f2eb1bc583962ea0b616577f47b325d548fd0efa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/28/2021
ms.locfileid: "114728660"
---
# <a name="azure-security-baseline-for-azure-virtual-desktop"></a>Azure Virtual Desktop에 대한 Azure 보안 기준

이 보안 기준은 [Azure Security Benchmark 버전 2.0](../security/benchmarks/overview.md)의 지침을 Azure Virtual Desktop에 적용합니다. Azure Security Benchmark는 Azure에서 클라우드 솔루션을 보호하는 방법에 대한 권장 사항을 제공합니다. 콘텐츠는 Azure Security Benchmark에서 정의한 **보안 컨트롤** 및 Azure Virtual Desktop에 적용되는 관련 지침에 따라 그룹화됩니다. Azure Virtual Desktop에 적용되지 않는 **컨트롤** 은 제외되었습니다.

Azure Virtual Desktop 서비스에는 서비스 자체, 다중 세션 가상 SKU를 위한 Windows 10 Enterprise 및 FSLogix가 포함되어 있습니다. FSLogix 관련 보안 권장 사항은 [스토리지의 보안 기준](../storage/common/security-baseline.md)을 참조하세요. 이 서비스의 에이전트는 가상 머신에서 실행되지만 가상 머신의 모든 제어 권한이 고객에게 있으므로 [컴퓨팅에 대한 보안 권장 사항](../virtual-machines/windows/security-baseline.md)을 따릅니다.

Azure Virtual Desktop이 완전히 Azure Security Benchmark에 매핑되는 방법을 보려면 [전체 Azure Virtual Desktop 보안 기준 매핑 파일](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)을 참조하세요.

## <a name="network-security"></a>네트워크 보안

자세한 내용은 [Azure Security Benchmark: 네트워크 보안](../security/benchmarks/security-controls-v2-network-security.md)을 참조하세요.

### <a name="ns-1-implement-security-for-internal-traffic"></a>NS-1: 내부 트래픽에 대한 보안 구현

**지침**: Azure Virtual Desktop에 등록할 가상 머신을 배포할 때 가상 네트워크를 새로 만들거나 기존 가상 네트워크를 사용해야 합니다. 모든 Azure 가상 네트워크가 비즈니스 위험에 적합한 엔터프라이즈 구분 원칙을 준수해야 합니다. 조직에 더 높은 위험을 초래할 수 있는 모든 시스템은 자체 가상 네트워크 내에서 격리되어야 하고 네트워크 보안 그룹 또는 Azure Firewall로 충분히 보호되어야 합니다.

Azure Security Center의 적응형 네트워크 강화 기능을 사용하여 포트와 원본 IP를 외부 네트워크 트래픽 규칙에 대한 참조로 제한하는 네트워크 보안 그룹 구성을 권장합니다.

애플리케이션 및 엔터프라이즈 분할 전략에 따라 네트워크 보안 그룹 규칙을 기반으로 내부 리소스 간의 트래픽을 제한하거나 허용합니다. 잘 정의된 특정 애플리케이션(예: 3계층 앱)의 경우 이는 매우 안전한 "기본적으로 거부, 예외를 통해 허용" 방식이 될 수 있습니다. 많은 애플리케이션과 엔드포인트가 서로 상호 작용하는 경우에는 스케일링이 원활하지 않을 수 있습니다. 수많은 엔터프라이즈 세그먼트 또는 스포크(허브/스포크 토폴로지의)를 중앙에서 관리해야 하는 경우 Azure Firewall을 사용할 수도 있습니다.

가상 머신(Azure Virtual Desktop의 일부) 서브넷에 연결된 네트워크 보안 그룹의 경우 특정 엔드포인트로 나가는 트래픽을 허용해야 합니다. 

- [Azure Virtual Desktop에 대한 액세스를 허용하는 데 필요한 URL](safe-url-list.md)

- [Azure Security Center의 적응형 네트워크 강화](../security-center/security-center-adaptive-network-hardening.md) 

- [Azure Virtual Desktop용 Azure Firewall](../firewall/protect-windows-virtual-desktop.md)

- [보안 규칙을 사용하여 네트워크 보안 그룹을 만드는 방법](../virtual-network/tutorial-filter-network-traffic.md)

 

- [Azure Firewall 배포 및 구성 방법](../firewall/tutorial-firewall-deploy-portal.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="ns-2-connect-private-networks-together"></a>NS-2: 여러 사설망 연결

**지침**: Azure ExpressRoute 또는 Azure VPN(가상 사설망)을 사용하면 공동 배치 환경의 온-프레미스 인프라와 Azure 데이터 센터와 간에 프라이빗 연결을 만들 수 있습니다. ExpressRoute 연결은 공용 인터넷을 사용하지 않으며 일반적인 인터넷 연결보다 안정적이고 속도가 빠르며 대기 시간이 짧습니다. 

지점 및 사이트 간 VPN(가상 사설망)과 사이트 간 VPN(가상 사설망)의 경우 VPN(가상 사설망) 옵션과 Azure ExpressRoute 조합을 사용하여 온-프레미스 디바이스 또는 네트워크를 가상 네트워크에 연결할 수 있습니다.

Azure에서 두 개 이상의 가상 네트워크를 연결하려면 가상 네트워크 피어링을 사용합니다. 피어링된 가상 네트워크 간의 네트워크 트래픽은 비공개이며 Azure 백본 네트워크에 보관됩니다.

- [ExpressRoute 연결 모델은 무엇인가요?](../expressroute/expressroute-connectivity-models.md) 

- [Azure VPN 개요](../vpn-gateway/vpn-gateway-about-vpngateways.md) 

- [가상 네트워크 피어링](../virtual-network/virtual-network-peering-overview.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="ns-4-protect-applications-and-services-from-external-network-attacks"></a>NS-4: 외부의 네트워크 공격으로부터 애플리케이션 및 서비스 보호

**지침**: Azure Firewall을 사용하면 인터넷 및 기타 외부 위치의 잠재적인 악성 트래픽으로부터 애플리케이션 및 서비스를 보호할 수 있습니다. 분산 서비스 거부 공격, 애플리케이션 관련 공격, 원치 않는 잠재적 악성 인터넷 트래픽 등의 외부 네트워크의 공격으로부터 Azure Virtual Desktop 리소스를 보호합니다. Azure Virtual Networks에서 DDoS 표준 보호를 사용하도록 설정하여 분산형 서비스 거부 공격으로부터 자산을 보호합니다. Azure Security Center를 사용하여 네트워크 관련 리소스와 관련된 구성 오류 위험을 감지합니다.

Azure Virtual Desktop은 웹 애플리케이션을 실행하기 위한 것이 아니며, 웹 애플리케이션을 대상으로 하는 외부 네트워크 공격으로부터 보호하기 위해 추가 설정을 구성하거나 추가 네트워크 서비스를 배포할 필요가 없습니다.

- [Azure Firewall 설명서](../firewall/index.yml)

- [Azure Portal을 사용하여 Azure DDoS Protection 표준 관리](../ddos-protection/manage-ddos-protection.md) 

- [Azure Security Center 권장 사항](../security-center/recommendations-reference.md#networking-recommendations)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="ns-5-deploy-intrusion-detectionintrusion-prevention-systems-idsips"></a>NS-5: IDS/IPS(침입 검색 시스템/침입 방지 시스템) 배포

**참고 자료**: 위협 인텔리전스 기반 필터링과 함께 Azure Firewall을 사용하여 알려진 악성 IP 주소 및 도메인과 주고 받는 트래픽에 대해 경고하고 필요에 따라 트래픽을 차단합니다. IP 주소 및 도메인은 Microsoft 위협 인텔리전스 피드에서 제공됩니다. 페이로드 검사가 필요한 경우 Azure Marketplace에서 타사 침입 감지 또는 방지 솔루션을 배포할 수 있습니다. 

침입 감지 또는 방지 솔루션을 사용해야 하는 규정 또는 기타 요구 사항이 있는 경우 SIEM(보안 정보 및 이벤트 관리) 솔루션에 유용한 경고를 제공하도록 항상 솔루션을 튜닝해야 합니다.

- [Azure Firewall을 배포하는 방법](../firewall/tutorial-firewall-deploy-portal.md) 

- [Azure Marketplace에 있는 타사 IDS 기능](https://azuremarketplace.microsoft.com/marketplace?search=IDS) 

- [Microsoft Defender ATP EDR 기능](/bs-cyrl-ba/windows/security/threat-protection/microsoft-defender-atp/overview-endpoint-detection-response)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="ns-6-simplify-network-security-rules"></a>NS-6: 네트워크 보안 규칙 간소화

**지침**: Azure Virtual Network 서비스 태그를 사용하여 Azure Virtual Desktop 리소스에 대해 구성된 네트워크 보안 그룹 또는 Azure Firewall에서 네트워크 액세스 제어를 정의합니다. 보안 규칙을 만들 때 특정 IP 주소 대신 서비스 태그를 사용할 수 있습니다. 서비스 태그 이름(예: WindowsVirtualDesktop)을 규칙의 적절한 원본 또는 대상 필드에 지정하면 해당 서비스에 대한 트래픽을 허용하거나 거부할 수 있습니다. Microsoft에서는 서비스 태그에서 압축한 주소 접두사를 관리하고 주소를 변경하는 대로 서비스 태그를 자동으로 업데이트합니다.

- [서비스 태그 이해 및 사용](../virtual-network/service-tags-overview.md)

- [Azure Virtual Desktop 서비스 태그에서 다루는 내용에 대한 자세한 정보](safe-url-list.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

## <a name="identity-management"></a>ID 관리

자세한 내용은 [Azure Security Benchmark: ID 관리](../security/benchmarks/security-controls-v2-identity-management.md)를 참조하세요.

### <a name="im-1-standardize-azure-active-directory-as-the-central-identity-and-authentication-system"></a>IM-1: Azure Active Directory를 중앙 ID 및 인증 시스템으로 표준화

**지침**: Azure Virtual Desktop은 기본 ID 및 액세스 관리 서비스로 Azure AD(Azure Active Directory)를 사용합니다. Azure AD를 표준화하여 다음 리소스에서 조직의 ID 및 액세스 관리를 관리해야 합니다.

- Microsoft 클라우드 리소스(예: Azure Portal, Azure Storage, Azure Virtual Machines(Linux 및 Windows), Azure Key Vault, PaaS 및 SaaS 애플리케이션)

- 조직의 리소스(예: Azure의 애플리케이션 또는 회사 네트워크 리소스)

Azure AD 보안은 조직의 클라우드 보안 사례에서 높은 우선 순위여야 합니다. Azure AD는 Microsoft의 모범 사례 권장 사항을 기준으로 ID 보안 태세를 평가하는 데 도움이 되는 ID 보안 점수를 제공합니다. 점수를 사용하여 구성이 모범 사례 권장 사항에 얼마나 근접하게 일치하는지 측정하고 보안 태세를 개선할 수 있습니다.

Azure AD는 Microsoft 계정이 없는 사용자가 애플리케이션 및 리소스에 로그인할 수 있는 외부 ID를 지원합니다.

- [Azure AD의 테넌트](../active-directory/develop/single-and-multi-tenant-apps.md)

- [애플리케이션에 외부 ID 공급자 사용](../active-directory/external-identities/identity-providers.md)

- [Azure AD의 ID 보안 점수란?](../active-directory/fundamentals/identity-secure-score.md)

- [Azure Virtual Desktop을 운영하는 데 필요한 역할](/azure/virtual-desktop/faq#what-are-the-minimum-admin-permissions-i-need-to-manage-objects)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="im-2-manage-application-identities-securely-and-automatically"></a>IM-2: 애플리케이션 ID를 안전하게 자동으로 관리

**지침**: Azure Virtual Desktop은 서비스 또는 자동화 같은 사람이 아닌 계정에 대해 Azure 관리 ID를 지원합니다. 보다 강력한 사용자 계정을 만드는 대신 Azure 관리 ID 기능을 사용하여 리소스에 액세스하거나 실행하는 것이 좋습니다. 

Azure Virtual Desktop에서는 Azure AD(Azure Active Directory)를 사용하여 리소스 수준에서 인증서 자격 증명을 사용하여 서비스 주체를 구성하고 클라이언트 비밀을 대체하는 제한된 권한이 있는 서비스 주체를 만드는 것이 좋습니다. 두 경우 모두 Azure 관리 ID와 함께 Azure Key Vault를 사용하므로 런타임 환경(예: Azure Function)이 키 자격 증명 모음에서 자격 증명을 검색할 수 있습니다.

- [Azure 리소스에 대한 관리 ID를 지원하는 서비스](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)

- [Azure 서비스 주체](/powershell/azure/create-azure-service-principal-azureps) 

- [인증서를 사용하여 서비스 주체 만들기](../active-directory/develop/howto-authenticate-service-principal-powershell.md) 

- [보안 주체 등록에 Azure Key Vault 사용](../key-vault/general/authentication.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="im-3-use-azure-ad-single-sign-on-sso-for-application-access"></a>IM-3: 애플리케이션 액세스에 Azure AD SSO(Single Sign-On) 사용

**지침**: Azure Virtual Desktop은 Azure AD(Azure Active Directory)를 사용하여 Azure 리소스, 클라우드 애플리케이션 및 온-프레미스 애플리케이션에 대한 ID 및 액세스 관리를 제공합니다. 여기에는 엔터프라이즈 ID(예: 직원) 및 외부 ID(예: 파트너, 공급업체 및 공급자)가 포함됩니다. 이를 통해 SSO(Single Sign-On)가 온-프레미스 및 클라우드에서 조직의 데이터와 리소스에 대한 액세스를 관리하고 보호할 수 있습니다. 원활하고 안전한 액세스와 향상된 가시성 및 제어를 위해 모든 사용자, 애플리케이션, 디바이스를 Azure AD에 연결합니다.

- [Azure AD에서의 애플리케이션 SSO 이해](../active-directory/manage-apps/what-is-single-sign-on.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="im-4-use-strong-authentication-controls-for-all-azure-active-directory-based-access"></a>IM-4: 모든 Azure Active Directory 기반 액세스에 강력한 인증 제어 사용 

**지침**: Azure Virtual Desktop은 다단계 인증과 강력한 암호 없는 방법을 통해 강력한 인증 컨트롤을 지원하는 Azure AD(Azure Active Directory)를 사용합니다.

- 다단계 인증 - Azure AD 다단계 인증을 사용하도록 설정하고 다단계 인증 설정의 몇 가지 모범 사례에 대한 Azure Security Center의 액세스 관리 권장 사항을 따릅니다. 다단계 인증은 로그인 조건 및 위험 요소에 따라 모든 사용자, 선택한 사용자 또는 사용자 단위 수준에서 적용할 수 있습니다.

- 암호 없는 인증 – 세 가지 암호 없는 인증 옵션을 사용할 수 있습니다. Windows Hello for Business, Microsoft Authenticator 앱 및 스마트 카드와 같은 온-프레미스 인증 방법입니다.

Azure Virtual Desktop는 기본 암호 정책이 있는 클라우드 전용 계정(Azure에서 직접 만든 사용자 계정) 또는 하이브리드 계정(온-프레미스 암호 정책을 따르는 온-프레미스 Azure AD의 사용자 계정)과 같은 레거시 암호 기반 인증을 지원합니다. 암호 기반 인증을 사용하는 경우 Azure AD는 사용자가 추측하기 쉬운 암호를 설정하지 못하게 하는 암호 보호 기능을 제공합니다. Microsoft는 원격 분석을 기반으로 하여 업데이트되는 금지된 암호의 전체 목록을 제공하며, 고객은 필요(예: 브랜드, 문화 참조 등)에 따라 목록을 확장할 수 있습니다. 이 암호 보호는 클라우드 전용 및 하이브리드 계정에 사용할 수 있습니다.

암호 자격 증명에만 기반한 인증은 일반적인 공격 방법의 영향을 받기 쉽습니다. 보안을 강화하기 위해 다단계 인증이나 강력한 암호 정책과 같은 강력한 인증을 사용합니다. 기본 암호가 있을 수 있는 타사 애플리케이션 및 마켓플레이스 서비스의 경우 서비스 초기 설정에서 암호를 변경해야 합니다.

관리자 및 권한 있는 사용자의 경우 가장 높은 수준의 강력한 인증 방법을 사용하고 다른 사용자에게 적절한 강력한 인증 정책을 롤아웃해야 합니다.

- [Azure Active Directory에 대한 암호 없는 인증 옵션 소개](../active-directory/authentication/concept-authentication-passwordless.md) 

- [Azure AD 기본 암호 정책](../active-directory/authentication/concept-sspr-policy.md#password-policies-that-only-apply-to-cloud-user-accounts) 

- [Azure Active Directory 암호 보호를 사용하여 잘못된 암호 제거](../active-directory/authentication/concept-password-ban-bad.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="im-5-monitor-and-alert-on-account-anomalies"></a>IM-5: 비정상 계정 활동 모니터링 및 경고

**지침**: Azure Virtual Desktop이 다음과 같은 데이터 원본을 제공하는 Azure AD(Azure Active Directory)와 통합됩니다.

- 로그인 – 로그인 보고서는 관리형 애플리케이션 및 사용자 로그인 활동의 사용에 대한 정보를 제공합니다.

- 감사 로그 - Azure AD 내의 다양한 기능에 의해 수행된 모든 변경 내용에 대한 로그를 통한 추적 기능을 제공합니다. 감사 로그의 예제로는 사용자, 앱, 그룹, 역할 및 정책 추가 또는 제거와 같은 Azure AD 내의 모든 리소스에 대한 변경 내용이 있습니다.

- 위험한 로그인 - 위험한 로그인은 사용자 계정의 정당한 소유자가 아닌 사용자에 의해 수행된 로그인 시도에 대한 지표입니다.

- 위험 플래그가 지정된 사용자 - 위험한 사용자는 손상되었을 수 있는 사용자 계정에 대한 표시기입니다.

이러한 데이터 원본은 Azure Monitor, Azure Sentinel 또는 타사 SIEM(보안 정보 및 이벤트 관리) 시스템과 통합할 수 있습니다. 또한 Azure Security Center는 과도한 인증 시도 실패 횟수, 구독에서 사용되지 않는 계정과 같은 의심스러운 특정 활동에 대해 경고할 수 있습니다. Azure ATP(Advanced Threat Protection)는 Active Directory 신호를 사용하여 지능형 위협, 손상된 ID 및 악의적인 참가자 작업을 식별, 탐지 및 조사할 수 있는 보안 솔루션입니다.

- [Azure AD에서 작업 보고서 감사](../active-directory/reports-monitoring/concept-audit-logs.md)

- [Azure AD 위험한 로그인을 확인하는 방법](../active-directory/identity-protection/overview-identity-protection.md)

- [Azure Security Center 위협 인텔리전스 보호 모듈의 경고](../security-center/alerts-reference.md)

- [Azure 활동 로그를 Azure Monitor에 통합하는 방법](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="im-6-restrict-azure-resource-access-based-on-conditions"></a>IM-6: 조건에 따라 Azure 리소스 액세스 제한

**지침**: Azure Virtual Desktop은 사용자 정의 조건에 따라 액세스 권한을 세밀하게 제어할 수 있도록 Azure AD(Azure Active Directory)를 사용하여 조건부 액세스를 지원합니다. 예를 들어 특정 IP 범위의 사용자 로그인에는 다단계 인증을 사용하도록 요구할 수 있습니다. 

또한 세밀한 인증 세션 관리 정책을 여러 사용 사례에 사용할 수 있습니다.

- [Azure 조건부 액세스 개요](../active-directory/conditional-access/overview.md) 

- [일반적인 조건부 액세스 정책](../active-directory/conditional-access/concept-conditional-access-policy-common.md) 

- [조건부 액세스를 사용하여 인증 세션 관리 구성](../active-directory/conditional-access/howto-conditional-access-session-lifetime.md) 

- [Azure Virtual Desktop 관련 조건부 액세스 설정 정보](set-up-mfa.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

## <a name="privileged-access"></a>권한 있는 액세스

자세한 내용은 [Azure Security Benchmark: 권한 있는 액세스](../security/benchmarks/security-controls-v2-privileged-access.md)를 참조하세요.

### <a name="pa-2-restrict-administrative-access-to-business-critical-systems"></a>PA-2: 중요 비즈니스용 시스템에 대한 관리 액세스 제한

**지침**: Azure Virtual Desktop은 Azure RBAC(Azure 역할 기반 액세스 제어)를 사용하여 중요 비즈니스용 시스템에 대한 액세스를 격리합니다. 또한 업무상 중요한 시스템에 설치된 에이전트를 사용하여 Active Directory 도메인 컨트롤러, 보안 도구 및 시스템 관리 도구와 같이 중요 비즈니스용 액세스에 대한 관리자 권한이 있는 관리, ID 및 보안 시스템에 대한 액세스를 제한해야 합니다. 이러한 관리 및 보안 시스템을 손상시키는 공격자가 중요 비즈니스용 자산을 손상시키기 위해 시스템을 즉시 무기화할 수 있습니다.

모든 형식의 액세스 제어는 일관된 액세스 제어를 보장하기 위해 엔터프라이즈 조각화 전략에 맞춰야 합니다.

- [Azure 구성 요소 및 참조 모델](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151) 

- [관리 그룹 액세스](../governance/management-groups/overview.md#management-group-access) 

- [Azure 구독 관리자](../cost-management-billing/manage/add-change-subscription-administrator.md)

- [Azure Virtual Desktop을 관리하는 데 필요한 최소 관리자 권한](/azure/virtual-desktop#what-are-the-minimum-admin-permissions-i-need-to-manage-objects)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="pa-3-review-and-reconcile-user-access-regularly"></a>PA-3: 정기적으로 사용자 액세스 검토 및 조정

**지침**: Azure Virtual Desktop은 Azure AD(Azure Active Directory) 계정을 사용하여 리소스를 관리하고, 사용자 계정 및 액세스 할당을 정기적으로 검토하여 계정 및 해당 액세스를 사용할 수 있도록 합니다.

Azure AD 액세스 검토를 사용하여 그룹 멤버 자격, 엔터프라이즈 애플리케이션에 대한 액세스 및 역할 할당을 검토하세요. Azure AD 보고는 부실 계정을 검색하는 데 도움이 되는 로그를 제공할 수 있습니다.

또한 Azure Privileged Identity Management는 과도한 수의 관리자 계정이 만들어질 때 경고하고, 부실하거나 부적절하게 구성된 관리자 계정을 식별하도록 구성할 수 있습니다.

일부 Azure 서비스는 Azure AD를 통해 관리되지 않는 로컬 사용자 및 역할을 지원합니다. 해당 사용자는 별도로 관리해야 합니다.

- [Azure Virtual Desktop의 기본 제공 역할](rbac.md)

- [PIM(Privileged Identity Management)에서 Azure 리소스 역할의 액세스 검토 만들기](../active-directory/privileged-identity-management/pim-resource-roles-start-access-review.md)

- [Azure AD ID 및 액세스 검토를 사용하는 방법](../active-directory/governance/access-reviews-overview.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="pa-4-set-up-emergency-access-in-azure-ad"></a>PA-4: Azure AD에서 응급 액세스 설정

**지침**: Azure Virtual Desktop은 Azure AD(Azure Active Directory)를 사용하여 리소스를 관리합니다. Azure AD 조직이 실수로 잠기는 것을 방지하려면 일반 관리 계정을 사용할 수 없을 경우 액세스를 위한 응급 액세스 계정을 설정합니다. 응급 액세스 계정은 일반적으로 권한이 높으며 특정 사용자에게 할당되면 안 됩니다. 응급 액세스 계정은 일반 관리 계정을 사용할 수 없는 '비상' 시나리오의 긴급한 상황으로 제한됩니다.

응급 액세스 계정의 자격 증명(예: 암호, 인증서 또는 스마트 카드)을 안전하게 유지하고 비상시에만 사용할 권한이 있는 사용자에게만 알립니다.

- [Azure AD에서 응급 액세스 계정 관리](../active-directory/roles/security-emergency-access.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="pa-5-automate-entitlement-management"></a>PA-5: 권한 관리 자동화 

**지침**: Azure Virtual Desktop은 Azure AD(Azure Active Directory)와 통합되어 리소스를 관리합니다. Azure AD 권한 관리 기능을 사용하여 액세스 할당, 검토, 만료 등의 액세스 요청 워크플로를 자동화할 수 있습니다. 이중 또는 다단계 승인도 지원됩니다.

- [Azure AD 액세스 검토란?](../active-directory/governance/access-reviews-overview.md) 

- [Azure AD 권한 관리란?](../active-directory/governance/entitlement-management-overview.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="pa-6-use-privileged-access-workstations"></a>PA-6: 권한 있는 액세스 워크스테이션 사용

**지침**: 안전하고 격리된 워크스테이션은 관리자, 개발자 및 중요 서비스 운영자와 같은 중요한 역할의 보안에 매우 중요합니다. 관리 작업에는 매우 안전한 사용자 워크스테이션 및/또는 Azure Bastion을 사용합니다. 

Azure AD(Azure Active Directory), Microsoft Defender ATP(Advanced Threat Protection) 또는 Microsoft Intune을 사용하여 관리 작업을 위한 관리형 보안 사용자 워크스테이션을 배포합니다. 보안 워크스테이션을 중앙에서 관리하여 강력한 인증, 소프트웨어 및 하드웨어 기준, 제한된 논리적 액세스 및 네트워크 액세스를 비롯한 보안 구성을 적용할 수 있습니다.

- [권한 있는 액세스 워크스테이션 이해](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/) 

- [권한 있는 액세스 워크스테이션 배포](/security/compass/privileged-access-deployment)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="pa-7-follow-just-enough-administration-least-privilege-principle"></a>PA-7: 충분한 관리 수행(최소 권한 원칙) 

**지침**: Azure Virtual Desktop이 Azure RBAC(Azure 역할 기반 액세스 제어)와 통합되어 해당 리소스를 관리합니다. Azure RBAC를 사용하면 역할 할당을 통해 Azure 리소스 액세스를 관리할 수 있습니다. 이러한 역할은 사용자, 그룹 서비스 주체 및 관리 ID에 할당할 수 있습니다. 특정 리소스에 대해 미리 정의된 기본 제공 역할이 있으며 이러한 역할은 Azure CLI, Azure PowerShell 또는 Azure Portal과 같은 도구를 통해 쿼리하거나 인벤토리에 포함할 수 있습니다. 

Azure RBAC를 통해 리소스에 할당하는 권한은 항상 역할에 필요한 권한으로 제한해야 합니다. 이렇게 하면 PIM(Privileged Identity Management)의 JIT(Just-in-Time) 접근 방식을 Azure AD(Azure Active Directory)로 보완하는 효과가 있으며, 정기적으로 검토해야 합니다.

또한 기본 제공 역할을 사용하여 권한을 할당하고 필요한 경우에만 사용자 지정 역할을 만듭니다.

- [Azure RBAC(Azure 역할 기반 액세스 제어)는 무엇인가요?](../role-based-access-control/overview.md) 

- [Azure RBAC를 구성하는 방법](../role-based-access-control/role-assignments-portal.md) 

- [Azure AD ID 및 액세스 검토를 사용하는 방법](../active-directory/governance/access-reviews-overview.md)

- [Azure Virtual Desktop의 기본 제공 역할](rbac.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="pa-8-choose-approval-process-for-microsoft-support"></a>PA-8: Microsoft 지원에 대한 승인 프로세스 선택  

**지침**: Microsoft가 고객 데이터에 액세스해야 하는 지원 시나리오에서 Azure Virtual Desktop은 고객 Lockbox가 고객 데이터 액세스 요청을 검토, 승인 또는 거부할 수 있는 인터페이스를 제공합니다.

- [고객 Lockbox의 이해](../security/fundamentals/customer-lockbox-overview.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: 공유됨

## <a name="data-protection"></a>데이터 보호

자세한 내용은 [Azure Security Benchmark: 데이터 보호](../security/benchmarks/security-controls-v2-data-protection.md)를 참조하세요.

### <a name="dp-1-discovery-classify-and-label-sensitive-data"></a>DP-1: 중요한 데이터 검색, 분류 및 레이블 지정

**지침**: 적절한 컨트롤을 디자인할 수 있도록 중요한 데이터를 검색, 분류하고 레이블을 지정합니다. 이렇게 하면 중요한 정보가 조직의 기술 시스템을 통해 안전하게 저장, 처리 및 전송됩니다.

Azure, 온-프레미스, Office 365 및 기타 위치에 있는 Office 문서 내의 중요한 정보에 대해서는 Azure Information Protection(및 관련 검색 도구)을 사용합니다.

Azure SQL 정보 보호를 사용하여 Azure SQL Database에 저장된 정보의 분류 및 레이블 지정을 지원할 수 있습니다.

- [Azure Information Protection을 사용하여 중요한 정보 태그 지정](/azure/information-protection/what-is-information-protection) 

- [Azure SQL 데이터 검색을 구현하는 방법](../azure-sql/database/data-discovery-and-classification-overview.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="dp-2-protect-sensitive-data"></a>DP-2: 중요한 데이터 보호

**지침**: Azure RBAC(Azure 역할 기반 액세스 제어), 네트워크 기반 액세스 제어 및 Azure 서비스의 특정 제어(예: SQL 및 기타 데이터베이스의 암호화)를 통해 액세스를 제한하여 중요한 데이터를 보호합니다.

일관된 액세스 제어를 보장하려면 모든 유형의 액세스 제어를 엔터프라이즈 조각화 전략에 맞게 조정해야 합니다. 또한 엔터프라이즈 조각화 전략은 중요 비즈니스용 데이터 및 시스템의 위치를 통해 알려야 합니다.

Microsoft는 모든 고객 콘텐츠를 중요하게 취급하며 고객 데이터 손실 및 노출로부터 보호합니다. Azure 내의 고객 데이터를 안전하게 유지하기 위해 Microsoft는 몇 가지 기본 데이터 보호 제어 및 기능을 구현했습니다.

- [Azure RBAC(역할 기반 액세스 제어)](../role-based-access-control/overview.md) 

- [Azure의 고객 데이터 보호 이해](../security/fundamentals/protection-customer-data.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="dp-3-monitor-for-unauthorized-transfer-of-sensitive-data"></a>DP-3: 중요한 데이터의 무단 전송 모니터링

**지침**: 엔터프라이즈 가시성 및 제어를 벗어난 위치로의 무단 데이터 전송을 모니터링합니다. 여기에는 일반적으로 무단 데이터 반출을 나타낼 수 있는 비정상 활동(대량 또는 비정상 전송)에 대한 모니터링이 포함됩니다.

Azure Storage와 Azure SQL ATP를 모두 사용하는 ATP(Advanced Threat Protection) 기능은 중요한 정보가 무단으로 전송되었을 가능성을 시사하는 비정상적인 정보 전송에 대해 경고할 수 있습니다.

AIP(Azure Information Protection)는 분류되고 레이블이 지정된 정보에 대한 모니터링 기능을 제공합니다.

데이터 손실 방지 솔루션(예: 호스트 기반 솔루션)을 사용하여 감지 및/또는 방지 컨트롤을 적용하여 데이터 반출을 방지하세요.

- [Azure SQL ATP 사용](../azure-sql/database/threat-detection-overview.md) 

- [Azure Storage ATP 사용](../storage/common/azure-defender-storage-configure.md?tabs=azure-security-center)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

## <a name="asset-management"></a>자산 관리

자세한 내용은 [Azure Security Benchmark: 자산 관리](../security/benchmarks/security-controls-v2-asset-management.md)를 참조하세요.

### <a name="am-1-ensure-security-team-has-visibility-into-risks-for-assets"></a>AM-1: 보안 팀에서 자산 위험에 대한 가시성을 확보하도록 보장

**지침**: Azure Security Center를 사용하여 보안 위험을 모니터링할 수 있도록 Azure 테넌트 및 구독에서 보안 읽기 권한자 권한을 보안 팀에 부여합니다. 

보안 팀의 책임이 구성된 방식에 따라 보안 위험 모니터링은 중앙 보안 팀 또는 로컬 팀의 책임이 될 수 있습니다. 즉, 보안 인사이트 및 위험이 항상 조직 내의 중앙에서 집계되어야 합니다. 

보안 읽기 권한자 권한은 전체 테넌트(루트 관리 그룹)에 광범위하게 적용하거나 범위를 관리 그룹 또는 특정 구독으로 지정할 수 있습니다. 

워크로드 및 서비스에 대한 가시성을 얻으려면 추가 권한이 필요할 수 있습니다. 

- [보안 읽기 권한자 역할 개요](../role-based-access-control/built-in-roles.md#security-reader)

- [Azure 관리 그룹 개요](../governance/management-groups/overview.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="am-2-ensure-security-team-has-access-to-asset-inventory-and-metadata"></a>AM-2: 보안 팀에 자산 인벤토리 및 메타데이터에 대한 액세스 권한이 있는지 확인

**지침**: Azure 리소스, 리소스 그룹 및 구독에 태그를 적용하여 논리적으로 분류합니다. 각 태그는 이름과 값 쌍으로 이루어져 있습니다. 예를 들어 프로덕션의 모든 리소스에 "환경" 이름과 "프로덕션" 값을 적용할 수 있습니다.

Azure Virtual Machine 인벤토리를 사용하여 Virtual Machines의 소프트웨어에 대한 정보 수집을 자동화할 수 있습니다. 소프트웨어 이름, 버전, 게시자 및 새로 고침 시간은 Azure Portal에서 사용할 수 있습니다. 설치 날짜 및 기타 정보에 대한 액세스 권한을 가져오려면 게스트 수준 진단을 사용하도록 설정하고 Windows 이벤트 로그를 Log Analytics 작업 영역으로 가져와야 합니다.

- [Azure Resource Graph Explorer를 사용하여 쿼리를 만드는 방법](../governance/resource-graph/first-query-portal.md) 

- [Azure Security Center 자산 인벤토리 관리](../security-center/asset-inventory.md) 

- [리소스 명명 및 태그 지정 의사 결정 가이드](/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=%2fazure%2fazure-resource-manager%2fmanagement%2ftoc.json)

- [Azure 가상 머신 인벤토리를 사용하는 방법](../automation/automation-tutorial-installed-software.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="am-3-use-only-approved-azure-services"></a>AM-3: 승인된 Azure 서비스만 사용

**참고 자료**: Azure Policy를 사용하여 환경에서 사용자가 프로비전할 수 있는 서비스를 감사하고 제한합니다. Azure Resource Graph를 사용하여 구독 내에서 리소스를 쿼리하고 검색합니다. 또한 Azure Monitor를 사용하여 승인되지 않은 서비스가 검색되면 경고를 트리거하는 규칙을 만들 수 있습니다.

- [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md) 

- [Azure Policy를 사용하여 특정 리소스 종류를 거부하는 방법](../governance/policy/samples/built-in-policies.md#general) 

- [Azure Resource Graph Explorer를 사용하여 쿼리를 만드는 방법](../governance/resource-graph/first-query-portal.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="am-4-ensure-security-of-asset-lifecycle-management"></a>AM-4: 자산 수명 주기 관리의 보안 보장

**지침**: 해당 사항 없음 Azure Virtual Desktop은 수명 주기 관리 프로세스에서 자산에 대한 보안을 보장하는 데 사용할 수 없습니다. 영향이 큰 자산의 특성 및 네트워크 구성을 유지 관리하는 것은 고객의 책임입니다. 

특성 및 네트워크 구성 변경 사항을 캡처하고 변경 영향을 측정하고 해당하는 경우 수정 작업을 생성하는 프로세스를 만드는 것이 좋습니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="am-5-limit-users-ability-to-interact-with-azure-resource-manager"></a>AM-5: Azure Resource Manager와 상호 작용하는 사용자 기능 제한

**지침**: “Microsoft Azure 관리” 앱에 대한 “액세스 차단”을 구성함으로써 Azure Conditional Access를 사용하여 Azure Resource Manager와 상호 작용하는 사용자 기능을 제한합니다.

- [Azure Resource Manager에 대한 액세스를 차단하도록 조건부 액세스를 구성하는 방법](../role-based-access-control/conditional-access-azure-management.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="am-6-use-only-approved-applications-in-compute-resources"></a>AM-6: 승인된 애플리케이션만 컴퓨팅 리소스에 사용

**지침**: Azure 가상 머신 인벤토리를 사용하여 Virtual Machines의 모든 소프트웨어에 대한 정보 수집을 자동화합니다. 소프트웨어 이름, 버전, 게시자 및 새로 고침 시간은 Azure Portal에서 사용할 수 있습니다. 설치 날짜 및 기타 정보에 대한 액세스 권한을 가져오려면 게스트 수준 진단을 사용하도록 설정하고 Windows 이벤트 로그를 Log Analytics 작업 영역으로 가져와야 합니다.

- [Azure 가상 머신 인벤토리를 사용하는 방법](../automation/automation-tutorial-installed-software.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

## <a name="logging-and-threat-detection"></a>로깅 및 위협 탐지

자세한 내용은 [Azure Security Benchmark: 로깅 및 위협 탐지](../security/benchmarks/security-controls-v2-logging-threat-detection.md)를 참조하세요.

### <a name="lt-1-enable-threat-detection-for-azure-resources"></a>LT-1: Azure 리소스에 위협 탐지 사용

**지침**: Azure Security Center 기본 제공 위협 탐지 기능을 사용하고 Azure Virtual Desktop 리소스에 대해 Azure Defender(공식 이름은 Azure Advanced Threat Protection)를 사용하도록 설정합니다. Azure Virtual Desktop용 Azure Defender는 Azure Virtual Desktop 리소스에 액세스하거나 악용하려는 비정상적이고 잠재적으로 유해한 시도를 탐지하는 추가 보안 인텔리전스 계층을 제공합니다.

Azure Virtual Desktop의 모든 로그를 사용자 지정 위협 탐지를 설정하는 데 사용할 수 있는 SIEM(보안 정보 및 이벤트 관리) 솔루션으로 전달합니다. 다양한 유형의 Azure 자산에서 잠재적인 위협과 이상 징후를 모니터링해야 확인합니다. 경고의 품질을 높이는 데 집중하여 분석가가 분류하는 가양성을 줄여야 합니다. 경고는 로그 데이터, 에이전트 또는 기타 데이터로부터 제공될 수 있습니다.

- [위협 방지 및 Azure Security Center](../security-center/azure-defender.md) 

- [Azure Security Center 보안 경고 참조 지침](../security-center/alerts-reference.md)

- [위협 탐지를 위한 사용자 지정 분석 규칙 만들기](../sentinel/tutorial-detect-threats-custom.md) 

- [Azure Sentinel을 사용한 사이버 위협 인텔리전스](/azure/architecture/example-scenario/data/sentinel-threat-intelligence)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="lt-2-enable-threat-detection-for-azure-identity-and-access-management"></a>LT-2: Azure ID 및 액세스 관리에 위협 탐지 사용

**지침**: Azure AD(Azure Active Directory)는 더 정교한 모니터링 및 분석 사용 사례를 위해 Azure AD 보고에서 보거나 Azure Monitor, Azure Sentinel 또는 기타 SIEM(보안 정보 및 이벤트 관리) 또는 모니터링 도구와 통합할 수 있는 다음과 같은 사용자 로그를 제공합니다.

- 로그인 – 로그인 보고서는 관리형 애플리케이션 및 사용자 로그인 활동의 사용에 대한 정보를 제공합니다.

- 감사 로그 - Azure AD 내의 다양한 기능에 의해 수행된 모든 변경 내용에 대한 로그를 통한 추적 기능을 제공합니다. 감사 로그의 예제로는 사용자, 앱, 그룹, 역할 및 정책 추가 또는 제거와 같은 Azure AD 내의 모든 리소스에 대한 변경 내용이 있습니다.

- 위험한 로그인 - 위험한 로그인은 사용자 계정의 정당한 소유자가 아닌 사용자에 의해 수행된 로그인 시도에 대한 지표입니다.

- 위험 플래그가 지정된 사용자 - 위험한 사용자는 손상되었을 수 있는 사용자 계정에 대한 표시기입니다.

또한 Azure Security Center는 과도한 인증 시도 실패 횟수, 구독에서 사용되지 않는 계정과 같은 의심스러운 특정 활동에 대해 경고할 수 있습니다. 기본 보안 예방 조치 모니터링 외에도 Azure Security Center의 위협 방지 모듈은 개별 Azure 컴퓨팅 리소스(가상 머신, 컨테이너, 앱 서비스), 데이터 리소스(SQL DB 및 스토리지) 및 Azure 서비스 계층에서 더 심층적인 보안 경고를 수집할 수 있습니다. 이 기능을 사용하면 개별 리소스 내에서 비정상 계정 활동을 볼 수 있습니다.

- [Azure Active Directory의 감사 활동 보고서](../active-directory/reports-monitoring/concept-audit-logs.md) 

- [Azure ID 보호 사용](../active-directory/identity-protection/overview-identity-protection.md) 

- [위협 방지 및 Azure Security Center](../security-center/azure-defender.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="lt-3-enable-logging-for-azure-network-activities"></a>LT-3: Azure 네트워크 활동에 대한 로깅 사용

**지침**: Azure Virtual Desktop은 DNS(도메인 이름 서비스) 쿼리 로그를 생성하거나 처리하지 않습니다. 그러나 서비스에 등록된 리소스는 흐름 로그를 생성할 수 있습니다.

보안 분석을 위한 네트워크 보안 그룹 리소스 및 흐름 로그, Azure Firewall 로그 및 WAF(Web Application Firewall) 로그를 사용하도록 설정하고 수집하여 인시던트 조사, 위협 헌팅, 보안 경고 생성을 지원합니다. 흐름 로그를 Azure Monitor Log Analytics 작업 영역으로 보낸 다음, 트래픽 분석을 사용하여 인사이트를 제공할 수 있습니다.

- [네트워크 보안 그룹 흐름 로그를 사용하도록 설정하는 방법](../network-watcher/network-watcher-nsg-flow-logging-portal.md) 

- [Azure Firewall 로그 및 메트릭](../firewall/logs-and-metrics.md) 

- [트래픽 분석을 설정하고 사용하는 방법](../network-watcher/traffic-analytics.md) 

- [Azure Monitor의 Azure 네트워킹 모니터링 솔루션](../azure-monitor/insights/azure-networking-analytics.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="lt-4-enable-logging-for-azure-resources"></a>LT-4: Azure 리소스에 대한 로깅 사용

**지침**: 자동으로 사용되는 활동 로그에는 읽기 작업(GET)을 제외하고 Azure Virtual Desktop 리소스에 대한 모든 쓰기 작업(PUT, POST, DELETE)이 포함됩니다. 활동 로그를 사용하여 문제를 해결할 때 오류를 찾거나 조직의 사용자가 리소스를 수정한 방법을 모니터링할 수 있습니다.

- [Azure Monitor를 사용하여 플랫폼 로그 및 메트릭을 수집하는 방법](../azure-monitor/essentials/diagnostic-settings.md) 

- [Azure의 로깅 및 여러 가지 로그 형식 이해](../azure-monitor/essentials/platform-logs-overview.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: 공유됨

### <a name="lt-5-centralize-security-log-management-and-analysis"></a>LT-5: 보안 로그 관리 및 분석 중앙 집중화

**지침**: 상관 관계를 사용하도록 설정하기 위해 로깅 스토리지 및 분석을 중앙 집중화합니다. 각 로그 원본에 대해 데이터 소유자, 액세스 지침, 스토리지 위치, 데이터를 처리하고 액세스하는 데 사용되는 도구, 데이터 보존 요구 사항을 할당했는지 확인합니다.

Azure 활동 로그를 중앙 로깅에 통합합니다. Azure Monitor를 통해 로그를 수집하여 엔드포인트 디바이스, 네트워크 리소스, 기타 보안 시스템에 의해 생성된 보안 데이터를 집계합니다. Azure Monitor에서 Log Analytics 작업 영역을 사용하여 분석을 쿼리하고 수행하며, 장기 및 기록 스토리지에 Azure Storage 계정을 사용할 수 있습니다.

또한 데이터를 사용하도록 설정하고 Azure Sentinel 또는 타사 SIEM(보안 정보 및 이벤트 관리) 솔루션에 온보딩할 수 있습니다. 많은 조직에서 자주 사용되는 “핫” 데이터에는 Azure Sentinel을 사용하고 덜 사용되는 “콜드” 데이터에는 Azure Storage를 사용합니다.

- [Azure Monitor를 사용한 플랫폼 로그 및 메트릭 수집 방법](../azure-monitor/essentials/diagnostic-settings.md) 

- [Azure Sentinel을 온보딩하는 방법](../sentinel/quickstart-onboard.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

## <a name="incident-response"></a>사고 대응

자세한 내용은 [Azure Security Benchmark: 인시던트 응답](../security/benchmarks/security-controls-v2-incident-response.md)을 참조하세요.

### <a name="ir-1-preparation--update-incident-response-process-for-azure"></a>IR-1: 준비 - Azure에 대한 인시던트 응답 프로세스 업데이트

**지침**: 조직이 보안 인시던트에 대응하는 프로세스를 보유하고, 해당 프로세스를 Azure에 대해 업데이트했으며, 준비 상태를 확인하기 위해 프로세스를 정기적으로 수행하는지 확인합니다.

- [엔터프라이즈 환경에서 보안 구현](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [인시던트 응답 참조 가이드](/microsoft-365/downloads/IR-Reference-Guide.pdf)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="ir-2-preparation--setup-incident-notification"></a>IR-2: 준비 - 인시던트 알림 설정

**지침**: Azure Security Center에서 보안 인시던트 연락처 정보를 설정합니다. 이 연락처 정보는 MSRC(Microsoft 보안 대응 센터)가 불법적인 당사자나 권한 없는 당사자가 데이터에 액세스한 것을 발견한 경우 Microsoft가 연락하는 데 사용됩니다. 또한 인시던트 응답 요구 사항에 따라 다양한 Azure 서비스에서 인시던트 경고 및 알림을 사용자 지정하는 옵션도 있습니다. 

- [Azure Security Center 보안 연락처를 설정하는 방법](../security-center/security-center-provide-security-contact-details.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="ir-3-detection-and-analysis--create-incidents-based-on-high-quality-alerts"></a>IR-3: 탐지 및 분석 – 고품질 경고를 기반으로 인시던트 만들기

**지침**: 고품질 경고를 만들고 경고의 품질을 측정하는 프로세스가 있는지 확인합니다. 이렇게 하면 이전 인시던트에서 상황을 확인하고 분석가를 위한 경고의 우선 순위를 지정할 수 있으므로 가양성으로 인해 시간을 낭비하지 않습니다. 

고품질 경고는 이전 인시던트, 유효성이 검사된 커뮤니티 소스, 다양한 신호 원본을 결합하고 상관 관계를 설정하여 경고를 생성하고 정리하도록 설계된 도구 등에서 얻은 경험을 기반으로 구축할 수 있습니다. 

Azure Security Center는 많은 Azure 자산에 대해 고품질 경고를 제공합니다. ASC 데이터 커넥터를 사용하여 경고를 Azure Sentinel로 스트림할 수 있습니다. Azure Sentinel을 사용하면 조사를 위해 인시던트를 자동으로 생성하는 고급 경고 규칙을 만들 수 있습니다. 

Azure 리소스에 대한 위험을 식별하는 데 도움이 되도록 내보내기 기능을 사용하여 Azure Security Center 경고 및 추천 사항을 내보냅니다. 경고 및 추천 사항을 수동 또는 지속적인 방식으로 내보냅니다.

- [내보내기를 구성하는 방법](../security-center/continuous-export.md)

- [경고를 Azure Sentinel로 스트림하는 방법](../sentinel/connect-azure-security-center.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="ir-4-detection-and-analysis--investigate-an-incident"></a>IR-4: 검색 및 분석 – 인시던트 조사

**지침**: 분석가가 잠재적 인시던트를 조사할 때 다양한 데이터 원본을 쿼리하고 사용하여 발생한 상황에 대한 전체 보기를 작성할 수 있는지 확인합니다. 사각지대를 방지하기 위해 다양한 로그를 수집하여 킬 체인 전체에서 잠재적 공격자의 활동을 추적해야 합니다.  또한 다른 분석가 및 향후 기록 참조를 위해 인사이트 및 습득 지식을 캡처해야 합니다.  

조사할 데이터 원본에는 범위 내 서비스 및 실행 중인 시스템에서 이미 수집되고 있는 중앙 집중식 로깅 원본이 포함되지만 다음과 같은 원본도 포함될 수 있습니다.

- 네트워크 데이터 – 네트워크 보안 그룹의 흐름 로그, Azure Network Watcher 및 Azure Monitor를 사용하여 네트워크 흐름 로그 및 기타 분석 정보를 캡처합니다. 

- 실행 중인 시스템의 스냅샷 

    - Azure 가상 머신의 스냅샷 기능을 사용하여 실행 중인 시스템의 디스크에 대한 스냅샷을 만듭니다. 

    - 운영 체제의 기본 메모리 덤프 기능을 사용하여 실행 중인 시스템의 메모리에 대한 스냅샷을 만듭니다.

    - Azure 서비스의 스냅샷 기능 또는 소프트웨어의 자체 기능을 사용하여 실행 중인 시스템의 스냅샷을 만듭니다.

Azure Sentinel은 거의 모든 로그 원본 및 사례 관리 포털에서 광범위한 데이터 분석을 제공하여 인시던트의 전체 수명 주기를 관리합니다. 조사 중에 인텔리전스 정보는 추적 및 보고를 위해 인시던트에 연결할 수 있습니다. 

- [Windows 컴퓨터의 디스크 스냅샷 만들기](../virtual-machines/windows/snapshot-copy-managed-disk.md)

- [Linux 컴퓨터의 디스크 스냅샷 만들기](../virtual-machines/linux/snapshot-copy-managed-disk.md)

- [Microsoft Azure 지원 진단 정보 및 메모리 덤프 수집](https://azure.microsoft.com/support/legal/support-diagnostic-information-collection/) 

- [Azure Sentinel을 사용하여 인시던트 조사](../sentinel/tutorial-investigate-cases.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="ir-5-detection-and-analysis--prioritize-incidents"></a>IR-5: 탐지 및 분석 – 인시던트 우선 순위 지정

**지침**: 경고 심각도 및 자산 민감도에 따라 먼저 집중해야 하는 인시던트에 대한 컨텍스트를 분석가에게 제공합니다. 

Azure Security Center는 먼저 조사해야 하는 경고의 우선 순위를 지정하는 데 도움이 되도록 심각도를 각 경고에 할당합니다. 심각도는 Security Center에서 경고를 발행하는 데 사용되는 결과 또는 분석의 신뢰도 및 경고가 발생한 활동에 악의적인 의도가 있었다는 신뢰도 수준을 기반으로 합니다.

또한 태그를 사용하여 리소스를 표시하고, Azure 리소스, 특히 중요한 데이터를 처리하는 리소스를 식별하고 분류할 수 있는 명명 시스템을 만듭니다. 인시던트가 발생한 Azure 리소스 및 환경의 중요도에 따라 경고의 수정에 대한 우선 순위를 지정해야 합니다.

- [Azure Security Center의 보안 경고](../security-center/security-center-alerts-overview.md)

- [태그를 사용하여 Azure 리소스 구성](../azure-resource-manager/management/tag-resources.md).

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="ir-6-containment-eradication-and-recovery--automate-the-incident-handling"></a>IR-6: 차단, 제거, 복구 - 인시던트 처리 자동화

**지침**: 수동 반복 작업을 자동화하여 응답 시간을 단축하고 분석가의 부담을 줄입니다. 수동 작업은 실행하는 데 더 오래 걸려 각 인시던트의 속도를 늦추고 분석가가 처리할 수 있는 인시던트 수를 줄입니다. 또한 수동 작업은 분석가를 지치게 하여 지연을 유발하는 인간 오류의 위험을 높이고, 복잡한 작업에 효과적으로 집중할 수 있는 분석가의 능력을 저하시킵니다. Azure Security Center 및 Azure Sentinel의 워크플로 자동화 기능을 사용하여 작업을 자동으로 트리거하거나 플레이북을 실행하여 들어오는 보안 경고에 대응합니다. 플레이북은 알림 보내기, 계정 사용 안 함 및 문제가 있는 네트워크 격리와 같은 작업을 수행합니다. 

- [Security Center에서 워크플로 자동화 구성](../security-center/workflow-automation.md)

- [Azure Security Center에서 자동화된 위협 대응 설정](../security-center/tutorial-security-incident.md#triage-security-alerts)

- [Azure Sentinel에서 자동화된 위협 응답 설정](../sentinel/tutorial-respond-threats-playbook.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

## <a name="posture-and-vulnerability-management"></a>태세 및 취약성 관리

*자세한 내용은 [Azure Security Benchmark: 보안 상태 및 취약성 관리](../security/benchmarks/security-controls-v2-posture-vulnerability-management.md)를 참조하세요.*

### <a name="pv-3-establish-secure-configurations-for-compute-resources"></a>PV-3: 컴퓨팅 리소스에 대한 보안 구성 설정

**지침**: Azure Security Center 및 Azure Policy를 사용하여 VM, 컨테이너 등을 비롯한 모든 컴퓨팅 리소스에 대한 보안 구성을 설정합니다.

사용자 지정 운영 체제 이미지 또는 Azure Automation 상태 구성을 사용하여 조직에 필요한 운영 체제의 보안 구성을 설정할 수 있습니다.

- [Azure Security Center 권장 사항을 모니터링하는 방법](../security-center/security-center-recommendations.md) 

- [Azure Automation State Configuration 개요](../automation/automation-dsc-overview.md) 

- [Azure Virtual Desktop 환경](environment-setup.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="pv-4-sustain-secure-configurations-for-compute-resources"></a>PV-4: 컴퓨팅 리소스에 대한 보안 구성 유지

**지침**: Azure Security Center 및 Azure Policy를 사용하여 가상 머신, 컨테이너를 포함한 Azure 컴퓨팅 리소스의 구성 위험을 정기적으로 평가하고 수정합니다. 또한 Azure Resource Manager 템플릿, 사용자 지정 운영 체제 이미지 또는 Azure Automation 상태 구성을 사용하여 조직에 필요한 운영 체제의 보안 구성을 유지 관리할 수 있습니다. Azure Automation State Configuration과 결합된 Microsoft 가상 머신 템플릿은 보안 요구 사항을 충족하고 유지 관리하는 데 도움이 될 수 있습니다.

Microsoft에서 게시하는 Azure Marketplace 가상 머신 이미지는 Microsoft에서 유지 관리합니다.

또한 Azure Security Center는 컨테이너 이미지의 취약성을 검사하고 Center Internet Security의 Docker 벤치마크와 비교하여 컨테이너의 Docker 구성을 지속적으로 모니터링할 수 있습니다. Azure Security Center 권장 사항 페이지를 사용하여 권장 사항을 살펴보고 문제를 해결할 수 있습니다.

- [Azure Security Center 취약성 평가 권장 사항을 구현하는 방법](../security-center/deploy-vulnerability-assessment-vm.md) 

- [ARM 템플릿에서 Azure 가상 머신을 만드는 방법](../virtual-machines/windows/ps-template.md) 

- [Azure Automation State Configuration 개요](../automation/automation-dsc-overview.md) 

- [Security Center의 컨테이너 보안](../security-center/container-security.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="pv-5-securely-store-custom-operating-system-and-container-images"></a>PV 5: 사용자 지정 운영 체제 및 컨테이너 이미지를 안전하게 저장

**지침**: 고객은 Azure Virtual Desktop을 통해 운영 체제 이미지를 관리할 수 있습니다. Azure RBAC(Azure 역할 기반 액세스 제어)를 사용하여 권한 있는 사용자만 이미지에 액세스할 수 있도록 합니다. Azure Shared Image Gallery를 사용하여 조직 내의 여러 사용자, 서비스 주체 또는 AD 그룹과 이미지를 공유할 수 있습니다. 컨테이너 이미지를 Azure Container Registry에 저장하고 RBAC를 사용하여 권한 있는 사용자만 액세스할 수 있도록 합니다.

- [Azure RBAC 이해](../role-based-access-control/rbac-and-directory-admin-roles.md) 

- [Azure RBAC를 구성하는 방법](../role-based-access-control/quickstart-assign-role-user-portal.md) 

- [Shared Image Gallery 개요](../virtual-machines/shared-image-galleries.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="pv-6-perform-software-vulnerability-assessments"></a>PV-6: 소프트웨어 취약성 평가 수행

**지침**: Azure Virtual Desktop을 사용하여 자체 가상 머신을 배포하고, 가상 머신을 서비스에 등록하고, 환경에서 SQL 데이터베이스를 실행할 수 있습니다.

Azure Virtual Desktop에서 타사 솔루션을 사용하여 네트워크 디바이스 및 웹 애플리케이션의 취약성을 평가할 수 있습니다. 원격 검사를 수행하는 경우 단일 영구 관리자 계정을 사용하지 마세요. 검색 계정에 대한 JIT 프로비저닝 방법론을 구현하는 것이 좋습니다. 검사 계정에 대한 자격 증명을 보호하고, 모니터링하고, 취약성 검색에만 사용해야 합니다.

필요한 경우 검사 결과를 일정한 간격으로 내보내고 결과를 이전 검사와 비교하여 취약성이 수정되었는지 확인합니다.

Azure Security Center의 권장 사항에 따라 Azure 가상 머신(및 SQL 서버)의 취약성 평가를 수행합니다. Azure Security Center는 가상 머신, 컨테이너 이미지 및 SQL 데이터베이스의 취약성을 검사하는 기본 스캐너를 제공합니다.

필요한 경우 검사 결과를 일정한 간격으로 내보내고 결과를 이전 검사와 비교하여 취약성이 수정되었는지 확인합니다. Azure Security Center에서 제안하는 취약성 관리 권장 사항을 사용하는 경우 선택한 솔루션의 포털을 중심으로 기록 검사 데이터를 볼 수 있습니다.

- [Azure Security Center 취약성 평가 권장 사항을 구현하는 방법](../security-center/deploy-vulnerability-assessment-vm.md) 

- [가상 머신용 통합 취약성 검사기](../security-center/deploy-vulnerability-assessment-vm.md) 
- [SQL 취약성 평가](../azure-sql/database/sql-vulnerability-assessment.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="pv-7-rapidly-and-automatically-remediate-software-vulnerabilities"></a>PV-7: 자동으로 신속하게 소프트웨어 취약성 수정

**지침**: Azure Virtual Desktop은 타사 소프트웨어를 사용하거나 요구하지 않습니다. 그러나 Azure Virtual Desktop을 사용하여 자체 가상 머신을 배포하고 서비스에 등록할 수 있습니다.

Azure Automation 업데이트 관리 또는 타사 솔루션을 사용하여 Windows Server 가상 머신에 최신 보안 업데이트가 설치되었는지 확인합니다. Windows 가상 머신의 경우 Windows 업데이트를 사용하도록 설정하고 자동으로 업데이트되도록 설정했는지 확인합니다.

타사 소프트웨어용 타사 패치 관리 솔루션 또는 Configuration Manager용 System Center Updates Publisher를 사용합니다.

- [Azure에서 가상 머신에 대한 업데이트 관리를 구성하는 방법](../automation/update-management/overview.md) 

- [Azure VM에 대한 업데이트 및 패치 관리](../automation/update-management/manage-updates-for-vm.md)

- [Azure Virtual Desktop에 대한 Microsoft Endpoint Configuration Manager 구성](configure-automatic-updates.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="pv-8-conduct-regular-attack-simulation"></a>PV-8: 정기적인 공격 시뮬레이션 수행

**지침**: Azure Virtual Desktop은 고객이 자체 Azure Virtual Desktop 리소스에서 자체적인 침투 테스트를 수행할 수 없습니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 공유됨

## <a name="endpoint-security"></a>엔드포인트 보안

*자세한 내용은 [Azure Security Benchmark: 엔드포인트 보안](../security/benchmarks/security-controls-v2-endpoint-security.md)을 참조하세요.*

### <a name="es-1-use-endpoint-detection-and-response-edr"></a>ES-1: EDR(엔드포인트 검색 및 응답) 사용

**지침**: Azure Virtual Desktop은 EDR(엔드포인트 검색 및 응답) 프로세스와 관련된 기능을 제공하지 않습니다. 그러나 서비스에 등록된 리소스는 엔드포인트 검색 및 응답 기능을 이용할 수 있습니다. 

서버 및 클라이언트에 엔드포인트 검색 및 응답 기능을 사용하도록 설정하고 SIEM(보안 정보 및 이벤트 관리) 솔루션 및 보안 작업 프로세스와 통합합니다.

Microsoft Defender의 Advanced Threat Protection은 고급 위협을 방지, 탐지, 조사하고 대응하기 위한 엔터프라이즈 엔드포인트 보안 플랫폼의 일부로 엔드포인트 검색 및 응답 기능을 제공합니다.

- [Microsoft Defender Advanced Threat Protection 개요](/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection) 

- [Windows 서버용 Microsoft Defender ATP 서비스](/windows/security/threat-protection/microsoft-defender-atp/configure-server-endpoints) 

- [비 Windows 서버용 Microsoft Defender ATP 서비스](/windows/security/threat-protection/microsoft-defender-atp/configure-endpoints-non-windows)

- [영구적이지 않은 가상 데스크톱 인프라를 위한 Microsoft Defender ATP](/windows/security/threat-protection/microsoft-defender-atp/configure-endpoints-vdi)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="es-2-use-centrally-managed-modern-anti-malware-software"></a>ES-2: 중앙 관리형 최신 맬웨어 방지 소프트웨어 사용

**지침**: 실시간 검사와 정기적 검사가 가능한 중앙 관리형 최신 엔드포인트 맬웨어 방지 솔루션으로 Azure Virtual Desktop 리소스를 보호합니다.

Azure Security Center는 가상 머신에 많이 사용되는 여러 맬웨어 방지 솔루션의 사용을 자동으로 식별하고, 엔드포인트 보호 실행 상태를 보고하고, 권장 사항을 작성할 수 있습니다.

Azure Cloud Services용 Microsoft Antimalware는 Windows VM(가상 머신)의 기본 맬웨어 방지 프로그램입니다. 또한 데이터 서비스를 위한 Azure Security Center의 위협 탐지를 사용하여 Azure Storage 계정에 업로드된 맬웨어를 탐지할 수 있습니다.

- [Cloud Services 및 Virtual Machines용 Microsoft Antimalware 구성 방법](../security/fundamentals/antimalware.md) 

- [지원되는 엔드포인트 보호 솔루션](../security-center/security-center-services.md?tabs=features-windows#supported-endpoint-protection-solutions-)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="es-3-ensure-anti-malware-software-and-signatures-are-updated"></a>ES-3: 맬웨어 방지 소프트웨어 및 서명이 업데이트되는지 확인

**지침**: 맬웨어 방지 서명이 신속하고 지속적으로 업데이트되는지 확인합니다.

Azure Security Center에서 “컴퓨팅 &amp; 앱”의 권장 사항에 따라 모든 가상 머신 및/또는 컨테이너가 최신 서명으로 업데이트되도록 합니다.

Microsoft 맬웨어 방지 프로그램은 기본적으로 최신 서명 및 엔진 업데이트를 자동 설치합니다.

- [Azure Cloud Services 및 Virtual Machines용 Microsoft Antimalware 배포 방법](../security/fundamentals/antimalware.md) 

- [Azure Security Center에서 엔드포인트 보호 평가 및 권장 사항](../security-center/security-center-endpoint-protection.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

## <a name="backup-and-recovery"></a>백업 및 복구

자세한 내용은 [Azure Security Benchmark: 백업 및 복구](../security/benchmarks/security-controls-v2-backup-recovery.md)를 참조하세요.

### <a name="br-1-ensure-regular-automated-backups"></a>BR-1: 자동화된 정기 백업 보장

**지침**: 예기치 않은 사건이 발생하더라도 비즈니스 연속성을 유지할 수 있도록 시스템 및 데이터를 백업합니다. RPO(복구 지점 목표) 및 RTO(복구 시간 목표)에 대한 모든 목표에서 이 지침을 따라야 합니다.

Azure Backup을 사용하도록 설정하고 백업 원본(예: Azure VM, SQL Server, HANA 데이터베이스 또는 파일 공유)과 원하는 빈도 및 보존 기간을 구성합니다.

중복도를 더 높이려면 지역 중복 스토리지 옵션을 사용하여 백업 데이터를 보조 지역으로 복제하고 지역 간 복원을 사용하여 복구하면 됩니다.

- [엔터프라이즈 규모 비즈니스 연속성 및 재해 복구](/azure/cloud-adoption-framework/ready/enterprise-scale/business-continuity-and-disaster-recovery) 

- [Azure Backup을 사용하는 방법](../backup/index.yml) 

- [지역 간 복원을 사용하도록 설정하는 방법](../backup/backup-azure-arm-restore-vms.md#cross-region-restore) 

- [Azure Virtual Desktop에서 비즈니스 연속성 및 재해 복구 계획을 설정하는 방법](disaster-recovery.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="br-2-encrypt-backup-data"></a>BR-2: 백업 데이터 암호화

**지침**: 공격으로부터 백업을 보호해야 합니다. 여기에는 기밀성 손실을 방지하기 위한 백업 암호화가 포함됩니다.

정기 Azure 서비스 백업의 경우 백업 데이터는 Azure 플랫폼 관리형 키를 사용하여 자동으로 암호화됩니다. 고객 관리형 키를 사용하여 백업을 암호화하도록 선택할 수 있습니다. 이 경우 키 자격 증명 모음의 고객 관리형 키도 백업 범위에 있어야 합니다.

Azure Backup, Azure Key Vault 또는 기타 리소스에서 역할 기반 액세스 제어를 사용하여 백업 및 고객 관리형 키를 보호합니다. 또한 고급 보안 기능을 사용하도록 설정하여 백업을 변경하거나 삭제하기 전에 다단계 인증을 요구할 수 있습니다.

Azure Backup의 보안 기능 개요 /azure/backup/security-overview 

- [고객 관리형 키를 사용하여 백업 데이터 암호화](../backup/encryption-at-rest-with-cmk.md) 

- [Azure에서 키 자격 증명 모음 키를 백업하는 방법](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?preserve-view=true&view=azurermps-6.13.0)

- [공격으로부터 하이브리드 백업을 보호하는 보안 기능](../backup/backup-azure-security-feature.md#prevent-attacks)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="br-3-validate-all-backups-including-customer-managed-keys"></a>BR-3: 고객 관리형 키를 비롯한 모든 백업 유효성 검사

**지침**: 백업이 정상적으로 작동하도록 데이터 복원 프로세스를 수행하여 정기적으로 백업 미디어의 데이터 무결성을 검사하는 것이 좋습니다.

- [Azure Virtual Machine 백업에서 파일을 복구하는 방법](../backup/backup-azure-restore-files-from-vm.md)

- [보안 구현](../backup/backup-azure-restore-files-from-vm.md#security-implementations)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

## <a name="governance-and-strategy"></a>거버넌스 및 전략

자세한 내용은 [Azure Security Benchmark: 거버넌스 및 전략](../security/benchmarks/security-controls-v2-governance-strategy.md)을 참조하세요.

### <a name="gs-1-define-asset-management-and-data-protection-strategy"></a>GS-1: 자산 관리 및 데이터 보호 전략 정의 

**지침**: 시스템 및 데이터를 지속적으로 모니터링하고 보호하기 위한 명확한 전략을 문서화하고 전달해야 합니다. 중요 비즈니스용 데이터 및 시스템의 검색, 평가, 보호, 모니터링에 우선 순위를 지정합니다. 

이 전략에는 다음 요소에 대한 문서화된 지침, 정책, 표준이 포함되어야 합니다. 

-   비즈니스 위험에 따른 데이터 분류 표준

-   위험 및 자산 인벤토리에 대한 보안 조직의 가시성 

-   사용할 Azure 서비스에 대한 보안 조직의 승인 

-   수명 주기 전체에서 자산 보안

-   조직 데이터 분류에 따른 필수 액세스 제어 전략

-   Azure 기본 및 타사 데이터 보호 기능 사용

-   전송 중 및 저장 사용 사례에 대한 데이터 암호화 요구 사항

-   적절한 암호화 표준

자세한 내용은 다음 참조 문서를 참조하세요.
- [Azure 보안 아키텍처 권장 사항 - 스토리지, 데이터, 암호화](/azure/architecture/framework/security/storage-data-encryption?bc=%2fsecurity%2fcompass%2fbreadcrumb%2ftoc.json&toc=%2fsecurity%2fcompass%2ftoc.json)

- [Azure 보안 기본 사항 - Azure 데이터 보안, 암호화, 스토리지](../security/fundamentals/encryption-overview.md)

- [클라우드 채택 프레임워크 - Azure 데이터 보안 및 암호화 모범 사례](../security/fundamentals/data-encryption-best-practices.md?bc=%2fazure%2fcloud-adoption-framework%2f_bread%2ftoc.json&toc=%2fazure%2fcloud-adoption-framework%2ftoc.json)

- [Azure Security Benchmark - 자산 관리](../security/benchmarks/security-controls-v2-asset-management.md)

- [Azure Security Benchmark - 데이터 보호](../security/benchmarks/security-controls-v2-data-protection.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="gs-2-define-enterprise-segmentation-strategy"></a>GS-2: 엔터프라이즈 구분 전략 정의 

**지침**: ID, 네트워크, 애플리케이션, 구독, 관리 그룹 및 기타 컨트롤의 조합을 사용하여 자산에 대한 액세스를 구분하는 엔터프라이즈 전체 전략을 수립합니다.

서로 통신하고 데이터에 액세스해야 하는 시스템의 일상 작업을 사용하도록 설정해야 할 필요성과 보안 분리의 필요성을 신중하게 조정해야 합니다.

구분 전략이 네트워크 보안, ID 및 액세스 모델, 애플리케이션 권한/액세스 모델 및 사용자 프로세스 컨트롤을 비롯하여 컨트롤 형식 간에 일관되게 구현되는지 확인합니다.

- [Azure의 구분 전략에 대한 지침(동영상)](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [Azure의 구분 전략에 대한 지침(문서)](/security/compass/governance#enterprise-segmentation-strategy)

- [엔터프라이즈 구분 전략에 맞춰 네트워크 구분 조정](/security/compass/network-security-containment#align-network-segmentation-with-enterprise-segmentation-strategy)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="gs-3-define-security-posture-management-strategy"></a>GS-3: 보안 태세 관리 전략 정의

**지침**: 개별 자산과 해당 자산이 호스트되는 환경에 대한 위험을 지속적으로 측정하고 완화합니다. 게시된 애플리케이션, 네트워크 수신 및 송신 지점, 사용자 및 관리자 엔드포인트 등과 같은 고가치 자산과 노출이 많은 공격 노출 영역에 우선 순위를 지정합니다.

- [Azure Security Benchmark - 태세 및 취약성 관리](../security/benchmarks/security-controls-v2-posture-vulnerability-management.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="gs-4-align-organization-roles-responsibilities-and-accountabilities"></a>GS-4: 조직 역할, 책임, 의무 조정

**지침**: 보안 조직의 역할 및 책임에 대한 명확한 전략을 문서화하고 전달하는지 확인합니다. 보안 의사 결정에 대한 명확한 책임을 제시하고, 공유 책임 모델을 모두에게 교육하고, 클라우드를 보호하는 기술에 관해 기술 팀을 교육하는 일에 우선 순위를 지정합니다.

- [Azure 보안 모범 사례 1 – 사용자: 클라우드 보안 경험에 대한 팀 교육](/azure/cloud-adoption-framework/security/security-top-10#1-people-educate-teams-about-the-cloud-security-journey)

- [Azure 보안 모범 사례 2 – 사용자: 클라우드 보안 기술에 대한 팀 교육](/azure/cloud-adoption-framework/security/security-top-10#2-people-educate-teams-on-cloud-security-technology)

- [Azure 보안 모범 사례 3 – 프로세스: 클라우드 보안 결정에 대한 책임 할당](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="gs-5-define-network-security-strategy"></a>GS-5: 네트워크 보안 전략 정의

**지침**: 조직의 전반적인 보안 액세스 제어 전략의 일부로 Azure 네트워크 보안 방법을 설정합니다.  

이 전략에는 다음 요소에 대한 문서화된 지침, 정책, 표준이 포함되어야 합니다. 

-   중앙 집중식 네트워크 관리 및 보안 책임

-   엔터프라이즈 구분 전략에 맞춰 조정된 가상 네트워크 구분 모델

-   다양한 위협 및 공격 시나리오에서 수정 전략

-   인터넷 에지 및 수신/송신 전략

-   하이브리드 클라우드 및 온-프레미스 상호 연결 전략

-   최신 네트워크 보안 아티팩트(예: 네트워크 다이어그램, 참조 네트워크 아키텍처)

자세한 내용은 다음 참조 문서를 참조하세요.
- [Azure 보안 모범 사례 11 – 아키텍처 단일 통합 보안 전략](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Azure Security Benchmark - 네트워크 보안](../security/benchmarks/security-controls-v2-network-security.md)

- [Azure 네트워크 보안 개요](../security/fundamentals/network-overview.md)

- [엔터프라이즈 네트워크 아키텍처 전략](/azure/cloud-adoption-framework/ready/enterprise-scale/architecture)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="gs-6-define-identity-and-privileged-access-strategy"></a>GS-6: ID 및 권한 있는 액세스 전략 정의

**지침**: 조직의 전반적인 보안 액세스 제어 전략의 일부로 Azure ID 및 권한 있는 액세스 방법을 설정합니다.  

이 전략에는 다음 요소에 대한 문서화된 지침, 정책, 표준이 포함되어야 합니다. 

-   중앙 집중식 ID 및 인증 시스템과 다른 내부 및 외부 ID 시스템 간의 상호 연결

-   다양한 사용 사례 및 조건에서 강력한 인증 방법

-   권한이 높은 사용자 보호

-   변칙 사용자 활동 모니터링 및 처리  

-   사용자 ID 및 액세스 검토와 조정 프로세스

자세한 내용은 다음 참조 문서를 참조하세요.

- [Azure Security Benchmark - ID 관리](../automation/update-management/overview.md)

- [Azure Security Benchmark - 권한 있는 액세스](../security/benchmarks/security-controls-v2-privileged-access.md)

- [Azure 보안 모범 사례 11 – 아키텍처 단일 통합 보안 전략](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Azure ID 관리 보안 개요](../security/fundamentals/identity-management-overview.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="gs-7-define-logging-and-threat-response-strategy"></a>GS-7: 로깅 및 위협 대응 전략 정의

**지침**: 규정 준수 요구 사항을 충족하면서 신속하게 위협을 탐지하고 수정할 수 있는 로깅 및 위협 대응 전략을 수립합니다. 통합 및 수동 단계가 아닌 위협에 집중할 수 있도록 분석가에게 고품질 경고 및 원활한 환경을 제공하는 것에 우선 순위를 지정합니다. 

이 전략에는 다음 요소에 대한 문서화된 지침, 정책, 표준이 포함되어야 합니다. 

-   보안 운영(SecOps) 조직의 역할 및 책임 

-   NIST 또는 다른 업계 프레임워크를 사용하여 잘 정의된 인시던트 응답 프로세스 

-   위협 탐지, 인시던트 응답 및 규정 준수 요구 사항을 지원하는 로그 캡처 및 보존

-   SIEM, 네이티브 Azure 기능, 기타 소스를 사용하여 위협에 대한 중앙 집중식 가시성 및 상관 관계 정보 

-   고객, 공급자 및 공공 당사자와의 통신 및 알림 계획

-   로깅 및 위협 탐지, 포렌식, 공격 수정, 제거와 같은 인시던트 처리를 위해 Azure 네이티브 및 타사 플랫폼 사용

-   확인한 상황 및 증거 보존을 포함하여 인시던트 및 인시던트 후 활동을 처리하는 프로세스

자세한 내용은 다음 참조 문서를 참조하세요.

- [Azure Security Benchmark - 로깅 및 위협 탐지](../security/benchmarks/security-controls-v2-logging-threat-detection.md)

- [Azure Security Benchmark - 인시던트 응답](../security/benchmarks/security-controls-v2-incident-response.md)

- [Azure 보안 모범 사례 4 - 프로세스 클라우드에 대한 인시던트 응답 프로세스 업데이트](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Azure 채택 프레임워크, 로깅, 보고 의사 결정 가이드](/azure/cloud-adoption-framework/decision-guides/logging-and-reporting/)

- [Azure 엔터프라이즈 규모, 관리, 모니터링](/azure/cloud-adoption-framework/ready/enterprise-scale/management-and-monitoring)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

## <a name="next-steps"></a>다음 단계

- [Azure Security Benchmark V2 개요](../security/benchmarks/overview.md)를 참조하세요.
- [Azure 보안 기준](../security/benchmarks/security-baselines-overview.md)에 대해 자세히 알아보세요.
