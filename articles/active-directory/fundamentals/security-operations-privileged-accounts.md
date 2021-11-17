---
title: 권한 있는 계정에 대한 Azure Active Directory 보안 작업
description: Azure Active Directory에서 기준을 설정하고, 권한 있는 계정으로 잠재적인 보안 문제를 모니터링하고 경고하는 방법을 알아봅니다.
services: active-directory
author: BarbaraSelden
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 07/15/2021
ms.author: baselden
ms.collection: M365-identity-device-management
ms.openlocfilehash: 313532b6292cb3a6799b3c14df69c39d92ba3c03
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130227514"
---
# <a name="security-operations-for-privileged-accounts"></a>권한 있는 계정에 대한 보안 작업

비즈니스 자산의 보안은 IT 시스템을 관리하는 권한 있는 계정의 무결성에 따라 달라집니다. 사이버 공격자는 자격 증명 도난 공격 및 기타 수단을 사용하여 권한 있는 계정을 대상으로 하여 중요한 데이터에 대한 액세스 권한을 얻습니다.

일반적으로 조직의 보안은 네트워크의 진입 및 출구 지점의 보안 경계를 중심으로 합니다. 그러나 인터넷의 SaaS(Software-as-a-Service) 애플리케이션 및 개인 디바이스는 이러한 접근 방식의 효과를 떨어뜨렸습니다. 

Azure AD(Azure Active Directory)는 ID 및 액세스 관리(IAM)를 컨트롤 플레인으로 사용합니다. 조직의 ID 계층에서 권한 있는 관리 역할에 할당된 사용자가 제어됩니다. 환경이 온-프레미스인지, 클라우드인지 또는 하이브리드 환경인지에 관계 없이 액세스에 사용되는 계정을 보호해야 합니다.

사용자는 온-프레미스 IT 환경에 대한 모든 보안 계층을 전적으로 책임져야 합니다. Azure 서비스를 사용하는 경우 예방 및 대응은 클라우드 서비스 공급자(Microsoft)와 고객(사용자)의 공동 책임입니다.

* 공유 책임 모델에 대한 자세한 내용은 [클라우드의 공유 책임](../../security/fundamentals/shared-responsibility.md)을 참조하세요.

* 권한 있는 사용자의 액세스를 보호하는 방법에 대한 자세한 내용은 [Azure AD에서 하이브리드 및 클라우드 배포를 위한 권한 있는 액세스 보안](../roles/security-planning.md)을 참조하세요.

* 권한 있는 ID의 주요 개념에 대한 광범위한 비디오, 방법 가이드 및 콘텐츠에 대해서는 [Privileged Identity Management 설명서](../privileged-identity-management/index.yml)를 참조하세요.

## <a name="where-to-look"></a>살펴볼 위치

조사 및 모니터링에 사용하는 로그 파일은 다음과 같습니다. 

* [Azure AD 감사 로그](../reports-monitoring/concept-audit-logs.md)

* [Microsoft 365 감사 로그](/microsoft-365/compliance/auditing-solutions-overview) 

* [Azure Key Vault 인사이트](../../azure-monitor/insights/key-vault-insights-overview.md)

Azure Portal에서 Azure AD 감사 로그를 보고 CSV(쉼표로 구분된 값) 또는 JSON(JavaScript Object Notation) 파일로 다운로드할 수 있습니다. Azure Portal에는 모니터링 및 경고를 더 효율적으로 자동화하도록 허용하는 다른 도구와 Azure AD 로그를 통합하는 몇 가지 방법이 있습니다.

* [Azure Sentinel](../../sentinel/overview.md) – SIEM(보안 정보 및 이벤트 관리) 기능을 제공하여 엔터프라이즈 수준에서 인텔리전트 보안 분석을 가능하게 합니다. 

* [Azure Monitor](../../azure-monitor/overview.md) – 다양한 조건에 대한 자동화된 모니터링 및 경고를 가능하게 합니다. 통합 문서를 만들거나 사용하여 다양한 원본의 데이터를 결합할 수 있습니다.

* SIEM과 통합된 [Azure Event Hubs](../../event-hubs/event-hubs-about.md) - Azure AD 로그는 Splunk, ArcSight, QRadar 및 Sumo Logic과 같은 [다른 SIEM에 푸시](../reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md)할 수 있습니다.

* [MCAS(Microsoft Cloud App Security)](/cloud-app-security/what-is-cloud-app-security) - 앱을 검색 및 관리하고, 앱과 리소스를 제어하고, 클라우드 앱의 규정 준수를 확인할 수 있습니다. 

* Microsoft Graph - 데이터 및 사용자 MS Graph를 내보내 추가 분석을 수행할 수 있습니다. MS Graph에 대한 자세한 내용은 [Microsoft Graph PowerShell SDK와 Azure Active Directory ID 보호](../identity-protection/howto-identity-protection-graph-api.md)를 참조하세요. 

* [ID 보호](../identity-protection/overview-identity-protection.md)-- 조사에 도움을 주는 데 사용할 수 있는 세 가지 주요 보고서를 생성합니다.

   * 위험한 사용자 - 위험에 노출된 사용자, 검색에 대한 세부 정보, 모든 위험한 로그인 기록 및 위험 기록에 대한 정보를 포함합니다.

   * 위험한 로그인 - 의심 스러운 상황을 나타낼 수 있는 로그인 관련 정보를 포함합니다. 이 보고서의 정보를 조사하는 방법에 대한 자세한 내용은 [방법: 위험 조사](../identity-protection/howto-identity-protection-investigate-risk.md)를 참조하세요. 

   * 위험 검색 – 위험이 검색될 때 트리거되는 기타 위험에 대한 정보, 로그인 위치와 같은 기타 관련 정보, MCAS(Microsoft Cloud App Security)의 세부 정보를 포함합니다.

 

이 방식은 권장되지 않지만 권한 있는 계정에는 고정 관리 권한이 있을 수 있습니다. 고정 권한을 사용하도록 선택하며 계정이 손상된 경우 상당히 부정적인 영향을 미칠 수 있습니다. 권한 있는 계정을 우선적으로 모니터링하고, PIM(Privileged Identity Management) 구성에 계정을 포함하는 것이 좋습니다. PIM에 대한 자세한 내용은 [Privileged Identity Management 사용 시작](../privileged-identity-management/pim-getting-started.md)을 참조하세요. 또한 해당 관리자 계정의 유효성을 검사하는 것이 좋습니다.

* 필수입니다.

* 필요한 작업을 실행할 수 있는 최소 권한이 있습니다.

* 최소한 MFA를 사용하여 보호됩니다.

* PAW(권한 있는 액세스 워크스테이션) 또는 SAW(보안 관리 워크스테이션) 디바이스에서 실행됩니다. 

이 문서의 나머지 부분에서는 모니터링하고 경고하는 것이 권장되는 사항에 대해 설명하며 위협 유형별로 구성되어 있습니다. 사전 구축된 특정 솔루션이 있는 경우 표에 따라 해당 솔루션에 연결합니다. 제공하지 않으면 이전 도구를 사용하여 경고를 작성할 수 있습니다. 특히 이 문서에서는 기준 설정, 권한 있는 계정의 로그인 및 사용 감사, 권한 있는 계정의 무결성을 유지하는 데 사용할 수 있는 도구 및 리소스에 대한 세부 정보를 제공합니다. 콘텐츠는 다음과 같은 토픽 영역으로 구성됩니다.

* 응급 "비상" 계정 

* 권한 있는 계정 로그인

* 권한 있는 계정 변경

* 권한 있는 그룹

* 권한 할당 및 권한 상승

## <a name="emergency-access-accounts"></a>응급 액세스 계정

실수로 Azure AD(Azure Active Directory) 테넌트에서 잠기지 않도록 하는 것이 중요합니다. 조직에서 둘 이상의 응급 액세스 계정을 만들어 실수로 인한 잠금의 영향을 완화할 수 있습니다. 응급 액세스 계정은 화재 경보와 같이 물리적 보안 장비에서 찾을 수 있는 "응급 상황에서 유리 깨기" 메시지에서와 같이 "비상" 계정으로도 알려져 있습니다.

응급 액세스 계정에는 높은 권한이 부여되고 특정 개인에게 할당되지 않습니다. 응급 액세스 계정은 권한 있는 일반 계정을 사용할 수 없는 비상 시나리오의 긴급한 상황으로 제한됩니다. 예를 들어 조건부 액세스 정책이 잘못 구성되면 모든 일반 관리 계정을 잠급니다. 응급 계정 사용을 절대적으로 필요한 경우로만 제한합니다.

또한 [Azure AD의 관리자를 위한 응급 보안 액세스 방법](../roles/security-planning.md)에서 수행할 작업에 대한 지침을 참조하세요.

응급 액세스 계정을 사용할 때마다 우선 순위가 높은 경고를 보냅니다.

### <a name="discovery"></a>발견(Discovery)

비상 계정은 응급 상황인 경우에만 사용되므로 모니터링은 계정 작업을 검색하지 않습니다. 응급 액세스 계정을 사용하거나 변경할 때마다 우선 순위가 높은 경고를 보냅니다.  다음 이벤트 중 하나는 잘못된 행위자가 사용자 환경을 손상시키려고 하고 있음을 나타낼 수 있습니다.

* 사용된 계정 – 다음을 비롯하여 이 유형의 계정을 사용하는 모든 작업을 모니터링하고 경고합니다.

* 로그인

* 계정 암호 변경 

* 계정 권한/역할이 변경됨 

* 자격 증명 또는 인증 방법이 추가 또는 변경됨 

응급 액세스 계정을 관리하는 방법에 대한 자세한 내용은 [Azure AD에서 응급 액세스 관리자 계정 관리](../roles/security-emergency-access.md)를 참조하세요. 응급 계정에 대한 경고를 만드는 방법에 대한 자세한 내용은 [경고 규칙 만들기](../roles/security-emergency-access.md)를 참조하세요. 

## <a name="privileged-account-sign-in"></a>권한 있는 계정 로그인

Azure AD 로그인 로그를 데이터 원본으로 사용하여 모든 권한 있는 계정 로그인 작업을 모니터링합니다. 로그인 성공 및 실패 정보 외에도 로그에는 다음과 같은 세부 정보가 포함됩니다.

* 인터럽트
* 디바이스
* 위치
* 위험
* 애플리케이션
* 날짜 및 시간
* 계정이 사용하지 않도록 설정됨
* 잠금
* MFA 사기 행위
* CA 오류

### <a name="things-to-monitor"></a>모니터링할 항목

Azure AD 로그인 로그에서 권한 있는 계정 로그인 이벤트를 모니터링할 수 있습니다. 권한 있는 계정에 대한 다음 이벤트에 대해 경고를 생성하고 조사합니다.

| 모니터링 대상 | 위험 수준 |  Where |  필터/하위 필터 | 참고 |
| - | - | - | - | - |
| 로그인 실패, 잘못된 암호 임계값 | 높음 | Azure AD 로그인 로그 | 상태 = 실패<br>및<br>오류 코드 = 50126 | 기준 임계값을 정의한 다음, 모니터링하여 조직 동작에 맞게 조정하고 false 경고가 생성되지 않도록 제한합니다. |
| CA 요구 사항으로 인한 오류 |높음 | Azure AD 로그인 로그 | 상태 = 실패<br>및<br>오류 코드 = 53003<br>및<br>실패 이유 = CA에서 차단됨 | 공격자가 계정으로 이동하려고 했음을 나타내는 것일 수 있습니다. |
| 명명 정책을 따르지 않는 권한 있는 계정입니다.| | Azure 구독 | [Azure Portal을 사용하여 Azure 역할 할당 나열 - Azure RBAC](../../role-based-access-control/role-assignments-list-portal.md)| 구독에 대한 역할 할당을 나열하고, 로그인 이름이 조직 형식과 일치하지 않는 경우 경고합니다. 예를 들어 접두사로 ADM_이 지정된 항목입니다. |
| Interrupt |  높음/보통 | Azure AD 로그인 | 상태 = 중단됨<br>및<br>오류 코드 = 50074<br>및<br>실패 이유 = 강력한 인증 필요<br>상태 = 중단됨<br>및<br>오류 코드 = 500121<br>실패 이유 = 강력한 인증 요청 중에 인증 실패 | 공격자에게 계정에 대한 암호가 있지만 MFA 챌린지를 통과할 수 없음을 나타내는 것일 수 있습니다. | 
| 명명 정책을 따르지 않는 권한 있는 계정입니다.| 높음 | Azure AD 디렉터리 | [Azure AD 역할 할당 나열](../roles/view-assignments.md)| Azure AD 역할에 대한 역할 할당을 나열하고, UPN이 조직 형식과 일치하지 않는 경우 경고합니다. 예를 들어 접두사로 ADM_이 지정된 항목입니다. |
| MFA에 등록되지 않은 권한 있는 계정을 검색합니다. | 높음 | Azure AD Graph API| 관리자 계정에 대한 IsMFARegistered eq false를 쿼리합니다. [credentialUserRegistrationDetails 나열 - Microsoft Graph 베타](/graph/api/reportroot-list-credentialuserregistrationdetails?view=graph-rest-beta&preserve-view=true&tabs=http) | 감사하고 조사하여 의도적인지 또는 실수인지 확인합니다. |
| 계정 잠금 | 높음 | Azure AD 로그인 로그 | 상태 = 실패<br>및<br>오류 코드 = 50053 | 기준 임계값을 정의한 다음, 모니터링하여 조직 동작에 맞게 조정하고 false 경고가 생성되지 않도록 제한합니다. |
| 계정 사용 안 함/로그인 차단됨 | 낮음 | Azure AD 로그인 로그 | 상태 = 실패<br>및<br>대상 = 사용자 UPN<br>및<br>오류 코드 = 50057 | 이 오류는 누군가가 조직을 떠난 후 계정에 액세스하려고 시도함을 나타낼 수 있습니다. 계정이 차단된 경우에도 이 작업을 기록 하고 경고해야 합니다. |
| MFA 사기 행위 경고/차단 | 높음 | Azure AD 로그인 로그/Azure Log Anaylitics | 로그인>인증 세부 정보 결과 세부 정보 = MFA 거부, 사기 코드 입력 | 권한 있는 사용자가 MFA 프롬프트를 조사하지 않았다고 명시했으며 공격자에게 계정에 대한 암호가 있음을 나타낼 수 있습니다. |
| MFA 사기 행위 경고/차단 | 높음 | Azure AD 감사 로그/Azure Log Anaylitics | 활동 유형 = 사기 행위 보고됨 - 사용자가 MFA에 대해 차단됨 또는 사기 행위 보고됨 - 수행된 작업 없음(사기 행위 보고서에 대한 테넌트 수준 설정 기반) | 권한 있는 사용자가 MFA 프롬프트를 조사하지 않았다고 명시했으며 공격자에게 계정에 대한 암호가 있음을 나타낼 수 있습니다. |
| 권한 있는 계정 로그인이 예상대로 제어되지 않습니다. |  | Azure AD 로그인 로그 | 상태 = 실패<br>UserPricipalName = \<Admin account\><br>위치 = \<unapproved location\><br>IP 주소 = \<unapproved IP\><br>디바이스 정보 = \<unapproved Browser, Operating System\> | 승인되지 않은 것으로 정의한 모든 항목을 모니터링하고 경고합니다. |
| 일반 로그인 시간을 벗어남 | 높음 | Azure AD 로그인 로그 | 상태 = 성공<br>및<br>위치 =<br>및<br>시간 = 근무 외 시간 | 예상 시간 외에 로그인이 발생하는지 모니터링하고 경고합니다. 각 권한 있는 계정에 대한 일반적인 작업 패턴을 찾고, 정상적인 업무 시간 외에 갑작스러운 변경이 수행된 경우 경고하는 것이 중요합니다. 정상적인 업무 시간 외의 로그인은 침해 또는 가능한 내부자 위협을 나타낼 수 있습니다. | 
| ID 보호 위험 | 높음 | ID 보호 로그 | 위험 상태 = 위험<br>및<br>위험 수준 = 낮음/보통/높음<br>및<br>작업 = 익숙하지 않은 로그인/TOR 등 | 계정에 대한 로그인과 관련해서 비정상적인 상황이 감지되었으며 경고를 표시해야 함을 나타냅니다. | 
| 암호 변경 | 높음 | Azure AD 감사 로그 | 작업 행위자 = 관리자/셀프 서비스<br>및<br>대상 = 사용자<br>및<br>상태 = 성공/실패 | 특히 전역 관리자, 사용자 관리자, 구독 관리자 및 응급 액세스 계정에 대한 관리자 계정 암호 변경 시 경고합니다. 모든 권한 있는 계정을 대상으로 하는 쿼리를 작성합니다. | 
| 레거시 인증 프로토콜 변경 | 높음 | Azure AD 로그인 로그 | 클라이언트 앱 = 기타 클라이언트, IMAP, POP3, MAPI, SMTP 등<br>및<br>사용자 이름 = UPN<br>및<br>애플리케이션 = Exchange(예) | 대부분의 공격은 레거시 인증을 사용하므로 사용자에 대한 인증 프로토콜이 변경된 경우 공격을 나타낼 수 있습니다. |
| 새 디바이스 또는 위치 | 높음 | Azure AD 로그인 로그 | 디바이스 정보 = 디바이스 ID<br>및<br>브라우저<br>및<br>OS<br>및<br>규격/관리형<br>및<br>대상 = 사용자<br>및<br>위치 | 대부분의 관리자 작업은 제한된 수의 위치에 있는 [권한 있는 액세스 디바이스](/security/compass/privileged-access-devices)에서 수행해야 합니다. 따라서 새 디바이스 또는 위치에 대해 경고합니다. |
| 감사 경고 설정이 변경되었습니다. | 높음 | Azure AD 감사 로그 | 서비스 = PIM<br>및<br>범주 = 역할 관리<br>및<br>작업 = PIM 경고 사용 안 함<br>및<br>상태 = 성공 | 예기치 않은 경우 핵심 경고에 대한 변경이 있을 때 경고해야 합니다. |


## <a name="changes-by-privileged-accounts"></a>권한 있는 계정별 변경 

권한 있는 계정별로 시도하고 완료한 모든 변경 내용을 모니터링합니다. 이렇게 하면 각 권한 있는 계정에 대한 정상적인 작업을 설정하고 예상과 다른 작업에 대해 경고할 수 있습니다. Azure AD 감사 로그는 이 유형의 이벤트를 기록하는 데 사용됩니다. Azure AD 감사 로그에 대한 자세한 내용은 [Azure Active Directory의 감사 로그](../reports-monitoring/concept-audit-logs.md)를 참조하세요. 

### <a name="azure-active-directory-domain-services"></a>Azure Active Directory Domain Services 

Azure Active Directory Domain Services에서 권한이 할당된 권한 있는 계정은 Azure AD Domain Services를 사용하는 Azure 호스티드 VM(가상 머신)의 보안 상태에 영향을 주는 Azure AD DS 관련 작업을 수행할 수 있습니다. VM에서 보안 감사를 사용하도록 설정하고 로그를 모니터링합니다. Azure AD DS 감사를 사용하도록 설정하는 방법에 대한 자세한 내용과 중요한 권한으로 간주되는 항목 목록은 다음 리소스를 참조하세요.

* [Azure Active Directory Domain Services에 대한 보안 감사 사용](../../active-directory-domain-services/security-audit-events.md)

* [중요한 권한 사용 감사](/windows/security/threat-protection/auditing/audit-sensitive-privilege-use)

| 모니터링 대상                                                         | 위험 수준 | Where               | 필터/하위 필터                                                                                                                                                                                                                                                                                                                                                                                                                                                                    | 참고                                                                                                                                                                                                                                                                                                                                                                                                                             |
|-------------------------------------------------------------------------|------------|---------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 시도된 변경 및 완료된 변경                                  | 높음       | Azure AD 감사 로그 | 날짜 및 시간<br>및<br>서비스<br>및<br>활동의 범주 및 이름(항목)<br>및<br>상태 = 성공 또는 실패<br>및<br>대상<br>및<br>초기자/행위자(주체)                                                                                                                                                                                                                                                                                                | 계획되지 않은 변경은 즉시 경고해야 합니다. 이러한 로그는 조사를 지원하기 위해 보존해야 합니다. 테넌트 수준 변경은 즉시 조사해야 합니다(인프라 문서에 연결). 테넌트의 보안 상태를 낮출 수 있는 변경도 마찬가지입니다. 예: MFA 또는 조건부 액세스에서 계정 제외 [애플리케이션에 대한 추가 또는 변경 내용](security-operations-applications.md)에 대해 경고합니다. |
| **예**<br>높은 가치의 앱 또는 서비스에 대해 시도된 변경 또는 완료된 변경 | 높음       | 감사 로그           | 서비스<br>및<br>작업의 범주 및 이름                                                                                                                                                                                                                                                                                                                                                                                                                                | <li>날짜 및 시간 <li>서비스 <li>작업의 범주 및 이름 <li>상태 = 성공 또는 실패 <li>대상 <li>초기자/행위자(주체)                                                                                                                                                                                                                                                                                        |
| Azure AD DS의 권한 있는 변경                                             | 높음       | AD DS               | 이벤트 [4673](/windows/security/threat-protection/auditing/event-4673) 찾기 | [Azure Active Directory Domain Services에 대한 보안 감사 사용](../../active-directory-domain-services/security-audit-events.md)<br>[중요한 권한 사용 감사](/windows/security/threat-protection/auditing/audit-sensitive-privilege-use) 모든 권한 있는 이벤트 목록은 문서를 참조하세요.                                                                                                                            |


## <a name="changes-to-privileged-accounts"></a>권한 있는 계정 변경

권한 있는 계정의 인증 규칙 및 권한에 대한 변경을 조사합니다. 특히 변경으로 인해 Azure AD 환경에서 작업을 수행할 수 있는 더 큰 권한 또는 능력이 생성되는 경우를 조사합니다. 

| 모니터링 대상| 위험 수준| Where| 필터/하위 필터| 참고 |
| - | - | - | - | - |
| 권한 있는 계정 만들기| 중간| Azure AD 감사 로그| 서비스 = 핵심 디렉터리<br>및<br>범주 = 사용자 관리<br>및<br>활동 유형 = 사용자 추가<br>-상관 관계-<br>범주 유형 = 역할 관리<br>및<br>활동 유형 = 역할에 멤버 추가<br>및<br>수정된 속성 = Role.DisplayName| 권한 있는 계정 만들기를 모니터링합니다. 계정 만들기 및 삭제 사이의 짧은 시간 범위에서 상관 관계를 찾습니다. |
| 인증 방법에 대한 변경| 높음| Azure AD 감사 로그| 서비스 = 인증 방법<br>및<br>활동 유형 = 사용자 등록 보안 정보<br>및<br>범주 = 사용자 관리| 공격자가 계정에 인증 방법을 추가하여 계속 액세스할 수 있도록 하는 것을 나타낼 수 있습니다. |
| 권한 있는 계정 권한 변경에 대한 경고| 높음| Azure AD 감사 로그| 범주 = 역할 관리<br>및<br>활동 유형 – 적격 구성원 추가(영구)<br>및<br>활동 유형 – 적격 구성원 추가(적격)<br>및<br>상태 = 성공/실패<br>및<br>수정된 속성 = Role.DisplayName| 특히 알려져 있지 않거나 일반적인 책임을 벗어나는 역할이 할당된 계정이 여기에 해당합니다. |
| 허용되지 않은 권한 있는 계정| 중간| Azure AD 액세스 검토| | 권한 있는 비활성 사용자 계정에 대한 월간 검토를 수행합니다. |
| 조건부 액세스에서 제외되는 계정| 높음| Azure Monitor 로그<br>또는<br>액세스 검토| 조건부 액세스 - 인사이트 및 보고| CA에서 제외되는 모든 계정은 보안 제어를 무시할 가능성이 높으며 손상에 더 취약합니다. 비상 계정은 제외입니다. 이 문서의 후속 섹션에서 비상 계정을 모니터링하는 방법에 대한 정보를 참조하세요.|


조건부 액세스 정책의 예외를 모니터링하는 방법에 대한 자세한 내용은 [조건부 액세스 인사이트 및 보고](../conditional-access/howto-conditional-access-insights-reporting.md)를 참조하세요.

사용하지 않은 권한 있는 계정을 검색하는 방법에 대한 자세한 내용은 [Privileged Identity Management에서 Azure AD 역할에 대한 액세스 검토 만들기](../privileged-identity-management/pim-create-azure-ad-roles-and-resource-roles-review.md)를 참조하세요.

 
## <a name="assignment-and-elevation"></a>할당 및 권한 상승

권한 있는 계정이 상승된 기능으로 영구적으로 프로비저닝되면 공격 노출 영역 및 보안 경계에 대한 위험이 증가할 수 있습니다. 대신, 권한 상승 절차를 사용하여 Just-In-Time 액세스를 사용합니다. 이 유형의 시스템을 사용하면 권한 있는 역할에 대한 자격을 할당할 수 있으며 관리자는 해당 권한이 필요한 작업을 수행할 때만 해당 역할로 권한을 상승시킬 수 있습니다. 권한 상승 프로세스를 사용하면 권한 있는 계정에 대한 권한 상승 및 미사용을 모니터링할 수 있습니다. 

### <a name="establish-a-baseline"></a>기초 설정

예외를 모니터링하려면 먼저 기준을 만들어야 합니다. 다음을 확인합니다.

* 관리자 계정 

   * 권한 있는 계정 전략

   * 온-프레미스 계정을 사용하여 온-프레미스 리소스 관리

   * 클라우드 기반 계정을 사용하여 클라우드 기반 리소스 관리

   * 온-프레미스 및 클라우드 기반 리소스에 대한 관리 권한 분리 및 모니터링

* 권한 있는 역할 보호 

   * 관리 권한이 있는 역할에 대한 보호 전략

   * 권한 있는 계정 사용에 대한 조직 정책

   * 영구적 권한을 유지하고 시간 바인딩과 승인된 액세스를 제공하기 위한 전략 및 원칙

 

다음 개념과 정보는 정책을 확인하는 데 도움이 됩니다.

* Just-in-time 관리 원칙 – Azure AD 로그를 사용하여 사용자 환경에서 공통되는 관리 작업을 수행하기 위한 정보를 캡처합니다. 작업을 완료하는 데 필요한 일반적인 기간을 결정합니다. 

* 충분한 관리 원칙 – 관리 작업에 필요한 사용자 지정 역할일 수 있는 [최소 권한 역할을 결정](../roles/delegate-by-task.md)합니다. 

* 권한 상승 정책 설정 – 상승된 권한의 필요 유형 및 각 작업에 필요한 기간에 대한 정보를 파악했으면 사용자 환경에 대해 상승된 권한 사용을 반영하는 정책을 만듭니다. 예를 들어 전역 관리자 액세스를 1시간으로 제한하는 정책을 정의합니다.

 기준을 설정하고 정책을 설정한 후에는 정책 외부에서 사용 현황을 검색하고 경고하도록 모니터링을 구성할 수 있습니다. 

### <a name="discovery"></a>발견(Discovery)

할당 및 권한 상승의 변경을 특히 주의하고 조사하는 것이 좋습니다. 

### <a name="things-to-monitor"></a>모니터링할 항목

Azure AD 감사 로그 및 Azure Monitor 로그를 사용하여 권한 있는 계정 변경을 모니터링할 수 있습니다. 특히 모니터링 프로세스에 다음을 포함하는 것이 좋습니다. 

| 모니터링 대상| 위험 수준| Where| 필터/하위 필터| 참고 |
| - | - | - | - | - |
| 적격의 권한 있는 역할에 추가되었습니다.| 높음| Azure AD 감사 로그| 서비스 = PIM<br>및<br>범주 = 역할 관리<br>및<br>활동 유형 – 역할에 대한 구성원 추가가 완료됨(적격)<br>및<br>상태 = 성공/실패<br>및<br>수정된 속성 = Role.DisplayName| 역할에 적합한 모든 계정에는 이제 권한 있는 액세스 권한이 부여됩니다. 할당이 예기치 않거나 계정 소유자의 책임이 아닌 역할이 할당된 경우 조사합니다. |
| PIM 외부에서 할당된 역할| 높음| Azure AD 감사 로그| 서비스 = PIM<br>및<br>범주 = 역할 관리<br>및<br>활동 유형 = 역할에 구성원 추가(영구)<br>및<br>상태 = 성공/실패<br>및<br>수정된 속성 = Role.DisplayName| 면밀히 모니터링하고 경고해야 합니다. 가능한 경우 사용자에게 PIM 외부의 역할을 할당하면 안 됩니다. |
| 권한 상승| 중간| Azure AD 감사 로그| 서비스 = PIM<br>및<br>범주 = 역할 관리<br>및<br>활동 유형 - 역할에 구성원 추가가 완료됨(PIM 활성화)<br>및<br>상태 = 성공/실패<br>및<br>수정된 속성 = Role.DisplayName| 상승된 권한 있는 계정은 이제 테넌트의 보안에 영향을 줄 수 있는 변경을 수행할 수 있습니다. 모든 권한 상승을 기록해야 하며, 해당 사용자의 표준 패턴을 벗어나는 경우 계획되지 않은 경우 사용자에게 경고하고 조사해야 합니다. |
| 권한 상승 승인 및 거부| 낮음| Azure AD 감사 로그| 서비스 = 액세스 검토<br>및<br>범주 = UserManagement<br>및<br>활동 유형 = 요청 승인/거부<br>및<br>시작된 행위자 = UPN| 공격에 대한 타임라인을 명확하게 나타낼 수 있기 때문에 모든 권한 상승을 모니터링합니다. |
| PIM 설정 변경| 높음| Azure AD 감사 로그| 서비스 = PIM<br>및<br>범주 = 역할 관리<br>및<br>활동 유형 = PIM의 역할 설정 업데이트<br>및<br>상태 이유 = 활성화 시 MFA 사용 안 함(예)| 이러한 작업 중 하나는 PIM 권한 상승의 보안을 줄이고 공격자가 권한 있는 계정을 더 쉽게 획득할 수 있도록 합니다. |
| SAW/PAW에서 상승이 발생하지 않음| 높음| Azure AD 로그인 로그| 디바이스 ID<br>및<br>브라우저<br>및<br>OS<br>및<br>규격/관리형<br>상관 관계:<br>서비스 = PIM<br>및<br>범주 = 역할 관리<br>및<br>활동 유형 - 역할에 구성원 추가가 완료됨(PIM 활성화)<br>및<br>상태 = 성공/실패<br>및<br>수정된 속성 = Role.DisplayName| 이 설정이 구성된 경우 공격자가 계정을 사용하려고 시도했음을 나타낼 수 있기 때문에 PAW/SAW가 아닌 디바이스에서 권한을 상승하려고 하는 모든 시도를 즉시 조사해야 합니다. |
| 모든 Azure 구독을 관리하기 위한 권한 상승| 높음| Azure Monitor| 활동 로그 탭 <br>디렉터리 활동 탭 <br> 작업 이름=호출자를 사용자 액세스 관리자에 할당 <br> 및 <br> 이벤트 범주=관리 <br> 및<br>상태 = 성공, 시작, 실패<br>및<br>이벤트를 시작한 사람| 계획된 변경이 아닌 경우 즉시 조사해야 합니다. 이 설정을 사용하면 공격자가 사용자 환경에서 Azure 구독에 액세스할 수 있습니다. |


권한 상승 관리에 대한 자세한 내용은 [모든 Azure 구독 및 관리 그룹을 관리할 수 있도록 액세스 권한 상승](../../role-based-access-control/elevate-access-global-admin.md)을 참조하세요. Azure AD 로그에서 사용할 수 있는 정보를 사용하여 권한 상승을 모니터링하는 방법은 Azure Monitor 설명서의 일부인 [Azure 활동 로그](../../azure-monitor/essentials/activity-log.md)를 참조하세요.

Azure 역할에 대한 경고를 구성하는 방법에 대한 내용은 [Privileged Identity Management에서 Azure 리소스 역할에 대한 보안 경고 구성](../privileged-identity-management/pim-resource-roles-configure-alerts.md)을 참조하세요. 

 ## <a name="next-steps"></a>다음 단계

다음 보안 작업 가이드 문서를 참조하세요.

[Azure AD 보안 작업 개요](security-operations-introduction.md)

[사용자 계정에 대한 보안 작업](security-operations-user-accounts.md)

[권한 있는 계정에 대한 보안 작업](security-operations-privileged-accounts.md)

[Privileged Identity Management에 대한 보안 작업](security-operations-privileged-identity-management.md)

[애플리케이션에 대한 보안 작업](security-operations-applications.md)

[디바이스에 대한 보안 작업](security-operations-devices.md)

 
[인프라에 대한 보안 작업](security-operations-infrastructure.md)
