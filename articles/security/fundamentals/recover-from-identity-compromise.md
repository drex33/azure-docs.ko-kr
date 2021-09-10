---
title: Microsoft 및 Azure 보안 리소스를 사용하여 시스템 ID 손상으로부터 복구 | Microsoft Docs
description: 'Microsoft 및 Azure 보안 리소스(예: Microsoft 365 Defender, Azure Sentinel, Azure Active Directory, Azure Security Center)와 Microsoft 권장 사항을 사용하여 2020년 12월 Nobelium 공격(Solorigate)과 유사한 시스템 ID 손상으로부터 시스템을 보호하는 방법을 알아봅니다.'
services: sentinel
documentationcenter: na
author: batamig
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/17/2021
ms.author: bagol
ms.openlocfilehash: f2f99e7c9a901b6e76b3d4bbe87967527f836d1b
ms.sourcegitcommit: 7854045df93e28949e79765a638ec86f83d28ebc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2021
ms.locfileid: "122864346"
---
# <a name="recovering-from-systemic-identity-compromise"></a>시스템 ID 손상으로부터 복구

이 문서에서는 2020년 12월 [Nobelium](https://aka.ms/solorigate) 공격과 같은 조직에 대한 시스템 ID 손상 공격으로부터 복구하기 위한 Microsoft 리소스 및 권장 사항에 대해 설명합니다.

이 문서의 내용은 Microsoft의 DART(Detection and Response Team)에서 제공하는 지침을 기반으로 합니다. 이 팀은 손상에 대응하고 고객이 사이버 복원력을 갖도록 지원합니다. DART 팀의 자세한 지침은 이 팀의 [Microsoft 보안 블로그 시리즈](https://www.microsoft.com/security/blog/microsoft-detection-and-response-team-dart-blog-series/)를 참조하세요.

많은 조직이 ID 및 액세스 관리에 대한 보안을 강화하기 위해 클라우드 기반 접근 방식으로 전환했습니다. 하지만 온-프레미스 시스템을 보유하고 다양한 하이브리드 아키텍처 메서드를 사용하는 조직도 있을 수도 있습니다. 이 문서에서는 시스템 ID 공격이 클라우드, 온-프레미스 및 하이브리드 시스템에 영향을 준다는 사실을 인지하고 이러한 모든 환경에 대한 권장 사항 및 참조를 제공합니다.

> [!IMPORTANT]
> 이 정보는 있는 그대로 제공되며 일반화된 지침을 구성합니다. 이 지침을 IT 환경 및 테넌트에 적용하는 방법에 대한 최종적인 결정에는 고유한 환경과 요구 사항이 고려되어야 하며, 각 고객은 이러한 사항을 확인할 수 있는 가장 좋은 위치에 있습니다.
>

## <a name="about-systemic-identity-compromise"></a>시스템 ID 손상

조직에 대한 시스템 ID 손상 공격은 공격자가 조직의 ID 인프라 관리에 대한 발판을 성공적으로 확보할 때 발생합니다.

조직에 이런 일이 발생하면 추가 피해가 발생하기 전에 환경을 보호하기 위해 공격자와 경합해야 하는 상황에 놓이게 됩니다.

- **환경의 ID 인프라에 대한 관리 제어권이 있는 공격자** 는 해당 제어권을 사용하여 해당 환경에서 ID 및 ID 권한을 생성, 수정 또는 삭제할 수 있습니다.

    온-프레미스 손상의 경우, 신뢰할 수 있는 SAML 토큰 서명 인증서가 [HSM](../../key-vault/keys/hsm-protected-keys.md)에 저장되어 있지 않으면 신뢰할 수 있는 SAML 토큰 서명 인증서에 대한 액세스가 공격에 포함됩니다.

- 그러면 **공격자는 인증서를 사용하여 SAML 토큰을 위조** 하여(계정 자격 증명에 액세스를 요구하지 않고 어떤 흔적도 남기지 않으면서) 조직의 기존 사용자 및 계정을 가장할 수 있습니다.

- **높은 권한의 계정 액세스** 를 사용하면 공격자가 제어하는 자격 증명을 기존 애플리케이션에 추가할 수도 있습니다. 그러면 공격자는 해당 권한을 사용하여 탐지되지 않고 시스템에 액세스(예: API를 호출)할 수 있습니다.

## <a name="responding-to-the-attack"></a>공격에 대응


시스템 ID 손상에 대한 대응에는 다음 이미지와 표에 표시된 단계가 포함되어야 합니다.

:::image type="content" source="media/recover-from-identity-compromise/recover-identity-compromise.png" alt-text="ID 손상으로부터 복구하는 단계":::


|단계  |Description  |
|---------|---------|
|**보안 통신 설정**     |  시스템 ID 손상이 발생한 조직에서는 모든 통신이 영향을 받는다고 가정해야 합니다. 복구 작업을 수행하기 전에, 조사 및 대응 업무의 핵심 팀 멤버가 서로 [안전하게 통신할 수 있는지](#establish-secure-communications) 확인해야 합니다. <br><br>통신 보안은 가장 첫 번째 단계여야 합니다. 그래야 공격자 모르게 진행할 수 있습니다.|
|**환경 조사**   | 핵심 조사 팀의 통신 보안을 확보한 후에는 초기 액세스 지점 및 지속성 기술을 찾기 시작할 수 있습니다. [손상 징후를 식별](#identify-indications-of-compromise)한 다음, 초기 액세스 지점과 지속성을 찾아봅니다. 동시에 복구 작업을 수행하는 동안 [지속적인 모니터링 작업을 설정](#establish-continuous-monitoring)하기 시작합니다.        |
|**보안 상태 개선**     | 모범 사례 권장 사항에 따라 [보안 기능을 사용하도록 설정](#improve-security-posture)하여 향후 시스템 보안을 개선할 수 있도록 합니다.  <br><br>시간이 흐르고 보안 환경이 변화함에 따라 [지속적인 모니터링](#establish-continuous-monitoring) 노력을 계속해야 합니다.    |
|**제어권 되찾기/유지**     |  공격자로부터 환경에 대한 관리 제어권을 되찾아야 합니다. 제어권을 다시 확보하고 시스템의 보안 상태를 새로 고친 후에는 가능한 모든 지속성 기술과 새로운 초기 액세스 익스플로잇을 [수정하거나 차단](#remediate-and-retain-administrative-control)해야 합니다.       |
|     |         |

## <a name="establish-secure-communications"></a>보안 통신 설정

대응을 시작하기 전에, 공격자 도청 없이 안전하게 통신할 수 있는지 확인해야 합니다. 인시던트에 관련된 모든 통신을 격리하여 공격자가 조사에 대해 알 수 없도록 하고 기습적으로 대응 조치를 수행해야 합니다.

예를 들면 다음과 같습니다.

1. 초기 일대일 및 그룹 통신의 경우 PSTN 통화, 회사 인프라에 연결되지 않은 회의 브리지 및 엔드투엔드 암호화 메시징 솔루션을 사용할 수 있습니다.

    이러한 프레임워크 외부의 통신은 보안 채널을 통해 검증되지 않는 한 손상되었고 신뢰할 수 없는 것으로 여겨야 합니다.

2. 이러한 초기 대화 후에는 조직의 프로덕션 테넌트로부터 격리된 완전히 새로운 Microsoft 365 테넌트를 만들 수 있습니다. 대응에 참여해야 하는 핵심 인력에 대해서만 계정을 만듭니다.

새 Microsoft 365 테넌트를 만드는 경우 테넌트, 특히 관리 계정 및 권한에 대한 모든 모범 사례를 따라야 합니다. 외부 애플리케이션 또는 공급업체에 대한 신뢰 없이 관리 권한을 제한합니다.

> [!IMPORTANT]
> 기존(및 잠재적으로 손상될 수 있는) 이메일 계정에서 새 테넌트에 대해 통신하지 말아야 합니다. 

자세한 내용은 [Microsoft 365를 안전하게 사용하기 위한 모범 사례](https://www.microsoft.com/security/blog/2019/01/10/best-practices-for-securely-using-microsoft-365-the-cis-microsoft-365-foundations-benchmark-now-available/)를 참조하세요.

## <a name="identify-indications-of-compromise"></a>손상 징후 식별

고객은 시스템 공급자(Microsoft 및 모든 파트너 포함)의 업데이트를 따르고 제공되는 모든 새로운 탐지 및 보호 기능을 구현하고 게시된 IOC(Incidents of Compromises)를 식별하는 것이 좋습니다.

다음 Microsoft 보안 제품에서 업데이트를 확인하고 권장되는 변경 사항을 구현합니다.

- [Azure Sentinel](../../sentinel/index.yml)
- [Microsoft 365 보안 솔루션 및 서비스](/microsoft-365/security/)
- [Windows 10 Enterprise 보안](/windows/security/)
- [Microsoft Cloud App Security](/cloud-app-security/)

새 업데이트를 구현하면 이전 캠페인을 식별하고 시스템에 대한 향후 캠페인을 방지하는 데 도움이 됩니다. IOC 목록은 완전하지 않을 수 있으며 조사가 계속되면서 확장될 수 있다는 점에 유의해야 합니다.

따라서 다음 작업도 수행하는 것이 좋습니다.

- [Azure 보안 벤치마크 설명서](/security/benchmark/azure/)를 적용했는지 확인하고 [Azure Security Center](../../security-center/index.yml)를 통해 규정 준수를 모니터링하고 있는지 확인합니다.

- [Azure Sentinel](../../sentinel/understand-threat-intelligence.md)에서 Microsoft 365 데이터 커넥터를 구성하는 등의 방식으로 위협 인텔리전스 피드를 SIEM에 통합합니다.

자세한 내용은 Microsoft의 보안 설명서를 참조하세요.

- [Microsoft 보안 설명서](/security/)
- [Azure 보안 설명서](../index.yml)

## <a name="investigate-your-environment"></a>환경 조사

인시던트 대응자 및 핵심 인력이 안전하게 협업할 수 있는 곳이 확보되면 손상된 환경에 대한 조사를 시작할 수 있습니다.

모든 비정상적 동작의 진상을 파악하고 빠른 조치를 취하여 공격자의 추가 활동을 막는 데 균형을 맞춰야 합니다. 성공적인 조치를 위해서는 공격자가 사용한 초기 진입 메서드와 지속성 메서드를 가능한 한 완벽하게 이해해야 합니다. 조사 중 누락된 지속성 메서드가 있으면 공격자가 계속 액세스할 수 있게 되어 잠재적인 재손상이 발생할 수 있습니다.

이 시점에서 위험 분석을 수행하여 작업의 우선 순위를 지정하는 것이 좋습니다. 자세한 내용은 다음을 참조하세요.

- [데이터 센터 위협, 취약성 및 위험 평가](/compliance/assurance/assurance-threat-vulnerability-risk-assessment)
- [위협 분석을 통해 새로운 위협 추적 및 대응](/microsoft-365/security/defender-endpoint/threat-analytics)
- [위협 및 취약성 관리](/microsoft-365/security/defender-endpoint/next-gen-threat-and-vuln-mgt)

Microsoft의 보안 서비스는 자세한 조사를 위한 광범위한 리소스를 제공합니다. 다음 섹션에서는 가장 많이 권장되는 작업에 대해 설명합니다.


> [!NOTE]
> 나열된 로깅 원본 중 하나 이상이 현재 보안 프로그램의 일부가 아닌 경우, 탐지 및 향후 로그 검토가 가능하도록 가능한 빨리 구성하는 것이 좋습니다.
>
> 조직의 향후 조사 목표를 지원할 수 있도록 로그 보존을 구성해야 합니다. 법률, 규정 또는 보험 목적에 맞게 필요에 따라 증거를 보관하십시오.
>

### <a name="investigate-and-review-cloud-environment-logs"></a>클라우드 환경 로그 조사 및 검토

의심스러운 작업 및 공격자의 손상 징후가 있는지 클라우드 환경 로그를 조사하고 검토합니다. 예를 들어 다음 로그를 확인합니다.

- [UAL(통합 감사 로그)](/powershell/module/exchange/search-unifiedauditlog)
- [Azure AD(Azure Active Directory) 로그](../../active-directory/reports-monitoring/overview-monitoring.md)
- [Microsoft Exchange 온-프레미스 로그](/exchange/mail-flow/transport-logs/transport-logs)
- VPN 로그(예: [VPN Gateway](../../vpn-gateway/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log.md)의 로그)
- 엔지니어링 시스템 로그
- 바이러스 백신 및 엔드포인트 탐지 로그

### <a name="review-endpoint-audit-logs"></a>엔드포인트 감사 로그 검토

온-프레미스 변경 내용(예: 다음과 같은 유형의 작업)에 대한 엔드포인트 감사 로그를 검토합니다.

- 그룹 멤버 자격 변경
- 새 사용자 계정 만들기
- Active Directory 내의 위임

특히 손상 또는 활동의 다른 일반적인 징후와 함께 발생하는 이러한 변경 내용을 고려합니다.

### <a name="review-administrative-rights-in-your-environments"></a>환경의 관리 권한 검토

클라우드 및 온-프레미스 환경 모두에서 관리 권한을 검토합니다. 예를 들면 다음과 같습니다.

|환경  |Description  |
|---------|---------|
|**모든 클라우드 환경**    |       - 클라우드의 권한 있는 액세스 권한을 검토하고 불필요한 권한을 제거<br>    - PIM(Privileged Identity Management) 구현<br>    - 강화하는 동안 관리 액세스를 제한하는 조건부 액세스 정책을 설정      |
|**모든 온-프레미스 환경**     |       - 온-프레미스의 권한 있는 액세스를 검토하고 불필요한 권한을 제거<br>   - 기본 제공 그룹의 멤버 자격 줄이기<br>    - Active Directory 위임 검증<br>    - Tier 0 환경을 강화하고 Tier 0 자산에 액세스할 수 있는 사람을 제한      |
|**모든 엔터프라이즈 애플리케이션**     | 다음 작업을 허용하는 위임된 권한 및 동의 부여를 검토합니다. <br><br>  - 권한 있는 사용자 및 역할 수정 <br>- 모든 사서함 읽기 또는 액세스 <br>- 다른 사용자를 대신하여 이메일 보내기 또는 전달 <br>- 모든 OneDrive 또는 SharePoint 사이트 콘텐츠에 액세스 <br>- 디렉터리에 읽기/쓰기가 가능한 서비스 주체 추가      |
|**Microsoft 365 환경**     |다음을 포함하여 Microsoft 365 환경에 대한 액세스 및 구성 설정을 검토합니다. <br>- SharePoint Online 공유 <br>- Microsoft Teams <br>- Power Apps <br>- Microsoft OneDrive for Business          |
| **환경의 사용자 계정 검토**   |- 더 이상 필요하지 않은 게스트 사용자 계정을 검토하고 제거합니다. <br>- 대리인, 사서함 폴더 권한, ActiveSync 모바일 디바이스 등록, 받은 편지함 규칙, 웹용 Outlook 옵션에 대한 이메일 구성을 검토합니다. <br>- ApplicationImpersonation 권한을 검토하고 레거시 인증 사용을 최대한 줄입니다. <br>- MFA가 적용되어 있고 모든 사용자에 대한 MFA 및 SSPR(셀프 서비스 암호 재설정) 연락처 정보가 모두 올바른지 확인합니다.         |
|     |         |

## <a name="establish-continuous-monitoring"></a>지속적인 모니터링 설정

공격자 행동 탐지에는 여러 가지 방법이 있으며 공격에 대응하기 위해 조직에서 사용할 수 있는 보안 도구에 따라 달라집니다.

예를 들어 Microsoft 보안 서비스에는 아래 섹션에 설명된 공격과 관련된 특정 리소스 및 지침이 있을 수 있습니다.

> [!IMPORTANT]
> 조직의 전역 관리자 계정 및/또는 신뢰할 수 있는 SAML 토큰 서명 인증서에 대한 액세스를 제공하는 시스템 손상을 통해 얻은 관리 권한의 증거가 조사에 의해 발견되면, [관리 제어권을 수정하고 유지](#remediate-and-retain-administrative-control)하기 위한 조치를 취하는 것이 좋습니다.
>

### <a name="monitoring-with-azure-sentinel"></a>Azure Sentinel로 모니터링

Azure Sentinel에는 조사에 도움이 되는 기본 제공 리소스가 많이 있습니다. 예를 들어, 헌팅 통합 문서 및 분석 규칙은 환경의 관련 영역에서 공격을 탐지하는 데 도움이 될 수 있습니다.

자세한 내용은 다음을 참조하세요.

- [환경 시각화 및 분석](../../sentinel/get-visibility.md)
- [처음부터 위협 탐지](../../sentinel/detect-threats-built-in.md)

### <a name="monitoring-with-microsoft-365-defender"></a>Microsoft 365 Defender로 모니터링

엔드포인트용 Microsoft 365 Defender 및 Microsoft Defender 바이러스 백신에서 공격과 관련된 특정 지침을 확인하는 것이 좋습니다.

Microsoft 365 Defender, Microsoft 365 Defender for Identity 및 Microsoft Cloud App Security와 같은 Microsoft 보안 센터에서 탐지, 헌팅 쿼리 및 위협 분석 보고서의 다른 예를 확인하세요. 적용 범위를 보장하려면 모든 도메인 컨트롤러와 함께 ADFS 서버에 [Microsoft Defender for Identity 에이전트](/defender-for-identity/install-step4)를 설치해야 합니다.

자세한 내용은 다음을 참조하세요.

- [위협 분석을 통해 새로운 위협 추적 및 대응](/windows/security/threat-protection/microsoft-defender-atp/threat-analytics)
- [위협 분석의 분석 보고서 이해](/microsoft-365/security/defender/threat-analytics-analyst-reports)

### <a name="monitoring-with-azure-active-directory"></a>Azure Active Directory로 모니터링

Azure Active Directory 로그인 로그는 다단계 인증이 올바르게 사용되고 있는지 여부를 표시할 수 있습니다. 로그인 로그는 Azure Portal의 Azure Active Directory 영역에서 직접 액세스하거나 **Get-AzureADAuditSignInLogs** cmdlet을 사용하거나 Azure Sentinel의 **로그** 영역에서 볼 수 있습니다.

예를 들어 **MFA 결과** 필드에 **MFA 요구 사항이 토큰의 클레임으로 충족됨** 값이 있는 경우 결과를 검색하거나 필터링합니다. 조직에서 ADFS를 사용하는 경우 기록된 클레임이 ADFS 구성에 포함되어 있지 않으면 해당 클레임은 공격자 활동을 나타낼 수 있습니다.

추가 노이즈를 제외하도록 결과를 더 검색하거나 필터링합니다. 예를 들어 페더레이션된 도메인의 결과만 포함할 수 있습니다. 의심스러운 로그인이 보이면 IP 주소, 사용자 계정 등을 기반으로 더 자세히 드릴다운합니다.

다음 표에는 조사에서 Azure Active Directory 로그를 사용하는 추가 방법이 설명되어 있습니다.

|메서드  |Description  |
|---------|---------|
|**위험한 로그인 이벤트 분석**     |  Azure Active Directory 및 해당 Identity Protection 플랫폼은 공격자가 생성한 SAML 토큰 사용과 관련된 위험 이벤트를 생성할 수 있습니다. <br><br>이러한 이벤트에는 익숙하지 않은 속성, 익명의 IP 주소, 불가능한 이동 등으로 레이블을 지정할 수 있습니다.   <br><br>관리자 권한이 있는 계정(자동으로 해제되거나 수정된 계정 포함)과 관련된 모든 위험 이벤트를 자세히 분석하는 것이 좋습니다. 예를 들어 사용자가 MFA를 통과했기 때문에 위험 이벤트 또는 익명 IP 주소가 자동으로 수정될 수 있습니다. <br><br>모든 인증 이벤트가 Azure AD에서 표시되도록 [ADFS Connect Health](../../active-directory/hybrid/how-to-connect-health-adfs.md)를 사용해야 합니다. |
|**도메인 인증 속성 탐지**     |  공격자가 도메인 인증 정책을 조작하려는 시도가 있으면 Azure Active Directory 감사 로그에 기록되고 통합 감사 로그에 반영됩니다. <br><br> 예를 들어 통합 감사 로그, Azure AD 감사 로그 및/또는 SIEM 환경에서 **도메인 인증 설정** 과 관련된 이벤트를 검토하고 나열된 모든 활동이 예상된 것이고 계획된 것인지 확인합니다.   |
|**OAuth 애플리케이션에 대한 자격 증명 탐지**     |  권한 있는 계정에 대한 제어권을 확보한 공격자는 조직의 모든 사용자 이메일에 액세스할 수 있는 애플리케이션을 검색한 다음, 그 애플리케이션에 공격자가 제어하는 자격 증명을 추가할 수 있습니다. <br><br>예를 들어 공격자 행동과 일치하는 다음 활동을 검색할 수 있습니다. <br>- 서비스 주체 자격 증명 추가 또는 업데이트 <br>- 애플리케이션 인증서 및 비밀 업데이트 <br>- 사용자에게 앱 역할 할당 권한 부여 추가 <br>- Oauth2PermissionGrant 추가 |
|**애플리케이션에서 이메일 액세스 탐지**     |  사용자 환경의 애플리케이션에서 이메일에 대한 액세스를 검색합니다. 예를 들어 [Microsoft 365 고급 감사 기능](/microsoft-365/compliance/mailitemsaccessed-forensics-investigations)을 사용하여 손상된 계정을 조사합니다. |
|**서비스 주체에 대한 비대화형 로그인 탐지**     | Azure Active Directory 로그인 보고서는 서비스 주체 자격 증명을 사용한 비대화형 로그인에 대한 세부 정보를 제공합니다.  예를 들어 로그인 보고서를 사용하면 공격자가 이메일 애플리케이션에 액세스하는 데 사용한 IP 주소와 같이 조사에 유용한 데이터를 찾을 수 있습니다.        |
|     |         |


## <a name="improve-security-posture"></a>보안 상태 개선

시스템에서 보안 이벤트가 발생한 경우 현재 보안 전략 및 우선 순위를 반영하는 것이 좋습니다.

새로운 위협에 직면한 조직이 우선 순위를 둬야 하는 투자에 대한 권장 사항을 인시던트 대응자에게 질문하는 경우가 많습니다.

이 문서에 설명된 권장 사항 외에도 공격자가 사용하는 후속 공격 기술과 이를 가능하게 하는 일반적인 보안 상태 간격에 대응하는 중점 영역의 우선 순위를 정하는 것이 좋습니다.

다음 섹션에는 일반 보안 상태와 ID 보안 상태를 모두 개선하기 위한 권장 사항이 나열되어 있습니다.

### <a name="improve-general-security-posture"></a>일반 보안 상태 개선

일반적인 보안 상태를 보장하기 위해 다음 작업을 수행하는 것이 좋습니다.

- **[Microsoft 보안 점수](/microsoft-365/security/mtp/microsoft-secure-score)를 검토하여** 사용하는 Microsoft 제품 및 서비스에 맞게 사용자 지정된 보안 기본 권장 사항을 확인하세요.

- [엔드포인트용 Microsoft 365 Defender](/microsoft-365/security/defender/microsoft-365-defender) 및 [Azure Sentinel](../../sentinel/overview.md)과 같은 **EDR 및 SIEM 솔루션이 조직에 있는지 확인** 합니다.

- **Microsoft의 [엔터프라이즈 액세스 모델](/security/compass/privileged-access-access-model)을 검토합니다**.

### <a name="improve-identity-security-posture"></a>ID 보안 상태 개선

ID 관련 보안 상태를 보장하기 위해 다음 작업을 수행하는 것이 좋습니다.

- **Microsoft의 [ID 인프라 보안을 위한 5단계](steps-secure-identity.md)를 검토** 하고 ID 아키텍처에 적합한 단계의 우선 순위를 지정합니다

- 인증 정책에 대해 **[Azure AD 보안 기본값으로 마이그레이션하는 것이 좋습니다](../../active-directory/fundamentals/concept-fundamentals-security-defaults.md)** .

- **조직의 레거시 인증 사용을 제거** 합니다(시스템이나 애플리케이션에서 여전히 필요한 경우). 자세한 내용은 [조건부 액세스를 사용하여 Azure AD에 대한 레거시 인증 차단](../../active-directory/conditional-access/block-legacy-authentication.md)을 참조하세요.

    > [!NOTE]
    > Exchange 팀은 2021년 하반기에 [EAS, EWS, POP, IMAP 및 RPS 프로토콜에 대한 기본 인증을 사용하지 않도록 설정](https://developer.microsoft.com/en-us/office/blogs/deferred-end-of-support-date-for-basic-authentication-in-exchange-online/)할 계획입니다.
    >
    > 명확히 하자면 보안 기본값과 인증 정책은 별개이지만 보완적인 기능을 제공합니다.
    >
    > 인증 정책을 사용하여 Exchange Online 프로토콜의 하위 집합에 대한 기본 인증을 해제하거나 대규모 조직에서 기본 인증을 점진적으로 해제하는 것이 좋습니다.
    >

- **ADFS 인프라와 AD Connect 인프라를 Tier 0 자산으로 처리합니다**.

- ADFS 서비스를 실행하는 데 사용되는 계정을 포함하여 **시스템에 대한 로컬 관리 액세스를 제한** 합니다.

    ADFS를 실행하는 계정에 필요한 최소 권한은 *서비스로 로그온* 사용자 권한 할당입니다.

- 원격 데스크톱용 Windows 방화벽 정책을 사용하여 **제한된 사용자 및 제한된 IP 주소 범위에서 관리 액세스를 제한** 합니다.

    Tier 0 점프 상자 또는 이와 동등한 시스템을 설정하는 것이 좋습니다.

- 환경 어디에서나 시스템에 대한 **모든 인바운드 SMB 액세스를 차단** 합니다. 자세한 내용은 [Beyond the Edge: How to Secure SMB Traffic in Windows](https://techcommunity.microsoft.com/t5/itops-talk-blog/beyond-the-edge-how-to-secure-smb-traffic-in-windows/ba-p/1447159)를 참조하세요. 또한 기록 및 사전 모니터링을 위해 Windows 방화벽 로그를 SIEM으로 스트리밍하는 것이 좋습니다.

- 서비스 계정을 사용 중이고 환경에서 지원되는 경우 **서비스 계정에서 gMSA(그룹 관리 서비스 계정)로 마이그레이션** 합니다. gMSA로 이동할 수 없으면 서비스 계정의 암호를 복잡한 암호로 교체합니다.

- **ADFS 시스템에서 자세한 정보 로깅을 사용하도록 설정되어 있는지 확인합니다**. 예를 들어 다음 명령을 실행합니다.

    ```powershell
    Set-AdfsProperties -AuditLevel verbose
    Restart-Service -Name adfssrv
    Auditpol.exe /set /subcategory:”Application Generated” /failure:enable /success:enable
    ```

## <a name="remediate-and-retain-administrative-control"></a>관리 제어권 수정 및 유지

조사 결과 조직의 클라우드 또는 온-프레미스 환경에서 공격자가 관리 제어권을 갖고 있는 것으로 확인되면 공격자가 지속할 수 없는 방식으로 제어권을 되찾아야 합니다.

이 섹션에서는 관리 제어권 복구 계획을 빌드할 때 고려할 수 있는 방법과 단계를 제공합니다.

> [!IMPORTANT]
> 조직에 필요한 정확한 단계는 조사에서 발견한 지속성 및 조사가 완료되었다고 확신하는 정도와 가능한 모든 진입 및 지속성 메서드를 파악했다고 확신하는 정도에 따라 달라집니다.
>
> 모든 작업이 [클린 소스](/security/compass/privileged-access-access-model)에서 빌드된 신뢰할 수 있는 디바이스에서 수행되었는지 확인합니다. 예를 들어, 새로운 [권한이 부여된 액세스 워크스테이션](/security/compass/privileged-access-deployment)을 사용합니다.
>

다음 섹션에는 관리 제어권을 수정하고 유지하기 위한 다음과 같은 유형의 권장 사항이 포함되어 있습니다.

- 현재 서버에서 신뢰 제거
- SAML 토큰 서명 인증서 순환 또는 필요한 경우 ADFS 서버 교체
- 클라우드 또는 온-프레미스 환경에 대한 특정 수정 활동

### <a name="remove-trust-on-your-current-servers"></a>현재 서버에서 신뢰 제거

토큰 서명 인증서 또는 페더레이션 트러스트에 대한 제어를 상실한 경우 가장 확실한 방법은 트러스트를 제거하고 온-프레미스를 수정하는 동안 클라우드 마스터 ID로 전환하는 것입니다.

트러스트를 제거하고 클라우드 마스터 ID로 전환하려면 세심한 계획과 ID 격리가 비즈니스 운영에 미치는 영향에 대한 심층적인 이해가 필요합니다. 자세한 내용은 [온-프레미스 공격으로부터 Microsoft 365 보호](../../active-directory/fundamentals/protect-m365-from-on-premises-attacks.md)를 참조하세요.

### <a name="rotate-your-saml-token-signing-certificate"></a>SAML 토큰 서명 인증서 순환

온-프레미스에서 관리 제어권을 복구하는 동안 [트러스트를 제거](#remove-trust-on-your-current-servers)하지 않기로 결정한 경우에는 온-프레미스에서 관리 제어권을 되찾은 후 SAML 토큰 서명 인증서를 교체하고 공격자가 서명 인증서에 다시 액세스할 수 없도록 차단해야 합니다.

토큰 서명 인증서를 한 번만 순환하면 이전 토큰 서명 인증서가 계속 작동할 수 있습니다. 이전 인증서가 계속 작동하도록 허용하는 것은 정상적인 인증서 순환을 위한 기본 제공 기능입니다. 이를 통해, 인증서가 만료되기 전에 조직이 신뢰 당사자 트러스트를 업데이트할 수 있는 유예 기간이 허용됩니다.

공격이 있었다면 공격자가 액세스 권한을 절대로 유지하면 안 됩니다. 다음 단계를 사용하여 공격자가 도메인에 대한 토큰을 위조하는 기능을 유지하지 못하도록 합니다.

> [!CAUTION]
> 이 절차의 마지막 단계에서는 휴대폰, 현재 웹 메일 세션, 연결된 토큰 및 새로 고침 토큰을 사용하는 기타 항목에서 사용자를 로그아웃합니다.
>

> [!TIP]
> ADFS 환경에서 이러한 단계를 수행하면 기본 인증서와 보조 인증서가 모두 만들어지고, 기본 기간인 5일이 지나면 보조 인증서가 기본 인증서로 자동 승격됩니다.
>
> 신뢰 당사자 트러스트가 있는 경우 초기 ADFS 환경이 변경되고 5일 후에 영향을 미칠 수 있으므로 계획에 감안해야 합니다. 기본 인증서를 세 번째로 교체하거나, **긴급** 플래그를 다시 사용하거나, 보조 인증서를 제거하거나 자동 인증서 순환을 해제하여 이 문제를 해결할 수도 있습니다.
>

**토큰 서명 인증서를 완전히 순환하고 공격자가 새 토큰을 위조하지 못하도록 막으려면 다음을 수행합니다.**

1. **AutoCertificateRollover** 매개 변수가 **True** 로 설정되어 있는지 확인합니다.

    ``` powershell
    Get-AdfsProperties | FL AutoCert*, Certificate*
    ```
    **AutoCertificateRollover** 가 **True** 로 설정되어 있지 않으면 값을 다음과 같이 설정합니다.

    ``` powershell
    Set-ADFSProperties -AutoCertificateRollover $true
    ```

1. Microsoft 온라인 서비스에 연결합니다.

    ``` powershell
    Connect-MsolService
    ```

1. 다음 명령을 실행하고 온-프레미스 및 클라우드 토큰 서명 인증서 지문 및 만료 날짜를 기록해 둡니다.

    ``` powershell
    Get-MsolFederationProperty -DomainName <domain>
    ```

    예를 들면 다음과 같습니다.

    ```powershell
    ...
    [Not Before]
        12/9/2020 7:57:13 PM

    [Not After]
        12/9/2021 7:57:13 PM

    [Thumbprint]
        3UD1JG5MEFHSBW7HEPF6D98EI8AHNTY22XPQWJFK6
    ```

1. **긴급** 스위치를 사용하여 기본 토큰 서명 인증서를 교체합니다. 이 명령을 실행하면 ADFS가 기본 인증서를 보조 인증서로 만들지 않고 즉시 교체합니다.

    ```powershell
    Update-AdfsCertificate -CertificateType Token-Signing -Urgent
    ```

1. **긴급** 스위치 없이 보조 토큰 서명 인증서를 만듭니다. 이 명령은 Azure Cloud와 동기화하기 전에 두 개의 온-프레미스 토큰 서명 인증서를 허용합니다.

    ```powershell
    Update-AdfsCertificate -CertificateType Token-Signing
    ```

1. 클라우드 게시 토큰 서명 인증서를 즉시 제거하도록 온-프레미스 기본 및 보조 인증서를 사용하여 클라우드 환경을 업데이트합니다.

    ```powershell
    Update-MsolFederatedDomain -DomainName <domain>
    ```

    > [!IMPORTANT]
    > 이 방법을 사용하여 이 단계를 수행하지 않으면 이전 토큰 서명 인증서가 여전히 사용자를 인증할 수 있습니다.

1. 이러한 단계가 올바르게 수행되었는지 확인하려면 3단계에서 이전에 표시된 인증서가 이제 제거되었는지 확인합니다.

    ```powershell
    Get-MsolFederationProperty -DomainName <domain>
    ```

1. 이전 토큰을 사용하여 액세스하지 못하도록 PowerShell을 통해 새로 고침 토큰을 해지합니다. 

    자세한 내용은 다음을 참조하세요.

    - [Azure Active Directory에서 사용자의 액세스 권한 취소](../../active-directory/enterprise-users/users-revoke-access.md)
    - [Revoke-AzureADUserAllRefreshToken PowerShell 설명서](/powershell/module/azuread/revoke-azureaduserallrefreshtoken)



### <a name="replace-your-adfs-servers"></a>ADFS 서버 바꾸기

[SAML 토큰 서명 인증서를 순환](#rotate-your-saml-token-signing-certificate)하는 대신 ADFS 서버를 클린 시스템으로 바꾸기로 결정한 경우에는 환경에서 기존 ADFS를 제거한 다음, 새 ADFS를 빌드해야 합니다. 

자세한 내용은 [구성 제거](../../active-directory/cloud-sync/how-to-configure.md#remove-a-configuration)를 참조하세요. 

### <a name="cloud-remediation-activities"></a>클라우드 수정 작업

이 문서 앞부분에 나열된 권장 사항 외에도 클라우드 환경에 대해 다음과 같은 작업을 수행하는 것이 좋습니다.

|작업  |설명  |
|---------|---------|
|**암호 재설정**     |   모든 [비상 계정](../../active-directory/roles/security-emergency-access.md)의 암호를 재설정하고 비상 계정 수를 필요한 절대 최솟값으로 줄입니다.    |
|**권한 있는 액세스 계정 제한**     |    액세스 권한이 있는 서비스 및 사용자 계정이 클라우드 전용 계정인지 확인하고 Azure Active Directory에 동기화되거나 페더레이션된 온-프레미스 계정은 사용하지 않습니다.  |
|**MFA 적용**     | 테넌트의 모든 관리자 권한 사용자에게 MFA(다단계 인증)를 적용합니다. 테넌트의 모든 사용자에게 MFA를 적용하는 것이 좋습니다.       |
|**관리 액세스 제한**     |    PIM([Privileged Identity Management](../../active-directory/privileged-identity-management/pim-configure.md)) 및 조건부 액세스를 구현하여 관리 액세스를 제한합니다.  <br><br>Microsoft 365 사용자의 경우 PAM([Privileged Access Management](https://techcommunity.microsoft.com/t5/microsoft-security-and/privileged-access-management-in-office-365-is-now-generally/ba-p/261751))을 구현하여 eDiscovery, 전역 관리자, 계정 관리 등과 같은 중요한 기능에 대한 액세스를 제한합니다.    |
|**위임된 권한 및 동의 부여 검토/축소**     |  다음 기능을 허용하는 모든 엔터프라이즈 애플리케이션 위임 권한 또는 [동의 부여](/graph/auth-limit-mailbox-access)를 검토하고 줄입니다. <br><br>- 권한 있는 사용자 및 역할 수정 <br>- 이메일 읽기, 보내기 또는 모든 사서함 액세스 <br>- OneDrive, Teams 또는 SharePoint 콘텐츠 액세스 <br>- 디렉터리에 읽기/쓰기가 가능한 서비스 주체 추가 <br>- 애플리케이션 권한 및 위임된 액세스       |
|     |         |

### <a name="on-premises-remediation-activities"></a>온-프레미스 수정 작업

이 문서 앞부분에 나열된 권장 사항 외에도 온-프레미스 환경에 대해 다음과 같은 작업을 수행하는 것이 좋습니다.

|작업  |설명  |
|---------|---------|
|**영향을 받는 시스템 다시 빌드**     |   조사 중에 공격자에 의해 손상된 것으로 식별된 시스템을 다시 빌드합니다.      |
|**불필요한 관리 사용자 제거**     |   Domain Admins, Backup Operators 및 Enterprise Admin 그룹에서 불필요한 구성원을 제거합니다. 자세한 내용은 [권한 있는 액세스 보안](/security/compass/overview)을 참조하세요. |
|**권한 있는 계정에 대한 암호 재설정**     |  환경에 있는 모든 권한 있는 계정의 암호를 재설정합니다. <br><br>**참고**: 권한 있는 계정은 기본 제공 그룹으로 국한되지 않으며 서버 관리, 워크스테이션 관리 또는 환경의 다른 영역에 대한 액세스 권한을 위임 받은 그룹일 수도 있습니다.      |
|**krbtgt 계정 초기화**     | [New-KrbtgtKeys](https://github.com/microsoft/New-KrbtgtKeys.ps1/blob/master/New-KrbtgtKeys.ps1) 스크립트를 사용하여 **krbtgt** 계정을 두 번 초기화합니다. <br><br>**참고**: 읽기 전용 도메인 컨트롤러를 사용하는 경우 읽기/쓰기 도메인 컨트롤러와 읽기 전용 도메인 컨트롤러에 대해 별도로 스크립트를 실행해야 합니다.        |
|**시스템 다시 시작 예약**     |   공격자가 만든 지속성 메커니즘이 존재하지 않거나 시스템에 남아 있지 않은지 확인한 후 메모리 상주 맬웨어를 제거하는 데 도움이 되도록 시스템을 다시 시작을 예약합니다. |
|**DSRM 암호 재설정**     |  각 도메인 컨트롤러의 DSRM(디렉터리 서비스 복원 모드) 암호를 고유하고 복잡한 암호로 재설정합니다.       |
|     |         |

### <a name="remediate-or-block-persistence-discovered-during-investigation"></a>조사 중 발견된 지속성 수정 또는 차단

조사는 반복적인 프로세스이며, 변칙을 식별할 때 수정하려는 조직의 욕구와 수정을 하면 공격자에게 탐지 사실이 알려지고 대응할 시간을 줄 가능성 사이에서 균형을 유지해야 합니다.

예를 들어, 탐지된 것을 알게 된 공격자는 기술을 변경하거나 더 많은 지속성을 만들 수 있습니다.

조사의 초기 단계에서 식별한 지속성 기술을 수정해야 합니다.

### <a name="remediate-user-and-service-account-access"></a>사용자 및 서비스 계정 액세스 수정

위에 나열된 권장 작업 외에도 다음 단계를 고려하여 사용자 계정을 수정하고 복원하는 것이 좋습니다.

- **신뢰할 수 있는 디바이스를 기반으로 조건부 액세스를 적용합니다**. 가능한 경우 조직 요구 사항에 맞게 위치 기반 조건부 액세스를 적용하는 것이 좋습니다.

- 손상되었을 수 있는 사용자 계정의 경우 제거 후 **암호를 재설정** 합니다. 디렉터리의 모든 계정에 대한 자격 증명을 재설정하는 중간 계획도 구현해야 합니다.

- 자격 증명을 순환한 직후 **새로 고침 토큰을 해지** 합니다.

    자세한 내용은 다음을 참조하세요.

    - [Azure Active Directory에서 긴급 상황 시 사용자 액세스 해지](../../active-directory/enterprise-users/users-revoke-access.md)
    - [Revoke-AzureADUserAllRefreshToken PowerShell 설명서](/powershell/module/azuread/revoke-azureaduserallrefreshtoken)



## <a name="next-steps"></a>다음 단계

- 상단 탐색 모음에서 **도움말**( **?** ) 단추를 선택하여 Microsoft 365 보안 센터, Microsoft 365 Security & Compliance 센터, Microsoft Defender 보안 센터를 포함한 **Microsoft 제품 내부에서 도움말을 참고하세요**.

- **배포 지원이 필요한 경우** [FastTrack](https://fasttrack.microsoft.com)에 문의해주세요.

- **제품 지원 관련 요구 사항이 있는 경우** https://support.microsoft.com/contactus 에서 Microsoft 지원 사례를 제출하세요.

    > [!IMPORTANT]
    > 손상을 입었다고 생각되어 인시던트 대응을 통한 지원이 필요한 경우 **Sev A** Microsoft 지원 사례를 열어주세요.
    >
