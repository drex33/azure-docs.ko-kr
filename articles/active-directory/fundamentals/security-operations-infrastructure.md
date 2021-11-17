---
title: 인프라에 대한 Azure Active Directory 보안 작업
description: 인프라 구성 요소를 모니터링하고 경고하여 보안 위협을 식별하는 방법을 알아봅니다.
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
ms.openlocfilehash: 12e86dc602e52fb96b7f9cea3a52079fc80c201b
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130041693"
---
# <a name="security-operations-for-infrastructure"></a>인프라에 대한 보안 작업

인프라에는 제대로 구성되지 않은 경우 취약성이 발생할 수 있는 많은 구성 요소가 있습니다. 인프라에 대한 모니터링 및 알림 전략의 일환으로 다음 영역에서 이벤트를 모니터링 및 경고합니다.

* 인증 및 권한 부여

* 하이브리드 인증 구성 요소(페더레이션 서버 포함)

* 정책 

* Subscriptions

인증 인프라의 구성 요소를 모니터링하고 경고하는 것은 매우 중요합니다. 손상으로 인해 전체 환경이 완전히 손상될 수 있습니다. Azure AD를 사용하는 많은 기업은 하이브리드 인증 환경에서 운영됩니다. 즉, 클라우드 및 온-프레미스 구성 요소가 모두 모니터링 및 알림 전략에 포함되어야 합니다. 하이브리드 인증 환경이 있으면 또 다른 공격 벡터가 환경에 도입됩니다.

모든 구성 요소를 컨트롤 플레인/계층 0 자산과 이를 관리하는 데 사용되는 계정으로 간주하는 것이 좋습니다. 환경 설계 및 구현에 대한 지침은 [SPA(권한 있는 자산 보안)](/security/compass/overview)를 참조하세요. 이 지침에는 잠재적으로 Azure AD 테넌트에 사용할 수 있는 각 하이브리드 인증 구성 요소에 대한 권장 사항이 포함되어 있습니다.

예기치 않은 이벤트와 잠재적 공격을 탐지할 수 있는 첫 번째 단계는 기준선을 설정하는 것입니다. 이 문서에 나와 있는 모든 온-프레미스 구성 요소에 대해서는 SPA(권한 있는 자산 보안) 가이드의 일부인 [권한 있는 액세스 배포](/security/compass/privileged-access-deployment)를 참조하세요.

## <a name="where-to-look"></a>살펴볼 위치

조사 및 모니터링에 사용하는 로그 파일은 다음과 같습니다. 

* [Azure AD 감사 로그](../reports-monitoring/concept-audit-logs.md)

* [로그인 로그](../reports-monitoring/concept-all-sign-ins.md)

* [Microsoft 365 감사 로그](/microsoft-365/compliance/auditing-solutions-overview) 

* [Azure Key Vault 로그](../../key-vault/general/logging.md?tabs=Vault)

Azure Portal에서 Azure AD 감사 로그를 보고, CSV(쉼표로 구분된 값) 또는 JSON(JavaScript Object Notation) 파일로 다운로드할 수 있습니다. Azure Portal에는 모니터링 및 경고를 더 효율적으로 자동화하도록 허용하는 다른 도구와 Azure AD 로그를 통합하는 몇 가지 방법이 있습니다.

* [Azure Sentinel](../../sentinel/overview.md) – SIEM(보안 정보 및 이벤트 관리) 기능을 제공하여 엔터프라이즈 수준에서 인텔리전트 보안 분석을 가능하게 합니다. 

* [Azure Monitor](../../azure-monitor/overview.md) – 다양한 조건에 대한 자동화된 모니터링 및 경고를 가능하게 합니다. 통합 문서를 만들거나 사용하여 다른 원본의 데이터를 결합할 수 있습니다.

* SIEM과 통합된 [Azure Event Hubs](../../event-hubs/event-hubs-about.md) - Azure AD 로그는 Splunk, ArcSight, QRadar 및 Sumo Logic과 같은 [다른 SIEM에 통합](../reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md)할 수 있습니다.

* [MCAS(Microsoft Cloud App Security)](/cloud-app-security/what-is-cloud-app-security) - 앱을 검색 및 관리하고, 앱과 리소스를 제어하고, 클라우드 앱의 규정 준수를 확인할 수 있습니다. 

이 문서의 나머지 부분은 모니터링 및 경고해야 하는 대상에 대해 설명하며, 위협 유형별로 구성되어 있습니다. 미리 빌드된 특정 솔루션이 있는 경우 해당 솔루션에 대한 링크가 표 뒤에 있습니다. 그렇지 않으면 이전 도구를 사용하여 경고를 작성할 수 있습니다.

## <a name="authentication-infrastructure"></a>인증 인프라

온-프레미스 및 클라우드 기반 리소스와 계정을 모두 포함하는 하이브리드 환경에서 Active Directory 인프라는 인증 스택의 주요 부분입니다. 스택은 공격의 대상이기도 하므로 보안 환경을 유지하도록 구성하고 적절하게 모니터링해야 합니다. 인증 인프라에 대해 사용되는 현재 공격 유형의 예에서는 암호 스프레이 및 Solorigate 기술을 사용합니다. 권장되는 문서에 대한 링크는 다음과 같습니다.

* [권한 있는 액세스 보안 개요](/security/compass/overview) – 이 문서에서는 제로 트러스트 기술을 사용하여 권한 있는 액세스 보안을 만들고 유지 관리하는 현재 기술에 대한 개요를 제공합니다.

* [Microsoft Defender for Identity에서 모니터링하는 도메인 활동](/defender-for-identity/monitored-activities) - 이 문서에서는 모니터링하고 경고를 설정하는 활동에 대한 포괄적인 목록을 제공합니다. 

* [Microsoft Defender for Identity 보안 경고 자습서](/defender-for-identity/understanding-security-alerts) - 이 문서에서는 보안 경고 전략을 만들고 구현하는 방법에 대한 지침을 제공합니다.

인증 인프라를 모니터링하고 경고하는 데 초점을 맞춘 특정 문서에 대한 링크는 다음과 같습니다.

* [Microsoft Defender for Identity를 사용한 수평 이동 경로 이해 및 사용](/defender-for-identity/use-case-lateral-movement-path) - 이 문서에서는 중요하지 않은 계정을 사용하여 네트워크 전체에서 중요한 계정에 액세스하는 경우를 식별하는 데 사용할 수 있는 검색 기술에 대해 설명합니다.

* [Microsoft Defender for Identity의 보안 경고 사용](/defender-for-identity/working-with-suspicious-activities) - 이 문서에서는 경고가 기록되면 이를 검토하고 관리하는 방법에 대해 설명합니다. 

 검색할 특정 항목은 다음과 같습니다.

| 모니터링 대상| 위험 수준| Where| 참고 |
| - | - | - | - |
| 엑스트라넷 잠금 추세| 높음| Azure AD Connect Health| 엑스트라넷 잠금 추세를 검색하는 데 도움이 되는 도구 및 기술에 대해서는 [Azure AD Connect Health를 사용하여 AD FS 모니터링](../hybrid/how-to-connect-health-adfs.md)의 정보를 사용하세요. |
| 실패한 로그인|높음 | Connect Health 포털| 위험한 IP 보고서를 내보내거나 다운로드하고, 다음 단계를 위해 [위험한 IP 보고서(공개 미리 보기)](../hybrid/how-to-connect-health-adfs-risky-ip.md)의 지침을 따릅니다. |
| 개인 정보 보호 준수| 낮음| Azure AD Connect Health| [사용자 개인 정보 및 Azure AD Connect Health](../hybrid/reference-connect-health-user-privacy.md) 문서를 사용하여 데이터 수집 및 모니터링을 사용하지 않도록 Azure AD Connect Health를 구성합니다. |
| LDAP에 대한 잠재적 무차별 암호 대입 공격| 중간| Microsoft Defender for Identity| 센서를 사용하여 LDAP에 대한 잠재적 무차별 암호 대입 공격을 탐지할 수 있습니다. |
| 계정 열거 정찰| 중간| Microsoft Defender for Identity| 센서를 사용하여 계정 열거 정찰을 수행할 수 있습니다. |
| Azure AD와 Azure AD FS 간의 일반적인 상관 관계|중간 | Microsoft Defender for Identity| 기능을 사용하여 Azure AD 및 Azure AD FS 환경 간의 활동을 상호 연결합니다. |


 

### <a name="pass-through-authentication-monitoring"></a>통과 인증 모니터링 

Azure AD(Active Directory) 통과 인증은 온-프레미스 Active Directory에 대해 암호의 유효성을 직접 검사하여 사용자를 로그인합니다. 

검색할 특정 항목은 다음과 같습니다.

| 모니터링 대상| 위험 수준| Where| 필터/하위 필터| 참고 |
| - | - | - | - | - |
| Azure AD 통과 인증 오류|중간 | Application 및 ‎Service Logs\Microsoft\A‎zureAdConnec‎t\Authenticatio‎nAgent\Admin| AADSTS80001 – Active Directory에 연결할 수 없습니다	.| 에이전트 서버가 유효성을 검사해야 하는 암호의 사용자와 동일한 AD 포리스트의 멤버이고 Active Directory에 연결할 수 있는지 확인합니다. |
| Azure AD 통과 인증 오류| 중간| Application 및 ‎Service Logs\Microsoft\A‎zureAdConnec‎t\Authenticatio‎nAgent\Admin| AADSTS8002 - Active Directory에 연결하는 데 시간이 초과되었습니다.| Active Directory를 사용할 수 있고 에이전트의 요청에 응답하는지 확인합니다. |
| Azure AD 통과 인증 오류|중간 | Application 및 ‎Service Logs\Microsoft\A‎zureAdConnec‎t\Authenticatio‎nAgent\Admin| AADSTS80004 - 에이전트에 전달된 사용자 이름이 잘못되었습니다.| 사용자가 올바른 사용자 이름을 사용하여 로그인을 시도하는지 확인합니다. |
| Azure AD 통과 인증 오류|중간 | Application 및 ‎Service Logs\Microsoft\A‎zureAdConnec‎t\Authenticatio‎nAgent\Admin| AADSTS80005 - 유효성 검사에서 예기치 않은 WebException이 발생했습니다.| 일시적인 오류입니다. 요청을 다시 시도하십시오. 계속 실패할 경우 Microsoft 지원에 문의하세요. |
| Azure AD 통과 인증 오류| 중간| Application 및 ‎Service Logs\Microsoft\A‎zureAdConnec‎t\Authenticatio‎nAgent\Admin| AADSTS80007 - Active Directory와 통신하는 동안 오류가 발생했습니다.| 에이전트 로그에서 자세한 정보를 확인하고 Active Directory가 예상대로 작동하는지 확인합니다. |
| Azure AD 통과 인증 오류|높음 | Win32 LogonUserA 함수 API| 로그온 이벤트 4624(s): 계정이 성공적으로 로그온되었습니다.<br>- 상관 관계 –<br>4625(F):계정을 로그온하지 못했습니다.| 요청을 인증하는 도메인 컨트롤러에서 의심되는 사용자 이름과 함께 사용합니다. [LogonUserA 함수(winbase.h)](/windows/win32/api/winbase/nf-winbase-logonusera)의 지침을 참조하세요. |
| Azure AD 통과 인증 오류| 중간| 도메인 컨트롤러의 PowerShell 스크립트| 표 뒤에 나오는 쿼리를 참조하세요. | 추가 지침은 [Azure AD Connect: 통과 인증 문제 해결](../hybrid/tshoot-connect-pass-through-authentication.md)의 정보를 사용하세요. |

```Kusto

<QueryList>

<Query Id="0" Path="Security">

<Select Path="Security">*[EventData[Data[@Name='ProcessName'] and (Data='C:\Program Files\Microsoft Azure AD Connect Authentication Agent\AzureADConnectAuthenticationAgentService.exe')]]</Select>

</Query>

</QueryList>
```

### <a name="appproxy-connector"></a>AppProxy 커넥터

Azure AD 및 Azure AD 애플리케이션 프록시는 원격 사용자에게 SSO(Single Sign-On) 환경을 제공합니다. 사용자는 VPN(가상 사설망) 또는 이중 홈 서버 및 방화벽 규칙 없이 온-프레미스 앱에 안전하게 연결합니다. Azure AD 애플리케이션 프록시 커넥터 서버가 손상된 경우 공격자는 SSO 환경을 변경하거나 게시된 애플리케이션에 대한 액세스를 변경할 수 있습니다. 

애플리케이션 프록시에 대한 모니터링을 구성하려면 [애플리케이션 프록시 문제 및 오류 메시지 해결](../app-proxy/application-proxy-troubleshoot.md)을 참조하세요. 정보를 기록하는 데이터 파일은 Applications and Services Logs\Microsoft\AadApplicationProxy\Connector\Admin에서 찾을 수 있습니다. 감사 활동에 대한 전체 참조 가이드는 [Azure AD 감사 활동 참조](../reports-monitoring/reference-audit-activities.md)를 참조하세요. 모니터링할 특정 항목:

| 모니터링 대상| 위험 수준| Where| 필터/하위 필터| 참고 |
| - | - | - | - | - |
| Kerberos 오류| 중간 | 다양한 도구| 중간 | [애플리케이션 프록시 문제 및 오류 메시지 해결](../app-proxy/application-proxy-troubleshoot.md)의 Kerberos 오류 아래에 있는 Kerberos 인증 오류 지침입니다. |
| DC 보안 문제| 높음| DC 보안 감사 로그| 이벤트 ID 4742(S): 컴퓨터 계정을 변경했습니다.<br>및<br>플래그 – 위임용으로 트러스트됨<br>또는<br>플래그 – 위임용으로 인증하도록 트러스트됨| 플래그 변경을 조사합니다. |
| Pass-the-Ticket 유사 공격| 높음| | | 다음 지침을 따릅니다.<li>[보안 주체 정찰(LDAP)(외부 ID 2038)](/defender-for-identity/reconnaissance-alerts)<li>[자습서: 손상된 자격 증명 경고](/defender-for-identity/compromised-credentials-alerts)<li> [Microsoft Defender for Identity의 횡적 이동 경로 이해 및 사용](/defender-for-identity/use-case-lateral-movement-path)<li> [엔터티 프로필 이해](/defender-for-identity/entity-profiles) |


### <a name="legacy-authentication-settings"></a>레거시 인증 설정

효과적인 MFA(다단계 인증)를 위해 레거시 인증도 차단해야 합니다. 그런 다음, 환경을 모니터링하고 레거시 인증 사용에 대해 경고해야 합니다. 이는 POP, SMTP, IMAP 및 MAPI와 같은 레거시 인증 프로토콜에서 MFA를 적용할 수 없기 때문입니다. 따라서 이러한 프로토콜은 조직의 공격자가 선호하는 진입점이 됩니다. 레거시 인증을 차단하는 데 사용할 수 있는 도구에 대한 자세한 내용은 [조직에서 레거시 인증을 차단하는 새 도구](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/new-tools-to-block-legacy-authentication-in-your-organization/ba-p/1225302)를 참조하세요. 

레거시 인증은 이벤트 세부 정보의 일부로 Azure AD 로그인 로그에 캡처됩니다. Azure Monitor 통합 문서를 사용하여 레거시 인증 사용을 식별할 수 있습니다. 자세한 내용은 [Azure Monitor 통합 문서를 Azure Active Directory 보고서에 사용하는 방법](../reports-monitoring/howto-use-azure-monitor-workbooks.md)의 일부인 [레거시 인증을 사용한 로그인](../reports-monitoring/howto-use-azure-monitor-workbooks.md)을 참조하세요. Azure Sentinel에 대한 사용할 수도 있습니다. 자세한 내용은 [Azure Sentinel 안전하지 않은 프로토콜 통합 문서 구현 가이드](https://techcommunity.microsoft.com/t5/azure-sentinel/azure-sentinel-insecure-protocols-workbook-implementation-guide/ba-p/1197564)를 참조하세요. 모니터링할 특정 활동은 다음과 같습니다.

| 모니터링 대상| 위험 수준| Where| 필터/하위 필터| 참고 |
| - | - | - | - | - |
| 레거시 인증|높음 | Azure AD 로그인 로그| ClientApp : POP<br>ClientApp : IMAP<br>ClientApp : MAPI<br>ClientApp: SMTP<br>ClientApp : ActiveSync(EXO로 이동)<br>기타 클라이언트 = SharePoint 및 EWS| 페더레이션된 도메인 환경에서는 실패한 인증이 기록되지 않으므로 로그에 표시되지 않습니다. |


## <a name="azure-ad-connect"></a>Azure AD Connect

Azure AD Connect는 온-프레미스 및 클라우드 기반 Azure AD 환경 간의 계정 및 특성 동기화를 가능하게 하는 중앙 집중식 위치를 제공합니다. Azure AD Connect는 하이브리드 ID 목표를 충족하고 달성하도록 설계된 Microsoft 도구입니다. 다음과 같은 기능을 제공합니다.

* [암호 해시 동기화](../hybrid/whatis-phs.md) - 사용자의 온-프레미스 AD 암호 해시를 Azure AD와 동기화하는 로그인 방법입니다.

* [동기화](../hybrid/how-to-connect-sync-whatis.md) - 사용자, 그룹 및 기타 개체를 생성합니다. 뿐만 아니라 온-프레미스 사용자 및 그룹의 ID 정보가 클라우드와 일치하도록 합니다. 이 동기화에는 암호 해시도 포함되어 있습니다.

* [상태 모니터링](../hybrid/whatis-azure-ad-connect.md) - Azure AD Connect Health는 강력한 모니터링을 제공하고 Azure Portal에서 중앙 위치를 제공하여 이 작업을 볼 수 있습니다.

온-프레미스 환경과 클라우드 환경 간에 ID를 동기화하면 온-프레미스 및 클라우드 기반 환경에 새로운 공격 표면이 도입됩니다. 다음이 권장됩니다.

* Azure AD Connect 주 및 스테이징 서버를 컨트롤 플레인에서 계층 0 시스템으로 처리합니다. 

* 사용자 환경에서 각 계정 유형과 해당 사용을 제어하는 표준 정책 세트를 따릅니다.

*  Azure AD Connect 및 Connect Health를 설치합니다. 이러한 서비스는 주로 환경에 대한 운영 데이터를 제공합니다. 

Azure AD Connect 작업의 로깅은 다음과 같은 다양한 방식으로 수행됩니다.

* Azure AD Connect 마법사는 데이터를 \ProgramData\AADConnect에 기록합니다. 마법사를 호출할 때마다 타임스탬프가 적용된 추적 로그 파일이 만들어집니다. 추적 로그는 Sentinel 또는 다른 타사 SIEM(보안 정보 및 이벤트 관리) 도구로 가져와서 분석할 수 있습니다.

* 일부 작업은 로깅 정보를 캡처하기 위해 PowerShell 스크립트를 시작합니다. 이 데이터를 수집하려면 스크립트 블록 로깅을 사용하도록 설정해야 합니다.

### <a name="monitoring-configuration-changes"></a>구성 변경 모니터링

Azure AD는 Microsoft SQL Server 데이터 엔진 또는 SQL을 사용하여 Azure AD Connect 구성 정보를 저장합니다. 따라서 구성과 관련된 로그 파일의 모니터링 및 감사는 모니터링 및 감사 전략에 포함되어야 합니다. 특히 다음 표를 모니터링 및 경고 전략에 포함합니다.

| 모니터링 대상| Where| 참고 |
| - | - | - |
| mms_management_agent| SQL 서비스 감사 레코드| [SQL Server Audit 레코드](/sql/relational-databases/security/auditing/sql-server-audit-records) 참조 |
| mms_partition| SQL 서비스 감사 레코드| [SQL Server Audit 레코드](/sql/relational-databases/security/auditing/sql-server-audit-records) 참조 |
| mms_run_profile| SQL 서비스 감사 레코드| [SQL Server Audit 레코드](/sql/relational-databases/security/auditing/sql-server-audit-records) 참조 |
| mms_server_configuration| SQL 서비스 감사 레코드| [SQL Server Audit 레코드](/sql/relational-databases/security/auditing/sql-server-audit-records) 참조 |
| mms_synchronization_rule| SQL 서비스 감사 레코드| [SQL Server Audit 레코드](/sql/relational-databases/security/auditing/sql-server-audit-records) 참조 |


구성 정보를 모니터링하는 대상 및 방법에 대한 정보는 다음을 참조하세요.

* SQL 서버의 경우 [SQL Server Audit 레코드](/sql/relational-databases/security/auditing/sql-server-audit-records)를 참조하세요.

* Azure Sentinel의 경우 [Windows 서버에 연결하여 보안 이벤트 수집](/sql/relational-databases/security/auditing/sql-server-audit-records)을 참조하세요. 

* Azure AD Connect를 구성하고 사용하는 방법에 대한 자세한 내용은 [Azure AD Connect란?](../hybrid/whatis-azure-ad-connect.md)을 참조하세요.

### <a name="monitoring-and-troubleshooting-synchronization"></a>동기화 모니터링 및 문제 해결

 Azure AD Connect의 한 가지 기능은 사용자의 온-프레미스 암호와 Azure AD 간의 해시 동기화를 동기화하는 것입니다. 암호가 예상대로 동기화되지 않으면 동기화가 일부 사용자 또는 모든 사용자에게 영향을 줄 수 있습니다. 다음을 사용하여 적절한 작업을 확인하거나 문제를 해결합니다.

* 해시 동기화 확인 및 문제 해결에 대한 내용은 [Azure AD Connect 동기화를 사용하여 암호 해시 동기화 문제 해결](../hybrid/tshoot-connect-password-hash-synchronization.md)을 참조하세요. 

* 커넥터 공간에 대한 수정은 [Azure AD Connect 개체 및 특성 문제 해결](/troubleshoot/azure/active-directory/troubleshoot-aad-connect-objects-attributes)을 참조하세요. 

**모니터링에 대한 중요 리소스**

| 모니터링 대상 | 리소스 |
| - | - |
| 해시 동기화 유효성 검사|[Azure AD Connect 동기화를 사용하여 암호 해시 동기화 문제 해결](../hybrid/tshoot-connect-password-hash-synchronization.md)을 참조하세요. |
 커넥터 공간 수정|[Azure AD Connect 개체 및 특성 문제 해결](/troubleshoot/azure/active-directory/troubleshoot-aad-connect-objects-attributes)을 참조하세요. |
| 구성한 규칙 수정| 특히 필터링 변경 내용, 도메인 및 OU 변경 내용, 특성 변경 내용 및 그룹 기반 변경 내용을 모니터링합니다. |
| SQL 및 MSDE 변경 | 로깅 매개 변수를 변경하고 사용자 지정 함수를 추가합니다. |

**다음을 모니터링합니다**. 

| 모니터링 대상| 위험 수준| Where| 필터/하위 필터| 참고 |
| - | - | - | - | - |
| 스케줄러 변경|높음 | PowerShell| Set-ADSyncScheduler| 일정에 대한 수정 내용을 찾습니다. |
| 예약된 작업 변경| 높음 | Azure AD 감사 로그| 활동 = 4699(S): 예약된 작업을 삭제했습니다.<br>또는<br>활동 = 4701(s): 예약된 작업을 사용하지 않도록 설정했습니다.<br>또는<br>활동 = 4701(s): 예약된 작업을 업데이트했습니다.| 모두 모니터링합니다. |



* PowerShell 스크립트 작업을 기록하는 방법에 대한 자세한 내용은 PowerShell 참조 설명서의 일부인 [스크립트 블록 로깅 사용](/powershell/module/microsoft.powershell.core/about/about_logging_windows)을 참조하세요.

* Splunk에서 분석할 PowerShell 로깅을 구성하는 방법에 대한 자세한 내용은 [Splunk 사용자 동작 분석으로 데이터 가져오기](https://docs.splunk.com/Documentation/UBA/5.0.4.1/GetDataIn/AddPowerShell)를 참조하세요.

### <a name="monitoring-seamless-single-sign-on"></a>Seamless Single Sign-On 모니터링

Azure AD(Active Directory) Seamless SSO(Seamless Single Sign-On)는 회사 네트워크에 연결된 회사 데스크톱에 있을 때 사용자를 자동으로 서명합니다. Seamless SSO는 추가 온-프레미스 구성 요소가 없어도 사용자가 클라우드 기반 애플리케이션에 쉽게 액세스할 수 있습니다. SSO는 Azure AD Connect에서 제공하는 통과 인증 및 암호 해시 동기화 기능을 사용합니다.

Single Sign-On 및 Kerberos 활동을 모니터링하면 일반적인 자격 증명 도난 공격 패턴을 검색하는 데 도움이 될 수 있습니다. 다음 정보를 사용하여 모니터링합니다.

| 모니터링 대상| 위험 수준| Where| 필터/하위 필터| 참고 |
| - | - | - | - | - |
| SSO 및 Kerberos 유효성 검사 실패와 관련된 오류|중간 | Azure AD 로그인 로그| | [Single Sign-On](../hybrid/tshoot-connect-sso.md)에서 발생하는 Single Sign-On 오류 코드 목록입니다. |
| 오류 문제 해결에 대한 쿼리|중간 | PowerShell| 표 뒤에 나오는 쿼리를 참조하세요. SSO가 사용하도록 설정된 각 포리스트를 체크 인합니다.| SSO가 사용하도록 설정된 각 포리스트를 체크 인합니다. |
| Kerberos 관련 이벤트|높음 | Microsoft Defender for Identity 모니터링| | [Microsoft Defender for Identity LMP(수평 이동 경로)](/defender-for-identity/use-case-lateral-movement-path)에서 사용할 수 있는 지침을 검토합니다. |

```kusto
<QueryList>

<Query Id="0" Path="Security">

<Select Path="Security">*[EventData[Data[@Name='ServiceName'] and (Data='AZUREADSSOACC$')]]</Select>

</Query>

</QueryList>
```
## <a name="password-protection-policies"></a>암호 보호 정책

Azure AD 암호 보호를 배포하는 경우 모니터링 및 보고는 필수 작업입니다. 다음 링크는 각 서비스에서 정보를 기록하는 위치 및 Azure AD 암호 보호 사용에 대해 보고하는 방법을 포함하여 다양한 모니터링 기술을 이해하는 데 도움이 되는 세부 정보를 제공합니다. 

DC(도메인 컨트롤러) 에이전트와 프록시 서비스는 모두 이벤트 로그 메시지를 기록합니다. 아래 설명된 모든 PowerShell cmdlet은 프록시 서버에서만 사용할 수 있습니다(AzureADPasswordProtection PowerShell 모듈 참조). DC 에이전트 소프트웨어는 PowerShell 모듈을 설치하지 않습니다.

온-프레미스 암호 보호를 계획하고 구현하는 방법에 대한 자세한 내용은 [온-프레미스 Azure Active Directory 암호 보호 계획 및 배포](../authentication/howto-password-ban-bad-on-premises-deploy.md)에서 사용할 수 있습니다. 모니터링 세부 정보는 [온-프레미스 Azure AD 암호 보호 모니터링](../authentication/howto-password-ban-bad-on-premises-monitor.md)을 참조하세요. 각 도메인 컨트롤러에서 DC 에이전트 서비스 소프트웨어는 각 개별 암호 유효성 검사 작업(및 기타 상태)의 결과를 다음 로컬 이벤트 로그에 기록합니다.

* \Applications and Services Logs\Microsoft\AzureADPasswordProtection\DCAgent\Admin

* \Applications and Services Logs\Microsoft\AzureADPasswordProtection\DCAgent\Operational

* \Applications and Services Logs\Microsoft\AzureADPasswordProtection\DCAgent\Trace

DC 에이전트 관리자 로그는 소프트웨어 작동 방식의 주요 정보 소스입니다. 기본적으로 추적 로그는 꺼져 있으며, 데이터를 기록하기 전에 사용하도록 설정해야 합니다. 애플리케이션 프록시 문제 및 오류 메시지를 해결하려면 [Azure Active Directory 애플리케이션 프록시 문제 해결](../app-proxy/application-proxy-troubleshoot.md)에서 자세한 정보를 사용할 수 있습니다. 이러한 이벤트에 대한 정보는 다음에 기록됩니다.

* Applications and Services Logs\Microsoft\AadApplicationProxy\Connector\Admin

* Azure AD 감사 로그, 범주 애플리케이션 프록시

Azure AD 감사 활동에 대한 전체 참조는 [Azure AD(Azure Active Directory) 감사 활동 참조](../reports-monitoring/reference-audit-activities.md)에서 사용할 수 있습니다. 

## <a name="next-steps"></a>다음 단계


다음 추가 보안 작업 가이드 문서를 참조하세요.

[Azure AD 보안 작업 개요](security-operations-introduction.md)

[사용자 계정에 대한 보안 작업](security-operations-user-accounts.md)

[권한 있는 계정에 대한 보안 작업](security-operations-privileged-accounts.md)

[Privileged Identity Management에 대한 보안 작업](security-operations-privileged-identity-management.md)

[애플리케이션에 대한 보안 작업](security-operations-applications.md)

[디바이스에 대한 보안 작업](security-operations-devices.md)
 
[인프라에 대한 보안 작업](security-operations-infrastructure.md)


 

  
‎
