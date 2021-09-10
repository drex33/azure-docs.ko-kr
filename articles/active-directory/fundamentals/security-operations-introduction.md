---
title: Azure Active Directory 보안 작업 가이드
description: 계정, 애플리케이션, 디바이스, 인프라 관련 보안 문제를 모니터링, 식별 및 경고하는 방법을 알아봅니다.
services: active-directory
author: BarbaraSelden
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 07/15/2021
ms.author: baselden
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: bef4e1b8f4524e9b883bfe4aa5ef7b2a0e2bcf83
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122536658"
---
# <a name="azure-active-directory-security-operations-guide"></a>Azure Active Directory 보안 작업 가이드

Microsoft는 ID를 컨트롤 플레인으로 활용하는 [심층 방어](https://us-cert.cisa.gov/bsi/articles/knowledge/principles/defense-in-depth) 원칙을 사용하여 [제로 트러스트 보안](https://aka.ms/Zero-Trust) 방법을 성공적으로 입증했습니다. 조직이 스케일링, 비용 절감, 보안을 위해 하이브리드 워크로드를 계속 수용함에 따라 Azure AD(Azure Active Directory)는 ID 관리 전략에 중요한 역할을 합니다. 최근에 ID 및 보안 손상에 관한 소식이 들리면서 기업 IT에서 ID 보안 태세를 방어 보안 성공의 측정값으로 고려하는 경향이 더욱 증가했습니다.

시간이 경과할수록 조직은 사용자가 온-프레미스 계정과 클라우드 전용 계정 둘 다로 액세스하는 온-프레미스 및 클라우드 애플리케이션의 혼합을 수용해야 합니다. 온-프레미스와 클라우드 둘 다에서 사용자, 애플리케이션, 디바이스를 관리하는 작업은 까다롭습니다.

Azure Active Directory는 위치에 관계없이 모든 리소스에 대한 인증과 권한 부여에 사용할 공통 사용자 ID를 만듭니다. 하이브리드 ID라고 합니다. 

Azure AD에서 하이브리드 ID를 구현하려면 시나리오에 따라 세 가지 인증 방법 중 하나를 사용하면 됩니다. 세 가지 방법은 다음과 같습니다.

* [PHS(암호 해시 동기화)](../hybrid/whatis-phs.md)

* [PTA(통과 인증)](../hybrid/how-to-connect-pta.md)

* [페더레이션(AD FS)](../hybrid/whatis-fed.md)

현재 보안 작업을 감사하거나 Azure 환경에 대한 보안 작업을 설정하는 경우 다음을 수행하는 것이 좋습니다.

* Microsoft 보안 지침의 특정 부분을 읽고 클라우드 기반 또는 하이브리드 Azure 환경 보안에 대한 기본 지식을 숙지합니다.

* 가장 일반적인 공격 벡터를 방지할 수 있도록 계정 및 암호 전략과 인증 방법을 감사합니다.

* 보안 위협을 나타낼 수 있는 활동에 대한 지속적인 모니터링 및 경고 전략을 만듭니다.

## <a name="audience"></a>사용자

Azure AD SecOps 가이드는 더 효율적인 ID 보안 구성 및 모니터링 프로필을 통해 위협에 대응해야 하는 기업 IT ID 및 보안 운영 팀과 관리형 서비스 공급자를 위한 것입니다. 이 가이드는 SOC(보안 운영 센터) 방어 및 침투 테스트 팀에 ID 보안 태세를 개선하고 유지 관리하도록 조언하는 IT 관리자 및 ID 설계자와 특히 관련이 있습니다. 

## <a name="scope"></a>범위

이 소개에서는 미리 읽어볼 자료를 제안하고 암호 감사 및 전략 권장 사항을 제공합니다. 또한 이 문서에서는 완전 클라우드 기반 Azure 환경뿐 아니라 하이브리드 Azure 환경에도 사용할 수 있는 도구를 간략하게 설명합니다. 마지막으로, SIEM(보안 정보 및 이벤트 관리) 전략과 환경을 구성하고 모니터링 및 경고하는 데 사용할 수 있는 데이터 원본 목록을 제공합니다. 나머지 지침은 다음 영역의 모니터링 및 경고 전략을 제공합니다.

* [사용자 계정](security-operations-user-accounts.md) – 비정상적인 계정 생성 및 사용, 비정상적인 로그인을 포함하여 관리자 권한이 없는 권한 없는 사용자 계정과 관련된 지침입니다.

* [권한 있는 계정](security-operations-privileged-accounts.md) – Azure AD 역할 할당, Azure 리소스 역할 할당, Azure 리소스 및 구독에 대한 액세스 관리를 포함하여 관리 작업을 수행할 수 있는 상승된 권한이 있는 권한 있는 사용자 계정과 관련된 지침입니다.

* [PIM(Privileged Identity Management)](security-operations-privileged-identity-management.md) – PIM을 사용하여 리소스에 대한 액세스를 관리, 제어, 모니터링하는 방법과 관련된 지침입니다. 

* [애플리케이션](security-operations-applications.md) – 애플리케이션에 대한 인증을 제공하는 데 사용되는 계정과 관련된 지침입니다. 

* [디바이스](security-operations-devices.md) – 정책 외부에서 등록되거나 조인된 디바이스, 비준수 사용, 디바이스 관리 역할 관리, 가상 머신 로그인에 대한 모니터링 및 경고와 관련된 지침입니다.

*  [인프라](security-operations-infrastructure.md) – 하이브리드와 완전 클라우드 기반 환경에 대한 위협 모니터링 및 경고와 관련된 지침입니다.

## <a name="important-reference-content"></a>중요한 참조 콘텐츠

Microsoft에는 요구 사항에 맞게 IT 환경을 사용자 지정할 수 있게 해주는 많은 제품과 서비스가 있습니다. 모니터링 및 경고 전략의 일부로, 운영 환경과 관련된 다음 지침을 검토하는 것이 좋습니다.

* Windows 운영 체제

   * [Windows 10 및 Windows Server 2016 보안 감사와 모니터링 참조](https://www.microsoft.com/download/details.aspx?id=52630)

   * [Windows 10 v1909 및 Windows Server v1909에 대한 보안 기준(최종)](https://techcommunity.microsoft.com/t5/microsoft-security-baselines/security-baseline-final-for-windows-10-v1909-and-windows-server/ba-p/1023093)

* 온-프레미스 환경

   * [Microsoft Defender for Identity 아키텍처](/defender-for-identity/architecture)

   * [Active Directory에 Microsoft Defender for Identity 연결 빠른 시작](/defender-for-identity/install-step2)

   * [Microsoft Defender for Identity에 대한 Azure 보안 기준](/defender-for-identity/security-baseline)

   * [Active Directory에서 손상 징후 모니터링](/windows-server/identity/ad-ds/plan/security-best-practices/monitoring-active-directory-for-signs-of-compromise)

* 클라우드 기반 Azure 환경


   * [Azure AD 로그인 로그를 사용하여 로그인 모니터링](../reports-monitoring/concept-all-sign-ins.md)

   * [Azure Active Directory 포털의 감사 활동 보고서](../reports-monitoring/concept-audit-logs.md)

   * [Azure Active Directory Identity Protection을 사용하여 위험 조사](../identity-protection/howto-identity-protection-investigate-risk.md) 

   * [Azure Sentinel에 Azure AD Identity Protection 데이터 연결](../../sentinel/connect-azure-ad-identity-protection.md)

* AD DS(Active Directory Domain Services)

   * [감사 정책 권장 사항](/windows-server/identity/ad-ds/plan/security-best-practices/audit-policy-recommendations)

* AD FS(Active Directory Federation Services)

   * [AD FS 문제 해결 - 이벤트 감사 및 로깅](/windows-server/identity/ad-fs/troubleshooting/ad-fs-tshoot-logging)

## <a name="data-sources"></a>데이터 원본 

조사 및 모니터링에 사용하는 로그 파일은 다음과 같습니다.

* [Azure AD 감사 로그](../reports-monitoring/concept-audit-logs.md)

* [로그인 로그](../reports-monitoring/concept-all-sign-ins.md)

* [Microsoft 365 감사 로그](/microsoft-365/compliance/auditing-solutions-overview?view=o365-worldwide)

* [Azure Key Vault 로그](../../key-vault/general/logging.md?tabs=Vault)

Azure Portal에서 Azure AD 감사 로그를 보고, CSV(쉼표로 구분된 값) 또는 JSON(JavaScript Object Notation) 파일로 다운로드할 수 있습니다. Azure Portal에는 모니터링과 경고를 더 효율적으로 자동화하는 다른 도구와 Azure AD 로그를 통합하는 몇 가지 방법이 있습니다.

* **[Azure Sentinel](../../sentinel/overview.md)** - SIEM(보안 정보 및 이벤트 관리) 기능을 제공하여 엔터프라이즈 수준에서 인텔리전트 보안 분석을 지원합니다. 

* **[Azure Monitor](../../azure-monitor/overview.md)** - 다양한 조건을 자동으로 모니터링하고 경고할 수 있습니다. 통합 문서를 만들거나 사용하여 다양한 원본의 데이터를 결합할 수 있습니다.

* **SIEM과 통합된 [Azure Event Hubs](../../event-hubs/event-hubs-about.md)** - Azure Event Hubs 통합을 통해 Splunk, ArcSight, QRadar, Sumo Logic 등의 [다른 SIEM에 Azure AD 로그를 통합할 수 있습니다](../reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md).

* **[MCAS(Microsoft Cloud App Security)](/cloud-app-security/what-is-cloud-app-security)** - 앱을 검색 및 관리하고, 앱과 리소스를 제어하고, 클라우드 앱의 규정 준수를 확인할 수 있습니다.

모니터링하고 경고할 항목의 대부분은 조건부 액세스 정책의 결과입니다. [조건부 액세스 인사이트 및 보고 통합 문서](../conditional-access/howto-conditional-access-insights-reporting.md)를 사용하여 로그인에 대한 하나 이상의 조건부 액세스 정책과 디바이스 상태를 포함한 정책 결과를 조사할 수 있습니다. 이 통합 문서를 사용하면 영향 요약을 살펴보고 특정 기간 동안의 영향을 확인할 수 있습니다. 통합 문서를 사용하여 특정 사용자의 로그인을 조사할 수도 있습니다. 

이 문서의 나머지 부분에서는 모니터링하고 경고하는 것이 좋은 사항에 대해 설명하며 위협 유형별로 구성되어 있습니다. 미리 빌드된 특정 솔루션이 있는 경우 해당 솔루션에 연결하거나 표 아래에 샘플을 제공합니다. 그러지 않으면 앞의 도구를 사용하여 경고를 빌드할 수 있습니다. 

* **[ID 보호](../identity-protection/overview-identity-protection.md)** - 조사에 활용할 수 있는 다음 세 개의 주요 보고서를 생성합니다.

   * **위험 사용자** - 위험에 노출된 사용자, 검색 세부 정보, 모든 위험한 로그인 기록, 위험 기록에 대한 정보를 포함합니다.

   * **위험한 로그인** - 의심스러운 상황을 나타낼 수 있는 로그인 상황과 관련된 정보를 포함합니다. 이 보고서의 정보를 조사하는 방법에 대한 자세한 내용은 [방법: 위험 조사](../identity-protection/howto-identity-protection-investigate-risk.md)를 참조하세요. 

   *  **위험 검색** - 로그인 및 사용자 위험을 알리는 Azure AD ID 보호에서 검색한 위험 신호에 대한 정보를 포함합니다. 자세한 내용은 [사용자 계정에 대한 Azure AD 보안 작업 가이드](security-operations-user-accounts.md)를 참조하세요.

### <a name="data-sources-for-domain-controller-monitoring"></a>도메인 컨트롤러 모니터링을 위한 데이터 원본

최상의 결과를 얻으려면 Microsoft Defender for Identity를 사용하여 도메인 컨트롤러를 모니터링하는 것이 좋습니다. 이렇게 하면 최상의 검색 및 자동화 기능을 사용할 수 있습니다. 다음 지침을 따르세요.

* [Microsoft Defender for Identity 아키텍처](/defender-for-identity/architecture)

* [Active Directory에 Microsoft Defender for Identity 연결 빠른 시작](/defender-for-identity/install-step2)

Microsoft Defender for Identity를 사용할 계획이 없는 경우 [이벤트 로그 메시지](/windows-server/identity/ad-ds/plan/security-best-practices/monitoring-active-directory-for-signs-of-compromise)를 통해 또는 [PowerShell cmdlet을 실행](/windows-server/identity/ad-ds/deploy/troubleshooting-domain-controller-deployment)하여 도메인 컨트롤러를 모니터링할 수 있습니다. 

## <a name="components-of-hybrid-authentication"></a>하이브리드 인증의 구성 요소

Azure 하이브리드 환경의 일부로, 다음 요소가 모니터링 및 경고 전략의 기준으로 포함되어야 합니다. 

* **PTA 에이전트** – 통과 인증 에이전트는 통과 인증을 사용하도록 설정하는 데 사용되며 온-프레미스에 설치됩니다. 에이전트 버전을 확인하는 방법과 다음 단계에 대한 자세한 내용은 [Azure AD 통과 인증 에이전트: 버전 릴리스 기록](../hybrid/reference-connect-pta-version-history.md)을 참조하세요. 

* **AD FS/WAP** – Azure AD FS(Azure Active Directory Federation Services)와 WAP(웹 애플리케이션 프록시)를 사용하면 보안과 엔터프라이즈 경계 간에 디지털 ID 및 자격 권한을 안전하게 공유할 수 있습니다. 보안 모범 사례에 대한 자세한 내용은 [Active Directory Federation Services 보안을 위한 모범 사례]/windows-server/identity/ad-fs/deployment/best-practices-securing-ad-fs)를 참조하세요. 

* **Azure AD Connect Health 에이전트** – Azure AD Connect Health에 대한 통신 링크를 제공하는 데 사용되는 에이전트입니다. 에이전트를 설치하는 방법에 대한 자세한 내용은 [Azure AD Connect Health 에이전트 설치](../hybrid/how-to-connect-health-agent-install.md)를 참조하세요. 

* **Azure AD Connect 동기화 엔진** - 온-프레미스 구성 요소입니다(동기화 엔진이라고도 함). 기능에 대한 자세한 내용은 [Azure AD Connect 동기화 서비스 기능](../hybrid/how-to-connect-syncservice-features.md)을 참조하세요.

* **암호 보호 DC 에이전트** – Azure 암호 보호 DC 에이전트는 이벤트 로그 메시지를 모니터링하고 보고하는 데 사용됩니다. 자세한 내용은 ../authentication/concept-password-ban-bad-on-premises.md를 참조하세요.

* **암호 필터 DLL** – DC 에이전트의 암호 필터 DLL은 운영 체제에서 사용자 암호 유효성 검사 요청을 받습니다. 이 필터는 DC에서 로컬로 실행되는 DC 에이전트 서비스에 요청을 전달합니다. DLL을 사용하는 방법에 대한 자세한 내용은 [Active Directory Domain Services에 온-프레미스 Azure AD 암호 보호 적용](../authentication/concept-password-ban-bad-on-premises.md)을 참조하세요. 

* **비밀번호 쓰기 저장 에이전트** – 비밀번호 쓰기 저장은 [Azure AD Connect](../hybrid/whatis-hybrid-identity.md)를 통해 활성화되며 클라우드의 암호 변경 내용을 기존 온-프레미스 디렉터리에 실시간으로 쓰기 저장할 수 있는 기능입니다. 이 기능에 대한 자세한 내용은 [Azure Active Directory에서 셀프 서비스 암호 재설정 쓰기 저장의 작동 방식](../authentication/concept-sspr-writeback.md)을 참조하세요.

* **Azure AD 애플리케이션 프록시 커넥터** – 온-프레미스에서 애플리케이션 프록시 서비스로의 아웃바운드 연결을 지원하는 경량 에이전트입니다. 자세한 내용은 [Azure ADF 애플리케이션 프록시 커넥터 이해](../app-proxy/application-proxy-connectors.md)를 참조하세요. 

## <a name="components-of-cloud-based-authentication"></a>클라우드 기반 인증의 구성 요소

Azure 클라우드 기반 환경의 일부로, 다음 요소가 모니터링 및 경고 전략의 기준으로 포함되어야 합니다.

* **Azure AD 애플리케이션 프록시** - 이 클라우드 서비스는 온-프레미스 웹 애플리케이션에 대한 보안 원격 액세스를 제공합니다. 자세한 내용은 [Azure AD 애플리케이션 프록시를 통해 온-프레미스 애플리케이션에 원격으로 액세스](../app-proxy/application-proxy-connectors.md)를 참조하세요. 

* **Azure AD Connect** – Azure AD Connect 솔루션에 사용되는 서비스입니다. 자세한 내용은 [Azure AD Connect란?](../hybrid/whatis-azure-ad-connect.md)을 참조하세요.

* **Azure AD Connect Health** – Service Health는 서비스를 사용하는 지역의 Azure 서비스 상태를 추적하는 사용자 지정 가능한 대시보드를 제공합니다. 자세한 내용은 [Azure AD Connect Health](../hybrid/whatis-azure-ad-connect.md)를 참조하세요.

* **Azure MFA** – Azure AD Multi-Factor Authentication을 사용하는 경우 사용자가 인증을 위해 둘 이상 형식의 증명을 제공해야 합니다. 이렇게 하면 환경을 보호하기 위한 첫 번째 사전 대응형 단계를 제공할 수 있습니다. 자세한 내용은 [작동 방식: Azure AD Multi-Factor Authentication](../authentication/concept-mfa-howitworks.md)을 참조하세요.

* **동적 그룹** – Azure AD(Azure Active Directory) 관리자에 대한 보안 그룹 멤버 자격의 동적 구성은 사용자 특성에 따라 Azure AD에 생성된 그룹을 채우는 규칙을 설정할 수 있습니다. 자세한 내용은 [동적 그룹 및 Azure Active Directory B2B 협업](../external-identities/use-dynamic-groups.md)을 참조합니다.

* **조건부 액세스** – 조건부 액세스는 Azure Active Directory에서 신호를 모으고, 의사 결정을 내리고, 조직 정책을 적용하는 데 사용하는 도구입니다. 조건부 액세스는 새로운 ID 중심 제어 평면의 핵심입니다. 자세한 내용은 [조건부 액세스란?](../conditional-access/overview.md)을 참조하세요.

* **ID 보호** – 조직에서 ID 기반 위험 검색 및 수정을 자동화하고, 포털의 데이터를 사용하여 위험을 조사하며, 위험 검색 데이터를 SIEM으로 내보낼 수 있게 해주는 도구입니다. 자세한 내용은 [ID 보호란?](../identity-protection/overview-identity-protection.md)을 참조하세요.

* **그룹 기반 라이선스** – 라이선스를 사용자에게 직접 할당하지 않고 그룹에 할당할 수 있습니다. Azure AD는 사용자에 대한 라이선스 할당 상태에 대한 정보를 저장합니다. 

* **프로비저닝 서비스** – 프로비저닝은 사용자가 액세스해야 하는 클라우드 애플리케이션에서 사용자 ID와 역할을 만드는 작업을 가리킵니다. 자동 프로비저닝에는 사용자 ID를 생성하는 것 외에도 상태 또는 역할이 변경될 때 사용자 ID의 유지 관리 및 제거가 포함됩니다. 자세한 내용은 [Azure Active Directory에서 애플리케이션 프로비저닝이 작동하는 방식](../app-provisioning/how-provisioning-works.md)을 참조하세요.

* **Graph API** – Microsoft Graph API는 Microsoft 클라우드 서비스 리소스에 액세스할 수 있게 해주는 RESTful 웹 API입니다. 앱을 등록하고 사용자 또는 서비스에 대한 인증 토큰을 가져온 후 Microsoft Graph API에 대한 요청을 수행할 수 있습니다. 자세한 내용은 [Microsoft Graph 개요](/graph/overview)를 참조하세요.

* **도메인 서비스** – AD DS(Azure Active Directory Domain Services)는 도메인 가입, 그룹 정책 등의 관리되는 도메인 서비스를 제공합니다. 자세한 내용은 [Azure Active Directory Domain Services란?](../../active-directory-domain-services/overview.md)을 참조하세요.

* **Azure Resource Manager** – Azure Resource Manager는 Azure용 배포 및 관리 서비스입니다. Azure 계정에서 리소스를 만들고, 업데이트하고, 삭제할 수 있는 관리 계층을 제공합니다. 자세한 내용은 [Azure Resource Manager란?](../../azure-resource-manager/management/overview.md)을 참조하세요.

* **관리 ID** – 관리 ID를 사용하면 개발자가 자격 증명을 관리할 필요가 없습니다. 관리 ID는 Azure AD(Azure Active Directory) 인증을 지원하는 리소스에 연결할 때 사용할 애플리케이션의 ID를 제공합니다. 자세한 내용은 [Azure 리소스에 대한 관리 ID란?](../managed-identities-azure-resources/overview.md)을 참조하세요.

* **Privileged Identity Management** – PIM(Privileged Identity Management)은 조직의 중요한 리소스에 대한 액세스를 관리, 제어, 모니터링할 수 있게 해주는 Azure AD(Azure Active Directory) 서비스입니다. 자세한 내용은 [Azure AD Privileged Identity Management가 무엇인가요](../privileged-identity-management/pim-configure.md)를 참조하세요.

* **액세스 검토** – Azure AD(Azure Active Directory) 액세스 검토를 사용하면 조직에서 그룹 멤버 자격, 엔터프라이즈 애플리케이션에 대한 액세스, 역할 할당을 효율적으로 관리할 수 있습니다. 사용자의 액세스는 정기적으로 검토하여 적합한 사용자만 계속 액세스할 수 있도록 합니다. 자세한 내용은 [Azure AD 액세스 검토란?](../governance/access-reviews-overview.md)을 참조하세요.

* **권한 관리** – Azure AD(Azure Active Directory) 권한 관리는 조직에서 액세스 요청 워크플로, 액세스 할당, 검토, 만료를 자동화하여 ID 및 액세스 수명 주기를 대규모로 관리할 수 있게 해주는 [ID 거버넌스](../governance/identity-governance-overview.md) 기능입니다. 자세한 내용은 [Azure AD 권한 관리가 무엇인가요?](../governance/entitlement-management-overview.md)를 참조하세요.

* **활동 로그** - 활동 로그는 구독 수준 이벤트에 대한 인사이트를 제공하는 Azure [플랫폼 로그](../../azure-monitor/essentials/platform-logs-overview.md)입니다. 여기에는 리소스가 수정되거나 가상 머신이 시작되는 등의 이벤트 정보가 포함됩니다. 자세한 내용은 [Azure 활동 로그](../../azure-monitor/essentials/activity-log.md)를 참조하세요.

* **셀프 서비스 암호 재설정 서비스** – Azure AD(Azure Active Directory) SSPR(셀프 서비스 암호 재설정)을 사용하면 관리자나 지원 센터에서 개입하지 않고도 사용자가 암호를 변경하거나 재설정할 수 있습니다. 자세한 내용은 [작동 방법: Azure AD 셀프 서비스 암호 재설정](../authentication/concept-sspr-howitworks.md)을 참조하세요.

* **디바이스 서비스** – 디바이스 ID 관리는 [디바이스 기반 조건부 액세스](../conditional-access/require-managed-devices.md)의 기초입니다. 디바이스 기반의 조건부 액세스 정책을 사용할 경우 관리 디바이스를 통해서만 환경의 리소스에 액세스하도록 할 수 있습니다. 자세한 내용은 [디바이스 ID란?](../devices/overview.md)을 참조하세요.

* **셀프 서비스 그룹 관리** – 사용자가 Azure AD(Azure Active Directory)에서 고유한 보안 그룹이나 Microsoft 365 그룹을 만들고 관리하도록 설정할 수 있습니다. 그룹 소유자는 멤버 자격 요청을 승인하거나 거부할 수 있으며, 그룹 멤버 자격에 대한 제어를 위임할 수 있습니다. 셀프 서비스 그룹 관리 기능은 보안 그룹 및 Office 365 그룹에 대해서만 사용할 수 있지만 메일 사용 가능 보안 그룹 및 메일링 그룹에는 사용할 수 없습니다. 자세한 내용은 [Azure Active Directory에서 셀프 서비스 그룹 관리 설정](../enterprise-users/groups-self-service-management.md)을 참조하세요.

* **위험 검색** – 위험 검색 시 트리거되는 기타 위험에 대한 정보, 로그인 위치와 같은 기타 관련 정보, MCAS(Microsoft Cloud App Security)의 세부 정보를 포함합니다.

## <a name="next-steps"></a>다음 단계

다음 보안 작업 가이드 문서를 참조하세요.

[Azure AD 보안 작업 개요](security-operations-introduction.md)

[사용자 계정에 대한 보안 작업](security-operations-user-accounts.md)

[권한 있는 계정에 대한 보안 작업](security-operations-privileged-accounts.md)

[Privileged Identity Management에 대한 보안 작업](security-operations-privileged-identity-management.md)

[애플리케이션에 대한 보안 작업](security-operations-applications.md)

[디바이스에 대한 보안 작업](security-operations-devices.md)

 
[인프라에 대한 보안 작업](security-operations-infrastructure.md)
